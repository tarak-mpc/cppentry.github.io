---
layout:     post
title:      HDFS的快照原理和Hbase基于快照的表修复
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">      前一篇文章《<a href="http://blog.csdn.net/chaolovejia/article/details/48265335" rel="nofollow">HDFS和Hbase误删数据恢复</a>》主要讲了hdfs的回收站机制和Hbase的删除策略。根据hbase的删除策略进行hbase的数据表恢复。本文主要介绍了hdfs的快照原理和根据快照进行的数据恢复。</span></p>
<h1><span style="font-size:18px;">1.Hdfs的快照原理</span></h1>
<h2><span style="font-size:18px;">1.1 快照原理 </span></h2>
<p><span style="font-size:18px;">     Hdfs的快照（snapshot）是在某一时间点对指定文件系统拷贝，快照采用只读模式，可以对重要数据进行恢复、防止用户错误性的操作。</span></p>
<p><span style="font-size:18px;">      快照分两种：一种是建立文件系统的索引，每次更新文件不会真正的改变文件，而是新开辟一个空间用来保存更改的文件，一种是拷贝所有的文件系统。Hdfs属于前者。</span></p>
<p><span style="font-size:18px;">Hdfs的快照的特征如下：</span></p>
<p><span style="font-size:18px;">1.      快照的创建是瞬间的，代价为O(1)，取决于子节点扫描文件目录的时间。</span></p>
<p><span style="font-size:18px;">2.      当且仅当做快照的文件目录下有文件更新时才会占用小部分内存，占用内存的大小为O(M)，其中M为更改文件或者目录的数量；</span></p>
<p><span style="font-size:18px;">3.      新建快照的时候，Datanode中的block不会被复制，快照中只是记录了文件块的列表和大小信息。</span></p>
<p><span style="font-size:18px;">4.      快照不会影响正常的hdfs的操作。对做快照之后的数据进行的更改将会按照时间顺序逆序的记录下来，用户访问的还是当前最新的数据，快照里的内容为快照创建的时间点时文件的内容减去当前文件的内容。</span></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;">每个快照最高限额为65536个文件或者文件夹，在快照的子文件夹中不允许在创建新的快照。</span></p>
<p><img src="https://img-blog.csdn.net/20150921153523480" alt="" width="1000" height="80"></p>
<p><span style="font-size:18px;">建快照前需要用allowSnapShot命令指定允许做快照的目录：</span></p>
<p><img src="https://img-blog.csdn.net/20150922090149291" width="500" height="50" alt=""></p>
<p><span style="font-size:18px;">这个目录下有一个文件：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153532975" alt="" width="700" height="60"></p>
<p><span style="font-size:18px;">开始新建快照，取名为clcshot1：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153536211" alt="" width="600" height="60"></p>
<p><span style="font-size:18px;">在目录下有一个.snapshot的文件夹，里面有针对该目录的所有快照，每个目录下都存有当前快照的目录：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153539642" alt="" width="1000" height="80"></p>
<p><span style="font-size:18px;">往一个文件里append数据，更改文件的内容：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153542815" alt="" width="1000" height="90"></p>
<p><span style="font-size:18px;">文件大小发生了更改：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153546040" alt="" width="700" height="60"></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;">快照文件夹中还是原始大小，说明hdfs为更改文件开辟了新的空间。</span></p>
<p><img src="https://img-blog.csdn.net/20150921153549547" alt="" width="900" height="60"></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;">尝试删除创建快照的目录：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153553696" alt="" width="1000" height="70"></p>
<p><span style="font-size:18px;">删除失败，想要删除该目录，需要将快照都删除。</span></p>
<p><span style="font-size:18px;">再创建一个snapshot：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153558024" alt="" width="600" height="60"><br></p>
<p><span style="font-size:18px;">在namenode首页上可以看到整个hdfssnapshot的信息：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153601699" alt=""></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;">比较同一个目录下不同版本快照的区别，用命令snapshotDiff：</span></p>
<p><img src="https://img-blog.csdn.net/20150921153604512" alt="" width="900" height="100"></p>
<p><span style="font-size:18px;"> </span></p>
<h2><span style="font-size:18px;">1.2 快照数据恢复</span></h2>
<p><span style="font-size:18px;">      清空/tmp/caolch/中的数据，可以用任何一个快照版本进行恢复。</span></p>
<p><span style="font-size:18px;">恢复命令就是简单的cp。</span></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;">首先清空/tmp/caolch下的所有文件。</span></p>
<p><img src="https://img-blog.csdn.net/20150921153609849" alt="" width="1000" height="80"></p>
<p><span style="font-size:18px;">然后将快照中的数据cp到需要恢复的目录。</span></p>
<p><img src="https://img-blog.csdn.net/20150921153613875" alt="" width="700" height="150"><br></p>
<p><span style="font-size:18px;">注意，mv命令和del命令是不允许的，因为快照是只读的。</span></p>
<p><span style="font-size:18px;"> </span></p>
<h1><span style="font-size:18px;">2.Hbase的基于快照的表修复</span></h1>
<p><span style="font-size:18px;">      Hdfs的快照同样适用于hbase表的恢复。在hbase的数据表目录/hbase/data/default/（default为默认namespace空间）中新建快照，就会在该目录下生成.snapshot的文件夹，里面放着针对该目录的所有快照。</span></p>
<p><img src="https://img-blog.csdn.net/20150921153616953" alt="" width="900" height="100"></p>
<p><span style="font-size:18px;">如果存在用户误删hbase表，</span></p>
<p><img src="https://img-blog.csdn.net/20150921153620699" alt="" width="400" height="300"></p>
<p><span style="font-size:18px;">将快照中保存的数据表文件夹cp到/hbase/data/default下，然后执行如下命令，修复元数据即可。</span></p>
<p><img src="https://img-blog.csdn.net/20150921153625938" alt="" width="400" height="35"></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;"><strong>注意：cp到/hbase/data/default目录下的数据表文件夹的权限要修改成hbase：hbase。</strong></span></p>
<p><span style="font-size:18px;"><strong>否则修改元数据的命令会失败。</strong></span></p>
<p><img src="https://img-blog.csdn.net/20150921153630473" alt="" width="700" height="40"></p>
<p><span style="font-size:18px;"> </span></p>
<p><span style="font-size:18px;">      上面是对整个hbase的namespace空间进行的快照备份，如果在某一个表目录下建快照，那么这个表目录就会变成只读，在hbase shell中执行disable+drop &lt;tablename&gt;不会将表删除，在建快照之后新增的表数据也不会丢失。</span></p>
<p><span style="font-size:18px;">虽然数据不会丢失但是元数据会被drop命令删除，还得用repair命令进行修复。</span></p>
<p><img src="https://img-blog.csdn.net/20150921153635391" alt="" width="400" height="35"></p>
<p><span style="font-size:18px;">修复完之后，再enable这个表，就ok了。</span></p>
            </div>
                </div>
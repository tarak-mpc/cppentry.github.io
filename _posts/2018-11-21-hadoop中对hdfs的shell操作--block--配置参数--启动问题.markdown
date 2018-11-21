---
layout:     post
title:      hadoop中对hdfs的shell操作--block--配置参数--启动问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:24px;color:rgb(255,0,0);">1.对hdfs操作的命令格式是hadoop fs </span><br><span></span><span style="font-size:18px;color:rgb(0,102,0);">1.1 -ls<span></span>&lt;path&gt;<span></span>表示对hdfs下一级目录的查看<br><span></span>1.2 -lsr<span> </span>
&lt;path&gt;<span> </span>表示对hdfs目录的递归查看<br><span></span>1.3<span> </span>-mkdir<span></span>&lt;path&gt;<span></span>创建目录<br><span></span>1.4 -put<span> </span>
&lt;src&gt;<span> </span>&lt;des&gt;<span> </span>
从linux上传文件到hdfs<br><span></span>1.5 -get<span> </span>
&lt;src&gt;<span> </span>&lt;des&gt;<span> </span>
从hdfs下载文件到linux<br><span></span>1.6 -text<span> </span>
&lt;path&gt;<span> </span>查看文件内容<br><span></span>1.7 -rm<span> </span>
&lt;path&gt;<span> </span>表示删除文件<br></span>
<p><span style="font-size:18px;color:rgb(0,102,0);"><span></span>1.7 -rmr<span></span>&lt;path&gt;<span></span>表示递归删除文件</span></p>
<p><span style="font-size:18px;color:rgb(255,0,0);">01、</span><span style="font-size:18px;color:rgb(0,102,0);">这里比如hadoop fs -lsr /    这种的话是省略写法，它会自动去找所操作的机子上的fs.default.name这个属性的值，查到后就自动加上了：hdfs://hadooptest:9000。全写法是：hadoop fs -lsr hdfs://hadooptest:9000/</span></p>
<p><span style="font-size:18px;color:rgb(255,0,0);">02、</span><span style="font-size:18px;color:rgb(0,102,0);">例外：删除根目录的时候，只能这么写：</span></p>
<p><span style="font-size:18px;color:rgb(0,102,0);"> </span><span style="color:rgb(0,102,0);font-size:18px;">hadoop fs -rmr hdfs://hadooptest:9000/*</span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;">删除其他具体文件或文件夹可以不用写全</span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;"><span style="color:rgb(255,0,0);font-size:18px;">03、HDFS fs命令</span><br></span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;">-help[cmd]  //显示命令的帮助信息<br>
-ls(r)&lt;path&gt;  //显示当前目录下所有文件<br>
-du(s)&lt;path&gt;  //显示目录中所有文件大小<br>
-count[-q]&lt;path&gt;  //显示目录中文件数量<br>
-mv&lt;src&gt; &lt;dst&gt;  //移动多个文件到目标目录<br>
-cp&lt;src&gt; &lt;dst&gt;  //复制多个文件到目标目录<br>
-rm(r)  //删除文件(夹)<br>
-put&lt;localsrc&gt; &lt;dst&gt;  //本地文件复制到hdfs<br>
-copyFromLocal  //同put<br>
-moveFromLocal  //从本地文件移动到hdfs<br>
-get[-ignoreCrc] &lt;src&gt; &lt;localdst&gt;  //复制文件到本地，可以忽略crc校验<br>
-getmerge&lt;src&gt; &lt;localdst&gt;  //将源目录中的所有文件排序合并到一个文件中<br>
-cat&lt;src&gt;  //在终端显示文件内容<br>
-text&lt;src&gt;  //在终端显示文件内容<br>
-copyToLocal[-ignoreCrc] &lt;src&gt; &lt;localdst&gt;  //复制到本地<br>
-moveToLocal&lt;src&gt; &lt;localdst&gt;<br>
-mkdir&lt;path&gt;  //创建文件夹<br>
-touchz&lt;path&gt;  //创建一个空文件</span></p>
<p><span style="font-size:24px;color:#ff0000;">----------------------------------------------------理解block--------------------------------------------------</span></p>
<p><span style="font-size:18px;"><span style="color:#000099;">在linux下，文件：</span><br></span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;"><img src="https://img-blog.csdn.net/20150115144735937" alt=""><br></span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;">首先：通过hadoop fs -put /test/hadoop-1.1.2.tar.gz   /test/test2<br></span><span style="color:rgb(0,102,0);font-size:18px;">                  hadoop fs -put /test/jdk-6u24-linux-i586.bin   /test/test2</span><span style="color:rgb(0,102,0);font-size:18px;"><br>
            命令上传到hdfs的test/test2目录下<br>
然后在linux系统下查看上传的文件：进入/test/tmp/dfs/data/current<br></span><span style="color:rgb(0,102,0);font-size:18px;">可以看到：</span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;"><img src="https://img-blog.csdn.net/20150115145118467" alt=""><br></span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;">可以看到hadoop-1.1.2.tar.gz大小是61927560，但是这里设置的块大小是67108864，所以没有达到一个块大小，所以占用一个块了，但是是实际大小。</span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;">jdk-6u24-linux-i586.bin大小是84927175，大于一个块大小，所以被分成两个块。已经分好的块，不会再扩展了，就是形成这么一个块了，大小不会再变了，比如这里的17818311大小的这个块，大小就已经不会变了。</span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;">.meta文件相当于一个验证文件（和md5验证差不多吧）。<br>
总结：hdfs在对数据存储进行block划分时，如果文件大小超过block，那么按照block大小进行划分；</span><span style="color:rgb(0,102,0);font-size:18px;">不到block size的，划分为一个块，是实际数据大小。</span></p>
<p><span style="font-size:24px;color:#ff0000;">---------------------------------------相关配置作用--------------------------------<span style="color:rgb(255,0,0);font-size:24px;">--------------------------</span></span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;">hadoop.tmp.dir<br>
dfs.replication  副本数<br>
dfs.permissions  权限<br><br><br>
dfs.block.size  一个block块大小<br>
fs.checkpoint.size  检查点触发最大edits文件的大小<br>
fs.checkpoint.period <span style="color:rgb(0,102,0);font-size:18px;">检查点触发的时间</span><br></span></p>
<p><span style="font-size:24px;color:#ff0000;">---------------------------------------namenode启动出问题的话-----------------------------------------</span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;"><span style="color:rgb(0,102,0);font-size:18px;">14 04:54:45,013 ERROR org.apache.hadoop.hdfs.server.namenode.FSNamesystem: FSNamesystem initialization failed.<br>
java.io.FileNotFoundException: /test/tmp/dfs/name/current/VERSION (Permission denied)<br><span></span>at java.io.RandomAccessFile.open(Native Method)<br><span></span>at java.io.RandomAccessFile.&lt;init&gt;(RandomAccessFile.java:212)<br><span></span>at org.apache.hadoop.hdfs.server.common.Storage$StorageDirectory.read(Storage.java:219)<br><span></span>at org.apache.hadoop.hdfs.server.common.Storage$StorageDirectory.read(Storage.java:215)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSImage.recoverTransitionRead(FSImage.java:314)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSDirectory.loadFSImage(FSDirectory.java:100)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.initialize(FSNamesystem.java:411)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.&lt;init&gt;(FSNamesystem.java:379)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.NameNode.initialize(NameNode.java:284)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.NameNode.&lt;init&gt;(NameNode.java:536)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.NameNode.createNameNode(NameNode.java:1410)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.NameNode.main(NameNode.java:1419)<br>
2015-01-14 04:54:45,014 ERROR org.apache.hadoop.hdfs.server.namenode.NameNode: java.io.FileNotFoundException: /test/tmp/dfs/name/current/VERSION (Permission denied)<br><span></span>at java.io.RandomAccessFile.open(Native Method)<br><span></span>at java.io.RandomAccessFile.&lt;init&gt;(RandomAccessFile.java:212)<br><span></span>at org.apache.hadoop.hdfs.server.common.Storage$StorageDirectory.read(Storage.java:219)<br><span></span>at org.apache.hadoop.hdfs.server.common.Storage$StorageDirectory.read(Storage.java:215)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSImage.recoverTransitionRead(FSImage.java:314)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSDirectory.loadFSImage(FSDirectory.java:100)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.initialize(FSNamesystem.java:411)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.FSNamesystem.&lt;init&gt;(FSNamesystem.java:379)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.NameNode.initialize(NameNode.java:284)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.NameNode.&lt;init&gt;(NameNode.java:536)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.NameNode.createNameNode(NameNode.java:1410)<br><span></span>at org.apache.hadoop.hdfs.server.namenode.NameNode.main(NameNode.java:1419)<br></span></span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;"><span style="color:rgb(0,102,0);font-size:18px;">这个主要是因为刚开始用了root启动，但是本来应该是hadoop用户的，所以把权限改为hadoop就可以了</span></span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;"><span style="color:rgb(0,102,0);font-size:18px;">直接chown -R   hadoop  file  。</span></span></p>
<p><span style="color:rgb(0,102,0);font-size:18px;"><span style="color:rgb(0,102,0);font-size:18px;">（扩展：</span></span><span style="color:rgb(0,102,0);font-size:18px;">chown -R  hadoop：hadoop file 是改变用户和组，chgrp -R  hadoop file是改变组）</span></p>
            </div>
                </div>
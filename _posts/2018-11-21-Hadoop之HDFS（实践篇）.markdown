---
layout:     post
title:      Hadoop之HDFS（实践篇）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：支持原创，欢迎转载					https://blog.csdn.net/penggougoude/article/details/82357005				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>上一篇文章介绍了HDFS的体系结构及基本原理等偏理论性的内容，本文将更多地从命令行操作、Java程序编写等实践角度着手，对HDFS的使用进行介绍。</p>



<h2 id="1hdfs的命令行操作">1、HDFS的命令行操作</h2>

<p>HDFS是存取数据的分布式文件系统，对HDFS的操作，就是文件系统的基本操作，如文件的创建、修改、删除、修改权限等。对HDFS的操作命令类似于Linux的shell对文件的操作，如ls、mkdir、rm等。总的来说，HDFS的命令行操作可以分为两类，一类为基本操作命令：hdfs dfs；另一类为管理命令：hdfs dfsadmin。HDFS常用基本操作命令列举如下：</p>

<p><img src="https://img-blog.csdn.net/20180904001720386?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20180904001727182?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h4 id="命令hdfs-dfs-getmerge使用实例">命令hdfs dfs -getmerge使用实例：</h4>

<ul>
<li><p>[root@hadoop221 data]# vi data1.txt  (在文件中输入I love Beijing)</p></li>
<li><p>[root@hadoop221 data]# vi data2.txt  (在文件中输入 I love ShangHai)</p></li>
<li><p>[root@hadoop221 data]# hdfs dfs -mkdir /data (在HDFS上创建目录/data)</p></li>
<li><p>[root@hadoop221 data]# hdfs dfs -put data*.txt /data  (将data1.txt和data2.txt文件上传到HDFS上的/data目录下)</p></li>
<li><p>[root@hadoop221 data]# hdfs dfs -getmerge /data/root/data/data3.txt (将HDFS上的/data目录下所有文件的内容合并后，下载到本地文件data3.txt)</p></li>
</ul>

<p>执行结果如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180903235908819?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>这些基本命令的使用方法都非常简单，这里不做过多介绍，简单举两个例子，如：（1）查询hdfs文件系统根目录下有哪些文件和目录，运行命令：</p>

<p>hdfs dfs–ls /</p>

<p>结果如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000005330?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>（2）统计HDFS某个路径下的目录个数，文件个数，以及文件总大小，运行命令：</p>

<p>hdfs dfs –count /data</p>

<p>运行结果如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000027818?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>—————————–目录个数为1 ———————–文件个数为2 ————————————–文件总大小为31字节</p>

<p>HDFS常用管理命令列举如下：</p>

<p><img src="https://img-blog.csdn.net/20180904002143784?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>管理命令不仅仅上面表格列出的这几个，还包括快照、配额等相关的一些命令，这将在后面进行介绍。下面简单介绍下安全模式相关命令的使用方法，如下图所示。从图中可以看到，当开启安全模式后，在HDFS文件系统中创建aaa目录时，创建失败，原因是安全模式下HDFS文件系统仅为可读的，不能写入。当关闭安全模式后，再在HDFS文件系统中创建aaa目录，便创建成功了。</p>

<p><img src="https://img-blog.csdn.net/20180904000107373?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h2 id="2使用java程序操作hdfs">2、使用Java程序操作HDFS</h2>



<h3 id="a准备工作">A、准备工作</h3>

<p>编写Java程序操作HDFS文件系统，不可避免地需要使用Hadoop提供的相关Jar包，需要使用到的所有Jar包位于如下四个路径：</p>

<ul>
<li><p>/root/training/hadoop-2.7.3/share/hadoop/common</p></li>
<li><p>/root/training/hadoop-2.7.3/share/hadoop/common/lib</p></li>
<li><p>/root/training/hadoop-2.7.3/share/hadoop/hdfs</p></li>
<li><p>/root/training/hadoop-2.7.3/share/hadoop/hdfs/lib</p></li>
</ul>

<p>在Eclipse中创建一个普通的Java Project，命名为OperateHDFS，再创建一个文件夹lib，将上述四个目录下所有的Jar文件都拷贝到lib目录下，然后选中所有这些Jar，添加到编译路径中Add to Build Path。</p>



<h3 id="b使用java程序对hdfs文件系统进行操作">B、使用Java程序对HDFS文件系统进行操作</h3>

<p>通过HDFS文件系统提供的Java API，我们可以完成如下的操作：</p>

<p>① 在HDFS文件系统中创建目录；</p>

<p>② 查看HDFS文件系统中的目录及文件信息；</p>

<p>③ 查找某个文件在HDFS集群中的位置；</p>

<p>④ 获取HDFS集群中所有数据节点的信息；</p>

<p>⑤ 通过FileSystem API读取数据（下载文件）；</p>

<p>⑥ 通过FileSystem API上传数据到HDFS文件系统中；</p>

<p>⑦ 删除HDFS文件系统中的数据。</p>

<p>需要注意的是，默认情况下HDFS文件系统的权限检查功能是开启的，因此直接对HDFS进行相关操作会出现权限问题，导致Java程序执行失败，有四种方法可以解决该问题，这里不详细介绍这四种方法。下面程序示例中给出了其中的一种方法，即System.setProperty(“HADOOP_USER_NAME”, “root”)。</p>

<ul>
<li>在HDFS文件系统中创建目录</li>
</ul>

<p><img src="https://img-blog.csdn.net/20180904000154199?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li>查看HDFS文件系统中的目录及文件信息</li>
</ul>

<p><img src="https://img-blog.csdn.net/20180904000214402?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>运行结果如下图所示:</p>

<p><img src="https://img-blog.csdn.net/20180904000227613?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li>查找某个文件在HDFS集群中的位置</li>
</ul>

<p><img src="https://img-blog.csdn.net/2018090400024464?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>运行结果如下图所示:</p>

<p><img src="https://img-blog.csdn.net/20180904000257159?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li>获取HDFS集群中所有数据节点的信息</li>
</ul>

<p><img src="https://img-blog.csdn.net/20180904000313490?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>运行结果如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000327636?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li>通过FileSystemAPI读取数据（下载文件）</li>
</ul>

<p><img src="https://img-blog.csdn.net/2018090400034856?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li>通过FileSystemAPI上传数据到HDFS文件系统中</li>
</ul>

<p><img src="https://img-blog.csdn.net/20180904000403770?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li>删除HDFS文件系统中的数据</li>
</ul>

<p><img src="https://img-blog.csdn.net/20180904000421758?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h3 id="c使用web-console操作hdfs">C、使用Web Console操作HDFS</h3>

<p>通过在浏览器中输入<a href="http://192.168.12.221:50070" rel="nofollow">http://192.168.12.221:50070</a>，可以访问HDFS文件系统的NameNode节点，查看相关信息，如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000444851?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>通过在浏览器中输入<a href="http://192.168.12.221:50090" rel="nofollow">http://192.168.12.221:50090</a>，可以访问HDFS文件系统的SecondaryNameNode节点，查看相关信息，如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000459356?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h2 id="3hdfs的高级功能">3、HDFS的高级功能</h2>



<h3 id="a回收站">A、回收站</h3>

<p>同Linux系统的回收站设计一样，HDFS会为每一个用户创建一个回收站目录：/user/用户名/.Trash/，每一个被用户通过Shell命令删除的文件/目录，在系统回收站中保存一个周期，也就是当系统回收站中的文件/目录在一段时间之内没有被用户回复的话，HDFS就会自动把这个文件/目录彻底删除，之后，用户就永远也找不回这个文件/目录了。</p>

<p>在HDFS内部的具体实现，就是在NameNode中开启了一个后台县城Emptier，这个线程专门管理和监控系统回收站下面的所有文件/目录，对于已经超过生命周期的文件/目录，这个线程就会自动地删除它们，不过这个管理的粒度很大。另外，用户也可以手动清空回收站，清空回收站的操作同删除普通文件目录是一样的，只不过HDFS会自动检测这个文件目录是不是位于回收站，如果是，HDFS当然就不会再把它放入用户的回收站中了。</p>

<p>这里需要注意的是：默认情况下，HDFS的回收站功能是关闭的，可以通过在core-site.xml配置文件中，设置fs.trash.interval参数打开回收站功能，这样，HDFS会为当前用户创建一个回收站，用户删除文件时，文件并不是彻底消失了，而是mv到了/user/用户名/.Trash/这个目录下。</p>

<p>运行命令hdfs dfs -rmr /aaa，执行结果如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000603877?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>上述命令的功能是删除HDFS文件系统中根目录下的aaa文件夹，从Log中可以看到，是将aaa文件夹移到了user/root/.Trash/Current目录下。运行命令hdfs dfs -ls /user/root/.Trash/Current，结果如下：</p>

<p><img src="https://img-blog.csdn.net/20180904000620216?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>通过运行命令hdfs dfs -cp /user/root/.Trash/Current/aaa /，可以看到实现了恢复文件夹aaa的功能。</p>

<p><img src="https://img-blog.csdn.net/20180904000634832?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>HDFS回收站功能的本质，总结为一句话就是：将一个文件/目录剪切到指定的目录下，并保存指定的时间，超时自动彻底删除。在默认情况下，回收站功能都是关闭的，这是为了避免空间的浪费，因为HDFS有了数据冗余机制保证了一定的安全性。</p>



<h3 id="b快照">B、快照</h3>

<p>在介绍HDFS的快照之前，需要先了解Snapshot快照的概念，它有一个很基本的原则，即快照不是数据的简单拷贝，只做差异的复制。这个原则在其他很多系统快照概念中都是遵守的，比如磁盘快照，也是不保存真实数据。因为不保存实际的数据，所以快照的生成往往非常的迅速。在HDFS中，如果对其中一个目录，比如/data创建一个快照，则快照文件中将会有与/data目录下完全一样的子目录文件结构以及相应的属性信息，通过命令hdfs dfs–cat能够看到文件的具体内容，但是这并不是意味着Snapshot对此数据进行了完全拷贝，这里遵循一个原则，对于大多数不变的数据，你所看到的数据其实是当前物理路径所指向的内容，而发生变化的inode才会被Snapshot额外拷贝，其实就是一个差异拷贝。</p>

<p>这里需要注意的是，默认情况下快照功能是禁止的，为了避免空间的浪费，除非针对特别重要的数据，才开启快照功能。通过运行命令hdfs dfsadmin -allowSnapshot /data，为目录/data开启快照功能，如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000655700?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>通过运行命令hdfs dfs -createSnapshot /data backup_data_0519，为目录/data创建了一个快照，如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000710798?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>通过运行命令hdfs dfs -put yarn-site.xml /data/，往/data目录中上传一个新文件，再运行命令hdfs dfs -createSnapshot /data backup_data_0519_ 02，为/data目录再创建一个快照，最后运行命令hdfs snapshotDiff /databackup_data_0519 backup_data_0519_02，对比这两个快照，如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904000724143?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>通过快照，可以很容易地恢复某个文件，假如一不小心把/data/yarn-site.xml文件给删除了，通过运行如下命令hdfs dfs-cp /data/.snapshot /backup_data_0519_02/yarn-site.xml /aaa，便可以恢复yarn-site.xml文件。</p>

<p><img src="https://img-blog.csdn.net/20180904000737736?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h3 id="c配额">C、配额</h3>

<p>HDFS文件系统允许管理员为独立的目录使用的名称数目和空间大小设置quota配额，新建立的目录是没有配额的，最大的配额是Long.Max_Value，配额为1可以强制目录保持为空。名称配额和空间配额的操作相互独立，但它们的管理和实现是紧密相连的。</p>

<p>（1）名称配额是对某个目录中文件和目录的总数目进行严格限制，如果超出配额，那么创建文件或目录的操作就会失败。重命名不会改变该目录的配额，如果重命名操作导致违反配额限制，该操作将会失败；如果尝试设置一个配额，而现有文件数目已经超出了这个新配额，则设置失败。配额和fsimage保持一致，当启动时，如果fsimage违反了某个配额限制（也许fsimage被偷偷改变了），则启动失败并生成错误报告。设置或删除一个配额会创建相应的日志记录。</p>

<p>举例如下图所示。从图中可以看到，刚开始aaa目录下只有一个文件，设置名称配额为3，然后在aaa目录下创建一个目录folder1，操作成功，此时aaa目录下共有2个文件/目录，再在aaa目录下创建一个目录便失败了，原因是违反了名称配额的限制。注意：设置名称配额为n，最多文件/目录数目为n-1。</p>

<p><img src="https://img-blog.csdn.net/20180904000758870?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>（2）空间配额是对某个目录下文件总大小进行的严格限制。如果配额不允许写入完整块，则块分配就会失败。块的每个副本都将根据配额进行计数。配额与重命名的目录保持一致，重命名文件夹后配额还是起作用，如果重命名操作将导致配额冲突，则重命名操作将失败。新创建的目录没有关联的配额。最大的配额上限是 Long.Max_Value。配额为零，仍然允许创建文件，但不能向文件中添加任何块。目录不使用主机文件系统空间，也不计入空间配额。用于保存文件元数据的主机文件系统空间不计入配额。配额按文件的预期副本因子计数；更改文件的副本因子将计入或扣除配额。</p>

<p>配额随着fsimage一起持久化。启动时，如果fsimage立即违反配额(可能是秘密修改了fsimage )，则会为每个此类违规打印警告。设置或删除配额将创建日志记录。</p>

<p>举例如下图所示。为空目录bbb设置空间配额为1M，然后将data目录下的一个非常小（最多几十字节）的文件data1.txt复制到bbb目录下，结果操作不成功，报超出空间配额的错误。这里需要注意：空间配额最小需要设置为128M，因为无论多小的文件，其占用的数据块大小默认是128M。</p>

<p><img src="https://img-blog.csdn.net/20180904000821580?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>（3）存储类型配额是对某个目录中使用的存储类型（SSD、磁盘、归档等）进行的严格限制。它在许多方面类似于存储空间配额，但对集群存储空间使用提供精细控制。要在目录上设置存储类型配额，必须在目录上配置存储策略，以便根据存储策略将文件存储在不同的存储类型中。</p>

<p>存储类型配额可以与空间配额和名称配额相结合，以有效的管理集群存储使用情况。这里不对存储类型配额做过多介绍，可以自行上网查询相关资料。</p>



<h3 id="d安全模式">D、安全模式</h3>

<p>安全模式是HDFS所处的一种特殊状态，在这种状态下，文件系统只接受读数据请求，而不接受删除、修改等变更请求。当HDFS的NameNode节点启动时，会进入安全模式阶段，在此阶段，DataNode会向NameNode上传他们数据块的列表，让NameNode得到数据块的位置信息，并对每个文件对应的数据块副本进行统计。当最小副本条件满足时，即：一定比例的数据块都到达最小副本数，系统会退出安全模式。而这需要一定的延迟时间。当最小的副本条件未达到要求时，就会对副本数不足的数据块安排DataNode进行复制，直到达到最小的副本数。而在安全模式下，系统会处于只读装态，NameNode不会处理任何数据块的复制和删除命令。</p>

<p>下图展示了HDFS的启动过程。第一个阶段加载元信息（即加载fsimage文件）；第二个阶段是加载日志（即加载edits文件）；第三个阶段是设置检查点（即Saving checkpoint）;第四个阶段自动进入安全模式，主要作用是检查数据块的副本率（检查是否满足冗余度的配置要求），如果不满足，则会执行相应的水平复制操作。</p>

<p><img src="https://img-blog.csdn.net/2018090400084493?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h2 id="4hdfs使用的底层原理">4、HDFS使用的底层原理</h2>



<h3 id="a代理对象">A、代理对象</h3>

<p>代理（Proxy）是一种设计模式，提供了对目标对象的另一种访问方式，即通过调用代理对象访问目标对象。这样做的好处是，可以在目标对象实现的基础之上，扩展额外的功能操作，而无需修改先前已经编写好的代码。目前来说，主要有三种代理类型，即静态代理、动态代理以及Cglib代理。</p>



<h4 id="静态代理">静态代理</h4>

<p>静态代理在使用时，需要定义接口或者父类，被代理对象与代理对象一起实现相同的接口或者继承相同父类。静态代理的优点是，可以做到在不修改目标对象功能的前提下，对目标对象的功能进行扩展；缺点是，由于代理对象需要和目标对象实现一样的接口，因而会出现很多的代理类，同时，一旦接口增加新方法，目标对象与代理对象都需要重新进行维护。解决静态代理中的缺点，可以使用动态代理方式。</p>



<h4 id="动态代理">动态代理</h4>

<p>动态代理对象不需要实现接口，但是目标对象一定要实现接口，否则不能使用动态代理。动态代理具有以下几个特点：</p>

<ul>
<li><p>代理对象不需要实现接口；</p></li>
<li><p>代理对象的生产，是利用JDK提供的API，动态地在内存中构建代理对象（需要指定被代理对象或目标对象实现的接口类型）；</p></li>
<li><p>动态代理也叫JDK代理，接口代理。</p></li>
</ul>

<p>下面以实际生活中的购买火车票为例，编写程序实现动态代理的操作。</p>

<p>定义接口类，如下图：</p>

<p><img src="https://img-blog.csdn.net/20180904000928308?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>定义接口类的实现类，如下图：</p>

<p><img src="https://img-blog.csdn.net/20180904000942218?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>编写测试类，如下图：</p>

<p><img src="https://img-blog.csdn.net/20180904000955755?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20180904001019638?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
运行结果如下图所示。可以看到，方法buyTicket2的功能被改变了。</p>

<p><img src="https://img-blog.csdn.net/20180904001032457?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h4 id="cglib代理">Cglib代理</h4>

<p>上文介绍的静态代理和动态代理模式都要求目标对象实现一个接口，但是有时候目标对象只是一个单独的对象，并没有实现任何的接口，这个时候就可以使用以目标对象子类的方式类实现代理，这种方法就叫做Cglib代理。Cglib代理，也叫做子类代理，它是在内存中构建一个子类对象，从而实现对目标对象功能的扩展。这里对Cglib代理不做过多解释，可以上网查找相关资料进行学习。</p>



<h3 id="brpc">B、RPC</h3>

<p>RPC，即Remote Procedure Call，中文名叫做远程过程调用，它允许一台计算机程序远程调用另外一台计算机的子程序，而不用去关心底层的网络通信细节，对用户来说是透明的。因此，它经常用于分布式网络通信中。RPC协议假定某些传输协议的存在，如TCP或UDP，为通信程序之间携带信息数据。Hadoop的进程间交互都是通过RPC来进行的，如NameNode与DataNode之间，Jobtracker与Tasktracker之间等。</p>

<p>RPC具有显著的特点，如（1）透明性：远程调用其他机器上的程序，对用户来说就像是调用本地方法一样；（2）高性能：RPC Server能够并发处理多个来自Client的请求；（2）可控性：jdk中已经提供了一个RPC框架——RMI，但是该RPC框架过于重量级并且可控之处比较少，所以Hadoop RPC实现了自定义的PRC框架。</p>

<p>Hadoop RPC对外主要提供了两种接口（见类org.apache.hadoop.ipc. RPC），分别是：</p>

<p>（1）public static  ProtocolProxy getProxy/waitForProxy(…)</p>

<p>　　构造一个客户端代理对象（该对象实现了某个协议），用于向服务器发送RPC请求。</p>

<p>（2）public static Server RPC.Builder (Configuration).build()</p>

<p>　　为某个协议（实际上是Java接口）实例构造一个服务器对象，用于处理客户端发送的请求。</p>

<p>使用HadoopRPC的四个步骤，分别如下：</p>

<p>（1）定义RPC协议</p>

<p>　　RPC协议是客户端和服务器端之间的通信接口，它定义了服务器端对外提供的服务接口。</p>

<p>（2）实现RPC协议</p>

<p>　　Hadoop RPC协议通常是一个Java接口，用户需要实现该接口。</p>

<p>（3）构造和启动RPC SERVER</p>

<p>　　直接使用静态类Builder构造一个RPC Server，并调用函数start()启动该Server。</p>

<p>（4）构造RPCClient并发送请求</p>

<p>使用静态方法getProxy构造客户端代理对象，直接通过代理对象调用远程端的方法。</p>

<p>下面举个实例，讲述如何使用Hadoop RPC。</p>

<p><img src="https://img-blog.csdn.net/20180904001112883?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>说明：这个接口类即为服务端自定义协议。Hadoop中所有自定义的RPC接口都需要继承VersionProtocol接口，它描述了协议的版本信息；默认情况下，不同版本号的RPCClient和Server之间不能相互通信，因此客户端和服务端通过版本号标识。</p>

<p><img src="https://img-blog.csdn.net/20180904001126548?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>说明：这个类为服务端自定义协议的实现类，实现了自定的方法callServerFunc，同时它还必须实现VersionedProtocol接口中的两个方法。</p>

<p><img src="https://img-blog.csdn.net/20180904001140469?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>说明：这个类即为服务端，通过RPC中的内部类Builder的静态方法build获得Server对象。builder对象设置了四个参数，第一个参数指定了服务端的地址；第二个参数指定了服务端的端口号；第三个参数指明了服务端协议；第四个参数指明了调用的服务端协议实例。获得服务器对象后，启动服务器。这样，服务器就在指定端口监听客户端的请求。到此为止，服务器就处于监听状态，不停地等待客户端请求到达。</p>

<p><img src="https://img-blog.csdn.net/20180904001155795?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>说明：这个类即为客户端，使用静态方法getProxy构造客户端代理对象，直接通过代理对象调用远程服务端的方法。该方法有四个参数，第一个参数是被调用的服务端的接口类（即自定义协议）；第二个参数是客户端版本号，需与服务端保持一致；第三个参数是服务端地址，使用的是套接字地址类型，封装了地址和端口号；第四个参数是配置信息，使用默认即可。返回的代理对象，就是服务端对象的代理，内部就是使用java.lang.Proxy实现的。</p>

<p>运行结果如下图所示：</p>

<p><img src="https://img-blog.csdn.net/20180904001210994?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<hr>

<p>更多大数据技术精彩内容，欢迎关注 <br>
<img src="https://img-blog.csdn.net/20180904001243971?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Blbmdnb3Vnb3VkZQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h4 id="参考文献">参考文献：</h4>

<p>——《百度百科》 <br>
——《CSDN其他博文》 <br>
——《潭州大数据课程课件》</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      hadoop(三) - HDFS分布式存储系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/zdp072/article/details/41258283				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:18px;background-color:rgb(102,102,102);"><span style="color:#ffffff;">一. 分布文件系统和HDFS:</span></span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">其实我们可以把分布式文件系统HDFS理解为windows文件系统, 可以在文件夹里面分门别类地存放文件, 只不过HDFS通过网络把文件存放在多台主机上</span></p>
<p><strong><br></strong></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:18px;color:rgb(255,255,255);background-color:rgb(102,102,102);">二. HDFS的shell操作:</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">HDFS是存取数据的分布式文件系统, 对HDFS的操作就是文件系统的基本操作, 比如文件的创建、修改、删除、修改权限等</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">对HDFS的操作命令类似Linux的shell对文件的操作, 如: ls、mkdir、rm等</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">注意：hadoop没有当前目录的概念，也没有cd命令</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">HDFS命令选项：</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">1.  - ls 显示当前目录结构</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -ls /</span></p>
<pre><code class="language-plain">root@zhangdongping:/usr# hadoop fs -ls /
Found 3 items
drwxr-xr-x   - root supergroup          0 2014-11-11 20:53 /d1
-rw-r--r--   1 root supergroup         20 2014-11-11 20:54 /d2
drwxr-xr-x   - root supergroup          0 2014-11-11 20:34 /usr
root@zhangdongping:/usr#</code></pre><span style="font-family:'Comic Sans MS';">首字母表示文件夹(如果是“d”) 或文件（如果是“-”）；<br>
后面的9 位字符表示权限；<br>
后面的数字或者“-”表示副本数。如果是文件，使用数字表示副本数；文件夹没有副本；<br>
后面的“root”表示属主；<br>
后面的“supergroup”表示属组；<br>
后面的“0”、“6176”、“37645”表示文件大小，单位是字节；<br>
后面的时间表示修改时间，格式是年月日时分；<br>
最后一项表示文件路径。</span>
<p><br></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">2. -lsr递归显示目录</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -lsr /usr</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令选项表示递归显示当前路径的目录结构, 后面跟hdfs路径</span></p>
<pre><code class="language-plain">root@zhangdongping:/usr# hadoop fs -lsr /usr
drwxr-xr-x   - root supergroup          0 2014-11-11 20:48 /usr/local
drwxr-xr-x   - root supergroup          0 2014-11-11 20:48 /usr/local/hadoop
drwxr-xr-x   - root supergroup          0 2014-11-11 20:48 /usr/local/hadoop/tmp
drwxr-xr-x   - root supergroup          0 2014-11-18 22:55 /usr/local/hadoop/tmp/mapred
drwx------   - root supergroup          0 2014-11-18 22:55 /usr/local/hadoop/tmp/mapred/system
-rw-------   1 root supergroup          4 2014-11-18 22:55 /usr/local/hadoop/tmp/mapred/system/jobtracker.info</code></pre><span style="font-family:'Comic Sans MS';"><br>
3. -du 统计目录下各文件大小</span>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -du /usr</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令显示指定路径下的文件大小, 单位是字节</span></p>
<pre><code class="language-plain">root@zhangdongping:/usr# hadoop fs -du /usr
Found 1 items
4           hdfs://zhangdongping:9000/usr/local</code></pre><br><p></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">4. -dus 汇总统计目录下文件大小</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -dus /usr</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令显示指定路径的文件大小, 单位是字节</span></p>
<pre><code class="language-plain">root@zhangdongping:/usr# hadoop fs -dus /usr
hdfs://zhangdongping:9000/usr   4</code></pre><br><span style="font-family:'Comic Sans MS';">5. -count 统计文件(夹)数量</span>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -count /usr</span></p>
<p><span style="font-size:14px;"><span style="font-family:'Comic Sans MS';">该命令显示指定路径下的文件夹数量、文件数量、文件总大小信息</span></span></p>
<pre><code class="language-plain">root@zhangdongping:/usr# hadoop fs -count /usr
           6            1                  4 hdfs://zhangdongping:9000/usr
root@zhangdongping:/usr# hadoop fs -lsr /usr
drwxr-xr-x   - root supergroup          0 2014-11-11 20:48 /usr/local
drwxr-xr-x   - root supergroup          0 2014-11-11 20:48 /usr/local/hadoop
drwxr-xr-x   - root supergroup          0 2014-11-11 20:48 /usr/local/hadoop/tmp
drwxr-xr-x   - root supergroup          0 2014-11-18 22:55 /usr/local/hadoop/tmp/mapred
drwx------   - root supergroup          0 2014-11-18 22:55 /usr/local/hadoop/tmp/mapred/system
-rw-------   1 root supergroup          4 2014-11-18 22:55 /usr/local/hadoop/tmp/mapred/system/jobtracker.info</code></pre>
<p><span style="font-family:'Comic Sans MS';"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">6. -mv 移动</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">命令: hadoop fs -mv /usr/root/abc  /usr</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令表示移动hdfs的文件到指定的hdfs目录中, </span></span><span style="font-size:14px;font-family:'Comic Sans MS';">后面跟两个路径, 第一个表示源文件, 第二个表示目的目录</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;"><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">7. -cp 复制</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -cp /usr/abc   /usr/root</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令表示复制hdfs指定的文件到指定的hdfs目录中, </span></span><span style="font-size:14px;font-family:'Comic Sans MS';">后面跟两个路径, 第一个是被复制的文件, 第二个是目的地</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">8. -rm 删除文件/空白文件夹</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -rm /usr/abc</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令表示删除指定的文件或者空目录</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">9. -rmr 递归删除</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -rmr /usr</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令表示递归删除指定目录下的所有子目录和文件</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">10. -put 上传文件</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -put hbase-env.sh /</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令表示把linux上的文件复制到hdfs中</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">11. -copyFromLocal 从本地复制</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">操作与-put一致</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">12. -moveFromLocal 从本地移动</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -<span style="font-family:'Comic Sans MS';font-size:14px;">moveFromLocal install.log</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令表示把文件从linux上移动到hdfs中</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">13. getmerge 合并到本地</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">命令: hadoop fs -getmerge /hbase abc</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令表示把hdfs指定目录下的所有文件内容合并到本地linux文件中</span></span></p>
<p><br></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">14. -cat 查看文件内容</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -cat /hbase-env.sh</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令是查看文件内容</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">15. -text 查看文件内容</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令作用和用法可以认为和-cat相同</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">16. -mkdir 创建空白文件夹</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -mkdir /abc</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令表示创建文件夹, 后面跟的路径是在hdfs中将要创建的文件夹</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">17. -setrep 设置副本数量</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -setrep 2  /install.log</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令是修改已保存文件的副本数量, 后面跟副本数量, 再跟文件路径</span></span></p>
<p><br></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">18. -touchz 创建空白文件</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -touchz /emptyfiles</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令是在hdfs中创建空白文件</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">19. -stat 显示文件的统计信息</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -statu  '%b %n %o %r %Y</span><span style="font-family:'Comic Sans MS';font-size:14px;">' /install.log</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令显示文件的一些统计信息</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">20. -tail 查看文件尾部内容</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -tail /install.log</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令显示文件最后1k字节的内容, 一般用于查看日志, 如果带有选项-f, 那么当文件内容变化时, 也会自动显示</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">21. -chmod 修改文件权限</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -chmod 755 /emptyfile</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令使用类似于linux shell中的chmod用法, 作用是修改文件的权限</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">如果加上选项-R, 可以对文件夹中所有文件修改权限:  hadoop fs -chmod -R  700  /usr</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;"></span></span></p>
<pre><code class="language-plain">root@zhangdongping:~/Desktop# hadoop fs -lsr /
-rw-r--r--   1 root supergroup         20 2014-11-11 20:54 /d2
-rw-r--r--   2 root supergroup         20 2014-11-19 21:16 /install.log
drwxr-xr-x   - root supergroup          0 2014-11-19 21:15 /user
drwxr-xr-x   - root supergroup          0 2014-11-19 21:15 /user/root
-rw-r--r--   1 root supergroup         20 2014-11-19 21:15 /user/root/d2
drwxr-xr-x   - root supergroup          0 2014-11-18 22:55 /usr
drwxr-xr-x   - root supergroup          0 2014-11-18 22:55 /usr/local
drwxr-xr-x   - root supergroup          0 2014-11-18 22:55 /usr/local/hadoop
drwxr-xr-x   - root supergroup          0 2014-11-19 20:42 /usr/local/hadoop/tmp
drwxr-xr-x   - root supergroup          0 2014-11-19 21:07 /usr/local/hadoop/tmp/mapred
drwx------   - root supergroup          0 2014-11-19 21:07 /usr/local/hadoop/tmp/mapred/system
-rw-------   1 root supergroup          4 2014-11-19 21:07 /usr/local/hadoop/tmp/mapred/system/jobtracker.info
root@zhangdongping:~/Desktop#
root@zhangdongping:~/Desktop#
root@zhangdongping:~/Desktop#
root@zhangdongping:~/Desktop# hadoop fs -chmod -R  700  /usr
root@zhangdongping:~/Desktop#
root@zhangdongping:~/Desktop#
root@zhangdongping:~/Desktop# hadoop fs -lsr /
-rw-r--r--   1 root supergroup         20 2014-11-11 20:54 /d2
-rw-r--r--   2 root supergroup         20 2014-11-19 21:16 /install.log
drwxr-xr-x   - root supergroup          0 2014-11-19 21:15 /user
drwxr-xr-x   - root supergroup          0 2014-11-19 21:15 /user/root
-rw-r--r--   1 root supergroup         20 2014-11-19 21:15 /user/root/d2
drwx------   - root supergroup          0 2014-11-18 22:55 /usr
drwx------   - root supergroup          0 2014-11-18 22:55 /usr/local
drwx------   - root supergroup          0 2014-11-18 22:55 /usr/local/hadoop
drwx------   - root supergroup          0 2014-11-19 20:42 /usr/local/hadoop/tmp
drwx------   - root supergroup          0 2014-11-19 21:07 /usr/local/hadoop/tmp/mapred
drwx------   - root supergroup          0 2014-11-19 21:07 /usr/local/hadoop/tmp/mapred/system
-rw-------   1 root supergroup          4 2014-11-19 21:07 /usr/local/hadoop/tmp/mapred/system/jobtracker.info</code></pre>
<p></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">22. -chown 修改属主</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -chown itcast /emptyfile</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令表示修改文件的属主为itcast,  </span></span><span style="font-size:14px;font-family:'Comic Sans MS';">如果带有选项-R, 意味着可以递归修改文件夹中的所有文件的属主、属组信息</span></p>
<p><span style="font-size:14px;font-family:'Comic Sans MS';"><span style="font-family:'Comic Sans MS';font-size:14px;">把文件emptyfile的属主和属组都修改为itcast         hadoop fs -chown itcast:itcast /emptyfile</span><br></span></p>
<p><span style="font-size:14px;font-family:'Comic Sans MS';"><span style="font-family:'Comic Sans MS';font-size:14px;"><span style="font-family:'Comic Sans MS';font-size:14px;">只修改文件emptyfile的属组为itcast                       <span style="font-family:'Comic Sans MS';font-size:14px;">hadoop
 fs -chown :itcast /emptyfile</span></span><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">23. -chgrp 修改属组</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -chgrp supergroup /emptyfile</span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;">该命令的作用是修改文件的属组</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">24. -help 帮助</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">命令: hadoop fs -help rm</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">该命令会显示帮助信息, 后面跟上需要查询的命令即可.</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><br></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-family:'Comic Sans MS';line-height:26px;"><span style="font-size:18px;color:rgb(255,255,255);background-color:rgb(102,102,102);">三. HDFS体系结构与基本概念:</span></span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><span style="line-height:26px;">我们通过hadoop shell上传的文件时存放在DataNode的block中, 可以用一句话描述HDFS: 把客户端的大文件存放在很多节点的数据块中.</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><span style="line-height:26px;">1. NameNode: </span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;">NameNode是HDFS的守护进程，负责记录文件时如何分割成数据块的，以及这些数据块分别被存储到那些数据节点上，它的主要功能是对内存及I/O进行集中管理</span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><span style="line-height:26px;"><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><span style="line-height:26px;">2. DataNode:</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><span style="line-height:26px;">DataNode是HDFS中真正存储数据的, DataNode在存储数据的时候是按照block为单位读写数据的, block是hdfs读写数据的基本单位, 每64MB字节划分为一个block, 这个64MB是配置在core-default.xml中的.</span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><span style="line-height:26px;">副本就是备份, 目的是为了安全, 副本分配在不同的DataNode服务器中, HDFS中每个数据块都有3份, 配置在hdfs-default中.</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;">注意: 我们从linux磁盘上传一个完整的文件到hdfs中, 这个文件在linux是可以看到的, 但上传到hdfs后, 就不会有一个对应的文件存在, 而是被划分成很多的block存在的.</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;"><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;">3. Secondary NameNode:</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;">Secondary NameNode是一个用来监控HDFS状态的辅助后台程序，它不接受或记录任何实时的数据变化，但是，它会与NameNode进行通信，以便定时地保存HDFS元数据的快照，由于NameNode是单点的，通过Secondary NameNode的快照功能，可以将NameNode的宕机时间和数据损失降低到最小。同时，如果NameNode发生问题，Secondary
 NameNode可以及时地作为备用NameNode使用。</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;"><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;">4. 读取数据流程：</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;">客户端要访问HDFS的一个文件， 首先从namenode获取组成这个文件的数据块位置列表，根据列表知道存储数据块的datanode；</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;">访问datanode获取数据，Namenode并不参与数据实际传输。</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20141229220943906" alt=""><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;"><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;">5. 写入数据流程：</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;">客户端请求namenode创建新文件，然后客户端将数据写入DFSOutputStream，并建立pipeline依次将目标数据块写入各个datanode，建立多个副本。</span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20141229221830270" alt=""><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="font-size:14px;line-height:26px;"><br></span></span></p>
<p><span style="font-family:'Comic Sans MS';font-size:14px;"><span style="line-height:26px;"></span></span></p>
<p><span style="font-family:'Comic Sans MS';"><span style="line-height:26px;"><span style="font-size:18px;color:rgb(255,255,255);background-color:rgb(102,102,102);">四. HDFS的web接口:</span></span></span></p>
<p><span style="font-family:'Comic Sans MS';">HDFS对外提供了可供访问的http server, 开放了很多端口, 下面介绍几个常用的端口:</span></p>
<p><span style="font-family:'Comic Sans MS';">1.  50070端口, 查看NameNode状态:    http://hadoop0:50070    该端口的定义位于core-default.xml</span></p>
<p><span style="font-family:'Comic Sans MS';">2. 50075端口, 查看DataNode的:  http://hadoop0:50075     <span style="font-size:14px;line-height:26px;">该端口的定义位于hdfs-default.xml</span></span></p>
<span style="font-family:'Comic Sans MS';">3. 50090端口, 查看SecondaryNameNode的  <span style="font-size:14px;line-height:26px;">http://</span><span style="font-size:14px;line-height:26px;">hadoop0:50090</span></span>
<p><span style="font-size:14px;"><span style="line-height:26px;"><span style="font-family:'Comic Sans MS';">4. 50030端口, 查看JobTracker状态的  <span style="font-size:14px;line-height:26px;">http://</span><span style="font-size:14px;line-height:26px;">hadoop0:50030  该端口的定义位于mapred-default.xml</span></span></span></span></p>
<p><span style="font-size:14px;"><span style="line-height:26px;"><span style="font-size:14px;line-height:26px;"><span style="font-family:'Comic Sans MS';">4. 50060端口, 查看TaskTracker  <span style="font-size:14px;line-height:26px;">http://</span><span style="font-size:14px;line-height:26px;">hadoop0:50060
  <span style="font-size:14px;line-height:26px;">该端口的定义位于mapred-default.xml</span></span></span></span><br></span></span><br></p>
<p></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"><span style="font-size:18px;color:rgb(255,255,255);background-color:rgb(102,102,102);">五. HDFS的Java访问接口:</span></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">通过本地Eclipse种的Java代码访问远程linux中的hdfs, 需要保证以下几点:</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">a. 确保宿主机与客户机的网络互通</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">b. 确保宿主机和客户机的防火墙都关闭</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">c. 确保宿主机与客户机使用的JDK版本一直, 否则会报不支持的版本的错误</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">d. 宿主机的登陆用户名必须与客户机的用户名一致, 否则会报权限异常</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"><br></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">1. 第一个测试代码:</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"></p>
<pre><code class="language-java">public class App1 {
	static final String PATH = "hdfs://zhangdongping:9000/hello.txt";
	public static void main(String[] args) throws Exception {
		URL.setURLStreamHandlerFactory(new FsUrlStreamHandlerFactory());
		
		final URL url = new URL(PATH);
		final InputStream in = url.openStream();
		
		// in 表示输入流, out 表示输出流, buffSize	表示缓冲大小, close 表示在传输结束后是否关闭流
		IOUtils.copyBytes(in, System.out, 1024, true);
	}
}</code></pre>
<p></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"><br></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">2. 使用FileSystem  api读写数据:</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"></p>
<pre><code class="language-java">public class App2 {
	static final String HDFS_PATH = "hdfs://zhangdongping:9000/"; 
	static final String DIR_PATH = "/d11"; 
	static final String FILE_PATH = "/d11/hello.txt"; 

	public static void main(String[] args) throws Exception {
		FileSystem fileSystem = getFileSystem();
		
		// 创建文件夹 hadoop fs -mkdir /d11
		mkdir(fileSystem);
		
		// 上传文件 hadoop fs -put src des
		putData(fileSystem);
		
		// 下载文件 hadoop fs -get src des
		getData(fileSystem);
		
		// 浏览文件夹 hadoop fs -ls /
		list(fileSystem);
		
		// 删除文件夹 hadoop fs -rm /d11/hello.txt
		remove(fileSystem);
	}
	
	public static FileSystem getFileSystem() throws Exception {
		return FileSystem.get(new URI(HDFS_PATH), new Configuration());
	}
	
	// 创建文件夹
	public static void mkdir(FileSystem fileSystem) throws Exception {
		fileSystem.mkdirs(new Path(DIR_PATH));
	}
	
	// 上传文件
	public static void putData(FileSystem fileSystem) throws Exception{
		final FSDataOutputStream out = fileSystem.create(new Path(FILE_PATH));
		final FileInputStream in = new FileInputStream("D:/readme.txt");
		IOUtils.copyBytes(in, out, 1024, true);
	}
	
	// 下载文件
	public static void getData(FileSystem fileSystem) throws Exception {
		final FSDataInputStream in = fileSystem.open(new Path(FILE_PATH));
		IOUtils.copyBytes(in, System.out, 1024, true);
	}
	
	// 列出文件
	public static void list(FileSystem fileSystem) throws IOException {
		final FileStatus[] listStatus = fileSystem.listStatus(new Path("/"));
		for (FileStatus fileStatus : listStatus) {
			String isDir = fileStatus.isDir() ? "文件夹" : "文件";
			final String permission = fileStatus.getPermission().toString();
			final short replication = fileStatus.getReplication();
			final long len = fileStatus.getLen();
			final String path = fileStatus.getPath().toString();
			System.out.println(isDir + "\t" + permission + "\t" + replication + "\t" + len + "\t" + path);
		}
	}
	
	// 删除文件
	public static void remove(FileSystem fileSystem) throws Exception {
		fileSystem.delete(new Path(DIR_PATH), true);
	}
}</code></pre><br><br><p></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"><span style="font-size:18px;color:rgb(255,255,255);background-color:rgb(102,102,102);">六. Hadoop2 RPC:</span></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">所谓的RPC(Remote Procedure Call) 就是不同进程之间的方法调用, 走的还是RMI</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">RPC它是一种通过网络从远程计算机程序上请求服务, 而不需要了解底层网络技术的协议, RPC跨越了传输层和应用层.</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">RPC采用客户机/服务器模式, 请求程序就是一个客户机, 而服务提供程序就是一个服务器. 首先, 客户机调用进程发送一个有进程参数的调用信息到服务进程, 然后等待应答程序. 在服务器端, 进程保持睡眠状态指导调用信息到达为止. 当一个调用信息到达, 服务器获得进程参数, 计算结果, 发送答复信息, 然后等待下一个调用信息, 最后, 客户端调用进程答复信息. 获得结果.</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">下面用一个程序模拟一下:</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;">1. 代理类和被代理类要实现同一个接口:</p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"></p>
<pre><code class="language-java">public interface Barty {
	public static final long versionID = 10010;
	public String sayHi(String name);
}</code></pre><br>
2. 服务端:
<p></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"></p>
<pre><code class="language-java">public class RPCServer implements Barty{
	public static void main(String[] args) throws Exception {
		Server server = new RPC.Builder(new Configuration())
			.setInstance(new RPCServer())
			.setBindAddress("192.168.8.100")
			.setPort(9527)
			.setProtocol(Barty.class)
			.build();
		server.start();
	}

	@Override
	public String sayHi(String name) {
		return "HI~" + name;
	}
}
</code></pre><br>
3. 客户端:
<p></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"></p>
<pre><code class="language-java">public class RPCClient {

	public static void main(String[] args) throws Exception {
		Barty proxy = RPC.getProxy(Barty.class, 10010,
				new InetSocketAddress("192.168.8.100", 9527), new Configuration());
		String sayHi = proxy.sayHi("tomcat");
		System.out.println(sayHi);
	}
}</code></pre><br><br><p></p>
<p style="font-family:'Comic Sans MS';font-size:14px;line-height:26px;"><br></p>
<br>            </div>
                </div>
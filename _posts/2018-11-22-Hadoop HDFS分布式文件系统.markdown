---
layout:     post
title:      Hadoop HDFS分布式文件系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">     引言：我们维护hadoop系统的时候，必不可少需要对HDFS分布式文件系统做操作，例如拷贝一个文件/目录，查看HDFS文件系统目录下的内容，删除HDFS文件系统中的内容（文件/目录），还有HDFS管理信息，单独启动停止 namenode  datanode  jobtracker  tasktracker等等一系列的操作。大家跟着做一遍呢！就会对HDFS的体系架构有更加深入的理解，不多说我们开始吧。<br>
环境<br>
操作系统：centos 6.0<br>
hadoop版本：0.20.2<br>
部署目录：/home/grid/hadoop-0.20.2<br>
名称节点元数据位置：/home/grid/hadoop-0.20.2/tmp<br>
数据节点数据位置：/home/grid/hadoop-0.20.2/data<br>
master节点：h1<br>
slave节点：h2 h4<br>
master 和 slave 节点进程状态 ，现在进程都在启动着<br>
[grid@h1 </span><a class="relatedlink" href="http://www.itpub.net/pubtree/?node=339" rel="nofollow"><span style="font-size:14px;">bi</span></a><span style="font-size:14px;">n]$ jps        <br>
5182 Jps<br>
4600 NameNode<br>
4791 JobTracker<br>
4728 SecondaryNameNode<br>
[grid@h2 ~]$ jps<br>
4471 Jps<br>
3935 DataNode<br>
4012 TaskTracker<br>
[grid@h4 ~]$ jps<br>
4001 TaskTracker<br>
4508 Jps<br>
3924 DataNode<br>
[grid@h1 grid]$ mkdir input                                               linux命令，创建一个input目录<br>
[grid@h1 grid]$ ll<br>
总用量 44<br>
drwxr-xr-x. 14 grid hadoop 4096  9月  2 20:29 hadoop-0.20.2<br>
drwxr-xr-x.  2 grid hadoop 4096  9月 16 19:56 input<br>
drwxr-xr-x.  7 grid hadoop 4096 10月 22 2009 vmware-tools-distrib<br>
drwxr-xr-x.  2 grid hadoop 4096  9月  1 13:55 公共的<br>
drwxr-xr-x.  2 grid hadoop 4096  9月  1 13:55 模板<br>
drwxr-xr-x.  2 grid hadoop 4096  9月  1 13:55 视频<br>
drwxr-xr-x.  2 grid hadoop 4096  9月  1 13:55 图片<br>
drwxr-xr-x.  2 grid hadoop 4096  9月  1 13:55 文档<br>
drwxr-xr-x.  2 grid hadoop 4096  9月  1 13:55 下载<br>
drwxr-xr-x.  2 grid hadoop 4096  9月  1 13:55 音乐<br>
drwxr-xr-x.  3 grid hadoop 4096  9月  2 18:50 桌面<br>
[grid@h1 grid]$ cd input/                                                 linux命令，进入目录<br>
[grid@h1 input]$ echo "hello leonarding" &gt; test1.txt                      linux命令，把字符串"hello leonarding"重定向到test1.txt<br>
[grid@h1 input]$ echo "hello world" &gt; test2.txt                           linux命令，把字符串"hello world"重定向到test2.txt<br>
[grid@h1 input]$ cat test1.txt                                            linux命令，查看test1.txt文件内容<br>
hello leonarding<br>
[grid@h1 input]$ cat test2.txt                                            linux命令，查看test2.txt文件内容<br>
hello world<br>
[grid@h1 input]$ cd ../hadoop-0.20.2/                                     linux命令，进入hadoop-0.20.2目录<br>
[grid@h1 input]$ cd /usr/java/jdk1.6.0_25/bin<br>
[grid@h1 bin]$ jps                                                        直接输入jps=java进程统计<br>
28037 NameNode                 名称节点<br>
32455 Jps<br>
28220 SecondaryNameNode        辅助名称节点<br>
28259 JobTracker               作业跟踪器<br><br><strong>1.把linux操作系统的input目录拷贝到hadoop文件系统中重命名为in目录<br></strong>[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -put ../input in           把linux操作系统的input目录拷贝到hadoop文件系统中重命名为in目录12/09/16 20:18:20 INFO hdfs.DFSClient: Exception in createBlockOutputStream java.io.IOException: Bad connect ack with firstBadLink 192.168.2.103:50010 
                                               没有关闭防火墙<br>
12/09/16 20:18:20 INFO hdfs.DFSClient: Abandoning block blk_-2034091982680781464_102612/09/16 20:18:26 INFO hdfs.DFSClient: Exception in createBlockOutputStream java.io.IOException: Bad connect ack withfirstBadLink192.168.2.103:5001012/09/16 20:18:26 INFO hdfs.DFSClient:
 Abandoning block blk_-3493343259834508347_102612/09/16 20:18:32 INFO hdfs.DFSClient: Exception in createBlockOutputStream java.io.IOException: Bad connect ack with firstBadLink 192.168.2.103:50010<br>
12/09/16 20:18:32 INFO hdfs.DFSClient: Abandoning block blk_4302532573839164645_1026<br>
12/09/16 20:18:38 INFO hdfs.DFSClient: Exception in createBlockOutputStream java.net.NoRouteToHostException: No route to host12/09/16 20:18:38 INFO hdfs.DFSClient: Abandoning block blk_4602162403828631999_1026<br>
12/09/16 20:18:44 WARN hdfs.DFSClient: DataStreamer Exception: java.io.IOException: Unable to create new block.at org.apache.hadoop.hdfs.DFSClient$DFSOutputStream.nextBlockOutputStream(DFSClient.java:2845) at org.apache.hadoop.hdfs.DFSClient$DFSOutputStream.access$2000(DFSClient.java:2102)
 at org.apache.hadoop.hdfs.DFSClient$DFSOutputStream$DataStreamer.run(DFSClient.java:2288)12/09/16 20:18:44 WARN hdfs.DFSClient: Error Recovery for block blk_4602162403828631999_1026 bad datanode[0] nodes == null12/09/16 20:18:44 WARN hdfs.DFSClient: Could
 not get block locations. Source file "/user/grid/in/input/test2.txt" - Aborting...<br>
put: No route to host12/09/16 20:18:44 ERROR hdfs.DFSClient: Exception closing file /user/grid/in/input/test2.txt : java.net.NoRouteToHostException: No route to hostjava.net.NoRouteToHostException: No route to host at sun.nio.ch.SocketChannelImpl.checkConnect(Natve
 Method) at sun.nio.ch.SocketChannelImpl.finishConnect(SocketChannelImpl.java:567) at org.apache.hadoop.net.SocketIOWithTimeout.connect(SocketIOWithTimeout.java:206) at org.apache.hadoop.net.NetUtils.connect(NetUtils.java:404) at org.apache.hadoop.hdfs.DFSClient$DFSOutputStream.createBlockOutputStream(DFSClient.java:2870)
 at org.apache.hadoop.hdfs.DFSClient$DFSOutputStream.nextBlockOutputStream(DFSClient.java:2826) atorg.apache.hadoop.hdfs.DFSClient$DFSOutputStream.access$2000(DFSClient.java:2102) at org.apache.hadoop.hdfs.DFSClient$DFSOutputStream$DataStreamer.run(DFSClient.java:2288)<br>
小结：这是没有关闭Linux防火墙造成的，使用 service iptables stop/start  临时关闭/开启  chkconfig iptables off/on  永久关闭/开启防火墙命令来控制防火墙，关闭之后就可以顺利的进行下面操作了。<br><br>
[grid@h1 grid]$ hadoop dfs -put abc.txt abc                               把linux操作系统东西拷贝到hadoop文件系统中并重命名abc<br>
[grid@h1 grid]$ hadoop dfs -ls                                            abc文件已经在hadoop文件系统根目录下了<br>
Found 3 items<br>
-rw-r--r--   2 grid supergroup         44 2012-09-18 19:12 /user/grid/abc 随机存放到某一个节点<br>
drwxr-xr-x   - grid supergroup          0 2012-09-17 19:44 /user/grid/in<br>
drwxr-xr-x   - grid supergroup          0 2012-09-17 20:41 /user/grid/out<br><br><strong>2.查看hadoop文件系统中根目录下in子目录中所有内容<br></strong><br>
[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -ls                                           查看hadoop文件系统根目录内容<br>
Found 1 items<br>
drwxr-xr-x   - grid supergroup          0 2012-09-17 19:44 /user/grid/in              只有一个in目录<br>
[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -ls ./in/*                                    查看hadoop文件系统中根目录下in子目录内容<br>
-rw-r--r--   2 grid supergroup         17 2012-09-17 19:44 /user/grid/in/test1.txt    有 2个 文件<br>
-rw-r--r--   2 grid supergroup         12 2012-09-17 19:44 /user/grid/in/test2.txt<br>
小结：Hadoop没有当前目录的概念，当然也不能进入in目录，更没有cd命令。所以查看时必须输入目录路径<br><br><strong>3.测试map_reduce系统是否可以正常工作，map reduce 采用“就近分配节点”原则执行数据<br></strong>jar包：/home/grid/hadoop-0.20.2/hadoop-0.20.2-examples.jar       安装hadoop时从源代码拷贝过来的，我们可以直接使用<br>
[grid@h1 hadoop-0.20.2]$ bin/hadoop jar hadoop-0.20.2-examples.jar wordcount in out   把这个jar包里wordcount功能提交给map_reduce当做一个作业运行，测试map_reduce系统是否可以正常工作，in  输入数据目录（数据源） out 输出数据目录（即输出到哪里）<br>
12/09/17 20:39:06 INFO input.FileInputFormat: Total input paths to process : 2<br>
12/09/17 20:39:07 INFO mapred.JobClient: Running job: job_201209172027_0002        运行作业号“2012年9月17日1856不是时间”<br>
12/09/17 20:39:08 INFO mapred.JobClient:  map 0% reduce 0%                        <br>
12/09/17 20:40:34 INFO mapred.JobClient:  map 50% reduce 0%<br>
12/09/17 20:40:49 INFO mapred.JobClient:  map 100% reduce 0%                       map  reduce进度<br>
12/09/17 20:41:02 INFO mapred.JobClient:  map 100% reduce 100%<br>
12/09/17 20:41:04 INFO mapred.JobClient: Job complete: job_201209172027_0002       作业完成<br>
12/09/17 20:41:04 INFO mapred.JobClient: Counters: 17<br>
12/09/17 20:41:04 INFO mapred.JobClient:   Job Counters                            作业计数器<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Launched reduce tasks=1               启动reduce任务1个<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Launched map tasks=3                  启动map任务3个<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Data-local map tasks=3<br>
12/09/17 20:41:04 INFO mapred.JobClient:   FileSystemCounters                      文件系统计数器<br>
12/09/17 20:41:04 INFO mapred.JobClient:     FILE_BYTES_READ=59<br>
12/09/17 20:41:04 INFO mapred.JobClient:     HDFS_BYTES_READ=29<br>
12/09/17 20:41:04 INFO mapred.JobClient:     FILE_BYTES_WRITTEN=188<br>
12/09/17 20:41:04 INFO mapred.JobClient:     HDFS_BYTES_WRITTEN=29<br>
12/09/17 20:41:04 INFO mapred.JobClient:   Map-Reduce Framework                    map_reduce框架<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Reduce input groups=3                 reduce输入组3<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Combine output records=4              合并输出记录4<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Map input records=2                   map输入记录2<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Reduce shuffle bytes=65               reduce shuffle=预处理 减少计算量 算的更快<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Reduce output records=3               reduce输出记录3<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Spilled Records=8                     溢出记录8<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Map output bytes=45                   map输出字节45<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Combine input records=4               合并输入记录4<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Map output records=4                  map输出记录4<br>
12/09/17 20:41:04 INFO mapred.JobClient:     Reduce input records=4                reduce输入记录4<br>
浏览器：</span><a href="http://192.168.2.102:50030/jobtracker.jsp" rel="nofollow"><span style="font-size:14px;color:#006699;">http://192.168.2.102:50030/jobtracker.jsp</span></a><span style="font-size:14px;">   这里有job更详细的信息<br>
小结：报错信息【org.apache.hadoop.util.DiskChecker$DiskErrorException: Could no find taskTracker/jobcache/job_201209171856_0001/attempt_201209171856_0001_m_000000_0/output/file.out.index in any of the configured local directories】  请执行stop-all.sh -&gt; start-all.sh   
 重启hadoop所有进程<br><br><strong>4.列出根目录下所有内容<br></strong>[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -ls<br>
Found 2 items<br>
drwxr-xr-x   - grid supergroup          0 2012-09-17 19:44 /user/grid/in<br>
drwxr-xr-x   - grid supergroup          0 2012-09-17 19:56 /user/grid/out<br>
  列出根目录下out子目录所有内容<br>
[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -ls ./out/<br>
Found 2 items<br>
drwxr-xr-x   - grid supergroup          0 2012-09-17 20:39 /user/grid/out/_logs<br>
-rw-r--r--   2 grid supergroup         29 2012-09-17 20:40 /user/grid/out/part-r-00000<br><br><strong>5.查看part-r-00000文件内容<br></strong>[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -cat /user/grid/out/part-r-00000      必须写绝对路径，因为hadoop下没有当前目录概念<br>
hello 2                     统计单词出现2次<br>
leonarding 1             统计单词出现1次<br>
world 1                     统计单词出现1次<br>
小结：cat 对象必须是文件，不能对目录查看<br><br><strong>6.删除hadoop文件系统文件和目录<br></strong>必须选项 -rmr   不能-rm   use -rmr instead<br>
[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -rmr /user/grid/in/input/test2.txt     删除hadoop文件系统里的test2.txt文件，一定要加目录路径<br>
Deleted hdfs://h1:9000/user/grid/in/input/test2.txt<br>
[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -rmr /user/grid/in/test2.txt           删除hadoop文件系统文件，一定要加目录路径<br>
Deleted hdfs://h1:9000/user/grid/in/test2.txt<br>
[grid@h1 hadoop-0.20.2]$ bin/hadoop dfs -rmr /user/grid/in                     删除in目录<br>
Deleted hdfs://h1:9000/user/grid/in<br>
h2  h4  的Linux操作系统上面看文件是不是已经被删除了，占用block已经被释放了<br>
小结：删除HDFS文件系统内文件和目录时选项必须要写 -rmr  而不能 -rm哦<br><br><strong>7.从Linux操作系统上看hadoop文件存放在哪里   数据只会存放在数据节点  h2  h4，而不会存放在名称节点 h1</strong></span></p>
<p><strong>[<span style="font-size:18px;">g</span></strong><a href="mailto:rid@h4" rel="nofollow"><span style="font-size:18px;color:#006699;"><strong>rid@h4</strong></span></a><span style="font-size:14px;"><strong><span style="font-size:18px;"> current]$ pwd<br></span></strong>     /home/grid/hadoop-0.20.2/data/current<br>
[grid@h4 current]$ ll                 h4节点<br>
总用量 224<br>
-rw-r--r--. 1 grid hadoop     17  9月 17 19:44 blk_1740766816993665795<br>
-rw-r--r--. 1 grid hadoop     11  9月 17 19:44 blk_1740766816993665795_1013.meta<br>
-rw-r--r--. 1 grid hadoop   9151  9月 17 20:41 blk_5787152218309601238<br>
-rw-r--r--. 1 grid hadoop     79  9月 17 20:41 blk_5787152218309601238_1026.meta<br>
-rw-r--r--. 1 grid hadoop 142466  9月 17 20:35 blk_-5912167526685784374<br>
-rw-r--r--. 1 grid hadoop   1123  9月 17 20:35 blk_-5912167526685784374_1020.meta<br>
-rw-r--r--. 1 grid hadoop      4  9月 17 20:28 blk_618795584037082806<br>
-rw-r--r--. 1 grid hadoop     11  9月 17 20:28 blk_618795584037082806_1019.meta<br>
-rw-r--r--. 1 grid hadoop     29  9月 17 20:40 blk_-6836333776136840432<br>
-rw-r--r--. 1 grid hadoop     11  9月 17 20:40 blk_-6836333776136840432_1026.meta<br>
-rw-r--r--. 1 grid hadoop     12  9月 17 19:44 blk_7008012568223069759<br>
-rw-r--r--. 1 grid hadoop     11  9月 17 19:44 blk_7008012568223069759_1012.meta<br>
-rw-r--r--. 1 grid hadoop  16737  9月 17 20:39 blk_8327174067432266416<br>
-rw-r--r--. 1 grid hadoop    139  9月 17 20:39 blk_8327174067432266416_1025.meta<br>
-rw-r--r--. 1 grid hadoop   1734  9月 18 07:51 dncp_block_verification.log.curr<br>
-rw-r--r--. 1 grid hadoop    158  9月 17 20:27 VERSION<br>
[grid@h2 current]$ ll                 h2节点<br>
总用量 224<br>
-rw-r--r--. 1 grid hadoop     17  9月 17 19:44 blk_1740766816993665795<br>
-rw-r--r--. 1 grid hadoop     11  9月 17 19:44 blk_1740766816993665795_1013.meta<br>
-rw-r--r--. 1 grid hadoop   9151  9月 17 20:41 blk_5787152218309601238<br>
-rw-r--r--. 1 grid hadoop     79  9月 17 20:41 blk_5787152218309601238_1026.meta<br>
-rw-r--r--. 1 grid hadoop 142466  9月 17 20:35 blk_-5912167526685784374<br>
-rw-r--r--. 1 grid hadoop   1123  9月 17 20:35 blk_-5912167526685784374_1020.meta<br>
-rw-r--r--. 1 grid hadoop      4  9月 17 20:28 blk_618795584037082806<br>
-rw-r--r--. 1 grid hadoop     11  9月 17 20:28 blk_618795584037082806_1019.meta<br>
-rw-r--r--. 1 grid hadoop     29  9月 17 20:40 blk_-6836333776136840432<br>
-rw-r--r--. 1 grid hadoop     11  9月 17 20:40 blk_-6836333776136840432_1026.meta<br>
-rw-r--r--. 1 grid hadoop     12  9月 17 19:44 blk_7008012568223069759<br>
-rw-r--r--. 1 grid hadoop     11  9月 17 19:44 blk_7008012568223069759_1012.meta<br>
-rw-r--r--. 1 grid hadoop  16737  9月 17 20:39 blk_8327174067432266416<br>
-rw-r--r--. 1 grid hadoop    139  9月 17 20:39 blk_8327174067432266416_1025.meta<br>
-rw-r--r--. 1 grid hadoop   1541  9月 18 07:51 dncp_block_verification.log.curr<br>
-rw-r--r--. 1 grid hadoop    158  9月 17 20:27 VERSION<br>
小结：hadoop数据只在datanode节点保存（h2 h4）不在namenode保存，由于我设置了 数据块复制2份，那么在h2 h4 里面文件都是一模一样的（冗余防错）hdfs_site.xml -&gt; &lt;name&gt;dfs.data.dir&lt;/name&gt;指定数据节点存放数据位置 /home/grid/hadoop-0.20.2/data/<br>
hadoop一个文件是由数据块组成，容量最大不超过64M，是由数据和元数据组成。<br>
datanode文件采用一次性写入多次读，不需修改，可以删除在重新写入<br>
NAMENODE介绍<br>
namenode节点中保存是映像文件和事务日志，即元数据，如果元数据被破坏了，那么我们整个HDFS系统就崩溃了<br>
映像文件：存放文件系统命名空间 ，例如 文件映像  文件属性<br>
事务日志：存放HDFS元数据记录<br>
什么是元数据：记录每个文件数据块在各个datanode上位置和副本（文件数据块都放在哪个节点上，副本有几个），元数据也可以复制多个副本，只在namenode里添加副本，缺点就是副本越多，空间利用率越小，安全性越大，速度越慢<br>
namenode冗余：namenode是HDFS文件系统总控节点，但它是一个单点，如果出现故障也需要手动切换到secondarynamenode SNN<br>
namenode不参与实际数据传输，只负责元数据查询<br>
namenode元数据位置：<br>
[grid@h1 current]$ pwd<br>
/home/grid/hadoop-0.20.2/tmp/dfs/name/current<br>
[grid@h1 current]$ ll<br>
总用量 16<br>
-rw-r--r--. 1 grid hadoop    4  9月 18 08:58 edits      事务日志<br>
-rw-r--r--. 1 grid hadoop 2130  9月 18 08:58 fsimage    映像文件<br>
-rw-r--r--. 1 grid hadoop    8  9月 18 08:58 fstime     事务日志<br>
-rw-r--r--. 1 grid hadoop  101  9月 18 08:58 VERSIO<br><br><strong>8.hadoop文件系统的管理报告，即HDFS基本统计信息<br></strong>[grid@h4 ~]$ hadoop dfsadmin -report            h4 节点<br>
Configured Capacity: 19865944064 (18.5 GB)      分配总容量<br>
Present Capacity: 8934457344 (8.32 GB)          <br>
DFS Remaining: 8933957632 (8.32 GB)             DFS剩余容量<br>
DFS Used: 499712 (488 KB)                       DFS已使用容量<br>
DFS Used%: 0.01%                                DFS已使用百分比<br>
Under replicated blocks: 1                      复制成功块数<br>
Blocks with corrupt replicas: 0                 复制失败块数<br>
Missing blocks: 0                               丢失块数<br>
-------------------------------------------------<br>
Datanodes available: 2 (2 total, 0 dead)        数据节点有效<br>
Name: 192.168.2.103:50010                       h2数据节点ip和端口<br>
Decommission Status : Normal                    状态正常<br>
Configured Capacity: 9932972032 (9.25 GB)       配置总容量<br>
DFS Used: 249856 (244 KB)                       DFS使用量<br>
Non DFS Used: 5350871040 (4.98 GB)              linux容量非DFS使用的<br>
DFS Remaining: 4581851136(4.27 GB)              DFS剩余容量<br>
DFS Used%: 0%                                   DFS已使用百分比<br>
DFS Remaining%: 46.13%                          DFS未用百分比<br>
Last contact: Tue Sep 18 19:34:32 CST 2012      最后的联接<br><br>
Name: 192.168.2.105:50010                       h2数据节点ip和端口<br>
Decommission Status : Normal                    状态正常<br>
Configured Capacity: 9932972032 (9.25 GB)       配置总容量<br>
DFS Used: 249856 (244 KB)                       DFS使用量<br>
Non DFS Used: 5580615680 (5.2 GB)               linux容量非DFS使用的<br>
DFS Remaining: 4352106496(4.05 GB)              DFS剩余容量<br>
DFS Used%: 0%                                   DFS已使用百分比<br>
DFS Remaining%: 43.81%                          DFS未用百分比<br>
Last contact: Tue Sep 18 19:34:32 CST 2012      最后的联接<br><br><strong>9.单独启动停止 namenode  datanode  jobtracker  tasktracker<br></strong>单独启动/停止 namenode      start-dfs.sh/stop-dfs.sh<br>
单独启动/停止 datanode      hadoop-daemon.sh start datanode/hadoop-daemon.sh stop datanode<br>
单独启动/停止 jobtracker    hadoop-daemon.sh start tasktracker/hadoop-daemon.sh stop tasktracker<br>
单独启动/停止 tasktracker   start-mapred.sh/stop-mapred.sh<br>
举例<br>
[grid@h4 bin]$ pwd<br>
/home/grid/hadoop-0.20.2/bin<br>
[grid@h4 bin]$ jps<br>
27736 Jps<br>
17119 DataNode<br>
17230 TaskTracker<br>
[grid@h4 bin]$ hadoop-daemon.sh stop tasktracker         停止tasktracker<br>
stopping tasktracker<br>
[grid@h4 bin]$ jps<br>
27783 Jps<br>
17119 DataNode<br>
[grid@h4 bin]$ hadoop-daemon.sh start tasktracker        启动tasktracker<br>
starting tasktracker, logging to /home/grid/hadoop-0.20.2/bin/../logs/hadoop-grid-tasktracker-h4.out<br>
[grid@h4 bin]$ jps<br>
27829 TaskTracker                                        已经重新启动，pid都改变了<br>
17119 DataNode<br>
27868 Jps<br>
[grid@h4 bin]$ pwd<br>
/home/grid/hadoop-0.20.2/bin<br><br><strong>10.均衡负载<br></strong>[grid@h4 bin]$ start-balancer.sh<br>
starting balancer, logging to /home/grid/hadoop-0.20.2/bin/../logs/hadoop-grid-balancer-h4.out<br>
场合：新增节点 或 节点故障  把所有数据块重新均衡负载到各各节点上，包括新增节点。例如 几个G数据，均衡负载十几分钟<br><br></span></p>
            </div>
                </div>
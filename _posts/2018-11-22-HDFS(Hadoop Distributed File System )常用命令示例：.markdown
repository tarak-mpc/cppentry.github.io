---
layout:     post
title:      HDFS(Hadoop Distributed File System )常用命令示例：
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-java">hadoop fs -mkdir /user/trunk
hadoop fs -ls /user
hadoop fs -lsr /user   (递归的)
hadoop fs -put test.txt /user/trunk
hadoop fs -put test.txt .  (复制到hdfs当前目录下，首先要创建当前目录)
hadoop fs -get /user/trunk/test.txt . (复制到本地当前目录下)
hadoop fs -cat /user/trunk/test.txt
hadoop fs -tail /user/trunk/test.txt  (查看最后1000字节)
hadoop fs -rm /user/trunk/test.txt
hadoop fs -rmr /home/less/hadoop/tmp/output  删除所有文件
 hadoop fs -help ls (查看ls命令的帮助文档)


hadoop fs -mkdir /tmp/input              在HDFS上新建文件夹
 
hadoop fs -mkdir  /test
hadoop fs -mkdir  /test
  
  
------------将本地磁盘文件存放到hdfs文件系统中------------------

 
hadoop fs -put input1.txt /tmp/input  把本地文件input1.txt传到HDFS的/tmp/input目录下

hadoop fs -get  input1.txt /tmp/input/input1.txt  把HDFS文件拉到本地

hadoop fs -ls /tmp/output                  列出HDFS的某目录

hadoop fs -cat /tmp/ouput/output1.txt  查看HDFS上的文件

hadoop fs -rmr /home/less/hadoop/tmp/output  删除HDFS上的目录

hadoop dfsadmin -report 查看HDFS状态，比如有哪些datanode，每个datanode的情况

hadoop dfsadmin -safemode leave  离开安全模式

hadoop dfsadmin -safemode enter  进入安全模式

    
--------hdfs内部复制文件命令

    
./hadoop fs -cp /input/jdk.rpm /test

hadoop fs -cp /input/jdk.rpm /test1

hadoop fs -cp /input/jdk.rpm /test2

hadoop fs -cp /input/jdk.rpm /test3


--------Snapshot 基本操作------------------------


对一个路径开启Snapshot： hdfs dfsadmin -allowSnapshot &lt;path&gt;

关闭 Snapsshots： hdfs dfsadmin -disallowSnapshot &lt;path&gt;

创建Snapshosts：hdfs dfs -createSnapsshot &lt;path&gt; [snapshot names]

删除Snapshots：hdfs dfs -deleteSnaphost &lt;path&gt; &lt;snapshotName&gt;

修改Snapshots的名字：hdfs dfs -renameSnapshot &lt;path&gt;  &lt;oldname&gt; &lt;newname&gt;

获取Snapshot 列表：hdfs lsSnapshottableDir

获取两个Snapshot的不同：hdfs snapsshotDiff &lt;path&gt; &lt;fromSnapshot&gt; &lt;toSnapshot&gt;



----先要开启snapshot功能,才能snapshot镜像


./hdfs dfsadmin -allowSnapshot  /test


./hdfs dfsadmin -allowSnapshot  /test1

./hdfs dfsadmin -allowSnapshot  /test123

./hdfs dfsadmin -allowSnapshot  /test2


-----------------------创建snapshot镜像

hdfs dfs -createSnapshot /test s1


./hdfs dfs -createSnapshot /test s1
./hdfs dfs -createSnapshot /test s2


./hdfs dfs -createSnapshot /test123 s2

./hdfs dfs -createSnapshot /test1 s3




----删除snapshot镜像


hdfs dfs -deleteSnaphost &lt;path&gt; &lt;snapshotName&gt;
hdfs dfs -deleteSnaphost  /test s1

hdfs dfs -deleteSnaphost  /test s2

-deleteSnapshot



------------------------DFS Snapshot


用于数据备份、回滚，以防止因用户的失误操作导致集群出现问题。


DFS Snapshot有以下几个特性： 

Snapshot 创建的时间 复杂度为O(1)，但是不包括INode 的寻找时间
只有当修改SnapShot时，才会有额外的内存占用，内存使用量为O(M),M 为修改的文件或者目录数
在datanode 上面的blocks 不会复制，做Snapshot 的文件是纪录了block的信息
Snapshot 并不会影响HDFS 的正常操作
产生了以下新的概念： 

Snapshot table：Snapshots 会存储在snapshottable的目录下。snapshottable下存储的snapshots 最多为65535个
Snapshot 路径：举例，假设/foo  是snapshottable，/foo/bar 是文件目录,/foo 拥有一个s0的snapshot，那么路径会是 /foo/.snapshot/s0/bar，我们可以通过
      hdfs dfs -ls /foo/.snapshot 
      hdfs dfs -ls /foo/.snapshot/s0 
      hdfs dfs -cp /foo/.snapshot/s0/bar /tmp 

来操作与查看副本文件。
Snapshot 基本操作： 

对一个路径开启Snapshot： hdfs dfsadmin -allowSnapshot &lt;path&gt;
关闭 Snapsshots： hdfs dfsadmin -disallowSnapshot &lt;path&gt;
创建Snapshosts：hdfs dfs -createSnapsshot &lt;path&gt; [snapshot names]
删除Snapshots：hdfs dfs -deleteSnaphost &lt;path&gt; &lt;snapshotName&gt;
修改Snapshots的名字：hdfs dfs -renameSnapshot &lt;path&gt;  &lt;oldname&gt; &lt;newname&gt;
获取Snapshot 列表：hdfs lsSnapshottableDir
获取两个Snapshot的不同：hdfs snapsshotDiff &lt;path&gt; &lt;fromSnapshot&gt; &lt;toSnapshot&gt;



--------------------------------------


----获取snapshot列表



./hdfs lsSnapshottableDir


--获取两个snapshot之间的不同


./hdfs snapsshotDiff  /test s1 s2

./hdfs dfs -createSnapshot /test s1
./hdfs dfs -createSnapshot /test s2


hdfs snapsshotDiff &lt;path&gt; &lt;fromSnapshot&gt; &lt;toSnapshot&gt;





创建f1,f2,f3三个文件

hdfs dfs -touchz /input/f{1,2,3}
#新建快照s1

hdfs dfs -createSnapshot /input s1


#此时当前文件系统和s1中都包含f1,f2,f3三个文件

hdfs dfs -ls -R /input

drwxr-xr-x   - hdfs supergroup          0 2014-12-20 10:45 /user/spark/tmp
-rw-r--r--   3 hdfs supergroup          0 2014-12-20 10:45 /user/spark/tmp/f1
-rw-r--r--   3 hdfs supergroup          0 2014-12-20 10:45 /user/spark/tmp/f2
-rw-r--r--   3 hdfs supergroup          0 2014-12-20 10:45 /user/spark/tmp/f3

#删除f3

hdfs dfs -rm /input/f3
 
14/12/20 10:46:09 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 1440 minutes, Emptier interval = 0 minutes.
Moved: 'hdfs://hdp01:8020/user/spark/tmp/f3' to trash at: hdfs://hdp01:8020/user/hdfs/.Trash/Current

#查看快照内容,可以发现当前文件系统已经没有f3，而快照s1还有f3文件存在。这样，通过拷贝s1下的f3文件就可以进行恢复。

hdfs dfs -ls -R /input/.snapshot




hdfs dfs -ls -R /input




#RO的快照不能被修改，目前还没有找到RW的实现方法=。=

hdfs dfs -touchz /user/spark/.snapshot/s1/f4

hdfs dfs -touchz /input/.snapshot/s1/f4





此外，另外几个常用的命令：

(1). 修改snapshot名称: hdfs dfs -renameSnapshot 


例如：将s0修改为s_init



hdfs dfs -renameSnapshot /input s1 s_init
 
hdfs dfs -ls /input/.snapshot




(2). 通过hdfs lsSnapshottableDir 来列出snapshottable的目录


hdfs lsSnapshottableDir 


(3). 比较两个快照之间的差异：hdfs snapshotDiff


hdfs snapshotDiff /user/spark s_init s1

hdfs snapshotDiff /input s_init s1



Difference between snapshot s_init and snapshot s1 under directory /user/spark:
M	./tmp
+	./tmp/f1
+	./tmp/f2
+	./tmp/f3
1
2
3
4
5
6
hdfs snapshotDiff /user/spark s_init s1
Difference between snapshot s_init and snapshot s1 under directory /user/spark:
M	./tmp
+	./tmp/f1
+	./tmp/f2
+	./tmp/f3


(4). 删除快照：hdfs dfs -deleteSnaphost


hdfs dfs -deleteSnapshot  /user/spark s_init
1
hdfs dfs -deleteSnapshot  /user/spark s_init
(5). 关闭 Snapsshots： hdfs dfsadmin -disallowSnapshot


hdfs dfsadmin -disallowSnapshot /user/spark

hdfs dfsadmin -disallowSnapshot /user/spark

通过Snapshot可以按照定时任务，按固定时间间隔（例如每天）的方式创建文件快照，并删除过期的文件快照，减少业务误操作造成的数据损失。

快照的操作远低于外部备份开销，可作为我们备份HDFS系统最常用的方式。














---hdfs授权



 ./hadoop -fs  -chmod 777 /wc



hadoop fs  -chmod 777  /


hadoop fs  -chmod 777  /input

</code></pre>
<p><br></p>
<p></p>
<pre><code class="language-java">BOOTPROTO=static
NAME=eno16777736
DEVICE=eno16777736
IPADDR=192.168.120.120
ONBOOT=yes
NAME=eno16777736</code></pre><br><br><p></p>
            </div>
                </div>
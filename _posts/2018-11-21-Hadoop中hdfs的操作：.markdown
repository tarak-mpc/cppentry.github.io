---
layout:     post
title:      Hadoop中hdfs的操作：
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-html">Hadoop中hdfs的操作：

HDFS命令示例：

hadoop fs -mkdir /user/trunk
hadoop fs -ls /user
hadoop fs -lsr /user   (递归的)
hadoop fs -put test.txt /user/trunk
hadoop fs -put test.txt .  (复制到hdfs当前目录下，首先要创建当前目录)
hadoop fs -get /user/trunk/test.txt . (复制到本地当前目录下)
hadoop fs -cat /user/trunk/test.txt
hadoop fs -tail /user/trunk/test.txt  (查看最后1000字节)
hadoop fs -rm /user/trunk/test.txt
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





---hdfs授权



./hadoop -fs  -chmod 777 /wc



hadoop fs  -chmod 777  /


hadoop fs  -chmod 777  /input

</code></pre><br>            </div>
                </div>
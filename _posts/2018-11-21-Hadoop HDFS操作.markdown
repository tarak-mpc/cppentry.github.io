---
layout:     post
title:      Hadoop HDFS操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Hadoop HDFS操作<br><br><br>
启动HDFS<br><br><br>
首先，格式化配置HDFS文件系统，打开NameNode(HDFS服务器)，然后执行以下命令。<br><br><br>
$ hadoop namenode -format <br>
格式化HDFS后，启动分布式文件系统。以下命令将启动名称节点和数据节点的集群。<br><br><br>
$ start-dfs.sh <br>
HDFS的文件列表<br><br><br>
加载服务器信息后，使用'ls' 可以找出文件列表中的目录，文件状态。下面给出的是ls，可以传递一个目录或文件名作为参数的语法。<br><br><br><br><br>
 <br>
$ $HADOOP_HOME/bin/hadoop fs -ls &lt;args&gt;<br>
将数据插入到HDFS<br><br><br>
假设在本地系统，这是所谓的file.txt文件中的数据,应当保存在HDFS文件系统。按照下面给出插入在Hadoop的文件系统所需要的文件的步骤。<br><br><br>
第1步<br><br><br>
必须创建一个输入目录。<br><br><br>
$ $HADOOP_HOME/bin/hadoop fs -mkdir /user/input <br>
第2步<br><br><br>
传输并使用本地系统put命令，Hadoop文件系统中存储的数据文件。<br><br><br>
$ $HADOOP_HOME/bin/hadoop fs -put /home/file.txt /user/input <br>
第3步<br><br><br>
可以使用ls命令验证文件。<br><br><br>
$ $HADOOP_HOME/bin/hadoop fs -ls /user/input <br>
从HDFS中检索数据<br><br><br>
假设在HDFS文件名为outfile。下面给出的是一个简单的演示用于检索从Hadoop文件系统所需的文件。<br><br><br>
第1步<br><br><br>
最初，使用cat命令来查看来自HDFS的数据。<br><br><br>
$ $HADOOP_HOME/bin/hadoop fs -cat /user/output/outfile <br>
第2步<br><br><br>
从HDFS得到文件使用get命令在本地文件系统。<br><br><br>
$ $HADOOP_HOME/bin/hadoop fs -get /user/output/ /home/hadoop_tp/ <br>
关闭HDFS<br><br><br>
可以使用下面的命令关闭HDFS。<br><br><br>
$ stop-dfs.sh 
            </div>
                </div>
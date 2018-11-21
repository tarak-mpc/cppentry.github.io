---
layout:     post
title:      hadoop入门：hadoop使用shell命令总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
第一部分：Hadoop Bin<br>
后面根据项目的实际需要Hadoop Bin  包括:<br>
Hadoop  hadoop的Shell<br>
hadoop-config.sh 它的作用是对一些变量进行赋值<br>
     HADOOP_HOME（hadoop的安装目录）。<br>
     HADOOP_CONF_DIR（hadoop的配置文件目录）。HADOOP_SLAVES（--hosts指定的文件的地址）<br>
hadoop-daemon.sh 单节点启动<br>
hadoop-daemons.sh 启动slaves.sh和hadoop-daemon.sh<br>
start-all.sh    启动HDFS与MapReduce<br>
start-balancer.sh 启动Hadoop的负载均衡<br>
start-dfs.sh  启动HDFS<br>
start-jobhistoryserver.sh<br>
start-mapred.sh  启动MapReduce<br>
来进行调整。<br>
后面根据项目的实际需要Hadoop Bin  包括:<br>
Hadoop  hadoop的Shell<br>
hadoop-config.sh 它的作用是对一些变量进行赋值<br>
     HADOOP_HOME（hadoop的安装目录）。<br>
     HADOOP_CONF_DIR（hadoop的配置文件目录）。<br>
     HADOOP_SLAVES（--hosts指定的文件的地址）<br>
hadoop-daemon.sh 单节点启动<br>
hadoop-daemons.sh 在所有slaves上运行相同的脚本hadoop-daemon.sh<br>
start-all.sh    启动HDFS与MapReduce<br>
start-balancer.sh 启动Hadoop的负载均衡<br>
start-dfs.sh  启动HDFS<br>
start-jobhistoryserver.sh<br>
start-mapred.sh  启动MapReduce<br>
来进行调整。<br>
stop-all.sh   停止HDFS与MapReduce<br>
stop-balancer.sh 停止做负载均衡<br>
stop-dfs.sh   停止HDFS<br>
stop-jobhistoryserver.sh 停止Job追踪<br>
stop-mapred.sh 停止MapReduce<br>
task-controller<br>
第二部分：Hadoop Shell 基本操作<br><br>
nHadoop shell<br>
  包括:
<div id="code_p68">
<ol><li>namenode -format     format the DFS filesystem<br></li><li>secondarynamenode    run the DFS secondary namenode<br></li><li>namenode             run the DFS namenode<br></li><li>datanode             run a DFS datanode<br></li><li>dfsadmin             run a DFS admin client<br></li><li>mradmin              run a Map-Reduce admin client<br></li><li>fsck                 run a DFS filesystem checking utility<br></li><li>fs                   run a generic filesystem user client<br></li><li>balancer             run a cluster balancing utility<br></li><li>fetchdt              fetch  delegation token from the NameNode<br></li><li>jobtracker           run the MapReduce job Tracker node<br></li><li>pipes                run a Pipes job<br></li><li>tasktracker          run a MapReduce task Tracker node<br></li><li>historyserver        run job history servers as a standalone daemon<br></li><li>job                  manipulate MapReduce jobs<br></li><li>queue                get information regarding JobQueues<br></li><li>version              print the version<br></li><li>jar &lt;jar&gt;            run a jar file<br></li><li>distcp &lt;srcurl&gt; &lt;desturl&gt; copy file or directories recursively<br></li><li>archive -archiveName NAME -p &lt;parent path&gt; &lt;src&gt;* &lt;dest&gt; create a hadoop archive<br></li><li>classpath            prints the class path needed to get the<br></li><li>                           Hadoop jar and the required libraries<br></li><li>daemonlog            get/set the log level for each daemon<br></li><li>or<br></li><li>CLASSNAME            run the class named CLASSNAME</li></ol></div>
            </div>
                </div>
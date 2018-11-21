---
layout:     post
title:      Hadoop实战-初级部分 之 Hadoop Shell 基本操作介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>第一部分：Hadoop Bin </div>
<div>
<div class="O">
<div>后面根据项目的实际需要Hadoop Bin  包括: </div>
<div>Hadoop  hadoop的Shell </div>
<div>hadoop-config.sh 它的作用是对一些变量进行赋值 </div>
<div>     HADOOP_HOME（hadoop的安装目录）。 </div>
<div>     HADOOP_CONF_DIR（hadoop的配置文件目录）。HADOOP_SLAVES（--hosts指定的文件的地址） </div>
<div>hadoop-daemon.sh 单节点启动 </div>
<div>hadoop-daemons.sh 启动slaves.sh和hadoop-daemon.sh </div>
<div>start-all.sh    启动HDFS与MapReduce </div>
<div>start-balancer.sh 启动Hadoop的负载均衡 </div>
<div>start-dfs.sh  启动HDFS </div>
<div>start-jobhistoryserver.sh </div>
<div>start-mapred.sh  启动MapReduce </div>
<div>来进行调整。 </div>
<div>
<div class="O">
<div>后面根据项目的实际需要Hadoop Bin  包括: </div>
<div>Hadoop  hadoop的Shell </div>
<div>hadoop-config.sh 它的作用是对一些变量进行赋值 </div>
<div>     HADOOP_HOME（hadoop的安装目录）。 </div>
<div>     HADOOP_CONF_DIR（hadoop的配置文件目录）。 </div>
<div>     HADOOP_SLAVES（--hosts指定的文件的地址） </div>
<div>hadoop-daemon.sh 单节点启动 </div>
<div>hadoop-daemons.sh 在所有slaves上运行相同的脚本hadoop-daemon.sh </div>
<div>start-all.sh    启动HDFS与MapReduce </div>
<div>start-balancer.sh 启动Hadoop的负载均衡 </div>
<div>start-dfs.sh  启动HDFS </div>
<div>start-jobhistoryserver.sh </div>
<div>start-mapred.sh  启动MapReduce </div>
<div>来进行调整。 </div>
<div>
<div class="O">
<div>stop-all.sh   停止HDFS与MapReduce </div>
<div>stop-balancer.sh 停止做负载均衡 </div>
<div>stop-dfs.sh   停止HDFS </div>
<div>stop-jobhistoryserver.sh 停止Job追踪 </div>
<div>stop-mapred.sh 停止MapReduce </div>
<div>task-controller </div>
<div>
<div class="O">
<div>第二部分：Hadoop Shell 基本操作 </div>
<div> 
<div class="O">
<div>nHadoop shell </div>
<div>  包括:    namenode -format     format the DFS filesystem </div>
<div>           secondarynamenode    run the DFS secondary namenode </div>
<div>           namenode             run the DFS namenode </div>
<div>           datanode             run a DFS datanode </div>
<div>           dfsadmin             run a DFS admin client </div>
<div>           mradmin              run a Map-Reduce admin client </div>
<div>           fsck                 run a DFS filesystem checking utility </div>
<div>           fs                   run a generic filesystem user client </div>
<div>           balancer             run a cluster balancing utility </div>
<div>           fetchdt              fetch  delegation token from the NameNode </div>
<div>           jobtracker           run the MapReduce job Tracker node </div>
<div>           pipes                run a Pipes job </div>
<div>
<div class="O">
<div>tasktracker          run a MapReduce task Tracker node </div>
<div>  historyserver        run job history servers as a standalone daemon </div>
<div>  job                  manipulate MapReduce jobs </div>
<div>  queue                get information regarding JobQueues </div>
<div>  version              print the version </div>
<div>  jar &lt;jar&gt;            run a jar file </div>
<div>  distcp &lt;srcurl&gt; &lt;desturl&gt; copy file or directories recursively </div>
<div>  archive -archiveName NAME -p &lt;parent path&gt; &lt;src&gt;* &lt;dest&gt; create a hadoop archive</div>
<div>  classpath            prints the class path needed to get the </div>
<div>                       Hadoop jar and the required libraries </div>
<div>  daemonlog            get/set the log level for each daemon </div>
<div> or </div>
<div>  CLASSNAME            run the class named CLASSNAME </div>
<div>  </div>
<div>  <span>私塾在线学习网原创内容，转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/5457.html" rel="nofollow">http://sishuok.com/forum/blogPost/list/5457.html</a></span><span>】</span>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>
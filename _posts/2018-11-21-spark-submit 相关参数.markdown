---
layout:     post
title:      spark-submit 相关参数
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
spark-submit 相关参数<br><br><br>
master url:<br>
local: 使用1个worker线程在本地运行Spark程序<br>
local[k]: 使用k个worker线程在本地运行Spark程序<br>
local[*]: 使用所有剩余worker线程在本地运行Spark程序<br>
spark://HOST:PORT: 连接到Spark Standalone集群,以便在该集群上运行Spark应用程序,默认端口7077<br>
mesos://HOST:PORT: 连接到Mesos集群,以便在该集群上运行Spark程序,默认端口5050<br>
yarn client: 以client方式连接到Yarn集群,集群的定位由环境变量HADOOP_CONF_DIR定义,该方式driver在client运行<br>
yarn cluster: 以cluster方式连接到Yarn集群,集群的定位由环境变量HADOOP_CONF_DIR定义,该方式driver在client运行<br><br><br>
 --master  MASTER_URL                          spark://host:port, mesos://host:port, yarn, or local.<br>
       --deploy-mode    DEPLOY_MODE          driver运行之处，client运行在本机，cluster运行在集群<br>
       --class CLASS_NAME                              应用程序包的要运行的class<br>
       --name NAME                                             应用程序名称<br>
       --jars JARS                                                  用逗号隔开的driver本地jar包列表以及executor类路径<br>
       --py-files PY_FILES                                   用逗号隔开的放置在Python应用程序PYTHONPATH上的.zip, .egg, .py文件列表<br>
       --files                                                            FILES 用逗号隔开的要放置在每个executor工作目录的文件列表<br>
       --properties-file                                           FILE 设置应用程序属性的文件放置位置，默认是conf/spark-defaults.conf<br>
       --driver-memory MEM                               driver内存大小，默认512M<br>
       --driver-java-options                                  driver的java选项<br>
       --driver-library-path                                    driver的库路径Extra library path entries to pass to the driver<br>
       --driver-class-path                                      driver的类路径，用--jars 添加的jar包会自动包含在类路径里<br>
       --executor-memory MEM                          executor内存大小，默认1G<br><br><br>
       Spark standalone with cluster deploy mode only:<br>
       --driver-cores NUM driver使用内核数，默认为1<br>
       --supervise 如果设置了该参数，driver失败是会重启<br><br><br>
       Spark standalone and Mesos only:<br>
       --total-executor-cores NUM executor使用的总核数<br><br><br>
       YARN-only:<br>
       --executor-cores NUM 每个executor使用的内核数，默认为1<br>
       --queue QUEUE_NAME 提交应用程序给哪个YARN的队列，默认是default队列<br>
       --num-executors NUM 启动的executor数量，默认是2个<br>
       --archives ARCHIVES 被每个executor提取到工作目录的档案列表，用逗号隔开<br>
      关于以上spark-submit的help信息，有几点需要强调一下：<br>
关于--master  --deploy-mode，正常情况下，可以不需要配置--deploy-mode，使用下面的值配置--master就可以了，使用类似 --master spark://host:port --deploy-mode cluster会将driver提<br><br><br>
交给cluster，然后就将worker给kill的现象。<br><br><br>
如果要使用--properties-file的话，在--properties-file中定义的属性就不必要在spark-sumbit中再定义了，比如在conf/spark-defaults.conf 定义了spark.master，就可以不使用--master了。<br><br><br>
关于Spark属性的优先权为：SparkConf方式 &gt; 命令行参数方式 &gt;文件配置方式，具体参见Spark1.0.0属性配置。<br>
和之前的版本不同，Spark1.0.0会将自身的jar包和--jars选项中的jar包自动传给集群。<br>
Spark使用下面几种URI来处理文件的传播：<br>
file:// 使用file://和绝对路径，是由driver的HTTP server来提供文件服务，各个executor从driver上拉回文件。<br>
hdfs:, http:, https:, ftp: executor直接从URL拉回文件<br>
local: executor本地本身存在的文件，不需要拉回；也可以是通过NFS网络共享的文件。<br>
如果需要查看配置选项是从哪里来的，可以用打开--verbose选项来生成更详细的运行信息以做参考。<br><br><br><br><br>
关于Spark属性的优先权为：SparkConf方式 &gt; 命令行参数方式 &gt; 文件配置方式(conf/spark-defaults.conf )<br><br><br>
http://blog.csdn.net/zrc199021/article/details/53999293
            </div>
                </div>
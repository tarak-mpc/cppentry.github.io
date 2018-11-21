---
layout:     post
title:      跟天齐老师学Spark（3）--搭建Spark环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xbs1019/article/details/54898060				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
搭建spark环境（伪分布式，在虚拟机上用一台机器来编写一个spark程序）：Spark解压就能用。<br><br>
首先spark环境，首先要按照jdk，但可以不用按照scala环境。<br><br>
下载的spark最好是和hadoop版本匹配的spark版本，比如：spark-1.6.2-bin-hadoop2.6.tgz;<br>
需要自己使用maven来编译（后面会讲）。<br><br>
使用bin目录下的spark-shell，这是spark的一个客户端。可以启动一个spark的单机版。<br>
它启动之后会创建一个SparkContext对象或实例。启动后会进入scala的命令行。<br><br>
spark中的方法（或算子）是延迟加载的。他的算子有两钟，其中有一种就是延迟加载的，一旦触发Action的时候，它才会加载数据；<br>
比如：sc.textFile("/root/words.txt")//延迟加载<br>
它默认使用hdfs的接口来读的。<br>
但是这个textFile方法会把key丢掉，只保留value，因为那个key没有用。<br>
sc.textFile("/root/words.txt").collect//收集结果<br><br>
sc.textFile("/root/words.txt").flatMap(_.split)<br>
这个flatMap是spark的rdd上的一个方法，和原来scala原生的flatMap方法的实现是不一样的，但是功能都是一样的。<br><br>
sc.textFile("/root/words.txt").flatMap(_.split(" ")).map(_, 1)<br>
它会生成一个RDD，这是一个分布式的数据集合。<br><br>
sc.textFile("/root/words.txt").flatMap(_.split(" ")).map(_, 1).reduceByKey(_+_)<br>
这个reduceByKey现在一个分区聚合，它会把value累加。到现在，这些方法都是延迟加载的，现在还没有触发任务。<br><br>
sc.textFile("/root/words.txt").flatMap(_.split(" ")).map(_, 1).reduceByKey(_+_).collect//收集结果<br>
这个时候会触发Action，开始计算，得到结果。<br><br>
sc.textFile("/root/words.txt").flatMap(_.split(" ")).map(_, 1).reduceByKey(_+_).sortBy(_._2, false).collect<br>
这些方法和scala中的那些原生的方法是不一样的，因为spark的这些方法都是针对分布式数据集合的。<br><br><br>
sc.textFile("/root/words.txt").flatMap(_.split(" ")).map(_, 1).reduceByKey(_+_).sortBy(_._2, false).saveAsTextFile("/root/out333")<br>
可以将计算结果保存到本地目录上。<br><br>
Spark的输入默认用的hdfs的InputFormat，输出默认用的也是hdfs的OutputFormat。<br><br>
------------------------------------<br><br>
将多有节点的时间做一个同步：date -s "016-08-15 16:00:00"<br><br>
部署Spark集群：一个Master，多个Worker。<br>
老大负责资源调度，小弟负责节点管理，真正负责干活的是Executor（执行器）。<br>
这是spark自带的一种集群模式：standalone。<br><br>
启动一个老大，小弟们和老大建立连接，向老大注册。<br>
提交任务的是Driver(客户端)：跟老大建立通信，Master进行资源分配，让Worker启动Executor，然后Executor会和Driver通信；<br><br>
现在我们部署3个节点：一个Master和两个Worker。<br><br>
不建议使用root用户，实际生产中可能会有权限问题。<br><br>
Apache提供了一些自动化部署工具，可以实现spark、hadoop、storm的自动部署。<br>
当然我们也可以写一些自动部署的shell脚本。<br>
spark的目录：<br>
    sbin：启动spark相关的命令，一般不加到classpath；<br>
    bin：hadoop和yarn相关的操作命令，一般会加到classpath下。<br>
    <br>
1.安装jdk；<br>
2.上传spark安装包；<br>
3.解压spark安装包；<br>
4.修改配置文件<br>
spark-env.sh.template 改成spark-env.sh<br>
slaves.template改成slaves<br><br><br>
（1）设置JAVA_HOME;<br>
        export JAVA_HOME=<br>
（2）告诉它Master的地址和端口。<br>
        vi spark-env.sh<br>
            export SPARK_MASTER_IP=hadoop01<br>
            export SPARK_MASTER_PORT=7077<br>
        <br>
        slaves<br>
            hadoop02<br>
            hadoop03<br>
5.将spark的配置拷贝到其他机器上<br>
    scp -r /itcast/hadoop02:/<br>
    scp -r /itcast/hadoop03:/<br>
    <br>
这样spark就部署好了。<br><br>
启动spark（提前配置ssh免密码登录）:    <br>
在Master机器上启动即可：sbin/start-all.sh<br>
通过jps查看进程，master机器上的进程为Master，其他两台机器都是Worker进程。<br><br>
spark只有两个进程，一种是Master进程，一种是Worker进程。<br><br>
spark的管理界面地址：http://hadoop01:8080<br><br><br>
---------------------------------------<br>
上面的部署存在单点故障，需要部署两个Master。<br>
多个Master和多个Worker之间的协调就要依靠zookeeper。<br>
Master启动之后连接zookeeper，zookeeper会通过协调让其中一个Master成为活跃的。<br>
而Worker启动后也连接zookeeper，间接的知道哪个是活着的Master。<br><br>
所以要部署高可用的spark集群，就需要安装zookeeper集群。<br><br>
在spark-env.sh中保留JAVA_HOME的配置，但是不要在配置Master的地址和端口号了，只要配置zookeeper的位置即可：<br>
export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=hadoop01:2181,hadoop02:2181,hadoop03:2181 -Dspark.deploy.zookeeper.dir=/sp"<br><br>
分别在3台机器上启动zookeeper，查看zookeeper的运行状态。<br>
在hadoop01上启动Master，使用spark/sbin/start-all.sh，这样就会启动一个Master和所有的Worker。<br>
然后在hadoop02上启动一个Master，使用spark/sbin/start-master.sh命令（这次就不用再使用start-all.sh命令了）启动。<br><br>
在spark的管理界面可以看到，一个Master是活跃（alive）状态，另一个Master是备用（standby）状态。<br>
Worker只会向活着的Master汇报，不会向Standby状态的Master汇报。所以在hadoop02这个Master的管理界面上没有Worker的信息。<br><br>
-----------------------------------------------------------<br>
spark/bin/spark-shell这个命令会启动一个单机版的客户端它没有连到我们的集群上，<br>
它会启动一个sparksubmit程序，它就相当于一个spark的客户端，它要跟我们的集群通信，<br>
它可以把任务提交到集群上。<br><br>
启动集群版的spark-shell的方法：<br>
在启动的时候指定一些参数，现在我们有两个Master，所以两个Master的地址和端口都要写（多个Master之间用逗号分隔）。<br>
spark/bin/spark-shell --master spark://hadoop01:7077,spark://hadoop02:7077<br>
注意：如果在启动spark-shell的时候，你没有给它指定要分配多少内存和CPU，那么它默认<br>
会给每一个Worker分配1G的内存，分配所有的核数。<br><br>
启动成功后，在Worker所在的机器上就会分别启动一个Executor进程；<br>
在scala的命令行使用sc.prarllelize(Array(12,3,5,3,6,3,5,7))可以将这些数分到多台机器上，<br>
RDD---&gt;弹性的可复原的分布式的数据集。<br><br><br>
退出shell界面后，sparksubmit进程就会消失，同时所有的Worker机器上的Executor进程也会消失。<br><br><br><br>
补充：启动spark-shell时指定cpu和内存的命令(根据任务来个性化分配资源)<br>
spark/bin/spark-shell --master spark://hadoop01:7077,spark://hadoop02:7077 --executor-memory 512m --total-executor-cores 7<br>            </div>
                </div>
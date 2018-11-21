---
layout:     post
title:      SparkSql使用内部集成hive访问hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
<p>SparkSql需要用到hive的元数据，有两种方式集成方式分别为使用buildin的hive直接连接hive的mysql元数据库和连接metastore thrift server<br>
注：Spark自带的hive版本为1.2.1，使用mysql方式时，spark会自动将hive源数据库中的metastore version改为1.2.0，导致高版本hive使用出现问题。<br>
实际测试时发现，如果想同时使用较高版本的hive和spark，可以使用hive2.3.x+spark 2.0.0-without-hive运行hive on spark，同时再部署一个spark 2.3.1执行spark sql连接hive，这样下来一共是一个hive，两个版本的spark</p>
</blockquote>
<p>##集成hive<br>
###启动hive的meatastore服务</p>
<blockquote>
<p>在$HIVE_HOME/bin目录下，输入**hive --service metastore &amp;**启动metastore服务</p>
</blockquote>
<p>###spark配置</p>
<blockquote>
<p>在$SPARK_HOME/conf目录下，新建hive-site.xml</p>
</blockquote>
<pre><code>&lt;?xml version="1.0" encoding="UTF-8" standalone="no"?&gt;
&lt;configuration&gt;
  &lt;property&gt;
    &lt;name&gt;hive.metastore.uris&lt;/name&gt;
    &lt;value&gt;thrift://dashuju174:9083&lt;/value&gt;
    &lt;description&gt;Thrift URI for the remote metastore. Used by metastore client to connect to remote metastore.&lt;/description&gt;
  &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<blockquote>
<p>复制hbase-site.xml到$SPARK_HOME/conf目录下</p>
</blockquote>
<p>##集成hbase<br>
###配置</p>
<blockquote>
<p>将hbase-site.xml复制到$SPARK_HOME/conf目录下，如果还需要在spark中使用hadoop，同样需要将core-site.xml和hdfs-site.xml复制到$SPARK_HOME/conf<br>
spark自带的jar放在了spark的jars目录，为了方便控制复制以下jar到spark的lib目录下</p>
</blockquote>
<pre><code>#位于$HBASE_HOME/lib目录
hbase-annotations-1.1.4.jar
hbase-client-1.1.4.jar
hbase-common-1.1.4.jar
hbase-hadoop2-compat-1.1.4.jar
hbase-hadoop-compat-1.1.4.jar
hbase-protocol-1.1.4.jar
hbase-server-1.1.4.jar
htrace-core-3.1.0-incubating.jar

#位于$HIVE_HOME/lib目录
hive-hbase-handler-2.3.2.jar
</code></pre>
<blockquote>
<p>spark中自带了htrace-core-3.0.4.jar，里面包名为org.htrace，在htrace-core-3.1.0改为了org.apache.htrace，因此必须要使用3.1.0+版本</p>
</blockquote>
<p>##使用SparkSQL控制台</p>
<blockquote>
<p>在$SPARK_HOME目录下，输入以下命令启动，控制台启动默认为yarn client模式，可以直接查询hive数据库。</p>
</blockquote>
<pre><code>spark-sql --master local[1] --deploy-mode client \
--jars \
$SPARK_HOME/lib/hbase-annotations-1.1.4.jar,\
$SPARK_HOME/lib/hbase-client-1.1.4.jar,\
$SPARK_HOME/lib/hbase-common-1.1.4.jar,\
$SPARK_HOME/lib/hbase-hadoop2-compat-1.1.4.jar,\
$SPARK_HOME/lib/hbase-hadoop-compat-1.1.4.jar,\
$SPARK_HOME/lib/hbase-protocol-1.1.4.jar,\
$SPARK_HOME/lib/hbase-server-1.1.4.jar,\
$SPARK_HOME/lib/hive-hbase-handler-2.3.2.jar,\
$SPARK_HOME/lib/htrace-core-3.1.0-incubating.jar
</code></pre>
<p>##spark streaming部署记录<br>
###增加依赖</p>
<blockquote>
<p>因为spark streaming中用到了jedis、kafka等功能，需要加入额外的jar包。在$SPARK_HOME/lib加入以下jar包</p>
</blockquote>
<pre><code>#用于json数据转换，按照实际情况增加
fastjson-1.2.47.jar

#用于读取flume kafka event数据，按照实际情况增加
flume-ng-sdk-1.7.0.jar

#redis依赖
commons-pool2-2.5.0.jar
jedis-2.9.0.jar

#kafka streaming依赖
kafka_2.11-0.8.2.1.jar
metrics-core-2.2.0.jar
spark-streaming-kafka_2.11-1.6.3.jar
</code></pre>
<p>###部署命令</p>
<blockquote>
<p>使用了spark standalong集群模式提交任务<br>
–jars 任务依赖的第三方jar文件，直接放到$SPARK_HOME/jars目录下无效</p>
</blockquote>
<pre><code>spark-submit \
--master spark://dashuju213:6066,dashuju214:6066 \
--deploy-mode cluster \
--jars $SPARK_HOME/lib/*.jar,/home/hadoop/application/dcm/*.jar \
--driver-memory 512m \
--executor-memory 512m \
--executor-cores 1 \
--num-executors 1 \
--class com.bwjf.dcm.spark.straming.job.InvoiceCountSparkJob /home/hadoop/application/dcm/dcm-spark-streaming-1.0.0.jar
</code></pre>
<p>###性能调优</p>
<blockquote>
<p><a href="http://spark.apachecn.org/docs/cn/2.2.0/hardware-provisioning.html" rel="nofollow">硬件配置</a>：官方推荐至少8核8G，并且只为 Spark分配最多75% 的内存<br>
资源配置：Standalone mode模式下必须配置spark.cores.max或者spark.deploy.defaultCores来限制单个应用所占用的节点个数，否则任务会占用所有资源，导致一个任务执行完之前，其他任务无法提交。<br>
spark-default.xml</p>
</blockquote>
<pre><code>#集群上每个任务可以使用的最大核心数
#spark.cores.max 1 #为FIAR时可不设置此值
spark.driver.cores 1
spark.driver.memory 512m
spark.driver.supervise true

#executor cores = spark.max.cores / spark.executor.cores
spark.executor.cores 1
spark.executor.memory 512m

#默认为FIFO(先进先出)，可以修改为FAIR
spark.scheduler.mode FAIR
#放弃作业之前任何特定任务的失败次数，允许重试次数=此值-1
spark.task.maxFailures 4
#默认为java序列化，改为Kryo库序列化，支持类型比java少，性能要高(10x)
spark.serializer org.apache.spark.serializer.KryoSerializer

</code></pre>
<blockquote>
<p><a href="http://spark-env.sh" rel="nofollow">spark-env.sh</a></p>
</blockquote>
<pre><code>#RAM 小于32 GB时，设置此值可以减少内存占用
-XX:+UseCompressedOops

#worker占用的CPS内核数
SPARK_WORKER_CORES=3
#worker占用的内存，建议为可用内存的75%
SPARK_WORKER_MEMORY=2500m
#worker的启动端口号，默认为随机端口
SPARK_WORKER_PORT=9090
</code></pre>
<p>###kafka配置调优</p>
<blockquote>
<p>参考<a href="https://www.cnblogs.com/jager/p/6343897.html?utm_source=itdadao&amp;utm_medium=referral" rel="nofollow">Kafka最佳实践</a></p>
</blockquote>
<pre><code># 每个topic默认partition数量，根据消费者实际情况配置，配置过小会影响消费性能
num.partitions=9

#用来从主partion同步数据的线程数，默认为1，建议适当调大，数据量大的时候一个同步线程可能不够用
num.replica.fetchers=3

#消息日志备份因子，默认是1
default.replication.factor=2

#日志传输时候的压缩格式，可选择lz4, snappy, gzip,不压缩。建议打开压缩，可以提高传输性能，压缩格式的选择可以参考文章结尾的参考资料。
compression.type=snappy

# 启用压缩的topic名称。若上面参数选择了一个压缩格式，那么压缩仅对本参数指定的topic有效，若本参数为空，则对所有topic有效。
compressed.topics=topic1
</code></pre>
<h4><a id="orgapachesparkSparkException_ArrayBufferorgapachesparkSparkException_Couldnt_find_leaders_for_Setdcm_user0_150"></a>org.apache.spark.SparkException: ArrayBuffer(org.apache.spark.SparkException: Couldn’t find leaders for Set([dcm_user,0]))</h4>
<blockquote>
<p><a href="https://www.jianshu.com/p/3841ea958a3a" rel="nofollow">解决方法</a>，增大num.replica.fetchers的值，默认为1，需要修改kafka配置</p>
</blockquote>
<p>###自动重启任务</p>
<blockquote>
<p><a href="https://blog.csdn.net/u014236468/article/details/79410109" rel="nofollow">自动化监控spark进程并重启</a><br>
加入–supervise参数</p>
</blockquote>
<p>###spark集群监控</p>
<blockquote>
<p><a href="http://xn--monitorSpark-4w4su5j.sh" rel="nofollow">上传monitorSpark.sh</a>，修改执行权限chmod 755 <a href="http://monitorSpark.sh" rel="nofollow">monitorSpark.sh</a><br>
<a href="http://monitorSpark.sh" rel="nofollow">monitorSpark.sh</a></p>
</blockquote>
<pre><code>#!/bin/bash
source /etc/profile
#
master=`ps -ef | grep Master | grep spark | awk '{print $2}'`
echo $master

if [ "$master" =  "" ]; then
        echo "Spark Master is restart!"
        $SPARK_HOME/sbin/start-master.sh
else
        echo "Spark Master is alive!"
fi

slave=`ps -ef | grep Worker | grep spark | grep 8081 | awk '{print $2}'`
echo $slave

if [ "$slave" =  "" ]; then
        echo "Spark Worker is restart!"
        $SPARK_HOME/sbin/start-slaves.sh
        #  $SPARK_HOME/start-slave.sh spark://dashuju213:7077,dashuju214:7077

else
        echo "Spark Worker is alive!"
fi

</code></pre>
<blockquote>
<p>加入crontab的定时，每分钟监控一次，如果服务进程不存在，则重启该服务。</p>
</blockquote>
<pre><code>crontab -e
*/1 * * * * /home/hadoop/application/spark/sbin/monitorSpark.sh
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
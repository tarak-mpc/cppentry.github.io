---
layout:     post
title:      SparkSql集成hive访问hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="sparksql使用内部集成hive访问hbase">SparkSql使用内部集成hive访问hbase</h1>

<p>标签（空格分隔）： spark</p>

<hr>

<blockquote>
  <p>SparkSql需要用到hive的元数据，有两种方式集成方式分别为使用buildin的hive直接连接hive的mysql元数据库和连接metastore thrift server <br>
  注：Spark自带的hive版本为1.2.1，使用mysql方式时，spark会自动将hive源数据库中的metastore version改为1.2.0，导致高版本hive使用出现问题。 <br>
  实际测试时发现，如果想同时使用较高版本的hive和spark，可以使用hive2.3.x+spark 2.0.0-without-hive运行hive on spark，同时再部署一个spark 2.3.1执行spark sql连接hive，这样下来一共是一个hive，两个版本的spark</p>
</blockquote>



<h2 id="集成hive">集成hive</h2>



<h3 id="启动hive的meatastore服务">启动hive的meatastore服务</h3>

<blockquote>
  <p>在<span>$</span>HIVE_HOME/bin目录下，输入<strong>hive –service metastore &amp;</strong>启动metastore服务</p>
</blockquote>



<h3 id="spark配置">spark配置</h3>

<blockquote>
  <p>在<span>$</span>SPARK_HOME/conf目录下，新建hive-site.xml</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8" standalone="no"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.metastore.uris<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>thrift://dashuju174:9083<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Thrift URI for the remote metastore. Used by metastore client to connect to remote metastore.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<blockquote>
  <p>复制hbase-site.xml到<span>$</span>SPARK_HOME/conf目录下</p>
</blockquote>



<h2 id="集成hbase">集成hbase</h2>



<h3 id="配置">配置</h3>

<blockquote>
  <p>将hbase-site.xml复制到<span>$</span>SPARK_HOME/conf目录下，如果还需要在spark中使用hadoop，同样需要将core-site.xml和hdfs-site.xml复制到<span>$</span>SPARK_HOME/conf <br>
  spark自带的jar放在了spark的jars目录，为了方便控制复制以下jar到spark的lib目录下</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#位于$HBASE_HOME/lib目录</span>
hbase-annotations-<span class="hljs-number">1.1</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>
hbase-client-<span class="hljs-number">1.1</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>
hbase-common-<span class="hljs-number">1.1</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>
hbase-hadoop2-compat-<span class="hljs-number">1.1</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>
hbase-hadoop-compat-<span class="hljs-number">1.1</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>
hbase-protocol-<span class="hljs-number">1.1</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>
hbase-server-<span class="hljs-number">1.1</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>
htrace-core-<span class="hljs-number">3.1</span><span class="hljs-number">.0</span>-incubating<span class="hljs-preprocessor">.jar</span>

<span class="hljs-preprocessor">#位于$HIVE_HOME/lib目录</span>
hive-hbase-handler-<span class="hljs-number">2.3</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.jar</span></code></pre>

<blockquote>
  <p>spark中自带了htrace-core-3.0.4.jar，里面包名为org.htrace，在htrace-core-3.1.0改为了org.apache.htrace，因此必须要使用3.1.0+版本</p>
</blockquote>



<h2 id="使用sparksql控制台">使用SparkSQL控制台</h2>

<blockquote>
  <p>在<span>$</span>SPARK_HOME目录下，输入以下命令启动，控制台启动默认为yarn client模式，可以直接查询hive数据库。</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs tex">spark-sql --master local<span class="hljs-special">[</span>1<span class="hljs-special">]</span> --deploy-mode client <span class="hljs-command">\
</span>--jars <span class="hljs-command">\
</span><span class="hljs-formula">$SPARK_HOME/lib/hbase-annotations-1.1.4.jar,<span class="hljs-command">\
</span>$</span>SPARK_HOME/lib/hbase-client-1.1.4.jar,<span class="hljs-command">\
</span><span class="hljs-formula">$SPARK_HOME/lib/hbase-common-1.1.4.jar,<span class="hljs-command">\
</span>$</span>SPARK_HOME/lib/hbase-hadoop2-compat-1.1.4.jar,<span class="hljs-command">\
</span><span class="hljs-formula">$SPARK_HOME/lib/hbase-hadoop-compat-1.1.4.jar,<span class="hljs-command">\
</span>$</span>SPARK_HOME/lib/hbase-protocol-1.1.4.jar,<span class="hljs-command">\
</span><span class="hljs-formula">$SPARK_HOME/lib/hbase-server-1.1.4.jar,<span class="hljs-command">\
</span>$</span>SPARK_HOME/lib/hive-hbase-handler-2.3.2.jar,<span class="hljs-command">\
</span><span class="hljs-formula">$SPARK_HOME/lib/htrace-core-3.1.0-incubating.jar</span></code></pre>



<h2 id="spark-streaming部署记录">spark streaming部署记录</h2>



<h3 id="增加依赖">增加依赖</h3>

<blockquote>
  <p>因为spark streaming中用到了jedis、kafka等功能，需要加入额外的jar包。在$SPARK_HOME/lib加入以下jar包</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#用于json数据转换，按照实际情况增加</span>
fastjson-<span class="hljs-number">1.2</span><span class="hljs-number">.47</span><span class="hljs-preprocessor">.jar</span>

<span class="hljs-preprocessor">#用于读取flume kafka event数据，按照实际情况增加</span>
flume-ng-sdk-<span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span>

<span class="hljs-preprocessor">#redis依赖</span>
commons-pool2-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span>
jedis-<span class="hljs-number">2.9</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span>

<span class="hljs-preprocessor">#kafka streaming依赖</span>
kafka_2<span class="hljs-number">.11</span>-<span class="hljs-number">0.8</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.jar</span>
metrics-core-<span class="hljs-number">2.2</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span>
spark-streaming-kafka_2<span class="hljs-number">.11</span>-<span class="hljs-number">1.6</span><span class="hljs-number">.3</span><span class="hljs-preprocessor">.jar</span></code></pre>



<h3 id="部署命令">部署命令</h3>

<blockquote>
  <p>使用了spark standalong集群模式提交任务 <br>
  –jars 任务依赖的第三方jar文件，直接放到$SPARK_HOME/jars目录下无效</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs haml">spark-submit \
-<span class="ruby">-master <span class="hljs-symbol">spark:</span>/<span class="hljs-regexp">/dashuju213:6066,dashuju214:6066 \
</span></span>-<span class="ruby">-deploy-mode cluster \
</span>-<span class="ruby">-jars <span class="hljs-variable">$SPARK_HOME</span>/lib/*.jar,<span class="hljs-regexp">/home/hadoop</span><span class="hljs-regexp">/application/dcm</span><span class="hljs-regexp">/*.jar \
</span></span>-<span class="ruby">-driver-memory <span class="hljs-number">512</span>m \
</span>-<span class="ruby">-executor-memory <span class="hljs-number">512</span>m \
</span>-<span class="ruby">-executor-cores <span class="hljs-number">1</span> \
</span>-<span class="ruby">-num-executors <span class="hljs-number">1</span> \
</span>-<span class="ruby">-<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">com</span>.<span class="hljs-title">bwjf</span>.<span class="hljs-title">dcm</span>.<span class="hljs-title">spark</span>.<span class="hljs-title">straming</span>.<span class="hljs-title">job</span>.<span class="hljs-title">InvoiceCountSparkJob</span> /<span class="hljs-title">home</span>/<span class="hljs-title">hadoop</span>/<span class="hljs-title">application</span>/<span class="hljs-title">dcm</span>/<span class="hljs-title">dcm</span>-<span class="hljs-title">spark</span>-<span class="hljs-title">streaming</span>-1.0.0.<span class="hljs-title">jar</span></span></span></code></pre>



<h3 id="性能调优">性能调优</h3>

<blockquote>
  <p><a href="http://spark.apachecn.org/docs/cn/2.2.0/hardware-provisioning.html" rel="nofollow">硬件配置</a>：官方推荐至少8核8G，并且只为 Spark分配最多75% 的内存 <br>
  资源配置：Standalone mode模式下必须配置spark.cores.max或者spark.deploy.defaultCores来限制单个应用所占用的节点个数，否则任务会占用所有资源，导致一个任务执行完之前，其他任务无法提交。 <br>
  spark-default.xml</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#集群上每个任务可以使用的最大核心数</span>
<span class="hljs-preprocessor">#spark.cores.max 1 #为FIAR时可不设置此值</span>
spark<span class="hljs-preprocessor">.driver</span><span class="hljs-preprocessor">.cores</span> <span class="hljs-number">1</span>
spark<span class="hljs-preprocessor">.driver</span><span class="hljs-preprocessor">.memory</span> <span class="hljs-number">512</span>m
spark<span class="hljs-preprocessor">.driver</span><span class="hljs-preprocessor">.supervise</span> true

<span class="hljs-preprocessor">#executor cores = spark.max.cores / spark.executor.cores</span>
spark<span class="hljs-preprocessor">.executor</span><span class="hljs-preprocessor">.cores</span> <span class="hljs-number">1</span>
spark<span class="hljs-preprocessor">.executor</span><span class="hljs-preprocessor">.memory</span> <span class="hljs-number">512</span>m

<span class="hljs-preprocessor">#默认为FIFO(先进先出)，可以修改为FAIR</span>
spark<span class="hljs-preprocessor">.scheduler</span><span class="hljs-preprocessor">.mode</span> FAIR
<span class="hljs-preprocessor">#放弃作业之前任何特定任务的失败次数，允许重试次数=此值-1</span>
spark<span class="hljs-preprocessor">.task</span><span class="hljs-preprocessor">.maxFailures</span> <span class="hljs-number">4</span>
<span class="hljs-preprocessor">#默认为java序列化，改为Kryo库序列化，支持类型比java少，性能要高(10x)</span>
spark<span class="hljs-preprocessor">.serializer</span> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.KryoSerializer</span>
</code></pre>

<blockquote>
  <p>spark-env.sh</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor">#RAM 小于32 GB时，设置此值可以减少内存占用</span>
-XX:+UseCompressedOops

<span class="hljs-preprocessor">#worker占用的CPS内核数</span>
SPARK_WORKER_CORES=<span class="hljs-number">3</span>
<span class="hljs-preprocessor">#worker占用的内存，建议为可用内存的75%</span>
SPARK_WORKER_MEMORY=<span class="hljs-number">2500</span>m
<span class="hljs-preprocessor">#worker的启动端口号，默认为随机端口</span>
SPARK_WORKER_PORT=<span class="hljs-number">9090</span></code></pre>



<h3 id="kafka配置调优">kafka配置调优</h3>

<blockquote>
  <p>参考<a href="https://www.cnblogs.com/jager/p/6343897.html?utm_source=itdadao&amp;utm_medium=referral" rel="nofollow">Kafka最佳实践</a></p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># 每个topic默认partition数量，根据消费者实际情况配置，配置过小会影响消费性能</span>
num<span class="hljs-preprocessor">.partitions</span>=<span class="hljs-number">9</span>

<span class="hljs-preprocessor">#用来从主partion同步数据的线程数，默认为1，建议适当调大，数据量大的时候一个同步线程可能不够用</span>
num<span class="hljs-preprocessor">.replica</span><span class="hljs-preprocessor">.fetchers</span>=<span class="hljs-number">3</span>

<span class="hljs-preprocessor">#消息日志备份因子，默认是1</span>
default<span class="hljs-preprocessor">.replication</span><span class="hljs-preprocessor">.factor</span>=<span class="hljs-number">2</span>

<span class="hljs-preprocessor">#日志传输时候的压缩格式，可选择lz4, snappy, gzip,不压缩。建议打开压缩，可以提高传输性能，压缩格式的选择可以参考文章结尾的参考资料。</span>
compression<span class="hljs-preprocessor">.type</span>=snappy

<span class="hljs-preprocessor"># 启用压缩的topic名称。若上面参数选择了一个压缩格式，那么压缩仅对本参数指定的topic有效，若本参数为空，则对所有topic有效。</span>
compressed<span class="hljs-preprocessor">.topics</span>=topic1</code></pre>



<h4 id="orgapachesparksparkexception-arraybufferorgapachesparksparkexception-couldnt-find-leaders-for-setdcmuser0">org.apache.spark.SparkException: ArrayBuffer(org.apache.spark.SparkException: Couldn’t find leaders for Set([dcm_user,0]))</h4>

<blockquote>
  <p><a href="https://www.jianshu.com/p/3841ea958a3a" rel="nofollow">解决方法</a>，增大num.replica.fetchers的值，默认为1，需要修改kafka配置</p>
</blockquote>



<h3 id="自动重启任务">自动重启任务</h3>

<blockquote>
  <p><a href="https://blog.csdn.net/u014236468/article/details/79410109" rel="nofollow">自动化监控spark进程并重启</a> <br>
  加入–supervise参数</p>
</blockquote>



<h3 id="spark集群监控">spark集群监控</h3>

<blockquote>
  <p>上传monitorSpark.sh，修改执行权限chmod 755 monitorSpark.sh <br>
  monitorSpark.sh</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-shebang">#!/bin/bash</span>
<span class="hljs-built_in">source</span> /etc/profile
<span class="hljs-comment">#</span>
master=`ps -ef | grep Master | grep spark | awk <span class="hljs-string">'{print $2}'</span>`
<span class="hljs-built_in">echo</span> <span class="hljs-variable">$master</span>

<span class="hljs-keyword">if</span> [ <span class="hljs-string">"<span class="hljs-variable">$master</span>"</span> =  <span class="hljs-string">""</span> ]; <span class="hljs-keyword">then</span>
        <span class="hljs-built_in">echo</span> <span class="hljs-string">"Spark Master is restart!"</span>
        <span class="hljs-variable">$SPARK_HOME</span>/sbin/start-master.sh
<span class="hljs-keyword">else</span>
        <span class="hljs-built_in">echo</span> <span class="hljs-string">"Spark Master is alive!"</span>
<span class="hljs-keyword">fi</span>

slave=`ps -ef | grep Worker | grep spark | grep <span class="hljs-number">8081</span> | awk <span class="hljs-string">'{print $2}'</span>`
<span class="hljs-built_in">echo</span> <span class="hljs-variable">$slave</span>

<span class="hljs-keyword">if</span> [ <span class="hljs-string">"<span class="hljs-variable">$slave</span>"</span> =  <span class="hljs-string">""</span> ]; <span class="hljs-keyword">then</span>
        <span class="hljs-built_in">echo</span> <span class="hljs-string">"Spark Worker is restart!"</span>
        <span class="hljs-variable">$SPARK_HOME</span>/sbin/start-slaves.sh
        <span class="hljs-comment">#  $SPARK_HOME/start-slave.sh spark://dashuju213:7077,dashuju214:7077</span>

<span class="hljs-keyword">else</span>
        <span class="hljs-built_in">echo</span> <span class="hljs-string">"Spark Worker is alive!"</span>
<span class="hljs-keyword">fi</span>
</code></pre>

<blockquote>
  <p>加入crontab的定时，每分钟监控一次，如果服务进程不存在，则重启该服务。</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs ruby">crontab -e
*<span class="hljs-regexp">/1 * * * * /home</span><span class="hljs-regexp">/hadoop/application</span><span class="hljs-regexp">/spark/sbin</span><span class="hljs-regexp">/monitorSpark.sh</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
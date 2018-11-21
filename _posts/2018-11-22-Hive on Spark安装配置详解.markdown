---
layout:     post
title:      Hive on Spark安装配置详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wslbd/article/details/52331091				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="简介">简介</h2>

<p>本文主要记录如何安装配置Hive on Spark，在执行以下步骤之前，请先确保已经安装Hadoop集群，Hive，MySQL，JDK，Scala，具体安装步骤不再赘述。</p>



<h2 id="背景">背景</h2>

<p>Hive默认使用MapReduce作为执行引擎，即Hive on mr。实际上，Hive还可以使用Tez和Spark作为其执行引擎，分别为Hive on Tez和Hive on Spark。由于MapReduce中间计算均需要写入磁盘，而Spark是放在内存中，所以总体来讲Spark比MapReduce快很多。因此，Hive on Spark也会比Hive on mr快。为了对比Hive on Spark和Hive on mr的速度，需要在已经安装了Hadoop集群的机器上安装Spark集群（Spark集群是建立在Hadoop集群之上的，也就是需要先装Hadoop集群，再装Spark集群，因为Spark用了Hadoop的HDFS、YARN等），然后把Hive的执行引擎设置为Spark。</p>

<p>Spark运行模式分为三种1、Spark on YARN 2、Standalone Mode 3、Spark on Mesos。 <br>
Hive on Spark默认支持Spark on YARN模式，因此我们选择Spark on YARN模式。Spark on YARN就是使用YARN作为Spark的资源管理器。分为Cluster和Client两种模式。</p>



<h2 id="一环境说明">一、环境说明</h2>

<p>本教程Hadoop相关软件全部基于CDH5.5.1，用yum安装，系统环境如下：</p>

<ul>
<li>操作系统：CentOS 7.2</li>
<li>Hadoop 2.6.0</li>
<li>Hive1.1.0</li>
<li>Spark1.5.0</li>
<li>MySQL 5.6</li>
<li>JDK 1.8</li>
<li>Maven 3.3.3</li>
<li>Scala 2.10</li>
</ul>

<p>各节点规划如下：</p>



<pre class="prettyprint"><code class=" hljs ">192.168.117.51     Goblin01           nn1  jn1  rm1  worker  master  hive  metastore  mysql
192.168.117.52     Goblin02    zk2    nn2  jn2  rm2  worker          hive
192.168.117.53     Goblin03    zk3    dn1  jn3       worker          hive
192.168.117.54     Goblin04    zk4    dn2            worker          hive</code></pre>

<p>说明：Goblin01~04是每台机器的hostname，zk代表zookeeper，nn代表hadoop的namenode，dn代表datanode，jn代表journalnode，rm代表resourcemanager，worker代表Spark的slaves，master代表Spark的master</p>



<h2 id="二编译和安装sparkspark-on-yarn">二、编译和安装Spark（Spark on YARN）</h2>



<h3 id="21-编译spark源码">2.1 编译Spark源码</h3>

<p>要使用Hive on Spark，所用的Spark版本必须不包含Hive的相关jar包，hive on spark 的官网上说“Note that you must have a version of Spark which does not include the Hive jars”。在spark官网下载的编译的Spark都是有集成Hive的，因此需要自己下载源码来编译，并且编译的时候不指定Hive。</p>

<p>我们这里用的Spark源码是spark-1.5.0-cdh5.5.1版本,下载地址如下：</p>

<p><a href="http://archive.cloudera.com/cdh5/cdh/5/spark-1.5.0-cdh5.5.1-src.tar.gz" rel="nofollow">http://archive.cloudera.com/cdh5/cdh/5/spark-1.5.0-cdh5.5.1-src.tar.gz</a></p>

<p>下载完后用 tar xzvf 命令解压，进入解压完的文件夹，准备编译。</p>

<p><strong>注意：编译前请确保已经安装JDK、Maven和Scala，maven为3.3.3及以上版本，并在/etc/profile里配置环境变量。</strong></p>

<p>命令行进入在源码根目录下，执行</p>



<pre class="prettyprint"><code class=" hljs avrasm">  ./make-distribution<span class="hljs-preprocessor">.sh</span> --name <span class="hljs-string">"hadoop2-without-hive"</span> --tgz <span class="hljs-string">"-Pyarn,hadoop-provided,hadoop-2.6,parquet-provided"</span></code></pre>

<p>若编译过程出现内存不足的情况，需要在运行编译命令之前先运行：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> MAVEN_OPTS=<span class="hljs-string">"-Xmx2g -XX:MaxPermSize=512M -XX:ReservedCodeCacheSize=512m"</span></code></pre>

<p>来设置Maven的内存。</p>

<p>编译过程由于要下载很多Maven依赖的jar包，需要时间较长（大概一两个小时），要保证网络状况良好，不然很容易编译失败。若出现以下结果，则编译成功：</p>

<p><img src="https://img-blog.csdn.net/20160826192444896" alt="这里写图片描述" title=""></p>

<p>编译成功后，会在源码根目录下多出一个文件(红色部分）：</p>

<p>spark-1.5.0-cdh5.5.1-bin-hadoop2-without-hive.tgz</p>

<p><img src="https://img-blog.csdn.net/20160826192423974" alt="这里写图片描述" title=""></p>

<h3 id="22-安装spark">2.2 安装Spark</h3>

<ul>
<li><p>将编译完生成的spark-1.5.0-cdh5.5.1-bin-hadoop2-without-hive.tgz拷贝到Spark的安装路径，并用 tar -xzvf 命令解压</p></li>
<li><p>配置环境变量</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-variable">$vim</span> /etc/profile
<span class="hljs-keyword">export</span> SPARK_HOME=spark安装路径
<span class="hljs-variable">$source</span> /etc/profile</code></pre></li>
</ul>



<h3 id="23-配置spark">2.3 配置Spark</h3>

<p>配置spark-env.sh、slaves和spark-defaults.conf三个文件</p>

<ul>
<li>spark-env.sh </li>
</ul>

<p>主要配置JAVA_HOME、SCALA_HOME、HADOOP_HOME、HADOOP_CONF_DIR、SPARK_MASTER_IP等</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/usr/lib/jvm/java
<span class="hljs-keyword">export</span> SCALA_HOME=/root/scala
<span class="hljs-keyword">export</span> HADOOP_HOME=/usr/lib/hadoop
<span class="hljs-keyword">export</span> HADOOP_CONF_DIR=<span class="hljs-variable">$HADOOP_HOME</span>/etc/hadoop 
<span class="hljs-keyword">export</span> YARN_CONF_DIR=<span class="hljs-variable">$HADOOP_HOME</span>/etc/hadoop
<span class="hljs-keyword">export</span> SPARK_LAUNCH_WITH_SCALA=<span class="hljs-number">0</span>
<span class="hljs-keyword">export</span> SPARK_WORKER_MEMORY=<span class="hljs-number">1</span>g
<span class="hljs-keyword">export</span> SPARK_DRIVER_MEMORY=<span class="hljs-number">1</span>g
<span class="hljs-keyword">export</span> SPARK_MASTER_IP=<span class="hljs-number">192.168</span>.<span class="hljs-number">117.51</span>
<span class="hljs-keyword">export</span> SPARK_LIBRARY_PATH=/root/spark-without-hive/lib
<span class="hljs-keyword">export</span> SPARK_MASTER_WEBUI_PORT=<span class="hljs-number">18080</span>
<span class="hljs-keyword">export</span> SPARK_WORKER_DIR=/root/spark-without-hive/work
<span class="hljs-keyword">export</span> SPARK_MASTER_PORT=<span class="hljs-number">7077</span>
<span class="hljs-keyword">export</span> SPARK_WORKER_PORT=<span class="hljs-number">7078</span>
<span class="hljs-keyword">export</span> SPARK_LOG_DIR=/root/spark-without-hive/log
<span class="hljs-keyword">export</span> SPARK_PID_DIR=<span class="hljs-string">'/root/spark-without-hive/run'</span></code></pre>

<ul>
<li>slaves（将所有节点都加入，master节点同时也是worker节点）</li>
</ul>



<pre class="prettyprint"><code class=" hljs ">Goblin01
Goblin02
Goblin03
Goblin04</code></pre>

<ul>
<li>spark-defaults.conf</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm"> spark<span class="hljs-preprocessor">.master</span>                     yarn-cluster
 spark<span class="hljs-preprocessor">.home</span>                       /root/spark-without-hive
 spark<span class="hljs-preprocessor">.eventLog</span><span class="hljs-preprocessor">.enabled</span>           true
 spark<span class="hljs-preprocessor">.eventLog</span><span class="hljs-preprocessor">.dir</span>               hdfs://Goblin01:<span class="hljs-number">8020</span>/spark-log
 spark<span class="hljs-preprocessor">.serializer</span>                 org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.KryoSerializer</span>
 spark<span class="hljs-preprocessor">.executor</span><span class="hljs-preprocessor">.memory</span>            <span class="hljs-number">1</span>g
 spark<span class="hljs-preprocessor">.driver</span><span class="hljs-preprocessor">.memory</span>              <span class="hljs-number">1</span>g
 spark<span class="hljs-preprocessor">.executor</span><span class="hljs-preprocessor">.extraJavaOptions</span>  -XX:+PrintGCDetails -Dkey=value -Dnumbers=<span class="hljs-string">"one two three"</span></code></pre>

<p>spark.master指定Spark运行模式，可以是yarn-client、yarn-cluster…</p>

<p>spark.home指定SPARK_HOME路径</p>

<p>spark.eventLog.enabled需要设为true</p>

<p>spark.eventLog.dir指定路径，放在master节点的hdfs中，端口要跟hdfs设置的端口一致（默认为8020），否则会报错</p>

<p>spark.executor.memory和spark.driver.memory指定executor和dirver的内存，512m或1g，既不能太大也不能太小，因为太小运行不了，太大又会影响其他服务</p>



<h2 id="三配置yarn">三、配置YARN</h2>

<p>配置yarn-site.xml，跟hdfs-site.xml在同一个路径下（$HADOOP_HOME/etc/hadoop)</p>



<pre class="prettyprint"><code class=" hljs avrasm">&lt;property&gt;
  &lt;name&gt;yarn<span class="hljs-preprocessor">.resourcemanager</span><span class="hljs-preprocessor">.scheduler</span><span class="hljs-preprocessor">.class</span>&lt;/name&gt;
  &lt;value&gt;org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.server</span><span class="hljs-preprocessor">.resourcemanager</span><span class="hljs-preprocessor">.scheduler</span><span class="hljs-preprocessor">.fair</span><span class="hljs-preprocessor">.FairScheduler</span>&lt;/value&gt;
&lt;/property&gt;</code></pre>



<h2 id="四配置hive">四、配置Hive</h2>

<ul>
<li>添加spark依赖到hive(将spark-assembly-1.5.0-cdh5.5.1-hadoop2.6.0.jar拷贝到$HIVE_HOME/lib目录下）</li>
</ul>

<p>进入SPARK_HOME</p>

<pre><code>cp spark-assembly-1.5.0-cdh5.5.1-hadoop2.6.0.jar /usr/lib/hive/lib
</code></pre>

<ul>
<li>配置hive-site.xml</li>
</ul>

<p>配置的内容与spark-defaults.conf相同，只是形式不一样,以下内容是追加到hive-site.xml文件中的,并且注意前两个配置，如果不设置hive的spark引擎用不了，在后面会有详细的错误说明。</p>



<pre class="prettyprint"><code class=" hljs xml">
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.execution.engine<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>spark<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.enable.spark.execution.engine<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.home<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/root/spark-without-hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.master<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn-client<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.enentLog.enabled<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.enentLog.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://Goblin01:8020/spark-log<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.serializer<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.spark.serializer.KryoSerializer<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.executor.memeory<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1g<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.driver.memeory<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1g<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.executor.extraJavaOptions<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>-XX:+PrintGCDetails -Dkey=value -Dnumbers="one two three"<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>



<h2 id="五验证是否安装配置成功">五、验证是否安装配置成功</h2>



<h3 id="1验证spark集群">1.验证Spark集群</h3>

<p><strong>注意：在启动Spark集群之前，要确保Hadoop集群和YARN均已启动</strong></p>

<ul>
<li>进入$SPARK_HOME目录，执行：</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql">./sbin/<span class="hljs-operator"><span class="hljs-keyword">start</span>-<span class="hljs-keyword">all</span>.sh</span></code></pre>

<p>用jps命令查看51节点上的master和worker，52、53、54节点上的worker是否都启动了</p>

<ul>
<li>同样在$SPARK_HOME目录下，提交计算Pi的任务，验证Spark集群是否能正常工作，运行如下命令</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">./bin/spark-submit --class org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.examples</span><span class="hljs-preprocessor">.SparkPi</span> --master yarn --deploy-mode client lib/spark-examples-<span class="hljs-number">1.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.5</span><span class="hljs-number">.1</span>-hadoop2<span class="hljs-number">.6</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span> <span class="hljs-number">10</span></code></pre>

<p>若无报错，并且算出Pi的值，说明Spark集群能正常工作</p>



<h3 id="2验证hive-on-spark是否可用">2.验证Hive on Spark是否可用</h3>

<ul>
<li>命令行输入 hive，进入hive CLI</li>
<li>set hive.execution.engine=spark; (将执行引擎设为Spark，默认是mr，退出hive CLI后，回到默认设置。若想让引擎默认为Spark，需要在hive-site.xml里设置）</li>
<li>create table test(ts BIGINT,line STRING); (创建表）</li>
<li>select count(*) from test;</li>
<li>若整个过程没有报错，并出现正确结果，则Hive on Spark配置成功。</li>
</ul>



<h2 id="六遇到的问题">六、遇到的问题</h2>



<h3 id="0">0</h3>

<p>编译spark基于maven有两种方式</p>

<ul>
<li>用mvn 命令编译</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/build/mvn <span class="hljs-attribute">-Pyarn</span> <span class="hljs-attribute">-Phadoop</span><span class="hljs-subst">-</span><span class="hljs-number">2.6</span> <span class="hljs-attribute">-Dhadoop</span><span class="hljs-built_in">.</span>version<span class="hljs-subst">=</span><span class="hljs-number">2.6</span><span class="hljs-number">.0</span> <span class="hljs-attribute">-DskipTests</span> clean package </code></pre>

<p>编译到倒数MQTT模块一直报错，而且编译出的文件比较大，不适合安装集群，因此不推荐。使用Intellij IDEA maven 插件报错如下：</p>

<p><img src="https://img-blog.csdn.net/20160826192604459" alt="这里写图片描述" title=""></p>

<ul>
<li>使用spark提供的预编译脚本，网络状况稳定，会编译出需要的安装版本，<strong><em>推荐</em></strong>。命令</li>
</ul>

<pre class="prettyprint"><code class=" hljs avrasm">./make-distribution<span class="hljs-preprocessor">.sh</span> --name <span class="hljs-string">"hadoop2-without-hive"</span> --tgz <span class="hljs-string">"-Pyarn,hadoop-provided,hadoop-2.6,parquet-provided"</span></code></pre>

<p>结果如上文所述。</p>



<h3 id="1">1</h3>

<p>运行：</p>



<pre class="prettyprint"><code class=" hljs avrasm">./bin/spark-submit --class org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.examples</span><span class="hljs-preprocessor">.SparkPi</span>  --master yarn  lib/spark-examples-<span class="hljs-number">1.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.5</span><span class="hljs-number">.1</span>-hadoop2<span class="hljs-number">.6</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span> <span class="hljs-number">10</span></code></pre>

<p>报错：</p>

<p><img src="https://img-blog.csdn.net/20160826192623178" alt="这里写图片描述" title=""></p>

<p>原因：</p>

<p>hdfs的默认端口为8020 ，而我们在spark-default.conf中配置成了8021端口，导致连接不上HDFS报错</p>

<pre class="prettyprint"><code class=" hljs avrasm">spark<span class="hljs-preprocessor">.eventLog</span><span class="hljs-preprocessor">.enabled</span>           true
spark<span class="hljs-preprocessor">.eventLog</span><span class="hljs-preprocessor">.dir</span>              hdfs://Goblin01:<span class="hljs-number">8021</span>/spark-log</code></pre>

<p>解决：</p>

<p>配置spark-default.conf中的spark.eventLog.dir 为本地路径，也就是不持久化日志到hdfs上，也就没有和hdfs的通行</p>

<p>or</p>

<p>spark-default.conf 注释掉 spark.eventLog.enabled   true</p>

<p>or</p>

<p>在spark-default.conf里配置的eventLog端口跟hdfs的默认端口（8020）一致</p>

<p>or</p>

<p>由于配置的hdfs是高可用的，51,52都可以作为namenode,我们的spark集群的主节点在51上，当51上的namenode变成standby，导致无法访问hdfs的8020端口（hdfs默认端口），也就是说在51上读不出hdfs上spark-log的内容，在spark-default.conf中配置为spark.eventLog.dir   hdfs://Goblin01:8021/spark-log，如果发生这种情况，直接kill掉52，让namenode只在51上运行。（这个后面要搭建spark的高可用模式解决）</p>



<h3 id="2">2</h3>

<p>运行：</p>

<p>在hive里设置引擎为spark，执行select count(*) from a; </p>

<p>报错：</p>



<pre class="prettyprint"><code class=" hljs ocaml">Failed <span class="hljs-keyword">to</span> execute spark task, <span class="hljs-keyword">with</span> <span class="hljs-keyword">exception</span> <span class="hljs-string">'org.apache.hadoop.hive.ql.metadata.HiveException(Unsupported execution engine: Spark. Please set hive.execution.engine=mr)'</span></code></pre>

<p>解决：</p>

<p>这是因为CDH版的Hive默认运行支持Hive on Spark（By default, Hive on Spark is not enabled）.</p>

<p>需要用cloudera manager（cloudera官网给的的方法，但是要装cloudera manager，比较麻烦，不建议）</p>



<pre class="prettyprint"><code class=" hljs applescript">Go <span class="hljs-keyword">to</span> <span class="hljs-keyword">the</span> Hive service.
Click <span class="hljs-keyword">the</span> Configuration <span class="hljs-constant">tab</span>.
Enter Enable Hive <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> Sparkin <span class="hljs-keyword">the</span> Search field.
Check <span class="hljs-keyword">the</span> box <span class="hljs-keyword">for</span> Enable Hive <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> Spark (Unsupported).
Locate <span class="hljs-keyword">the</span> Spark On YARN Service <span class="hljs-keyword">and</span> click SPARK_ON_YARN.
Click Save Changes <span class="hljs-keyword">to</span> commit <span class="hljs-keyword">the</span> changes.</code></pre>

<p>或者</p>

<p>在hive-site.xml添加配置(简单、推荐）</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.enable.spark.execution.engine<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>



<h3 id="3">3</h3>

<p>终端输入hive无法启动hive CLI</p>

<p>原因：namenode挂了</p>

<p>解决：重启namenode</p>



<h3 id="4">4</h3>

<p>运行：</p>



<pre class="prettyprint"><code class=" hljs avrasm">./bin/spark-submit --class org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.examples</span><span class="hljs-preprocessor">.SparkPi</span> --master yarn --deploy-mode client lib/spark-examples-<span class="hljs-number">1.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.5</span><span class="hljs-number">.1</span>-hadoop2<span class="hljs-number">.6</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.jar</span> <span class="hljs-number">10</span></code></pre>

<p>问题：</p>

<p>没有报错，但是出现以下情况，停不下来</p>

<p><img src="https://img-blog.csdn.net/20160826192717251" alt="这里写图片描述" title=""></p>

<p>原因：</p>

<ol>
<li>ResourceManager或者NodeManager挂掉，一直没有NodeManager响应，任务无法执行，所有停不下来。</li>
<li>还有一种情况是spark有别的application在运行，导致本次spark任务的等待或者失败</li>
</ol>

<p>解决：</p>

<ol>
<li>对于原因1，重启ResourceManager和NodeManager。</li>
</ol>

<pre class="prettyprint"><code class=" hljs sql">service hadoop-yarn-resourcemanager <span class="hljs-operator"><span class="hljs-keyword">start</span>;</span>
service hadoop-yarn-nodemanager <span class="hljs-operator"><span class="hljs-keyword">start</span>;</span></code></pre>

<ol>
<li>对于原因2，解决办法是在hadoop配置文件中设置yarn的并行度，在<code>/etc/hadoop/conf/capacity-scheduler.xml</code>文件中配置yarn.scheduler.capacity.maximum-am-resource-percent from 0.1 to 0.5</li>
</ol>



<pre class="prettyprint"><code class=" hljs applescript"> &lt;<span class="hljs-keyword">property</span>&gt;
    &lt;<span class="hljs-property">name</span>&gt;yarn.scheduler.capacity.maximum-am-resource-percent&lt;/<span class="hljs-property">name</span>&gt;
    &lt;value&gt;<span class="hljs-number">0.5</span>&lt;/value&gt;
    &lt;description&gt;
      Maximum percent <span class="hljs-keyword">of</span> resources <span class="hljs-keyword">in</span> <span class="hljs-keyword">the</span> cluster which can be used <span class="hljs-keyword">to</span> <span class="hljs-command">run</span>
      <span class="hljs-type">application</span> masters i.e. controls <span class="hljs-type">number</span> <span class="hljs-keyword">of</span> concurrent <span class="hljs-property">running</span>
      applications.
    &lt;/description&gt;
  &lt;/<span class="hljs-keyword">property</span>&gt;
</code></pre>



<h2 id="七参考资料">七、参考资料</h2>

<ul>
<li><p><a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started</a></p></li>
<li><p><a href="http://www.cloudera.com/documentation/enterprise/5-5-x/topics/admin_hos_config.html" rel="nofollow">http://www.cloudera.com/documentation/enterprise/5-5-x/topics/admin_hos_config.html</a></p></li>
<li><a href="http://spark.apache.org/docs/latest/building-spark.html" rel="nofollow">http://spark.apache.org/docs/latest/building-spark.html</a></li>
<li><a href="http://stackoverflow.com/questions/31743586/apache-spark-running-locally-giving-refused-connection-error" rel="nofollow">http://stackoverflow.com/questions/31743586/apache-spark-running-locally-giving-refused-connection-error</a></li>
<li><p><a href="http://stackoverflow.com/questions/30828879/application-report-for-application-state-accepted-never-ends-for-spark-submi" rel="nofollow">http://stackoverflow.com/questions/30828879/application-report-for-application-state-accepted-never-ends-for-spark-submi</a></p></li>
<li><p><a href="http://www.voidcn.com/blog/tianyiii/article/p-5986990.html" rel="nofollow">http://www.voidcn.com/blog/tianyiii/article/p-5986990.html</a></p></li>
<li><p><a href="http://www.imooc.com/article/8613" rel="nofollow">http://www.imooc.com/article/8613</a></p></li>
<li><p><a href="http://lxw1234.com/archives/2016/05/673.htm" rel="nofollow">http://lxw1234.com/archives/2016/05/673.htm</a></p></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
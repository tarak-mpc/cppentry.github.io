---
layout:     post
title:      hive on spark入门安装(hive2.0、spark1.5)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载，转载时请注明出处。欢迎留言讨论，大数据讨论qq群201011292					https://blog.csdn.net/Dante_003/article/details/54944399				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="简介">简介</h2>

<p>hive on spark安装，hive是基于hadoop的数据仓库，hdfs为hive存储空间，mapreduce为hive的sql计算引擎。但是由于mapreduce很多计算过程都要经过硬盘读写等劣势，和spark等计算引擎相比，无论是计算速度，还是计算灵活度上都有很多劣势，这也导致了hive on mapreduce计算速度并不是令人很满意。本篇来讲下hive on spark，将hive的计算引擎替换为spark，速度将有很大的提升.</p>



<h2 id="环境">环境</h2>

<p>centos6 <br>
hadoop2.6集群，需要hdfs、yarn <br>
hive2.0.0 <br>
spark1.5源码 <br>
maven3.3（自行安装） <br>
jdk1.7（自行安装） <br>
scala2.10（自行安装）</p>



<h2 id="安装配置">安装配置</h2>

<p><font color="red" size="5">注意：安装时需要注意几个组件的安装节点。</font> <br>
例如我的机器环境 <br>
<strong>node21</strong> <br>
hdfs:namenode,secondarynode,datanode <br>
yarn:resourcemanager,nodemanager <br>
hive <br>
spark <br>
<strong>node22</strong> <br>
hdfs:datanode <br>
yarn:nodemanager <br>
<strong>node23</strong> <br>
hdfs:datanode <br>
yarn:nodemanager <br>
<font color="red" size="4">以上hadoop中的hdfs和yarn安装后（我的机器不多将主节点也当datanode和nodemanager了）。 <br>
<strong>hive和spark必须在一个节点上</strong>。因为hive要用spark客户端来启动服务。 <br>
<strong>hive和spark必须要在hadoop的其中一个节点上</strong>。因为hive是一定要依赖hdfs的，hive启动的时候要拿环境变量HADOOP_HOME去拿hdfs相关文件，根据spark的启动模式yarn、standalone、mesos来选择是否去拿yarn配置文件，本文用的yarn的启动模式，其它几种模式大家也可以试试。 <br>
<strong>如果用yarn部署模式不用每台机器都部署hive和spark</strong>，hive只是单个服务，不用部署多个，他只存逻辑表的元信息，接收sql任务，翻译成相应的spark任务并执行。用yarn部署模式的话，hive启动spark任务的时候会将spark-assmebly包上传到hdfs上，yarn根据哪些节点启动了spark任务，这些节点自动去hdfs上下载，所以不用每台机器都部署spark，部署了yarn启动时也不用。 <br>
下面的安装过程是先安装hadoop集群（hdfs和yarn），然后在namenode上面部署了spark和hive并配置相关配置文件。 <br>
</font> <br>
安装参考官网  <br>
<a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started" rel="nofollow" target="_blank">https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started</a></p>



<h3 id="1hadoop">1.hadoop</h3>



<h4 id="11hadoop26集群安装略">1.1.hadoop2.6集群安装略</h4>

<p>参考 <a href="http://blog.csdn.net/dante_003/article/details/54943774" rel="nofollow" target="_blank">http://blog.csdn.net/dante_003/article/details/54943774</a></p>



<h3 id="2spark">2.spark</h3>



<h4 id="21spark编译">2.1.spark编译</h4>

<p>预编译好的spark是不能部署hive on spark的，因为spark里面默认打入了hive的包（sparksql以及sqlcontext这些都是基于hive做的），spark里面的hive版本和我们安装的hive版本不一样，而且spark里面的hive是被修改过的，所以要将spark里面的hive包给去掉（同理，去掉hive的spark客户端就不能运行sparksql和sqlcontext了），否则执行时会报错。 <br>
从github的地址上下载spark的源码<a href="https://github.com/apache/spark/tree/branch-1.5" rel="nofollow" target="_blank">https://github.com/apache/spark/tree/branch-1.5</a> <br>
我从spark官网上下载的1.5源码编译报错 <br>
下载完成后进入源码根目录执行下面命令，编译时需要用到scala2.10（scala版本要对应，有的反应版本不对编译错误或编译出来的有问题）、jdk1.7、maven3.3</p>



<pre class="prettyprint"><code class="language-bash hljs "><span class="hljs-number">2.0</span>版本之前编译
./make-distribution.sh --name <span class="hljs-string">"hadoop2-without-hive"</span> --tgz <span class="hljs-string">"-Pyarn,hadoop-provided,hadoop-2.6,parquet-provided"</span>


<span class="hljs-number">2.0</span>版本之后编译
./dev/make-distribution.sh --name <span class="hljs-string">"hadoop2-without-hive"</span> --tgz <span class="hljs-string">"-Pyarn,hadoop-provided,hadoop-2.7,parquet-provided"</span></code></pre>

<p>编译刚开始应该是在扫描文件，会等几分钟才开始编译，不要认为编译失败卡住了，耐心等待，整个编译过程大约几个小时，主要是要下很多maven包。有资源的朋友可以用vpn或者在aws的ec2上编译。 <br>
当出现build success时，说明编译成功，切在根目录里面多出了一个tar.gz包spark-1.5.3-SNAPSHOT-bin-hadoop2-without-hive.tgz</p>

<h4 id="22spark部署">2.2spark部署</h4>

<p>将上一部编译成功的tar.gz包解压到你安装的文件夹。</p>



<pre class="prettyprint"><code class="language-bash hljs ">tar xzvf spark-<span class="hljs-number">1.5</span>.<span class="hljs-number">3</span>-SNAPSHOT-bin-hadoop2-without-hive.tgz -C /opt
<span class="hljs-built_in">cd</span> /opt
mv spark-<span class="hljs-number">1.5</span>.<span class="hljs-number">3</span>-SNAPSHOT-bin-hadoop2-without-hive spark</code></pre>

<p>配置环境变量</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> SPARK_HOME=/opt/spark
<span class="hljs-built_in">source</span> /etc/profile</code></pre>



<h4 id="23spark配置">2.3spark配置</h4>

<ul>
<li>spark-env.sh</li>
</ul>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/opt/java/jdk
<span class="hljs-keyword">export</span> SCALA_HOME=/opt/scala
<span class="hljs-keyword">export</span> HADOOP_HOME=/opt/hadoop
<span class="hljs-keyword">export</span> HADOOP_CONF_DIR=<span class="hljs-variable">$HADOOP_HOME</span>/etc/hadoop
<span class="hljs-keyword">export</span> YARN_CONF_DIR=<span class="hljs-variable">$HADOOP_HOME</span>/etc/hadoop
<span class="hljs-keyword">export</span> SPARK_LAUNCH_WITH_SCALA=<span class="hljs-number">0</span>
<span class="hljs-keyword">export</span> SPARK_WORKER_MEMORY=<span class="hljs-number">1</span>g
<span class="hljs-keyword">export</span> SPARK_DRIVER_MEMORY=<span class="hljs-number">1</span>g
<span class="hljs-keyword">export</span> SPARK_MASTER_IP=<span class="hljs-number">192.168</span>.<span class="hljs-number">1.21</span>
<span class="hljs-keyword">export</span> SPARK_LIBRARY_PATH=/opt/spark/lib
<span class="hljs-keyword">export</span> SPARK_MASTER_WEBUI_PORT=<span class="hljs-number">18080</span>
<span class="hljs-keyword">export</span> SPARK_WORKER_DIR=/opt/spark/work
<span class="hljs-keyword">export</span> SPARK_MASTER_PORT=<span class="hljs-number">7077</span>
<span class="hljs-keyword">export</span> SPARK_WORKER_PORT=<span class="hljs-number">7078</span>
<span class="hljs-comment">##下面这个参数不配置会说找不到相关jar包</span>
<span class="hljs-keyword">export</span> SPARK_DIST_CLASSPATH=$(/opt/hadoop/bin/hadoop classpath)</code></pre>

<ul>
<li>spark-defaults.conf <br>
配置spark启动的默认参数</li>
</ul>



<pre class="prettyprint"><code class="language-bash hljs ">spark.master yarn-cluster
spark.home /opt/spark
spark.serializer org.apache.spark.serializer.KryoSerializer
spark.executor.memory <span class="hljs-number">1</span>g
spark.driver.memory <span class="hljs-number">1</span>g
spark.executor.extraJavaOptions -XX:+PrintGCDetails -Dkey=value -Dnumbers=<span class="hljs-string">"one two three"</span>
</code></pre>



<h3 id="3hive">3.hive</h3>



<h4 id="31hive安装配置略">3.1.hive安装配置略</h4>

<p>参考<a href="http://blog.csdn.net/dante_003/article/details/54944319" rel="nofollow" target="_blank">http://blog.csdn.net/dante_003/article/details/54944319</a></p>



<h4 id="32配置">3.2.配置</h4>

<ul>
<li><p>拷贝spark包到hive下面 <br>
拷贝spark/lib/spark-assembly-1.5.3-SNAPSHOT-hadoop2.6.0.jar 到 hive/lib下面</p></li>
<li><p>hive-site.xml</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs xml">      <span class="hljs-comment">&lt;!-- 元数据相关，存储在mysql，没有mysql驱动的要把mysql驱动拷贝到hive的lib下面  --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://192.168.1.82:3306/hive?createDatabaseIfNotExist=true&amp;useUnicode=true&amp;characterEncoding=UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>JDBC connect string for a JDBC metastore<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.mysql.jdbc.Driver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Driver class name for a JDBC metastore<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>root<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>username to use against metastore database<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>123456<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>password to use against metastore database<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-comment">&lt;!--spark --&gt;</span>
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
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/spark<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-comment">&lt;!--sparkcontext --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.master<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn-cluster<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
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
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.executor.cores<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>2<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.executor.instances<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>4<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.app.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>myInceptor<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-comment">&lt;!--事务相关 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.support.concurrency<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.enforce.bucketing<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.exec.dynamic.partition.mode<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>nonstrict<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.txn.manager<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.hadoop.hive.ql.lockmgr.DbTxnManager<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.compactor.initiator.on<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.compactor.worker.threads<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>spark.executor.extraJavaOptions<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>-XX:+PrintGCDetails -Dkey=value -Dnumbers="one two three"
        <span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-comment">&lt;!--其它 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.server2.enable.doAs<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>



<h2 id="启动">启动</h2>



<h3 id="启动hadoop">启动hadoop</h3>

<p>hadoop/sbin/start-all.sh <br>
查看相关web页面，保证hdfs和yarn都启动。</p>



<h3 id="spark">spark</h3>

<p>spark不用启动，和hive在一个节点上就可以</p>



<h3 id="启动hive">启动hive</h3>

<p>（也可以启动hiveserver2 通过beeline进入到hive，输入sql执行任务，启动hiveserver2参考<a href="http://blog.csdn.net/dante_003/article/details/54944319" rel="nofollow" target="_blank">http://blog.csdn.net/dante_003/article/details/54944319</a>） <br>
bin/hive <br>
进入命令行模式，运行统计数量的命令 <br>
select count(*) from test; <br>
第一次运行会比较慢，因为第一次hive要启动spark集群。 <br>
在yarn的页面里面可以看到hive在yarn上面启动了一个spark长服务 <br>
<img src="https://img-blog.csdn.net/20170209102130232?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFudGVfMDAz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
点击右侧的applicationmaster可以看到spark集群的信息 <br>
<img src="https://img-blog.csdn.net/20170209102304591?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvRGFudGVfMDAz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
以上可以在hive里面运行其它sql，查看spark集群的执行情况。 <br>
hive on spark成功了。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
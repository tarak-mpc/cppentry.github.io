---
layout:     post
title:      hive集群配置 hive on spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="hive_hive_on_spark_0"></a>hive集群配置 hive on spark</h1>
<p>标签（空格分隔）： hive</p>
<hr>
<p>##HiveServer2的高可用-HA配置</p>
<blockquote>
<p><a href="http://lxw1234.com/archives/2016/05/675.htm" rel="nofollow">HiveServer2的高可用-HA配置</a></p>
</blockquote>
<p>##hive on spark</p>
<blockquote>
<p><a href="http://blog.csdn.net/jsin31/article/details/69372148" rel="nofollow">基于Spark 2.0.0搭建Hive on Spark环境</a><br>
<a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started" rel="nofollow">官方文档</a></p>
</blockquote>
<p>###编译spark</p>
<blockquote>
<p>hive on spark要求spark编译时不集成hive，编辑命令如下，需要安装maven，命令中hadoop版本根据实际情况调整</p>
</blockquote>
<pre><code>#Spark 2.0.0以后
./dev/make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.6,parquet-provided"
</code></pre>
<p>###环境变量</p>
<blockquote>
<p>在/etc/profile中加入以下环境变量</p>
</blockquote>
<pre><code>export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop  
export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop  
</code></pre>
<p>###hive配置</p>
<blockquote>
<p>在hive-site.xml中加入以下配置，同时将spark的jars目录中的jar文件传到hdfs对应目录下</p>
</blockquote>
<pre><code>#创建hdfs目录
hadoop fs -mkdir /spark
#上传/application/spark/jars文件夹到hdfs的/spark目录
hadoop fs -put /application/spark/jars/ /spark/
</code></pre>
<pre class=" language-xml"><code class="prism  language-xml"><span class="token comment">&lt;!-- hive 2.2.0以后,这项配置必须放在hive-site.xml中 --&gt;</span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>  
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>spark.yarn.jars<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>  
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>hdfs://xxxx:9000/spark/jars/*.jar<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>  
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>###spark配置yarn模式</p>
<blockquote>
<p>在spark-env.sh中加入以下配置，注意不要有spark集群的配置，会导致hive on spark出现异常</p>
</blockquote>
<pre><code>#注意$(hadoop classpath)需要支持hadoop命令可执行，可以修改成根目录形式$(/application/hadoop-2.6.4/bin/hadoop classpath)
export SPARK_DIST_CLASSPATH=$(hadoop classpath)
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export YARN_CONF_DIR=$HADOOP_HOME/etc/hadoop
</code></pre>
<blockquote>
<p>在spark-defaults.conf中加入以下配置，需要将该文件放入$HIVE_HOME/conf目录下</p>
</blockquote>
<pre><code>spark.master yarn
spark.submit.deployMode client
spark.eventLog.enabled true
spark.eventLog.dir hdfs://dashuju174:9000/spark/logs

spark.driver.memory 512m
spark.driver.cores 1
spark.executor.memory 512m
spark.executor.cores 1
spark.executor.instances 2
spark.serializer org.apache.spark.serializer.KryoSerializer
spark.yarn.jars hdfs://dashuju174:9000/spark/jars/*.jar
</code></pre>
<h4><a id="yarn4_66"></a><a href="https://blog.csdn.net/sinat_29581293/article/details/58143159" rel="nofollow">yarn调度模式配置</a></h4>
<blockquote>
<p>官方建议修改yarn的调度模式为FAIR，其他调度模式其实也可以使用<br>
修改yarn-site.xml</p>
</blockquote>
<pre><code>  &lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.scheduler.class&lt;/name&gt;
    &lt;value&gt;org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler&lt;/value&gt;
  &lt;/property&gt;
</code></pre>
<blockquote>
<p>当启动spark任务时，会根据spark-defaule.conf中的配置向yarn请求内存和CPU，计算公式参考<a href="https://blog.csdn.net/lingbo229/article/details/80914283" rel="nofollow">Spark 2.2 内存占用计算公式</a><br>
以上面的配置为例：<br>
yarn.scheduler.minimum-allocation-mb=512M,spark.yarn.driver.memoryOverhead=384m(最小值)，spark.yarn.executor.memoryOverhead=384m(最小值)<br>
实际driver占用内存为spark.driver.memory+(max(spark.yarn.driver.memoryOverhead, 0.1<em>spark.driver.memory))=512m+(max(384m: 0.1</em>512m))=规整化后1G，然后再乘以spark.driver.cores*spark.executor.instances<br>
实际executor占用内存计算方法也一样<br>
实际的内存占用可以通过ApplicationMaster查看，开启ApplicationMaster命令：<a href="http://yarn-daemon.sh" rel="nofollow">yarn-daemon.sh</a> start proxyserver</p>
</blockquote>
<p>####yarn.scheduler.minimum-allocation-mb设置较小时解决办法</p>
<blockquote>
<p><a href="https://blog.csdn.net/hthou/article/details/53195319" rel="nofollow">spark ERROR YarnClientSchedulerBackend: Yarn application has already exited</a></p>
</blockquote>
<p>###异常</p>
<blockquote>
<p><a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Started#HiveonSpark:GettingStarted-CommonIssues%28Greenareresolved,willberemovedfromthislist%29:%24f;" rel="nofollow">官方文档常见异常</a></p>
</blockquote>
<p>####1、java.lang.NoClassDefFoundError: scala/collection/Iterable</p>
<blockquote>
<p>hive未加载spark的jar造成的问题<br>
按一下方式修改$HIVE_HOME/bin/hive文件</p>
</blockquote>
<pre><code>#原文件内容
for f in ${HIVE_LIB}/*.jar; do
    CLASSPATH=${CLASSPATH}:$f;
done

#新增以下代码
for f in ${SPARK_HOME}/jars/*.jar; do
     CLASSPATH=${CLASSPATH}:$f;
done
</code></pre>
<p>done</p>
<pre><code>
####2、unread block data
&gt; 在hive-site.xml中加入hbase的jar配置,spark.driver.extraClassPath和spark.executor.extraClassPath中内容相同
```xml
  &lt;property&gt;
    &lt;name&gt;spark.driver.extraClassPath&lt;/name&gt;
    &lt;value&gt;$SPARK_HOME/lib/mysql-connector-java-5.1.34.jar:$SPARK_HOME/lib/hbase-annotations-1.1.4.jar:$SPARK_HOME/lib/hbase-client-1.1.4.jar:$SPARK_HOME/lib/hbase-common-1.1.4.jar:$SPARK_HOME/lib/hbase-hadoop2-compat-1.1.4.jar:$SPARK_HOME/lib/hbase-hadoop-compat-1.1.4.jar:$SPARK_HOME/lib/hbase-protocol-1.1.4.jar:$SPARK_HOME/lib/hbase-server-1.1.4.jar:$SPARK_HOME/lib/hive-hbase-handler-2.3.2.jar:$SPARK_HOME/lib/htrace-core-3.1.0-incubating.jar&lt;/value&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;spark.executor.extraClassPath&lt;/name&gt;
    &lt;value&gt;$SPARK_HOME/lib/mysql-connector-java-5.1.34.jar:$SPARK_HOME/lib/hbase-annotations-1.1.4.jar:$SPARK_HOME/lib/hbase-client-1.1.4.jar:$SPARK_HOME/lib/hbase-common-1.1.4.jar:$SPARK_HOME/lib/hbase-hadoop2-compat-1.1.4.jar:$SPARK_HOME/lib/hbase-hadoop-compat-1.1.4.jar:$SPARK_HOME/lib/hbase-protocol-1.1.4.jar:$SPARK_HOME/lib/hbase-server-1.1.4.jar:$SPARK_HOME/lib/hive-hbase-handler-2.3.2.jar:$SPARK_HOME/lib/htrace-core-3.1.0-incubating.jar&lt;/value&gt;
  &lt;/property&gt;
</code></pre>
<p>####ClassNotFoundException: org.apache.hadoop.hive.hbase.HiveHBaseTableInputFormat</p>
<blockquote>
<p>实际上在集成之前在集成hbase时，hive是不缺少jar包的，在spark.executor.extraClassPath和spark.driver.extraClassPath加入了hive-hbase-handler-2.3.2.jar之后，就出现了这个异常可以在hive.aux.jars.path中加入</p>
</blockquote>
<pre class=" language-xml"><code class="prism  language-xml">  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>hive.aux.jars.path<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>/home/hadoop/application/hive/lib/hive-hbase-handler-2.3.2.jar<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<blockquote>
<p>或在hive命令行中执行以下命令解决</p>
</blockquote>
<pre><code>add jar /home/hadoop/application/hive/lib/hive-hbase-handler-2.3.2.jar
</code></pre>
<p>##namenode HA问题</p>
<blockquote>
<p>当namedode存在HA时,hdfs的访问地址需要改为hadoop/etc/hadoop/hdfs-site.xml 中dfs.nameservices配置的值，不需要指定端口例如hdfs://hadoop-cluster/xxx</p>
</blockquote>
<p>####hive on spark各种ClassNotFoundException</p>
<blockquote>
<p>检查hive-site.xml,spark-default.xml中spark.yarn.jars是否配置正确<br>
检查hdfs://xxx/spark/jars中jar包是否是<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi>S</mi><mi>P</mi><mi>A</mi><mi>R</mi><msub><mi>K</mi><mi>H</mi></msub><mi>O</mi><mi>M</mi><mi>E</mi><mi mathvariant="normal">/</mi><mi>j</mi><mi>a</mi><mi>r</mi><mi>s</mi><mi mathvariant="normal">中</mi><mi>j</mi><mi>a</mi><mi>r</mi><mi mathvariant="normal">包</mi><mi mathvariant="normal">，</mi><mi mathvariant="normal">不</mi><mi mathvariant="normal">要</mi><mi mathvariant="normal">多</mi><mi mathvariant="normal">或</mi><mi mathvariant="normal">者</mi><mi mathvariant="normal">少</mi><mi mathvariant="normal">，</mi><mi>s</mi><mi>p</mi><mi>a</mi><mi>r</mi><mi>k</mi><mn>2.0.2</mn><mi mathvariant="normal">为</mi><mn>93</mn><mi mathvariant="normal">个</mi><mi mathvariant="normal">，</mi><mi mathvariant="normal">版</mi><mi mathvariant="normal">本</mi><mi mathvariant="normal">不</mi><mi mathvariant="normal">要</mi><mi mathvariant="normal">弄</mi><mi mathvariant="normal">错</mi><mi mathvariant="normal">检</mi><mi mathvariant="normal">查</mi><mi mathvariant="normal">环</mi><mi mathvariant="normal">境</mi><mi mathvariant="normal">变</mi><mi mathvariant="normal">量</mi><mi>e</mi><mi>x</mi><mi>p</mi><mi>o</mi><mi>r</mi><mi>t</mi><mi>H</mi><mi>A</mi><mi>D</mi><mi>O</mi><mi>O</mi><msub><mi>P</mi><mi>C</mi></msub><mi>L</mi><mi>A</mi><mi>S</mi><mi>S</mi><mi>P</mi><mi>A</mi><mi>T</mi><mi>H</mi><mo>=</mo></mrow><annotation encoding="application/x-tex">SPARK_HOME/jars中jar包，不要多或者少，spark2.0.2为93个，版本不要弄错
检查环境变量export HADOOP_CLASSPATH=</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 1em; vertical-align: -0.25em;"></span><span class="mord mathit" style="margin-right: 0.05764em;">S</span><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="mord mathit">A</span><span class="mord mathit" style="margin-right: 0.00773em;">R</span><span class="mord"><span class="mord mathit" style="margin-right: 0.07153em;">K</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.328331em;"><span class="" style="top: -2.55em; margin-left: -0.07153em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight" style="margin-right: 0.08125em;">H</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"><span class=""></span></span></span></span></span></span><span class="mord mathit" style="margin-right: 0.02778em;">O</span><span class="mord mathit" style="margin-right: 0.10903em;">M</span><span class="mord mathit" style="margin-right: 0.05764em;">E</span><span class="mord">/</span><span class="mord mathit" style="margin-right: 0.05724em;">j</span><span class="mord mathit">a</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord mathit">s</span><span class="mord cjk_fallback">中</span><span class="mord mathit" style="margin-right: 0.05724em;">j</span><span class="mord mathit">a</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord cjk_fallback">包</span><span class="mord cjk_fallback">，</span><span class="mord cjk_fallback">不</span><span class="mord cjk_fallback">要</span><span class="mord cjk_fallback">多</span><span class="mord cjk_fallback">或</span><span class="mord cjk_fallback">者</span><span class="mord cjk_fallback">少</span><span class="mord cjk_fallback">，</span><span class="mord mathit">s</span><span class="mord mathit">p</span><span class="mord mathit">a</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord mathit" style="margin-right: 0.03148em;">k</span><span class="mord">2</span><span class="mord">.</span><span class="mord">0</span><span class="mord">.</span><span class="mord">2</span><span class="mord cjk_fallback">为</span><span class="mord">9</span><span class="mord">3</span><span class="mord cjk_fallback">个</span><span class="mord cjk_fallback">，</span><span class="mord cjk_fallback">版</span><span class="mord cjk_fallback">本</span><span class="mord cjk_fallback">不</span><span class="mord cjk_fallback">要</span><span class="mord cjk_fallback">弄</span><span class="mord cjk_fallback">错</span><span class="mord cjk_fallback">检</span><span class="mord cjk_fallback">查</span><span class="mord cjk_fallback">环</span><span class="mord cjk_fallback">境</span><span class="mord cjk_fallback">变</span><span class="mord cjk_fallback">量</span><span class="mord mathit">e</span><span class="mord mathit">x</span><span class="mord mathit">p</span><span class="mord mathit">o</span><span class="mord mathit" style="margin-right: 0.02778em;">r</span><span class="mord mathit">t</span><span class="mord mathit" style="margin-right: 0.08125em;">H</span><span class="mord mathit">A</span><span class="mord mathit" style="margin-right: 0.02778em;">D</span><span class="mord mathit" style="margin-right: 0.02778em;">O</span><span class="mord mathit" style="margin-right: 0.02778em;">O</span><span class="mord"><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="msupsub"><span class="vlist-t vlist-t2"><span class="vlist-r"><span class="vlist" style="height: 0.328331em;"><span class="" style="top: -2.55em; margin-left: -0.13889em; margin-right: 0.05em;"><span class="pstrut" style="height: 2.7em;"></span><span class="sizing reset-size6 size3 mtight"><span class="mord mathit mtight" style="margin-right: 0.07153em;">C</span></span></span></span><span class="vlist-s">​</span></span><span class="vlist-r"><span class="vlist" style="height: 0.15em;"><span class=""></span></span></span></span></span></span><span class="mord mathit">L</span><span class="mord mathit">A</span><span class="mord mathit" style="margin-right: 0.05764em;">S</span><span class="mord mathit" style="margin-right: 0.05764em;">S</span><span class="mord mathit" style="margin-right: 0.13889em;">P</span><span class="mord mathit">A</span><span class="mord mathit" style="margin-right: 0.13889em;">T</span><span class="mord mathit" style="margin-right: 0.08125em;">H</span><span class="mspace" style="margin-right: 0.277778em;"></span><span class="mrel">=</span></span></span></span></span>HBASE_HOME/lib/*:classpath<br>
修改配置或修改hdfs中jar之后都重启hive服务再测试</p>
</blockquote>
<p>###hive select count(*)可以执行，select显示hbase连接超时</p>
<blockquote>
<p>权限问题造成，修改hive-site.xml</p>
</blockquote>
<pre><code>  &lt;property&gt;
    &lt;name&gt;hive.server2.enable.doAs&lt;/name&gt;
    &lt;value&gt;false&lt;/value&gt;
  &lt;/property&gt;
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
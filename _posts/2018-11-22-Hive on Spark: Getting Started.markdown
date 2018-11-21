---
layout:     post
title:      Hive on Spark: Getting Started
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Hive on Spark provides Hive with the ability to utilize <a href="http://spark.apache.org/" rel="nofollow">Apache Spark</a> as its execution engine.</p>

<pre>
set hive.execution.engine=spark;</pre>

<p>Hive on Spark was added in <a href="https://issues.apache.org/jira/browse/HIVE-7292" rel="nofollow">HIVE-7292</a>.</p>

<h2 id="HiveonSpark:GettingStarted-VersionCompatibility">Version Compatibility</h2>

<p>Hive on Spark is only tested with a specific version of Spark, so a given version of Hive is only guaranteed to work with a specific version of Spark. Other versions of Spark may work with a given version of Hive, but that is not guaranteed. Below is a list of Hive versions and their corresponding compatible Spark versions.</p>

<table><tbody><tr><th>Hive Version</th>
			<th>Spark Version</th>
		</tr><tr><td>master</td>
			<td>2.3.0</td>
		</tr><tr><td colspan="1">3.0.x</td>
			<td colspan="1">2.3.0</td>
		</tr><tr><td>2.3.x</td>
			<td>2.0.0</td>
		</tr><tr><td>2.2.x</td>
			<td>1.6.0</td>
		</tr><tr><td>2.1.x</td>
			<td>1.6.0</td>
		</tr><tr><td>2.0.x</td>
			<td>1.5.0</td>
		</tr><tr><td colspan="1">1.2.x</td>
			<td colspan="1">1.3.1</td>
		</tr><tr><td colspan="1">1.1.x</td>
			<td colspan="1">1.2.0</td>
		</tr></tbody></table><h2 id="HiveonSpark:GettingStarted-SparkInstallation">Spark Installation</h2>

<p>Follow instructions to install Spark:  </p>

<p> YARN Mode: <a href="http://spark.apache.org/docs/latest/running-on-yarn.html" rel="nofollow">http://spark.apache.org/docs/latest/running-on-yarn.html</a> <br>
Standalone Mode: <a href="https://spark.apache.org/docs/latest/spark-standalone.html" rel="nofollow">https://spark.apache.org/docs/latest/spark-standalone.html</a></p>

<p>Hive on Spark supports <a href="http://spark.apache.org/docs/latest/running-on-yarn.html" rel="nofollow">Spark on YARN</a> mode as default.</p>

<p>For the installation perform the following tasks:</p>

<ol><li>Install Spark (either download pre-built Spark, or build assembly from source).  
	<ul><li>Install/build a compatible version.  Hive root <code>pom.xml</code>'s &lt;spark.version&gt; defines what version of Spark it was built/tested with. </li>
		<li>Install/build a compatible distribution.  Each version of Spark has several distributions, corresponding with different versions of Hadoop.</li>
		<li>Once Spark is installed, find and keep note of the &lt;spark-assembly-*.jar&gt; location.</li>
		<li>
		<p>Note that you must have a version of Spark which does <strong>not</strong> include the Hive jars. Meaning one which was not built with the Hive profile. If you will use Parquet tables, it's recommended to also enable the "parquet-provided" profile. Otherwise there could be conflicts in Parquet dependency. To remove Hive jars from the installation, simply use the following command under your Spark repository:</p>

		<p>Prior to Spark 2.0.0:</p>

		<pre>
./make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.4,parquet-provided"</pre>

		<p>Since Spark 2.0.0:</p>

		<pre>
./dev/make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.7,parquet-provided"</pre>

		<p>Since Spark 2.3.0:</p>

		<pre>
./dev/make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.7,parquet-provided,orc-provided"</pre>
		</li>
	</ul></li>
	<li>Start Spark cluster
	<ul><li>Keep note of the &lt;Spark Master URL&gt;.  This can be found in Spark master WebUI.</li>
	</ul></li>
</ol><h2 id="HiveonSpark:GettingStarted-ConfiguringYARN">Configuring YARN</h2>

<p>Instead of the <a href="https://hadoop.apache.org/docs/r2.4.1/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html" rel="nofollow">capacity scheduler</a>, the <a href="https://hadoop.apache.org/docs/r2.7.1/hadoop-yarn/hadoop-yarn-site/FairScheduler.html" rel="nofollow">fair scheduler</a> is required.  This fairly distributes an equal share of resources for jobs in the YARN cluster.</p>

<p>yarn.resourcemanager.scheduler.class=org.apache.hadoop.yarn.server.resourcemanager.scheduler.fair.FairScheduler</p>

<h2 id="HiveonSpark:GettingStarted-ConfiguringHive">Configuring Hive</h2>

<ol><li>
	<p>To add the Spark dependency to Hive:</p>

	<ul><li>Prior to Hive 2.2.0, link the spark-assembly jar to <code>HIVE_HOME/lib</code>.</li>
		<li>Since Hive 2.2.0, Hive on Spark runs with Spark 2.0.0 and above, which doesn't have an assembly jar.
		<ul><li>To run with YARN mode (either yarn-client or yarn-cluster), link the following jars to <code>HIVE_HOME/lib</code>.
			<ul><li>scala-library</li>
				<li>spark-core</li>
				<li>spark-network-common</li>
			</ul></li>
			<li>To run with LOCAL mode (for debugging only), link the following jars in addition to those above to <code>HIVE_HOME/lib</code>.
			<ul><li>chill-java  chill  jackson-module-paranamer  jackson-module-scala  jersey-container-servlet-core</li>
				<li>jersey-server  json4s-ast  kryo-shaded  minlog  scala-xml  spark-launcher</li>
				<li>spark-network-shuffle  spark-unsafe  xbean-asm5-shaded</li>
			</ul></li>
		</ul></li>
	</ul></li>
	<li>
	<p>Configure Hive execution engine to use Spark:</p>

	<pre>
set hive.execution.engine=spark;</pre>

	<p>See the <a href="https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties#ConfigurationProperties-Spark" rel="nofollow">Spark section of Hive Configuration Properties</a> for other properties for configuring Hive and the Remote Spark Driver.</p>

	<p> </p>
	</li>
	<li>
	<p>Configure Spark-application configs for Hive.  See: <a href="http://spark.apache.org/docs/latest/configuration.html" rel="nofollow">http://spark.apache.org/docs/latest/configuration.html</a>.  This can be done either by adding a file "spark-defaults.conf" with these properties to the Hive classpath, or by setting them on Hive configuration (<code>hive-site.xml</code>). For instance:</p>

	<pre>
set spark.master=&lt;Spark Master URL&gt;
set spark.eventLog.enabled=true;
set spark.eventLog.dir=&lt;Spark event log folder (must exist)&gt;
set spark.executor.memory=512m;              
set spark.serializer=org.apache.spark.serializer.KryoSerializer;</pre>

	<h3 id="HiveonSpark:GettingStarted-Configurationpropertydetails">Configuration property details</h3>

	<ul><li><code>spark.executor.memory</code>: Amount of memory to use per executor process.</li>
		<li><code>spark.executor.cores</code>: Number of cores per executor.</li>
		<li>
		<p><code>spark.yarn.executor.memoryOverhead</code>: The amount of off heap memory (in megabytes) to be allocated per executor, when running Spark on Yarn. This is memory that accounts for things like VM overheads, interned strings, other native overheads, etc. In addition to the executor's memory, the container in which the executor is launched needs some extra memory for system processes, and this is what this overhead is for.</p>
		</li>
		<li><code>spark.executor.instances</code>: The number of executors assigned to each application.</li>
		<li><code>spark.driver.memory</code>: The amount of memory assigned to the Remote Spark Context (RSC). We recommend 4GB.</li>
		<li><code>spark.yarn.driver.memoryOverhead</code>: We recommend 400 (MB).</li>
	</ul></li>
	<li>
	<p>Allow Yarn to cache necessary spark dependency jars on nodes so that it does not need to be distributed each time when an application runs.</p>

	<ul><li>
		<p>Prior to Hive 2.2.0, upload spark-assembly jar to hdfs file(for example: <a>hdfs://xxxx:8020/spark-assembly.jar</a>) and add following in hive-site.xml</p>

		<pre>
&lt;property&gt;
  &lt;name&gt;spark.yarn.jar&lt;/name&gt;
  &lt;value&gt;hdfs://xxxx:8020/spark-assembly.jar&lt;/value&gt;
&lt;/property&gt;</pre>
		</li>
		<li>
		<p>Hive 2.2.0, upload all jars in $SPARK_HOME/jars to hdfs folder(for example:<a>hdfs:///xxxx:8020/spark-jars</a>) and add following in hive-site.xml</p>

		<pre>
&lt;property&gt;
  &lt;name&gt;spark.yarn.jars&lt;/name&gt;
  &lt;value&gt;hdfs://xxxx:8020/spark-jars/*&lt;/value&gt;
&lt;/property&gt;</pre>
		</li>
	</ul></li>
</ol><h2 id="HiveonSpark:GettingStarted-ConfiguringSpark">Configuring Spark</h2>

<p>Setting executor memory size is more complicated than simply setting it to be as large as possible. There are several things that need to be taken into consideration:</p>

<ul><li>
	<p>More executor memory means it can enable mapjoin optimization for more queries.</p>
	</li>
	<li>
	<p>More executor memory, on the other hand, becomes unwieldy from GC perspective.</p>
	</li>
	<li>Some experiments shows that HDFS client doesn’t handle concurrent writers well, so it may face race condition if executor cores are too many. </li>
</ul><p>The following settings need to be tuned for the cluster, these may also apply to submission of Spark jobs outside of Hive on Spark:</p>

<table><tbody><tr><th colspan="1">Property</th>
			<th colspan="1">Recommendation</th>
		</tr><tr><td>spark.executor.cores</td>
			<td>Between 5-7, See tuning details section</td>
		</tr><tr><td>spark.executor.memory</td>
			<td>yarn.nodemanager.resource.memory-mb * (spark.executor.cores / yarn.nodemanager.resource.cpu-vcores) </td>
		</tr><tr><td>spark.yarn.executor.memoryOverhead</td>
			<td>15-20% of spark.executor.memory</td>
		</tr><tr><td colspan="1">spark.executor.instances</td>
			<td colspan="1">Depends on spark.executor.memory + spark.yarn.executor.memoryOverhead, see tuning details section.</td>
		</tr></tbody></table><h3 id="HiveonSpark:GettingStarted-TuningDetails">Tuning Details</h3>

<p>When running Spark on YARN mode, we generally recommend setting spark.executor.cores to be 5, 6 or 7, depending on what the typical node is divisible by. For instance, if yarn.nodemanager.resource.cpu-vcores is 19, then 6 is a better choice (all executors can only have the same number of cores, here if we chose 5, then every executor only gets 3 cores; if we chose 7, then only 2 executors are used, and 5 cores will be wasted). If it’s 20, then 5 is a better choice (since this way you’ll get 4 executors, and no core is wasted).</p>

<p>For<code> spark.executor.memory</code>, we recommend to calculate yarn.nodemanager.resource.memory-mb * (spark.executor.cores / yarn.nodemanager.resource.cpu-vcores) then split that between spark.executor.memory and <code>spark.yarn.executor.memoryOverhead</code>. According to our experiment, we recommend setting <code>spark.yarn.executor.memoryOverhead</code> to be around 15-20% of the total memory.</p>

<p>After you’ve decided on how much memory each executor receives, you need to decide how many executors will be allocated to queries. In the GA release Spark dynamic executor allocation will be supported. However for this beta only static resource allocation can be used. Based on the physical memory in each node and the configuration of  <code>spark.executor.memory</code> and <code>spark.yarn.executor.memoryOverhead</code>, you will need to choose the number of instances and set <code>spark.executor.instances</code>.</p>

<p>Now a real world example. Assuming 10 nodes with 64GB of memory per node with 12 virtual cores, e.g., <code>yarn.nodemanager.resource.cpu-vcores=12</code>. One node will be used as the master and as such the cluster will have 9 slave nodes. We’ll configure <code>spark.executor.cores</code> to 6. Given 64GB of ram <code>yarn.nodemanager.resource.memory-mb</code> will be 50GB. We’ll determine the amount of memory for each executor as follows: 50GB * (6/12) = 25GB. We’ll assign 20% to <code>spark.yarn.executor.memoryOverhead</code>, or 5120, and 80% to <code>spark.executor.memory</code>, or 20GB.</p>

<p>On this 9 node cluster we’ll have two executors per host. As such we can configure <code>spark.executor.instances</code> somewhere between 2 and 18. A value of 18 would utilize the entire cluster.</p>

<h2 id="HiveonSpark:GettingStarted-CommonIssues(Greenareresolved,willberemovedfromthislist)">Common Issues (Green are resolved, will be removed from this list)</h2>

<table><tbody><tr><th>Issue</th>
			<th>Cause</th>
			<th>Resolution</th>
		</tr><tr><td colspan="1">Error: Could not find or load main class org.apache.spark.deploy.SparkSubmit</td>
			<td colspan="1">Spark dependency not correctly set.</td>
			<td colspan="1">Add Spark dependency to Hive, see Step 1 <a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started#HiveonSpark:GettingStarted-ConfiguringHive" rel="nofollow">above</a>.</td>
		</tr><tr><td colspan="1">
			<p>org.apache.spark.SparkException: Job aborted due to stage failure:</p>

			<p>Task 5.0:0 had a not serializable result: java.io.NotSerializableException: org.apache.hadoop.io.BytesWritable</p>
			</td>
			<td colspan="1">Spark serializer not set to Kryo.</td>
			<td colspan="1">Set spark.serializer to be org.apache.spark.serializer.KryoSerializer, see Step 3 <a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started#HiveonSpark:GettingStarted-ConfiguringHive" rel="nofollow">above</a>.</td>
		</tr><tr><td colspan="1">
			<p>[ERROR] Terminal initialization failed; falling back to unsupported<br>
			java.lang.IncompatibleClassChangeError: Found class jline.Terminal, but interface was expected</p>
			</td>
			<td colspan="1">Hive has upgraded to Jline2 but jline 0.94 exists in the Hadoop lib.</td>
			<td colspan="1">
			<ol><li>Delete jline from the Hadoop lib directory (it's only pulled in transitively from ZooKeeper).</li>
				<li>export HADOOP_USER_CLASSPATH_FIRST=true</li>
				<li>If this error occurs during mvn test, perform a mvn clean install on the root project and itests directory.</li>
			</ol></td>
		</tr><tr><td colspan="1">
			<p>Spark executor gets killed all the time and Spark keeps retrying the failed stage; you may find similar information in the YARN nodemanager log.</p>

			<p>WARN org.apache.hadoop.yarn.server.nodemanager.containermanager.monitor.ContainersMonitorImpl: Container [pid=217989,containerID=container_1421717252700_0716_01_50767235] is running beyond physical memory limits. Current usage: 43.1 GB of 43 GB physical memory used; 43.9 GB of 90.3 GB virtual memory used. Killing container.</p>
			</td>
			<td colspan="1">For Spark on YARN, nodemanager would kill Spark executor if it used more memory than the configured size of "spark.executor.memory" + "spark.yarn.executor.memoryOverhead".</td>
			<td colspan="1">Increase "spark.yarn.executor.memoryOverhead" to make sure it covers the executor off-heap memory usage.</td>
		</tr><tr><td colspan="1">
			<p>Run query and get an error like:</p>

			<p>FAILED: Execution Error, return code 3 from org.apache.hadoop.hive.ql.exec.spark.SparkTask</p>

			<p>In Hive logs, it shows:</p>

			<p>java.lang.NoClassDefFoundError: Could not initialize class org.xerial.snappy.Snappy<br>
			  at org.xerial.snappy.SnappyOutputStream.&lt;init&gt;(SnappyOutputStream.java:79)</p>
			</td>
			<td colspan="1">
			<p>Happens on Mac (not officially supported).</p>

			<p>This is a general Snappy issue with Mac and is not unique to Hive on Spark, but workaround is noted here because it is needed for startup of Spark client.</p>
			</td>
			<td colspan="1">
			<p>Run this command before starting Hive or HiveServer2:</p>

			<p>export HADOOP_OPTS="-Dorg.xerial.snappy.tempdir=/tmp -Dorg.xerial.snappy.lib.name=libsnappyjava.jnilib $HADOOP_OPTS"</p>
			</td>
		</tr><tr><td colspan="1">
			<p>Stack trace: ExitCodeException exitCode=1: .../launch_container.sh: line 27: $PWD:$PWD/__spark__.jar:$HADOOP_CONF_DIR.../usr/hdp/${hdp.version}/hadoop/lib/hadoop-lzo-0.6.0.${hdp.version}.jar:/etc/hadoop/conf/secure:$PWD/__app__.jar:$PWD/*: bad substitution</p>

			<p> </p>
			</td>
			<td colspan="1">
			<p>The key mapreduce.application.classpath in /etc/hadoop/conf/mapred-site.xml contains a variable which is invalid in bash.</p>
			</td>
			<td colspan="1">
			<p>From <strong>mapreduce.application.classpath</strong> remove</p>

			<pre>
:/usr/hdp/${hdp.version}/hadoop/lib/hadoop-lzo-0.6.0.${hdp.version}.jar</pre>

			<p>from</p>

			<p><strong>/etc/hadoop/conf/mapred-site.xml</strong></p>
			</td>
		</tr><tr><td colspan="1">
			<p>Exception in thread "Driver" scala.MatchError: java.lang.NoClassDefFoundError: org/apache/hadoop/mapreduce/TaskAttemptContext (of class java.lang.NoClassDefFoundError)<br>
			  at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:432)</p>
			</td>
			<td colspan="1">MR is not on the YARN classpath.</td>
			<td colspan="1">
			<p>If on HDP change from</p>

			<p><strong>/hdp/apps/${hdp.version}/mapreduce/mapreduce.tar.gz#mr-framework</strong></p>

			<p>to</p>

			<p><strong>/hdp/apps/2.2.0.0-2041/mapreduce/mapreduce.tar.gz#mr-framework</strong></p>
			</td>
		</tr><tr><td colspan="1">java.lang.OutOfMemoryError: PermGen space with spark.master=local</td>
			<td colspan="1">By default (<a href="https://issues.apache.org/jira/browse/SPARK-1879" rel="nofollow">SPARK-1879</a>), Spark's own launch scripts increase PermGen to 128 MB, so we need to increase PermGen in hive launch script.</td>
			<td colspan="1">
			<p>If use JDK7, append following in conf/hive-env.sh:</p>

			<pre>
export HADOOP_OPTS="$HADOOP_OPTS -XX:MaxPermSize=128m"</pre>

			<p>If use JDK8, append following in Conf/hive-env.sh:</p>

			<pre>
export HADOOP_OPTS="$HADOOP_OPTS -XX:MaxMetaspaceSize=512m"</pre>
			</td>
		</tr></tbody></table><h2 id="HiveonSpark:GettingStarted-RecommendedConfiguration">Recommended Configuration</h2>

<p>See <a href="https://issues.apache.org/jira/browse/HIVE-9153" rel="nofollow">HIVE-9153</a> for details on these settings.</p>

<pre>
mapreduce.input.fileinputformat.split.maxsize=750000000
hive.vectorized.execution.enabled=true

hive.cbo.enable=true
hive.optimize.reducededuplication.min.reducer=4
hive.optimize.reducededuplication=true
hive.orc.splits.include.file.footer=false
hive.merge.mapfiles=true
hive.merge.sparkfiles=false
hive.merge.smallfiles.avgsize=16000000
hive.merge.size.per.task=256000000
hive.merge.orcfile.stripe.level=true
hive.auto.convert.join=true
hive.auto.convert.join.noconditionaltask=true
hive.auto.convert.join.noconditionaltask.size=894435328
hive.optimize.bucketmapjoin.sortedmerge=false
hive.map.aggr.hash.percentmemory=0.5
hive.map.aggr=true
hive.optimize.sort.dynamic.partition=false
hive.stats.autogather=true
hive.stats.fetch.column.stats=true
hive.vectorized.execution.reduce.enabled=false
hive.vectorized.groupby.checkinterval=4096
hive.vectorized.groupby.flush.percent=0.1
hive.compute.query.using.stats=true
hive.limit.pushdown.memory.usage=0.4
hive.optimize.index.filter=true
hive.exec.reducers.bytes.per.reducer=67108864
hive.smbjoin.cache.rows=10000
hive.exec.orc.default.stripe.size=67108864
hive.fetch.task.conversion=more
hive.fetch.task.conversion.threshold=1073741824
hive.fetch.task.aggr=false
mapreduce.input.fileinputformat.list-status.num-threads=5
spark.kryo.referenceTracking=false
spark.kryo.classesToRegister=org.apache.hadoop.hive.ql.io.HiveKey,org.apache.hadoop.io.BytesWritable,org.apache.hadoop.hive.ql.exec.vector.VectorizedRowBatch</pre>

<p>See <a href="https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties#ConfigurationProperties-Spark" rel="nofollow">Spark section of configuration page</a> for additional properties.</p>

<h2 id="HiveonSpark:GettingStarted-Designdocuments">Design documents</h2>

<ul><li><a href="https://issues.apache.org/jira/secure/attachment/12652517/Hive-on-Spark.pdf" rel="nofollow">Hive on Spark: Overall Design</a> from <a href="https://issues.apache.org/jira/browse/HIVE-7292" rel="nofollow">HIVE-7272</a></li>
	<li><a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Join+Design+Master" rel="nofollow">Hive on Spark: Join Design (HIVE-7613)</a></li>
	<li><a href="https://issues.apache.org/jira/browse/HIVE-9449" rel="nofollow">Hive on Spark Configuration (HIVE-9449)</a></li>
	<li><a href="https://cwiki.apache.org/confluence/download/attachments/44302539/hos_explain.pdf?version=1&amp;modificationDate=1425575903000&amp;api=v2" rel="nofollow">Hive on Spark Explain Plan</a></li>
</ul>            </div>
                </div>
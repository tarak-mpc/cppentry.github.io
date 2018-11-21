---
layout:     post
title:      hive on spark 编译
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/stark_summer/article/details/48466749				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="前置条件说明">前置条件说明</h1>

<p>Hive on Spark是Hive跑在Spark上，用的是Spark执行引擎，而不是MapReduce，和Hive on Tez的道理一样。 <br>
从Hive 1.1版本开始，Hive on Spark已经成为Hive代码的一部分了，并且在spark分支上面，可以看这里<a href="https://github.com/apache/hive/tree/spark" rel="nofollow">https://github.com/apache/hive/tree/spark</a>，并会定期的移到master分支上面去。 <br>
关于Hive on Spark的讨论和进度，可以看这里<a href="https://issues.apache.org/jira/browse/HIVE-7292" rel="nofollow">https://issues.apache.org/jira/browse/HIVE-7292</a>。 <br>
hive on spark文档:<a href="https://issues.apache.org/jira/secure/attachment/12652517/Hive-on-Spark.pdf" rel="nofollow">https://issues.apache.org/jira/secure/attachment/12652517/Hive-on-Spark.pdf</a></p>



<h1 id="源码下载">源码下载</h1>

<p>git clone <a href="https://github.com/apache/hive.git" rel="nofollow">https://github.com/apache/hive.git</a> hive_on_spark</p>



<h1 id="编译">编译</h1>



<pre class="prettyprint"><code class=" hljs lasso"> cd hive_on_spark<span class="hljs-subst">/</span>
 git branch <span class="hljs-attribute">-r</span>
  origin/HEAD <span class="hljs-subst">-&gt; </span>origin/master
  origin/HIVE<span class="hljs-subst">-</span><span class="hljs-number">4115</span>
  origin/HIVE<span class="hljs-subst">-</span><span class="hljs-number">8065</span>
  origin/beeline<span class="hljs-attribute">-cli</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.10</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.11</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.12</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.13</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.14</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.2</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.3</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.4</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.5</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.6</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.7</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.8</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.8</span><span class="hljs-attribute">-r2</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">0.9</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">1</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">1.0</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">1.0</span><span class="hljs-number">.1</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">1.1</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">1.1</span><span class="hljs-number">.1</span>
  origin/branch<span class="hljs-subst">-</span><span class="hljs-number">1.2</span>
  origin/cbo
  origin/hbase<span class="hljs-attribute">-metastore</span>
  origin/llap
  origin/master
  origin/maven
  origin/next
  origin/parquet
  origin/ptf<span class="hljs-attribute">-windowing</span>
  origin/release<span class="hljs-subst">-</span><span class="hljs-number">1.1</span>
  origin/spark
  origin/spark<span class="hljs-attribute">-new</span>
  origin/spark2
  origin/tez
  origin/vectorization

 git checkout origin/spark
 git branch
<span class="hljs-subst">*</span> （分离自 origin/spark）
  master</code></pre>

<p>修改$HIVE_ON_SPARK/pom.xml <br>
spark版本改成spark1.4.1</p>



<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-tag">&lt;<span class="hljs-title">spark.version</span>&gt;</span>1.4.1<span class="hljs-tag">&lt;/<span class="hljs-title">spark.version</span>&gt;</span></code></pre>

<p>hadoop版本改成2.3.0-cdh5.1.0</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">hadoop-23.version</span>&gt;</span>2.3.0-cdh5.1.0<span class="hljs-tag">&lt;/<span class="hljs-title">hadoop-23.version</span>&gt;</span></code></pre>

<p>编译命令</p>



<pre class="prettyprint"><code class=" hljs d"><span class="hljs-keyword">export</span> MAVEN_OPTS=<span class="hljs-string">"-Xmx2g -XX:MaxPermSize=512M -XX:ReservedCodeCacheSize=512m"</span>
mvn clean <span class="hljs-keyword">package</span> -Phadoop-<span class="hljs-number">2</span> -DskipTests</code></pre>



<h1 id="添加spark的依赖到hive的方法">添加Spark的依赖到Hive的方法</h1>

<p>spark home:/home/cluster/apps/spark/spark-1.4.1 <br>
hive home:/home/cluster/apps/hive_on_spark</p>

<p>1.set the property ‘spark.home’ to point to the Spark installation: </p>



<pre class="prettyprint"><code class=" hljs ruby">hive&gt; set spark.home=<span class="hljs-regexp">/home/cluster</span><span class="hljs-regexp">/apps/spark</span><span class="hljs-regexp">/spark-1.4.1;  </span></code></pre>

<ol>
<li>Define the SPARK_HOME environment variable before starting Hive CLI/HiveServer2:</li>
</ol>

<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-constant">SPARK_HOME</span>=<span class="hljs-regexp">/home/cluster</span><span class="hljs-regexp">/apps/spark</span><span class="hljs-regexp">/spark-1.4.1</span></code></pre>

<p>3.Set the spark-assembly jar on the Hive auxpath:</p>

<pre class="prettyprint"><code class=" hljs vbnet">hive --auxpath /home/cluster/apps/spark/spark-<span class="hljs-number">1.4</span><span class="hljs-number">.1</span>/<span class="hljs-keyword">lib</span>/spark-<span class="hljs-keyword">assembly</span>-*.jar</code></pre>

<ol>
<li>Add the spark-assembly jar for the current user session:</li>
</ol>

<pre class="prettyprint"><code class=" hljs vbnet">hive&gt; add jar /home/cluster/apps/spark/spark-<span class="hljs-number">1.4</span><span class="hljs-number">.1</span>/<span class="hljs-keyword">lib</span>/spark-<span class="hljs-keyword">assembly</span>-*.jar;</code></pre>

<ol>
<li>Link the spark-assembly jar to $HIVE_HOME/lib.</li>
</ol>

<h1 id="启动hive过程中可能出现的错误">启动Hive过程中可能出现的错误：</h1>



<pre class="prettyprint"><code class=" hljs avrasm">[ERROR] Terminal initialization failed<span class="hljs-comment">; falling back to unsupported</span>
<span class="hljs-label">java.lang.IncompatibleClassChangeError:</span> Found class jline<span class="hljs-preprocessor">.Terminal</span>, but interface was expected
        at jline<span class="hljs-preprocessor">.TerminalFactory</span><span class="hljs-preprocessor">.create</span>(TerminalFactory<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">101</span>)
        at jline<span class="hljs-preprocessor">.TerminalFactory</span><span class="hljs-preprocessor">.get</span>(TerminalFactory<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">158</span>)
        at jline<span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.ConsoleReader</span>.&lt;init&gt;(ConsoleReader<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">229</span>)
        at jline<span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.ConsoleReader</span>.&lt;init&gt;(ConsoleReader<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">221</span>)
        at jline<span class="hljs-preprocessor">.console</span><span class="hljs-preprocessor">.ConsoleReader</span>.&lt;init&gt;(ConsoleReader<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">209</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hive</span><span class="hljs-preprocessor">.cli</span><span class="hljs-preprocessor">.CliDriver</span><span class="hljs-preprocessor">.getConsoleReader</span>(CliDriver<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">773</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hive</span><span class="hljs-preprocessor">.cli</span><span class="hljs-preprocessor">.CliDriver</span><span class="hljs-preprocessor">.executeDriver</span>(CliDriver<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">715</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hive</span><span class="hljs-preprocessor">.cli</span><span class="hljs-preprocessor">.CliDriver</span><span class="hljs-preprocessor">.run</span>(CliDriver<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">675</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hive</span><span class="hljs-preprocessor">.cli</span><span class="hljs-preprocessor">.CliDriver</span><span class="hljs-preprocessor">.main</span>(CliDriver<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">615</span>)
        at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.NativeMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>0(Native Method)
        at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.NativeMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>(NativeMethodAccessorImpl<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">57</span>)
        at sun<span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.DelegatingMethodAccessorImpl</span><span class="hljs-preprocessor">.invoke</span>(DelegatingMethodAccessorImpl<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">43</span>)
        at java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.reflect</span><span class="hljs-preprocessor">.Method</span><span class="hljs-preprocessor">.invoke</span>(Method<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">606</span>)
        at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.RunJar</span><span class="hljs-preprocessor">.main</span>(RunJar<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">212</span>)

Exception <span class="hljs-keyword">in</span> thread <span class="hljs-string">"main"</span> java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.IncompatibleClassChangeError</span>: Found class jline<span class="hljs-preprocessor">.Terminal</span>, but interface was expected</code></pre>

<p>解决方法：export HADOOP_USER_CLASSPATH_FIRST=true</p>

<p>其他场景的错误解决方法参见：<a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started</a></p>



<h1 id="需要设置sparkeventlogdir参数比如">需要设置spark.eventLog.dir参数，比如：</h1>

<p>set spark.eventLog.dir= hdfs://master:8020/directory <br>
否则查询会报错，否则一直报错:/tmp/spark-event类似的文件夹不存在</p>



<h1 id="启动hive后设置执行引擎为spark">启动hive后设置执行引擎为spark：</h1>



<pre class="prettyprint"><code class=" hljs avrasm">hive&gt; <span class="hljs-keyword">set</span> hive<span class="hljs-preprocessor">.execution</span><span class="hljs-preprocessor">.engine</span>=spark<span class="hljs-comment">;</span></code></pre>



<h1 id="设置spark的运行模式">设置spark的运行模式：</h1>



<pre class="prettyprint"><code class=" hljs cs">hive&gt; <span class="hljs-keyword">set</span> spark.master=spark:<span class="hljs-comment">//master:7077</span></code></pre>

<p>或者yarn：spark.master=yarn</p>



<h1 id="configure-spark-application-configs-for-hive">Configure Spark-application configs for Hive</h1>

<p>可以配置在spark-defaults.conf或者hive-site.xml</p>



<pre class="prettyprint"><code class=" hljs r">spark.master=&lt;Spark Master URL&gt;
spark.eventLog.enabled=true;            
spark.executor.memory=512m;             
spark.serializer=org.apache.spark.serializer.KryoSerializer;
spark.executor.memory=<span class="hljs-keyword">...</span>  <span class="hljs-comment">#Amount of memory to use per executor process.</span>
spark.executor.cores=<span class="hljs-keyword">...</span>  <span class="hljs-comment">#Number of cores per executor.</span>
spark.yarn.executor.memoryOverhead=<span class="hljs-keyword">...</span>
spark.executor.instances=<span class="hljs-keyword">...</span>  <span class="hljs-comment">#The number of executors assigned to each application.</span>
spark.driver.memory=<span class="hljs-keyword">...</span>  <span class="hljs-comment">#The amount of memory assigned to the Remote Spark Context (RSC). We recommend 4GB.</span>
spark.yarn.driver.memoryOverhead=<span class="hljs-keyword">...</span>  <span class="hljs-comment">#We recommend 400 (MB).</span></code></pre>

<p>参数配置详见文档：<a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started</a></p>



<h1 id="执行sql语句后可以在监控页面查看jobstages等信息">执行sql语句后可以在监控页面查看job/stages等信息</h1>



<pre class="prettyprint"><code class=" hljs vbnet">hive (<span class="hljs-keyword">default</span>)&gt; <span class="hljs-keyword">select</span> city_id, count(*) c <span class="hljs-keyword">from</span> city_info <span class="hljs-keyword">group</span> <span class="hljs-keyword">by</span> city_id <span class="hljs-keyword">order</span> <span class="hljs-keyword">by</span> c desc limit <span class="hljs-number">5</span>;
Query ID = spark_20150309173838_444cb5b1-b72e-<span class="hljs-number">4</span>fc3-<span class="hljs-number">87</span>db-<span class="hljs-number">4162e364</span>cb1e
Total jobs = <span class="hljs-number">1</span>
Launching Job <span class="hljs-number">1</span> out <span class="hljs-keyword">of</span> <span class="hljs-number">1</span>
<span class="hljs-keyword">In</span> <span class="hljs-keyword">order</span> <span class="hljs-keyword">to</span> change the average load <span class="hljs-keyword">for</span> a reducer (<span class="hljs-keyword">in</span> bytes):
  <span class="hljs-keyword">set</span> hive.exec.reducers.bytes.per.reducer=&lt;number&gt;
<span class="hljs-keyword">In</span> <span class="hljs-keyword">order</span> <span class="hljs-keyword">to</span> limit the maximum number <span class="hljs-keyword">of</span> reducers:
  <span class="hljs-keyword">set</span> hive.exec.reducers.max=&lt;number&gt;
<span class="hljs-keyword">In</span> <span class="hljs-keyword">order</span> <span class="hljs-keyword">to</span> <span class="hljs-keyword">set</span> a constant number <span class="hljs-keyword">of</span> reducers:
  <span class="hljs-keyword">set</span> mapreduce.job.reduces=&lt;number&gt;
state = SENT
state = STARTED
state = STARTED
state = STARTED
state = STARTED
Query Hive <span class="hljs-keyword">on</span> Spark job[<span class="hljs-number">0</span>] stages:
<span class="hljs-number">1</span>
Status: Running (Hive <span class="hljs-keyword">on</span> Spark job[<span class="hljs-number">0</span>])
Job Progress Format
CurrentTime StageId_StageAttemptId: SucceededTasksCount(+RunningTasksCount-FailedTasksCount)/TotalTasksCount [StageCost]
<span class="hljs-number">2015</span>-<span class="hljs-number">03</span>-<span class="hljs-number">09</span> <span class="hljs-number">17</span>:<span class="hljs-number">38</span>:<span class="hljs-number">11</span>,<span class="hljs-number">822</span> Stage-<span class="hljs-number">0</span>_0: <span class="hljs-number">0</span>(+<span class="hljs-number">1</span>)/<span class="hljs-number">1</span>      Stage-<span class="hljs-number">1</span>_0: <span class="hljs-number">0</span>/<span class="hljs-number">1</span>  Stage-<span class="hljs-number">2</span>_0: <span class="hljs-number">0</span>/<span class="hljs-number">1</span>
state = STARTED
state = STARTED
state = STARTED
<span class="hljs-number">2015</span>-<span class="hljs-number">03</span>-<span class="hljs-number">09</span> <span class="hljs-number">17</span>:<span class="hljs-number">38</span>:<span class="hljs-number">14</span>,<span class="hljs-number">845</span> Stage-<span class="hljs-number">0</span>_0: <span class="hljs-number">0</span>(+<span class="hljs-number">1</span>)/<span class="hljs-number">1</span>      Stage-<span class="hljs-number">1</span>_0: <span class="hljs-number">0</span>/<span class="hljs-number">1</span>  Stage-<span class="hljs-number">2</span>_0: <span class="hljs-number">0</span>/<span class="hljs-number">1</span>
state = STARTED
state = STARTED
<span class="hljs-number">2015</span>-<span class="hljs-number">03</span>-<span class="hljs-number">09</span> <span class="hljs-number">17</span>:<span class="hljs-number">38</span>:<span class="hljs-number">16</span>,<span class="hljs-number">861</span> Stage-<span class="hljs-number">0</span>_0: <span class="hljs-number">1</span>/<span class="hljs-number">1</span> Finished Stage-<span class="hljs-number">1</span>_0: <span class="hljs-number">0</span>(+<span class="hljs-number">1</span>)/<span class="hljs-number">1</span>      Stage-<span class="hljs-number">2</span>_0: <span class="hljs-number">0</span>/<span class="hljs-number">1</span>
state = SUCCEEDED
<span class="hljs-number">2015</span>-<span class="hljs-number">03</span>-<span class="hljs-number">09</span> <span class="hljs-number">17</span>:<span class="hljs-number">38</span>:<span class="hljs-number">17</span>,<span class="hljs-number">867</span> Stage-<span class="hljs-number">0</span>_0: <span class="hljs-number">1</span>/<span class="hljs-number">1</span> Finished Stage-<span class="hljs-number">1</span>_0: <span class="hljs-number">1</span>/<span class="hljs-number">1</span> Finished Stage-<span class="hljs-number">2</span>_0: <span class="hljs-number">1</span>/<span class="hljs-number">1</span> Finished
Status: Finished successfully <span class="hljs-keyword">in</span> <span class="hljs-number">10.07</span> seconds
OK
city_id c
-<span class="hljs-number">1000</span>   <span class="hljs-number">22826</span>
-<span class="hljs-number">10</span>     <span class="hljs-number">17294</span>
-<span class="hljs-number">20</span>     <span class="hljs-number">10608</span>
-<span class="hljs-number">1</span>      <span class="hljs-number">6186</span>
    <span class="hljs-number">4158</span>
Time taken: <span class="hljs-number">18.417</span> seconds, Fetched: <span class="hljs-number">5</span> row(s)</code></pre>

<p><img src="https://img-blog.csdn.net/20150915140117154" alt="这里写图片描述" title=""></p>

<p>尊重原创,拒绝转载,<a href="http://blog.csdn.net/stark_summer/article/details/48466749" rel="nofollow">http://blog.csdn.net/stark_summer/article/details/48466749</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
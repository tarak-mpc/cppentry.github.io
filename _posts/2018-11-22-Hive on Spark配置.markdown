---
layout:     post
title:      Hive on Spark配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/harryxujiao/article/details/79132727				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><code>@ 2016-05-30</code></p>

<p></p><div class="toc">
<ul>
<li><ul>
<li><ul>
<li><a href="#1-%E4%BF%AE%E6%94%B9hivehomeconfhive-sitexml" rel="nofollow">修改HIVE_HOMEconfhive-sitexml</a></li>
<li><a href="#2-%E6%B7%BB%E5%8A%A0spark-assembly%E5%8C%85" rel="nofollow">添加spark-assembly包</a></li>
<li><a href="#3-%E9%A2%9D%E5%A4%96%E6%B7%BB%E5%8A%A0%E5%87%A0%E4%B8%AAjar%E5%8C%85" rel="nofollow">额外添加几个jar包</a></li>
<li><a href="#%E9%99%84-%E7%BC%96%E8%AF%91spark-assembly%E5%8C%85" rel="nofollow">附 编译spark-assembly包</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>


<p><strong>Hive on Spark</strong>，基于Spark的Hive，实质上是将Hive默认的计算引擎MapReduce替换成Spark。Hive on Spark的设置相当简单，但是其中也是各种坑~</p>

<blockquote>
  <p><strong>环境说明</strong></p>
  
  <ul>
  <li>CentOS 6.7</li>
  <li>Hadoop 2.7.2</li>
  <li>Hive 2.0.0</li>
  <li>Spark 1.5.0</li>
  <li>Kylin 1.5.1</li>
  </ul>
</blockquote>

<h3 id="1-修改hivehomeconfhive-sitexml">1. 修改$HIVE_HOME/conf/hive-site.xml</h3>



<pre class="prettyprint"><code class="language-bash hljs ">&lt;property&gt;
    &lt;name&gt;hive.execution.engine&lt;/name&gt;
    &lt;value&gt;spark&lt;/value&gt;
    &lt;description&gt;这个值可以使mr/tez/spark中的一个作为hive的执行引擎，mr是默认值，而tez仅hadoop <span class="hljs-number">2</span>支持&lt;/description&gt;
 &lt;/property&gt;</code></pre>



<h3 id="2-添加spark-assembly包">2. 添加spark-assembly包</h3>

<p><strong>注意</strong>，起初Spark使用的版本并非1.5.0，当使用其spark-assembly包时，hive无法正常使用spark进行计算，后来查看hive-2.0.0-src中的pom.xml，找到spark的版本为1.5.0，因此自己对spark进行了编译，具体编译方法在末尾小节中。</p>

<ol>
<li>拷贝spark-assembly-1.5.0-hadoop2.6.0.jar到Hive下：</li>
</ol>



<pre class="prettyprint"><code class="language-bash hljs ">cp <span class="hljs-variable">$SPARK_HOME</span>/lib/spark-assembly-<span class="hljs-number">1.5</span>.<span class="hljs-number">0</span>-hadoop2.<span class="hljs-number">6.0</span>.jar <span class="hljs-variable">$HIVE_HOME</span>/lib/</code></pre>

<ol>
<li>测试，进入Hive Cli执行计算语句，如下：</li>
</ol>



<pre class="prettyprint"><code class="language-bash hljs ">hive&gt; select sum(measure) from tb_fact;
Query ID = nanyue_20160530194626_77f06879-<span class="hljs-number">0508</span>-<span class="hljs-number">49</span>cc-b4fd-<span class="hljs-number">8</span>e4748da4193
Total jobs = <span class="hljs-number">1</span>
Launching Job <span class="hljs-number">1</span> out of <span class="hljs-number">1</span>
In order to change the average load <span class="hljs-keyword">for</span> a reducer (<span class="hljs-keyword">in</span> bytes):
  <span class="hljs-keyword">set</span> hive.exec.reducers.bytes.per.reducer=&lt;number&gt;
In order to limit the maximum number of reducers:
  <span class="hljs-keyword">set</span> hive.exec.reducers.max=&lt;number&gt;
In order to <span class="hljs-keyword">set</span> a constant number of reducers:
  <span class="hljs-keyword">set</span> mapreduce.job.reduces=&lt;number&gt;
Starting Spark Job = <span class="hljs-number">2</span>de6c9eb-b79b-<span class="hljs-number">4111</span><span class="hljs-operator">-a</span>594-b4c4552440da

Query Hive on Spark job[<span class="hljs-number">0</span>] stages:
<span class="hljs-number">0</span>
<span class="hljs-number">1</span>

Status: Running (Hive on Spark job[<span class="hljs-number">0</span>])
Job Progress Format
CurrentTime StageId_StageAttemptId: SucceededTasksCount(+RunningTasksCount-FailedTasksCount)/TotalTasksCount [StageCost]
<span class="hljs-number">2016</span>-<span class="hljs-number">05</span>-<span class="hljs-number">30</span> <span class="hljs-number">19</span>:<span class="hljs-number">46</span>:<span class="hljs-number">34</span>,<span class="hljs-number">737</span> Stage-<span class="hljs-number">0</span>_0: <span class="hljs-number">0</span>(+<span class="hljs-number">1</span>)/<span class="hljs-number">2</span>  Stage-<span class="hljs-number">1</span>_0: <span class="hljs-number">0</span>/<span class="hljs-number">1</span>
........
<span class="hljs-number">2016</span>-<span class="hljs-number">05</span>-<span class="hljs-number">30</span> <span class="hljs-number">19</span>:<span class="hljs-number">46</span>:<span class="hljs-number">48</span>,<span class="hljs-number">815</span> Stage-<span class="hljs-number">0</span>_0: <span class="hljs-number">2</span>/<span class="hljs-number">2</span> Finished Stage-<span class="hljs-number">1</span>_0: <span class="hljs-number">1</span>/<span class="hljs-number">1</span> Finished 
Status: Finished successfully <span class="hljs-keyword">in</span> <span class="hljs-number">18.10</span> seconds
OK
<span class="hljs-number">4.9995</span>E9
Time taken: <span class="hljs-number">22.783</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)</code></pre>



<h3 id="3-额外添加几个jar包">3. 额外添加几个jar包</h3>

<p>在我的环境下，Hive on Spark和Hive&amp;HBase是同时使用的，且基于此还有Kylin需要使用Hive和HBase，经过测试之后，在.hiverc下添加了下面这些jar包，才使得Hive/HBase/Kylin各组件协调统一的运行正常。</p>



<pre class="prettyprint"><code class="language-bash hljs ">add jar /opt/hbase-<span class="hljs-number">1.1</span>.<span class="hljs-number">3</span>/lib/htrace-core-<span class="hljs-number">3.1</span>.<span class="hljs-number">0</span>-incubating.jar;
add jar /opt/hive-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>/lib/hive-metastore-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>.jar;
add jar /opt/hbase-<span class="hljs-number">1.1</span>.<span class="hljs-number">3</span>/lib/hbase-server-<span class="hljs-number">1.1</span>.<span class="hljs-number">3</span>.jar;
add jar /opt/hbase-<span class="hljs-number">1.1</span>.<span class="hljs-number">3</span>/lib/hbase-client-<span class="hljs-number">1.1</span>.<span class="hljs-number">3</span>.jar;
add jar /opt/hbase-<span class="hljs-number">1.1</span>.<span class="hljs-number">3</span>/lib/hbase-protocol-<span class="hljs-number">1.1</span>.<span class="hljs-number">3</span>.jar;
add jar /opt/hive-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>/hcatalog/share/hcatalog/hive-hcatalog-core-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>.jar;
add jar /opt/hive-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>/lib/datanucleus-core-<span class="hljs-number">4.1</span>.<span class="hljs-number">6</span>.jar;
add jar /opt/hive-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>/lib/datanucleus-api-jdo-<span class="hljs-number">4.2</span>.<span class="hljs-number">1</span>.jar;
add jar /opt/hive-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>/lib/datanucleus-rdbms-<span class="hljs-number">4.1</span>.<span class="hljs-number">7</span>.jar;
add jar /opt/hive-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>/lib/hive-cli-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>.jar;
add jar /opt/hive-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>/lib/hive-hbase-handler-<span class="hljs-number">2.0</span>.<span class="hljs-number">0</span>.jar;</code></pre>



<h3 id="附-编译spark-assembly包">附. 编译spark-assembly包</h3>

<p><strong>建议</strong>：在大数据平台中，必然会使用到各种各样的开源组件，因此在使用某一组件之前，强烈建议弄清楚该组件的版本中适用于现有组件版本有哪些。开源组件的版本兼容性问题，一直是挖坑利器。</p>

<ol>
<li>预先准备MAVEN环境 <br>
下载MAVEN的bin包，设置好环境变量</li>
</ol>



<pre class="prettyprint"><code class="language-bash hljs ">[mars@master ~]$ <span class="hljs-built_in">echo</span> <span class="hljs-variable">$MAVEN_HOME</span>
/opt/maven</code></pre>

<ol>
<li>下载对应的Spark源码包，这里下载spark-1.5.0.tgz，解压</li>
<li>进入spark-1.5.0，执行mvn编译语句</li>
</ol>



<pre class="prettyprint"><code class="language-bash hljs ">mvn -Pyarn -Dyarn.version=<span class="hljs-number">2.6</span>.<span class="hljs-number">0</span> -Phadoop-<span class="hljs-number">2.6</span> -Dhadoop.version=<span class="hljs-number">2.6</span>.<span class="hljs-number">0</span> -DskipTests clean package</code></pre>

<p>这里需要指定hadoop和yarn，若指定为2.7.2，编译会报错找不到hadoop-2.7.2，不要添加-Phive相关的参数；编译的时长与网络情况相关，如果失败了，多试几次应该可以的。 <br>
4. 编译完成之后可以得到spark-assembly…jar，和几个datanucleus相关的几个包</p>



<pre class="prettyprint"><code class="language-bash hljs ">[mars@master spark-<span class="hljs-number">1.5</span>.<span class="hljs-number">0</span>]$ ll assembly/target/scala-<span class="hljs-number">2.10</span>/
total <span class="hljs-number">179660</span>
-rw-rw-r-- <span class="hljs-number">1</span> nanyue nanyue <span class="hljs-number">183970627</span> May <span class="hljs-number">26</span> <span class="hljs-number">10</span>:<span class="hljs-number">46</span> spark-assembly-<span class="hljs-number">1.5</span>.<span class="hljs-number">0</span>-hadoop2.<span class="hljs-number">6.0</span>.jar
[mars@master spark-<span class="hljs-number">1.5</span>.<span class="hljs-number">0</span>]$ ll lib_managed/jars/
total <span class="hljs-number">3948</span>
-rw-rw-r-- <span class="hljs-number">1</span> nanyue nanyue  <span class="hljs-number">339666</span> May <span class="hljs-number">26</span> <span class="hljs-number">10</span>:<span class="hljs-number">43</span> datanucleus-api-jdo-<span class="hljs-number">3.2</span>.<span class="hljs-number">6</span>.jar
-rw-rw-r-- <span class="hljs-number">1</span> nanyue nanyue <span class="hljs-number">1890075</span> May <span class="hljs-number">26</span> <span class="hljs-number">10</span>:<span class="hljs-number">43</span> datanucleus-core-<span class="hljs-number">3.2</span>.<span class="hljs-number">10</span>.jar
-rw-rw-r-- <span class="hljs-number">1</span> nanyue nanyue <span class="hljs-number">1809447</span> May <span class="hljs-number">26</span> <span class="hljs-number">10</span>:<span class="hljs-number">43</span> datanucleus-rdbms-<span class="hljs-number">3.2</span>.<span class="hljs-number">9</span>.jar</code></pre>

<p>在第二步添加spark-assembly包这一步中，使用你自己编译的包，并且在使用编译得到的datanucleus的几个包替换掉$HIVE_HOME/lib/下相同的包。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Apache Spark学习：利用Eclipse构建Spark集成开发环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
前一篇文章“<a href="http://dongxicheng.org/framework-on-yarn/build-spark-on-hadoop-2-yarn/" rel="nofollow">Apache Spark学习：将Spark部署到Hadoop 2.2.0上</a>”介绍了如何使用Maven编译生成可直接运行在Hadoop 2.2.0上的Spark jar包，而本文则在此基础上，<del>介绍如何利用Eclipse构建Spark集成开发环境</del>。<span style="color:#ff0000;">不建议大家使用eclipse开发spark程序和阅读源代码，推荐使用Intellij
 IDEA，具体参考文章：</span><a href="http://dongxicheng.org/framework-on-yarn/apache-spark-intellij-idea/" rel="nofollow">Apache Spark探秘：利用Intellij IDEA构建开发环境</a>。
<p><strong>（1） 准备工作 </strong></p>
<p>在正式介绍之前，先要以下软硬件准备：</p>
<p>软件准备：</p>
<p><a href="http://www.eclipse.org/juno/" rel="nofollow">Eclipse Juno版本（4.2版本）</a>，可以直接点击这里下载：<a href="http://www.eclipse.org/downloads/packages/eclipse-ide-java-developers/junosr2" rel="nofollow">Eclipse 4.2</a></p>
<p>Scala 2.9.3版本，Window安装程序可以直接点击这里下载：<a href="http://www.scala-lang.org/files/archive/scala-2.9.3.msi" rel="nofollow">Scala 2.9.3</a></p>
<p>Eclipse Scala IDE插件，可直接点击这里下载：<a href="http://download.scala-ide.org/sdk/e38/scala29/stable/update-site.zip" rel="nofollow">Scala IDE(for Scala 2.9.x and Eclipse Juno)</a></p>
<p>硬件准备</p>
<p>装有Linux或者Windows操作系统的机器一台</p>
<p><strong>（2） 构建Spark集成开发环境 </strong></p>
<p>我是在windows操作系统下操作的，流程如下：</p>
<p>步骤1：安装scala 2.9.3：直接点击安装即可。</p>
<p>步骤2：将Eclipse Scala IDE插件中features和plugins两个目录下的所有文件拷贝到Eclipse解压后对应的目录中</p>
<p>步骤3：重新启动Eclipse，点击eclipse右上角方框按钮，如下图所示，展开后，点击“Other….”，查看是否有“Scala”一项，有的话，直接点击打开，否则进行步骤4操作。</p>
<p style="text-align:center;"><a href="http://dongxicheng.org/wp-content/uploads/2013/12/spark-eclipse.jpg" rel="nofollow"><img class="aligncenter size-full wp-image-2981" title="spark-eclipse" src="http://dongxicheng.org/wp-content/uploads/2013/12/spark-eclipse.jpg" alt="" height="340" width="672"></a></p>
<p>步骤4：在Eclipse中，依次选择“Help” –&gt; “Install New Software…”，在打开的卡里填入<a href="http://download.scala-ide.org/sdk/e38/scala29/stable/site" rel="nofollow">http://download.scala-ide.org/sdk/e38/scala29/stable/site</a>，并按回车键，可看到以下内容，选择前两项进行安装即可。（由于步骤3已经将jar包拷贝到eclipse中，安装很快，只是疏通一下）安装完后，重复操作一遍步骤3便可。</p>
<p><a href="http://dongxicheng.org/wp-content/uploads/2013/12/spark-eclipse-3.jpg" rel="nofollow"><img class="aligncenter size-full wp-image-2982" title="spark-eclipse-3" src="http://dongxicheng.org/wp-content/uploads/2013/12/spark-eclipse-3.jpg" alt="" height="429" width="518"></a></p>
<p><strong>（3） 使用Scala语言开发Spark程序 </strong></p>
<p>在eclipse中，依次选择“File” –&gt;“New” –&gt; “Other…” –&gt;  “Scala Wizard” –&gt; “Scala Project”，创建一个Scala工程，并命名为“SparkScala”。</p>
<p>右击“SaprkScala”工程，选择“Properties”，在弹出的框中，按照下图所示，依次选择“Java Build Path” –&gt;“Libraties” –&gt;“Add External JARs…”，导入文章“<a href="http://dongxicheng.org/framework-on-yarn/build-spark-on-hadoop-2-yarn/" rel="nofollow">Apache Spark学习：将Spark部署到Hadoop 2.2.0上</a>”中给出的</p>
<p>assembly/target/scala-2.9.3/目录下的spark-assembly-0.8.1-incubating-hadoop2.2.0.jar，这个jar包也可以自己编译spark生成，放在spark目录下的assembly/target/scala-2.9.3/目录中。</p>
<p style="text-align:center;"><a href="http://dongxicheng.org/wp-content/uploads/2013/12/spark-eclipse-4.jpg" rel="nofollow"><img class="aligncenter size-full wp-image-2983" title="spark-eclipse-4" src="http://dongxicheng.org/wp-content/uploads/2013/12/spark-eclipse-4.jpg" alt="" height="558" width="673"></a></p>
<p>跟创建Scala工程类似，在工程中增加一个Scala Class，命名为：WordCount，整个工程结构如下：</p>
<p><a href="http://dongxicheng.org/wp-content/uploads/2013/12/spark-eclipse-scala-ide.jpg" rel="nofollow"><img class="aligncenter size-full wp-image-2984" title="spark-eclipse-scala-ide" src="http://dongxicheng.org/wp-content/uploads/2013/12/spark-eclipse-scala-ide.jpg" alt="" height="234" width="377"></a></p>
<p>WordCount就是最经典的词频统计程序，它将统计输入目录中所有单词出现的总次数，Scala代码如下：</p>
<div>
<div id="highlighter_518590" class="syntaxhighlighter scala">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
<div class="line number15 index14 alt2">15</div>
<div class="line number16 index15 alt1">16</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="scala keyword">import</code> <code class="scala plain">
org.apache.spark.</code><code class="scala keyword">_</code></div>
<div class="line number2 index1 alt1"><code class="scala keyword">import</code> <code class="scala plain">
SparkContext.</code><code class="scala keyword">_</code></div>
<div class="line number3 index2 alt2"><code class="scala keyword">object</code> <code class="scala plain">
WordCount {</code></div>
<div class="line number4 index3 alt1"><code class="scala spaces">  </code><code class="scala keyword">def</code>
<code class="scala plain">main(args</code><code class="scala keyword">:</code> <code class="scala plain">
Array[String]) {</code></div>
<div class="line number5 index4 alt2"><code class="scala spaces">    </code><code class="scala keyword">if</code>
<code class="scala plain">(args.length !</code><code class="scala keyword">=</code>
<code class="scala value">3</code> <code class="scala plain">){</code></div>
<div class="line number6 index5 alt1"><code class="scala spaces">      </code><code class="scala plain">println(</code><code class="scala string">"usage is org.test.WordCount &lt;master&gt; &lt;input&gt; &lt;output&gt;"</code><code class="scala plain">)</code></div>
<div class="line number7 index6 alt2"><code class="scala spaces">      </code><code class="scala keyword">return</code></div>
<div class="line number8 index7 alt1"><code class="scala spaces">    </code><code class="scala plain">}</code></div>
<div class="line number9 index8 alt2"><code class="scala spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">sc </code><code class="scala keyword">=</code> <code class="scala keyword">
new</code> <code class="scala plain">SparkContext(args(</code><code class="scala value">0</code><code class="scala plain">),
</code><code class="scala string">"WordCount"</code><code class="scala plain">,</code></div>
<div class="line number10 index9 alt1"><code class="scala spaces">    </code><code class="scala plain">System.getenv(</code><code class="scala string">"SPARK_HOME"</code><code class="scala plain">), Seq(System.getenv(</code><code class="scala string">"SPARK_TEST_JAR"</code><code class="scala plain">)))</code></div>
<div class="line number11 index10 alt2"><code class="scala spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">textFile </code><code class="scala keyword">=</code> <code class="scala plain">
sc.textFile(args(</code><code class="scala value">1</code><code class="scala plain">))</code></div>
<div class="line number12 index11 alt1"><code class="scala spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">result </code><code class="scala keyword">=</code> <code class="scala plain">
textFile.flatMap(line </code><code class="scala keyword">=</code><code class="scala plain">&gt; line.split(</code><code class="scala string">"\\s+"</code><code class="scala plain">))</code></div>
<div class="line number13 index12 alt2"><code class="scala spaces">        </code><code class="scala plain">.map(word
</code><code class="scala keyword">=</code><code class="scala plain">&gt; (word, </code>
<code class="scala value">1</code><code class="scala plain">)).reduceByKey(</code><code class="scala keyword">_</code>
<code class="scala plain">+ </code><code class="scala keyword">_</code><code class="scala plain">)</code></div>
<div class="line number14 index13 alt1"><code class="scala spaces">    </code><code class="scala plain">result.saveAsTextFile(args(</code><code class="scala value">2</code><code class="scala plain">))</code></div>
<div class="line number15 index14 alt2"><code class="scala spaces">  </code><code class="scala plain">}</code></div>
<div class="line number16 index15 alt1"><code class="scala plain">}</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p>在Scala工程中，右击“WordCount.scala”，选择“Export”，并在弹出框中选择“Java” –&gt; “JAR File”，进而将该程序编译成jar包，可以起名为“spark-wordcount-in-scala.jar”，我导出的jar包下载地址是
<a href="http://kanboxshare.com/link/khGIHySIDoEg8SMP96C2PUVeLmQrHyI9BeZFGMOmRX9svM3UOu2dPT66Md4a0ZkQCKZeGiKJvHfUvM3wbLO4XeorAn3V2kvZ8V7Qq" rel="nofollow">
spark-wordcount-in-scala.jar</a>。</p>
<p>该WordCount程序接收三个参数，分别是master位置，HDFS输入目录和HDFS输出目录，为此，可编写run_spark_wordcount.sh脚本：</p>
<blockquote>
<p># 配置成YARN配置文件存放目录</p>
<p>export YARN_CONF_DIR=/opt/hadoop/yarn-client/etc/hadoop/</p>
<p>SPARK_JAR=./assembly/target/scala-2.9.3/spark-assembly-0.8.1-incubating-hadoop2.2.0.jar \</p>
<p>./spark-class org.apache.spark.deploy.yarn.Client \</p>
<p>–jar spark-wordcount-in-scala.jar \</p>
<p>–class WordCount \</p>
<p>–args yarn-standalone \</p>
<p>–args hdfs://hadoop-test/tmp/input \</p>
<p>–args hdfs:/hadoop-test/tmp/output \</p>
<p>–num-workers 1 \</p>
<p>–master-memory 2g \</p>
<p>–worker-memory 2g \</p>
<p>–worker-cores 2</p>
</blockquote>
<p>需要注意以下几点：WordCount程序的输入参数通过“-args”指定，每个参数依次单独指定，第二个参数是HDFS上的输入目录，需要事先创建好，并上传几个文本文件，以便统计词频，第三个参数是HDFS上的输出目录，动态创建，运行前不能存在。</p>
<p>直接运行run_spark_wordcount.sh脚本即可得到运算结果。</p>
<p>在运行过程中，发现一个bug，org.apache.spark.deploy.yarn.Client有一个参数“–name”可以指定应用程序名称：</p>
<p style="text-align:center;"><a href="http://dongxicheng.org/wp-content/uploads/2013/12/spark-yarn-client-cli.jpg" rel="nofollow"><img class="aligncenter size-full wp-image-2985" title="spark-yarn-client-cli" src="http://dongxicheng.org/wp-content/uploads/2013/12/spark-yarn-client-cli.jpg" alt="" height="259" width="657"></a></p>
<p>但是使用过程中，该参数会阻塞应用程序，查看源代码发现原来是个bug，该Bug已提交到<a href="https://spark-project.atlassian.net/browse/SPARK-1003" rel="nofollow">Spark jira</a>上：</p>
<div>
<div id="highlighter_921452" class="syntaxhighlighter scala">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="scala comments">// 位置：new-yarn/src/main/scala/org/apache/spark/deploy/yarn/ClientArguments.scala</code></div>
<div class="line number2 index1 alt1"><code class="scala spaces">        </code><code class="scala keyword">case</code>
<code class="scala plain">(</code><code class="scala string">"--queue"</code><code class="scala plain">)
</code><code class="scala keyword">::</code> <code class="scala plain">value </code>
<code class="scala keyword">::</code> <code class="scala plain">tail </code><code class="scala keyword">=</code><code class="scala plain">&gt;</code></div>
<div class="line number3 index2 alt2"><code class="scala spaces">          </code><code class="scala plain">amQueue
</code><code class="scala keyword">=</code> <code class="scala plain">value</code></div>
<div class="line number4 index3 alt1"><code class="scala spaces">          </code><code class="scala plain">args
</code><code class="scala keyword">=</code> <code class="scala plain">tail</code></div>
<div class="line number5 index4 alt2"> </div>
<div class="line number6 index5 alt1"><code class="scala spaces">        </code><code class="scala keyword">case</code>
<code class="scala plain">(</code><code class="scala string">"--name"</code><code class="scala plain">)
</code><code class="scala keyword">::</code> <code class="scala plain">value </code>
<code class="scala keyword">::</code> <code class="scala plain">tail </code><code class="scala keyword">=</code><code class="scala plain">&gt;</code></div>
<div class="line number7 index6 alt2"><code class="scala spaces">          </code><code class="scala plain">appName
</code><code class="scala keyword">=</code> <code class="scala plain">value</code></div>
<div class="line number8 index7 alt1"><code class="scala spaces">          </code><code class="scala plain">args
</code><code class="scala keyword">=</code> <code class="scala plain">tail </code>
<code class="scala comments">//漏了这行代码，导致程序阻塞</code></div>
<div class="line number9 index8 alt2"> </div>
<div class="line number10 index9 alt1"><code class="scala spaces">        </code><code class="scala keyword">case</code>
<code class="scala plain">(</code><code class="scala string">"--addJars"</code><code class="scala plain">)
</code><code class="scala keyword">::</code> <code class="scala plain">value </code>
<code class="scala keyword">::</code> <code class="scala plain">tail </code><code class="scala keyword">=</code><code class="scala plain">&gt;</code></div>
<div class="line number11 index10 alt2"><code class="scala spaces">          </code><code class="scala plain">addJars
</code><code class="scala keyword">=</code> <code class="scala plain">value</code></div>
<div class="line number12 index11 alt1"><code class="scala spaces">          </code><code class="scala plain">args
</code><code class="scala keyword">=</code> <code class="scala plain">tail</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p>因此，大家先不要使用“–name”这个参数，或者修复这个bug，重新编译Spark。</p>
<p><strong>（4） 使用Java语言开发Spark程序 </strong></p>
<p>方法跟普通的Java程序开发一样，只要将Spark开发程序包spark-assembly-0.8.1-incubating-hadoop2.2.0.jar作为三方依赖库即可。</p>
<p><strong>（5） 总结 </strong></p>
<p>初步试用Spark On YARN过程中，发现问题还是非常多，使用起来非常不方便，门槛还是很高，远不如Spark On Mesos成熟。</p>
<div style="font-style:italic;">
<p><strong>原创文章，转载请注明：</strong> 转载自<a href="http://dongxicheng.org/" rel="nofollow">董的博客</a></p>
<p><strong>本文链接地址:</strong> <a href="http://dongxicheng.org/framework-on-yarn/spark-eclipse-ide/" rel="nofollow">
http://dongxicheng.org/framework-on-yarn/spark-eclipse-ide/</a></p>
</div>
            </div>
                </div>
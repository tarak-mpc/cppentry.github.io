---
layout:     post
title:      spark standalone模式作业迁移到spark on yarn
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。-- Bruce Xu					https://blog.csdn.net/xwc35047/article/details/53247702				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="本文主要介绍spark-standalone模式的作业迁移到spark-on-yarn上的一些操作">本文主要介绍spark standalone模式的作业迁移到spark on yarn上的一些操作。</h4>



<h3 id="1代码重新编译">1、代码重新编译</h3>

<pre><code>因为之前spark standalone项目使用的是spark 1.5.2版本，而现在的spark on yarn 使用的是spark 2.0.1，所以先需要对原来代码重新编译，建议使用maven构建项目，根据需要使用下面提供的pom.xml文件即可自动下载当前部署版本所需的jar包。
</code></pre>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.scala-lang<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>scala-library<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.11.8<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-core_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.scala-lang<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>scala-reflect<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.11.8<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-flume_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-sql_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-mllib_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-kafka-0-8_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.scalikejdbc<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>scalikejdbc_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.2.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>mysql<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>mysql-connector-java<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>5.1.35<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-mllib_2.11<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.0.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span></code></pre>



<h3 id="2参数变化">2、参数变化</h3>

<pre><code>因为spark standalone与spark on yarn的资源管理机制不同，所以有一些参数需要调整才能在spark on yarn运行，这里只介绍需要修改的部分，没涉及到的参数则不需要变动。
</code></pre>

<p>- master <br>
这里修改成yarn，此外–deploy-mode参数可以是client和cluster，这里的区别是client模式的driver在submit任务的机器上，作业运行日志直接从当前客户端打印，方便作业调试；而cluster模式是把driver随机分配到某一台机器，适用于作业调试后稳定运行。 <br>
- executor <br>
废弃standalone模式中的–total-executor-cores，而改用–num-executors</p>



<h3 id="3任务执行目录">3、任务执行目录</h3>

<pre><code>spark2.0.1客户端在hadoop001的/opt/spark2，为了平滑迁移，原来spark1.5.2的客户端路径不变，建议大家把新需求提在yarn上面。
</code></pre>



<h3 id="4作业历史记录查询">4、作业历史记录查询</h3>

<pre><code>spark的job historyserver部署在hadoop003，故历史记录查看地址是hadoop003.dx.momo.com:18080
</code></pre>

<p>这里以同一个任务在standalone和spark on yarn的执行脚本作为示范，以示区别： <br>
- standalone模式 <br>
bin/spark-submit –class spark.mllib.als  <strong>-master</strong> spark://spark002.dx.momo.com:6066 –deploy-mode cluster –executor-memory 4g  <strong>–total-executor-cores</strong> 200 –queue data <br>
- spark on yarn 模式 <br>
bin/spark-submit –class spark.mllib.als  <strong>-master</strong> yarn –deploy-mode cluster –executor-memory 4g  <strong>–num-executors</strong> 200 –queue data</p>



<h3 id="5下面提供提几个简单的spark-on-yarn使用接口方便大家学习和使用spark">5、下面提供提几个简单的spark on yarn使用接口，方便大家学习和使用spark：</h3>

<ul>
<li>spark-shell  作业调试中最常用的方法，方便代码调试及调优 <br>
bin/spark-shell –master yarn  –deploy-mode client –executor-memory 2G –num-executors 20 –executor-cores 1 –queue data</li>
<li>spark-sql  调试sql语句常用 <br>
bin/spark-sql –master yarn  –deploy-mode client –executor-memory 2G –num-executors 20 –executor-cores 1 –queue data</li>
</ul>



<h3 id="6作业迁移过程中可能的报错">6、作业迁移过程中可能的报错</h3>

<ul>
<li>Container killed by YARN for exceeding memory limits. 3.0 GB of 3 GB physical memory used. Consider boosting spark.yarn.executor.memoryOverhead <br>
分析： 因为spark on yarn会有yarn来检测spark container的资源使用情况（standalone没有），所以作业迁移过来很可能出现该报错，解决方法就是直接加大spark.yarn.executor.memoryOverhead或者executor memory（spark.yarn.executor.memoryOverhead默认为executor memory大小的的10%），可以在作业提交脚本中使用 –conf spark.yarn.executor.memoryOverhead=*  （如1024）或提高 –executor-memory 来保证作业在新环境下运行。</li>
</ul>



<h4 id="以上是迁移的指南供大家顺利迁移作业后续会继续整理作业调试及调优方面的分享"><strong>以上是迁移的指南，供大家顺利迁移作业，后续会继续整理作业调试及调优方面的分享.</strong></h4>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
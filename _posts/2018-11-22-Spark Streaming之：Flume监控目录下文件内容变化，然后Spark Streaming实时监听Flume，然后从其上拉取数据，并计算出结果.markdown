---
layout:     post
title:      Spark Streaming之：Flume监控目录下文件内容变化，然后Spark Streaming实时监听Flume，然后从其上拉取数据，并计算出结果
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/toto1297488504/article/details/75084472				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1、安装flume <br>
2、到Spark-Streaming官网下载poll方式的Sink <br>
3、将sink放入到flume的lib包里面 <br>
4、先启动flume(多个)，然后在启动Streaming程序</p>

<p>下载spark-flume <br>
<a href="http://spark.apache.org/documentation.html" rel="nofollow" target="_blank">http://spark.apache.org/documentation.html</a> <br>
到Spark-1.6.2中 <br>
<a href="http://spark.apache.org/docs/1.6.2/" rel="nofollow" target="_blank">http://spark.apache.org/docs/1.6.2/</a>，</p>

<p><img src="https://img-blog.csdn.net/20170713141514022?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170713141640572?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>搜一下flume</p>

<p>最后在安装的flume中加入：commons-lang3-3.3.2.jar、scala-library-2.10.5.jar、spark-streaming-flume-sink_2.10-1.6.1.jar，效果如右侧： <br>
<img src="https://img-blog.csdn.net/20170713142409145?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>同步到集群中的其它的flume中：</p>

<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@hadoop1</span> lib]<span class="hljs-comment"># pwd</span>
/home/tuzq/software/apache-flume-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin/lib
[root<span class="hljs-variable">@hadoop1</span> lib]<span class="hljs-comment"># scp -r * root<span class="hljs-yardoctag">@hadoop</span>2:$PWD</span>
[root<span class="hljs-variable">@hadoop1</span> lib]<span class="hljs-comment"># scp -r * root<span class="hljs-yardoctag">@hadoop</span>3:$PWD</span>
[root<span class="hljs-variable">@hadoop1</span> lib]<span class="hljs-comment"># scp -r * root<span class="hljs-yardoctag">@hadoop</span>4:$PWD</span>
[root<span class="hljs-variable">@hadoop1</span> lib]<span class="hljs-comment"># scp -r * root<span class="hljs-yardoctag">@hadoop</span>5:$PWD</span></code></pre>

<p>编写flume的配置文件：</p>

<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@hadoop1</span> agentconf]<span class="hljs-comment"># pwd</span>
/home/tuzq/software/apache-flume-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin/agentconf
[root<span class="hljs-variable">@hadoop1</span> agentconf]<span class="hljs-comment"># vim flume-poll.conf</span></code></pre>

<p>其中flume-poll.conf的内容如下：</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = spooldir
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.spoolDir</span> = /home/tuzq/software/flumedata
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.fileHeader</span> = true

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.SparkSink</span>
<span class="hljs-preprocessor">#表示从这里拉数据</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hostname</span> = hadoop1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">8888</span>

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p><strong>启动flume.</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@hadoop1</span> apache-flume-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin]<span class="hljs-comment"># cd /home/tuzq/software/apache-flume-1.6.0-bin</span>
[root<span class="hljs-variable">@hadoop1</span> apache-flume-<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span>-bin]<span class="hljs-comment"># bin/flume-ng agent -n a1 -c agentconf/ -f agentconf/flume-poll.conf -Dflume.root.logger=WARN,console</span></code></pre>

<p>启动后的效果如下： <br>
<img src="https://img-blog.csdn.net/20170713155858234?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>这样，一直启动Flume</p>

<p><strong>然后编写从Flume中读取数据的程序。</strong> <br>
pom文件的内容如下：</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>cn.toto.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>bigdata<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">maven.compiler.source</span>&gt;</span>1.7<span class="hljs-tag">&lt;/<span class="hljs-title">maven.compiler.source</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">maven.compiler.target</span>&gt;</span>1.7<span class="hljs-tag">&lt;/<span class="hljs-title">maven.compiler.target</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">encoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">encoding</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">scala.version</span>&gt;</span>2.10.6<span class="hljs-tag">&lt;/<span class="hljs-title">scala.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">spark.version</span>&gt;</span>1.6.2<span class="hljs-tag">&lt;/<span class="hljs-title">spark.version</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">hadoop.version</span>&gt;</span>2.6.4<span class="hljs-tag">&lt;/<span class="hljs-title">hadoop.version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.scala-lang<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>scala-library<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.scala-lang<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>scala-compiler<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.scala-lang<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>scala-reflect<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${scala.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-core_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hadoop-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${hadoop.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>mysql<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>mysql-connector-java<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>5.1.38<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-sql_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-mllib_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-hive_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-flume_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.spark<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spark-streaming-kafka_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${spark.version}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">build</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">sourceDirectory</span>&gt;</span>src/main/scala<span class="hljs-tag">&lt;/<span class="hljs-title">sourceDirectory</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">testSourceDirectory</span>&gt;</span>src/test/scala<span class="hljs-tag">&lt;/<span class="hljs-title">testSourceDirectory</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">plugins</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>net.alchim31.maven<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>scala-maven-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>3.2.2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">executions</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">execution</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">goals</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">goal</span>&gt;</span>compile<span class="hljs-tag">&lt;/<span class="hljs-title">goal</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">goal</span>&gt;</span>testCompile<span class="hljs-tag">&lt;/<span class="hljs-title">goal</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">goals</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">args</span>&gt;</span>
                                <span class="hljs-tag">&lt;<span class="hljs-title">arg</span>&gt;</span>-make:transitive<span class="hljs-tag">&lt;/<span class="hljs-title">arg</span>&gt;</span>
                                <span class="hljs-tag">&lt;<span class="hljs-title">arg</span>&gt;</span>-dependencyfile<span class="hljs-tag">&lt;/<span class="hljs-title">arg</span>&gt;</span>
                                <span class="hljs-tag">&lt;<span class="hljs-title">arg</span>&gt;</span>${project.build.directory}/.scala_dependencies<span class="hljs-tag">&lt;/<span class="hljs-title">arg</span>&gt;</span>
                            <span class="hljs-tag">&lt;/<span class="hljs-title">args</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">execution</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">executions</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>

            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.maven.plugins<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-surefire-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.18.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">useFile</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">useFile</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">disableXmlReport</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">disableXmlReport</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">includes</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">include</span>&gt;</span>**/*Test.*<span class="hljs-tag">&lt;/<span class="hljs-title">include</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">include</span>&gt;</span>**/*Suite.*<span class="hljs-tag">&lt;/<span class="hljs-title">include</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">includes</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>

            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.maven.plugins<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-shade-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.4.3<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">executions</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">execution</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">phase</span>&gt;</span>package<span class="hljs-tag">&lt;/<span class="hljs-title">phase</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">goals</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">goal</span>&gt;</span>shade<span class="hljs-tag">&lt;/<span class="hljs-title">goal</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">goals</span>&gt;</span>
                        <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">filters</span>&gt;</span>
                                <span class="hljs-tag">&lt;<span class="hljs-title">filter</span>&gt;</span>
                                    <span class="hljs-tag">&lt;<span class="hljs-title">artifact</span>&gt;</span>*:*<span class="hljs-tag">&lt;/<span class="hljs-title">artifact</span>&gt;</span>
                                    <span class="hljs-tag">&lt;<span class="hljs-title">excludes</span>&gt;</span>
                                        <span class="hljs-tag">&lt;<span class="hljs-title">exclude</span>&gt;</span>META-INF/*.SF<span class="hljs-tag">&lt;/<span class="hljs-title">exclude</span>&gt;</span>
                                        <span class="hljs-tag">&lt;<span class="hljs-title">exclude</span>&gt;</span>META-INF/*.DSA<span class="hljs-tag">&lt;/<span class="hljs-title">exclude</span>&gt;</span>
                                        <span class="hljs-tag">&lt;<span class="hljs-title">exclude</span>&gt;</span>META-INF/*.RSA<span class="hljs-tag">&lt;/<span class="hljs-title">exclude</span>&gt;</span>
                                    <span class="hljs-tag">&lt;/<span class="hljs-title">excludes</span>&gt;</span>
                                <span class="hljs-tag">&lt;/<span class="hljs-title">filter</span>&gt;</span>
                            <span class="hljs-tag">&lt;/<span class="hljs-title">filters</span>&gt;</span>
                            <span class="hljs-tag">&lt;<span class="hljs-title">transformers</span>&gt;</span>
                                <span class="hljs-tag">&lt;<span class="hljs-title">transformer</span> <span class="hljs-attribute">implementation</span>=<span class="hljs-value">"org.apache.maven.plugins.shade.resource.ManifestResourceTransformer"</span>&gt;</span>
                                    <span class="hljs-tag">&lt;<span class="hljs-title">mainClass</span>&gt;</span>cn.toto.spark.FlumeStreamingWordCount<span class="hljs-tag">&lt;/<span class="hljs-title">mainClass</span>&gt;</span>
                                <span class="hljs-tag">&lt;/<span class="hljs-title">transformer</span>&gt;</span>
                            <span class="hljs-tag">&lt;/<span class="hljs-title">transformers</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;/<span class="hljs-title">execution</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">executions</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">plugins</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">build</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>

<p>编写代码：</p>

<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.toto</span><span class="hljs-preprocessor">.spark</span>

import java<span class="hljs-preprocessor">.net</span><span class="hljs-preprocessor">.InetSocketAddress</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.SparkConf</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.storage</span><span class="hljs-preprocessor">.StorageLevel</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.dstream</span>.{DStream, ReceiverInputDStream}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.flume</span>.{FlumeUtils, SparkFlumeEvent}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>.{Seconds, StreamingContext}

<span class="hljs-comment">/**
  * Created by toto on 2017/7/13.
  */</span>
object FlumeStreamingWordCount {
  def main(args: Array[String]): Unit = {
    val conf = new SparkConf()<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"FlumeStreamingWordCount"</span>)<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local[2]"</span>)
    //创建StreamingContext并设置产生批次的间隔时间
    val ssc = new StreamingContext(conf,Seconds(<span class="hljs-number">15</span>))
    //从Socket端口中创建RDD,这里的SocketAddress可以传递多个
    val flumeStream: ReceiverInputDStream[SparkFlumeEvent] =
      FlumeUtils<span class="hljs-preprocessor">.createPollingStream</span>(ssc, Array(new InetSocketAddress(<span class="hljs-string">"hadoop1"</span>, <span class="hljs-number">8888</span>)),
        StorageLevel<span class="hljs-preprocessor">.MEMORY</span>_AND_DISK)
    //去取Flume中的数据
    val words = flumeStream<span class="hljs-preprocessor">.flatMap</span>(<span class="hljs-built_in">x</span> =&gt; new String(<span class="hljs-built_in">x</span><span class="hljs-preprocessor">.event</span><span class="hljs-preprocessor">.getBody</span>()<span class="hljs-preprocessor">.array</span>())<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))
    val wordAndOne : DStream[(String,Int)] = words<span class="hljs-preprocessor">.map</span>((_,<span class="hljs-number">1</span>))
    val result : DStream[(String,Int)] = wordAndOne<span class="hljs-preprocessor">.reduceByKey</span>(_+_)
    //打印
    result<span class="hljs-preprocessor">.print</span>()
    //开启程序
    ssc<span class="hljs-preprocessor">.start</span>()
    //等待结束
    ssc<span class="hljs-preprocessor">.awaitTermination</span>()
  }
}</code></pre>

<p>启动程序。然后往Flume监控的flumedata目录下放入文件，如： <br>
<img src="https://img-blog.csdn.net/20170713165612602?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>其中1.txt的内容如下： <br>
<img src="https://img-blog.csdn.net/20170713165701591?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>最后在IDEA的控制台中观察结果： <br>
<img src="https://img-blog.csdn.net/20170713165817376?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
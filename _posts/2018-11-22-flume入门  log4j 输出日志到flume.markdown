---
layout:     post
title:      flume入门  log4j 输出日志到flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>将log4j产生的日志直接输出到flume控制台</p>



<h3 id="1编写客户端">1.编写客户端</h3>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.study.test;

<span class="hljs-keyword">import</span> org.apache.commons.logging.Log;
<span class="hljs-keyword">import</span> org.apache.commons.logging.LogFactory;

<span class="hljs-keyword">import</span> java.util.Date;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WriteLog</span> {</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Log logger = LogFactory.getLog(WriteLog.class);


    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> InterruptedException {

        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
            <span class="hljs-comment">// 每隔两秒log输出一下当前系统时间戳</span>
            logger.info(<span class="hljs-keyword">new</span> Date().getTime());
            <span class="hljs-comment">//System.out.println(new Date().getTime());</span>
            Thread.sleep(<span class="hljs-number">2000</span>);
            <span class="hljs-keyword">try</span> {
                <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> Exception(<span class="hljs-string">"exception msg"</span>);
            }
            <span class="hljs-keyword">catch</span> (Exception e) {
                logger.error(<span class="hljs-string">"error:"</span> + e.getMessage());
            }
        }
    }
}
</code></pre>



<h3 id="2pomxml">2.pom.xml</h3>



<pre class="prettyprint"><code class=" hljs xml">    <span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>com.study.test<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>log4j2flume<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume.flume-ng-clients<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-log4jappender<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.7.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.slf4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>slf4j-simple<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.6.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>



<h3 id="3-log4jproperties">3. log4j.properties</h3>



<pre class="prettyprint"><code class=" hljs perl">log4j.rootLogger=INFO,flume
log4j.appender.console=org.apache.log4j.ConsoleAppender
log4j.appender.console.target=System.out
log4j.appender.console.layout=org.apache.log4j.PatternLayout
log4j.appender.console.layout.ConversionPattern=<span class="hljs-string">"<span class="hljs-variable">%d</span>{yyyy-MM-dd HH:mm:ss} <span class="hljs-variable">%p</span> [<span class="hljs-variable">%c</span>:<span class="hljs-variable">%L</span>] - <span class="hljs-variable">%m</span><span class="hljs-variable">%n</span>

log4j.appender.flume = org.apache.flume.clients.log4jappender.Log4jAppender
log4j.appender.flume.Hostname = 10.45.17.66
log4j.appender.flume.Port = 4444
log4j.appender.flume.UnsafeMode = true
log4j.appender.flume.layout=org.apache.log4j.PatternLayout
log4j.appender.flume.layout.ConversionPattern=<span class="hljs-variable">%d</span>{yyyy-MM-dd HH:mm:ss} <span class="hljs-variable">%p</span> [<span class="hljs-variable">%c</span>:<span class="hljs-variable">%L</span>] - <span class="hljs-variable">%m</span><span class="hljs-variable">%n</span></span></code></pre>



<h3 id="4配置flume-建立testlog2flumeconf">4.配置flume 建立testlog2flume.conf</h3>



<pre class="prettyprint"><code class=" hljs avrasm">a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = avro
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> =<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">4444</span>

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span>=logger
<span class="hljs-preprocessor">#a1.sinks.k1.type = file_roll</span>
<span class="hljs-preprocessor">#a1.sinks.k1.sink.directory = /data/soft/flume/tmp</span>
<span class="hljs-preprocessor">#a1.sinks.k1.sink.rollInterval=86400</span>
<span class="hljs-preprocessor">#a1.sinks.k1.sink.batchSize=100</span>
<span class="hljs-preprocessor">#a1.sinks.k1.sink.serializer=text</span>
<span class="hljs-preprocessor">#a1.sinks.k1.sink.serializer.appendNewline = false</span>

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">1000</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
</code></pre>

<p>该配置文件中 配置了一个代理a1  <br>
在代理agent中配置了一个source（源）一个sink（接收器）和一个channel（通道），分别为：r1，k1，c1 <br>
r1的类型定义为avro(序列化)，对应该类型参数为bind和port 分别为0.0.0.0和4141 <br>
k1的类型定义为logger，直接输出到日志文件中 <br>
cl的类型定义为内存方式，设置其参数capactiy和transactionCapacity分别为1000和1000  <br>
指定r1和k1的channel为c1</p>

<p>将上述配置中a1.sinks.k1.type=logger注释，把a1.sinks其他的给打开，就是将日志输入到文件</p>



<h3 id="5到flume目录下启动flume">5.到flume目录下启动flume</h3>



<pre class="prettyprint"><code class=" hljs lasso">bin/flume<span class="hljs-attribute">-ng</span> agent <span class="hljs-attribute">-c</span> conf <span class="hljs-attribute">-f</span> conf/testlog2flume<span class="hljs-built_in">.</span>conf <span class="hljs-subst">--</span>name a1 <span class="hljs-attribute">-Dflume</span><span class="hljs-built_in">.</span>root<span class="hljs-built_in">.</span>logger<span class="hljs-subst">=</span>INFO,console</code></pre>

<p>flume-ng ：flume 命令 <br>
agent：运行一个Flume Agent <br>
-c：在指定目录下使用配置 use configs in  directory <br>
-f：指定配置文件，这个配置文件必须在全局选项的–conf（-c）参数定义的目录下 <br>
–name:Agent的名称（必填） <br>
 -Dflume.root.logger=INFO,console 该参数将会把flume的日志输出到console</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
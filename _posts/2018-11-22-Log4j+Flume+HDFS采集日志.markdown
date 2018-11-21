---
layout:     post
title:      Log4j+Flume+HDFS采集日志
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="环境">环境</h3>

<p>首先我们搭建好了hadoop环境，flume等软件。 <br>
我们首先配置flume中的flume.conf</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#Name the components on this agent</span>
tier1<span class="hljs-preprocessor">.sources</span>=source1  
tier1<span class="hljs-preprocessor">.channels</span>=channel1  
tier1<span class="hljs-preprocessor">.sinks</span>=sink1  


tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.type</span>=avro  
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.bind</span>=<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
<span class="hljs-preprocessor">#监听的端口号  </span>
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.port</span>=<span class="hljs-number">44444</span>  
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.channels</span>=channel1  

tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span>=i1
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.type</span>=timestamp
tier1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.source</span>1<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.preserveExisting</span>=true


tier1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.channel</span>1<span class="hljs-preprocessor">.type</span>=memory  


tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.type</span>=hdfs  
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.channel</span>=channel1  
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://master:<span class="hljs-number">8020</span>/flume/%<span class="hljs-built_in">Y</span>-%m-%d
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream  
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span>=Text
<span class="hljs-preprocessor">#间隔多长时间将临时文件滚动成最终目标文件，默认值是30，如果设置0则不根据时间滚动文件    </span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">0</span>
<span class="hljs-preprocessor">#当临时文件达到该文件大小滚动文件  </span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span>=<span class="hljs-number">102400</span>
<span class="hljs-preprocessor">#当events数据达到该数量时，将临时文件滚动成目标文件，0表示不根据events数据来滚动    </span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span>=<span class="hljs-number">0</span>
<span class="hljs-preprocessor">#当目前被打开的临时文件在该参数指定的时间(秒)内，没有数据写入，则将临时文件关闭并重名为目标文件    </span>
tier1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.sink</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.idleTimeout</span>=<span class="hljs-number">60</span></code></pre>

<p>再来写java工程，我这里使用的maven构建项目。 <br>
1.配置pom.xml <br>
在MyEclipse创建Maven项目中的pom.xml中加入如下内容，配置所需要的依赖。</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.slf4j<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>slf4j-log4j12<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.7.10<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-core<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.5.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume.flume-ng-clients<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-log4jappender<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.5.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span></code></pre>

<p>2.配置log4j.properties</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">##Log4j有三个主要组件:Loggers(记录器),Appenders(输出源)和Layouts(布局)</span>
<span class="hljs-preprocessor">###这里可以简单理解为日志的类别，日志要输出的地方，日志以何种形式输出</span>


<span class="hljs-preprocessor">##config rootLogger配置根记录器</span>
<span class="hljs-preprocessor">###格式:log4j.rootLogger = [level],appenderName1,appenderName2</span>

<span class="hljs-preprocessor">###实例log4j的根记录器,定义记录器必须得大于等于INFO的级别才能输出,输出源为stdout和flume。</span>
<span class="hljs-preprocessor">####其中级别有DEBUG、INFO、WARN、ERROR和FATAL,其中这个五个的级别顺序是,DEBUG&lt;INFO&lt;WARN&lt;ERROR&lt;FATAL。</span>
log4j<span class="hljs-preprocessor">.rootLogger</span> = INFO,stdout,flume


<span class="hljs-preprocessor">##配置stdout输出源(控制台输出)</span>
<span class="hljs-preprocessor">###配置输出源的类名</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.ConsoleAppender</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.Target</span>=System<span class="hljs-preprocessor">.out</span>
<span class="hljs-preprocessor">###指定日志信息的level为INFO</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.Threshold</span>=INFO
<span class="hljs-preprocessor">###指定输出的格式的类</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
<span class="hljs-preprocessor">###指定输出的格式</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.layout</span><span class="hljs-preprocessor">.ConversionPattern</span>=%d{yyyy-MM-dd HH:mm:ss} %c{<span class="hljs-number">1</span>} [%p] %m%n


<span class="hljs-preprocessor">##配置flume输出源(输出到flume中)</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.log</span>4jappender<span class="hljs-preprocessor">.Log</span>4jAppender
<span class="hljs-preprocessor">###配置flume所在的主机号</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.Hostname</span>=master
<span class="hljs-preprocessor">###配置flume监听的端口</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.Port</span>=<span class="hljs-number">44444</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.UnsafeMode</span> = true
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.Threshold</span>=INFO
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.layout</span><span class="hljs-preprocessor">.ConversionPattern</span>=%d{yyyy-MM-dd HH\:mm\:ss} %c{<span class="hljs-number">1</span>} [%p] %m%n</code></pre>

<p>3.编写测试类</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.slf4j.Logger;
<span class="hljs-keyword">import</span> org.slf4j.LoggerFactory;
<span class="hljs-keyword">import</span> com.log.flume.writeLog;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">writeLog</span> {</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = LoggerFactory
            .getLogger(writeLog.class);

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">echo</span>() {
        System.out.println(<span class="hljs-keyword">new</span> Date() + <span class="hljs-string">"============"</span>);
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">while</span> (<span class="hljs-keyword">true</span>) {
            logger.info(String.valueOf(<span class="hljs-keyword">new</span> Date().getTime()));
            <span class="hljs-keyword">try</span> {
                Thread.sleep(<span class="hljs-number">2000</span>);
            } <span class="hljs-keyword">catch</span> (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}</code></pre>

<p>在启动flume后，运行程序便可以将信息通过log4j传入flume中，flume再存放到你之前在flume.conf中配置的hdfs的相应的路径下。我们便可以在hdfs路径下查看打印的内容。 <br>
在实际应用时我们可以在做相应的操作时，加上log4j的打印，便可以实现采集用户操作的日志信息。方便后面对这些日志进行分析工作。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
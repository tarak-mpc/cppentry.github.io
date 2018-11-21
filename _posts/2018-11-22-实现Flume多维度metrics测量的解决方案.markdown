---
layout:     post
title:      实现Flume多维度metrics测量的解决方案
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc">
<ul>
<li><ul>
<li><ul>
<li><a href="#kafka-metrics-%E5%AE%9E%E7%8E%B0%E6%96%B9%E5%BC%8F" rel="nofollow">kafka metrics 实现方式</a></li>
<li><a href="#%E5%A2%9E%E5%BC%BAflume%E6%B5%8B%E9%87%8F%E5%8A%9F%E8%83%BD" rel="nofollow">增强flume测量功能</a><ul>
<li><a href="#%E5%85%88%E5%AE%9A%E4%B9%89%E5%85%A8%E5%B1%80%E7%9A%84metricsregistry" rel="nofollow">先定义全局的MetricsRegistry</a></li>
<li><a href="#%E7%BC%96%E5%86%99interceptor" rel="nofollow">编写Interceptor</a></li>
<li><a href="#%E5%88%9B%E5%BB%BA%E7%9B%91%E6%8E%A7%E6%9C%8D%E5%8A%A1-influxmetricsserver" rel="nofollow">创建监控服务 InfluxMetricsServer</a></li>
<li><a href="#report%E5%87%BAmetrics" rel="nofollow">report出metrics</a></li>
</ul>
</li>
<li><a href="#%E6%80%BB%E7%BB%93" rel="nofollow">总结</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>


<p>由于公司大数据架构是，flume收集所有数据，流到kafka。 <br>
kafka自带相关metrics测量，而flume没有，导致数据定位发生困难。 <br>
为此，特地研究了kafka metrics的相关源码模块，将其实现原理移植到flume</p>

<h3 id="kafka-metrics-实现方式">kafka metrics 实现方式</h3>

<p>kafka 相关测量模块主要在入口处，是用scala语言实现。 <br>
通过研究，发现kafka 是用 开源测量库 yammer metrics 2.2.0，实现了count gague、histogram、meter、timer 五种测量类型</p>



<h3 id="增强flume测量功能">增强flume测量功能</h3>

<p>在flume Interceptor 位置，编写自定义的Interceptor拦截器。模拟kafka的实现，也利用yammer  metrics 2.2.0。</p>



<h4 id="先定义全局的metricsregistry">先定义全局的MetricsRegistry</h4>

<p>其实不定义也行yammer   metrics 2.2.0有默认的MetricsRegistry，为防止受干扰，自定义了下</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> global;

<span class="hljs-keyword">import</span> com.yammer.metrics.core.MetricsRegistry;

<span class="hljs-javadoc">/**
 * Created by shen.xiangxiang on 2017/4/21.
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">GlobalMetricRegistry</span> {</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span>  MetricsRegistry flumeMetricsRegistry = <span class="hljs-keyword">new</span> MetricsRegistry();

}
</code></pre>



<h4 id="编写interceptor">编写Interceptor</h4>

<p>编写自定义的Interceptor ，对经过flume的每一条event 进行测量 <br>
关键是覆写intercept() 方法 <br>
此处贴出样例代码，读者可以根据自己的业务场景做相应的修改</p>



<pre class="prettyprint"><code class=" hljs javascript">@Override
    public Event intercept(Event event) {
        log.debug(<span class="hljs-string">"HeaderTopicInterceptor intercept start"</span>);
        Event eventOut;
        Map&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt; eventHeader = <span class="hljs-keyword">new</span> HashMap&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt;();
        <span class="hljs-comment">// 获得当前消息头</span>
        Map&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt; eventInputHeader = event.getHeaders();
        <span class="hljs-comment">// 提取消息头的消息类型字段</span>
        <span class="hljs-built_in">String</span> messageType = <span class="hljs-string">""</span>;
        <span class="hljs-keyword">if</span> (eventInputHeader.containsKey(<span class="hljs-string">"mt"</span>)) {
            messageType = eventInputHeader.get(<span class="hljs-string">"mt"</span>);
            <span class="hljs-keyword">if</span> (messageType.equals(<span class="hljs-string">"TEXT"</span>)) {
                <span class="hljs-comment">// 文本消息，增加主题键</span>
                <span class="hljs-built_in">String</span> eventBody = <span class="hljs-keyword">new</span> <span class="hljs-built_in">String</span>(event.getBody());
                <span class="hljs-comment">// split eventBody by '|'</span>
                <span class="hljs-built_in">String</span>[] eventBodySplit = eventBody.split(<span class="hljs-string">"\\|"</span>);
                <span class="hljs-keyword">if</span> (eventBodySplit.length &gt;= <span class="hljs-number">6</span>) {
                    <span class="hljs-comment">// check first item in eventBodySplit is 'V2'</span>
                    <span class="hljs-keyword">if</span> (eventBodySplit[<span class="hljs-number">0</span>].equals(DEFAULT_TEXT_MSG_HEADER_VERSION)) {
                        <span class="hljs-comment">// set Topic Key as the third item in eventBodySplit</span>
                        <span class="hljs-comment">// TODO: 增加一个计数器</span>
                        <span class="hljs-comment">// 判断subject是否合法 subject = domain.dataType dataType即topic</span>
                        boolean illegal = <span class="hljs-literal">false</span>;
                        <span class="hljs-built_in">String</span> topic = eventBodySplit[<span class="hljs-number">2</span>];
                        <span class="hljs-built_in">String</span> businessDomain = eventBodySplit[<span class="hljs-number">3</span>];
                        <span class="hljs-built_in">String</span> _subject = businessDomain + <span class="hljs-string">"."</span> + topic;
                        <span class="hljs-built_in">String</span> timestampString = eventBodySplit[<span class="hljs-number">4</span>];
                        SimpleDateFormat timestampFormater = <span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyyy-MM-dd HH:mm:ss"</span>);

                        <span class="hljs-comment">//用于测量</span>
                        Map&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt; mapEvent = <span class="hljs-keyword">new</span> HashMap&lt;<span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span>&gt;();
                        mapEvent.put(<span class="hljs-string">"domain"</span>, businessDomain);
                        mapEvent.put(<span class="hljs-string">"topic"</span>, topic);
                        mapEvent.put(<span class="hljs-string">"id"</span>, ID);
                        mapEvent.put(<span class="hljs-string">"mt"</span>, messageType);
                        <span class="hljs-built_in">String</span> scope = bulidScope(mapEvent);


                        <span class="hljs-keyword">try</span> {
                            timestampFormater.setLenient(<span class="hljs-literal">false</span>);
                            timestampFormater.parse(timestampString);
                            <span class="hljs-keyword">if</span> (SubjectWhitelistUtil.subjects.contains(_subject)) {
                                illegal = <span class="hljs-literal">true</span>;
                            }
                        } <span class="hljs-keyword">catch</span> (ParseException e) {

                        }

                        <span class="hljs-keyword">if</span> (illegal) {
                            eventHeader.put(KafkaSink.TOPIC_HDR, topic);

                            <span class="hljs-comment">//message count 统计</span>
                            Meter meterMessage = GlobalMetricRegistry.flumeMetricsRegistry.newMeter(<span class="hljs-keyword">new</span> MetricName(getClass(), <span class="hljs-string">"flume_FilterSuccessMessages"</span>, scope), <span class="hljs-string">"messages"</span>, TimeUnit.SECONDS);
                            meterMessage.mark(<span class="hljs-number">1</span>);

                            <span class="hljs-comment">//message byte</span>
                            Meter meterBytes = GlobalMetricRegistry.flumeMetricsRegistry.newMeter(<span class="hljs-keyword">new</span> MetricName(getClass(), <span class="hljs-string">"flume_FilterSuccessBytes"</span>, scope), <span class="hljs-string">"bytes"</span>, TimeUnit.SECONDS);
                            meterBytes.mark(event.getBody().length);

                            Histogram hisBytes = GlobalMetricRegistry.flumeMetricsRegistry.newHistogram(<span class="hljs-keyword">new</span> MetricName(getClass(), <span class="hljs-string">"flume_FilterSuccessBytesHis"</span>, scope), <span class="hljs-literal">false</span>);
                            hisBytes.update(event.getBody().length);


                        } <span class="hljs-keyword">else</span> {
                            eventHeader.put(KafkaSink.TOPIC_HDR, DEFAULT_UNKNOWN_TOPIC);

                            mapEvent.put(<span class="hljs-string">"dropReason"</span>, <span class="hljs-string">"notInSubjectWhitelist"</span>);
                            scope = bulidScope(mapEvent);

                            <span class="hljs-comment">//message count 统计</span>
                            Meter meterMessage = GlobalMetricRegistry.flumeMetricsRegistry.newMeter(<span class="hljs-keyword">new</span> MetricName(getClass(), <span class="hljs-string">"flume_FilterDropMessages"</span>, scope), <span class="hljs-string">"messages"</span>, TimeUnit.SECONDS);
                            meterMessage.mark(<span class="hljs-number">1</span>);

                            <span class="hljs-comment">//message byte</span>
                            Meter meterBytes = GlobalMetricRegistry.flumeMetricsRegistry.newMeter(<span class="hljs-keyword">new</span> MetricName(getClass(), <span class="hljs-string">"flume_FilterDropBytes"</span>, scope), <span class="hljs-string">"bytes"</span>, TimeUnit.SECONDS);
                            meterBytes.mark(event.getBody().length);

                            Histogram hisBytes = GlobalMetricRegistry.flumeMetricsRegistry.newHistogram(<span class="hljs-keyword">new</span> MetricName(getClass(), <span class="hljs-string">"flume_FilterDropBytesHis"</span>, scope), <span class="hljs-literal">false</span>);
                            hisBytes.update(event.getBody().length);

                        }


                    } 
                } 
            } 
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-comment">// Cannot find message type</span>
            <span class="hljs-comment">// TODO: 增加一个计数器</span>
            eventHeader.put(KafkaSink.TOPIC_HDR, DEFAULT_UNKNOWN_TOPIC);
        }

        <span class="hljs-comment">// set Event</span>
        eventOut = EventBuilder.withBody(event.getBody(), eventHeader);
        log.debug(<span class="hljs-string">"HeaderTopicInterceptor intercept end"</span>);
        <span class="hljs-keyword">return</span> eventOut;
    }</code></pre>

<p>封装的bulidScope() 方法</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-keyword">public</span> <span class="hljs-built_in">String</span> bulidScope(<span class="hljs-built_in">Map</span><span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span><span class="hljs-subst">&gt;</span> mapBusiness) {
        <span class="hljs-built_in">String</span> scope <span class="hljs-subst">=</span> <span class="hljs-string">""</span>;
        Iterator<span class="hljs-subst">&lt;</span><span class="hljs-built_in">Map</span><span class="hljs-built_in">.</span>Entry<span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span><span class="hljs-subst">&gt;&gt;</span> entries <span class="hljs-subst">=</span> mapBusiness<span class="hljs-built_in">.</span>entrySet()<span class="hljs-built_in">.</span>iterator();
        <span class="hljs-keyword">while</span> (entries<span class="hljs-built_in">.</span>hasNext()) {
            <span class="hljs-built_in">Map</span><span class="hljs-built_in">.</span>Entry<span class="hljs-subst">&lt;</span><span class="hljs-built_in">String</span>, <span class="hljs-built_in">String</span><span class="hljs-subst">&gt;</span> entry <span class="hljs-subst">=</span> entries<span class="hljs-built_in">.</span>next();
            <span class="hljs-built_in">String</span> strKey <span class="hljs-subst">=</span> entry<span class="hljs-built_in">.</span>getKey();
            <span class="hljs-built_in">String</span> strVal <span class="hljs-subst">=</span> entry<span class="hljs-built_in">.</span>getValue();
            scope <span class="hljs-subst">+=</span> strKey <span class="hljs-subst">+</span> <span class="hljs-string">"."</span> <span class="hljs-subst">+</span> strVal <span class="hljs-subst">+</span> <span class="hljs-string">"."</span>;
        }
        <span class="hljs-keyword">return</span> scope<span class="hljs-built_in">.</span>substring(<span class="hljs-number">0</span>, scope<span class="hljs-built_in">.</span>length() <span class="hljs-subst">-</span> <span class="hljs-number">1</span>);
    }</code></pre>



<h4 id="创建监控服务-influxmetricsserver">创建监控服务 InfluxMetricsServer</h4>

<p>样例代码如下</p>



<pre class="prettyprint"><code class=" hljs java">
<span class="hljs-keyword">package</span> org.apache.flume.instrumentation.http;

<span class="hljs-keyword">import</span> global.GlobalMetricRegistry;
<span class="hljs-keyword">import</span> org.apache.flume.Context;
<span class="hljs-keyword">import</span> org.apache.flume.conf.ConfigurationException;
<span class="hljs-keyword">import</span> org.apache.flume.instrumentation.MonitorService;
<span class="hljs-keyword">import</span> org.apache.flume.instrumentation.http.tool.HostUtils;
<span class="hljs-keyword">import</span> org.slf4j.Logger;
<span class="hljs-keyword">import</span> org.slf4j.LoggerFactory;

<span class="hljs-keyword">import</span> java.util.concurrent.TimeUnit;

<span class="hljs-javadoc">/**
 * A Monitor service implementation that runs a web server on a configurable
 * port and returns the metrics for components in JSON format.
 * &lt;p&gt;
 * Optional parameters:
 * &lt;p&gt;
 * &lt;tt&gt;port&lt;/tt&gt; : The port on which the server should listen to.
 * &lt;p&gt;
 * Returns metrics in the following format:
 * &lt;p&gt;
 *
 * {
 * &lt;p&gt;
 * "componentName1":{"metric1" : "metricValue1","metric2":"metricValue2"}
 * &lt;p&gt;
 * "componentName1":{"metric3" : "metricValue3","metric4":"metricValue4"}
 * &lt;p&gt;
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">InfluxMetricsServer</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">MonitorService</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Logger logger = LoggerFactory
            .getLogger(InfluxMetricsServer.class);

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">final</span> String CONF_HOST = <span class="hljs-string">"host"</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">final</span> String CONF_POLL_FREQUENCY = <span class="hljs-string">"pollFrequency"</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">int</span> DEFAULT_POLL_FREQUENCY = <span class="hljs-number">60</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">final</span> String CONF_VERSION = <span class="hljs-string">"version"</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">final</span> String DEFAULT_VERSION = <span class="hljs-string">"1.0.0"</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">final</span> String CONF_INSTANCE_ID = <span class="hljs-string">"instanceId"</span>;

    <span class="hljs-keyword">private</span> String path = <span class="hljs-string">"http://"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> pollFrequency;
    <span class="hljs-keyword">private</span> String version;

<span class="hljs-comment">//  private ScheduledExecutorService service = Executors</span>
<span class="hljs-comment">//          .newSingleThreadScheduledExecutor();</span>

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String localhost = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String instanceId = <span class="hljs-keyword">null</span>;



    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configure</span>(Context context) {
        <span class="hljs-comment">// http地址</span>
        String host = context.getString(<span class="hljs-keyword">this</span>.CONF_HOST);



        <span class="hljs-keyword">if</span> ((host == <span class="hljs-keyword">null</span>) || host.isEmpty()) {
            <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> ConfigurationException(<span class="hljs-string">"Hosts list cannot be empty."</span>);
        }



        path += host;
        <span class="hljs-comment">// 频率/s</span>
        <span class="hljs-keyword">this</span>.pollFrequency = context.getInteger(<span class="hljs-keyword">this</span>.CONF_POLL_FREQUENCY,
                <span class="hljs-keyword">this</span>.DEFAULT_POLL_FREQUENCY);
        <span class="hljs-comment">// 测量消息schema版本</span>
        <span class="hljs-keyword">this</span>.version = context.getString(<span class="hljs-keyword">this</span>.CONF_VERSION,
                <span class="hljs-keyword">this</span>.DEFAULT_VERSION);
        <span class="hljs-comment">// 实例id</span>
        localhost = HostUtils.getHostName();

        <span class="hljs-comment">//实例id</span>
        instanceId = context.getString(<span class="hljs-keyword">this</span>.CONF_INSTANCE_ID,<span class="hljs-string">"unknown"</span>);
<span class="hljs-comment">//      if (instanceId == null || instanceId.isEmpty()) {</span>
<span class="hljs-comment">//          throw new ConfigurationException("instanceId list cannot be empty.");</span>
<span class="hljs-comment">//      }</span>
        logger.info(<span class="hljs-string">"InfluxMetricsServer configure finished"</span>);
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">start</span>() {



<span class="hljs-comment">//      if (service.isShutdown() || service.isTerminated()) {</span>
<span class="hljs-comment">//          service = Executors.newSingleThreadScheduledExecutor();</span>
<span class="hljs-comment">//      }</span>
<span class="hljs-comment">//      logger.info("InfluxMetricsServer start before scheduleWithFixedDelay");</span>
<span class="hljs-comment">//      service.scheduleWithFixedDelay(this.task(), 0, pollFrequency, TimeUnit.SECONDS);</span>
<span class="hljs-comment">//      logger.info("InfluxMetricsServer start after scheduleWithFixedDelay");</span>

        <span class="hljs-comment">//TODO  增加 report</span>
        logger.info(<span class="hljs-string">" enable InfluxReporter"</span>);
<span class="hljs-comment">//      InfluxReporter reporter = new InfluxReporter(GlobalMetricRegistry.flumeMetricsRegistry,path, MetricPredicate.ALL);</span>
<span class="hljs-comment">//      reporter.start(pollFrequency, TimeUnit.SECONDS);</span>

        InfluxReporter.enable(GlobalMetricRegistry.flumeMetricsRegistry, pollFrequency, path ,TimeUnit.SECONDS);

        logger.info(<span class="hljs-string">" after InfluxReporter"</span>);


    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">stop</span>() {
        logger.info(<span class="hljs-string">"InfluxMetricsServer configure stop"</span>);
    }



}



</code></pre>



<h4 id="report出metrics">report出metrics</h4>

<p>上述监控I服务nfluxMetricsServer样例代码使用了 InfluxReporter  <br>
样例代码如下，同时也可以参考我之前的blog《基于yammer metrics 开发InfluxReport》: <a href="https://blog.csdn.net/zhixingheyi_tian/article/details/77848432" rel="nofollow">https://blog.csdn.net/zhixingheyi_tian/article/details/77848432</a> </p>



<pre class="prettyprint"><code class=" hljs avrasm">
package org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.instrumentation</span><span class="hljs-preprocessor">.http</span><span class="hljs-comment">;</span>

import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.yammer</span><span class="hljs-preprocessor">.metrics</span><span class="hljs-preprocessor">.Metrics</span><span class="hljs-comment">;</span>
import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.yammer</span><span class="hljs-preprocessor">.metrics</span><span class="hljs-preprocessor">.core</span>.*<span class="hljs-comment">;</span>
import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.yammer</span><span class="hljs-preprocessor">.metrics</span><span class="hljs-preprocessor">.core</span><span class="hljs-preprocessor">.Timer</span><span class="hljs-comment">;</span>
import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.yammer</span><span class="hljs-preprocessor">.metrics</span><span class="hljs-preprocessor">.reporting</span><span class="hljs-preprocessor">.AbstractPollingReporter</span><span class="hljs-comment">;</span>
import <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.yammer</span><span class="hljs-preprocessor">.metrics</span><span class="hljs-preprocessor">.stats</span><span class="hljs-preprocessor">.Snapshot</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.instrumentation</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.tool</span><span class="hljs-preprocessor">.HostUtils</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.instrumentation</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.JMXPollUtil</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.influxdb</span><span class="hljs-preprocessor">.InfluxDB</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.influxdb</span><span class="hljs-preprocessor">.InfluxDBFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.influxdb</span><span class="hljs-preprocessor">.dto</span><span class="hljs-preprocessor">.BatchPoints</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.influxdb</span><span class="hljs-preprocessor">.dto</span><span class="hljs-preprocessor">.Point</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Logger</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.LoggerFactory</span><span class="hljs-comment">;</span>

import java<span class="hljs-preprocessor">.util</span>.*<span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.TimeUnit</span><span class="hljs-comment">;</span>


<span class="hljs-comment">/**
 * Created by shen.xiangxiang on 2017/4/20.
 */</span>

<span class="hljs-comment">/**
 * 继承 AbstractPollingReporter，实现influx输出格式及httpreport方式
 */</span>

public class InfluxReporter extends AbstractPollingReporter implements MetricProcessor&lt;InfluxReporter<span class="hljs-preprocessor">.Context</span>&gt; {



    <span class="hljs-comment">/**
     * 创建 enable
     * Enables the console reporter for the default metrics registry, and causes it to print to
     * influx with the specified period.
     *
     * @param period the period between successive outputs
     * @param unit   the time unit of {@code period}
     */</span>
    public static void enable(long period, String hostPort ,TimeUnit unit) {
        enable(Metrics<span class="hljs-preprocessor">.defaultRegistry</span>(),period, hostPort,unit)<span class="hljs-comment">;</span>
    }

    <span class="hljs-comment">/**
     * Enables the console reporter for the given metrics registry, and causes it to print to influx
     * with the specified period and unrestricted output.
     *
     * @param metricsRegistry the metrics registry
     * @param period          the period between successive outputs
     * @param unit            the time unit of {@code period}
     */</span>
    public static void enable(MetricsRegistry metricsRegistry, long period, String hostPort ,TimeUnit unit) {
        final InfluxReporter reporter = new InfluxReporter(metricsRegistry,
                hostPort,
                MetricPredicate<span class="hljs-preprocessor">.ALL</span>)<span class="hljs-comment">;</span>
        reporter<span class="hljs-preprocessor">.start</span>(period, unit)<span class="hljs-comment">;</span>
    }


    private static final Logger LOG = LoggerFactory<span class="hljs-preprocessor">.getLogger</span>(InfluxReporter<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

    private static final MetricPredicate DEFAULT_METRIC_PREDICATE = MetricPredicate<span class="hljs-preprocessor">.ALL</span><span class="hljs-comment">;</span>

    private BatchPoints batchPoints<span class="hljs-comment">;</span>

    //传入 host名
    private String hostport<span class="hljs-comment">;</span>

    //本地 host
    private String tag_host<span class="hljs-comment">;</span>

    private InfluxDB influxDBclient<span class="hljs-comment">;</span>

    private Clock clock<span class="hljs-comment">;</span>

    private final MetricPredicate predicate<span class="hljs-comment">;</span>

    private Context context<span class="hljs-comment">;</span>

    <span class="hljs-comment">/**
     *
     * @param hostPort,
     * 快捷构造 ，for the default metrics registry,
     */</span>
    public InfluxReporter(String hostPort) {
        this(Metrics<span class="hljs-preprocessor">.defaultRegistry</span>(), hostPort, MetricPredicate<span class="hljs-preprocessor">.ALL</span>)<span class="hljs-comment">;</span>
    }


    <span class="hljs-comment">/**
     * Creates a new {@link AbstractPollingReporter} instance.
     **/</span>
    public InfluxReporter(MetricsRegistry metricsRegistry, String hostPort, MetricPredicate predicate) {
        super(metricsRegistry, <span class="hljs-string">"influx-reporter"</span>)<span class="hljs-comment">;</span>
        this<span class="hljs-preprocessor">.hostport</span> = hostPort<span class="hljs-comment">;</span>
        this<span class="hljs-preprocessor">.influxDBclient</span> = InfluxDBFactory<span class="hljs-preprocessor">.connect</span>(hostPort)<span class="hljs-comment">;</span>
        this<span class="hljs-preprocessor">.clock</span> = Clock<span class="hljs-preprocessor">.defaultClock</span>()<span class="hljs-comment">;</span>
        this<span class="hljs-preprocessor">.tag</span>_host = HostUtils<span class="hljs-preprocessor">.getHostName</span>()<span class="hljs-comment">;</span>
        this<span class="hljs-preprocessor">.predicate</span> = predicate<span class="hljs-comment">;</span>
        this<span class="hljs-preprocessor">.context</span> = new Context() {
            @Override
            public long getTime() {
                return InfluxReporter<span class="hljs-preprocessor">.this</span><span class="hljs-preprocessor">.clock</span><span class="hljs-preprocessor">.time</span>()<span class="hljs-comment">;</span>
            }
        }<span class="hljs-comment">;</span>
    }


    @Override
    public void run() {
        try {
            // BatchPoints 构造参数  均没有用，随便填，因为数据最终以http协议发出
            this<span class="hljs-preprocessor">.batchPoints</span> = BatchPoints
                    <span class="hljs-preprocessor">.database</span>(<span class="hljs-string">"Metrics"</span>)
                    <span class="hljs-preprocessor">.retentionPolicy</span>(<span class="hljs-string">"autogen"</span>)
                    <span class="hljs-preprocessor">.consistency</span>(InfluxDB<span class="hljs-preprocessor">.ConsistencyLevel</span><span class="hljs-preprocessor">.ALL</span>)
                    <span class="hljs-preprocessor">.build</span>()<span class="hljs-comment">;</span>
            printRegularMetrics(context)<span class="hljs-comment">;</span>
            //TODO
            processFlumeCounter()<span class="hljs-comment">;</span>

            //TODO
//            LOG<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"batchPoints.lineProtocol():"</span> + batchPoints<span class="hljs-preprocessor">.lineProtocol</span>())<span class="hljs-comment">;</span>
            this<span class="hljs-preprocessor">.influxDBclient</span><span class="hljs-preprocessor">.write</span>(batchPoints)<span class="hljs-comment">;</span>
        } catch (Exception e) {
            LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"Cannot send metrics to InfluxDB {}"</span>, e)<span class="hljs-comment">;</span>
            LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"error batchPoints.lineProtocol():"</span> + batchPoints<span class="hljs-preprocessor">.lineProtocol</span>())<span class="hljs-comment">;</span>
        }
    }



    private void printRegularMetrics(final Context context) {

        //TODO
        LOG<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"getMetricsRegistry().groupedMetrics(DEFAULT_METRIC_PREDICATE).size():"</span> + getMetricsRegistry()<span class="hljs-preprocessor">.groupedMetrics</span>(DEFAULT_METRIC_PREDICATE)<span class="hljs-preprocessor">.size</span>())<span class="hljs-comment">;</span>

        for (Map<span class="hljs-preprocessor">.Entry</span>&lt;String, SortedMap&lt;MetricName, Metric&gt;&gt; entry : getMetricsRegistry()<span class="hljs-preprocessor">.groupedMetrics</span>(DEFAULT_METRIC_PREDICATE)<span class="hljs-preprocessor">.entrySet</span>()) {
            for (Map<span class="hljs-preprocessor">.Entry</span>&lt;MetricName, Metric&gt; subEntry : entry<span class="hljs-preprocessor">.getValue</span>()<span class="hljs-preprocessor">.entrySet</span>()) {
                final MetricName metricName = subEntry<span class="hljs-preprocessor">.getKey</span>()<span class="hljs-comment">;</span>
                final Metric metric = subEntry<span class="hljs-preprocessor">.getValue</span>()<span class="hljs-comment">;</span>
                if (predicate<span class="hljs-preprocessor">.matches</span>(metricName, metric)){
                    try {
                        metric<span class="hljs-preprocessor">.processWith</span>(this, subEntry<span class="hljs-preprocessor">.getKey</span>(), context)<span class="hljs-comment">;</span>
                    } catch (Exception ignored) {
                        LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"Error printing regular metrics:"</span>, ignored)<span class="hljs-comment">;</span>
                    }
                }
            }
        }
    }


    public void processGauge(MetricName name, Gauge&lt;?&gt; gauge, Context context) throws Exception {

        Point<span class="hljs-preprocessor">.Builder</span> pointbuilder = buildMetricsPoint(name, context)<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"metric_type"</span>, <span class="hljs-string">"gague"</span>)<span class="hljs-comment">;</span>

        Object fieldValue = gauge<span class="hljs-preprocessor">.value</span>()<span class="hljs-comment">;</span>
        String fieldName = <span class="hljs-string">"value"</span><span class="hljs-comment">;</span>
        // Long Interger 统一转Float型，以防止 schema冲突
        if( fieldValue instanceof Float)
            pointbuilder<span class="hljs-preprocessor">.addField</span>(fieldName, (Float)fieldValue)<span class="hljs-comment">;</span>
        else if( fieldValue instanceof Double)
            pointbuilder<span class="hljs-preprocessor">.addField</span>(fieldName, (Double)fieldValue)<span class="hljs-comment">;</span>
        else if( fieldValue instanceof Long)
            pointbuilder<span class="hljs-preprocessor">.addField</span>(fieldName, Float<span class="hljs-preprocessor">.valueOf</span>(((Long)fieldValue)<span class="hljs-preprocessor">.toString</span>()))<span class="hljs-comment">;</span>
        else if( fieldValue instanceof Integer)
            pointbuilder<span class="hljs-preprocessor">.addField</span>(fieldName, Float<span class="hljs-preprocessor">.valueOf</span>(((Integer)fieldValue)<span class="hljs-preprocessor">.toString</span>()))<span class="hljs-comment">;</span>
        else if( fieldValue instanceof String)
            pointbuilder<span class="hljs-preprocessor">.addField</span>(fieldName, (String)fieldValue)<span class="hljs-comment">;</span>
        else
            return<span class="hljs-comment">;</span>
        batchPoints<span class="hljs-preprocessor">.point</span>(pointbuilder<span class="hljs-preprocessor">.build</span>())<span class="hljs-comment">;</span>
    }

    @Override
    public void processCounter(MetricName metricName, Counter counter, Context context) throws Exception {

        Point<span class="hljs-preprocessor">.Builder</span> pointbuilder = buildMetricsPoint(metricName, context)<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"metric_type"</span>, <span class="hljs-string">"counter"</span>)<span class="hljs-comment">;</span>

        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"count"</span>, counter<span class="hljs-preprocessor">.count</span>())<span class="hljs-comment">;</span>
        batchPoints<span class="hljs-preprocessor">.point</span>(pointbuilder<span class="hljs-preprocessor">.build</span>())<span class="hljs-comment">;</span>

    }


    @Override
    public void processMeter(MetricName metricName, Metered meter, Context context) throws Exception {

        Point<span class="hljs-preprocessor">.Builder</span> pointbuilder = buildMetricsPoint(metricName, context)<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"metric_type"</span>, <span class="hljs-string">"meter"</span>)<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"eventType"</span>, meter<span class="hljs-preprocessor">.eventType</span>())<span class="hljs-comment">;</span>


        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"count"</span>, meter<span class="hljs-preprocessor">.count</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"meanRate"</span>, meter<span class="hljs-preprocessor">.meanRate</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"1MinuteRate"</span>, meter<span class="hljs-preprocessor">.oneMinuteRate</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"5MinuteRate"</span>, meter<span class="hljs-preprocessor">.fiveMinuteRate</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"15MinuteRate"</span>, meter<span class="hljs-preprocessor">.fifteenMinuteRate</span>())<span class="hljs-comment">;</span>


        batchPoints<span class="hljs-preprocessor">.point</span>(pointbuilder<span class="hljs-preprocessor">.build</span>())<span class="hljs-comment">;</span>

    }



    @Override
    public void processHistogram(MetricName metricName, Histogram histogram, Context context) throws Exception {
        final Snapshot snapshot = histogram<span class="hljs-preprocessor">.getSnapshot</span>()<span class="hljs-comment">;</span>

        Point<span class="hljs-preprocessor">.Builder</span> pointbuilder = buildMetricsPoint(metricName, context)<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"metric_type"</span>, <span class="hljs-string">"histogram"</span>)<span class="hljs-comment">;</span>

        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"max"</span>, histogram<span class="hljs-preprocessor">.max</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"mean"</span>, histogram<span class="hljs-preprocessor">.mean</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"min"</span>, histogram<span class="hljs-preprocessor">.min</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"stddev"</span>, histogram<span class="hljs-preprocessor">.max</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"sum"</span>, histogram<span class="hljs-preprocessor">.sum</span>())<span class="hljs-comment">;</span>

        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"median"</span>, snapshot<span class="hljs-preprocessor">.getMedian</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p75"</span>, snapshot<span class="hljs-preprocessor">.get</span>75thPercentile())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p95"</span>, snapshot<span class="hljs-preprocessor">.get</span>95thPercentile())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p98"</span>, snapshot<span class="hljs-preprocessor">.get</span>98thPercentile())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p99"</span>, snapshot<span class="hljs-preprocessor">.get</span>99thPercentile())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p999"</span>, snapshot<span class="hljs-preprocessor">.get</span>999thPercentile())<span class="hljs-comment">;</span>

        batchPoints<span class="hljs-preprocessor">.point</span>(pointbuilder<span class="hljs-preprocessor">.build</span>())<span class="hljs-comment">;</span>

    }

    public void processTimer(MetricName metricName, Timer timer, Context context) throws Exception {
        final Snapshot snapshot = timer<span class="hljs-preprocessor">.getSnapshot</span>()<span class="hljs-comment">;</span>

        Point<span class="hljs-preprocessor">.Builder</span> pointbuilder = buildMetricsPoint(metricName, context)<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"metric_type"</span>, <span class="hljs-string">"timer"</span>)<span class="hljs-comment">;</span>


        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"count"</span>, timer<span class="hljs-preprocessor">.count</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"meanRate"</span>, timer<span class="hljs-preprocessor">.meanRate</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"1MinuteRate"</span>, timer<span class="hljs-preprocessor">.oneMinuteRate</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"5MinuteRate"</span>, timer<span class="hljs-preprocessor">.fiveMinuteRate</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"15MinuteRate"</span>, timer<span class="hljs-preprocessor">.fifteenMinuteRate</span>())<span class="hljs-comment">;</span>


        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"max"</span>, timer<span class="hljs-preprocessor">.max</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"mean"</span>, timer<span class="hljs-preprocessor">.mean</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"min"</span>, timer<span class="hljs-preprocessor">.min</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"stddev"</span>, timer<span class="hljs-preprocessor">.max</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"sum"</span>, timer<span class="hljs-preprocessor">.sum</span>())<span class="hljs-comment">;</span>

        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"median"</span>, snapshot<span class="hljs-preprocessor">.getMedian</span>())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p75"</span>, snapshot<span class="hljs-preprocessor">.get</span>75thPercentile())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p95"</span>, snapshot<span class="hljs-preprocessor">.get</span>95thPercentile())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p98"</span>, snapshot<span class="hljs-preprocessor">.get</span>98thPercentile())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p99"</span>, snapshot<span class="hljs-preprocessor">.get</span>99thPercentile())<span class="hljs-comment">;</span>
        pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"p999"</span>, snapshot<span class="hljs-preprocessor">.get</span>999thPercentile())<span class="hljs-comment">;</span>




        batchPoints<span class="hljs-preprocessor">.point</span>(pointbuilder<span class="hljs-preprocessor">.build</span>())<span class="hljs-comment">;</span>
    }


    private Point<span class="hljs-preprocessor">.Builder</span> buildMetricsPoint(MetricName metricName, Context context) {

        //name要注意规范，加上前缀
        Point<span class="hljs-preprocessor">.Builder</span> pointbuilder = Point<span class="hljs-preprocessor">.measurement</span>(metricName<span class="hljs-preprocessor">.getName</span>())
                <span class="hljs-preprocessor">.time</span>(context<span class="hljs-preprocessor">.getTime</span>(), TimeUnit<span class="hljs-preprocessor">.MILLISECONDS</span>)
                <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"group"</span>, metricName<span class="hljs-preprocessor">.getGroup</span>())
                <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"type"</span>, metricName<span class="hljs-preprocessor">.getType</span>())
                //自动获取  host
                <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"host"</span>, tag_host)<span class="hljs-comment">;</span>


        //扩展区域
        if( metricName<span class="hljs-preprocessor">.hasScope</span>() ) {
            String scope = metricName<span class="hljs-preprocessor">.getScope</span>()<span class="hljs-comment">;</span>

            List&lt;String&gt; scopes = Arrays<span class="hljs-preprocessor">.asList</span>(scope<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">"\\."</span>))<span class="hljs-comment">;</span>
            if( scopes<span class="hljs-preprocessor">.size</span>() % <span class="hljs-number">2</span> == <span class="hljs-number">0</span>) {
                Iterator&lt;String&gt; iterator = scopes<span class="hljs-preprocessor">.iterator</span>()<span class="hljs-comment">;</span>
                while (iterator<span class="hljs-preprocessor">.hasNext</span>()) {
                    pointbuilder<span class="hljs-preprocessor">.tag</span>(iterator<span class="hljs-preprocessor">.next</span>(), iterator<span class="hljs-preprocessor">.next</span>())<span class="hljs-comment">;</span>
                }
            }
            else pointbuilder<span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"scope"</span>, scope)<span class="hljs-comment">;</span>
        }
        return pointbuilder<span class="hljs-comment">;</span>
    }


    public interface Context {

        long getTime()<span class="hljs-comment">;</span>
    }

    //TODO 定制化吐出flumecounter
    public void processFlumeCounter() {

        Map&lt;String, Map&lt;String, String&gt;&gt; metricsMap = JMXPollUtil
                <span class="hljs-preprocessor">.getAllMBeans</span>()<span class="hljs-comment">;</span>
        LOG<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"metricsMap:"</span> + metricsMap)<span class="hljs-comment">;</span>
        long timestamp = new Date()<span class="hljs-preprocessor">.getTime</span>()<span class="hljs-comment">;</span>

        //参数写死 因为其不起作用
//        BatchPoints batchPoints = BatchPoints
//                <span class="hljs-preprocessor">.database</span>(<span class="hljs-string">"FlumeMetrics"</span>)
//                <span class="hljs-preprocessor">.retentionPolicy</span>(<span class="hljs-string">"autogen"</span>)
//                <span class="hljs-preprocessor">.consistency</span>(InfluxDB<span class="hljs-preprocessor">.ConsistencyLevel</span><span class="hljs-preprocessor">.ALL</span>)
//                <span class="hljs-preprocessor">.build</span>()<span class="hljs-comment">;</span>

        try {
            for (String component : metricsMap<span class="hljs-preprocessor">.keySet</span>()) {
                Map&lt;String, String&gt; attributeMap = metricsMap
                        <span class="hljs-preprocessor">.get</span>(component)<span class="hljs-comment">;</span>
                String type = attributeMap<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"Type"</span>)<span class="hljs-comment">;</span>
                attributeMap<span class="hljs-preprocessor">.remove</span>(<span class="hljs-string">"Type"</span>)<span class="hljs-comment">;</span>
                attributeMap<span class="hljs-preprocessor">.remove</span>(<span class="hljs-string">"StopTime"</span>)<span class="hljs-comment">;</span>
                attributeMap<span class="hljs-preprocessor">.remove</span>(<span class="hljs-string">"StartTime"</span>)<span class="hljs-comment">;</span>
                attributeMap<span class="hljs-preprocessor">.remove</span>(<span class="hljs-string">"ChannelFillPercentage"</span>)<span class="hljs-comment">;</span>
                for (String attribute : attributeMap<span class="hljs-preprocessor">.keySet</span>()) {

                    //TODO
//                    LOG<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"attribute:"</span> + attribute)<span class="hljs-comment">;</span>
//                    LOG<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"componentname:"</span> + component<span class="hljs-preprocessor">.replace</span>(type + <span class="hljs-string">"."</span>, <span class="hljs-string">""</span>))<span class="hljs-comment">;</span>
//                    LOG<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"type:"</span> + type)<span class="hljs-comment">;</span>


                    Point<span class="hljs-preprocessor">.Builder</span> pointbuilder = Point<span class="hljs-preprocessor">.measurement</span>(<span class="hljs-string">"flume_counter"</span>)
                            <span class="hljs-preprocessor">.time</span>(timestamp, TimeUnit<span class="hljs-preprocessor">.MILLISECONDS</span>)
                            <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"name"</span>, attribute)
                            <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"host"</span>, tag_host)
                            <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"componentname"</span>, component<span class="hljs-preprocessor">.replace</span>(type + <span class="hljs-string">"."</span>, <span class="hljs-string">""</span>))
                            <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"type"</span>, type)
                            <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"group"</span>, <span class="hljs-string">"flume.agent"</span>)
                            <span class="hljs-preprocessor">.tag</span>(<span class="hljs-string">"metric_type"</span>, <span class="hljs-string">"counter"</span>)<span class="hljs-comment">;</span>

                    pointbuilder<span class="hljs-preprocessor">.addField</span>(<span class="hljs-string">"values"</span>, Long<span class="hljs-preprocessor">.valueOf</span>(attributeMap<span class="hljs-preprocessor">.get</span>(attribute)))<span class="hljs-comment">;</span>

                    batchPoints<span class="hljs-preprocessor">.point</span>(pointbuilder<span class="hljs-preprocessor">.build</span>())<span class="hljs-comment">;</span>

                }
            }
        }
        catch(IllegalArgumentException ille)
        {
            ille<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"error metricsMap:"</span> + metricsMap)<span class="hljs-comment">;</span>
            LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"error:"</span> + ille)<span class="hljs-comment">;</span>
            LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"ille.getMessage():"</span> + ille<span class="hljs-preprocessor">.getMessage</span>())<span class="hljs-comment">;</span>
        }
        catch(Exception e)
        {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"error metricsMap:"</span> + metricsMap)<span class="hljs-comment">;</span>
            LOG<span class="hljs-preprocessor">.error</span>(<span class="hljs-string">"e.getMessage():"</span> + e<span class="hljs-preprocessor">.getMessage</span>())<span class="hljs-comment">;</span>
        }


//        influxDBclient<span class="hljs-preprocessor">.write</span>(batchPoints)<span class="hljs-comment">;</span>
        LOG<span class="hljs-preprocessor">.info</span>(<span class="hljs-string">"InfluxMetricsServer run onetime"</span>)<span class="hljs-comment">;</span>

    }

}
</code></pre>



<h3 id="总结">总结</h3>

<p>此处演示了如何增强flume metrics测量的功能，有了测量之后还需要report出去。 <br>
主要有三段sample代码，分享给读者及同仁。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
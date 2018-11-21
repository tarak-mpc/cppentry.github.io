---
layout:     post
title:      Flume event转化为ES的document过程分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>使用更改后的MultiExecSource + MemoryChannel +　ElasticsearchSink 将flume收集到的日志信息发送到es集群中。</p>

<!-- more -->

<p><br></p>



<h4 id="一条flume-event的构成"><strong>一条flume event的构成</strong></h4>

<p>一个flume event由 header + body 构成：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
 * Basic representation of a data object in Flume.
 * Provides access to data as it flows through the system.
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">Event</span> {</span>

  <span class="hljs-javadoc">/**
   * Returns a map of name-value pairs describing the data stored in the body.
   */</span>
  <span class="hljs-keyword">public</span> Map&lt;String, String&gt; <span class="hljs-title">getHeaders</span>();

  <span class="hljs-javadoc">/**
   * Set the event headers
   *<span class="hljs-javadoctag"> @param</span> headers Map of headers to replace the current headers.
   */</span>
  <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setHeaders</span>(Map&lt;String, String&gt; headers);

  <span class="hljs-javadoc">/**
   * Returns the raw byte array of the data contained in this event.
   */</span>
  <span class="hljs-keyword">public</span> <span class="hljs-keyword">byte</span>[] <span class="hljs-title">getBody</span>();

  <span class="hljs-javadoc">/**
   * Sets the raw byte array of the data contained in this event.
   *<span class="hljs-javadoctag"> @param</span> body The data.
   */</span>
  <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setBody</span>(<span class="hljs-keyword">byte</span>[] body);

}</code></pre>

<p>如果使用Source使用ExecSource读取文件，那么默认body就是每一行的内容，headers可以通过interceptor来添加。</p>

<p><br></p>



<h4 id="一条es-document的构成"><strong>一条es document的构成</strong></h4>

<p>比如，使用ElasticsearchSink，往es里导入数据，es中的索引信息如下：</p>



<pre class="prettyprint"><code class=" hljs perl">[root<span class="hljs-variable">@10</span> /data/home/ambari]<span class="hljs-comment">#   curl -XGET 'http://localhost:9200/log-2016-04-20/_mapping/log?pretty'             </span>
{
  <span class="hljs-string">"log-2016-04-20"</span> : {
    <span class="hljs-string">"mappings"</span> : {
      <span class="hljs-string">"log"</span> : {
        <span class="hljs-string">"properties"</span> : {
          <span class="hljs-string">"<span class="hljs-variable">@message</span>"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"string"</span>
          },
          <span class="hljs-string">"<span class="hljs-variable">@timestamp</span>"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"date"</span>,
            <span class="hljs-string">"format"</span> : <span class="hljs-string">"dateOptionalTime"</span>
          },
          <span class="hljs-string">"component"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"string"</span>
          },
          <span class="hljs-string">"content"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"string"</span>
          },
          <span class="hljs-string">"fileName"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"string"</span>
          },
          <span class="hljs-string">"hostname"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"string"</span>
          },
          <span class="hljs-string">"level"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"string"</span>
          },
          <span class="hljs-string">"offset"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"long"</span>
          },
          <span class="hljs-string">"service"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"string"</span>
          },
          <span class="hljs-string">"timestamps"</span> : {
            <span class="hljs-string">"type"</span> : <span class="hljs-string">"string"</span>
          }
        }
      }
    }
  }
}</code></pre>

<p>这里面，<code>@message</code>是flume event的<code>body</code>，其余的field是flume event的<code>headers</code>中的每一个header构建而来。</p>

<p><br></p>



<h4 id="转化过程"><strong>转化过程</strong></h4>

<p>构建过程在<code>ElasticSearchLogStashEventSerializer.java</code>中可以找到：</p>



<pre class="prettyprint"><code class=" hljs cs">  @Override
  <span class="hljs-keyword">public</span> XContentBuilder <span class="hljs-title">getContentBuilder</span>(Event <span class="hljs-keyword">event</span>) throws IOException {
    XContentBuilder builder = jsonBuilder().startObject();
    appendBody(builder, <span class="hljs-keyword">event</span>);
    appendHeaders(builder, <span class="hljs-keyword">event</span>);
    <span class="hljs-keyword">return</span> builder;
  }</code></pre>

<p>可以看到，<code>appendBody</code>是把event的body加到es里的document中，<code>appendHeaders</code>是把event的headers加到es里的document中，默认一个flume event对应一个es document。</p>

<p>source的配置如下（有6个interceptor，其中的一个interceptor又产生了3个header）：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># source1:hdfs_datanode_src ----------</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.type</span> = <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.pku</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.MultiLineExecSource</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.command</span> = tail -F /var/log/hdfs/hdfs/hadoop-hdfs-datanode-<span class="hljs-number">192.168</span><span class="hljs-number">.37</span><span class="hljs-number">.3</span><span class="hljs-preprocessor">.log</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.channels</span> = memoryChannel
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.restart</span> = true
<span class="hljs-preprocessor"># interceptor</span>
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span> = i1 i2 i3 i4 i5 i6
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.type</span> = host
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.useIP</span> = true
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.hostHeader</span> = hostname
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>1<span class="hljs-preprocessor">.preserveExisting</span> = false
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>2<span class="hljs-preprocessor">.type</span> = static
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>2<span class="hljs-preprocessor">.key</span> = service
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>2<span class="hljs-preprocessor">.value</span> = hdfs
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>3<span class="hljs-preprocessor">.type</span> = static
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>3<span class="hljs-preprocessor">.key</span> = component
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>3<span class="hljs-preprocessor">.value</span> = datanode
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>4<span class="hljs-preprocessor">.type</span> = timestamp

agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>5<span class="hljs-preprocessor">.type</span> = regex_extractor
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>5<span class="hljs-preprocessor">.regex</span> = ^(?:\\n)?(.+)\\s(INFO|ERROR|WARN|DEBUG|FATAL)\\s(.+)$
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>5<span class="hljs-preprocessor">.serializers</span> = s1 s2 s3
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>5<span class="hljs-preprocessor">.serializers</span><span class="hljs-preprocessor">.s</span>1<span class="hljs-preprocessor">.name</span> = timestamps
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>5<span class="hljs-preprocessor">.serializers</span><span class="hljs-preprocessor">.s</span>2<span class="hljs-preprocessor">.name</span> = level
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>5<span class="hljs-preprocessor">.serializers</span><span class="hljs-preprocessor">.s</span>3<span class="hljs-preprocessor">.name</span> = content

agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>6<span class="hljs-preprocessor">.type</span> = static
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>6<span class="hljs-preprocessor">.key</span> = fileName
agent<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.hdfs</span>_datanode_src<span class="hljs-preprocessor">.interceptors</span><span class="hljs-preprocessor">.i</span>6<span class="hljs-preprocessor">.value</span> = hadoop-hdfs-datanode-<span class="hljs-number">192.168</span><span class="hljs-number">.37</span><span class="hljs-number">.3</span><span class="hljs-preprocessor">.log</span></code></pre>

<p>一个flume event转化为es的一个document的对应关系如下：</p>

<p>flume body:</p>



<pre class="prettyprint"><code class=" hljs d"><span class="hljs-keyword">body</span> -&gt; <span class="hljs-keyword">@message</span></code></pre>

<p>flume headers:</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hostname<span class="hljs-function"><span class="hljs-params">(interceptor加入的)</span> -&gt;</span> hostname(类型string)
service<span class="hljs-function"><span class="hljs-params">(interceptor加入的)</span> -&gt;</span> service(类型string)
component<span class="hljs-function"><span class="hljs-params">(interceptor加入的)</span> -&gt;</span> component(类型string)
timestamp<span class="hljs-function"><span class="hljs-params">(interceptor加入的)</span> -&gt;</span> <span class="hljs-property">@timestamp</span>(类型date)
timestamps<span class="hljs-function"><span class="hljs-params">(interceptor加入的)</span> -&gt;</span> timestamps(类型string)
level<span class="hljs-function"><span class="hljs-params">(interceptor加入的)</span> -&gt;</span> level(类型string)
content<span class="hljs-function"><span class="hljs-params">(interceptor加入的)</span> -&gt;</span> content(类型string)
fileName<span class="hljs-function"><span class="hljs-params">(interceptor加入的)</span> -&gt;</span> fileName(类型string)
offset<span class="hljs-function"><span class="hljs-params">(在MultiLineExecSource的run过程加入的)</span> -&gt;</span> offset(类型long)</code></pre>

<p><br></p>



<h4 id="遇到的错误"><strong>遇到的错误</strong></h4>

<p>实际测试过程中遇到过这样一类错误：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-number">19</span> Apr <span class="hljs-number">2016</span> <span class="hljs-number">13</span>:<span class="hljs-number">33</span>:<span class="hljs-number">00</span>,<span class="hljs-number">734</span> ERROR [SinkRunner-PollingRunner-DefaultSinkProcessor] (org.apache.flume.SinkRunner$PollingRunner.run:<span class="hljs-number">160</span>)  - Unable <span class="hljs-built_in">to</span> deliver event. Exception follows.
org.apache.flume.EventDeliveryException: Failed <span class="hljs-built_in">to</span> commit transaction. Transaction rolled back.
        <span class="hljs-keyword">at</span> org.apache.flume.sink.elasticsearch.ElasticSearchSink.<span class="hljs-built_in">process</span>(ElasticSearchSink.java:<span class="hljs-number">227</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.sink.DefaultSinkProcessor.<span class="hljs-built_in">process</span>(DefaultSinkProcessor.java:<span class="hljs-number">68</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.SinkRunner$PollingRunner.run(SinkRunner.java:<span class="hljs-number">147</span>)
        <span class="hljs-keyword">at</span> java.lang.Thread.run(Thread.java:<span class="hljs-number">745</span>)
Caused <span class="hljs-keyword">by</span>: org.elasticsearch.common.jackson.core.JsonParseException: Unrecognized <span class="hljs-keyword">token</span> <span class="hljs-string">'Topic'</span>: was expecting (<span class="hljs-string">'true'</span>, <span class="hljs-string">'false'</span> <span class="hljs-operator">or</span> <span class="hljs-string">'null'</span>)
 <span class="hljs-keyword">at</span> [Source: [B@<span class="hljs-number">6e1</span>faec2; <span class="hljs-built_in">line</span>: <span class="hljs-number">1</span>, column: <span class="hljs-number">7</span>]
        <span class="hljs-keyword">at</span> org.elasticsearch.common.jackson.core.JsonParser.<span class="hljs-title">_construct</span>Error(JsonParser.java:<span class="hljs-number">1487</span>)
        <span class="hljs-keyword">at</span> org.elasticsearch.common.jackson.core.base.ParserMinimalBase.<span class="hljs-title">_report</span>Error(ParserMinimalBase.java:<span class="hljs-number">518</span>)
        <span class="hljs-keyword">at</span> org.elasticsearch.common.jackson.core.json.UTF8StreamJsonParser.<span class="hljs-title">_report</span>InvalidToken(UTF8StreamJsonParser.java:<span class="hljs-number">3323</span>)
        <span class="hljs-keyword">at</span> org.elasticsearch.common.jackson.core.json.UTF8StreamJsonParser.<span class="hljs-title">_handle</span>UnexpectedValue(UTF8StreamJsonParser.java:<span class="hljs-number">2482</span>)
        <span class="hljs-keyword">at</span> org.elasticsearch.common.jackson.core.json.UTF8StreamJsonParser.<span class="hljs-title">_next</span>TokenNotInObject(UTF8StreamJsonParser.java:<span class="hljs-number">801</span>)
        <span class="hljs-keyword">at</span> org.elasticsearch.common.jackson.core.json.UTF8StreamJsonParser.nextToken(UTF8StreamJsonParser.java:<span class="hljs-number">697</span>)
        <span class="hljs-keyword">at</span> org.elasticsearch.common.xcontent.json.JsonXContentParser.nextToken(JsonXContentParser.java:<span class="hljs-number">51</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.sink.elasticsearch.ContentBuilderUtil.addComplexField(ContentBuilderUtil.java:<span class="hljs-number">60</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.sink.elasticsearch.ContentBuilderUtil.appendField(ContentBuilderUtil.java:<span class="hljs-number">47</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.sink.elasticsearch.ElasticSearchLogStashEventSerializer.appendHeaders(ElasticSearchLogStashEventSerializer.java:<span class="hljs-number">162</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.sink.elasticsearch.ElasticSearchLogStashEventSerializer.getContentBuilder(ElasticSearchLogStashEventSerializer.java:<span class="hljs-number">92</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.sink.elasticsearch.ElasticSearchLogStashEventSerializer.getContentBuilder(ElasticSearchLogStashEventSerializer.java:<span class="hljs-number">76</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.sink.elasticsearch.client.ElasticSearchTransportClient.addEvent(ElasticSearchTransportClient.java:<span class="hljs-number">166</span>)
        <span class="hljs-keyword">at</span> org.apache.flume.sink.elasticsearch.ElasticSearchSink.<span class="hljs-built_in">process</span>(ElasticSearchSink.java:<span class="hljs-number">189</span>)
        ... <span class="hljs-number">3</span> more</code></pre>

<p>BUG出现的原因：sink出现没有被捕获，导致sink无法正常运行</p>

<p>BUG会导致的问题：这样会导致sink一直不能消费channel中的event，channel中的event因此一直积压发不出去，导致内存占用过高，一直做GC。</p>

<p>Flume社区有这个issue的解决办法： <br>
<a href="https://issues.apache.org/jira/browse/FLUME-2769" rel="nofollow">https://issues.apache.org/jira/browse/FLUME-2769</a></p>

<p>需要修改flume1.6.0源码，目前看来是flume的一个bug，修复办法： <br>
<a href="https://github.com/agentgt/flume/commit/09089257df239eeac942ef64b2d24c68efb5bec7" rel="nofollow">https://github.com/agentgt/flume/commit/09089257df239eeac942ef64b2d24c68efb5bec7</a></p>

<p>即出现异常时捕获该异常，强制结束该event的相应header转换为document的一个field的过程。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
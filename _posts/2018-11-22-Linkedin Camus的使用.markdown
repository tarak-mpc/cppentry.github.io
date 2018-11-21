---
layout:     post
title:      Linkedin Camus的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="wmd-preview-section-2818" class="wmd-preview-section preview-content" style="color:rgb(44,63,81);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:16px;line-height:24px;">
<h2 id="preface" style="font-family:inherit;line-height:1.1;color:inherit;font-size:2.15em;">
Preface</h2>
<p>在实现Lambda架构的时候，我以Kafka作为系统的输入，同时需要将数据批量从Kafka导入到HDFS存储起来，以备Batch layer批处理计算。</p>
<p>而从Kafka到HDFS的数据传输，Linkedin已经有一个开源的项目，即Linkedin Camus。Camus是Kafka到HDFS的管道，它实际上是向Hadoop提交一个作业，并从Kafka获取指定topic的消息，存储到HDFS中。</p>
<p>实际上在使用Camus的时候，只有3件事是我们关注的。</p>
<ol><li>确定MessageDecoder</li><li>确定RecordWriterProvider</li><li>确定Kafka话题</li></ol></div>
<div id="wmd-preview-section-2819" class="wmd-preview-section preview-content" style="color:rgb(44,63,81);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:16px;line-height:24px;">
<h2 id="messagedecoder" style="font-family:inherit;line-height:1.1;color:inherit;font-size:2.15em;">
MessageDecoder</h2>
<p><span>MessageDecoder</span> 是对Kafka的消息进行解析的解析器，比如Camus自带了几个Decoder：KafkaAvroMessageDecoder，JsonStringMessageDecoder等。 <br>
我们也可以自行设计符合自己业务需求的Decoder，而消息解析后的内容，会作为输出后期被存储到HDFS（当然还会加上压缩）</p>
<p>我在系统中实现了一个简单的字符串解析器，基本保持消息原文，也就是我只是要直接存储消息原文即可：</p>
<pre class="prettyprint hljs-dark" style="font-family:'Source Code Pro', monospace;font-size:.9em;line-height:1.45;color:rgb(51,51,51);border:0px;background:rgb(246,246,246);"><code class="hljs scala" style="font-family:'Source Code Pro', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);"><span class="hljs-keyword" style="color:rgb(249,38,114);">package</span> com.linkedin.camus.etl.kafka.coders;

<span class="hljs-keyword" style="color:rgb(249,38,114);">import</span> com.linkedin.camus.coders.<span class="hljs-type">CamusWrapper</span>;
<span class="hljs-keyword" style="color:rgb(249,38,114);">import</span> com.linkedin.camus.coders.<span class="hljs-type">MessageDecoder</span>;
<span class="hljs-keyword" style="color:rgb(249,38,114);">import</span> org.apache.log4j.<span class="hljs-type">Logger</span>;
<span class="hljs-keyword" style="color:rgb(249,38,114);">import</span> java.util.<span class="hljs-type">Properties</span>;


<span class="hljs-comment" style="color:rgb(117,113,94);">/**
 * MessageDecoder class that will convert the payload into a String object,
 * System.currentTimeMillis() will be used to set CamusWrapper's
 * timestamp property

 * This MessageDecoder returns a CamusWrapper that works with Strings payloads,
 */</span>
public <span class="hljs-class"><span class="hljs-keyword" style="color:rgb(102,217,239);">class</span> <span class="hljs-title">StringMessageDecoder</span> <span class="hljs-keyword" style="color:rgb(249,38,114);"><span class="hljs-keyword" style="color:rgb(102,217,239);">extends</span></span> <span class="hljs-title" style="color:rgb(166,226,46);font-style:italic;">MessageDecoder&lt;byte</span>[</span>], <span class="hljs-type">String</span>&gt; {
  <span class="hljs-keyword" style="color:rgb(249,38,114);">private</span> static <span class="hljs-keyword" style="color:rgb(249,38,114);">final</span> <span class="hljs-type">Logger</span> log = <span class="hljs-type">Logger</span>.getLogger(<span class="hljs-type">StringMessageDecoder</span>.<span class="hljs-keyword" style="color:rgb(249,38,114);">class</span>);

  <span class="hljs-annotation">@Override</span>
  public void init(<span class="hljs-type">Properties</span> props, <span class="hljs-type">String</span> topicName) {
    <span class="hljs-keyword" style="color:rgb(249,38,114);">this</span>.props = props;
    <span class="hljs-keyword" style="color:rgb(249,38,114);">this</span>.topicName = topicName;
  }

  <span class="hljs-annotation">@Override</span>
  public <span class="hljs-type">CamusWrapper</span>&lt;<span class="hljs-type">String</span>&gt; decode(byte[] payload) {
    long timestamp = <span class="hljs-number" style="color:rgb(174,129,255);">0</span>;
    <span class="hljs-type">String</span> payloadString;

    payloadString = <span class="hljs-keyword" style="color:rgb(249,38,114);">new</span> <span class="hljs-type">String</span>(payload);
    timestamp = <span class="hljs-type">System</span>.currentTimeMillis();

    <span class="hljs-keyword" style="color:rgb(249,38,114);">return</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">new</span> <span class="hljs-type">CamusWrapper</span>&lt;<span class="hljs-type">String</span>&gt;(payloadString, timestamp);
  }
}
</code></pre>
<p>简单说一下，该类继承于<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">com.linkedin.camus.coders.MessageDecoder</code>，输入的类型为<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">byte[]</code>，解析后的类型为<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">String</code>。重点是实现<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">decode</code>方法，返回一个<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">CamusWrapper</code>对象，该对象包含了解析后的数据。</p>
</div>
<div id="wmd-preview-section-2820" class="wmd-preview-section preview-content" style="color:rgb(44,63,81);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:16px;line-height:24px;">
<h2 id="recordwriterprovider" style="font-family:inherit;line-height:1.1;color:inherit;font-size:2.15em;">
RecordWriterProvider</h2>
<p>相比MessageDecoder在Camus的输入端发挥作用，RecordWriterProvider则在输出端发挥作用，确定Camus如何把数据写到HDFS中。</p>
<p>Camus同样有实现好的，比如<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">AvroRecordWriterProvider</code>，这也是Camus默认的，因为Camus默认是支持Avro格式，而我只需要纯文本即可，所以我使用的是同样Camus自带的<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">com.linkedin.camus.etl.kafka.common.StringRecordWriterProvider</code>，代码在Camus源码中可以找到。</p>
<p>既然是RecordWriter的Provider，其核心功能当然是<span>提供一个RecordWriter</span>，这是Hadoop的一个类，规定了Job如何输出到文件系统的，只要实现其中的<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">write</code>方法即可。</p>
<p>另外，关于输出还有一个压缩的问题，比如在<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">StringRecordWriterProvider</code>中根据配置文件确定是否压缩以及压缩算法，并确定输出时的行为，部分代码如下：</p>
<pre class="prettyprint hljs-dark" style="font-family:'Source Code Pro', monospace;font-size:.9em;line-height:1.45;color:rgb(51,51,51);border:0px;background:rgb(246,246,246);"><code class="hljs nix" style="font-family:'Source Code Pro', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);"><span class="hljs-variable">isCompressed =</span> FileOutputFormat.getCompressOutput(context);

<span class="hljs-keyword" style="color:rgb(249,38,114);">if</span> (isCompressed) {
  Class&lt;? extends CompressionCodec&gt; <span class="hljs-variable">codecClass =</span> <span class="hljs-constant" style="color:rgb(102,217,239);">null</span>;
  <span class="hljs-keyword" style="color:rgb(249,38,114);">if</span> (<span class="hljs-string" style="color:rgb(230,219,116);">"snappy"</span>.equals(EtlMultiOutputFormat.getEtlOutputCodec(context))) {
    <span class="hljs-variable">codecClass =</span> SnappyCodec.class;
  } <span class="hljs-keyword" style="color:rgb(249,38,114);">else</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">if</span> (<span class="hljs-string" style="color:rgb(230,219,116);">"gzip"</span>.equals((EtlMultiOutputFormat.getEtlOutputCodec(context)))) {
    <span class="hljs-variable">codecClass =</span> GzipCodec.class;
  } <span class="hljs-keyword" style="color:rgb(249,38,114);">else</span> {
    <span class="hljs-variable">codecClass =</span> DefaultCodec.class;
  }
  <span class="hljs-variable">codec =</span> ReflectionUtils.newInstance(codecClass, conf);
  <span class="hljs-variable">extension =</span> codec.getDefaultExtension();
}
</code></pre>
<p>以及：</p>
<pre class="prettyprint hljs-dark" style="font-family:'Source Code Pro', monospace;font-size:.9em;line-height:1.45;color:rgb(51,51,51);border:0px;background:rgb(246,246,246);"><code class="hljs actionscript" style="font-family:'Source Code Pro', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">FileSystem fs = path.getFileSystem(context.getConfiguration());
<span class="hljs-keyword" style="color:rgb(249,38,114);">if</span> (!isCompressed) {
  FSDataOutputStream fileOut = fs.create(path, <span class="hljs-literal" style="color:rgb(174,129,255);">false</span>);
  <span class="hljs-keyword" style="color:rgb(249,38,114);">return</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">new</span> ByteRecordWriter(fileOut, recordDelimiter);
} <span class="hljs-keyword" style="color:rgb(249,38,114);">else</span> {
  FSDataOutputStream fileOut = fs.create(path, <span class="hljs-literal" style="color:rgb(174,129,255);">false</span>);
  <span class="hljs-keyword" style="color:rgb(249,38,114);">return</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">new</span> ByteRecordWriter(<span class="hljs-keyword" style="color:rgb(249,38,114);">new</span> DataOutputStream(codec.createOutputStream(fileOut)), recordDelimiter);
}
</code></pre>
</div>
<div id="wmd-preview-section-2821" class="wmd-preview-section preview-content" style="color:rgb(44,63,81);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:16px;line-height:24px;">
<h2 id="配置" style="font-family:inherit;line-height:1.1;color:inherit;font-size:2.15em;">
配置</h2>
<p>以上说到的内容，都是通过配置文件在构建的时候整合到camus中。</p>
<p><code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">camus.message.decoder.class</code> 用来配置MessageDecoder</p>
<p><code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">etl.record.writer.provider.class</code> 用来配置RecordWriterProvider</p>
<p>关于压缩，可以参照下面配置：</p>
<pre class="prettyprint hljs-dark" style="font-family:'Source Code Pro', monospace;font-size:.9em;line-height:1.45;color:rgb(51,51,51);border:0px;background:rgb(246,246,246);"><code class="hljs stylus" style="font-family:'Source Code Pro', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">mapred<span class="hljs-class">.output</span><span class="hljs-class">.compress</span>=true     #告诉Hadoop需不需要压缩
etl<span class="hljs-class">.output</span><span class="hljs-class">.codec</span>=deflate        #配置压缩算法为deflate
etl<span class="hljs-class">.deflate</span><span class="hljs-class">.level</span>=<span class="hljs-number" style="color:rgb(174,129,255);">6</span>
</code></pre>
<p>另外，配置一些HDFS路径</p>
<p><code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">etl.destination.path</code> 是文件最终输出的文件夹 <br><code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">etl.execution.base.path</code> 和 <code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">etl.execution.history.path</code>分别是Camus运行信息临时存放和已完成的Job的运行信息存放的地方</p>
<p><code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">kafka.whitelist.topics</code>和<code style="font-family:'Source Code Pro', monospace;font-size:.9em;color:rgb(199,37,78);background-color:rgb(249,242,244);">kafka.blacklist.topics</code>分别是Kafka话题的白名单和黑名单，将要订阅的topics以逗号分隔的形式赋值给whitelist即可。</p>
</div>
            </div>
                </div>
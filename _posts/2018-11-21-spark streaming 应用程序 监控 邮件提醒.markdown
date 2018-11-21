---
layout:     post
title:      spark streaming 应用程序 监控 邮件提醒
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>spark streaming应用程序，放到线上后，怎么监测spark streaming程序的阻塞状态， <br>
虽然spark 提供了spark webUI去查看，但是作为开发人员总不能天天去看spark webUI页面吧， <br>
去官网看，貌似可以通过请求spark 自带的jetty 服务器可以获取相关检测统计信息,</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-symbol">http:</span>/<span class="hljs-regexp">/host:8088/proxy</span><span class="hljs-regexp">/application_1517299288666_7058/streaming</span><span class="hljs-regexp">/</span></code></pre>

<p>返回的数据是要html页面，可以通过正则去解析需要的信息；但是这样子很不方便，能不能在spark streaming 发生阻塞的时候给我发送邮件提醒甚至是钉钉提醒呢？</p>

<p>这个方法比较笨，有没有更好的方法呢？跟踪spark StreamingContext源码：</p>

<p>spark streaming  StreamingJobProgressListener 监控器</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">private</span> val nextInputStreamId = <span class="hljs-keyword">new</span> AtomicInteger(<span class="hljs-number">0</span>)

  <span class="hljs-keyword">private</span>[streaming] <span class="hljs-keyword">var</span> checkpointDir: String = {
    <span class="hljs-keyword">if</span> (isCheckpointPresent) {
      sc.setCheckpointDir(cp_.checkpointDir)
      cp_.checkpointDir
    } <span class="hljs-keyword">else</span> {
      <span class="hljs-keyword">null</span>
    }
  }

  <span class="hljs-keyword">private</span>[streaming] val checkpointDuration: Duration = {
    <span class="hljs-keyword">if</span> (isCheckpointPresent) cp_.checkpointDuration <span class="hljs-keyword">else</span> graph.batchDuration
  }

  <span class="hljs-keyword">private</span>[streaming] val scheduler = <span class="hljs-keyword">new</span> JobScheduler(<span class="hljs-keyword">this</span>)

  <span class="hljs-keyword">private</span>[streaming] val waiter = <span class="hljs-keyword">new</span> ContextWaiter

  <span class="hljs-keyword">private</span>[streaming] val progressListener = <span class="hljs-keyword">new</span> StreamingJobProgressListener(<span class="hljs-keyword">this</span>)

  <span class="hljs-keyword">private</span>[streaming] val uiTab: Option[StreamingTab] =
    <span class="hljs-keyword">if</span> (conf.getBoolean(<span class="hljs-string">"spark.ui.enabled"</span>, <span class="hljs-keyword">true</span>)) {
      Some(<span class="hljs-keyword">new</span> StreamingTab(<span class="hljs-keyword">this</span>))
    } <span class="hljs-keyword">else</span> {
      None
    }
</code></pre>

<p>StreamingJobProgressListener实现：</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">private</span>[streaming] <span class="hljs-keyword">class</span> <span class="hljs-title">StreamingJobProgressListener</span>(ssc: StreamingContext)
  extends StreamingListener with SparkListener {

  <span class="hljs-keyword">private</span> val waitingBatchUIData = <span class="hljs-keyword">new</span> HashMap[Time, BatchUIData]
  <span class="hljs-keyword">private</span> val runningBatchUIData = <span class="hljs-keyword">new</span> HashMap[Time, BatchUIData]
  <span class="hljs-keyword">private</span> val completedBatchUIData = <span class="hljs-keyword">new</span> Queue[BatchUIData]
  <span class="hljs-keyword">private</span> val batchUIDataLimit = ssc.conf.getInt(<span class="hljs-string">"spark.streaming.ui.retainedBatches"</span>, <span class="hljs-number">1000</span>)
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> totalCompletedBatches = <span class="hljs-number">0</span>L
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> totalReceivedRecords = <span class="hljs-number">0</span>L
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">var</span> totalProcessedRecords = <span class="hljs-number">0</span>L
  <span class="hljs-keyword">private</span> val receiverInfos = <span class="hljs-keyword">new</span> HashMap[Int, ReceiverInfo]
  ......</code></pre>

<p>然后我自己实现了一个Listener类，当发生阻塞的时候，可以发送邮件，以下实现比较简单</p>



<pre class="prettyprint"><code class=" hljs scala">
<span class="hljs-keyword">import</span> org.apache.spark.streaming.scheduler._
<span class="hljs-keyword">import</span> streaming.test.email.EmailSender
<span class="hljs-keyword">import</span> org.slf4j._


<span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">BJJListener</span><span class="hljs-params">(private val appName:String, private val duration: Int)</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">StreamingListener</span>{</span>

  <span class="hljs-keyword">private</span> <span class="hljs-keyword">val</span> logger = LoggerFactory.getLogger(<span class="hljs-string">"BJJListener"</span>)

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> onReceiverStarted(receiverStarted: StreamingListenerReceiverStarted): Unit = {
    <span class="hljs-keyword">super</span>.onReceiverStarted(receiverStarted)
  }

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> onReceiverError(receiverError: StreamingListenerReceiverError): Unit = <span class="hljs-keyword">super</span>.onReceiverError(receiverError)

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> onReceiverStopped(receiverStopped: StreamingListenerReceiverStopped): Unit = <span class="hljs-keyword">super</span>.onReceiverStopped(receiverStopped)

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> onBatchSubmitted(batchSubmitted: StreamingListenerBatchSubmitted): Unit = {
    <span class="hljs-keyword">super</span>.onBatchSubmitted(batchSubmitted)
    <span class="hljs-keyword">val</span> batchInfo = batchSubmitted.batchInfo
    <span class="hljs-keyword">val</span> batchTime = batchInfo.batchTime
    logger.info(<span class="hljs-string">"BJJListener  batchTime : "</span>, batchTime)
  }

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> onBatchStarted(batchStarted: StreamingListenerBatchStarted): Unit = {
    <span class="hljs-keyword">val</span> batchInfo = batchStarted.batchInfo
    <span class="hljs-keyword">val</span> processingStartTime = batchInfo.processingStartTime
    logger.info(<span class="hljs-string">"BJJListener  processingStartTime : "</span>, processingStartTime)
  }

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> onBatchCompleted(batchCompleted: StreamingListenerBatchCompleted): Unit = {
    <span class="hljs-keyword">val</span> batchInfo = batchCompleted.batchInfo
    <span class="hljs-keyword">val</span> processingStartTime = batchCompleted.batchInfo.processingStartTime

    <span class="hljs-keyword">val</span> processingEndTime = batchInfo.processingEndTime
    <span class="hljs-keyword">val</span> processingDelay = batchInfo.processingDelay
    <span class="hljs-keyword">val</span> totalDelay = batchInfo.totalDelay

    <span class="hljs-keyword">if</span>(totalDelay.get &gt;= <span class="hljs-number">6</span> * duration * <span class="hljs-number">1000</span> &amp;&amp; totalDelay.get &gt;= <span class="hljs-number">10</span> * duration * <span class="hljs-number">1000</span>){
      <span class="hljs-keyword">val</span> monitorTitle = s<span class="hljs-string">"spark streaming $appName 程序阻塞异常警告"</span>
      <span class="hljs-keyword">val</span> monitorContent = s<span class="hljs-string">"BJJListener : processingStartTime -&gt; ${processingStartTime.get}, processingEndTime -&gt; ${processingEndTime.get} , "</span> +
        s<span class="hljs-string">"processingDelay -&gt; ${processingDelay.get} , totalDelay -&gt; ${totalDelay.get}, 请及时检查！"</span>
      EmailSender.sendMail(monitorTitle, monitorContent)
    }
    logger.info(<span class="hljs-string">"BJJListener  processingEndTime : "</span>, processingEndTime)
    logger.info(<span class="hljs-string">"BJJListener  processingDelay : "</span>, processingDelay)
    logger.info(<span class="hljs-string">"BJJListener  totalDelay : "</span>, totalDelay)
  }

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> onOutputOperationStarted(outputOperationStarted: StreamingListenerOutputOperationStarted): Unit =
    <span class="hljs-keyword">super</span>.onOutputOperationStarted(outputOperationStarted)

  <span class="hljs-keyword">override</span> <span class="hljs-keyword">def</span> onOutputOperationCompleted(outputOperationCompleted: StreamingListenerOutputOperationCompleted): Unit =
    <span class="hljs-keyword">super</span>.onOutputOperationCompleted(outputOperationCompleted)

}

</code></pre>

<p>通过可插拔的方式添加自己实现的listener</p>



<pre class="prettyprint"><code class=" hljs cs">    ssc.addStreamingListener(<span class="hljs-keyword">new</span> BJJListener(appName, <span class="hljs-number">10</span>))
</code></pre>

<p>//spark streaming程序测试的例子：</p>



<pre class="prettyprint"><code class=" hljs avrasm">import kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.StringDecoder</span>
import org<span class="hljs-preprocessor">.ansj</span><span class="hljs-preprocessor">.splitWord</span><span class="hljs-preprocessor">.analysis</span><span class="hljs-preprocessor">.NlpAnalysis</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j.{Level, Logger}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span>.{SparkConf, SparkContext}
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.KafkaUtils</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.streaming</span>.{Seconds, StreamingContext}

<span class="hljs-comment">/**
  * Created by Amy on 2018/2/2.
  */</span>
object test {
  def main(args: Array[String]): Unit = {

    System<span class="hljs-preprocessor">.setProperty</span>(<span class="hljs-string">"hadoop.home.dir"</span>, <span class="hljs-string">"D:\\mcyarn\\hadoop-common-2.2.0-bin-master"</span>)
    Logger<span class="hljs-preprocessor">.getLogger</span>(<span class="hljs-string">"org.apache.spark"</span>)<span class="hljs-preprocessor">.setLevel</span>(Level<span class="hljs-preprocessor">.ERROR</span>)
    Logger<span class="hljs-preprocessor">.getLogger</span>(<span class="hljs-string">"org.eclipse.jetty.server"</span>)<span class="hljs-preprocessor">.setLevel</span>(Level<span class="hljs-preprocessor">.OFF</span>)
    val appName = <span class="hljs-string">"spark Steaming test"</span>
    val conf = new SparkConf()<span class="hljs-preprocessor">.setMaster</span>(<span class="hljs-string">"local[2]"</span>)<span class="hljs-preprocessor">.setAppName</span>(<span class="hljs-string">"test"</span>)
    val sc = new SparkContext(conf)
    val ssc = new StreamingContext(sc, Seconds(<span class="hljs-number">10</span>))

    val brokerList = <span class="hljs-string">"localhost:9092"</span>
    val zookeeperConnect = <span class="hljs-string">"localhost:2181"</span>
    val groupId = <span class="hljs-string">"baasdf20180302"</span>
    val newsTopic = <span class="hljs-string">"test"</span>

    val kafkaParams = Map(<span class="hljs-string">"metadata.broker.list"</span> -&gt; brokerList, <span class="hljs-string">"group.id"</span> -&gt; groupId,
      <span class="hljs-string">"zookeeper.connect"</span>-&gt;zookeeperConnect,
      <span class="hljs-string">"auto.offset.reset"</span> -&gt; kafka<span class="hljs-preprocessor">.api</span><span class="hljs-preprocessor">.OffsetRequest</span><span class="hljs-preprocessor">.LargestTimeString</span>)

    val kafkaStream = KafkaUtils<span class="hljs-preprocessor">.createDirectStream</span>[String,String,StringDecoder, StringDecoder](ssc, kafkaParams,
      topics = <span class="hljs-keyword">Set</span>(newsTopic))<span class="hljs-preprocessor">.map</span>(_._1)

    kafkaStream<span class="hljs-preprocessor">.foreachRDD</span>(rdd=&gt;{
      if(!rdd<span class="hljs-preprocessor">.isEmpty</span>()){

        val rdds = rdd<span class="hljs-preprocessor">.union</span>(rdd)<span class="hljs-preprocessor">.union</span>(rdd)<span class="hljs-preprocessor">.union</span>(rdd)
        val transform = rdds<span class="hljs-preprocessor">.map</span>(news=&gt;{
          if(news!=null){
            val split = NlpAnalysis<span class="hljs-preprocessor">.parse</span>(news)<span class="hljs-preprocessor">.toStringWithOutNature</span>(<span class="hljs-string">" "</span>)
            split
          }else{
            null
          }
        })

        val wordCount = transform<span class="hljs-preprocessor">.map</span>(word=&gt;(word, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_+_)
        wordCount<span class="hljs-preprocessor">.foreach</span>(println)
        println(rdd<span class="hljs-preprocessor">.count</span>())
      }
    })

    ssc<span class="hljs-preprocessor">.addStreamingListener</span>(new BJJListener(appName, <span class="hljs-number">10</span>))

    ssc<span class="hljs-preprocessor">.start</span>()
    ssc<span class="hljs-preprocessor">.awaitTermination</span>()

  }
}</code></pre>

<p>阻塞到什么程序发送邮件，可以自己决定，可以发送邮件，也可以发送钉钉等，比较方便。</p>



<h2 id="emailsendinfo-类">EmailSendInfo 类</h2>



<pre class="prettyprint"><code class=" hljs http">

<span class="java"><span class="hljs-keyword">import</span> java.util.Properties;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">EmailSendInfo</span> {</span>

    <span class="hljs-comment">// 发送邮件的服务器的IP和端口</span>
    <span class="hljs-keyword">private</span> String mailServerHost;
    <span class="hljs-keyword">private</span> String mailServerPort = <span class="hljs-string">"25"</span>;
    <span class="hljs-comment">// 邮件发送者的地址</span>
    <span class="hljs-keyword">private</span> String fromAddress;
    <span class="hljs-comment">// 邮件接收者的地址</span>
    <span class="hljs-keyword">private</span> String toAddress;
    <span class="hljs-comment">// 登陆邮件发送服务器的用户名和密码</span>
    <span class="hljs-keyword">private</span> String userName;
    <span class="hljs-keyword">private</span> String password;
    <span class="hljs-comment">// 是否需要身份验证</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">boolean</span> validate = <span class="hljs-keyword">false</span>;
    <span class="hljs-comment">// 邮件主题</span>
    <span class="hljs-keyword">private</span> String subject;
    <span class="hljs-comment">// 邮件的文本内容</span>
    <span class="hljs-keyword">private</span> String content;
    <span class="hljs-comment">// 邮件附件的文件名</span>
    <span class="hljs-keyword">private</span> String[] attachFileNames;

    <span class="hljs-javadoc">/**
     * 获得邮件会话属性
     */</span>
    <span class="hljs-keyword">public</span> Properties <span class="hljs-title">getProperties</span>() {
        Properties p = <span class="hljs-keyword">new</span> Properties();
        p.put(<span class="hljs-string">"mail.smtp.host"</span>, <span class="hljs-keyword">this</span>.mailServerHost);
        p.put(<span class="hljs-string">"mail.smtp.port"</span>, <span class="hljs-keyword">this</span>.mailServerPort);
        p.put(<span class="hljs-string">"mail.smtp.auth"</span>, validate ? <span class="hljs-string">"true"</span> : <span class="hljs-string">"false"</span>);
        <span class="hljs-keyword">return</span> p;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getMailServerHost</span>() {
        <span class="hljs-keyword">return</span> mailServerHost;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> mailServerHost
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setMailServerHost</span>(String mailServerHost) {
        <span class="hljs-keyword">this</span>.mailServerHost = mailServerHost;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getMailServerPort</span>() {
        <span class="hljs-keyword">return</span> mailServerPort;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> mailServerPort
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setMailServerPort</span>(String mailServerPort) {
        <span class="hljs-keyword">this</span>.mailServerPort = mailServerPort;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">isValidate</span>() {
        <span class="hljs-keyword">return</span> validate;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> validate
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setValidate</span>(<span class="hljs-keyword">boolean</span> validate) {
        <span class="hljs-keyword">this</span>.validate = validate;
    }

    <span class="hljs-keyword">public</span> String[] <span class="hljs-title">getAttachFileNames</span>() {
        <span class="hljs-keyword">return</span> attachFileNames;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> fileNames
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setAttachFileNames</span>(String[] fileNames) {
        <span class="hljs-keyword">this</span>.attachFileNames = fileNames;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getFromAddress</span>() {
        <span class="hljs-keyword">return</span> fromAddress;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> fromAddress
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setFromAddress</span>(String fromAddress) {
        <span class="hljs-keyword">this</span>.fromAddress = fromAddress;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getPassword</span>() {
        <span class="hljs-keyword">return</span> password;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> password
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setPassword</span>(String password) {
        <span class="hljs-keyword">this</span>.password = password;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getToAddress</span>() {
        <span class="hljs-keyword">return</span> toAddress;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> toAddress
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setToAddress</span>(String toAddress) {
        <span class="hljs-keyword">this</span>.toAddress = toAddress;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getUserName</span>() {
        <span class="hljs-keyword">return</span> userName;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> userName
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setUserName</span>(String userName) {
        <span class="hljs-keyword">this</span>.userName = userName;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getSubject</span>() {
        <span class="hljs-keyword">return</span> subject;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> subject
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setSubject</span>(String subject) {
        <span class="hljs-keyword">this</span>.subject = subject;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getContent</span>() {
        <span class="hljs-keyword">return</span> content;
    }

    <span class="hljs-javadoc">/**
     *
     *<span class="hljs-javadoctag"> @param</span> textContent
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setContent</span>(String textContent) {
        <span class="hljs-keyword">this</span>.content = textContent;
    }
}
</span></code></pre>



<h2 id="emailauthenticator-类">EmailAuthenticator 类</h2>



<pre class="prettyprint"><code class=" hljs java">
<span class="hljs-keyword">import</span> javax.mail.*;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">EmailAuthenticator</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">Authenticator</span>{</span>

    <span class="hljs-keyword">private</span> String userName;
    <span class="hljs-keyword">private</span> String password;

    <span class="hljs-keyword">public</span> <span class="hljs-title">EmailAuthenticator</span>(){}

    <span class="hljs-keyword">public</span> <span class="hljs-title">EmailAuthenticator</span>(String username, String password) {
        <span class="hljs-keyword">this</span>.userName = username;
        <span class="hljs-keyword">this</span>.password = password;
    }

    <span class="hljs-keyword">protected</span> PasswordAuthentication <span class="hljs-title">getPasswordAuthentication</span>(){
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> PasswordAuthentication(userName, password);
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getUserName</span>() {
        <span class="hljs-keyword">return</span> userName;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setUserName</span>(String userName) {
        <span class="hljs-keyword">this</span>.userName = userName;
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getPassword</span>() {
        <span class="hljs-keyword">return</span> password;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setPassword</span>(String password) {
        <span class="hljs-keyword">this</span>.password = password;
    }

}

</code></pre>



<h2 id="emailsender">EmailSender</h2>



<pre class="prettyprint"><code class=" hljs lasso">
<span class="hljs-keyword">import</span> javax<span class="hljs-built_in">.</span>mail<span class="hljs-built_in">.</span><span class="hljs-subst">*</span>;
<span class="hljs-keyword">import</span> javax<span class="hljs-built_in">.</span>mail<span class="hljs-built_in">.</span>internet<span class="hljs-built_in">.</span>InternetAddress;
<span class="hljs-keyword">import</span> javax<span class="hljs-built_in">.</span>mail<span class="hljs-built_in">.</span>internet<span class="hljs-built_in">.</span>MimeMessage;
<span class="hljs-keyword">import</span> java<span class="hljs-built_in">.</span>util<span class="hljs-built_in">.</span><span class="hljs-built_in">Date</span>;
<span class="hljs-keyword">import</span> java<span class="hljs-built_in">.</span>util<span class="hljs-built_in">.</span>Properties;

<span class="hljs-keyword">public</span> class EmailSender {

    <span class="hljs-keyword">private</span> static boolean sendTextMail(EmailSendInfo mailInfo) {
        boolean sendStatus <span class="hljs-subst">=</span> <span class="hljs-literal">false</span>;<span class="hljs-comment">//发送状态</span>
        <span class="hljs-comment">// 判断是否需要身份认证</span>
        EmailAuthenticator authenticator <span class="hljs-subst">=</span> <span class="hljs-built_in">null</span>;
        Properties pro <span class="hljs-subst">=</span> mailInfo<span class="hljs-built_in">.</span>getProperties();
        <span class="hljs-keyword">if</span> (mailInfo<span class="hljs-built_in">.</span>isValidate()) {
            <span class="hljs-comment">// 如果需要身份认证，则创建一个密码验证器</span>
            authenticator <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> EmailAuthenticator(mailInfo<span class="hljs-built_in">.</span>getUserName(), mailInfo<span class="hljs-built_in">.</span>getPassword());
        }
        <span class="hljs-comment">// 根据邮件会话属性和密码验证器构造一个发送邮件的session</span>
        Session sendMailSession <span class="hljs-subst">=</span> Session<span class="hljs-built_in">.</span>getInstance(pro, authenticator);
        <span class="hljs-comment">//【调试时使用】开启Session的debug模式</span>
        sendMailSession<span class="hljs-built_in">.</span>setDebug(<span class="hljs-literal">true</span>);
        try {
            <span class="hljs-comment">// 根据session创建一个邮件消息</span>
            MimeMessage mailMessage <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> MimeMessage(sendMailSession);
            <span class="hljs-comment">// 创建邮件发送者地址</span>
            Address from <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> InternetAddress(mailInfo<span class="hljs-built_in">.</span>getFromAddress());
            <span class="hljs-comment">// 设置邮件消息的发送者</span>
            mailMessage<span class="hljs-built_in">.</span>setFrom(from);
            <span class="hljs-comment">// 创建邮件的接收者地址，并设置到邮件消息中</span>
            Address <span class="hljs-keyword">to</span> <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> InternetAddress(mailInfo<span class="hljs-built_in">.</span>getToAddress());
            mailMessage<span class="hljs-built_in">.</span>setRecipient(Message<span class="hljs-built_in">.</span>RecipientType<span class="hljs-built_in">.</span><span class="hljs-keyword">TO</span>,<span class="hljs-keyword">to</span>);
            <span class="hljs-comment">// 设置邮件消息的主题</span>
            mailMessage<span class="hljs-built_in">.</span>setSubject(mailInfo<span class="hljs-built_in">.</span>getSubject(), <span class="hljs-string">"UTF-8"</span>);
            <span class="hljs-comment">// 设置邮件消息发送的时间</span>
            mailMessage<span class="hljs-built_in">.</span>setSentDate(<span class="hljs-literal">new</span> <span class="hljs-built_in">Date</span>());
            <span class="hljs-comment">// 设置邮件消息的主要内容</span>
            <span class="hljs-built_in">String</span> mailContent <span class="hljs-subst">=</span> mailInfo<span class="hljs-built_in">.</span>getContent();
            mailMessage<span class="hljs-built_in">.</span>setText(mailContent, <span class="hljs-string">"UTF-8"</span>);

            <span class="hljs-comment">// 发送邮件</span>
            Transport<span class="hljs-built_in">.</span>send(mailMessage);
            sendStatus <span class="hljs-subst">=</span> <span class="hljs-literal">true</span>;
        } catch (MessagingException ex) {
            ex<span class="hljs-built_in">.</span>printStackTrace();
        }
        <span class="hljs-keyword">return</span> sendStatus;
    }


    <span class="hljs-keyword">public</span> static <span class="hljs-literal">void</span> sendMail(<span class="hljs-built_in">String</span> monitorTitle, <span class="hljs-built_in">String</span> monitorContent){
        <span class="hljs-built_in">String</span> fromaddr <span class="hljs-subst">=</span> <span class="hljs-string">"xxxx@yqzbw.com"</span>;
        <span class="hljs-built_in">String</span> toaddr <span class="hljs-subst">=</span> <span class="hljs-string">"xxxx@yqzbw.com"</span>;
        <span class="hljs-built_in">String</span> port <span class="hljs-subst">=</span> <span class="hljs-string">"25"</span>;
        <span class="hljs-built_in">String</span> host <span class="hljs-subst">=</span> <span class="hljs-string">"smtp.exmail.qq.com"</span>;
        <span class="hljs-built_in">String</span> userName <span class="hljs-subst">=</span> <span class="hljs-string">"xxxxxg@yqzbw.com"</span>;
        <span class="hljs-built_in">String</span> password <span class="hljs-subst">=</span> <span class="hljs-string">"12345678"</span>;

        EmailSendInfo mailInfo <span class="hljs-subst">=</span> <span class="hljs-literal">new</span> EmailSendInfo();
        mailInfo<span class="hljs-built_in">.</span>setMailServerHost(host);
        mailInfo<span class="hljs-built_in">.</span>setValidate(<span class="hljs-literal">true</span>);
        mailInfo<span class="hljs-built_in">.</span>setUserName(userName);
        mailInfo<span class="hljs-built_in">.</span>setPassword(password);
        mailInfo<span class="hljs-built_in">.</span>setFromAddress(fromaddr);
        mailInfo<span class="hljs-built_in">.</span>setToAddress(toaddr);
        mailInfo<span class="hljs-built_in">.</span>setSubject(monitorTitle);
        mailInfo<span class="hljs-built_in">.</span>setContent(monitorContent);
        <span class="hljs-comment">//发送文体格式邮件</span>
        EmailSender<span class="hljs-built_in">.</span>sendTextMail(mailInfo);
    }

}
</code></pre>

<p>以上实现比较简单，有牛逼的大神，补充后，可以私信我</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
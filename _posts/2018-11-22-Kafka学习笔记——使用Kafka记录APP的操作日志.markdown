---
layout:     post
title:      Kafka学习笔记——使用Kafka记录APP的操作日志
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>上一篇文章我们讲到了Kafka的工作原理和如何使用Kafka的代码示例，这里我们开始讲解Kafka的实战，在实际的应用中我们如何使用kafka的。下面将介绍前台的操作日志定时推送到kafka，然后通过kafka将消息日志进行保存，方便大数据的统计分析形成运营报表。 <br>
我们先看看工程的目录结构： <br>
<img src="https://img-blog.csdn.net/20170610232147750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZnV5dXdlaTIwMTU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
 kafka的版本是：</p>



<pre class="prettyprint"><code class=" hljs xml">       <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>kafka_2.10<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.10.2.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
       <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>下面我们依次看下代码实现：</p>

<h1 id="错误码字典类codeconstantjava">错误码字典类CodeConstant.java</h1>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> CodeConstant {
    <span class="hljs-comment">// 参数为空</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">int</span> NULL_ERROR = -<span class="hljs-number">1</span>;

    <span class="hljs-comment">// 请求参数错误</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">int</span> PARAM_ERROR = -<span class="hljs-number">2</span>;

    <span class="hljs-comment">// token错误</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">int</span> TOKEN_ERROR = -<span class="hljs-number">3</span>;


}</code></pre>

<h1 id="返回信息实体类jsonmsgjava">返回信息实体类JsonMsg.java</h1>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> JsonMsg {
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> code;

    <span class="hljs-keyword">private</span> String message;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> <span class="hljs-title">getCode</span>() {
        <span class="hljs-keyword">return</span> code;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setCode</span>(<span class="hljs-keyword">int</span> code) {
        <span class="hljs-keyword">this</span>.code = code;
    }
    <span class="hljs-keyword">public</span> String <span class="hljs-title">getMessage</span>() {
        <span class="hljs-keyword">return</span> message;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setMessage</span>(String message) {
        <span class="hljs-keyword">this</span>.message = message;
    }


}</code></pre>

<h1 id="kafka消息实体类messagejava">kafka消息实体类Message.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
 * kafka消息实体类
 *<span class="hljs-javadoctag"> @author</span> fuyuwei
 * 2017年6月10日 下午10:57:17
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Message</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Serializable</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = -<span class="hljs-number">6170235919490993626</span>L;
    <span class="hljs-javadoc">/**
     * 消息主键
     */</span>
    <span class="hljs-keyword">protected</span> String messageId;
    <span class="hljs-javadoc">/**
     * 回复消息对应的源消息主键
     */</span>
    <span class="hljs-keyword">protected</span> String sourceMessageId;
    <span class="hljs-javadoc">/**
     * 发送消息相关信息
     */</span>
    <span class="hljs-keyword">protected</span> String  sender;
    <span class="hljs-javadoc">/**
     * 消息体
     */</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">byte</span>[]  messageBody;
    <span class="hljs-javadoc">/**
     * 消息创建时间
     */</span>
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">long</span> createTime;

    <span class="hljs-keyword">public</span> <span class="hljs-title">Message</span>(<span class="hljs-keyword">byte</span>[] messageBody){
        <span class="hljs-keyword">this</span>.sender = getIps();
        createMessageId();
        <span class="hljs-keyword">this</span>.messageBody = messageBody;
        <span class="hljs-keyword">this</span>.createTime = System.currentTimeMillis();
    }

    <span class="hljs-keyword">public</span> String <span class="hljs-title">getIps</span>(){
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">return</span> InetAddress.getLocalHost().getHostAddress();
        } <span class="hljs-keyword">catch</span> (UnknownHostException e) {
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> <span class="hljs-string">""</span>;
    }

    <span class="hljs-javadoc">/**
     * 消息转为在消息中间件传输的内容
     *<span class="hljs-javadoctag"> @return</span> 
     *<span class="hljs-javadoctag"> @throws</span> BusinessException
     */</span>
    <span class="hljs-keyword">public</span> String <span class="hljs-title">toJSONString</span>() <span class="hljs-keyword">throws</span> BusinessException {
        createMessageId();
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">return</span> JsonUtil.toJSon(<span class="hljs-keyword">this</span>);
        } <span class="hljs-keyword">catch</span> (BusinessException e) {
            <span class="hljs-keyword">throw</span> e;
        }
    }
    <span class="hljs-javadoc">/**
     * 接收到的消息转为实体对象
     *<span class="hljs-javadoctag"> @param</span> content 消息内容
     *<span class="hljs-javadoctag"> @return</span> 消息实体
     *<span class="hljs-javadoctag"> @throws</span> BusinessException
     */</span>
    <span class="hljs-keyword">public</span> Message <span class="hljs-title">toMessage</span>(String content) <span class="hljs-keyword">throws</span> BusinessException{
        <span class="hljs-keyword">return</span> JsonUtil.readValue(content, Message.class);
    }
    <span class="hljs-keyword">public</span> String <span class="hljs-title">toString</span>(){
        String date =<span class="hljs-keyword">null</span>;      
        <span class="hljs-keyword">try</span> {
            SimpleDateFormat sdf=<span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyyy-MM-dd HH:mm:ss"</span>); 
            date = sdf.format(<span class="hljs-keyword">new</span> Date(createTime));
        } <span class="hljs-keyword">catch</span> (Exception e) {         

        } 
        StringBuffer sb = <span class="hljs-keyword">new</span> StringBuffer();
        sb.append(<span class="hljs-string">"messageId:"</span>+<span class="hljs-keyword">this</span>.messageId+<span class="hljs-string">"\r\n"</span>).append(<span class="hljs-string">"sourceMessageId:"</span>+<span class="hljs-keyword">this</span>.messageId+<span class="hljs-string">"\r\n"</span>)
          .append(<span class="hljs-string">"sender:"</span>+sender+<span class="hljs-string">"\r\n"</span>).append(<span class="hljs-string">"messageBody"</span>+messageBody+<span class="hljs-string">"\r\n"</span>)
          .append(<span class="hljs-string">"createTime="</span>+date+<span class="hljs-string">"\r\n"</span>);
        <span class="hljs-keyword">return</span> sb.toString();

    }
    <span class="hljs-keyword">public</span> String <span class="hljs-title">getMessageId</span>() {
        <span class="hljs-keyword">return</span> messageId;
    }
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createMessageId</span>() {        
        <span class="hljs-keyword">this</span>.messageId = sender+createUUID();
    }
    <span class="hljs-keyword">private</span> String <span class="hljs-title">createUUID</span>(){
        String id = UUID.randomUUID().toString();       
        <span class="hljs-keyword">return</span> id.substring(<span class="hljs-number">0</span>,<span class="hljs-number">8</span>)+id.substring(<span class="hljs-number">9</span>,<span class="hljs-number">13</span>)+id.substring(<span class="hljs-number">14</span>,<span class="hljs-number">18</span>)+id.substring(<span class="hljs-number">19</span>,<span class="hljs-number">23</span>)+id.substring(<span class="hljs-number">24</span>); <span class="hljs-comment">//去掉“-”符号 </span>
    }   
    <span class="hljs-keyword">public</span> String <span class="hljs-title">getSender</span>() {
        <span class="hljs-keyword">return</span> sender;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setSender</span>(String sender) {
        <span class="hljs-keyword">this</span>.sender = sender;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">long</span> <span class="hljs-title">getCreateTime</span>() {
        <span class="hljs-keyword">return</span> createTime;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setCreateTime</span>(<span class="hljs-keyword">long</span> createTime) {
        <span class="hljs-keyword">this</span>.createTime = createTime;
    }
    <span class="hljs-keyword">public</span> String <span class="hljs-title">getSourceMessageId</span>() {
        <span class="hljs-keyword">return</span> sourceMessageId;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setSourceMessageId</span>(String sourceMessageId) {
        <span class="hljs-keyword">this</span>.sourceMessageId = sourceMessageId;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">byte</span>[] <span class="hljs-title">getMessageBody</span>() {
        <span class="hljs-keyword">return</span> messageBody;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setMessageBody</span>(<span class="hljs-keyword">byte</span>[] messageBody) {
        <span class="hljs-keyword">this</span>.messageBody = messageBody;
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">setMessageId</span>(String messageId) {
        <span class="hljs-keyword">this</span>.messageId = messageId;
    }

}</code></pre>

<h1 id="http访问类kafkalogcontrollerjava">Http访问类KafkaLogController.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
 * kafka log接收器
 *<span class="hljs-javadoctag"> @author</span> fuyuwei
 * 2017年6月10日 下午8:00:07
 */</span>
<span class="hljs-annotation">@Controller</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">KafkaLogController</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> MessageProducer producer = MessageProducer.getInstance();

    <span class="hljs-javadoc">/**
     * 接收前台传来的日志字符串
     * 既然采用Http协议请求，务必考虑传输的安全性，添加了请求的参数拦截校验
     *<span class="hljs-javadoctag"> @author</span> fuyuwei
     * 2017年6月10日 下午8:01:36
     *<span class="hljs-javadoctag"> @param</span> req
     *<span class="hljs-javadoctag"> @param</span> resp
     *<span class="hljs-javadoctag"> @throws</span> Throwable
     */</span>
    <span class="hljs-annotation">@RequestMapping</span>(value = <span class="hljs-string">"/kafka/log/receiveLog.do"</span>, method= RequestMethod.POST)
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">receiveLog</span>(HttpServletRequest req, HttpServletResponse resp) <span class="hljs-keyword">throws</span> Throwable{
        ServletInputStream is = req.getInputStream();
        <span class="hljs-keyword">byte</span>[] bytes = readStream(is);
        <span class="hljs-keyword">if</span>(bytes == <span class="hljs-keyword">null</span> || bytes.length == <span class="hljs-number">0</span>){
            JsonMsg msg = <span class="hljs-keyword">new</span> JsonMsg();
            msg.setCode(CodeConstant.NULL_ERROR);
            msg.setMessage(<span class="hljs-string">"the request data is null"</span>);
            <span class="hljs-comment">// 不设置缓存</span>
            RespUtil.responJson(resp, msg, <span class="hljs-number">0</span>);
            <span class="hljs-keyword">return</span>;
        }
        Message message = <span class="hljs-keyword">new</span> Message(bytes);
        producer.sendMessage(<span class="hljs-string">"appLog"</span>, message);
        BizLogger.info(<span class="hljs-string">"receiveLog"</span>,<span class="hljs-string">"appLog"</span>,message.getMessageId());
    }

    <span class="hljs-javadoc">/**
     * 把日志字符串转换为字节流数组
     *<span class="hljs-javadoctag"> @author</span> fuyuwei
     * 2017年6月10日 下午8:05:20
     *<span class="hljs-javadoctag"> @param</span> inStream
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">byte</span>[] <span class="hljs-title">readStream</span>(InputStream inStream){
        ByteArrayOutputStream outStream = <span class="hljs-keyword">new</span> ByteArrayOutputStream();
        BufferedInputStream inputStream = <span class="hljs-keyword">new</span> BufferedInputStream(inStream);
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">byte</span>[] buffer = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">1024</span>];
            <span class="hljs-keyword">int</span> len = -<span class="hljs-number">1</span>;
            <span class="hljs-keyword">while</span>((len = inputStream.read(buffer)) != -<span class="hljs-number">1</span>){
                outStream.write(buffer,<span class="hljs-number">0</span>,len);
            }
            <span class="hljs-keyword">return</span> outStream.toByteArray();
        } <span class="hljs-keyword">catch</span> (IOException e) {
            BizLogger.error(e, <span class="hljs-string">"inputStream.read failure..."</span>);
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
    }

}</code></pre>

<h1 id="spring启动加载类initmessageconsumerjava">Spring启动加载类InitMessageConsumer.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">InitMessageConsumer</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">InitializingBean</span>, <span class="hljs-title">DisposableBean</span> {</span>
    <span class="hljs-keyword">public</span> MessageConsumer consumer;
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">destroy</span>() <span class="hljs-keyword">throws</span> Exception {
    }
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">afterPropertiesSet</span>() <span class="hljs-keyword">throws</span> Exception {
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">initMethod</span>() {
        BizLogger.info(<span class="hljs-string">"init MessageReceiver start"</span>);
        consumer = <span class="hljs-keyword">new</span> MessageConsumer(<span class="hljs-string">"appLog"</span>, <span class="hljs-number">2</span>,<span class="hljs-string">"app-group"</span>, <span class="hljs-keyword">new</span> MessageConsumerExecutor());
        <span class="hljs-keyword">try</span> {
            consumer.receiveMessage();
        } <span class="hljs-keyword">catch</span> (Exception e) {
            BizLogger.error(e, <span class="hljs-string">"InitAndDestroySeqBean initMethod"</span>);
        }
        BizLogger.info(<span class="hljs-string">"MessageReceiver init finish!"</span>);
    }
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">destroyMethod</span>() {
        <span class="hljs-keyword">if</span> (<span class="hljs-keyword">null</span> != consumer) {
            consumer.close();
        }
    }
}</code></pre>

<h1 id="拦截器accessinteceptorjava">拦截器AccessInteceptor.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">AccessInteceptor</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">HandlerInterceptor</span> {</span>
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">afterCompletion</span>(HttpServletRequest req,
            HttpServletResponse res, Object o, Exception e) <span class="hljs-keyword">throws</span> Exception {
    }
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">postHandle</span>(HttpServletRequest req, HttpServletResponse res,
            Object o, ModelAndView m) <span class="hljs-keyword">throws</span> Exception {
    }
    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">preHandle</span>(HttpServletRequest req, HttpServletResponse res,
            Object o) <span class="hljs-keyword">throws</span> Exception {
        String flagImmei = req.getHeader(<span class="hljs-string">"flagImmei"</span>);
        String tk = req.getHeader(<span class="hljs-string">"token"</span>);
        <span class="hljs-keyword">if</span>(flagImmei.length() &gt; <span class="hljs-number">40</span>){
            JsonMsg msg = <span class="hljs-keyword">new</span> JsonMsg();
            msg.setCode(CodeConstant.PARAM_ERROR);
            msg.setMessage(<span class="hljs-string">"the request data is null"</span>);
            <span class="hljs-comment">// 不设置缓存</span>
            RespUtil.responJson(res, msg, <span class="hljs-number">0</span>);
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">if</span>(!AppAESUtil.check(tk, flagImmei)){
            JsonMsg msg = <span class="hljs-keyword">new</span> JsonMsg();
            msg.setCode(CodeConstant.TOKEN_ERROR);
            msg.setMessage(<span class="hljs-string">"the token is error"</span>);
            RespUtil.responJson(res, msg, <span class="hljs-number">0</span>);
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
    }
}</code></pre>

<h1 id="消息生产者messageproducerjava">消息生产者MessageProducer.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MessageProducer</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">MessageService</span> {</span>
    <span class="hljs-keyword">private</span> Producer&lt;String, <span class="hljs-keyword">byte</span>[]&gt; producer;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> MessageProducer instance = <span class="hljs-keyword">null</span>;
    <span class="hljs-javadoc">/**
     * 初始化生产者
     */</span>
    <span class="hljs-keyword">private</span> <span class="hljs-title">MessageProducer</span>() {
        <span class="hljs-keyword">try</span> {
            Properties properties = <span class="hljs-keyword">new</span> Properties();
            properties.load(<span class="hljs-keyword">new</span> ClassPathResource(<span class="hljs-string">"producer.properties"</span>).getInputStream());
            producer = <span class="hljs-keyword">new</span> KafkaProducer&lt;&gt;(properties);
        } <span class="hljs-keyword">catch</span> (IOException e) {
            BizLogger.error(e, <span class="hljs-string">"load producer file fail!"</span>);
        }
    }
    <span class="hljs-javadoc">/**
     * 单例模式
     *<span class="hljs-javadoctag"> @author</span> fuyuwei
     * 2017年6月10日 下午8:44:05
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">synchronized</span> MessageProducer <span class="hljs-title">getInstance</span>() {
        <span class="hljs-keyword">if</span>(instance == <span class="hljs-keyword">null</span>){
            <span class="hljs-keyword">synchronized</span>(MessageProducer.class){
                <span class="hljs-keyword">if</span>(instance == <span class="hljs-keyword">null</span>){
                    instance = <span class="hljs-keyword">new</span> MessageProducer();
                }
            }
        }
        <span class="hljs-keyword">return</span> instance;
    }
    <span class="hljs-javadoc">/**
     * 发送消息
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">sendMessage</span>(String topic, Message message) <span class="hljs-keyword">throws</span> Exception {
        Collection&lt;Message&gt; messages = <span class="hljs-keyword">new</span> ArrayList&lt;Message&gt;();
        messages.add(message);
        <span class="hljs-keyword">return</span> sendMessage(topic, messages);
    }
    <span class="hljs-javadoc">/**
     * 批量发送消息
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">sendMessage</span>(String topic, Collection&lt;Message&gt; messages) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">if</span> (messages == <span class="hljs-keyword">null</span> || messages.isEmpty()) {
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        <span class="hljs-keyword">for</span> (Message message : messages) {
            ProducerRecord&lt;String, <span class="hljs-keyword">byte</span>[]&gt; km = <span class="hljs-keyword">new</span> ProducerRecord&lt;String, <span class="hljs-keyword">byte</span>[]&gt;(topic, message.getMessageId(),
                    message.getMessageBody());
            producer.send(km);
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
    }
    <span class="hljs-javadoc">/**
     * 关闭发送客户端
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">close</span>() {
        producer.close();
    }
}</code></pre>

<h1 id="消息消费者messageconsumerjava">消息消费者MessageConsumer.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MessageConsumer</span> {</span>
    <span class="hljs-keyword">private</span> String topic;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> partitionsNum; 
    <span class="hljs-keyword">private</span> String topicConsumerGroup;
    <span class="hljs-keyword">private</span> MessageExecutor executor;  
    <span class="hljs-keyword">private</span> ConsumerConnector connector; 
    <span class="hljs-keyword">private</span> ExecutorService threadPool;  
    <span class="hljs-keyword">public</span> <span class="hljs-title">MessageConsumer</span>(String topic, <span class="hljs-keyword">int</span> partitionsNum,String topicConsumerGroup, MessageExecutor executor){ 
        <span class="hljs-keyword">this</span>.topic = topic;
        <span class="hljs-keyword">this</span>.executor = executor;  
        <span class="hljs-keyword">this</span>.partitionsNum = partitionsNum;
        <span class="hljs-keyword">this</span>.topicConsumerGroup = topicConsumerGroup;
        createConsumerConsumer();
    }
    <span class="hljs-javadoc">/**
     * 初始化消息消费者，创建connector
     *<span class="hljs-javadoctag"> @author</span> fuyuwei
     * 2017年6月10日 下午11:02:26
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">createConsumerConsumer</span>() { 
        <span class="hljs-keyword">try</span>{
            Properties properties = <span class="hljs-keyword">new</span> Properties();  
            properties.load(<span class="hljs-keyword">new</span> ClassPathResource(<span class="hljs-string">"consumer.properties"</span>).getInputStream());  
            properties.put(<span class="hljs-string">"group.id"</span>,topicConsumerGroup);  
            ConsumerConfig config=<span class="hljs-keyword">new</span> ConsumerConfig(properties); 
            connector=Consumer.createJavaConsumerConnector(config);    
            <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;            
        }<span class="hljs-keyword">catch</span> (IOException e) {
            BizLogger.error(e, <span class="hljs-string">"MessageConsumer"</span>,<span class="hljs-string">"init kafka consumer properties error"</span>);
        }
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
    }  

    <span class="hljs-javadoc">/**
     * 接收消息，并启动线程放到线程池执行
     *<span class="hljs-javadoctag"> @author</span> fuyuwei
     * 2017年6月10日 下午11:02:51
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">receiveMessage</span>() <span class="hljs-keyword">throws</span> Exception{      
        Map&lt;String,Integer&gt; topics = <span class="hljs-keyword">new</span> HashMap&lt;String,Integer&gt;();  
        topics.put(topic, partitionsNum);  
        Map&lt;String, List&lt;KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt;&gt;&gt; streams = connector.createMessageStreams(topics);  
        List&lt;KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt;&gt; partitions = streams.get(topic);  
        threadPool = Executors.newFixedThreadPool(partitionsNum);  
        <span class="hljs-keyword">for</span>(KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; partition : partitions){                
            threadPool.submit(<span class="hljs-keyword">new</span> ReceiverMessageRunner(partition));  
        }   

    }          
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">close</span>(){  
        <span class="hljs-keyword">try</span>{  
            <span class="hljs-keyword">if</span> (threadPool != <span class="hljs-keyword">null</span>)
                threadPool.shutdownNow();  
        }<span class="hljs-keyword">catch</span>(Exception e){  
            BizLogger.error(e, <span class="hljs-string">"MessageConsumer"</span>,<span class="hljs-string">"close fail"</span>);
        }<span class="hljs-keyword">finally</span>{  
            <span class="hljs-keyword">if</span> (connector != <span class="hljs-keyword">null</span>)
                connector.shutdown();  
        }  

    }  

    <span class="hljs-keyword">private</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ReceiverMessageRunner</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Runnable</span>{</span>  
        <span class="hljs-keyword">private</span> KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; partition;  

        <span class="hljs-keyword">public</span> <span class="hljs-title">ReceiverMessageRunner</span>(KafkaStream&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; partition) {  
            <span class="hljs-keyword">this</span>.partition = partition;  
        }  

        <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">run</span>(){  
            ConsumerIterator&lt;<span class="hljs-keyword">byte</span>[], <span class="hljs-keyword">byte</span>[]&gt; it = partition.iterator();  
            <span class="hljs-keyword">while</span>(it.hasNext()){  
                MessageAndMetadata&lt;<span class="hljs-keyword">byte</span>[],<span class="hljs-keyword">byte</span>[]&gt; item = it.next();  
                executor.execute(item.message());
            }  
        }  
    }        

}</code></pre>

<h1 id="执行消息的保存操作messageconsumerexecutorjava">执行消息的保存操作MessageConsumerExecutor.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MessageConsumerExecutor</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">MessageExecutor</span> {</span>

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">execute</span>(<span class="hljs-keyword">byte</span>[] message ) {
        <span class="hljs-keyword">try</span> {
            BizLogger.info(<span class="hljs-string">"ReceiverMessageExecutor"</span>,<span class="hljs-string">"start Resolve message"</span>);
            String random =  randomString();
            <span class="hljs-keyword">int</span> totalLength = message.length;
            <span class="hljs-keyword">if</span>(totalLength &lt;= <span class="hljs-number">4</span> ){
                BizLogger.info(<span class="hljs-string">"message length is not correct"</span>);
            }
            <span class="hljs-keyword">byte</span>[] header = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">4</span>];<span class="hljs-comment">// 4个字节的消息头</span>
            System.arraycopy(message, <span class="hljs-number">0</span>, header, <span class="hljs-number">0</span>, <span class="hljs-number">4</span>);
            <span class="hljs-keyword">int</span> headerLength = Utility.byte2Int(header);
            <span class="hljs-keyword">if</span>(headerLength &gt;= totalLength){
                BizLogger.info(<span class="hljs-string">"message header is not correct"</span>,<span class="hljs-string">"headerLength"</span>,headerLength,<span class="hljs-string">"totalLength"</span>,totalLength);
                <span class="hljs-keyword">return</span>;
            }
            <span class="hljs-keyword">byte</span>[] headerMessage = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[headerLength];
            System.arraycopy(message, <span class="hljs-number">4</span>, headerMessage, <span class="hljs-number">0</span>, headerLength);
            BizLogger.info(<span class="hljs-string">"start parse headerMessage"</span>);
            NYMobStatHeader mobheader = NYMobStatHeader.parseFrom(headerMessage);
            BizLogger.info(<span class="hljs-string">"header"</span>,mobheader.getAppVer(),mobheader.getSysVer(),mobheader.getSdkVer(),mobheader.getDeviceName(),mobheader.getTelcom(),mobheader.getImei(),mobheader.getNetworkType(),mobheader.getAppId(),mobheader.getUserId(),random + mobheader.getFileName());
            <span class="hljs-keyword">int</span> currentLength = <span class="hljs-number">4</span> + headerLength;
            <span class="hljs-keyword">while</span> (currentLength &lt; totalLength) {
                <span class="hljs-keyword">byte</span>[] bodyMessageFlag = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">4</span>];<span class="hljs-comment">// 4个字节代表消息体的长度</span>
                System.arraycopy(message, currentLength, bodyMessageFlag, <span class="hljs-number">0</span>, <span class="hljs-number">4</span>);
                <span class="hljs-keyword">int</span> bodyLength = Utility.byte2Int(bodyMessageFlag);
                <span class="hljs-keyword">if</span>(bodyLength &gt;= totalLength){
                    BizLogger.info(<span class="hljs-string">"message body is not correct"</span>);
                    <span class="hljs-keyword">return</span>;
                }
                <span class="hljs-keyword">byte</span>[] bodyMessage = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[bodyLength];
                currentLength = currentLength + <span class="hljs-number">4</span> ;
                System.arraycopy(message, currentLength, bodyMessage, <span class="hljs-number">0</span>, bodyLength);
                currentLength = currentLength + bodyLength;
                NYMobStatModel statModel = NYMobStatModel.parseFrom(bodyMessage);
                Map&lt;String,String&gt; maps = statModel.getEventAttributesMap();
                StringBuffer keys = <span class="hljs-keyword">new</span> StringBuffer();
                <span class="hljs-keyword">if</span>(maps != <span class="hljs-keyword">null</span>){
                    Set&lt;String&gt; keySet=maps.keySet();  
                       Iterator&lt;String&gt; iterator=keySet.iterator();  
                       <span class="hljs-keyword">while</span>(iterator.hasNext()){  
                           String key=iterator.next();
                           String value = maps.get(key);
                           keys.append(key).append(<span class="hljs-string">":"</span>).append(value).append(<span class="hljs-string">","</span>);
                       }  
                }
                BizLogger.info(<span class="hljs-string">"body"</span>,statModel.getDataType(),statModel.getCtime(),statModel.getEventId(),statModel.getEventLabel(),keys.toString(),statModel.getPageId(),statModel.getFromPageId(),statModel.getUserId(),random + mobheader.getFileName());
            }
        } <span class="hljs-keyword">catch</span> (InvalidProtocolBufferException e) {
            BizLogger.info(<span class="hljs-string">"protobuff parse fail  "</span>);
            ErrorMessageLogger.info(<span class="hljs-string">"ReceiverMessageExecutor"</span>,<span class="hljs-string">"protobuff parse fail"</span>);
        }<span class="hljs-keyword">catch</span> (Exception e) {
            BizLogger.info(<span class="hljs-string">"parse fail  "</span>);
            ErrorMessageLogger.info(<span class="hljs-string">"ReceiverMessageExecutor"</span>,<span class="hljs-string">"parse fail"</span>);
        }
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">randomString</span>(){
        String s = <span class="hljs-string">"ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz"</span>;     
        <span class="hljs-keyword">char</span>[] c = s.toCharArray();     
        Random random = <span class="hljs-keyword">new</span> Random();
        StringBuffer buffer = <span class="hljs-keyword">new</span> StringBuffer();
        <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>;i&lt; <span class="hljs-number">5</span>;i++){
            buffer.append(c[random.nextInt(c.length)]);
        }
        <span class="hljs-keyword">return</span> buffer.toString();
    }
}</code></pre>

<h1 id="定义保存消息操作接口类messageexecutorjava">定义保存消息操作接口类MessageExecutor.java</h1>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">interface</span> MessageExecutor {        
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">execute</span>(<span class="hljs-keyword">byte</span>[] message) ;   
}</code></pre>

<h1 id="发送消息接口类messageservicejava">发送消息接口类MessageService.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">interface</span> <span class="hljs-title">MessageService</span> {</span>
    <span class="hljs-javadoc">/**
     * 发送消息
     *<span class="hljs-javadoctag"> @param</span> message 消息
     *<span class="hljs-javadoctag"> @return</span>
     *<span class="hljs-javadoctag"> @throws</span> BusinessException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">sendMessage</span>(String topic,Message message) <span class="hljs-keyword">throws</span> Exception; 
    <span class="hljs-javadoc">/**
     * 批量发送消息
     *<span class="hljs-javadoctag"> @param</span> messages 消息集合
     *<span class="hljs-javadoctag"> @return</span>
     *<span class="hljs-javadoctag"> @throws</span> BusinessException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">sendMessage</span>(String topic,Collection&lt;Message&gt; messages)<span class="hljs-keyword">throws</span> Exception;
}</code></pre>

<h1 id="序列化工具类googleprotobufutilsjava和往前台返回json信息的工具类resputiljava">序列化工具类GoogleprotobufUtils.java和往前台返回json信息的工具类RespUtil.java</h1>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">RespUtil</span> {</span>
    <span class="hljs-javadoc">/**
     * 
     *<span class="hljs-javadoctag"> @author</span> fuyuwei
     * 2017年6月10日 下午8:23:41
     *<span class="hljs-javadoctag"> @param</span> resp
     *<span class="hljs-javadoctag"> @param</span> msg
     *<span class="hljs-javadoctag"> @param</span> cachetime
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">responJson</span>(HttpServletResponse resp, JsonMsg msg,<span class="hljs-keyword">int</span> cachetime) <span class="hljs-keyword">throws</span> IOException {
        resp.setHeader(<span class="hljs-string">"Access-Control-Allow-Origin"</span>, <span class="hljs-string">"*"</span>);
        <span class="hljs-keyword">if</span> (cachetime == <span class="hljs-number">0</span>) {
            resp.setHeader(<span class="hljs-string">"Cache-Control"</span>, <span class="hljs-string">"no-cache"</span>);
            resp.setHeader(<span class="hljs-string">"Pragma"</span>, <span class="hljs-string">"no-cache"</span>);
        } <span class="hljs-keyword">else</span> {
            resp.setHeader(<span class="hljs-string">"Cache-Control"</span>,
                    (<span class="hljs-keyword">new</span> StringBuilder()).append(<span class="hljs-string">"max-age="</span>).append(cachetime)
                            .toString());
        }
        resp.setContentType(<span class="hljs-string">"application/json;charset=utf-8"</span>);
        resp.getWriter().write(msg.toString());
        resp.getWriter().close();
    }
}</code></pre>

<h1 id="消费者配置文件consumerproperties">消费者配置文件consumer.properties</h1>



<pre class="prettyprint"><code class=" hljs avrasm">zookeeper<span class="hljs-preprocessor">.connect</span>=<span class="hljs-number">127.0</span><span class="hljs-number">.01</span>:<span class="hljs-number">2181</span>
<span class="hljs-preprocessor"># timeout in ms for connecting to zookeeper </span>
zookeeper<span class="hljs-preprocessor">.session</span><span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">20000</span>
zookeeper<span class="hljs-preprocessor">.connectiontimeout</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">1000000</span>
zookeeper<span class="hljs-preprocessor">.sync</span><span class="hljs-preprocessor">.time</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">20000</span>  
auto<span class="hljs-preprocessor">.commit</span><span class="hljs-preprocessor">.enable</span>=true
auto<span class="hljs-preprocessor">.commit</span><span class="hljs-preprocessor">.interval</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">1000</span>
queued<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.chunks</span>=<span class="hljs-number">50</span>
rebalance<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.retries</span>=<span class="hljs-number">5</span>
<span class="hljs-preprocessor"># 最大取多少块缓存到消费者(默认10)</span>
queued<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.message</span><span class="hljs-preprocessor">.chunks</span>=<span class="hljs-number">50</span>
<span class="hljs-preprocessor"># 每次feth将得到多条消息,此值为总大小,提升此值,将会消耗更多的consumer端内存</span>
fetch<span class="hljs-preprocessor">.min</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">6553600</span>
<span class="hljs-preprocessor"># 当消息的尺寸不足时,server阻塞的时间,如果超时,消息将立即发送给consumer</span>
fetch<span class="hljs-preprocessor">.wait</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">5000</span>
socket<span class="hljs-preprocessor">.receive</span><span class="hljs-preprocessor">.buffer</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">655360</span>
auto<span class="hljs-preprocessor">.offset</span><span class="hljs-preprocessor">.reset</span>=largest
<span class="hljs-preprocessor"># 指定序列化处理类(mafka client API调用说明--&gt;3.序列化约定wiki)，默认为kafka.serializer.DefaultDecoder,即byte[]</span>
derializer<span class="hljs-preprocessor">.class</span>=kafka<span class="hljs-preprocessor">.serializer</span><span class="hljs-preprocessor">.DefaultDecoder</span></code></pre>

<h1 id="生产者配置producerproperties">生产者配置producer.properties</h1>



<pre class="prettyprint"><code class=" hljs avrasm">bootstrap<span class="hljs-preprocessor">.servers</span>=<span class="hljs-number">127.0</span><span class="hljs-number">.01</span>:<span class="hljs-number">9092</span>
partitioner<span class="hljs-preprocessor">.class</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.clients</span><span class="hljs-preprocessor">.producer</span><span class="hljs-preprocessor">.internals</span><span class="hljs-preprocessor">.DefaultPartitioner</span>
value<span class="hljs-preprocessor">.serializer</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.ByteArraySerializer</span>
key<span class="hljs-preprocessor">.serializer</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.common</span><span class="hljs-preprocessor">.serialization</span><span class="hljs-preprocessor">.StringSerializer</span>
buffer<span class="hljs-preprocessor">.memory</span>=<span class="hljs-number">33554432</span>
linger<span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">0</span>
acks=<span class="hljs-number">1</span>
request<span class="hljs-preprocessor">.timeout</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">10000</span></code></pre>

<h1 id="spring文件配置spring-mvcxml">Spring文件配置spring-mvc.xml</h1>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">beans</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://www.springframework.org/schema/beans"</span>
       <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span> <span class="hljs-attribute">xmlns:mvc</span>=<span class="hljs-value">"http://www.springframework.org/schema/mvc"</span>
       <span class="hljs-attribute">xmlns:context</span>=<span class="hljs-value">"http://www.springframework.org/schema/context"</span>
       <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"
    http://www.springframework.org/schema/beans
    http://www.springframework.org/schema/beans/spring-beans-4.0.xsd
    http://www.springframework.org/schema/context 
    http://www.springframework.org/schema/context/spring-context-4.0.xsd
    http://www.springframework.org/schema/mvc  
    http://www.springframework.org/schema/mvc/spring-mvc-4.0.xsd"</span>&gt;</span>
    <span class="hljs-comment">&lt;!-- jsp视图解析器 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"jspViewResolver"</span>
          <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.springframework.web.servlet.view.InternalResourceViewResolver"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"viewClass"</span>
                  <span class="hljs-attribute">value</span>=<span class="hljs-value">"org.springframework.web.servlet.view.JstlView"</span>/&gt;</span>
        <span class="hljs-comment">&lt;!-- &lt;property name="prefix" value="/jsp/" /&gt; --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"viewNames"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"/jsp/*"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"suffix"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">".jsp"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"order"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"1"</span>/&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">bean</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">mvc:default-servlet-handler</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">context:annotation-config</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">mvc:annotation-driven</span> /&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"initAndDestroySeqBean"</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"com.swk.init.InitMessageConsumer"</span> <span class="hljs-attribute">init-method</span>=<span class="hljs-value">"initMethod"</span> <span class="hljs-attribute">destroy-method</span>=<span class="hljs-value">"destroyMethod"</span>/&gt;</span>  
    <span class="hljs-comment">&lt;!-- 扫描控制器类 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">context:component-scan</span> <span class="hljs-attribute">base-package</span>=<span class="hljs-value">"com/swk/controller"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">context:component-scan</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.springframework.web.servlet.mvc.annotation.DefaultAnnotationHandlerMapping"</span>/&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.springframework.web.servlet.mvc.annotation.AnnotationMethodHandlerAdapter"</span>/&gt;</span>
    <span class="hljs-comment">&lt;!-- 采用注解方式配置MVC --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">mvc:annotation-driven
</span>            <span class="hljs-attribute">content-negotiation-manager</span>=<span class="hljs-value">"contentNegotiationManager"</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">mvc:annotation-driven</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">mvc:interceptors</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">mvc:interceptor</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mvc:mapping</span> <span class="hljs-attribute">path</span>=<span class="hljs-value">"/kafka/log/*"</span> /&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"com.swk.interceptor.AccessInteceptor"</span> /&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">mvc:interceptor</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">mvc:interceptors</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">bean</span> <span class="hljs-attribute">id</span>=<span class="hljs-value">"contentNegotiationManager"</span>
          <span class="hljs-attribute">class</span>=<span class="hljs-value">"org.springframework.web.accept.ContentNegotiationManagerFactoryBean"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"favorPathExtension"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"true"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"favorParameter"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"true"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"defaultContentType"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"text/html"</span>/&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"mediaTypes"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>
                json=application/json
                xml=application/xml
            <span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">bean</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">beans</span>&gt;</span>
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
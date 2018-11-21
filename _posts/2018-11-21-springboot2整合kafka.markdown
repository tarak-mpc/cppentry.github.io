---
layout:     post
title:      springboot2整合kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="一下载和启动kafka">一、下载和启动kafka</h4>

<blockquote>
  <p>启动kafka服务之前要先启动zookeeper，目前kafka的压缩包中是包含zookeeper的</p>
</blockquote>

<p>1、从官网下载kafka的压缩包：<a href="http://kafka.apache.org/downloads" rel="nofollow">http://kafka.apache.org/downloads</a> <br>
2、解压下载的压缩包 ，我本地解压后的kafka文件夹放在了E:\keluosi目录下 <br>
3、启动zookeeper</p>

<pre class="prettyprint"><code class=" hljs tex">E:<span class="hljs-command">\keluosi</span><span class="hljs-command">\kafka</span>_2.11-1.1.0<span class="hljs-command">\bin</span><span class="hljs-command">\windows</span>&gt;.<span class="hljs-command">\zookeeper</span>-server-start.bat ..<span class="hljs-command">\.</span>.<span class="hljs-command">\config</span><span class="hljs-command">\zookeeper</span>.properties</code></pre>

<p>4、启动kafka</p>

<pre class="prettyprint"><code class=" hljs tex">E:<span class="hljs-command">\keluosi</span><span class="hljs-command">\kafka</span>_2.11-1.1.0<span class="hljs-command">\bin</span><span class="hljs-command">\windows</span>&gt; .<span class="hljs-command">\kafka</span>-server-start.bat ..<span class="hljs-command">\.</span>.<span class="hljs-command">\config</span><span class="hljs-command">\server</span>.properties</code></pre>

<h4 id="二创建springboot项目进行整合">二、创建springboot项目进行整合</h4>

<blockquote>
  <p>消息发送方</p>
</blockquote>

<p>1、导入kafka的jar包</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-kafka<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>2、配置application.yml文件</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">server:</span>
  port: <span class="hljs-number">11000</span>
<span class="hljs-label">spring:</span>
  kafka:
    bootstrap-servers: localhost:<span class="hljs-number">9092</span>
</code></pre>

<p>3、编写发送消息的代码</p>



<pre class="prettyprint"><code class=" hljs avrasm">import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.beans</span><span class="hljs-preprocessor">.factory</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.Autowired</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.kafka</span><span class="hljs-preprocessor">.core</span><span class="hljs-preprocessor">.KafkaTemplate</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.web</span><span class="hljs-preprocessor">.bind</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.RequestMapping</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.springframework</span><span class="hljs-preprocessor">.web</span><span class="hljs-preprocessor">.bind</span><span class="hljs-preprocessor">.annotation</span><span class="hljs-preprocessor">.RestController</span><span class="hljs-comment">;</span>

@RestController
public class KafkaController {

    @Autowired
    private KafkaTemplate&lt;String, String&gt; kafkaTemplate<span class="hljs-comment">;</span>

    @RequestMapping(<span class="hljs-string">"/send"</span>)
    public String send(String name) {
        kafkaTemplate<span class="hljs-preprocessor">.send</span>(<span class="hljs-string">"mytopic"</span>, name)<span class="hljs-comment">;</span>
        return name<span class="hljs-comment">;</span>
    }

}</code></pre>

<blockquote>
  <p>消息接收方</p>
</blockquote>

<p>1、导入kafka的jar包</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.springframework.kafka<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>spring-kafka<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span></code></pre>

<p>2、配置application.yml文件</p>



<pre class="prettyprint"><code class=" hljs lasso">server:
  port: <span class="hljs-number">11001</span>
spring:
  kafka:
    bootstrap<span class="hljs-attribute">-servers</span>: localhost:<span class="hljs-number">9092</span>
    consumer:
      <span class="hljs-keyword">group</span><span class="hljs-attribute">-id</span>: mygroup</code></pre>

<p>3、编写发送消息的代码</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> org.apache.kafka.clients.consumer.ConsumerRecord;
<span class="hljs-keyword">import</span> org.springframework.kafka.annotation.KafkaListener;
<span class="hljs-keyword">import</span> org.springframework.stereotype.Component;

<span class="hljs-javadoc">/**
 *<span class="hljs-javadoctag"> @author</span>: keluosi@bicitech.cn
 *<span class="hljs-javadoctag"> @review</span>:
 *<span class="hljs-javadoctag"> @date</span>: 2018/9/14
 */</span>
<span class="hljs-annotation">@Component</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MyConsumer</span> {</span>

    <span class="hljs-annotation">@KafkaListener</span>(topics = <span class="hljs-string">"mytopic"</span>)
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listen</span>(ConsumerRecord&lt;?,String&gt; record) {
        String value = record.value();
        System.out.println(value);
        System.out.println(record);
    }
}</code></pre>



<h2 id="进行测试">进行测试</h2>

<p>1、输入url地址，访问消息发送方</p>

<p><img src="https://img-blog.csdn.net/20180914174129552?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTcwNTgz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>2、观察后端控制台打印</p>

<p><img src="https://img-blog.csdn.net/20180914174220512?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTcwNTgz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
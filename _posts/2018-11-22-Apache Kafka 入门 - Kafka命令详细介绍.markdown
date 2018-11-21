---
layout:     post
title:      Apache Kafka 入门 - Kafka命令详细介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权归博主所有，转载请带上本文链接！联系方式：abel533@gmail.com					https://blog.csdn.net/isea533/article/details/73720066				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="apache-kafka-入门">Apache Kafka 入门</h1>

<p>Apache Kafka 入门大概分为5篇博客，内容都比较基础，计划包含以下内容：</p>

<ul>
<li>Kafka的基本配置和运行</li>
<li>Kafka命令详细介绍</li>
<li>Kafka-manager的基本配置和运行</li>
<li>Kafka API 简单用法</li>
<li>Spring Boot 集成Kafka</li>
</ul>

<p>Kafka支持Linux和WIndows环境，本文运行环境使用Linux（CentOS）。</p>

<p>本篇为第二篇。</p>



<h2 id="kafka命令行详细介绍">Kafka命令行详细介绍</h2>

<p>常用的几个命令如下：</p>

<ul>
<li>kafka-server-start.sh</li>
<li>kafka-console-consumer.sh</li>
<li>kafka-console-producer.sh</li>
<li>kafka-topics.sh</li>
</ul>

<p>在这几个命令中，第一个仅用于启动Kafka，后两个console常用于测试，用途最多的是最后一个命令，所以下面命令中主要介绍的就是 kafka-topics.sh。</p>



<h3 id="kafka-server-startsh">kafka-server-start.sh</h3>

<p>用法：<code>&gt; bin/kafka-server-start.sh [-daemon] server.properties [--override property=value]*</code></p>

<p>这个命令后面可以有多个参数，第一个是可选参数，该参数可以让当前命令以后台服务方式执行，第二个必须是 Kafka 的配置文件。后面还可以有多个<code>--override</code>开头的参数，其中的<code>property</code>可以是<strong><a href="https://kafka.apache.org/documentation/#brokerconfigs" rel="nofollow">Broker Configs</a></strong>中提供的所有参数。这些额外的参数会覆盖配置文件中的设置。</p>

<p>例如下面使用同一个配置文件，通过参数覆盖启动多个Broker。</p>



<pre class="prettyprint"><code class="language-shell hljs lasso"><span class="hljs-subst">&gt;</span> bin/kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh <span class="hljs-attribute">-daemon</span> config/server<span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>override broker<span class="hljs-built_in">.</span>id<span class="hljs-subst">=</span><span class="hljs-number">0</span> <span class="hljs-subst">--</span>override <span class="hljs-keyword">log</span><span class="hljs-built_in">.</span>dirs<span class="hljs-subst">=</span>/tmp/kafka<span class="hljs-attribute">-logs</span><span class="hljs-subst">-</span><span class="hljs-number">1</span> <span class="hljs-subst">--</span>override listeners<span class="hljs-subst">=</span>PLAINTEXT:<span class="hljs-comment">//:9092 --override advertised.listeners=PLAINTEXT://192.168.16.150:9092</span>

<span class="hljs-subst">&gt;</span> bin/kafka<span class="hljs-attribute">-server</span><span class="hljs-attribute">-start</span><span class="hljs-built_in">.</span>sh <span class="hljs-attribute">-daemon</span> config/server<span class="hljs-built_in">.</span>properties <span class="hljs-subst">--</span>override broker<span class="hljs-built_in">.</span>id<span class="hljs-subst">=</span><span class="hljs-number">1</span> <span class="hljs-subst">--</span>override <span class="hljs-keyword">log</span><span class="hljs-built_in">.</span>dirs<span class="hljs-subst">=</span>/tmp/kafka<span class="hljs-attribute">-logs</span><span class="hljs-subst">-</span><span class="hljs-number">2</span> <span class="hljs-subst">--</span>override listeners<span class="hljs-subst">=</span>PLAINTEXT:<span class="hljs-comment">//:9093 --override advertised.listeners=PLAINTEXT://192.168.16.150:9093</span></code></pre>

<p>上面这种用法只是用于演示，真正要启动多个Broker 应该针对不同的 Broker 创建相应的 server.properties 配置。</p>



<h3 id="kafka-console-consumersh">kafka-console-consumer.sh</h3>

<p>这个命令只是简单的将消息输出到标准输出中，该命令支持的参数如下。</p>



<pre class="prettyprint"><code class=" hljs vbnet"><span class="hljs-keyword">option</span>                                   Description                            
------                                   -----------                            
--blacklist &lt;<span class="hljs-built_in">String</span>: blacklist&gt;          Blacklist <span class="hljs-keyword">of</span> topics <span class="hljs-keyword">to</span> exclude <span class="hljs-keyword">from</span>    
                                           consumption.                         
--bootstrap-server &lt;<span class="hljs-built_in">String</span>: server <span class="hljs-keyword">to</span>    REQUIRED (unless old consumer <span class="hljs-keyword">is</span>       
  connect <span class="hljs-keyword">to</span>&gt;                              used): The server <span class="hljs-keyword">to</span> connect <span class="hljs-keyword">to</span>.     
--consumer-<span class="hljs-keyword">property</span> &lt;<span class="hljs-built_in">String</span>:             A mechanism <span class="hljs-keyword">to</span> pass user-defined       
  consumer_prop&gt;                           properties <span class="hljs-keyword">in</span> the form <span class="hljs-keyword">key</span>=value <span class="hljs-keyword">to</span>  
                                           the consumer.                        
--consumer.config &lt;<span class="hljs-built_in">String</span>: config file&gt;  Consumer config properties file. Note  
                                           that [consumer-<span class="hljs-keyword">property</span>] takes       
                                           precedence over this config.         
--csv-reporter-enabled                   <span class="hljs-keyword">If</span> <span class="hljs-keyword">set</span>, the CSV metrics reporter will  
                                           be enabled                           
--delete-consumer-offsets                <span class="hljs-keyword">If</span> specified, the consumer path <span class="hljs-keyword">in</span>     
                                           zookeeper <span class="hljs-keyword">is</span> deleted <span class="hljs-keyword">when</span> starting up
--enable-systest-events                  Log lifecycle events <span class="hljs-keyword">of</span> the consumer   
                                           <span class="hljs-keyword">in</span> addition <span class="hljs-keyword">to</span> logging consumed      
                                           messages. (This <span class="hljs-keyword">is</span> specific <span class="hljs-keyword">for</span>      
                                           system tests.)                       
--formatter &lt;<span class="hljs-built_in">String</span>: <span class="hljs-keyword">class</span>&gt;              The name <span class="hljs-keyword">of</span> a <span class="hljs-keyword">class</span> <span class="hljs-keyword">to</span> use <span class="hljs-keyword">for</span>         
                                           formatting kafka messages <span class="hljs-keyword">for</span>        
                                           display. (<span class="hljs-keyword">default</span>: kafka.tools.      
                                           DefaultMessageFormatter)             
--<span class="hljs-keyword">from</span>-beginning                         <span class="hljs-keyword">If</span> the consumer does <span class="hljs-keyword">not</span> already have  
                                           an established offset <span class="hljs-keyword">to</span> consume     
                                           <span class="hljs-keyword">from</span>, start <span class="hljs-keyword">with</span> the earliest        
                                           message present <span class="hljs-keyword">in</span> the log rather    
                                           than the latest message.             
--<span class="hljs-keyword">key</span>-deserializer &lt;<span class="hljs-built_in">String</span>:                                                     
  deserializer <span class="hljs-keyword">for</span> <span class="hljs-keyword">key</span>&gt;                                                         
--max-messages &lt;<span class="hljs-built_in">Integer</span>: num_messages&gt;   The maximum number <span class="hljs-keyword">of</span> messages <span class="hljs-keyword">to</span>      
                                           consume before exiting. <span class="hljs-keyword">If</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">set</span>,  
                                           consumption <span class="hljs-keyword">is</span> continual.            
--metrics-dir &lt;<span class="hljs-built_in">String</span>: metrics           <span class="hljs-keyword">If</span> csv-reporter-enable <span class="hljs-keyword">is</span> <span class="hljs-keyword">set</span>, <span class="hljs-keyword">and</span>     
  directory&gt;                               this parameter isset, the csv        
                                           metrics will be outputed here        
--<span class="hljs-keyword">new</span>-consumer                           Use the <span class="hljs-keyword">new</span> consumer implementation.   
                                           This <span class="hljs-keyword">is</span> the <span class="hljs-keyword">default</span>.                 
--offset &lt;<span class="hljs-built_in">String</span>: consume offset&gt;        The offset id <span class="hljs-keyword">to</span> consume <span class="hljs-keyword">from</span> (a non-  
                                           negative number), <span class="hljs-keyword">or</span> <span class="hljs-comment">'earliest'      </span>
                                           which means <span class="hljs-keyword">from</span> beginning, <span class="hljs-keyword">or</span>       
                                           <span class="hljs-comment">'latest' which means from end        </span>
                                           (<span class="hljs-keyword">default</span>: latest)                    
--partition &lt;<span class="hljs-built_in">Integer</span>: partition&gt;         The partition <span class="hljs-keyword">to</span> consume <span class="hljs-keyword">from</span>.         
--<span class="hljs-keyword">property</span> &lt;<span class="hljs-built_in">String</span>: prop&gt;                The properties <span class="hljs-keyword">to</span> initialize the       
                                           message formatter.                   
--<span class="hljs-keyword">skip</span>-message-<span class="hljs-keyword">on</span>-<span class="hljs-keyword">error</span>                  <span class="hljs-keyword">If</span> there <span class="hljs-keyword">is</span> an <span class="hljs-keyword">error</span> <span class="hljs-keyword">when</span> processing a 
                                           message, <span class="hljs-keyword">skip</span> it instead <span class="hljs-keyword">of</span> halt.    
--timeout-ms &lt;<span class="hljs-built_in">Integer</span>: timeout_ms&gt;       <span class="hljs-keyword">If</span> specified, <span class="hljs-keyword">exit</span> <span class="hljs-keyword">if</span> no message <span class="hljs-keyword">is</span>    
                                           available <span class="hljs-keyword">for</span> consumption <span class="hljs-keyword">for</span> the    
                                           specified interval.                  
--topic &lt;<span class="hljs-built_in">String</span>: topic&gt;                  The topic id <span class="hljs-keyword">to</span> consume <span class="hljs-keyword">on</span>.            
--value-deserializer &lt;<span class="hljs-built_in">String</span>:                                                   
  deserializer <span class="hljs-keyword">for</span> values&gt;                                                      
--whitelist &lt;<span class="hljs-built_in">String</span>: whitelist&gt;          Whitelist <span class="hljs-keyword">of</span> topics <span class="hljs-keyword">to</span> include <span class="hljs-keyword">for</span>     
                                           consumption.                         
--zookeeper &lt;<span class="hljs-built_in">String</span>: urls&gt;               REQUIRED (only <span class="hljs-keyword">when</span> <span class="hljs-keyword">using</span> old          
                                           consumer): The connection <span class="hljs-built_in">string</span> <span class="hljs-keyword">for</span> 
                                           the zookeeper connection <span class="hljs-keyword">in</span> the form 
                                           host:port. Multiple URLS can be      
                                           given <span class="hljs-keyword">to</span> allow fail-over. </code></pre>

<p><code>--bootstrap-server</code> 必须指定，通常<code>--topic</code>也要指定查看的主题。如果想要从头查看消息，还可以指定<code>--from-beginning</code>参数。一般使用的命令如下。</p>



<pre class="prettyprint"><code class="language-shell hljs lasso"><span class="hljs-subst">&gt;</span> bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>bootstrap<span class="hljs-attribute">-server</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span></code></pre>

<p>还可以通过下面的命令指定分区查看：</p>



<pre class="prettyprint"><code class="language-shell hljs brainfuck">&gt;&gt; <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">bootstrap</span><span class="hljs-literal">-</span><span class="hljs-comment">server</span> <span class="hljs-comment">localhost:9092</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partition</span> <span class="hljs-comment">0</span></code></pre>



<h3 id="kafka-console-producersh">kafka-console-producer.sh</h3>

<p>这个命令可以将文件或标准输入的内容发送到Kafka集群。该命令参数如下。</p>



<pre class="prettyprint"><code class=" hljs vbnet"><span class="hljs-keyword">Option</span>                                   Description                            
------                                   -----------                            
--batch-size &lt;<span class="hljs-built_in">Integer</span>: size&gt;             Number <span class="hljs-keyword">of</span> messages <span class="hljs-keyword">to</span> send <span class="hljs-keyword">in</span> a <span class="hljs-built_in">single</span> 
                                           batch <span class="hljs-keyword">if</span> they are <span class="hljs-keyword">not</span> being sent     
                                           synchronously. (<span class="hljs-keyword">default</span>: <span class="hljs-number">200</span>)        
--broker-list &lt;<span class="hljs-built_in">String</span>: broker-list&gt;      REQUIRED: The broker list <span class="hljs-built_in">string</span> <span class="hljs-keyword">in</span>    
                                           the form HOST1:PORT1,HOST2:PORT2.    
--compression-codec [<span class="hljs-built_in">String</span>:             The compression codec: either <span class="hljs-comment">'none',  </span>
  compression-codec]                       <span class="hljs-comment">'gzip', 'snappy', or 'lz4'.If        </span>
                                           specified without value, <span class="hljs-keyword">then</span> it     
                                           defaults <span class="hljs-keyword">to</span> <span class="hljs-comment">'gzip'                   </span>
--<span class="hljs-keyword">key</span>-serializer &lt;<span class="hljs-built_in">String</span>:                The <span class="hljs-keyword">class</span> name <span class="hljs-keyword">of</span> the message encoder  
  encoder_class&gt;                           implementation <span class="hljs-keyword">to</span> use <span class="hljs-keyword">for</span>            
                                           serializing keys. (<span class="hljs-keyword">default</span>: kafka.   
                                           serializer.DefaultEncoder)           
--line-reader &lt;<span class="hljs-built_in">String</span>: reader_class&gt;     The <span class="hljs-keyword">class</span> name <span class="hljs-keyword">of</span> the <span class="hljs-keyword">class</span> <span class="hljs-keyword">to</span> use <span class="hljs-keyword">for</span> 
                                           reading lines <span class="hljs-keyword">from</span> standard <span class="hljs-keyword">in</span>. <span class="hljs-keyword">By</span>   
                                           <span class="hljs-keyword">default</span> <span class="hljs-keyword">each</span> line <span class="hljs-keyword">is</span> read <span class="hljs-keyword">as</span> a       
                                           separate message. (<span class="hljs-keyword">default</span>: kafka.   
                                           tools.                               
                                           ConsoleProducer$LineMessageReader)   
--max-block-ms &lt;<span class="hljs-built_in">Long</span>: max block <span class="hljs-keyword">on</span>       The max time that the producer will    
  send&gt;                                    block <span class="hljs-keyword">for</span> during a send request      
                                           (<span class="hljs-keyword">default</span>: <span class="hljs-number">60000</span>)                     
--max-memory-bytes &lt;<span class="hljs-built_in">Long</span>: total memory   The total memory used <span class="hljs-keyword">by</span> the producer  
  <span class="hljs-keyword">in</span> bytes&gt;                                <span class="hljs-keyword">to</span> buffer records waiting <span class="hljs-keyword">to</span> be sent 
                                           <span class="hljs-keyword">to</span> the server. (<span class="hljs-keyword">default</span>: <span class="hljs-number">33554432</span>)   
--max-partition-memory-bytes &lt;<span class="hljs-built_in">Long</span>:      The buffer size allocated <span class="hljs-keyword">for</span> a        
  memory <span class="hljs-keyword">in</span> bytes per partition&gt;           partition. <span class="hljs-keyword">When</span> records are received 
                                           which are smaller than this size the 
                                           producer will attempt <span class="hljs-keyword">to</span>             
                                           optimistically <span class="hljs-keyword">group</span> them together   
                                           <span class="hljs-keyword">until</span> this size <span class="hljs-keyword">is</span> reached.          
                                           (<span class="hljs-keyword">default</span>: <span class="hljs-number">16384</span>)                     
--message-send-max-retries &lt;<span class="hljs-built_in">Integer</span>&gt;     Brokers can fail receiving the message 
                                           <span class="hljs-keyword">for</span> multiple reasons, <span class="hljs-keyword">and</span> being      
                                           unavailable transiently <span class="hljs-keyword">is</span> just one  
                                           <span class="hljs-keyword">of</span> them. This <span class="hljs-keyword">property</span> specifies the 
                                           number <span class="hljs-keyword">of</span> retires before the         
                                           producer give up <span class="hljs-keyword">and</span> drop this       
                                           message. (<span class="hljs-keyword">default</span>: <span class="hljs-number">3</span>)                
--metadata-expiry-ms &lt;<span class="hljs-built_in">Long</span>: metadata     The period <span class="hljs-keyword">of</span> time <span class="hljs-keyword">in</span> milliseconds     
  expiration interval&gt;                     after which we force a refresh <span class="hljs-keyword">of</span>    
                                           metadata even <span class="hljs-keyword">if</span> we haven<span class="hljs-comment">'t seen any </span>
                                           leadership changes. (<span class="hljs-keyword">default</span>: <span class="hljs-number">300000</span>)
--old-producer                           Use the old producer implementation.   
--producer-<span class="hljs-keyword">property</span> &lt;<span class="hljs-built_in">String</span>:             A mechanism <span class="hljs-keyword">to</span> pass user-defined       
  producer_prop&gt;                           properties <span class="hljs-keyword">in</span> the form <span class="hljs-keyword">key</span>=value <span class="hljs-keyword">to</span>  
                                           the producer.                        
--producer.config &lt;<span class="hljs-built_in">String</span>: config file&gt;  Producer config properties file. Note  
                                           that [producer-<span class="hljs-keyword">property</span>] takes       
                                           precedence over this config.         
--<span class="hljs-keyword">property</span> &lt;<span class="hljs-built_in">String</span>: prop&gt;                A mechanism <span class="hljs-keyword">to</span> pass user-defined       
                                           properties <span class="hljs-keyword">in</span> the form <span class="hljs-keyword">key</span>=value <span class="hljs-keyword">to</span>  
                                           the message reader. This allows      
                                           <span class="hljs-keyword">custom</span> configuration <span class="hljs-keyword">for</span> a user-     
                                           defined message reader.              
--queue-enqueuetimeout-ms &lt;<span class="hljs-built_in">Integer</span>:      Timeout <span class="hljs-keyword">for</span> <span class="hljs-keyword">event</span> enqueue (<span class="hljs-keyword">default</span>:    
  queue enqueuetimeout ms&gt;                 <span class="hljs-number">2147483647</span>)                          
--queue-size &lt;<span class="hljs-built_in">Integer</span>: queue_size&gt;       <span class="hljs-keyword">If</span> <span class="hljs-keyword">set</span> <span class="hljs-keyword">and</span> the producer <span class="hljs-keyword">is</span> running <span class="hljs-keyword">in</span>  
                                           asynchronous mode, this gives the    
                                           maximum amount <span class="hljs-keyword">of</span>  messages will     
                                           queue awaiting sufficient batch      
                                           size. (<span class="hljs-keyword">default</span>: <span class="hljs-number">10000</span>)               
--request-required-acks &lt;<span class="hljs-built_in">String</span>:         The required acks <span class="hljs-keyword">of</span> the producer      
  request required acks&gt;                   requests (<span class="hljs-keyword">default</span>: <span class="hljs-number">1</span>)                
--request-timeout-ms &lt;<span class="hljs-built_in">Integer</span>: request   The ack timeout <span class="hljs-keyword">of</span> the producer        
  timeout ms&gt;                              requests. Value must be non-negative 
                                           <span class="hljs-keyword">and</span> non-zero (<span class="hljs-keyword">default</span>: <span class="hljs-number">1500</span>)         
--retry-backoff-ms &lt;<span class="hljs-built_in">Integer</span>&gt;             Before <span class="hljs-keyword">each</span> retry, the producer        
                                           refreshes the metadata <span class="hljs-keyword">of</span> relevant   
                                           topics. Since leader election takes  
                                           a bit <span class="hljs-keyword">of</span> time, this <span class="hljs-keyword">property</span>         
                                           specifies the amount <span class="hljs-keyword">of</span> time that    
                                           the producer waits before refreshing 
                                           the metadata. (<span class="hljs-keyword">default</span>: <span class="hljs-number">100</span>)         
--socket-buffer-size &lt;<span class="hljs-built_in">Integer</span>: size&gt;     The size <span class="hljs-keyword">of</span> the tcp RECV size.         
                                           (<span class="hljs-keyword">default</span>: <span class="hljs-number">102400</span>)                    
--sync                                   <span class="hljs-keyword">If</span> <span class="hljs-keyword">set</span> message send requests <span class="hljs-keyword">to</span> the    
                                           brokers are synchronously, one at a  
                                           time <span class="hljs-keyword">as</span> they arrive.                 
--timeout &lt;<span class="hljs-built_in">Integer</span>: timeout_ms&gt;          <span class="hljs-keyword">If</span> <span class="hljs-keyword">set</span> <span class="hljs-keyword">and</span> the producer <span class="hljs-keyword">is</span> running <span class="hljs-keyword">in</span>  
                                           asynchronous mode, this gives the    
                                           maximum amount <span class="hljs-keyword">of</span> time a message     
                                           will queue awaiting sufficient batch 
                                           size. The value <span class="hljs-keyword">is</span> given <span class="hljs-keyword">in</span> ms.      
                                           (<span class="hljs-keyword">default</span>: <span class="hljs-number">1000</span>)                      
--topic &lt;<span class="hljs-built_in">String</span>: topic&gt;                  REQUIRED: The topic id <span class="hljs-keyword">to</span> produce      
                                           messages <span class="hljs-keyword">to</span>.                         
--value-serializer &lt;<span class="hljs-built_in">String</span>:              The <span class="hljs-keyword">class</span> name <span class="hljs-keyword">of</span> the message encoder  
  encoder_class&gt;                           implementation <span class="hljs-keyword">to</span> use <span class="hljs-keyword">for</span>            
                                           serializing values. (<span class="hljs-keyword">default</span>: kafka. 
                                           serializer.DefaultEncoder)   </code></pre>

<p>其中 <code>--broker-list</code> 和 <code>--topic</code> 是两个必须提供的参数。</p>

<p>常用命令如下。</p>

<p>使用标准输入方式。</p>



<pre class="prettyprint"><code class="language-shell hljs lasso"><span class="hljs-subst">&gt;</span> bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test</code></pre>

<p>从文件读取：</p>



<pre class="prettyprint"><code class="language-shell hljs lasso"><span class="hljs-subst">&gt;</span> bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> localhost:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic test <span class="hljs-subst">&lt;</span> file<span class="hljs-attribute">-input</span><span class="hljs-built_in">.</span>txt</code></pre>



<h3 id="kafka-topicssh">kafka-topics.sh</h3>

<p>相比上面几个偶尔使用的命令来说，kafka-topics.sh 相对就比较重要。该命令包含以下参数。</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">Create</span>, <span class="hljs-keyword">delete</span>, <span class="hljs-keyword">describe</span>, <span class="hljs-keyword">or</span> change a topic.
<span class="hljs-keyword">Option</span>                                   Description                            
------                                   -----------                            
--<span class="hljs-keyword">alter</span>                                  <span class="hljs-keyword">Alter</span> the <span class="hljs-keyword">number</span> <span class="hljs-keyword">of</span> partitions,        
                                           replica assignment, <span class="hljs-keyword">and</span>/<span class="hljs-keyword">or</span>           
                                           configuration <span class="hljs-keyword">for</span> the topic.         
--config &lt;String: name=<span class="hljs-keyword">value</span>&gt;            A topic configuration override <span class="hljs-keyword">for</span> the 
                                           topic being created <span class="hljs-keyword">or</span> altered.The   
                                           following <span class="hljs-keyword">is</span> a list <span class="hljs-keyword">of</span> valid         
                                           configurations:                      
                                            cleanup.policy                        
                                            compression.type                      
                                            <span class="hljs-keyword">delete</span>.retention.ms                   
                                            file.<span class="hljs-keyword">delete</span>.delay.ms                  
                                            flush.messages                        
                                            flush.ms                              
                                            follower.replication.throttled.       
                                           replicas                             
                                            index.<span class="hljs-keyword">interval</span>.bytes                  
                                            leader.replication.throttled.replicas 
                                            <span class="hljs-aggregate">max</span>.message.bytes                     
                                            message.format.version                
                                            message.<span class="hljs-keyword">timestamp</span>.difference.<span class="hljs-aggregate">max</span>.ms   
                                            message.<span class="hljs-keyword">timestamp</span>.type                
                                            <span class="hljs-aggregate">min</span>.cleanable.dirty.ratio             
                                            <span class="hljs-aggregate">min</span>.compaction.lag.ms                 
                                            <span class="hljs-aggregate">min</span>.insync.replicas                   
                                            preallocate                           
                                            retention.bytes                       
                                            retention.ms                          
                                            segment.bytes                         
                                            segment.index.bytes                   
                                            segment.jitter.ms                     
                                            segment.ms                            
                                            unclean.leader.election.enable        
                                         See the Kafka documentation <span class="hljs-keyword">for</span> <span class="hljs-keyword">full</span>   
                                           details <span class="hljs-keyword">on</span> the topic configs.        
--<span class="hljs-keyword">create</span>                                 <span class="hljs-keyword">Create</span> a new topic.                    
--<span class="hljs-keyword">delete</span>                                 <span class="hljs-keyword">Delete</span> a topic                         
--<span class="hljs-keyword">delete</span>-config &lt;String: name&gt;           A topic configuration override <span class="hljs-keyword">to</span> be   
                                           removed <span class="hljs-keyword">for</span> an existing topic (see   
                                           the list <span class="hljs-keyword">of</span> configurations under the 
                                           --config <span class="hljs-keyword">option</span>).                    
--<span class="hljs-keyword">describe</span>                               List details <span class="hljs-keyword">for</span> the given topics.     
--disable-rack-aware                     Disable rack aware replica assignment  
--force                                  Suppress console prompts               
--help                                   Print <span class="hljs-keyword">usage</span> information.               
--<span class="hljs-keyword">if</span>-<span class="hljs-keyword">exists</span>                              <span class="hljs-keyword">if</span> <span class="hljs-keyword">set</span> <span class="hljs-keyword">when</span> altering <span class="hljs-keyword">or</span> deleting       
                                           topics, the <span class="hljs-keyword">action</span> will <span class="hljs-keyword">only</span> <span class="hljs-keyword">execute</span> 
                                           <span class="hljs-keyword">if</span> the topic <span class="hljs-keyword">exists</span>                  
--<span class="hljs-keyword">if</span>-<span class="hljs-keyword">not</span>-<span class="hljs-keyword">exists</span>                          <span class="hljs-keyword">if</span> <span class="hljs-keyword">set</span> <span class="hljs-keyword">when</span> creating topics, the       
                                           <span class="hljs-keyword">action</span> will <span class="hljs-keyword">only</span> <span class="hljs-keyword">execute</span> <span class="hljs-keyword">if</span> the      
                                           topic does <span class="hljs-keyword">not</span> already exist         
--list                                   List <span class="hljs-keyword">all</span> available topics.             
--partitions &lt;<span class="hljs-keyword">Integer</span>: # <span class="hljs-keyword">of</span> partitions&gt;  正在创建或更改主题的分区数
                                         （警告：如果为具有密钥的主题   
                                         （分区）增加了分区  
                                          消息的逻辑或排序将受到影响                    
--replica-assignment &lt;String:            A list <span class="hljs-keyword">of</span> manual partition-<span class="hljs-keyword">to</span>-broker   
  broker_id_for_part1_replica1 :           assignments <span class="hljs-keyword">for</span> the topic being      
  broker_id_for_part1_replica2 ,           created <span class="hljs-keyword">or</span> altered.                  
  broker_id_for_part2_replica1 :                                                
  broker_id_for_part2_replica2 , ...&gt;                                           
--replication-factor &lt;<span class="hljs-keyword">Integer</span>:           正在创建的主题中每个分区的复制因子。        
  replication factor&gt;                    
--topic &lt;String: topic&gt;                  The topic <span class="hljs-keyword">to</span> be <span class="hljs-keyword">create</span>, <span class="hljs-keyword">alter</span> <span class="hljs-keyword">or</span>       
                                           <span class="hljs-keyword">describe</span>. Can also accept a regular  
                                           expression <span class="hljs-keyword">except</span> <span class="hljs-keyword">for</span> --<span class="hljs-keyword">create</span> <span class="hljs-keyword">option</span>
--topics-<span class="hljs-keyword">with</span>-overrides                  <span class="hljs-keyword">if</span> <span class="hljs-keyword">set</span> <span class="hljs-keyword">when</span> describing topics, <span class="hljs-keyword">only</span>    
                                           <span class="hljs-keyword">show</span> topics that have overridden     
                                           configs                              
--unavailable-partitions                 <span class="hljs-keyword">if</span> <span class="hljs-keyword">set</span> <span class="hljs-keyword">when</span> describing topics, <span class="hljs-keyword">only</span>    
                                           <span class="hljs-keyword">show</span> partitions whose leader <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span>  
                                           available                            
--under-replicated-partitions            <span class="hljs-keyword">if</span> <span class="hljs-keyword">set</span> <span class="hljs-keyword">when</span> describing topics, <span class="hljs-keyword">only</span>    
                                           <span class="hljs-keyword">show</span> under replicated partitions     
--zookeeper &lt;String: urls&gt;               REQUIRED: The <span class="hljs-keyword">connection</span> string <span class="hljs-keyword">for</span>    
                                           the zookeeper <span class="hljs-keyword">connection</span> <span class="hljs-keyword">in</span> the form 
                                           host:port. Multiple URLS can be      
                                           given <span class="hljs-keyword">to</span> allow fail-over.         </span></code></pre>

<p>下面是几种常用的 topic 命令。</p>



<h4 id="描述主题的配置">描述主题的配置</h4>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">configs</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">entity</span><span class="hljs-literal">-</span><span class="hljs-comment">type</span> <span class="hljs-comment">topics</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">entity</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">test_topic</span></code></pre>



<h4 id="设置保留时间">设置保留时间</h4>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">#</span> <span class="hljs-comment">Deprecated</span> <span class="hljs-comment">way</span>
<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span>  <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">alter</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test_topic</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">config</span> <span class="hljs-comment">retention</span><span class="hljs-string">.</span><span class="hljs-comment">ms=1000</span>

<span class="hljs-comment">#</span> <span class="hljs-comment">Modern</span> <span class="hljs-comment">way</span>
<span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">configs</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">alter</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">entity</span><span class="hljs-literal">-</span><span class="hljs-comment">type</span> <span class="hljs-comment">topics</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">entity</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">test_topic</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">add</span><span class="hljs-literal">-</span><span class="hljs-comment">config</span> <span class="hljs-comment">retention</span><span class="hljs-string">.</span><span class="hljs-comment">ms=1000</span></code></pre>

<p>如果您需要删除主题中的所有消息，则可以利用保留时间。首先将保留时间设置为非常低（1000 ms），等待几秒钟，然后将保留时间恢复为上一个值。</p>

<p>注意：默认保留时间为24小时（86400000毫秒）。</p>



<h4 id="删除主题">删除主题</h4>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">delete</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test_topic</span></code></pre>

<blockquote>
  <p>注意：需要在Broker的配置文件server.properties中配置 delete.topic.enable=true 才能删除主题。</p>
</blockquote>



<h4 id="主题信息">主题信息</h4>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test_topic</span></code></pre>



<h4 id="添加分区">添加分区</h4>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">alter</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test_topic</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">3</span></code></pre>



<h4 id="创建主题">创建主题</h4>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">1</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test_topic</span></code></pre>



<h4 id="列出主题">列出主题</h4>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">list</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span></code></pre>

<blockquote>
  <p>topic 相关内容来源：<a href="http://ronnieroller.com/kafka/cheat-sheet" rel="nofollow">http://ronnieroller.com/kafka/cheat-sheet</a></p>
</blockquote>



<h3 id="命令那么多怎么记">命令那么多，怎么记？</h3>

<p>Kafka 的命令行工具提供了非常丰富的提示信息，所以只需要记住上面大概的几个用法，知道怎么写就行。当需要用到某个命令时，通过命令提示进行操作。</p>

<p>比如说，如何使用 kafka-configs.sh 查看主题（Topic）的配置？</p>

<p>首先，在命令行中输入<code>bin/kafka-configs.sh</code>，然后或输出下面的命令提示信息。</p>



<pre class="prettyprint"><code class=" hljs delphi">Add/Remove entity config <span class="hljs-keyword">for</span> a topic, client, user <span class="hljs-keyword">or</span> broker
Option                      Description                                        
------                      -----------                                        
--add-config &lt;<span class="hljs-keyword">String</span>&gt;       Key Value pairs <span class="hljs-keyword">of</span> configs <span class="hljs-keyword">to</span> add. Square brackets 
                              can be used <span class="hljs-keyword">to</span> group values which contain commas:
                              <span class="hljs-string">'k1=v1,k2=[v1,v2,v2],k3=v3'</span>. The following is a  
                              list <span class="hljs-keyword">of</span> valid configurations: <span class="hljs-keyword">For</span> entity_type    
                              <span class="hljs-string">'topics'</span>:                                        
                                cleanup.policy                                    
                                compression.<span class="hljs-keyword">type</span>                                  
                                delete.retention.ms                               
                                <span class="hljs-keyword">file</span>.delete.delay.ms                              
                                flush.messages                                    
                                flush.ms                                          
                                follower.replication.throttled.replicas           
                                <span class="hljs-keyword">index</span>.interval.bytes                              
                                leader.replication.throttled.replicas             
                                max.<span class="hljs-keyword">message</span>.bytes                                 
                                <span class="hljs-keyword">message</span>.format.version                            
                                <span class="hljs-keyword">message</span>.timestamp.difference.max.ms               
                                <span class="hljs-keyword">message</span>.timestamp.<span class="hljs-keyword">type</span>                            
                                min.cleanable.dirty.ratio                         
                                min.compaction.lag.ms                             
                                min.insync.replicas                               
                                preallocate                                       
                                retention.bytes                                   
                                retention.ms                                      
                                segment.bytes                                     
                                segment.<span class="hljs-keyword">index</span>.bytes                               
                                segment.jitter.ms                                 
                                segment.ms                                        
                                unclean.leader.election.enable                    
                            <span class="hljs-keyword">For</span> entity_type <span class="hljs-string">'brokers'</span>:                         
                                follower.replication.throttled.rate               
                                leader.replication.throttled.rate                 
                            <span class="hljs-keyword">For</span> entity_type <span class="hljs-string">'users'</span>:                           
                                producer_byte_rate                                
                                SCRAM-SHA-<span class="hljs-number">256</span>                                     
                                SCRAM-SHA-<span class="hljs-number">512</span>                                     
                                consumer_byte_rate                                
                            <span class="hljs-keyword">For</span> entity_type <span class="hljs-string">'clients'</span>:                         
                                producer_byte_rate                                
                                consumer_byte_rate                                
                            Entity types <span class="hljs-string">'users'</span> <span class="hljs-keyword">and</span> <span class="hljs-string">'clients'</span> may be specified
                              together <span class="hljs-keyword">to</span> update config <span class="hljs-keyword">for</span> clients <span class="hljs-keyword">of</span> a       
                              specific user.                                   
--alter                     Alter the configuration <span class="hljs-keyword">for</span> the entity.            
--delete-config &lt;<span class="hljs-keyword">String</span>&gt;    config keys <span class="hljs-keyword">to</span> remove <span class="hljs-string">'k1,k2'</span>                      
--describe                  List configs <span class="hljs-keyword">for</span> the given entity.                 
--entity-<span class="hljs-keyword">default</span>            <span class="hljs-keyword">Default</span> entity name <span class="hljs-keyword">for</span> clients/users (applies <span class="hljs-keyword">to</span>  
                              corresponding entity <span class="hljs-keyword">type</span> <span class="hljs-keyword">in</span> command line)       
--entity-name &lt;<span class="hljs-keyword">String</span>&gt;      Name <span class="hljs-keyword">of</span> entity (topic name/client id/user principal
                              name/broker id)                                  
--entity-<span class="hljs-keyword">type</span> &lt;<span class="hljs-keyword">String</span>&gt;      <span class="hljs-keyword">Type</span> <span class="hljs-keyword">of</span> entity (topics/clients/users/brokers)      
--force                     Suppress console prompts                           
--help                      Print usage information.                           
--zookeeper &lt;<span class="hljs-keyword">String</span>: urls&gt;  REQUIRED: The connection <span class="hljs-keyword">string</span> <span class="hljs-keyword">for</span> the zookeeper  
                              connection <span class="hljs-keyword">in</span> the form host:port. Multiple URLS  
                              can be given <span class="hljs-keyword">to</span> allow fail-over.</code></pre>

<p>从第一行可以看到这个命令可以修改 topic, client, user 或 broker 的配置。</p>

<p>如果要设置 topic，就需要设置 <code>entity-type</code> 为<code>topics</code>，输入如下命令：</p>



<pre class="prettyprint"><code class="language-shell hljs livecodeserver">&gt; bin/kafka-configs.sh <span class="hljs-comment">--entity-type topics</span>
Command must <span class="hljs-built_in">include</span> exactly <span class="hljs-constant">one</span> action: <span class="hljs-comment">--describe, --alter</span></code></pre>

<p>命令提示需要指定一个操作（不只是上面提示的两个操作），增加<code>--describe</code>试试：</p>



<pre class="prettyprint"><code class="language-shell hljs fsharp">&gt; bin/kafka-configs.sh --entity-<span class="hljs-class"><span class="hljs-keyword">type</span> <span class="hljs-title">topics</span> --<span class="hljs-title">describe</span></span>
[root@localhost kafka_2<span class="hljs-number">.11</span>-<span class="hljs-number">0.10</span><span class="hljs-number">.2</span><span class="hljs-number">.1</span>]# bin/kafka-configs.sh --entity-<span class="hljs-class"><span class="hljs-keyword">type</span> <span class="hljs-title">topics</span> --<span class="hljs-title">describe</span></span>
Missing required argument <span class="hljs-string">"[zookeeper]"</span></code></pre>

<p>继续增加 <code>--zookeeper</code>：</p>

<pre class="prettyprint"><code class="language-shell hljs ocaml">&gt; bin/kafka-configs.sh --entity-<span class="hljs-class"><span class="hljs-keyword">type</span> <span class="hljs-title">topics</span> --<span class="hljs-title">describe</span> --<span class="hljs-title">zookeeper</span> <span class="hljs-title">localhost</span>:2181</span>
Configs <span class="hljs-keyword">for</span> topic <span class="hljs-string">'__consumer_offsets'</span> are segment.bytes=<span class="hljs-number">104857600</span>,cleanup.policy=compact,compression.<span class="hljs-keyword">type</span>=producer</code></pre>

<p>由于没有指定主题名，这里显示了<code>__consumer_offsets</code>的信息。下面指定一个topic试试。</p>



<pre class="prettyprint"><code class="language-shell hljs brainfuck">&gt; <span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">configs</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">entity</span><span class="hljs-literal">-</span><span class="hljs-comment">type</span> <span class="hljs-comment">topics</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">localhost:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">entity</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">test</span>
<span class="hljs-comment">Configs</span> <span class="hljs-comment">for</span> <span class="hljs-comment">topic</span> <span class="hljs-comment">'test'</span> <span class="hljs-comment">are</span></code></pre>

<p>此时显示了<code>test</code>主题的信息，这里是空。</p>

<p>因为Kafka完善的命令提示，可以很轻松的通过提示信息来进行下一步操作，运用熟练后，基本上很快就能实现自己想要的命令。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
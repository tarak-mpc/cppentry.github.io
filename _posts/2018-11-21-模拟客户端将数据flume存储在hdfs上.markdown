---
layout:     post
title:      模拟客户端将数据flume存储在hdfs上
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_32967001/article/details/53174264				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.首先，需要在机器上安装flume工具 <br>
2.安装flume： <br>
 在文件夹 cd /usr/hdp/2.4.0.0-169/下创建flume文件目录 <br>
    tar zxvf apache-flume-1.6.0-bin.tar.gz  <br>
     mv apache-flume-1.6.0-bin flume <br>
     进入到flume文件的conf文件夹下边cd conf <br>
3.在conf文件夹下创建test1文件： <br>
  vim test1：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = netcat
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> =<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = logger

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p>a1.sinks.k1.type = logger–表示以日志的形式打印</p>

<p>返回到flume文件夹下cd .. <br>
  然后运行flume：命令如下： <br>
  bin/flume-ng agent –conf /conf/ –conf-file conf/test1 –name a1 -Dflume.root.logger=INFO,console <br>
  此时处于等待状态，图片 <br>
  <img src="https://img-blog.csdn.net/20161115180307009" alt="这里写图片描述" title="">从 <br>
此时需要在eclipse端启动一个客户端： <br>
在flume的avro  source</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.sxt.flume;

<span class="hljs-keyword">import</span> org.apache.commons.io.output.ThresholdingOutputStream;
<span class="hljs-keyword">import</span> org.apache.flume.Event;
<span class="hljs-keyword">import</span> org.apache.flume.EventDeliveryException;
<span class="hljs-keyword">import</span> org.apache.flume.api.RpcClient;
<span class="hljs-keyword">import</span> org.apache.flume.api.RpcClientFactory;
<span class="hljs-keyword">import</span> org.apache.flume.event.EventBuilder;
<span class="hljs-keyword">import</span> java.nio.charset.Charset;

<span class="hljs-comment">// 官方例程</span>
<span class="hljs-comment">/*
 * As of Flume 1.4.0, Avro is the default RPC protocol. 
 * The NettyAvroRpcClient and ThriftRpcClient implement the RpcClient interface. 
 * The client needs to create this object with the host and port of the target Flume agent, 
 * and can then use the RpcClient to send data into the agent. 
 * The following example shows how to use the Flume Client SDK API within a user’s data-generating application:
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">FlumeClientTest</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception{
        MyRpcClientFacade client = <span class="hljs-keyword">new</span> MyRpcClientFacade();
        <span class="hljs-comment">// Initialize client with the remote Flume agent's host and port</span>
        client.init(<span class="hljs-string">"192.168.1.241"</span>, <span class="hljs-number">44444</span>);

        <span class="hljs-comment">// Send 10 events to the remote Flume agent. That agent should be</span>
        <span class="hljs-comment">// configured to listen with an AvroSource.</span>
        <span class="hljs-comment">// String sampleData = "ERROR";</span>
        String sampleData = <span class="hljs-string">"Hello Flume!"</span>;
        System.out.println(<span class="hljs-string">"发送数据"</span>);
          <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; <span class="hljs-number">10</span>; i++) {
              client.sendDataToFlume(sampleData+<span class="hljs-string">"    "</span>+i);
          }   
        client.cleanUp();
    }
}

class MyRpcClientFacade {
    <span class="hljs-keyword">private</span> RpcClient client;
    <span class="hljs-keyword">private</span> String hostname;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> port;

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span>(String hostname, <span class="hljs-keyword">int</span> port) {
        <span class="hljs-comment">// Setup the RPC connection</span>
        <span class="hljs-keyword">this</span>.hostname = hostname;
        <span class="hljs-keyword">this</span>.port = port;
        <span class="hljs-keyword">this</span>.client = RpcClientFactory.getDefaultInstance(hostname, port);
        System.out.println(<span class="hljs-string">"建立连接"</span>);
        <span class="hljs-comment">// Use the following method to create a thrift client (instead of the</span>
        <span class="hljs-comment">// above line):</span>
        <span class="hljs-comment">// this.client = RpcClientFactory.getThriftInstance(hostname, port);</span>
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">sendDataToFlume</span>(String data) {
        <span class="hljs-comment">// Create a Flume Event object that encapsulates the sample data</span>
        Event event = EventBuilder.withBody(data, Charset.forName(<span class="hljs-string">"UTF-8"</span>));

        <span class="hljs-comment">// Send the event</span>
        <span class="hljs-keyword">try</span> {
            client.append(event);
        } <span class="hljs-keyword">catch</span> (EventDeliveryException e) {
            <span class="hljs-comment">// clean up and recreate the client</span>
            client.close();
            client = <span class="hljs-keyword">null</span>;
            client = RpcClientFactory.getDefaultInstance(hostname, port);
            <span class="hljs-comment">// Use the following method to create a thrift client (instead of</span>
            <span class="hljs-comment">// the above line):</span>
            <span class="hljs-comment">// this.client = RpcClientFactory.getThriftInstance(hostname, port);</span>
        }
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">cleanUp</span>() {
        <span class="hljs-comment">// Close the RPC connection</span>
        System.out.println(<span class="hljs-string">"断开连接"</span>);
        client.close();
    }

}</code></pre>

<p>项目的目录 <br>
<img src="https://img-blog.csdn.net/20161115180730057" alt="这里写图片描述" title="">f <br>
执行代码后开启flume的telnet 192.168.1.241 44444（复制节点） <br>
<img src="https://img-blog.csdn.net/20161115181116965" alt="这里写图片描述" title=""></p>

<p>二、使用avro  source将客户端输入的数据写到本地的文件夹下 <br>
vim test2：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = avro
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> =<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.241</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = file_roll
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.directory</span>=/home/sink
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">0</span>

<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p>a1.sinks.k1.type = file_roll <br>
a1.sinks.k1.sink.directory=/home/sink <br>
接收到的数据存放到了sink本地的文件夹下边 <br>
1）执行flume的test2 <br>
bin/flume-ng agent –conf /conf/ –conf-file conf/test2 –name a1 -Dflume.root.logger=INFO,console <br>
2）运行java代码 <br>
  去文件/home/sink/下查看存放的数据 <br>
  <img src="https://img-blog.csdn.net/20161115182031312" alt="这里写图片描述" title=""> <br>
  执行一次java代码就会多10条数据 <br>
  三、读取文件夹下的目录内容存放到hdfs上 <br>
  vim  test4：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># example.conf: A single-node Flume configuration</span>

<span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> =spooldir 
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.spoolDir</span>=/opt/data

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span>=hdfs 
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.245</span>:<span class="hljs-number">8020</span>/flume/data
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span>=<span class="hljs-number">10240000</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">0</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span>=<span class="hljs-number">0</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.idleTimeout</span>=<span class="hljs-number">5</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream


<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
</code></pre>

<p>首先，hdfs的集群可用，将存储到hdfs的active的节点上，文件夹/opt/data/的文件夹存在，并且有文件 <br>
<img src="https://img-blog.csdn.net/20161115182802331" alt="这里写图片描述" title=""> <br>
运行flume下的test4后查看hdfs目录下存在文件 <br>
<img src="https://img-blog.csdn.net/20161115183324067" alt="这里写图片描述" title=""> <br>
查看文件的内容可以使用hdfs <br>
<img src="https://img-blog.csdn.net/20161115183516271" alt="这里写图片描述" title=""> <br>
 四、将avro端读取的数据传到hdfs上边 <br>
 vim  test6：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor"># example.conf: A single-node Flume configuration</span>

<span class="hljs-preprocessor"># Name the components on this agent</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor"># Describe/configure the source</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> =avro
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> =<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor"># Describe the sink</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span>=hdfs 
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span>=hdfs://<span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.245</span>:<span class="hljs-number">8020</span>/flume/data/%<span class="hljs-built_in">Y</span>-%m-%d
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span>=<span class="hljs-number">10240000</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span>=<span class="hljs-number">0</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span>=<span class="hljs-number">0</span>
<span class="hljs-preprocessor">#a1.sinks.k1.hdfs.idleTimeout=5</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span>=DataStream
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.useLocalTimeStamp</span>=true



<span class="hljs-preprocessor"># Use a channel which buffers events in memory</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Bind the source and sink to the channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>

<p>a1.sinks.k1.hdfs.path=hdfs://192.168.1.245:8020/flume/data/%Y-%m-%d <br>
hdfs的数据按目录存放。 <br>
执行flume的test6文件，之后运行java客户端的代码，查看hdfs端 <br>
<img src="https://img-blog.csdn.net/20161115184036336" alt="这里写图片描述" title=""> <br>
执行一次java代码，就会增加10条数据</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
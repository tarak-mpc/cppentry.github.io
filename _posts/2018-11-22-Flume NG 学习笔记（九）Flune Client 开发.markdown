---
layout:     post
title:      Flume NG 学习笔记（九）Flune Client 开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/looklook5/article/details/40621209				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>文章内容还是来自官网<a href="http://flume.apache.org/FlumeDeveloperGuide.html" rel="nofollow">http://flume.apache.org/FlumeDeveloperGuide.html</a></p>
<p>由于在实际工作中，数据的生产方式极具多样性，Flume 虽然包含了一些内置的机制来采集数据，但是更多的时候用户更希望能将应用程序和flume直接相通。所以这边运行用户开发应用程序，通过IPC或者RPC连接flume并往flume发送数据。</p>
<h2>一、RPC client interface</h2>
<p>Flume的RpcClient实现了Flume的RPC机制。用户的应用程序可以很简单的调用Flume Client SDK的append(Event) 或者appendBatch(List&lt;Event&gt;) 方法发送数据，不用担心底层信息交换的细节。用户可以提供所需的event通过直接实现Event接口，例如可以使用简单的方便的实现SimpleEvent类或者使用EventBuilder的writeBody()静态辅助方法。</p>
<p>自Flume 1.4.0起，Avro是默认的RPC协议。NettyAvroRpcClient和ThriftRpcClient实现了RpcClient接口。实现中我们需要知道我们将要连接的目标flume agent的host和port用于创建client实例，然后使用RpcClient发送数据到flume agent。</p>
<p>官网给了一个Avro RPCclients的例子，这边直接拿来做实际测试例子。</p>
<p>这里我们把client.init("host.example.org",41414);</p>
<p>改成 client.init("192.168.233.128",50000);  与我们的主机对接</p>
<div style="background:#EEFFCC;">
<p align="left"></p>
<pre><code class="language-java">import org.apache.flume.Event;
import org.apache.flume.EventDeliveryException;
import org.apache.flume.api.RpcClient;
import org.apache.flume.api.RpcClientFactory;
import org.apache.flume.event.EventBuilder;
import java.nio.charset.Charset;
 
public class MyApp {
  public static voidmain(String[] args) {
   MyRpcClientFacade client = new MyRpcClientFacade();
   // Initializeclient with the remote Flume agent's host and port
//client.init("host.example.org",41414);
client.init("192.168.233.128",50000);
 
   // Send 10events to the remote Flume agent. That agent should be
   // configured tolisten with an AvroSource.
   String sampleData = "Hello Flume!";
   for (int i =0; i &lt; 10; i++) {
     client.sendDataToFlume(sampleData);
   }
 
   client.cleanUp();
  }
}
 
class MyRpcClientFacade {
  private RpcClient client;
  private String hostname;
  private int port;
 
  public void init(String hostname, int port) {
   // Setup the RPCconnection
   this.hostname = hostname;
   this.port = port;
   this.client = RpcClientFactory.getDefaultInstance(hostname, port);
   // Use thefollowing method to create a thrift client (instead of the above line):
    // this.client = RpcClientFactory.getThriftInstance(hostname, port);
  }
 
  public void sendDataToFlume(String data) {
   // Create aFlume Event object that encapsulates the sample data
   Event event = EventBuilder.withBody(data, Charset.forName("UTF-8"));
 
   // Send theevent
   try {
     client.append(event);
   } catch (EventDeliveryException e) {
     // clean up andrecreate the client
     client.close();
     client = null;
     client = RpcClientFactory.getDefaultInstance(hostname, port);
     // Use thefollowing method to create a thrift client (instead of the above line):
     // this.client =RpcClientFactory.getThriftInstance(hostname, port);
   }
  }
 
  public void cleanUp() {
   // Close the RPCconnection
   client.close();
  }
 
}</code></pre><span style="background-color:rgb(255,255,255);">这边代码不解释了，主要是将HelloFlume 发送10遍给flume,同时记得将flume 安装主目录下的lib 文件都添加进项目，才能正常运行程序。</span>
<p></p>
</div>
<p> </p>
<p>下面是代理配置：</p>
<p></p>
<pre><code class="language-html">#配置文件：avro_client_case20.conf
# Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1
 
# Describe/configure the source
a1.sources.r1.type = avro
a1.sources.r1.port = 50000
a1.sources.r1.host = 192.168.233.128
a1.sources.r1.channels = c1
 
# Describe the sink
a1.sinks.k1.channel = c1
a1.sinks.k1.type = logger
 
# Use a channel which buffers events inmemory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100</code></pre><br><br><p></p>
<p>这里要注意下，之前说了，在接收端需要AvroSource或者Thrift Source来监听接口。所以配置代理的时候要把a1.sources.r1.type 写成avro或者thrift</p>
<p><strong>#敲命令</strong></p>
<p><span style="color:rgb(0,176,80);">flume-ng agent -c conf -f conf/avro_client_case20.conf-n a1 -Dflume.root.logger=INFO,console</span><br></p>
<p>启动成功后</p>
<p> </p>
<p>在eclipse 里运行JAVA程序，当然也可以打包后在服务器上运行JAVA程序。</p>
<p><span style="color:#FF0000;"> </span></p>
<p><strong>#在启动源发送的代理终端查看console输出</strong></p>
<p><strong><img src="https://img-blog.csdn.net/20141030160225609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></strong></p>
<p></p>
<p>可以看到10条数据正常发送。</p>
<p>这里要说明下，开发代码中<span style="color:rgb(51,51,51);">client.</span><span style="color:#008080;">append</span><span style="color:#333333;">(</span><span style="color:rgb(51,51,51);">event)</span>不仅仅可以发送一条数据，也可以发送一个List(string) 的数据信息，也就是批量发送。这边就不做演示了。</p>
<h2 style="font-weight:bold;">二、Failover Client</h2>
<p>这个类包封装了Avro RPCclient的类默认提供故障处理能力。hosts采用空格分开host:port所代表的flume agent，构成一个故障处理组。这Failover RPC Client目前不支持thrift。如果当前选择的host agent有问题，这个failover client会自动负载到组中下一个host中。</p>
<p>下面是官网开发例子：</p>
<pre><code class="language-java">// Setup properties for the failover
Properties props = new Properties();
props.put("client.type", "default_failover");

// List of hosts (space-separated list of user-chosen host aliases)
props.put("hosts", "h1 h2 h3");

// host/port pair for each host alias
String host1 = "host1.example.org:41414";
String host2 = "host2.example.org:41414";
String host3 = "host3.example.org:41414";
props.put("hosts.h1", host1);
props.put("hosts.h2", host2);
props.put("hosts.h3", host3);

// create the client with failover properties
RpcClient client = RpcClientFactory.getInstance(props);
</code></pre>
<p>下面是测试的开发例子</p>
<pre><code class="language-java">import org.apache.flume.Event;
import org.apache.flume.EventDeliveryException;
import org.apache.flume.api.RpcClient;
import org.apache.flume.api.RpcClientFactory;
import org.apache.flume.event.EventBuilder;

import java.nio.charset.Charset;
import java.util.Properties;

public class Failover_Client {
	public static void main(String[] args) {
	    MyRpcClientFacade2 client = new MyRpcClientFacade2();
	    // Initialize client with the remote Flume agent's host and port
	    client.init();

	    // Send 10 events to the remote Flume agent. That agent should be
	    // configured to listen with an AvroSource.
	    String sampleData = "Hello Flume!";
	    for (int i = 0; i &lt; 10; i++) {
	      client.sendDataToFlume(sampleData);
	    }

	    client.cleanUp();
	  }
	}

	class MyRpcClientFacade2 {
	  private RpcClient client;
	  private String hostname;
	  private int port;

	  public void init() {
	    // Setup the RPC connection
	    // Use the following method to create a thrift client (instead of the above line):
	    // this.client = RpcClientFactory.getThriftInstance(hostname, port);
	 // Setup properties for the failover
	    Properties props = new Properties();
	    props.put("client.type", "default_failover");

	    // List of hosts (space-separated list of user-chosen host aliases)
	    props.put("hosts", "h1 h2 h3");

	    // host/port pair for each host alias
	    String host1 = "192.168.233.128:50000";
	    String host2 = "192.168.233.128:50001";
	    String host3 = "192.168.233.128:50002";
	    props.put("hosts.h1", host1);
	    props.put("hosts.h2", host2);
	    props.put("hosts.h3", host3);

	    // create the client with failover properties
	    client = RpcClientFactory.getInstance(props);
	  }

	  public void sendDataToFlume(String data) {
	    // Create a Flume Event object that encapsulates the sample data
	    Event event = EventBuilder.withBody(data, Charset.forName("UTF-8"));

	    // Send the event
	    try {
	      client.append(event);
	    } catch (EventDeliveryException e) {
	      // clean up and recreate the client
	      client.close();
	      client = null;
	      client = RpcClientFactory.getDefaultInstance(hostname, port);
	      // Use the following method to create a thrift client (instead of the above line):
	      // this.client = RpcClientFactory.getThriftInstance(hostname, port);
	    }
	  }

	  public void cleanUp() {
	    // Close the RPC connection
	    client.close();
	  }
}
</code></pre>
<p>这边代码设三个host用于故障转移，这里偷懒，用同一个主机的3个端口模拟。代码还是将Hello Flume 发送10遍给第一个flume代理，当第一个代理故障的时候，则发送给第二个代理，以顺序进行故障转移。</p>
<p>下面是代理配置沿用之前的那个，并对配置文件进行拷贝，</p>
<p><span style="color:#00B050;">cp avro_client_case20.conf avro_client_case21.conf</span></p>
<p><span style="color:#00B050;">cp avro_client_case20.conf avro_client_case22.conf</span></p>
<p>分别修改avro_client_case21.conf与avro_client_case22.conf中的</p>
<p>a1.sources.r1.port= 50001 与a1.sources.r1.port = 50002</p>
<p><strong>#敲命令</strong></p>
<p><span style="color:#00B050;">flume-ng agent -c conf -f conf/avro_client_case20.conf-n a1 -Dflume.root.logger=INFO,console</span></p>
<p><span style="color:#00B050;">flume-ng agent -c conf -f conf/avro_client_case21.conf-n a1 -Dflume.root.logger=INFO,console</span></p>
<p><span style="color:#00B050;">flume-ng agent -c conf -f conf/avro_client_case22.conf-n a1 -Dflume.root.logger=INFO,console</span></p>
<p><span style="color:#00B050;"> </span></p>
<p>启动成功后</p>
<p> </p>
<p>在eclipse 里运行JAVA程序Failover_Client.java，当然也可以打包后在服务器上运行JAVA程序。</p>
<p><strong>#在启动源发送的3个代理终端查看console输出</strong></p>
<p>我们可以看到第一个代理终端收到了，数据而其他2个终端没有数据。</p>
<img src="https://img-blog.csdn.net/20141030160324431?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><br><p>然后我们把第一个终端的进程关掉，再运行一遍client程序，然后会发现这个时候是发生到第二个终端中。当第二个终端也关闭的时候，再发送数据，则是发送到最后一个终端。这里我们可以看到，故障转移的代理主机转移是采用顺序序列的。</p>
<p> </p>
<h2>三、LoadBalancing RPC client</h2>
<p>Flume Client SDK也支持在多个host之间使用负载均衡的Rpc Client。这种类型的client带有一个通过空格分隔的host:port主机列表并构成了一个负载均衡组。这个client可以指定一个负载均衡的策略，既可以随机的选择一个配置的host，也可以循环选择一个host。当然你也可以自己编写一个类实现LoadBalancingRpcClient$HostSelector接口以至于用户可以使用自己编写的选择顺序。在这种情况下，用户自定义的类需要被指定为host-selector属性的值。LoadBalancing
 RPC Client当前不支持thrift。</p>
<p>如果开启了backoff，那么client失败将被放入黑名单中，只有过了被指定的超时之间之后这个被选择的失败的主机才会从黑名单中被排除。当超时到了，如果主机还是没有反应，那么这被认为是一个连续的失败并且超时时间会成倍的增长，以避免可能陷入对反应迟钝主机的长时间等待中。</p>
<p>这backoff的最大超时时间可以通过maxBackoff属性来配置，单位是毫秒。在默认情况下maxBackoff的值是30秒（在orderSelector类里面指定）。</p>
<p>下面是官网例子</p>
<pre><code class="language-java">// Setup properties for the load balancing
Properties props = new Properties();
props.put("client.type", "default_loadbalance");

// List of hosts (space-separated list of user-chosen host aliases)
props.put("hosts", "h1 h2 h3");

// host/port pair for each host alias
String host1 = "host1.example.org:41414";
String host2 = "host2.example.org:41414";
String host3 = "host3.example.org:41414";
props.put("hosts.h1", host1);
props.put("hosts.h2", host2);
props.put("hosts.h3", host3);

props.put("host-selector", "random"); // For random host selection
// props.put("host-selector", "round_robin"); // For round-robin host
//                                            // selection
props.put("backoff", "true"); // Disabled by default.

props.put("maxBackoff", "10000"); // Defaults 0, which effectively
                                  // becomes 30000 ms

// Create the client with load balancing properties
RpcClient client = RpcClientFactory.getInstance(props);
</code></pre>
<p>下面是测试的开发例子</p>
<pre><code class="language-java">import java.nio.charset.Charset;

import org.apache.flume.Event;
import org.apache.flume.EventDeliveryException;
import org.apache.flume.api.RpcClient;
import org.apache.flume.api.RpcClientFactory;
import org.apache.flume.event.EventBuilder;
import java.util.Properties;

public class Load_Client {
	public static void main(String[] args) {
	    MyRpcClientFacade3 client = new MyRpcClientFacade3();
	    // Initialize client with the remote Flume agent's host and port
	    client.init();

	    // Send 10 events to the remote Flume agent. That agent should be
	    // configured to listen with an AvroSource.
	    String sampleData = "Flume Load_Client";
	    for (int i = 0; i &lt; 10; i++) {
	      client.sendDataToFlume(sampleData);
	    }

	    client.cleanUp();
	  }
	}

	class MyRpcClientFacade3{
	  private RpcClient client;
	  private String hostname;
	  private int port;

	  public void init() {
		  Properties props = new Properties();
		  props.put("client.type", "default_loadbalance");

		  // List of hosts (space-separated list of user-chosen host aliases)
		  props.put("hosts", "h1 h2 h3");

		  // host/port pair for each host alias
		  String host1 = "192.168.233.128:50000";
		  String host2 = "192.168.233.128:50001";
		  String host3 = "192.168.233.128:50002";
		  props.put("hosts.h1", host1);
		  props.put("hosts.h2", host2);
		  props.put("hosts.h3", host3);

		  props.put("host-selector", "random"); // For random host selection
		  // props.put("host-selector", "round_robin"); // For round-robin host
//		                                              // selection
		  props.put("backoff", "true"); // Disabled by default.

		  props.put("maxBackoff", "10000"); // Defaults 0, which effectively
		                                    // becomes 30000 ms

		  // Create the client with load balancing properties
		  client = RpcClientFactory.getInstance(props);
	  }

	  public void sendDataToFlume(String data) {
	    // Create a Flume Event object that encapsulates the sample data
	    Event event = EventBuilder.withBody(data, Charset.forName("UTF-8"));

	    // Send the event
	    try {
	      client.append(event);
	    } catch (EventDeliveryException e) {
	      // clean up and recreate the client
	      client.close();
	      client = null;
	      client = RpcClientFactory.getDefaultInstance(hostname, port);
	      // Use the following method to create a thrift client (instead of the above line):
	      // this.client = RpcClientFactory.getThriftInstance(hostname, port);
	    }
	  }

	  public void cleanUp() {
	    // Close the RPC connection
	    client.close();
	  }
}
</code></pre>
<p>这里采用随机的负载均衡props.put("host-selector","random") 。测试的时候沿用之前的3个接受代理配置avro_client_case20.conf、avro_client_case21.conf和avro_client_case22.conf，并将他们起起来。</p>
<p><strong>#敲命令</strong></p>
<p><span style="color:#00B050;">flume-ng agent -c conf -f conf/avro_client_case20.conf-n a1 -Dflume.root.logger=INFO,console</span></p>
<p><span style="color:#00B050;">flume-ng agent -c conf -f conf/avro_client_case21.conf-n a1 -Dflume.root.logger=INFO,console</span></p>
<p><span style="color:#00B050;">flume-ng agent -c conf -f conf/avro_client_case22.conf-n a1 -Dflume.root.logger=INFO,console</span></p>
<p><span style="color:#00B050;"> </span></p>
<p>启动成功后</p>
<p> </p>
<p>在eclipse 里运行JAVA程序Failover_Client.java，当然也可以打包后在服务器上运行JAVA程序。</p>
<p><strong>#在启动源发送的3个代理终端查看console输出</strong></p>
<p>下面是Host1，收到了2条数据</p>
<img src="https://img-blog.csdn.net/20141030160534093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p>下面是Host2，收到了2条数据</p>
<img src="https://img-blog.csdn.net/20141030160517375?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p>下面是Host3，收到了6条数据。</p>
<img src="https://img-blog.csdn.net/20141030160554035?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<p>可以看到我们开发例子中，host-selector选择的是随机，因此程序也是随机发送数据。下面我们测试轮询round_robin选项。</p>
<p>程序里我们修改这句</p>
<p align="left"><span style="color:#3F7F5F;">//props.put("host-selector","random"); // For random host selection</span></p>
<p><strong><span style="color:#7F0055;">props.put("</span></strong><span style="color:#2A00FF;">host-selector</span><strong><span style="color:#7F0055;">", "</span></strong><span style="color:#2A00FF;">round_robin</span>"<strong><span style="color:#7F0055;">);</span></strong><span style="color:#3F7F5F;">//
 Forround-robin host</span></p>
<p>再运行Java 程序</p>
<p>下面是Host1，收到了4条数据</p>
<img src="https://img-blog.csdn.net/20141030160712968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<p>下面是Host2，收到了3条数据</p>
<img src="https://img-blog.csdn.net/20141030160651038?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<p>同样Host3，收到了3条数据，这边就不放图了。轮询就是按照顺序放图。</p>
<br>            </div>
                </div>
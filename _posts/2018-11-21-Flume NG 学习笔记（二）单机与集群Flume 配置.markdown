---
layout:     post
title:      Flume NG 学习笔记（二）单机与集群Flume 配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/looklook5/article/details/40375497				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span>下面的内容基本来自官网：<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a>英语好的就不用看我这里来。。。</p>
<p><span></span>本文使用的是最新版本的apache flume 1.5，安装完Flume然后测试下Flume是否可以用，在Flume目录下用以下语句测试：</p>
<p><span style="color:#00B050;"><span></span>bin/flume-ng agent -n$agent_name -c conf -f conf/flume-conf.properties.template</span></p>
<p><span></span>结果如图显示：</p>
<p><img src="https://img-blog.csdn.net/20141022140540140?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p></p>
<p>Ok,我们接下去看下面常用架构、功能配置示例</p>
<h2>一、最简单的单一代理Flume 配置</h2>
<p> </p>
<p>下面是配置文件：</p>
<p></p><pre><code class="language-html">#文件名：single_case1.conf.conf
#配置内容：
#single_case1.conf.conf: A single-node Flume configuration
#Name the components on this agent
a1.sources= r1
a1.sinks= k1
a1.channels= c1
 
#Describe/configure the source
a1.sources.r1.type= netcat
a1.sources.r1.bind= localhost
a1.sources.r1.port= 44444
 
#Describe the sink
a1.sinks.k1.type= logger
 
#Use a channel which buffers events in memory
a1.channels.c1.type= memory
a1.channels.c1.capacity= 1000
a1.channels.c1.transactionCapacity= 100
 
#Bind the source and sink to the channel
a1.sources.r1.channels= c1
a1.sinks.k1.channel= c1</code></pre><br><br><p><strong><span style="background:#FFFF00;">说明下，这里所有的例子都是将配置文件放到 $FLUME_HOME/conf </span>
目录下，后面就不赘述了。</strong></p>
<p><strong><span style="background:#D9D9D9;"> </span></strong></p>
<p><strong>#敲命令</strong></p>
<p><span style="color:#00B050;">flume-ng agent -cconf -f conf/single_case1.conf -n a1 -Dflume.root.logger=INFO,console</span></p>
<p> </p>
<p><strong>#参数命令</strong></p>
<p><span style="color:#00B0F0;">-c conf </span><span style="color:#00B0F0;">指定配置目录为</span><span style="color:#00B0F0;">conf</span></p>
<p><span style="color:#00B0F0;">-f conf/single_case1.conf</span><span style="color:#00B0F0;">指定配置文件为</span><span style="color:#00B0F0;">conf/single_case1.conf</span></p>
<p><span style="color:#00B0F0;">-n a1 </span><span style="color:#00B0F0;">指定</span><span style="color:#00B0F0;">agent</span><span style="color:#00B0F0;">名字为</span><span style="color:#00B0F0;">a1,</span><span style="color:#00B0F0;">需要与</span><span style="color:#00B0F0;">case1_example.conf</span><span style="color:#00B0F0;">中的一致</span></p>
<p><span style="color:#00B0F0;">-Dflume.root.logger=INFO,console</span><span style="color:#00B0F0;">指定</span><span style="color:#00B0F0;">DEBUF</span><span style="color:#00B0F0;">模式在</span><span style="color:#00B0F0;">console</span><span style="color:#00B0F0;">输出</span><span style="color:#00B0F0;">INFO</span><span style="color:#00B0F0;">信息</span></p>
<p><span style="color:#00B0F0;">具体参数命令请通过</span><span style="color:#00B0F0;">flume-nghelp</span><span style="color:#00B0F0;">查看</span></p>
<p><span style="color:#00B0F0;"> </span></p>
<p><strong>#然后在另一个终端进行测试</strong></p>
<p><span style="color:#FF0000;">telnet 127.0.0.1 44444</span></p>
<img src="https://img-blog.csdn.net/20141022140643375?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p>然后会看在之前启动的终端查看console输出到如下：</p>
<img src="https://img-blog.csdn.net/20141022141103885?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p>这里会发现消息hello world! 输出了，而hello world! hello world!hello world!则被拦截了。因为在配置文件中，我们选择的输出方式为：a1.sinks.k1.type= logger</p>
<p>，即console输出，flume-ng针对logger是只显示16个字节的，剩下的都被sink截了。下面是源码<br>
在LoggerSink.java中：</p>
<p></p><pre><code class="language-java">if(event != null) {
       if (logger.isInfoEnabled()) {
         logger.info("Event: " + EventHelper.dumpEvent(event));
       }
}</code></pre><br><p>我们去看EventHelper.java的dumpEvent方法：</p>
<p></p><pre><code class="language-java">privatestatic final int DEFAULT_MAX_BYTES = 16;
publicstatic String dumpEvent(Event event) {
   return dumpEvent(event, DEFAULT_MAX_BYTES);
}
 
publicstatic String dumpEvent(Event event, int maxBytes) {
   StringBuilder buffer = new StringBuilder();
   if (event == null || event.getBody() == null) {
     buffer.append("null");
   } else if (event.getBody().length == 0) {
     // do nothing... in this case, HexDump.dump() will throw anexception
   } else {
     byte[] body = event.getBody();
     byte[] data = Arrays.copyOf(body, Math.min(body.length,maxBytes));
     ByteArrayOutputStream out = new ByteArrayOutputStream();
     try {
       HexDump.dump(data, 0, out, 0);
       String hexDump = new String(out.toByteArray());
       // remove offset since it's not relevant for such a smalldataset
       if(hexDump.startsWith(HEXDUMP_OFFSET)) {
         hexDump =hexDump.substring(HEXDUMP_OFFSET.length());
       }
       buffer.append(hexDump);
     } catch (Exception e) {
      if(LOGGER.isInfoEnabled()) {
        LOGGER.info("Exception while dumpingevent", e);
      }
       buffer.append("...Exception while dumping:").append(e.getMessage());
     }
     String result = buffer.toString();
     if(result.endsWith(EOL) &amp;&amp; buffer.length() &gt;EOL.length()) {
       buffer.delete(buffer.length() - EOL.length(),buffer.length()).toString();
     }
   }
   return "{ headers:" + event.getHeaders() + " body:"+ buffer + " }";
 }</code></pre><br><p>不难看出，在event处理过程中，发生了数据截取操作。</p>
<p align="left">Ok，进入下一个环节。</p>
<p> </p>
<h2>二、“集群”代理Flume 配置</h2>
<img src="https://img-blog.csdn.net/20141022141145881?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p><span></span>这里集群的概念是多台机器的管理，最简单的就是两台机器一台代理主机从数据源获取数据，然后将数据在传送到另一台主机上，进行输出。这样做的意义是，一个业务多数据源的时候，我们可以对每个数据源设置代理，然后将它们汇总到一台代理主机上进行输出。</p>
<p><span></span>下面实现最简单的集群配置，即两个代理，一台接受数据源数据的代理将数据推送到汇总的代理，而汇总的代理再将数据输出。因此这两台主机分别是push,pull</p>
<p><span></span>根据上图需要用AVRO RPC通信，因此推数据sinks类型与拉数据的sources的类型都是avro 。而拉数据代理的数据源，我们用前文讲的Spool Source 形式来处理，这里我们预先建好目录与文件，test.log</p>
<img src="https://img-blog.csdn.net/20141022140833562?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p>下面设置推代理主机的flume配置文件：</p>
<p></p><pre><code class="language-html">#推数据代理的配置文件push.conf
#Name the components on this agent
a2.sources= r1
a2.sinks= k1
a2.channels= c1
 
#Describe/configure the source
a2.sources.r1.type= spooldir
a2.sources.r1.spoolDir= /tmp/logs
a2.sources.r1.channels= c1
 
#Use a channel which buffers events in memory
a2.channels.c1.type= memory
a2.channels.c1.keep-alive= 10
a2.channels.c1.capacity= 100000
a2.channels.c1.transactionCapacity= 100000
 
#Describe/configure the source
a2.sinks.k1.type= avro
a2.sinks.k1.channel= c1
a2.sinks.k1.hostname= pull
a2.sinks.k1.port= 4444</code></pre><br><br><p>下面设置汇总代理主机的flume配置文件：</p>
<p></p><pre><code class="language-html">#汇总数据代理的配置文件pull.conf
#Name the components on this agent
a1.sources= r1
a1.sinks= k1
a1.channels= c1
 
#Describe/configure the source
a1.sources.r1.type= avro
a1.sources.r1.channels= c1
a1.sources.r1.bind= pull
a1.sources.r1.port= 44444
 
#Describe the sink
a1.sinks.k1.type= logger
 a1.sinks.k1.channel = c1
 
#Use a channel which buffers events in memory
a1.channels.c1.type= memory
a1.channels.c1.keep-alive= 10
a1.channels.c1.capacity= 100000
a1.channels.c1.transactionCapacity= 100000</code></pre><br><br><p><span></span>虽然Spool Source是非实时的，但由于数据量少，处理还是很快的，因此我们只能先启动pull代理。</p>
<p><strong>#敲命令</strong></p>
<p><span style="color:#00B050;">flume-ng agent -c conf -f conf/pull.conf -n a1 -Dflume.root.logger=INFO,console</span></p>
<p><span style="color:#00B050;"><img src="https://img-blog.csdn.net/20141022141334426?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><span style="color:#00B050;"></span></p>
<p><span></span>上图显示成功。</p>
<p><span></span>先后去启动push主机的flume</p>
<p><strong>#敲命令</strong></p>
<p><span style="color:rgb(0,176,80);">flume-ng agent -n a2 -c conf -f conf/push.conf -Dflume.root.logger=INFO,console</span></p>
<img src="https://img-blog.csdn.net/20141022141247064?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p></p>
<p><span></span>查看pull主机的状态，发现数据已经传过来了。</p>
<p>然后会过去看push主机的文件</p>
<img src="https://img-blog.csdn.net/20141022141420945?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbG9va2xvb2s1/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p><span></span>已经加上后缀名.COMPLETED。这与前文说的是一致的。</p>
<p> </p>
<p><span></span>下面只要将新数据存入到目录/tmp/logs，push主机就会将数据发送到pull主机输出，并修改新数据文件的文件名。</p>
<br><p><br></p>
            </div>
                </div>
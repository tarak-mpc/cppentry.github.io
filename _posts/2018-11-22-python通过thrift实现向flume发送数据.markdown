---
layout:     post
title:      python通过thrift实现向flume发送数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lijinqi1987/article/details/77892491				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'Microsoft YaHei';font-size:16px;color:rgb(51,51,51);text-align:justify;">Apahce Thrift</span><span style="font-family:'Microsoft YaHei';font-size:16px;color:rgb(51,51,51);text-align:justify;">是</span><span style="font-family:'Microsoft YaHei';font-size:16px;color:rgb(51,51,51);text-align:justify;">FaceBook</span><span style="font-family:'Microsoft YaHei';font-size:16px;color:rgb(51,51,51);text-align:justify;">实现的一种高效的、支持多种语言的远程服务调用的框架。Source是负责接收数据到Flume
 Agent的组件，可以从其他系统中接收数据。Flume有Java Thrift RPC客户端，是FLume SDK的一部分，Thrift Source是多线程，高性能的Thrift服务器，Flume的Thrift接口定义（IDL）语言如下：</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:16px;color:rgb(51,51,51);text-align:justify;"></span></p>
<pre><code class="language-cpp">namespace java org.apache.flume.thrift

struct ThriftFlumeEvent {
  1: required map &lt;string, string&gt; headers,
  2: required binary body,
}

enum Status {
  OK,
  FAILED,
  ERROR,
  UNKNOWN
}

service ThriftSourceProtocol {
  Status append(1: ThriftFlumeEvent event),
  Status appendBatch(1: list&lt;ThriftFlumeEvent&gt; events),
}
</code></pre><br><p></p>
<p><span style="font-family:'Microsoft YaHei';font-size:16px;color:rgb(51,51,51);text-align:justify;">存放在在flume源码包apache-flume-1.7.0-src\flume-ng-sdk\src\main\thrift\flume.thrift</span></p>
<p><br></p>
<p style="text-align:justify;"><span style="font-family:'Microsoft YaHei';color:#333333;"><span style="font-size:16px;">首先使用thrift文件生成客户端模块基础代码</span></span></p>
<p style="text-align:justify;"><span style="font-family:'Microsoft YaHei';color:#333333;"><span style="font-size:16px;"></span></span></p>
<pre><code class="language-plain">thrift --gen py flume.thrit </code></pre>可以得到一个<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;">gen-py的目录，将文件夹重命名为genpy，放到python的import路径下</span>
<p></p>
<p style="text-align:justify;"><span style="color:#333333;"><span style="font-family:Arial;"><span style="font-size:14px;">注：</span></span></span></p>
<p style="text-align:justify;"><span style="color:#333333;"><span style="font-family:Arial;"><span style="font-size:14px;"></span></span></span></p>
<ul style="color:rgb(63,63,63);font-family:'microsoft yahei';font-size:15px;"><li>
<p>
<span>sudo apt-get install 安装的package存放在 /usr/lib/python2.7/dist-packages目录中</span></p>
</li><li>
<p>
<span>pip 或者 easy_install安装的package存放在/usr/local/lib/python2.7/dist-packages目录</span></p>
</li><li>
<p>
<span>手动从源代码安装的package存放在site-packages目录中</span></p>
</li></ul><br>
客户端python源码如下flume.py：
<p></p>
<p style="text-align:justify;"><span style="color:#333333;"></span></p>
<pre><code class="language-python">#coding=utf-8  
''''' 
Created on 2013-07-18 
 
 
@author: Felix 
'''  
from genpy.flume import ThriftSourceProtocol  
from genpy.flume.ttypes import ThriftFlumeEvent  
  
  
from thrift.transport import TTransport, TSocket  
from thrift.protocol import TCompactProtocol  
  
  
class _Transport(object):  
    def __init__(self, thrift_host, thrift_port, timeout=None, unix_socket=None):  
        self.thrift_host = thrift_host  
        self.thrift_port = thrift_port  
        self.timeout = timeout  
        self.unix_socket = unix_socket  
          
        self._socket = TSocket.TSocket(self.thrift_host, self.thrift_port, self.unix_socket)  
        self._transport_factory = TTransport.TFramedTransportFactory()  
        self._transport = self._transport_factory.getTransport(self._socket)  
          
    def connect(self):  
        try:  
            if self.timeout:  
                self._socket.setTimeout(self.timeout)  
            if not self.is_open():  
                self._transport = self._transport_factory.getTransport(self._socket)  
                self._transport.open()  
        except Exception, e:  
            print(e)  
            self.close()  
      
    def is_open(self):  
        return self._transport.isOpen()  
      
    def get_transport(self):  
        return self._transport  
      
    def close(self):  
        self._transport.close()  
          
class FlumeClient(object):  
    def __init__(self, thrift_host, thrift_port, timeout=None, unix_socket=None):  
        self._transObj = _Transport(thrift_host, thrift_port, timeout=timeout, unix_socket=unix_socket)  
        self._protocol = TCompactProtocol.TCompactProtocol(trans=self._transObj.get_transport())  
        self.client = ThriftSourceProtocol.Client(iprot=self._protocol, oprot=self._protocol)  
        self._transObj.connect()  
          
    def send(self, event):  
        try:  
            self.client.append(event)  
        except Exception, e:  
            print(e)  
        finally:  
            self._transObj.connect()  
      
    def send_batch(self, events):  
        try:  
            self.client.appendBatch(events)  
        except Exception, e:  
            print(e)  
        finally:  
            self._transObj.connect()  
      
    def close(self):  
        self._transObj.close()  
      
if __name__ == '__main__':  
    import random  
    flume_client = FlumeClient('127.0.0.1', 9413)  
    event = ThriftFlumeEvent({'a':'hello', 'b':'world'}, 'events under hello world2')  
    events = [ThriftFlumeEvent({'a':'hello', 'b':'world'}, 'events under hello world%s' % random.randint(0, 1000)) for _ in range(100)]  
      
    flume_client.send(event)  
    flume_client.send_batch(events)  
    flume_client.close() 
</code></pre><br><br><p></p>
<p style="text-align:justify;"><span style="color:#333333;">测试：</span></p>
<p style="text-align:justify;"><span style="color:#333333;">1、</span><span style="color:rgb(51,51,51);">在本机启动flume，配置thrift souece端口9413，sink方式为logger，启动flume，配置文件如下：</span></p>
<p style="text-align:justify;"></p>
<pre><code class="language-plain">agent.sources = r1
agent.sinks = k1
agent.channels = c1

# Describe/configure the source
agent.sources.r1.type = thrift
agent.sources.r1.port = 9413
agent.sources.r1.bind = 0.0.0.0
agent.sources.r1.threads = 50

# Use a channel which buffers events in file
agent.channels.c1.type = memory
agent.channels.c1.capacity = 10000000
agent.channels.c1.transactionCapacity= 2000

# Describe the sink k1
agent.sinks.k1.type = logger

# Bind the source and sink to the channel
agent.sources.r1.channels = c1
agent.sinks.k1.channel = c1</code></pre><br>
2、启动flume<br><pre><code class="language-plain">bin/flume-ng agent --conf ./conf/ -f conf/thrift_logger.conf -n agent -Dflume.root.logger=INFO,console &amp;</code></pre>
<p></p>
<p style="text-align:justify;">3、执行flume.py 在flume端看到打印如下：</p>
<p style="text-align:justify;"></p>
<pre><code class="language-plain">2017-09-08 12:08:06,444 (lifecycleSupervisor-1-3) [INFO - org.apache.flume.source.ThriftSource.getProtocolFactory(ThriftSource.java:302)] Using TCompactProtocol
2017-09-08 12:08:07,450 (lifecycleSupervisor-1-3) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:120)] Monitored counter group for type: SOURCE, name: r1: Successfully registered new MBean.
2017-09-08 12:08:07,455 (lifecycleSupervisor-1-3) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:96)] Component type: SOURCE, name: r1 started
2017-09-08 12:08:07,456 (lifecycleSupervisor-1-3) [INFO - org.apache.flume.source.ThriftSource.start(ThriftSource.java:250)] Started Thrift source.
2017-09-08 12:08:51,455 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{a=hello, b=world} body: 65 76 65 6E 74 73 20 75 6E 64 65 72 20 68 65 6C events under hel }
2017-09-08 12:08:51,458 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{a=hello, b=world} body: 65 76 65 6E 74 73 20 75 6E 64 65 72 20 68 65 6C events under hel }
2017-09-08 12:08:51,464 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{a=hello, b=world} body: 65 76 65 6E 74 73 20 75 6E 64 65 72 20 68 65 6C events under hel }
2017-09-08 12:08:51,472 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{a=hello, b=world} body: 65 76 65 6E 74 73 20 75 6E 64 65 72 20 68 65 6C events under hel }
</code></pre><br><br><p></p>
<p style="text-align:justify;"><span style="color:rgb(51,51,51);"><br></span></p>
<p style="text-align:justify;"><span style="color:rgb(51,51,51);">参考：</span></p>
<p style="text-align:justify;"><span style="color:rgb(51,51,51);">http://blog.csdn.net/cnweike/article/details/9372161<br></span></p>
<p style="text-align:justify;"><span style="color:rgb(51,51,51);"><br></span></p>
            </div>
                </div>
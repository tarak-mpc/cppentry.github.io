---
layout:     post
title:      单节点搭建flume之exec和spooldir
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Technology_2016/article/details/51132471				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>一、flume安装</p>
<p>1、上传flume包到master并解压缩<br></p>
<p></p>
<pre><code class="language-html">tar -zxvf flume-ng-1.2.0-cdh3u5.tar.gz </code></pre>2、添加flume路径到.bash_profile中（可有可无，只为方便）<br><pre><code class="language-html">export FLUME_HOME=/home/hadoop/flume-ng-1.2.0-cdh3u5
export FLUME_CONF_DIR=$FLUME_HOME/conf</code></pre>3、修改flume配置文件（文件位置：flume-ng-1.2.0-cdh3u5/conf/）
<p></p>
<p>cp flume-conf.properties.template flume-conf.properties<br>
编辑flume-conf.properties<br></p>
<pre><code class="language-html">a1.sources = r1
a1.sinks = k1
a1.channels = c1

# Describe/configure the source
a1.sources.r1.type = netcat
a1.sources.r1.bind = hd2
a1.sources.r1.port = 44444

# Describe the sink
a1.sinks.k1.type = logger

# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1</code></pre>4、运行flume（flume-ng-1.2.0-cdh3u5/下运行）<br><pre><code class="language-html">bin/flume-ng agent --conf /home/hadoop/flume-ng-1.2.0-cdh3u5/conf/ --conf-file conf/flume-conf.properties --name a1 -Dflume.root.logger=INFO,console</code></pre>5、上面界面保留，另开一个界面输入
<p></p>
<p></p>
<pre><code class="language-html">telnet hadoop110（主机名） 44444
hello  world
</code></pre>6、可查看到如下结果，证明成功
<p></p>
<p><img src="" alt=""><br><span style="color:#FF0000;">以上配置文件仅为flume测试使用，企业中切勿进行。</span><br></p>
<p>二、企业flume之exec---常用于日志的抽取</p>
<p>1、只需要在配置文件conf中添加一个配置文件即可，文件名无要求（我这里的叫flume.conf ）</p>
<p></p>
<pre><code class="language-html">agent.sources = reader

agent.channels = memoryChannel

agent.sinks = avro-forward-sink

agent.sources.reader.type = exec

agent.sources.reader.command =&lt;span style="color:#FF0000;"&gt; tail -f /home/flume/sf&lt;/span&gt;

agent.sources.reader.logStdErr = true

agent.sources.reader.restart = true
agent.sources.reader.channels = memoryChannel
agent.sinks.avro-forward-sink.type = avro

agent.sinks.avro-forward-sink.hostname = &lt;span style="color:#FF0000;"&gt;192.168.0.13&lt;/span&gt;

agent.sinks.avro-forward-sink.port = 44444
agent.sinks.avro-forward-sink.channel = memoryChannel
agent.channels.memoryChannel.type = memory
agent.channels.memoryChannel.capacity = 10000

agent.channels.memoryChannel.transactionCapacity = 100</code></pre>agent.sources.reader.command = 源地址    agent.sinks.avro-forward-sink.hostname =目标地址   其他参数可以查看其他文档<br>
2、运行<pre><code class="language-html">bin/flume-ng agent -n agent -c conf -f /usr/local/flume/conf/flume.conf -Dflume.root.logger=INFO,console</code></pre>3、上面的窗口不要关闭，在另一个窗口中传入数据到/home/flume/sf中，你会发现上面的窗口有显示出你抽取的文件名称，则成功
<p></p>
<p>三、企业flume之spooldir-----常用于文件的抽取</p>
<p>同二，只需要新建一个配置文件即可，并加入一下内容</p>
<p></p>
<pre><code class="language-html">agent.sources = reader
agent.channels = fileChannel
agent.sinks = avro-forward-sink


agent.sources.reader.type=spooldir
agent.sources.reader.spoolDir=&lt;span style="color:#FF0000;"&gt;/home/history&lt;/span&gt;
agent.sources.reader.channels=fileChannel
agent.sources.reader.fileHeader = false
agent.sources.reader.interceptors = i1
agent.sources.reader.interceptors.i1.type = timestamp
agent.sources.reader.channels = fileChannel

agent.sinks.avro-forward-sink.type = avro
agent.sinks.avro-forward-sink.hostname =&lt;span style="color:#FF0000;"&gt; 192.168.0.12&lt;/span&gt;
agent.sinks.avro-forward-sink.port = 44444
agent.sinks.avro-forward-sink.channel = fileChannel


agent.channels.fileChannel.type=file
agent.channels.fileChannel.checkpointDir=/home/test
agent.channels.fileChannel.dataDirs=/home/test1</code></pre><br>
运行
<p></p>
<p></p>
<pre><code class="language-html">bin/flume-ng agent -n agent -c conf -f /usr/local/flume/conf/history.conf -Dflume.root.logger=INFO,console</code></pre><br>
查看结果同上，最终结果也可以在192.168.0.12上进行查看
<p></p>
<p><br></p>
<p>四、flume总结</p>
<p>1、flume抽取具有很大的灵活性，只需适当的修改配置文件中的内容即可</p>
<p>2、flume不能抽取pdf，jpg等文件，会报错<br></p>
            </div>
                </div>
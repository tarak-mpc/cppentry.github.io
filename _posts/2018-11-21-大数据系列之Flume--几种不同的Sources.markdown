---
layout:     post
title:      大数据系列之Flume--几种不同的Sources
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mengfanzhundsc/article/details/62046586				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                1.flume概念
<p>flume是分布式的，可靠的，高可用的，用于对不同来源的大量的日志数据进行有效收集、聚集和移动，并以集中式的数据存储的系统。</p>
<p>flume目前是apache的一个顶级项目。</p>
<p>flume需要java运行环境，要求java1.6以上，推荐java1.7.</p>
<p>将下载好的flume安装包解压到指定目录即可。</p>
<h2>2.flume中的重要模型</h2>
<h3>2.1.1.flume Event：</h3>
<p>flume 事件，被定义为一个具有有效荷载的字节数据流和可选的字符串属性集。</p>
<h3>2.1.2.flume Agent：</h3>
<p>flume 代理，是一个进程承载从外部源事件流到下一个目的地的过程。包含source channel 和 sink。</p>
<h3>2.1.3.Source</h3>
<p>数据源，消耗外部传递给他的事件，外部源将数据按照flume Source 能识别的格式将Flume 事件发送给flume Source。</p>
<h3>2.1.4.Channel</h3>
<p>数据通道，是一个被动的存储，用来保持事件，直到由一个flume Sink消耗。</p>
<h3>2.1.5.Sink</h3>
<p>数据汇聚点，代表外部数据存放位置。发送flume event到指定的外部目标</p>
<h2>2.2.          flume流动模型</h2>
<p> <img src="http://images2015.cnblogs.com/blog/941878/201703/941878-20170313173352495-518397077.png" alt="" width="689" height="255"></p>
<p> </p>
<p> </p>
<h2>2.3.          flume的特点</h2>
<h3>2.3.1.  复杂流动性</h3>
<p>Flume允许用户进行多级流动到最终目的地，也允许扇出流（一到多）、扇入流(多到一)的、故障转移和失败处理。</p>
<h3>2.3.2.  可靠性</h3>
<p>事务性的数据传递，保证了数据的可靠性。</p>
<h3>2.3.3.  可恢复性</h3>
<p>通道可以以内存或文件的方式实现，内存更快，但是不可恢复，而文件虽然比较慢但提供了可恢复性。</p>
<p> </p>
<h1>     入门案例</h1>
<p> </p>
<p>1.首先编写一个配置文件：</p>
<div class="cnblogs_Highlighter">
<pre class="brush:bash;gutter:true;">＃example.conf：单节点Flume配置
＃命名Agent a1的组件
a1.sources  =  r1
a1.sinks  =  k1
a1.channels  =  c1

＃描述/配置Source
a1.sources.r1.type  =  netcat
a1.sources.r1.bind  =  0.0.0.0<br>a1.sources.r1.port  =  44444

＃描述Sink
a1.sinks.k1.type  =  logger

＃描述内存Channel
a1.channels.c1.type  =  memory
a1.channels.c1.capacity  =  1000 
a1.channels.c1.transactionCapacity  =  100

＃为Channle绑定Source和Sink
a1.sources.r1.channels  =  c1
a1.sinks.k1.channel  =  c1
        
</pre>
</div>
<p> 2.通过flume的工具启动agent</p>
<div class="cnblogs_Highlighter">
<pre class="brush:bash;gutter:true;">$ bin/flume-ng agent --conf conf --conf-file example.conf --name a1 -Dflume.root.logger=INFO,console
</pre>
</div>
<p> 3、发送数据</p>
<p>在windows中通过telnet命令连接flume所在机器的44444端口发送数据。</p>
<p> </p>
<h1>4.     Source详解</h1>
<p>现在介绍几种比较重要的Source</p>
<p> </p>
<h2>4.1.    Avro Source</h2>
<p> </p>
<p>监听AVRO端口来接受来自外部AVRO客户端的事件流。利用Avro Source可以实现多级流动、扇出流、扇入流等效果。另外也可以接受通过flume提供的Avro客户端发送的日志信息。</p>
<p> </p>
<h3>4.1.1.   Avro Source属性说明</h3>
<p>!channels  –   </p>
<p>!type  –   类型名称，"AVRO"</p>
<p>!bind  –   需要监听的主机名或IP</p>
<p>!port  –   要监听的端口</p>
<p>threads    –   工作线程最大线程数</p>
<p>selector.type     </p>
<p>selector.*     </p>
<p>interceptors  –   空格分隔的拦截器列表</p>
<p>interceptors.*        </p>
<p>compression-type  none   压缩类型，可以是“none”或“default”，这个值必须和AvroSource的压缩格式匹配</p>
<p>sslfalse  是否启用ssl加密，如果启用还需要配置一个“keystore”和一个“keystore-password”。</p>
<p>keystore   –   为SSL提供的java密钥文件所在路径。</p>
<p>keystore-password–   为SSL提供的java密钥文件 密码。</p>
<p>keystore-typeJKS密钥库类型可以是“JKS”或“PKCS12”。</p>
<p>exclude-protocolsSSLv3  空格分隔开的列表，用来指定在SSL / TLS协议中排除。SSLv3将总是被排除除了所指定的协议。</p>
<p>ipFilter   false  如果需要为netty开启ip过滤，将此项设置为true</p>
<p>ipFilterRules–   定义netty的ip过滤设置表达式规则</p>
<p> </p>
<p><strong>案例：</strong></p>
<p><strong>编写配置文件  修改上面给出的配置文件，除了Source部分配置不同，其余部分都一样。不同的地方如下：</strong></p>
<div class="cnblogs_Highlighter">
<pre class="brush:bash;gutter:true;">＃描述/配置Source<br>a1.sources.r1.type  =  avro
a1.sources.r1.bind  =  0.0.0.0
a1.sources.r1.port  =  44444
</pre>
</div>
<p> 启动flume：</p>
<p>    ./flume-ng agent --conf ../conf --conf-file ../conf/template2.conf --name a1 -Dflume.root.logger=INFO,console</p>
<p> 通过flume提供的avro客户端向指定机器指定端口发送日志信息：</p>
<p>    ./flume-ng avro-client --conf ../conf --host 0.0.0.0 --port 44444 --filename ../mydata/log1.txt</p>
<p>会发现确实收集到日志</p>
<p> </p>
<p> </p>
<h2>4.2.    Spooling Directory Source</h2>
<p> </p>
<p>这个Source允许你将将要收集的数据放置到"自动搜集"目录中。这个Source将监视该目录，并将解析新文件的出现。事件处理逻辑是可插拔的，当一个文件被完全读入通道，它会被重命名或可选的直接删除。</p>
<p>要注意的是，放置到自动搜集目录下的文件不能修改，如果修改，则flume会报错。另外，也不能产生重名的文件，如果有重名的文件被放置进来，则flume会报错。</p>
<p>属性说明：（由于比较长 这里只给出了必须给出的属性，全部属性请参考官方文档）：</p>
<p> </p>
<p>!channels  –   </p>
<p>!type  –   类型，需要指定为"spooldir"</p>
<p>!spoolDir  –   读取文件的路径，即"搜集目录"</p>
<p>fileSuffix.COMPLETED对处理完成的文件追加的后缀</p>
<p><strong>案例：</strong></p>
<p><strong>编写配置文件  修改上面给出的配置文件，除了Source部分配置不同，其余部分都一样。不同的地方如下：</strong></p>
<div class="cnblogs_Highlighter">
<pre class="brush:bash;gutter:true;">＃描述/配置Source
a1.sources.r1.type  = spooldir
a1.sources.r1.spoolDir=/home/park/work/apache-flume-1.6.0-bin/mydata
</pre>
</div>
<p> 启动flume：</p>
<p>    ./flume-ng agent --conf ../conf --conf-file ../conf/template4.conf --name a1 -Dflume.root.logger=INFO,console</p>
<p> 向指定目录中传输文件，发现flume收集到了该文件，将文件中的每一行都作为日志来处理</p>
<p> </p>
<p> </p>
<h2>4.3.    NetCat Source</h2>
<p> </p>
<p>一个NetCat Source用来监听一个指定端口，并将接收到的数据的每一行转换为一个事件。</p>
<p> </p>
<h3>4.3.1.   NetCat Source属性说明</h3>
<p>！channels–   </p>
<p>！type–   类型名称，需要被设置为"netcat"</p>
<p>！bind–   指定要绑定到的ip或主机名。</p>
<p>！port–   指定要绑定到的端口号</p>
<p>max-line-length   512单行最大字节数</p>
<p> </p>
<p> </p>
<p>案例：上面完整的例子即是</p>
<p> </p>
<p> </p>
<h2>4.4.    HTTP Source</h2>
<p> </p>
<p>HTTP Source接受HTTP的GET和POST请求作为Flume的事件,其中GET方式应该只用于试验。</p>
<p>该Source需要提供一个可插拔的"处理器"来将请求转换为事件对象，这个处理器必须实现HTTPSourceHandler接口，该处理器接受一个 HttpServletRequest对象，并返回一个Flume Envent对象集合。</p>
<p>从一个HTTP请求中得到的事件将在一个事务中提交到通道中。因此允许像文件通道那样对通道提高效率。</p>
<p>如果处理器抛出一个异常，Source将会返回一个400的HTTP状态码。</p>
<p>如果通道已满，无法再将Event加入Channel，则Source返回503的HTTP状态码，表示暂时不可用。</p>
<p> </p>
<p> </p>
<h3>4.4.1.   HTTP Source属性说明</h3>
<p>！type    类型，必须为"HTTP"</p>
<p>！port–   监听的端口</p>
<p>bind   0.0.0.0    监听的主机名或ip</p>
<p>handler      org.apache.flume.source.http.JSONHandler处理器类，需要实现HTTPSourceHandler接口</p>
<p>handler.*  –   处理器的配置参数</p>
<p>selector.type</p>
<p>selector.*    </p>
<p>interceptors  –   </p>
<p>interceptors.*        </p>
<p>enableSSL  false  是否开启SSL,如果需要设置为true。注意，HTTP不支持SSLv3。</p>
<p>excludeProtocols  SSLv3  空格分隔的要排除的SSL/TLS协议。SSLv3总是被排除的。</p>
<p>keystore      密钥库文件所在位置。</p>
<p>keystorePassword Keystore 密钥库密码</p>
<p><strong>案例：</strong></p>
<p><strong>编写配置文件  修改上面给出的配置文件，除了Source部分配置不同，其余部分都一样。不同的地方如下：</strong></p>
<div class="cnblogs_Highlighter">
<pre class="brush:bash;gutter:true;">＃描述/配置Source
	a1.sources.r1.type  = http
	a1.sources.r1.port  = 66666
</pre>
</div>
<p> 启动flume:</p>
<p>    ./flume-ng agent --conf ../conf --conf-file ../conf/template6.conf --name a1 -Dflume.root.logger=INFO,console</p>
<p> 通过命令发送HTTP请求到指定端口：</p>
<p>    curl -X POST -d ‘[{ "headers" :{"a" : "a1","b" : "b1"},"body" : "hello~http~flume~"}]‘ http://0.0.0.0:6666</p>            </div>
                </div>
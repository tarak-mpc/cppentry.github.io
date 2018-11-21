---
layout:     post
title:      Flume日志采集，avro采集，以及通过参数控制下沉到hdfs的文件大小，时间等控制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/toto1297488504/article/details/80783285				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1-flume日志收集">1 Flume日志收集</h2>



<h3 id="11-总体介绍">1.1  总体介绍</h3>

<p>官方地址：<a href="http://flume.apache.org/" rel="nofollow">http://flume.apache.org/</a></p>



<h3 id="111-背景">1.1.1    背景</h3>

<p>flume 作为 cloudera 开发的实时日志收集系统，受到了业界的认可与广泛应用。Flume 初始的发行版本目前被统称为 Flume OG（original generation），属于 cloudera。但随着 FLume 功能的扩展，Flume OG 代码工程臃肿、核心组件设计不合理、核心配置不标准等缺点暴露出来，尤其是在 Flume OG 的最后一个发行版本 0.94.0 中，日志传输不稳定的现象尤为严重，为了解决这些问题，2011 年 10 月 22 号，cloudera 完成了 Flume-728，对 Flume 进行了里程碑式的改动：重构核心组件、核心配置以及代码架构，重构后的版本统称为 Flume NG（next generation）；改动的另一原因是将 Flume 纳入 apache 旗下，cloudera Flume 改名为 Apache Flume。  <br>
　　flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。支持在日志系统中定制各类数据发送方，用于收集数据;同时，Flume提供对数据进行简单处理，并写到各种数据接受方(比如文本、HDFS、Hbase等)的能力 。  <br>
　　flume的数据流由事件(Event)贯穿始终。事件是Flume的基本数据单位，它携带日志数据(字节数组形式)并且携带有头信息，这些Event由Agent外部的Source生成，当Source捕获事件后会进行特定的格式化，然后Source会把事件推入(单个或多个)Channel中。你可以把Channel看作是一个缓冲区，它将保存事件直到Sink处理完该事件。Sink负责持久化日志或者把事件推向另一个Source。</p>



<h3 id="112-特点">1.1.2    特点</h3>

<p><strong>Flume的可靠性</strong> <br>
    当节点出现故障时，日志能够被传送到其他节点上而不会丢失。Flume提供了三种级别的可靠性保障，从强到弱依次分别为：end-to-end（收到数据agent首先将event写到磁盘上，当数据传送成功后，再删除；如果数据发送失败，可以重新发送。），Store on failure（这也是scribe采用的策略，当数据接收方crash时，将数据写到本地，待恢复后，继续发送），Besteffort（数据发送到接收方后，不会进行确认）</p>

<p><strong>可恢复性</strong> <br>
    还是靠Channel.推荐使用FileChannel,事件持久化在本地文件系统里面（性能比较差）。</p>

<p><strong>核心概念</strong> <br>
<strong>Agent:</strong>使用JVM运行Flume。每台机器运行一个agent，但是可以再一个agent中包含多个sources和sinks。 <br>
<strong>Client:</strong> 生产数据，运行一个独立的线程。 <br>
<strong>Source:</strong> 从Client收集数据，运行一个独立线程。 <br>
<strong>Channel:</strong> 连接sources和sinks,这个有点像一个队列。 <br>
<strong>Events:</strong> 可以是日志记录、avro对象等。</p>

<p>Flume以agent为最小的独立运行单位。一个agent就是一个JVM。单agent由Source、Sink和Channel三大组件构成，如下图： <br>
<img src="https://img-blog.csdn.net/20180623141400903?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG8xMjk3NDg4NTA0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> </p>

<p>值得注意的是，Flume提供了大量内置的Source、Channel和Sink类型。不同类型的Source,Channel和sink可以自由组合。组合方式基于用于设置的配置文件，非常灵活。比如:Channel可以把时间暂存在内存里，也可以持久化到本地硬盘上。Sink可以把日志写入HDFS，HBase，甚至是另外一个Source等等。Flume支持用户建立多级流，也就是说，多个agent可以协同工作，并且支持Fan-in、Fan-out、Contextual Routing、Backup Routes。如下图所示： <br>
<img src="https://img-blog.csdn.net/20180623141429226?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG8xMjk3NDg4NTA0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> </p>



<h2 id="113-采集形式">1.1.3 采集形式</h2>

<p>日志收集中一个非常常见的场景是大量的日志生成客户端将数据发送给附加到存储子系统的一些消费者代理。例如，从数百个web服务器中收集的日志，发送给数十个写入HDFS集群的代理。 <br>
<img src="https://img-blog.csdn.net/20180623141453896?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG8xMjk3NDg4NTA0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> </p>

<p>在离线阶段，Flume的日志收集，Agent1/Agent2/Agent3端也是Flume端，主要的作用是采集nginx日志，采集到的日志文件最后下沉到另外一个agent4，最终通过agent4将数据下沉到hdfs中。</p>



<h2 id="12-agent1agent2agent3-flume采集端配置">1.2   Agent1/Agent2/Agent3  Flume采集端配置</h2>



<h3 id="121-前期准备">1.2.1    前期准备</h3>

<p>运行Flume-1.8.0的时候，需要JDK，否则会报错。修改jdk位置：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@bigdata1</span> conf]<span class="hljs-comment"># cd /home/bigdata/installed/flume-1.8.0/conf</span>
[root<span class="hljs-variable">@bigdata1</span> conf]<span class="hljs-comment"># vim flume-env.sh</span>
export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/home/bigdata</span><span class="hljs-regexp">/installed/jdk</span>1.<span class="hljs-number">8.0_73</span></code></pre>



<h3 id="122-配置flume内存参数">1.2.2    配置Flume内存参数</h3>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@bigdata1</span> flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># cd /home/bigdata/installed/flume-1.8.0/conf</span>
[root<span class="hljs-variable">@bigdata1</span> flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># vim flume-env.sh</span>
修改flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>/bin/flume-ng，主要修改<span class="hljs-constant">JAVA_OPTS</span>这个参数的值，具体的值是：
<span class="hljs-constant">JAVA_OPTS</span>=<span class="hljs-string">"-Xmx256m"</span></code></pre>

<p>内容是： <br>
<img src="https://img-blog.csdn.net/20180623141610446?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RvdG8xMjk3NDg4NTA0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> </p>



<h3 id="123-创建文件夹">1.2.3    创建文件夹</h3>

<p>进入线上服务器flume所在位置。</p>



<pre class="prettyprint"><code class=" hljs haml">[root@bigdata1 flume-1.8.0]# cd /xxxxx/apache-flume-1.8.0-bin
[root@bigdata1 flume-1.8.0]# ll
总用量 6296
drwxrwxrwx  2 bigdata bigdata      62 3月  22 10:46 bin
-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata   <span class="hljs-number">81264</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">CHANGELOG</span>
</span>drwxrwxrwx  2 bigdata bigdata     286 6月   5 21:10 conf
-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata    <span class="hljs-number">5681</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">DEVNOTES</span>
</span>-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata    <span class="hljs-number">2873</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> doap_Flume.rdf
</span>drwxrwxrwx 10 bigdata bigdata    4096 9月  15 2017 docs
drwxr-xr-x  3 root    root        123 6月  11 15:51 fileCHK
drwxr-xr-x  2 root    root        170 6月  11 16:50 fileData
drwxrwxrwx  2 bigdata bigdata    8192 3月  22 10:46 lib
-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata   <span class="hljs-number">27663</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">LICENSE</span>
</span>drwxr-xr-x  2 root    root         23 5月  15 14:47 logs
-<span class="ruby">rw-------  <span class="hljs-number">1</span> root    root    <span class="hljs-number">4054498</span> <span class="hljs-number">6</span>月  <span class="hljs-number">11</span> <span class="hljs-number">16</span><span class="hljs-symbol">:</span><span class="hljs-number">50</span> nohup.out
</span>-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata     <span class="hljs-number">249</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">NOTICE</span>
</span>-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata    <span class="hljs-number">2483</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">README</span>.md
</span>-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata    <span class="hljs-number">1588</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">RELEASE</span>-<span class="hljs-constant">NOTES</span>
</span>drwxrwxrwx  2 bigdata bigdata      68 3月  22 10:46 tools
-<span class="ruby">rw-r--r--  <span class="hljs-number">1</span> root    root    <span class="hljs-number">1242266</span> <span class="hljs-number">6</span>月  <span class="hljs-number">11</span> <span class="hljs-number">16</span><span class="hljs-symbol">:</span><span class="hljs-number">20</span> zookeeper.out
</span>[root@bigdata1 flume-1.8.0]#</code></pre>

<p>要注意的是，上面的fileCHK,fileData为自己手动创建的，创建命令：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@bigdata1</span> flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># cd /xxxxx/apache-flume-1.8.0-bin</span>
[root<span class="hljs-variable">@bigdata1</span> flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># mkdir fileCHK</span>
[root<span class="hljs-variable">@bigdata1</span> flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># mkdir fileData</span></code></pre>



<h3 id="124-查看杀死flume进程的方式">1.2.4    查看/杀死Flume进程的方式</h3>

<p>查看Flume的方式：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@bigdata1</span> flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># grep flume | grep -v grep</span></code></pre>

<p>杀死Flume的方式：</p>



<pre class="prettyprint"><code class=" hljs asciidoc">[root@bigdata1 flume-1.8.0]# kill -9 <span class="hljs-smartquote">`ps -ef | grep flume | grep -v grep | awk '</span>{print $2}<span class="hljs-emphasis">'`</span></code></pre>



<h3 id="125-conf文件配置">1.2.5    Conf文件配置</h3>

<p>进入conf文件夹，创建采集日志的配置文件</p>



<pre class="prettyprint"><code class=" hljs ruby">[xxx<span class="hljs-variable">@xxx</span>]<span class="hljs-comment"># vim file-collect-logs.conf</span></code></pre>

<p>内容是：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#定义agent各个组件的名称</span>
a1<span class="hljs-preprocessor">.sources</span>=<span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span>=k1
a1<span class="hljs-preprocessor">.channels</span>=c1

<span class="hljs-preprocessor">#描述source端</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span>=exec
<span class="hljs-preprocessor">#要注意的是，这里使用的是-F，只有这样才能保证nginx日志文件变化之后，不会出错</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.command</span>=tail -n +<span class="hljs-number">0</span> -F /var/log/nginx/tomcat_access<span class="hljs-preprocessor">.log</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span>=c1
<span class="hljs-preprocessor">#a1.sources.r1.restart=true</span>
<span class="hljs-preprocessor">#a1.sources.r1.restartThrottle=5000</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.threads</span>=<span class="hljs-number">100</span>

<span class="hljs-preprocessor">#Describe the sink</span>
<span class="hljs-preprocessor">##type设置成avro来设置发消息</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = avro
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1
<span class="hljs-preprocessor">##下沉到bigdata1这台机器</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hostname</span> = xxx<span class="hljs-preprocessor">.xxx</span><span class="hljs-preprocessor">.xxx</span><span class="hljs-preprocessor">.xxx</span>
<span class="hljs-preprocessor">##下沉到bigdata1中的4141</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">4141</span> (此处和线上配置不一致)
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.batch</span>-size = <span class="hljs-number">10000</span>

<span class="hljs-preprocessor">#使用File通道</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span>=file
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.checkpointDir</span>=/usr/local/work/apache-flume-<span class="hljs-number">1.8</span><span class="hljs-number">.0</span>-bin/fileCHK
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.dataDirs</span>=/usr/local/work/apache-flume-<span class="hljs-number">1.8</span><span class="hljs-number">.0</span>-bin/fileData
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.keep</span>-alive=<span class="hljs-number">60</span></code></pre>

<h3 id="126-flume启动">1.2.6    Flume启动</h3>

<p>采集端配置好之后，等待接收端Flume启动后，启动采集端Flume,启动方式是：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-preprocessor">[</span>x@xxx apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.8</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> cd /xxx/apache-flume-1.8.0-bin
</span><span class="hljs-preprocessor">[</span>x@xxx apache<span class="hljs-attribute">-flume</span><span class="hljs-subst">-</span><span class="hljs-number">1.8</span><span class="hljs-number">.0</span><span class="hljs-attribute">-bin</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> nohup bin/flume-ng agent -c conf -f conf/file-collect-logs.conf -n a1 -Dflume.root.logger=INFO,console &amp;</span></code></pre>

<p>其中杀死进程的方式：</p>



<pre class="prettyprint"><code class=" hljs asciidoc">kill -9 <span class="hljs-smartquote">`ps -ef | grep flume | grep -v grep | awk '</span>{print $2}<span class="hljs-emphasis">'`</span></code></pre>

<p>要注意的是： <br>
在Flume修改配置之后是不需要重启Flume的。直接保存配置文件即可让配置启用。</p>



<h2 id="13-agent4-avro接收端flume参数配置">1.3   Agent4 avro接收端Flume参数配置</h2>



<h3 id="131-前期准备">1.3.1    前期准备</h3>

<p>运行Flume-1.8.0的时候，需要JDK，否则会报错。修改jdk位置：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@bigdata1</span> conf]<span class="hljs-comment"># cd /home/bigdata/installed/flume-1.8.0/conf</span>
[root<span class="hljs-variable">@bigdata1</span> conf]<span class="hljs-comment"># vim flume-env.sh</span>
export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/home/bigdata</span><span class="hljs-regexp">/installed/jdk</span>1.<span class="hljs-number">8.0_73</span></code></pre>



<h3 id="132-配置flume内存参数">1.3.2    配置Flume内存参数</h3>

<p>参考1.2.1</p>



<h3 id="133-创建文件夹">1.3.3    创建文件夹</h3>

<p>进入Flume所在位置，创建fileCHK 和 fileData</p>



<pre class="prettyprint"><code class=" hljs haml">[root@bigdata1 flume-1.8.0]# cd /xxxx/installed/flume-1.8.0
[root@bigdata1 flume-1.8.0]# ll
总用量 6360
drwxrwxrwx  2 bigdata bigdata      62 3月  22 10:46 bin
-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata   <span class="hljs-number">81264</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">CHANGELOG</span>
</span>drwxrwxrwx  2 bigdata bigdata     286 6月   5 21:10 conf
-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata    <span class="hljs-number">5681</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">DEVNOTES</span>
</span>-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata    <span class="hljs-number">2873</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> doap_Flume.rdf
</span>drwxrwxrwx 10 bigdata bigdata    4096 9月  15 2017 docs
drwxr-xr-x  3 root    root        123 6月  11 15:51 fileCHK
drwxr-xr-x  2 root    root        170 6月  11 17:18 fileData
drwxrwxrwx  2 bigdata bigdata    8192 3月  22 10:46 lib
-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata   <span class="hljs-number">27663</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">LICENSE</span>
</span>drwxr-xr-x  2 root    root         23 5月  15 14:47 logs
-<span class="ruby">rw-------  <span class="hljs-number">1</span> root    root    <span class="hljs-number">4121563</span> <span class="hljs-number">6</span>月  <span class="hljs-number">11</span> <span class="hljs-number">17</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> nohup.out
</span>-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata     <span class="hljs-number">249</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">NOTICE</span>
</span>-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata    <span class="hljs-number">2483</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">README</span>.md
</span>-<span class="ruby">rwxrwxrwx  <span class="hljs-number">1</span> bigdata bigdata    <span class="hljs-number">1588</span> <span class="hljs-number">9</span>月  <span class="hljs-number">15</span> <span class="hljs-number">2017</span> <span class="hljs-constant">RELEASE</span>-<span class="hljs-constant">NOTES</span>
</span>drwxrwxrwx  2 bigdata bigdata      68 3月  22 10:46 tools
-<span class="ruby">rw-r--r--  <span class="hljs-number">1</span> root    root    <span class="hljs-number">1242266</span> <span class="hljs-number">6</span>月  <span class="hljs-number">11</span> <span class="hljs-number">16</span><span class="hljs-symbol">:</span><span class="hljs-number">20</span> zookeeper.out
</span>[root@bigdata1 flume-1.8.0]#
创建fileCHK 和 fileData
[root@bigdata1 flume-1.8.0]# cd /xxxxx/apache-flume-1.8.0-bin
[root@bigdata1 flume-1.8.0]# mkdir fileCHK
[root@bigdata1 flume-1.8.0]# mkdir fileData</code></pre>



<h3 id="134-查看杀死flume进程的方式">1.3.4    查看/杀死Flume进程的方式</h3>

<p>参看1.2.4</p>



<h3 id="135-编写flume-sinkhdfs的配置文件">1.3.5    编写Flume sink—&gt;hdfs的配置文件</h3>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@bigdata1</span> conf]<span class="hljs-comment"># cd /home/bigdata/installed/flume-1.8.0/conf</span>
[root<span class="hljs-variable">@bigdata1</span> conf]<span class="hljs-comment"># vim avro-collect-logger.conf</span></code></pre>

<p>具体内容如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#定义agent中的各各组件</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor">##描述和配置source端</span>
<span class="hljs-preprocessor">##source中的avro组件是一个接收者服务</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = avro
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
<span class="hljs-preprocessor">#这里的bigdata1是机器hostname,可以写成ip地址。外界调用的时候，可以使用这个ip和下面的端口号port联合调用</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = bigdata1
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">4141</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.threads</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># Describe the sink</span>
<span class="hljs-preprocessor">## 表示下沉到hdfs，类型决定了下面的参数</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = hdfs
<span class="hljs-preprocessor">## hdfs目录路径</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.path</span> = /nginx/%<span class="hljs-built_in">y</span>-%m-%d
<span class="hljs-preprocessor">##写入hdfs的文件名前缀，可以使用flume提供的日期及%{host}表达式</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.filePrefix</span> = events-
<span class="hljs-preprocessor">## 表示到了需要触发的时间时，是否要更新文件夹，true:表示要</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.round</span> = true
<span class="hljs-preprocessor">#多久时间后close hdfs文件。单位是秒，默认30秒。设置为0的话表示不根据时间close hdfs文件</span>
<span class="hljs-preprocessor">#配置下面的参数，表示40分钟滚动一次</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollInterval</span> = <span class="hljs-number">3600</span>
<span class="hljs-preprocessor">## 表示每隔value分钟改变一次(在0~24之间)</span>
<span class="hljs-preprocessor">#a1.sinks.k1.hdfs.roundValue = 0</span>
<span class="hljs-preprocessor">## 切换文件的时候的时间单位是分钟</span>
<span class="hljs-preprocessor">#a1.sinks.k1.hdfs.roundUnit = minute</span>
<span class="hljs-preprocessor">## 文件大小超过一定值后，close文件。默认值1024，单位是字节。设置为0的话表示不基于文件大小,134217728表示128m</span>
<span class="hljs-preprocessor">## 0表示不按照文件大小进行滚动。</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollSize</span> = <span class="hljs-number">0</span>
<span class="hljs-preprocessor">## 写入了多少个事件后close文件。默认值是10个。设置为0的话表示不基于事件个数</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.rollCount</span> = <span class="hljs-number">0</span>
<span class="hljs-preprocessor">## 批次数，HDFS Sink每次从Channel中拿的事件个数。默认值100</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.batchSize</span> = <span class="hljs-number">100000</span>
<span class="hljs-preprocessor">## 使用本地时间戳</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.useLocalTimeStamp</span> = true
<span class="hljs-preprocessor">#生成的文件类型，默认是Sequencefile，可用DataStream：为普通文本</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.fileType</span> = DataStream
<span class="hljs-preprocessor">#HDFS操作允许的时间，比如hdfs文件的open，write，flush，close操作。单位是毫秒，默认值是10000，一小时为：3600000，这里稍微设置的大写</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.callTimeout</span> = <span class="hljs-number">7200000</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.writeFormat</span> = Text
<span class="hljs-preprocessor">#配置这个参数之后，不会因为文件所在块的复制而滚动文件了，只会根据你的配置项来滚动文件了。</span>
<span class="hljs-preprocessor">#设置这个参数之后，上面的rollInterval、rollSize、rollCount等才会起作用。</span>

<span class="hljs-preprocessor">#hdfs.minBlockReplicas是为了让flume感知不到hdfs的块复制，这样滚动配置才不会受影响。</span>
<span class="hljs-preprocessor">#假如hdfs的副本为3.那么配置的滚动时间为10秒，那么在第二秒的时候，flume检测到hdfs在复制块，那么这时候flume就会滚动</span>
<span class="hljs-preprocessor">#这样导致flume的滚动方式受到影响。所以配置flume hdfs.minBlockReplicas配置为1，就检测不到副本的复制了。但是hdfs的副本还是3</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.hdfs</span><span class="hljs-preprocessor">.minBlockReplicas</span>=<span class="hljs-number">1</span>

<span class="hljs-preprocessor">#描述channel，适用场景是要求并发高，并且机器内存足够，丢失数据不会很影响总体结果的场景。但是重启和宕机，数据会丢失。</span>
<span class="hljs-preprocessor">#配置不好的时候，会出现内存等的问题。</span>
<span class="hljs-preprocessor">#a1.channels.c1.type=memory</span>
<span class="hljs-preprocessor">#a1.channels.c1.capacity=10000</span>
<span class="hljs-preprocessor">#a1.channels.c1.transactionCapacity=10000</span>
<span class="hljs-preprocessor">#a1.channels.c1.byteCapacityBufferPercentage = 20</span>
<span class="hljs-preprocessor">#a1.channels.c1.byteCapacity = 800000</span>
<span class="hljs-preprocessor">#a1.channels.c1.keep-alive = 60</span>

<span class="hljs-preprocessor">#适用场景，并发要求不高，要求硬盘足够即可，数据最后存在file中的，机器重启ls</span>
或flume重启，只要调整参数，可以保证数据不丢失。
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span>=file
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.checkpointDir</span>=/home/bigdata/installed/flume-<span class="hljs-number">1.8</span><span class="hljs-number">.0</span>/fileCHK
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.dataDirs</span>=/home/bigdata/installed/flume-<span class="hljs-number">1.8</span><span class="hljs-number">.0</span>/fileData
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.keep</span>-alive=<span class="hljs-number">60</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span>=<span class="hljs-number">10000000</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span>=<span class="hljs-number">10000000</span>


<span class="hljs-preprocessor">#绑定source和sink到channel</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>



<h3 id="136-flume启动">1.3.6    Flume启动</h3>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@bigdata1</span> flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># cd /home/bigdata/installed/flume-1.8.0</span>
[root<span class="hljs-variable">@bigdata1</span> flume-<span class="hljs-number">1.8</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># nohup bin/flume-ng agent -c conf -f conf/avro-collect-logger.conf -n a1 -Dflume.root.logger=INFO,console &amp;</span></code></pre>



<h3 id="137-查看未关闭的块">1.3.7    查看未关闭的块</h3>



<pre class="prettyprint"><code class=" hljs 1c">hadoop fsck -openforwrite <span class="hljs-string">| more  查看未关闭块</span></code></pre>



<h3 id="138-查看运行日志">1.3.8    查看运行日志</h3>

<p>可以通过查看nohup.out的方式查看运行日志。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
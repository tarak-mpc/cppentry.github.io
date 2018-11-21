---
layout:     post
title:      kafka集群监控之KafkaOffsetMonitor 0.4.1版本 以及用sbt编译KafkaOffsetMonitor
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_20641565/article/details/72897666				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>黑窗口的kafka集群没有监控，可以使用三方开源的监控工具，查询了些资料，网上流传最多的有三个监控工具：</p>

<ol>
<li>Kafka Web Conslole</li>
<li>Kafka Manager</li>
<li>KafkaOffsetMonitor</li>
</ol>

<p>先补一张生产数据（28个patition，不算冗余的数据 一天大概200G）：</p>

<p><img src="https://img-blog.csdn.net/20170628104145538?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>根据网友的实践：</strong></p>

<p><strong>Kafka Web Conslole</strong></p>

<p>程序运行后，会定时去读取kafka集群分区的日志长度，读取完毕后，连接没有正常释放，一段时间后产生大量的socket连接，导致网络堵塞，所以不建议使用。</p>

<p><strong>Kafka Manager</strong></p>

<p>雅虎开源的一个监控工具，可以用来监控多个集群。</p>

<p><strong>KafkaOffsetMonitor</strong></p>

<p>相对上面来说，这个工具耦合性比较低，单用于监控比较适合而且部署很简单。 <br>
<strong>这里也介绍了下安装步骤，本来很简单的，但是版本不兼容，导致需要自己编译，然后记录下踩的坑。。。</strong></p>

<p><strong>（注意：kafka新的版本offset存储在自己的一个topic，不像老版本是存在zookeeper里面，所以用KafkaOffsetMonitor2.x的版本监控会有问题，根本监控不到数据，所以最好在github上面下载最新的版本自己编译，注意jdk版本）</strong></p>

<p><strong>我们使用KafkaOffsetMonitor监控工具</strong></p>

<ul>
<li>KafkaOffsetMonitor的安装</li>
</ul>

<p>下载软件包： <br>
github地址（0.2.1版本）：<a href="https://github.com/quantifind/KafkaOffsetMonitor/releases/download/v0.2.1/KafkaOffsetMonitor-assembly-0.2.1.jar" rel="nofollow">点击下载</a></p>

<p>如果下载不懂，我已经上传了我的百度云：<a href="http://pan.baidu.com/s/1jI7icmE" rel="nofollow">0.2.0版本</a>，<a href="http://pan.baidu.com/s/1o8uiIaY" rel="nofollow">0.2.1版本</a></p>

<p>这个是用assembly插件直接把所有包打到了一个jar里面，上传服务器，直接启动就行，特别方便。</p>

<p>上传jar包到服务器，然后创建一个kafka监控的目录</p>

<pre class="prettyprint"><code class=" hljs perl"><span class="hljs-keyword">mkdir</span> /mydata/kafkamonitorlogs</code></pre>

<p>执行如下语句启动kafka监控</p>



<pre class="prettyprint"><code class=" hljs haml">java -Xms512M -Xmx512M -Xss1024K -XX:PermSize=256m -XX:MaxPermSize=512m -cp KafkaOffsetMonitor-assembly-0.2.1.jar \
     com.quantifind.kafka.offsetapp.OffsetGetterWeb \
     -<span class="ruby">-zk <span class="hljs-number">10.104</span>.<span class="hljs-number">109.176</span><span class="hljs-symbol">:</span><span class="hljs-number">2181</span>,<span class="hljs-number">10.104</span>.<span class="hljs-number">86.238</span><span class="hljs-symbol">:</span><span class="hljs-number">2182</span>,<span class="hljs-number">10.104</span>.<span class="hljs-number">122.21</span><span class="hljs-symbol">:</span><span class="hljs-number">2181</span> \
</span>     -<span class="ruby">-port <span class="hljs-number">8787</span> \
</span>     -<span class="ruby">-refresh <span class="hljs-number">10</span>.seconds \
</span>     -<span class="ruby">-retain <span class="hljs-number">7</span>.days <span class="hljs-number">1</span>&gt;<span class="hljs-regexp">/mydata/kafkamonitorlogs</span><span class="hljs-regexp">/stdout.log 2&gt;/mydata</span><span class="hljs-regexp">/kafkamonitorlogs/stderr</span>.log &amp;</span></code></pre>

<ul>
<li>或者自己写个启动停止脚本：</li>
</ul>

<p><strong>start.sh</strong></p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-shebang">#!/bin/bash</span>
java -Xms512M -Xmx512M -Xss1024K -XX:PermSize=<span class="hljs-number">256</span>m -XX:MaxPermSize=<span class="hljs-number">512</span>m -cp KafkaOffsetMonitor-assembly-<span class="hljs-number">0.2</span>.<span class="hljs-number">1</span>.jar com.quantifind.kafka.offsetapp.OffsetGetterWeb --zk <span class="hljs-number">10.104</span>.<span class="hljs-number">109.176</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">10.104</span>.<span class="hljs-number">86.238</span>:<span class="hljs-number">2182</span>,<span class="hljs-number">10.104</span>.<span class="hljs-number">122.21</span>:<span class="hljs-number">2181</span> --port <span class="hljs-number">8787</span> --refresh <span class="hljs-number">10</span>.seconds --retain <span class="hljs-number">7</span>.days <span class="hljs-number">1</span>&gt;/mydata/kafkamonitorlogs/stdout.log <span class="hljs-number">2</span>&gt;/mydata/kafkamonitorlogs/stderr.log &amp;</code></pre>

<p><strong>stop.sh</strong></p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-shebang">#!/bin/bash
</span>
killnum=`jps | grep OffsetGetterWeb | awk <span class="hljs-string">'{print $1}'</span>`
kill -<span class="hljs-number">9</span> <span class="hljs-variable">${killnum}</span>
<span class="hljs-built_in">echo</span> <span class="hljs-string">"OK...."</span></code></pre>

<p>启动之后查看web界面 <br>
访问：安装服务器的ip:8787</p>



<pre class="prettyprint"><code class=" hljs cs">http:<span class="hljs-comment">//testslave02:8787</span></code></pre>

<p>界面如下：</p>

<p><img src="https://img-blog.csdn.net/20170607110530530?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170607110606598?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>其他功能自己去试一试。</p>

<p>但是发现，没有获取到broker，kafka的版本安装过高，不兼容，只有自己去下载最新的源码来编译了</p>

<p>GitHub连接地址（上面有命令的详细介绍）：<a href="https://github.com/Morningstar/kafka-offset-monitor" rel="nofollow" target="_blank">点击进入</a></p>

<p>安装sbt环境：</p>

<p>下载sbt，官网上面的半天下载不懂，找了个csdn上面的链接 0.13.8版本的，地址如下：<a href="http://download.csdn.net/download/yhao2014/8792815" rel="nofollow" target="_blank">点击进入</a></p>

<p>解压安装sbt：</p>

<p>我放到了D盘下面，创建一个目录将sbt安装到该目录下：</p>



<pre class="prettyprint"><code class=" hljs mathematica"><span class="hljs-keyword">D</span>:\sbt0<span class="hljs-number">.13</span><span class="hljs-number">.8</span></code></pre>

<p>修改sbtconfig.txt配置文件： <br>
添加一项：</p>



<pre class="prettyprint"><code class=" hljs haml">-<span class="ruby"><span class="hljs-constant">Dsbt</span>.ivy.home=<span class="hljs-constant">D</span><span class="hljs-symbol">:/sbtivy</span></span></code></pre>

<p>在修改用户目录下的.sbt目录下创建repositories文件并且写入：</p>



<pre class="prettyprint"><code class=" hljs markdown">[repositories]  
public: http://maven.aliyun.com/nexus/content/groups/public/
typesafe:http://dl.bintray.com/typesafe/ivy-releases/ , [<span class="hljs-link_label">organization</span>]/[<span class="hljs-link_label">module</span>]/(scala<span class="hljs-emphasis">_[scalaVersion]/)(sbt_</span>[<span class="hljs-link_label">sbtVersion</span>]/)[<span class="hljs-link_label">revision</span>]/[<span class="hljs-link_label">type</span>]s/[<span class="hljs-link_label">artifact</span>](<span class="hljs-link_url">-[classifier]</span>).[ext], bootOnly
ivy-sbt-plugin:http://dl.bintray.com/sbt/sbt-plugin-releases/, [<span class="hljs-link_label">organization</span>]/[<span class="hljs-link_label">module</span>]/(scala<span class="hljs-emphasis">_[scalaVersion]/)(sbt_</span>[<span class="hljs-link_label">sbtVersion</span>]/)[<span class="hljs-link_label">revision</span>]/[<span class="hljs-link_label">type</span>]s/[<span class="hljs-link_label">artifact</span>](<span class="hljs-link_url">-[classifier]</span>).[ext]</code></pre>

<p>在命令行执行 sbt，开始下载包， <br>
但是执行到后面时候报错：</p>



<pre class="prettyprint"><code class=" hljs avrasm">download failed: org<span class="hljs-preprocessor">.scalamacros</span><span class="hljs-preprocessor">#quasiquotes_2.10;2.0.1!quasiquotes_2.10.jar</span></code></pre>

<p>在阿里云镜像里面没有，我果断把阿里镜像换成了默认的中央仓库，把repositories文件的</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-symbol">public:</span> <span class="hljs-symbol">http:</span>/<span class="hljs-regexp">/maven.aliyun.com/nexus</span><span class="hljs-regexp">/content/groups</span><span class="hljs-regexp">/public/</span></code></pre>

<p>改为：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">public:</span> http://repo1<span class="hljs-preprocessor">.maven</span><span class="hljs-preprocessor">.org</span>/maven2/</code></pre>

<p>再次执行，OK</p>

<p><strong>为了下载包速度快，我又把maven的仓库改为了阿里云的</strong></p>

<p>开始编译KafkaOffsetMonitor</p>

<p>到工程目录下打开cmd执行</p>

<p><img src="https://img-blog.csdn.net/20170607134458017?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class=" hljs vbnet">sbt clean <span class="hljs-keyword">assembly</span></code></pre>

<p>就等待他慢慢编译： <br>
<img src="https://img-blog.csdn.net/20170607134555080?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>等着。。。  突然发现，作者的在git上面写着的</p>

<p><strong>If you do not want to build it manually, just download the current jar.</strong></p>

<p><img src="https://img-blog.csdn.net/20170607135518203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>居然有编译好的，反正不管了，编译的也让它编译着，先直接下载上面提供的编译好的jar包。</p>

<p>地址：<a href="https://github.com/Morningstar/kafka-offset-monitor/releases/tag/0.4.1" rel="nofollow" target="_blank">点击进入</a></p>

<p>如果下载不动的话，我也上传我自己的百度云了，链接：<a href="http://pan.baidu.com/s/1pKAf3GR" rel="nofollow" target="_blank">点击下载</a></p>

<p><img src="https://img-blog.csdn.net/20170607135927634?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>将此jar包上传服务器</p>

<p>然后执行：</p>



<pre class="prettyprint"><code class=" hljs haml">java -Xms512M -Xmx512M -Xss1024K -XX:PermSize=256m -XX:MaxPermSize=512m \
     -<span class="ruby">cp <span class="hljs-constant">KafkaOffsetMonitor</span>-assembly-<span class="hljs-number">0</span>.<span class="hljs-number">4.1</span>-<span class="hljs-constant">SNAPSHOT</span>.jar \
</span>     com.quantifind.kafka.offsetapp.OffsetGetterWeb \
     -<span class="ruby">-offsetStorage kafka \
</span>     -<span class="ruby">-kafkaBrokers <span class="hljs-number">10.104</span>.<span class="hljs-number">109.176</span><span class="hljs-symbol">:</span><span class="hljs-number">9092</span>,<span class="hljs-number">10.104</span>.<span class="hljs-number">86.238</span><span class="hljs-symbol">:</span><span class="hljs-number">9092</span>,<span class="hljs-number">10.104</span>.<span class="hljs-number">122.21</span><span class="hljs-symbol">:</span><span class="hljs-number">9092</span> \
</span>     -<span class="ruby">-kafkaSecurityProtocol <span class="hljs-constant">SASL_PLAINTEXT</span> \
</span>     -<span class="ruby">-zk <span class="hljs-number">10.104</span>.<span class="hljs-number">109.176</span><span class="hljs-symbol">:</span><span class="hljs-number">2181</span>,<span class="hljs-number">10.104</span>.<span class="hljs-number">86.238</span><span class="hljs-symbol">:</span><span class="hljs-number">2182</span>,<span class="hljs-number">10.104</span>.<span class="hljs-number">122.21</span><span class="hljs-symbol">:</span><span class="hljs-number">2181</span> \
</span>     -<span class="ruby">-port <span class="hljs-number">8787</span> \
</span>     -<span class="ruby">-refresh <span class="hljs-number">10</span>.seconds \
</span>     -<span class="ruby">-retain <span class="hljs-number">5</span>.days \
</span>     -<span class="ruby">-dbName offsetapp_kafka</span></code></pre>

<p>jdk版本不兼容：</p>

<p><img src="https://img-blog.csdn.net/20170607143321599?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>我自己的电脑也是1.8jdk，虽然编译成功了 但是服务器上面是jdk1.7 刚编译的执行了也不兼容，把本机jdk换成1.7重新编译….</p>

<p>等了大概半个多小时，报错包又拉不下来，又把仓库换成public: <a href="http://repo1.maven.org/maven2/" rel="nofollow" target="_blank">http://repo1.maven.org/maven2/</a> 继续编译。。。。 <br>
编译失败。。报错版本问题。。。 <br>
<img src="https://img-blog.csdn.net/20170607170616839?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>然后想到一个办法：直接在服务器上面创建一个新用户，并把新用户的自己的环境变量设置成jdk1.8，然后在启动，启动命令如下（zk端口换了，之前用的cdh的，这次自己搭建的zk）：</p>



<pre class="prettyprint"><code class=" hljs haml">java -Xms512M -Xmx512M -Xss1024K -XX:PermSize=256m -XX:MaxPermSize=512m \
     -<span class="ruby">cp <span class="hljs-constant">KafkaOffsetMonitor</span>-assembly-<span class="hljs-number">0</span>.<span class="hljs-number">4.1</span>-<span class="hljs-constant">SNAPSHOT</span>.jar \
</span>     com.quantifind.kafka.offsetapp.OffsetGetterWeb \
     -<span class="ruby">-offsetStorage kafka \
</span>     -<span class="ruby">-kafkaBrokers <span class="hljs-symbol">testmaster:</span><span class="hljs-number">9092</span>,<span class="hljs-symbol">testslave01:</span><span class="hljs-number">9092</span>,<span class="hljs-symbol">testslave02:</span><span class="hljs-number">9092</span> \
</span>     -<span class="ruby">-kafkaSecurityProtocol <span class="hljs-constant">PLAINTEXT</span> \
</span>     -<span class="ruby">-zk <span class="hljs-symbol">testmaster:</span><span class="hljs-number">2188</span>,<span class="hljs-symbol">testslave01:</span><span class="hljs-number">2188</span>,<span class="hljs-symbol">testslave02:</span><span class="hljs-number">2188</span> \
</span>     -<span class="ruby">-port <span class="hljs-number">8787</span> \
</span>     -<span class="ruby">-refresh <span class="hljs-number">10</span>.seconds \
</span>     -<span class="ruby">-retain <span class="hljs-number">5</span>.days \
</span>     -<span class="ruby">-dbName offsetapp_kafka</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170607201341872?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170608191323371?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class=" hljs mel">  topic：创建topic的名称
  <span class="hljs-keyword">partition</span>：分区编号
  offset：该<span class="hljs-keyword">partition</span>已经消费了多少条message
  logsize：该<span class="hljs-keyword">partition</span>已经写入了多少条message
  lag：该<span class="hljs-keyword">partition</span>未消费多少条message
  owner：gaipartition所属消费者</code></pre>

<p>界面也都OK都能监控了：</p>

<p><img src="https://img-blog.csdn.net/20170607201420107?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMjA2NDE1NjU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
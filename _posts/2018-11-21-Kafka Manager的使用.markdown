---
layout:     post
title:      Kafka Manager的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_33689414/article/details/80958045				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="kafka-manager的使用">Kafka Manager的使用</h3>



<h4 id="1安装">1.安装</h4>

<p>Kafka Manager是雅虎开发的一个基于Web的kafka管理工具。具有非常强大的功能。</p>

<p>kafka manager项目地址：<a href="https://github.com/yahoo/kafka-manager" rel="nofollow">https://github.com/yahoo/kafka-manager</a></p>

<p><strong>下载编译kafka manager</strong></p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># 从git上下载Kafka manager源码</span>
git clone https:<span class="hljs-comment">//github.com/yahoo/kafka-manager</span>

<span class="hljs-preprocessor"># 使用sbt进行编译，编译过程中需要等待很长时间</span>
./stb clean dist</code></pre>

<p>编译完成会生成一个kafka-manager-1.3.x.x.zip的压缩包，或者我们可以直接从网上下载一个<code>kafka-manager-xxxx.zip</code>压缩包。</p>

<p><a href="https://download.csdn.net/download/weixin_41582312/10186839" rel="nofollow">kafka-manager-1.3.3.15.zip</a></p>

<p><a href="https://download.csdn.net/download/jiu123ba/9937296" rel="nofollow">kafka-manager-1.3.3.7.zip</a></p>

<p><strong>解压</strong></p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># 进入kafka manager的zip包目录，解压</span>
unzip kafka-manager-<span class="hljs-number">1.3</span><span class="hljs-number">.3</span><span class="hljs-number">.7</span>.zip</code></pre>

<p>解压后的目录</p>

<p><img src="https://raw.githubusercontent.com/zhang3550545/image_center/master/image-2018/kafka-manager-1.jpeg" alt="image" title=""></p>

<p><strong>修改配置文件<code>./conf/application.conf</code></strong></p>

<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># 默认是localhost，将zkhosts改为自己zk集群的hosts</span>
kafka-manager.zkhosts=<span class="hljs-string">"localho:2181"</span></code></pre>

<h4 id="2启动">2.启动</h4>

<p><strong>启动zookeeper和kafka</strong></p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># 启动zookeeper</span>
zkServer start

<span class="hljs-preprocessor"># 启动kafka，JMX_PORT=9991指定Kafka的JMX端口为9991，方便对kafka监控</span>
JMX_PORT=<span class="hljs-number">9991</span> kafka-server-start -daemon /usr/local/etc/kafka/server.properties</code></pre>

<p><strong>启动Kafka Manager</strong> <br>
进入kafka manager的解压目录下的bin目录，执行kafka-manager命令。</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># 进入/kafka-manager/bin</span>
cd ../kafka-manager/bin

<span class="hljs-preprocessor"># 执行kafka-manager命令</span>
sh kafka-manager</code></pre>

<p>启动后会在控制台输出一些log信息，在浏览器中输入：<code>localhost:9000</code>，就会出现一个Kafka Manager的管理界面。</p>

<p><img src="https://raw.githubusercontent.com/zhang3550545/image_center/master/image-2018/kafka-manager-2.jpeg" alt="image" title=""></p>



<h4 id="3ui界面的介绍">3.UI界面的介绍</h4>

<p><strong>添加Cluster</strong></p>

<p><img src="https://raw.githubusercontent.com/zhang3550545/image_center/master/image-2018/kafka-manager-3.jpeg" alt="image" title=""></p>

<p><strong>topic Summary页面和topic的list页面</strong></p>

<p><img src="https://raw.githubusercontent.com/zhang3550545/image_center/master/image-2018/kafka-manager-4.jpeg" alt="image" title=""></p>

<p><img src="https://raw.githubusercontent.com/zhang3550545/image_center/master/image-2018/kafka-manager-5.jpeg" alt="image" title=""></p>

<p><strong>topic的详情页面</strong></p>

<p>topic的详情页面，可以对topic进行重新分区操作，删除topic操作，更新配置操作等等。</p>

<p><img src="https://raw.githubusercontent.com/zhang3550545/image_center/master/image-2018/kafka-manager-6.jpeg" alt="image" title=""></p>

<p>topic删除操作，如果在server.properties设置delete.topic.enable=true，可以直接删除。如果设置成false，topic通过Delete Topic按钮删除，实际是不能删除的。</p>

<p><strong>Broker信息页面</strong></p>

<p><img src="https://raw.githubusercontent.com/zhang3550545/image_center/master/image-2018/kafka-manager-7.jpeg" alt="image" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
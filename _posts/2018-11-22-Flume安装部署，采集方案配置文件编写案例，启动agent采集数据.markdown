---
layout:     post
title:      Flume安装部署，采集方案配置文件编写案例，启动agent采集数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/toto1297488504/article/details/73194903				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="12-flume实战案例">1.2 Flume实战案例</h2>



<h3 id="121-flume的安装部署">1.2.1 Flume的安装部署</h3>

<p>1、Flume的安装非常简单，只需要解压即可，当然，前提是已有hadoop环境 <br>
上传安装包到数据源所在节点上 <br>
然后解压  tar -zxvf apache-flume-1.6.0-bin.tar.gz，最终解压到的位置是：/home/tuzq/software/apache-flume-1.6.0-bin <br>
然后进入flume的目录，修改conf下的flume-env.sh，在里面配置JAVA_HOME <br>
<img src="https://img-blog.csdn.net/20170613200459763?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>2、根据数据采集的需求配置采集方案，描述在配置文件中(文件名可任意自定义) <br>
3、指定采集方案配置文件，在相应的节点上启动flume agent</p>



<h3 id="先用一个最简单的例子来测试一下程序环境是否正常">先用一个最简单的例子来测试一下程序环境是否正常</h3>



<h4 id="1先在flume的conf目录下新建一个文件">1、先在flume的conf目录下新建一个文件</h4>

<p>vi   netcat-logger.conf（这个是我们自己定义的采集方案的配置文件）</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-preprocessor">#定义这个agent中各组件的名字</span>
<span class="hljs-preprocessor">##表示agent中的source组件</span>
a1<span class="hljs-preprocessor">.sources</span> = <span class="hljs-built_in">r1</span>
<span class="hljs-preprocessor">##表示的是下沉组件sink</span>
a1<span class="hljs-preprocessor">.sinks</span> = k1
<span class="hljs-preprocessor">##agent内部的数据传输通道channel，用于从source将数据传递到sink</span>
a1<span class="hljs-preprocessor">.channels</span> = c1

<span class="hljs-preprocessor">#描述和配置source组件：r1</span>
<span class="hljs-preprocessor">##netcat用于监听一个端口的</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.type</span> = netcat
<span class="hljs-preprocessor">##配置的绑定地址,这个机器的hostname是hadoop1,所以下面也可以配置成hadoop1</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.bind</span> = localhost
<span class="hljs-preprocessor">##配置的绑定端口</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.port</span> = <span class="hljs-number">44444</span>

<span class="hljs-preprocessor">#描述和配置sink组件：k1</span>
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = logger

<span class="hljs-preprocessor">##描述和配置channel组件，此处使用时内存缓存的方式</span>
<span class="hljs-preprocessor">#下面表示的是缓存到内存中，如果是文件，可以使用file的那种类型</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.type</span> = memory
<span class="hljs-preprocessor">#表示用多大的空间</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">1000</span>
<span class="hljs-preprocessor">#下面表示用事务的空间是多大</span>
a1<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.c</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100</span>

<span class="hljs-preprocessor"># 描述和配置source channel sink之间的连接关系,因为source和sink依赖channel来传递数据，所以要分别指定用的是哪个channel。</span>
a1<span class="hljs-preprocessor">.sources</span><span class="hljs-preprocessor">.r</span>1<span class="hljs-preprocessor">.channels</span> = c1
a1<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = c1</code></pre>



<h4 id="2启动agent去采集数据">2、启动agent去采集数据</h4>

<p>bin/flume-ng agent -c conf -f conf/netcat-logger.conf -n a1  -Dflume.root.logger=INFO,console <br>
-c conf   指定flume自身的配置文件所在目录 <br>
-f conf/netcat-logger.con  指定我们所描述的采集方案 <br>
-n a1  指定我们这个agent的名字（是配置文件中写的那个agent）</p>



<h4 id="3测试">3、测试</h4>

<p>先要往agent采集监听的端口上发送数据，让agent有数据可采 <br>
随便在一个能跟agent节点联网的机器上 <br>
telnet anget-hostname  port   （telnet hadoop1 44444）  <br>
<img src="https://img-blog.csdn.net/20170613201135859?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
在telnet终端上输入内容，最后回到打开的agent,发现有内容输出，输出的内容如下： <br>
<img src="https://img-blog.csdn.net/20170613201356847?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdG90b3R1enVvcXVhbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>综上所述：说明flume已经安装配置成功！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      spark学习-spark安装和启动
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xfks55/article/details/80784424				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="文章连接">文章连接</h2>

<p>1.<a href="https://blog.csdn.net/xfks55/article/details/80783375" rel="nofollow">spark学习-hadoop安装与启动</a> <br>
2.<a href="https://blog.csdn.net/xfks55/article/details/80784424" rel="nofollow">spark学习-spark安装和启动</a></p>

<hr>



<h2 id="服务器信息">服务器信息:</h2>

<p>172.18.101.157 spark-master  <br>
172.18.101.162 spark-slave1  <br>
172.18.132.162 spark-slave2</p>



<h2 id="安装前准备">安装前准备</h2>

<ul>
<li>spark下载 <br>
由于我们的hadoop的版本是2.7.6.所以在选中spark版本的时候,要注意. <br>
要选中pre-built for apache hadoop2.7 and later的版本 <br>
<img src="https://img-blog.csdn.net/20180623154151761?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hma3M1NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
</ul>



<pre class="prettyprint"><code class=" hljs perl"><span class="hljs-comment">#下载spark</span>
[root<span class="hljs-variable">@spark</span>-master <span class="hljs-keyword">local</span>]<span class="hljs-comment"># wget http://mirror.bit.edu.cn/apache/spark/spark-2.3.0/spark-2.3.0-bin-hadoop2.7.tgz</span>
<span class="hljs-comment">#解压</span>
[root<span class="hljs-variable">@spark</span>-master <span class="hljs-keyword">local</span>]<span class="hljs-comment"># tar -xvf spark-2.3.0-bin-hadoop2.7.tgz</span>
<span class="hljs-comment">#重命名文件夹</span>
[root<span class="hljs-variable">@spark</span>-master <span class="hljs-keyword">local</span>]<span class="hljs-comment"># tar -xvf spark-2.3.0-bin-hadoop2.7 spark-2.3.0</span>
<span class="hljs-comment">#配置环境变量</span>
export SPARK_HOME=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/spark-2.3.0
export PATH=$PATH:$SPARK_HOME/bin</span>

<span class="hljs-comment">#使环境变量生效</span>
[root<span class="hljs-variable">@spark</span>-master <span class="hljs-keyword">local</span>]<span class="hljs-comment"># source /etc/profile</span></code></pre>

<ul>
<li>scala的安装 <br>
spark需要安装Scala语言的支持,在spark的下载页面,我们看到要求最低的版本是scala2.10 <br>
我们这里安装的是scala-2.12.6</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-comment">#解压</span>
[root<span class="hljs-variable">@spark</span>-master opt]<span class="hljs-comment"># tar -xvf scala-2.12.6.tgz </span>
<span class="hljs-comment">#配置环境变量</span>
[root<span class="hljs-variable">@spark</span>-master opt]<span class="hljs-comment"># vim /etc/profile</span>
export <span class="hljs-constant">SCALA_HOME</span>=<span class="hljs-regexp">/usr/scala</span>-<span class="hljs-number">2.12</span>.<span class="hljs-number">6</span>
export <span class="hljs-constant">PATH</span>=<span class="hljs-variable">$PATH</span><span class="hljs-symbol">:</span><span class="hljs-variable">$SCALA_HOME</span>/bin
<span class="hljs-comment">#使环境变量生效</span>
[root<span class="hljs-variable">@spark</span>-master opt]<span class="hljs-comment"># source /etc/profile</span>
<span class="hljs-comment">#检查是否配置成功</span>
[root<span class="hljs-variable">@spark</span>-master opt]<span class="hljs-comment"># scala -version</span>
<span class="hljs-constant">Scala</span> code runner version <span class="hljs-number">2.12</span>.<span class="hljs-number">6</span> -- <span class="hljs-constant">Copyright</span> <span class="hljs-number">2002</span>-<span class="hljs-number">2018</span>, <span class="hljs-constant">LAMP</span>/<span class="hljs-constant">EPFL</span> <span class="hljs-keyword">and</span> <span class="hljs-constant">Lightbend</span>, <span class="hljs-constant">Inc</span>.</code></pre>

<p>安装完成之后,安装同样的步骤安装到另外两台slave机器上</p>



<h2 id="修改配置文件">修改配置文件</h2>

<p>需要修改的配置文件有两个  <br>
spark-env.sh ,spark-defaults.conf,slaves</p>

<p>spark-env.sh </p>

<pre class="prettyprint"><code class=" hljs coffeescript"><span class="hljs-comment"># 复制模版配置文件</span>
[root<span class="hljs-property">@spark</span>-master spark-<span class="hljs-number">2.3</span><span class="hljs-number">.0</span>]<span class="hljs-comment"># cp conf/spark-env.sh.template conf/spark-env.sh</span>
<span class="hljs-comment">#修改配置文件.</span>
[root<span class="hljs-property">@spark</span>-master spark-<span class="hljs-number">2.3</span><span class="hljs-number">.0</span>]<span class="hljs-comment"># vim conf/spark-env.sh</span>

<span class="hljs-reserved">export</span> JAVA_HOME=/opt/jdk1<span class="hljs-number">.8</span><span class="hljs-number">.0</span>_144

<span class="hljs-reserved">export</span> SCALA_HOME=/opt/scala-<span class="hljs-number">2.12</span><span class="hljs-number">.6</span>

<span class="hljs-reserved">export</span> HADOOP_HOME=<span class="hljs-regexp">/usr/local/hadoop-2.7.6/</span>

<span class="hljs-reserved">export</span> HADOOP_CONF_DIR=/usr/local/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.6</span>/etc/hadoop


<span class="hljs-comment">#定义管理端口</span>
<span class="hljs-reserved">export</span> SPARK_MASTER_WEBUI_PORT=<span class="hljs-number">8088</span>
<span class="hljs-comment">#定义master域名和端口</span>
<span class="hljs-reserved">export</span> SPARK_MASTER_HOST=spark-master
<span class="hljs-reserved">export</span> SPARK_MASTER_PORT=<span class="hljs-number">7077</span>
<span class="hljs-comment">#定义master的地址slave节点使用</span>
<span class="hljs-reserved">export</span> SPARK_MASTER_IP=spark-master
<span class="hljs-comment">#定义work节点的管理端口.work节点使用</span>
<span class="hljs-reserved">export</span> SPARK_WORKER_WEBUI_PORT=<span class="hljs-number">8088</span>
<span class="hljs-comment">#每个worker节点能够最大分配给exectors的内存大小 </span>
<span class="hljs-reserved">export</span> SPARK_WORKER_MEMORY=<span class="hljs-number">4</span>g</code></pre>

<p>slaves</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-comment">#复制模版配置文件</span>
[root<span class="hljs-variable">@spark</span>-master spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># cp conf/slaves.template conf/slaves</span>
<span class="hljs-comment">#修改配置文件.</span>
[root<span class="hljs-variable">@spark</span>-master spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">0</span>]<span class="hljs-comment"># vim conf/slaves</span>
spark-slave1
spark-slave2</code></pre>

<p>spark-defaults.conf</p>

<pre class="prettyprint"><code class=" hljs avrasm">[root@spark-master spark-<span class="hljs-number">2.3</span><span class="hljs-number">.0</span>]<span class="hljs-preprocessor"># vim conf/spark-defaults.conf</span>

spark<span class="hljs-preprocessor">.eventLog</span><span class="hljs-preprocessor">.enabled</span>=true
spark<span class="hljs-preprocessor">.eventLog</span><span class="hljs-preprocessor">.compress</span>=true
<span class="hljs-preprocessor">#保存在本地</span>
<span class="hljs-preprocessor">#spark.eventLog.dir=file://usr/local/hadoop-2.7.6/logs/userlogs</span>
<span class="hljs-preprocessor">#spark.history.fs.logDirectory=file://usr/local/hadoop-2.7.6/logs/userlogs</span>

<span class="hljs-preprocessor">#保存在hdfs上</span>
spark<span class="hljs-preprocessor">.eventLog</span><span class="hljs-preprocessor">.dir</span>=hdfs://spark-master:<span class="hljs-number">9000</span>/tmp/logs/root/logs
spark<span class="hljs-preprocessor">.history</span><span class="hljs-preprocessor">.fs</span><span class="hljs-preprocessor">.logDirectory</span>=hdfs://spark-master:<span class="hljs-number">9000</span>/tmp/logs/root/logs
spark<span class="hljs-preprocessor">.yarn</span><span class="hljs-preprocessor">.historyServer</span><span class="hljs-preprocessor">.address</span>=spark-master:<span class="hljs-number">18080</span></code></pre>

<h2 id="启动spark">启动spark</h2>



<pre class="prettyprint"><code class=" hljs avrasm">[root@spark-master spark-<span class="hljs-number">2.3</span><span class="hljs-number">.0</span>]<span class="hljs-preprocessor"># sbin/start-all.sh </span>
starting org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.master</span><span class="hljs-preprocessor">.Master</span>, logging to /usr/local/spark-<span class="hljs-number">2.3</span><span class="hljs-number">.0</span>/logs/spark-root-org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.master</span><span class="hljs-preprocessor">.Master</span>-<span class="hljs-number">1</span>-spark-master<span class="hljs-preprocessor">.out</span>
spark-slave1: starting org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.worker</span><span class="hljs-preprocessor">.Worker</span>, logging to /usr/local/spark-<span class="hljs-number">2.3</span><span class="hljs-number">.0</span>/logs/spark-root-org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.worker</span><span class="hljs-preprocessor">.Worker</span>-<span class="hljs-number">1</span>-spark-slave1<span class="hljs-preprocessor">.out</span>
spark-slave2: starting org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.worker</span><span class="hljs-preprocessor">.Worker</span>, logging to /usr/local/spark-<span class="hljs-number">2.3</span><span class="hljs-number">.0</span>/logs/spark-root-org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.spark</span><span class="hljs-preprocessor">.deploy</span><span class="hljs-preprocessor">.worker</span><span class="hljs-preprocessor">.Worker</span>-<span class="hljs-number">1</span>-spark-slave2<span class="hljs-preprocessor">.out</span></code></pre>

<p>查看web管理界面 <br>
<a href="http://172.18.101.157:8088/" rel="nofollow">http://172.18.101.157:8088/</a> <br>
<img src="https://img-blog.csdn.net/20180623172935170?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hma3M1NQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
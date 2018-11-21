---
layout:     post
title:      大数据手册(Spark)--Spark安装配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="准备工作">准备工作</h1>

<ul>
<li>安装spark <a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html" rel="nofollow">运行环境 jdk</a></li>
<li>如果使用python API，安装运行环境 <a href="https://www.python.org/getit/" rel="nofollow">python2或python3</a></li>
<li>如果使用scala语言，安装运行环境（<a href="https://www.scala-lang.org/download/" rel="nofollow">官网链接</a>）</li>
<li>安装spark服务器，配置<a href="https://blog.csdn.net/qq_41518277/article/details/80720390" rel="nofollow">免密登陆</a></li>
</ul>



<h1 id="spark下载和安装">Spark下载和安装</h1>

<ul>
<li><a href="http://spark.apache.org/downloads.html" rel="nofollow">官网下载spark文件</a>（搭建spark不需要hadoop，如果已经有Hadoop集群，可下载相应的版本）</li>
<li>解压到指定目录</li>
</ul>



<pre class="prettyprint"><code class="language-bash hljs ">tar zxvf spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">7</span>.tgz -C /usr/local/</code></pre>



<h1 id="配置spark环境变量">配置spark环境变量</h1>



<pre class="prettyprint"><code class="language-bash hljs ">vi /etc/profile

<span class="hljs-comment"># 定义spark_home并把路径加到path参数中</span>
SPARK_HOME=/usr/local/spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">7</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$SPARK_HOME</span>/bin:<span class="hljs-variable">$SPARK_HOME</span>/sbin:<span class="hljs-variable">$PATH</span>

<span class="hljs-comment"># 定义Scala_home目录，不使用scala语言时无需进行配置</span>
SCALA_HOME=/usr/local/scala-<span class="hljs-number">2.10</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$SCALA_HOME</span>/bin:<span class="hljs-variable">$PATH</span>

<span class="hljs-comment">#加载配置文件，可在任意位置启动pyspark,spark-shell,sparkR</span>
<span class="hljs-built_in">source</span> /etc/profile</code></pre>

<h1 id="spark配置文件">Spark配置文件</h1>



<pre class="prettyprint"><code class="language-bash hljs "><span class="hljs-comment">#--------切换到spark配置目录-------</span>
<span class="hljs-built_in">cd</span> /usr/local/spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">7</span>/conf/

<span class="hljs-comment">#--------配置文件spark-enc.sh--------</span>
mv spark-env.sh.template spark-env.sh  <span class="hljs-comment">#重命名文件</span>
vi spark-env.sh   
<span class="hljs-comment"># 在文件末尾添加环境，保存并退出</span>
<span class="hljs-keyword">export</span> JAVA_HOME=/usr/local/jdk1.<span class="hljs-number">8.0</span>_171  <span class="hljs-comment">#指定jdk位置</span>
<span class="hljs-keyword">export</span> SPARK_MASTER_IP=master       <span class="hljs-comment">#master主机IP（单机为localhost,ifconfig命令查看本机IP）</span>
<span class="hljs-keyword">export</span> SPARK_MASTER_PORT=<span class="hljs-number">7077</span>       <span class="hljs-comment">#master主机端口</span>
<span class="hljs-comment"># 使用Scala语言时配置</span>
<span class="hljs-keyword">export</span> SCALA_HOME=/usr/local/scala-<span class="hljs-number">2.10</span>
<span class="hljs-comment"># 已有Hadoop集群时配置</span>
<span class="hljs-keyword">export</span> HADOOP_HOME=/usr/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>
<span class="hljs-keyword">export</span> HADOOP_CONF_DIR=/usr/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/etc/hadoop

<span class="hljs-comment">#--------配置文件slaves--------</span>
mv slaves.template slaves  <span class="hljs-comment">#重命名文件</span>
vi slaves    
<span class="hljs-comment">#在该文件中添加子节点的IP或主机名（worker节点），保存并退出</span>
node01
node02

<span class="hljs-comment">#------将配置好的spark拷贝到其他节点上------</span>
scp -r spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">7</span>/ node01:/usr/
scp -r spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">7</span>/ node02:/usr/</code></pre>



<h1 id="启动spark集群">启动Spark集群</h1>

<p>spark集群配置完毕，目前是1个master，2个worker</p>



<pre class="prettyprint"><code class="language-bash hljs "><span class="hljs-comment">#----------在master上启动集群------------</span>
<span class="hljs-built_in">cd</span> /usr/local/spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">7</span>/sbin/
bash start-all.sh     <span class="hljs-comment">#或者bash start-master.sh + bash start-slaves.sh</span>
<span class="hljs-comment">#----------查看进程-----------------</span>
jps
<span class="hljs-comment">#----------查看集群状态----------</span>
master任意浏览器登陆：http://master:<span class="hljs-number">8080</span>/</code></pre>



<h1 id="启动shell界面">启动Shell界面</h1>



<pre class="prettyprint"><code class="language-bash hljs "><span class="hljs-built_in">cd</span> /usr/local/spark-<span class="hljs-number">2.3</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">7</span>/bin/

<span class="hljs-comment">#-------------选择一种编程环境启动-----------------</span>
bash spark-shell    <span class="hljs-comment"># 启动Scala shell</span>
bash pyspark        <span class="hljs-comment"># 启动python shell</span>
bash sparkR         <span class="hljs-comment"># 启动R shell</span>
<span class="hljs-comment">#启动时若Java版本报错，安装需要的版本即可</span>
<span class="hljs-built_in">sudo</span> apt-get install openjdk-<span class="hljs-number">8</span>-jdk</code></pre>

<hr>

<p><a href="https://blog.csdn.net/weixin_36394852/article/details/76030317" rel="nofollow">spark-2.2.0安装和部署——Spark集群学习日记</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
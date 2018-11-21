---
layout:     post
title:      Hadoop系列－－Hadoop再安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_33429968/article/details/76914717				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1-安装步骤简介"><strong>1 安装步骤简介</strong></h2>



<h3 id="11-前言"><strong>1.1 前言</strong></h3>

<p>　　讲真，上次的Hadoop的安装实际上是失败的，正应了那句话，Hadoop的学习是有一定的门槛的，从Hadoop的安装就可见一斑。 <br>
　　最近看了一些关于Hadoop的理论知识，对Hadoop的理解更进了一步，便决定重新安装一次Hadoop。</p>



<h3 id="12-安装大体步骤"><strong>1.2 安装大体步骤</strong></h3>

<p>　　因为Hadoop是Java语言开发的，所以必然需要Java的运行环境，那么就需要配置JDK，鉴于开发的效率，Linux系统环境是最理想的开发环境了，那么可以1配置Linux虚拟机或者2购买远程Linux主机或者3直接安装Linux系统；最后安装配置Hadoop即可。 <br>
　　总体来说，大体步骤如下：</p>

<ul>
<li>准备Hadoop系统环境</li>
<li>配置JDK</li>
<li>安装Hadoop</li>
<li>配置Hadoop</li>
</ul>



<h2 id="2-安装步骤详解"><strong>2 安装步骤详解</strong></h2>

<p>　　下面展开详解。</p>



<h3 id="21-准备hadoop系统环境"><strong>2.1 准备Hadoop系统环境</strong></h3>

<p>　　按照上面说的，方法有三个： <br>
　　</p>

<ul>
<li>配置Linux虚拟机</li>
<li>购买远程Linux主机</li>
<li>直接安装Linux系统</li>
</ul>



<h3 id="22-配置jdk"><strong>2.2 配置JDK</strong></h3>

<p><strong>1.下载JDK</strong> <br>
<strong>2.安装JDK</strong> <br>
<strong>3.配置环境变量</strong> <br>
　　可以配置.bashrc和/etc/profile两个文件，但是.bashrc的优先级高于/etc/profile。 <br>
　　个人参照如下： <br>
　　</p>



<pre class="prettyprint"><code class=" hljs bash">  <span class="hljs-keyword">export</span> JAVA_HOME=/home/gaoziqiang/devel/jdk/jdk_8/jdk1.<span class="hljs-number">8.0</span>
  <span class="hljs-keyword">export</span> JRE_HOME=<span class="hljs-variable">$JAVA_HOME</span>/jre                                                        
  <span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$JAVA_HOME</span>/bin:JRE_HOME/bin:<span class="hljs-variable">$PATH</span>
  <span class="hljs-keyword">export</span> CLASSPATH=.:<span class="hljs-variable">$JAVA_HOME</span>/lib/dt.jar:<span class="hljs-variable">$JAVA_HOME</span>/lib/tools.jar
</code></pre>



<h3 id="23-安装hadoop"><strong>2.3 安装Hadoop</strong></h3>

<p>　　经过十多年的发展，Hadoop已经发布了ver1.x和ver2.x两套版本，ver1.x比较稳定。故在这使用ver1.2版本。 <br>
<strong>1.下载hadoop.1.2.1.tar.gz</strong> <br>
<strong>2.安装Hadoop</strong> <br>
　　使用命令在相应的目录下： <br>
　　</p>

<blockquote>
  <p>tar -zxvf hadoop.1.2.1.tar.gz</p>
</blockquote>



<h3 id="24-配置hadoop"><strong>2.4 配置Hadoop</strong></h3>

<p><strong>1.配置四个文件</strong> <br>
<strong>A.core-site.xml</strong></p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">discription</span>&gt;</span>Hadoop的工作目录<span class="hljs-tag">&lt;/<span class="hljs-title">discription</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/hadoop/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">discription</span>&gt;</span>HDFS的namenode工作目录<span class="hljs-tag">&lt;/<span class="hljs-title">discription</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.default.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">discription</span>&gt;</span>namenode的访问方式<span class="hljs-tag">&lt;/<span class="hljs-title">discription</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p><strong>B.hdfs-site.xml</strong></p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/hadoop/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">discription</span>&gt;</span>datanode的存放目录<span class="hljs-tag">&lt;/<span class="hljs-title">discription</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p><strong>C.mapred-site.xml</strong></p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapred.job.tracker<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:9001<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">discription</span>&gt;</span>MapReduce的地址与端口<span class="hljs-tag">&lt;/<span class="hljs-title">discription</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p><strong>D.hadoop-env.xml</strong> <br>
　　配置$JAVA_HOME</p>



<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-variable">$JAVA_HOME</span>=<span class="hljs-regexp">/home/gaoziqiang</span><span class="hljs-regexp">/devel/jdk</span><span class="hljs-regexp">/jdk_8/jdk</span>1.<span class="hljs-number">8.0</span></code></pre>

<p><strong>2.声明$HADOOP_HOME环境变量</strong> <br>
　　在/etc/profile文件。 <br>
　　</p>



<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-constant">HADOOP_HOME</span>=<span class="hljs-regexp">/home/gaoziqiang</span><span class="hljs-regexp">/devel/hadoop</span><span class="hljs-regexp">/hadoop-1.2.1</span></code></pre>



<h2 id="3-启动hadoop"><strong>3 启动Hadoop</strong></h2>

<p><strong>1初始化文件系统</strong> <br>
　　在HADOOP_HOME/bin： <br>
　　</p>

<blockquote>
  <p>./hadoop namenode -format</p>
</blockquote>

<p><strong>2.启动Hadoop</strong> <br>
　　在HADOOP_HOME/bin： <br>
　　</p>

<blockquote>
  <p>./start-all.sh</p>
</blockquote>

<p><strong>3.检测是否启动成功</strong> <br>
　　使用命令:</p>

<blockquote>
  <p>jps</p>
</blockquote>

<p>　　成功后会显示一下五项内容，缺一不可。 <br>
　　<img src="https://img-blog.csdn.net/20170808160747908?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzM0Mjk5Njg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="jps" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
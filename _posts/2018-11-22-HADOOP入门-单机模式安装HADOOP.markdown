---
layout:     post
title:      HADOOP入门-单机模式安装HADOOP
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="2-安装hadoop"><strong>2. 安装HADOOP</strong></h1>

<hr>



<h2 id="21-单机模式安装hadoop"><strong>2.1. 单机模式安装Hadoop</strong></h2>

<p>安装单机模式的Hadoop无需配置，在这种方式下，Hadoop被认为是一个独立的Java进程，这种方式经常用来调试。</p>



<h3 id="211-上传安装包"><strong>2.1.1. 上传安装包</strong></h3>

<p>使用hadoop账户，将Hadoop安装包上传至/home/hadoop目录下，并解压缩安装包。</p>



<pre class="prettyprint"><code class="language-bash hljs ">[hadoop@hadoop ~]$ whoami 
hadoop
[hadoop@hadoop ~]$ <span class="hljs-built_in">pwd</span>
/home/hadoop
[hadoop@hadoop ~]$ ll
total <span class="hljs-number">59388</span>
-rw-r--r--. <span class="hljs-number">1</span> hadoop hadoop <span class="hljs-number">60811130</span> May <span class="hljs-number">21</span> <span class="hljs-number">14</span>:<span class="hljs-number">52</span> hadoop-<span class="hljs-number">1.0</span>.<span class="hljs-number">1</span>.tar.gz
[hadoop@hadoop ~]$ tar -zxvf hadoop-<span class="hljs-number">1.0</span>.<span class="hljs-number">1</span>.tar.gz 
[hadoop@hadoop ~]$ ll
total <span class="hljs-number">59392</span>
drwxr-xr-x. <span class="hljs-number">14</span> hadoop hadoop     <span class="hljs-number">4096</span> Feb <span class="hljs-number">14</span>  <span class="hljs-number">2012</span> hadoop-<span class="hljs-number">1.0</span>.<span class="hljs-number">1</span>
-rw-r--r--.  <span class="hljs-number">1</span> hadoop hadoop <span class="hljs-number">60811130</span> May <span class="hljs-number">21</span> <span class="hljs-number">14</span>:<span class="hljs-number">52</span> hadoop-<span class="hljs-number">1.0</span>.<span class="hljs-number">1</span>.tar.gz</code></pre>



<h3 id="212-配置jdk的主目录"><strong>2.1.2. 配置JDK的主目录</strong></h3>

<p><strong>修改Hadoop环境配置脚本</strong></p>



<pre class="prettyprint"><code class="language-bash hljs ">[hadoop@hadoop conf]$ whoami 
hadoop
[hadoop@hadoop conf]$ <span class="hljs-built_in">pwd</span>
/home/hadoop/hadoop-<span class="hljs-number">1.0</span>.<span class="hljs-number">1</span>/conf
[hadoop@hadoop conf]$ vi hadoop-env.sh </code></pre>

<p><strong>原来内容</strong>    </p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># export JAVA_HOME=/usr/lib/j2sdk1.5-sun</span></code></pre>

<p><strong>修改后的内容</strong>    </p>



<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/usr/lib</span><span class="hljs-regexp">/jvm/jdk</span>1.<span class="hljs-number">6.0_45</span></code></pre>



<h3 id="213-修改hadoop配置文件"><strong>2.1.3. 修改Hadoop配置文件</strong></h3>

<p><strong>核心配置文件</strong> <br>
conf/core-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.default.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>

<p><strong>HDFS配置文件</strong> <br>
conf/hdfs-site.xml    </p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>MapReduce配置文件</strong> <br>
conf/mapred-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapred.job.tracker<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:9001<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>



<h3 id="214-格式化hdfs文件系统"><strong>2.1.4. 格式化HDFS文件系统</strong></h3>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@hadoop</span> bin]<span class="hljs-variable">$ </span>pwd
/home/hadoop/hadoop-<span class="hljs-number">1.0</span>.<span class="hljs-number">1</span>/bin
[hadoop<span class="hljs-variable">@hadoop</span> bin]<span class="hljs-variable">$ </span>./hadoop namenode -format</code></pre>



<h3 id="215-启动hadoop"><strong>2.1.5. 启动Hadoop</strong></h3>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@hadoop</span> bin]<span class="hljs-variable">$ </span>./start-all.sh </code></pre>



<h3 id="216-验证hadoop是否安装成功"><strong>2.1.6. 验证Hadoop是否安装成功</strong></h3>

<p>打开浏览器，分别输入以下地址：</p>



<pre class="prettyprint"><code class="language-http hljs "><span class="hljs-attribute">http://localhost:50030  MapReduce的WEB页面
http://localhost:50070  HDFS的WEB页面</span></code></pre>

<p>如果可以访问，则表示安装成功</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
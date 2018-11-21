---
layout:     post
title:      [big data 1] Hadoop 2 .x 伪分布搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>这是第一篇关于big data的文章，首先来搭建一个ubuntu 14.04 LTS 系统的hadoop 2.x 的一个<strong>伪分布</strong>式环境。</p>

<p></p><div class="toc">
<ul>
<li><ul>
<li><a href="#hadoop-1x-%E5%92%8C-2x-%E6%9E%B6%E6%9E%84" rel="nofollow">Hadoop 1x 和 2x 架构</a></li>
<li>
<li><a href="#%E4%BF%AE%E6%94%B9ubuntu%E7%9A%84hostname-%E5%92%8Chosts" rel="nofollow">修改ubuntu的hostname 和hosts</a></li>
<li><a href="#%E5%AE%89%E8%A3%85jdk" rel="nofollow">安装JDK</a></li>
<li><a href="#%E5%AE%89%E8%A3%85hadoop" rel="nofollow">安装Hadoop</a></li>
<li><a href="#%E6%A0%BC%E5%BC%8F%E5%8C%96-hadoop" rel="nofollow">格式化 Hadoop</a></li>
<li><a href="#%E6%B5%8B%E8%AF%95hadoop" rel="nofollow">测试hadoop</a></li>
</ul>
</li>
</ul>
</div>


<h2 id="hadoop-1x-和-2x-架构">Hadoop 1.x 和 2.x 架构</h2>



<h2 id="title-1"><img src="https://img-blog.csdn.net/20151110155859639" alt=" hadoop 1.x 和2.x 结构对比" title=""></h2>



<h2 id="修改ubuntu的hostname-和hosts">修改ubuntu的hostname 和hosts</h2>

<p>修改hostname为kuyu</p>



<pre class="prettyprint"><code class="language-shell hljs bash">$ <span class="hljs-built_in">sudo</span> vim /etc/hostname</code></pre>

<p>在hosts中添加<strong>192.168.128.134 kuyu</strong>，其中192.168.128.134 是主机的ip地址,可以通过<span class="MathJax_Preview"></span><span style="" aria-readonly="true" role="textbox" id="MathJax-Element-1-Frame" class="MathJax"><nobr><span style="width: 4.75em; display: inline-block;" id="MathJax-Span-1" class="math"><span style="display: inline-block; position: relative; width: 3.787em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.802em, 1000em, 3.032em, -0.459em); top: -2.667em; left: 0em;"><span id="MathJax-Span-2" class="mrow"><span style="font-family: MathJax_Math; font-style: italic;" id="MathJax-Span-3" class="mi">i</span><span style="font-family: MathJax_Math; font-style: italic;" id="MathJax-Span-4" class="mi">f<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.06em;"></span></span><span style="font-family: MathJax_Math; font-style: italic;" id="MathJax-Span-5" class="mi">c</span><span style="font-family: MathJax_Math; font-style: italic;" id="MathJax-Span-6" class="mi">o</span><span style="font-family: MathJax_Math; font-style: italic;" id="MathJax-Span-7" class="mi">n</span><span style="font-family: MathJax_Math; font-style: italic;" id="MathJax-Span-8" class="mi">f<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.06em;"></span></span><span style="font-family: MathJax_Math; font-style: italic;" id="MathJax-Span-9" class="mi">i</span><span style="font-family: MathJax_Math; font-style: italic;" id="MathJax-Span-10" class="mi">g<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span></span><span style="display: inline-block; width: 0px; height: 2.667em;"></span></span></span><span style="border-left: 0em solid; display: inline-block; overflow: hidden; width: 0px; height: 1.271em; vertical-align: -0.323em;"></span></span></nobr></span><script id="MathJax-Element-1" type="math/tex">ifconfig</script> 命令查询得到.</p>



<pre class="prettyprint"><code class="language-shell hljs bash">$ <span class="hljs-built_in">sudo</span> vim /etc/hosts</code></pre>



<h2 id="安装jdk">安装JDK</h2>

<p>hadoop官网介绍<a href="http://wiki.apache.org/hadoop/HadoopJavaVersions" rel="nofollow">推荐</a>安装的jdk版本是jdk1.6和jdk1.7,所以在Oracle 官网下载<a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" rel="nofollow">JDK 7</a> .</p>



<pre class="prettyprint"><code class="language-shell hljs bash"><span class="hljs-comment"># 解压JDK</span>
$  tar -zxvf jdk-*-linux-x64.tar.gz
<span class="hljs-comment"># 重命令jdk* 为java</span>
$ mv jdk*/ java
<span class="hljs-comment"># 移动java到/usr/local 目录下</span>
$ <span class="hljs-built_in">sudo</span> mv java/ /usr/local/
<span class="hljs-comment"># 配置环境变量</span>
$ <span class="hljs-built_in">sudo</span> vim /etc/profile 
<span class="hljs-keyword">export</span> JAVA_HOME=/usr/local/java
<span class="hljs-keyword">export</span> CLASSPATH=.:<span class="hljs-variable">$CLASSPATH</span>:<span class="hljs-variable">$JAVA_HOME</span>/lib:<span class="hljs-variable">$JAVA_HOM</span>/jre/lib
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$JAVA_HOME</span>/bin:<span class="hljs-variable">$JAVA_HOME</span>/jre/bin
<span class="hljs-comment"># source </span>
$ <span class="hljs-built_in">source</span> /etc/profile</code></pre>



<h2 id="安装hadoop">安装Hadoop</h2>

<p>在hadoop的<a href="http://archive.apache.org/dist/" rel="nofollow">官方滤镜</a>下载hadoop的最新稳定版hadoop 2.x </p>



<pre class="prettyprint"><code class="language-shell hljs bash"><span class="hljs-comment"># 解压hadoop</span>
$  tar -zxvf hadoop-<span class="hljs-number">2</span>.*.*.tar.gz
<span class="hljs-comment"># 重命令hadoop-2.*.*为hadoop</span>
$ mv hadoop-<span class="hljs-number">2</span>.*.* hadoop
<span class="hljs-comment"># 移动hadoop到/usr/local 目录下</span>
$ <span class="hljs-built_in">sudo</span> mv hadoop/ /usr/local/
<span class="hljs-comment"># 配置环境变量</span>
$ <span class="hljs-built_in">sudo</span> vim /etc/profile 
<span class="hljs-comment"># hadoop</span>
<span class="hljs-keyword">export</span> HADOOP_HOME=/usr/local/hadoop
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HADOOP_HOME</span>/bin:<span class="hljs-variable">$HADOOP_HOME</span>/sbin    
<span class="hljs-comment"># source </span>
$ <span class="hljs-built_in">source</span> /etc/profile</code></pre>

<p>除此之外，我们还需要修改hadoop的配置文件</p>



<pre class="prettyprint"><code class="language-shell hljs bash">$ <span class="hljs-built_in">cd</span> /usr/local/hadoop/etc/hadoop</code></pre>

<p><strong>hadoop-env.sh</strong> </p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-comment">#添加java 安装路径</span>
<span class="hljs-variable">$ </span>export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/java</span></code></pre>

<p><strong>Core-site.xml</strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-comment">&lt;!-- 指定HDFS namenode的通信地址 --&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://kuyu:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-comment">&lt;!-- 指定hadoop运行时产生文件的存储路径 --&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/loca/hadoop/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>hdfs-site.xml</strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-comment">&lt;!-- 设置hdfs副本数量 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hadoop/dfs.data/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hadoop/dfs.data/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>

<p><strong>mapred-site.xml</strong> <br>
如果mapred-site.xml 没有的话，需要cp一份</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">$ <span class="hljs-keyword">cp</span> mapred-site<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.template</span> mapred-site<span class="hljs-preprocessor">.xml</span></code></pre>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-comment">&lt;!-- 通知框架MR使用YARN --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>yarn-site.xml</strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-comment">&lt;!-- reducer取数据的方式是mapreduce_shuffle --&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

            <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>kuyu<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>注意</strong> <br>
在安装hadoop 的时候另外指定了两个目录/usr/local/hadoop/tmp 和 /usr/local/hadoop/dfs.data, 需要事先创建</p>



<pre class="prettyprint"><code class="language-shell hljs perl">$ <span class="hljs-keyword">mkdir</span> /usr/<span class="hljs-keyword">local</span>/hadoop/tmp
$ <span class="hljs-keyword">mkdir</span> /usr/<span class="hljs-keyword">local</span>/hadoop/dfs.data</code></pre>



<h2 id="格式化-hadoop">格式化 Hadoop</h2>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>hdfs namenode -format</code></pre>



<h2 id="测试hadoop">测试hadoop</h2>



<pre class="prettyprint"><code class="language-shell hljs livecodeserver"><span class="hljs-comment"># 启动hadoop</span>
$ start-dfs.sh
$ start-yarn.sh
<span class="hljs-comment"># 上传文件</span>
$ hadoop fs -<span class="hljs-built_in">put</span> [ubuntu <span class="hljs-built_in">file</span> path] [hdfs <span class="hljs-built_in">file</span> path]
<span class="hljs-comment"># 下载文件</span>
$ hadoop fs -<span class="hljs-built_in">get</span> [hdfs <span class="hljs-built_in">file</span> path] [ubuntu <span class="hljs-built_in">file</span> path]
<span class="hljs-comment">#运行wordconut 代码</span>
$ hadoop jar /usr/<span class="hljs-built_in">local</span>/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-<span class="hljs-number">2.7</span><span class="hljs-number">.1</span>.jar wordcount [hdfs <span class="hljs-built_in">file</span> input path] [hdfs <span class="hljs-built_in">file</span> output path]</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Hadoop学习（一）: Ubuntu上安装Hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Hadoop学习（一）: Ubuntu上安装Hadoop</p>

<p>１．安装ｓｓｈ</p>

<pre class="prettyprint"><code class="language-shell hljs lasso">$ sudo apt<span class="hljs-attribute">-get</span> install openssh<span class="hljs-attribute">-client</span>
$ sudo apt<span class="hljs-attribute">-get</span> install openssh<span class="hljs-attribute">-server</span></code></pre>

<p>2.查看ＪＡＶＡ_HOME变量值</p>



<pre class="prettyprint"><code class="language-shell hljs ">/opt/jdk1.8.0_91</code></pre>

<p>３．安装ｈａｄｏｏｐ－２．７．２ <br>
从官网（<a href="http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.2/" rel="nofollow">http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.2/</a>）下载，解压到hadoop-2.7.2</p>

<p>４．修改hadoop-2.7.2的etc/hadoop/hadoop-env.sh文件，设置ＪＡＶＡ_ＨＯＭＥ</p>



<pre class="prettyprint"><code class="language-shell hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/opt/jdk1.<span class="hljs-number">8.0</span>_91</code></pre>

<p>输入以下命令,弹出ｈａｄｏｏｐ的用法，则配置成功</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>bin/hadoop</code></pre>

<p>ｈａｄｏｏｐ支持以下三种模式:</p>

<p>５．Standalone Operation（单机模式） <br>
<strong>开启ｓｓｈ服务</strong></p>



<pre class="prettyprint"><code class="language-shell hljs bash">$ <span class="hljs-built_in">sudo</span> /etc/init.d/ssh start</code></pre>

<p><strong>免密码登陆</strong></p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-comment">#client端产生密钥：</span>
<span class="hljs-variable">$ </span>ssh-keygen -t rsa
<span class="hljs-comment">#ｓｅｒｖｅｒ端：</span>
<span class="hljs-variable">$ </span>cp id_rsa.pub authorized_keys
<span class="hljs-variable">$ </span>chmod <span class="hljs-number">600</span> authorized_keys</code></pre>

<p>测试：The following example copies the unpacked conf directory to use as input and then finds and displays every match of the given regular expression. Output is written to the given output directory.</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"> <span class="hljs-variable">$ </span>mkdir input
 <span class="hljs-variable">$ </span>cp etc/hadoop/*.xml input
 <span class="hljs-variable">$ </span>bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>.jar grep input output <span class="hljs-string">'dfs[a-z.]+'</span>
 <span class="hljs-variable">$ </span>cat output/*</code></pre>

<p>６．Pseudo-Distributed Operation(单机伪分布模式) <br>
修改两处文件： <br>
etc/hadoop/core-site.xml:</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>etc/hadoop/hdfs-site.xml:</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>ｓｓｈ登陆：</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>ssh localhost</code></pre>

<p>执行：</p>



<pre class="prettyprint"><code class="language-shell hljs lasso"><span class="hljs-comment">//Format the filesystem:</span>
$ bin/hdfs namenode <span class="hljs-attribute">-format</span>

<span class="hljs-comment">//Start NameNode daemon and DataNode daemon:</span>
$ sbin/start<span class="hljs-attribute">-dfs</span><span class="hljs-built_in">.</span>sh</code></pre>

<p>Browse the web interface for the NameNode; by default it is available at: NameNode - <a href="http://localhost:50070/" rel="nofollow">http://localhost:50070/</a></p>



<pre class="prettyprint"><code class="language-shell hljs lasso"><span class="hljs-comment">//Make the HDFS directories required to execute MapReduce jobs:</span>
$ bin/hdfs dfs <span class="hljs-attribute">-mkdir</span> /user
$ bin/hdfs dfs <span class="hljs-attribute">-mkdir</span> /user<span class="hljs-subst">/</span><span class="hljs-subst">&lt;</span>username<span class="hljs-subst">&gt;</span>

<span class="hljs-comment">//Copy the input files into the distributed filesystem:</span>
$ bin/hdfs dfs <span class="hljs-attribute">-put</span> etc/hadoop input

<span class="hljs-comment">//Run some of the examples provided:</span>
$ bin/hadoop jar share/hadoop/mapreduce/hadoop<span class="hljs-attribute">-mapreduce</span><span class="hljs-attribute">-examples</span><span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.2</span><span class="hljs-built_in">.</span>jar grep input output <span class="hljs-string">'dfs[a-z.]+'</span>

<span class="hljs-comment">//Examine the output files: Copy the output files from the distributed filesystem to the local filesystem and examine them:</span>
$ bin/hdfs dfs <span class="hljs-attribute">-get</span> output output
$ cat output<span class="hljs-comment">/*
//or view the output files on the distributed filesystem:
$ bin/hdfs dfs -cat output/*

//When you’re done, stop the daemons with:
$ sbin/stop-dfs.sh</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
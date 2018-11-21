---
layout:     post
title:      Ubuntu:Spark安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ShellDawn/article/details/79129842				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>创建hadoop用户：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">sudo</span> user add -m Hadoop <span class="hljs-operator">-s</span> /bin/bash
<span class="hljs-built_in">sudo</span> passed hadoop
<span class="hljs-built_in">sudo</span> adduser Hadoop <span class="hljs-built_in">sudo</span></code></pre>

<hr>

<p>登陆hadoop用户</p>



<pre class="prettyprint"><code class=" hljs lasso">sudo apt<span class="hljs-attribute">-get</span> update
sudo apt<span class="hljs-attribute">-get</span> install vim
sudo apt<span class="hljs-attribute">-get</span> install openssh<span class="hljs-attribute">-server</span>
ssh localhost
exit
cd ~<span class="hljs-subst">/</span><span class="hljs-built_in">.</span>ssh<span class="hljs-subst">/</span>
ssh<span class="hljs-attribute">-keygen</span> <span class="hljs-attribute">-t</span> rsa
cat <span class="hljs-built_in">.</span>/id_rsa<span class="hljs-built_in">.</span>pub <span class="hljs-subst">&gt;&gt;</span> <span class="hljs-built_in">.</span>/authorized_keys</code></pre>

<hr>

<p>安装java环境</p>



<pre class="prettyprint"><code class=" hljs lasso">sudo apt<span class="hljs-attribute">-get</span> install openjdk<span class="hljs-subst">-</span><span class="hljs-number">8</span><span class="hljs-attribute">-jre</span> openjdk<span class="hljs-subst">-</span><span class="hljs-number">8</span><span class="hljs-attribute">-jdk</span>
vim ~<span class="hljs-subst">/</span><span class="hljs-built_in">.</span>bashrc</code></pre>

<p>文件最后添加路径</p>



<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/usr/lib</span><span class="hljs-regexp">/jvm/java</span>-<span class="hljs-number">8</span>-openjdk-amd64</code></pre>

<p>是文件生效</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">source</span> ~/.bashrc</code></pre>

<hr>

<p>安装hadoop2 <br>
选择stable文件夹下不带src的tar包 <br>
下载链接 <br>
<a href="http://mirror.bit.edu.cn/apache/hadoop/common/" rel="nofollow">http://mirror.bit.edu.cn/apache/hadoop/common/</a>或者 <br>
<a href="http://mirrors.cnnic.cn/apache/hadoop/common/" rel="nofollow">http://mirrors.cnnic.cn/apache/hadoop/common/</a></p>

<pre class="prettyprint"><code class=" hljs lasso">sudo tar <span class="hljs-attribute">-zxvf</span> ~/Downloads/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.9</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz <span class="hljs-attribute">-C</span> /usr/<span class="hljs-built_in">local</span>
cd /usr/<span class="hljs-built_in">local</span><span class="hljs-subst">/</span>
sudo mv <span class="hljs-built_in">.</span>/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.6</span><span class="hljs-number">.0</span><span class="hljs-subst">/</span> <span class="hljs-built_in">.</span>/hadoop
sudo chown <span class="hljs-attribute">-R</span> Hadoop <span class="hljs-built_in">.</span>/hadoop
cd /usr/<span class="hljs-built_in">local</span>/hadoop
<span class="hljs-built_in">.</span>/bin/hadoop version</code></pre>

<p>到此已经安装完成</p>

<hr>

<p>Hadoop单机配置</p>

<pre class="prettyprint"><code class=" hljs perl">cd /usr/<span class="hljs-keyword">local</span>/hadoop
<span class="hljs-keyword">mkdir</span> ./input
cp ./etc/hadoop/<span class="hljs-variable">*.</span>xml ./input
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-<span class="hljs-variable">*.</span>jar <span class="hljs-keyword">grep</span> ./input ./output <span class="hljs-string">'dfs[a-z.]+'</span>
cat ./output/*
rm -r ./output</code></pre>

<hr>

<p>hadoop伪分布式配置 <br>
修改/usr/local/hadoop/etc/hadoop/core-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Abase for other temporary directories.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>修改/usr/local/hadoop/etc/hadoop/hdfs-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp/dfs/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp/dfs/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>执行NameNode初始化</p>

<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/bin/hdfs namenode <span class="hljs-attribute">-format</span>
<span class="hljs-built_in">.</span>/sbin/start<span class="hljs-attribute">-dfs</span><span class="hljs-built_in">.</span>sh</code></pre>

<p>成功启动后，可以Web访问<a href="http://localhost:50070" rel="nofollow">http://localhost:50070</a> <br>
运行伪分布式实例</p>

<pre class="prettyprint"><code class=" hljs coffeescript">./bin/hdfs dfs -mkdir -p /user/hadoop
.<span class="hljs-regexp">/bin/hdfs dfs -put ./etc/hadoop/</span>*.xml input
./bin/hdfs dfs -ls input
./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output <span class="hljs-string">'dfs[a-z.]+'</span>
.<span class="hljs-regexp">/bin/hdfs dfs -cat output/</span>*
rm -r ./output
./bin/hdfs dfs -get output ./output
cat .<span class="hljs-regexp">/output/</span>*
./bin/hdfs dfs -rm -r output
./sbin/stop-dfs.sh</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Hadoop笔记：HDFS环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/japson_iot/article/details/80467481				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="大数据笔记hdfs环境搭建">大数据笔记：HDFS环境搭建</h1>

<p>标签： 大数据</p>

<p></p><div class="toc">
<ul>
<li><a href="#%E5%A4%A7%E6%95%B0%E6%8D%AE%E7%AC%94%E8%AE%B0hdfs%E7%8E%AF%E5%A2%83%E6%90%AD%E5%BB%BA" rel="nofollow">大数据笔记：HDFS环境搭建</a><ul>
<li><a href="#prerequisites" rel="nofollow">Prerequisites</a><ul>
<li><a href="#%E5%AE%89%E8%A3%85jdk7" rel="nofollow">安装jdk7</a></li>
<li><a href="#%E5%AE%89%E8%A3%85ssh" rel="nofollow">安装ssh</a></li>
</ul>
</li>
<li><a href="#%E5%AE%89%E8%A3%85%E4%BC%AA%E5%88%86%E5%B8%83%E5%BC%8Fhdfs" rel="nofollow">安装伪分布式HDFS</a><ul>
<li><a href="#%E4%B8%8B%E8%BD%BD" rel="nofollow">下载</a></li>
<li><a href="#%E6%9B%B4%E6%94%B9%E9%85%8D%E7%BD%AE" rel="nofollow">更改配置</a></li>
</ul>
</li>
<li><a href="#%E5%90%AF%E5%8A%A8hdfs" rel="nofollow">启动hdfs</a></li>
<li><a href="#%E5%81%9C%E6%AD%A2hdfs" rel="nofollow">停止hdfs</a></li>
</ul>
</li>
</ul>
</div>


<hr>

<p>环境： <br>
CentOS6.4 <br>
Hadoop 2.6.0 -cdh5.7.0</p>



<h2 id="prerequisites">Prerequisites</h2>

<p>首先我们进入到官方网址<a href="http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0/" rel="nofollow">http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0/</a></p>

<p>由于我们一开始是要做一个“伪分布式”，因此我们在左侧<code>General</code>中选择<code>Single Node Setup</code></p>

<p>然后我们看到，需要安装JDK以及SSH：</p>



<pre class="prettyprint"><code class=" hljs applescript">Required software <span class="hljs-keyword">for</span> Linux include:

<span class="hljs-number">1.</span> Java™ must be installed. Recommended Java versions are described <span class="hljs-keyword">at</span> HadoopJavaVersions.
<span class="hljs-number">2.</span> ssh must be installed <span class="hljs-keyword">and</span> sshd must be <span class="hljs-property">running</span> <span class="hljs-keyword">to</span> use <span class="hljs-keyword">the</span> Hadoop scripts <span class="hljs-keyword">that</span> manage remote Hadoop daemons.</code></pre>



<h3 id="安装jdk7">安装jdk7</h3>

<p>推荐的是安装jdk7：<a href="http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html" rel="nofollow">http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html</a></p>

<p>下载：jdk-7u79-linux-i586.tar.gz</p>

<p>然后下载，在Linux的Firefox下压缩包被下载到Downloads下，我们使用mv命令将其移动到software下。</p>

<p>使命令解压到app目录下:</p>



<pre class="prettyprint"><code class=" hljs lasso">tar <span class="hljs-attribute">-zxvf</span> jdk<span class="hljs-subst">-</span><span class="hljs-number">7</span>u79<span class="hljs-attribute">-linux</span><span class="hljs-attribute">-x64</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz <span class="hljs-attribute">-C</span> ~/app</code></pre>

<p>然后我们在使用pwd获取其全路径。<strong>将jdk配置到系统环境变量中</strong>。</p>

<p>我们<strong>打开用户目录下的<code>.hash_profile</code>文件</strong>： <br>
 <code> <br>
 vim ~/.bash_profile <br>
</code> <br>
然后<strong>编辑文件，添加系统环境变量</strong>：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment"># .bash_profile</span>

<span class="hljs-comment"># Get the aliases and functions</span>
<span class="hljs-keyword">if</span> [ <span class="hljs-operator">-f</span> ~/.bashrc ]; <span class="hljs-keyword">then</span>
        . ~/.bashrc
<span class="hljs-keyword">fi</span>

<span class="hljs-comment"># User specific environment and startup programs</span>

<span class="hljs-keyword">export</span> JAVA_HOME=/home/japson/app/jdk1.<span class="hljs-number">7.0</span>_79

<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$JAVA_HOME</span>/bin:<span class="hljs-variable">$PATH</span>
</code></pre>

<p>然后我们<strong>要使环境变量配置生效</strong>：</p>



<pre class="prettyprint"><code class=" hljs avrasm">surce ~/<span class="hljs-preprocessor">.bash</span>_profile</code></pre>

<p>此时我们已经配置完系统环境变量了，我们进行检查：</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> jdk1.<span class="hljs-number">7.0_79</span>]<span class="hljs-variable">$ </span>echo <span class="hljs-variable">$JAVA_HOME</span>
/home/japson/app/jdk1.<span class="hljs-number">7.0_79</span>
[japson<span class="hljs-variable">@localhost</span> jdk1.<span class="hljs-number">7.0_79</span>]<span class="hljs-variable">$ </span>java -version
java version <span class="hljs-string">"1.7.0_79"</span>
<span class="hljs-constant">Java</span>(<span class="hljs-constant">TM</span>) <span class="hljs-constant">SE</span> <span class="hljs-constant">Runtime</span> <span class="hljs-constant">Environment</span> (build <span class="hljs-number">1.7</span>.<span class="hljs-number">0_7</span>9-b15)
<span class="hljs-constant">Java</span> <span class="hljs-constant">HotSpot</span>(<span class="hljs-constant">TM</span>) <span class="hljs-number">64</span>-<span class="hljs-constant">Bit</span> <span class="hljs-constant">Server</span> <span class="hljs-constant">VM</span> (build <span class="hljs-number">24.79</span>-b02, mixed mode)</code></pre>



<h3 id="安装ssh">安装ssh</h3>

<p>在CentOS中，我们使用yum来安装软件。</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">sudo</span> yum install ssh</code></pre>

<p>但是出现了一个问题：</p>



<pre class="prettyprint"><code class=" hljs applescript">japson <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">in</span> <span class="hljs-keyword">the</span> sudoers <span class="hljs-type">file</span>. This incident will be reported.</code></pre>

<p>究其原因是用户没有加入到sudo的配置文件里，使用<code>su root</code>切换到root用户，运行visudo命令：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> japson]<span class="hljs-comment"># visudo</span></code></pre>

<p>在打开的配置文件中，找到root ALL=(ALL) ALL，在下面添加一行 <br>
japson ALL=(ALL) ALL </p>

<p>输入:wq保存并退出配置文件，并使用<code>su japson</code>切换回用户</p>

<p>再次使用sudo命令就不会有上面的提示了。</p>

<p>然后我们要<strong>对ssh进行一个免密码连接的配置</strong></p>

<p>首先要生成密钥：</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>ssh-keygen -t rsa</code></pre>

<p>然后一路按回车，提示我们：</p>



<pre class="prettyprint"><code class=" hljs vbnet">Your <span class="hljs-keyword">public</span> <span class="hljs-keyword">key</span> has been saved <span class="hljs-keyword">in</span> /home/japson/.ssh/id_rsa.pub.</code></pre>

<p>我们找到对应的目录：</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>ls .ssh
id_rsa  id_rsa.pub</code></pre>

<p>然后我们将id_rsa.pub 复制为 authorized_keys</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>cp ~<span class="hljs-regexp">/.ssh/id</span>_rsa.pub ~<span class="hljs-regexp">/.ssh/authorized</span>_keys</code></pre>

<p>ssh就配置完成了。线面我们进行验证，使用ssh命令连接localhost并退出：</p>



<pre class="prettyprint"><code class=" hljs applescript">[japson@localhost .ssh]$ ssh localhost
The authenticity <span class="hljs-keyword">of</span> host 'localhost (::<span class="hljs-number">1</span>)' can't be established.
RSA key fingerprint <span class="hljs-keyword">is</span> <span class="hljs-number">63</span>:<span class="hljs-number">3</span>f:<span class="hljs-number">25</span>:ca:<span class="hljs-number">15</span>:<span class="hljs-number">35</span>:<span class="hljs-number">17</span>:<span class="hljs-number">97</span>:cc:ea:eb:<span class="hljs-number">08</span>:c5:<span class="hljs-number">15</span>:<span class="hljs-number">1</span>c:f1.
Are you sure you want <span class="hljs-keyword">to</span> <span class="hljs-keyword">continue</span> connecting (yes/no)? yes
Warning: Permanently added 'localhost' (RSA) <span class="hljs-keyword">to</span> <span class="hljs-keyword">the</span> <span class="hljs-type">list</span> <span class="hljs-keyword">of</span> known hosts.
Last login: Fri May <span class="hljs-number">18</span> <span class="hljs-number">02</span>:<span class="hljs-number">54</span>:<span class="hljs-number">24</span> <span class="hljs-number">2018</span> <span class="hljs-keyword">from</span> <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.108</span>
[japson@localhost ~]$ <span class="hljs-keyword">exit</span>
logout
Connection <span class="hljs-keyword">to</span> localhost closed.</code></pre>



<h2 id="安装伪分布式hdfs">安装伪分布式HDFS</h2>



<h3 id="下载">下载</h3>

<p>我们要下载Hadoop，地址：<a href="http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0.tar.gz" rel="nofollow">http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0.tar.gz</a></p>

<p>将其移动到software包下，然后解压到app目录下：</p>



<pre class="prettyprint"><code class=" hljs lasso">tar <span class="hljs-attribute">-zxvf</span> hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.6</span><span class="hljs-number">.0</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.7</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz <span class="hljs-attribute">-C</span> ~/app<span class="hljs-subst">/</span></code></pre>

<p>解压完成后，我们在hadoop目录下看一下结构：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">japson@localhost</span> <span class="hljs-comment">hadoop</span><span class="hljs-literal">-</span><span class="hljs-comment">2</span><span class="hljs-string">.</span><span class="hljs-comment">6</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-literal">-</span><span class="hljs-comment">cdh5</span><span class="hljs-string">.</span><span class="hljs-comment">7</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-title">]</span><span class="hljs-comment">$</span> <span class="hljs-comment">ll</span>
<span class="hljs-comment">total</span> <span class="hljs-comment">76</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">2</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">bin</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">2</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">bin</span><span class="hljs-literal">-</span><span class="hljs-comment">mapreduce1</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">3</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">cloudera</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">6</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">etc</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">5</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">examples</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">3</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">examples</span><span class="hljs-literal">-</span><span class="hljs-comment">mapreduce1</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">2</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">include</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">3</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">lib</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">2</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">libexec</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-string">.</span>  <span class="hljs-comment">1</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">17087</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">LICENSE</span><span class="hljs-string">.</span><span class="hljs-comment">txt</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-string">.</span>  <span class="hljs-comment">1</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>   <span class="hljs-comment">101</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">NOTICE</span><span class="hljs-string">.</span><span class="hljs-comment">txt</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-string">.</span>  <span class="hljs-comment">1</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">1366</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">README</span><span class="hljs-string">.</span><span class="hljs-comment">txt</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">3</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">sbin</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span>  <span class="hljs-comment">4</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">share</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span><span class="hljs-string">.</span> <span class="hljs-comment">17</span> <span class="hljs-comment">japson</span> <span class="hljs-comment">japson</span>  <span class="hljs-comment">4096</span> <span class="hljs-comment">Mar</span> <span class="hljs-comment">23</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">src</span></code></pre>

<p>其中bin目录下是一些可执行文件，etc目录是一个很重要的目录，其中有很多重要的配置文件，sbin目录是一些启动和关闭文件，jar目录下的/hadoop/mapreduce目录下有一个examples的jar包，有一些例子供我们直接使用。</p>



<h3 id="更改配置">更改配置</h3>

<p>按照官网所说：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">Unpack <span class="hljs-operator">the</span> downloaded Hadoop distribution. In <span class="hljs-operator">the</span> distribution, edit <span class="hljs-operator">the</span> <span class="hljs-built_in">file</span> etc/hadoop/hadoop-env.sh <span class="hljs-built_in">to</span> define some parameters <span class="hljs-keyword">as</span> follows:

  <span class="hljs-comment"># set to the root of your Java installation</span>
  export JAVA_HOME=/usr/java/latest

  <span class="hljs-comment"># Assuming your installation directory is /usr/local/hadoop</span>
  export HADOOP_PREFIX=/usr/<span class="hljs-built_in">local</span>/hadoop

Try <span class="hljs-operator">the</span> following <span class="hljs-command"><span class="hljs-keyword">command</span>:</span>
  $ bin/hadoop
This will display <span class="hljs-operator">the</span> usage documentation <span class="hljs-keyword">for</span> <span class="hljs-operator">the</span> hadoop script.

Now you are ready <span class="hljs-built_in">to</span> start your Hadoop cluster <span class="hljs-operator">in</span> <span class="hljs-constant">one</span> <span class="hljs-operator">of</span> <span class="hljs-operator">the</span> <span class="hljs-constant">three</span> supported modes:

· Local (Standalone) Mode
· Pseudo-Distributed Mode
· Fully-Distributed Mode</code></pre>

<p>我们来到hadoop目录下，<code>etc/hadoop/hadoop-env.sh</code> 找到并改写路径</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-comment"># The java implementation to use.</span>
<span class="hljs-comment"># export JAVA_HOME=${JAVA_HOME}</span>
export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/home/japson</span><span class="hljs-regexp">/app/jdk</span>1.<span class="hljs-number">7.0_79</span></code></pre>

<p>到这里我们就可以继续往下看了。</p>

<p>因为我们要进行的是Pseudo-Distributed Operation（伪分布式操作）。</p>

<blockquote>
  <p>Hadoop can also be run on a single-node in a pseudo-distributed mode where each Hadoop daemon runs in a separate Java process.</p>
</blockquote>

<p>配置HDFS默认文件系统的地址和缓存文件的存储地址 <br>
<strong>core-site.xml</strong></p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:8020<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/home/japson/app/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>配置hdfs副本系数为1，因为我们只有一个节点，没有三份。 <br>
<strong>hdfs-site.xml</strong></p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>还有一个slaves文件，里面会配置DataNode的主机名。</p>

<p>在这里，我们最好将bin目录也配置到系统的环境变量中去。 <br>
即通过pwd获取路径，然后将其配置到<code>~/.bash_profile</code>中去：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> HADOOP_HOME=/home/japson/app/hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">0</span>-cdh5.<span class="hljs-number">7.0</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$HADOOP_HOME</span>/bin:<span class="hljs-variable">$PATH</span></code></pre>

<p>然后使用source使其生效并检验：</p>



<pre class="prettyprint"><code class=" hljs mel">[japson<span class="hljs-variable">@localhost</span> bin]$ <span class="hljs-keyword">source</span> ~/.bash_profile
[japson<span class="hljs-variable">@localhost</span> bin]$ echo <span class="hljs-variable">$HADOOP_HOME</span>
/home/japson/app/hadoop-<span class="hljs-number">2.6</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.7</span><span class="hljs-number">.0</span></code></pre>



<h2 id="启动hdfs">启动hdfs</h2>

<ol>
<li><p>格式化文件系统（客户端操作，仅第一次执行即可，不要重复执行）：hdfs namenode -format</p></li>
<li><p>启动hdfs：sbin/start-dfs.sh</p></li>
<li><p>验证是否启动成功：在浏览器中输入<code>localhost:50070</code>看是否能访问，或者查看进程：</p></li>
</ol>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> sbin]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">4450</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">4834</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">4565</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">4719</span> <span class="hljs-constant">SecondaryNameNode</span></code></pre>



<h2 id="停止hdfs">停止hdfs</h2>

<p>sbin/stop-dfs.sh</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
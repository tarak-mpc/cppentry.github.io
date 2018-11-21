---
layout:     post
title:      HDFS 指令（一） version，mkdir，ls，put，copyFromLocal，get，copyToLocal，cat，mv，cp
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="1-简介"><strong>1. 简介</strong></h1>

<p>本文主要简单介绍一些常用的hdfs，包括复制文件，修改文件权限，查看文件内容，改变文件的所有权，创建目录，等HDFS文件操作 <br>
所有的Hadoop文件系统shell命令是由在bin / HDFS脚本调用 <br>
<img src="http://cdn.data-flair.training/blogs/wp-content/uploads/HDFS-Commands.jpg" alt="这里写图片描述" title=""></p>



<h1 id="2-version-查看版本"><strong>2. version  查看版本</strong></h1>

<p><strong>指令用法</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hdfs <span class="hljs-built_in">version</span></code></pre>

<p>例子</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> ~]<span class="hljs-variable">$ </span>hdfs version</code></pre>

<p>输出hadoop版本号  Hadoop 2.7.1</p>



<h1 id="3-mkdir-创建目录"><strong>3. mkdir 创建目录</strong></h1>

<p><strong>指令用法</strong></p>



<pre class="prettyprint"><code class=" hljs xml">mkdir <span class="hljs-tag">&lt;<span class="hljs-title">path</span>&gt;</span></code></pre>

<p>例子 , 创建testdir目录</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> ~]<span class="hljs-variable">$ </span>hdfs dfs -mkdir /testdir</code></pre>



<h1 id="4-ls-查看目录文件"><strong>4. ls  查看目录文件</strong></h1>

<p>能显示指定路径的文件或文件夹列表，显示每个条目 的名称，权限，拥有者，大小和修改日期，路径。  </p>

<p><strong>指令用法</strong></p>



<pre class="prettyprint"><code class=" hljs xml">ls <span class="hljs-tag">&lt;<span class="hljs-title">path</span>&gt;</span>
</code></pre>

<p>例子，查看 /test目录 (我之前创建了test目录，并且放了一个文件在里面)</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> ~]<span class="hljs-variable">$ </span>hdfs dfs -ls  /test</code></pre>

<p>输出</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">Found</span> <span class="hljs-comment">1</span> <span class="hljs-comment">items</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span>   <span class="hljs-comment">2</span> <span class="hljs-comment">hadoop</span> <span class="hljs-comment">supergroup</span>         <span class="hljs-comment">14</span> <span class="hljs-comment">2017</span><span class="hljs-literal">-</span><span class="hljs-comment">04</span><span class="hljs-literal">-</span><span class="hljs-comment">03</span> <span class="hljs-comment">14:45</span> <span class="hljs-comment">/test/slaves</span></code></pre>



<h1 id="5-put-复制文件到hdfs"><strong>5. put 复制文件到hdfs</strong></h1>

<p>可以复制本地文件到hdfs，也可以复制hdfs上的文件，到hdfs的另外一个地方 <br>
<strong>指令用法</strong></p>



<pre class="prettyprint"><code class=" hljs xml">put <span class="hljs-tag">&lt;<span class="hljs-title">localSrc</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">dest</span>&gt;</span>
</code></pre>

<p>例子，把hdfs.site文件放到 /test目录下</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> hadoop]<span class="hljs-variable">$ </span>hdfs dfs -put /home/hadoop/<span class="hljs-constant">MyCloudera</span>/<span class="hljs-constant">APP</span>/hadoop/hadoop/etc/hadoop/hdfs-site.xml /test</code></pre>



<h1 id="6-copyfromlocal"><strong>6. copyFromLocal</strong></h1>

<p>和put很像，但是指定了只能是从本地复制到hdfs <br>
<strong>指令用法</strong></p>



<pre class="prettyprint"><code class=" hljs xml">copyFromLocal <span class="hljs-tag">&lt;<span class="hljs-title">localSrc</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">dest</span>&gt;</span></code></pre>

<p>例子，把hadoop-env.sh复制到 /test中</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> hadoop]<span class="hljs-variable">$ </span>hdfs dfs -copyFromLocal /home/hadoop/<span class="hljs-constant">MyCloudera</span>/<span class="hljs-constant">APP</span>/hadoop/hadoop/etc/hadoop/hadoop-env.sh  /test</code></pre>



<h1 id="7-get-复制hdfs文件到本地"><strong>7. get 复制hdfs文件到本地</strong></h1>

<p>指令用法</p>



<pre class="prettyprint"><code class=" hljs xml">get [-crc] <span class="hljs-tag">&lt;<span class="hljs-title">src</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">localDest</span>&gt;</span>
</code></pre>

<p>例如我将hdfs 上的文件拷贝到本地</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> <span class="hljs-constant">Downloads</span>]<span class="hljs-variable">$ </span>hdfs dfs -get /test/slaves /home/hadoop/<span class="hljs-constant">Downloads</span>/</code></pre>



<h1 id="8-copytolocal-复制hdfs文件到本地"><strong>8. copyToLocal  复制hdfs文件到本地</strong></h1>

<p>类似于get命令，唯一的区别是，在这个目的路径被限制到本地</p>

<p><strong>指令用法</strong></p>



<pre class="prettyprint"><code class=" hljs xml">copyToLocal <span class="hljs-tag">&lt;<span class="hljs-title">src</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">localDest</span>&gt;</span>
</code></pre>

<p>例子, 复制slaves到本地,Downloads目录</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> <span class="hljs-constant">Downloads</span>]<span class="hljs-variable">$ </span>hdfs dfs -copyToLocal /test/slaves /home/hadoop/<span class="hljs-constant">Downloads</span>/</code></pre>



<h1 id="9-cat-查看文件内容"><strong>9. cat 查看文件内容</strong></h1>

<p>将文件的内容输出到console或者stdout控制台 <br>
<strong>指令用法</strong></p>



<pre class="prettyprint"><code class=" hljs applescript">cat &lt;<span class="hljs-type">file</span>-<span class="hljs-property">name</span>&gt;
</code></pre>

<p>例如，查看/test/slaves文件</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> <span class="hljs-constant">Downloads</span>]<span class="hljs-variable">$ </span>hdfs dfs -cat /test/slaves</code></pre>

<p>控制台打印</p>



<pre class="prettyprint"><code class=" hljs ">master
node01</code></pre>



<h1 id="10-mv-移动"><strong>10. mv 移动</strong></h1>

<p>类似于linux的mv命令，移动hdfs中的一个地方到另外一个地方 <br>
<strong>指定用法</strong></p>



<pre class="prettyprint"><code class=" hljs xml">mv <span class="hljs-tag">&lt;<span class="hljs-title">src</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">dest</span>&gt;</span>
</code></pre>

<p>把/test/slaves 复制到 /testdir </p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> <span class="hljs-constant">Downloads</span>]<span class="hljs-variable">$ </span>hdfs dfs -mv /test/slaves /testdir </code></pre>



<h1 id="11-copy复制"><strong>11. copy复制</strong></h1>

<p>类似于linux中的cp方法，复制hdfs中的一个文件到另外一个文件目录</p>

<p><strong>指令用法</strong></p>



<pre class="prettyprint"><code class=" hljs xml">cp <span class="hljs-tag">&lt;<span class="hljs-title">src</span>&gt;</span> <span class="hljs-tag">&lt;<span class="hljs-title">dest</span>&gt;</span></code></pre>

<p>例子</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@master</span> <span class="hljs-constant">Downloads</span>]<span class="hljs-variable">$ </span>hdfs dfs -cp /testdir/slaves /test</code></pre>

<p>翻译原文：<a href="http://data-flair.training/blogs/top-hadoop-hdfs-commands-tutorial/" rel="nofollow">http://data-flair.training/blogs/top-hadoop-hdfs-commands-tutorial/</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
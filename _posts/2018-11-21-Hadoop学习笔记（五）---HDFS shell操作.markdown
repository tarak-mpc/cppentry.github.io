---
layout:     post
title:      Hadoop学习笔记（五）---HDFS shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.查看hadoop上根目录的文件：</p>

<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> current]<span class="hljs-comment"># hadoop fs -ls /</span></code></pre>

<p>2.上传文件到hadoop：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># vim aaa</span>
[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -put aaa /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -ls /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Found</span> <span class="hljs-number">2</span> items
-rw-r--r--   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> /aaa
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">19</span> /usr</code></pre>

<p>我先创建了一个文件aaa，然后用命令<code>hadoop fs -put aaa /</code>，指的是把aaa这个文件上传到hadoop的根目录，然后我又查看了以下文件，发现hadoop下面有了我上传的文件</p>

<p>3.查看文件内容 <code>hadoop fs -cat /aaa</code></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -cat /aaa</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

helloworld</code></pre>

<p>4.改变权限 <code>hadoop fs -chmod 777 /aaa</code></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -chmod 777 /aaa</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -ls /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Found</span> <span class="hljs-number">2</span> items
-rw-rw-rw-   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> /aaa
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">19</span> /usr</code></pre>

<p>hadoop是没有执行权限的，所以你即便是给文件可读，可写，可执行，hadoop也不会给他可执行权限</p>

<p>5.复制远程的内容到本地：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -get /aaa ./bbb</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># ls</span>
aaa  bbb  <span class="hljs-constant">Desktop</span>  <span class="hljs-constant">Documents</span>  <span class="hljs-constant">Downloads</span>  <span class="hljs-constant">Music</span>  <span class="hljs-constant">Pictures</span>  <span class="hljs-constant">Public</span>  <span class="hljs-constant">Templates</span>  <span class="hljs-constant">Videos</span></code></pre>

<p>这里说的是把hadoop根目录下面的aaa文件复制到本地当前目录下面的bbb文件，完成后你会发现多了一个bbb文件</p>

<p>6.统计远程的文件 ：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -count /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

           <span class="hljs-number">7</span>            <span class="hljs-number">2</span>                 <span class="hljs-number">15</span> <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/172.21.15.189:9000/</span></code></pre>

<p>这里指的是hadoop根目录下面有7个文件夹，2个文件，占15个字节</p>

<p>7.hadoop复制文件：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -cp /aaa /bbb</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -ls /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Found</span> <span class="hljs-number">3</span> items
-rw-rw-rw-   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> /aaa
-rw-r--r--   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">34</span> /bbb
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">19</span> /usr</code></pre>

<p>你会发现根目录下面多了一个bbb文件</p>

<p>8.递归列出文件：</p>



<pre class="prettyprint"><code class=" hljs perl">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -lsr /</span>
Warning: <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

-rw-rw-rw-   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span>:<span class="hljs-number">18</span> /aaa
-rw-r--r--   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span>:<span class="hljs-number">34</span> /bbb
drwxr-xr-<span class="hljs-keyword">x</span>   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span>:<span class="hljs-number">19</span> /usr
drwxr-xr-<span class="hljs-keyword">x</span>   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span>:<span class="hljs-number">19</span> /usr/<span class="hljs-keyword">local</span>
drwxr-xr-<span class="hljs-keyword">x</span>   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span>:<span class="hljs-number">19</span> /usr/<span class="hljs-keyword">local</span>/hadoop
drwxr-xr-<span class="hljs-keyword">x</span>   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span>:<span class="hljs-number">19</span> /usr/<span class="hljs-keyword">local</span>/hadoop/tmp
drwxr-xr-<span class="hljs-keyword">x</span>   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span>:<span class="hljs-number">19</span> /usr/<span class="hljs-keyword">local</span>/hadoop/tmp/mapred
drwx------   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span>:<span class="hljs-number">36</span> /usr/<span class="hljs-keyword">local</span>/hadoop/tmp/mapred/<span class="hljs-keyword">system</span>
-rw-------   <span class="hljs-number">1</span> root supergroup          <span class="hljs-number">4</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span>:<span class="hljs-number">36</span> /usr/<span class="hljs-keyword">local</span>/hadoop/tmp/mapred/<span class="hljs-keyword">system</span>/jobtracker.info</code></pre>

<p>你会发现所有的文件都递归列出来了</p>

<p>9.创建文件夹</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -mkdir /mydir</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -ls /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Found</span> <span class="hljs-number">4</span> items
-rw-rw-rw-   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> /aaa
-rw-r--r--   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">34</span> /bbb
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">43</span> /mydir
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">19</span> /usr</code></pre>

<p>10.移动或者重命名文件：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -mv /aaa /helloworld</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -ls /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Found</span> <span class="hljs-number">4</span> items
-rw-r--r--   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">34</span> /bbb
-rw-rw-rw-   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> /helloworld
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">43</span> /mydir
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">19</span> /usr</code></pre>

<p>11.删除文件或者文件夹：</p>

<p>文件：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -rm /bbb</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Deleted</span> <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/172.21.15.189:9000/bbb</span>
[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -ls /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Found</span> <span class="hljs-number">3</span> items
-rw-rw-rw-   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> /helloworld
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">43</span> /mydir
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">19</span> /usr</code></pre>

<p>文件夹：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -rmr /mydir</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Deleted</span> <span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/172.21.15.189:9000/mydir</span>
[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -ls /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Found</span> <span class="hljs-number">2</span> items
-rw-rw-rw-   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> /helloworld
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">19</span> /usr</code></pre>

<p>12.创建文件：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -touchz /aaa</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

[root<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-comment"># hadoop fs -ls /</span>
<span class="hljs-constant">Warning</span><span class="hljs-symbol">:</span> <span class="hljs-variable">$HADOOP_HOME</span> is deprecated.

<span class="hljs-constant">Found</span> <span class="hljs-number">3</span> items
-rw-r--r--   <span class="hljs-number">1</span> root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">55</span> /aaa
-rw-rw-rw-   <span class="hljs-number">1</span> root supergroup         <span class="hljs-number">11</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">07</span><span class="hljs-symbol">:</span><span class="hljs-number">18</span> /helloworld
drwxr-xr-x   - root supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2015</span>-<span class="hljs-number">05</span>-<span class="hljs-number">31</span> <span class="hljs-number">03</span><span class="hljs-symbol">:</span><span class="hljs-number">19</span> /usr</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
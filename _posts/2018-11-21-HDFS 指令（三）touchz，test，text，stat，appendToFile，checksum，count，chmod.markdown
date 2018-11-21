---
layout:     post
title:      HDFS 指令（三）touchz，test，text，stat，appendToFile，checksum，count，chmod
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>关键词：hdfs命令，touchz，test，text，stat，appendToFile，checksum，count，chmod</p>



<h1 id="本章目的"><strong>本章目的</strong></h1>

<p>在这个Hadoop HDFS命令教程中，我们将学习剩下一些重要并且经常使用的HDFS命令，借助这些命令，我们将能够执行HDFS文件操作，如复制文件，更改文件权限，查看文件内容，更改文件所有权，创建目录等。要了解有关世界上最可靠的存储层的更多信息，请参阅HDFS入门指南。 <br>
<img src="http://cdn.data-flair.training/blogs/wp-content/uploads/HDFS-Commands.jpg" alt="image" title=""></p>



<h1 id="1-touchz"><strong>1. touchz</strong></h1>

<p>在指定目录创建一个新文件，如果文件存在，则创建失败 <br>
<strong>命令用法</strong></p>



<pre class="prettyprint"><code class=" hljs xml">touchz <span class="hljs-tag">&lt;<span class="hljs-title">path</span>&gt;</span></code></pre>

<p><strong>例子</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">hdfs dfs <span class="hljs-attribute">-touchz</span> /user/dataflair/dir2</code></pre>



<h1 id="2-test"><strong>2. test</strong></h1>

<p>测试hdfs中的某个文件或者目录是否存在 <br>
-d: 如果测试的路径是一个文件夹, 则返回0，否则返回1。 <br>
-e: 如果测试的路径存在, 则返回0，否则返回1。 <br>
-f: 如果测试的路径是一个文件, 则返回0，否则返回1。 <br>
-s: if 如果测试的路径不是空(文件夹下有文件或者文件夹), 则返回0，否则返回1。 <br>
-z: 如果测试的是一个文件，并且这个文件不为空, 则返回0，否则返回1。 <br>
<strong>命令用法</strong></p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hdfs</span> <span class="hljs-tag">dfs</span> <span class="hljs-tag">-test</span> <span class="hljs-tag">-</span><span class="hljs-attr_selector">[ezd]</span> <span class="hljs-tag">URI</span></code></pre>

<p><strong>例子</strong></p>



<pre class="prettyprint"><code class=" hljs 1c"><span class="hljs-string">"hdfs dfs -test -e /test/file/sample</span>
hdfs dfs -test -z /test/file/sample
hdfs dfs -test -d /test/file/sample</code></pre>



<h1 id="3-text"><strong>3. text</strong></h1>

<p>格式化输出文件的内容，允许的格式化包括zip,和 TextRecordInputStream <br>
<strong>命令用法</strong></p>



<pre class="prettyprint"><code class=" hljs mel">hdfs dfs -<span class="hljs-keyword">text</span> &lt;<span class="hljs-keyword">source</span>&gt;</code></pre>

<p><strong>例子</strong></p>



<pre class="prettyprint"><code class=" hljs glsl">hdfs dfs -text /user/dataflair/dir1/<span class="hljs-keyword">sample</span></code></pre>



<h1 id="4-stat"><strong>4. stat</strong></h1>

<p>打印有关路径的信息，可以加下面的格式化输出 <br>
%b: 文件大小  </p>

<p>%n: 文件名  </p>

<p>%o: 块大小  </p>

<p>%r: 副本个数  </p>

<p>%y, %Y: 修改日期. <br>
<strong>命令用法</strong></p>



<pre class="prettyprint"><code class=" hljs perl">hdfs dfs -<span class="hljs-keyword">stat</span> [<span class="hljs-keyword">format</span>] path
</code></pre>

<p><strong>例子</strong></p>



<pre class="prettyprint"><code class=" hljs perl">hdfs dfs -<span class="hljs-keyword">stat</span> /user/dataflair/dir1
hdfs fs -<span class="hljs-keyword">stat</span> <span class="hljs-string">"<span class="hljs-variable">%o</span> <span class="hljs-variable">%r</span>"</span> /user/dataflair/dir1</code></pre>



<h1 id="5-appendtofile"><strong>5. appendToFile</strong></h1>

<p>appendToFile命令是将一个或者多个文件添加到HDFS系统中，他也是从标准输入中读取，然后添加到目标文件系统汇总 <br>
<strong>命令用法</strong></p>



<pre class="prettyprint"><code class=" hljs r">hadoop fs -appendToFile &lt;localsource&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;
</code></pre>

<p><strong>例子</strong></p>



<pre class="prettyprint"><code class=" hljs glsl">hadoop fs -appendToFile /home/dataflair/Desktop/<span class="hljs-keyword">sample</span> /user/dataflair/dir1</code></pre>



<h1 id="6-checksum"><strong>6. checksum</strong></h1>

<p>Datanode在把数据实际存储之前会验证数据的校验和（checksum的初始值？）如果某个client在读取数据时检测到数据错误, 在抛出ChecksumException <br>
参考： <a href="http://blog.csdn.net/oh_mourinho/article/details/52524442" rel="nofollow">http://blog.csdn.net/oh_mourinho/article/details/52524442</a> <br>
<strong>命令用法</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-checksum</span> URI</code></pre>

<p><strong>例子</strong></p>



<pre class="prettyprint"><code class=" hljs glsl">hadoop fs -checksum /user/dataflair/dir1/<span class="hljs-keyword">sample</span></code></pre>



<h1 id="7-count"><strong>7. count</strong></h1>

<p>统计hdfs对应路径下的目录个数，文件个数，文件总计大小 <br>
显示为目录个数，文件个数，文件总计大小，输入路径 <br>
<strong>命令用法</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">hdfs dfs <span class="hljs-attribute">-count</span> <span class="hljs-preprocessor">[</span><span class="hljs-attribute">-q</span><span class="hljs-preprocessor">]</span><span class="hljs-markup"> &lt;paths&gt;</span></code></pre>

<p><strong>例子</strong></p>



<pre class="prettyprint"><code class=" hljs axapta">hdfs dfs -<span class="hljs-keyword">count</span> /user/dataflair</code></pre>



<h1 id="8-chmod"><strong>8. chmod</strong></h1>

<p>类似于linux，用来修改权限 <br>
<strong>命令用法</strong></p>



<pre class="prettyprint"><code class=" hljs r">chmod [-R] mode,mode,<span class="hljs-keyword">...</span> &lt;path&gt;<span class="hljs-keyword">...</span></code></pre>

<p><strong>例子</strong></p>



<pre class="prettyprint"><code class=" hljs glsl">hdfs dfs -chmod <span class="hljs-number">777</span> /user/dataflair/dir1/<span class="hljs-keyword">sample</span></code></pre>

<p>翻译原文：<a href="http://data-flair.training/blogs/hadoop-hdfs-commands-tutorial/" rel="nofollow">http://data-flair.training/blogs/hadoop-hdfs-commands-tutorial/</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
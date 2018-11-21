---
layout:     post
title:      向HDFS提交数据并读取测试WordCount案例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/YaboSun/article/details/80136740				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>要使用hdfs需要切换到Hadoop安装目录下，然后使用命令</p>



<pre class="prettyprint"><code class=" hljs coffeescript">bin<span class="hljs-regexp">/hdfs dfs -ls /</span> </code></pre>

<p>可以查看对应节点hdfs文件系统下的目录结构 <br>
接着将测试数据放到hdfs上，这里是将数据放到/testdata目录下</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">bin/hdfs dfs -<span class="hljs-built_in">put</span> /<span class="hljs-built_in">word</span>.txt /testdata</code></pre>

<p>注意这里的word.txt是自己在namenode根目录创建的文件，内容如下： <br>
<img src="https://img-blog.csdn.net/20180428162548714" alt="这里写图片描述" title=""> <br>
查看是否上传:</p>



<pre class="prettyprint"><code class=" hljs mel">bin/hdfs dfs -<span class="hljs-keyword">ls</span> /testdata</code></pre>

<p><img src="https://img-blog.csdn.net/20180428162726610" alt="这里写图片描述" title=""></p>

<p>可以看到对应的文件已经上传到hdfs文件系统中 <br>
接着测试在spark-shell中直接编写wordcount程序，并从hdfs读取数据，最后返回结果 <br>
进入spark安装目录，并启动spark-shell <br>
首先指定要读取的文件路径： <br>
这里需要写hdfs的路径：</p>

<pre class="prettyprint"><code class=" hljs profile">val file = <span class="hljs-filename">sc.textFile("hdfs</span>://<span class="hljs-filename">your.master.ip</span>:<span class="hljs-number">9000</span>/testdata/<span class="hljs-filename">word.txt")</span></code></pre>

<p>接着进行计算：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver"> val rdd = <span class="hljs-built_in">file</span>.flatMap(<span class="hljs-built_in">line</span> =&gt; <span class="hljs-built_in">line</span>.<span class="hljs-built_in">split</span>(<span class="hljs-string">" "</span>)).map(<span class="hljs-built_in">word</span> =&gt; (<span class="hljs-built_in">word</span>,<span class="hljs-number">1</span>)).reduceByKey(_+_)</code></pre>

<p>使用collect将计算的数据存储，并返回：</p>

<pre class="prettyprint"><code class=" hljs avrasm">rdd<span class="hljs-preprocessor">.collect</span>()</code></pre>

<p>最后打印显示count的结果：</p>

<pre class="prettyprint"><code class=" hljs cs">rdd.<span class="hljs-keyword">foreach</span>(println)</code></pre>

<p><img src="https://img-blog.csdn.net/20180428163240478" alt="这里写图片描述" title=""></p>

<p>OK，到此简单的测试了如何将本地数据上传到hdfs文件系统，并使用spark-shell实现WordCount案例，并将计算后的数据打印输出。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Spark学习笔记（三）-开始第一个spark程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark学习笔记-开始第一个spark程序">Spark学习笔记-开始第一个spark程序</h1>

<p>　　在安装完Spark，其实最疑惑还是Spark怎么用的问题，由于也是刚学习linux，也不是很习惯linux下的命令行操作。按照Spark的官方文档，我们开始编写Spark的的第一个程序。</p>



<h2 id="一标准进入spark方式">一、标准进入spark方式</h2>

<p>　　很多人在安装完spark后就懵了，本人也是一样。毕竟spark在linux下没有什么桌面图标这种明显的东西。要启动Spark，我们先要进入Spark的目录下。我们在Unbutu桌面输入Ctrl+Alt+T 打开控制台，然后输入：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> /usr/lib/spark/spark-<span class="hljs-number">1.4</span>.<span class="hljs-number">0</span></code></pre>

<p>　　这个路径是我的安装路径，也就是spark的目录。Spark一共提供了3中API 分别为 scala、java以及python，同时提供了scala和python的命令行操作。 <br>
　　 <br>
　　进入scala环境，输入：</p>



<pre class="prettyprint"><code class="language-ruby hljs ">./bin/spark-shell</code></pre>

<p>　　进入python环境，输入：</p>



<pre class="prettyprint"><code class="language-ruby hljs ">./bin/pyspark</code></pre>



<h2 id="二快捷进入spark方式">二、快捷进入spark方式</h2>

<p>　　这个是官方提供的进入spark命令行的方式，实际上我发现直接呼出控制台然后直接输入spark-shell以及pyspark也能够直接进入相应的环境。如果能够直接进入spark的环境，那么就可以偷个懒直接输入spark-shell和pyspark。</p>



<h2 id="三编写spark的第一个程序">三、编写spark的第一个程序</h2>

<p>　　由于本人不太熟悉python，同时spark的官方例程也是scala编写的，所以这里的DEMO默认都是spark环境下的程序。 <br>
　　Spark最主要的抽象是RDD，前面一篇文章《Spark学习笔记-Spark-弹性分布式数据集RDDs》已经初步的总结了一下。RDD有很多的创建方式，我们甚至可以通硬盘中的一个txt文件创建一个RDD。 <br>
　　输入：</p>



<pre class="prettyprint"><code class=" hljs fsharp">　　　　　　　　　　　　<span class="hljs-keyword">val</span> textFile = sc.textFile(<span class="hljs-string">"README.md"</span>)</code></pre>

<p>　　得到： <br>
　　　　　　　<img src="https://img-blog.csdn.net/20160918165215208" alt="结果" title=""> <br>
　　 <br>
　　经过前面的语句我们可以得到RDD到textFile的映射，也就是textFile这个变量，同时textFile就是一个RDD。我们可以对它进行很多RDD的操作。 <br>
　　例如输入：</p>



<pre class="prettyprint"><code class=" hljs axapta">　　　　　　　　　　　　　　　　　　　textFile.<span class="hljs-keyword">count</span>()</code></pre>

<p>　　得到： <br>
　　　　　　　　　　　　　　　　　　           <img src="https://img-blog.csdn.net/20160918165528413" alt="res" title=""> <br>
　　说明：实际上spark运行过程中控制台还会输出其他信息，这里只输出最后的结果。还有一点就是本人在直接进入spark-shell时，这个例程运行出现错误，猜测这个地方和Matlab类似，有个目录的问题，这个README.md在spark的目录下面，如果直接进入可能导致spark找不到这个文件。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
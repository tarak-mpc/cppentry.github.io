---
layout:     post
title:      HDFS quota
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HDFS提供了两种配额的命令（<strong>hdfs dfsadmin</strong>）：</p>



<h1 id="1-setquota"><font color="20B2AA">1. setQuota</font></h1>

<hr>



<pre class="prettyprint"><code class=" hljs r">-setQuota &lt;quota&gt; &lt;dirname&gt;<span class="hljs-keyword">...</span>&lt;dirname&gt;</code></pre>

<p>setQuota指的是对HDFS中某个目录设置文件和目录数量之和的最大值。</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hdfs dfsadmin -setQuota <span class="hljs-number">5</span> /user/hadoop/quota</code></pre>

<p><strong>设置HDFS中/user/hadoop/quota目录下文件数和目录数之和不超过5。</strong></p>

<hr>



<h1 id="2-setspacequota"><font color="20B2AA">2. setSpaceQuota</font></h1>

<hr>



<pre class="prettyprint"><code class=" hljs r">-setSpaceQuota &lt;quota&gt; &lt;dirname&gt;<span class="hljs-keyword">...</span>&lt;dirname&gt;</code></pre>

<p>setSpaceQuota针对的是设置HDFS中某个目录可用存储空间大小，单位是byte，类似与百度网盘的个人存储空间。<strong>在使用该命令的时候最好设置空间大小为块的整数倍！</strong></p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hdfs dfsadmin -setSpaceQuota <span class="hljs-number">134217728</span> /user/hadoop/spaceQuota</code></pre>

<p><strong>设置HDFS中/user/hadoop/spaceQuota目录存储空间为128MB。</strong></p>

<hr>

<p><strong>清除配额的命令为：</strong></p>



<pre class="prettyprint"><code class=" hljs r">$ hdfs dfsadmin -clrQuota &lt;dirname&gt;<span class="hljs-keyword">...</span>&lt;dirname&gt;
$ hdfs dfsadmin -clrSpaceQuota &lt;dirname&gt;<span class="hljs-keyword">...</span>&lt;dirname&gt;</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
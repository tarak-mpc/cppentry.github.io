---
layout:     post
title:      hadoop命令start-dfs.sh、 start-yarn.sh出错
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/God_V/article/details/82690931				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="start-dfssh出错">start-dfs.sh出错</h2>

<pre class="prettyprint"><code class=" hljs vbnet"><span class="hljs-keyword">ERROR</span>: Attempting <span class="hljs-keyword">to</span> operate <span class="hljs-keyword">on</span> hdfs namenode <span class="hljs-keyword">as</span> root
<span class="hljs-keyword">ERROR</span>: but there <span class="hljs-keyword">is</span> no HDFS_NAMENODE_USER defined. Aborting operation.
Starting datanodes
<span class="hljs-keyword">ERROR</span>: Attempting <span class="hljs-keyword">to</span> operate <span class="hljs-keyword">on</span> hdfs datanode <span class="hljs-keyword">as</span> root
<span class="hljs-keyword">ERROR</span>: but there <span class="hljs-keyword">is</span> no HDFS_DATANODE_USER defined. Aborting operation.
Starting secondary namenodes [iz2ze49dsj767ouqcmxlqaz]
<span class="hljs-keyword">ERROR</span>: Attempting <span class="hljs-keyword">to</span> operate <span class="hljs-keyword">on</span> hdfs secondarynamenode <span class="hljs-keyword">as</span> root
<span class="hljs-keyword">ERROR</span>: but there <span class="hljs-keyword">is</span> no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.</code></pre>

<p>（缺少用户定义而造成的）因此编辑启动和关闭</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>vim sbin/start-dfs.sh 
<span class="hljs-variable">$ </span>vim sbin/stop-dfs.sh</code></pre>

<p>顶部空白处</p>



<pre class="prettyprint"><code class=" hljs ini"><span class="hljs-setting">HDFS_DATANODE_USER=<span class="hljs-value">root</span></span>
<span class="hljs-setting">HADOOP_SECURE_DN_USER=<span class="hljs-value">hdfs</span></span>
<span class="hljs-setting">HDFS_NAMENODE_USER=<span class="hljs-value">root</span></span>
<span class="hljs-setting">HDFS_SECONDARYNAMENODE_USER=<span class="hljs-value">root</span></span></code></pre>

<hr>

<h2 id="start-yarnsh出错">start-yarn.sh出错</h2>

<pre class="prettyprint"><code class=" hljs vbnet">Starting resourcemanager <span class="hljs-keyword">ERROR</span>: Attempting <span class="hljs-keyword">to</span> operate <span class="hljs-keyword">on</span> yarn resourcemanager <span class="hljs-keyword">as</span> root <span class="hljs-keyword">ERROR</span>: but there <span class="hljs-keyword">is</span> no YARN_RESOURCEMANAGER_USER defined. Aborting operation. Starting nodemanagers <span class="hljs-keyword">ERROR</span>: Attempting <span class="hljs-keyword">to</span> operate <span class="hljs-keyword">on</span> yarn nodemanager <span class="hljs-keyword">as</span> root <span class="hljs-keyword">ERROR</span>: but there <span class="hljs-keyword">is</span> no YARN_NODEMANAGER_USER defined. Aborting operation.</code></pre>

<p>是因为缺少用户定义造成的，所以分别编辑开始和关闭脚本 </p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>vim sbin/start-yarn.sh 
<span class="hljs-variable">$ </span>vim sbin/stop-yarn.sh </code></pre>

<p>顶部添加</p>



<pre class="prettyprint"><code class=" hljs ini"><span class="hljs-setting">YARN_RESOURCEMANAGER_USER=<span class="hljs-value">root</span></span>
<span class="hljs-setting">HADOOP_SECURE_DN_USER=<span class="hljs-value">yarn</span></span>
<span class="hljs-setting">YARN_NODEMANAGER_USER=<span class="hljs-value">root</span></span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
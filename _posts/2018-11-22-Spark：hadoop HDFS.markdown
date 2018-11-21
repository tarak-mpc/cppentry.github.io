---
layout:     post
title:      Spark：hadoop HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ShellDawn/article/details/80073274				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>使用HDFS首先打开HDFS组件，如果有集群的话，则集群的hdfs都会被打开。</p>



<pre class="prettyprint"><code class=" hljs sql">./hadoop/sbin/<span class="hljs-operator"><span class="hljs-keyword">start</span>-dfs.sh</span></code></pre>

<p>如果第一次使用HDFS的话，需要创建一个工作目录，这个目录只存在于hdfs文件系统中，并不会在你的系统文件中出现。</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">.</span>/hadoop/bin/hdfs dfs <span class="hljs-attribute">-mkdir</span> <span class="hljs-attribute">-p</span> /user/your_ubuntu_username</code></pre>

<p>使用ls命令可以查看当前hdfs文件系统中的内容</p>



<pre class="prettyprint"><code class=" hljs mel">./hadoop/bin/hdfs dfs -<span class="hljs-keyword">ls</span> /user/your_ubuntu_username</code></pre>

<p>使用put命令可以将本地文件上传至hdfs文件系统中</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">./hadoop/bin/hdfs dfs -<span class="hljs-built_in">put</span> /your_localfile_path /user/your_ubuntu_username/</code></pre>

<p>使用hdfs中的文件可以用以下路径</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/localhost:9000/user</span><span class="hljs-regexp">/your_ubuntu_username/your</span>_file.txt</code></pre>

<p>当然删除文件可以用-rm -rf关闭可以用</p>

<pre class="prettyprint"><code class=" hljs vbnet">./hadoop/sbin/<span class="hljs-keyword">stop</span>-dfs.sh</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
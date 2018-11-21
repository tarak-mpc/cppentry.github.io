---
layout:     post
title:      使用Docker运行spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kongxx/article/details/80906710				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="获取docker镜像">获取docker镜像</h2>



<pre class="prettyprint"><code class="language-shell hljs bash"><span class="hljs-built_in">sudo</span> docker pull sequenceiq/spark:<span class="hljs-number">1.6</span>.<span class="hljs-number">0</span></code></pre>



<h2 id="运行docker容器">运行docker容器</h2>



<pre class="prettyprint"><code class="language-shell hljs applescript">sudo docker <span class="hljs-command">run</span> -<span class="hljs-keyword">it</span> <span class="hljs-comment">--name spark --rm sequenceiq/spark:1.6.0 /bin/bash</span></code></pre>



<h2 id="运行作业">运行作业</h2>



<pre class="prettyprint"><code class="language-shell hljs lasso">$ cd /usr/<span class="hljs-built_in">local</span>/spark
$ bin/spark<span class="hljs-attribute">-submit</span> <span class="hljs-subst">--</span>master yarn<span class="hljs-attribute">-client</span> <span class="hljs-subst">--</span>class org<span class="hljs-built_in">.</span>apache<span class="hljs-built_in">.</span>spark<span class="hljs-built_in">.</span>examples<span class="hljs-built_in">.</span>JavaWordCount lib/spark<span class="hljs-attribute">-examples</span><span class="hljs-subst">-</span><span class="hljs-number">1.6</span><span class="hljs-number">.0</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-number">.6</span><span class="hljs-number">.0</span><span class="hljs-built_in">.</span>jar file:/usr/<span class="hljs-built_in">local</span>/hadoop/input<span class="hljs-subst">/</span></code></pre>

<p>我们也可以把启动容器和运行作业放在一起，比如：</p>



<pre class="prettyprint"><code class="language-shell hljs brainfuck"><span class="hljs-comment">sudo</span> <span class="hljs-comment">docker</span> <span class="hljs-comment">run</span> <span class="hljs-literal">-</span><span class="hljs-comment">it</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">name</span> <span class="hljs-comment">spark</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">rm</span> <span class="hljs-comment">sequenceiq/spark:1</span><span class="hljs-string">.</span><span class="hljs-comment">6</span><span class="hljs-string">.</span><span class="hljs-comment">0</span> <span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-comment">c</span> <span class="hljs-comment">"\"spark</span><span class="hljs-literal">-</span><span class="hljs-comment">submit</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">master</span> <span class="hljs-comment">yarn</span><span class="hljs-literal">-</span><span class="hljs-comment">client</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">class</span> <span class="hljs-comment">org</span><span class="hljs-string">.</span><span class="hljs-comment">apache</span><span class="hljs-string">.</span><span class="hljs-comment">spark</span><span class="hljs-string">.</span><span class="hljs-comment">examples</span><span class="hljs-string">.</span><span class="hljs-comment">JavaWordCount</span> <span class="hljs-comment">/usr/local/spark/lib/spark</span><span class="hljs-literal">-</span><span class="hljs-comment">examples</span><span class="hljs-literal">-</span><span class="hljs-comment">1</span><span class="hljs-string">.</span><span class="hljs-comment">6</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-literal">-</span><span class="hljs-comment">hadoop2</span><span class="hljs-string">.</span><span class="hljs-comment">6</span><span class="hljs-string">.</span><span class="hljs-comment">0</span><span class="hljs-string">.</span><span class="hljs-comment">jar</span> <span class="hljs-comment">file:/usr/local/hadoop/input/\""</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
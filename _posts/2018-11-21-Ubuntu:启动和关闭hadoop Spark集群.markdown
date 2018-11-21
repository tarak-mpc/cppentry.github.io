---
layout:     post
title:      Ubuntu:启动和关闭hadoop Spark集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ShellDawn/article/details/79539518				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre class="prettyprint"><code class=" hljs avrasm">hadoop/sbin/start-all<span class="hljs-preprocessor">.sh</span>
spark/sbin/start-master<span class="hljs-preprocessor">.sh</span>
spark/sbin/start-slaves<span class="hljs-preprocessor">.sh</span>

spark/sbin/stop-master<span class="hljs-preprocessor">.sh</span>
spark/sbin/stop-slaves<span class="hljs-preprocessor">.sh</span>
hadoop/sbin/stop-all<span class="hljs-preprocessor">.sh</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
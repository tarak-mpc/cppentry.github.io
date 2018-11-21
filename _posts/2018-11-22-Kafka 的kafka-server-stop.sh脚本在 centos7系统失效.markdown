---
layout:     post
title:      Kafka 的kafka-server-stop.sh脚本在 centos7系统失效
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/dmcpxy/article/details/69569175				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Kafka 的kafka-server-stop.sh脚本在 centos7系统失效，执行后并不能停止 kafka 进程。研究一下内容，可以用如下的命令来停止：</p>

<pre class="prettyprint"><code class="language-shell hljs bash">kill <span class="hljs-operator">-s</span> TERM $(jps <span class="hljs-operator">-l</span> | grep <span class="hljs-string">'kafka\.Kafka'</span> | awk <span class="hljs-string">'{print $1}'</span>)</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
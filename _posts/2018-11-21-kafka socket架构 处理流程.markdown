---
layout:     post
title:      kafka socket架构 处理流程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，如转载，请保留来源					https://blog.csdn.net/LG772EF/article/details/72821645				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka-socket架构">kafka socket架构</h1>

<p><img src="https://img-blog.csdn.net/20170531164851518?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTEc3NzJFRg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h1 id="kafka-socket处理流程">kafka socket处理流程</h1>

<h2 id="socket-server-startup">socket server startup</h2>

<p><img src="https://img-blog.csdn.net/20170531202009702?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTEc3NzJFRg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<br></p>



<h2 id="acceptor初始化">acceptor初始化</h2>

<p><img src="https://img-blog.csdn.net/20170531202035744?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTEc3NzJFRg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<br></p>



<h2 id="acceptorrun">acceptor.run</h2>

<p><img src="https://img-blog.csdn.net/20170531202100546?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTEc3NzJFRg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<br></p>



<h2 id="processorrun">processor.run</h2>

<p><img src="https://img-blog.csdn.net/20170531202135499?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvTEc3NzJFRg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
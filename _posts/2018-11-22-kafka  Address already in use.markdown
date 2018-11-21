---
layout:     post
title:      kafka  Address already in use
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：如若转载，请联系作者。					https://blog.csdn.net/liu16659/article/details/80023951				</div>
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="安装kafka时报错address-already-in-use">安装kafka时，报错：Address already in use</h3>

<p>1.可能的原因是：在安装zookeeper之后，又开启了zookeeper的server和client。然后又继续安装kafka，而kafka有继续开启zookeeper，导致重复使用端口报错。 <br>
2.解决方法，停止使用zookeeper的服务，或者直接开启kafka的server和client。而不是使用kafka开启zookeeper。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
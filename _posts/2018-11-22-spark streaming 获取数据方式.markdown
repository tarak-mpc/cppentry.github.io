---
layout:     post
title:      spark streaming 获取数据方式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013086392/article/details/78676934				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>推模式(Flume push SparkStreaming) VS 拉模式（SparkStreaming poll Flume）</strong> <br>
采用推模式：推模式的理解就是Flume作为缓存，存有数据。监听对应端口，如果服务可以链接，就将数据push过去。(简单，耦合要低)，缺点是SparkStreaming 程序没有启动的话，Flume端会报错，同时会导致Spark Streaming 程序来不及消费的情况。 <br>
采用拉模式：拉模式就是自己定义一个sink，SparkStreaming自己去channel里面取数据，根据自身条件去获取数据，稳定性好。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      单机安装Hbase、Phoenix
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_27252133/article/details/82903130				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="Hbase_0"></a>Hbase安装</h2>
<p><a href="http://www.bigdata234.com/2018/03/17/hbase%E5%8D%95%E6%9C%BA%E7%89%88%E7%9A%84%E5%AE%89%E8%A3%85/" rel="nofollow">http://www.bigdata234.com/2018/03/17/hbase单机版的安装/</a><br>
注意两点：</p>
<pre><code>1.启动hbase的时候注意要将我们外部的zk停掉
2.将Phoenix中phoenix-4.8.2-HBase-1.2-server.jar、phoenix-core-4.8.2-HBae-1.2.jar拷贝到hbase的lib中
</code></pre>
<h2><a id="Phoenix_7"></a>Phoenix安装</h2>
<p><a href="http://www.bigdata234.com/2018/03/18/apache-phoenix-%E5%8D%95%E6%9C%BA%E7%89%88%E7%9A%84%E5%AE%89%E8%A3%85/" rel="nofollow">http://www.bigdata234.com/2018/03/18/apache-phoenix-单机版的安装/</a><br>
这篇文章中有一个问题，启动Phoenix时注意两点：</p>
<pre><code>1. 保证queryserver服务启动  ./bin/queryserver.py start
2. 使用standalone方式登录Phoenix  ./bin/sqlline-thin.py localhsot:8765
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
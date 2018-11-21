---
layout:     post
title:      flume:java.lang.AbstractMethodError:getMaxBackOffSleepInterval()J
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>从网上下载开源工具 flume-ng-sql-source-1.3.7.jar，实现flume抓取大量的mysql数据时出错 <br>
flume:java.lang.AbstractMethodError:getMaxBackOffSleepInterval()J <br>
主要是因为该开源工具编译时用的flume-ng-core版本和flume环境中flume-ng-core版本不一致。 <br>
解决方案有两种： <br>
1.重新编译flume-ng-sql-source包，将flume-ng-core依赖修改为环境中版本 <br>
2.修改环境的flume版本</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
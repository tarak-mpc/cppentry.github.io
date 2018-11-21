---
layout:     post
title:      kafka  gc情况在clouder manager 如何查看
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>缘由：我们的kafka 服务由于是clouderManager 启动的，所以用户名是kafka,普通用户无权限，因此先 which jstat 查找下jstat安装位置，然后以kafka的身份去执行：sudo -u kafka /usr/local/jdk/bin/jstat -gcutil 21359</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
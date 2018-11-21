---
layout:     post
title:      Kafka遇到的坑-- Error while fetching metadata with correlation id   : {LEADER_NOT_AVAILABLE}
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.创建topic中出现出现错误</p>
<p>kafka出现:</p>
<p>Error while fetching metadata with correlation id   : {LEADER_NOT_AVAILABLE}</p>
<p>表示无法识别kafka hostname</p>
<p>正确处理姿势</p>
<p>1、修改server.properties如下，目的是让kafka监听的到</p>
<p>vi   /opt/kafka/kafka/config/server.properties<br>
<img src="https://img-blog.csdnimg.cn/20181115155033617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZjdDIwMDExNDAyNjk=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>同时一定写正确主机名称，切记！！<br>
我的之前写成hostname，<a href="http://xn--host-zi0il0y2ju8pd.name" rel="nofollow">正确的是host.name</a> ！注意一定要多看配置中的注释和小心谨慎。否则后期的排除bug真心是个力气活！！！</p>
<p><img src="https://img-blog.csdnimg.cn/20181115155546388.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZjdDIwMDExNDAyNjk=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      cloudera集成kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：博客内容会时不时更新的					https://blog.csdn.net/zhaomeng1123/article/details/48933819				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>CDH这个大包没有将kafka集成进来，所以单独来说下kafka</p>

<hr>

<h3 id="既然kafka没有被集成进来为什么添加服务的时候能看到有kafka">既然kafka没有被集成进来，为什么添加服务的时候能看到有kafka</h3>

<p><img src="https://img-blog.csdn.net/20151006194800972" alt="添加kafka" title=""></p>

<p>所看到的其实是Kafka Custom Service Descriptor (CSD)，这仅仅是一个kafka服务的描述和相关的配置参数，真正的安装包需要单独下载</p>

<p>步骤</p>

<ul>
<li>本文所使用版本见（<a href="http://blog.csdn.net/zhaomeng1123/article/details/48860437" rel="nofollow">cloudera manager和cdh离线安装</a>）</li>
<li>主页-&gt;主机-&gt;Parcel（选项）-&gt;编辑配置,添加kafka parcel地址 <br>
<ul><li><a href="http://archive.cloudera.com/kafka/parcels/latest/" rel="nofollow">http://archive.cloudera.com/kafka/parcels/latest/</a></li></ul></li>
<li>下载、分发、激活</li>
<li>添加服务</li>
<li>配置kafka的broker_max_heap_size,默认是0，可以配置为1</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
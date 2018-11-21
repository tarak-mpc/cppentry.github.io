---
layout:     post
title:      kafka-proxy的设计
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-tomorrow-night-eighties">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="kafka-proxy是什么">kafka-proxy是什么？</h3>

<blockquote>
  <p>kafka-proxy是位于kafka集群和应用系统之间的一层代理服务，负责将应用系统的消息转发给kafka集群</p>
</blockquote>

<h3 id="为什么要用这个东西">为什么要用这个东西？</h3>

<blockquote>
  <p>1.kafka的生产者来自于不同的元数据系统，在生产者与kafka集群之间添加一层代理可以屏蔽各生产者之间的差异，甚至有的语言没有对应的kafka客户端； <br>
  2.生产者与Kafka集群之间是通过建立tcp长连接来保持通信，如果每个生产者都与之建立长连接，那么会增加服务端的压力；</p>
</blockquote>

<h3 id="kafka-proxy主要的工作">Kafka-proxy主要的工作</h3>

<blockquote>
  <p>1.生产者系统的权限控制，防止系统的非法接入 <br>
  2.接入标准控制，保证写入的数据符合设计的规范 <br>
  3.请求过滤和转发 <br>
  4.负载均衡</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
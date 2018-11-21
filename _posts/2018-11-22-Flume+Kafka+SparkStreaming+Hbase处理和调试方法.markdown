---
layout:     post
title:      Flume+Kafka+SparkStreaming+Hbase处理和调试方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1. 首先得确保Flume能接受到数据和输出数据，所以源头监控log文件，输出在控制台</p>

<p>2. 确保kafka接受到数据并能通过consumer消费到，控制台输出</p>

<p>3.对接Flume和Kafka，将Flume的输出改为Kafka , 测试能否在Kafka处输出</p>

<p>4.SparkStreaming测试程序，测试能否接受到kafka数据并在控制台输出</p>

<p>5.编写sparkStreaming 处理逻辑， 并输出到hbase</p>

<p> </p>            </div>
                </div>
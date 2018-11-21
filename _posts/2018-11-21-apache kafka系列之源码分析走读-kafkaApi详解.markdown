---
layout:     post
title:      apache kafka系列之源码分析走读-kafkaApi详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:18px;color:rgb(0,153,0);">Kafka源码中数据交互流程</span></p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<img src="https://img-blog.csdn.net/20150228182541591?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" width="710" height="264" alt="" style="border:0px;vertical-align:middle;"><br></p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;"><span></span><span style="color:rgb(255,0,0);"><span></span>图1</span></span></p>
<h1 style="font-size:2.6em;font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);">
<a name="t0" style="background:transparent;color:rgb(79,161,219);"></a>1.概述</h1>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;">kafka启动时做很多初始化运行环境工作，具体请参考：<a href="http://blog.csdn.net/lizhitao/article/details/37911993" rel="nofollow" style="background:transparent;color:rgb(202,12,22);">apache
 kafka系列之源码分析走读-kafka内部模块分析</a></span></p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;">其中SockeServer类启动时，首先初始化NIO网络环境、启动监听、创建主线程、工作线程池、设置参数等等。</span></p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;">从上图1中可以看到整个交互过程中，kafka的所有逻辑处理和交互实际是交给KafkaApi类来处理的。</span></p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;"></span></p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;">通过请求的类型，把具体的request路由到对应的handler处理。目前kafka并没有把handler抽象出来，</span></p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;">而是每个handler都是一个函数，混在KafkaApi类中。</span><br></p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<h1 style="font-size:2.6em;font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);">
<a name="t1" style="background:transparent;color:rgb(79,161,219);"></a>2. Request请求类别</h1>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
kafka-0.8.1版本中定义了10种类型请求，请求类型说明如下：</p>
<p class="p1" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<table cellspacing="0" cellpadding="0" style="border-collapse:collapse;border-spacing:0px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><tbody><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(33,33,33) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="color:rgb(0,0,0);font-size:14px;font-family:'Heiti SC Light';letter-spacing:0px;">参数</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="color:rgb(0,0,0);font-size:14px;font-family:'Heiti SC Light';letter-spacing:0px;">说明</span><span style="color:rgb(0,0,0);font-size:14px;font-family:Helvetica;letter-spacing:0px;">(</span><span style="color:rgb(0,0,0);font-size:14px;font-family:'Heiti SC Light';letter-spacing:0px;">解释</span><span style="color:rgb(0,0,0);font-size:14px;font-family:Helvetica;letter-spacing:0px;">)</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="color:rgb(0,0,0);font-size:14px;font-family:'Heiti SC Light';letter-spacing:0px;">请求二进制数据解码类</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.ProduceKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">producer</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">ProducerRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.FetchKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">consumer</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">FetchRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.OffsetsKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">topic</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">的</span><span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">offset</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">OffsetRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.MetadataKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">topic</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">元数据请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">TopicMetadataRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.LeaderAndIsrKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">leader</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">和</span><span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">isr</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">信息更新请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">LeaderAndIsrRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.StopReplicaKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">停止</span><span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">replica</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">StopReplicaRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.UpdateMetadataKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">更新元数据请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">UpdateMetadataRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.ControlledShutdownKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">controlledShutdown</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">ControlledShutdownRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.OffsetCommitKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">commitOffset</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">OffsetCommitRequest</span></p>
</td>
</tr><tr><td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(0,0,0) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">RequestKeys.OffsetFetchKey</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(33,33,33) rgb(0,0,0) rgb(0,0,0);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">consumer</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">的</span><span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">offset</span><span style="font-family:'Heiti SC Light';letter-spacing:0px;color:rgb(4,51,255);">请求</span></p>
</td>
<td valign="top" style="border-style:solid;border-width:1px;border-color:rgb(0,0,0) rgb(0,0,0) rgb(170,170,170) rgb(33,33,33);">
<p>
<span style="font-family:Helvetica;letter-spacing:0px;color:rgb(4,51,255);">OffsetFetchRequest</span></p>
</td>
</tr></tbody></table><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
下面是KafkaApi中handle方法代码：</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;background-color:rgb(231,229,220);color:rgb(69,69,69);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);border-right:1px solid rgb(231,229,220);">
<span>[java]</span> <a href="http://blog.csdn.net/lizhitao/article/details/43987319#" rel="nofollow" class="ViewSource" title="view plain" style="background-color:inherit;color:rgb(160,160,160);border:none;display:inline-block;width:16px;text-indent:-2000px;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/lizhitao/article/details/43987319#" rel="nofollow" class="CopyToClipboard" title="copy" style="background-color:inherit;color:rgb(160,160,160);border:none;display:inline-block;width:16px;text-indent:-2000px;">copy</a></span>
<div style="width:16px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" class="dp-j" style="border-top:none;border-right:1px solid rgb(231,229,220);border-bottom:none;border-left:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span style="border:none;background-color:inherit;">def handle(request: RequestChannel.Request) {  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">try</span><span style="border:none;background-color:inherit;">{  </span></span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    trace(<span class="string" style="border:none;color:rgb(0,0,255);background-color:inherit;">"Handling request: "</span><span style="border:none;background-color:inherit;"> + request.requestObj + </span><span class="string" style="border:none;color:rgb(0,0,255);background-color:inherit;">" from client: "</span><span style="border:none;background-color:inherit;"> + request.remoteAddress)  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    request.requestId match {  </span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.ProduceKey =&gt; handleProducerRequest(request)  </span><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// producer</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.FetchKey =&gt; handleFetchRequest(request)       </span><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// consumer</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.OffsetsKey =&gt; handleOffsetRequest(request)  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.MetadataKey =&gt; handleTopicMetadataRequest(request)  </span></span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.LeaderAndIsrKey =&gt; handleLeaderAndIsrRequest(request) </span><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">//成为leader或follower设置同步副本组信息</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.StopReplicaKey =&gt; handleStopReplicaRequest(request)  </span></span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.UpdateMetadataKey =&gt; handleUpdateMetadataRequest(request)  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.ControlledShutdownKey =&gt; handleControlledShutdownRequest(request)  </span><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">//shutdown broker</span><span style="border:none;background-color:inherit;">  </span></span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.OffsetCommitKey =&gt; handleOffsetCommitRequest(request)  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> RequestKeys.OffsetFetchKey =&gt; handleOffsetFetchRequest(request)  </span></span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> requestId =&gt; </span><span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">throw</span><span style="border:none;background-color:inherit;"> </span><span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">new</span><span style="border:none;background-color:inherit;"> KafkaException(</span><span class="string" style="border:none;color:rgb(0,0,255);background-color:inherit;">"Unknown api code "</span><span style="border:none;background-color:inherit;"> + requestId)  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    }  </span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  } <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">catch</span><span style="border:none;background-color:inherit;"> {  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">case</span><span style="border:none;background-color:inherit;"> e: Throwable =&gt;  </span></span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      request.requestObj.handleError(e, requestChannel, request)  </span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      error(<span class="string" style="border:none;color:rgb(0,0,255);background-color:inherit;">"error when handling request %s"</span><span style="border:none;background-color:inherit;">.format(request.requestObj), e)  </span></span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  } <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);font-size:14px;border:none;">finally</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    request.apiLocalCompleteTimeMs = SystemTime.milliseconds  </span></li><li class="alt" style="margin-left:40px;list-style:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">}  </span></li></ol></div>
<br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><h1 style="font-size:2.6em;font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);">
<a name="t2" style="background:transparent;color:rgb(79,161,219);"></a>3.<span style="font-size:24px;">请求交互二进制数据格式</span></h1>
<div style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;">kafka中客户端与server端交互有多种类型，那它是怎么交互数据呢，格式是怎样？下面来揭开面纱。</span></div>
<div style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;">请求交互二进制数据</span><span style="font-size:14px;">组成为：请求类型 + 请求数据。</span></div>
<div style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;"><img src="https://img-blog.csdn.net/20150228180452154?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" width="338" height="120" alt="" style="border:0px;vertical-align:middle;"><br></span></div>
<h2 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:2.15em;">
<a name="t3" style="background:transparent;color:rgb(79,161,219);"></a><span style="font-size:14px;">3.1 <span style="font-family:Helvetica;line-height:26px;">ProducerRequest二进制格式</span></span></h2>
<div style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-size:14px;"><span style="font-family:Helvetica;line-height:26px;"><img src="https://img-blog.csdn.net/20150228190346680?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span></div>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
3.2 FetchRequest<span style="font-family:Helvetica;line-height:26px;font-size:14px;">二进制格式</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Helvetica;"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20150301220338340?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span>3.3 OffsetRequest<span style="font-family:Helvetica;font-size:14px;line-height:26px;">二进制格式</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Helvetica;"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20150301221357742?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span>3.4 TopicMetadataRequest<span style="font-family:Helvetica;font-size:14px;line-height:26px;">二进制格式</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Helvetica;"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20150301221404041?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span>3.5 LeaderAndIsrRequest<span style="font-family:Helvetica;font-size:14px;line-height:26px;">二进制格式</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Helvetica;"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20150301221441510?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span>3.6 StopReplicaRequest<span style="font-family:Helvetica;font-size:14px;line-height:26px;">二进制格式</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Helvetica;"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20150301221458232?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span>3.7 UpdateMetadataRequest<span style="font-family:Helvetica;font-size:14px;line-height:26px;">二进制格式</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Helvetica;"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20150301221514589?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span>3.8 ControlledShutdownRequest<span style="font-family:Helvetica;font-size:14px;line-height:26px;">二进制格式</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Helvetica;"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20150301221506535?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span>3.9 OffsetCommitRequest<span style="font-family:Helvetica;font-size:14px;line-height:26px;">二进制格式</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="font-family:Helvetica;"><span style="font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20150301221521027?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;"><br></span></span>3.10 OffsetFetchRequest<span style="font-family:Helvetica;font-size:14px;line-height:26px;">二进制格式</span></p>
<img src="https://img-blog.csdn.net/20150301221557009?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:0px;vertical-align:middle;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><div><br></div>
            </div>
                </div>
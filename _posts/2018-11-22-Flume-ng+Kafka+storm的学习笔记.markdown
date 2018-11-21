---
layout:     post
title:      Flume-ng+Kafka+storm的学习笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
Flume-ng</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(101,101,101)">Flume</span><span style="color:rgb(101,101,101)">是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
     Flume的文档可以看<a target="_blank" href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow" style="color:rgb(255,153,0); text-decoration:none">http://flume.apache.org/FlumeUserGuide.html</a> 官方的英文文档 介绍的比较全面。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
       不过这里写写自己的见解</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<img src="https://img-blog.csdn.net/20140121113721015?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenhjdmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:none; max-width:100%"><br>
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
这个是flume的架构图</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 从上图可以看到几个名词：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
Agent: 一个Agent包含<span style="color:rgb(54,46,43)">Source</span><span style="color:rgb(54,46,43)">、</span><span style="color:rgb(54,46,43)">Channel</span><span style="color:rgb(54,46,43)">、</span><span style="color:rgb(54,46,43)">Sink</span><span style="color:rgb(54,46,43)">和其他的组件。</span><span style="color:rgb(54,46,43)">Flume</span><span style="color:rgb(54,46,43)">就是一个或多个</span><span style="color:rgb(54,46,43)">Agent</span><span style="color:rgb(54,46,43)">构成的。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
      Source：数据源。简单的说就是agent获取数据的入口 。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
       Channel：管道。数据流通和存储的通道。一个source必须至少和一个channel关联。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
       Sink：用来接收channel传输的数据并将之传送到指定的地方。传送成功后数据从channel中删除。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
Flume具有高可扩展性 可随意组合：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 <img src="https://img-blog.csdn.net/20140121113950031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenhjdmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:none; max-width:100%"></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
注意 source是接收源 sink是发送源</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<br>
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<img src="https://img-blog.csdn.net/20140121114003328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenhjdmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:none; max-width:100%"><img src="https://img-blog.csdn.net/20140121114011531?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenhjdmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:none; max-width:100%"><br>
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
上图是一个source将数据发给3个channel 其中的sink2将数据发给JMS ，sink3将数据发给另一个source。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
总的来说flume的扩展性非常高 根据需要可随意组合。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
现在在说说一个概念叫Event：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
       Event是flume的数据传输的基本单元。Flume本质上是将数据作为一个event从源头传到结尾。是由可选的Headers和载有数据的一个byte array构成。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
  代码结构：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<br>
</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas,'Courier New',Courier,mono,serif; width:936.53125px; overflow:hidden; padding-top:1px; line-height:26px; margin:18px 0px!important">
<div class="bar" style="position:relative; padding-left:45px">
<div class="tools" style="padding:3px 8px 10px 10px; font-size:9px; line-height:normal; font-family:Verdana,Geneva,Arial,Helvetica,sans-serif; color:silver; border-left-width:3px; border-left-style:solid; border-left-color:rgb(153,153,153)">
<strong>[java]</strong> <a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">view
 plain</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">copy</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">print</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="About" title="?" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">?</a>
<div style="position:absolute; left:638px; top:2106px; width:27px; height:15px; z-index:99">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="padding:0px; border:none; position:relative; color:rgb(92,92,92); margin:0px 0px 1px 45px!important">
<li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">/**</span> </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit"> * Basic representation of a data object inFlume.</span> </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit"> * Provides access to data as it flows throughthe system.</span> </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit"> */</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">interface</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Event{  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">  <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">/**</span> </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   * Returns a map of name-valuepairs describing the data stored in the body.</span> </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   */</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">  <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Map&lt;String, String&gt; getHeaders();  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">  <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">/**</span> </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   * Set the event headers</span> </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   * @param headersMap of headers to replace the current headers.</span> </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   */</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">  <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> setHeaders(Map&lt;String, String&gt; headers);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">  <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">/**</span> </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   * Returns the raw byte array of the datacontained in this event.</span> </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   */</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">  <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[] getBody();  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">  <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">/**</span> </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   * Sets the raw byte array of the datacontained in this event.</span> </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   * @param body Thedata.</span> </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">   */</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">  <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> setBody(</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[] body);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">}  </span></li></ol>
</div>
<br style="font-family:Arial; font-size:14px; line-height:26px">
<br style="font-family:Arial; font-size:14px; line-height:26px">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
这个是网上找的flume channel ，source，sink的汇总</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
链接是<a target="_blank" href="http://abloz.com/2013/02/26/flume-channel-source-sink-summary.html" rel="nofollow" style="color:rgb(255,153,0); text-decoration:none">http://abloz.com/2013/02/26/flume-channel-source-sink-summary.html</a></p>
<table border="0" cellspacing="0" cellpadding="0" width="274" style="color:rgb(0,0,0); font-family:Arial; font-size:14px; line-height:26px">
<tbody>
<tr>
<td>
<table border="0" cellspacing="0" cellpadding="0" width="975">
<tbody>
<tr>
<td style="background:rgb(221,221,221)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<strong><span style="color:rgb(51,51,51)">Component</span></strong></p>
</td>
<td style="background:rgb(221,221,221)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<strong><span style="color:rgb(51,51,51)">Type</span></strong></p>
</td>
<td style="background:rgb(221,221,221)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<strong><span style="color:rgb(51,51,51)">Description</span></strong></p>
</td>
<td style="background:rgb(221,221,221)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<strong><span style="color:rgb(51,51,51)">Implementation Class</span></strong></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Channel</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">memory</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">In-memory, fast, non-durable event transport</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">MemoryChannel</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Channel</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">file</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">A channel for reading, writing, mapping, and manipulating a file</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">FileChannel</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Channel</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">jdbc</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">JDBC-based, durable event transport (Derby-based)</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">JDBCChannel</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Channel</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">recoverablememory</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">A durable channel implementation that uses the local file system for its storage</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">RecoverableMemoryChannel</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Channel</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.channel.PseudoTxnMemoryChannel</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Mainly for testing purposes. Not meant for production use.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">PseudoTxnMemoryChannel</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Channel</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom type as FQCN)</span></em></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own Channel impl.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom FQCN)</span></em></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">avro</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Avro Netty RPC event source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">AvroSource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">exec</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Execute a long-lived Unix process and read from stdout</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">ExecSource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">netcat</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Netcat style TCP event source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">NetcatSource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">seq</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Monotonically incrementing sequence generator event source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SequenceGeneratorSource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.source.StressSource</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Mainly for testing purposes. Not meant for production use. Serves as a continuous source of events where each event has the same payload. The payload consists of some number of bytes (specified by</span><em><span style="color:rgb(51,51,51)">size</span></em><span style="color:rgb(51,51,51)"> property,
 defaults to 500) where each byte has the signed value Byte.MAX_VALUE (0x7F, or 127).</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.source.StressSource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">syslogtcp</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SyslogTcpSource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">syslogudp</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SyslogUDPSource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.source.avroLegacy.AvroLegacySource</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">AvroLegacySource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.source.thriftLegacy.ThriftLegacySource</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">ThriftLegacySource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.source.scribe.ScribeSource</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">ScribeSource</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Source</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom type as FQCN)</span></em></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own Source impl.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom FQCN)</span></em></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">hdfs</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Writes all events received to HDFS (with support for rolling, bucketing, HDFS-200 append, and more)</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">HDFSEventSink</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.sink.hbase.HBaseSink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">A simple sink that reads events from a channel and writes them to HBase.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.sink.hbase.HBaseSink</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.sink.hbase.AsyncHBaseSink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.sink.hbase.AsyncHBaseSink</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">logger</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Log events at INFO level via configured logging subsystem (log4j by default)</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">LoggerSink</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">avro</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink that invokes a pre-defined Avro protocol method for all events it receives (when paired with an avro source, forms tiered collection)</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">AvroSink</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">file_roll</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">RollingFileSink</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">irc</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">IRCSink</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">null</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">/dev/null for Flume – blackhole all events received</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">NullSink</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Sink</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom type as FQCN)</span></em></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own Sink impl.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom FQCN)</span></em></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">ChannelSelector</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">replicating</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">ReplicatingChannelSelector</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">ChannelSelector</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">multiplexing</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">MultiplexingChannelSelector</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">ChannelSelector</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">(custom type)</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own ChannelSelector impl.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom FQCN)</span></em></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SinkProcessor</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">default</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">DefaultSinkProcessor</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SinkProcessor</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">failover</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">FailoverSinkProcessor</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SinkProcessor</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">load_balance</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Provides the ability to load-balance flow over multiple sinks.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">LoadBalancingSinkProcessor</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SinkProcessor</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom type as FQCN)</span></em></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own SinkProcessor impl.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom FQCN)</span></em></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Interceptor$Builder</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">host</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">HostInterceptor$Builder</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Interceptor$Builder</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">timestamp</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">TimestampInterceptor</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">TimestampInterceptor$Builder</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Interceptor$Builder</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">static</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">StaticInterceptor$Builder</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Interceptor$Builder</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">regex_filter</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">RegexFilteringInterceptor$Builder</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Interceptor$Builder</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom type as FQCN)</span></em></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own Interceptor$Builder impl.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom FQCN)</span></em></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">EventSerializer$Builder</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">text</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">BodyTextEventSerializer$Builder</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">EventSerializer$Builder</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">avro_event</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">FlumeEventAvroEventSerializer$Builder</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">EventSerializer</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.sink.hbase.SimpleHbaseEventSerializer</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SimpleHbaseEventSerializer</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">EventSerializer</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.sink.hbase.SimpleAsyncHbaseEventSerializer</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">SimpleAsyncHbaseEventSerializer</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">EventSerializer</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">org.apache.flume.sink.hbase.RegexHbaseEventSerializer</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">RegexHbaseEventSerializer</span></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">HbaseEventSerializer</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Custom implementation of serializer for HBaseSink.<br>
</span><em><span style="color:rgb(51,51,51)">(custom type as FQCN)</span></em></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own HbaseEventSerializer impl.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom FQCN)</span></em></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">AsyncHbaseEventSerializer</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Custom implementation of serializer for AsyncHbase sink.<br>
</span><em><span style="color:rgb(51,51,51)">(custom type as FQCN)</span></em></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own AsyncHbaseEventSerializer impl.</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<em><span style="color:rgb(51,51,51)">(custom FQCN)</span></em></p>
</td>
</tr>
<tr>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">EventSerializer$Builder</span></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Custom implementation of serializer for all sinks except for HBaseSink and AsyncHBaseSink.<br>
</span><em><span style="color:rgb(51,51,51)">(custom type as FQCN)</span></em></p>
</td>
<td style="background:rgb(239,239,239)">
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
<span style="color:rgb(51,51,51)">Your own EventSerializer$Builder impl.</span></p>
</td>
</tr>
</tbody>
</table>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px">
 </p>
</td>
</tr>
</tbody>
</table>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
下面介绍下kafka以及kafka和flume的整合</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
Kafka：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
       从这个链接抄了些内容下来<a target="_blank" href="http://dongxicheng.org/search-engine/kafka/" rel="nofollow" style="color:rgb(255,153,0); text-decoration:none">http://dongxicheng.org/search-engine/kafka/</a></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px; background:rgb(247,243,237)">
  Kafka是Linkedin于2010年12月份开源的消息系统，它主要用于处理活跃的流式数据。活跃的流式数据在web网站应用中非常常见，这些数据包括网站的pv、用户访问了什么内容，搜索了什么内容等。 这些数据通常以日志的形式记录下来，然后每隔一段时间进行一次统计处理。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
传统的日志分析系统提供了一种离线处理日志信息的可扩展方案，但若要进行实时处理，通常会有较大延迟。而现有的消（队列）系统能够很好的处理实时或者近似实时的应用，但未处理的数据通常不会写到磁盘上，这对于Hadoop之类（一小时或者一天只处理一部分数据）的离线应用而言，可能存在问题。Kafka正是为了解决以上问题而设计的，它能够很好地离线和在线应用。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<strong>2、  设计目标</strong></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
（1）数据在磁盘上存取代价为O(1)。一般数据在磁盘上是使用BTree存储的，存取代价为O（lgn）。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
（2）高吞吐率。即使在普通的节点上每秒钟也能处理成百上千的message。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
（3）显式分布式，即所有的producer、broker和consumer都会有多个，均为分布式的。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
（4）支持数据并行加载到Hadoop中。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<strong>3、  KafKa部署结构</strong></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<strong><img src="https://img-blog.csdn.net/20140121114101953?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenhjdmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:none; max-width:100%"><br>
</strong></p>
<p align="center" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<strong></strong></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
kafka是显式分布式架构，producer、broker（Kafka）和consumer都可以有多个。Kafka的作用类似于缓存，即活跃的数据和离线处理系统之间的缓存。几个基本概念：</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
（1）message（消息）是通信的基本单位，每个producer可以向一个topic（主题）发布一些消息。如果consumer订阅了这个主题，那么新发布的消息就会广播给这些consumer。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
（2）Kafka是显式分布式的，多个producer、consumer和broker可以运行在一个大的集群上，作为一个逻辑整体对外提供服务。对于consumer，多个consumer可以组成一个group，这个message只能传输给某个group中的某一个consumer.</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px; background:rgb(247,243,237)">
 </p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px; background:rgb(247,243,237)">
  <span style="color:rgb(34,34,34); background:white">数据从</span><span style="color:rgb(34,34,34); background:white">producer</span><span style="color:rgb(34,34,34); background:white">推送到</span><span style="color:rgb(34,34,34); background:white">broker</span><span style="color:rgb(34,34,34); background:white">，接着</span><span style="color:rgb(34,34,34); background:white">consumer</span><span style="color:rgb(34,34,34); background:white">在从</span><span style="color:rgb(34,34,34); background:white">broker</span><span style="color:rgb(34,34,34); background:white">上拉取数据。<a target="_blank" name="OLE_LINK2" style="color:rgb(255,153,0)"></a><a target="_blank" name="OLE_LINK1" style="color:rgb(255,153,0)">Zookeeper</a></span>是一个<span style="color:rgb(34,34,34); background:white">分布式服务框架
 用来解决分布式应用中的数据管理问题等。</span></p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px; background:rgb(247,243,237)">
<span style="color:rgb(34,34,34); background:white"> </span><span style="color:rgb(34,34,34); background:white">在kafka</span>中 有几个重要概念<span style="color:rgb(34,34,34); background:white">producer</span><span style="color:rgb(34,34,34); background:white">生产者
 consumer</span> 消费者 topic 主题。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px; background:rgb(247,243,237)">
我们来实际开发一个简单的生产者消费者的例子。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px; background:rgb(247,243,237)">
生产者：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
   </p>
<div class="dp-highlighter bg_java" style="font-family:Consolas,'Courier New',Courier,mono,serif; width:936.53125px; overflow:hidden; padding-top:1px; line-height:26px; margin:18px 0px!important">
<div class="bar" style="position:relative; padding-left:45px">
<div class="tools" style="padding:3px 8px 10px 10px; font-size:9px; line-height:normal; font-family:Verdana,Geneva,Arial,Helvetica,sans-serif; color:silver; border-left-width:3px; border-left-style:solid; border-left-color:rgb(153,153,153)">
<strong>[java]</strong> <a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">view
 plain</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">copy</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">print</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="About" title="?" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">?</a>
<div style="position:absolute; left:638px; top:5552px; width:27px; height:15px; z-index:99">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="padding:0px; border:none; position:relative; color:rgb(92,92,92); margin:0px 0px 1px 45px!important">
<li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> classProducerTest {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">       </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">static</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> main(String[] args) {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            Properties props = newProperties();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                props.setProperty(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"metadata.broker.list"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"xx.xx.xx.xx:9092"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">             props.setProperty(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"serializer.class"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"kafka.serializer.StringEncoder"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">              props.put(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"request.required.acks"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"1"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">              ProducerConfigconfig = <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> ProducerConfig(props);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">             Producer&lt;String, String&gt; producer = newProducer&lt;String, String&gt;(config);  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">             KeyedMessage&lt;String, String&gt; data = newKeyedMessage&lt;String, String&gt;(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"kafka"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"test-kafka"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">              <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">try</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                producer.send(data);  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 } <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">catch</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> (Exception e) {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                  e.printStackTrace();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">             producer.close();   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    }  </span></li></ol>
</div>
<br style="font-family:Arial; font-size:14px; line-height:26px">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
上面的代码中的xx.xx.xx.xx是kafka server的地址.</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
上面代码的意思就是向主题 kafka中同步（不配置的话 默认是同步发射）发送了一个信息 是test-kafka.</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
下面来看看消费者：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
      </p>
<div class="dp-highlighter bg_java" style="font-family:Consolas,'Courier New',Courier,mono,serif; width:936.53125px; overflow:hidden; padding-top:1px; line-height:26px; margin:18px 0px!important">
<div class="bar" style="position:relative; padding-left:45px">
<div class="tools" style="padding:3px 8px 10px 10px; font-size:9px; line-height:normal; font-family:Verdana,Geneva,Arial,Helvetica,sans-serif; color:silver; border-left-width:3px; border-left-style:solid; border-left-color:rgb(153,153,153)">
<strong>[java]</strong> <a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">view
 plain</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">copy</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">print</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="About" title="?" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">?</a>
<div style="position:absolute; left:638px; top:6072px; width:27px; height:15px; z-index:99">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="padding:0px; border:none; position:relative; color:rgb(92,92,92); margin:0px 0px 1px 45px!important">
<li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> classConsumerTest </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">extends</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Thread {   </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> finalConsumerConnector consumer;   </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">final</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> String topic;   </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">static</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> voidmain(String[] args) {   </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        ConsumerTest consumerThread = newConsumerTest(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"kafka"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);   </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        consumerThread.start();   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    }   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    publicConsumerTest(String topic) {   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        consumer =kafka.consumer.Consumer   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                .createJavaConsumerConnector(createConsumerConfig());   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.topic =topic;   </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    }   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> staticConsumerConfig createConsumerConfig() {   </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        Properties props = newProperties();   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        props.put(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"zookeeper.connect"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"xx.xx.xx.xx:2181"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);   </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        props.put(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"group.id"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">, </span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"0"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);   </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        props.put(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"zookeeper.session.timeout.ms"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"10000"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);   </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">//       props.put("zookeeper.sync.time.ms", "200"); </span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">//       props.put("auto.commit.interval.ms", "1000"); </span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">return</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> newConsumerConfig(props);   </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    }   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> run(){   </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        Map&lt;String,Integer&gt; topickMap = <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> HashMap&lt;String, Integer&gt;();  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">        topickMap.put(topic, <span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">1</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         Map&lt;String, List&lt;KafkaStream&lt;<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[],</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[]&gt;&gt;&gt;  streamMap =consumer.createMessageStreams(topickMap);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         KafkaStream&lt;<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[],</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[]&gt;stream = streamMap.get(topic).get(</span><span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">0</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         ConsumerIterator&lt;<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[],</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[]&gt; it =stream.iterator();  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         System.out.println(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"--------------------------"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">while</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">(it.hasNext()){  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">//</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">             System.out.println(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"(consumer)--&gt; "</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> +</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> String(it.next().message()));  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">    }   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">}  </span></li></ol>
</div>
<br style="font-family:Arial; font-size:14px; line-height:26px">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
上面的代码就是负责接收生产者发送过来的消息 测试的时候先开启消费者 然后再运行生产者即可看到效果。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
接下来 我们将flume 和kafka进行整合：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 在flume的source数据源接收到数据后 通过管道 到达sink，我们需要写一个kafkaSink 来将sink从channel接收的数据作为kafka的生产者 将数据 发送给消费者。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 具体代码：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
     </p>
<div class="dp-highlighter bg_java" style="font-family:Consolas,'Courier New',Courier,mono,serif; width:936.53125px; overflow:hidden; padding-top:1px; line-height:26px; margin:18px 0px!important">
<div class="bar" style="position:relative; padding-left:45px">
<div class="tools" style="padding:3px 8px 10px 10px; font-size:9px; line-height:normal; font-family:Verdana,Geneva,Arial,Helvetica,sans-serif; color:silver; border-left-width:3px; border-left-style:solid; border-left-color:rgb(153,153,153)">
<strong>[java]</strong> <a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">view
 plain</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">copy</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">print</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="About" title="?" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">?</a>
<div style="position:absolute; left:638px; top:7058px; width:27px; height:15px; z-index:99">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="padding:0px; border:none; position:relative; color:rgb(92,92,92); margin:0px 0px 1px 45px!important">
<li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">class</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> KafkaSink </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">extends</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> AbstractSinkimplementsConfigurable {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">       </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">static</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">final</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Log logger = LogFactory.getLog(KafkaSink.</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">class</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">       </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Stringtopic;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Producer&lt;String, String&gt;producer;  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">       </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Status process()throwsEventDeliveryException {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            Channel channel =getChannel();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         Transaction tx =channel.getTransaction();  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">try</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 tx.begin();  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 Event e = channel.take();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">if</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">(e ==</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">null</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">) {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                         tx.rollback();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                         <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">return</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Status.BACKOFF;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 KeyedMessage&lt;String,String&gt; data = <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> KeyedMessage&lt;String, String&gt;(topic,newString(e.getBody()));  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 producer.send(data);  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 logger.info(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"Message: {}"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">+</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> String( e.getBody()));  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 tx.commit();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">return</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Status.READY;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         } <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">catch</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">(Exceptione) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           logger.error(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"KafkaSinkException:{}"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,e);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 tx.rollback();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">return</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Status.BACKOFF;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         } <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">finally</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 tx.close();  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">         }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> configure(Context context) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           topic = <span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"kafka"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            Properties props = newProperties();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                props.setProperty(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"metadata.broker.list"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"xx.xx.xx.xx:9092"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">             props.setProperty(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"serializer.class"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"kafka.serializer.StringEncoder"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">//           props.setProperty("producer.type", "async");</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">//           props.setProperty("batch.num.messages", "1");</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">              props.put(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"request.required.acks"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"1"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">              ProducerConfigconfig = <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> ProducerConfig(props);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">              producer = newProducer&lt;String, String&gt;(config);  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">}  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li></ol>
</div>
<br style="font-family:Arial; font-size:14px; line-height:26px">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
将此文件打成jar包 传到flume的lib下面 如果你也用的是maven的话 需要用到assembly 将依赖的jar包一起打包进去。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
      在flume的配置是如下：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas,'Courier New',Courier,mono,serif; width:936.53125px; overflow:hidden; padding-top:1px; line-height:26px; margin:18px 0px!important">
<div class="bar" style="position:relative; padding-left:45px">
<div class="tools" style="padding:3px 8px 10px 10px; font-size:9px; line-height:normal; font-family:Verdana,Geneva,Arial,Helvetica,sans-serif; color:silver; border-left-width:3px; border-left-style:solid; border-left-color:rgb(153,153,153)">
<strong>[plain]</strong> <a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">view
 plain</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">copy</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">print</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="About" title="?" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">?</a>
<div style="position:absolute; left:642px; top:8066px; width:27px; height:15px; z-index:99">
</div>
</div>
</div>
<ol start="1" style="padding:0px; border:none; position:relative; color:rgb(92,92,92); margin:0px 0px 1px 45px!important">
<li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      agent1.sources = source1  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.sinks = sink1  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.channels =channel1  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"># Describe/configuresource1  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.sources.source1.type= avro  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.sources.source1.bind= localhost  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.sources.source1.port= 44444  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"># Describe sink1  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.sinks.sink1.type= xx.xx.xx.KafkaSink(这是类的路径地址)  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"># Use a channel whichbuffers events in memory  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.channels.channel1.type= memory  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.channels.channel1.capacity= 1000  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.channels.channel1.transactionCapactiy= 100  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"># Bind the source andsink to the channel  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.sources.source1.channels= channel1  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">agent1.sinks.sink1.channel= channel1  </span></li></ol>
</div>
<br style="font-family:Arial; font-size:14px; line-height:26px">
<br style="font-family:Arial; font-size:14px; line-height:26px">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 </p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
测试的话是avro的方式传送数据的 可以这样测试</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(75,75,75)">bin/flume-ng avro-client--conf conf -H localhost -p 44444 -F<a target="_blank" name="OLE_LINK4" style="color:rgb(255,153,0)"></a><a target="_blank" name="OLE_LINK3" style="color:rgb(255,153,0)">/data/flumetmp/a</a></span></p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(75,75,75)">/data/flumetmp/a</span> <span style="color:rgb(75,75,75)">这个为文件的地址.</span></p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
测试的时候在本地 一定要把上面写的消费者程序打开 以便接收数据测试是否成功。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
接下来我们介绍下storm然后将kafka的消费者和storm进行整合：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
Storm：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
   Storm是一个分布式的实时消息处理系统。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 Storm各个组件之间的关系：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<img src="https://img-blog.csdn.net/20140121114156343?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenhjdmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" style="border:none; max-width:100%"><br>
</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51)">Storm</span><span style="color:rgb(51,51,51)">集群主要由一个主节点和一群工作节点（worker node</span>）组成，通过 Zookeeper进行协调。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51)"> </span><span style="color:rgb(51,51,51)">主节点：主节点通常运行一个后台程序 —— Nimbus</span>，用于响应分布在集群中的节点，分配任务和监测故障。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(51,51,51)">工作节点：</span><span style="color:rgb(51,51,51)"> </span><span style="color:rgb(51,51,51)">Supervisor</span>,<span style="color:rgb(69,69,69); background:rgb(252,248,233)">负责接受nimbus</span>分配的任务，启动和停止属于自己管理的worker进程。Nimbus和Supervisor之间的协调由zookeeper完成。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(69,69,69); background:rgb(252,248,233)"> Worker</span><span style="color:rgb(69,69,69); background:rgb(252,248,233)">：处理逻辑的进程，在其中运行着多个Task</span>，每个task 是一组spout/blots的组合。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(69,69,69); background:rgb(252,248,233)"> </span></p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<span style="color:rgb(69,69,69); background:rgb(252,248,233)">Topology</span>:<span style="color:rgb(69,69,69); background:rgb(252,248,233)">是storm</span>的实时应用程序，从启动开始一直运行，只要有tuple过来 就会触发执行。拓扑：storm的消息流动很像一个拓扑结构。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<strong>2. </strong>stream是storm的核心概念，一个stream是一个持续的tuple序列，这些tuple被以分布式并行的方式创建和处理。</p>
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<strong>3.</strong> spouts是一个stream的源头，spouts负责从外部系统读取数据，并组装成tuple发射出去，tuple被发射后就开始再topology中传播。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
<strong>4.</strong> bolt是storm中处理 数据的核心，storm中所有的数据处理都是在bolt中完成的</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
这里就简单介绍一些概念 具体的可以看些详细的教程。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 </p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
我们接下来开始整合storm和kafka。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
从上面的介绍得知storm的spout是负责从外部读取数据的 所以我们需要开发一个KafkaSpout 来作为kafka的消费者和storm的数据接收源。可以看看这个<a target="_blank" href="https://github.com/HolmesNL/kafka-spout" rel="nofollow" style="color:rgb(255,153,0); text-decoration:none">https://github.com/HolmesNL/kafka-spout</a>。我在下面只写一个简单的可供测试。</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
具体代码：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas,'Courier New',Courier,mono,serif; width:936.53125px; overflow:hidden; padding-top:1px; line-height:26px; margin:18px 0px!important">
<div class="bar" style="position:relative; padding-left:45px">
<div class="tools" style="padding:3px 8px 10px 10px; font-size:9px; line-height:normal; font-family:Verdana,Geneva,Arial,Helvetica,sans-serif; color:silver; border-left-width:3px; border-left-style:solid; border-left-color:rgb(153,153,153)">
<strong>[java]</strong> <a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">view
 plain</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">copy</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">print</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="About" title="?" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">?</a>
<div style="position:absolute; left:638px; top:9469px; width:27px; height:15px; z-index:99">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="padding:0px; border:none; position:relative; color:rgb(92,92,92); margin:0px 0px 1px 45px!important">
<li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">class</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> KafkaSpout </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">implements</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> IRichSpout {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">static</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">final</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Log logger = LogFactory.getLog(KafkaSpout.</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">class</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">/**</span> </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">       *</span> </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">       */</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">static</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">final</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">long</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> serialVersionUID = -5569857211173547938L;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      SpoutOutputCollector collector;  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> ConsumerConnectorconsumer;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Stringtopic;  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> KafkaSpout(String topic) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.topic = topic;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> open(Map conf, TopologyContext context,  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 SpoutOutputCollector collector) {  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.collector = collector;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">static</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> ConsumerConfig createConsumerConfig() {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           Properties props = newProperties();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           props.put(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"zookeeper.connect"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"xx.xx.xx.xx:2181"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           props.put(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"group.id"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"0"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           props.put(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"zookeeper.session.timeout.ms"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"10000"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">//props.put("zookeeper.sync.time.ms", "200");</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">//props.put("auto.commit.interval.ms", "1000");</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">return</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> ConsumerConfig(props);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> close() {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">// TODOAuto-generated method stub</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> activate() {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.consumer = Consumer.createJavaConsumerConnector(createConsumerConfig());  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           Map&lt;String, Integer&gt; topickMap = newHashMap&lt;String, Integer&gt;();  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           topickMap.put(topic,<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Integer(</span><span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">1</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">));  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           Map&lt;String, List&lt;KafkaStream&lt;<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[],</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[]&gt;&gt;&gt;streamMap =consumer.createMessageStreams(topickMap);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           KafkaStream&lt;<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[],</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[]&gt;stream = streamMap.get(topic).get(</span><span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">0</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           ConsumerIterator&lt;<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[],</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">byte</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">[]&gt; it =stream.iterator();  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">while</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> (it.hasNext()) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 String value = newString(it.next().message());  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 System.out.println(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"(consumer)--&gt;"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> + value);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 collector.emit(<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Values(value), value);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> deactivate() {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">// TODOAuto-generated method stub</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">private</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">boolean</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> isComplete;  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> nextTuple() {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> ack(Object msgId) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">// TODOAuto-generated method stub</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> fail(Object msgId) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">// TODOAuto-generated method stub</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> declareOutputFields(OutputFieldsDeclarer declarer) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           declarer.declare(<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Fields(</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"KafkaSpout"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">));  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="annotation" style="margin:0px; padding:0px; border:none; color:rgb(100,100,100); background-color:inherit">@Override</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Map&lt;String, Object&gt; getComponentConfiguration() {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">// TODOAuto-generated method stub</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">return</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">null</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">;  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">}  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">class</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> FileBlots implementsIRichBolt{  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">       </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      OutputCollector collector;  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">       </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> prepare(Map stormConf, TopologyContext context,  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 OutputCollector collector) {  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.collector = collector;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> execute(Tuple input) {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           String line = input.getString(<span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">0</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">for</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">(String str : line.split(</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"\\s+"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">)){  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           List a = newArrayList();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           a.add(input);   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.collector.emit(a,newValues(str));  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.collector.ack(input);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> cleanup() {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> declareOutputFields(OutputFieldsDeclarer declarer) {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           declarer.declare(<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Fields(</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"words"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">));  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Map&lt;String, Object&gt; getComponentConfiguration() {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">// TODOAuto-generated method stub</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">return</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">null</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">;  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">}  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">class</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> WordsCounterBlots implementsIRichBolt{  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">       </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      OutputCollector collector;  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      Map&lt;String, Integer&gt; counter;  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">       </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> prepare(Map stormConf, TopologyContext context,  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 OutputCollector collector) {  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.collector = collector;  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.counter =</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> HashMap&lt;String, Integer&gt;();  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> execute(Tuple input) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           String word = input.getString(<span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">0</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           Integer integer = <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.counter.get(word);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">if</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">(integer !=</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">null</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">){  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 integer +=<span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">1</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">;  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.counter.put(word, integer);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           }<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">else</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">{  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.counter.put(word, </span><span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">1</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           System.out.println(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"execute"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           Jedis jedis = JedisUtils.getJedis();  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           jedis.incrBy(word, <span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">1</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           System.out.println(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"============================================="</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.collector.ack(input);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> cleanup() {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">for</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">(Entry&lt;String, Integer&gt; entry :</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">this</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">.counter.entrySet()){  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                      System.out.println(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"------:"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">+entry.getKey()+</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"=="</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">+entry.getValue());  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> declareOutputFields(OutputFieldsDeclarer declarer) {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Map&lt;String, Object&gt; getComponentConfiguration() {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="comment" style="margin:0px; padding:0px; border:none; color:rgb(204,204,204); background-color:inherit">// TODOAuto-generated method stub</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">return</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">null</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">;  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">}  </span></li></ol>
</div>
<br style="font-family:Arial; font-size:14px; line-height:26px">
<br style="font-family:Arial; font-size:14px; line-height:26px">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
Topology测试：</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas,'Courier New',Courier,mono,serif; width:936.53125px; overflow:hidden; padding-top:1px; line-height:26px; margin:18px 0px!important">
<div class="bar" style="position:relative; padding-left:45px">
<div class="tools" style="padding:3px 8px 10px 10px; font-size:9px; line-height:normal; font-family:Verdana,Geneva,Arial,Helvetica,sans-serif; color:silver; border-left-width:3px; border-left-style:solid; border-left-color:rgb(153,153,153)">
<strong>[java]</strong> <a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">view
 plain</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">copy</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="PrintSource" title="print" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">print</a><a target="_blank" href="http://blog.csdn.net/zxcvg/article/details/18600335/#" rel="nofollow" class="About" title="?" style="color:rgb(204,204,204); text-decoration:none; border:none; padding:0px; margin:0px 10px 0px 0px; font-size:9px; background-color:inherit">?</a>
<div style="position:absolute; left:638px; top:12763px; width:27px; height:15px; z-index:99">
</div>
</div>
</div>
<ol start="1" class="dp-j" style="padding:0px; border:none; position:relative; color:rgb(92,92,92); margin:0px 0px 1px 45px!important">
<li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit"><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">class</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> KafkaTopology {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">   </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">public</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">static</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> </span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">void</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> main(String[] args) {  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">try</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 JedisUtils.initialPool(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"xx.xx.xx.xx"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">, </span><span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">6379</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           } <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">catch</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> (Exception e) {  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 e.printStackTrace();  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           }  </span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">            </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           TopologyBuilder builder = newTopologyBuilder();           builder.setSpout(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"kafka"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> KafkaSpout(</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"kafka"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">));  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           builder.setBolt(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"file-blots"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> FileBlots()).shuffleGrouping(</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"kafka"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           builder.setBolt(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"words-counter"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> WordsCounterBlots(),</span><span class="number" style="margin:0px; padding:0px; border:none; color:rgb(192,0,0); background-color:inherit">2</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">).fieldsGrouping(</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"file-blots"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">,</span><span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Fields(</span><span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"words"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">));  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           Config config = <span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">new</span><span style="margin:0px; padding:0px; border:none; background-color:inherit"> Config();  </span></span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">           config.setDebug(<span class="keyword" style="margin:0px; padding:0px; border:none; color:rgb(102,204,255); font-weight:bold; background-color:inherit">true</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">);  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 LocalCluster local = newLocalCluster();  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">                 local.submitTopology(<span class="string" style="margin:0px; padding:0px; border:none; color:rgb(255,153,0); background-color:inherit">"counter"</span><span style="margin:0px; padding:0px; border:none; background-color:inherit">, config, builder.createTopology());  </span></span></li><li class="alt" style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">      }  </span></li><li style="margin:0px!important; padding:0px 3px 0px 10px!important; border-style:none none none solid; border-left-width:3px; border-left-color:rgb(153,153,153); list-style:decimal-leading-zero outside; color:rgb(238,238,238); line-height:18px">
<span style="margin:0px; padding:0px; border:none; color:rgb(255,255,255); background-color:inherit">}  </span></li></ol>
</div>
<br style="font-family:Arial; font-size:14px; line-height:26px">
<br style="font-family:Arial; font-size:14px; line-height:26px">
<p style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
</p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
 </p>
<p align="left" style="margin-top:0px; margin-bottom:0px; padding-top:0px; padding-bottom:0px; font-family:Arial; font-size:14px; line-height:26px">
至此flume + kafka+storm的整合就写完了。注意 这个是 初始学习阶段做的测试 不可正式用于线上环境，在写本文之时 已经离测试过去了一段时间 所以可能会有些错误 请见谅。</p>
            </div>
                </div>
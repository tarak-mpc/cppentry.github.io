---
layout:     post
title:      flume如何向kafka写数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>介绍整合flume和kafka的文章很多。</p>
<p>最近正好学习并动手实践了一回，记下自己的一些心得。</p>
<p><br></p>
<h3>Flume简介<br></h3>
<p>flume是 Cloudera 开发的实时日志收集系统。</p>
<p>她包含三个重要组件，就是source(采集读入数据)， sink(输出数据）和channel（source和sink之间的buffer)。</p>
<p>值得一提的是， flume支持一个source采集数据，并通过多个channel发送至多个sink，配置样例见下文链接。<br></p>
<p></p>
<p>个人感觉开发者是希望分布式的实时日志采集变得更加简单，用户只需通过配置甚至不需要开发代码，就可以实现日志采集。</p>
<p>同时，还有很重要的一点，Flume比较适合分布式扩展，这对成长迅速的大型分布式系统比较有利。</p>
<p>大家可以参考如下一片文章进一步学习Flume:    <a href="http://my.oschina.net/leejun2005/blog/288136" rel="nofollow">
Flume NG 简介及配置实战</a><br></p>
<p><br></p>
<h3><strong>整合Flume和Kafka:</strong></h3>
<p>总的来说需要如下几步：</p>
<blockquote>
<p>1&gt; 安装好Flume</p>
<p>2&gt; 下载第三方的或自己写flume kafka sink, 编译成jar包，放入flume/lib/目录下<br></p>
<p>3&gt; 配置Flume安装目录下的conf / <strong>XXX</strong>.properties （就是定义自己的flume source, channel 和 sink），注意 sink的类型就是&lt;2&gt;中自己定义的sink类全名（package.class)<br></p>
<p>4&gt; 下载kafka安装包，将 kafka/lib/下的所有jar包都copy至 flume/lib/目录下</p>
<p>5&gt; 启动flume, 验证是否成功 （可能碰到的问题包括scala版本问题，kafka jar包的版本问题和sfj多版本冲突等）<br></p>
</blockquote>
<p><br></p>
<p>本周内会继续将如上内容补充完整，争取能给刚上手的同学更多帮助。</p>
<p><br></p>
<p><span style="color:#3333FF;">IT人的微信自媒体--- 杰天空， 走在寻找创意的路上<br>
发掘创意，点缀生活，品味人生。<br>
请搜索微信订阅号：<span style="font-size:18px;"><strong> jksy_studio </strong> </span>，或者微信扫描下图二维码添加关注</span><br><img src="https://img-blog.csdn.net/20140816002823151?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvanNreV9zdHVkaW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><span style="color:#3333FF;">杰天空静候您的光临。</span><br></p>
            </div>
                </div>
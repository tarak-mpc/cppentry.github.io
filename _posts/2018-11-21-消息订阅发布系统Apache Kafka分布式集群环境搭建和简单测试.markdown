---
layout:     post
title:      消息订阅发布系统Apache Kafka分布式集群环境搭建和简单测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'宋体', SimSun;color:rgb(0,0,0);line-height:21px;font-weight:700;font-size:18px;">一、什么是kafka?</span><span style="font-family:'宋体', SimSun;font-size:16px;"><span style="color:rgb(0,0,0);line-height:21px;font-weight:700;"></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:rgb(0,0,0);line-height:21px;">　　kafka是LinkedIn开发并开源的一个分布式MQ系统，现在是Apache的一个孵化项目。在它的主页描述kafka为一个高吞吐量的分布式（能将消息分散到不同的节点上）MQ。Kafka仅仅由7000行Scala编写，据了解，Kafka每秒可以生产约25万消息（50 MB），每秒处理55万消息（110 MB）</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'宋体', SimSun;font-size:16px;color:rgb(0,0,0);line-height:21px;"><br></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="color:rgb(0,0,0);font-family:'宋体', SimSun;line-height:21px;font-weight:700;font-size:18px;">二、kafka的官方网站在哪里？</span><span style="color:rgb(0,0,0);font-family:'宋体', SimSun;font-size:16px;line-height:21px;"><span style="font-weight:700;"></span><br>
　　<a href="http://kafka.apache.org/" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://kafka.apache.org/</a><br><br><span style="font-size:18px;font-weight:700;">三、在哪里下载？需要哪些组件的支持？</span><br>
　　kafka2.9.2在下面的地址可以下载：<br>
　　<a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.1.1/kafka_2.9.2-0.8.1.1.tgz" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.1.1/kafka_2.9.2-0.8.1.1.tgz</a></span><span style="font-family:'宋体', SimSun;color:rgb(0,0,0);line-height:21px;font-size:16px;"><span style="font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><br></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><span style="font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:18px;font-weight:700;">四、如何安装？</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:Tahoma, 'Microsoft Yahei', Simsun;color:rgb(0,0,0);line-height:21px;"><span style="font-family:'宋体', SimSun;"></span><span style="font-size:4px;"><span>    <br></span></span><span style="font-family:'楷体', '楷体_GB2312', SimKai;font-size:16px;">1、本机环境描述</span></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'宋体', SimSun;color:rgb(0,0,0);line-height:21px;">    3台主机：操作系统为CentOS6.4，zookeeper版本为3.4.3，jdk版本为1.6(可参考前面的文章)<br></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'宋体', SimSun;color:rgb(0,0,0);line-height:21px;">        ip和hostname分别为192.168.41.100/master，192.168.41.101/slave1，192.168.41.102/master<br></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'宋体', SimSun;color:rgb(0,0,0);line-height:21px;"><br></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-size:16px;font-family:'楷体', '楷体_GB2312', SimKai;color:rgb(0,0,0);line-height:21px;">2.解压kafka_2.9.2-0.8.1.1.tgz，本文中解压到/home/hadoop目录下</span></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_592741" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;"># tar -zxvf -C /home/hadoop/ kafka_2.9.2-0.8.1.1.tgz</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;font-size:16px;line-height:21px;">3、修改config/server.properties配置文件。修改部分如下：</span><br></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_76572" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
<div class="line number2 index1 alt1" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
2</div>
<div class="line number3 index2 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
3</div>
<div class="line number4 index3 alt1" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
4</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash plain" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;background:none !important;">broker.</code><code class="bash functions" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(255,20,147) !important;background:none !important;">id</code><code class="bash plain" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;background:none !important;">=0</code></div>
<div class="line number2 index1 alt1" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash plain" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;background:none !important;">host.name=master</code></div>
<div class="line number3 index2 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash plain" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;background:none !important;">advertised.host.name=master</code></div>
<div class="line number4 index3 alt1" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash plain" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;background:none !important;">zookeeper.connect=master:2181,slave1:2181,slave2:2181</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"></span>其中</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
broker.id为整数，建议根据ip区分,这里我是使用zookeeper中的id来设置</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
host.name和advertised.host.name为本机的hostname</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
zookeeper.connect为连接的机器和端口</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
修改后如下：</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M00/49/1A/wKiom1QO8rKDDhKoAABXjFQd4-k786.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M00/49/1A/wKiom1QO8rKDDhKoAABXjFQd4-k786.jpg" title="1.png" alt="wKiom1QO8rKDDhKoAABXjFQd4-k786.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M01/49/1B/wKioL1QO8r-D7NuvAAEdY0yUlag626.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M01/49/1B/wKioL1QO8r-D7NuvAAEdY0yUlag626.jpg" title="2.png" alt="wKioL1QO8r-D7NuvAAEdY0yUlag626.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M01/49/1A/wKiom1QO8rOxUwdZAAHBpB1BlL0143.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M01/49/1A/wKiom1QO8rOxUwdZAAHBpB1BlL0143.jpg" title="3.png" alt="wKiom1QO8rOxUwdZAAHBpB1BlL0143.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;">4.针对bin/<span style="color:rgb(51,51,51);font-family:'微软雅黑', Verdana, sans-serif, '宋体';font-size:13px;line-height:18.75px;">kafka-run-class.sh</span>移除相关参数：</span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'宋体', SimSun;">移除代码为</span></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_695006" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash plain" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;background:none !important;">-XX:+UseCompressedOops</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'宋体', SimSun;">不然会出现</span></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_728001" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash plain" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;background:none !important;">Unrecognized VM option </code><code class="bash string" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:#0000FF !important;background:none !important;">'UseCompressedOops'</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
修改后如下：</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M00/49/1B/wKioL1QO8qvCYLubAAC8A321gj8823.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M00/49/1B/wKioL1QO8qvCYLubAAC8A321gj8823.jpg" title="4.png" alt="wKioL1QO8qvCYLubAAC8A321gj8823.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;line-height:21px;">5.把kafka文件夹拷贝到其他节点，并修改</span><span style="font-family:'楷体', '楷体_GB2312', SimKai;line-height:21px;">server.properties相关内容</span></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_973974" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;">#  scp -r /home/hadoop/kafka_2.9.2-0.8.1.1/ slave1:/home/hadoop/kafka_2.9.2-0.8.1.1/</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;line-height:21px;"></span><br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;">6.启动zookeeper</span><br></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_852438" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;"># zkServer.sh start</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
启动后可以用以下命令在每台机器上查看状态：</p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_621432" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;"># zkServer.sh status</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M02/49/1B/wKioL1QO8z6SDSbNAACvDNvEJ0c011.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M02/49/1B/wKioL1QO8z6SDSbNAACvDNvEJ0c011.jpg" title="5.png" alt="wKioL1QO8z6SDSbNAACvDNvEJ0c011.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;">7.启动各个节点的kafka</span></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_607378" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;"># kafka-server-start.sh /home/hadoop/kafka_2.9.2-0.8.1.1/config/server.properties &amp;</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
部分截图：</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M02/49/1A/wKiom1QO9BfDRZG8AAMI33q1a8c881.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M02/49/1A/wKiom1QO9BfDRZG8AAMI33q1a8c881.jpg" title="6.png" alt="wKiom1QO9BfDRZG8AAMI33q1a8c881.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:18px;line-height:21px;"><strong>四、简单安装</strong></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;">1.在master上创建一个test主题</span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;"><span style="font-family:'宋体', SimSun;">命令如下：</span></span></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_162027" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;"># kafka-topics.sh --create --topic test --replication-factor 3 --partitions 2 --zookeeper master:2181</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;"><span style="font-family:'宋体', SimSun;"></span></span><a href="http://s3.51cto.com/wyfs02/M00/49/1A/wKiom1QO9MWx7Y6HAAC3j0VrdJA252.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M00/49/1A/wKiom1QO9MWx7Y6HAAC3j0VrdJA252.jpg" title="7.png" alt="wKiom1QO9MWx7Y6HAAC3j0VrdJA252.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<br></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;">2.在master上查看test主题</span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;"><span style="font-family:'宋体', SimSun;">命令如下：</span></span></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_348439" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;"># kafka-topics.sh --list --zookeeper master:2181</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;"></span>如图：</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;"><a href="http://s3.51cto.com/wyfs02/M01/49/1A/wKiom1QO9S-iG_IUAAB3-mrgr30317.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M01/49/1A/wKiom1QO9S-iG_IUAAB3-mrgr30317.jpg" title="8.png" alt="wKiom1QO9S-iG_IUAAB3-mrgr30317.jpg" style="vertical-align:top;border:none;"></a></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;"><br></span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;">3)在slave1上发送消息至kafka（slave1模拟producer），发送消息“hello”,</span><span style="font-family:'楷体', '楷体_GB2312', SimKai;">在slave2上开启一个消费者（slave2模拟consumer），可以看到刚才发送的消息</span></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'宋体', SimSun;">slave1主机上的命令为：</span></p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_317677" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;"># kafka-console-producer.sh --broker-list master:9092 --sync --topic test</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<span style="font-family:'楷体', '楷体_GB2312', SimKai;"></span>出现如下界面后，键入hello</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M02/49/1B/wKioL1QO9kaxTMKnAAEF9Q3_yBI702.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M02/49/1B/wKioL1QO9kaxTMKnAAEF9Q3_yBI702.jpg" title="9.png" alt="wKioL1QO9kaxTMKnAAEF9Q3_yBI702.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
键入hello后：</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M02/49/1A/wKiom1QO9ljRwwGzAAEPTr3xscE724.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M02/49/1A/wKiom1QO9ljRwwGzAAEPTr3xscE724.jpg" title="10.png" alt="wKiom1QO9ljRwwGzAAEPTr3xscE724.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
slave2主机上的命令为：</p>
<div style="color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<div id="highlighter_735174" class="syntaxhighlighter bash" style="border:0px !important;line-height:1.1em !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;">
<table border="0" cellpadding="0" cellspacing="0" style="font-size:12px;border-collapse:collapse;"><tbody style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;"><tr style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;min-height: !important;background:none !important;"><td class="gutter" style="font-size:1em !important;border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;color:rgb(175,175,175) !important;background:none !important;">
<div class="line number1 index0 alt2" style="border-width:0px 3px 0px 0px !important;border-right-style:solid !important;border-right-color:rgb(108,226,108) !important;line-height:1.1em !important;overflow:visible !important;text-align:right !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
1</div>
</td>
<td class="code" style="width:692px;font-size:1em !important;line-height:1.1em !important;border:0px !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;background:none !important;">
<div style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;background:none !important;">
<div class="line number1 index0 alt2" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-size:1em !important;min-height: !important;">
<code class="bash comments" style="border:0px !important;line-height:1.1em !important;overflow:visible !important;vertical-align:baseline !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;font-size:1em !important;min-height: !important;color:rgb(0,130,0) !important;background:none !important;"># kafka-console-consumer.sh --zookeeper master:2181 --topic test --from-beginning</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
结果如下：</p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M00/49/1A/wKiom1QO9p3jYxAMAADGg8WbYnU114.jpg" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;"><img src="http://s3.51cto.com/wyfs02/M00/49/1A/wKiom1QO9p3jYxAMAADGg8WbYnU114.jpg" title="11.png" alt="wKiom1QO9p3jYxAMAADGg8WbYnU114.jpg" style="vertical-align:top;border:none;"></a></p>
<p style="clear:both;overflow:hidden;color:rgb(85,85,85);font-family:'宋体', 'Arial Narrow', arial, serif;font-size:14px;line-height:28px;">
本文出自 “<a href="http://xlows.blog.51cto.com/" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;">Xlows</a>” 博客，请务必保留此出处<a href="http://xlows.blog.51cto.com/5380484/1550335" rel="nofollow" style="color:rgb(75,0,2);text-decoration:none;">http://xlows.blog.51cto.com/5380484/1550335</a></p>
            </div>
                </div>
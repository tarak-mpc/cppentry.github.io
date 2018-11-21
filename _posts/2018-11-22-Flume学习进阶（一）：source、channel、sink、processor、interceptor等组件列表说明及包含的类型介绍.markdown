---
layout:     post
title:      Flume学习进阶（一）：source、channel、sink、processor、interceptor等组件列表说明及包含的类型介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>最近在做flume日志收集系统相关项目，收集flume相关的知识，以便查阅、分享。<br></h2>
<h2>转载出处：http://www.cnblogs.com/tq03/p/5151954.html<br></h2>
<h2>一、flume的基本架构图：</h2>
<p>下面这个图基本说明了flume的作用，以及flume中的基本组件：source、channel、sink。</p>
<p><strong>Source</strong>:完成对日志数据的收集，分成transtion 和 event 打入到channel之中。   <br><strong>Channel</strong>:主要提供一个队列的功能，对source提供中的数据进行简单的缓存。   <br><strong>Sink</strong>:取出Channel中的数据，进行相应的存储文件系统，数据库，或者提交到远程服务器。</p>
<p><a href="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174135640-1998721442.png" rel="nofollow"><img title="image" alt="image" src="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174136031-1609513011.png" border="0" height="187" width="561" style="border-top:0px;border-right:0px;border-bottom:0px;border-left:0px;display:inline;"></a></p>
<p> </p>
<p><a href="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122175833656-1356149016.png" rel="nofollow"><img title="image" alt="image" src="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122175834156-382087870.png" border="0" height="203" width="924" style="border-top:0px;border-right:0px;border-bottom:0px;border-left:0px;display:inline;"></a></p>
<h2>二、source列表：</h2>
<p><a href="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174136468-1351341961.png" rel="nofollow"><img title="image" alt="image" src="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174137078-1587199479.png" border="0" height="454" width="875" style="border-top:0px;border-right:0px;border-bottom:0px;border-left:0px;display:inline;"></a></p>
<p>简要说明：</p>
<p>1、Avro Source：支持Avro协议（实际上是Avro RPC），内置支持</p>
<p>2、Thrift Source：支持Thrift协议，内置支持</p>
<p>3、Exec Source | 基于Unix的command在标准输出上生产数据</p>
<p>4、JMS Source：从JMS系统（消息、主题）中读取数据，ActiveMQ已经测试过</p>
<p>5、Spooling Directory Source：监控指定目录内数据变更</p>
<p>6、Twitter 1% firehose Source：通过API持续下载Twitter数据，试验性质</p>
<p>7、Netcat Source：监控某个端口，将流经端口的每一个文本行数据作为Event输入</p>
<p>8、Sequence Generator Source：序列生成器数据源，生产序列数据</p>
<p>9、Syslog Sources：读取syslog数据，产生Event，支持UDP和TCP两种协议</p>
<p>10、HTTP Source：基于HTTP POST或GET方式的数据源，支持JSON、BLOB表示形式</p>
<p>11、Legacy Sources：兼容老的Flume OG中Source（0.9.x版本）</p>
<h2>三、channel列表：</h2>
<p><a href="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174137718-256416348.png" rel="nofollow"><img title="image" alt="image" src="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174138390-1389820285.png" border="0" height="218" width="875" style="border-top:0px;border-right:0px;border-bottom:0px;border-left:0px;display:inline;"></a></p>
<h2>四、sink列表：</h2>
<p><a href="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174138859-1641435892.png" rel="nofollow"><img title="image" alt="image" src="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174139343-138702809.png" border="0" height="333" width="873" style="border-top:0px;border-right:0px;border-bottom:0px;border-left:0px;display:inline;"></a></p>
<h2>五、processor列表：</h2>
<p><a href="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174140328-466669569.png" rel="nofollow"><img title="image" alt="image" src="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174140797-843842285.png" border="0" height="253" width="872" style="border-top:0px;border-right:0px;border-bottom:0px;border-left:0px;display:inline;"></a></p>
<p>六、interceptor列表：</p>
<p><a href="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174141484-864690469.png" rel="nofollow"><img title="image" alt="image" src="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174142015-1733924705.png" border="0" height="203" width="873" style="border-top:0px;border-right:0px;border-bottom:0px;border-left:0px;display:inline;"></a></p>
<h2>七、其他组件列表：</h2>
<p><a href="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174142656-1124200363.png" rel="nofollow"><img title="image" alt="image" src="http://images2015.cnblogs.com/blog/587689/201601/587689-20160122174143625-1060031898.png" border="0" height="169" width="867" style="border-top:0px;border-right:0px;border-bottom:0px;border-left:0px;display:inline;"></a></p>
<br>            </div>
                </div>
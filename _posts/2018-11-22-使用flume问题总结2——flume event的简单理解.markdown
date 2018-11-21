---
layout:     post
title:      使用flume问题总结2——flume event的简单理解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/anjing900812/article/details/48997801				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3><span style="font-size:18px;">Flume Event</span></h3>
<ol><li><span style="font-size:12px;">event是flume中处理消息的基本单元，由零个或者多个header和正文body组成。</span></li><li><span style="font-size:12px;">Header 是 key/value 形式的，可以用来制造路由决策或携带其他结构化信息(如事件的时间戳或事件来源的服务器主机名)。你可以把它想象成和 HTTP 头一样提供相同的功能——通过该方法来传输正文之外的额外信息。<br></span></li><li><span style="font-size:12px;"><span style="font-family:'Book Antiqua';font-size:10pt;">Body<span style="font-family:'宋体';font-size:10pt;">是一个字节数组，包含了实际的内容。</span></span></span></li><li><span style="font-size:12px;"><span style="font-family:'Book Antiqua';font-size:10pt;"><span style="font-family:'宋体';font-size:10pt;">flume提供的不同source会给其生成的event添加不同的header，例如：</span></span></span></li><li><span style="font-size:12px;"><span style="font-family:'Book Antiqua';font-size:10pt;"><span style="font-family:'宋体';font-size:10pt;"></span></span></span><pre><code class="language-html">Objavro.codecnullavro.schema?{"type":"record","name":"Event","fields":[{"name":"headers","type":{"type":"map","values":"string"}},{"name":"body","type":"bytes"}]} ?u 1?荍?只3L??&lt;span style="color:#ff0000;"&gt;Severity&lt;/span&gt;0&amp;&lt;span style="color:#ff0000;"&gt;flume.syslog.status&lt;/span&gt;Invalid&lt;span style="color:#ff0000;"&gt;Facility&lt;/span&gt;0&lt;span style="color:#ff0000;"&gt;domain_name&lt;/span&gt;sina PYM:sina YDIP:81.168.44.33 QQLX:NS IPGS:3Severity0&amp;flume.syslog.statusInvalidFacility0domain_namesina PYM:sina YDIP:111.168.44.33 QQLX:A IPGS:2?u 1?荍?只3L?</code></pre>这是我测试时使用SyslogTCPSource，sink为FillRollSink，输出至文件中的event信息。有两个event，固定的header有：<span style="color:rgb(255,0,0);font-size:13.3333330154419px;font-family:'宋体';background-color:rgb(240,240,240);">Severity、</span><span style="color:rgb(255,0,0);font-size:13.3333330154419px;font-family:'宋体';background-color:rgb(240,240,240);">flume.syslog.status、</span><span style="color:rgb(255,0,0);font-size:13.3333330154419px;font-family:'宋体';background-color:rgb(240,240,240);">Facility，</span><span style="font-size:13.3333330154419px;font-family:'宋体';background-color:rgb(240,240,240);">最后一个</span><span style="color:rgb(255,0,0);font-size:13.3333330154419px;font-family:'宋体';background-color:rgb(240,240,240);">domain_name</span><span style="font-size:13.3333330154419px;font-family:'宋体';background-color:rgb(240,240,240);">是我自己通过拦截器添加的。</span></li><li><span style="font-family:'宋体';"><span style="font-size:13.3333330154419px;background-color:rgb(240,240,240);">由于测试程序中只是实现了一个socket的客户端，发送字符串给flume，并没有严格按照syslog的标准格式构造消息（可参考RFC3164），因此flume.syslog.status的值为Invalid.</span></span></li><li>
<div><span style="font-family:'宋体';"><span style="font-size:13.3333330154419px;">flume允许用户修改event，添加header，从而通过消息的内容对日志进行路由。具体需要使用的机制有：拦截器interceptor和选择器selector；</span></span></div>
</li><li>
<div><span style="font-family:'宋体';"><span style="font-size:13.3333330154419px;">关于拦截器和选择器的原理介绍以及简单实例可参考官方用户手册以及《Apache Flume: Distributed Log Collection for Hadoop》（该书有中文版的译本，由冯佳冬翻译，看完之后受益匪浅）</span></span></div>
</li></ol><h6><span style="font-family:'宋体';"><span style="font-size:13.3333330154419px;">参考文献：</span></span></h6>
<h6>
</h6><ol><li><span style="font-family:'宋体';font-size:13.3333330154419px;">《Apache Flume: Distributed Log Collection for Hadoop》冯佳冬翻译</span></li><li><span style="font-size:13.3333330154419px;font-family:'宋体';">下载链接：</span><a href="http://download.csdn.net/detail/jiadong588/8124809#comment" rel="nofollow" style="font-size:13.3333330154419px;font-family:'宋体';">http://download.csdn.net/detail/jiadong588/8124809#comment</a></li></ol>
<div></div>
<p></p>
            </div>
                </div>
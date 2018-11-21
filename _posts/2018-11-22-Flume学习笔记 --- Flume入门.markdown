---
layout:     post
title:      Flume学习笔记 --- Flume入门
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/54378275				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">        Flume是一个分布式、可靠、和高可用的海量日志聚合的系统</span><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">，支持在系统中定制各类数据发送方，用于收集数据</span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。</span><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">一个独立的Flume进程称之为Agent,包含组件Source、Channel、Sink
 Source。</span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><span style="color:rgb(255,0,0);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">Flume 的内部实现  </span><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><span style="color:rgb(255,0,0);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><img src="https://img-blog.csdn.net/20170112151753532?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><span style="color:rgb(255,0,0);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><span style="color:rgb(255,0,0);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><span style="color:rgb(255,0,0);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"></span></span></p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><span style="color:rgb(255,0,0);">Event：</span></span>Event是Flume数据传输的基本单元。Flume以Event的形式将数据从源头传送到最终目的。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><span style="color:rgb(255,0,0);">Source：</span></span>Source负责接收events或通过特殊机制产生events，并将events批量的放到一个或多个Channels。Flume支持文件、消息流等数据源，并在Source部件中将接收到的数据转换为一个Event。例如Flume支持监听文件目录（spooling directory
 source），当监听的目录下新到一个文件，Flume就会将其作为数据源通过Source转换为Event实时的传输走。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><span style="color:rgb(255,0,0);">Channel：</span></span>Channel位于Source和Sink之间，用于缓存进来的events，当Sink成功地将events发送到下一跳的channel或最终目的，events从Channel移除。目前Flume支持3种channel memory channel：消息放在内存中，提供高吞吐，但不提供可靠性；可能丢失数据；
 file channel：对数据持久化；但是配置较为麻烦，需要配置数据目录和checkpoint目录；不同的file channel均需要配置一个checkpoint 目录； jdbc channel：内置的derby数据库，对event进行了持久化，提供高可靠性；未来取代同样具有持久特性的file channel</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><span style="color:rgb(255,0,0);">Sink：</span></span>Sink负责将events传输到下一跳或最终目的。Sink支持将数据写入到离线存储如HDFS、消息系统如Kafka等。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
<span><span style="color:rgb(255,0,0);">Interceptor：</span></span>用于Source的一组拦截器，按照预设的顺序在必要地方对events进行过滤和自定义的处理逻辑实现。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Channel Selector允许Source基于预设的规则，从所有Channel中，选择一个或多个Channel。例如根据话单中的漫游字段，可以将原始话单放到不同的Channel，这样Sink就可以将数据送到不同的目标系统中。</p>
<p style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;">
Channel Selector支持两种选择器： 复制Replicating: 一个event被复制到多个channel； 复用Multiplexing: event被路由到特定的channel，即非复制模式。</p>
<br>            </div>
                </div>
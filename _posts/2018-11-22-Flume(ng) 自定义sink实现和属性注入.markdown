---
layout:     post
title:      Flume(ng) 自定义sink实现和属性注入
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(54,46,43);font-family:Arial;font-size:14px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">问题导读：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="color:rgb(255,0,0);">1.如何实现flume端自定一个sink，来按照我们的规则来保存日志？</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="color:rgb(255,0,0);">2.想从flume的配置文件中获取rootPath的值，该如何配置？</span></span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><img id="aimg_OMVU0" class="zoom" src="http://www.aboutyun.com/static/image/hrline/4.gif" border="0" alt="" width="500" height="35" style="border:none;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">最近需要利用flume来做收集远端日志，所以学习一些flume最基本的用法。这里仅作记录。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">远端日志收集的整体思路是远端自定义实现log4j的appender把消息发送到flume端，flume端自定义实现一个sink来按照我们的规则保存日志。</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">自定义Sink代码：</span><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"></p>
<div class="blockcode" style="font-size:14px;overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">
<div id="code_jXg">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
public class LocalFileLogSink extends AbstractSink implements Configurable {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
     private static final Logger logger = LoggerFactory<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
              . getLogger(LocalFileLogSink .class );<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
            private static final String PROP_KEY_ROOTPATH = "rootPath";<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      private String rootPath;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
     @Override<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
     public void configure(Context context) {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
          String rootPath = context.getString(PROP_KEY_ROOTPATH );<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
          setRootPath(rootPath);<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
     }<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
           <br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
          @Override<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
          public Status process() throws EventDeliveryException {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
           logger .debug("Do process" );<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
       ｝<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
}</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<span style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">实现Configurable接口，即可在初始化时，通过configure方法从context中获取配置的参数的值。这里，我们是想从flume的配置文件中获取rootPath的值，也就是日志保存的根路径。在flume-conf.properties中配置如下：</span><br style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><div class="blockcode" style="font-size:14px;overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">
<div id="code_S82">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent.sinks = loggerSink<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
agent.sinks.loggerSink.rootPath = ./logs</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">loggerSink是自定义sink的名称，我们取值时的key，只需要loggerSink后面的部分即可，即这里的rootPath。</span><br style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><span style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">实际业务逻辑的执行，是通过继承复写AbstractSink中的process方法实现。从基类的getChannel方法中获取信道，从中取出Event处理即可。</span><br style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><br style="font-size:14px;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;"><div class="blockcode" style="font-size:14px;overflow:hidden;background-color:rgb(247,247,247);color:rgb(102,102,102);border:1px solid rgb(204,204,204);font-family:Tahoma, 'Microsoft Yahei', Simsun;line-height:21px;">
<div id="code_tHl">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
Channel ch = getChannel();<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
           Transaction txn = ch.getTransaction();<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
         txn.begin();<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
          try {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
              logger .debug("Get event." );<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
             Event event = ch.take();<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
             txn.commit();<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
             status = Status. READY ;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
             return status;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
                   ｝finally {<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
             Log. info( "trx close.");<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
             txn.close();<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
         }</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
            </div>
                </div>
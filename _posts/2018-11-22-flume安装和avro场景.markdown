---
layout:     post
title:      flume安装和avro场景
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="background:#FFFFFF;"><span style="color:#000000;">flume</span><span style="font-family:'宋体';"><span style="color:#000000;">安装</span></span></p>
<p style="background:#FFFFFF;"><span style="color:#000000;"><span style="font-family:'宋体';">官网：</span></span><span style="color:#000000;"><br>
  http://flume.apache.org/<br></span><span style="color:#000000;"><span style="font-family:'宋体';">安装包下载：</span></span><span style="color:#000000;"><br></span><span style="color:#000000;"><span style="font-family:'宋体';">　</span></span><span style="color:#000000;">http://www.apache.org/dyn/closer.cgi/flume/1.5.0/apache-flume-1.5.0-bin.tar.gz<br></span><span style="color:#000000;"><span style="font-family:'宋体';">解压：</span></span><span style="color:#000000;"><br>
  tar xzvf apache-flume-1.5.0-bin.tar.gz<br>
      </span>*<span style="color:#000000;"><span style="font-family:'宋体';">注</span></span><span style="color:#000000;">:</span><span style="color:#000000;"><span style="font-family:'宋体';">本猿解压的目录是</span></span><span style="color:#000000;">apche/flume/<br><br></span><span style="color:#000000;"><span style="font-family:'宋体';">配置</span></span><span style="color:#000000;">java<br></span><span style="color:#000000;"><span style="font-family:'宋体';">在文件</span></span><span style="color:#000000;">conf/flume-env.sh</span><span style="color:#000000;"><span style="font-family:'宋体';">中设置</span></span><span style="color:#000000;">java</span><span style="color:#000000;"><span style="font-family:'宋体';">环境变量</span></span><span style="color:#000000;"><br>
JAVA_HOME=/usr/lib/jvm/java-7-oracle</span></p>
<p style="background:#FFFFFF;"><span style="color:#000000;">      </span><span style="color:#000000;">*</span><span style="color:#000000;"><span style="font-family:'宋体';">注</span></span><span style="color:#000000;">:</span><span style="color:#000000;"><span style="font-family:'宋体';">当然需要</span></span><span style="color:#000000;">java</span><span style="color:#000000;"><span style="font-family:'宋体';">安装在这个目录</span></span><span style="color:#000000;"><br><br></span><span style="color:#000000;"><span style="font-family:'宋体';">验证是否安装成功</span></span><span style="color:#000000;"><br>
flume-1.5.0-bin/bin/flume-ng version<br><br></span><span style="color:#000000;"><span style="font-family:'宋体';">添加环境变量</span></span><span style="color:#000000;">(</span><span style="color:#000000;"><span style="font-family:'宋体';">方便使用</span></span><span style="color:#000000;">)<br>
export FLUME_HOME=/soft/apache/flume/apache-flume-1.5.0-bin<br>
export PATH=$PATH:$FLUME_HOME/bin<br>
      </span>*<span style="color:#000000;"><span style="font-family:'宋体';">注</span></span><span style="color:#000000;">:</span><span style="color:#000000;"><span style="font-family:'宋体';">使用</span></span><span style="color:#000000;">vi ~/.bashrc  </span><span style="color:#000000;"><span style="font-family:'宋体';">将上面两行添加到最后</span></span><span style="color:#000000;"><br>
      </span>*<span style="color:#000000;"><span style="font-family:'宋体';">注</span></span><span style="color:#000000;">:</span><span style="color:#000000;">. ~/.bashrc</span><span style="color:#000000;"><span style="font-family:'宋体';">或者</span></span><span style="color:#000000;">source
 ~/.bashrc</span><span style="font-family:'宋体';"><span style="color:#000000;">理解生效</span></span></p>
<p style="background:#FFFFFF;"><span style="color:#000000;"> </span></p>
<p style="background:#FFFFFF;"><span style="color:#000000;">***************************************************************************************************************</span></p>
<p style="background:#FFFFFF;"><span style="color:#000000;"><span style="font-family:'宋体';">Avro案例</span></span><span style="color:#000000;">:</span></p>
<p style="background:#FFFFFF;"><span style="color:#000000;"><span style="font-family:'宋体';">环境</span></span><span style="color:#000000;">:<br></span><span style="color:#000000;"><span style="font-family:'宋体';">两台</span></span><span style="color:#000000;">pc</span><span style="color:#000000;"><span style="font-family:'宋体';">均安装</span></span><span style="color:#000000;">flume(</span><span style="color:#000000;"><span style="font-family:'宋体';">这里可以使用一台机器看效果，标注两台好理解一些</span></span><span style="color:#000000;">)<br></span><span style="color:#000000;"><span style="font-family:'宋体';">假定</span></span><span style="color:#000000;">ip</span><span style="color:#000000;"><span style="font-family:'宋体';">分别为</span></span><span style="color:#000000;">172.17.0.2</span><span style="color:#000000;"><span style="font-family:'宋体';">、</span></span><span style="color:#000000;">172.17.0.3<br>
1.</span><span style="color:#000000;"><span style="font-family:'宋体';">在</span></span><span style="color:#000000;">172.17.0.2</span><span style="color:#000000;"><span style="font-family:'宋体';">上添加</span></span><span style="color:#000000;">agent a1</span><span style="color:#000000;"><span style="font-family:'宋体';">配置文件</span></span><span style="color:#000000;">flume-1.5.0-bin/conf/avro.conf<br></span><span style="color:#000000;"><span style="font-family:'宋体';">内容：</span></span><span style="color:#000000;"><br>
      </span>a1.sources = r1<br>
      a1.sinks = k1<br>
      a1.channels = c1</p>
<p style="background:#FFFFFF;"><span style="color:#000000;"><br>
      </span>a1.sources.r1.bind = 172.17.0.2<br>
      a1.sources.r1.port = 4141<br><br>
      a1.sources.r1.type = avro<br>
      a1.sinks.k1.type = logger<br>
      a1.sources.r1.channels = c1<br>
      a1.sinks.k1.channel = c1<br><br>
      a1.channels.c1.type = memory<br><br><br>
2.<span style="color:#000000;"><span style="font-family:'宋体';">在</span></span><span style="color:#000000;">172.17.0.2</span><span style="color:#000000;"><span style="font-family:'宋体';">上启动</span></span><span style="color:#000000;">agent a1<br>
      </span>flume-ng agent -c . -f $FLUME_HOME/conf/avro.conf -Dflume.root.logger=INFO,console -n a1 <br><br>
3.<span style="color:#000000;"><span style="font-family:'宋体';">在</span></span><span style="color:#000000;">172.17.0.3</span><span style="color:#000000;"><span style="font-family:'宋体';">使用</span></span><span style="color:#000000;">avro-client</span><span style="color:#000000;"><span style="font-family:'宋体';">发送文件</span></span><span style="color:#000000;">(log.00</span><span style="color:#000000;"><span style="font-family:'宋体';">是自己随意构造的文件</span></span><span style="color:#000000;">)<br>
      </span>flume-ng avro-client -c . -H 172.17.0.2 -p 4141 -F log.00<br><br>
4.<span style="color:#000000;"><span style="font-family:'宋体';">可以在</span></span><span style="color:#000000;">172.17.0.2</span><span style="color:#000000;"><span style="font-family:'宋体';">的控制台看到</span></span><span style="color:#000000;">log.00</span><span style="color:#000000;"><span style="font-family:'宋体';">的内容</span></span></p>
            </div>
                </div>
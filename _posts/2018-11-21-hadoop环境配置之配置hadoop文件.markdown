---
layout:     post
title:      hadoop环境配置之配置hadoop文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<div><strong>下来进行Hadoop的安装和配置。<br></strong>
<div id="_mcePaste" style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>下载hadoop-0.20.2.tar.gz到/home/hadoop目录，并解压：<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>tar -vxzf hadoop-0.20.2.tar.gz     //解压hadoop到当前目录</strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>解压完后进入/home/hadoop/hadoop-0.20.2/conf目录进行配置。<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>cd /home/hadoop/hadoop-0.20.2/conf</strong></div>
<strong></strong></div>
<div>文件配置，conf：</div>
<div>1、masters文件        sist08</div>
<div>2、slaves 文件      sist10</div>
<div>3、core-site.xml中：</div>
<div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;?xml version="1.0"?&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;!-- Put site-specific property overrides in this file. --&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;configuration&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;property&gt;</strong></div>
<strong></strong></div>
<div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;name&gt;fs.default.name&lt;/name&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;value&gt;</strong>hdfs://192.168.130.63:9000 <strong>&lt;/value&gt;</strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong></strong>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;/property&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;property&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;value&gt;/home/hadoop/hadoop-0.20.2/tmpvalue&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;/property&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;/configuration&gt;</strong></div>
<strong>4、<strong>修改hadoop-env.sh配置文件如下：<br></strong></strong>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>export JAVA_HOME=/usr/java/jdk1.6.0——01</strong></div>
5修改hdfs-site.xml文件如下：<strong><br></strong>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>=====line 1 col 0 lines from top 1 ============================================</strong></div>
<strong><br></strong>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;?xml version="1.0"?&gt;</strong></div>
<strong><br></strong>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</strong></div>
<strong><br></strong>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;!-- Put site-specific property overrides in this file. --&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;configuration&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;property&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;name&gt;dfs.replication&lt;/name&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;value&gt;1&lt;/value&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;/property&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;/configuration&gt;</strong></div>
<strong><br></strong>6、<strong>修改mapred-site.xml文件如下：<br></strong>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;?xml version="1.0"?&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;!-- Put site-specific property overrides in this file. --&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;configuration&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;property&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;name&gt;mapred.job.tracker&lt;/name&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;value&gt;sist08:9001&lt;/value&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;/property&gt;</strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;property&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;name&gt;mapred.system.dir&lt;/name&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;value&gt;/tmp/hadoop/mapred/system&lt;/value&gt;<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;/property&gt;<br></strong></div>
</div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>&lt;/configuration&gt;</strong></div>
<strong></strong>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>配置文件修改完毕后格式化Namenode(运行Hadoop之前必须先进行格式化)，进入/home/hadoop/hadoop-0.20.2/目录，命令如下：<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>./bin/hadoop namenode -format<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>格式化完毕后就可以运行Hadoop了，命令如下:<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>./bin/start-all.sh      //在/home/hadoop/hadoop-0.20.2/目录下运行<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>如果要停止运行如下命令：<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>./bin/stop-all.sh       //在/home/hadoop/hadoop-0.20.2/目录下运行<br></strong></div>
<div style="border-right-width:0px;background-color:transparent;border-top-width:0px;border-bottom-width:0px;font-size:13px;vertical-align:baseline;border-left-width:0px;">
<strong>到此，Hadoop的配置已经完成了.</strong></div>
<br><br>
﻿</div>
</div>
</div>
            </div>
                </div>
---
layout:     post
title:      Hbase 安装配置时HMaster启动后宕机的解决方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013181284/article/details/50747409				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">问题：启动HBase后，出现</span></p>
<p><span style="font-size:14px;">2014-03-15 11:55:22,153 ERROR org.apache.hadoop.hbase.master.HMasterCommandLine: Failed to start master<br>
java.lang.RuntimeException: HMaster Aborted</span></p>
<p><span style="font-size:14px;">HMaster进程自动死掉（消失）。</span></p>
<p><span style="font-size:14px;">原因：一、时间不同步</span></p>
<p><span style="font-size:14px;">解决方法：把时区设置为上海，执行以下命令：</span></p>
<p><span style="font-size:14px;">                 cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime<br>
                 ntpdate us.pool.ntp.org</span></p>
<p><span style="font-size:14px;">                  再使用date -R查看下时间。</span></p>
<p><span style="font-size:14px;">                  重启hbase.</span></p>
<p><span style="font-size:14px;">          二、HBase启动会在zookeeper注册一个hbase znode,配置文件错误导致第一次启动hbase失败，在第二次启动时，由于hbase已经存在，无法注册hbase znode,导致HMaster在启动几秒后消失。</span></p>
<p><span style="font-size:14px;">解决方法：zkCli.sh -server master进入zookeeper</span></p>
<p><span style="font-size:14px;">                 进入zookeeper后执行 ls /</span></p>
<p><span style="font-size:14px;"><img alt="" src=""></span></p>
<p><span style="font-size:14px;">                然后删除hbase.</span></p>
<p><span style="font-size:14px;">                rmr hbase</span></p>
<p><span style="font-size:14px;"><img alt="" src=""></span></p>
<p><span style="font-size:14px;">                重启hbase.</span></p>
<p><span style="font-size:14px;"><br></span></p>
            </div>
                </div>
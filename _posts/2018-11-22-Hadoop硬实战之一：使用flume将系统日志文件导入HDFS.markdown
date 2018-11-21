---
layout:     post
title:      Hadoop硬实战之一：使用flume将系统日志文件导入HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>问题来源：将所有生产系统产生的日志文件导入到HDFS</p>
<p>解决方案：使用数据收集系统Flume将Linux日志文件导入HDFS，将讲解如何在分布式环境中配置Flume，并检验Flume的可靠性模式，本文以配置单机为例进行说明。</p>
<p>实施步骤：</p>
<p>1.在伪分布式模型中运行Flume.</p>
<p> #yum install -y flume flume-master flume-node</p>
<p>安装完成后，启动Master和Agent</p>
<p> #/etc/init.d/flume-master start<br>
 #/etc/init.d/flume-node start<br></p>
<p>2.格式配置。默认情况下，Flume以Avro JSON格式写数据，若保留syslog原始格式，那么需要创建和编辑flume-site.xml文件。</p>
<p><img src="" alt=""><img src="" alt=""><br></p>
<p>3.前面已经启动flume master和Agent结点，另外还需要启动Collector结点作为Agent结点的数据接收器。</p>
<p> #flume node nowatch -n collector</p>
<p>需要注意的是：Flume以用户flume运行，需要确保该用户具有足够权限访问数据源文件（如 /var/log/messages），可通过chmod命令修改权限，否则数据源文件将无法上传到HDFS。</p>
<p>4.Flume Agent节点配置。访问http://172.16.24.160:35871/，如下图所示（已经配置完的结果）：</p>
<p><img src="" alt=""><img src="" alt=""><br></p>
<p>点击config弹出对话框中配置如下：</p>
<p><img src="" alt=""><br></p>
<p>5.Flume Collector节点配置。点击config弹出对话框中配置如下：</p>
<p><img src="" alt=""><br></p>
<p>6.验证文件是否成功上传到HDFS。</p>
<p><img src="" alt=""><br></p>
<p><br></p>
            </div>
                </div>
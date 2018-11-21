---
layout:     post
title:      hadoop bin 目录主要文件说明
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span>1. hadoop</p>
<p>                  hadoop的shell命令</p>
<p>         2. hadoop-config.sh</p>
<p>                  对一些变量进行赋值</p>
<p>                  HADOOP_HOME（hadoop的安装目录）</p>
<p>                  HADOOP_CONF_DIR  （hadoop的配置文件目录）</p>
<p>                  HADOOP_SLAVES   （--hosts指定的文件的地址）</p>
<p>         3. hadoop-daemon.sh</p>
<p>                  启动一个单一节点</p>
<p>                          hadoop-daemon.sh start namenode</p>
<p>                          hadoop-daemon.sh stop namenode</p>
<p>         4.hadoop-daemons.sh</p>
<p>                  在所有slaves上运行相同的脚本hadoop-daemon.sh</p>
<p>         5.start-balancer.sh</p>
<p>                  启动hadoop的负载均衡，占用带宽，在空闲时间运行</p>
         6.start-jobhistoryserver.sh
            </div>
                </div>
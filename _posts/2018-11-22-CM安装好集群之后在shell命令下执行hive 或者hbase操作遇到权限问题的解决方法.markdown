---
layout:     post
title:      CM安装好集群之后在shell命令下执行hive 或者hbase操作遇到权限问题的解决方法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>CM 安装的hive,默认使用CM创建的hive用户启动的hive相关的进程,同理hbase也一样，用hbase用户启动的hbase相关的进程。</p>
<p>在hdfs上，相应的文件夹的owner也是对应的账户，比如hive相关的文件夹的owner和group都为hive 和hive ,hbase的为hbase:hbase</p>
<p>这样你在控制台用其他用户进入hive或者hbase客户端shell的时候，执行相关的命令，就会报访问权限问题了，比如用root用户执行hive打开hive客户端，在里面执行查询就会报错。</p>
<p>这时候就要用到sudo命令，以相应用户的身份来运行程序。</p>
<p>比如运行hive客户端，就可以用:sudo -u hive hive</p>
<p>运行hbase相关的命令，就可以用sudo -u hbase hbase XXX,比如运行hbase检查程序：sudo -u hbase hbase hbck</p>
<p><br></p>
<p>哪些进程由哪些用户运行的在管理平台上都能很清楚的看到，比如hdfs用户是hdfs的超级管理员,flume用户是用来启动flume的。</p>
            </div>
                </div>
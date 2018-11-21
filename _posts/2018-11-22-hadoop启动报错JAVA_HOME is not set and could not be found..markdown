---
layout:     post
title:      hadoop启动报错JAVA_HOME is not set and could not be found.
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>前提条件：centos7已经安装jdk和hadoop，并配置好环境变量！</p>

<p style="margin-left:0in;">原因：/soft/hadoop/etc/hadoop目录下的hadoop-env.sh环境变量配置问题</p>

<p style="margin-left:0in;">解决办法：</p>

<p style="margin-left:0in;">1）打开hadoop-env.sh文件</p>

<p style="margin-left:0in;">sudo root    //切换到root用户</p>

<p style="margin-left:0in;">vim /soft/hadoop/etc/hadoop/hadoop-env.sh</p>

<p style="margin-left:0in;">注：具体路径因人而异</p>

<p style="margin-left:0in;">2）修改JAVA_HOME：</p>

<p style="margin-left:0in;">export JAVA_HOME=<span style="color:#f33b45;"><strong>/soft/jdk                    //一定要修改为绝对路径！</strong></span></p>

<p>注：/soft/jdk为本人的jdk安装目录</p>            </div>
                </div>
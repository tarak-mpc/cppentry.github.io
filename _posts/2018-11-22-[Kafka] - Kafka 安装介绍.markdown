---
layout:     post
title:      [Kafka] - Kafka 安装介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="postTitle" style="line-height:1.5;clear:both;font-size:14px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<a class="postTitle2" href="http://www.cnblogs.com/liuming1992/p/6423294.html" rel="nofollow" id="cb_post_title_url" style="color:rgb(7,93,179);">[Kafka] - Kafka 安装介绍</a></h1>
<div class="clear" style="clear:both;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
</div>
<div class="postBody" style="line-height:1.5;font-size:13px;font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(254,254,242);">
<div id="cnblogs_post_body">
<p style="line-height:1.5;">
Kafka是由<span>LinkedIn</span>公司开发的，之后贡献给Apache基金会，成为Apache的一个顶级项目，开发语言为Scala。提供了各种不同语言的API，具体参考Kafka的<a href="https://cwiki.apache.org/confluence/display/KAFKA/Clients%0D" rel="nofollow" style="color:rgb(7,93,179);">cwiki</a>页面；</p>
<p style="line-height:1.5;">
Kafka的安装方式主要分为三种：<span>单机安装</span>、<span>伪分布式安装</span>、<span>分布式安装；</span>安装基本配置一样，区别在于：单机安装是在一台服务器上只安装一个broker服务，伪分布式安装是指在一台服务器上安装多个broker服务，分布式安装是指具有多台服务器，至于每天服务器上的broker服务数量可选；所以这里只介绍伪分布式安装的讲解</p>
<p style="line-height:1.5;">
Kafka安装步骤：</p>
<p style="line-height:1.5;">
   -1. 安装JDK环境(配置JAVA_HOME, 1.7.0_79)</p>
<p style="line-height:1.5;">
   -2. 安装Scala环境(配置SCALA_HOME, 2.10.4)</p>
<p style="line-height:1.5;">
   -3. 安装zookeeper环境(3.4.5)</p>
<p style="line-height:1.5;">
   -4. <span>安装kafka环境</span></p>
<p style="line-height:1.5;">
至于JDK、SCALA、Zookeeper的安装步骤省略，直接进入Kafka环境安装步骤：</p>
<p style="line-height:1.5;">
-1. 下载安装包，下载地址：<a href="http://archive.apache.org/dist/kafka/" rel="nofollow" style="color:rgb(7,93,179);">http://archive.apache.org/dist/kafka/</a>，在该目录下选择具体版本，比如这里选择版本<a href="http://archive.apache.org/dist/kafka/0.8.2.1/kafka_2.10-0.8.2.1.tgz" rel="nofollow" style="color:rgb(7,93,179);">0.8.2.1</a>安装包进行下载，如果没有找到对应的安装包，可以选择源码进行下载重新编译；将下载好的压缩包上传到Linux服务器上</p>
<p style="line-height:1.5;">
-2. 解压下载好的压缩包，并配置KAFKA_HOME环境变量(配置变量步骤可选)</p>
<p style="line-height:1.5;">
-3. 复制${KAFKA_HOME}/config/server.properties文件为server1.properties、server2.properties、server3.properties三个服务器配置文件</p>
<p style="line-height:1.5;">
<img src="http://images2015.cnblogs.com/blog/708990/201702/708990-20170221111726288-245518619.png" alt="" style="border:0px;"></p>
<p style="line-height:1.5;">
-4. 修改server{1,2,3}.properties配置文件的内容，主要更改参数为：</p>
<p style="line-height:1.5;">
<img src="http://images2015.cnblogs.com/blog/708990/201702/708990-20170221112117132-687707856.png" alt="" style="border:0px;"></p>
<p style="line-height:1.5;">
<img src="http://images2015.cnblogs.com/blog/708990/201702/708990-20170221112125976-693436340.png" alt="" style="border:0px;"></p>
<p style="line-height:1.5;">
<img src="http://images2015.cnblogs.com/blog/708990/201702/708990-20170221112133913-1794194133.png" alt="" style="border:0px;"></p>
<p style="line-height:1.5;">
-5. 启动kafka服务, 参数-daemon的含义是指启动的服务进程是作为后台进程(守护线程)启动还是作为前端线程来启动</p>
<p style="line-height:1.5;">
${KAFKA_HOME}/bin/kafka-server-start.sh -daemon ${KAFKA_HOME}/conf/server1.properties</p>
<p style="line-height:1.5;">
${KAFKA_HOME}/bin/kafka-server-start.sh -daemon ${KAFKA_HOME}/conf/server2.properties</p>
<p style="line-height:1.5;">
${KAFKA_HOME}/bin/kafka-server-start.sh -daemon ${KAFKA_HOME}/conf/server3.properties</p>
<p style="line-height:1.5;">
-6. 查看Kafka的进程是否存在</p>
<p style="line-height:1.5;">
jps -ml</p>
<p style="line-height:1.5;">
<img src="http://images2015.cnblogs.com/blog/708990/201702/708990-20170221112713195-540769229.png" alt="" style="border:0px;"></p>
<p style="line-height:1.5;">
-7. 关闭Kafka服务</p>
<p style="line-height:1.5;">
${KAFKA_HOME}/bin/kafka-server-stop.sh</p>
<p style="line-height:1.5;">
至此，Kafka安装完成</p>
</div>
</div>
            </div>
                </div>
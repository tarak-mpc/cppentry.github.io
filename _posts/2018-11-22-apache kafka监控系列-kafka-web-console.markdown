---
layout:     post
title:      apache kafka监控系列-kafka-web-console
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lizhitao/article/details/35595723				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span style="color:rgb(51,51,51);font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:15px;line-height:25.5px;"><span style="color:rgb(51,51,51);font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:15px;line-height:25.5px;">Kafka
 Web Console安装和使用</span></span></h1>
<div><span style="color:rgb(51,51,51);font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:15px;line-height:25.5px;"><span style="color:rgb(51,51,51);font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:15px;line-height:25.5px;"><span style="color:rgb(51,51,255);font-family:Arial, sans-serif;font-size:24px;line-height:36px;">apache
 kafka中国社区QQ群:162272557</span><br></span></span></div>
<p><span style="color:rgb(51,51,51);font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:15px;line-height:25.5px;">Kafka Web Console是kafka的开源web监控程序.</span></p>
<p><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;">功能介绍如下:</span></span></p>
<p></p>
<ul><li><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;">brokers列表</span></span></li><li><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;">连接kafka的zk集群列表</span></span></li><li><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;">所有topic列表，操作相应topic可以浏览查看相应message生产和消费流量图.</span></span></li></ul><h2><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;">1.下载Kafka Web Console</span></span></h2>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;"><a href="https://github.com/claudemamo/kafka-web-console/archive/master.zip" rel="nofollow">Kafka
 Web Console</a><br></span></span></div>
<h2><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;color:#333333;">2.安装sbt</span></h2>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;color:#333333;">a. centos  : yum install sbt</span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;color:#333333;">b. ubuntu : apt-get install sbt   </span></div>
<h2><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;color:#333333;">3.配置<span style="color:rgb(51,51,51);font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:15px;line-height:25.5px;">Kafka
 Web Console</span></span></h2>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;color:#333333;"><span style="color:rgb(51,51,51);font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:15px;line-height:25.5px;">a.增加数据库依赖包(mysql)，解压kafka-web-console.tar.gz,进入目录cd
 kafka-web-console</span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;">编辑文件vim build.sbt </span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;">增加mysql配置：</span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#333333;"><span style="font-size:15px;line-height:25.5px;background-color:rgb(204,204,204);">......</span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="line-height:25.5px;background-color:rgb(204,204,204);"><span style="color:#333333;font-size:15px;">libraryDependencies ++= Seq(</span><br><span style="color:#333333;font-size:15px;">  jdbc,</span><br><span style="color:#333333;font-size:15px;">  cache,</span><br><span style="color:#333333;font-size:15px;">  "org.squeryl" % "squeryl_2.10" % "0.9.5-6",</span><br><span style="color:#333333;font-size:15px;">  "com.twitter" % "util-zk_2.10" % "6.11.0",</span><br><span style="color:#333333;font-size:15px;">  "com.twitter" % "finagle-core_2.10" % "6.15.0",</span><br><span style="color:#333333;font-size:15px;">  "org.apache.kafka" % "kafka_2.10" % "0.8.1",</span><br><span style="color:#333333;font-size:15px;">  "org.quartz-scheduler" % "quartz" % "2.2.1",</span><br><span style="color:#333333;font-size:15px;">  </span><span style="font-size:18px;color:#ff0000;"><strong>"mysql" % "mysql-connector-java" % "5.1.9"</strong></span><br><span style="color:#333333;font-size:15px;">    exclude("javax.jms", "jms")</span><br><span style="color:#333333;font-size:15px;">    exclude("com.sun.jdmk", "jmxtools")</span><br><span style="color:#333333;font-size:15px;">    exclude("com.sun.jmx", "jmxri")</span><br><span style="color:#333333;font-size:15px;">)</span><br></span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:rgb(51,51,51);"><span style="font-size:15px;line-height:25.5px;background-color:rgb(204,204,204);">.......</span></span></div>
<div><br><h2><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="line-height:25.5px;"><span style="font-size:14px;">4.配置mysql的jdbc驱动</span></span></span></h2>
</div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="line-height:25.5px;"><span style="font-size:14px;">vim application.conf</span></span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;">增加代码如下:</span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="line-height:25.5px;"><span style="font-size:14px;"></span></span></span><pre><code class="language-plain">.......
db.default.driver=com.mysql.jdbc.Driver
db.default.url="jdbc:mysql://192.168.2.105:3306/mafka?useUnicode=true&amp;characterEncoding=UTF8&amp;connectTimeout=5000&amp;socketTimeout=10000"
db.default.user=xxx
db.default.password=xxx
.......</code></pre>
<h2><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="line-height:25.5px;"><span style="font-size:14px;">5.执行sql语句(如下绿色选框所示)</span></span></span></h2>
</div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="line-height:25.5px;"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20140628213633390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></span>
<h2><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="line-height:25.5px;"><span style="font-size:14px;">6.编译</span></span></span></h2>
</div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="line-height:25.5px;"><span style="font-size:14px;">lizhitao@localhost:~$ sbt package<br></span></span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;">打包编译时会从官网上下载很多jar，由于网络原因，所以很慢，需要耐心等待。</span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;">注意:下载的jar是隐藏的，<span style="color:#ff0000;"><strong>在cd  ~/.ivy2 目录(相应子目录)下可以看到所有jar.</strong></span></span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';color:#ff0000;"><span style="font-size:14px;line-height:25.5px;"><strong>ivy2所有jar包百度云下载</strong></span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';"><span style="font-size:14px;line-height:25.5px;"><a href="http://pan.baidu.com/s/1gdgQqqB" rel="nofollow">ivy2所有jar包下载</a><br></span></span></div>
<h2><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;">7.运行</span></span></h2>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;"><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;line-height:25.5px;">lizhitao@localhost:~$
 sbt run</span><br></span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;"><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;line-height:25.5px;"><img src="https://img-blog.csdn.net/20140628214559562?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl6aGl0YW8=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></span></span></div>
<h2><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;">8.浏览访问</span></span></h2>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;">访问地址: http://ip:9000/</span></span></div>
<div><span style="font-family:Helvetica, arial, freesans, clean, sans-serif, 'Segoe UI Emoji', 'Segoe UI Symbol';font-size:14px;"><span style="line-height:25.5px;"><br></span></span></div>
<div></div>
<p></p>
            </div>
                </div>
---
layout:     post
title:      linux系统下flume读取mysql数据源并发送给kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>linux系统下flume读取mysql数据源并发送给kafka<br>
flume版本：apache-flume-1.8.0-bin.tar<br>
kafka版本：kafka_2.11-0.10.2.0<br>
所需插件：flume-ng-sql-source-1.4.4 和 mysql-connector-java-5.1.43-bin注意与mysql版本一致</p>
<p>kafka和flume可在官方下载</p>
<p>flume-ng-sql-source-1.4.4下载地址：<a href="https://pan.baidu.com/s/1mDETw5CH2W5DoP7rLXoyRA" rel="nofollow">https://pan.baidu.com/s/1mDETw5CH2W5DoP7rLXoyRA</a> 密码：36nf</p>
<p>mysql-connector-java-5.1.43-bin下载地址：<a href="https://pan.baidu.com/s/1Wez_2jCwvVScAyFZqbrPzg" rel="nofollow">https://pan.baidu.com/s/1Wez_2jCwvVScAyFZqbrPzg</a> 密码：preb</p>
<p>第一步:先将flume安装包apache-flume-1.8.0-bin.tar在linux操作系统解压<br>
<img src="https://img-blog.csdn.net/20180928105523818?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dmbmgxMjUxMQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>第二步：将两个插件拷贝到lib目录下</p>
<p>第三步：在conf目录下新文件flume-mysql.conf并粘入如下内容<br>
a1.channels = c1<br>
a1.sources = s1<br>
a1.sinks = k1</p>
<p>###########sources#################<br>
####s1######<br>
a1.sources.s1.type = org.keedio.flume.source.SQLSource<br>
a1.sources.s1.hibernate.connection.url = jdbc:mysql://192.168.1.120:3306/test<br>
a1.sources.s1.hibernate.connection.user = hadoop<br>
a1.sources.s1.hibernate.connection.password = hadoop<br>
a1.sources.s1.hibernate.connection.autocommit = flase<br>
a1.sources.s1.hibernate.dialect = org.hibernate.dialect.MySQL5Dialect<br>
a1.sources.s1.hibernate.connection.driver_class = com.mysql.jdbc.Driver<br>
a1.sources.s1.run.query.delay=10000<br>
a1.sources.s1.status.file.path = /home/sanwei/apache-flume-1.8.0-src/var/lib/flume<br>
<a href="http://a1.sources.s1.status.file.name" rel="nofollow">a1.sources.s1.status.file.name</a> = sqlSource.status<br>
a1.sources.s1.custom.query = select * from wlslog where id&gt; $<span class="katex--inline"><span class="katex"><span class="katex-mathml"><math><semantics><mrow><mi mathvariant="normal">@</mi></mrow><annotation encoding="application/x-tex">@</annotation></semantics></math></span><span class="katex-html" aria-hidden="true"><span class="base"><span class="strut" style="height: 0.69444em; vertical-align: 0em;"></span><span class="mord">@</span></span></span></span></span> $ order by id asc</p>
<p>a1.sources.s1.batch.size = 1000<br>
a1.sources.s1.max.rows = 1000<br>
a1.sources.s1.hibernate.connection.provider_class = org.hibernate.connection.C3P0ConnectionProvider<br>
a1.sources.s1.hibernate.c3p0.min_size=1<br>
a1.sources.s1.hibernate.c3p0.max_size=10</p>
<p>############channels###############</p>
<p>a1.channels.c1.type = memory<br>
a1.channels.c1.capacity = 10000<br>
a1.channels.c1.transactionCapacity = 10000<br>
a1.channels.c1.byteCapacityBufferPercentage = 20<br>
a1.channels.c1.byteCapacity = 800000</p>
<p>############sinks##################<br>
a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink<br>
a1.sinks.k1.topic = my-topic<br>
a1.sinks.k1.brokerList = 192.168.1.121:9092<br>
a1.sinks.k1.requiredAcks = 1<br>
a1.sinks.k1.batchSize = 20<br>
a1.sinks.k1.channel = c1</p>
<p>a1.sinks.k1.channel = c1<br>
a1.sources.s1.channels=c1</p>
<p>第四步：启动命令<br>
~/apache-flume-1.8.0-bin/bin/flume-ng agent -n a1 -c conf -f /home/sanwei/apache-flume-1.8.0-bin/conf/flume-mysql.conf -Dflume.root.logger=DEBUG,console</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
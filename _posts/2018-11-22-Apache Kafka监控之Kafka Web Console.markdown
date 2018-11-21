---
layout:     post
title:      Apache Kafka监控之Kafka Web Console
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
昨天在<a title="" href="http://www.iteblog.com/archives/1083" rel="nofollow" style="color:rgb(0,166,124);text-decoration:none;">《Apache Kafka监控之KafkaOffsetMonitor》</a>介绍了<span class="wp_keywordlink_affiliate"><a href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">Kafka</a></span>OffsetMonitor的编译以及配置等内容，今天继续来介绍<span class="wp_keywordlink_affiliate"><a href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">Kafka</a></span>的第二种监控系统<span class="wp_keywordlink_affiliate"><a href="http://www.iteblog.com/archives/tag/kafka" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;">Kafka</a></span> Web
 Console。他也是一款开源的系统，源码的地址在https://github.com/claudemamo/kafka-web-console中。Kafka Web Console也是用Scala语言编写的Java web程序用于监控Apache Kafka。这个系统的功能和KafkaOffsetMonitor很类似，但是我们从源码角度来看，这款系统实现比KafkaOffsetMonitor要复杂很多，而且编译配置比KafkaOffsetMonitor较麻烦。<br>
　　要想运行这套系统我们需要的先行条件为：</p>
<ol style="list-style:none;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;"><li style="line-height:30px;">Play Framework 2.2.x</li><li style="line-height:30px;">Apache Kafka 0.8.x</li><li style="line-height:30px;">Zookeeper 3.3.3 or 3.3.4</li></ol><p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　同样，我们从https://github.com/claudemamo/kafka-web-console上面将源码下载下来，然后用sbt进行编译，在编译前我们需要做如下的修改：<br>
　　<strong>1、Kafka Web Console默认用的数据库是H2，它支持以下几种数据库：</strong></p>
<pre><code class="language-java">H2 (default)
PostgreSql
Oracle
DB2
MySQL
Apache Derby
Microsoft SQL Server
</code></pre>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
为了方便，我们可以使用Mysql数据库，只要做如下修改即可，找到 conf/application.conf文件，并修改如下</p>
<pre><code class="language-java">#############################################################################
 User: 过往记忆
 Date: 14-08-08
 Time: 11:37
 bolg: http://www.iteblog.com
 本文地址：http://www.iteblog.com/archives/1084
 过往记忆博客，专注于hadoop、hive、spark、shark、flume的技术博客，大量的干货
 过往记忆博客微信公共帐号：iteblog_hadoop
#############################################################################
将这个
db.default.driver=org.h2.Driver
db.default.url="jdbc:h2:file:play"
# db.default.user=sa
# db.default.password=""


修改成
db.default.driver=com.mysql.jdbc.Driver
db.default.url="jdbc:mysql://localhost:3306/kafkamonitor"
db.default.user=iteblog
db.default.pass=wyp
</code></pre>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
我们还需要修改build.sbt，加入对Mysql的依赖:</p>
<pre><code class="language-java">"mysql" % "mysql-connector-java" % "5.1.31"
</code></pre>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　<strong>2、执行conf/evolutions/default/bak目录下面的1.sql、2.sql和3.sql三个文件。需要注意的是，这三个sql文件不能直接运行，有语法错误，需要做一些修改。</strong><br>
上面的注意事项弄完之后，我们就可以编译下载过来的源码：</p>
<pre><code class="language-java"># sbt package
</code></pre>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　编译的过程比较慢，有些依赖包下载速度非常地慢，请耐心等待。</p>
<div class="note" style="border:1px solid rgb(214,233,198);color:rgb(60,118,61);font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;background-color:rgb(223,240,216);">
　　在编译的过程中，可能会出现有些依赖包无法下载，如下错误：
<p></p>
<pre><code class="language-java">[warn] module not found: com.typesafe.play#sbt-plugin;2.2.1
[warn] ==== typesafe-ivy-releases: tried
[warn] http://repo.typesafe.com/typesafe/ivy-releases/
com.typesafe.play/sbt-plugin/scala_2.9.2/sbt_0.12/2.2.1/ivys/ivy.xml
[warn] ==== sbt-plugin-releases: tried
[warn] http://scalasbt.artifactoryonline.com/scalasbt/sbt-plugin-releases/
com.typesafe.play/sbt-plugin/scala_2.9.2/sbt_0.12/2.2.1/ivys/ivy.xml
[warn] ==== local: tried
[warn] /home/iteblog/.ivy2/local/com.typesafe.play/
sbt-plugin/scala_2.9.2/sbt_0.12/2.2.1/ivys/ivy.xml
[warn] ==== Typesafe repository: tried
[warn] http://repo.typesafe.com/typesafe/releases/com/
typesafe/play/sbt-plugin_2.9.2_0.12/2.2.1/sbt-plugin-2.2.1.pom
[warn] ==== public: tried
[warn] http://repo1.maven.org/maven2/com/typesafe/play/
sbt-plugin_2.9.2_0.12/2.2.1/sbt-plugin-2.2.1.pom
[warn] ::::::::::::::::::::::::::::::::::::::::::::::

==== local: tried

/home/iteblog/.ivy2/local/org.scala-sbt/collections/0.13.0/jars/collections.jar

::::::::::::::::::::::::::::::::::::::::::::::

:: FAILED DOWNLOADS ::

:: ^ see resolution messages for details ^ ::

::::::::::::::::::::::::::::::::::::::::::::::

:: org.scala-sbt#collections;0.13.0!collections.jar

::::::::::::::::::::::::::::::::::::::::::::::
</code></pre>
<p>　　我们可以手动地下载相关依赖，并放到类似/home/iteblog/.ivy2/local/org.scala-sbt/collections/0.13.0/jars/目录下面。然后再编译就可以了。</p>
</div>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
　　最后，我们可以通过下面命令启动Kafka Web Console监控系统：</p>
<pre><code class="language-java"># sbt run
</code></pre>
<p style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
并可以在http://localhost:9000查看。下面是一张效果图</p>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;">
<a href="http://www.iteblog.com/pic/topics.png" rel="nofollow" title="" style="color:rgb(0,166,124);text-decoration:none;"><img alt="" src="http://www.iteblog.com/pic/topics.png" width="770" style="vertical-align:middle;border:0px;"></a></div>
<div class="wp-caption aligncenter" style="text-align:center;font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:22.265625px;color:#FF0000;">
<span style="font-weight:bold;font-size:15px;line-height:26px;">尊重原创，转载请注明： 转载自</span><a href="http://www.iteblog.com/" rel="nofollow" title="" style="font-weight:bold;font-size:15px;line-height:26px;color:#FF0000;text-decoration:none;">过往记忆（http://www.iteblog.com/）</a></div>
<span style="font-family:'Microsoft Yahei', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:15px;line-height:26px;color:#FF0000;display:block;"><strong>本文链接地址: <a href="http://www.iteblog.com/archives/1084" rel="nofollow" title="" style="color:#FF0000;text-decoration:none;">《Apache
 Kafka监控之Kafka Web Console》（http://www.iteblog.com/archives/1084）</a></strong></span>
            </div>
                </div>
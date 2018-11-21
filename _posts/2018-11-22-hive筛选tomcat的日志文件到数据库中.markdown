---
layout:     post
title:      hive筛选tomcat的日志文件到数据库中
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：尊重原创，转载请标明"本文转自:https://blog.csdn.net/high2011"					https://blog.csdn.net/high2011/article/details/51595493				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Tomcat <span style="font-family:'宋体';">日志文件目录、</span>脚本正则表达式抓取</p>
<p></p>
<p><strong>1、创建hive表：apachelog </strong></p>
<p>语句如下： </p>
<p>CREATE TABLE apachelog (</p>
<p> host STRING,</p>
<p> identity STRING,</p>
<p> t_user STRING,</p>
<p> time STRING,</p>
<p> type STRING,</p>
<p> http STRING,</p>
<p> http_type STRING,</p>
<p> status STRING,</p>
<p> agent STRING</p>
<p> )</p>
<p> ROW FORMAT SERDE 'org.apache.hadoop.hive.serde2.RegexSerDe'</p>
<p> WITH SERDEPROPERTIES (</p>
<p> "input.regex" = "([^ ]*) ([^ ]*) ([^ ]*) \\[(.*?) .*?\\] \"([^ ]*) (.*?)\" ([^ ]*) ([^ ]*)"</p>
<p>  )</p>
<p>  STORED AS TEXTFILE;</p>
<p> </p>
<p>最后<span style="font-family:Calibri;">load</span><span style="font-family:'宋体';">日志文件：</span></p>
<p>#LOAD DATA LOCAL INPATH  <span style="color:#ff0000;">'log<span style="font-family:'宋体';">日志的绝对目录</span></span><span style="font-family:Calibri;"><span style="color:#ff0000;">'
</span> OVERWRITE INTO TABLE apachelog; </span></p>
<br><p><strong>2、可以添加一个定时任务每小时去执行日志收集：</strong></p>
<p>crontab -e</p>
<p>*/2400 * * * * /usr/sbin/sh shell<span style="font-family:'宋体';">脚本</span></p>
<p><br></p>
<p><strong>日志格式可以如下：</strong></p>
<p>127.0.0.1 - - [24/Apr/2016:09:55:45 +0800] "GET / HTTP/1.1" 200 11418<br>
127.0.0.1 - - [24/Apr/2016:09:55:47 +0800] "GET / HTTP/1.1" 200 11418<br>
127.0.0.1 - - [24/Apr/2016:09:57:52 +0800] "GET / HTTP/1.1" 200 11418<br>
0:0:0:0:0:0:0:1 - - [24/Apr/2016:09:57:56 +0800] "GET / HTTP/1.1" 200 11418<br>
0:0:0:0:0:0:0:1 - - [24/Apr/2016:09:57:56 +0800] "GET /tomcat.css HTTP/1.1" 200 5926<br>
0:0:0:0:0:0:0:1 - - [24/Apr/2016:09:57:56 +0800] "GET /tomcat.png HTTP/1.1" 200 5103<br>
0:0:0:0:0:0:0:1 - - [24/Apr/2016:09:57:56 +0800] "GET /bg-nav.png HTTP/1.1" 200 1401<br>
0:0:0:0:0:0:0:1 - - [24/Apr/2016:09:57:56 +0800] "GET /asf-logo.png HTTP/1.1" 200 17811<br>
0:0:0:0:0:0:0:1 - - [24/Apr/2016:09:57:56 +0800] "GET /bg-middle.png HTTP/1.1" 200 1918<br>
0:0:0:0:0:0:0:1 - - [24/Apr/2016:09:57:56 +0800] "GET /bg-button.png HTTP/1.1" 200 713<br>
0:0:0:0:0:0:0:1 - - [24/Apr/2016:09:57:56 +0800] "GET /bg-upper.png HTTP/1.1" 200 3103<br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
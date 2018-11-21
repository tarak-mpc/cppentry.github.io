---
layout:     post
title:      hive安装、配置 mysql存储元数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p> </p>
<p><span style="font-size:14px;">1.下载hive安装包</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;">2.安装hive</span></p>
<p><span style="font-size:14px;">将hive解压到指定路径</span></p>
<p><span style="font-size:14px;">#mv hive-0.9.0.tar.gz ~hadoop</span></p>
<p><span style="font-size:14px;">#chown hadoop.hadoop ~hadoop/hive-0.9.0.tar.gz</span></p>
<p><span style="font-size:14px;">#su - hadoop</span></p>
<p><span style="font-size:14px;">$tar zxf hive-0.9.0.tar.gz</span></p>
<p><span style="font-size:14px;">$mv hive-0.9.0.tar.gz hive</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;">3.配置hive</span></p>
<p><span style="font-size:14px;">$pwd</span></p>
<p><span style="font-size:14px;">/home/hadoop/hive</span></p>
<p><span style="font-size:14px;">$cd conf</span></p>
<p><span style="font-size:14px;">$vi hadoop-env.sh</span></p>
<p><span style="font-size:14px;">修改以下内容：</span></p>
<p><span style="font-size:14px;">export  HADOOP_HOME=/home/hadoop/hadoop</span></p>
<p><span style="font-size:14px;">export  HIVE_HOME=/home/hadoop/hive</span></p>
<p><span style="font-size:14px;">保存退出</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;">此时可以运行hive进行安装测试： </span></p>
<p><span style="font-size:14px;">$./bin/hive</span></p>
<p><span style="font-size:14px;">hive&gt;</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;">4.使用mysql存储数据元（默认使用derby）</span></p>
<p><span style="font-size:14px;">(1) 修改配置文件</span></p>
<p><span style="font-size:14px;">$pwd</span></p>
<p><span style="font-size:14px;">/home/hadoop/hive/conf</span></p>
<p><span style="font-size:14px;">$vi hive-site.xml</span></p>
<p><span style="font-size:14px;">修改如下内容：</span></p>
<p><span style="font-size:14px;">&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
  &lt;value&gt;<span style="color:#cc0000;">jdbc:mysql://masterIP:3306/hivedb</span>?useUnicode=true&amp;amp;characterEncoding=UTF-8&amp;amp;createDatabaseIfNotExist=true&lt;/value&gt;<br>
  &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;<br>
&lt;/property&gt;</span></p>
<p><span style="font-size:14px;">&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;<br>
  &lt;value&gt;<span style="color:#cc0000;">com.mysql.jdbc.Driver</span>&lt;/value&gt;<br>
  &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;<br>
&lt;/property&gt;</span></p>
<p><span style="font-size:14px;">&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
  &lt;value&gt;hive&lt;/value&gt;<br>
  &lt;description&gt;username to use against metastore database&lt;/description&gt;<br>
&lt;/property&gt;</span></p>
<p><span style="font-size:14px;">&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;<br>
  &lt;value&gt;hive&lt;/value&gt;<br>
  &lt;description&gt;password to use against metastore database&lt;/description&gt;<br>
&lt;/property&gt;</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;">(2)获取<span style="font-size:14px;">mysql-connector-java-X.jar</span></span></p>
<p><span style="font-size:14px;">将mysql-connector-java-X.jar拷贝到 hive/lib下</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;">(3)</span><span style="font-size:14px;">登录mysql数据库，创建hivedb数据库，创建hive用户并赋予相应权限</span></p>
<p><span style="font-size:14px;">mysql -p</span></p>
<p><span style="font-size:14px;">mysql&gt;create database hivedb;</span></p>
<p><span style="font-size:14px;">mysql&gt;grant all priviledeges to hive.* on </span>
<a href="mailto:hivedb@'%20rel=" nofollow><span style="font-size:14px;">hivedb@'localhost'</span></a><span style="font-size:14px;"> idetified by 'hive';</span></p>
<p><span style="font-size:14px;">mysql&gt;grant all priviledeges to hive.* on </span>
<a href="mailto:hivedb@'%20rel=" nofollow><span style="font-size:14px;">hivedb@'master'</span></a><span style="font-size:14px;"> idetified by 'hive';</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;">5.运行hive，检测安装配置情况</span></p>
<p><span style="font-size:14px;">$./bin/hive</span></p>
<p><span style="font-size:14px;">hive&gt;show databases;</span></p>
<p><span style="font-size:14px;">OK</span></p>
<p><span style="font-size:14px;">default;</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;">6.查看mysql数据库中的hive数据元</span></p>
<p><span style="font-size:14px;">mysql -p</span></p>
<p><span style="font-size:14px;">mysql&gt;use hivedb;</span></p>
<p><span style="font-size:14px;">mysql&gt;show tables;</span></p>
<p><span style="font-size:14px;">+-----------------+<br>
| Tables_in_hive |<br>
+-----------------+<br>
| BUCKETING_COLS |<br>
| CDS |<br>
| COLUMNS_V2 |<br>
| DATABASE_PARAMS |<br>
| DBS |<br>
| PARTITION_KEYS |<br>
| SDS |<br>
| SD_PARAMS |<br>
| SEQUENCE_TABLE |<br>
| SERDES |<br>
| SERDE_PARAMS |<br>
| SORT_COLS |<br>
| TABLE_PARAMS |<br>
| TBLS |<br>
+-----------------+<br>
14 rows in set (0.00 sec)<br></span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;"> 注意hive日志的位置，有问题先查看日志信息</span></p>
<p><span style="font-size:14px;"></span> </p>
<p><span style="font-size:14px;"></span> </p>
            </div>
                </div>
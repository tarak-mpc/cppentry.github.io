---
layout:     post
title:      Hive-mysql存储元数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>    Hive中的数据保存在hdfs中，主要根据HADOOP_HOME环境变量来获取hadoop的配置信息。hive中除了hdfs外，还需要保存描述库、表的数据，称为元数据。元数据默认由hive内置的derby数据库进行存储，默认存放在hive/bin/metastore.db中。</p><p>    可以将hive默认元数据存储的数据库改为mysql。</p><p>    修改为mysql数据库，需要在hive/lib下加入mysql的驱动包。<br></p><p>     在hive/conf下新增hive-site.xml，配置mysql数据库连接信息，用户密码，驱动如下：</p><pre><code class="language-html">&lt;configuration&gt;
&lt;property&gt;
&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
&lt;value&gt;jdbc:mysql://192.168.0.1:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
 &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
&lt;value&gt;root&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
&lt;value&gt;root&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;</code></pre><p>启动hive报错：</p><p><img src="https://img-blog.csdn.net/20180715011132705?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3N0YXJzYW5kMTAxXw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:For direct MetaStore DB connections, we don't support retries at the client level.)<br></p><p>上述错误为，mysql编码集与hive编码集不匹配。</p><p>解决方案，将hive删除，重新建立，并制定编码集。</p><p>mysql&gt; drop database hive;<br>Query OK, 22 rows affected (0.81 sec)<br>mysql&gt; create database hive charset latin1;<br>Query OK, 1 row affected (0.00 sec)<br></p><p><br></p><p><br></p>            </div>
                </div>
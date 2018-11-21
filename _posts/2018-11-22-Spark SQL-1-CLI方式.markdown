---
layout:     post
title:      Spark SQL-1-CLI方式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wl101yjx/article/details/43846261				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:'Times New Roman';"><strong><span style="font-size:24px;">1.1、Spark SQL CLI配置 </span></strong><br><span style="font-size:18px;">Spark SQL访问Hive，Hive服务器必须先启动Hive Metastore（Spark会读取$SPARK_HOME/conf/hive-site.xml）</span><br><span style="font-size:18px;">Spark SQL is designed to be compatible with the Hive Metastore, SerDes and UDFs. Currently Spark SQL is based on Hive 0.12.0 and 0.13.1.</span><br></span>
<p><span style="font-family:'Times New Roman';"><span style="font-size:18px;color:#ff0000;">在使用Spark SQL CLI前，要先启动hive metastore。</span></span></p>
<p><span style="font-family:'Times New Roman';"><span style="font-size:18px;color:#ff0000;"><br></span></span></p>
<span style="font-family:'Times New Roman';"><span style="font-size:18px;">启动Hive Metastore Server（Hive下执行命令）</span><br><span style="font-size:18px;color:#ff0000;">hive --service metastore</span><br><span style="font-size:18px;">nohup hive --service metastore &gt; /home/jianxin/other/logs/metastore.log 2&gt;&amp;1 &amp;</span><br><span style="font-size:18px;">netstat -ntlp | grep 9083</span><br><span style="font-size:18px;">lsof -i:9083</span><br><br></span>
<p><span style="font-family:'Times New Roman';"><span style="font-size:18px;">客户端$SPARK_HOME/conf/hive-site.xml作如下配置：</span></span></p>
<p><span style="font-family:'Times New Roman';"></span></p>
<pre><code class="language-html">&lt;configuration&gt;  
&lt;property&gt;  
&lt;name&gt;hive.metastore.uris&lt;/name&gt;  
&lt;value&gt;thrift://bigdata0:9083&lt;/value&gt;
&lt;/property&gt;  
&lt;/configuration&gt; </code></pre><br><strong><span style="font-size:24px;">1.2、访问，Spark SQL CLI进入</span></strong><br><span style="font-family:'Times New Roman';font-size:18px;">cd /opt/spark/spark/bin<br>
spark-sql --master spark://bigdata0:7077 --executor-memory 3g <br>
进入后：<span style="color:#ff0000;">spark-sql&gt;</span><br>
SHOW DATABASES;<br>
CREATE DATABASE d1;<br>
USE d1;<br>
SHOW TABLES;<br>
CREATE TABLE IF NOT EXISTS t1(c1 STRING, c2 STRING)ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';  <br>
LOAD DATA LOCAL INPATH '/data1/db1t1.data'  INTO TABLE t1;  <br>
SELECT * FROM t1 LIMIT 10;<br>
quit;<br></span>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">SELECT visit_time,count(1) FROM t_visit_log GROUP BY visit_time.<br>
SELECT visit_time,user_id FROM t_visit_log SORT BY user_id LIMIT 10; <br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;"><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:18px;">bin/spark-sql --master spark://bigdata0:7077 --executor-memory 3g -h bigdata0 -p10000<br>
进入后：[bigdata0:10000]spark-sql&gt;<br>
show databases;——无反应，以后再研究<br><br>
-h指定Hive所在主机 <br>
-p指定端口</span><br></p>
<p><span style="font-family:'Times New Roman';"><strong><span style="font-size:24px;">2.1、Thrift JDBC/ODBC server</span></strong></span></p>
<p><span style="font-family:'Times New Roman';"><span style="font-size:18px;">ThriftServer服务器$SPARK_HOME/conf/hive-site.xml先做如下配置<br></span></span></p>
<p><span style="font-family:'Times New Roman';"><span style="font-size:18px;"></span></span></p>
<pre><code class="language-java">&lt;configuration&gt;  
&lt;!--Spark SQL CLI--&gt;
    &lt;property&gt;  
        &lt;name&gt;hive.metastore.uris&lt;/name&gt;  
        &lt;value&gt;thrift://bigdata0:9083&lt;/value&gt;  
    &lt;/property&gt;  

&lt;!--Thrift JDBC/ODBC server--&gt;
    &lt;property&gt;
        &lt;name&gt;hive.server2.thrift.min.worker.threads&lt;/name&gt;
        &lt;value&gt;5&lt;/value&gt;
    &lt;/property&gt;

    &lt;property&gt;
        &lt;name&gt;hive.server2.thrift.max.worker.threads&lt;/name&gt;
        &lt;value&gt;500&lt;/value&gt;
    &lt;/property&gt;

    &lt;property&gt;
        &lt;name&gt;hive.server2.thrift.port&lt;/name&gt;
        &lt;value&gt;10000&lt;/value&gt;
    &lt;/property&gt;

    &lt;property&gt;
        &lt;name&gt;hive.server2.thrift.bind.host&lt;/name&gt;
        &lt;value&gt;bigdata0&lt;/value&gt;
    &lt;/property&gt;

&lt;/configuration&gt;</code></pre>
<p></p>
<p><span style="font-family:'Times New Roman';"><span style="font-size:18px;">启动ThriftServer服务器<br>
sbin/<span style="color:#ff0000;">start-thriftserver.sh</span> --master spark://bigdata0:7077 --executor-memory 3g<br></span></span></p>
<p><span style="font-family:'Times New Roman';"><span style="font-size:18px;"><strong>访问</strong><br>
cd /opt/spark/spark<br>
bin/beeline<br>
!connect jdbc:hive2://bigdata0:10000<br>
!quit</span></span></p>
<p><span style="font-family:'Times New Roman';"><span style="font-size:18px;"><br></span></span></p>
            </div>
                </div>
---
layout:     post
title:      hive 分析nginx的access.log日志
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。转载请注明来自http://blog.csdn.net/lr131425					https://blog.csdn.net/lr131425/article/details/72457202				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>在hive 的安装目录启动hive后。</p>
<p></p><pre><code class="language-plain">[root@master hive]# ./bin/hive
which: no hbase in (/usr/tools/hadoop-2.7.3/bin/:/usr/java/jdk1.7.0_79/bin/:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin)
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/tools/hive/lib/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/tools/hadoop/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.apache.logging.slf4j.Log4jLoggerFactory]

Logging initialized using configuration in file:/usr/tools/hive/conf/hive-log4j2.properties Async: true
Hive-on-MR is deprecated in Hive 2 and may not be available in the future versions. Consider using a different execution engine (i.e. tez, spark) or using Hive 1.X releases.
</code></pre> 分析access.log日志的格式内容。
<p></p><pre><code class="language-plain">58.60.168.164 - - [07/Jan/2016:09:09:43 +0800] "GET / HTTP/1.1" 200 11250 "-" "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164 - - [07/Jan/2016:09:09:43 +0800] "GET /tomcat.css HTTP/1.1" 304 0 "http://120.55.190.57/" "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164 - - [07/Jan/2016:09:09:43 +0800] "GET /tomcat.png HTTP/1.1" 304 0 "http://120.55.190.57/" "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164 - - [07/Jan/2016:09:09:44 +0800] "GET / HTTP/1.1" 200 11250 "-" "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164 - - [07/Jan/2016:09:09:44 +0800] "GET /tomcat.css HTTP/1.1" 304 0 "http://120.55.190.57/" "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164 - - [07/Jan/2016:09:09:44 +0800] "GET /tomcat.png HTTP/1.1" 304 0 "http://120.55.190.57/" "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164 - - [07/Jan/2016:09:09:45 +0800] "GET / HTTP/1.1" 200 11250 "-" "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164 - - [07/Jan/2016:09:09:45 +0800] "GET /tomcat.css HTTP/1.1" 304 0 "http://120.55.190.57/" "Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"</code></pre><br>
在hive中创建表 <pre><code class="language-plain">hive&gt;CREATE TABLE  apachelog  (ipaddress STRING,identity STRING,t_user STRING,time 
STRING,request STRING,protocol STRING,status STRING,size STRING,referer STRING,agent STRING)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.RegexSerDe' 
WITH SERDEPROPERTIES ("input.regex" = "([^ ]*) ([^ ]*) ([^ ]*) (-|\\[[^\\]]*\\]) ([^ \"]*|\"[^\"]*) ([^ ]*\") (-|[0-9]*) (-|[0-9]*)(?: ([^ \"]*|\".*\") ([^ \"]*|\".*\"))?",
"output.format.string" = "%1$s %2$s %3$s %4$s %5$s %6$s %7$s %8$s %9$s %10$s")STORED AS TEXTFILE;</code></pre><br><p>然后把access.log内容导入表中。</p>
<p></p><pre><code class="language-plain">hive&gt;load data local inpath '/usr/toos/access.log' into table apachelog;</code></pre>local inpath表示的是本地文件系统的文件路径， 如果不加local 表示的是从hdfs文件系统导入 example ： load data inpath '/usr/dfs/access.log' into taable apachelog;
<p>此时可能会报错：</p>
<p></p><pre><code class="language-plain">Caused by: java.lang.RuntimeException: Map operator initialization failed
	at org.apache.hadoop.hive.ql.exec.mr.ExecMapper.configure(ExecMapper.java:137)
	... 22 more
Caused by: org.apache.hadoop.hive.ql.metadata.HiveException: java.lang.ClassNotFoundException: Class org.apache.hadoop.hive.contrib.serde2.RegexSerDe not found
	at org.apache.hadoop.hive.ql.exec.MapOperator.getConvertedOI(MapOperator.java:329)
	at org.apache.hadoop.hive.ql.exec.MapOperator.setChildren(MapOperator.java:364)
	at org.apache.hadoop.hive.ql.exec.mr.ExecMapper.configure(ExecMapper.java:106)
	... 22 more
Caused by: java.lang.ClassNotFoundException: Class org.apache.hadoop.hive.contrib.serde2.RegexSerDe not found
	at org.apache.hadoop.conf.Configuration.getClassByName(Configuration.java:2101)
	at org.apache.hadoop.hive.ql.plan.PartitionDesc.getDeserializer(PartitionDesc.java:175)
	at org.apache.hadoop.hive.ql.exec.MapOperator.getConvertedOI(MapOperator.java:295)
	... 24 more</code></pre><br>
找不到class 类 REgexSerDe类，
<p>在hive的conf目录下找到 hive-env.sh打开，最后一行配置上 所需jar路径   在hive的安装目录lib下</p>
<p>export HIVE_AUX_JARS_PATH=/usr/tools/hive/lib<br></p>
<p>重新启动hive，再执行上面的load data操作。</p>
<p>load data成功之后，可以简单查询下。</p>
<p></p><pre><code class="language-plain">hive&gt; select ipaddress,time,agent from apachelog limit 5;
OK
58.60.168.164	[07/Jan/2016:09:09:43 +0800]	"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164	[07/Jan/2016:09:09:43 +0800]	"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164	[07/Jan/2016:09:09:43 +0800]	"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164	[07/Jan/2016:09:09:44 +0800]	"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
58.60.168.164	[07/Jan/2016:09:09:44 +0800]	"Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.106 Safari/537.36"
Time taken: 0.143 seconds, Fetched: 5 row(s)
</code></pre><br>
接下来做个简单的统计查询，查询ip访问次数降序排列（此处是把结果输出到本地文件系统的目录 /usr/tools/hive/output目录，字段之间用，号分割显示）
<p> 也可以直接执行后面的select....输出到控制台。</p>
<p></p><pre><code class="language-plain">hive&gt; INSERT OVERWRITE local DIRECTORY '/usr/tools/hive/output' row format delimited
fields terminated by ',' select ipaddress ,count(ipaddress) as count from apachelog group by ipaddress order by count desc;</code></pre>
<p>控制台运行过程如下。</p>
<img src="https://img-blog.csdn.net/20170518092357513" alt=""><br><p><br></p>
<p>也可以通过hadoop的web管理页面查看mapreduce的运行情况</p>
<p><img src="https://img-blog.csdn.net/20170518092535576" alt=""><br></p>
<p><br></p>
<p>运行结果：（，号前面ipaddress，后面是访问次数。此次分析的只是很少的一个片段的access.log日志）</p>
<p><img src="https://img-blog.csdn.net/20170518093759958" alt=""><br><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
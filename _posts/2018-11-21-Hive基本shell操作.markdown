---
layout:     post
title:      Hive基本shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/aryoyo/article/details/80959522				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>用户手册：<br><a href="https://cwiki.apache.org/confluence/display/Hive/Home#Home-UserDocumentation" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/Home#Home-UserDocumentation</a></p>

<p>1&gt;<br>
java客户端操作hive需要启动hive远程服务：hiveserver2<br>
https://cwiki.apache.org/confluence/display/Hive/Setting+Up+HiveServer2<br>
$HIVE_HOME/bin/hive --service hiveserver2</p>

<p>2&gt;参考：<a href="https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline%E2%80%93CommandLineShell" rel="nofollow">beeline shell</a><br>
Hiveserver2 支持一个新的命令行Shell为Beeline，它是基于SQLLine CLI的JDBC客户端。<br>
Beeline支持嵌入模式(embedded mode)和远程模式(remote mode)。在嵌入式模式下，<br>
运行嵌入式的Hive(类似下面的Hive CLI)，而远程模式可以通过Thrift连接到独立的HiveServer2进程上.<br>
进入beeline命令行如下：<br>
     1. $HIVE_HOME/bin/hive --service hiveserver2<br>
     2.<br>
     修改hadoop 配置文件 etc/hadoop/core-site.xml,加入如下配置项<br>
      &lt;property&gt;<br>
      &lt;name&gt;hadoop.proxyuser.root.hosts&lt;/name&gt;  ----root 换成自己环境中的用户<br>
      &lt;value&gt;*&lt;value&gt;<br>
      &lt;/property&gt;<br>
      &lt;property&gt;<br>
      &lt;name&gt;hadoop.proxyuser.root.groups&lt;/name&gt;<br>
      &lt;value&gt;*&lt;/value&gt;<br>
       &lt;/property&gt;<br>
     3.  $HIVE_HOME/bin/beeline</p>

<pre class="has">
<code class="language-plain">[root@rhel64-64bit bin]# beeline
Beeline version 2.3.3 by Apache Hive
beeline&gt;  !connect jdbc:hive2://127.0.0.1:10000
Connecting to jdbc:hive2://127.0.0.1:10000
Enter username for jdbc:hive2://127.0.0.1:10000: root
Enter password for jdbc:hive2://127.0.0.1:10000: ********
Connected to: Apache Hive (version 2.3.3)
Driver: Hive JDBC (version 2.3.3)
Transaction isolation: TRANSACTION_REPEATABLE_READ
0: jdbc:hive2://127.0.0.1:10000&gt; show databases;
+----------------+
| database_name  |
+----------------+
| default        |          
| yoyo2          |
+----------------+
3 rows selected (25.382 seconds)
0: jdbc:hive2://127.0.0.1:10000&gt;
</code></pre>

<p>3&gt;<br>
进入hive cli模式操作hive：<br>
$HIVE_HOME/bin/hive<br>
或者<br>
hive --service cli 也可以进入cli模式<br>
hive --help查看hive的可选参数<br>
hive -S 静默模式不输出mapre过程<br>
打开debug模式：<br>
hive -hiveconf hive.root.logger=DEBUG,console</p>

<p>hive 命令主要分为以下几类：<br>
       进入与退出hive交互：hive/quit/exit<br>
       参数设置：set/reset<br>
       资源文件管理：add/list/delete<br>
       执行shell命令：！操作系统命令  <br>
       hdfs文件操作命令： dfs -ls /dfs -cat<br>
       执行外部文件：如source file<br>
       hiveQL:DDL/DML语句等</p>

<p><strong><u>参数设置</u></strong>：set/reset(这里修改只对该会话生效，如要永久生效需要修改hive-site.xml文件)</p>

<pre class="has">
<code class="language-plain">       set;列出所有参数,
       set map使用tab键 和 linux 一样
        hive -e "set;"|grep task
        hive&gt; set mapreduce.job.reduces; ---查看原始值
                  mapreduce.job.reduces=-1
        hive&gt; set mapreduce.job.reduces=10; ---修改为10
        hive&gt; set mapreduce.job.reduces;  ---查看修改成功
                  mapreduce.job.reduces=10

        hive&gt; reset mapreduce.job.reduces;---重置参数
        hive&gt; set mapreduce.job.reduces;  ----参看重置成功
        mapreduce.job.reduces=-1

        hive&gt; set hive.cli.print.current.db=true;---显示当前的数据库
        hive (default)&gt; 
        hive&gt; set hive.fetch.task.conversion; ---简单查询不需要转换成mapr作业
                  hive.fetch.task.conversion=more
     
        hive -e "set;" |grep map ---查看包含map的参数</code></pre>

<p><strong><u>执行shell命令</u></strong>：！命令  例如：！pwd<br>
        hive (default)&gt; !pwd;  ---输出就是在linux上执行pwd的结果一样<br>
        /root/Desktop</p>

<p><strong><u>hdfs文件操作命令</u></strong>： dfs -ls /dfs -cat<br>
        dfs -ls -R /user;</p>

<p><strong><u>执行外部文件</u></strong>：如source file<br>
        source my.sql;mysql里面就是select 语句如：select * from table1；</p>

<p><strong><u>hiveQL语句</u></strong>：</p>

<pre class="has">
<code class="language-plain">    show functions； ---查看内置函数
    select current_timestamp();---使用函数；

也可以自定义函数：
需要继承org.apache.hadoop.hive.ql.UDF ，需要实现evaluate函数支持重载
1.先开发一个简单的Java类
import org.apache.hadoop.hive.ql.exec.UDF;
public final class nadd extends UDF {
public Integer evaluate(Integer a, Integer b) {
if (null == a || null == b) {
return null;
} return a + b;
}

2.添加jar包
     hive &gt;add jar /home/lan/jar/nadd.jar;
3.创建临时函数与开发好的class关联起来 
     hive &gt;CREATE TEMPORARY FUNCTION myadd  AS 'demo.udf.nadd';
4.使用自定义函数  
     hive &gt;SELECT myadd(num1, num2) FROM t2;
5.销毁临时函数：
     hive &gt;DROP TEMPORARY FUNCTION myadd;
</code></pre>

<pre class="has">
<code class="language-plain">DDL语句：
hive的数据类型：
https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-NumericTypes
复杂的数据类型例子：
   array类型：
   create table tt（id int,  name string,    grades  array&lt;map&lt;string,float&gt;&gt;  ）;
   表数据为：  1,  tom,  [&lt;'chinese',90&gt;,   &lt;'english',96&gt;,    &lt;'music',79&gt;]

   struct类型：
   create table tt（id int,   info struct&lt;name:tring,age:int,sex:string&gt;）;
   表数据为： 1, {'tom', 13, 'F' }

创建内部表：
create table t1 ( id  int,name string,gender string) ---创建的是内部表
row format delimited fields terminated by ','        ---表字段以逗号分隔存放在文件中
location '/input'                                    ----指定表存放位置，
                                                         默认是放在hdfs的 /user/hive/warehouse/目录下的
partitioned by (gender string)                       ----指明为分区表，在hdfs的/user/hive/warehouse/t1会为分区生成不同目录
clustered by(id) into 3 buckets;                     ----指明为桶表，Hive里的分桶=MapReduce中的分区，而Hive中的分区只是将数据分到了不同的                                                         文件夹

创建外部表：只是建立和hdfs上的文件的链接关系，drop table并不能把hdfs上的文件删除
create external table t1 ( id  int,name string,gender string)
location '/input'； ---与hdfs上的/input下面的文件建立链接关系

创建表的时候同时导入数据： 也可以使用 load
create table t2 
row format delimited fields terminated by ',' 
as select * from t1;

修改表结构：
alter table t1 add columns(score int);</code></pre>

<pre class="has">
<code class="language-plain">DML语句：
hive是一个高延时用于海量数据分析的数据仓库，它不支持单条数据插入，新版本有支持单条记录的修改，
只是限制颇多只是针对 tables that support ACID如下：
https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML#LanguageManualDML-Update

批量导入数据：
---从本地/hive/1.txt导入数据到表t1中的gender='M'分区中，如果是hdfs上文件则不用加local参数
load data local inpath '/hive/1.txt' overwrite into table t1 partition (gender='M');
insert into t1 partition (gender='M') select * from t2 where gender='M';

查询语句：大多与mysql还是类似的：
select * from t1 where name='tom';
explain select * from t1 where name='tom'; 输出结果是从下往上读</code>
</pre>

<p>4&gt;使用sqooq导入mysql数据到hive中：</p>

<pre class="has">
<code class="language-plain">bin/sqoop import \
--connect jdbc:mysql://127.0.0.1:3306/testdb \
--username root2 \
--password password2 \
--query 'select * from t2 where age&gt;12 and $CONDITIONS LIMIT 20' \
--num-mappers 1 \
--target-dir '/sqooq/t2' \
--hive-tables t2
</code></pre>

<p> </p>            </div>
                </div>
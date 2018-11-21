---
layout:     post
title:      第39天： UDF函数  user defined function
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Hive学习实战   <br>
--------------------------------------------------------------<br>
Hive从入门到实战【40讲】---笔记记录<br>
--------------------------------------------------------------<br>
 <br>
hive命令<br>
1、show tables;<br>
2、show databases;<br>
3、 desc login; ---查看表结构。<br>
4、 show partitions test5; --查看分区<br><br>
create table login_struct(<br>
 ip string,<br>
 user struct&lt;uid:bigint,name :string&gt;<br>
 )<br>
 partitioned by (dt string)<br>
 row format delimited<br>
 fields terminated by ','<br>
 collection items terminated by '|'<br>
 stored as textfile;<br><br><br>
load data local inpath<br>
'/hadoop/hive0.9/testHive/login_struct.txt'<br>
into table login_struct  partition (dt='2015-02-07');<br><br>
第38天： UDF函数<br><br>
 hive进行UDF的开发非常简单<br>
 此处说的UDF为<br>
 Temporary的function<br>
 <br>
 背景：<br>
 支持用户定制：<br>
 1、文件格式 text File  Sequence File<br>
 2、内存中的数据格式，java integer string<br>
 3、用户提供的map、reduce脚本，不管什么语言，<br>
 4、用户自定义的函数：substr，trim<br>
 5、用户自定义的聚合函数 sum average <br><br><br>
UDF<br>
定义： UDF（user-defined-function)<br>
用户自定义的函数对数据进行处理<br><br>
用法<br>
1、udf函数可以直接应用于select语句，对<br>
查询结果做格式化处理后，在输出内容。<br><br>
2、编写udf函数的时候，需要注意一下几点<br>
 a 自定义的UDF需要集成org.apache.hadoop.hive.ql.UDF<br>
 b 需要实现evaluate函数<br>
 c evaluate 函数支持重载。<br><br><br><br>
UDF步骤<br><br>
1、把程序打包jar包放入到目标机器上去、<br>
2、进入hive，操作 hive&gt;add jar /opt/test/jar/udf_test.jar<br>
3、创建临时函数：hive&gt;create  temporary function<br>
 add_test as 'hive.udf.add';<br><br>
 查询HQL语句<br><br>
 select　add_test('abc') from test;<br>
 4、销毁临时函数<br>
    hive&gt; drop temporary function add_test;<br><br><br><br><br><br><br>
/hadoop/hive/testhivew/add_test2.jar<br>
create  temporary function<br>
 add_test as 'hive.AddTest';<br><br><br>
select add_test(1,2) from a2;<br><br><br><br>
select add_test(ip,user) from a2;<br><br>            </div>
                </div>
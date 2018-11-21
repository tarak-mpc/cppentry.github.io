---
layout:     post
title:      第27天：     lateral View语法
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
第27天：     lateral View语法<br><br>
lateral view 用于split explode等<br>
UDTF一起使用。<br>
它能够将一行数据拆成多行数据，<br>
在此基础上可以对拆分后的数据进行聚合<br>
lateral view首先为原始表的每行调用<br>
UDTF ，UDTF会把一行拆分<br>
成一或者多行，lateral view<br>
在把结果组合，产生一个支持别名表的虚拟表。<br><br><br><br>
例子<br><br>
create table pageAds(<br>
 pageid  string,<br>
  ad_list array&lt;int&gt;<br>
 )<br>
  <br>
 row format delimited<br>
 fields terminated by ','<br>
 collection items terminated by ':'<br>
 <br><br><br>
front_page,1:2:3<br>
contact_page,3:4:4<br><br><br><br>
load data local inpath<br>
'/hadoop/hive/testhivew/a3.txt'<br>
into table pageAds   ;<br><br><br><br>
select  pageid, adid<br>
from pageAds lateral view  <br>
explode(ad_list) adTable as adid;<br><br><br>
hive&gt; select * from pageAds;<br>
 <br>
front_page    [1,2,3]<br>
contact_page    [3,4,4]<br><br><br>
经过latera view explode之后得到的结果<br>
front_page    1<br>
front_page    2<br>
front_page    3<br>
contact_page    3<br>
contact_page    4<br>
contact_page    4<br><br>
把列转为行了。。牛逼。<br><br><br>
接下来是聚和统计<br>
select adid, count(0)<br>
from pageAds lateral view <br>
explode (ad_list) adTable as adid<br>
group by adid;<br>
结果<br>
2    1<br>
4    2<br>
1    1<br>
3    2<br><br>
------------------------------------------<br><br>
------------------------------------------<br><br>
create table baseTable(<br>
 col1  array&lt;int&gt;,<br>
  col2 array&lt;string&gt;<br>
 )<br>
  <br>
 row format delimited<br>
 fields terminated by ','<br>
 collection items terminated by ':'<br>
 <br><br>
 <br>
load data local inpath<br>
'/hadoop/hive/testhivew/a4.txt'<br>
into table baseTable   ;<br><br>
第一列拆分<br>
select myCol1,col2  <br>
from baseTable lateral view <br>
explode (col1) adTable as myCol1 ;<br><br><br><br>
第二列拆分分<br>
select myCol1,myCol2  <br>
from baseTable lateral view <br>
explode (col1) adTable as myCol1 <br><br>
 lateral view <br>
explode (col2) adTable as myCol2 ;<br><br><br>            </div>
                </div>
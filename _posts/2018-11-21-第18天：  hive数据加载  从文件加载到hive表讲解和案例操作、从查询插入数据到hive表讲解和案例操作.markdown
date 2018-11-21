---
layout:     post
title:      第18天：  hive数据加载  从文件加载到hive表讲解和案例操作、从查询插入数据到hive表讲解和案例操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Hive学习实战   <br>
--------------------------------------------------------------<br>
Hive从入门到实战【40讲】---笔记记录<br>
--------------------------------------------------------------<br>
 <br>
hive命令<br>
1、show tables;<br>
2、show databases;<br>
3、 desc login; ---查看表结构。<br>
4、 show partitions test5; --查看分区<br><br><br>
第18天：  hive数据加载  从文件加载到hive表讲解和案例操作、从查询插入数据到hive表讲解和案例操作<br><br><br>
insert overwrite table tablname1 [partition ]<br>
if not existes   select_statement1 from statement;<br><br><br>
从别的表查询，插入到其他表中。<br><br><br>
从查询插入数据到hive表<br>
示例：<br>
insert overwrite table login_user  select destinct uid from<br>
login_log  <br>
从登录日志表查询登录用户<br>
插入到表login_user中，如果login_user以有数据，则覆盖，<br>
否则创建<br><br><br>
insert overwrite table  login user partiton(dt=..)<br>
selct  ...<br><br><br><br><br>
create table login_user(<br>
  uid string <br>
  <br>
)<br>
partitioned by (dt string)<br>
 <br>
stored as textfile; <br><br><br><br><br>
insert overwrite table  login_user<br>
partition(dt='20150207') <br>
select distinct uid from login where dt='20150207'<br><br><br><br><br><br><br><br><br>
create table userinfo(<br>
  id int,<br>
  name string<br>
)<br>
 <br>
row format delimited<br>
fields terminated by ','<br>
stored as textfile; <br>
 <br>
create table mutil1(<br>
  id int,<br>
  name string<br>
 )<br>
row format delimited<br>
fields terminated by ','<br>
stored as textfile; <br><br><br>
create table  mutil2(<br>
  sumid int,<br>
  name string<br>
) <br>
row format delimited<br>
fields terminated by ','<br>
stored as textfile; <br><br><br>
load data local inpath '/hadoop/hive0.9/testHive/userinfo.txt'<br>
into table userinfo;<br><br><br>
-------<br>
同事插入两张表中。<br>
------<br>
from  userinfo<br>
insert overwrite table mutil1  select id ,name<br>
insert overwrite table mutil2 <br>
select count(distinct id),name group by name;
            </div>
                </div>
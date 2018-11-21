---
layout:     post
title:      hadoop,hive常用指令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="hadoophdfs_0"></a>hadoop-hdfs常用指令</h1>
<h2><a id="1_1"></a>1.安全模式常用操作命令：</h2>
<p>hdfs dfsadmin -safemode leave //强制 NameNode 退出安全模式<br>
hdfs dfsadmin -safemode enter //进入安全模式<br>
hdfs dfsadmin -safemode get //查看安全模式状态<br>
hdfs dfsadmin -safemode wait //等待，一直到安全模式结束</p>
<h2><a id="2linux_6"></a>2.linux保证时间同步</h2>
<p>ntpdate ‘时间服务器’   如：ntpdate <a href="http://ntp1.aliyun.com" rel="nofollow">ntp1.aliyun.com</a></p>
<h2><a id="3_8"></a>3.网页访问验证：</h2>
<p>hdfs的网页：	namenode主机名：50070<br>
hadoop01:50070<br>
yarn的网页：resourcemanager的主机名：8088<br>
hadoop03:8088</p>
<h2><a id="4_13"></a>4.单独启动某个进程</h2>
<p>hdfs相关进程：<br>
<a href="http://hadoop-daemon.sh" rel="nofollow">hadoop-daemon.sh</a> start namenode<br>
<a href="http://hadoop-daemon.sh" rel="nofollow">hadoop-daemon.sh</a> start datanode<br>
yarn相关进程：<br>
<a href="http://yarn-daemon.sh" rel="nofollow">yarn-daemon.sh</a> start resourcemanager<br>
<a href="http://yarn-daemon.sh" rel="nofollow">yarn-daemon.sh</a> start nodemanager</p>
<h2><a id="5linuxjar__20"></a>5.linux运行jar包 指令</h2>
<p>右键-export-jar.file-选择要打成jar包的文件<br>
01          02       03           04                   05        06<br>
hadoop jar wc.jar mr01.Driver /words.txt /out02<br>
#03代表上传的jar包名<br>
#04代表主类的全限定名称<br>
#05代表输入路径（对应主类输入路径的args(0)参数）<br>
#06代表输出路径（对应主类输出路径的args(1)参数）</p>
<h2><a id="6_28"></a>6、查看是否丢失数据的指令</h2>
<p>hadoop fskc /         查看根目录下所有文件的健康状况，是否有损坏的数据块</p>
<h1><a id="hive_30"></a>hive常用命令</h1>
<h2><a id="1_31"></a>1.启动服务为后台进程</h2>
<p>1).nohup hiveserver2 1 &gt;&gt; /home/hadoop/stand.log<br>
2 &gt;&gt; /home/hadoop/error.log &amp;<br>
2).nohup hiveserver2 1&gt; /home/hdp01/lg.log 2&gt;&amp;1 &amp;</p>
<h2><a id="2_35"></a>2.创建表的格式</h2>
<p>1.CREATE [EXTERNAL] TABLE [IF NOT EXISTS] table_name<br>
[(col_name data_type [COMMENT col_comment], …)]<br>
[COMMENT table_comment]  //指定的是表的描述信息<br>
[PARTITIONED BY (col_name data_type [COMMENT col_comment], …)]//分区信息<br>
[CLUSTERED BY (col_name, col_name, …)//分桶信息<br>
[SORTED BY (col_name [ASC|DESC], …)] INTO num_buckets BUCKETS]<br>
[ROW FORMAT row_format]<br>
[STORED AS file_format]<br>
[LOCATION hdfs_path]</p>
<p>2.create table t_user(id string, name string) row format delimited fields terminated by ‘,’;</p>
<h2><a id="3_48"></a>3.建表案例</h2>
<p>建表案例：<br>
hive中大小写不敏感的<br>
95002,刘晨,女,19,IS<br>
1.建一个普通的内部表<br>
create table if not exists student(id int,name string,sex string,age int comment ‘19-23’,department string) comment ‘1805student’ row format delimited fields terminated by ‘,’ STORED as TEXTFILE LOCATION ‘/user/hive/testhive’;<br>
2.建一个外部表<br>
create external table if not exists student_external(id int,name string,sex string,age int comment ‘19-23’,department string) row format delimited fields terminated by ‘,’;<br>
注意：没有一个表可以同时既是内部表又是外部表<br>
3.建一个分区表   可以和内部表或外部表同时存在<br>
分区字段作为过滤条件的时候，减少查询过程中的数据的扫描范围<br>
create external table student_ptn(id int,name string,sex string,department string) PARTITIONED BY(age int COMMENT ‘partition ziduan’) row format delimited fields terminated by ‘,’;<br>
添加分区:<br>
alter table student_ptn add partition(age=17);<br>
4.建一个分桶表<br>
create EXTERNAL table student_buk(id int,name string,sex string,age int,department string) CLUSTERED by (name) SORTED by(age desc) into 3 BUCKETS row format delimited fields terminated by ‘,’;<br>
SORTED by指定的是每一个桶中的排序规则<br>
5.ctas建表<br>
create table as select …   from …<br>
student:<br>
load data inpath ‘/stuin’ into table student;<br>
create table student_d20 as select * from student where age&gt;20;<br>
select * from student_d20;<br>
6.表复制  like<br>
只会复制表结构   不会进行表的数据的复制<br>
create table stu_like like student;</p>
<h2><a id="4_75"></a>4.删除数据库：</h2>
<p>空数据库：drop database dbname;<br>
删除包含表的数据库：drop database dbname cascade;<br>
查看hive所有内置函数：show functions;<br>
查看某一函数的具体使用方法：desc function extended functionname(如:desc function extended explode，查看爆裂函数使用方法);</p>
<h2><a id="5Hive_80"></a>5.Hive中内部表与外部表的区别：</h2>
<p>1）创建表时：创建内部表时，会将数据移动到数据仓库指向的路径；若创建外部表，仅记录数据所在的路径， 不对数据的位置做任何改变。<br>
2）删除表时：在删除表的时候，内部表的元数据和数据会被一起删除， 而外部表只删除元数据，不删除数据。这样外部表相对来说更加安全些，数据组织也更加灵活，方便共享源数据。</p>
<h2><a id="6hive_84"></a>6.查看hive所有内置函数</h2>
<p>show functions;</p>
<h2><a id="7hive_87"></a>7.hive表的数据导入</h2>
<p>1.load方式<br>
load data [local] inpath ‘’ into table tablename;<br>
local:本地数据加载<br>
如果数据从本地加载  需要添加这个关键字<br>
如果数据从hdfs进行加载则不需要添加这个关键字<br>
从本地：<br>
load data local inpath ‘/home/hadoop/apps/stu’ into table student01;<br>
相当于将本地的文件 进行拷贝到student01表的hdfs的存储目录下<br>
2.insert方式<br>
1）insert into [table] name values();<br>
2）单重数据插入  一次插入多条数据<br>
insert into table tablename select … from … where …<br>
insert into table stu_like01 select * from student01 where age=18;   扫描一次<br>
insert into table stu_like01 select * from student01 where age=19;   扫描一次<br>
insert into table stu_like01 select * from student01 where age=20;   扫描一次<br>
3）多重数据插入<br>
from tablename1<br>
insert into table tablename select … where …<br>
insert into table tablename select … where …<br>
对原始数据表只扫描一次<br>
分别插入数据到两个表中<br>
from student01<br>
insert into table stu_like01 select * where age=18<br>
insert into table stu_like02 select * where age=20;</p>
<h2><a id="8hive_112"></a>8.hive的数据导出</h2>
<p>从hive表将数据导出到本地或hdfs<br>
1）单重数据导出：<br>
INSERT OVERWRITE [LOCAL] DIRECTORY directory1 select_statement<br>
local：将数据导出到本地<br>
将student01表中的年龄小于19的数据导出到本地/home/hadoop/apps/stu_18<br>
insert OVERWRITE local DIRECTORY ‘/home/hadoop/apps/stu_18’ select * from student01 where age&lt;19;<br>
2)多重数据导出<br>
FROM from_statement<br>
INSERT OVERWRITE [LOCAL] DIRECTORY directory1 select_statement1<br>
[INSERT OVERWRITE [LOCAL] DIRECTORY directory2 select_statement2] …<br>
from student01<br>
insert OVERWRITE local DIRECTORY ‘/home/hadoop/apps/stu_19’ select * where age=19<br>
insert OVERWRITE local DIRECTORY ‘/home/hadoop/apps/stu_20’ select * where age=20;</p>
<h2><a id="9hivesemi_join_126"></a>9.hive中的半连接semi join</h2>
<p>半连接:出现的意义是补充hive中一个语句的缺陷<br>
判断左表中的关联的字段是否在右表中出现  如果出现则返回<br>
left semi join  左半连接<br>
select<br>
*<br>
from testa a left semi join testb b on <a href="http://a.id=b.id" rel="nofollow">a.id=b.id</a>;<br>
相当于内连接  只取左半表<br>
在sql语句中有一个语法  in/exists   字段1 in (ziduan …)<br>
在hive中多 对in、exits这种语法支持很薄弱  支持但是效率很低 原因就是key的不好确定  这个时候就出现了半连接</p>
<h2><a id="10hivegroup_by4by_136"></a>10.hive查询语句中的group by和常用的4个排序by</h2>
<h2><a id="1hivegroup_by_137"></a>1.hive中的group by:</h2>
<p>1)执行的顺序select语句之前  select后给的字段的别名是不可以使用的<br>
student01   求每一个部门的年龄之和<br>
select<br>
department dept,sum(age) age<br>
from student01 group by dept   错错错</p>
<p>select<br>
department dept,sum(age) age<br>
from student01 group by department;  正确的</p>
<p>2)如果hql语句中有group by   那么select后面只能跟两种内容<br>
1）group by的字段<br>
2）group by的聚合函数   sum   count    avg…<br>
select<br>
department dept,name,sum(age) age<br>
from student01 group by department;   错的</p>
<h2><a id="24by_155"></a>2.常用4个排序by</h2>
<h3><a id="order_by_156"></a>order by：</h3>
<p>hive中的排序   这个排序指的值对hive的表数据进行全排序<br>
对hive表student01表  按照年龄排序  升序<br>
select * from student01 order by age;</p>
<h3><a id="sort_by_160"></a>sort by:</h3>
<p>局部排序    部分排序<br>
只对每一个reducetask的输出结果进行排序<br>
select * from student01 sort by age;<br>
当reducetask只有一个的时候 相当于全局排序 这个时候和order by效果是一样得<br>
设置reducetask个数：<br>
set mapreduce.job.reduces=3<br>
select * from student01 order by age;//仍然是全局排序<br>
select * from student01 sort by age;<br>
对于sort by  注意   reducetask的数据划分是随机取的一个列</p>
<h3><a id="distribute_by_____170"></a>distribute by：分桶  或  分组</h3>
<p>类似于job.setNumReducetask()<br>
不会进行排序,作用仅仅是分  将数据按照distribute by的字段进行分开			最终分成几部分取决于reducetask的个数  distribute by指定的仅仅是用于分的字段是哪一个      每一个reducetask中数据分规则：distribute by后面的字段%4<br>
注意：distribute by后面的字段数值：distribute by后面的字段%4<br>
distribute by后面的字段非数值：distribute by后面的字段。hashCode%4<br>
set mapreduce.job.reduces=4；<br>
select * from student01 distribute by age;<br>
select * from student01 distribute by age sort by age;</p>
<h3><a id="cluster_by_178"></a>cluster by</h3>
<p>当distribute by和sort by的字段一样的时候=distribute by+sort by<br>
先进行数据的分组：分组依据cluster by后面的字段<br>
在每一个组进行排序，排序的规则cluster by后面的字段<br>
select * from student01 cluster by age;<br>
当distribute by+sort by的字段相同时可以用cluster by代替两者，但若两者by的字段不相同则不可以用cluster by替代的</p>
<h2><a id="11hive_184"></a>11.hive中的数据类型相关知识</h2>
<p>基本数据类型：int(tinyint,smallint,int,bigint)、float double、boolean、string、timestamp</p>
<p>复杂数据类型:有基本数据类型构成的<br>
array:数组数据类型  指定数组的类型的时候一定给泛型&lt;&gt;   集合list set<br>
map:映射   k-v<br>
struct:类似于java中的对象类型<br>
flowbean:手机号  上行流量  下行流量   总流量</p>
<h3><a id="array_192"></a>array类型：</h3>
<p>数据：<br>
id	name	hobby<br>
1	xh	sing,movies,basketball<br>
2	ls	football,food<br>
3	ww	sleep,walk,food,chaojia<br>
建表：<br>
create table test_array(id int,name string,hobby array) row format delimited fields terminated by ‘\t’ collection items terminated by ‘,’;<br>
<strong>collection items terminated by:指定的是集合的每个元素之间的分割符</strong><br>
数据加载：<br>
load data local inpath ‘/home/hadoop/apps/test_array’ into table test_array;	<br>
数据查询：<br>
通过下标进行数据访问<br>
select hobby[0] from test_array;</p>
<h3><a id="map_207"></a>map类型：指定泛型</h3>
<p>数据：<br>
id	name piaofang<br>
1	luhan	paonan:1000,dadianying:100<br>
2	hunagbo	yichuhaoxi:10000,shansheng:20000,taijun:250000<br>
3	xuzheng	yaoshen:350000,zhubajie:60000</p>
<p>建表：<br>
注意：建表时候指定分割符   ===  从外向内依次指定<br>
create table test_map(id int,name string,piaofang map&lt;string,bigint&gt;) row format delimited fields terminated by ‘\t’ collection items terminated by ‘,’ map keys terminated by ‘:’;<br>
数据导入：<br>
load data local inpath ‘/home/hadoop/apps/test_map’ into table test_map;<br>
数据查询：<br>
select piaofang[‘paonan’] from test_map;</p>
<h3><a id="struct_222"></a>struct:数据比较规程，数据中的每一个字段对应的含义一样</h3>
<p>id   name   age<br>
1	zs		23<br>
2	ls		32<br>
class {<br>
id;<br>
name;<br>
age;<br>
}<br>
数据：<br>
id	name	info<br>
1	zs	xian,20,680<br>
2	zl	wuhan,18,520<br>
3	xh	shenzheng,17,490<br>
建表：<br>
create table test_struct(id int,name string,info struct<a>chushengdi:string,age:int,score:int</a>) row format delimited fields terminated by ‘\t’ collection items terminated by ‘,’;<br>
collection items terminated by ‘,’:指定struct的每一个属性之间的分割符<br>
加载数据：<br>
load data local inpath ‘/home/hadoop/apps/test_struct’ into table test_struct;<br>
数据查询：<br>
通过.进行数据访问<br>
select info.chushengdi,info.score from test_struct;</p>
<h2><a id="hive_244"></a>hive的窗口函数</h2>
<p>用于解决  topN   动态分组的问题   累加计算的问题等<br>
样例数据：<br>
userid	userdate	count<br>
A       2015-01 5<br>
A       2015-01 15<br>
B       2015-01 5<br>
A       2015-01 8<br>
B       2015-01 25<br>
A       2015-01 5<br>
A       2015-02 4<br>
A       2015-02 6<br>
B       2015-02 10<br>
B       2015-02 5<br>
A       2015-03 16<br>
A       2015-03 22<br>
B       2015-03 23<br>
B       2015-03 10<br>
B       2015-03 11<br>
1)over子句<br>
相当于单独的一个计算语句 这个计算语句相当于重新取数据进行计算 可以指定分组条件和排序规则的  不是一定需要同时指定的  按需指定<br>
over（distribute by   sort by）<br>
over（partition by   order by）<br>
1）聚合函数+over  版本2.1之后<br>
count   sum    avg   max   min<br>
这时候的聚合函数  是根据over子句指定的规则进行聚合的<br>
select userid,userdate,sum(count) over(distribute by userid,userdate) from user_info;<br>
select  userid,userdate,sum(count) from user_info group by userid,userdate;</p>
<p>A       2015-01 5       33<br>
A       2015-01 15      33<br>
A       2015-01 8       33<br>
A       2015-01 5       33<br>
A       2015-02 6       10<br>
A       2015-02 4       10<br>
A       2015-03 16      38<br>
A       2015-03 22      38<br>
B       2015-01 25      30<br>
B       2015-01 5       30<br>
B       2015-02 10      15<br>
B       2015-02 5       15<br>
B       2015-03 11      44<br>
B       2015-03 23      44<br>
B       2015-03 10      44</p>
<p>去重：<br>
select distinct userid,userdate,sum(count) over(distribute by userid,userdate) from user_info;<br>
A       2015-01 33<br>
A       2015-02 10<br>
A       2015-03 38<br>
B       2015-01 30<br>
B       2015-02 15<br>
B       2015-03 44</p>
<p>select distinct userid,userdate,sum(count) over(distribute by userid,userdate sort by userdate) from user_info;<br>
A       2015-01 33<br>
A       2015-02 10<br>
A       2015-03 38<br>
B       2015-01 30<br>
B       2015-02 15<br>
B       2015-03 44</p>
<p>2)分析函数：<br>
row_number<br>
rank<br>
dense_rank	<br>
3)over子句中可以指定统计的数据的范围<br>
用于指定聚合函数的统计的数据的范围的<br>
(ROWS | RANGE  指定行) BETWEEN   and指定行的范围的<br>
UNBOUNDED   无边界   向前到第一条数据  向后最后数据<br>
| [num]) PRECEDING   向前几行的数据<br>
CURRENT ROW    当前行<br>
[num]) FOLLOWING)  向后几行的数据<br>
没有指定需要统计的数据范围  默认是全部的数据<br>
select userid,userdate,count,sum(count) over(distribute by userid,userdate sort by userdate rows between CURRENT row and UNBOUNDED FOLLOWING) from user_info;</p>
<p>4)窗口函数和over子句连用<br>
lag（需要取的字段，偏移量，如果取不到  给的默认值）<br>
偏移量    取前面的<br>
lead     取后面的<br>
比赛数据分析<br>
这个人  赛成绩<br>
1	182828	6<br>
2	272873  7</p>
<p>select  userid,userdate,count,lag(count,1,0) over(distribute by userid,userdate sort by userdate) from user_info;<br>
select  userid,userdate,count,lead(count,1,0) over(distribute by userid,userdate sort by userdate) from user_info;<br>
first_value（需要获取的字段）   分组的第一个值<br>
last_value   分组的最后一个值<br>
select  userid,userdate,count,first_value(count) over(distribute by userid,userdate sort by count)  from user_info;		<br>
select  userid,userdate,count,last_value(count) over(distribute by userid,userdate sort by count)  from user_info;</p>
<h2><a id="mysql_335"></a>mysql写的顺序及执行顺序</h2>
<p>写的顺序：<br>
select …  from …where …group by…having…order by…limit<br>
执行顺序：<br>
from<br>
where<br>
group by  不能使用select后面的字段别名的  必须使用原声的<br>
having<br>
select<br>
order by   可以使用select后面的字段的别名的<br>
注：本博客内容后续会不断补充完善</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      hive hbase整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:SimSun;font-size:14px;"><span style="color:rgb(54,46,43);">      Hive和Hbase有各自不同的特征：hive是高延迟、结构化和面向分析的，hbase是低延迟、非结构化和面向编程的。Hive数据仓库在hadoop上是高延迟的。Hive集成Hbase就是为了使用hbase的一些特性。<br>
     Hive继承HBase可以有效利用HBase数据库的存储特性，如行更新和列索引等。在集成的过程中注意维持HBase jar包的一致性。Hive集成HBase需要在Hive表和HBase表之间建立映射关系，也就是Hive表的列和列类型与HBase表的列族及列限定词建立关联。每一个在Hive表中的域都存在与HBase中，而在Hive表中不需要包含所有HBase中的列。HBase中的rowkey对应到Hive中为选择一个域使用 :key 来对应，列族(cf映射到Hive中的其他所有域，列为(cf:cq)。<br></span><span style="color:rgb(54,46,43);"></span></span>
<p style="color:rgb(54,46,43);"><span style="font-family:SimSun;font-size:14px;"><img src="http://blog.csdn.net/dehu_zhou/article/details/53102772" alt="" style="border:none;"><br></span></p>
<p style="color:rgb(54,46,43);"><span style="color:rgb(255,0,0);"><span style="font-family:SimSun;font-size:14px;">下面来简单实战下：</span></span></p>
<p style="color:rgb(54,46,43);"><span style="color:rgb(51,51,255);"><span style="font-family:SimSun;font-size:14px;">创建<a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="color:rgb(223,52,52);">Hbase</a>表：</span></span></p>
<p style="color:rgb(54,46,43);"><span style="font-family:SimSun;font-size:14px;"><br></span></p>
<span style="font-family:SimSun;font-size:14px;"><span style="color:rgb(54,46,43);">create 'user1',{NAME =&gt; 'info',VERSIONS =&gt; 1}<br><br><span style="color:rgb(51,51,255);">向user表中插入一些数据：</span><br>
put 'user1','1','info:name','zhangsan'<br>
put 'user1','1','info:age','25'<br>
put 'user1','2','info:name','lisi'<br>
put 'user1','2','info:age','22'<br>
put 'user1','3','info:name','wangswu'<br>
put 'user1','3','info:age','21'<br><br><br><span style="color:rgb(51,51,255);">使用scan命令来查看下user表中的数据：</span><br>
scan 'user';<br><br><br><span style="color:rgb(51,51,255);">结果如下：</span><br>
ROW COLUMN+CELL<br>
1 column=info:age, timestamp=1476773051604, value=25<br>
1 column=info:name, timestamp=1476773046344, value=zhangsan<br>
2 column=info:age, timestamp=1476773051685, value=22<br>
2 column=info:name, timestamp=1476773051661, value=lisi<br>
3 column=info:age, timestamp=1476773052632, value=21<br>
3 column=info:name, timestamp=1476773051709, value=wangswu<br>
3 row(s) in 0.0610 seconds<br></span><span style="color:rgb(54,46,43);"></span></span>
<p style="color:rgb(54,46,43);"><span style="font-family:SimSun;font-size:14px;"><br></span></p>
<p style="color:rgb(54,46,43);"><span style="color:rgb(51,51,255);"><span style="font-family:SimSun;font-size:14px;">创建<a href="http://lib.csdn.net/base/hive" rel="nofollow" class="replace_word" title="Hive知识库" style="color:rgb(223,52,52);">Hive</a>中与HBase中对应的表：</span></span></p>
<span style="font-family:SimSun;font-size:14px;"><span style="color:rgb(54,46,43);">SET hbase.zookeeper.quorum=slave1<span style="color:rgb(255,0,0);">（主机名）;</span><br>
SET zookeeper.znode.parent=/hbase;<br>
ADD jar <span style="color:rgb(204,51,204);">/usr/tools/hive-1.2.1-bin/lib/hive-hbase-handler-1.2.1.jar;</span><br></span><span style="color:rgb(54,46,43);"></span></span>
<p style="color:rgb(54,46,43);"><span style="font-family:SimSun;font-size:14px;"><br></span></p>
<p style="color:rgb(54,46,43);"><span style="color:rgb(0,153,0);"><span style="font-family:SimSun;font-size:14px;">// user1表：</span></span></p>
<span style="color:rgb(54,46,43);"><span style="font-family:SimSun;font-size:14px;">CREATE EXTERNAL TABLE user1 (<br>
rowkey string,<br>
info map&lt;STRING,STRING&gt;<br>
) STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'<br>
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,info:")<br>
TBLPROPERTIES ("hbase.table.name" = "user1");<br><span style="color:rgb(0,153,0);">// user2表：</span><br>
CREATE EXTERNAL TABLE user2 (<br>
rowkey string,<br>
name string,<br>
age int<br>
) STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'<br>
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,info:name,info:age")<br>
TBLPROPERTIES ("hbase.table.name" = "user2");<br><br>
查询user1，user2表数据<br>
查询user1表数据<br>
select * from user1;<br><br>
结果如下<br>
OK<br>
1       {"age":"25","name":"zhangsan"}<br>
2       {"age":"22","name":"lisi"}<br>
3       {"age":"21","name":"wangswu"}<br>
Time taken: 0.467 seconds, Fetched: 3 row(s)<br><br>
查询user2表数据<br>
select * from user2;<br><br>
结果如下:<br>
OK<br>
1 zhangsan 25<br>
2 lisi 22<br>
3 wangswu 21<br>
Time taken: 0.087 seconds, Fetched: 3 row(s)<br><span style="color:rgb(51,51,255);">hive插入数据到hbase：</span><br>
INSERT INTO TABLE user1<br>
SELECT '4' AS rowkey,<br>
map('name','lijin','age','22') AS info<br>
from dual limit 1;</span></span>
            </div>
                </div>
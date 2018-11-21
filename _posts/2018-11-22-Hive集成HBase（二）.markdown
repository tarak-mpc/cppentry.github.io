---
layout:     post
title:      Hive集成HBase（二）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u010376788/article/details/50905476				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://blog.csdn.net/u010376788/article/details/50890622" rel="nofollow">Hive集成HBase（一）</a></p>
<p><a href="http://blog.csdn.net/u010376788/article/details/50905476" rel="nofollow">Hive集成HBase（二）</a></p>
<h1>1.添加依赖jar</h1>
在Hive启动时添加依赖jar：hive-hbase-handler-x.y.z.jar和编译hive-handler源码时依赖的jar。添加jar时，有两种方法：<br><ol><li>在Hive CLI启动时添加，</li><li>在Hive CLI启动后用ADD jar添加。</li></ol><div>对于单节点的HBase服务器，Hive CLI启动时：<br></div>
<div><pre><code class="language-plain">hive --auxpath  /root/lib/hive-hbase-handler-0.9.0.jar, /root/lib/hbase-0.92.0.jar, /root/lib/zookeeper-3.3.4.jar, /root/lib/guava-r09.jar --hiveconf hbase.master=hbase.yoyodyne.com:60000</code></pre><br>
对于单节点的HBase服务器，Hive CLI启动后，用ADD jar：<br></div>
<div>
<p></p>
<pre><code class="language-plain">ADD jar /root/lib/hive-hbase-handler-0.9.0.jar
/root/lib/hbase-0.92.0.jar
/root/lib/zookeeper-3.3.4.jar
/root/lib/guava-r09.jar;

SET hbase.master=hbase.yoyodyne.com:60000;
</code></pre><br>
对于分布式HBase服务器，Hive CLI启动时：<br><p></p>
</div>
<div><pre><code class="language-plain">hive --auxpath  /root/lib/hive-hbase-handler-0.9.0.jar, /root/lib/hbase-0.92.0.jar, /root/lib/zookeeper-3.3.4.jar, /root/lib/guava-r09.jar --hiveconf hbase.zookeeper.quorum=zk1.yoyodyne.com,zk2.yoyodyne.com,zk3.yoyodyne.com</code></pre><br>
对于分布式HBase服务器，Hive CLI启动后用ADD jar添加：<br></div>
<div><pre><code class="language-plain">ADD jar /root/lib/hive-hbase-handler-0.9.0.jar
/root/lib/hbase-0.92.0.jar
/root/lib/zookeeper-3.3.4.jar
/root/lib/guava-r09.jar;

SET hbase.zookeeper.quorum=zk1.yoyodyne.com,zk2.yoyodyne.com,zk3.yoyodyne.com;
</code></pre><br><h1>2.创建Hive表和HBase表的映射</h1>
Hive中的原表grades：<br><pre><code class="language-sql">CREATE TABLE grades(
	id int,
	name string,
	age int)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY ','
LINES TERMINATED BY '\n'</code></pre><br>
查出来数据如:<br></div>
<div><pre><code class="language-plain">hive&gt; SELECT * FROM grades;
OK
1	Tom	24
2	Bill	25
3	Alice	24
Time taken: 0.17 seconds, Fetched: 3 row(s)
</code></pre><br>
在创建Hive表和HBase表的映射关系时，会产生一个Hive中间表hive_hbase。<br></div>
<div><pre><code class="language-plain">CREATE TABLE hbase_table(
	key int, 
	name string, 
	age int) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,
	cf1:name，
	cf1:age")
TBLPROPERTIES ("hbase.table.name" = "xyz", "hbase.mapred.output.outputtable" = "xyz");
</code></pre><br><span style="color:#ff0000;"><strong>hbase.columns.mapping</strong></span>参数提供了特殊的功能，格式"hbase.columns.mapping" = ":key,ColumnFamily1:Column,...,ColumnFamilyN:Column "。使用“:key”映射行健，用户可以把映射行健的特殊列放置在任意位置。<br><strong><span style="color:#ff0000;">hbase.table.name</span></strong>这是属性是可选的，仅当用户想在Hive和HBase中使用不同名字的表名时才需要填写。如果使用相同的名字，则可以省略。<br>
执行完这段Hive脚本后，只是创建定义了Hive表和HBase表的映射关系，且Hive中间表和HBase表并无数据，因此并未有MapReduce启动。</div>
<div><strong><span style="color:#ff0000;">hbase.mapred.output.outputtable</span></strong>这个属性是可选的，仅当你想往这个表中插入数据时使用（这个属性在hbase.mapreduce.TableOutputFormat中使用到）。<br><br></div>
<div>
<h1>3.向HBase表插入数据</h1>
向HBase表插入数据的HiveQL语句，和Hive中把一张表的数据插入另一张表一样：<br><pre><code class="language-sql">INSERT OVERWRITE TABLE hbase_table SELECT * FROM grades;</code></pre><br>
虽然说是向Hive的中间表中插入数据，但是由于映射关系，所以在HBase表中也可以查到数据。<br><br></div>
<div>
<h1>4.多个列和列族的映射</h1>
如果有多个列和列族的话，在定义映射关系时，略微有一点儿变化：<br><p></p>
<pre><code class="language-sql">CREATE TABLE hbase_table(
	key int, 
	name string, 
	age int, 
	txt string) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = ":key,
	cf1:name,
	cf1:name,
	cf2:age)
TBLPROPERTIES ("hbase.table.name" = "xyz", "hbase.mapred.output.outputtable" = "xyz");
</code></pre><br>
插入数据：</div>
<div><pre><code class="language-sql">INSERT OVERWRITE TABLE hbase_table
SELECT id, name, age, cast(age as string)
FROM grades;
</code></pre><br><h1>5.Hive的Map映射HBase的列族</h1>
对于Hive表中的Map数据类型，要映射到HBase一个列族中，映射关系需要像如下定义：<br><pre><code class="language-sql">CREATE TABLE hbase_table(
	key int, 
	txt map&lt;string, string&gt;)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES (
"hbase.columns.mapping" = ":key,
	cf:)
TBLPROPERTIES ("hbase.table.name" = "xyz", "hbase.mapred.output.outputtable" = "xyz");
</code></pre><br>
此时，Hive中创建了hbase_table表，但是，是张空表：</div>
<div><pre><code class="language-plain">hive&gt; source hive_hbase.hql;
OK
Time taken: 0.041 seconds
OK
Time taken: 3.953 seconds
hive&gt; show tables;
OK
grades
hbase_table
Time taken: 0.021 seconds, Fetched: 2 row(s)
hive&gt; select * from hbase_table;
OK
Time taken: 1.122 seconds
</code></pre><br>
而映射到HBase中的表xyz也在HBase中创建了，但是也是空表：</div>
<div><pre><code class="language-plain">hbase(main):001:0&gt; list
TABLE                                                                                                                                
0 row(s) in 0.6350 seconds

=&gt; []
</code></pre><br>
然后，再插入数据，这时候会启动MapReduce任务：</div>
<div><pre><code class="language-sql">INSERT OVERWRITE TABLE hbase_table
SELECT id, map('name', name, 'age', cast(age as string))
FROM grades;
</code></pre><br>
此时，在查看Hive中的hbase_table表：</div>
<div><pre><code class="language-plain">hive&gt; source insert.hql;
OK
Time taken: 0.014 seconds
FAILED: SemanticException [Error 10044]: Line 3:23 Cannot insert into target table because column number/types are different 'hbase_table': Table insclause-0 has 2 columns, but query has 3 columns.
hive&gt; source insert.hql;
OK
Time taken: 0.04 seconds
Query ID = root_20160316001818_8ea198e2-f1ca-424d-b8c3-c329fc497c81
Total jobs = 1
Launching Job 1 out of 1
Number of reduce tasks is set to 0 since there's no reduce operator
Starting Job = job_1458112443618_0001, Tracking URL = http://slave-1:8088/proxy/application_1458112443618_0001/
Kill Command = /root/install/hadoop-2.4.1/bin/hadoop job  -kill job_1458112443618_0001
Hadoop job information for Stage-0: number of mappers: 1; number of reducers: 0
2016-03-16 00:19:16,175 Stage-0 map = 0%,  reduce = 0%
2016-03-16 00:19:28,328 Stage-0 map = 100%,  reduce = 0%, Cumulative CPU 2.75 sec
MapReduce Total cumulative CPU time: 2 seconds 750 msec
Ended Job = job_1458112443618_0001
MapReduce Jobs Launched: 
Stage-Stage-0: Map: 1   Cumulative CPU: 2.75 sec   HDFS Read: 255 HDFS Write: 0 SUCCESS
Total MapReduce CPU Time Spent: 2 seconds 750 msec
OK
Time taken: 38.091 seconds
hive&gt; select * from hbase_table;
OK
1	{"age":"24","name":"Tom"}
2	{"age":"25","name":"Bill"}
3	{"age":"24","name":"Alice"}
Time taken: 0.181 seconds, Fetched: 3 row(s)</code></pre><br>
再查看HBase中的xyz表：</div>
<div><pre><code class="language-plain">hbase(main):004:0&gt; scan 'xyz'
ROW                                COLUMN+CELL                                                                                       
 1                                 column=cf:age, timestamp=1458112767571, value=24                                                  
 1                                 column=cf:name, timestamp=1458112767571, value=Tom                                                
 2                                 column=cf:age, timestamp=1458112767571, value=25                                                  
 2                                 column=cf:name, timestamp=1458112767571, value=Bill                                               
 3                                 column=cf:age, timestamp=1458112767571, value=24                                                  
 3                                 column=cf:name, timestamp=1458112767571, value=Alice                                              
3 row(s) in 0.3680 seconds
</code></pre><br><h1>6.映射已经存在的HBase表</h1>
</div>
<div>对于已经存在的HBase表xyz，在建立映射关系时，仅仅多了“EXTERNAL”关键字：</div>
<div><pre><code class="language-plain">CREATE EXTERNAL TABLE hbase_table(
    id int,
    txt map&lt;string, string&gt;)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = "
    :key,
    cf:")
TBLPROPERTIES ("hbase.table.name" = "xyz", "hbase.mapred.output.outputtable" = "xyz");</code></pre><br>
将Hive表的数据导入HBase表xyz也和之前的操作一样：</div>
<div><pre><code class="language-plain">INSERT OVERWRITE TABLE hbase_table
SELECT id, map('name', name, 'age', cast(age as string))
FROM grades;</code></pre><br><h1>7.用Hive CLI操作HBase表</h1>
</div>
<div>我在公司做Hive表数据快速导入HBase调研中，发现这种HBaseIntegration方法的效率非常慢，导入一个30GB的表花费4小时的时间还没跑完（哈哈），更高效的方法我会在后续的博文<a href="http://blog.csdn.net/u010376788/article/details/51055352" rel="nofollow" style="color:rgb(255,0,0);font-size:18px;font-weight:bold;font-style:italic;">Bulkload
 Hive表到HBase</a><span style="font-size:10px;">中</span>详细介绍。这个HBaseIntegration还有一个非常重要的作用，就是HBase Shell命令太少，所以对表的操作不方便，因此这种方法可以把HBase表用Hive CLI访问。因此，我个人觉得HBaseIntegration这个方法更适用于Hive CLI高效访问HBase表。此时，不需要导入数据，因此，只需要建立映射关系即可：</div>
<div><pre><code class="language-plain">CREATE EXTERNAL TABLE hbase_table_1(
    id int,
    txt map&lt;string, string&gt;)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = "
    :key,
    cf:")
TBLPROPERTIES ("hbase.table.name" = "xyz", "hbase.mapred.output.outputtable" = "xyz");</code></pre><br>
现在HBase表xyz，就可以用Hive CLI来进行操作：</div>
<div>
<p></p>
<pre><code class="language-plain">hive&gt; select * from hbase_table_1 where id &gt; 1;
OK
2	{"age":"25","name":"Bill"}
3	{"age":"24","name":"Alice"}
Time taken: 0.211 seconds, Fetched: 2 row(s)</code></pre><br>
这样就可以对HBase表xyz，在Hive CLI中进行条件查询。
<p></p>
<div><br></div>
<strong><span style="font-size:18px;color:#ff0000;">注意：</span></strong></div>
<div>
<ol><li><span style="color:rgb(255,0,0);font-size:18px;"><strong>建立Hive表和HBase表之间的映射关系之后，只需要删除Hive表，同时与之映射的HBase表会自动“级联”删除。</strong></span></li><li><span style="color:rgb(255,0,0);font-size:18px;"><strong>多张Hive表关联一张HBase表也是可以的。</strong></span></li><li><span style="font-size:18px;"><strong><span style="color:#ff0000;">HBaseIntegration这种方法同样也适用与跨集群，如Hive在集群mycluster1，HBase在集群mycluster2（其中的奥妙在</span><em><a href="http://blog.csdn.net/u010376788/article/details/50890622" rel="nofollow"><span style="color:#33ffff;">Hive集成HBase（一）</span></a></em><span style="color:#ff0000;">中提到的hive-hbase-handler）。</span></strong></span></li></ol><br></div>
<div><br></div>
<div>参考文献：</div>
<div><a href="https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration</a><br></div>
<div><br></div>
<div><br></div>
            </div>
                </div>
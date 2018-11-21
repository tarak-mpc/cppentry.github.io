---
layout:     post
title:      hive_hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre style="background-color:rgb(250,250,250);border:1px solid rgb(226,226,226);color:rgb(72,72,72);">版本匹配最重要</pre>
<pre style="background-color:rgb(250,250,250);border:1px solid rgb(226,226,226);color:rgb(72,72,72);">
</pre>
<pre style="background-color:rgb(250,250,250);border:1px solid rgb(226,226,226);color:rgb(72,72,72);">hive-hbase
配置：
把hbase对应jar包替换掉hive/lib下的包，
cp protobuf-java-2.4.0a.jar /opt/hadoop/hive/lib

启动hive
hive --auxpath /opt/hadoop/hive/lib/hbase-0.94.10-security.jar,/opt/hadoop/hive/lib/hive-hbase-handler-0.10.0.jar

创建hbase的hive 外部表

hive&gt; CREATE EXTERNAL TABLE f2 (name string, dd double)        
    &gt; STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'  
    &gt; WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,percent:user_mer_dist")  
    &gt; TBLPROPERTIES("hbase.table.name" = "f2_test_out"); 
OK
Time taken: 1.279 seconds
hive&gt; select * from f2;
OK
13005400398_104440354113025_14007572    0.5
13005400398_898440354119665_96654013    0.5
13005400686_104440354113025_14007572    1.0
13005404751_104440354113025_14007572    1.0
13025423121_104440354113025_14007572    1.0
13025425645_104440354113025_14007572    1.0
18998919503_000000440301609_58403918    1.0
Time taken: 0.122 seconds

hive创建hbase表
CREATE TABLE hbase_hive_table(key int, value1 string)  
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
WITH SERDEPROPERTIES ( 
"hbase.columns.mapping" = ":key,a:b" 
);  

hive&gt; desc hbase_hive_table;
OK
key    int    from deserializer
value1    string    from deserializer
Time taken: 0.082 seconds

1.9.3p385 :011 &gt; put 'hbase_hive_table','1','a:b','abc'
0 row(s) in 2.0550 seconds

1.9.3p385 :012 &gt; scan 'hbase_hive_table'
ROW                                         COLUMN+CELL                                                                                                                 
 1                                          column=a:b, timestamp=1377262561618, value=abc                                                                              
1 row(s) in 0.9260 seconds

hive&gt; select * from hbase_hive_table;
OK
1    abc
Time taken: 1.625 seconds

--------------------------------------------------------------------------
运行job需要辅助jar包
hive --auxpath /opt/hadoop/hive/lib/hbase-0.94.10-security.jar,
/opt/hadoop/hive/lib/hive-hbase-handler-0.10.0.jar,
/opt/hadoop/hive/lib/zookeeper-3.4.5.jar,
/opt/hadoop/hive/lib/protobuf-java-2.4.0a.jar

hive&gt; select count(*) from f2;
Total MapReduce CPU Time Spent: 2 seconds 610 msec
OK
7</pre>
            </div>
                </div>
---
layout:     post
title:      hive和hbase整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">整合后hive的启动方法</span></p>
<p><span style="font-size:18px;">1 单节点启动 hive -hiveconf hbase.master=master:60000</span></p>
<p><span style="font-size:18px;">2 集群启动 hive -hiveconf hbase.zookeeper.quorum=node1,node2</span></p>
<p><span style="font-size:18px;">3.在hive中创建hbase识别的表的方法</span></p>
<p><span style="font-size:18px;color:#6666cc;">create table hbase_table_1(key int,value string, value2 string,value3 string) stored by  'org.apache.hadoop.hive.hbase.HBaseStorageHandler' with serdeproperties</span><span style="color:rgb(102,102,204);font-size:18px;">("hbase.columns.mapping"=":key,cf1:val,cf1:val2,cf2:val")
 tblproperties("hbase.table.name"="xyz");</span></p>
<p><span style="font-size:18px;"><span></span><span style="color:#ff6666;">hbase.table.name</span>:是定义在hbase中table表名<br></span></p>
<p><span style="font-size:18px;"><span></span><span style="color:#ff6666;">hbase.columns.mapping</span>：定义hbase的列簇和列 ，:key是固定的而且唯一，后面的是列，列的数量必须和<span></span>hbase_table_1中value的数量一致<br></span></p>
<p><span style="font-size:18px;">4.因为在hive中创建的和hbase关联的表不能通过load data 来导入数据，所以需要在hive中创建一个临时表，然后把数据导入到临时表中，最后再把临时表中的数据添加到hbase表中</span></p>
<p><span style="font-size:18px;">1.创建新表 create table temp(key int , value1 string, value2 string,value3 string) row format delimited fields terminated by '\t';</span></p>
<p><span style="font-size:18px;">2.导入数据 load data local inpath 'test.txt' into table temp;</span></p>
<p><span style="font-size:18px;">3.将数据导入到hbase表中 insert overwrite table hbase_table_1<span style="color:#ff0000;">select * from temp</span></span></p>
<p><span style="font-size:18px;color:#333333;">4.可以在hbase中查看导入的数据：scan ‘hbase_table_1'</span></p>
            </div>
                </div>
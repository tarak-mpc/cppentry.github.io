---
layout:     post
title:      Hive操作Hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:14px;">启动hive，进入hive的终端 <br></span>
<p><span style="font-size:14px;">  hive --auxpath /opt/hive/lib/hive-hbase-handler-2.1.0.jar, /opt/hive/lib/zookeeper-3.4.6.jar
<br></span></p>
<p><span style="font-size:14px;">      --hiveconf hbase.master=master:16010 <br></span></p>
<p><span style="font-size:14px;">      --hiveconf hbase.zookeeper.quorum=master,slave,node1</span></p>
<span style="font-size:14px;">在Hive里面操作HBase <br>
        创建一张表： <br>
            如果hbase中不存在该表 <br>
                我们只能在hive中使用创建内部表的方式，来创建一张表，同会在hbase中也会创建相关的表。 <br>
                    create table h2hb_1 <br>
                    (id int, <br>
                     name string, <br>
                     age int <br>
                    )row format delimited <br>
                     fields terminated by ',' <br>
                     stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' with serdeproperties (
<br>
                     "hbase.columns.mapping" = ":key,cf:name,cf:age", <br>
                     "hbase.table.name" = "t" <br>
                    ); <br>
                    在hive中创建了一张表h2hb_1，有三列内容id, name, age,同时映射到hbase中的表t，其中id对应行健
<br>
                    name对应hbase中列族cf中的name，age同理 <br>
            如果hbase中已经存在该表 <br>
                如果使用上述建表语句创建的时候，则会报错，因为在hbase中已经存在了一张表为t，所以这时只能创建外部表去映射hbase中的一张表。
<br>
                    create external table h2hb_2 <br>
                    (id int, <br>
                     name string, <br>
                     age int <br>
                    )row format delimited <br>
                     fields terminated by ',' <br>
                     stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' with serdeproperties (
<br>
                     "hbase.columns.mapping" = ":key,cf:name,cf:age", <br>
                     "hbase.table.name" = "t" <br>
                    );</span>
            </div>
                </div>
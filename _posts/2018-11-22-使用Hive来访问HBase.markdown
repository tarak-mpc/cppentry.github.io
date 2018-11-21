---
layout:     post
title:      使用Hive来访问HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kaaosidao/article/details/78242235				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:14px;">启动hive，进入hive的终端<br><span></span>hive --auxpath /opt/hive/lib/hive-hbase-handler-2.1.0.jar,/opt/hive/lib/zookeeper-3.4.6.jar --hiveconf hbase.master=hadoop01:16010 --hiveconf hbase.zookeeper.quorum=hadoop01,hadoop02,hadoop03<br><span></span>在Hive里面操作HBase<br><span></span>创建一张表：<br><span></span>如果hbase中不存在该表<br><span></span>我们只能在hive中使用创建内部表的方式，来创建一张表，同会在hbase中也会创建相关的表。<br><span></span>eg.<br><span></span>create table h2hb_1(<br><span></span>id int,<br><span></span>name string,<br><span></span>age int<br><span></span>)row format delimited<br><span></span>fields terminated by ','<br><span></span>stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' with serdeproperties (<br><span></span>"hbase.columns.mapping" = ":key,cf:name,cf:age",<br><span></span>"hbase.table.name" = "t"<br><span></span>);<br><span></span>在hive中创建了一张表h2hb_1，有三列内容id, name, age,同时映射到hbase中的表t，其中id对应行健<br><span></span>name对应hbase中列族cf中的name，age同理<br><span></span>如果hbase中已经存在该表<br><span></span>如果使用上述建表语句创建的时候，则会报错，因为在hbase中已经存在了一张表为t，所以这时只能创建外部表去映射hbase中的一张表。<br><span></span>create external table h2hb_2<br><span></span>(id int,<br><span></span>name string,<br><span></span>age int<br><span></span>)row format delimited<br><span></span>fields terminated by ','<br><span></span>stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' with serdeproperties (<br><span></span>"hbase.columns.mapping" = ":key,cf:name,cf:age",<br><span></span>"hbase.table.name" = "t"<br><span></span>);<br><span></span>我们即可对hbase中的表，使用hql来进行常见的分析操作，比较便利。</span>
            </div>
                </div>
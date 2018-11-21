---
layout:     post
title:      Hive 练习操作2  文件保存在HDFS , HIVE 数据仓库建表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sylmoon/article/details/77684547				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="line-height:1.75;font-size:14px;"><span style="font-size:26px;font-family:Arial;"><strong>步骤1 ：拷贝数据文件 HDFS</strong></span></div>
<div style="line-height:1.75;font-size:14px;">[root@master /]# hadoop fs -put /opt/exercise/names.txt /user/root/names.txt</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">[root@master /]# hadoop fs -ls /user/root/names.txt</div>
<div style="line-height:1.75;font-size:14px;">-rw-r--r-- 2 root supergroup 78 2017-08-28 15:17 /user/root/names.txt</div>
<div style="line-height:1.75;font-size:14px;">You have new mail in /var/spool/mail/root</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:26px;font-family:Arial;"><strong>步骤2 : 新建文件夹</strong></span></div>
<div style="line-height:1.75;font-size:14px;">[root@master /]# hadoop fs -mkdir /user/root/hivedemo</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:26px;font-family:Arial;"><strong>步骤3：新建表， 并指定数据存储位置在 /user/root/hivedemo</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">hive (default)&gt; create table names(id int , name string)</div>
<div style="line-height:1.75;font-size:14px;">&gt; ROW FORMAT delimited fields terminated by '\t'</div>
<div style="line-height:1.75;font-size:14px;">&gt; LOCATION '/USER/ROOT/hivedemo';</div>
<div style="line-height:1.75;font-size:14px;">OK</div>
<div style="line-height:1.75;font-size:14px;">Time taken: 3.438 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:26px;font-family:Arial;"><strong>步骤4：把数据导入hive 外部表的names 表中
</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">hive (default)&gt; load data inpath '/user/root/names.txt' into table names;</div>
<div style="line-height:1.75;font-size:14px;">Loading data to table default.names</div>
<div style="line-height:1.75;font-size:14px;">Table default.names stats: [numFiles=0, totalSize=0]</div>
<div style="line-height:1.75;font-size:14px;">OK</div>
<div style="line-height:1.75;font-size:14px;">Time taken: 1.661 seconds</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:26px;font-family:Arial;"><strong>步骤5： 查询</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">hive (default)&gt; select * from names;</div>
<div style="line-height:1.75;font-size:14px;">OK</div>
<div style="line-height:1.75;font-size:14px;">names.id names.name</div>
<div style="line-height:1.75;font-size:14px;">0 Rich</div>
<div style="line-height:1.75;font-size:14px;">1 Barry</div>
<div style="line-height:1.75;font-size:14px;">2 George</div>
<div style="line-height:1.75;font-size:14px;">3 Ulf</div>
<div style="line-height:1.75;font-size:14px;">4 Danielle</div>
<div style="line-height:1.75;font-size:14px;">5 Tom</div>
<div style="line-height:1.75;font-size:14px;">6 manish</div>
<div style="line-height:1.75;font-size:14px;">7 Brian</div>
<div style="line-height:1.75;font-size:14px;">8 Mark</div>
<div style="line-height:1.75;font-size:14px;">Time taken: 1.38 seconds, Fetched: 9 row(s)</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">hive (default)&gt; dfs -ls hivedemo;</div>
<div style="line-height:1.75;font-size:14px;">hive (default)&gt; dfs -ls /user/hive/warehouse;</div>
<div style="line-height:1.75;font-size:14px;">Found 2 items</div>
<div style="line-height:1.75;font-size:14px;">-rw-r--r-- 2 root supergroup 989239 2017-08-24 15:03 /user/hive/warehouse/employees</div>
<div style="line-height:1.75;font-size:14px;">drwxr-xr-x - root supergroup 0 2017-08-24 15:08 /user/hive/warehouse/people_visits</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;">如果drop table names 但是HDFS 里面并不会删除</div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
            </div>
                </div>
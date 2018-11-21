---
layout:     post
title:      HBase学习笔记 --- RDBMS sqoop 导入数据到HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/53175645				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">使用语句如下：</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">sqoop import --connect jdbc:mysql://mysqlip:port/db --table src_tablename --hbase-table db:tablename --column-family colfamname --hbase-create-table -username 'root' -password 'password' -m 3<br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">对于上述语句的解析可以理解为如下：</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">sqoop import --connect  连接语句</span><br></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">jdbc:mysql://mysqlip:port/db配置数据源ip, database</span><br></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;">--table src_tablename --hbase-table db:tablename 数据源的表，目的数据库的namesapce:table</span><br></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;">--column-family colfamname  配置的是columnfamily</span><br></span></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><br></span></span></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;">--hbase-create-table 建表</span><br></span></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;">-username 'root' -password 'password' -m 3 数据库mysql的账号密码，以及启动的mapreduce数量</span></span></span></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><br></span></span></span></span></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><span style="font-size:18px;"><br></span></span></span></span></p>
            </div>
                </div>
---
layout:     post
title:      hive中Specified key was too long; max key length is 767 bytes问题解决
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
当hive安装完成后，在hive中执行show table等任何命令，都会报以下错：<br><br>
[grid@racdb ~]$ hive<br>
Logging initialized using configuration in jar:file:/home/grid/hive-0.8.1/lib/hive-common-0.8.1.jar!/hive-log4j.properties<br>
Hive history file=/tmp/grid/hive_job_log_grid_201510181909_1589231771.txt<br>
hive&gt; show tables;<br>
   FAILED: Error in metadata: javax.jdo.JDODataStoreException: Error(s) were found while auto-creating/validating the datastore for classes. The errors are printed in the log, and are attached to this exception.<br>
NestedThrowables:<br>
com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes<br>
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask<br>
（略去 很多java 提示）<br><br><br>
解决办法：<br>
是到mysql中的hive数据库里执行 <span style="color:#ff0000;">alter database hive character set latin1;</span><br>
改变hive元数据库的字符集，问题就可以解决！<br><br><br>
参考：http://sunjia-704471770-qq-com.iteye.com/blog/1631430
            </div>
                </div>
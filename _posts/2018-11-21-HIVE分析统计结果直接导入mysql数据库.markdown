---
layout:     post
title:      HIVE分析统计结果直接导入mysql数据库
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
大部分利用hive做数据分析的步骤是先用hive将统计结果导出到本地文件或者Hive的其他表中，再将本地文件导入到mysql或者利用sqoop将Hive表导入到mysql中。<br>
 <br>
今天同事给推荐了一个利用udf函数直接将统计结果导入mysql的方法。<br>
 <br>
步骤为<br>
 <br>
hive&gt;add jar /usr/lib/hive/lib/hive-contrib-0.9.0-cdh4.1.2.jar;<br>
 <br>
Added /usr/lib/hive/lib/hive-contrib-0.9.0-cdh4.1.2.jar to class path<br>
 Added resource: /usr/lib/hive/lib/hive-contrib-0.9.0-cdh4.1.2.jar<br>
 <br>
hive&gt;add jar /usr/share/java/mysql-connector-java-5.1.17.jar;<br>
 <br>
Added /usr/share/java/mysql-connector-java-5.1.17.jar to class path<br>
 Added resource: /usr/share/java/mysql-connector-java-5.1.17.jar<br>
 <br><br><br>
hive&gt;CREATE TEMPORARY FUNCTION dboutput AS 'org.apache.Hadoop.hive.contrib.genericudf.example.GenericUDFDBOutput';<br>
 <br>
hive&gt;select dboutput('jdbc:mysql://localhost/result','root','123456','INSERT INTO dc(code,size) VALUES (?,?)',code,size) from accesslog limit 10;<br>
 <br>
注：result为mysql数据库名，dc为数据库result中的表名 dc(code,size)括号中的字段为mysql表dc字段，values(?,?)对应hive统计结果的值 后面的code,size为hive表中的字段，accesslog表示hive中的表名称。<br>
 <br>
通过以上步骤即可将hive统计结果直接导入到mysql数据库中。
            </div>
                </div>
---
layout:     post
title:      利用udf函数将hive统计结果直接插入到mysql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>大部分利用hive做数据分析的步骤是先用hive将统计结果导出到本地文件或者Hive的其他表中，再将本地文件导入到mysql或者利用sqoop将Hive表导入到mysql中。</p>
<p>今天同事给推荐了一个利用udf函数直接将统计结果导入mysql的方法。</p>
<p>步骤为</p>
<p>hive&gt;add jar /usr/lib/hive/lib/hive-contrib-0.9.0-cdh4.1.2.jar;</p>
<p>Added /usr/lib/hive/lib/hive-contrib-0.9.0-cdh4.1.2.jar to class path<br>
Added resource: /usr/lib/hive/lib/hive-contrib-0.9.0-cdh4.1.2.jar</p>
<p>hive&gt;add jar /usr/share/java/mysql-connector-java-5.1.17.jar;</p>
<p>Added /usr/share/java/mysql-connector-java-5.1.17.jar to class path<br>
Added resource: /usr/share/java/mysql-connector-java-5.1.17.jar<br></p>
<p>hive&gt;<span>CREATE TEMPORARY FUNCTION dboutput AS 'org.apache.hadoop.hive.contrib.genericudf.example.GenericUDFDBOutput';</span></p>
<p><span>hive&gt;select dboutput('jdbc:mysql://localhost/result','root','123456','INSERT INTO dc(code,size) VALUES (?,?)',code,size) from accesslog limit 10;</span></p>
<p><span style="background-color:rgb(255,255,255);"><span style="color:#ff0000;">注：result为mysql数据库名，dc为数据库result中的表名 dc(code,size)括号中的字段为mysql表dc字段，values(?,?)对应hive统计结果的值 后面的code,size为hive表中的字段，accesslog表示hive中的表名称。</span></span></p>
<p><span>通过以上步骤即可将hive统计结果直接导入到mysql数据库中。<br></span><br></p>
            </div>
                </div>
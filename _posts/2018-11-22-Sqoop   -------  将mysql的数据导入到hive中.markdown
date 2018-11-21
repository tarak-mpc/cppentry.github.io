---
layout:     post
title:      Sqoop   -------  将mysql的数据导入到hive中
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：个人原创，转载请标注！					https://blog.csdn.net/Z_Date/article/details/84035052				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;">将数据导入到hive中，但是实际上来说hive中的数据也是在hdfs上进行保存的。在进行导入hive的本质其实就是先把数据导入到hdfs 在从hdfs把数据迁移到hive表所在的hdfs路径</p>

<p style="margin-left:0cm;">范例：从mysql数据库中导入数据到hive中</p>

<pre class="has">
<code>sqoop import \
--connect jdbc:mysql://mini3:3306/hive \
--username root \
--password Root123@ \
--table tohdfs \
--hive-import \
--hive-database student \
--hive-table stu_info \
--target-dir /tohdfs3 \
--fields-terminated-by '\t' \
--num-mappers 1 </code></pre>

<p style="margin-left:0cm;">之后在hive中准备要导入的数据库以及数据表</p>

<pre class="has">
<code class="language-sql">create database student;</code></pre>

<p style="margin-left:0cm;">创建数据表，准备接收导入的数据</p>

<pre class="has">
<code class="language-sql">create table stu_info(id int,name string)
row format delimited fields terminated by '\t';</code></pre>

<p> </p>            </div>
                </div>
---
layout:     post
title:      大数据Spark “蘑菇云”行动第88课：Hive脚本、常用命令、having查询及变种实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/53399566				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>大数据Spark “蘑菇云”行动第88课：Hive脚本、常用命令、having查询及变种实战</p>
<p>HiveScript.sql的脚本内容<br></p>
<p>show databases;<br>
use default;<br>
show tables;<br>
select * from employees;<br><br><br><br><br>
hive&gt; !pwd<br>
hive&gt; ! /bin/echo "Hive on spark!"<br><br><br>
hive&gt; dfs -ls /<br></p>
<p> <br><br>
# hive -s -e "select * from employee" &gt;&gt; /usr/local/test.log<br>
# hive  -e "select * from employee"  <br>
hive -S -e "set" | grep warehouse<br></p>
<p>desc employee;<br>
select name from (select name,sum(salary) as salarytotal from employee)<br></p>
<p><br></p>
<p><br></p>
<p>employeeforhaving加一个字段 性别 男、女<br><br><br>
create  table employeeforhaving (userid int, name string,address string,salary double,gender string) <br>
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' LINES TERMINATED BY '\n'");<br><br><br><br><br>
load data local inpath '/..../employee.txt int tables‘ employeeforhaving；<br><br><br><br><br>
select gender form(select gender,sun(salary) as salarytotal from employeesforhaving group by gender ) subquery where <br>
subquery.salarytotal &gt;30000<br><br><br><br><br>
SELECT  gender ,SUM(salary) FROM employeesforhaving<br>
GROUP BY  gender <br>
HAVING SUM(salary)&gt; 30000<br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20161129212434922?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212446038?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212454329?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212500976?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212508219?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212515266?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212522891?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212530742?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212537485?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212548149?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212555821?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212603079?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212609532?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212614220?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212619478?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212625665?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212631954?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212636860?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212642439?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212648853?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><img src="https://img-blog.csdn.net/20161129212653994?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
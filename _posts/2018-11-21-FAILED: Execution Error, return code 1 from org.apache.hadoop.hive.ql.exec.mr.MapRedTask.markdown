---
layout:     post
title:      FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.mr.MapRedTask
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p>Eclispe本地使用JDBC连接hive,查询的时候出现：FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.mr.MapRedTask 错误，<img src="https://img-blog.csdn.net/20180129172616244?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM4Mjk3ODUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p><br></p>
<p>Hive是基于hadoop的封装,所有,hive在执行hive ql的时候回创建一个hadoop的Job（MapReduce），所以需要操作到hadoop的HDFS文件系统，Eclipse 本地开发的时候使用的用户 对某些文件会没有权限,导致出现上述错误,所以只要对报读的路径变更权限就好了<img src="https://img-blog.csdn.net/20180129173209956?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM4Mjk3ODUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""> 
 hdfs dfs -chmod 777 文件路径<br>
这样就能执行了</p>
<p><img src="https://img-blog.csdn.net/20180129173257485?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2VpeGluXzM4Mjk3ODUz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
            </div>
                </div>
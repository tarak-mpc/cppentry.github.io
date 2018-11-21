---
layout:     post
title:      spark sql和DataFrame本质
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'宋体';">一：</span>Spark SQL<span style="font-family:'宋体';">与</span><span style="font-family:Calibri;">DataFrame</span></p>
<p>1， Spark SQL<span style="font-family:'宋体';">之所以是除了</span><span style="font-family:Calibri;">Spark Core</span><span style="font-family:'宋体';">以外最大的和最受关注的组件，原因是：</span></p>
<p>a) <span style="font-family:'宋体';">处理一切存储介质和各种格式的数据（您同时可以方便的扩展</span>Spark SQL<span style="font-family:'宋体';">的功能来支持更多类型的数据，例如</span><span style="font-family:Calibri;">Kudu</span><span style="font-family:'宋体';">）；</span></p>
<p>b) Spark SQL<span style="font-family:'宋体';">把数据仓库的计算能力推向了新的高度，不仅是无敌的计算速度（</span><span style="font-family:Calibri;">Spark SQL</span><span style="font-family:'宋体';">比</span><span style="font-family:Calibri;">Shark</span><span style="font-family:'宋体';">快了至少一个数量级，而</span><span style="font-family:Calibri;">Shark</span><span style="font-family:'宋体';">比</span><span style="font-family:Calibri;">Hive</span><span style="font-family:'宋体';">快了至少一个数量级，尤其是在</span><span style="font-family:Calibri;">Tungsten</span><span style="font-family:'宋体';">成熟以后会更加无可匹敌），更为重要的是把数据仓库的计算复杂度推向了历史上全新的高度（</span><span style="font-family:Calibri;">Spark
 SQL</span><span style="font-family:'宋体';">后续推出的</span><span style="font-family:Calibri;">DataFrame</span><span style="font-family:'宋体';">可以让数据仓库直接使用机器学习、图计算等复杂的算法库来对数据仓库进行复杂深度数据价值的挖掘）；</span></p>
<p>c) Spark SQL(DataFrame<span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">DataSet)</span><span style="font-family:'宋体';">不仅是数据仓库的引擎，</span><strong><span style="color:rgb(255,0,0);"><span style="font-family:'宋体';">而是也是数据挖掘的引擎，更为重要的是</span>Spark
 SQL<span style="font-family:'宋体';">是数据科学计算和分析引擎！！！</span></span></strong></p>
<p><span style="color:rgb(255,0,0);">d) </span><strong><span style="color:rgb(255,0,0);"><span style="font-family:'宋体';">后来的</span>DataFrame<span style="font-family:'宋体';">让</span><span style="font-family:Calibri;">Spark
</span><span style="font-family:'宋体';">（</span><span style="font-family:Calibri;">SQL</span><span style="font-family:'宋体';">）一举成为了大数据计算引擎的技术实现霸主（尤其是在</span><span style="font-family:Calibri;">Tungsten</span><span style="font-family:'宋体';">的强力支持下）！</span></span></strong></p>
<p><span style="color:rgb(255,0,0);">e) </span><strong><span style="color:rgb(255,0,0);">Hive+Spark SQL+ DataFrame<span style="font-family:'宋体';">：目前至少是在中国所有的大数据项目至少</span><span style="font-family:Calibri;">90%</span><span style="font-family:'宋体';">无法逃脱该技术组合</span></span></strong></p>
<p><span style="color:rgb(255,0,0);">i. </span><strong><span style="color:rgb(255,0,0);">Hive<span style="font-family:'宋体';">：负责廉价的数据仓库存储</span></span></strong></p>
<p><span style="color:rgb(255,0,0);">ii. </span><strong><span style="color:rgb(255,0,0);">Spark SQL<span style="font-family:'宋体';">：负责高速的计算；</span></span></strong></p>
<p><span style="color:rgb(255,0,0);">iii. </span><strong><span style="color:rgb(255,0,0);">DataFrame<span style="font-family:'宋体';">：负责复杂的数据挖掘</span></span></strong></p>
<p> </p>
<p><span style="font-family:'宋体';">二：</span>DataFrame<span style="font-family:'宋体';">与</span><span style="font-family:Calibri;">RDD</span></p>
<p>1， R<span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">Python</span><span style="font-family:'宋体';">中都有</span><span style="font-family:Calibri;">DataFrame</span><span style="font-family:'宋体';">，</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">中的</span><span style="font-family:Calibri;">DataFrame</span><span style="font-family:'宋体';">从形式上看最大的不同点是其天生是分布式的；你可以简单的认为</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">中的</span><span style="font-family:Calibri;">DataFrame</span><span style="font-family:'宋体';">是一个分布式的</span><span style="font-family:Calibri;">Table</span><span style="font-family:'宋体';">，形式如下所述：</span></p>
<table><tbody><tr><td valign="top">
<p>Name</p>
</td>
<td valign="top">
<p>Age</p>
</td>
<td valign="top">
<p>Tel</p>
</td>
</tr><tr><td valign="top">
<p>String</p>
</td>
<td valign="top">
<p>Int</p>
</td>
<td valign="top">
<p>Long</p>
</td>
</tr><tr><td valign="top">
<p>String</p>
</td>
<td valign="top">
<p>Int</p>
</td>
<td valign="top">
<p>Long</p>
</td>
</tr><tr><td valign="top">
<p>String</p>
</td>
<td valign="top">
<p>Int</p>
</td>
<td valign="top">
<p>Long</p>
</td>
</tr><tr><td width="544" valign="top" colspan="3">
<p> </p>
<p> </p>
<p> </p>
</td>
</tr><tr><td valign="top">
<p>String</p>
</td>
<td valign="top">
<p>Int</p>
</td>
<td valign="top">
<p>Long</p>
</td>
</tr><tr><td valign="top">
<p>String</p>
</td>
<td valign="top">
<p>Int</p>
</td>
<td valign="top">
<p>Long</p>
</td>
</tr><tr><td valign="top">
<p>String</p>
</td>
<td valign="top">
<p>Int</p>
</td>
<td valign="top">
<p>Long</p>
</td>
</tr></tbody></table><p> </p>
<p><span style="font-family:'宋体';">而</span>RDD<span style="font-family:'宋体';">是形如以下所述：</span></p>
<table><tbody><tr><td valign="top">
<p align="center"><strong><span style="color:rgb(227,108,9);">Person</span></strong></p>
</td>
</tr><tr><td valign="top" style="background:rgb(253,229,209);">
<p align="center"><strong><span style="color:rgb(227,108,9);">Person</span></strong></p>
</td>
</tr><tr><td valign="top">
<p align="center"><strong><span style="color:rgb(227,108,9);">Person</span></strong></p>
</td>
</tr><tr><td valign="top" style="background:rgb(253,229,209);">
<p align="center"><strong><span style="color:rgb(227,108,9);"> </span></strong></p>
<p align="center"><strong><span style="color:rgb(227,108,9);"> </span></strong></p>
</td>
</tr><tr><td valign="top">
<p align="center"><strong><span style="color:rgb(227,108,9);">Person</span></strong></p>
</td>
</tr><tr><td valign="top" style="background:rgb(253,229,209);">
<p align="center"><strong><span style="color:rgb(227,108,9);">Person</span></strong></p>
</td>
</tr><tr><td valign="top">
<p align="center"><strong><span style="color:rgb(227,108,9);">Person</span></strong></p>
</td>
</tr></tbody></table><p>2， RDD<span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">DataFrame</span><span style="font-family:'宋体';">的根本差异：</span></p>
<p>a) RDD<span style="font-family:'宋体';">是以</span><span style="font-family:Calibri;">Record</span><span style="font-family:'宋体';">为单位的，</span><span style="font-family:Calibri;">Spark</span><span style="font-family:'宋体';">在优化的时候无法东西</span><span style="font-family:Calibri;">Record</span><span style="font-family:'宋体';">内部的细节，所以也就无法进行更深度的优化，这极大的限制了</span><span style="font-family:Calibri;">Spark
 SQL</span><span style="font-family:'宋体';">性能的提升！</span></p>
<p>b) DataFrame<span style="font-family:'宋体';">包含了每个</span><span style="font-family:Calibri;">Record</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">Metadata</span><span style="font-family:'宋体';">信息，也就是说</span><span style="font-family:Calibri;">DataFrame</span><span style="font-family:'宋体';">的优化时基于列内部的优化，而不是像</span><span style="font-family:Calibri;">RDD</span><span style="font-family:'宋体';">一样只能够基于行进行优化；</span></p>
<p> </p>
<p><span style="font-family:'宋体';">三：</span>Spark SQL<span style="font-family:'宋体';">企业级最佳实践</span></p>
<p><span style="font-family:'宋体';">第一阶段：最开始阶段文件存储，</span>c代码处理<br><span style="font-family:'宋体';">第二阶段：</span>javaEE加数据库，瓶颈数据库不能分布式（扩展性），企业只能处理局部数据，数据过滤然后在放在数据库中。<br><span style="font-family:'宋体';">第三阶段数据局限导致</span>hive，hive 计算能力有限，速递性能问题<br><span style="font-family:'宋体';">第四阶段：</span>hive 转向sparksql+hive，计算能力是一个问题<br><span style="font-family:'宋体';">第五阶段：</span>hive+Spark SQL+dataFrame<br><span style="font-family:'宋体';">第六阶段：</span>hive+ Spark SQL+dataFrame +dataSet</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
            </div>
                </div>
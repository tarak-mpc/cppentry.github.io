---
layout:     post
title:      彷徨 | Hive的SQL--DDL详细操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：版权所有,转载请注明出处.谢谢					https://blog.csdn.net/weixin_35353187/article/details/82185206				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <hr><p>Hive的简介与安装见另一篇文章 : <a href="https://blog.csdn.net/weixin_35353187/article/details/82154151" rel="nofollow">https://blog.csdn.net/weixin_35353187/article/details/82154151</a></p>

<hr id="hr-toc"><h1 id="Hive%E7%9A%84%E4%B8%89%E7%A7%8D%E4%BD%BF%E7%94%A8%E6%96%B9%E5%BC%8F%20%3A">Hive的三种使用方式 :</h1>

<p><strong>方式一 : bin/hive  交互式查询</strong></p>

<p><strong>方式二 : 启动Hive的网络服务 , 然后通过客户端beeline去连接服务进行查询 :</strong></p>

<p><strong>              启动服务 : <span style="color:#f33b45;">bin/hiveserver2</span></strong></p>

<p><strong>              启动客户端去连接Hive服务 : <span style="color:#f33b45;">bin/beeline -u jdbc:hive2://hadoop01:10000 -n root</span></strong></p>

<p><strong>方式三 : shell脚本方式查询</strong></p>

<pre class="has">
<code>#!/bin/bash
HIVE_HOME=/root/apps/hive-1.2.2
$HIVE_HOME/bin/hive -e 'insert into table t_avg  as  select skuid,avg(amount) from t_2 group by skuid'
$HIVE_HOME/bin/hive -e 'create table t_result as select skuid,sum(amount) from t_2 group by skuid'
</code></pre>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>补充一 ：bin/hive -f /root/etl.sql  （把sql语句写到一个专门的文件里）, Linux 会去执行SQL文件里面的所有语句</strong></span></p>

<p style="margin-left:0cm;"><strong>新建一个test.sql文件 , 里面放俩条SQL语句 :</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="50" src="https://img-blog.csdn.net/2018083109404344?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="997"></p>

<p style="margin-left:0cm;"><strong>执行  <span style="color:#f33b45;">hive -f /root/test.sql</span>  命令</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="357" src="https://img-blog.csdn.net/20180831094300207?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>补充二 : 在Linux窗口hive -e '' SQL语句 ''  也可以执行SQL语句</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>hive -e 'select * from t_access'</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="212" src="https://img-blog.csdn.net/20180831093225977?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><img alt="" class="has" height="260" src="https://img-blog.csdn.net/20180831093250797?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>可以看出查询结果是一样的</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>补充三 : SQL是一种面向集合的编程语言</strong></span></p>

<hr><h1 id="1%C2%A0%20%E4%BB%A5%E6%9C%8D%E5%8A%A1%E7%9A%84%E5%BD%A2%E5%BC%8F%E5%90%AF%E5%8A%A8Hive%20%3A">1  以服务的形式启动Hive :</h1>

<p><span style="color:#f33b45;"><strong>nohup hiveserver2 &gt;/dev/null 2&gt;&amp;1  &amp;</strong></span></p>

<hr><h1 id="2%C2%A0%20%E5%AE%A2%E6%88%B7%E7%AB%AF%E8%BF%9E%E6%8E%A5%C2%A0%3A%C2%A0"><strong>2  客户端连接 : </strong></h1>

<p><strong>方式一:</strong></p>

<p><strong>beeline  <br>
!connect jdbc:hive2://hadoop01:10000<br>
root<br>
方式二:</strong></p>

<p><span style="color:#f33b45;"><strong>beeline -u jdbc:hive2://hadoop01:10000 -n root</strong></span></p>

<p><img alt="" class="has" height="174" src="https://img-blog.csdn.net/20180829170300127?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="900"></p>

<h3 id="%E2%80%8B"><img alt="" class="has" height="330" src="https://img-blog.csdn.net/20180829171111777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="900"></h3>

<p><span style="color:#3399ea;"><strong>查看端口是否被监听 : </strong></span><span style="color:#f33b45;"><strong>netstat -nltp  </strong></span></p>

<p><span style="color:#3399ea;"><strong>如果10000端口被监听 , hive服务就启动了 .</strong></span></p>

<p><img alt="" class="has" height="299" src="https://img-blog.csdn.net/20180831091253905?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<hr><h1 id="3%C2%A0%20%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C%E8%AF%AD%E5%8F%A5">3  基本操作语句</h1>

<h2 id="3.1%C2%A0%20%E5%88%9B%E5%BB%BA%E5%86%85%E9%83%A8%E8%A1%A8"><strong>3.1  创建内部表</strong></h2>

<p><span style="color:#f33b45;"><strong>create table t_user(id string,name string) <br>
row format delimited <br>
fields terminated by ',';</strong></span></p>

<h2><img alt="" class="has" height="54" src="https://img-blog.csdn.net/2018082918213089?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></h2>

<hr><h2 id="%C2%A03.2%C2%A0%20%E5%88%9B%E5%BB%BA%E5%A4%96%E9%83%A8%E8%A1%A8"><strong>3.2  创建外部表</strong></h2>

<p><span style="color:#f33b45;"><strong>create external table t_access(ip String,url String,access_time String)<br>
row format delimited <br>
fields terminated by ','<br>
location "/data/acc";</strong></span></p>

<p><img alt="" class="has" height="85" src="https://img-blog.csdn.net/20180829183352616?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><span style="color:#3399ea;"><strong>内部表直接把数据上传到hdfs对应的目录(/user/hive/warehouse)上，就能够关联起来 . 外部表跟内部表的区别，内部表放在warehouse下面，删除表的时候会把数据删除掉，外部表示需要自己制定目录，删除表的时候，不会删除数据</strong></span></p>

<hr><h2 id="3.3%C2%A0%20%E6%9F%A5%E7%9C%8B%E8%A1%A8%E7%BB%93%E6%9E%84."><strong>3.3  查看表结构.</strong></h2>

<p><span style="color:#f33b45;"><strong>desc tablename</strong></span></p>

<p><img alt="" class="has" height="340" src="https://img-blog.csdn.net/20180829184110574?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<hr><h2 id="3.4%C2%A0%20%E5%88%A0%E9%99%A4%E8%A1%A8"><strong>3.4  删除表</strong></h2>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>drop table t_order;</strong></span></p>

<p style="margin-left:0cm;">删除表的效果是：</p>

<p style="margin-left:0cm;">hive会从元数据库中清除关于这个表的信息；</p>

<p style="margin-left:0cm;">hive还会从hdfs中删除这个表的表目录；</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="349" src="https://img-blog.csdn.net/20180829191135247?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<hr><h2 id="3.5%C2%A0%20%E5%88%86%E5%8C%BA%E8%A1%A8" style="margin-left:0cm;"><strong>3.5  分区表</strong></h2>

<h3 id="3.5.1%20%E4%B8%80%E4%B8%AA%E5%88%86%E5%8C%BA%E5%BB%BA%E8%A1%A8"><strong>3.5.1 一个分区建表</strong></h3>

<p style="margin-left:0cm;"><strong> 注意：分区字段，不能出现在表字段里面 , 不同的分区数据存放在不同的目录下面</strong><br><span style="color:#f33b45;"><strong>create table t_access(id string,url string,access_time string)<br>
partitioned by(dt string)<br>
row format delimited  fields terminated by ',';</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="100" src="https://img-blog.csdn.net/20180829192622979?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>日志文件 : </strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="230" src="https://img-blog.csdn.net/20180829193032393?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>向分区中导入数据 : </strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>load data local inpath '/root/access.log.2018-08-29.log' into table t_access partition(dt='20170829');</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>load data local inpath '/root/access.log.2018-08-30.log' into table t_access partition(dt='20170830');</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="105" src="https://img-blog.csdn.net/20180829193627823?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="277" src="https://img-blog.csdn.net/20180829193820713?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>针对分区进行查询 : </strong></p>

<p style="margin-left:0cm;"><strong>A : 统计8月30号的总量：实质：就是将分区字段当成表字段来用，就可以使用where子句指定分区了</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select count(*) from t_access where dt='20180829';</strong></span></p>

<p style="margin-left:0cm;"><strong>B : 统计表中所有数据总量：实质：不指定分区条件即可</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select count(*) from t_access;</strong></span></p>

<h3 id="3.5.2%20%E5%A4%9A%E4%B8%AA%E5%88%86%E5%8C%BA%E5%BB%BA%E8%A1%A8" style="margin-left:0cm;"><strong>3.5.2 多个分区建表</strong></h3>

<h3 id="3.5.2.1%20%E5%86%85%E9%83%A8%E8%A1%A8%E5%88%86%E5%8C%BA"><strong>3.5.2.1 内部表分区</strong></h3>

<p><strong>建表 : </strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>CREATE TABLE t_2(id int,skuid string,price float,amount int)</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>partitioned by (day string,city string)</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>row format delimited fields terminated by ',';</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="84" src="https://img-blog.csdn.net/20180829202936491?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>导数据 :</strong></p>

<p style="margin-left:0cm;"><strong>t 2.1 数据 ：2018-04-15 北京</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="73" src="https://img-blog.csdn.net/20180830203521405?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="909"></p>

<p style="margin-left:0cm;"><strong>t 2.2 数据 ：2018-04-15 上海</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="44" src="https://img-blog.csdn.net/20180830203949701?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"> <strong>t 2.3 数据 ：</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="54" src="https://img-blog.csdn.net/20180830204150312?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>LOAD DATA LOCAL INPATH '/root/t2.1' into TABLE t_2 PARTITION(day='2018-04-15',city='beijing');</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>LOAD DATA LOCAL INPATH '/root/t2.2' into TABLE t_2 PARTITION(day='2018-04-15',city='shanghai');</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>LOAD DATA LOCAL INPATH '/root/t2.3' into TABLE t_2 PARTITION(day='2018-04-16',city='beijing');</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="201" src="https://img-blog.csdn.net/20180830204729906?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="174" src="https://img-blog.csdn.net/20180830205236455?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="218" src="https://img-blog.csdn.net/20180830205338271?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="149" src="https://img-blog.csdn.net/20180830205439421?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>查询 ：</strong></p>

<p style="margin-left:0cm;"><strong>1 ： <span style="color:#f33b45;">select * from t_2；</span></strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="228" src="https://img-blog.csdn.net/20180830204913593?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>2 ： <span style="color:#f33b45;">select sum(price*amount) from t_2;</span></strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="94" src="https://img-blog.csdn.net/20180830210927224?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>3 ：<span style="color:#f33b45;"> select sum(price*amount) from t_2 where day = "2018-04-15" and city = "beijing";</span></strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="86" src="https://img-blog.csdn.net/20180830211403902?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.5.2.2%20%E5%A4%96%E9%83%A8%E8%A1%A8%E5%88%86%E5%8C%BA" style="margin-left:0cm;"><strong>3.5.2.2 外部表分区</strong></h3>

<p><strong>建表 ：注：外部表建表时，最后需要制定一个目录 location '/xx/yy';</strong></p>

<p><img alt="" class="has" height="106" src="https://img-blog.csdn.net/20180830212352760?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><strong>导数据 :</strong></p>

<p><span style="color:#f33b45;"><strong>LOAD DATA LOCAL INPATH '/root/t2.1' into TABLE t_2_ex PARTITION(day='2018-04-15');</strong></span></p>

<p><img alt="" class="has" height="84" src="https://img-blog.csdn.net/20180830212814738?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><strong>查询 :</strong></p>

<p><img alt="" class="has" height="164" src="https://img-blog.csdn.net/20180830212846385?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="%E6%B3%A8%20%3A%20%E7%BB%99%E5%A4%96%E9%83%A8%E8%A1%A8%E6%B7%BB%E5%8A%A0%E5%88%86%E5%8C%BA"><strong><span style="color:#7c79e5;">注 : 给外部表添加分区</span></strong></h3>

<p><span style="color:#7c79e5;"><strong>已经存在一个目录 , 但是不在外部表的指定目录下 , 我们可以修改表 , 给这个表添加一个目录 .即将一个已存在的文件夹 , 作为表的一个分区 .</strong></span></p>

<p><span style="color:#86ca5e;"><strong>此时,根目录下面有一个2018-04-16的文件夹,里面有一个名为 t2.1 的文件</strong></span></p>

<p><img alt="" class="has" height="98" src="https://img-blog.csdn.net/20180830213720329?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><span style="color:#86ca5e;"><strong>我们将其添加到外部表的指定目录  /xx/yy 下 , 以便一起查询 . 此时只是Hive记录了那个文件的位置 , 并没有将文件复制或剪贴到外部表的指定目录.</strong></span></p>

<p><span style="color:#f33b45;"><strong>alter table t_2_ex  add partition (day = '2018-04-16') location '/2018-04-16';</strong></span></p>

<p><img alt="" class="has" height="67" src="https://img-blog.csdn.net/2018083021415365?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1178"></p>

<p><strong>查询 :</strong></p>

<p><span style="color:#f33b45;"><strong>select * from t_2_ex;</strong></span></p>

<p><img alt="" class="has" height="183" src="https://img-blog.csdn.net/20180830214328148?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="900"></p>

<p><span style="color:#86ca5e;"><strong>我们将其添加到外部表的指定目录  /xx/yy 下 , 以便一起查询 . 此时只是Hive记录了那个文件的位置 , 并没有将文件复制或剪贴到外部表的指定目录 . 可以看到外部表的指定目录 /xx/yy下只有2018-04-15一个文件夹 , 并没有我们刚才添加的2018-04-16文件夹 ,但是查询的时候 , 会查询到里面的内容 . </strong></span></p>

<p><img alt="" class="has" height="192" src="https://img-blog.csdn.net/20180830214831497?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><span style="color:#3399ea;"><strong>此方法也适用于内部表 , 可以将一个已存在的文件夹作为内部表的一个分区</strong></span></p>

<hr><h2 id="3.6%C2%A0%20CTAS%E5%BB%BA%E8%A1%A8%E8%AF%AD%E6%B3%95" style="margin-left:0cm;"><strong>3.6  CTAS建表</strong>语法</h2>

<h3 id="3.6.1%20%E5%8F%AF%E4%BB%A5%E9%80%9A%E8%BF%87%E5%B7%B2%E5%AD%98%E5%9C%A8%E8%A1%A8%E6%9D%A5%E5%BB%BA%E8%A1%A8%EF%BC%9A" style="margin-left:0cm;">3.6.1 可以通过已存在表来建表：</h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>create table t_user_2 like t_user;</strong></span></p>

<p style="margin-left:0cm;">新建的t_user_2表结构定义与源表t_user一致，但是没有数据</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="348" src="https://img-blog.csdn.net/20180829203824191?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>查看表数据 : 并没有数据</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="114" src="https://img-blog.csdn.net/20180829203905902?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.6.2%20%E5%9C%A8%E5%BB%BA%E8%A1%A8%E7%9A%84%E5%90%8C%E6%97%B6%E6%8F%92%E5%85%A5%E6%95%B0%E6%8D%AE" style="margin-left:0cm;"><strong>3.6.2 在建表的同时插入数据</strong></h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>create table t_user_3</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>as</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select id,name from t_user;</strong></span></p>

<p style="margin-left:0cm;">t_user_3会根据select查询的字段来建表，同时将查询的结果插入新表中</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="278" src="https://img-blog.csdn.net/20180829204449382?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>查询新表数据 :</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="384" src="https://img-blog.csdn.net/20180829204558516?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="900"></p>

<hr><h2 id="3.7%C2%A0%20%E6%95%B0%E6%8D%AE%E7%9A%84%E5%AF%BC%E5%85%A5%E4%B8%8E%E5%AF%BC%E5%87%BA%C2%A0" style="margin-left:0cm;">3.7  数据的导入与导出 </h2>

<h2 id="3.7.1%20%E5%B0%86%E6%95%B0%E6%8D%AE%E6%96%87%E4%BB%B6%E5%AF%BC%E5%85%A5hive%E7%9A%84%E8%A1%A8">3.7.1 将数据文件导入hive的表</h2>

<p style="margin-left:0cm;"><strong>方式1：导入数据的一种方式：手动用hdfs命令，将文件放入表目录；</strong></p>

<p style="margin-left:0cm;"><strong>方式2：<span style="color:#3399ea;">在hive的交互式shell中用hive命令来导入本地数据到表目录 ( 将本地文件导入 Hive 中的表 ) </span></strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>hive&gt;load data local inpath '/root/order.data.2' into table t_order;</strong></span></p>

<p style="margin-left:0cm;"><strong>方式3：<span style="color:#3399ea;">用hive命令导入hdfs中的数据文件到表目录 ( 将 HDFS 中的文件导入Hive中 )</span></strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>hive&gt;load data inpath '/access.log.2017-08-06.log' into table t_access partition(dt='20170806');</strong></span></p>

<p style="margin-left:0cm;"><u><span style="color:#86ca5e;"><strong>注意：导本地文件和导HDFS文件的区别：</strong></span></u></p>

<p style="margin-left:0cm;"><u><span style="color:#86ca5e;"><strong>本地文件导入表：复制</strong></span></u></p>

<p style="margin-left:0cm;"><u><span style="color:#86ca5e;"><strong>hdfs文件导入表：移动</strong></span></u></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><u><strong>注 :Hive不会对用户所导入的数据做任何的检查和约束;想导什么数据就导什么数据,但是字段不匹配会出现问题.</strong></u></span></p>

<h2 id="3.7.2%20%E5%B0%86hive%E8%A1%A8%E4%B8%AD%E7%9A%84%E6%95%B0%E6%8D%AE%E5%AF%BC%E5%87%BA%E5%88%B0%E6%8C%87%E5%AE%9A%E8%B7%AF%E5%BE%84%E7%9A%84%E6%96%87%E4%BB%B6" style="margin-left:0cm;">3.7.2 将hive表中的数据导出到指定路径的文件</h2>

<p><strong>将hive表中的数据导入HDFS的文件</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>insert overwrite directory '/root/access-data'</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>row format delimited fields terminated by ','</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_access;</strong></span></p>

<p style="margin-left:0cm;"><strong>将hive表中的数据导入本地磁盘文件</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>insert overwrite local directory '/root/access-data'</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>row format delimited fields terminated by ','</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_access limit 100000;</strong></span></p>

<h2 id="3.7.3%20hive%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F" style="margin-left:0cm;">3.7.3 hive文件格式</h2>

<p style="margin-left:0cm;">HIVE支持很多种文件格式： SEQUENCE FILE | TEXT FILE | PARQUET FILE | RC FILE</p>

<p style="margin-left:0cm;">create table t_pq(movie string,rate int)  stored as textfile;</p>

<p style="margin-left:0cm;">create table t_pq(movie string,rate int)  stored as sequencefile;</p>

<p style="margin-left:0cm;">create table t_pq(movie string,rate int)  stored as parquetfile;</p>

<hr><h2 id="3.8%C2%A0%20%E6%97%B6%E9%97%B4%E7%B1%BB%E5%9E%8B" style="margin-left:0cm;">3.8  时间类型</h2>

<p style="margin-left:0cm;"><a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-timestamp" rel="nofollow">TIMESTAMP</a> (时间戳) (包含年月日时分秒的一种封装)</p>

<p style="margin-left:0cm;"><a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Types#LanguageManualTypes-date" rel="nofollow">DATE</a> (日期)（只包含年月日）</p>

<p style="margin-left:0cm;">示例，假如有以下数据文件：</p>

<table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:426.1pt;">
			<p style="margin-left:0cm;">1,zhangsan,1985-06-30</p>

			<p style="margin-left:0cm;">2,lisi,1986-07-10</p>

			<p style="margin-left:0cm;">3,wangwu,1985-08-09</p>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;">那么，就可以建一个表来对数据进行映射</p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>create table t_customer(id int,name string,birthday date)</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>row format delimited fields terminated by ',';</strong></span></p>

<p style="margin-left:0cm;">然后导入数据</p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>load data local inpath '/root/customer.dat' into table t_customer;</strong></span></p>

<p style="margin-left:0cm;">然后，就可以正确查询</p>

<hr><h2 id="3.9%C2%A0%C2%A0%E5%A4%8D%E5%90%88%E7%B1%BB%E5%9E%8B" style="margin-left:0cm;"><strong>3.9  </strong>复合类型</h2>

<h3 id="3.9.1%C2%A0array%E6%95%B0%E7%BB%84%E7%B1%BB%E5%9E%8B">3.9.1 array数组类型</h3>

<p style="margin-left:0cm;">示例：array类型的应用</p>

<p style="margin-left:0cm;">假如有如下数据需要用hive的表去映射：</p>

<table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:426.1pt;">
			<p style="margin-left:0cm;">战狼2,吴京:吴刚:龙母,2017-08-16</p>

			<p style="margin-left:0cm;">三生三世十里桃花,刘亦菲:痒痒,2017-08-20</p>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;">设想：如果主演信息用一个数组来映射比较方便</p>

<p style="margin-left:0cm;">建表：</p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>create table t_movie(moive_name string,actors array&lt;string&gt;,first_show date)</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>row format delimited fields terminated by ','</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>collection items terminated by ':';</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="85" src="https://img-blog.csdn.net/20180829205930325?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;">导入数据：</p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>load data local inpath '/root/movie.dat' into table t_movie;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="50" src="https://img-blog.csdn.net/20180829210157378?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>查询：</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_movie;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="156" src="https://img-blog.csdn.net/20180829210321206?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;">我也不知道为啥没对齐,很尴尬</p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select moive_name,actors[0] from t_movie;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="169" src="https://img-blog.csdn.net/20180829210509104?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select movie_name,actors from t_movie where array_contains(actors,'吴刚');</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="145" src="https://img-blog.csdn.net/20180829211009310?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select movie_name,size(actors) from t_movie;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="160" src="https://img-blog.csdn.net/20180829210946218?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.9.2%20Map%E7%B1%BB%E5%9E%8B" style="margin-left:0cm;">3.9.2 Map类型</h3>

<p style="margin-left:0cm;">maps: MAP&lt;primitive_type, data_type&gt; </p>

<p><strong>假如有以下数据：</strong></p>

<table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:426.1pt;">
			<p style="margin-left:0cm;">1,zhangsan,father:xiaoming#mother:xiaohuang#brother:xiaoxu,28</p>

			<p style="margin-left:0cm;">2,lisi,father:mayun#mother:huangyi#brother:guanyu,22</p>

			<p style="margin-left:0cm;">3,wangwu,father:wangjianlin#mother:ruhua#sister:jingtian,29</p>

			<p style="margin-left:0cm;">4,mayun,father:mayongzhen#mother:angelababy,26</p>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;">可以用一个map类型来对上述数据中的家庭成员进行描述</p>

<p style="margin-left:0cm;"><strong>建表语句：</strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#f33b45;">create table t_person(id int,name string,family_members map&lt;string,string&gt;,age int)</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#f33b45;">row format delimited fields terminated by ','</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#f33b45;">collection items terminated by '#'</span></strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#f33b45;">map keys terminated by ':';</span></strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="109" src="https://img-blog.csdn.net/20180829211613765?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>导入数据</strong>：</p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>load data local inpath '/root/person.dat' into table t_person;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="50" src="https://img-blog.csdn.net/20180829211848385?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><strong>查询</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_person;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="181" src="https://img-blog.csdn.net/20180829211925529?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>取map字段的指定key的值</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select id,name,family_members['father'] as father from t_person;</strong></span></p>

<p style="margin-left:0cm;"><strong>注 : as  father  为设置一个别名</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="188" src="https://img-blog.csdn.net/20180829212104576?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>取map字段的所有key</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select id,name,map_keys(family_members) as relation from t_person;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="183" src="https://img-blog.csdn.net/20180829212254379?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>取map字段的所有value</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select id,name,map_values(family_members) from t_person;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="186" src="https://img-blog.csdn.net/20180829212442995?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select id,name,map_values(family_members)[0] from t_person;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="193" src="https://img-blog.csdn.net/20180829212539415?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>综合：查询有brother的用户信息</strong></p>

<p style="margin-left:0cm;"><strong><span style="color:#f33b45;">select id,name,brother <br>
from <br>
(select id,name,family_members['brother'] as brother from t_person) tmp <br>
where brother is not null;</span></strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="200" src="https://img-blog.csdn.net/20180829213853576?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.9.3%20struct%E7%B1%BB%E5%9E%8B" style="margin-left:0cm;">3.9.3 struct类型</h3>

<p style="margin-left:0cm;">structs: STRUCT&lt;col_name : data_type, ...&gt;</p>

<p style="margin-left:0cm;"><strong>假如有如下数据：</strong></p>

<table border="1" cellspacing="0"><tbody><tr><td style="vertical-align:top;width:426.1pt;">
			<p style="margin-left:0cm;">1,zhangsan,18:male:beijing</p>

			<p style="margin-left:0cm;">2,lisi,28:female:shanghai</p>
			</td>
		</tr></tbody></table><p style="margin-left:0cm;">其中的用户信息包含：年龄：整数，性别：字符串，地址：字符串</p>

<p style="margin-left:0cm;">设想用一个字段来描述整个用户信息，可以采用struct</p>

<p style="margin-left:0cm;"><strong>建表：</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>create table t_person_struct(id int,name string,info struct&lt;age:int,sex:string,addr:string&gt;)</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>row format delimited fields terminated by ','</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>collection items terminated by ':';</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="83" src="https://img-blog.csdn.net/20180829214713579?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>导入数据</strong>：</p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>load data local inpath '/root/person_struct.dat' into table t_person_struct;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="53" src="https://img-blog.csdn.net/20180829215146142?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><strong>查询</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_person_struct;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="143" src="https://img-blog.csdn.net/20180829215305348?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select id,name,info.age from t_person_struct;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="148" src="https://img-blog.csdn.net/20180829215418490?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select id,name,info.sex from t_person_struct;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="150" src="https://img-blog.csdn.net/20180829220326514?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<hr><h2 id="3.10%C2%A0%C2%A0%E4%BF%AE%E6%94%B9%E8%A1%A8%E5%AE%9A%E4%B9%89" style="margin-left:0cm;"><strong>3.10</strong><span style="color:#f33b45;"><strong> </strong></span> 修改表定义</h2>

<p style="margin-left:0cm;"><strong><span style="color:#000000;">仅修改</span><span style="color:#000000;">Hive</span><span style="color:#000000;">元数据，不会触动表中的数据，用户需要确定实际的数据布局符合元数据的定义。</span></strong></p>

<h3 id="%E4%BF%AE%E6%94%B9%E8%A1%A8%E5%90%8D%EF%BC%9A" style="margin-left:0cm;">修改表名：</h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>ALTER TABLE table_name RENAME TO new_table_name</strong></span></p>

<p style="margin-left:0cm;">示例：<span style="color:#f33b45;"><strong>alter table t_zhang rename to t_junjie;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="92" src="https://img-blog.csdn.net/20180831085502242?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="%E4%BF%AE%E6%94%B9%E5%88%86%E5%8C%BA%E5%90%8D%EF%BC%9A" style="margin-left:0cm;">修改分区名：</h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>alter table t_partition partition(department='xiangsheng',sex='male',howold=20) <br>
rename to partition(department='1',sex='1',howold=20);</strong></span></p>

<h3 id="%E6%B7%BB%E5%8A%A0%E5%88%86%E5%8C%BA%EF%BC%9A" style="margin-left:0cm;">添加分区：</h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>alter table t_partition add partition (department='2',sex='0',howold=40); </strong></span></p>

<h3 id="%E5%88%A0%E9%99%A4%E5%88%86%E5%8C%BA%EF%BC%9A" style="margin-left:0cm;">删除分区：</h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>alter table t_partition drop partition (department='2',sex='2',howold=24); </strong></span></p>

<h3 id="%E4%BF%AE%E6%94%B9%E8%A1%A8%E7%9A%84%E6%96%87%E4%BB%B6%E6%A0%BC%E5%BC%8F%E5%AE%9A%E4%B9%89%EF%BC%9A" style="margin-left:0cm;">修改表的文件格式定义：</h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>ALTER TABLE table_name [PARTITION partitionSpec] SET FILEFORMAT file_format</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>alter table t_partition partition(department='2',sex='0',howold=40 ) set fileformat sequencefile;</strong></span></p>

<h3 id="%E4%BF%AE%E6%94%B9%E5%88%97%E5%90%8D%E5%AE%9A%E4%B9%89%EF%BC%9A" style="margin-left:0cm;">修改列名定义：</h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>ALTER TABLE table_name CHANGE [COLUMN] col_old_name col_new_name column_type [COMMENTcol_comment] [FIRST|(AFTER column_name)]  </strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>alter table t_user change price jiage float first;</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>price为之前的列名 , jiage为新的列名 , float 为字段的类型 ,first 可加可不加 , 加的话该列修改以后会放到第一列 .</strong></span></p>

<h3 id="%E5%A2%9E%E5%8A%A0%2F%E6%9B%BF%E6%8D%A2%E5%88%97%EF%BC%9A" style="margin-left:0cm;">增加/替换列：</h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>ALTER TABLE table_name ADD|REPLACE COLUMNS (col_name data_type[COMMENT col_comment], ...)  </strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>alter table t_user add columns (sex string,addr string);</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>添加列 , 一次可以添加多个列 . (sex,addr)即为新添加的列 , 需要跟上字段类型</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>alter table t_user replace columns (id string,age int,price float);</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>替换 ,直接将原字段替换掉</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>alter table t_junjie replace columns (id int,sex string);</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="316" src="https://img-blog.csdn.net/20180831090610611?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h2 id="3.11%C2%A0%20hive%E6%9F%A5%E8%AF%A2%E8%AF%AD%E6%B3%95">3.11  hive查询语法</h2>

<p style="margin-left:0cm;">sql是一门面向集合的编程语言；</p>

<p style="margin-left:0cm;">select 1;</p>

<p style="margin-left:0cm;">提示：在做小数据量查询测试时，可以让hive将mrjob提交给本地运行器运行，可以在hive会话中设置如下参数：</p>

<h3 id="hive%3Ehive%3E%20set%20hive.exec.mode.local.auto%3Dtrue%3B" style="margin-left:0cm;"><span style="color:#3399ea;"><strong>hive&gt; set hive.exec.mode.local.auto=true;</strong> </span></h3>

<h3 id="hive%3E%20set%20hive.exec.mode.local.auto%3Dtrue%3B" style="margin-left:0cm;"><span style="color:#3399ea;"><strong>hive&gt; set hive.exec.mode.local.auto=true;</strong></span></h3>

<h3 style="margin-left:0cm;"><span style="color:#3399ea;"><strong>hive&gt; set hive.exec.mode.local.auto=true;</strong></span></h3>

<p><strong>基本查询示例</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_access;</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select count(*) from t_access;</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select max(ip) from t_access;</strong></span></p>

<p><strong>条件查询</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_access where access_time&lt;'2017-08-06 15:30:20'</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_access where access_time&lt;'2017-08-06 16:30:20' and ip&gt;'192.168.33.3';</strong></span></p>

<h2 id="3.12%C2%A0%20join%E5%85%B3%E8%81%94%E6%9F%A5%E8%AF%A2%E7%A4%BA%E4%BE%8B" style="margin-left:0cm;"><strong>3.12  join关联查询示例</strong></h2>

<p><span style="color:#3399ea;"><strong>注 : Hive中 join 不支持不等值连接 , 只支持等值连接 , 其他 SQL 支持不等值 join 连接 .</strong></span></p>

<p style="margin-left:0cm;">假如有a.txt文件</p>

<pre class="has">
<code>a,1
b,2
c,3
d,4</code></pre>

<p style="margin-left:0cm;">假如有b.txt文件</p>

<pre class="has">
<code>a,aa
b,bb
d,cc
e,dd</code></pre>

<p style="margin-left:0cm;"><strong>创建  t_a 表和 t_b 表 :</strong></p>

<p><img alt="" class="has" height="122" src="https://img-blog.csdn.net/20180830182935887?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><strong>导入数据 :</strong></p>

<p><img alt="" class="has" height="163" src="https://img-blog.csdn.net/2018083018334852?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.12.1%20%E5%86%85%E8%BF%9E%E6%8E%A5"><strong>3.12.1 内连接</strong></h3>

<p><span style="color:#f33b45;"><strong>select * from t_a a join t_b b on a.name = b.name;</strong></span><br><img alt="" class="has" height="19" src="https://img-blog.csdn.net/20180830191948649?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>结果：</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="119" src="https://img-blog.csdn.net/20180830191917646?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.12.2%20%E5%B7%A6%E5%A4%96%E8%BF%9E%E6%8E%A5" style="margin-left:0cm;"><strong>3.12.2 左外连接</strong></h3>

<p><span style="color:#f33b45;"><strong>select * from t_a a left join t_b b on a.name = b.name;</strong></span></p>

<p><img alt="" class="has" height="26" src="https://img-blog.csdn.net/2018083019212584?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p><strong>结果 : </strong></p>

<p><img alt="" class="has" height="172" src="https://img-blog.csdn.net/20180830192251839?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.12.3%20%E5%8F%B3%E5%A4%96%E8%BF%9E%E6%8E%A5"><strong>3.12.3 右外连接</strong></h3>

<p><span style="color:#f33b45;"><strong>select * from t_a a right join t_b b on a.name = b.name;</strong></span></p>

<p><img alt="" class="has" height="19" src="https://img-blog.csdn.net/20180830192428202?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>结果：</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="143" src="https://img-blog.csdn.net/20180830192516657?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.12.4%20full%20outer%20join" style="margin-left:0cm;"><strong>3.12.4 全外连接  full outer join</strong></h3>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_a a full join t_b b on a.name = b.name;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="23" src="https://img-blog.csdn.net/20180830192643136?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="989"><br><strong>结果：</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="185" src="https://img-blog.csdn.net/20180830192713229?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h3 id="3.12.5%20left%20semi%20join" style="margin-left:0cm;">3.12.5 左半连接  left semi join</h3>

<p style="margin-left:0cm;"><span style="color:#e579b6;"><strong><em>Left semi join </em><em>：相当于join</em><em>连接两个表后产生的数据中的左半部分</em></strong></span></p>

<p style="margin-left:0cm;"><span style="color:#e579b6;"><strong><em>注意： left semi join的 select</em><em>子句中，不能有右表的字段</em></strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select * from t_a a left semi join t_b b on a.name = b.name;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="23" src="https://img-blog.csdn.net/20180830193200976?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;">结果：</p>

<p style="margin-left:0cm;"><img alt="" class="has" height="135" src="https://img-blog.csdn.net/20180830193043495?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<h2 id="3.13%20group%20by%E5%88%86%E7%BB%84%E8%81%9A%E5%90%88" style="margin-left:0cm;">3.13 group by分组聚合</h2>

<p><img alt="" class="has" height="350" src="https://img-blog.csdn.net/20180831141921237?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>注意： 一旦有group by子句，那么，在select子句中就不能有 </strong></span><span style="color:#f33b45;"><strong>（分组字段，聚合函数） 以外</strong></span><span style="color:#3399ea;"><strong>的字段</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>(说的简单通俗一点就是 , 分组以后的查询只能查询分组的字段 , 以及分组以后可以聚合的字段 , 比如最大值 , 最小值 , 求和 , 求平均值等等的</strong></span><span style="color:#f33b45;"><strong>答案只有一个的字段 ,</strong></span><span style="color:#3399ea;"><strong> 如果按性别分组 , 会有 male这种结果 , 我们可以求成绩的最大值 , 或者年龄的平均值 , 又或是年龄的最小值 , 但是不能求姓名 , 因为对应的 male 只有一行 , 而姓名有俩个 , 就会出现俩行<span style="color:#f33b45;"><strong> </strong></span>) . </strong></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>为什么where必须写在group by的前面，为什么group by后面的条件只能用having</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>因为，where是用于在真正执行查询逻辑之前过滤数据用的</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>having是对group by聚合之后的结果进行再过滤；</strong></span></p>

<p style="margin-left:0cm;"><strong>有如下数据 :</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="121" src="https://img-blog.csdn.net/20180831142422339?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>创建一个表 :</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>create table t_user(id int,name string,age int,score int,sex string)</strong></span></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>row format delimited fields terminated by ',';</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="36" src="https://img-blog.csdn.net/20180831142900364?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>导数据 :</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>load data local inpath '/root/user.txt' into table t_user;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="24" src="https://img-blog.csdn.net/20180831143019956?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="175" src="https://img-blog.csdn.net/20180831143100455?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>分组查询 :</strong></p>

<p style="margin-left:0cm;"><strong>1 按性别分组 , 并查询性别以及年龄的最大值</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select max(age),sex from t_user group by sex ;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="129" src="https://img-blog.csdn.net/20180831144007602?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>2 求每一种性别的平均成绩 , 但请过滤掉平均年龄 &gt;25岁的性别 </strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select sex,avg(score) from t_user group by sex having avg(age)&lt;=25;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="107" src="https://img-blog.csdn.net/20180831144718471?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>3 . 求每一种性别的平均成绩 , 但是性别平均年龄&gt;25的不要 , 而且性别平均成绩低于85分的不要 </strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select sex,avg(score) from t_user group by sex having avg(age)&lt;=25 and avg(score)&gt;=85;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="102" src="https://img-blog.csdn.net/2018083114542925?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>4 求每种性别的平均成绩 , 但是成绩低于82分不计入统计 , 并且最后结果中 , 去除性别平均年龄&gt;25岁的;</strong></p>

<p style="margin-left:0cm;"><span style="color:#f33b45;"><strong>select sex,avg(score) from t_user where score&gt;82 group by sex having avg(age)&lt;25;</strong></span></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="95" src="https://img-blog.csdn.net/20180831145942780?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><strong>查询过程图 :</strong></p>

<p style="margin-left:0cm;"><img alt="" class="has" height="393" src="https://img-blog.csdn.net/20180831151349802?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zNTM1MzE4Nw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="950"></p>

<p style="margin-left:0cm;"><span style="color:#3399ea;"><strong>上述语句的执行逻辑：</strong></span></p>

<p><span style="color:#3399ea;"><strong>where过滤不满足条件的数据</strong></span></p>

<p><span style="color:#3399ea;"><strong>用聚合函数和group by进行数据运算聚合，得到聚合结果</strong></span></p>

<p><span style="color:#3399ea;"><strong>用having条件过滤掉聚合结果中不满足条件的数据</strong></span></p>            </div>
                </div>
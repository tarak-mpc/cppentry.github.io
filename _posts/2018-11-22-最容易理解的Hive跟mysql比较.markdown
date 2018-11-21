---
layout:     post
title:      最容易理解的Hive跟mysql比较
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1、查询语言</p>

<p>hive是类是sql的查询语言hql，mysql是sql查询语言</p>

<p>2、数据存储位置</p>

<p>hive存储在hdfs上 mysql存储在本地磁盘上</p>

<p>3、数据更新</p>

<p>hive不支持插入跟更新操作，mysql都可以</p>

<p>4、索引</p>

<p>hive不存在索引 ，mysql有索引</p>

<p>5、执行</p>

<p>hive大多数查询通过mapreduce，mysql是自己的执行引擎</p>

<p>6、执行延迟</p>

<p>hive没有索引，查询要搜索整个表，另外执行需要mapreduce，mr本身的延迟就高，所以hive延迟较高，mysql有索引，查询较快</p>

<p>7、可扩展性</p>

<p>hive是建立在Hadoop之上的 ，因此 Hive 的可扩展性是和 Hadoop 的可扩展性是一致的（世界上最大的Hadoop 集群在Yahoo!，2009 年的规模在 4000 台节点左右）</p>

<p>mysql吗没有扩展性</p>

<p>8、数据规模</p>

<p>hive处理大数据，mysql相对较小</p>            </div>
                </div>
---
layout:     post
title:      Spark SQL 笔记(4)——Spark SQL 介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012292754/article/details/83616262				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="1_Spark_SQL__0"></a>1 Spark SQL 背景介绍</h1>
<h2><a id="11_Hive__1"></a>1.1 Hive 介绍</h2>
<ul>
<li>类似 sql 的 Hive QL 语言， sql -&gt; mapreduce</li>
<li>改进： hive on tez，hive on spark,  hive on mapreduce</li>
</ul>
<h2><a id="12_Spark_SQL__4"></a>1.2 Spark SQL 前世</h2>
<ul>
<li>hive on spark -&gt; shark</li>
<li>shark，基于 spark,基于内存的列式存储，与 hive 能够兼容</li>
<li>shark 缺点： hive ql 的解析、逻辑执行计划生成、执行计划的优化是依赖 hive 的；仅仅只是把物理执行计划从 mr 作业替换成 spark 作业</li>
</ul>
<h2><a id="13_Shark__8"></a>1.3 Shark 终止</h2>
<p>Shark 终止后，产生了 2 个分支：</p>
<ol>
<li>hive on spark: Hive 社区，源码在 Hive 中</li>
<li>Spark SQL： Spark SQL 社区；支持多种数据源<br>
<img src="https://img-blog.csdnimg.cn/2018110111500933.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></li>
</ol>
<h2><a id="14_SQL_on_Hadoop__13"></a>1.4 SQL on Hadoop 常用框架</h2>
<ol>
<li>Hive ,sql -&gt; mapreduce;facebook 开源; metastore:元数据；</li>
<li>impala ，cloudera公司:cdh(建议生产中使用)；cm；sql:自己的守护进程执行的，非mr；metastore:元数据；</li>
<li>presto: facebook 开源；京东；sql</li>
<li>drill: sql; 访问 ： hdfs 、rdbms,json,hbase,mangodb,s3,hive</li>
<li>Spark SQL: sql; dataframe/dataset api; metastore; 访问 ： hdfs 、rdbms,json,hbase,mangodb,s3,hive<br>
<img src="https://img-blog.csdnimg.cn/20181101115350714.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></li>
</ol>
<h2><a id="15_Spark_SQL__20"></a>1.5 Spark SQL 总结</h2>
<ul>
<li>应用不局限于 SQL</li>
<li>访问 hive,json,parquet 等文件的数据；</li>
</ul>
<h2><a id="16_Spark_SQL__23"></a>1.6 Spark SQL 架构</h2>
<p><img src="https://img-blog.csdnimg.cn/20181101121346898.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
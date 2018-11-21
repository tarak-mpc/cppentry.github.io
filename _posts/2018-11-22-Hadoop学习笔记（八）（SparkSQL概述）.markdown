---
layout:     post
title:      Hadoop学习笔记（八）（SparkSQL概述）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/victorzzzz/article/details/83421542				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>

<p><strong>Hive: </strong>类似于sql的Hive QL语言， sql==<span style="color:#f33b45;">（转换）</span>==&gt;mapreduce</p>

<ul><li>    <strong>特点：</strong>底层是mapreduce，效率不高</li>
	<li>    <strong>改进：</strong>执行引擎更换：hive on tez、hive on spark、hive on mapreduce</li>
</ul><p><strong>Spark：</strong>hive on spark ==&gt; shark(hive on spark) shark翻译成RDD组件</p>

<p><strong>Shark</strong></p>

<ul><li><strong>特点：</strong></li>
</ul><p>                （1）基于spark （2）基于内存的列式存储 （3）与hive能够兼容</p>

<ul><li><strong>缺点：</strong>hive ql的解析、逻辑执行计划生成、执行计划的优化是依赖于hive的</li>
</ul><p>                      仅仅只是把物理执行计划从mr作业替换成spark作业</p>

<p><br><strong>Shark终止以后，产生了2个分支：</strong><br><span style="color:#f33b45;">1）</span><strong>hive on spark</strong><br>
                 Hive社区，源码是在Hive中<br><span style="color:#f33b45;">2）</span><strong>Spark SQL</strong><br>
                 Spark社区，源码是在Spark中<br>
                 支持多种数据源，多种优化技术，扩展性好很多</p>

<p>【可以提供SQL on Hadoop的组件】<br><strong>1）Hive</strong><br>
    sql ==&gt; mapreduce<br>
    metastore ： 元数据<br>
    sql：database、table、view<br>
    facebook</p>

<p><strong>2）impala</strong><br>
    cloudera ： cdh（建议大家在生产上使用的hadoop系列版本）、cm<br>
    sql：自己的守护进程执行的，非mr，基于内存的<br>
    metastore</p>

<p><strong>3）presto</strong><br>
    facebook<br>
    京东<br>
    sql</p>

<p><strong>4）drill</strong><br>
    sql，交互式<br>
    访问：hdfs、rdbms、json、hbase、mongodb、s3、hive</p>

<p><strong>5）Spark SQL</strong></p>

<p>   <u><em>Spark SQL is Apache Spark's module for working with structured data.</em></u><br>
    sql<br>
    dataframe/dataset api<br>
    metastore<br>
    访问：hdfs、rdbms、json、hbase、mongodb、s3、hive  ==&gt; 外部数据源</p>

<p><br>
Spark SQL它不仅仅有访问或者操作SQL的功能，还提供了其他的非常丰富的操作：外部数据源、优化</p>

<p><span style="color:#f33b45;"><strong>Spark SQL概述小结：</strong></span><br>
1）Spark SQL的应用并不局限于SQL；<br>
2）访问hive、json、parquet等文件的数据；<br>
3）SQL只是Spark SQL的一个功能而已；===&gt; Spark SQL这个名字起的并不恰当<br>
4）Spark SQL提供了SQL的api、DataFrame和Dataset的API；</p>

<p>Spark SQL 架构（待完善...）</p>

<p> </p>

<p> </p>            </div>
                </div>
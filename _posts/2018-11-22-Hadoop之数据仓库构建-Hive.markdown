---
layout:     post
title:      Hadoop之数据仓库构建-Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
目录</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">Hive是什么<span>... </span><span>1</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">hadoop是什么<span>... </span><span>1</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">hadoop、hive两者关系<span>... </span><span>2</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">hive的优缺点<span>... </span><span>2</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">hive的存在意义<span>... </span><span>2</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">hive常用命令<span>... </span><span>2</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">一些相关命令<span>... </span><span>2</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">创建表<span>... </span><span>3</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">创建分区partition_table.dt<span>. </span><span>3</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">加载数据到分区<span>... </span><span>4</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">删除表 drop
 table partition_table<span>. </span><span>4</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">桶表<span>... </span><span>4</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">内部表与外部表:<span>4</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">UDF、UDAF、UDTF:<span>4</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">hive使用技巧与优化方法<span>... </span><span>4</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">3.1 
 重点参数设置<span>... </span><span>4</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">3.2 
 MapJoin<span>. </span><span>4</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">3.3 
 数据倾斜问题原因查找与解决方法<span>... </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">3.4 
 减少重复IO<span>.. </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">3.5 
 Map/Reduce参数的优化设置<span>... </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">FAQ<span>.. </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">4.1
 执行慢或卡死<span>... </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">4.2
 突然报异常，重跑后正常了/重跑后不正常?<span>. </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">4.3
 任务执行忽快忽慢，如之奈何<span>... </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">4.4
 数据评估一个任务合理的运行时间<span>... </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;">4.5
 如何系统地完成一个hive任务的设计与实现<span>... </span><span>5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a style="color:rgb(51,102,153);text-decoration:none;"><span style="color:#336699;">参考资料：</span><span style="text-decoration:none;">... </span><span style="text-decoration:none;">5</span></a></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 </p>
<h1 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t0" style="color:rgb(51,102,153);"></a><a name="_Toc432407588" style="color:rgb(51,102,153);">Hive</a>是什么</h1>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t1" style="color:rgb(51,102,153);"></a><a name="_Toc432407589" style="color:rgb(51,102,153);">hadoop</a>是什么</h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
分布式存储系统(HDFS)  </p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
并行计算框架(Map/Reduce) </p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t2" style="color:rgb(51,102,153);"></a><a name="_Toc432407590" style="color:rgb(51,102,153);">hadoop</a>、hive两者关系</h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
   前者为Core,后者为Shell</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
   前者为驱体，后者为形状</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t3" style="color:rgb(51,102,153);"></a><a name="_Toc432407591" style="color:rgb(51,102,153);">hive</a>的优缺点</h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
    过度封装，易于使用，但难理其所以然，如遇异常情况经常使初学者或欠经验者卡壳。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
开放出来的API不够丰富灵活，个性化优化偶尔会比较困难，必要时要写原生态的MR。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
存在很多版本上的bug,有一定的不稳定性，升级成本较高。而原生态的MR要小很多。</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t4" style="color:rgb(51,102,153);"></a><a name="_Toc432407592" style="color:rgb(51,102,153);">hive</a>的存在意义</h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
易于构建数据仓库：承载<a href="http://lib.csdn.net/base/spark" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">大数据</a>量、易扩展、接口统一、易于维护</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
BI等轻开发等人员的需要</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
降低大数据应用研发门槛</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">Hadoop</a>生态圈的重要一环</p>
<h1 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t5" style="color:rgb(51,102,153);"></a><a name="_Toc432407593" style="color:rgb(51,102,153);">hive</a>常用命令</h1>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t6" style="color:rgb(51,102,153);"></a><a name="_Toc432407594" style="color:rgb(51,102,153);">一些相关命令</a></h2>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
SHOW TABLES; #查看所有的表</p>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
SHOW TABLES '*TMP*'; #支持模糊查询</p>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
SHOWPARTITIONS TMP_TABLE; #查看表有哪些分区</p>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
DESCRIBE TMP_TABLE; #查看表结构</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t7" style="color:rgb(51,102,153);"></a><a name="_Toc432407595" style="color:rgb(51,102,153);">创建表</a></h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
CREATE TABLE tmp_table #表名</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(102,204,102);">(</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
title   string, #字段名称 字段类型</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
minimum_bid     double,</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
quantity        bigint,</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
have_invoice    bigint</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
)COMMENT'注释：XXX' #表注释</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 PARTITIONED BY(pt STRING) #分区表字段（如果你文件非常之大的话，采用分区表可以快过滤出按分区字段划分的数据）</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 ROW FORMAT DELIMITED</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
   FIELDSTERMINATED BY '\001'   # 字段是用什么分割开的</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'; #用哪种方式存储数据</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t8" style="color:rgb(51,102,153);"></a><a name="_Toc432407596" style="color:rgb(51,102,153);">创建分区partition_table.dt</a></h2>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
创建表</p>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
createtable partition_table(c1 string,c2 string)</p>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 partitioned by(daytime string,city string)</p>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
row formatdelimited fields terminated by '\t'</p>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
stored asTEXTFILE;</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t9" style="color:rgb(51,102,153);"></a><a name="_Toc432407597" style="color:rgb(51,102,153);">加载数据到分区</a></h2>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
load data local inpath '/home/partition_table.dat'into table</p>
<p align="left" style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 partition_tablepartition(daytime='2013-02-01',city='bj');</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t10" style="color:rgb(51,102,153);"></a><a name="_Toc432407598" style="color:rgb(51,102,153);">删除表 drop table partition_table</a></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t11" style="color:rgb(51,102,153);"></a><a name="_Toc432407599" style="color:rgb(51,102,153);">桶表</a></h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
    桶表是对数据进行哈希取值，然后放到不同文件中存储。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
创建表create table bucket_table(id string) clustered by(id) into 4buckets; 加载数据set hive.enforce.bucketing = true;</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
数据加载到桶表时，会对字段取hash值，然后与桶的数量取模。把数据放到对应的文件中。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
T1, f1,</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
T2 f2</p>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t12" style="color:rgb(51,102,153);"></a><a name="_Toc432407600" style="color:rgb(51,102,153);">内部表与外部表:</a></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t13" style="color:rgb(51,102,153);"></a><a name="_Toc432407601" style="color:rgb(51,102,153);">UDF</a>、UDAF、UDTF:</h2>
<h1 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t14" style="color:rgb(51,102,153);"></a><a name="_Toc432407602" style="color:rgb(51,102,153);">hive</a>使用技巧与优化方法</h1>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t15" style="color:rgb(51,102,153);"></a><a name="_Toc432407603" style="color:rgb(51,102,153);">3.1  </a><strong>重点参数设置</strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t16" style="color:rgb(51,102,153);"></a><strong><em>MR:Map(combiner),Reduce(copy,sort,reduce)</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t17" style="color:rgb(51,102,153);"></a><strong><em>set hive.map.aggr=true;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t18" style="color:rgb(51,102,153);"></a><strong><em>set hive.exec.dynamic.partition=true;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t19" style="color:rgb(51,102,153);"></a><strong><em>set hive.exec.dynamic.partition.mode=nonstrict;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t20" style="color:rgb(51,102,153);"></a><strong><em>set hive.exec.parallel=true;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t21" style="color:rgb(51,102,153);"></a><strong><em>set hive.exec.parallel.thread.number=4;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t22" style="color:rgb(51,102,153);"></a><strong><em>set hive.groupby.skewindata=false;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t23" style="color:rgb(51,102,153);"></a><strong><em>set mapreduce.reduce.shuffle.memory.limit.percent=0.01;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t24" style="color:rgb(51,102,153);"></a><strong><em>set mapreduce.reduce.shuffle.input.buffer.percent=0.01;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t25" style="color:rgb(51,102,153);"></a><strong><em>set mapred.reduce.copy.backoff=20;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t26" style="color:rgb(51,102,153);"></a><strong><em>set io.sort.mb=200;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t27" style="color:rgb(51,102,153);"></a><strong><em>set mapred.max.split.size=536870912;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t28" style="color:rgb(51,102,153);"></a><strong><em>set mapred.child.java.opts=-Xms512m -Xmx5120m;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t29" style="color:rgb(51,102,153);"></a><strong><em>set mapred.job.map.memory.mb=3072;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t30" style="color:rgb(51,102,153);"></a><strong><em>set mapred.job.reduce.memory.mb=5120;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t31" style="color:rgb(51,102,153);"></a><strong><em>set mapred.reduce.tasks.speculative.execution=true;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t32" style="color:rgb(51,102,153);"></a><strong><em>set mapred.map.tasks.speculative.execution=true;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t33" style="color:rgb(51,102,153);"></a><strong><em>set hive.mapred.reduce.tasks.speculative.execution=true;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t34" style="color:rgb(51,102,153);"></a><strong><em>set mapred.reduce.tasks=1500;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t35" style="color:rgb(51,102,153);"></a><strong><em>set mapred.reduce.slowstart.completed.maps=0.95;</em></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t36" style="color:rgb(51,102,153);"></a><a name="_Toc432407604" style="color:rgb(51,102,153);">3.2  MapJoin</a><strong></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t37" style="color:rgb(51,102,153);"></a><strong>   </strong>http://blog.csdn.net/liuxianbing119/article/details/8668635<strong></strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t38" style="color:rgb(51,102,153);"></a><a name="_Toc432407605" style="color:rgb(51,102,153);">3.3 </a><strong>数据倾斜问题原因查找与解决方法</strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t39" style="color:rgb(51,102,153);"></a><a name="_Toc432407606" style="color:rgb(51,102,153);">3.4  </a><strong>减少重复IO</strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t40" style="color:rgb(51,102,153);"></a><strong>      一次查询，多次插入，即Multi-groupBy操作</strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t41" style="color:rgb(51,102,153);"></a><a name="_Toc432407607" style="color:rgb(51,102,153);">3.5  Map/Reduce</a><strong>参数的优化设置</strong></h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<strong> </strong></p>
<h1 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t42" style="color:rgb(51,102,153);"></a><a name="_Toc432407608" style="color:rgb(51,102,153);">FAQ</a></h1>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t43" style="color:rgb(51,102,153);"></a><strong>      <a name="_Toc432407609" style="color:rgb(51,102,153);">4.1 </a>执行慢或卡死</strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t44" style="color:rgb(51,102,153);"></a><strong>      <a name="_Toc432407610" style="color:rgb(51,102,153);">4.2 </a>突然报异常，重跑后正常了/重跑后不正常?</strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t45" style="color:rgb(51,102,153);"></a><strong>      <a name="_Toc432407611" style="color:rgb(51,102,153);">4.3 </a>任务执行忽快忽慢，如之奈何</strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t46" style="color:rgb(51,102,153);"></a><strong>      <a name="_Toc432407612" style="color:rgb(51,102,153);">4.4 </a>数据评估一个任务合理的运行时间</strong></h2>
<h2 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t47" style="color:rgb(51,102,153);"></a><strong>      <a name="_Toc432407613" style="color:rgb(51,102,153);">4.5 </a>如何系统地完成一个hive任务的设计与实现         </strong></h2>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<strong> </strong></p>
<h1 style="color:rgb(51,51,51);font-family:Arial;line-height:26px;">
<a name="t48" style="color:rgb(51,102,153);"></a><a name="_Toc432407614" style="color:rgb(51,102,153);">参考资料：</a></h1>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<strong>  天亮教育云分享地址：</strong><span style="color:rgb(51,102,153);text-decoration:none;"><strong><a href="http://pan.baidu.com/s/1pJJrcqJ" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">http://pan.baidu.com/s/1pJJrcqJ</a></strong></span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<strong>  技术交流群：320349384</strong></p>
            </div>
                </div>
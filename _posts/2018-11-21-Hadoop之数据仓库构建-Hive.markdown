---
layout:     post
title:      Hadoop之数据仓库构建-Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>目录</p>
<p><a>Hive是什么<span>...
</span><span>1</span></a></p>
<p><a>hadoop是什么<span>...
</span><span>1</span></a></p>
<p><a>hadoop、hive两者关系<span>...
</span><span>2</span></a></p>
<p><a>hive的优缺点<span>...
</span><span>2</span></a></p>
<p><a>hive的存在意义<span>...
</span><span>2</span></a></p>
<p><a>hive常用命令<span>...
</span><span>2</span></a></p>
<p><a>一些相关命令<span>...
</span><span>2</span></a></p>
<p><a>创建表<span>...
</span><span>3</span></a></p>
<p><a>创建分区partition_table.dt<span>.
</span><span>3</span></a></p>
<p><a>加载数据到分区<span>...
</span><span>4</span></a></p>
<p><a>删除表 drop
 table partition_table<span>. </span><span>4</span></a></p>
<p><a>桶表<span>...
</span><span>4</span></a></p>
<p><a>内部表与外部表:<span>4</span></a></p>
<p><a>UDF、UDAF、UDTF:<span>4</span></a></p>
<p><a>hive使用技巧与优化方法<span>...
</span><span>4</span></a></p>
<p><a>3.1 
 重点参数设置<span>... </span><span>4</span></a></p>
<p><a>3.2 
 MapJoin<span>. </span><span>4</span></a></p>
<p><a>3.3 
 数据倾斜问题原因查找与解决方法<span>... </span><span>5</span></a></p>
<p><a>3.4 
 减少重复IO<span>.. </span><span>5</span></a></p>
<p><a>3.5 
 Map/Reduce参数的优化设置<span>... </span><span>5</span></a></p>
<p><a>FAQ<span>..
</span><span>5</span></a></p>
<p><a>4.1
 执行慢或卡死<span>... </span><span>5</span></a></p>
<p><a>4.2
 突然报异常，重跑后正常了/重跑后不正常?<span>. </span><span>5</span></a></p>
<p><a>4.3
 任务执行忽快忽慢，如之奈何<span>... </span><span>5</span></a></p>
<p><a>4.4
 数据评估一个任务合理的运行时间<span>... </span><span>5</span></a></p>
<p><a>4.5
 如何系统地完成一个hive任务的设计与实现<span>... </span><span>5</span></a></p>
<p><a>参考资料：<span>...
</span><span>5</span></a></p>
<p><a>QA<span>.
</span><span>6</span></a></p>
<p><a>自由问答时间<span>...
</span><span>6</span></a></p>
<p> </p>
<h1><a name="_Toc432407588">Hive</a>是什么</h1>
<h2><a name="_Toc432407589">hadoop</a>是什么</h2>
<p>分布式存储系统(HDFS)   </p>
<p>并行计算框架(Map/Reduce)  </p>
<h2><a name="_Toc432407590">hadoop</a>、hive两者关系</h2>
<p>   前者为Core,后者为Shell</p>
<p>   前者为驱体，后者为形状</p>
<h2><a name="_Toc432407591">hive</a>的优缺点</h2>
<p>    过度封装，易于使用，但难理其所以然，如遇异常情况经常使初学者或欠经验者卡壳。</p>
<p>开放出来的API不够丰富灵活，个性化优化偶尔会比较困难，必要时要写原生态的MR。</p>
<p>存在很多版本上的bug,有一定的不稳定性，升级成本较高。而原生态的MR要小很多。</p>
<h2><a name="_Toc432407592">hive</a>的存在意义</h2>
<p>易于构建数据仓库：承载大数据量、易扩展、接口统一、易于维护</p>
<p>BI等轻开发等人员的需要</p>
<p>降低大数据应用研发门槛</p>
<p>Hadoop生态圈的重要一环</p>
<h1><a name="_Toc432407593">hive</a>常用命令</h1>
<h2><a name="_Toc432407594">一些相关命令</a> </h2>
<p align="left">SHOW TABLES; #查看所有的表 </p>
<p align="left">SHOW TABLES '*TMP*'; #支持模糊查询 </p>
<p align="left">SHOWPARTITIONS TMP_TABLE; #查看表有哪些分区 </p>
<p align="left">DESCRIBE TMP_TABLE; #查看表结构 </p>
<h2><a name="_Toc432407595">创建表</a></h2>
<p>CREATE TABLE tmp_table #表名 </p>
<p><span style="color:#66CC66;">(</span> </p>
<p>title   string, #字段名称 字段类型 </p>
<p>minimum_bid     double, </p>
<p>quantity        bigint, </p>
<p>have_invoice    bigint </p>
<p>)COMMENT'注释：XXX' #表注释 </p>
<p> PARTITIONED BY(pt STRING) #分区表字段（如果你文件非常之大的话，采用分区表可以快过滤出按分区字段划分的数据） </p>
<p> ROW FORMAT DELIMITED </p>
<p>   FIELDSTERMINATED BY '\001'   # 字段是用什么分割开的 </p>
<p>STORED AS INPUTFORMAT 'org.apache.hadoop.mapred.TextInputFormat'; #用哪种方式存储数据 </p>
<h2><a name="_Toc432407596">创建分区partition_table.dt</a> </h2>
<p align="left">创建表 </p>
<p align="left">createtable partition_table(c1 string,c2 string)</p>
<p align="left"> partitioned by(daytime string,city string) </p>
<p align="left">row formatdelimited fields terminated by '\t' </p>
<p align="left">stored asTEXTFILE; </p>
<h2><a name="_Toc432407597">加载数据到分区</a> </h2>
<p align="left">load data local inpath '/home/partition_table.dat'into table</p>
<p align="left"> partition_tablepartition(daytime='2013-02-01',city='bj'); </p>
<h2><a name="_Toc432407598">删除表 drop table partition_table</a></h2>
<h2><a name="_Toc432407599">桶表</a></h2>
<p>    桶表是对数据进行哈希取值，然后放到不同文件中存储。 </p>
<p>创建表create table bucket_table(id string) clustered by(id) into 4buckets; 加载数据set hive.enforce.bucketing = true;
</p>
<p>数据加载到桶表时，会对字段取hash值，然后与桶的数量取模。把数据放到对应的文件中。</p>
<p>T1, f1,</p>
<p>T2 f2 </p>
<h2><a name="_Toc432407600">内部表与外部表:</a></h2>
<h2><a name="_Toc432407601">UDF</a>、UDAF、UDTF:</h2>
<h1><a name="_Toc432407602">hive</a>使用技巧与优化方法</h1>
<h2><a name="_Toc432407603"><strong>3.1  </strong></a><strong>重点参数设置</strong></h2>
<h2><strong><em>MR:Map(combiner),Reduce(copy,sort,reduce)</em></strong></h2>
<h2><strong><em>set hive.map.aggr=true;</em></strong></h2>
<h2><strong><em>set hive.exec.dynamic.partition=true;</em></strong></h2>
<h2><strong><em>set hive.exec.dynamic.partition.mode=nonstrict;</em></strong></h2>
<h2><strong><em>set hive.exec.parallel=true;</em></strong></h2>
<h2><strong><em>set hive.exec.parallel.thread.number=4;</em></strong></h2>
<h2><strong><em>set hive.groupby.skewindata=false;</em></strong></h2>
<h2><strong><em>set mapreduce.reduce.shuffle.memory.limit.percent=0.01;</em></strong></h2>
<h2><strong><em>set mapreduce.reduce.shuffle.input.buffer.percent=0.01;</em></strong></h2>
<h2><strong><em>set mapred.reduce.copy.backoff=20;</em></strong></h2>
<h2><strong><em>set io.sort.mb=200;</em></strong></h2>
<h2><strong><em>set mapred.max.split.size=536870912;</em></strong></h2>
<h2><strong><em>set mapred.child.java.opts=-Xms512m -Xmx5120m;</em></strong></h2>
<h2><strong><em>set mapred.job.map.memory.mb=3072;</em></strong></h2>
<h2><strong><em>set mapred.job.reduce.memory.mb=5120;</em></strong></h2>
<h2><strong><em>set mapred.reduce.tasks.speculative.execution=true;</em></strong></h2>
<h2><strong><em>set mapred.map.tasks.speculative.execution=true;</em></strong></h2>
<h2><strong><em>set hive.mapred.reduce.tasks.speculative.execution=true;</em></strong></h2>
<h2><strong><em>set mapred.reduce.tasks=1500;</em></strong></h2>
<h2><strong><em>set mapred.reduce.slowstart.completed.maps=0.95;</em></strong></h2>
<h2><a name="_Toc432407604"><strong>3.2  MapJoin</strong></a><strong></strong></h2>
<h2><strong>   </strong>http://blog.csdn.net/liuxianbing119/article/details/8668635<strong></strong></h2>
<h2><a name="_Toc432407605"><strong>3.3 </strong></a><strong>数据倾斜问题原因查找与解决方法</strong></h2>
<h2><a name="_Toc432407606"><strong>3.4  </strong></a><strong>减少重复IO</strong></h2>
<h2><strong>      一次查询，多次插入，即Multi-groupBy操作</strong></h2>
<h2><a name="_Toc432407607"><strong>3.5  Map/Reduce</strong></a><strong>参数的优化设置</strong></h2>
<p><strong> </strong></p>
<h1><a name="_Toc432407608">FAQ</a></h1>
<h2><strong>      <a name="_Toc432407609">4.1 </a>执行慢或卡死</strong></h2>
<h2><strong>      <a name="_Toc432407610">4.2 </a>突然报异常，重跑后正常了/重跑后不正常?</strong></h2>
<h2><strong>      <a name="_Toc432407611">4.3 </a>任务执行忽快忽慢，如之奈何</strong></h2>
<h2><strong>      <a name="_Toc432407612">4.4 </a>数据评估一个任务合理的运行时间</strong></h2>
<h2><strong>      <a name="_Toc432407613">4.5 </a>如何系统地完成一个hive任务的设计与实现         
</strong></h2>
<p><strong> </strong></p>
<h1><a name="_Toc432407614">参考资料：</a></h1>
<p><strong>  天亮教育云分享地址：</strong><a href="http://pan.baidu.com/s/1pJJrcqJ" rel="nofollow"><strong>http://pan.baidu.com/s/1pJJrcqJ</strong></a><strong></strong></p>
<p><strong>  Csdn博客： </strong><a href="http://blog.csdn.net/erliang20088" rel="nofollow"><strong>http://blog.csdn.net/erliang20088</strong></a><strong></strong></p>
<p><strong>  技术交流群：320349384</strong></p>
<p><strong> </strong></p>
<h1><a name="_Toc432407615">QA</a></h1>
<h2><a name="_Toc432407616">自由问答时间</a></h2>
<p><strong> </strong></p>
            </div>
                </div>
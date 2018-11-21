---
layout:     post
title:      HBase--NoSQL的数据库产品
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<div>
<div>
<p>1、HBase简介</p>
<p style="color:#000000;">HBase – HadoopDatabase，是一个高可靠性、高性能、面向列、可伸缩的分布式存储系统，利用HBase技术可在廉价PC Server上搭建起大规模结构化存储集群。HBase利用Hadoop HDFS作为其文件存储系统，利用Hadoop MapReduce来处理HBase中的海量数据，利用Zookeeper作为协调工具。</p>
<p> </p>
<p>hbase是bigtable的开源山寨版本。是建立的hdfs之上，提供高可靠性、高性能、列存储、可伸缩、实时读写的数据库系统。</p>
<p>它介于nosql和RDBMS之间，仅能通过主键(rowkey)和主键的range来检索数据，仅支持单行事务(可通过hive支持来实现多表join等复杂操作)。主要用来存储非结构化和半结构化的松散数据。</p>
<p>与hadoop一样，Hbase目标主要依靠横向扩展，通过不断增加廉价的商用服务器，来增加计算和存储能力。</p>
<p>2、HBase中的表一般有这样的特点：</p>
<ul type="disc"><li>大：一个表可以有上亿行，上百万列</li><li>面向列:面向列(族)的存储和权限控制，列(族)独立检索。</li><li>稀疏:对于为空(null)的列，并不占用存储空间，因此，表可以设计的非常稀疏。</li></ul><p>3、Hbase在Hadoop Ecosystem中的位置。</p>
<div>
</div>
<p>4、HBase的特点</p>
<div>
<table cellpadding="0" cellspacing="0" border="1"><tbody><tr><td>
<p>表(table)——用于存储数据的，数据也是存放在表中的。</p>
<p>行(row)——嵌套的说法。</p>
<p>行键(row key)——类似于主键，也是用于区分每一行的。</p>
<p>列簇(column family)——列的集合</p>
<p>列(column)——相当于RDBMS中的字段</p>
<p>单元格(cell)——单元格用于存储数据的，存储的事多份，按时间戳来区分</p>
<p>多个版本的数据——带有时间戳的数据，默认取最新的数值</p>
<p> </p>
<p>hbase中的数据都是字节数组存储的，所以比较时按照一个个字节比较。</p>
<p>hbase中的记录是按照rowkey排序存储的。</p>
<p>hbase中的列簇根据大小划分为很多的region，不同的region可以存储在不同的region服务器（regionServer）中。那么，海量数据的查询就可以转换为<span style="color:#FF0000;">并行查询</span>了。</p>
<p>每个region中存放的数据范围[startkey，stopkey]</p>
<p> </p>
<p>预分区操作指的是在建表的时候，预先划分成指定数量的region，插入数据时均匀的分散到不同的region中。</p>
<p> </p>
<p>在hbase中，有一张表叫做.META.，里面存储的是用户表的regionserver与rowkey的映射关系。</p>
<p>在hbase中，有一张表叫做-ROOT-，里面存储的是.META.的rowkey与regionserver的映射关系。</p>
<p> </p>
<p style="color:#FF0000;">读取流程图：</p>
<p> </p>
<p>表示hbase某张表中的一行记录，看rowkey确定一行记录。</p>
<p>行中有很多列。这些列分到很多列簇中存储。</p>
<p>每一个列簇是一个独立的存储文件。</p>
</td>
</tr></tbody></table></div>
<p>5、常见问题</p>
<div>
<table cellpadding="0" cellspacing="0" border="1"><tbody><tr><td>
<p>问：为什么一行记录含有多个时间戳的值？</p>
<p>答：因为这样能够体现聚合的思想，实际上体现了RDBMS中的一对多问题。</p>
<p> </p>
<p>问：为什么一行记录按照列簇分成独立的物理文件存储，而不是一行记录全部放到一起存储？</p>
<p>答：因为查询时，一行记录并不是全部字段都需要，只需要一部分字段。如果一行记录所有字段的值放在一起的话，那么一次性读取所有字段，显然很浪费。分别存储的话， 一次查询只需要少量字段，那么整体的查询吞吐量就会提高。</p>
<p> </p>
<p>问：建立索引时为什么key是regionserver，value是rowkey；而不是相反的哪？</p>
<p>答：因为rowkey的数量太大了，按照上面的方式建立索引的话，映射数据量就太大。</p>
<p> </p>
<p> </p>
<p>问：设计rowkey是hbase表设计中的重点？</p>
<p>答：(1)因为hbase查询默认是按照rowkey查询的，因此业务上最需要的查询条件应该设计到rowkey的组成中</p>
<p>    (2)因为hbase表分区的话，那么再插入数据的时候容易产生热点。为了避免热点，插入数据应该尽可能随机，需要注意rowkey的设计，最好是rowkey的开始节点都是随机的或者接近随机的。</p>
<p> </p>
</td>
</tr></tbody></table></div>
<p>6、集群搭建（查看端口60010）</p>
<div>
<table cellpadding="0" cellspacing="0" border="1"><tbody><tr><td>
<p>hbase的集群搭建过程：</p>
<p>********************注意:(1)hadoop必须启动，并且可以写入文件(2)zookeeper必须在各个节点启动成功****************************</p>
<p>1.确定集群结构，以hadoop0作为主节点，hadoop1和hadoop2作为从节点</p>
<p>2.在hadoop0、hadoop1、hadoop2上分别启动ZooKeeper，命令是zkServer.sh start</p>
<p>3.在hadoop0的现有的伪分布配置上做修改</p>
<p style="color:#000000;">修改/etc/profile文件。</p>
<p style="color:#000000;">#vi /etc/profile</p>
<p style="color:#000000;">增加 </p>
<p style="color:#000000;">export HBASE_HOME=/home/hbase</p>
<p style="color:#000000;">修改 </p>
<p style="color:#000000;">export PATH=$JAVA_HOME/bin:$PATH:$HADOOP_HOME/bin:$HBASE_HOME/bin</p>
<p style="color:#000000;">保存退出</p>
<p style="color:#000000;">#source /etc/profile</p>
<p>3.1 修改conf/hbase-env.sh，修改</p>
<p style="color:#000000;">export JAVA_HOME=/usr/local/jdk</p>
<p style="color:#000000;">export HBASE_MANAGES_ZK=false</p>
<p>3.2 修改conf/hbase-site.xml，修改hbase.zookeeper.quorum的值改为hadoop0,hadoop1,hadoop2</p>
<p style="color:#000000;">修改$HBASE_HOME/conf/hbase-site.xml</p>
<p style="color:#000000;">&lt;property&gt;</p>
<p style="color:#000000;">  &lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p style="color:#000000;">  &lt;value&gt;hdfs://hadoop0:9000/hbase&lt;/value&gt;</p>
<p style="color:#000000;">&lt;/property&gt;</p>
<p style="color:#000000;">&lt;property&gt;</p>
<p style="color:#000000;">  &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</p>
<p style="color:#000000;">  &lt;value&gt;true&lt;/value&gt;</p>
<p style="color:#000000;">&lt;/property&gt;</p>
<p style="color:#000000;">&lt;property&gt;</p>
<p style="color:#000000;">  &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;</p>
<p style="color:#000000;">  &lt;value&gt;hadoop0&lt;/value&gt;</p>
<p style="color:#000000;">&lt;/property&gt;</p>
<p style="color:#000000;">&lt;property&gt;</p>
<p style="color:#000000;">  &lt;name&gt;dfs.replication&lt;/name&gt;</p>
<p style="color:#000000;">  &lt;value&gt;1&lt;/value&gt;</p>
<p style="color:#000000;">&lt;/property&gt;</p>
<p style="color:#000000;">注意：$HBASE_HOME/conf/hbase-site.xml的hbase.rootdir的主机和端口号与$HADOOP_HOME/conf/core-site.xml的fs.default.name的主机和端口号一致</p>
<p>3.3 修改conf/regionservers，修改内容为hadoop1、hadoop2(每个一行)</p>
<p>4.把hbase文件夹复制到hadoop1、hadoop2的对应目录下，执行命令</p>
<p>   scp -r /usr/local/hbase   hadoop1:/usr/local</p>
<p>   scp -r /usr/local/hbase   hadoop2:/usr/local</p>
<p>5.把hadoop0上的/etc/profile复制到其他节点，然后在其他节点执行source /etc/profile</p>
<p>6.首先关闭伪分布，执行命令stop-hbase.sh</p>
<p>7.在hadoop0上执行命令start-hbase.sh，启动集群</p>
<p> </p>
<p> </p>
<p>list，查看表</p>
<p> </p>
<p style="color:#FF0000;">实时数据库openTSDB</p>
</td>
<td>
<p> </p>
</td>
</tr></tbody></table></div>
</div>
</div>
</div>
            </div>
                </div>
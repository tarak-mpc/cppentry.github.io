---
layout:     post
title:      hadoop overview
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <ul><li>
<strong>Hadoop生态圈</strong>
</li>
</ul><p>貌似翻开任何一本介绍hadoop的书籍这都是必须的，好吧，就是这些：</p>
<div class="quote_title"> 写道</div>
<div class="quote_div">
<strong>The project includes these subprojects:</strong>
<br>
Hadoop Common: The common utilities that support the other Hadoop subprojects.<br>
Hadoop Distributed File System (HDFS™): A distributed file system that provides high-throughput access to application data.<br>
Hadoop MapReduce: A software framework for distributed processing of large data sets on compute clusters.<br><br><strong>Other Hadoop-related projects at Apache include:</strong>
<br>
    Avro™: A data serialization system.<br>
    Cassandra™: A scalable multi-master database with no single points of failure.<br>
    Chukwa™: A data collection system for managing large distributed systems.<br>
    HBase™: A scalable, distributed database that supports structured data storage for large tables.<br>
    Hive™: A data warehouse infrastructure that provides data summarization and ad hoc querying.<br>
    Mahout™: A Scalable machine learning and data mining library.<br>
    Pig™: A high-level data-flow language and execution framework for parallel computation.<br>
    ZooKeeper™: A high-performance coordination service for distributed applications.</div>
<p> </p>
<ul><li>
<strong>疯狂的数据</strong>
</li>
</ul><p>
如何对数据进行分析及面临的困难：多年来磁盘存储容量的快速增加的同时，起访问速度-磁盘数据读取速度-却未能与时俱进<br><br>
一个例子：</p>
<div class="quote_title"> 写道</div>
<div class="quote_div">1990年一个普通磁盘已经可容纳1370MB的数据，读取速率为4.4MB/s,而20年后的今天，1T的磁盘已经普及，而读取的速率为100MB/S，按照这个比例来算，以前读一个磁盘的数据要5分钟，而现在则要2.5小时</div>
<p>
读取磁盘中所有数据需要很长时间，当然写就更慢了。基于一个简单的想法：<span style="background-color:#ffff99;">我们可以同时从多个磁盘上读数据来减少读取的时间。将1T的数据分割放在不同的磁盘上同时读取！</span>
</p>
<p><br>
当然如果要放在不同磁盘上读取，就面临较多的问题了：</p>
<ol><li>
使用多个硬件读取，单点故障发生率提高，如何避免数据丢失</li>
<li>
要对读取的结果进行分析，如何保证数据的准确性(如不重复读，不遗漏等)</li>
</ol><p>
当然这就设计hadoop中两个核心HDFS和MapReduce。用他们的话说</p>
<div class="quote_title"> 写道</div>
<div class="quote_div">Hadoop提供了一个可靠的共享存储和分析系统。HDFS实现存储，MapReduce实现分析处理</div>
<p> </p>
<p>当然他也说了</p>
<div class="quote_title"> 写道</div>
<div class="quote_div">MapReduce似乎采用的一个蛮力方法：每个查询处理整个数据集-或至少数据集的很大一部分，反过来想，这也是他的能力</div>
<p> </p>
<ul><li>
<strong>vs关系型数据库</strong>
</li>
</ul><p>
其实这也涉及到什么的数据适合使用MapReduce来分析。一般来说<span style="background-color:#ffff99;">处理大数据TB/PB级和非结构化或半结构化数据是比较擅长的</span>
 。而小文件则不适合，一个比较明显的理由是大量小文件最小化寻址的开销和文件传输速率比较不明显<br><br>
一个简单的比较<br><em>关系型数据库和MapReduce的比较</em>
<br><br>
数据大小                  GB                                     PB<br>
访问                         交互式和批处理                 批处理<br>
更新                         多次读写                           一次写入多次读取<br>
结构                         静态模式                           动态模式<br>
完整性                      高                                     低<br>
横向扩展                  非线性                              线性</p>
<p> </p>            </div>
                </div>
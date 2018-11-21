---
layout:     post
title:      HBase是什么？为什么要使用HBase？
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>
<p><strong>HBase是什么？ </strong></p>
<p><strong></strong><br>HBase是Apache Hadoop中的一个子项目，Hbase依托于Hadoop的HDFS作为最基本存储基础单元，通过使用hadoop的DFS工具就可以看到这些这些数据 存储文件夹的结构,还可以通过Map/Reduce的框架(算法)对HBase进行操作，如右侧的图所示：<br><img src="http://niaklq.bay.livefilestore.com/y1pG0dfN--ZmWUPjevQkIGp0CsXxWQQ6tyzYR-XAsrWWAJYHtvcdtxk0tIK66x8N_1TKj1CO79xLljO32yk0KvLiNi7GAAaa2Lm/storage-hbase-m.png?psid=1" alt="http://niaklq.bay.livefilestore.com/y1pG0dfN--ZmWUPjevQkIGp0CsXxWQQ6tyzYR-XAsrWWAJYHtvcdtxk0tIK66x8N_1TKj1CO79xLljO32yk0KvLiNi7GAAaa2Lm/storage-hbase-m.png?psid=1" align="right"><br>HBase在产品中还包含了Jetty，在HBase启动时采用嵌入式的方式来启动Jetty，因此可以通过web界面对HBase进行管理和查看当前运行的一些状态，非常轻巧。</p>
<p> </p>
<p><strong>为什么采用<strong>HBase</strong></strong>？</p>
<p><br>HBase 不同于一般的关系数据库,它是一个适合于非结构化数据存储的数据库.所谓非结构化数据存储就是说HBase是基于列的而不是基于行的模式，这样方面读写你的大数据内容。</p>
<p>HBase是介于Map Entry(key &amp; value)和DB Row之间的一种数据存储方式。就点有点类似于现在流行的Memcache，但不仅仅是简单的一个key对应一个 value，你很可能需要存储多个属性的数据结构，但没有传统数据库表中那么多的关联关系，这就是所谓的松散数据。</p>
<p>简单来说，你在HBase中的表创建的可以看做是一张很大的表，而这个表的属性可以根据需求去动态增加，在HBase中没有表与表之间关联查询。你只需要 告诉你的数据存储到Hbase的那个column families 就可以了，不需要指定它的具体类型：char,varchar,int,tinyint,text等等。但是你需要注意HBase中不包含事务此类的功 能。</p>
<p>Apache HBase 和Google Bigtable 有非常相似的地方，一个数据行拥有一个可选择的键和任意数量的列。表是疏松的存储的，因此用户可以给行定义各种不同的列，对于这样的功能在大项目中非常实用，可以简化设计和升级的成本。</p>
<p> </p>            </div>
                </div>
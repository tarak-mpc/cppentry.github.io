---
layout:     post
title:      Hive,Hbase,HDFS等之间的关系
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载时请务必注明本文地址， 禁止用于任何商业用途， 否则会用法律维权。					https://blog.csdn.net/BLSPers/article/details/83066489				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div class="htmledit_views">
</div><p><span>Hive：</span></p>
<p><span></span>Hive基于<span><span>数据仓库</span></span>，提供静态数据的动态查询。其使用类SQL语言，底层经过编译转为MapReduce程序，在Hadoop上运行，数据存储在HDFS上。</p>
<p><span><span>HDFS:</span></span></p>
<p><span>HDFS是GFS的一种实现，他的完整名字是分布式文件系统，类似于<span>FAT32</span><span>，NTFS，是一种文件格式，</span>是底层的。</span><br></p>
<p><span><span>Hive与Hbase的数据一般都存储在HDFS上。<span>Hadoop
 HDFS为他们提供了</span><span>高可靠性的底层存储支持。</span></span></span></p>
<p><span><span>Hbase:</span></span></p>
<p><span>Hbase是Hadoop database，即Hadoop数据库。它是一个适合于非结构化数据存储的</span><span><span>数据库</span></span><span>，HBase基于列的而不是基于行的模式。</span></p>
<p><span>HBase是Google Bigtable的开源实现，类似Google Bigtable利用GFS作为其文件存储系统，HBase利用Hadoop HDFS作为其文件存储系统；Google运行MapReduce来处理Bigtable中的海量数据，HBase同样利用Hadoop
 MapReduce来处理HBase中的海量数据。</span></p>
<p><span><span>Hadoop HDFS为HBase提供了高可靠性的底层存储支持，Hadoop MapReduce为HBase提供了高性能的计算能力，Zookeeper为HBase提供了稳定服务和failover机制。</span><span>Pig和Hive还为HBase提供了高层语言支持，使得在HBase上进行数据统计处理变的非常简单。
 Sqoop则为HBase提供了方便的RDBMS（关系型数据库）数据导入功能，使得</span><a href="http://baike.baidu.com/view/1437981.htm" rel="nofollow" target="_blank">传统数据库</a><span>数据向HBase中迁移变的非常方便。</span></span></p>
<p><span><span>Pig：</span><br></span></p>
<p><span>Pig的语言层包括一个叫做<strong><span>PigLatin</span></strong>的<strong><span>文本语言</span></strong><span>,</span>Pig
 Latin是面向<strong><span>数据流</span></strong>的编程方式。Pig和Hive类似更侧重<span>于数据的<span>查询和分析<span>，底层都<span>是转化<span>成MapReduce程序运行。</span></span></span></span></span></span></p>
<p><span>区别是Hive是类SQL的查询语言，要求数据存储<span>于表中</span>，<span>而Pig是面向数据流的一个程序语言。</span><br></span></p>
            
<blockquote>
<p>参考：<a href="https://blog.csdn.net/OOC_ZC/article/details/50444222" rel="nofollow">https://blog.csdn.net/OOC_ZC/article/details/50444222</a></p>
</blockquote>
<blockquote>
<p>—–乐于分享，共同进步！<br>
—–更多文章请看：<a href="http://blog.csdn.net/BLSPers" rel="nofollow">http://blog.csdn.net/BLSPers</a></p>
</blockquote>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      大数据学习  -------  Hbase 与hadoop区别 Hbase 与关系型数据库的区别  Hbase特点
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：个人原创，转载请标注！					https://blog.csdn.net/Z_Date/article/details/84069556				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;"><strong> Hbase 与hadoop的区别？</strong></p>

<p style="margin-left:0cm;">1、 HDFS 是一个分布式文件系统，存储大量的数据。Hbase是数据库是建立在HDFS之上的</p>

<p style="margin-left:0cm;">2、 HDFS 不支持单条记录的快速检索，而Hbase 检索是最基本的功能</p>

<p style="margin-left:0cm;">3、 HDFS 提供是一个高延迟的处理,hbase提供了单行记录低延迟的随机访问</p>

<p style="margin-left:0cm;"><strong>Hbase 与关系型数据库的区别？</strong></p>

<p style="margin-left:0cm;">1 Hbase 没有事务的支持，hbase存储的数据都是非结构化或者半结构化的数据，而关系型数据库存储的都是结构化数据</p>

<p style="margin-left:0cm;"><strong>Hbase的特点如下：</strong></p>

<p style="margin-left:0cm;">1 面向列：存储都是按照列进行存储的</p>

<p style="margin-left:0cm;">2 稀疏：对于空的列，是不占用空间的</p>

<p style="margin-left:0cm;">3 无类型： Hbase存储的数据都是字符串,没有数据类型</p>            </div>
                </div>
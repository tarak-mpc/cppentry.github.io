---
layout:     post
title:      Hbase 学习笔记（四）: Hbase的架构和实现原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>转自：<a href="http://ygydaiaq-gmail-com.iteye.com/blog/1733367" rel="nofollow">http://ygydaiaq-gmail-com.iteye.com/blog/1733367</a></p>
<p><br></p>
<p></p>
<div class="blog_title" style="font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:18px;">
<h3 style="font-size:16px;line-height:1.5em;">
<a href="http://ygydaiaq-gmail-com.iteye.com/blog/1733367" rel="nofollow" style="color:rgb(16,138,198);">Hbase 学习笔记（四）: Hbase的架构和实现原理</a><span class="actions" style="font-style:italic;"></span></h3>
<strong>博客分类：</strong> 

<a href="http://ygydaiaq-gmail-com.iteye.com/category/253868" rel="nofollow" style="color:rgb(16,138,198);">Hbase</a><div class="news_tag" style="clear:both;line-height:20px;"><a href="http://www.iteye.com/blogs/tag/Hbase" rel="nofollow" style="color:rgb(255,255,255);text-decoration:none;display:inline-block;background-color:rgb(170,181,195);">Hbase</a><a href="http://www.iteye.com/blogs/tag/%E6%9E%B6%E6%9E%84" rel="nofollow" style="color:rgb(255,255,255);text-decoration:none;display:inline-block;background-color:rgb(170,181,195);">架构</a><a href="http://www.iteye.com/blogs/tag/%E5%AD%98%E5%82%A8" rel="nofollow" style="color:rgb(255,255,255);text-decoration:none;display:inline-block;background-color:rgb(170,181,195);">存储</a> </div>
</div>
<div id="blog_content" class="blog_content" style="font-size:14px;line-height:1.8em;font-family:Helvetica, Tahoma, Arial, sans-serif;">
<p>
一、架构思路</p>
<p>
    Hbase是基于Hadoop的项目，所以一般情况下我们使用的直接就是HDFS文件系统，这里我们不深谈HDFS如何构造其分布式的文件系统，只需要知道虽然Hbase中有多个RegionServer的概念，并不意味着数据是持久化在RegionServer上的，事实上，RegionServer是调度者，管理Regions，但是数据是持久化在HDFS上的。明确这一点，在后面的讨论中，我们直接把文件系统抽象为HDFS，不再深究。</p>
<p>
 </p>
<p>
    Hbase是一个分布式的数据库，使用Zookeeper来管理集群。在架构层面上分为Master（Zookeeper中的leader）和多个RegionServer，基本架构如图：</p>
<p>
<br><img src="http://dl.iteye.com/upload/attachment/0076/9504/d05f66d8-c3e6-31c1-b0de-b707435fc25f.png" alt="" style="border:0px;"></p>
<p>
    在Hbase的概念中，RegionServer对应于集群中的一个节点，而一个RegionServer负责管理多个Region。一个Region代表一张表的一部分数据，所以在Hbase中的一张表可能会需要很多个Region来存储其数据，但是每个Region中的数据并不是杂乱无章的，Hbase在管理Region的时候会给每个Region定义一个Rowkey的范围，落在特定范围内的数据将交给特定的Region，从而将负载分摊到多个节点上，充分利用分布式的优点。另外，Hbase会自动的调节Region处在的位置，如果一个RegionServer变得Hot（大量的请求落在这个Server管理的Region上），Hbase就会把Region移动到相对空闲的节点，依次保证集群环境被充分利用。</p>
<p>
 </p>
<p>
二、存储模型</p>
<p>
    有了架构层面的保证，接下来的事情就只是关注于数据的具体存储了。这里就是每个Region所承担的工作了。我们知道一个Region代表的是一张Hbase表中特定Rowkey范围内的数据，而Hbase是面向列存储的数据库，所以在一个Region中，有多个文件来存储这些列。Hbase中数据列是由列簇来组织的，所以每一个列簇都会有对应的一个数据结构，Hbase将列簇的存储数据结构抽象为Store，一个Store代表一个列簇。</p>
<p>
<br><img src="http://dl.iteye.com/upload/attachment/0076/9523/41a6ad6f-7077-3a4e-9257-46bc7948c928.png" alt="" style="border:0px;"><br>
     所以在这里也可以看出为什么在我们查询的时候要尽量减少不需要的列，而经常一起查询的列要组织到一个列簇里：因为要需要查询的列簇越多，意味着要扫描越多的Store文件，这就需要越多的时间。</p>
<p>
 </p>
<p>
    我们来深入Store中存储数据的方式。Hbase的实现是用了一种LSM 树的结构，LSM树是由B+树改进而来，所以我们首先来简单的看看B+树。</p>
<p>
<br><img src="http://dl.iteye.com/upload/attachment/0076/9514/134069a3-720c-3421-8bb0-659aa8cab9df.png" alt="" style="border:0px;"></p>
 
<p>
    这是一颗简单的B+树，含义不言而喻，这里不多分析，但是这种数据结构并不适合Hbase中的应用场景。这样的数据结构在内存中效率是很高的，但是Hbase中数据是存储在文件中的，如果按照这样的结构来存储，意味着我们每一次插入数据都要由一级索引找到文件再在文件中间作操作来保证数据的有序性，这无疑是效率低下的。所以Hbase采用的是LSM树的结构，这种结构的关键是，每一次的插入操作都会先进入MemStore（内存缓冲区），当MemStore达到上限的时候，Hbase会将内存中的数据输出为有序的StoreFile文件数据（根据Rowkey、版本、列名排序，这里已经和列簇无关了因为Store里都属于同一个列簇）。这样会在Store中形成很多个小的StoreFile，当这些小的File数量达到一个阀值的时候，Hbase会用一个线程来把这些小File合并成一个大的File。这样，Hbase就把效率低下的文件中的插入、移动操作转变成了单纯的文件输出、合并操作。</p>
<p>
 </p>
<p>
    由上可知，在Hbase底层的Store数据结构中，每个StoreFile内的数据是有序的，但是StoreFile之间不一定是有序的，Store只需要管理StoreFile的索引就可以了。这里也可以看出为什么指定版本和Rowkey可以加强查询的效率，因为指定版本和Rowkey的查询可以利用StoreFile的索引跳过一些肯定不包含目标数据的数据。</p>
<p>
 </p>
<p>
 </p>
<p>
<img src="http://dl.iteye.com/upload/attachment/0076/9584/acabc47a-6acc-3630-a5d4-0e1538941177.png" alt="" style="border:0px;"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
</div>
<br><p></p>
            </div>
                </div>
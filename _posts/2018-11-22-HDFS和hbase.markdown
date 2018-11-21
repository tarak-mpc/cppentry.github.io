---
layout:     post
title:      HDFS和hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hdfs">HDFS</h2>

<h3 id="hdfs简介">hdfs简介</h3>

<ul>
<li>hadoop中高效数据处理基础是它的数据存储模型</li>
<li>hdfs是hadoop分布式文件系统的实现</li>
<li>它是参考谷歌文件系统设计</li>
<li>hdfs为处理大批大量数据而设计</li>
<li>通过网络提供给多个分部的客户端易用的数据访问方式</li>
</ul>

<h3 id="hdfs优势">hdfs优势</h3>

<ul>
<li>相比于nfs之类的分布式文件系统，hdfs可以存储大量数据并支持更大的文件</li>
<li>hdfs是为在大量机器和文件系统之间传输数据而设计的</li>
<li>hdfs具有可靠的数据存储能力，并通过数据副本的方式处理集群中的某台机器宕机或数据丢失的情况</li>
<li>hdfs于hadoop的mapreduce模型易于集成，因此，允许数据从本地读取和处理</li>
</ul>

<h3 id="hdfs的不足">hdfs的不足</h3>

<ul>
<li>hdfs并非一个通用的应用程序，而是局限于某些特定的场景</li>
<li>hdfs优化了高速流数据读取性能，代价则是随机读取性能上的削弱</li>
<li>访问hdfs文件最好采取顺序读取的方式</li>
<li>hdfs只支持在文件上做少数的操作，但不包括任何更新的操作</li>
<li>hdfs不提供数据的本地缓存机制</li>
</ul>

<h2 id="hbase">HBase</h2>

<p>HBase是一个分布式的，本地化的，面向列的，多维度的存储系统，在设计上具备高性能和高可用性。 <br>
*  hbase是goole的bigtable架构的开源实现 <br>
*  与传统的关系型数据库管理系统类似，hbase中也是以表的形式组织数据 <br>
*  hbase支持非常松散的结构定义，但不支持任何表连接joins，查询数据或sql操作 <br>
*  hbase主要在大稀疏表上做crud操作 <br>
*  大多是hbse的实现都在使用高度非结构化数据 <br>
*  参与主从架构实现 <br>
*  hbase利用hdfs做持久化数据存储 <br>
*  具有校验，数据复制和存储 <br>
*  hbase数据管理由分布式的域服务器（region server）实现，域服务器由hbase主控服务器hmaster管理</p>

<p>hbase主要操作： <br>
*  get：获取制定行的版本信息，获取指定行和指定列簇的所有column <br>
*  put：键值不存在时，在表中添加新航，如果键值已经存在，则更新对应值即可 <br>
*  scan：允许在多行上迭代以查找特定值，该特定值可以包括正航或其任意子集 <br>
*  delete：删除一行或者指定列簇</p>

<h3 id="结合hdfs和hbase">结合hdfs和hbase</h3>

<ul>
<li>hdfs采用顺序读取访问数据，并具有海量数据的存储能力，然后hbae则以数据的快速随机访问能力建厂</li>
<li>hbase并不适用于非常大的数据量</li>
<li>hdfs通过mapfiles文件类型提供了快速访问特殊文件类型的机制，然而并不能随着key的数量增加得到比较好的扩展</li>
</ul>

<p>hadoop中所有应用程序设计的重要一环是选择合适的数据存储方式 <br>
*  如果数据植被mapreduce模型实现，则hdfs是最佳方案 <br>
*  sequencefiles是采用mapreduce模型处理的最佳选择 <br>
*   应用程序的实际分区哦是取决去数据的使用模式 <br>
*  通用的方法是评估计算请求的数量设计合适的分区模式</p>

<ul>
<li>当新数据产生时候： <br>
<ul><li>如果数据的计算结果需要更新时，那么应该考虑一种新的数据存储设计方案</li>
<li>hadoop唯一支持更新存储机制的组建是hbase </li>
<li>所以，如果mapreduce计算结果需要更新数据，hbase就是最好的方案</li>
<li>当数据量超级大的时候：</li>
<li>hbase不是足好的选择，典型的解决方案是hbase和hadoop的结合使用</li>
<li>hdfs存储实际的数据，hbase负责数据的索引文件</li></ul></li>
<li>实时访问数据时： <br>
<ul><li>如果对数据进行实时访问，根据数据量大大小，Hadoop采用不同的解决方案</li>
<li>如果数据的key space相对较狭而且数据不经常改变sequencefiles是一个不错的选择</li>
<li>对于较大的keyspace和有数据更新需求的时候，hbase或则hbase／hdfs联合使用时最合适的解决方案</li>
<li>选择合适的数据存储方案的时候，安全性也是必须考虑的</li></ul></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
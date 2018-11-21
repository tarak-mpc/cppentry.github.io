---
layout:     post
title:      有Mysql数据库的情况下为什么要用Hive数据库？
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="markdown_views">
<h1 id="有mysql数据库的情况下为什么要用hive">有Mysql数据库的情况下为什么要用Hive？</h1>

<p>最近接到公司的一个需求，要求使用Hive做数据查询。当时第一反应就是What？Hive是什么鬼？一脸懵逼状。（请原谅一个刚开始实习的Java实习生见识短浅）然后发现了hive的一些问题。下面简单介绍一下Hive。</p>

<hr><p>网上对于hive与mysql的区别的文章也不是很多。so只能问问公司大牛们，看看他们是怎样理解的。</p>

<p><em>由于 Hive 采用了 SQL 的查询语言 HQL，因此很容易将 Hive 理解为数据库。其实 从结构上来看，Hive 和数据库除了拥有类似的查询语言，再无类似之处。</em> <br><em>数据库可以用在 Online 的应用中，但是 Hive 是为数据仓库而设计的，清楚这一点，有助于从应用角度理解 Hive 的特性。</em></p>

<p>一、Hive是一个基于Hadoop的数据仓库平台。通过hive，我们可以方便地进行ETL的工作。hive定义了一个类似于SQL的查询语言：HQL，能 够将用户编写的QL转化为相应的Mapreduce程序基于Hadoop执行。</p>

<p>Hive是Facebook 2008年8月刚开源的一个数据仓库框架，其系统目标与 Pig 有相似之处，但它有一些Pig目前还不支持的机制，比如：更丰富的类型系统、更类似SQL的查询语言、Table/Partition元数据的持久化等。 <br><strong>Hive   可以看成是从SQL到Map-Reduce的   映射器</strong> <br><img src="http://img.blog.csdn.net/20170110161612291?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbnh3X3RzcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""><br>
Hive的数据放在哪儿？</p>

<p>数据在HDFS的warehouse目录下，一个表对应一个子目录。</p>

<p>本地的/tmp目录存放日志和执行计划</p>

<p>hive的表分为两种，内表和外表。  <br>
Hive 创建内部表时，会将数据移动到数据仓库指向的路径；若创建外部表，仅记录数据所在的路径，不对数据的位置做任何改变。  <br>
在删除表的时候，内部表的元数据和数据会被一起删除， 而外部表只删除元数据，不删除数据。这样外部表相对来说更加安全些，数据组织也更加灵活，方便共享源数据。</p>

<p>使用Mysql作为Hive metaStore的存储数据库</p>

<p>其中主要涉及到的表如下： <br><img src="http://img.blog.csdn.net/20170110161754261?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbnh3X3RzcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>但是对于一个菜鸟来说，看完这些还是有点云里雾里。</p>

<p>下面来看他们的异同。 <br><img src="http://img.blog.csdn.net/20170111093125875?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbnh3X3RzcA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ol><li><p>查询语言。由于 SQL 被广泛的应用在数据仓库中，因此，专门针对 Hive 的特性设计了类 SQL 的查询语言 HQL。熟悉 SQL 开发的开发者可以很方便的使用 Hive 进行开发。  </p></li>
<li><p>数据存储位置。Hive 是建立在 Hadoop 之上的，所有 Hive 的数据都是存储在 HDFS 中的。而数据库 则可以将数据保存在本地文件系统中。</p></li>
<li><p>数据格式。Hive 中没有定义专门的数据格式，数据格式可以由用户指定，用户定义数据格式需要指定三 个属性：列分隔符（通常为空格、”\t”、”\x001″）、行分隔符（”\n”）以及读取文件数据的方法（Hive 中默认有三个文件格式 TextFile，SequenceFile 以及 RCFile）。由于在加载数据的过程中，不需要从用户数据格式到 Hive 定义的数据格式的转换，因此，Hive 在加载的过程中不会对数据本身进行任何修改，而只是将数据内容复制或者移动到相应的 HDFS 目录中。而在数据库中，不同的数据库有不同的存储引擎，定义了自己的数据格式。所有数据都会按照一定的组织存储，因此，数据库加载数据的过程会比较耗时。</p></li>
<li><p>数据更新。由于 Hive 是针对数据仓库应用设计的，而数据仓库的内容是读多写少的。因此，Hive 中不 支持对数据的改写和添加，所有的数据都是在加载的时候中确定好的。而数据库中的数据通常是需要经常进行修改的，因此可以使用 INSERT INTO …  VALUES 添加数据，使用 UPDATE …  SET 修改数据。</p></li>
<li><p>索引。之前已经说过，Hive 在加载数据的过程中不会对数据进行任何处理，甚至不会对数据进行扫描， 因此也没有对数据中的某些 Key 建立索引。Hive 要访问数据中满足条件的特定值时，需要暴力扫描整个数据，因此访问延迟较高。由于 MapReduce 的引入， Hive 可以并行访问数据，因此即使没有索引，对于大数据量的访问，Hive 仍然可以体现出优势。数据库中，通常会针对一个或者几个列建立索引，因此对于少量的特定条件的数据的访问，数据库可以有很高的效率，较低的延迟。由于数据的访问延迟较高，决定了 Hive 不适合在线数据查询。</p></li>
<li><p>执行。Hive 中大多数查询的执行是通过 Hadoop 提供的 MapReduce 来实现的（类似 select * from tbl  的查询不需要 MapReduce）。而数据库通常有自己的执行引擎。</p></li>
<li><p>执行延迟。之前提到，Hive 在查询数据的时候，由于没有索引，需要扫描整个表，因此延迟较高。另外 一个导致 Hive 执行延迟高的因素是 MapReduce 框架。由于 MapReduce 本身具有较高的延迟，因此在利用 MapReduce 执行 Hive 查询时，也会有较高的延迟。相对的，数据库的执行延迟较低。当然，这个低是有条件的，即数据规模较小，当数据规模大到超过数据库的处理能力的时候，Hive 的并行计算显然能体现出优势。 </p></li>
<li><p>可扩展性。由于 Hive 是建立在 Hadoop 之上的，因此 Hive 的可扩展性是和 Hadoop 的可扩展性是 一致的（世界上最大的 Hadoop 集群在 Yahoo!，2009年的规模在 4000 台节点左右）。而数据库由于 ACID 语义的严格限制，扩展行非常有限。目前最先进的并行数据库 Oracle 在理论上的扩展能力也只有 100 台左右。 </p></li>
<li><p>数据规模。由于 Hive 建立在集群上并可以利用 MapReduce 进行并行计算，因此可以支持很大规模的 数据；对应的，数据库可以支持的数据规模较小。 </p></li>
</ol><p>看了这些，我说为什么hive查询数据怎么这么慢呢。</p>

<p>最后再来一下数据库和数据仓储的区别。</p>



<pre class="prettyprint"><code class="hljs markdown">
<span class="hljs-blockquote">&gt; 数据库是面向事务的设计，数据仓库是面向主题设计的。 数据库一般存储在线交易数据，数据仓库存储的一般是历史数据。</span>
<span class="hljs-blockquote">&gt; 数据库设计是尽量避免冗余，一般采用符合范式的规则来设计，数据仓库在设计是有意引入冗余，采用反范式的方式来设计。</span>
<span class="hljs-blockquote">&gt; 数据库是为捕获数据而设计，数据仓库是为分析数据而设计，它的两个基本的元素是维表和事实表。（维是看问题的角度，比如时间，部门，维表放的就是这些东西的定义，事实表里放着要查询的数据，同时有维的ID）</span>
</code></pre>

<p>以上文章部分内容来自与网络。</p>
</div>
        &lt;script type="text/javascript"&gt;
            $(function () {
                $('pre.prettyprint code').each(function () {
                    var lines = $(this).text().split('\n').length;
                    var $numbering = $('&lt;ul/&gt;').addClass('pre-numbering').hide();
                    $(this).addClass('has-numbering').parent().append($numbering);
                    for (i = 1; i &lt;= lines; i++) {
                        $numbering.append($('&lt;li/&gt;').text(i));
                    };
                    $numbering.fadeIn(1700);
                });
            });
        &lt;/script&gt;            </div>
                </div>
---
layout:     post
title:      HBase 数据模型（Data Model）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 align="left" style="margin-left:30px;text-align:center;">HBase Data Model——HBase 数据模型（翻译）</h1>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
在HBase中，数据是存储在有行有列的表格中。这是与关系型数据库重复的术语，并不是有用的类比。相反，HBase可以被认为是一个多维度的映射。</p>
<h2 align="left" style="margin-left:30px;">HBase数据模型术语</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
<strong>Table（表格）</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
一个HBase表格由多行组成。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
<strong>Row（行）</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
HBase中的行里面包含一个key和一个或者多个包含值的列。行按照行的key字母顺序存储在表格中。因为这个原因，行的key的设计就显得非常重要。数据的存储目标是相近的数据存储到一起。一个常用的行的key的格式是网站域名。如果你的行的key是域名，你应该将域名进行反转(org.apache.www, org.apache.mail, org.apache.jira)再存储。这样的话，所有Apache域名将会存储在一起，好过基于子域名的首字母分散在各处。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
<strong>Column（列）</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
HBase中的列包含用：分隔开的列族和列的限定符。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
<strong>Column Family（列族）</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
因为性能的原因，列族物理上包含一组列和它们的值。每一个列族拥有一系列的存储属性，例如值是否缓存在内存中，数据是否要压缩或者他的行key是否要加密等等。表格中的每一行拥有相同的列族，尽管一个给定的行可能没有存储任何数据在一个给定的列族中。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
<strong>Column Qualifier（列的限定符）</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
列的限定符是列族中数据的索引。例如给定了一个列族content，那么限定符可能是content:html，也可以是content:pdf。列族在创建表格时是确定的了，但是列的限定符是动态地并且行与行之间的差别也可能是非常大的。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
<strong>Cell（单元）</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
单元是由行、列族、列限定符、值和代表值版本的时间戳组成的。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
<strong>Timestamp（时间戳）</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:60px;">
时间戳是写在值旁边的一个用于区分值的版本的数据。默认情况下，时间戳表示的是当数据写入时RegionSever的时间点，但你也可以在写入数据时指定一个不同的时间戳。</p>
<h2 align="left" style="margin-left:30px;">19.概念视图</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
你可以读一下 Jim R写的<a href="http://jimbojw.com/wiki/index.php?title=Understanding_Hbase_and_BigTable" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Understanding HBase and BigTable</a> 博客来简单了解一下HBase的数据模型，另一个好的解释是Amandeep Khurana.的 <a href="http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Introduction
 to Basic Schema Design</a> 。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
学习不同的方面的资料可能会帮助你更透彻地了解HBase的设计。所链接的文章覆盖本部分所讲的信息。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
接下来的例子是  取自<a href="http://research.google.com/archive/bigtable.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">BigTable</a> 中第二页中的例子，在此基础上做了些许的改变。一个名为webable的表格，表格中有两行（com.cnn.www 和 com.example.www）和三个列族（contents, anchor, 和 people）。在这个例子当中，第一行(com.cnn.www)中anchor包含两列（anchor:cssnsi.com, anchor:my.look.ca）和content包含一列（contents:html）。这个例子中com.cnn.www拥有5个版本而com.example.www有一个版本。contents:html列中包含给定网页的整个HTML。anchor限定符包含能够表示行的站点以及链接中文本。People列族表示跟站点有关的人。</p>
<table border="1" cellpadding="0" style="color:rgb(0,0,0);font-size:14px;line-height:21px;margin-left:30px;"><thead style="margin-left:30px;"><tr style="margin-left:30px;"><td colspan="6" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><em>Table 4. Table webtable</em></p>
</td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Row Key</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Time Stamp</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>ColumnFamily </strong>contents</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>ColumnFamily </strong>anchor</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>ColumnFamily </strong>people</p>
</td>
</tr></thead><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td width="80"> </td>
<td colspan="5" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><em>列名</em></p>
<p align="left" style="margin-left:30px;">按照所定义好的，一个列名的格式为列族名前缀加限定符。例如，列contents:html由列族contents和html限定符。冒号（:）用于将列族和列限定符分开。</p>
</td>
</tr></tbody><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t9</p>
</td>
<td valign="top"> </td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">anchor:cnnsi.com = "CNN"</p>
</td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t8</p>
</td>
<td valign="top"> </td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">anchor:my.look.ca = "CNN.com"</p>
</td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t6</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
<td valign="top"> </td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t5</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
<td valign="top"> </td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t3</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
<td valign="top"> </td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">com.example.www</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t5</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html: "&lt;html&gt;..."</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left"> </p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">people:author: "John Doe"</p>
</td>
</tr></tbody></table><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
在HBase中，表格中的单元如果是空将不占用空间或者事实上不存在。这就使得HBase看起来“稀疏”。表格视图不是唯一方式来查看HBase中数据，甚至不是最精确的。下面的方式以多维度映射的方式来表达相同的信息。下面只是一个用于说明目的的模型可能不是百分百的精确。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
{</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  "com.cnn.www": {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    contents: {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t6: contents:html: "&lt;html&gt;..."</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t5: contents:html: "&lt;html&gt;..."</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t3: contents:html: "&lt;html&gt;..."</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    anchor: {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t9: anchor:cnnsi.com = "CNN"</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t8: anchor:my.look.ca = "CNN.com"</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    people: {}</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  "com.example.www": {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    contents: {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t5: contents:html: "&lt;html&gt;..."</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    anchor: {}</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    people: {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t5: people:author: "John Doe"</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
}</p>
<h2 align="left" style="margin-left:30px;">20. 物理视图</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
尽管一个概念层次的表格可能看起来是由一些列稀疏的行组成，但他们是通过列族来存储的。一个新建的限定符(column_family:column_qualifier)可以随时地添加到已存在的列族中。</p>
<table border="1" cellpadding="0" style="color:rgb(0,0,0);font-size:14px;line-height:21px;margin-left:30px;"><thead style="margin-left:30px;"><tr style="margin-left:30px;"><td colspan="3" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><em>Table 5. ColumnFamily anchor</em></p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Row Key</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Time Stamp</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Column Family </strong>anchor</p>
</td>
</tr></thead><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t9</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">anchor:cnnsi.com = "CNN"</p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t8</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">anchor:my.look.ca = "CNN.com"</p>
</td>
</tr></tbody></table><p align="left" style="font-size:14px;line-height:21px;">
 </p>
<table border="1" cellpadding="0" style="color:rgb(0,0,0);font-size:14px;line-height:21px;margin-left:30px;"><thead style="margin-left:30px;"><tr style="margin-left:30px;"><td colspan="3" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><em>Table 6. ColumnFamily contents</em></p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Row Key</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Time Stamp</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>ColumnFamily </strong>contents:</p>
</td>
</tr></thead><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t6</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t5</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t3</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
</tr></tbody></table><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
概念视图中的空单元实际上是没有进行存储的。因此对于返回时间戳为t8的contents:html的值的请求，结果为空。同样的，一个返回时间戳为t9的anchor:my.look.ca的值的请求，结果也为空。然而，如果没有指定时间戳的话，那么会返回特定列的最新值。对有多个版本的列，优先返回最新的值，因为时间戳是按照递减顺序存储的。因此对于一个返回com.cnn.www里面所有的列的值并且没有指定时间戳的请求，返回的结果会是时间戳为t6的contents:html 的值、时间戳 t9的anchor:cnnsi.com f的值和时间戳t8的 anchor:my.look.ca 。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
关于Apache Hbase如何存储数据的内部细节，请查看  <a href="http://hbase.apache.org/book.html#regions.arch" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">regions.arch</a>.</p>
<h2 align="left" style="margin-left:30px;">21. 命名空间</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
命名空间是一个类似于关系型数据库系统中的数据库的逻辑上的表分组的概念。这个抽象的概念为即将到来的多租户相关特性奠定了基础：</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>Quota Management (<a href="https://issues.apache.org/jira/browse/HBASE-8410" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-8410</a>) - Restrict the amount of resources (i.e. regions, tables) a
 namespace can consume.</li><li>Namespace Security Administration (<a href="https://issues.apache.org/jira/browse/HBASE-9206" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-9206</a>) - Provide another level of security administration
 for tenants.</li><li>Region server groups (<a href="https://issues.apache.org/jira/browse/HBASE-6721" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-6721</a>) - A namespace/table can be pinned onto a subset of RegionServers
 thus guaranteeing a course level of isolation.</li></ul></li></ul><h3 align="left" style="margin-left:30px;">21.1. 命名空间管理</h3>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
命名空间可以被创建、移除和修改。命名空间关系的指定是在创建表格通过指定一个完全限定表名的形式完成的：</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
&lt;table namespace&gt;:&lt;table qualifier&gt;</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>Example 11. Examples</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#Create a namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
create_namespace 'my_ns'</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#create my_table in my_ns namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
create 'my_ns:my_table', 'fam'</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#drop namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
drop_namespace 'my_ns'</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#alter namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
alter_namespace 'my_ns', {METHOD =&gt; 'set', 'PROPERTY_NAME' =&gt; 'PROPERTY_VALUE'}</p>
<h3 align="left" style="margin-left:30px;">21.2. 预定义命名空间</h3>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
There are two predefined special namespaces:</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
有两种预定义的特殊的命名空间</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>hbase –系统命名空间, 用于包含HBase内部表</li><li>default – 没有明确指定命名空间的表将会自动落入这个命名空间</li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>Example 12. Examples</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#namespace=foo and table qualifier=bar</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
create 'foo:bar', 'fam'</p>
<p align="left" style="font-size:14px;line-height:21px;">
 </p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#namespace=default and table qualifier=bar</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
create 'bar', 'fam'</p>
<h2 align="left" style="margin-left:30px;">22. 表格</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
表格在架构设计的时候预先声明。</p>
<h2 align="left" style="margin-left:30px;">23. 行</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
行键是未解释的字节。行是按照字典顺序进行排序的并且最小的排在前面。空的字节数据用来表示表格的命名空间的开头和结尾。</p>
<h2 align="left" style="margin-left:30px;">24. 列族</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
列在HBase中是归入到列族里面的。一个列的所有列成员都涌向相同的前缀。例如，列courses:history和cources:math是cources列族的成员，冒号用于将列族和列限定符分开。列族前缀必须由可打印的字符组成。列限定符可以由任意字节组成。列族必须在结构定义阶段预先声明号而列则不需要再结构设计阶段预先定义而是可以在表格的创建和运行阶段快速的加入。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
物理上来说，所有的列族成员都是存储在文件系统。因为调试和存储技术参数都是在列族这个层次上，建议所有的列族都要拥有相同的通用访问格式和大小特征。</p>
<h2 align="left" style="margin-left:30px;">25. 单元</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
一个{row,column,version}完全指定了HBase的一个单元。单元内容是未解释的字节</p>
<h2 align="left" style="margin-left:30px;">26. 数据模型操作</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
数据模型的四个主要操作是Get，Put，Scan和Delete。可以通过Table实例进行操作。</p>
<h3 align="left" style="margin-left:30px;">26.1. 获取</h3>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Get</a> 返回指定行的属性 Gets 通过 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html#get(org.apache.hadoop.hbase.client.Get)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table.get</a>.执行。</p>
<h3 align="left" style="margin-left:30px;">26.2. 插入</h3>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Put.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Put</a> 操作是在行键不存在时添加新行或者行键已经存在时进行更新。 Puts 是通过 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html#put(org.apache.hadoop.hbase.client.Put)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table.put</a> (写缓存)
 或者<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html#batch(java.util.List,%20java.lang.Object%5B%5D)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table.batch</a> (没有写缓存)执行的。</p>
<h3 align="left" style="margin-left:30px;">26.3. 扫描</h3>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Scan.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Scan</a> 允许为指定属性迭代多行。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
下面是表格实例中Scan的例子。假设一个表格里面有"row1", "row2", "row3"，然后有另外一组行键为"abc1", "abc2",和"abc3"。下面的例子展示如何设置一个Scan实例来返回以“row”开头的行。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;">
 </p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Table table = ...      <em>// instantiate a Table instance</em></p>
<p align="left" style="font-size:14px;line-height:21px;">
 </p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Scan scan = <strong>new</strong> Scan();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
scan.addColumn(CF, ATTR);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
scan.setRowPrefixFilter(Bytes.toBytes("row"));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
ResultScanner rs = table.getScanner(scan);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>try</strong> {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  <strong>for</strong> (Result r = rs.next(); r != null; r = rs.next()) {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    <em>// process result...</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
} <strong>finally</strong> {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  rs.close();  <em>// always close the ResultScanner!</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
}</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
需要说明的是通常最简单的指定Scan的一个特定停止点的方法是使用<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/filter/InclusiveStopFilter.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">InclusiveStopFilter</a> 类。</p>
<h3 align="left" style="margin-left:30px;">26.4. 删除</h3>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Delete.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Delete</a> 操作是将一个行从表中移除. Deletes 通过 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html#delete(org.apache.hadoop.hbase.client.Delete)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table.delete</a>执行。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
HBase不会立刻对数据的进行操作（可以理解为不对数据执行删除操作），而是为死亡数据创建一个称为墓碑的标签。这个墓碑和死亡数据会在重要精简工作中被删除。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
查看 <a href="http://hbase.apache.org/book.html#version.delete" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">version.delete</a> 获取更多关于列的版本删除的信息，查看 <a href="http://hbase.apache.org/book.html#compaction" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">compaction</a> 获取关于精简工作的更多信息。</p>
<h2 align="left" style="margin-left:30px;">27. 版本</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A <em>{row, column, version}</em>在HBase完全指定一个单元。理论上来说行和列都一样的单元的数量是无限的，因为单元的地址是通过版本这个维度来区分的。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
行和列使用字节来表达，而版本是通过长整型来指定的。典型来说，这个长时间实例就像java.util.Date.getTime() 或者 System.currentTimeMillis()返回的一样，以毫秒为单位，返回当前时间和<em>January 1, 1970 UTC的时间差</em>。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
HBase的版本维度以递减顺序存储，以致读取一个存储的文件时，返回的是最新版本的数据。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
关于单元的版本有许多的困扰，尤其是：</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>如果多个数据写到一个具有相同版本的单元里，只能获取到最后写入的那个</li><li>以非递增的版本顺序写入也是可以的。</li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
下面我们将描述HBase中版本维度是如何运作的。可以看 <a href="https://issues.apache.org/jira/browse/HBASE-2406" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-2406</a> 关于HBase版本的讨论。 <a href="http://outerthought.org/blog/417-ot.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Bending
 time in HBase</a> 是关于HBase的版本或者时间维度的好读物。它提供了比这里更多的关于版本的细节信息。正如这里写到的，这里提到的<em>覆盖存在的时间戳</em>的限制将不再存在。这部分只是Bruno Dumon所写的关于版本的基本大纲。</p>
<h3 align="left" style="margin-left:30px;">27.1.指定版本的存储数量</h3>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
版本的最大存储数量是列结构的一个部分并且在表格创建时指定，或者通过alter命令行，或者通过 HColumnDescriptor.DEFAULT_VERSIONS来修改。HBase0.96之前，默认数量是3，HBase0.96之后改为1.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>Example 13. 修改一个列族的最大版本数</em>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
这个例子使用HBase Shell来修改列族 f1的最大版本数为5，你也可以使用 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/HColumnDescriptor.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HColumnDescriptor</a>来实现。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
hbase&gt; alter ‘t1′, NAME =&gt; ‘f1′, VERSIONS =&gt; 5</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>Example 14. 修改一个列族的最小版本数Modify</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
你也可以通过指定最小半本书来存储列族。默认情况下，该值为零，意味着这个属性是禁用的。下面的例子是通过HBase Shell设置列族f1中的所有列的最小版本数为2。你也可以通过 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/HColumnDescriptor.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HColumnDescriptor</a>来实现。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
hbase&gt; alter ‘t1′, NAME =&gt; ‘f1′, MIN_VERSIONS =&gt; 2</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
从HBase0.98.2开始，你可以通过设定在<em>hbase-site.xml</em>中设置hbase.column.max.version属性为所有新建的列指定一个全局的默认的最大版本数。</p>
<h3 align="left" style="margin-left:30px;">27.2. 版本和HBase 操作</h3>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
在这部分我们来看一下版本维度在HBase的每个核心操作中的表现。</p>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;">27.2.1. Get/Scan</h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Get是通过获取Scan的第一个数据来实现的。下面的讨论适用于 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Get</a> 和 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Scan.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Scans</a>.。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
默认情况下，如果你没有指定明确的版本，当你执行一个Get操作时，那个版本为最大值的单元将被返回（可能是也可能不是最新写人的那个）。默认的行为可以通过下面方式来修改：</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>返回不止一个版本 查看 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html#setMaxVersions()" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Get.setMaxVersions()</a></li><li>返回最新版本以外的版本, 查看 <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html#setTimeRange(long,%20long)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Get.setTimeRange()</a></li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
想要获得小于或等于固定值的最新版本，仅仅通过使用一个0到期望版本的范围和设置最大版本数为1就可以实现获得一个特定时间点的最新版本的记录。</p>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;">27.2.2. 默认的Get 例子</h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
下面例子仅仅返回行的当前版本。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Get get = <strong>new</strong> Get(Bytes.toBytes("row1"));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Result r = table.get(get);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>byte[]</strong> b = r.getValue(CF, ATTR);  <em>// returns current version of value</em></p>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;">27.2.3. Get版本的例子</h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
下面是获得行的最新3个版本的例子：</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Get get = <strong>new</strong> Get(Bytes.toBytes("row1"));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
get.setMaxVersions(3);  <em>// will return last 3 versions of row</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Result r = table.get(get);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>byte[]</strong> b = r.getValue(CF, ATTR);  <em>// returns current version of value</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
List&lt;KeyValue&gt; kv = r.getColumn(CF, ATTR);  <em>// returns all versions of this column</em></p>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;">27.2.4. Put</h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Put操作常常是以固定的时间戳来创建一个新单元。默认情况下，系统使用服务的 currentTimeMillis，但是你也可以为每一个列自己指定版本（长整型）。这就意味着你可以指定一个过去或者未来的时间点，或者不是时间格式的长整型。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
为了覆盖已经存在的值，对和那个你想要覆盖的单元完全一样的row、column和version进行put操作。</p>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;"><em>隐式版本例子</em></h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
下面Put是以当前时间为版本的隐式操作</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Put put = <strong>new</strong> Put(Bytes.toBytes(row));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
put.add(CF, ATTR, Bytes.toBytes( data));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
table.put(put);</p>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;"><em>显示版本例子</em></h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
下面的put是显示指定时间戳的操作。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Put put = <strong>new</strong> Put( Bytes.toBytes(row));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>long</strong> explicitTimeInMs = 555;  <em>// just an example</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
put.add(CF, ATTR, explicitTimeInMs, Bytes.toBytes(data));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
table.put(put);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
警告: 版本时间戳是HBase内部用来计算数据的存活时间的。它最好避免自己设置。最好是将时间戳作为行的单独属性或者作为key的一部分，或者两者都有。</p>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;">27.2.5. Delete</h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
There are three different types of internal delete markers. See Lars Hofhansl’s blog for discussion of his attempt adding another, <a href="http://hadoop-hbase.blogspot.com/2012/01/scanning-in-hbase.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Scanning
 in HBase: Prefix Delete Marker</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
有三种不同的删除类型。可以看看Lars Hofhansl所写的博客 <a href="http://hadoop-hbase.blogspot.com/2012/01/scanning-in-hbase.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Scanning in HBase: Prefix Delete Marker</a>.</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>Delete:列的指定版本</li><li>Delete column:列的所有版本</li><li>Delete family:特定列族里面的所有列。</li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
当要删除整个行时，HBase将会在内部为每一个列族创建一个墓碑。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
删除通过创建一个墓碑标签来工作的。例如，让我们来设想我们要删除一个行。为此你可指定一个版本，或者使用默认的currentTimeMillis 。这就是删除小于等于该版本的所有单元。HBase不会修改数据，例如删除操作将不会立刻删除满足删除条件的文件。相反的，称为墓碑的会被写入，用来掩饰被删除的数据。当HBase执行一个精简操作（可以理解为清理），墓碑将会执行一个真正地删除死亡值和墓碑自己的删除操作。如果你的删除操作指定的版本大于目前所有的版本，那么可以认为是删除整个行的数据。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
你可以在 <a href="http://comments.gmane.org/gmane.comp.java.hadoop.hbase.user/28421" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Put w/timestamp → Deleteall → Put w/ timestamp fails</a> 用户邮件列表中查看关于删除和版本之间的相互影响的有益信息。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<span style="text-decoration:underline;">keyvalue</span>也可以到<a href="http://hbase.apache.org/book.html#keyvalue" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">keyvalue</a> 查看更多关于内部KeyValue格式的信息。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
删除标签会在下一次仓库精简操作中被清理掉，除非为列族设置了 KEEP_DELETED_CELLS (查看 <a href="http://hbase.apache.org/book.html#cf.keep.deleted" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Keeping Deleted Cells</a>)。为了保证删除时间的可配置性，你可以通过在 <em>hbase-site.xml</em>.中hbase.hstore.time.to.purge.deletes属性来设置TTL（生存时间）。如果 hbase.hstore.time.to.purge.deletes没有设置或者设置为0，所有的删除标签包括哪些墓碑都会在下一次精简操作中被干掉。此外，未来带有时间戳的删除标签将会保持到发生在hbase.hstore.time.to.purge.deletes加上代表标签的时间戳的时间和的下一次精简操作。</p>
<table border="0" cellpadding="0" style="color:rgb(0,0,0);font-size:14px;line-height:21px;margin-left:30px;"><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td width="80"> </td>
<td style="margin-left:30px;">
<p align="left" style="margin-left:30px;">This behavior represents a fix for an unexpected change that was introduced in HBase 0.94, and was fixed in <a href="https://issues.apache.org/jira/browse/HBASE-10118" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-10118</a>.
 The change has been backported to HBase 0.94 and newer branches.</p>
</td>
</tr></tbody></table><h3 align="left" style="margin-left:30px;">27.3. 当前的局限性</h3>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;">27.3.1. Deletes mask Puts删除覆盖插入/更新</h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
删除操作覆盖插入/更新操作，即使put在delete之后执行的。可以查看 <a href="https://issues.apache.org/jira/browse/HBASE-2256" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-2256</a>. 还记得一个删除写入一个墓碑，只有当下一次精简操作发生时才会执行真正地删除操作。假设你执行了一个删除全部小于等于T的操作。在此之外又做了一个时间戳为T的put操作。这个put操作即使是发生在delete之后，也会被delete墓碑所覆盖。执行put的时候不会报错，不过当你执行一个get的时候会发现执行无效。你会在精简操作之后重新开始工作。如果你在put的使用的递增的版本，那么这些问题将不会出现。但如果你不在意时间，在执行delelte后立刻执行put的话，那么它们将有可能发生在同一时间点，这将会导致上述问题的出现。</p>
<h4 align="left" style="font-size:14px;line-height:21px;margin-left:30px;">27.3.2. 精简操作影响查询结果</h4>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
创建三个版本为t1,t2,t3的单元，并且设置最大版本数为2.所以当我们查询所有版本时，只会返回t2和t3。但是当你删除版本t2和t3的时候，版本t1会重新出现。显然，一旦重要精简工作运行之后，这样的行为就不会再出现。（查看 <a href="http://outerthought.org/blog/417-ot.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Bending time in HBase</a>.）</p>
<h2 align="left" style="margin-left:30px;">28. 排序次序</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
HBase中所有的数据模型操作返回的数据都是经过排序的。首先是行排序，其次是列族，接着是列限定符，最后是时间戳（递减排序，左右最新的记录最先返回）</p>
<h2 align="left" style="margin-left:30px;">29. 列元数据</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
所有列的元数据都存储在一个列族的内部KeyValue实例中。因此，HBsase不仅支持一行中有多列，而且支持行之间的列的差异多样化。跟踪列名是你的责任。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
唯一获取一个列族的所有列的方法是处理所有的行。查看 <a href="http://hbase.apache.org/book.html#keyvalue" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">keyvalue</a>获得更多关于HBase内部如何存储数据的信息。</p>
<h2 align="left" style="margin-left:30px;">30. Joins</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
HBase是否支持join是一个常见的问题，答案是没有，至少没办法像RDBMS那样支持（例如等价式join或者外部join）。正如本章节所阐述的，HBase中读取数据的操作是Get和Scan。</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
然而，这不意味着等价式join功能没办法在你的应用中实现，但是你必须自己实现。两种主要策略是将数据非结构化地写到HBase中，或者查找表格然后在应用中或者MapReduce代码中实现join操作（正如RDBMS所演示的，将根据表格的大小会有几种不同的策略，例如嵌套使循环和hash-join）。哪个是最好的方法？这将依赖于你想做什么，没有一种方案能够应对各种情况。</p>
<h2 align="left" style="margin-left:30px;">31. ACID</h2>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
查看 <a href="http://hbase.apache.org/acid-semantics.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">ACID Semantics</a>. Lars Hofhansl也写了一份报告 <a href="http://hadoop-hbase.blogspot.com/2012/03/acid-in-hbase.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">ACID
 in HBase</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;">
 </p>
<p style="font-size:14px;line-height:21px;margin-left:30px;">
 下面是原文</p>
<hr style="font-size:14px;line-height:21px;"><p style="font-size:14px;line-height:21px;">
 </p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Data Model</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
In HBase, data is stored in tables, which have rows and columns. This is a terminology overlap with relational databases (RDBMSs), but this is not a helpful analogy. Instead, it can be helpful to think of an HBase table as a multi-dimensional map.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>HBase Data Model Terminology</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>Table</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
An HBase table consists of multiple rows.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>Row</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A row in HBase consists of a row key and one or more columns with values associated with them. Rows are sorted alphabetically by the row key as they are stored. For this reason, the design of the row key is very important. The goal is to store data in such
 a way that related rows are near each other. A common row key pattern is a website domain. If your row keys are domains, you should probably store them in reverse (org.apache.www, org.apache.mail, org.apache.jira). This way, all of the Apache domains are near
 each other in the table, rather than being spread out based on the first letter of the subdomain.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>Column</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A column in HBase consists of a column family and a column qualifier, which are delimited by a : (colon) character.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>Column Family</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Column families physically colocate a set of columns and their values, often for performance reasons. Each column family has a set of storage properties, such as whether its values should be cached in memory, how its data is compressed or its row keys are encoded,
 and others. Each row in a table has the same column families, though a given row might not store anything in a given column family.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>Column Qualifier</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A column qualifier is added to a column family to provide the index for a given piece of data. Given a column family content, a column qualifier might be content:html, and another might be content:pdf. Though column families are fixed at table creation, column
 qualifiers are mutable and may differ greatly between rows.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>Cell</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A cell is a combination of row, column family, and column qualifier, and contains a value and a timestamp, which represents the value’s version.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>Timestamp</strong></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A timestamp is written alongside each value, and is the identifier for a given version of a value. By default, the timestamp represents the time on the RegionServer when the data was written, but you can specify a different timestamp value when you put data
 into the cell.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
19. Conceptual View</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
You can read a very understandable explanation of the HBase data model in the blog post <a href="http://jimbojw.com/wiki/index.php?title=Understanding_Hbase_and_BigTable" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Understanding HBase
 and BigTable</a> by Jim R. Wilson. Another good explanation is available in the PDF <a href="http://0b4af6cdc2f0c5998459-c0245c5c937c5dedcca3f1764ecc9b2f.r43.cf2.rackcdn.com/9353-login1210_khurana.pdf" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Introduction
 to Basic Schema Design</a> by Amandeep Khurana.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
It may help to read different perspectives to get a solid understanding of HBase schema design. The linked articles cover the same ground as the information in this section.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The following example is a slightly modified form of the one on page 2 of the <a href="http://research.google.com/archive/bigtable.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">BigTable</a> paper. There is a table called webtable that
 contains two rows (com.cnn.www and com.example.www) and three column families named contents, anchor, and people. In this example, for the first row (com.cnn.www), anchor contains two columns (anchor:cssnsi.com, anchor:my.look.ca) and contents contains one
 column (contents:html). This example contains 5 versions of the row with the row key com.cnn.www, and one version of the row with the row key com.example.www. The contents:html column qualifier contains the entire HTML of a given website. Qualifiers of the anchor column
 family each contain the external site which links to the site represented by the row, along with the text it used in the anchor of its link. The people column family represents people associated with the site.</p>
<table border="0" cellpadding="0" style="color:rgb(0,0,0);font-size:14px;line-height:21px;margin-left:30px;"><thead style="margin-left:30px;"><tr style="margin-left:30px;"><td colspan="6" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><em>Table 4. Table webtable</em></p>
</td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Row Key</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Time Stamp</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>ColumnFamily </strong>contents</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>ColumnFamily </strong>anchor</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>ColumnFamily </strong>people</p>
</td>
</tr></thead><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td width="80"> </td>
<td colspan="5" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><em>Column Names</em></p>
<p align="left" style="margin-left:30px;">By convention, a column name is made of its column family prefix and a <em>qualifier</em>. For example, the column <em>contents:html</em> is made up of the column family contents and
 the html qualifier. The colon character (:) delimits the column family from the column family <em>qualifier</em>.</p>
</td>
</tr></tbody><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t9</p>
</td>
<td valign="top"> </td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">anchor:cnnsi.com = "CNN"</p>
</td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t8</p>
</td>
<td valign="top"> </td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">anchor:my.look.ca = "CNN.com"</p>
</td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t6</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
<td valign="top"> </td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t5</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
<td valign="top"> </td>
<td valign="top"> </td>
</tr><tr style="margin-left:30px;"><td colspan="2" valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t3</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
<td valign="top"> </td>
<td valign="top"> </td>
</tr></tbody></table><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Cells in this table that appear to be empty do not take space, or in fact exist, in HBase. This is what makes HBase "sparse." A tabular view is not the only possible way to look at data in HBase, or even the most accurate. The following represents the same
 information as a multi-dimensional map. This is only a mock-up for illustrative purposes and may not be strictly accurate.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
{</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  "com.cnn.www": {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    contents: {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t6: contents:html: "&lt;html&gt;..."</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t5: contents:html: "&lt;html&gt;..."</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t3: contents:html: "&lt;html&gt;..."</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    anchor: {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t9: anchor:cnnsi.com = "CNN"</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t8: anchor:my.look.ca = "CNN.com"</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    people: {}</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  "com.example.www": {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    contents: {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t5: contents:html: "&lt;html&gt;..."</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    anchor: {}</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    people: {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
      t5: people:author: "John Doe"</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
}</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
20. Physical View</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Although at a conceptual level tables may be viewed as a sparse set of rows, they are physically stored by column family. A new column qualifier (column_family:column_qualifier) can be added to an existing column family at any time.</p>
<table border="1" cellpadding="0" style="color:rgb(0,0,0);font-size:14px;line-height:21px;margin-left:30px;"><thead style="margin-left:30px;"><tr style="margin-left:30px;"><td colspan="3" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><em>Table 5. ColumnFamily anchor</em></p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Row Key</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Time Stamp</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Column Family </strong>anchor</p>
</td>
</tr></thead><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t9</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">anchor:cnnsi.com = "CNN"</p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t8</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">anchor:my.look.ca = "CNN.com"</p>
</td>
</tr></tbody></table><p align="left" style="font-size:14px;line-height:21px;">
 </p>
<table border="1" cellpadding="0" style="color:rgb(0,0,0);font-size:14px;line-height:21px;margin-left:30px;"><thead style="margin-left:30px;"><tr style="margin-left:30px;"><td colspan="3" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><em>Table 6. ColumnFamily contents</em></p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Row Key</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>Time Stamp</strong></p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;"><strong>ColumnFamily </strong>contents:</p>
</td>
</tr></thead><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t6</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t5</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
</tr><tr style="margin-left:30px;"><td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">"com.cnn.www"</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">t3</p>
</td>
<td valign="top" style="margin-left:30px;">
<p align="left" style="margin-left:30px;">contents:html = "&lt;html&gt;…​"</p>
</td>
</tr></tbody></table><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The empty cells shown in the conceptual view are not stored at all. Thus a request for the value of the contents:html column at time stamp t8 would return no value. Similarly, a request for an anchor:my.look.cavalue at time stamp t9 would return no value. However,
 if no timestamp is supplied, the most recent value for a particular column would be returned. Given multiple versions, the most recent is also the first one found, since timestamps are stored in descending order. Thus a request for the values of all columns
 in the row com.cnn.www if no timestamp is specified would be: the value of contents:html from timestamp t6, the value of anchor:cnnsi.com from timestamp t9, the value of anchor:my.look.ca from timestamp t8.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
For more information about the internals of how Apache HBase stores data, see <a href="http://hbase.apache.org/book.html#regions.arch" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">regions.arch</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
21. Namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A namespace is a logical grouping of tables analogous to a database in relation database systems. This abstraction lays the groundwork for upcoming multi-tenancy related features:</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>Quota Management (<a href="https://issues.apache.org/jira/browse/HBASE-8410" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-8410</a>) - Restrict the amount of resources (i.e. regions, tables) a
 namespace can consume.</li><li>Namespace Security Administration (<a href="https://issues.apache.org/jira/browse/HBASE-9206" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-9206</a>) - Provide another level of security administration
 for tenants.</li><li>Region server groups (<a href="https://issues.apache.org/jira/browse/HBASE-6721" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-6721</a>) - A namespace/table can be pinned onto a subset of RegionServers
 thus guaranteeing a course level of isolation.</li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
21.1. Namespace management</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A namespace can be created, removed or altered. Namespace membership is determined during table creation by specifying a fully-qualified table name of the form:</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
&lt;table namespace&gt;:&lt;table qualifier&gt;</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>Example 11. Examples</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#Create a namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
create_namespace 'my_ns'</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#create my_table in my_ns namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
create 'my_ns:my_table', 'fam'</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#drop namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
drop_namespace 'my_ns'</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#alter namespace</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
alter_namespace 'my_ns', {METHOD =&gt; 'set', 'PROPERTY_NAME' =&gt; 'PROPERTY_VALUE'}</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
21.2. Predefined namespaces</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
There are two predefined special namespaces:</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>hbase - system namespace, used to contain HBase internal tables</li><li>default - tables with no explicit specified namespace will automatically fall into this namespace</li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>Example 12. Examples</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#namespace=foo and table qualifier=bar</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
create 'foo:bar', 'fam'</p>
<p align="left" style="font-size:14px;line-height:21px;">
 </p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
#namespace=default and table qualifier=bar</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
create 'bar', 'fam'</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
22. Table</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Tables are declared up front at schema definition time.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
23. Row</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Row keys are uninterpreted bytes. Rows are lexicographically sorted with the lowest order appearing first in a table. The empty byte array is used to denote both the start and end of a tables' namespace.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
24. Column Family</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Columns in Apache HBase are grouped into <em>column families</em>. All column members of a column family have the same prefix. For example, the columns <em>courses:history</em> and <em>courses:math</em> are both members of the <em>courses</em> column family.
 The colon character (:) delimits the column family from the column family qualifier. The column family prefix must be composed of <em>printable</em> characters. The qualifying tail, the column family <em>qualifier</em>, can be made of any arbitrary bytes.
 Column families must be declared up front at schema definition time whereas columns do not need to be defined at schema time but can be conjured on the fly while the table is up and running.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Physically, all column family members are stored together on the filesystem. Because tunings and storage specifications are done at the column family level, it is advised that all column family members have the same general access pattern and size characteristics.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
25. Cells</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A <em>{row, column, version}</em> tuple exactly specifies a cell in HBase. Cell content is uninterpreted bytes</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
26. Data Model Operations</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The four primary data model operations are Get, Put, Scan, and Delete. Operations are applied via <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table</a> instances.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
26.1. Get</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Get</a> returns attributes for a specified row. Gets are executed via <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html#get(org.apache.hadoop.hbase.client.Get)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table.get</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
26.2. Put</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Put.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Put</a> either adds new rows to a table (if the key is new) or can update existing rows (if the key already
 exists). Puts are executed via <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html#put(org.apache.hadoop.hbase.client.Put)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table.put</a> (writeBuffer) or <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html#batch(java.util.List,%20java.lang.Object%5B%5D)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table.batch</a> (non-writeBuffer).</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
26.3. Scans</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Scan.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Scan</a> allow iteration over multiple rows for specified attributes.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The following is an example of a Scan on a Table instance. Assume that a table is populated with rows with keys "row1", "row2", "row3", and then another set of rows with the keys "abc1", "abc2", and "abc3". The following example shows how to set a Scan instance
 to return the rows beginning with "row".</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;">
 </p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Table table = ...      <em>// instantiate a Table instance</em></p>
<p align="left" style="font-size:14px;line-height:21px;">
 </p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Scan scan = <strong>new</strong> Scan();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
scan.addColumn(CF, ATTR);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
scan.setRowPrefixFilter(Bytes.toBytes("row"));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
ResultScanner rs = table.getScanner(scan);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>try</strong> {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  <strong>for</strong> (Result r = rs.next(); r != null; r = rs.next()) {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
    <em>// process result...</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  }</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
} <strong>finally</strong> {</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
  rs.close();  <em>// always close the ResultScanner!</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
}</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Note that generally the easiest way to specify a specific stop point for a scan is by using the <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/filter/InclusiveStopFilter.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">InclusiveStopFilter</a>class.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
26.4. Delete</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Delete.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Delete</a> removes a row from a table. Deletes are executed via <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Table.html#delete(org.apache.hadoop.hbase.client.Delete)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Table.delete</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
HBase does not modify data in place, and so deletes are handled by creating new markers called <em>tombstones</em>. These tombstones, along with the dead values, are cleaned up on major compactions.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
See <a href="http://hbase.apache.org/book.html#version.delete" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">version.delete</a> for more information on deleting versions of columns, and see <a href="http://hbase.apache.org/book.html#compaction" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">compaction</a> for
 more information on compactions.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27. Versions</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
A <em>{row, column, version}</em> tuple exactly specifies a cell in HBase. It’s possible to have an unbounded number of cells where the row and column are the same but the cell address differs only in its version dimension.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
While rows and column keys are expressed as bytes, the version is specified using a long integer. Typically this long contains time instances such as those returned by java.util.Date.getTime() or System.currentTimeMillis(), that is: <em>the difference, measured
 in milliseconds, between the current time and midnight, January 1, 1970 UTC</em>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The HBase version dimension is stored in decreasing order, so that when reading from a store file, the most recent values are found first.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
There is a lot of confusion over the semantics of cell versions, in HBase. In particular:</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>If multiple writes to a cell have the same version, only the last written is fetchable.</li><li>It is OK to write cells in a non-increasing version order.</li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Below we describe how the version dimension in HBase currently works. See <a href="https://issues.apache.org/jira/browse/HBASE-2406" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-2406</a> for discussion of HBase versions. <a href="http://outerthought.org/blog/417-ot.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Bending
 time in HBase</a> makes for a good read on the version, or time, dimension in HBase. It has more detail on versioning than is provided here. As of this writing, the limitation <em>Overwriting values at existing timestamps</em> mentioned in the article no longer
 holds in HBase. This section is basically a synopsis of this article by Bruno Dumon.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.1. Specifying the Number of Versions to Store</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The maximum number of versions to store for a given column is part of the column schema and is specified at table creation, or via an alter command, via HColumnDescriptor.DEFAULT_VERSIONS. Prior to HBase 0.96, the default number of versions kept was 3, but
 in 0.96 and newer has been changed to 1.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>Example 13. Modify the Maximum Number of Versions for a Column Family</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
This example uses HBase Shell to keep a maximum of 5 versions of all columns in column family f1. You could also use <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/HColumnDescriptor.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HColumnDescriptor</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
hbase&gt; alter ‘t1′, NAME =&gt; ‘f1′, VERSIONS =&gt; 5</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>Example 14. Modify the Minimum Number of Versions for a Column Family</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
You can also specify the minimum number of versions to store per column family. By default, this is set to 0, which means the feature is disabled. The following example sets the minimum number of versions on all columns in column family f1 to 2, via HBase Shell.
 You could also use <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/HColumnDescriptor.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HColumnDescriptor</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
hbase&gt; alter ‘t1′, NAME =&gt; ‘f1′, MIN_VERSIONS =&gt; 2</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Starting with HBase 0.98.2, you can specify a global default for the maximum number of versions kept for all newly-created columns, by setting hbase.column.max.version in <em>hbase-site.xml</em>. See <a href="http://hbase.apache.org/book.html#hbase.column.max.version" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">hbase.column.max.version</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.2. Versions and HBase Operations</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
In this section we look at the behavior of the version dimension for each of the core HBase operations.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.2.1. Get/Scan</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Gets are implemented on top of Scans. The below discussion of <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Get</a> applies equally to <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Scan.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Scans</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
By default, i.e. if you specify no explicit version, when doing a get, the cell whose version has the largest value is returned (which may or may not be the latest one written, see later). The default behavior can be modified in the following ways:</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>to return more than one version, see <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html#setMaxVersions()" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Get.setMaxVersions()</a></li><li>to return versions other than the latest, see <a href="http://hbase.apache.org/apidocs/org/apache/hadoop/hbase/client/Get.html#setTimeRange(long,%20long)" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Get.setTimeRange()</a></li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
To retrieve the latest version that is less than or equal to a given value, thus giving the 'latest' state of the record at a certain point in time, just use a range from 0 to the desired version and set the max versions to 1.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.2.2. Default Get Example</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The following Get will only retrieve the current version of the row</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Get get = <strong>new</strong> Get(Bytes.toBytes("row1"));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Result r = table.get(get);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>byte[]</strong> b = r.getValue(CF, ATTR);  <em>// returns current version of value</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.2.3. Versioned Get Example</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The following Get will return the last 3 versions of the row.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Get get = <strong>new</strong> Get(Bytes.toBytes("row1"));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
get.setMaxVersions(3);  <em>// will return last 3 versions of row</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Result r = table.get(get);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>byte[]</strong> b = r.getValue(CF, ATTR);  <em>// returns current version of value</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
List&lt;KeyValue&gt; kv = r.getColumn(CF, ATTR);  <em>// returns all versions of this column</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.2.4. Put</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Doing a put always creates a new version of a cell, at a certain timestamp. By default the system uses the server’s currentTimeMillis, but you can specify the version (= the long integer) yourself, on a per-column level. This means you could assign a time in
 the past or the future, or use the long value for non-time purposes.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
To overwrite an existing value, do a put at exactly the same row, column, and version as that of the cell you want to overwrite.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Implicit Version Example</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The following Put will be implicitly versioned by HBase with the current time.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Put put = <strong>new</strong> Put(Bytes.toBytes(row));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
put.add(CF, ATTR, Bytes.toBytes( data));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
table.put(put);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Explicit Version Example</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The following Put has the version timestamp explicitly set.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> CF = "cf".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>public static final byte[]</strong> ATTR = "attr".getBytes();</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
...</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Put put = <strong>new</strong> Put( Bytes.toBytes(row));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<strong>long</strong> explicitTimeInMs = 555;  <em>// just an example</em></p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
put.add(CF, ATTR, explicitTimeInMs, Bytes.toBytes(data));</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
table.put(put);</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Caution: the version timestamp is used internally by HBase for things like time-to-live calculations. It’s usually best to avoid setting this timestamp yourself. Prefer using a separate timestamp attribute of the row, or have the timestamp as a part of the
 row key, or both.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.2.5. Delete</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
There are three different types of internal delete markers. See Lars Hofhansl’s blog for discussion of his attempt adding another, <a href="http://hadoop-hbase.blogspot.com/2012/01/scanning-in-hbase.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Scanning
 in HBase: Prefix Delete Marker</a>.</p>
<ul style="list-style-type:none;font-size:14px;line-height:21px;"><li style="list-style-type:none;">
<ul style="list-style-type:none;"><li>Delete: for a specific version of a column.</li><li>Delete column: for all versions of a column.</li><li>Delete family: for all columns of a particular ColumnFamily</li></ul></li></ul><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
When deleting an entire row, HBase will internally create a tombstone for each ColumnFamily (i.e., not each individual column).</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Deletes work by creating <em>tombstone</em> markers. For example, let’s suppose we want to delete a row. For this you can specify a version, or else by default the currentTimeMillis is used. What this means is <em>delete all cells where the version is less
 than or equal to this version</em>. HBase never modifies data in place, so for example a delete will not immediately delete (or mark as deleted) the entries in the storage file that correspond to the delete condition. Rather, a so-called <em>tombstone</em> is
 written, which will mask the deleted values. When HBase does a major compaction, the tombstones are processed to actually remove the dead values, together with the tombstones themselves. If the version you specified when deleting a row is larger than the version
 of any value in the row, then you can consider the complete row to be deleted.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
For an informative discussion on how deletes and versioning interact, see the thread <a href="http://comments.gmane.org/gmane.comp.java.hadoop.hbase.user/28421" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Put w/timestamp → Deleteall
 → Put w/ timestamp fails</a> up on the user mailing list.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Also see <a href="http://hbase.apache.org/book.html#keyvalue" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">keyvalue</a> for more information on the internal KeyValue format.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Delete markers are purged during the next major compaction of the store, unless the KEEP_DELETED_CELLS option is set in the column family (See <a href="http://hbase.apache.org/book.html#cf.keep.deleted" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Keeping
 Deleted Cells</a>). To keep the deletes for a configurable amount of time, you can set the delete TTL via the hbase.hstore.time.to.purge.deletes property in <em>hbase-site.xml</em>. If hbase.hstore.time.to.purge.deletes is not set, or set to 0, all delete
 markers, including those with timestamps in the future, are purged during the next major compaction. Otherwise, a delete marker with a timestamp in the future is kept until the major compaction which occurs after the time represented by the marker’s timestamp
 plus the value of hbase.hstore.time.to.purge.deletes, in milliseconds.</p>
<table border="0" cellpadding="0" style="color:rgb(0,0,0);font-size:14px;line-height:21px;margin-left:30px;"><tbody style="margin-left:30px;"><tr style="margin-left:30px;"><td width="80"> </td>
<td style="margin-left:30px;">
<p align="left" style="margin-left:30px;">This behavior represents a fix for an unexpected change that was introduced in HBase 0.94, and was fixed in <a href="https://issues.apache.org/jira/browse/HBASE-10118" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-10118</a>.
 The change has been backported to HBase 0.94 and newer branches.</p>
</td>
</tr></tbody></table><p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.3. Current Limitations</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.3.1. Deletes mask Puts</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Deletes mask puts, even puts that happened after the delete was entered. See <a href="https://issues.apache.org/jira/browse/HBASE-2256" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">HBASE-2256</a>. Remember that a delete writes a tombstone,
 which only disappears after then next major compaction has run. Suppose you do a delete of everything ⇐ T. After this you do a new put with a timestamp ⇐ T. This put, even if it happened after the delete, will be masked by the delete tombstone. Performing
 the put will not fail, but when you do a get you will notice the put did have no effect. It will start working again after the major compaction has run. These issues should not be a problem if you use always-increasing versions for new puts to a row. But they
 can occur even if you do not care about time: just do delete and put immediately after each other, and there is some chance they happen within the same millisecond.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
27.3.2. Major compactions change query results</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
<em>…​create three cell versions at t1, t2 and t3, with a maximum-versions setting of 2. So when getting all versions, only the values at t2 and t3 will be returned. But if you delete the version at t2 or t3, the one at t1 will appear again. Obviously, once
 a major compaction has run, such behavior will not be the case anymore…​</em> (See <em>Garbage Collection</em> in <a href="http://outerthought.org/blog/417-ot.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">Bending time in HBase</a>.)</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
28. Sort Order</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
All data model operations HBase return data in sorted order. First by row, then by ColumnFamily, followed by column qualifier, and finally timestamp (sorted in reverse, so newest records are returned first).</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
29. Column Metadata</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
There is no store of column metadata outside of the internal KeyValue instances for a ColumnFamily. Thus, while HBase can support not only a wide number of columns per row, but a heterogeneous set of columns between rows as well, it is your responsibility to
 keep track of the column names.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
The only way to get a complete set of columns that exist for a ColumnFamily is to process all the rows. For more information about how HBase stores data internally, see <a href="http://hbase.apache.org/book.html#keyvalue" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">keyvalue</a>.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
30. Joins</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
Whether HBase supports joins is a common question on the dist-list, and there is a simple answer: it doesn’t, at not least in the way that RDBMS' support them (e.g., with equi-joins or outer-joins in SQL). As has been illustrated in this chapter, the read data
 model operations in HBase are Get and Scan.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
However, that doesn’t mean that equivalent join functionality can’t be supported in your application, but you have to do it yourself. The two primary strategies are either denormalizing the data upon writing to HBase, or to have lookup tables and do the join
 between HBase tables in your application or MapReduce code (and as RDBMS' demonstrate, there are several strategies for this depending on the size of the tables, e.g., nested loops vs. hash-joins). So which is the best approach? It depends on what you are
 trying to do, and as such there isn’t a single answer that works for every use case.</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
31. ACID</p>
<p align="left" style="font-size:14px;line-height:21px;margin-left:30px;">
See <a href="http://hbase.apache.org/acid-semantics.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">ACID Semantics</a>. Lars Hofhansl has also written a note on <a href="http://hadoop-hbase.blogspot.com/2012/03/acid-in-hbase.html" rel="nofollow" style="color:rgb(61,107,167);text-decoration:none;">ACID
 in HBase</a>.</p>
            </div>
                </div>
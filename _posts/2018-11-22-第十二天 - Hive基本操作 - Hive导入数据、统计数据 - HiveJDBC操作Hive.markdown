---
layout:     post
title:      第十二天 - Hive基本操作 - Hive导入数据、统计数据 - HiveJDBC操作Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="第十二天-hive基本操作-hive导入数据统计数据-hivejdbc操作hive">第十二天 - Hive基本操作 - Hive导入数据、统计数据 - HiveJDBC操作Hive</h1>

<p></p><div class="toc">
<ul>
<li><a href="#%E7%AC%AC%E5%8D%81%E4%BA%8C%E5%A4%A9-hive%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C-hive%E5%AF%BC%E5%85%A5%E6%95%B0%E6%8D%AE%E7%BB%9F%E8%AE%A1%E6%95%B0%E6%8D%AE-hivejdbc%E6%93%8D%E4%BD%9Chive" rel="nofollow">第十二天 - Hive基本操作 - Hive导入数据、统计数据 - HiveJDBC操作Hive</a><ul>
<li><ul>
<li><a href="#%E4%B8%80hive%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86%E8%A1%A5%E5%85%85" rel="nofollow">一、Hive基础知识补充</a><ul>
<li><ul>
<li><a href="#%E6%A6%82%E5%BF%B5" rel="nofollow">概念</a></li>
<li><a href="#hive%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C%E4%BA%8C" rel="nofollow">Hive基本操作(二)</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E4%BA%8Chive-sql%E6%93%8D%E4%BD%9C%E6%A1%88%E4%BE%8B%E4%B8%80%E7%BB%9F%E8%AE%A1dataflowlog%E6%96%87%E4%BB%B6%E4%B8%AD%E7%9A%84%E6%B5%81%E9%87%8F%E6%95%B0%E6%8D%AE" rel="nofollow">二、Hive SQL操作案例一：统计dataflow.log文件中的流量数据</a><ul>
<li><ul>
<li><a href="#%E6%9C%AA%E6%8C%87%E5%AE%9A%E5%88%86%E9%9A%94%E7%AC%A6" rel="nofollow">未指定分隔符</a></li>
<li><a href="#%E6%8C%87%E5%AE%9A%E5%88%86%E9%9A%94%E7%AC%A6" rel="nofollow">指定分隔符</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E4%B8%89hive-sql%E6%93%8D%E4%BD%9C%E6%A1%88%E4%BE%8B%E4%BA%8C" rel="nofollow">三、Hive SQL操作案例二：</a><ul>
<li><ul>
<li><a href="#%E9%80%9A%E8%BF%87navicat%E5%B0%86sql%E6%96%87%E4%BB%B6%E8%BD%AC%E4%B8%BAtxt" rel="nofollow">通过navicat将sql文件转为txt</a></li>
<li><a href="#%E5%B0%86%E6%95%B0%E6%8D%AE%E6%96%87%E4%BB%B6txt%E5%AF%BC%E5%85%A5%E5%88%B0hive%E4%B8%AD" rel="nofollow">将数据文件.txt导入到hive中</a></li>
<li><a href="#%E5%AF%B9%E5%BA%93%E4%B8%AD%E7%9A%84%E4%BF%A1%E6%81%AF%E8%BF%9B%E8%A1%8C%E6%9F%A5%E8%AF%A2%E6%93%8D%E4%BD%9C" rel="nofollow">对库中的信息进行查询操作</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E5%9B%9Bhivejdbc%E6%93%8D%E4%BD%9Chive" rel="nofollow">四、HiveJDBC操作Hive</a><ul>
<li><ul>
<li><a href="#%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C" rel="nofollow">准备工作</a></li>
<li><a href="#%E7%BC%96%E5%86%99%E4%BB%A3%E7%A0%81" rel="nofollow">编写代码</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</div>




<h3 id="一hive基础知识补充">一、Hive基础知识补充</h3>



<h5 id="概念">概念</h5>

<ul>
<li>Hive可以通过HiveJDBC进行操作，方便应用的封装</li>
<li>Hive有内置函数和语法，也可以编写自定义函数</li>
<li>Hive的数据类型，大部分与java中一致，其中long在hive中为bigint</li>
<li>创建数据表时，需要和数据文件结构(数据类型、列数等)对应，特别是分隔符要正确</li>
<li>Hive默认不支持update和delete语句,但是可以通过函数实现，insert语句执行较慢，一般采用导入文件数据进行导入</li>
<li>Hive主要用于数据计算</li>
</ul>



<h5 id="hive基本操作二">Hive基本操作(二)</h5>

<ul>
<li><p>切换数据库</p>

<p>use {dbName};</p></li>
<li><p>删除数据库</p>

<p>drop database [if exists] {dbName} [cascade];</p></li>
<li><p>查看当前库的所有表</p>

<p>show tables [like ‘*’] [in database];</p></li>
<li><p>创建表</p>

<p>create database.table({col_name} {data_type},…)[row format delimited  fields terminated by ‘\t’  lines terminated by ‘\n’ ];</p>

<p>[]中用于指定分隔符</p></li>
<li><p>增加字段</p>

<p>alter table {tableName} add columns (new_colName data_type);</p></li>
<li><p>查看表结构</p>

<p>desc {tableName};</p></li>
<li><p>重命名表名</p>

<p>alter table {tableName} rename to {newName};</p></li>
<li><p>删除表</p>

<p>drop table if exists {tableName};</p></li>
<li><p>导入数据</p>

<ol><li><p>从本地导入：load data local inpath ‘{localPath}’ into table {dbName.tableName};</p></li>
<li><p>从hdfs导入：load data inpath ‘{hdfsPath}’ into table {dbName.tableName};</p>

<p>内部表的数据如果从hdfs中导入，相当于移动操作，原文件会被移动到hive对应的表的文件夹下</p></li></ol></li>
</ul>



<h3 id="二hive-sql操作案例一统计dataflowlog文件中的流量数据">二、Hive SQL操作案例一：统计dataflow.log文件中的流量数据</h3>



<h5 id="未指定分隔符">未指定分隔符</h5>

<ol>
<li><p>在命令行中输入hive进入hive客户端</p>

<blockquote>
  <p>hive</p>
</blockquote></li>
<li><p>查看数据库列表</p>

<blockquote>
  <p>show databases;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536715597490.png" alt="1536715597490" title=""></p></li>
<li><p>切换数据库</p>

<blockquote>
  <p>use test；</p>
</blockquote></li>
<li><p>查看当前数据库下的所有表</p>

<blockquote>
  <p>show tables；</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536715716294.png" alt="1536715716294" title=""></p></li>
<li><p>将昨天测试用的两个表删除</p>

<blockquote>
  <p>drop table t1;</p>
  
  <p>drop table t2;</p>
</blockquote></li>
<li><p>创建内部表，本次未指定分隔符</p>

<blockquote>
  <p>create table dataflow(</p>
  
  <p>phoneNumber string,</p>
  
  <p>upflow int,</p>
  
  <p>downflow int</p>
  
  <p>);</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536716063889.png" alt="1536716063889" title=""></p></li>
<li><p>从本地导入数据文件“dataflow.log”</p>

<p>load data local inpath ‘/home/bigdata/hadoopTest/dataflow.log’ into table dataflow;</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536716406249.png" alt="1536716406249" title=""></p></li>
<li><p>查看表，使用limit关键字限定读取的行数</p>

<p>由于创建表时没有指定正确的列分隔符，所以所有数据都分在了第一列，具体可见<a href="https://blog.csdn.net/cry970795248/article/details/82632114#%E5%AF%B9hive%E7%9A%84%E6%95%B0%E6%8D%AE%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C%E4%B8%80" rel="nofollow">对hive的数据基本操作一</a></p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536716518273.png" alt="1536716518273" title=""></p></li>
</ol>



<h5 id="指定分隔符">指定分隔符</h5>

<ol>
<li><p>删除表dataflow</p>

<blockquote>
  <p>drop dataflow；</p>
</blockquote></li>
<li><p>创建表dataflow，指定分隔符</p>

<blockquote>
  <p>create table dataflow(</p>
  
  <p>phoneNumber string ,</p>
  
  <p>upflow int,</p>
  
  <p>downflow int</p>
  
  <p>)row format delimited fields terminated by ‘,’;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536716859742.png" alt="1536716859742" title=""></p></li>
<li><p>导入数据</p>

<blockquote>
  <p>load data local inpath ‘/home/bigdata/hadoopTest/dataflow.log’ into table dataflow;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536716406249.png" alt="1536716406249" title=""></p></li>
<li><p>查看表，由于创建表时指定了正确的分隔符，此时能正确读取</p></li>
</ol>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536716925409.png" alt="1536716925409" title=""></p>

<ol>
<li><p>当使用聚合函数、group by 等时，hql将转换成MapReduce进行计算，即涉及到数据计算时才会触发MapReduce转换。</p>

<p>group by相当于MapReduce的mapper阶段，输出keyout，valueout；</p>

<p>聚合函数相当于MapReduce的reducer阶段，执行reduce()方法中的迭代计算。</p>

<blockquote>
  <p>select phoneNumber,sum(upflow),sum(downflow)</p>
  
  <p>from dataflow</p>
  
  <p>group by phoneNumber;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719165577.png" alt="1536719165577" title=""></p>

<p>如果运行进程卡死或者出了问题，可以使用此命令强制结束</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719189908.png" alt="1536719189908" title=""></p>

<p>运行结果</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719244757.png" alt="1536719244757" title=""></p>

<p>加上总计流量</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719355556.png" alt="1536719355556" title=""></p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719410012.png" alt="1536719410012" title=""></p></li>
</ol>



<h3 id="三hive-sql操作案例二">三、Hive SQL操作案例二：</h3>



<h5 id="通过navicat将sql文件转为txt">通过navicat将sql文件转为txt</h5>

<ul>
<li><p>导入sql文件</p>

<ol><li>新建数据库，注意选择字符集为utf8</li></ol>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719829681.png" alt="1536719829681" title=""></p>

<ol><li>右键库，运行SQL文件</li></ol>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719975518.png" alt="1536719975518" title=""></p>

<ol><li>导入完成，刷新数据库</li></ol>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719985738.png" alt="1536719985738" title=""></p></li>
</ul>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536719921668.png" alt="1536719921668" title=""></p>

<ul>
<li><p>将mysql中已有的数据导入到hive中</p>

<ol><li><p>右键数据库，选择导出，选择表类型为文本文件(.txt)</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536720327733.png" alt="1536720327733" title=""></p></li>
<li><p>每个表作为独立的文件导出，对于每个表需要指定导出路径</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536720376343.png" alt="1536720376343" title=""></p></li>
<li><p>列分隔符(默认\t)，日期格式，是否包含该行首(表结构信息)，可以做自动读取表结构信息的功能</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536720205745.png" alt="1536720205745" title=""></p></li>
<li><p>导出完成</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536720265415.png" alt="1536720265415" title=""></p></li>
<li><p>在导出目录中将会出现导出的txt文件</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536720282630.png" alt="1536720282630" title=""></p></li></ol></li>
</ul>



<h5 id="将数据文件txt导入到hive中">将数据文件.txt导入到hive中</h5>

<ol>
<li><p>使用Xftp将文件上传至Linux中</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536720645502.png" alt="1536720645502" title=""></p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536720673360.png" alt="1536720673360" title=""></p></li>
<li><p>在导入数据文件前需要先创建表，根据表结构创建make、orders、purchaser、type四个表</p>

<p>创建表时需要注意指定分隔符</p>

<ul><li><p>表make</p>

<p>创建</p>

<blockquote>
  <p>create table make(</p>
  
  <p>id int,</p>
  
  <p>makeName string</p>
  
  <p>)row format delimited fields terminated by ‘\t’;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536720749481.png" alt="1536720749481" title=""></p>

<p>导入数据</p>

<blockquote>
  <p>load data local inpath ‘/home/bigdata/data/make.txt’ into table make;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536721077569.png" alt="1536721077569" title=""></p>

<p>查看数据</p>

<blockquote>
  <p>select * from make limit 10;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536721097224.png" alt="1536721097224" title=""></p></li>
<li><p>表orders</p>

<p>创建</p>

<blockquote>
  <p>id int,</p>
  
  <p>typeId int,</p>
  
  <p>retail int,</p>
  
  <p>purchaserId int</p>
  
  <p>)row format delimited fields terminated by ‘\t’;</p>
</blockquote>

<p>导入数据</p>

<blockquote>
  <p>load data local inpath ‘/home/bigdata/data/orders.txt’ into table orders;</p>
</blockquote>

<p>查看数据</p>

<blockquote>
  <p>select * from orders limit 10;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536721372793.png" alt="1536721372793" title=""></p></li>
<li><p>表purchaser</p>

<p>创建</p>

<blockquote>
  <p>create table purchaser( <br>
  id int,</p>
  
  <p>age int,</p>
  
  <p>income bigint,</p>
  
  <p>deposit float</p>
  
  <p>)row format delimited fields terminated by ‘\t’;</p>
</blockquote>

<p>导入数据</p>

<blockquote>
  <p>load data local inpath ‘/home/bigdata/data/purchaser.txt/’ into table purchaser;</p>
</blockquote>

<p>查看数据</p>

<blockquote>
  <p>select * from purchaser limit 10;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536722625117.png" alt="1536721846497" title=""></p></li>
<li><p>表type</p>

<p>创建</p>

<blockquote>
  <p>create table type(</p>
  
  <p>id int,</p>
  
  <p>typeName string,</p>
  
  <p>energyConsumption float,</p>
  
  <p>makeId int</p>
  
  <p>)row format delimited fields terminated by ‘\t’;</p>
</blockquote>

<p>导入数据</p>

<blockquote>
  <p>load data local inpath ‘/home/bigdata/data/type.txt’ into table type;</p>
</blockquote>

<p>查看数据</p>

<blockquote>
  <p>select * from type</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536722239019.png" alt="1536722239019" title=""></p></li></ul></li>
</ol>



<h5 id="对库中的信息进行查询操作">对库中的信息进行查询操作</h5>

<ol>
<li><p>统计每种车型(type)的成交量，显示车型名称、成交量</p>

<blockquote>
  <p>SELECT t.typeName,COUNT(o.id) as count <br>
  FROM orders o,type t <br>
  WHERE o.typeId = t.id <br>
  GROUP BY o.typeId;</p>
  
  <p>左连接：</p>
  
  <p>SELECT t.typeName,COUNT(o.id) as count <br>
  from orders o <br>
  LEFT JOIN type t <br>
  ON o.typeId = t.id <br>
  GROUP BY t.typeName,t.id;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536751055948.png" alt="1536722239019" title=""></p></li>
<li><p>统计每种车型(type)的成交额，显示车型名称、成交额</p>

<blockquote>
  <p>select typeName,SUM(o.retail) as sum <br>
  from orders o,type t <br>
  where o.typeId = t.id <br>
  GROUP BY o.typeId;</p>
  
  <p>左连接：</p>
  
  <p>SELECT t.typeName,SUM(o.retail) as sum <br>
  FROM orders o LEFT JOIN type t <br>
  ON o.typeId = t.id <br>
  GROUP BY t.typeName,o.typeId;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536752002372.png" alt="1536722239019" title=""></p></li>
<li><p>统计每种品牌(make)的成交量，显示品牌名称、成交量</p>

<blockquote>
  <p>SELECT makeName,SUM(a.typeCount) as count <br>
  FROM <br>
  (SELECT COUNT(o.id) as typeCount,makeId <br>
  FROM <br>
  orders o,type t <br>
  WHERE o.typeId = t.id <br>
  GROUP BY o.typeId) a,make m <br>
  WHERE a.makeId = m.id <br>
  GROUP BY makeName,m.id;</p>
  
  <p>左连接：</p>
  
  <p>select m.makeName,SUM(a.count) <br>
  FROM <br>
  (SELECT t.makeId,t.typeName,COUNT(o.id) as count <br>
  from orders o <br>
  LEFT JOIN type t <br>
  ON o.typeId = t.id <br>
  GROUP BY t.makeId,t.typeName,t.id) as a LEFT JOIN make m <br>
  on a.makeId = m.id <br>
  GROUP BY m.makeName,m.id;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536752270144.png" alt="1536722239019" title=""></p></li>
<li><p>统计每种品牌(make)的成交额，显示品牌名称、成交额</p>

<blockquote>
  <p>SELECT makeName,SUM(a.typeSum) as sum <br>
  FROM <br>
  (SELECT sum(o.retail) as typeSum,makeId <br>
  FROM <br>
  orders o,type t <br>
  WHERE o.typeId = t.id <br>
  GROUP BY o.typeId,makeId) a,make m <br>
  WHERE a.makeId = m.id <br>
  GROUP BY makeName,m.id;</p>
  
  <p>左连接：</p>
  
  <p>select m.makeName,SUM(a.sum) <br>
  FROM <br>
  (SELECT t.makeId,t.typeName,SUM(o.retail) as sum <br>
  from orders o <br>
  LEFT JOIN type t <br>
  ON o.typeId = t.id <br>
  GROUP BY t.id,t.makeId,t.typeName) as a LEFT JOIN make m <br>
  on a.makeId = m.id <br>
  GROUP BY m.id,m.makeName;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536752482734.png" alt="1536722239019" title=""></p></li>
<li><p>统计每个年龄段成交量，显示年龄段和成交量</p>

<blockquote>
  <p>select FLOOR(p.age/10) ,count(o.retail) <br>
  from purchaser p,orders o <br>
  where p.id = o.purchaserId <br>
  GROUP BY FLOOR(p.age/10) <br>
  ;</p>
  
  <p>左连接：</p>
  
  <p>select FLOOR(p.age/10) ,count(o.retail) <br>
  from purchaser p <br>
  LEFT JOIN orders o <br>
  on p.id = o.purchaserId <br>
  GROUP BY FLOOR(p.age/10) <br>
  ;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536753202301.png" alt="1536722239019" title=""></p></li>
<li><p>统计每个年龄段成交额，显示年龄段和成交额</p>

<blockquote>
  <p>select FLOOR(p.age/10) ,sum(o.retail) <br>
  from purchaser p,orders o <br>
  where p.id = o.purchaserId <br>
  GROUP BY FLOOR(p.age/10) <br>
  ;</p>
  
  <p>左连接：</p>
  
  <p>select FLOOR(p.age/10) ,sum(o.retail) <br>
  from purchaser p <br>
  LEFT JOIN orders o <br>
  on p.id = o.purchaserId <br>
  GROUP BY FLOOR(p.age/10) <br>
  ;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536753358573.png" alt="1536722239019" title=""></p></li>
<li><p>统计每种汽车排量的成交量，显示汽车排量和成交量</p>

<blockquote>
  <p>select t.energyConsumption ,count(o.retail) <br>
  from orders o,type t <br>
  where o.typeId = t.id <br>
  GROUP BY t.energyConsumption;</p>
  
  <p>左连接：</p>
  
  <p>select t.energyConsumption ,count(o.retail) <br>
  from orders o <br>
  LEFT JOIN type t <br>
  on o.typeId = t.id <br>
  GROUP BY t.energyConsumption;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536753616125.png" alt="1536722239019" title=""></p></li>
<li><p>统计每种汽车排量的成交额，显示汽车排量和成交额</p>

<blockquote>
  <p>select t.energyConsumption ,sum(o.retail) <br>
  from orders o,type t <br>
  where o.typeId = t.id <br>
  GROUP BY t.energyConsumption;</p>
  
  <p>左连接：</p>
  
  <p>select t.energyConsumption ,sum(o.retail) <br>
  from orders o <br>
  LEFT JOIN type t <br>
  on o.typeId = t.id <br>
  GROUP BY t.energyConsumption;</p>
</blockquote>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536753767164.png" alt="1536722239019" title=""></p></li>
</ol>



<h3 id="四hivejdbc操作hive">四、HiveJDBC操作Hive</h3>



<h5 id="准备工作">准备工作</h5>

<ul>
<li><p>打开HiveJDBC的远程监控服务端口</p>

<ol><li><p>前台运行，需要一直停留在该命令</p>

<blockquote>
  <p>hive –service hiveserver2 –hiveconf hive.server2.thrift.port=10010</p>
</blockquote>

<p>执行此命令后，会话窗口光标将会一直停留，无法执行新命令，如果此时ctrl+c强行停止命令或者关闭了会话窗口，远程连接的监控服务端口也会关闭</p></li>
<li><p>设置为后台运行</p>

<blockquote>
  <p>hive –service hiveserver2 –hiveconf hive.server2.thrift.port=10010 &amp;</p>
</blockquote>

<p>执行此命令后，远程连接的监控服务端口将会在后台运行，但是如果关闭了会话窗口，此端口也会关闭</p></li>
<li><p>设置为始终运行状态，此时关闭会话也可以继续运行，但是如果重启机器就需要重新启动服务</p>

<blockquote>
  <p>nohup hive –service hiveserver2 –hiveconf hive.server2.thrift.port=10010 &amp;</p>
</blockquote></li></ol></li>
<li><p>在Eclipse中新建普通Java项目，右键项目新建文件夹lib</p></li>
<li><p>导入共11个jar包到lib目录中，并且添加至构建路径</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536753900424.png" alt="1536722239019" title=""></p></li>
<li><p>从CentOS中的hive安装路径的conf目录下下载hive-log4j.properties至本地，放在src目录下并重命名为log4j.properties</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536756559270.png" alt="1536756559270" title=""></p></li>
<li><p>修改log4j.properties第19行为hive.root.logger=DEBUG,console，作用是在运行时能在控制台输出hive的运行状态信息</p></li>
<li><p>控制台输出的信息不全面，如果要查看详细信息需要在CentOS中查看日志文件。日志文件的目录在hive安装路径的conf目录下hive-log4j.properties中配置的路径，</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536754156740.png" alt="1536722239019" title=""></p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536754175489.png" alt="1536722239019" title=""></p></li>
</ul>



<h5 id="编写代码">编写代码</h5>

<p>Test01.java</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.sql.Connection;
<span class="hljs-keyword">import</span> java.sql.DriverManager;
<span class="hljs-keyword">import</span> java.sql.ResultSet;
<span class="hljs-keyword">import</span> java.sql.Statement;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">Test01</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1.加载驱动</span>
        Class.forName(<span class="hljs-string">"org.apache.hive.jdbc.HiveDriver"</span>);
        <span class="hljs-comment">// 2.打开连接</span>
        Connection connection = DriverManager.getConnection(<span class="hljs-string">"jdbc:hive2://SZ01:10010/test"</span>);
        <span class="hljs-comment">// 3.获得操作对象</span>
        Statement statement = connection.createStatement();
        <span class="hljs-comment">// 4.操作Hive</span>
        String sql = <span class="hljs-string">"select a.energyConsumption,sum(b.retail) from orders b LEFT JOIN type a  on a.id = b.typeId GROUP BY a.energyConsumption"</span>;
        <span class="hljs-comment">// 5.接受结果</span>
        ResultSet rs = statement.executeQuery(sql);
        <span class="hljs-keyword">while</span>(rs.next()) {
            System.out.println(rs.getFloat(<span class="hljs-number">1</span>) + <span class="hljs-string">" "</span> +  rs.getString(<span class="hljs-number">2</span>));
        }
        <span class="hljs-comment">// 6.关闭连接</span>
        rs.close();
        statement.close();
        connection.close();
    }
}</code></pre>

<p>运行结果</p>

<p><img src="https://day12-1253629415.cos.ap-guangzhou.myqcloud.com/1536754419122.png" alt="1536722239019" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
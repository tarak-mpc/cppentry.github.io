---
layout:     post
title:      Hive 操作 HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Luomingkui1109/article/details/82748023				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>0.Hive on Hbase</p>

<p>    HBase 用于在线业务服务，不适合做统计分析。(使用 HBase 进行查询的条件比较苛 刻，只能根据 RowKey 去进行查询)</p>

<p>    Hive 用于离线分析，适合数据分析，统计。</p>

<p>    在 Hbase 的基础课程中，我们在 Hive 中创建表，并将表格关联到 Hbase 中的表格，通 过这种方式可以借助 HQL 对 Hbase 中的数据进行分析，但是，在执行分析语句时，会发现 HQL 执行的非常慢。</p>

<p>    这是因为虽然我们使用 Hive 创建了一张表，但是这张表并不适用于做统计分析，因为 数据都是以 key-value 形式存在的，虽然在 Hive 建立了表，但是本质上数据还是在 HBase 上，你所执行的 Hive 语句(比如 group by 等按列的操作)，还是会转化为 HBase 中的操作 (scan 等)，其效果与直接在 HBase 里进行相同操作是一样的，都是灾难性的。</p>

<p>    因此，我们一般使用 Hive on HBase 完成数据的加载，也就是关系型数据库、Hive 数 据库或者文件中的数据向 HBase 的导入。我们一般会创建一张映射表，然后往映射表里灌 入数据，后台就会帮我们把数据灌入 HBase 中。</p>

<p> </p>

<p>1.HBase与Hive的对比</p>

<p>（1）Hive</p>

<p>    数据仓库：Hive的本质其实就相当于将HDFS中已经存储的文件在Mysql中做了一个双射关系，以方便使用HQL去管理查询。</p>

<p>    用于数据分析、清洗：Hive适用于离线的数据分析和清洗，延迟较高。</p>

<p>    基于HDFS、MapReduce：Hive存储的数据依旧在DataNode上，编写的HQL语句终将是转换为MapReduce代码执行。</p>

<p>（2）HBase</p>

<p>    数据库：是一种面向列存储的非关系型数据库。</p>

<p>    用于存储结构化和非结构话的数据：适用于单表非关系型数据的存储，不适合做关联查询，类似JOIN等操作。</p>

<p>    基于HDFS：数据持久化存储的体现形式是Hfile，存放于DataNode中，被ResionServer以region的形式进行管理。</p>

<p>    延迟较低，接入在线业务使用：面对大量的企业数据，HBase可以直线单表大量数据的存储，同时提供了高效的数据访问速度。</p>

<p> </p>

<p>2.HBase与Hive集成使用</p>

<p>    尖叫提示：HBase与Hive的集成在最新的两个版本中无法兼容。所以，我们只能含着泪勇敢的重新编译：hive-hbase-handler-1.2.2.jar！！好气！！</p>

<p>    因为我们后续可能会在操作Hive的同时对HBase也会产生影响，所以Hive需要持有操作HBase的Jar，那么接下来拷贝Hive所依赖的Jar包（或者使用软连接的形式）。</p>

<pre class="has">
<code>$ export HBASE_HOME=/opt/module/hbase

$ export HIVE_HOME=/opt/module/ hive-1.2.2



$ ln -s $HBASE_HOME/lib/hbase-common-1.3.1.jar  $HIVE_HOME/lib/hbase-common-1.3.1.jar

$ ln -s $HBASE_HOME/lib/hbase-server-1.3.1.jar $HIVE_HOME/lib/hbase-server-1.3.1.jar

$ ln -s $HBASE_HOME/lib/hbase-client-1.3.1.jar $HIVE_HOME/lib/hbase-client-1.3.1.jar

$ ln -s $HBASE_HOME/lib/hbase-protocol-1.3.1.jar $HIVE_HOME/lib/hbase-protocol-1.3.1.jar

$ ln -s $HBASE_HOME/lib/hbase-it-1.3.1.jar $HIVE_HOME/lib/hbase-it-1.3.1.jar

$ ln -s $HBASE_HOME/lib/htrace-core-3.1.0-incubating.jar $HIVE_HOME/lib/htrace-core-3.1.0-incubating.jar

$ ln -s $HBASE_HOME/lib/hbase-hadoop2-compat-1.3.1.jar $HIVE_HOME/lib/hbase-hadoop2-compat-1.3.1.jar

$ ln -s $HBASE_HOME/lib/hbase-hadoop-compat-1.3.1.jar $HIVE_HOME/lib/hbase-hadoop-compat-1.3.1.jar</code></pre>

<p>    同时在hive-site.xml中修改zookeeper的属性，如下：</p>

<pre class="has">
<code>&lt;property&gt;

  &lt;name&gt;hive.zookeeper.quorum&lt;/name&gt;

  &lt;value&gt;hadoop102,hadoop103,hadoop104&lt;/value&gt;

  &lt;description&gt;The list of ZooKeeper servers to talk to. This is only needed for read/write locks.&lt;/description&gt;

&lt;/property&gt;

&lt;property&gt;

  &lt;name&gt;hive.zookeeper.client.port&lt;/name&gt;

  &lt;value&gt;2181&lt;/value&gt;

  &lt;description&gt;The port of ZooKeeper servers to talk to. This is only needed for read/write locks.&lt;/description&gt;

&lt;/property&gt;</code></pre>

<p>1．案例一</p>

<p>目标：建立Hive表，关联HBase表，插入数据到Hive表的同时能够影响HBase表。</p>

<p>分步实现：</p>

<p>(1) 在Hive中创建表同时关联HBase</p>

<pre class="has">
<code>CREATE TABLE hive_hbase_emp_table(

empno int,

ename string,

job string,

mgr int,

hiredate string,

sal double,

comm double,

deptno int)

STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'

WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,info:ename,info:job,info:mgr,info:hiredate,info:sal,info:comm,info:deptno")

TBLPROPERTIES ("hbase.table.name" = "hbase_emp_table");</code></pre>

<p>    提示：完成之后，可以分别进入Hive和HBase查看，都生成了对应的表。</p>

<p>(2) 在Hive中创建临时中间表，用于load文件中的数据</p>

<p>    提示：不能将数据直接load进Hive所关联HBase的那张表中</p>

<pre class="has">
<code>CREATE TABLE emp(

empno int,

ename string,

job string,

mgr int,

hiredate string,

sal double,

comm double,

deptno int)

row format delimited fields terminated by '\t';</code></pre>

<p>(3) 向Hive中间表中load数据</p>

<p>    hive&gt; load data local inpath '/home/admin/softwares/data/emp.txt' into table emp;</p>

<p>(4) 通过insert命令将中间表中的数据导入到Hive关联HBase的那张表中</p>

<p>    hive&gt; insert into table hive_hbase_emp_table select * from emp;</p>

<p>(5) 查看Hive以及关联的HBase表中是否已经成功的同步插入了数据</p>

<p>     Hive：</p>

<p>    hive&gt; select * from hive_hbase_emp_table;</p>

<p>    HBase：</p>

<p>    hbase&gt; scan ‘hbase_emp_table’</p>

<p>2．案例二</p>

<p>目标：在HBase中已经存储了某一张表hbase_emp_table，然后在Hive中创建一个外部表来关联HBase中的hbase_emp_table这张表，使之可以借助Hive来分析HBase这张表中的数据。</p>

<p>注：该案例2紧跟案例1的脚步，所以完成此案例前，请先完成案例1。</p>

<p>分步实现：</p>

<p>(1) 在Hive中创建外部表</p>

<pre class="has">
<code>CREATE EXTERNAL TABLE relevance_hbase_emp(

empno int,

ename string,

job string,

mgr int,

hiredate string,

sal double,

comm double,

deptno int)

STORED BY

'org.apache.hadoop.hive.hbase.HBaseStorageHandler'

WITH SERDEPROPERTIES ("hbase.columns.mapping" =

":key,info:ename,info:job,info:mgr,info:hiredate,info:sal,info:comm,info:deptno")

TBLPROPERTIES ("hbase.table.name" = "hbase_emp_table");</code></pre>

<p>(2) 关联后就可以使用Hive函数进行一些分析操作了</p>

<p>    hive (default)&gt; select * from relevance_hbase_emp;</p>            </div>
                </div>
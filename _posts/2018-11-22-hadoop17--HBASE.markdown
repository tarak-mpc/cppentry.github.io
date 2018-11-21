---
layout:     post
title:      hadoop17--HBASE
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请标明出处。https://blog.csdn.net/kXYOnA63Ag9zqtXx0/article/details/82954503					https://blog.csdn.net/forever428/article/details/84071599				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><ul><li><a href="#HBASE_1" rel="nofollow">HBASE的基本概念</a></li><ul><li><a href="#hiveHBASE_3" rel="nofollow">hive和HBASE区别</a></li><li><a href="#HBASEHDFS_8" rel="nofollow">HBASE与HDFS的区别</a></li><li><a href="#HBASE_14" rel="nofollow">HBASE与关系型数据库的区别</a></li><li><a href="#HBASE_18" rel="nofollow">HBASE的来源</a></li><ul><li><a href="#bigtable_28" rel="nofollow">bigtable背景</a></li><li><a href="#_34" rel="nofollow">非关系型数据库</a></li><li><a href="#_39" rel="nofollow">关系型数据库</a></li><li><a href="#HBASE_45" rel="nofollow">HBASE解决的问题与诞生背景</a></li></ul></ul><li><a href="#HBASE_52" rel="nofollow">HBASE的特点</a></li><li><a href="#HBASE_58" rel="nofollow">HBASE的存储</a></li><li><a href="#HBSE_65" rel="nofollow">HBSE架构</a></li><li><a href="#HBASE_73" rel="nofollow">HBASE的安装</a></li><ul><li><a href="#HBASE_77" rel="nofollow">HBASE完全分布式安装.</a></li><li><a href="#HBASE_150" rel="nofollow">启动HBASE</a></li></ul><li><a href="#HBASEshell_161" rel="nofollow">HBASE的基本使用(shell操作)</a></li><ul><li><a href="#HBASEDDL_182" rel="nofollow">HBASE---DDL</a></li><ul><li><a href="#namespace_184" rel="nofollow">namespace的操作</a></li><li><a href="#table_216" rel="nofollow">table的操作</a></li></ul><li><a href="#HBASEDML_265" rel="nofollow">HBASE---DML</a></li><ul><li><a href="#_267" rel="nofollow">插入数据</a></li><li><a href="#_308" rel="nofollow">查询数据</a></li><li><a href="#_361" rel="nofollow">删除表</a></li></ul></ul></ul></ul></ul></div><p></p>
<h3><a id="HBASE_1"></a>HBASE的基本概念</h3>
<h4><a id="hiveHBASE_3"></a>hive和HBASE区别</h4>
<ol>
<li>hive是数据仓库的工具, hive不是真正保存数据的, 而是对HDFS上的数据的一个映射, 而HBASE是一个数据库, 数据是由HBASE来保存的.</li>
<li>hive主要是用来分析数据, 而HBASE主要是用来保存数据的</li>
</ol>
<h4><a id="HBASEHDFS_8"></a>HBASE与HDFS的区别</h4>
<ol>
<li>HDFS是一个分布式存储系统, 能够存储大量的数据, 但是不支持查询等操作, 而HBASE是建立在HDFS上的数据库</li>
<li>HDFS不支持单条记录的快速检索, 而HBASE是数据库, 检索是最基本的功能</li>
<li>HDFS提供的是一个高延迟的处理, HBASE提供了单行记录低延迟的随机访问</li>
</ol>
<h4><a id="HBASE_14"></a>HBASE与关系型数据库的区别</h4>
<p>HBASE没有事务的支持, HBASE存储的数据都是非结构化或者半结构化的数据 , 而关系型数据库存储的都是结构化的数据</p>
<h4><a id="HBASE_18"></a>HBASE的来源</h4>
<p>hadoop的诞生源自于谷歌的三大论文</p>

<table>
<thead>
<tr>
<th>谷歌论文</th>
<th>山寨框架</th>
</tr>
</thead>
<tbody>
<tr>
<td>GFS</td>
<td>HDFS</td>
</tr>
<tr>
<td>mapreduce</td>
<td>mapreduce</td>
</tr>
<tr>
<td>bigtable</td>
<td>HBASE(hadoop base)</td>
</tr>
</tbody>
</table><h5><a id="bigtable_28"></a>bigtable背景</h5>
<p>谷歌搜索引擎爬取的网页, 都是非结构化数据, 如何存储数据变成了一个难题. bigtable的设计思想为多行三列的一个大表</p>
<p>HBASE继承了bigtable的思想, 开发了 <strong>面向列的</strong>, <strong>可扩展的</strong>, <strong>非关系型的</strong>数据库</p>
<h5><a id="_34"></a>非关系型数据库</h5>
<ol>
<li>MongoDB : c++编写, 基于文档方式的存储, 存储格式是json格式</li>
<li>redis: 基于内存的, key-value的形式, 通过key快速找到value</li>
</ol>
<h5><a id="_39"></a>关系型数据库</h5>
<ol>
<li>mysql</li>
<li>Oracle</li>
<li>SQLserver</li>
</ol>
<h5><a id="HBASE_45"></a>HBASE解决的问题与诞生背景</h5>
<ol>
<li>
<p>互联网的公司数据一般都是爬取过来的, 大量的数据, 并且都是非结构化, 传统的数据库已经不能满足需求了</p>
</li>
<li>
<p>交通部门的摄像头, 每天拍摄的内容都是图象, 不能保存在关系型数据库</p>
<p>HBASE适合存储海量数据, 能够将查询速度范围控制在秒的级别, 并且HBASE是面向列存储的.</p>
</li>
</ol>
<h3><a id="HBASE_52"></a>HBASE的特点</h3>
<ol>
<li>面向列: 存储都是按照列进行存储的</li>
<li>稀疏: 对于空的列式不占用空间的</li>
<li>无类型: HBASE存储的数据都是字符串, 没有数据类型.</li>
</ol>
<h3><a id="HBASE_58"></a>HBASE的存储</h3>
<ol>
<li>列簇: 一个列簇下边保存多个列, 所谓的列簇指的就是一组列</li>
<li>行键: HBASE没有主键的概念, 但是存在行键(rowkey)的概念, 可以把行键理解成主键, 行键就是一条记录的唯一标识</li>
<li>版本: 用于表示更新的数据, 也可以作为数据的检索项</li>
<li>单元格: 最小的存储单元, 在检索数据的时候, 可以通过以下方式进行单元格数据的唯一确定 <mark>rowkey+列簇+时间戳+value</mark></li>
</ol>
<h3><a id="HBSE_65"></a>HBSE架构</h3>
<p><img src="https://img-blog.csdnimg.cn/20181114173632599.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2ZvcmV2ZXI0Mjg=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
主要组件</p>
<ol>
<li>client : 访问HMaster</li>
<li>Hmaster : 管理类的工作, 主要管理regionServer, 负载均衡, 管理元数据(数据库的名字, 表的名字)</li>
<li>regionServer : 存放region, 而region里边存放的是真正的数据, 并且regionServer可以响应读写请求, 来管理表中的数据</li>
<li>zookeeper : 保证对外提供服务的只有一台, 作为HBASE访问的入口, 切换HMaster的主备, HBASE的部分元数据也是保存在zookeeper中.</li>
</ol>
<h3><a id="HBASE_73"></a>HBASE的安装</h3>
<p>由于HBASE是基于HDFS上的, 所以在安装HBASE是, 必须存在hadoop, 另外HBASE依赖于zookeeper, 所以在及集群中存在zookeeper</p>
<h4><a id="HBASE_77"></a>HBASE完全分布式安装.</h4>
<p>在完全分布式的安装中, 由于HBASE对时间的要求</p>
<ol>
<li>解压HBASE安装文件到安装路径下</li>
</ol>
<pre><code>sudo tar -zvxf hbase-1.3.1-bin.tar.gz -C /opt/app/

</code></pre>
<ol start="2">
<li>修改权限为hadoop用户</li>
</ol>
<pre><code>sudo chown -R hadoop:hadoop hbase-1.3.1/

</code></pre>
<ol start="3">
<li>配置HBASE的环境 $HBASE/conf/hvase-env.sh</li>
</ol>
<pre><code>// 修改JAVA_HOME
export JAVA_HOME=/opt/app/jdk1.8.0_181

// 关闭自带的zookeeper, 不需要HBASE自带的zookeeper
export HBASE_MANAGES_ZK=false
</code></pre>
<ol start="4">
<li>配置HBASE-site.xml文件</li>
</ol>
<pre><code>// 配置HBASE临时目录
&lt;property &gt;
		&lt;name&gt;hbase.tmp.dir&lt;/name&gt;
		&lt;value&gt;/opt/app/hbase-1.3.1/hbase&lt;/value&gt;
  &lt;/property&gt;

// 配置hbase 临时目录保存位置
 &lt;property &gt;
	    &lt;name&gt;hbase.rootdir&lt;/name&gt;
	    &lt;value&gt;hdfs://ns1/hbase&lt;/value&gt;
 &lt;/property&gt;

//设置hbase的存储模式为分布式存储
 &lt;property &gt;
	    &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
	    &lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;

//设置hbase中zookeeper 的信息，地址为自己配置的主机名称
&lt;property&gt;
	    &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
	    &lt;value&gt;hadoop01,hadoop02,hadoop03&lt;/value&gt;
&lt;/property&gt;
</code></pre>
<ol start="5">
<li>配置hbase在hdfs上的保存路径，如果配置了HA 则路径必须和HA提供的路径一直，并且还需要把hdfs-site.xml 和 core-site.xml 两个配置文件复制到hbase的conf目录下</li>
</ol>
<pre><code>cp core-site.xml /opt/app/hbase-1.3.1/conf/
cp hdfs-site.xml /opt/app/hbase-1.3.1/conf/
</code></pre>
<ol start="6">
<li>配置regionservers 该文件类似于 salves 文件，描述的是 regionserver的节点信息. 在配置过程中存在几个regionserver 就写几个主机名称</li>
</ol>
<pre><code>hadoop01
hadoop02
hadoop03
</code></pre>
<ol start="7">
<li>修改 hbase中hadoop的jar包，由于hbase要操作hdfs，所以hbase需要hadoop的jar包支持。所以在hbase中配置环境中hadoop的jar包</li>
<li>分发hbase到不同的节点上</li>
</ol>
<pre><code>sudo scp -r hbase-1.3.1/ hadoop02:/opt/app/
sudo scp -r hbase-1.3.1/ hadoop03:/opt/app/
</code></pre>
<ol start="9">
<li>修改分发后的文件权限</li>
</ol>
<h4><a id="HBASE_150"></a>启动HBASE</h4>
<ol>
<li>启动master服务(只需要启动一个)</li>
</ol>
<pre><code>bin/hbase-daemon.sh start master
</code></pre>
<ol start="2">
<li>启动 regionserver 服务 在3个节点上启动</li>
</ol>
<pre><code>bin/hbase-daemon.sh start regionserver
</code></pre>
<ol start="3">
<li>在web端查看, 端口号为16010</li>
</ol>
<h3><a id="HBASEshell_161"></a>HBASE的基本使用(shell操作)</h3>
<p>HBASE自带了shell操作, 而HBASE本身就是可以作为client</p>
<pre><code>bin/base shell
</code></pre>
<p>在shell中删除时, 若出现不能删除时, 则可以使用<strong>Ctrl+delete</strong>进行删除, 在shell中可以通过help命令进行查看操作命令介绍</p>
<pre><code>hbase(main):002:0&gt; help
---------------------------------------------------
 Group name: ddl
  Commands: alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, get_table, is_disabled, is_enabled, list, locate_region, show_filters

  Group name: namespace
  Commands: alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables

  Group name: dml
  Commands: append, count, delete, deleteall, get, get_counter, get_splits, incr, put, scan, truncate, truncate_preserve

</code></pre>
<p>通过help命令可查看到很多操作信息，主要在shell操作中分为两类操作 ddl dml</p>
<h4><a id="HBASEDDL_182"></a>HBASE—DDL</h4>
<p>在hbase中没有数据库的概念，数据库的概念在hbase中称作namespace 可以理解为namespace就是关系型数据库中的数据库的概念，在namespace下面存在着数据表</p>
<h5><a id="namespace_184"></a>namespace的操作</h5>
<ol>
<li>显示所有的namespace</li>
</ol>
<pre><code>hbase(main):003:0&gt;  list_namespace
NAMESPACE
default
hbase
student
3 row(s) in 0.0230 seconds
</code></pre>
<ol start="2">
<li>查看namespace下的所有表</li>
</ol>
<pre><code>hbase(main):004:0&gt; create_namespace 'hbase'
0 row(s) in 1.4860 seconds
</code></pre>
<ol start="3">
<li>创建namespace</li>
</ol>
<pre><code>hbase(main):006:0&gt; create_namespace 'nstest'
0 row(s) in 0.9010 seconds
</code></pre>
<ol start="4">
<li>查看namespace描述</li>
</ol>
<pre><code>hbase(main):007:0&gt; describe_namespace 'nstest'
DESCRIPTION
{NAME =&gt; 'nstest'}
1 row(s) in 0.0630 seconds
</code></pre>
<ol start="5">
<li>删除namespace</li>
</ol>
<pre><code>hbase(main):008:0&gt; drop_namespace 'nstest'
0 row(s) in 0.9190 seconds
</code></pre>
<h5><a id="table_216"></a>table的操作</h5>
<p>HBASE中提供的建表的实例</p>
<pre><code>create 'ns1:t1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}
</code></pre>
<ul>
<li>ns1 : 表示namespace的名字</li>
<li>t1 : 表名称</li>
<li>f1 : 列簇名称</li>
<li>=&gt; : 等于的意思</li>
<li>{} : 定义一个列簇，不同列簇可以在不同{}中表示, 一个{}只能定义一个列簇</li>
<li>{}中的变量名称必须为大写</li>
<li>在创建表的时候，可以指定一个namespace,如果没有指定namespace则创建的表在默认的namespace下</li>
</ul>
<ol>
<li>创建表, 没有指定namespace 则该表创建在默认的default中</li>
</ol>
<pre><code>hbase(main):009:0&gt; create 'stu_info','f1'
0 row(s) in 4.4160 seconds

=&gt; Hbase::Table - stu_info
</code></pre>
<ol start="2">
<li>创建表，并且指定一个namespace</li>
</ol>
<pre><code>hbase(main):038:0&gt; create 'student:stu_info2','f1'
0 row(s) in 2.2320 seconds

=&gt; Hbase::Table - student:stu_info2
</code></pre>
<p>Hbase是基于hdfs之上的，可以在HFDS上查看创建好的表</p>
<ol start="3">
<li>查看表的描述信息</li>
</ol>
<pre><code>hbase(main):039:0&gt; desc 'stu_info'
Table stu_info is ENABLED
stu_info
COLUMN FAMILIES DESCRIPTION
{NAME =&gt; 'f1', BLOOMFILTER =&gt; 'ROW', VERSIONS =&gt; '1', IN_MEMORY =&gt; 'false', KEEP
_DELETED_CELLS =&gt; 'FALSE', DATA_BLOCK_ENCODING =&gt; 'NONE', TTL =&gt; 'FOREVER', COMP
RESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', BLOCKCACHE =&gt; 'true', BLOCKSIZE =&gt; '6553
6', REPLICATION_SCOPE =&gt; '0'}
1 row(s) in 0.0780 seconds
</code></pre>
<ol start="4">
<li>删除表. 删除表之前需要禁用该表, 然后再进行删除操作,  否则会抛异常</li>
</ol>
<pre><code>hbase(main):041:0&gt;  disable 'stu_info' //禁用要删除的表, 禁用是disable, 启用是enable
0 row(s) in 2.3570 seconds

hbase(main):042:0&gt; drop  'stu_info'  // 删除表
0 row(s) in 1.2420 seconds

</code></pre>
<h4><a id="HBASEDML_265"></a>HBASE—DML</h4>
<h5><a id="_267"></a>插入数据</h5>
<p>HBASE提供的实例:</p>
<pre><code>help 'put'
hbase&gt; put 'ns1:t1', 'r1', 'c1', 'value'
</code></pre>
<p>可以把put理解为mysql中的insert语句, 代表插入一条语句</p>
<p>ns1: 命名空间</p>
<p>t1: 表名称</p>
<p>r1: rowkey</p>
<p>c1: 要插入的值</p>
<p>value:</p>
<ol>
<li>在默认的命名空间中创建一张表, 作为插入数据测试用</li>
</ol>
<pre><code>create 'hadoop' 'info','f1'
0 row(s) in 6.1850 seconds

=&gt; Hbase::Table - hadoopinfo
</code></pre>
<ol start="2">
<li>插入数据</li>
</ol>
<pre><code>hbase(main):004:0&gt; put 'hadoopinfo','1001','f1:name','zhangsan'
0 row(s) in 0.6470 seconds
hbase(main):004:0&gt; put 'hadoopinfo','1001','f1:age','18'
0 row(s) in 0.2920 seconds
</code></pre>
<ol start="3">
<li>在插入数据的时候, rowkey都是以字符创的格式进行保存的, 它的最大长度可以保存64kb</li>
</ol>
<h5><a id="_308"></a>查询数据</h5>
<p>对于查询的数据可以使用get命令, get命令可以理解为快速查询, 不支持全表查询</p>
<ol>
<li>查询指定的rowkey查询</li>
</ol>
<pre><code>hbase(main):007:0&gt; get 'hadoopinfo', '1001'
COLUMN                CELL
 f1:age               timestamp=1542082626437, value=18
 f1:name              timestamp=1542082513237, value=zhangsan
1 row(s) in 0.2580 seconds
</code></pre>
<ol start="2">
<li>按照指定的列簇进行查询</li>
</ol>
<pre><code>hbase(main):008:0&gt; get 'hadoopinfo','1001','f1'
COLUMN                CELL
 f1:age               timestamp=1542082626437, value=18
 f1:name              timestamp=1542082513237, value=zhangsan
1 row(s) in 0.0580 seconds
</code></pre>
<ol start="3">
<li>按照指定列簇下的列进行查询</li>
</ol>
<pre><code>hbase(main):009:0&gt; get 'hadoopinfo','1001','f1:name'
COLUMN                CELL
 f1:name              timestamp=1542082513237, value=zhangsan
1 row(s) in 0.0170 seconds

hbase(main):010:0&gt; get 'hadoopinfo','1001','f1:age'
COLUMN                CELL
 f1:age               timestamp=1542082626437, value=18
1 row(s) in 0.0110 seconds
</code></pre>
<p>get命令不支持全表查询, 如果要查询全表, 则可以使用scan命令, 类似于mysql中的select * from . 这种全表扫描的方式在企业开发中是不用的, 因为数据量特别大, 全表扫面所需要的时间特别长.</p>
<pre><code>hbase(main):016:0&gt; scan 'hadoopinfo'	//禁止使用
ROW                   COLUMN+CELL
 1001                 column=f1:age, timestamp=1542082626437, value=18
 1001                 column=f1:name, timestamp=1542082513237, value=zhangsan
1 row(s) in 0.0180 seconds

hbase(main):015:0&gt;  scan 'hadoopinfo', {COLUMNS =&gt; 'f1:name'}//斟酌使用
ROW                   COLUMN+CELL
 1001                 column=f1:name, timestamp=1542082513237, value=zhangsan
1 row(s) in 0.0370 seconds
</code></pre>
<h5><a id="_361"></a>删除表</h5>
<p>在删除表的时候, 可以使用delete, 可以按照指定的rowkey进行删除, 但是只删除一个列簇是删不掉的, 执行命令不报异常</p>
<pre><code>hbase(main):017:0&gt; delete 'hadoopinfo' ,'1001', 'f1'
0 row(s) in 0.1060 seconds
hbase(main):019:0&gt; scan 'hadoopinfo'//查看删除后的数据
ROW                   COLUMN+CELL
 1001                 column=f1:age, timestamp=1542082626437, value=18
 1001                 column=f1:name, timestamp=1542082513237, value=zhangsan
1 row(s) in 0.0180 seconds
</code></pre>
<p>在删除时必须指定一个列</p>
<pre><code>delete 'hadoopinfo' ,'1001', 'f1:name'
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
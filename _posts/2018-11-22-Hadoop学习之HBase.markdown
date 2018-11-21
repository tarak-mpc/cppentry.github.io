---
layout:     post
title:      Hadoop学习之HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1. HBase有哪些基本的特征？</p>
<p>2. HBase相对于关系数据库能解决的问题是什么？</p>
<p>3. HBase的数据模型是什么？如何表述？有哪些操作形式？</p>
<p>4. HBase的模式Schema设计的一些概念和原则</p>
<p>5. HBase的拓扑结构是什么样的？</p>
<p>6. HBase与Cassender的比较？</p>
<p><br></p>
<h3><a name="t0"></a>1. HBase有哪些基本的特征？</h3>
<p>HBase是类似于google的bigtable的开源实现，拥有以下特征：</p>
<p>1）. 在HDFS之上</p>
<p>2）. 基于列存储的分布式数据库</p>
<p>3）. 用于实时地读、写大规模数据集</p>
<p>其他HBase的特性： </p>
<p>1）.没有真正的索引，行顺序存储，也没有所谓的索引膨胀问题。</p>
<p>2） 自动分区，表增长时，自动分区到新的节点上。</p>
<p>3） 线性扩展和区域会自动重新平衡，运行RegionServer，达到负载均衡的目的。</p>
<p>4）.容错和普通商用的硬件支持。这点同hadoop类似。</p>
<p><br></p>
<h3><a name="t1"></a>2. HBase相对于关系数据库能解决的问题是什么？</h3>
<p>HBase与关系数据的区别？</p>
<p>其实就是关系数据库与HBase各自的优缺点。</p>
<p>关系数据库的缺憾：</p>
<p>1）. 扩展困难</p>
<p>2）. 维护复杂</p>
<p>HBase就是解决可伸缩行的问题。通过简单增加节点来获取线性扩展性。不支持SQL。</p>
<p>HBase与RDBMS的区别？</p>
<p>1）. 表的设计：HBase的表可以很高，很宽，可伸缩性很强。而且表的模式是物理存储的直接反映。</p>
<p>2）. 拓扑： HBase能水平分区并在上千个节点上自动复制。</p>
<p>3）. 应用形式： 开发者必须承担更多的责任来正确地利用HBase的检索和存储方式。</p>
<p>4）. RDBMS 遵循固定的模式，如“codd 12 规则”，强调事务的“强一致性”、参照完整性、SQL支持、数据的逻辑与物理形式相对独立。等等。适用于中小规模的数据，但对于数据的<span>规模和并发读写</span>方面进行大规模扩展时，RDBMS会性能大大降低，分布式更为困难，因为其需要放弃很多RDBMS的易用的特性。</p>
<p><br></p>
<p>HBase适用于上亿、上千亿级的数据，如果是只有上千、上百万级别是数据，传统的RDBMS是更好的选择。</p>
<p>HBase需要更多硬件，如果硬件较少，如5个，干不成什么好事。</p>
<p>如果从RDBMS移植到HBase，需要消除RDBMS的很多额外特性，如列数据类型、第二索引、事务、高级查询等。</p>
<p><br></p>
<h3><a name="t2"></a>3. HBase的数据模式是怎么样的？即有哪些元素？如何存储？等</h3>
<p>1）. 数据模式</p>
<p>如下列三个表：</p>
<p>第一个是一个稀疏的表，实际上它是一个虚表，仅是一个概念视图，不是真实的存储形式，它来源于后两个表。</p>
<p>而后两个表才是真正的表，物理视图，他们是实际的存储形式，而且它们是按列族进行存储的。</p>
<p></p>
<table summary="表 webtable" border="1" style="font-family:'Times New Roman';"><thead><tr><th align="left" style="line-height:1.2;">Row Key</th>
<th align="left" style="line-height:1.2;">Time Stamp</th>
<th align="left" style="line-height:1.2;">ColumnFamily <code class="varname">contents</code></th>
<th align="left" style="line-height:1.2;">ColumnFamily <code class="varname">anchor</code></th>
</tr></thead><tbody><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t9</td>
<td align="left" style="line-height:1.2;"> </td>
<td align="left" style="line-height:1.2;"><code class="varname">anchor:cnnsi.com</code> = "CNN"</td>
</tr><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t8</td>
<td align="left" style="line-height:1.2;"> </td>
<td align="left" style="line-height:1.2;"><code class="varname">anchor:my.look.ca</code> = "CNN.com"</td>
</tr><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t6</td>
<td align="left" style="line-height:1.2;"><code class="varname">contents:html</code> = "&lt;html&gt;..."</td>
<td align="left" style="line-height:1.2;"> </td>
</tr><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t5</td>
<td align="left" style="line-height:1.2;"><code class="varname">contents:html</code> = "&lt;html&gt;..."</td>
<td align="left" style="line-height:1.2;"> </td>
</tr><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t3</td>
<td align="left" style="line-height:1.2;"><code class="varname">contents:html</code> = "&lt;html&gt;..."</td>
<td align="left" style="line-height:1.2;"> </td>
</tr></tbody></table><p><br></p>
<p></p>
<table summary="ColumnFamily anchor" border="1" style="font-family:'Times New Roman';"><colgroup><col align="left"><col align="left"><col align="left"></colgroup><thead><tr><th align="left" style="line-height:1.2;">Row Key</th>
<th align="left" style="line-height:1.2;">Time Stamp</th>
<th align="left" style="line-height:1.2;">Column Family <code class="varname">anchor</code></th>
</tr></thead><tbody><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t9</td>
<td align="left" style="line-height:1.2;"><code class="varname">anchor:cnnsi.com</code> = "CNN"</td>
</tr><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t8</td>
<td align="left" style="line-height:1.2;"><code class="varname">anchor:my.look.ca</code> = "CNN.com"</td>
</tr></tbody></table><p><br></p>
<p></p>
<table summary="ColumnFamily contents" border="1" style="font-family:'Times New Roman';"><colgroup><col align="left"><col align="left"><col align="left"></colgroup><thead><tr><th align="left" style="line-height:1.2;">Row Key</th>
<th align="left" style="line-height:1.2;">Time Stamp</th>
<th align="left" style="line-height:1.2;">ColumnFamily "contents:"</th>
</tr></thead><tbody><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t6</td>
<td align="left" style="line-height:1.2;"><code class="varname">contents:html</code> = "&lt;html&gt;..."</td>
</tr><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t5</td>
<td align="left" style="line-height:1.2;"><code class="varname">contents:html</code> = "&lt;html&gt;..."</td>
</tr><tr><td align="left" style="line-height:1.2;">"com.cnn.www"</td>
<td align="left" style="line-height:1.2;">t3</td>
<td align="left" style="line-height:1.2;"><code class="varname">contents:html</code> = "&lt;html&gt;..."</td>
</tr></tbody></table><br><br><p>2）. HBase的基本元素：</p>
<p>表、行、列、单元格： 表的基本要素</p>
<p>键：一般是指行的键，即唯一标识某行的元素。表中的行，可以根据键进行排序，而对表的访问，也通过键。</p>
<p>列族：所有列族成员拥有相同的前缀，某列族的成员，需要预先定义，但也可以直接进行追加。</p>
<p>列族成员会一起放进存储器。而HBase面向列的存储，是面向列族的数据存储（这个通过上面那个表的示例可以看出来），数据存储与调优都在这个层次，HBase表与RDBMS中表类似，行是排序的，客户端可以把列添加到列族中去。</p>
<p>单元格cell： 单元格中存放的是不可分割的字节数组。并且每个单元格拥有版本信息。HBase的是按版本信息倒序排列。</p>
<p>区域region：将表水平划分，是HBase集群分布数据的最小单位。在线的所有区域就构成了表的内容。</p>
<p>加锁：对数据行进行更新，都需加锁。保持原子性。</p>
<p><br></p>
<p>3） 数据模型有哪些操作？</p>
<p>Get、Scan、Put、Delete，即返回特定行的属性，多行属性、插入、删除数据。</p>
<p>这些都需要一个HTable实例来操作。分别有Get、Scan、Put、Delete类来指定相应的参数、属性。</p>
<p>以scan为示例：</p>
<p><br></p>
<div class="dp-highlighter bg_java">
<div class="bar">
<div class="tools"><strong>[java]</strong> <a href="http://blog.csdn.net/viewcode/article/details/9967497#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><a href="http://blog.csdn.net/viewcode/article/details/9967497#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></div>
</div>
<ol class="dp-j" start="1"><li class="alt"><span><span>HTable htable = ...      </span><span class="comment">// instantiate HTable</span><span>  </span></span></li><li><span>      </span></li><li class="alt"><span>Scan scan = <span class="keyword">new</span><span> Scan();  </span></span></li><li><span>scan.addColumn(Bytes.toBytes(<span class="string">"cf"</span><span>),Bytes.toBytes(</span><span class="string">"attr"</span><span>));  </span></span></li><li class="alt"><span>scan.setStartRow( Bytes.toBytes(<span class="string">"row"</span><span>));                   </span><span class="comment">// start key is inclusive</span><span>  </span></span></li><li><span>scan.setStopRow( Bytes.toBytes(<span class="string">"row"</span><span> +  (</span><span class="keyword">char</span><span>)</span><span class="number">0</span><span>));  </span><span class="comment">// stop key is exclusive</span><span>  </span></span></li><li class="alt"><span>ResultScanner rs = htable.getScanner(scan);  </span></li><li><span><span class="keyword">try</span><span> {  </span></span></li><li class="alt"><span>  <span class="keyword">for</span><span> (Result r = rs.next(); r != </span><span class="keyword">null</span><span>; r = rs.next()) {  </span></span></li><li><span>  <span class="comment">// process result...</span><span>  </span></span></li><li class="alt"><span>} <span class="keyword">finally</span><span> {  </span></span></li><li><span>  rs.close();  <span class="comment">// always close the ResultScanner!</span><span>  </span></span></li><li class="alt"><span>}  </span></li></ol></div>
<br><br>
4） 返回结果的排序方式是什么？
<p>先是行、再是列族、再是列修饰符，最后是时间戳（反向排序，最新的在前面）。</p>
<p><br></p>
<p>5）最后，HBase不支持联合查询</p>
<p><br></p>
<p>6）mapreduce与HBase表配合使用，默认mapreduce的任务分割是根据HBase表中region的多少来分割，一个region就有一个map。</p>
<p><br></p>
<h3><a name="t3"></a>4. HBase的模式Schema设计的一些概念和原则</h3>
<p>1）模式的创建与更新</p>
<p>可以使用HBase Shell或HBase Admin来创建和编辑HBase的模式。</p>
<p>在0.90.x 版本，只能先禁用表，再修改列族，而0.92.x版本以后，支持在线修改。</p>
<p><br></p>
<p>而且表和列族修改后，如size， region， block size等，在下次 <span>主紧缩 或 存储文件</span>时 起作用。</p>
<p><br></p>
<p>2）列族的数量</p>
<p>-列族数量越少越好，即使同时有两个列族，查询的时候总是访问其中一个列族，不会同时访问。</p>
<p>-当一个表存在多个列族，当基数差距很大时，如A族有100万行，B族10亿行，A族可能会被分散到很多区域region，导致扫描A的效率降低。</p>
<p>-另外，多个列族在flush和compaction时，会造成很多I/O负担。</p>
<p><br></p>
<p>3）行键设计RowKey</p>
<p>a. 不要将RowKey设计成有序的形式，因为这样容易阻塞并行性，将负载压都在一台机器上</p>
<p>b. 定位一个单元，需要行，列名和时间戳。如果一个单元格的坐标很大，会占用内存，索引用光。所以，解决方法：列族名尽量小，如一个字符a，短属性名，而行键长度可读即可（行键长度对数据访问无太大影响）,将数字字符转换为数字字节模式（节省空间）。</p>
<p>c. 倒序时间戳有助于找到找到最近版本值</p>
<p>d. 行键是在列族范围内有效，不同列族中可以拥有同样的行键</p>
<p>e. 行键永远不能变</p>
<p><br></p>
<p>4）HBase支持所有能转换为字节数组的东西，如字符串、数字、复杂对象、计数器、甚至图像。</p>
<p>5）列族可以设置存活时间TTL，超时后，HBase自动删除数据</p>
<p>6）第二索引和查询： 这里面有很多东西，需要查看对应版本官方的文档更好些。</p>
<p><br></p>
<h3><a name="t4"></a>5. HBase的拓扑结构是什么？</h3>
<p>1）拓扑结构： 类似于HDFS的mast与slave，mapreduce的tasktracker与jobtracker的关系，HBase也有master和RegionServer</p>
<p><br></p>
<p>2）HBase与ZooKeeper的关系是什么？</p>
<p>HBase必须管理一个ZooKeeper实例，它依赖ZooKeeper，主要目的是，通过ZooKeeper来协调区域内的服务器，它负责目录表、主控机地址等重要信息，若有服务器崩溃，HBase就可以通过ZooKeeper来协调分配。<br>
RegionServer在HBase的配置文件conf/regionservers文件中，而HBase集群的站点配置在conf/hbase-site.xml和conf/hbase-env.sh中配置。HBase尽量遵循了Hadoop的规则。</p>
<p><br></p>
<p>3）HBase的内部结构管理状况：</p>
<p>其内部有-ROOT, -META的特殊目录表，用于维护当前集群上所有<span style="color:#ff0000;">区域的列表、位置和状态</span>。</p>
<p>-ROOT表包含 -META表的区域列表，而-META表示包含用户的的区域列表。</p>
<p>所以，HBase管理的流程是: </p>
<p>Client -- 链接到ZooKeeper -- 查找-ROOT表的位置 -- 查找-META表的位置 -- 查找用户的区域所在的节点、位置及其状态等 -- 直接管理指定区域的RegionServer并进行交互。</p>
<p><br></p>
<p>HBase支持Java及MapReduce的开发。</p>
<p>HBase提供了Thrift、REST及Avro的接口。HBase需要有一个相应的接口客户端负责与这些接口的交互。但是这些需要代理进行处理请求和响应，所以比java更慢。</p>
<p>%hbase-daemon.sh start/stop rest/thrift/avro  //启动或终止对应的客户端</p>
<p><br></p>
<p>4）HBase的使用示例：</p>
<p>1. 创建表</p>
<p>如在外壳环境下： create ‘station’， {NAME =&gt; 'info', VERSION &gt;= 1}</p>
<p>2. 加载数据</p>
<p>MapReduce 与 HBase可以充分利用集群的分布式模型，将原始数据复制到hdfs中。</p>
<p>每个任务只有一个HTable实例， 默认情况下，每个HTable.put(put)，在执行插入操作时，不任何缓存。不过想使用缓存，是可以自己设置的。</p>
<p>3. Web查询</p>
<p>可以直接使用HBase的java API来实现一个Web的应用。HTable.get()可用来获取已定义的列族的所有内容。get的结果返回给Result， 包含的是数据行。</p>
<p>HBase还可以使用扫描器scanner来检索观测数据。并且获取的是一个有序的结果。类似于传统数据库中的“游标”。HTable.getScanner(scan).</p>
<p><br></p>
<h3><a name="t5"></a>6. HBase与Cassender的比较</h3>
<div>不同的应用，应该选用不同的NoSQL数据库，Cassandra, HBase, MongoDB, Riak都有各自的优缺点。</div>
<div>而且以上各种数据库，都在发展中，随着版本变换，特点也会发生变化。</div>
<div> 根据 CAP理论（Consistency一致性, Availability 可获得性, Partitioning tolerance 分区容忍），二者可以简单区分一下。</div>
<div><br></div>
<div>HBase是Hadoop生态系统的一部分，又其他框架如PIG, HIVE等的支持，而Cassender上运行mapreduce相对比较复杂的。总体上来说，Cassender或许在存储上比较有效，但HBase的数据处理能力更强些。</div>
<div>HBase有Shell脚本和Web页面的处理能力，而Cassender没有Shell的支持，只有API，可用性上不如HBase。</div>
<div><br></div>
<div>Cassender的Schema发生变化时，需要集群重启，但Cassender宣称“写操作永不失败”，而HBase是有可能的。</div>
<div>场景：Cassandra 对由高速光纤连接的小型数据中心（几百个节点左右）是最佳的，而HBase适合网络“缓慢”且不可预料的Internet网。</div>
<div><br></div>
<div>其他：</div>
<div>HBase性能调优，别人很好的总结：</div>
<a href="http://rdc.taobao.com/team/jm/archives/975" rel="nofollow">http://rdc.taobao.com/team/jm/archives/975</a>
            </div>
                </div>
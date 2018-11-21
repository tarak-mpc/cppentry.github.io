---
layout:     post
title:      简述 Hbase && Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<ul><li><span style="font-size:18px;color:#666666;">Hbase数据模型</span></li><li><span style="font-size:18px;color:#666666;">Hbase架构</span></li><li><span style="font-size:18px;color:#666666;">Hbase传统关系数据库的区别</span></li><li><span style="font-size:18px;color:#666666;">Hive的体系结构</span></li><li><span style="font-size:18px;color:#666666;">HIve部署类型</span></li></ul><p><span style="font-size:14px;color:#666666;"><br></span></p>
<p><span style="color:rgb(102,102,102);"><span style="font-size:14px;"><br></span></span></p>
<p><span style="background-color:rgb(255,255,204);"><span style="font-size:14px;">Hbase数据模型</span></span></p>
<p style="text-align:center;"><span style="font-size:14px;color:#666666;"><img src="https://img-blog.csdn.net/20171220111500201" width="450" height="230" alt=""><br></span></p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20171220114552675" width="450" height="150" alt=""></p>
<p style="text-align:center;"></p>
<div style="text-align:left;"><span style="font-size:12px;">     </span><span style="font-size:12px;"> •表中仅有一行数据，行的唯一标识为<em>com.cnn.www</em>，对这行数据的每一次逻辑修改都有一个时间戳关联对应。</span></div>
<div style="text-align:left;"><span style="font-size:12px;">      •表中共有四列：<em>contents:html</em>，<em>anchor:cnnsi.com</em>，<em>anchor:my.look.ca</em>，<em>mime:type</em>，每一列以前缀的方式给出其所属的列族。</span></div>
<div style="text-align:left;"><span style="font-size:12px;"><br></span></div>
<div style="text-align:left;font-size:14px;"><span style="font-size:12px;"> <span style="font-size:14px;"> <u>元素</u>由行健、列（</span></span><span style="font-size:14px;">&lt;列族&gt;:&lt;限定符&gt;）和时间戳唯一确定，元素中的数据以字节码的形式存储，没有类型之分。</span></div>
<div style="text-align:left;">
<p style="font-size:14px;">  <u>行键</u>是数据行在表中的唯一标识，并作为检索记录的主键。</p>
<p style="font-size:14px;"><br></p>
<p style="font-size:14px;">  在Hbase中<u>访问表中的行</u>有三种方式：通过单个行键访问，给定行键的范围访问，全表扫描。</p>
<p style="font-size:14px;"><br></p>
<p style="font-size:14px;"></p>
<p><span style="font-size:14px;">  </span><span style="font-size:14px;">Hbase提供了两个版本的<u>回收方式</u>：</span></p>
<span style="font-size:14px;">    </span><span style="font-size:12px;">1.对每个数据单元，只存储指定个数的最新版本；</span></div>
<p><span style="font-size:12px;">     2.保存最近一段时间内的版本（如七天），客户端可以按需查询。</span></p>
<div style="text-align:left;"><span style="font-size:14px;"><br></span></div>
<div style="text-align:left;"><span style="background-color:rgb(255,255,204);"><span style="font-size:14px;">物理模型</span></span></div>
<div style="text-align:left;">
<p><span style="font-size:14px;">   概念模型中的一个行进行分割并按照列族存储。</span></p>
<p><span style="font-size:14px;">   </span><span style="font-size:12px;">表中的空值是不被存储的；如果没有指名时间戳，则返回指定列的最新数据值；可以随时向表中的任何一个列添加新列，而不需要事先声明。</span></p>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20171220115259743" width="500" height="230" alt=""></div>
<div style="text-align:center;"><span style="font-size:14px;"><br></span></div>
<div style="text-align:center;"><span style="font-size:14px;"><br></span></div>
<div style="text-align:center;"><span style="font-size:14px;"><br></span></div>
<div style="text-align:left;"><span style="background-color:rgb(255,255,204);"><span style="font-size:14px;">Hbase架构</span></span></div>
<div style="text-align:left;"><span style="font-size:14px;"></span>
<p><span style="font-size:14px;">   Hbase采用master/slave架构，</span><span style="font-size:14px;">主节点运行的服务称为</span><span style="font-size:14px;">HMaster</span><span style="font-size:14px;">，从节点服务称为</span><span style="font-size:14px;">HRegionServer，</span><span style="font-size:14px;">底层采用</span><span style="font-size:14px;">HDFS</span><span style="font-size:14px;">存储数据。</span></p>
</div>
<div style="text-align:center;"><br><br></div>
<div style="text-align:center;"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20171220115525030" width="800" height="400" alt=""><br></span></div>
<div style="text-align:left;">
<p><span style="font-size:12px;">      1) Client</span></p>
<p><span style="font-size:12px;">          Client端使用Hbase的RPC机制与HMaster和HRegionServer进行通信。</span></p>
<p><span style="font-size:12px;">      2) ZooKeeper</span></p>
<p><span style="font-size:12px;">          存储了ROOT表的地址、HMaster的地址和HRegionServer地址。</span></p>
<p><span style="font-size:12px;">      3) HMaster</span></p>
<p><span style="font-size:12px;">          Hbase主节点，将Region分配给HRegionServer，协调HRegionServer的负载并维护集群状态。</span></p>
<p><span style="font-size:12px;">      4) HRegionServer</span></p>
<p><span style="font-size:12px;">          HRegionServer主要负责响应用户I/O请求，向HDFS文件系统中读写数据。</span></p>
<p><br><br></p>
<p></p>
<div><span style="background-color:rgb(255,255,204);"><span style="font-size:14px;"> Hbase和关系型数据库的区别</span></span><span style="font-size:14px;background-color:rgb(255,255,255);">： </span></div>
<div style="font-size:14px;"><span style="background-color:rgb(255,255,255);">   · Hbase只提供字符串这一种数据类型，其他数据类型的操作只能靠用户自行处理，而关系型数据库有丰富的数据类型；</span></div>
<div style="font-size:14px;"><span style="background-color:rgb(255,255,255);">   · Hbase数据操作只有很简单的插入、查询、删除、修改、清空等操作，不能实现表与表关联操作，而关系型数据库有大量此类SQL语句和函数； </span></div>
<div style="font-size:14px;"><span style="background-color:rgb(255,255,255);">   · Hbase基于列式存储，每个列族都由几个文件保存，不同列族的文件是分离的，关系型数据库基于表格设计和行模式保存；</span></div>
<div style="font-size:14px;"><span style="background-color:rgb(255,255,255);">   · Hbase修改和删除数据实现上是插入带有特殊标记的新纪录，而关系型数据库是数据内容的替换和修改；·Hbase为分布式而设计，可通过增加机器实现性能和数据增长，而关系型数据库很难做到这一点。 </span></div>
<br><p></p>
<p><br></p>
<p><span style="font-size:14px;background-color:rgb(255,255,204);">Hive</span></p>
</div>
</div>
<p><span style="background-color:rgb(255,255,255);"><span style="font-size:14px;">     </span><span style="font-size:14px;">Hive</span><span style="font-size:14px;">是</span></span><span style="font-size:14px;">一个构建在Hadoop上的数据仓库框架，它起源于Facebook内部信息处理平台。</span></p>
<p><span style="font-size:14px;">    <u>设计目的</u>：让Facebook内精通SQL（但Java编程相对较弱）的分析师能够以类SQL的方式查询存放在HDFS的大规模数据集。</span></p>
<p><span style="font-size:14px;"><br></span></p>
<span style="font-size:14px;">Hive 基本框架</span>
<p><span style="font-size:14px;">Hive包含Shell环境、元数据库、解析器和数据仓库的组件，其<span style="background-color:rgb(255,255,204);">体系结构</span>如图所示：</span></p>
<p style="text-align:center;"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20171220131319380" width="500" height="320" alt=""><br></span></p>
<p></p>
<p><span style="font-size:12px;"><span style="color:rgb(64,64,64);">                      用户接口：包括</span><span style="color:rgb(64,64,64);">HiveShell</span><span style="color:rgb(64,64,64);">、</span><span style="color:rgb(64,64,64);">Thrift</span><span style="color:rgb(64,64,64);">客户端、</span><span style="color:rgb(64,64,64);">Web</span><span style="color:rgb(64,64,64);">接口。</span></span></p>
<p><span style="font-size:12px;color:rgb(64,64,64);">                      Thrift服务器：包括</span><span style="font-size:12px;color:rgb(64,64,64);">HiveShell</span><span style="font-size:12px;color:rgb(64,64,64);">、</span><span style="font-size:12px;color:rgb(64,64,64);">Thrift</span><span style="font-size:12px;color:rgb(64,64,64);">客户端、</span><span style="font-size:12px;color:rgb(64,64,64);">Web</span><span style="font-size:12px;color:rgb(64,64,64);">接口。</span></p>
<p><span style="font-size:12px;color:rgb(64,64,64);"><span style="color:rgb(64,64,64);font-size:12px;">                     </span> 元数据库：Hive</span><span style="font-size:12px;color:rgb(64,64,64);">元数据（如表信息）的集中存放地。</span></p>
<p><span style="font-size:12px;color:rgb(64,64,64);"><span style="color:rgb(64,64,64);font-size:12px;">                      </span>解析器：将</span><span style="font-size:12px;color:rgb(64,64,64);">Hive</span><span style="font-size:12px;color:rgb(64,64,64);">语句翻译成</span><span style="font-size:12px;color:rgb(64,64,64);">MapReduce</span><span style="font-size:12px;color:rgb(64,64,64);">操作。</span></p>
<p><span style="font-size:12px;color:rgb(64,64,64);"><span style="color:rgb(64,64,64);font-size:12px;">                      </span>Hadoop：底层分布式存储和计算引擎。</span></p>
<p><span style="color:rgb(64,64,64);"><br></span></p>
<span style="font-size:14px;"><span style="font-family:'微软雅黑';"></span></span>
<p><span style="font-family:'微软雅黑';background-color:rgb(255,255,204);">Hive部署</span></p>
    按照元数据库（<span style="font-family:'微软雅黑';">M</span><span style="font-family:'微软雅黑';">etastore</span><span style="font-family:'微软雅黑';">）存储位置的不同，分为：内嵌模式、本地模式和完全远程模式。</span><br><p style="text-align:center;"><span style="color:rgb(102,102,102);"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20171220183908118" width="280" height="200" alt=""><br></span></span></p>
<p><span style="color:rgb(102,102,102);"><span style="font-size:14px;"><br></span></span></p>
<p><span style="color:rgb(102,102,102);"><u><span style="font-size:14px;">内嵌模式</span></u></span></p>
<p><span style="color:rgb(102,102,102);"></span></p>
<div><span style="font-size:14px;"><span style="color:#404040;">     此</span><span style="color:#404040;">模式是安装时的默认部署模式，此时元数据存储在一个内存数据库</span><span style="color:#404040;">Derby</span><span style="color:#404040;">中，并且所有组件（如数据库、元数据服务）都运行在同一个进程</span><span style="color:#404040;">内。</span></span><span style="font-size:14px;color:rgb(64,64,64);">这种</span><span style="font-size:14px;color:rgb(64,64,64);">模式下，一段时间内只支持一个活动用户</span><span style="font-size:14px;color:rgb(64,64,64);">。</span><span style="font-size:14px;color:rgb(64,64,64);">但</span><span style="font-size:14px;color:rgb(64,64,64);">这种模式配置简单，所需机器</span><span style="font-size:14px;color:rgb(64,64,64);">较少。</span></div>
<div style="text-align:center;"><span style="font-size:14px;"><span style="color:rgb(64,64,64);"><img src="https://img-blog.csdn.net/20171220184139714" width="400" height="140" alt=""><br></span></span></div>
<div style="text-align:center;"><span style="font-size:14px;"><span style="color:rgb(64,64,64);"><br></span></span></div>
<div><span style="font-size:14px;"><span style="color:#666666;"><br></span></span></div>
<div><span style="font-size:14px;"></span>
<p><span style="color:#333333;"><u><span style="font-size:14px;">本地模式</span></u></span></p>
<p><span style="color:#333333;"></span></p>
<p><span style="color:#404040;">      此模式是</span><span style="color:#404040;">Hive</span><span style="color:#404040;">元数据服务依旧运行在</span><span style="color:#404040;">Hive</span><span style="color:#404040;">服务主进程中，但元数据存储在独立数据库中（可以是远程机器），当涉及元数据操作时，</span><span style="color:#404040;">Hive</span><span style="color:#404040;">服务中的元数据服务模块会通过</span><span style="color:#404040;">JDBC</span><span style="color:#404040;">和存储于</span><span style="color:#404040;">DB</span><span style="color:#404040;">里的元数据数据库交互。</span></p>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20171220184416331" width="340" height="100" alt=""></div>
<div style="text-align:center;"><br></div>
<p><span style="color:#333333;"></span></p>
<p><u><span style="font-size:14px;background-color:rgb(255,255,255);"><span style="color:#333333;">完全远程模式</span></span></u></p>
<p></p>
<p><span style="color:#404040;">     元数据服务以独立进程运行，并且元数据存储在一个独立的数据库里。</span></p>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20171220184423225" width="400" height="240" alt=""></div>
<br></div>
<br><p></p>
            </div>
                </div>
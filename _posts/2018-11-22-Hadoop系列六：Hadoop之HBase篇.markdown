---
layout:     post
title:      Hadoop系列六：Hadoop之HBase篇
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/fjssharpsword/article/details/50886733				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>HBase安装和运行</p>
<p>1）HBase理解</p>
<p>HBase是一个开源的、分布式的、多版本的、面向列的存储模型。可以直接使用本地文件系统也可使用Hadoop的HDFS文件存储系统。HBase存储的数据介于映射（key/value）和关系型数据之间。</p>
<p>Hadoop整体生态系统示意图：</p>
<p><img src="https://img-blog.csdn.net/20160314152419432?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>HBase位于结构化存储层，围绕HBase，各部件对HBase的支持情况：</p>
<p><strong>Hadoop部件　　　　　　　　　　　　作用</strong></p>
<p>HDFS　　　　　　　　　　　　　　高可靠的底层存储支持</p>
<p>MapReduce 　　　　　　　　　　  高性能的计算能力</p>
<p>Zookeeper 　　　　　　　　　　　稳定服务和failover机制</p>
<p>Pig&amp;Hive　　　　　　　　　　　　高层语言支持，便于数据统计</p>
<p>Sqoop　　　　　　　　　　　　　 提供RDBMS数据导入，便于传统数据库向HBase迁移</p>
<p>HBase 数据模型</p>
<p><img src="https://img-blog.csdn.net/20160314152436533?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p align="center"></p>
<p>组成部件说明：</p>
<p>Row Key：　　 　　Table主键行键 Table中记录按照Row Key排序</p>
<p>Timestamp：   　　每次对数据操作对应的时间戳，也即数据的version number</p>
<p>Column Family： 　列簇，一个table在水平方向有一个或者多个列簇，列簇可由任意多个Colum</p>
<p>组成，列簇支持动态扩展，无须预定义数量及类型，二进制存储，用户需自行进行类型转换</p>
<p>HBase 系统架构图</p>
<p><img src="https://img-blog.csdn.net/20160314152451237?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>组成部件说明</p>
<p>Client：</p>
<p>使用HBase RPC机制与HMaster和HRegionServer进行通信</p>
<p>Client与HMaster进行通信进行管理类操作</p>
<p>Client与HRegionServer进行数据读写类操作</p>
<p>Zookeeper：</p>
<p>Zookeeper Quorum存储-ROOT-表地址、HMaster地址</p>
<p>HRegionServer把自己以Ephedral方式注册到Zookeeper中，HMaster随时感知各HRegionServer的健康状况</p>
<p>Zookeeper避免HMaster单点问题</p>
<p>HMaster：</p>
<p>HMaster没有单点问题，HBase中可以启动多个HMaster，通过Zookeeper的Master Election机制保证总有一个Master在运行</p>
<p>主要负责Table和Region的管理工作：管理用户对表的增删改查操作；管理HRegionServer的负载均衡，调整Region分布； Region Split后，负责新Region的分布；在HRegionServer停机后，负责失效HRegionServer上Region迁移。</p>
<p>HRegionServer：HBase中最核心的模块，主要负责响应用户I/O请求，向HDFS文件系统中读写数据</p>
<p><img src="https://img-blog.csdn.net/20160314152512652?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>HRegionServer管理一些列HRegion对象；</p>
<p>每个HRegion对应Table中一个Region，HRegion由多个HStore组成；</p>
<p>每个HStore对应Table中一个Column Family的存储；</p>
<p>Column Family就是一个集中的存储单元，故将具有相同IO特性的Column放在一个ColumnFamily会更高效</p>
<p>HLog</p>
<p>引入HLog原因：在分布式系统环境中，无法避免系统出错或者宕机，一旦HRegionServer以外退出，MemStore中的内存数据就会丢失，引入HLog就是防止这种情况。工作机制：每个HRegionServer中都会有一个HLog对象，HLog是一个实现WriteAhead Log的类，每次用户操作写入Memstore的同时，也会写一份数据到HLog文件，HLog文件定期会滚动出新，并删除旧的文件(已持久化到StoreFile中的数据)。当HRegionServer意外终止后，HMaster会通过Zookeeper感知，HMaster首先处理遗留的HLog文件，将不同region的log数据拆分，分别放到相应region目录下，然后再将失效的region重新分配，领取到这些region的HRegionServer在Load
 Region的过程中，会发现有历史HLog需要处理，因此会Replay HLog中的数据到MemStore中，然后flush到StoreFiles，完成数据恢复。</p>
<p>2）安装和配置</p>
<p>Ubuntu操作系统，主机名hp，已安装文件存储系统HDFS、海量数据处理MapReduce和协同服务管理Zookeeper。</p>
<p>官网<a href="https://hbase.apache.org/" rel="nofollow">https://hbase.apache.org/</a> 下载hbase-1.2.0-bin.tar.gz并解压。HBase的安装有三种模式：单机模式、伪分布模式和完全分布式模式。</p>
<p>配置conf/hbase-env.sh文件</p>
<p>export JAVA_HOME=/home/hp/jdk/    #JDK安装路径</p>
<p>export HBASE_CLASSPATH=/home/hp/hadoop/conf    #Hadoop配置文件路径</p>
<p>export HBASE_MANAGES_ZK=true    #使用hbase 默认自带的 Zookeeper </p>
<p>配置conf/hbase-site.xml文件（在hbase目录下增加<span style="color:rgb(51,51,51);">tmp</span><span style="color:rgb(51,51,51);">和</span>zookeeper两个新目录）</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p>&lt;value&gt;hdfs://hp:9000/hbase&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</p>
<p>&lt;value&gt;true&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.tmp.dir&lt;/name&gt;</p>
<p>&lt;value&gt;/home/hp/hbase/tmp&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;</p>
<p>&lt;value&gt;hp&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;</p>
<p>&lt;value&gt;/home/hp/hbase/zookeeper&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;dfs.replication&lt;/name&gt;</p>
<p>&lt;value&gt;1&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.master.maxclockskew&lt;/name&gt;</p>
<p>&lt;value&gt;30000&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>       &lt;name&gt;hbase.master.info.port&lt;/name&gt;</p>
<p>        &lt;value&gt;60010&lt;/value&gt;</p>
<p> &lt;/property&gt;</p>
<p>其中，hbase.master是指定运行HMaster的服务器及端口号；hbase.master.maxclockskew是用来防止HBase节点之间时间不一致造成regionserver启动失败，默认值是30000；hbase.rootdir指定HBase的存储目录；hbase.cluster.distributed设置集群处于分布式模式；hbase.zookeeper.quorum设置Zookeeper节点的主机名，它的值个数必须是奇数；hbase.zookeeper.property.dataDir设置Zookeeper的目录，默认为/tmp，dfs.replication设置数据备份数，集群节点小于3时需要修改。</p>
<p>设置/etc/profile文件的HBase环境变量：</p>
<p>$sudo gedit/etc/profile</p>
<p>export HBASE_HOME=/home/hp/hbase</p>
<p>export PATH=$PATH:${HBASE_HOME}/bin</p>
<p>$source /etc/profile</p>
<p>3）启动运行</p>
<p>启动Hadoop：$sbin/start-dfs.sh和 ￥sbin/start-yarn.sh</p>
<p>启动HBase：$bin/start-hbase.sh</p>
<p>关闭HBase：$bin/stop-hbase.sh</p>
<p>启动后jps查看HQuromPeer、HRegionServer、HMaster三个进程。</p>
<p>查看hbase管理界面http://127.0.0.1: 60010</p>
<p>$hbase shell进入hbase操作</p>
<p>hbase&gt;status</p>
<p>开始hbase的DDL和DML操作。</p>
<p>hbase&gt;create ‘t1’,’id’,’addr’,’info’</p>
hbase&gt;list
            </div>
                </div>
---
layout:     post
title:      HBase入门笔记（四）--完全分布式HBase集群安装配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><a id="cb_post_title_url" href="http://www.cnblogs.com/ventlam/archive/2011/01/22/HBaseCluster.html" rel="nofollow">HBase入门笔记（四）--完全分布式HBase集群安装配置</a>
</h1>
<div id="cnblogs_post_body">
<p>        <span style="color:#333333;">HBase </span><span style="color:#333333;">是一个开源的非关系</span><span style="color:#333333;">(NoSQL)</span><span style="color:#333333;">的可伸缩性分布式数据库。它是面向列的，并适合于存储超大型松散数据。HBase适合于实时，随机对Big数据进行读写操作的业务环境。关于HBase的更多介绍请参见<a href="http://hbase.apache.org/" rel="nofollow">HBase项目官网</a>。</span></p>
<p><span style="color:#333333;">    本文环境与上一讲--<a href="http://www.cnblogs.com/ventlam/archive/2011/01/21/hadoopcluster.html" rel="nofollow">完全分布式Hadoop集群配置</a>一致。OS是Ubuntu Server 10.04，HBase版本是0.20.6。</span></p>
<p><span style="color:#333333;">         </span><span style="color:#333333;">HRegionServer&amp;HQuorumPeer:dm1,IP:192.168.0.17;</span></p>
<p>         HRegionServer&amp;HQuorumPeer:dm2,IP:192.168.0.18;</p>
<p>         HRegionServer&amp;HQuorumPeer:dm3,IP:192.168.0.9;</p>
<p>                  HMaster&amp;NameNode:dm4,IP:192.168.0.10;（SecondaryNameNode）</p>
<p>  虽然secondarynamenode和namenode放在同一台机器上比较不合理。但是考虑到这只是个实验的小集群（硬件环境不允许），再者有xenserver的时序快照的保障，就不将SecondaryNameNode部署在其他机器上了。</p>
<p> </p>
<p><span style="color:#333333;">     主要的还是配置工作，依然将HBase放在/home下，编辑/home/hbase/conf下的hbase-site.xml，hbase-default.xml,</span><span style="color:#333333;">hbase-env.sh
</span><span style="color:#333333;">这几个文件。具体步骤如下：</span></p>
<p><span style="color:#333333;">     一</span>.编辑<span style="color:#ff0000;"><strong>所有机器</strong></span>上的hbase-site文件，命令如下：</p>
<p><span style="color:#333333;"> </span></p>
<div>
<div>
<div id="highlighter_495686">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
</td>
<td>
<div>
<div><code>vi /home/hbase/conf/hbase-site.xml</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p><span style="color:#333333;">      编辑文件如下列代码所示。注意项有2：</span></p>
<p><span style="color:#333333;">    1.其中首先需要注意hdfs://dm4:9000/hbase这里，<strong><span style="color:#ff0000;">必须与你的Hadoop集群的</span></strong></span><span style="color:#333333;"><strong><span style="color:#ff0000;">core-site.xml文件配置保持完全一致</span></strong>才行，如果你Hadoop的hdfs使用了其它端口，请在这里也修改。再者就是Hbase该项并<strong><span style="color:#ff0000;">不识别机器IP，只能使用机器hostname</span></strong>才可行，即若使用dm4的IP(192.168.0.10)是会抛出java错误，至于具体的错误由于时间久远，我就懒得去翻查那大量的log了。</span></p>
<p><span style="color:#333333;">      2.</span><strong><span style="color:#ff0000;">hbase.zookeeper.quorum 的个数必须是奇数</span></strong>。</p>
<p><span style="color:#333333;"> </span></p>
<div>
<div>
<div id="highlighter_797242">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
<div>2</div>
<div>3</div>
<div>4</div>
<div>5</div>
<div>6</div>
<div>7</div>
<div>8</div>
<div>9</div>
<div>10</div>
<div>11</div>
<div>12</div>
<div>13</div>
<div>14</div>
<div>15</div>
<div>16</div>
<div>17</div>
<div>18</div>
</td>
<td>
<div>
<div><code>&lt;configuration&gt;</code></div>
<div><code>&lt;property&gt;</code></div>
<div><code> </code><code>&lt;name&gt;hbase.rootdir&lt;/name&gt;</code></div>
<div><code> </code><code>&lt;value&gt;hdfs:</code><code>//dm4:9000/hbase&lt;/value&gt;</code></div>
<div><code>&lt;/property&gt;</code></div>
<div><code>&lt;property&gt;</code></div>
<div><code> </code><code>&lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</code></div>
<div><code> </code><code>&lt;value&gt;</code><code>true</code><code>&lt;/value&gt;</code></div>
<div><code>&lt;/property&gt;</code></div>
<div><code>&lt;property&gt;</code></div>
<div><code>&lt;name&gt;hbase.master&lt;/name&gt;</code></div>
<div><code>&lt;value&gt;</code><code>192.168</code><code>.</code><code>0.10</code><code>:</code><code>60000</code><code>&lt;/value&gt;</code></div>
<div><code>&lt;/property&gt;</code></div>
<div><code>&lt;property&gt;</code></div>
<div><code> </code><code>&lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;</code></div>
<div><code> </code><code>&lt;value&gt;</code><code>192.168</code><code>.</code><code>0.9</code><code>,</code><code>192.168</code><code>.</code><code>0.17</code><code>,</code><code>192.168</code><code>.</code><code>0.18</code><code>&lt;/value&gt;</code></div>
<div><code>&lt;/property&gt;</code></div>
<div><code>&lt;/configuration&gt;</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p>   二.编辑<span style="color:#ff0000;">所有机器</span>的 <span style="color:#333333;">hbase-default.xml，命令如下：</span></p>
<p><span style="color:#333333;"> </span></p>
<div>
<div>
<div id="highlighter_341650">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
</td>
<td>
<div>
<div><code>vi /home/hbase/conf/hbase-</code><code>default</code><code>.xml</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p>    <span style="color:#333333;">只需修改前面</span>hbase.rootdir 与hbase.cluster.distributed 这两项。修改如下面代码所示：</p>
<p>HBase的数据重启就被擦掉，如果需要数据持久化的，就修改rootdir项，写定你的HDFS目录。</p>
<p>至于default内其它的项的含义与修改，再请参考官网。<span style="color:#333333;"> </span></p>
<p><span style="color:#333333;"> </span></p>
<div>
<div>
<div id="highlighter_990791">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
<div>2</div>
<div>3</div>
<div>4</div>
<div>5</div>
<div>6</div>
<div>7</div>
<div>8</div>
<div>9</div>
<div>10</div>
<div>11</div>
<div>12</div>
<div>13</div>
<div>14</div>
<div>15</div>
<div>16</div>
<div>17</div>
<div>18</div>
<div>19</div>
<div>20</div>
<div>21</div>
<div>22</div>
</td>
<td>
<div>
<div><code>&lt;configuration&gt;</code></div>
<div><code> </code><code>&lt;property&gt;</code></div>
<div><code> </code><code>&lt;name&gt;hbase.rootdir&lt;/name&gt;</code></div>
<div><code>&lt;value&gt;hdfs:</code><code>//dm4:9000/hbase_rootdir&lt;/value&gt;</code></div>
<div><code> </code><code>&lt;description&gt;The directory shared by region servers.</code></div>
<div><code> </code><code>Should be fully-qualified to include the filesystem to use.</code></div>
<div><code> </code><code>E.g: hdfs:</code><code>//NAMENODE_SERVER:PORT/HBASE_ROOTDIR</code></div>
<div><code> </code><code>&lt;/description&gt;</code></div>
<div><code> </code><code>&lt;/property&gt;</code></div>
<div><code> </code><code>&lt;property&gt;</code></div>
<div><code> </code><code>&lt;name&gt;hbase.master.port&lt;/name&gt;</code></div>
<div><code> </code><code>&lt;value&gt;</code><code>60000</code><code>&lt;/value&gt;</code></div>
<div><code> </code><code>&lt;description&gt;The port master should bind to.&lt;/description&gt;</code></div>
<div><code> </code><code>&lt;/property&gt;</code></div>
<div><code> </code><code>&lt;property&gt;</code></div>
<div><code> </code><code>&lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</code></div>
<div><code> </code><code>&lt;value&gt;</code><code>true</code><code>&lt;/value&gt;</code></div>
<div><code> </code><code>&lt;description&gt;The mode the cluster will be in. Possible values are</code></div>
<div><code> </code><code>false</code><code>: standalone and pseudo-distributed setups with managed Zookeeper</code></div>
<div><code> </code><code>true</code><code>: fully-distributed with unmanaged Zookeeper Quorum (see hbase-env.sh)</code></div>
<div><code> </code><code>&lt;/description&gt;</code></div>
<div><code> </code><code>&lt;/property&gt;</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p>   三. 编辑<strong><span style="color:#ff0000;">所有机器</span></strong>的<span style="color:#333333;">hbase-env.sh，命令如下：</span></p>
<p><span style="color:#333333;"> </span></p>
<div>
<div>
<div id="highlighter_557449">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
</td>
<td>
<div>
<div><code>vi /home/hbase/conf/hbase-env.sh</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p><span style="color:#333333;">     修改代码如下所示：</span></p>
<div>
<div>
<div id="highlighter_793775">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
<div>2</div>
<div>3</div>
<div>4</div>
<div>5</div>
<div>6</div>
</td>
<td>
<div>
<div><code>export HBASE_OPTS="$HBASE_OPTS -XX:+HeapDumpOnOutOfMemoryError -XX:+UseConcMarkS</code></div>
<div><code>weepGC -XX:+CMSIncrementalMode"</code></div>
<div><code> </code><code>export JAVA_HOME=/usr/lib/jvm/java-</code><code>6</code><code>-sun-</code><code>1.6</code><code>.</code><code>0.22</code></div>
<div><code>export HBASE_MANAGES_ZK=</code><code>true</code></div>
<div><code>export HBASE_HOME=/home/hbase</code></div>
<div><code>export HADOOP_HOME=/home/hadoop </code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p>     四.编辑所有机器的HBase的HMasters和HRegionServers。修改/home/hbase/conf 文件夹下的regionservers文</p>
<p>件。添加DataNode的IP即可。代码如下：</p>
<p> </p>
<div>
<div>
<div id="highlighter_580388">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
<div>2</div>
<div>3</div>
</td>
<td>
<div>
<div><code>192.168</code><code>.</code><code>0.9</code></div>
<div><code>192.168</code><code>.</code><code>0.17</code></div>
<div><code>192.168</code><code>.</code><code>0.18</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p>    行文至此，HBase集群的配置已然完成。以下便是启动和测试。</p>
<p>    五.启动.测试HBase数据库。</p>
<p>     在HMaster即Namenode （dm4）启动HBase数据库（Hadoop集群必须已经启动）。 启动命令：</p>
<p> </p>
<div>
<div>
<div id="highlighter_498204">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
</td>
<td>
<div>
<div><code>/home/hbase/bin/start-hbase.sh</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p>  Hbase启动如下图所示：</p>
<p><img src="http://pic002.cnblogs.com/images/2011/74048/2011012217223981.png" alt=""></p>
<p>    最好输入JPS命令测试一下你当前Hbase集群进程。如下图：</p>
<p><img src="http://pic002.cnblogs.com/images/2011/74048/2011012217295074.png" alt=""></p>
<p> </p>
<p> </p>
<p> </p>
<p>然后输入如下命令进入hbase的命令行管理界面：</p>
<p> </p>
<div>
<div>
<div id="highlighter_282313">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td>
<div>1</div>
</td>
<td>
<div>
<div><code>/home/hbase/bin/hbase shell</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
</div>
<p>   在hbase shell下 输入list，如下所示，列举你当前数据库的名称，如下图所示。如果你的Hbase没配置成功会抛出java错误。</p>
<p> </p>
<p><img src="http://pic002.cnblogs.com/images/2011/74048/2011012217360618.png" alt=""></p>
<p>   我们也可以通过WEB页面来管理查看HBase数据库。</p>
<p>  HMaster：<a href="http://192.168.0.10:60010/master.jsp" rel="nofollow">http://192.168.0.10:60010/master.jsp</a></p>
<p>  我的HBase数据库截图：</p>
<p><img src="http://pic002.cnblogs.com/images/2011/74048/2011012217420290.png" alt=""></p>
<p> 至于HBase的命令参见：<a href="http://wiki.apache.org/hadoop/FrontPage" rel="nofollow" style="color:#000000;">Hadoop Wiki</a></p>
<p> HBase数据库的开发应用，包括数据库读写和条件查询等，请参见我未来的文章……</p>
<p>  六.参考文献</p>
<p>   1.HBase: Bigtable-like structured storage for Hadoop HDFS</p>
<p>     <a href="http://wiki.apache.org/hadoop/Hbase" rel="nofollow">http://wiki.apache.org/hadoop/Hbase</a></p>
<p>   2.HBase Testing Tutorial</p>
<p>        <a href="http://wiki.apache.org/hadoop/Hbase/HowToTest" rel="nofollow">http://wiki.apache.org/hadoop/Hbase/HowToTest</a></p>
</div>
<div id="EntryTag">标签: <a href="http://www.cnblogs.com/ventlam/tag/HBase/" rel="nofollow">
HBase</a>, <a href="http://www.cnblogs.com/ventlam/tag/Hadoop/" rel="nofollow">Hadoop</a>,
<a href="http://www.cnblogs.com/ventlam/tag/%E5%AE%89%E8%A3%85%E9%85%8D%E7%BD%AE/" rel="nofollow">
安装配置</a>, <a href="http://www.cnblogs.com/ventlam/tag/NoSQL/" rel="nofollow">NoSQL</a>,
<a href="http://www.cnblogs.com/ventlam/tag/%E9%9D%9E%E5%85%B3%E7%B3%BB%E6%95%B0%E6%8D%AE%E5%BA%93/" rel="nofollow">
非关系数据库</a></div>
            </div>
                </div>
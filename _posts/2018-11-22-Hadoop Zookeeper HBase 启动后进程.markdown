---
layout:     post
title:      Hadoop Zookeeper HBase 启动后进程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1. 版本和配置</p>
<p>Hadoop 2.2.0， Zookeeper 3.4.6， HBase 0.98</p>
<p>6台机器，使用单独的Zookeeper，未使用HBase自带的。</p>
<p><br></p>
<p>2. 启动和关闭顺序</p>
<p>启动：Hadoop -&gt; Zookeeper -&gt; HBase</p>
<p>(1) hadoop on namenode</p>
<p>su hadoop</p>
<p>cd /usr/local/hadoop/sbin</p>
<p>./start-all.sh</p>
<p>(2) zookeepr on datanodes</p>
<p>su hadoop</p>
<p>cd /usr/local/zookeeper/bin</p>
<p>./zkServer.sh start</p>
<p>(3) HBase on namenode</p>
<p>su hadoop</p>
<p>cd /usr/local/hbase/bin</p>
<p>./start-hbase.sh</p>
<p><br></p>
<p>关闭：HBase -&gt;Zookeeper -&gt; Hadoop</p>
<p><br></p>
<p>3. 启动后进程（使用jps查看）</p>
<p>(1) Hadoop</p>
<p>namenode : NameNode, SecondaryNameNode, ResourceManager</p>
<p>datanode : DataNode, NodeManager</p>
<p>(2) Zookeeper（安装Zookeeper的机器要为奇数台，这里有5台）</p>
<p>QuorumPeerManager</p>
<p>(3) HBase</p>
<p>master : HMaster</p>
<p>slave : HRegionServer</p>
            </div>
                </div>
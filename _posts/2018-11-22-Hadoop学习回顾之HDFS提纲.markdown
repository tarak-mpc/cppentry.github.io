---
layout:     post
title:      Hadoop学习回顾之HDFS提纲
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 style="margin-left:0cm;">HDFS</h1>

<p style="margin-left:0cm;">HDFS的设计理念：</p>

<ol><li>超大存储</li>
	<li>廉价x86服务器</li>
	<li>流式数据访问（一次写入，多次读取，如果不借助HUE，无法直接对hdfs文件进行修改保存，需要利用 hdfs dfs –put命令）</li>
</ol><p style="margin-left:0cm;">HDFS缺点：</p>

<ol><li>实时访问弱，如要求秒级或者毫秒级数据访问，考虑使用HBase</li>
	<li>不适合存储大量小文件，HDFS启动时，Namenode将所有元数据读至内存，一条元数据150B，内存小会对Namenode有极大压力，解决方法，hdfs联邦</li>
</ol><p style="margin-left:0cm;">HDFS架构：</p>

<p style="margin-left:0cm;">Namenode，Secondary Namenode，DataNode，common storage</p>

<p style="margin-left:0cm;">NameNode和其冷备Secondary NameNode：</p>

<ol><li>SNN引导NN滚动更新Edit Log，将新的操作写入EditLog.new</li>
	<li>SNN将NN的时间最近的FSImage和时间最近的Editlog复制到SNN的检查点</li>
	<li>SNN将FSImage+Editlog合并成新的FSImage，将新的FSImage写入磁盘。</li>
	<li>SNN将新的FSImage返回给NN，NN接受FSImage直接加载和应用。NN将Editlog.new 更名为Edit Log</li>
</ol><p style="margin-left:0cm;">HDFS三副本，两冗余，副本存储策略选择<strong><span style="color:#ff0000;">，随机选择一个</span></strong><strong><span style="color:#ff0000;">DataNode-&gt;</span></strong><strong><span style="color:#ff0000;">另一机架节点</span><span style="color:#ff0000;">-&gt;</span></strong><strong><span style="color:#ff0000;">与副本</span><span style="color:#ff0000;">2</span></strong><strong><span style="color:#ff0000;">同机架的随机另一节点</span></strong>，策略选定后，会根据集群网络拓扑创建管道。</p>

<p style="margin-left:0cm;">HDFS读与写：</p>

<ol><li>读：HDFS客户端请求，NN返回数据块，主机名，HDFS Client再次向DataNode请求数据块</li>
	<li>写：客户端与三个要写入数据的datanode的写入顺序与写完响应，手拉手成环。</li>
	<li>HDFS以写方式打开text.txt</li>
	<li>请求送达至Namenode创建对应的元数据，但不与block关联</li>
	<li>数据写入流，数据自动拆分成数据包。打开复制管道，管道类似于进程锁复制，</li>
	<li>一批完成继续复制下一批，完成后结束。</li>
</ol><p style="margin-left:0cm;">NameNode HA：（热备）</p>

<p style="margin-left:0cm;">技术点及其实现:</p>

<ol><li>主备同步，备节点共享存储，QJM周期性获取编辑日志（Paxos算法），zookeeper的选主机制</li>
	<li>防止脑裂，防止突然都挂死或者突然都具备控制权。共享存储进行隔离，客户端隔离</li>
	<li>切换上层应用无感知。RPC封装，失败连接新的namenode，对应用户几乎无感知。</li>
</ol><p style="margin-left:0cm;">HDFS联邦：</p>

<ol><li>HDFS Federation+ViewFS</li>
	<li>HDFS Federation+ViewFS+HA</li>
	<li>HDFS元数据迁移，FastCopy，全局硬链接？</li>
</ol><p style="margin-left:0cm;">HDFS命令行接口：</p>

<p style="margin-left:0cm;">Hdfs dfs –ls /,hdfs dfs –lsr，hdfs dfs –expunge （清空回收站，rm的第一步是mv到trash目录）,hdfs dfs –cat ,hdfs dfs –touchz,hdfs dfs –mkdir ,hdfs dfs –put/copyFromLocal,hdfs dfs –get/copyToLocal</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"> </p>            </div>
                </div>
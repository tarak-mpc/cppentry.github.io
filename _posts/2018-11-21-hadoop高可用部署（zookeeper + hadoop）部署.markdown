---
layout:     post
title:      hadoop高可用部署（zookeeper + hadoop）部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/uevol14/article/details/80245689				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hadoop高可用部署zookeeper-hadoop部署">hadoop高可用部署（zookeeper + hadoop）部署</h1>



<h2 id="主要软件版本">主要软件版本</h2>

<table>
<thead>
<tr>
  <th>software</th>
  <th>version</th>
</tr>
</thead>
<tbody><tr>
  <td>java</td>
  <td>jdk-8u171-linux-x64.rpm</td>
</tr>
<tr>
  <td>hadoop</td>
  <td>hadoop-2.7.6.tar.gz</td>
</tr>
<tr>
  <td>zookeeper</td>
  <td>zookeeper-3.4.12.tar.gz</td>
</tr>
</tbody></table>




<h2 id="集群规划">集群规划</h2>

<table>
<thead>
<tr>
  <th>hostname</th>
  <th>os</th>
  <th>ip</th>
  <th>software</th>
  <th>deamon</th>
</tr>
</thead>
<tbody><tr>
  <td>master</td>
  <td>centos7</td>
  <td>192.168.3.100</td>
  <td>jdk, hadoop</td>
  <td>NameNode, ResourceManager, DFSZKFailoverController(zkfc)</td>
</tr>
<tr>
  <td>master1</td>
  <td>centos7</td>
  <td>192.168.3.101</td>
  <td>jdk, hadoop</td>
  <td>NameNode, ResourceManager, DFSZKFailoverController(zkfc)</td>
</tr>
<tr>
  <td>slave1</td>
  <td>centos7</td>
  <td>192.168.3.102</td>
  <td>jdk, hadoop, zookeeper</td>
  <td>DateNode, NodeManager, JournalNode, QuorumPeerMain</td>
</tr>
<tr>
  <td>slave2</td>
  <td>centos7</td>
  <td>192.168.3.103</td>
  <td>jdk, hadoop, zookeeper</td>
  <td>DateNode, NodeManager, JournalNode, QuorumPeerMain</td>
</tr>
<tr>
  <td>slave3</td>
  <td>centos7</td>
  <td>192.168.3.104</td>
  <td>jdk, hadoop, zookeeper</td>
  <td>DateNode, NodeManager, JournalNode, QuorumPeerMain</td>
</tr>
</tbody></table>




<h2 id="基础环境配置">基础环境配置</h2>

<p>基础环境配置参考<a href="https://blog.csdn.net/uevol14/article/details/80236326" rel="nofollow">基础环境配置章节</a></p>



<h2 id="zookeeper安装">zookeeper安装</h2>

<p>zookeeper安装参考<a href="https://blog.csdn.net/uevol14/article/details/80240042" rel="nofollow">zookeeper分布式安装</a></p>



<h2 id="hadoop集群配置">hadoop集群配置</h2>



<h4 id="1-修改core-sitexml">1. 修改core-site.xml</h4>

<pre><code>&lt;configuration&gt;
    &lt;!-- 指定hdfs的nameservice为ns1 --&gt;
    &lt;property&gt;
        &lt;name&gt;fs.defaultFS&lt;/name&gt;
        &lt;value&gt;hdfs://master&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 指定hadoop临时目录 --&gt;
    &lt;property&gt;  
        &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
        &lt;value&gt;/usr/local/hadoop/tmp&lt;/value&gt;
        &lt;description&gt;Abase for other temporary directories.&lt;/description&gt;
    &lt;/property&gt;             
    &lt;!-- 指定zookeeper地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt;
        &lt;value&gt;slave1:2181,slave1:2181,slave1:2181&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>

<p>此处 /usr/local/hadoop/tmp如果写成file:/usr/local/hadoop/tmp会报java.lang.IllegalArgumentException: URI has an authority component错误</p>

<h4 id="2-修改hdfs-sitexml">2. 修改hdfs-site.xml</h4>

<pre><code>&lt;configuration&gt;
    &lt;!--指定hdfs的nameservice为master，需要和core-site.xml中的保持一致 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.nameservices&lt;/name&gt;
        &lt;value&gt;master&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- ns1下面有两个NameNode，分别是nn1，nn2 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.namenodes.master&lt;/name&gt;
        &lt;value&gt;nn1,nn2&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- nn1的RPC通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.rpc-address.master.nn1&lt;/name&gt;
        &lt;value&gt;master:9000&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- nn1的http通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.http-address.master.nn1&lt;/name&gt;
        &lt;value&gt;master:50070&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- nn2的RPC通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.rpc-address.master.nn2&lt;/name&gt;
        &lt;value&gt;master1:9000&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- nn2的http通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.http-address.ns1.nn2&lt;/name&gt;
        &lt;value&gt;master1:50070&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 指定NameNode的元数据在JournalNode上的存放位置 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;
        &lt;value&gt;qjournal://slave1:8485;slave2:8485;slave3:8485/ns1&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 指定JournalNode在本地磁盘存放数据的位置 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.journalnode.edits.dir&lt;/name&gt;
        &lt;value&gt;/usr/local/hadoop/journaldata&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 开启NameNode失败自动切换 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt;
        &lt;value&gt;true&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 配置失败自动切换实现方式 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.client.failover.proxy.provider.master&lt;/name&gt;
        &lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 配置隔离机制方法，多个机制用换行分割，即每个机制暂用一行--&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;
        &lt;value&gt;
        sshfence
        shell(/bin/true)
        &lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 使用sshfence隔离机制时需要ssh免登陆 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.fencing.ssh.private-key-files&lt;/name&gt;
        &lt;value&gt;/root/.ssh/id_rsa&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 配置sshfence隔离机制超时时间 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.ha.fencing.ssh.connect-timeout&lt;/name&gt;
        &lt;value&gt;30000&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>



<h4 id="3-修改mapred-sitexml">3. 修改mapred-site.xml</h4>

<pre><code>&lt;configuration&gt;
    &lt;property&gt;
        &lt;name&gt;mapreduce.framework.name&lt;/name&gt;
        &lt;value&gt;yarn&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>



<h4 id="4-修改yarn-sitexml">4. 修改yarn-site.xml</h4>

<pre><code>&lt;configuration&gt;
    &lt;!-- 开启RM高可用 --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.ha.enabled&lt;/name&gt;
        &lt;value&gt;true&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 指定RM的cluster id --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.cluster-id&lt;/name&gt;
        &lt;value&gt;yrc&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 指定RM的名字 --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.ha.rm-ids&lt;/name&gt;
        &lt;value&gt;rm1,rm2&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 分别指定RM的地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.hostname.rm1&lt;/name&gt;
        &lt;value&gt;master&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.hostname.rm2&lt;/name&gt;
        &lt;value&gt;master1&lt;/value&gt;
    &lt;/property&gt;
    &lt;!-- 指定zk集群地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.zk-address&lt;/name&gt;
        &lt;value&gt;slave1:2181,slave1:2181,slave1:2181&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
        &lt;value&gt;mapreduce_shuffle&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>



<h4 id="4-修改slaves文件">4. 修改slaves文件</h4>

<pre><code>slave1
slave2
slave3
</code></pre>



<h4 id="5-修改hadoop-envsh配置javahome">5. 修改hadoop-env.sh配置JAVA_HOME</h4>

<pre><code>export JAVA_HOME=/usr/java/default
</code></pre>



<h2 id="复制文件至其他slave">复制文件至其他slave</h2>

<pre><code>scp -r /usr/local/hadoop slave1:/usr/local/
scp -r /usr/local/hadoop slave2:/usr/local/
scp -r /usr/local/hadoop slave3:/usr/local/
scp -r /usr/local/hadoop master1:/usr/local/
</code></pre>



<h2 id="启动zookeeper集群">启动zookeeper集群</h2>

<pre><code>在slave1, slave2, slave3上分别启动zookeeper
zkServer.sh start  # 启动
zkServer.sh status # 查看状态
zkServer.sh stop   # 停止
</code></pre>



<h2 id="启动journalnode">启动journalnode</h2>

<pre><code>在slave1, slave2, slave3上分别启动journalnode
hadoop-daemon.sh start journalnode
jps # 查看JournalNode进程
</code></pre>



<h2 id="在master节点格式化hdfs">在master节点格式化HDFS</h2>

<pre><code>hdfs namenode -format
</code></pre>

<p>格式化后会在根据core-site.xml中的hadoop.tmp.dir配置生成个文件，/usr/local/hadoop/tmp拷贝到master1的/usr/local/hadoop</p>

<pre><code>scp -r /usr/local/hadoop/tmp master1:/usr/local/hadoop
</code></pre>



<h2 id="在master节点格式化zkfc">在master节点格式化ZKFC</h2>

<pre><code>hdfs zkfc -formatZK
</code></pre>



<h2 id="在master节点启动hdfs">在master节点启动HDFS</h2>

<pre><code>start-dfs.sh
</code></pre>



<h2 id="启动yarn">启动YARN</h2>

<pre><code>start-yarn.sh
</code></pre>



<h2 id="web-interface">web Interface</h2>

<pre><code>NameNode            http://192.168.3.100:50070        Default HTTP port is 50070
ResourceManager     http://192.168.3.100:8088         Default HTTP port is 8088
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
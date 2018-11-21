---
layout:     post
title:      HBase集群安装-3 HBase安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="修改hbase-运行环境">修改hbase 运行环境</h1>

<pre><code>cd /usr/local/hadoop/hbase-1.1.1/bin/
vim hbase-env.sh
export JAVA_HOME=/usr/local/jdk1.7.0_79
</code></pre>

<h1 id="配置-hbase">配置 hbase</h1>

<p>cd /usr/local/hadoop/hbase-1.1.1/conf <br>
vim hbase-site.xml</p>

<pre><code>&lt;property&gt;  
  &lt;name&gt;hbase.rootdir&lt;/name&gt;
  &lt;value&gt;hdfs://master:9000/hbase&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
  &lt;value&gt;master,slave1,slave2&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
  &lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;
  &lt;value&gt;/usr/local/hadoop/zk/data&lt;/value&gt;
&lt;/property&gt;
</code></pre>

<h1 id="配置regionservers">配置regionservers</h1>

<pre><code>vim regionservers
</code></pre>

<p>修改内容</p>

<pre><code>slave1
slave2
</code></pre>

<h1 id="同步hbase到-salve1-和-slave2">同步hbase到 salve1 和 slave2</h1>

<pre><code>[hadoop@master conf]$ rsync -r /usr/local/hadoop/hbase-1.1.1 slave1:/usr/local/hadoop/
[hadoop@master conf]$ rsync -r /usr/local/hadoop/hbase-1.1.1 slave2:/usr/local/hadoop/
</code></pre>

<h1 id="启动hbase">启动hbase</h1>

<pre><code>cd /usr/local/hadoop/hbase-1.1.1/bin
./start-hbase.sh
</code></pre>

<h1 id="检查hbase进程jps">检查hbase进程，jps</h1>

<pre><code>[hadoop@master bin]$ jps
12016 Jps
10110 QuorumPeerMain
10412 HMaster
8959 SecondaryNameNode
8754 NameNode

[hadoop@slave1 bin]$ jps
6372 QuorumPeerMain
6581 HRegionServer
7731 Jps
5817 DataNode

[hadoop@slave2 data]$ jps
6396 HRegionServer
6187 QuorumPeerMain
7525 Jps
5787 DataNode
</code></pre>

<h1 id="test">test</h1>

<p>利用客户端连接到HBase</p>

<pre><code>cd /usr/local/hadoop/hbase-1.1.1/bin/
./hbase shell
</code></pre>

<p>创建测试表、查看、插入数据</p>

<pre><code>create 'test', 'cf1'
list
put 'test', 'row1', 'cf1:a', 'value1'     
put 'test', 'row1', 'cf1:b', 'value2'     
scan 'test'    
disable 'test'    
drop 'test'
</code></pre>

<h1 id="坑">坑：</h1>

<p>resionServer起不来，log报错</p>

<pre><code>2015-08-30 23:05:20,308 INFO  [regionserver/slave1/192.168.0.242:16020] regionserver.HRegionServer: STOPPED: Unhandled: org.apache.hadoop.hbase.ClockOutOfSyncException: Server slave1,16020,1440947118671 has been rejected; Reported time is too far out of sync with master.  Time difference of 581888ms &gt; max allowed of 30000ms
</code></pre>

<p>原因： RegionServer与Master的时间不一致造成的。</p>

<p>定时同步授时中心时钟 <br>
ntpdate asia.pool.ntp.org</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      利用HBase shell设计一个hbase表的初衷
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/helloxiaozhe/article/details/80497461				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>利用HBase shell设计一个hbase表的初衷</strong></p><p><strong><span style="color:#ff0000;">ps、hbase zkcli 和 hbase shell的使用</span></strong></p><p><strong>一、 hbase shell的使用</strong></p><p><strong>1.1 hbase 可使用的命令参数如下:</strong></p><pre><code class="language-plain">localhost:bin a6$ hbase
Usage: hbase [&lt;options&gt;] &lt;command&gt; [&lt;args&gt;]
Options:
  --config DIR    Configuration direction to use. Default: ./conf
  --hosts HOSTS   Override the list in 'regionservers' file
  --auth-as-server Authenticate to ZooKeeper using servers configuration

Commands:
Some commands take arguments. Pass no args or -h for usage.
  shell           Run the HBase shell
  hbck            Run the hbase 'fsck' tool
  snapshot        Create a new snapshot of a table
  snapshotinfo    Tool for dumping snapshot information
  wal             Write-ahead-log analyzer
  hfile           Store file analyzer
  zkcli           Run the ZooKeeper shell
  upgrade         Upgrade hbase
  master          Run an HBase HMaster node
  regionserver    Run an HBase HRegionServer node
  zookeeper       Run a Zookeeper server
  rest            Run an HBase REST server
  thrift          Run the HBase Thrift server
  thrift2         Run the HBase Thrift2 server
  clean           Run the HBase clean up script
  classpath       Dump hbase CLASSPATH
  mapredcp        Dump CLASSPATH entries required by mapreduce
  pe              Run PerformanceEvaluation
  ltt             Run LoadTestTool
  version         Print the version
  CLASSNAME       Run the class named CLASSNAME</code></pre><p><strong>1.2 创建表：</strong></p><pre><code class="language-plain">hbase(main):010:0&gt; create 'test1', 'lf', 'sf'
0 row(s) in 1.3020 seconds

=&gt; Hbase::Table - test1</code></pre><div><span style="color:rgb(0,0,0);font-family:Consolas, Inconsolata, Courier, monospace;font-size:14px;white-space:pre;background-color:rgb(246,248,250);">其中:<br></span></div><strong>lf: column family of LONG values (binary value)<br></strong><p><strong>sf: column family of STRING values</strong></p><p><strong>1.3 导入数据</strong></p><pre><code class="language-plain">put 'test1', 'user1|ts1', 'sf:c1', 'sku1'
put 'test1', 'user1|ts2', 'sf:c1', 'sku188'
put 'test1', 'user1|ts3', 'sf:s1', 'sku123'

put 'test1', 'user2|ts4', 'sf:c1', 'sku2'
put 'test1', 'user2|ts5', 'sf:c2', 'sku288'
put 'test1', 'user2|ts6', 'sf:s1', 'sku222'</code></pre><strong><span style="color:#ff0000;">一个用户（userX），在什么时间（tsX），作为rowkey；<br>对什么产品（value：skuXXX），做了什么操作作为列名，比如，<br>c1: click from homepage; c2: click from ad; <br></span></strong><p><strong><span style="color:#ff0000;">s1: search from homepage; b1: buy</span></strong></p><p></p><p><strong>1.4 查询案例</strong></p><p><strong>1.4.1 谁的值=sku188</strong></p><pre><code class="language-plain">hbase(main):002:0&gt; scan 'test1', FILTER=&gt;"ValueFilter(=,'binary:sku188')"
ROW                                COLUMN+CELL
 user1|ts2                         column=sf:c1, timestamp=1527579481653, value=sku188
1 row(s) in 0.4180 seconds</code></pre><p><strong>1.4.2 谁的值包含88</strong></p><pre><code class="language-plain">hbase(main):003:0&gt; scan 'test1', FILTER=&gt;"ValueFilter(=,'substring:88')"
ROW                                COLUMN+CELL
 user1|ts2                         column=sf:c1, timestamp=1527579481653, value=sku188
 user2|ts5                         column=sf:c2, timestamp=1527579481866, value=sku288
2 row(s) in 0.0390 seconds</code></pre><p><strong>1.4.3 通过广告点击进来的(column为c2)值包含88的用户</strong></p><pre><code class="language-plain">hbase(main):004:0&gt; scan 'test1', FILTER=&gt;"ColumnPrefixFilter('c2') AND ValueFilter(=,'substring:88')"
ROW                                COLUMN+CELL
 user2|ts5                         column=sf:c2, timestamp=1527579481866, value=sku288
1 row(s) in 0.0450 seconds</code></pre><p><strong>1.4.4 通过搜索进来的(column为s)值包含123或者222的用户</strong></p><pre><code class="language-plain">hbase(main):005:0&gt; scan 'test1', FILTER=&gt;"ColumnPrefixFilter('s') AND ( ValueFilter(=,'substring:123') OR ValueFilter(=,'substring:222') )"
ROW                                COLUMN+CELL
 user1|ts3                         column=sf:s1, timestamp=1527579481758, value=sku123
 user2|ts6                         column=sf:s1, timestamp=1527579482849, value=sku222
2 row(s) in 0.0210 seconds</code></pre><p><strong>1.4.5 rowkey为user1开头的</strong></p><pre><code class="language-plain">hbase(main):006:0&gt; scan 'test1', FILTER =&gt; "PrefixFilter ('user1')"
ROW                                COLUMN+CELL
 user1|ts1                         column=sf:c1, timestamp=1527579473815, value=sku1
 user1|ts2                         column=sf:c1, timestamp=1527579481653, value=sku188
 user1|ts3                         column=sf:s1, timestamp=1527579481758, value=sku123
3 row(s) in 0.0260 seconds</code></pre><p>2.</p><p><strong> FirstKeyOnlyFilter: 一个rowkey可以有多个version,同一个rowkey的同一个column也会有多个的值, 只拿出key中的第一个column的第一个version</strong></p><p><strong>2.1 KeyOnlyFilter: 只要key,不要value</strong></p><pre><code class="language-plain">hbase(main):007:0&gt; scan 'test1', FILTER=&gt;"FirstKeyOnlyFilter() AND ValueFilter(=,'binary:sku188') AND KeyOnlyFilter()"
ROW                                COLUMN+CELL
 user1|ts2                         column=sf:c1, timestamp=1527579481653, value=
1 row(s) in 0.0700 second</code></pre><p><strong>2.2 从user1|ts2开始,找到所有的rowkey以user1开头的</strong></p><pre><code class="language-plain">hbase(main):008:0&gt; scan 'test1', {STARTROW=&gt;'user1|ts2', FILTER =&gt; "PrefixFilter ('user1')"}
ROW                                COLUMN+CELL
 user1|ts2                         column=sf:c1, timestamp=1527579481653, value=sku188
 user1|ts3                         column=sf:s1, timestamp=1527579481758, value=sku123
2 row(s) in 0.0280 seconds</code></pre><p><strong>2.3 从user1|ts2开始,找到所有的到rowkey以user2开头</strong></p><pre><code class="language-plain">hbase(main):009:0&gt; scan 'test1', {STARTROW=&gt;'user1|ts2', STOPROW=&gt;'user2'}
ROW                                COLUMN+CELL
 user1|ts2                         column=sf:c1, timestamp=1527579481653, value=sku188
 user1|ts3                         column=sf:s1, timestamp=1527579481758, value=sku123
2 row(s) in 0.0220 seconds</code></pre><p><strong>2.4 查询rowkey里面包含ts3的</strong></p><pre><code class="language-plain">import org.apache.hadoop.hbase.filter.CompareFilter
import org.apache.hadoop.hbase.filter.SubstringComparator
import org.apache.hadoop.hbase.filter.RowFilter
scan 'test1', {FILTER =&gt; RowFilter.new(CompareFilter::CompareOp.valueOf('EQUAL'), SubstringComparator.new('ts3'))}
ROW                          COLUMN+CELL
 user1|ts3                   column=sf:s1, timestamp=1409122354954, value=sku123 </code></pre><p><strong>2.5 查询rowkey里面包含ts的</strong></p><pre><code class="language-plain">import org.apache.hadoop.hbase.filter.CompareFilter
import org.apache.hadoop.hbase.filter.SubstringComparator
import org.apache.hadoop.hbase.filter.RowFilter
scan 'test1', {FILTER =&gt; RowFilter.new(CompareFilter::CompareOp.valueOf('EQUAL'), SubstringComparator.new('ts'))}
 
ROW                          COLUMN+CELL
 user1|ts1                   column=sf:c1, timestamp=1409122354868, value=sku1
 user1|ts2                   column=sf:c1, timestamp=1409122354918, value=sku188
 user1|ts3                   column=sf:s1, timestamp=1409122354954, value=sku123
 user2|ts4                   column=sf:c1, timestamp=1409122354998, value=sku2
 user2|ts5                   column=sf:c2, timestamp=1409122355030, value=sku288
 user2|ts6                   column=sf:s1, timestamp=1409122355970, value=sku222</code></pre><p><strong>2.6 在查询中使用正则匹配</strong></p><pre><code class="language-plain">加入一条测试数据
put 'test1', 'user2|err', 'sf:s1', 'sku999'

查询rowkey里面以user开头的，新加入的测试数据并不符合正则表达式的规则，故查询不出来

import org.apache.hadoop.hbase.filter.RegexStringComparator
import org.apache.hadoop.hbase.filter.CompareFilter
import org.apache.hadoop.hbase.filter.SubstringComparator
import org.apache.hadoop.hbase.filter.RowFilter
scan 'test1', {FILTER =&gt; RowFilter.new(CompareFilter::CompareOp.valueOf('EQUAL'),RegexStringComparator.new('^user\d+\|ts\d+$'))}

ROW                          COLUMN+CELL
 user1|ts1                   column=sf:c1, timestamp=1409122354868, value=sku1
 user1|ts2                   column=sf:c1, timestamp=1409122354918, value=sku188
 user1|ts3                   column=sf:s1, timestamp=1409122354954, value=sku123
 user2|ts4                   column=sf:c1, timestamp=1409122354998, value=sku2
 user2|ts5                   column=sf:c2, timestamp=1409122355030, value=sku288
 user2|ts6                   column=sf:s1, timestamp=1409122355970, value=sku222

加入测试数据

put 'test1', 'user1|ts9', 'sf:b1', 'sku1'

b1开头的列中并且值为sku1的

scan 'test1', FILTER=&gt;"ColumnPrefixFilter('b1') AND ValueFilter(=,'binary:sku1')"
 
ROW                          COLUMN+CELL                                                                       
 user1|ts9                   column=sf:b1, timestamp=1409124908668, value=sku1

SingleColumnValueFilter的使用，b1开头的列中并且值为sku1的

import org.apache.hadoop.hbase.filter.CompareFilter
import org.apache.hadoop.hbase.filter.SingleColumnValueFilter
import org.apache.hadoop.hbase.filter.SubstringComparator
scan 'test1', {COLUMNS =&gt; 'sf:b1', FILTER =&gt; SingleColumnValueFilter.new(Bytes.toBytes('sf'), Bytes.toBytes('b1'), CompareFilter::CompareOp.valueOf('EQUAL'), Bytes.toBytes('sku1'))}
 
ROW                          COLUMN+CELL
 user1|ts9                   column=sf:b1, timestamp=1409124908668, value=sku1</code></pre><p><strong>二、hbase zkcli 的使用</strong></p><p><strong>1.1 启动并查看：</strong></p><pre><code class="language-plain">localhost:bin a6$ hbase zkcli
SLF4J: Class path contains multiple SLF4J bindings.
……
……
……
2018-05-29 16:14:21,221 INFO  [main] zookeeper.ZooKeeper: Client environment:java.compiler=&lt;NA&gt;
2018-05-29 16:14:21,221 INFO  [main] zookeeper.ZooKeeper: Client environment:os.name=Mac OS X
2018-05-29 16:14:21,221 INFO  [main] zookeeper.ZooKeeper: Client environment:os.arch=x86_64
2018-05-29 16:14:21,221 INFO  [main] zookeeper.ZooKeeper: Client environment:os.version=10.13.2
2018-05-29 16:14:21,221 INFO  [main] zookeeper.ZooKeeper: Client environment:user.name=a6
2018-05-29 16:14:21,221 INFO  [main] zookeeper.ZooKeeper: Client environment:user.home=/Users/a6
2018-05-29 16:14:21,221 INFO  [main] zookeeper.ZooKeeper: Client environment:user.dir=/Users/a6/Applications/hbase-1.2.6/bin
2018-05-29 16:14:21,223 INFO  [main] zookeeper.ZooKeeper: Initiating client connection, connectString=localhost:2182 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@588df31b
Welcome to ZooKeeper!
2018-05-29 16:14:21,257 INFO  [main-SendThread(localhost:2182)] zookeeper.ClientCnxn: Opening socket connection to server localhost/127.0.0.1:2182. Will not attempt to authenticate using SASL (unknown error)
JLine support is enabled
2018-05-29 16:14:21,283 INFO  [main-SendThread(localhost:2182)] zookeeper.ClientCnxn: Socket connection established to localhost/127.0.0.1:2182, initiating session
2018-05-29 16:14:21,298 INFO  [main-SendThread(localhost:2182)] zookeeper.ClientCnxn: Session establishment complete on server localhost/127.0.0.1:2182, sessionid = 0x163aac3f5960008, negotiated timeout = 30000

WATCHER::

WatchedEvent state:SyncConnected type:None path:null
[zk: localhost:2182(CONNECTED) 0] ls /
[zookeeper, hbase]</code></pre><p><strong>1.2 其他命令：</strong></p><pre><code class="language-plain">[zk: localhost:2182(CONNECTED) 1] ls /hbase
[replication, meta-region-server, rs, splitWAL, backup-masters, table-lock, flush-table-proc, region-in-transition, online-snapshot, master, running, recovering-regions, draining, namespace, hbaseid, table]
[zk: localhost:2182(CONNECTED) 2] ls /hbase/table
[hbase:meta, hbase:namespace, test, test10, test011, test001, new_emp, emp, test010, test1, test_tb_paysuccess, test8, test9, test6, test7, t1]
[zk: localhost:2182(CONNECTED) 3] ls /hbase/table/test1
[]
[zk: localhost:2182(CONNECTED) 4] get /hbase/table/test1
�master:61536�'�v*��PBUF
cZxid = 0x1925
ctime = Tue May 29 15:37:27 CST 2018
mZxid = 0x192b
mtime = Tue May 29 15:37:27 CST 2018
pZxid = 0x1925
cversion = 0
dataVersion = 2
aclVersion = 0
ephemeralOwner = 0x0
dataLength = 31
numChildren = 0
[zk: localhost:2182(CONNECTED) 5]</code></pre>参考: https://blog.csdn.net/vaq37942/article/details/54949428            </div>
                </div>
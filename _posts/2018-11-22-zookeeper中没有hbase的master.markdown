---
layout:     post
title:      zookeeper中没有hbase的master
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014034934/article/details/75114776				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">      hbase出了一个错，</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-java">hbase(main):001:0&gt; list
TABLE                                                                                                                                                                                          
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/home/hadoop/hbase-0.98.9-hadoop2/lib/phoenix-4.2.2-client.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/home/hadoop/hbase-0.98.9-hadoop2/lib/slf4j-log4j12-1.6.4.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/home/hadoop/hadoop-2.6.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
2017-07-14 15:06:48,032 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable

ERROR: Can't get master address from ZooKeeper; znode data == null

Here is some help for this command:
List all tables in hbase. Optional regular expression parameter could
be used to filter the output. Examples:

  hbase&gt; list
  hbase&gt; list 'abc.*'
  hbase&gt; list 'ns:abc.*'
  hbase&gt; list 'ns:.*'


</code></pre>
<p></p>
<p>      看出错信息是不能从zookeeper中得到master地址。用hbase zkcli命令查看</p>
<p></p>
<pre><code class="language-java">[zk: xxxx:2181(CONNECTED) 0] ls /
[hbase, zookeeper]
[zk: xxxx:2181(CONNECTED) 1] ls /hbase
[meta-region-server, backup-masters, table, draining, region-in-transition, table-lock, balancer, namespace, hbaseid, online-snapshot, replication, splitWAL, recovering-regions, rs]
</code></pre>      确实没有master文件夹。然后我就关闭hbase和hadoop，清空zookeeper的data目录，在重启就好了。
<p></p>
<p></p>
<pre><code class="language-java">[zk: xxxx:2181(CONNECTED) 0] ls /hbase
[meta-region-server, backup-masters, table, draining, region-in-transition, table-lock, running, master, namespace, hbaseid, online-snapshot, replication, splitWAL, recovering-regions, rs]
</code></pre>     出现了master文件夹。不知道是什么原因造成的，记录一下。<br><br><p></p>
<p><br><br></p>
<br><br>            </div>
                </div>
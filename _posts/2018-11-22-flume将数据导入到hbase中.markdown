---
layout:     post
title:      flume将数据导入到hbase中
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_37581329/article/details/79800407				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>接着前面的练习，flume已经安装完成并可以正常运行。</p><p>运行/opt/flume/bin/flume-ng version可以查看安装的版本。</p><p>1.把/opt/hbase/lib目录下的以下文件复制替换到/opt/flume/lib目录下面</p><pre><code class="language-html">[root@elephant lib]# cp protobuf-java-2.5.0.jar /opt/flume/lib
cp: overwrite `/opt/flume/lib/protobuf-java-2.5.0.jar'? yes
[root@elephant lib]# cp hbase-protocol-1.2.6.jar /opt/flume/lib
[root@elephant lib]# cp hbase-client-1.2.6.jar /opt/flume/lib
[root@elephant lib]# cp hbase-common-1.2.6.jar /opt/flume/lib
[root@elephant lib]# cp hbase-server-1.2.6.jar /opt/flume/lib
[root@elephant lib]# cp hbase-hadoop2-compat-1.2.6.jar /opt/flume/lib
[root@elephant lib]# cp hbase-hadoop-compat-1.2.6.jar /opt/flume/lib
[root@elephant lib]# cp htrace-core-3.1.0-incubating.jar /opt/flume/lib
</code></pre><pre><code class="language-html">2.启动hbase,创建表</code></pre><pre><code class="language-html">[root@elephant bin]# hbase shell
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/opt/hbase/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/opt/hadoop-2.7.5/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 1.2.6, rUnknown, Mon May 29 02:25:32 CDT 2017

hbase(main):001:0&gt; create 'test_idoall_org','uid','name'
0 row(s) in 3.1930 seconds

=&gt; Hbase::Table - test_idoall_org
hbase(main):002:0&gt; put 'test_idoall_org','10086','name:idoall','idoallvalue'
0 row(s) in 0.3450 seconds

hbase(main):003:0&gt; list
TABLE                                                                                                                                                        
test_idoall_org                                                                                                                                              
1 row(s) in 0.0230 seconds

</code></pre><p>3.在/opt/flume/conf/目录下，创建hbase_simple.conf文件，设置如下</p><pre><code class="language-html">[root@elephant conf]# cat hbase_simple.conf</code></pre><pre><code class="language-html">a1.sources = r1
a1.sinks = k1
a1.channels = c1
 
# Describe/configure the source
a1.sources.r1.type = exec
a1.sources.r1.command = tail -F /tmp/logs/data.txt
a1.sources.r1.port = 44444
a1.sources.r1.host = 192.168.205.136
a1.sources.r1.channels = c1
 
# Describe the sink
a1.sinks.k1.type = logger
a1.sinks.k1.type = hbase
a1.sinks.k1.table = test_idoall_org
a1.sinks.k1.columnFamily = name
a1.sinks.k1.column = idoall
a1.sinks.k1.serializer = org.apache.flume.sink.hbase.RegexHbaseEventSerializer
a1.sinks.k1.channel = memoryChannel
 
# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
 
# Bind the source and sink to the channel
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre><p>4.启动flume agent</p><pre><code class="language-html">[root@elephant conf]# bin/flume-ng agent --conf-file conf/hbase_simple.conf -n a1 -Dflume.root.logger=INFO,console
</code></pre><p>5.生成数据</p><pre><code class="language-html">[root@elephant kafka]# cd /tmp/logs
[root@elephant logs]# ls
test.log  test.log.COMPLETED
[root@elephant logs]# touch data.txt
[root@elephant logs]# ls
data.txt  test.log  test.log.COMPLETED
[root@elephant logs]# echo "hello idoall.org from flume"&gt;&gt;data.txt
[root@elephant logs]# echo "hello this is a flume to hbase test code"&gt;&gt;data.txt
</code></pre><p>6.查看hbase，可以看到数据已经插入hbase对应的表格</p><pre><code class="language-html">hbase(main):004:0&gt; scan 'test_idoall_org'
ROW                                      COLUMN+CELL                                                                                                         
 10086                                   column=name:idoall, timestamp=1522723531960, value=idoallvalue                                                      
 1522724221086-BaBHa2Hs6n-0              column=name:payload, timestamp=1522724224327, value=hello idoall.org from flume                                     
2 row(s) in 0.0830 seconds

hbase(main):005:0&gt; scan 'test_idoall_org'
ROW                                      COLUMN+CELL                                                                                                         
 10086                                   column=name:idoall, timestamp=1522723531960, value=idoallvalue                                                      
 1522724221086-BaBHa2Hs6n-0              column=name:payload, timestamp=1522724224327, value=hello idoall.org from flume                                     
2 row(s) in 0.0570 seconds
</code></pre><br><br>            </div>
                </div>
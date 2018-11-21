---
layout:     post
title:      flume的sink写入hive表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1><a href="https://www.cnblogs.com/zuizui1204/p/7800226.html" rel="nofollow" id="cb_post_title_url">flume的sink写入hive表</a></h1>

<p>flume的配置文件如下：</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
<code class="language-html hljs">a1.sources=r1
a1.channels=c1
a1.sinks=s1

a1.sources.r1.type=netcat
a1.sources.r1.bind=master
a1.sources.r1.port=44444

a1.sinks.s1.type=hive
a1.sinks.s1.hive.metastore=thrift://master:9083
a1.sinks.s1.hive.database=bd14
a1.sinks.s1.hive.table=flume_user
a1.sinks.s1.serializer=DELIMITED
a1.sinks.s1.serializer.delimiter="\t"
a1.sinks.s1.serializer.serdeSeparator='\t'
a1.sinks.s1.serializer.fieldnames=user_id,user_name,age

a1.channels.c1.type=memory
a1.channels.c1.capacity=1000
a1.channels.c1.transactionCapacity=100

a1.sources.r1.channels=c1
a1.sinks.s1.channel=c1</code></pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p> </p>

<p><br>
保存<br>
运行flume-ng agent -c conf -f netcat_to_hive_user.conf --name a1<br>
hive根目录下的/hcatalog/share/hcatalog文件夹中的如下三个文件夹添加到flume的lib目录下</p>

<pre>
<code class="language-html hljs">-rw-r--r--. 1 root root 260425 Jun 17  2016 hive-hcatalog-core-2.1.0.jar
-rw-r--r--. 1 root root  52709 Jun 17  2016 hive-hcatalog-pig-adapter-2.1.0.jar
-rw-r--r--. 1 root root  55274 Jun 17  2016 hive-hcatalog-server-extensions-2.1.0.jar
-rw-r--r--. 1 root root 121591 Jun 17  2016 hive-hcatalog-streaming-2.1.0.jar</code></pre>

<p>在hive中</p>

<p>创建一张hive表 表名是flume_user，字段是user_id,user_name,age</p>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<pre>
<code class="language-html hljs">create table flume_user(
user_id int
,user_name string
,age int
)
clustered by (user_id) into 2 buckets
stored as orc
tblproperties("transactional"='true')

set hive.support.concurrency=true
set hive.txn.manager=org.apache.hadoop.hive.ql.lockmgr.DbTxnManager;
select * from flume_user</code></pre>

<p><a><img alt="复制代码" class="has" src="https://common.cnblogs.com/images/copycode.gif"></a></p>

<p>打开一个连接 到服务器然后输入 ：<br>
telnet localhost 44444 <br>
然后输入 对应字符<br>
1 tab键 王五 tab键 15 tab键</p>            </div>
                </div>
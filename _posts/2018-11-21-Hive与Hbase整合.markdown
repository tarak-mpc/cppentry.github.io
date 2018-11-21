---
layout:     post
title:      Hive与Hbase整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wsscy2004/article/details/38420775				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 class="note-title" style="font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;line-height:1.1;color:rgb(22,32,41);font-size:30px;">
Hive与Hbase整合</h2>
<p style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;">
我们这边开始使用hbase做实时查询，但是分析的任务还是得交给hive,hive计算的结果导入到hbase.<br>
hive提供了几个jar包，帮助我们实现：</p>
<ul style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;"><li style="line-height:1.6;">创建与hbase共享的表，数据（数据和表两边都有)</li><li style="line-height:1.6;">映射来自hbase的表到hive</li><li style="line-height:1.6;">hive查询的结果直接导入hbase</li></ul><h3 style="font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;line-height:1.1;color:rgb(22,32,41);font-size:24px;">
启动hive</h3>
<p style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;">
启动命令如下，主要是指定jar包，以及hbase使用的zookeeper的地址</p>
<pre class="hljs-dark" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:14px;line-height:22.399999618530273px;color:rgb(51,51,51);border:0px;background-color:rgb(245,245,245);"><code class="hljs javascript" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">bin/hive --auxpath /opt/CDH/hive/lib/hive-hbase-handler-<span class="hljs-number" style="color:rgb(174,129,255);">0.10</span><span class="hljs-number" style="color:rgb(174,129,255);">.0</span>-cdh4<span class="hljs-number" style="color:rgb(174,129,255);">.3</span><span class="hljs-number" style="color:rgb(174,129,255);">.2</span>.jar,<span class="hljs-regexp" style="color:rgb(174,129,255);">/opt/</span>CDH/hive/lib/hbase-<span class="hljs-number" style="color:rgb(174,129,255);">0.94</span><span class="hljs-number" style="color:rgb(174,129,255);">.6</span>-cdh4<span class="hljs-number" style="color:rgb(174,129,255);">.3</span><span class="hljs-number" style="color:rgb(174,129,255);">.2</span>.jar,<span class="hljs-regexp" style="color:rgb(174,129,255);">/opt/</span>CDH/hive/lib/zookeeper-<span class="hljs-number" style="color:rgb(174,129,255);">3.4</span><span class="hljs-number" style="color:rgb(174,129,255);">.5</span>-cdh4<span class="hljs-number" style="color:rgb(174,129,255);">.3</span><span class="hljs-number" style="color:rgb(174,129,255);">.2</span>.jar,<span class="hljs-regexp" style="color:rgb(174,129,255);">/opt/</span>CDH/hive/lib/guava-<span class="hljs-number" style="color:rgb(174,129,255);">11.0</span><span class="hljs-number" style="color:rgb(174,129,255);">.2</span>.jar -hiveconf hbase.zookeeper.quorum=<span class="hljs-number" style="color:rgb(174,129,255);">192.168</span><span class="hljs-number" style="color:rgb(174,129,255);">.253</span><span class="hljs-number" style="color:rgb(174,129,255);">.119</span>,<span class="hljs-number" style="color:rgb(174,129,255);">192.168</span><span class="hljs-number" style="color:rgb(174,129,255);">.253</span><span class="hljs-number" style="color:rgb(174,129,255);">.130</span>
</code></pre>
<h3 style="font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;line-height:1.1;color:rgb(22,32,41);font-size:24px;">
测试表</h3>
<p style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;">
我们先在hive中创建测试表:</p>
<pre class="hljs-dark" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:14px;line-height:22.399999618530273px;color:rgb(51,51,51);border:0px;background-color:rgb(245,245,245);"><code class="hljs sql" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">//<span class="hljs-operator"><span class="hljs-keyword" style="color:rgb(249,38,114);">create</span> hive tmp <span class="hljs-keyword" style="color:rgb(249,38,114);">table</span>
<span class="hljs-keyword" style="color:rgb(249,38,114);">CREATE</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">TABLE</span> pokes (foo <span class="hljs-keyword" style="color:rgb(249,38,114);">INT</span>, bar <span class="hljs-keyword" style="color:rgb(249,38,114);">STRING</span>)
<span class="hljs-keyword" style="color:rgb(249,38,114);">ROW</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">FORMAT</span> DELIMITED
<span class="hljs-keyword" style="color:rgb(249,38,114);">FIELDS</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">TERMINATED</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">BY</span> <span class="hljs-string" style="color:rgb(230,219,116);">'\t'</span>;</span>

//test.txt数据格式：1  hello
//插入数据到hive表
<span class="hljs-operator"><span class="hljs-keyword" style="color:rgb(249,38,114);">LOAD</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">DATA</span>  INPATH <span class="hljs-string" style="color:rgb(230,219,116);">'/user/mapred/test.txt'</span> OVERWRITE <span class="hljs-keyword" style="color:rgb(249,38,114);">INTO</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">TABLE</span> pokes;</span>
</code></pre>
<h3 style="font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;line-height:1.1;color:rgb(22,32,41);font-size:24px;">
创建hive-hbase表</h3>
<p style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;">
在hive中创建表时，制定映射到对应的hbase表，默认两边的表名字一样。</p>
<pre class="hljs-dark" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:14px;line-height:22.399999618530273px;color:rgb(51,51,51);border:0px;background-color:rgb(245,245,245);"><code class="hljs sql" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">//<span class="hljs-operator"><span class="hljs-keyword" style="color:rgb(249,38,114);">create</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">table</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">share</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">with</span> hbase
hive&gt; <span class="hljs-keyword" style="color:rgb(249,38,114);">CREATE</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">TABLE</span> hbase_hive_table(<span class="hljs-keyword" style="color:rgb(249,38,114);">key</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">int</span>, <span class="hljs-keyword" style="color:rgb(249,38,114);">value</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">string</span>) STORED <span class="hljs-keyword" style="color:rgb(249,38,114);">BY</span> <span class="hljs-string" style="color:rgb(230,219,116);">'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">WITH</span> SERDEPROPERTIES (<span class="hljs-string" style="color:rgb(230,219,116);">"hbase.columns.mapping"</span> = <span class="hljs-string" style="color:rgb(230,219,116);">":key,cf1:val"</span>) ;</span>
</code></pre>
<p style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;">
切换到hbase shell,查看一下表是否存在：</p>
<pre class="hljs-dark" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:14px;line-height:22.399999618530273px;color:rgb(51,51,51);border:0px;background-color:rgb(245,245,245);"><code class="hljs coffeescript" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">hbase(main):<span class="hljs-number" style="color:rgb(174,129,255);">007</span>:<span class="hljs-number" style="color:rgb(174,129,255);">0</span>&gt; describe <span class="hljs-string" style="color:rgb(230,219,116);">'hbase_hive_table'</span>
DESCRIPTION                                                        ENABLED                            
 {NAME<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'hbase_hive_table'</span>, FAMILIES<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> [{NAME<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'cf1'</span>, DATA_BL <span class="hljs-literal" style="color:rgb(174,129,255);">true</span>                               
 OCK_ENCODING<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'NONE'</span>, BLOOMFILTER<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'NONE'</span>, REPLICATION_SCOPE<span class="hljs-function" style="color:rgb(249,38,114);">                                     
 =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'0'</span>, VERSIONS<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'3'</span>, COMPRESSION<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'NONE'</span>, MIN_VERSIONS<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'                                    
 0'</span>, TTL<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'2147483647'</span>, KEEP_DELETED_CELLS<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'false'</span>, BLOCKSIZE<span class="hljs-function" style="color:rgb(249,38,114);">                                    
  =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'65536'</span>, IN_MEMORY<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'false'</span>, ENCODE_ON_DISK<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'true'</span>, BLOC                                    
 KCACHE<span class="hljs-function" style="color:rgb(249,38,114);"> =&gt;</span> <span class="hljs-string" style="color:rgb(230,219,116);">'true'</span>}]}                                                                                  
<span class="hljs-number" style="color:rgb(174,129,255);">1</span> row(s) <span class="hljs-keyword" style="color:rgb(249,38,114);">in</span> <span class="hljs-number" style="color:rgb(174,129,255);">0.0800</span> seconds
</code></pre>
<h3 style="font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;line-height:1.1;color:rgb(22,32,41);font-size:24px;">
写数据测试</h3>
<pre class="hljs-dark" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:14px;line-height:22.399999618530273px;color:rgb(51,51,51);border:0px;background-color:rgb(245,245,245);"><code class="hljs sql" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">//<span class="hljs-operator"><span class="hljs-keyword" style="color:rgb(249,38,114);">insert</span> test
hive&gt; <span class="hljs-keyword" style="color:rgb(249,38,114);">INSERT</span> OVERWRITE <span class="hljs-keyword" style="color:rgb(249,38,114);">TABLE</span> hbase_hive_table <span class="hljs-keyword" style="color:rgb(249,38,114);">SELECT</span> * <span class="hljs-keyword" style="color:rgb(249,38,114);">FROM</span> pokes <span class="hljs-keyword" style="color:rgb(249,38,114);">WHERE</span> foo=<span class="hljs-number" style="color:rgb(174,129,255);">1</span>;</span>
Total MapReduce jobs = 1
Launching Job 1 out of 1
Number of reduce tasks is <span class="hljs-operator"><span class="hljs-keyword" style="color:rgb(249,38,114);">set</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">to</span> <span class="hljs-number" style="color:rgb(174,129,255);">0</span> since there<span class="hljs-string" style="color:rgb(230,219,116);">'s no reduce operator
Starting Job = job_201407241659_0007, Tracking URL = http://centos149:50030/jobdetails.jsp?jobid=job_201407241659_0007
Kill Command = /opt/CDH/hadoop/share/hadoop/mapreduce1/bin/hadoop job  -kill job_201407241659_0007
Hadoop job information for Stage-0: number of mappers: 1; number of reducers: 0
2014-08-07 16:15:14,505 Stage-0 map = 0%,  reduce = 0%
2014-08-07 16:15:20,010 Stage-0 map = 100%,  reduce = 0%, Cumulative CPU 2.46 sec
2014-08-07 16:15:21,087 Stage-0 map = 100%,  reduce = 0%, Cumulative CPU 2.46 sec
2014-08-07 16:15:22,190 Stage-0 map = 100%,  reduce = 0%, Cumulative CPU 2.46 sec
2014-08-07 16:15:23,200 Stage-0 map = 100%,  reduce = 100%, Cumulative CPU 2.46 sec
MapReduce Total cumulative CPU time: 2 seconds 460 msec
Ended Job = job_201407241659_0007
1 Rows loaded to hbase_hive_table
MapReduce Jobs Launched: 
Job 0: Map: 1   Cumulative CPU: 2.46 sec   HDFS Read: 196 HDFS Write: 0 SUCCESS
Total MapReduce CPU Time Spent: 2 seconds 460 msec
OK
Time taken: 34.594 seconds</span></span>
</code></pre>
<p style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;">
我们切换到hbase shell,查看一下表是否已经写入信息：</p>
<pre class="hljs-dark" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:14px;line-height:22.399999618530273px;color:rgb(51,51,51);border:0px;background-color:rgb(245,245,245);"><code class="hljs cs" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">hbase(main):<span class="hljs-number" style="color:rgb(174,129,255);">005</span>:<span class="hljs-number" style="color:rgb(174,129,255);">0</span>&gt; scan <span class="hljs-string" style="color:rgb(230,219,116);">'hbase_hive_table'</span>
ROW                        COLUMN+CELL                                                                
 <span class="hljs-number" style="color:rgb(174,129,255);">1</span>                         column=cf1:val, timestamp=<span class="hljs-number" style="color:rgb(174,129,255);">1407399353262</span>, <span class="hljs-keyword" style="color:rgb(249,38,114);">value</span>=hello
</code></pre>
<p style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;">
如果想要提高写入hbase表的速度，可以添加如下设置，关闭wal预写日志</p>
<pre class="hljs-dark" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:14px;line-height:22.399999618530273px;color:rgb(51,51,51);border:0px;background-color:rgb(245,245,245);"><code class="hljs sql" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(248,248,242);display:block;background:rgb(35,36,31);">//hbase write maybe slow, because of  wal, so <span class="hljs-operator"><span class="hljs-keyword" style="color:rgb(249,38,114);">set</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">to</span> <span class="hljs-keyword" style="color:rgb(249,38,114);">false</span>
<span class="hljs-keyword" style="color:rgb(249,38,114);">set</span> hive.hbase.wal.enabled=<span class="hljs-keyword" style="color:rgb(249,38,114);">false</span>;</span>
</code></pre>
<h3 style="font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;line-height:1.1;color:rgb(22,32,41);font-size:24px;">
Reference</h3>
<p style="line-height:22.399999618530273px;color:rgb(22,32,41);font-family:'Helvetica Neue', Arial, 'Hiragino Sans GB', STHeiti, 'Microsoft YaHei', 'WenQuanYi Micro Hei', SimSun, Song, sans-serif;font-size:14px;">
<a href="https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/HBaseIntegration</a></p>
            </div>
                </div>
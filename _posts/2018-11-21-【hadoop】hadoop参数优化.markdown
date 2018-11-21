---
layout:     post
title:      【hadoop】hadoop参数优化
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span class="topictitle" style="line-height:26px;font-size:13px;font-weight:bold;font-family:verdana, arial, helvetica, sans-serif;"><a href="http://sishuok.com/forum/blogPost/list/5938.html;jsessionid=E2DE3CE060B8E4883B0BC9059E93AC25" rel="nofollow" title="Hadoop实战-中高级部分 之 Hadoop作业调优参数调整及原理" class="blogtitle" style="color:rgb(0,94,167);text-decoration:none;font-size:18px !important;">Hadoop实战-中高级部分
 之 Hadoop作业调优参数调整及原理 </a></span><span style="font-size:14px;line-height:26px;font-family:verdana, arial, helvetica, sans-serif;background-color:rgb(250,250,250);"></span><br style="font-size:14px;line-height:26px;font-family:verdana, arial, helvetica, sans-serif;"><span style="font-size:14px;line-height:26px;font-family:verdana, arial, helvetica, sans-serif;"><span>浏览(2826)|<a href="http://sishuok.com/forum/blogPost/list/5938.html#comments" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">评论</a>(1)
   <span>交流分类：<a href="http://sishuok.com/forum/blog/index/0/26.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Java</a></span>|笔记分类: <a href="http://sishuok.com/forum/blogCategory/showByCategory.html?categories_id=134&amp;user_id=8636" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop实战-中……</a> </span></span><br style="font-size:14px;line-height:26px;font-family:verdana, arial, helvetica, sans-serif;"><br style="font-size:14px;line-height:26px;font-family:verdana, arial, helvetica, sans-serif;"><div class="div_content_text_all" style="font-size:14px;border:0px;overflow:hidden;background-color:rgb(250,250,250);line-height:1.5em;font-family:verdana, arial, helvetica, sans-serif;">
<div class="O" style="font-size:12px;border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop RestFul</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5936.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop HDFS原理1</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5937.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop HDFS原理2</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5938.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop作业调优参数调整及原理</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5939.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop HA</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5961.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop MapReduce高级编程</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop IO</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5965.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop MapReduce工作原理</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5966.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop 管理</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5969.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop 集群安装</a></div>
<div style="border-width:0px;overflow:hidden;"><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">Hadoop RPC</a></div>
<div style="border-width:0px;overflow:hidden;"> </div>
</div>
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-weight:bold;">第一部分：core-site.xml</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">•core-site.xml为Hadoop的核心属性文件，参数为Hadoop的核心功能，独立于HDFS与MapReduce。</div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">参数列表</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•fs.default.name</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 file:///</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•说明：设置Hadoop  namenode的hostname及port，预设是Standalone mode,如果是伪分布式文件系统要设置成</div>
<div class="O" style="border-width:0px;overflow:hidden;">
     hdfs://localhost:9000，如果使用集群模式则配置为 hdfs://hostname:9000</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•hadoop.tmp.dir</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值/tmp/hadoop-${user.name}</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•会在tmp下根据username生成不同的目录</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•fs.<a id="FALINK_2_0_1" class="FAtxtL" href="http://sishuok.com/forum/blogPost/list/5938.html#" rel="nofollow" style="color:rgb(243,91,0) !important;text-decoration:none;background-color:transparent !important;border-bottom-color:rgb(243,91,0) !important;border-bottom-style:solid !important;border-bottom-width:1px !important;display:inline !important;">checkpoint</a>.dir</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值${hadoop.tmp.dir}/dfs/namesecondary</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•Sencondary NameNode 镜像存储目录</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•fs.checkpoint.period</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值: 3600(秒)</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• 控制 secondary namenode 的 checkpoint 时间间隔。如果距离上次 checkpoint 的时间大于这个参数的设定，就会触发 checkpoint。secondary namenode 會把 namenode 的 fsimage 和 editlog 做 snapshot。如果存取 Hadoop 的次数频繁或为了減少重起 namenode 的 downtime，可以把這个值设小一点。</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•fs.checkpoint.size</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值67108864(byte)</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•如果 Hadoop 非常的忙碌，editlog 可能会在短时间內变的很大，fs.checkpoint.period 的设定不见得可以完全预测这个状况，所以保险的做法会多设定这个值，以保证当数据大到超过 fs.checkpoint.size 的值也会触发 checkpoint。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•io.file.buffer.size</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 4096</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•這是读写 sequence file 的 buffer size, 可减少 I/O 次数。在大型的 Hadoop cluster，建议可设定为 65536 到 131072。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•ipc.client.connection.maxidletime</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 10000(毫秒)</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•设定 Hadoop client 连接時最大的闲置，默认是是 10 秒。如果 Hadoop cluster 的网络联系不稳，可以把这个值设到 60000(60秒)</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•ipc.server.tcpnodelay</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 false</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•在 Hadoop server 是否启动 Nagle’s 算法。设 true 会 disable 这个演算法，关掉会减少延迟，但是会增加小数据包的传输。server site 不太需要这定这个值。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•hadoop.security.authorization</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 false</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•是不是要开启 账号验证机制，开启之后 Hadoop 在执行任何动作之前都会先确认是否有权限。详細的权限设定会放在 hadoop-policy.xml 裡。例如要让 fenriswolf 这个 account 及 mapreduce group 可以 <a id="FALINK_3_0_2" class="FAtxtL" href="http://sishuok.com/forum/blogPost/list/5938.html#" rel="nofollow" style="color:rgb(243,91,0) !important;text-decoration:none;background-color:transparent !important;border-bottom-color:rgb(243,91,0) !important;border-bottom-style:solid !important;border-bottom-width:1px !important;display:inline !important;">submit</a> M/R
 jobs，要设定security.job.submission.protocol.acl</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•hadoop.security.<a id="FALINK_1_0_0" class="FAtxtL" href="http://sishuok.com/forum/blogPost/list/5938.html#" rel="nofollow" style="color:rgb(243,91,0) !important;text-decoration:none;background-color:transparent !important;border-bottom-color:rgb(243,91,0) !important;border-bottom-style:solid !important;border-bottom-width:1px !important;display:inline !important;">authentication</a></div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 simple</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• simple 表示沒有 authentication，Hadoop 會用 system account 及 group 來控管q权限。另外可以指定為  <span>kerberos</span>，这部分相对比較复杂，要有一個 kerberos server 并产生 account keytab，在执行任何操作前 client 要先用 kinit 指令對 kerberos server 認證，之後的任何操作都是以 kerberos account
 來執行。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•fs.trash.interval</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 0（分）</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•清掉垃圾筒的时间。预设是不清, 所以在刪除文件时要自己执行</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•hadoop.native.lib</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 true</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
• 默认 Hadoop 会去找所有可用的 native libraries 并自动 load 进來使用，例如压缩类的 libraries 像 GZIP, LZO 等等。
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-weight:bold;">第二部分：hdfs-site.xml</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">参数列表</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•dfs.block.size</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值67108864（字节）</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认每個 block 是 64MB。如果確定存取的文件块都很大可以改為 134217728(128MB)。Client 也可自行决定要使用的 block size 而不需要更改整個 cluster 的设定。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•dfs.safemode.threshold.pct</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值  0.999f</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•  Hadoop 启动时会进入 safe mode，也就是安全模式，這时是不能写入数据的。只有当99.9% 的 blocks 达到最小的 dfs.replication.min 数量(默认是3)才会离开safe mode。在 dfs.replication.min 设的比较大或 data nodes 数量比较多时会等比较久。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•dfs.namenode.handler.count</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 10</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•设定 namenode server threads 的数量，这些 threads 會用 RPC 跟其他的 datanodes 沟通。当 datanodes 数量太多时会发現很容易出現 RPC timeout，解決方法是提升网络速度或提高这个值，但要注意的是 thread 数量多也表示 namenode 消耗的内存也随着增加</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•dfs.datanode.handler.count</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值  3</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•  指定 data node 上用的 thread 数量。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•dfs.datanode.max.xcievers</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 256</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•这个值是指定 datanode 可同時处理的最大文件数量、</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•dfs.datanode.du.reserved</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 0</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值表示 data nodes 会使用整个 磁盘，写满之后会导致无法再写入 M/R jobs。如果还有其他程式共用这些目录也会受到影响。建议保留至少 1073741824(1G) 的空间。
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;"> </div>
<div style="border-width:0px;overflow:hidden;"><span class="bold" style="font-weight:bold;">第三部分：mapred-site.xml</span></div>
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
<span class="bold" style="font-weight:bold;">参数列表</span></div>
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•io.sort.mb</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值100</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•缓存map中间结果的buffer大小(in MB)</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•io.sort.record.percent</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 0.05</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•io.sort.mb中用来保存map output记录边界的百分比，其他缓存用来保存数据</div>
<div class="O" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•io.sort.spill.percent</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值0.80</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•map开始做spill操作的阈值</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•io.sort.factor</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 10</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•做merge操作时同时操作的stream数上限。</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•min.num.spill.for.combine</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值3</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•combiner函数运行的最小spill数</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.compress.map.output</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 false</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•map中间结果是否采用压缩</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.map.output.compression.codec</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•org.apache.hadoop.io.compress.DefaultCodec</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•min.num.spill.for.combine</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值3</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•combiner函数运行的最小spill数</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.compress.map.output</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 false</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•map中间结果是否采用压缩</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.map.output.compression.codec</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•org.apache.hadoop.io.compress.DefaultCodec</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.reduce.parallel.copies</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值5</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•每个reduce并行下载map结果的最大线程数</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.reduce.copy.backoff</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 300</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•reduce下载线程最大等待时间（in sec）</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•io.sort.factor</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值10</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•org.apache.hadoop.io.compress.DefaultCodec</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
<div style="border-width:0px;overflow:hidden;">
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.job.shuffle.input.buffer.percent</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值0.7</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•用来缓存shuffle数据的reduce task heap百分比</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.job.shuffle.merge.percent</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值 0.66</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•缓存的内存中多少百分比后开始做merge操作</div>
<div class="O" style="border-width:0px;overflow:hidden;">
•mapred.job.reduce.input.buffer.percent</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•默认值0.0</div>
<div class="O1" style="border-width:0px;overflow:hidden;">
•sort完成后reduce计算阶段用来缓存数据的百分比</div>
<div class="O" style="border-width:0px;overflow:hidden;">
 转载请注明出处【  <a href="http://sishuok.com/forum/blogPost/list/0/5938.html" rel="nofollow" style="color:rgb(0,94,167);text-decoration:none;">http://sishuok.com/forum/blogPost/list/0/5938.html</a>】</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>
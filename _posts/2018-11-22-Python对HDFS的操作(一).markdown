---
layout:     post
title:      Python对HDFS的操作(一)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>HDFS</h1>
<h2>hdfs的定义:</h2>
<p>　　Hadoop的分布式文件系统（HDFS）被设计成适合运行通用硬件上的分布式文件系统，它和现有的分布式文件系统有很多的共同点。但同时，它和其它的分布式文件系统的区别也是很明显的，hdfs是一个高容错性的系统，适合部署在廉价的机器上。HDFS能提供高吞吐量的数据访问，非常适合大规模数据集上使用。HDFS放宽了一部分POSIX(https://baike.baidu.com/item/POSIX/3792413?fr=aladdin)约束,来实现流式读取文件系统数据的目的。HDFS在最开始是作为Apache Nutch搜索引擎项目的基础架构而开发的。HDFS现在是Apache Hadoop Core项目的一部分，这个项目的地址是：http://hadoop.apache.org/core/。</p>
<h2>安装：</h2>
<p>　　因为这里介绍的是python版本的使用，所以需要安装相应的包：</p>
<div class="cnblogs_code">
<pre>　pip install hdfs</pre>
</div>
<h2> 基础使用方法：</h2>
<h3>　　Client---创建集群链接:</h3>
<div class="cnblogs_code">
<pre><span style="color:#0000ff;">from</span> hdfs <span style="color:#0000ff;">import</span> *<span style="color:#000000;">
client</span>=Client(<span style="color:#800000;">"</span><span style="color:#800000;">https://hdfsip:50070</span><span style="color:#800000;">"</span>)</pre>
</div>
<p> </p>
<p>　　参数说明：</p>
<p>　　　　<em class="property">class  </em><code class="descclassname">hdfs.client.</code><code class="descname">Client</code><span class="sig-paren">(<em>url</em>, <em>root=None</em>, <em>proxy=None</em>, <em>timeout=None</em>, <em>session=None</em><span class="sig-paren">)</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　　　url: ip:端口</span></span></p>
<p><span class="sig-paren"><span class="sig-paren"> 　　　　　  root：指定hdfs根目录</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　　　proxy：制定登陆用户的身份</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　　　timout： 设置超时时间</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　　　session：<img src="https://img2018.cnblogs.com/blog/1333418/201810/1333418-20181024220929960-126998217.png" alt="">(官方解释，没弄明白具体意思，也暂时没有用到这个参数，等用到之后再进行补充)</span></span></p>
<h3><span class="sig-paren"><span class="sig-paren">　　dir---查看Client所有支持的方法：</span></span></h3>
<div class="cnblogs_code">
<pre>&gt;&gt;&gt; dir(client)<br>&gt;&gt;&gt;<code class="java plain">[</code><code class="java string">'__class__'</code><code class="java plain">, </code><code class="java string">'__delattr__'</code><code class="java plain">, </code><code class="java string">'__dict__'</code><code class="java plain">, </code><code class="java string">'__dir__'</code><code class="java plain">, </code><code class="java string">'__doc__'</code><code class="java plain">, </code><code class="java string">'__eq__'</code><code class="java plain">, </code><code class="java string">'__format__'</code><code class="java plain">, </code><code class="java string">'__ge__'</code><code class="java plain">, </code><code class="java string">'__getattribute__'</code><code class="java plain">, </code><code class="java string">'__gt__'</code><code class="java plain">,</code></pre>
<div class="line number3 index2 alt2"><code class="java string">　　'__hash__'</code><code class="java plain">, </code><code class="java string">'__init__'</code><code class="java plain">, </code><code class="java string">'__le__'</code><code class="java plain">, </code><code class="java string">'__lt__'</code><code class="java plain">, </code><code class="java string">'__module__'</code><code class="java plain">, </code><code class="java string">'__ne__'</code><code class="java plain">, </code><code class="java string">'__new__'</code><code class="java plain">, </code><code class="java string">'__reduce__'</code><code class="java plain">, </code><code class="java string">'__reduce_ex__'</code><code class="java plain">, </code><code class="java string">'__registry__'</code><code class="java plain">,</code></div>
<div class="line number4 index3 alt1"><code class="java spaces"> 　　</code><code class="java string">'__repr__'</code><code class="java plain">, </code><code class="java string">'__setattr__'</code><code class="java plain">, </code><code class="java string">'__sizeof__'</code><code class="java plain">, </code><code class="java string">'__str__'</code><code class="java plain">, </code><code class="java string">'__subclasshook__'</code><code class="java plain">, </code><code class="java string">'__weakref__'</code><code class="java plain">, </code><code class="java string">'_append'</code><code class="java plain">, </code><code class="java string">'_create'</code><code class="java plain">, </code><code class="java string">'_delete'</code><code class="java plain">,</code></div>
<div class="line number5 index4 alt2"><code class="java spaces"> 　　</code><code class="java string">'_get_content_summary'</code><code class="java plain">, </code><code class="java string">'_get_file_checksum'</code><code class="java plain">, </code><code class="java string">'_get_file_status'</code><code class="java plain">, </code><code class="java string">'_get_home_directory'</code><code class="java plain">, </code><code class="java string">'_list_status'</code><code class="java plain">, </code><code class="java string">'_mkdirs'</code><code class="java plain">, </code><code class="java string">'_open'</code><code class="java plain">,</code></div>
<div class="line number6 index5 alt1"><code class="java spaces"> 　　</code><code class="java string">'_proxy'</code><code class="java plain">, </code><code class="java string">'_rename'</code><code class="java plain">, </code><code class="java string">'_request'</code><code class="java plain">, </code><code class="java string">'_session'</code><code class="java plain">, </code><code class="java string">'_set_owner'</code><code class="java plain">, </code><code class="java string">'_set_permission'</code><code class="java plain">, </code><code class="java string">'_set_replication'</code><code class="java plain">, </code><code class="java string">'_set_times'</code><code class="java plain">, </code><code class="java string">'_timeout'</code><code class="java plain">,</code></div>
<div class="line number7 index6 alt2"><code class="java string">　　'checksum'</code><code class="java plain">, </code><code class="java string">'content'</code><code class="java plain">, </code><code class="java string">'delete'</code><code class="java plain">, </code><code class="java string">'download'</code><code class="java plain">, </code><code class="java string">'from_options'</code><code class="java plain">, </code><code class="java string">'list'</code><code class="java plain">, </code><code class="java string">'makedirs'</code><code class="java plain">, </code><code class="java string">'parts'</code><code class="java plain">, </code><code class="java string">'read'</code><code class="java plain">, </code><code class="java string">'rename'</code><code class="java plain">, </code><code class="java string">'resolve'</code><code class="java plain">, </code><code class="java string">'root'</code><code class="java plain">,</code></div>
<div class="line number8 index7 alt1"><code class="java spaces"> 　　</code><code class="java string">'set_owner'</code><code class="java plain">, </code><code class="java string">'set_permission'</code><code class="java plain">, </code><code class="java string">'set_replication'</code><code class="java plain">, </code><code class="java string">'set_times'</code><code class="java plain">, </code><code class="java string">'status'</code><code class="java plain">, </code><code class="java string">'upload'</code><code class="java plain">,</code></div>
<div class="line number9 index8 alt2"><code class="java spaces"> 　　</code><code class="java string">'url'</code><code class="java plain">, </code><code class="java string">'walk'</code><code class="java plain">, </code><code class="java string">'write'</code><code class="java plain">]</code></div>
</div>
<h3>　　status---获取指定路径的具体信息：</h3>
<div class="cnblogs_code">
<pre>&gt;&gt;&gt; client.status(<span style="color:#800000;">"</span><span style="color:#800000;">/</span><span style="color:#800000;">"</span><span style="color:#000000;">)

{</span><span style="color:#800000;">'</span><span style="color:#800000;">accessTime</span><span style="color:#800000;">'</span>: 0, <span style="color:#800000;">'</span><span style="color:#800000;">pathSuffix</span><span style="color:#800000;">'</span>: <span style="color:#800000;">''</span>, <span style="color:#800000;">'</span><span style="color:#800000;">group</span><span style="color:#800000;">'</span>: <span style="color:#800000;">'</span><span style="color:#800000;">supergroup</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">type</span><span style="color:#800000;">'</span>: <span style="color:#800000;">'</span><span style="color:#800000;">DIRECTORY</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">owner</span><span style="color:#800000;">'</span>: <span style="color:#800000;">'</span><span style="color:#800000;">root</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">childrenNum</span><span style="color:#800000;">'</span>: 4, <span style="color:#800000;">'</span><span style="color:#800000;">blockSize</span><span style="color:#800000;">'</span><span style="color:#000000;">: 0,

 </span><span style="color:#800000;">'</span><span style="color:#800000;">fileId</span><span style="color:#800000;">'</span>: 16385, <span style="color:#800000;">'</span><span style="color:#800000;">length</span><span style="color:#800000;">'</span>: 0, <span style="color:#800000;">'</span><span style="color:#800000;">replication</span><span style="color:#800000;">'</span>: 0, <span style="color:#800000;">'</span><span style="color:#800000;">storagePolicy</span><span style="color:#800000;">'</span>: 0, <span style="color:#800000;">'</span><span style="color:#800000;">modificationTime</span><span style="color:#800000;">'</span>: 1473023149031, <span style="color:#800000;">'</span><span style="color:#800000;">permission</span><span style="color:#800000;">'</span>: <span style="color:#800000;">'</span><span style="color:#800000;">777</span><span style="color:#800000;">'</span>}</pre>
</div>
<p> </p>
<p>　　参数说明：</p>
<p>　　　　status(hdfs_path,strict=True)</p>
<p><span class="sig-paren"><span class="sig-paren">　　　　hdfs_path:就是hdfs的路径</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　strict：当设置为True时，hdfs的路径不存在时，返回异常信息</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　　　　当设置为False时，hdfs的路径不存在时，返回None</span></span></p>
<h3><span class="sig-paren"><span class="sig-paren">　　list---获取指定路径的子目录信息：</span></span></h3>
<div class="cnblogs_code">
<pre>client.list(<span style="color:#800000;">"</span><span style="color:#800000;">/</span><span style="color:#800000;">"</span><span style="color:#000000;">)
["test01","test02","test03"</span>]</pre>
</div>
<p> </p>
<p><span class="sig-paren"><span class="sig-paren">       参数说明:</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　list(hdfs_path,status=False)</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　hdfs_path: hdfs的路径</span></span></p>
<p><span class="sig-paren"><span class="sig-paren">　　　　status：为True时，同时反hi子目录的状态信息，默认为False</span></span></p>
<h3><span class="sig-paren"><span class="sig-paren">　　makedirs--创建目录</span></span></h3>
<div class="cnblogs_code">
<pre>client.makedirs(<span style="color:#800000;">"</span><span style="color:#800000;">test06</span><span style="color:#800000;">"</span>)</pre>
</div>
<p>　　参数说明：</p>
<p>　　　　makedirs(hdfs_path,permission=None)</p>
<p>　　　　hdfs_path: 要创建目录</p>
<p>　　　　permission：对创建的文件夹设置权限</p>
<h3>　　rename—重命名</h3>
<div>
<div id="highlighter_449617" class="syntaxhighlighter java">
<div class="cnblogs_code">
<pre>&gt;&gt;&gt; client.rename(<span style="color:#800000;">"</span><span style="color:#800000;">/test</span><span style="color:#800000;">"</span>,<span style="color:#800000;">"</span><span style="color:#800000;">/new_name</span><span style="color:#800000;">"</span><span style="color:#000000;">)
</span>&gt;&gt;&gt; client.list(<span style="color:#800000;">"</span><span style="color:#800000;">/</span><span style="color:#800000;">"</span><span style="color:#000000;">)
[</span><span style="color:#800000;">'</span><span style="color:#800000;">file</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">gyt</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">hbase</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">new_name</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">tmp</span><span style="color:#800000;">'</span>]</pre>
</div>
<p> 　　参数说明：</p>
<p>　　　　rename(hdfs_path, local_path）</p>
<h3>　delete—删除</h3>
<div class="cnblogs_code">
<pre>&gt;&gt;&gt; client.list(<span style="color:#800000;">"</span><span style="color:#800000;">/</span><span style="color:#800000;">"</span><span style="color:#000000;">)
[</span><span style="color:#800000;">'</span><span style="color:#800000;">file</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">gyt</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">hbase</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">new_name</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">tmp</span><span style="color:#800000;">'</span><span style="color:#000000;">]
</span>&gt;&gt;&gt; client.delete(<span style="color:#800000;">"</span><span style="color:#800000;">/new_name</span><span style="color:#800000;">"</span><span style="color:#000000;">)
True
</span>&gt;&gt;&gt; client.list(<span style="color:#800000;">"</span><span style="color:#800000;">/</span><span style="color:#800000;">"</span><span style="color:#000000;">)
[</span><span style="color:#800000;">'</span><span style="color:#800000;">file</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">gyt</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">hbase</span><span style="color:#800000;">'</span>, <span style="color:#800000;">'</span><span style="color:#800000;">tmp</span><span style="color:#800000;">'</span>]</pre>
</div>
<p> </p>
</div>
</div>
<div>
<div id="highlighter_242521" class="syntaxhighlighter java">
<div class="toolbar">　　参数说明：</div>
<div class="toolbar">　　　　delete(hdfs_path,recursive=False)</div>
</div>
</div>
<p>　　　　recursive：删除文件和其子目录，设置为False如果不存在，则会抛出异常，默认为False</p>
<h3>　upload——上传数据</h3>
<div class="cnblogs_code">
<pre>&gt;&gt;&gt; client.list(<span style="color:#800000;">"</span><span style="color:#800000;">/</span><span style="color:#800000;">"</span><span style="color:#000000;">)
[u</span><span style="color:#800000;">'</span><span style="color:#800000;">hbase</span><span style="color:#800000;">'</span>, u<span style="color:#800000;">'</span><span style="color:#800000;">test</span><span style="color:#800000;">'</span><span style="color:#000000;">]
</span>&gt;&gt;&gt; client.upload(<span style="color:#800000;">"</span><span style="color:#800000;">/test</span><span style="color:#800000;">"</span>,<span style="color:#800000;">"</span><span style="color:#800000;">/opt/bigdata/hadoop/NOTICE.txt</span><span style="color:#800000;">"</span><span style="color:#000000;">)
</span><span style="color:#800000;">'</span><span style="color:#800000;">/test/NOTICE.txt</span><span style="color:#800000;">'</span>
&gt;&gt;&gt; client.list(<span style="color:#800000;">"</span><span style="color:#800000;">/</span><span style="color:#800000;">"</span><span style="color:#000000;">)
[u</span><span style="color:#800000;">'</span><span style="color:#800000;">hbase</span><span style="color:#800000;">'</span>, u<span style="color:#800000;">'</span><span style="color:#800000;">test</span><span style="color:#800000;">'</span><span style="color:#000000;">]
</span>&gt;&gt;&gt; client.list(<span style="color:#800000;">"</span><span style="color:#800000;">/test</span><span style="color:#800000;">"</span><span style="color:#000000;">)
[u</span><span style="color:#800000;">'</span><span style="color:#800000;">NOTICE.txt</span><span style="color:#800000;">'</span>]</pre>
</div>
<p> </p>
<p>　　参数说明：</p>
<p>　　　　upload(hdfs_path,local_path,overwrite=False,n_threads=1,temp_dir=None,</p>
<p>　　　　chunk_size=65536,progress=None,cleanup=True,**kwargs)</p>
<p>　　　　overwrite：是否是覆盖性上传文件</p>
<p>　　　　n_threads：启动的线程数目</p>
<p>　　　　temp_dir：当overwrite=true时，远程文件一旦存在，则会在上传完之后进行交换</p>
<p>　　　　chunk_size：文件上传的大小区间</p>
<p>　　　　progress：回调函数来跟踪进度，为每一chunk_size字节。它将传递两个参数，文件上传的路径和传输的字节数。一旦完成，-1将作为第二个参数</p>
<p>　　　　cleanup：如果在上传任何文件时发生错误，则删除该文件</p>
<h3>　　download——下载</h3>
<div class="cnblogs_code">
<pre>&gt;&gt;&gt; client.download(<span style="color:#800000;">"</span><span style="color:#800000;">/test/NOTICE.txt</span><span style="color:#800000;">"</span>,<span style="color:#800000;">"</span><span style="color:#800000;">/home</span><span style="color:#800000;">"</span><span style="color:#000000;">)
</span><span style="color:#800000;">'</span><span style="color:#800000;">/home/NOTICE.txt</span><span style="color:#800000;">'</span>
&gt;&gt;&gt; <span style="color:#0000ff;">import</span><span style="color:#000000;"> os
</span>&gt;&gt;&gt; os.system(<span style="color:#800000;">"</span><span style="color:#800000;">ls /home</span><span style="color:#800000;">"</span><span style="color:#000000;">)
lost</span>+<span style="color:#000000;">found  NOTICE.txt  thinkgamer
0</span></pre>
</div>
<p>　　参数说明：</p>
<p>　　　　download(hdfs_path,local_path,overwrite=False,n_threads=1,temp_dir=None,**kwargs)</p>
<p>　　　　和上传的参数一样</p>
<h3>　　read——读取文件</h3>
<div class="cnblogs_code">
<pre>&gt;&gt;&gt; with client.read(<span style="color:#800000;">"</span><span style="color:#800000;">/test/NOTICE.txt</span><span style="color:#800000;">"</span><span style="color:#000000;">) as reader:
...     </span><span style="color:#0000ff;">print</span><span style="color:#000000;"> reader.read()
...
This product includes software developed by The Apache Software
Foundation (https:</span>//www.apache.org/<span style="color:#000000;">).
 
</span>&gt;&gt;&gt;</pre>
</div>
<p>参数说明：</p>
<p>　　read(*args,**kwds)</p>
<p>　　hdfs_path：hdfs路径</p>
<p>　　offset：设置开始的字节位置</p>
<p>　　length：读取的长度（字节为单位）</p>
<p>　　buffer_size：用于传输数据的字节的缓冲区的大小。默认值设置在HDFS配置。</p>
<p>　　encoding：制定编码</p>
<p>　　chunk_size：如果设置为正数，上下文管理器将返回一个发生器产生的每一chunk_size字节而不是一个类似文件的对象</p>
<p>　　delimiter：如果设置，上下文管理器将返回一个发生器产生每次遇到分隔符。此参数要求指定的编码。</p>
<p>　　progress：回调函数来跟踪进度，为每一chunk_size字节（不可用，如果块大小不是指定）。它将传递两个参数，文件上传的路径和传输的字节数。称为一次与- 1作为第二个参数。</p>
<div>
<div id="highlighter_457657" class="syntaxhighlighter java">
<div class="toolbar">我在测试的时候遇见这个错误：
<div class="cnblogs_code">
<pre>hdfs.util.HdfsError: Permission denied: user=dr.who, access=WRITE, inode=<span style="color:#800000;">"</span><span style="color:#800000;">/test</span><span style="color:#800000;">"</span>:root:supergroup:drwxr-xr-x</pre>
</div>
<p> 找到的解决办法是：</p>
</div>
<div class="toolbar">　　解决办法是：在配置文件hdfs-site.xml中加入</div>
<div class="cnblogs_code">
<pre>&lt;property&gt;
  &lt;name&gt;dfs.permissions&lt;/name&gt;
  &lt;value&gt;false&lt;/value&gt;
&lt;/property&gt;</pre>
</div>
<p> 重启集群</p>
</div>
</div>
<h1><span style="font-family:'幼圆';">有点长了，webhdfs在下一篇中写</span></h1>
<p> </p>            </div>
                </div>
---
layout:     post
title:      hadoop18--JavaAPI, 读写流程, 物理模型
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请标明出处。https://blog.csdn.net/kXYOnA63Ag9zqtXx0/article/details/82954503					https://blog.csdn.net/forever428/article/details/84111881				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="hbase_0"></a>hbase的物理模型</h3>
<p>hbase的物理模型, 就是说在hbase中数据是如何存储的, 以及存储的位置和原理</p>
<p>在hbase中最小的存储单元是cell(单元格): rowkey + 列簇 + 时间戳 + value, 可以唯一确定一个单元格的值</p>
<p>在hbase物理模型中, rowkey所在行的方向上可以划分多个region, 并且是按照字典排序的</p>
<p>region可以理解为分区的概念, 一个region 里边会存在一个列簇+value</p>
<h3><a id="hbase_10"></a>hbase中存在系统管理表</h3>
<ol>
<li>
<p><mark>hbase: namespace</mark>: 记录了当前系统中所有的namespace的信息</p>
<p>查看系统中所有的namespace</p>
</li>
</ol>
<pre><code>hbase(main):017:0&gt; scan 'hbase:namespace'
ROW                                        COLUMN+CELL
 default                                   column=info:d, timestamp=1542080423695, value=\x0A\x07default
 hbase                                     column=info:d, timestamp=1542080424908, value=\x0A\x05hbase
2 row(s) in 0.0150 seconds
</code></pre>
<ol start="2">
<li><mark>hbase:meta</mark>: 记录了所有表的region信息</li>
</ol>
<pre><code>hbase(main):020:0&gt; scan 'hbase:meta'
ROW                                        COLUMN+CELL
 hadoop,,1542146532516.edec440c50dc8b6a571 column=info:regioninfo, timestamp=1542146534724, value={ENCODED =&gt; edec440c50dc8b6a571ddbe678ce0c9c, NAME =&gt; 'hadoop,,1542
 ddbe678ce0c9c.                            146532516.edec440c50dc8b6a571ddbe678ce0c9c.', STARTKEY =&gt; '', ENDKEY =&gt; ''}
 hadoop,,1542146532516.edec440c50dc8b6a571 column=info:seqnumDuringOpen, timestamp=1542146534724, value=\x00\x00\x00\x00\x00\x00\x00\x02
 ddbe678ce0c9c.
 hadoop,,1542146532516.edec440c50dc8b6a571 column=info:server, timestamp=1542146534724, value=hadoop02:16020
 ddbe678ce0c9c.
 hadoop,,1542146532516.edec440c50dc8b6a571 column=info:serverstartcode, timestamp=1542146534724, value=1542142878250
 ddbe678ce0c9c.
 hbase:namespace,,1542080421925.833f0607b5 column=info:regioninfo, timestamp=1542143164503, value={ENCODED =&gt; 833f0607b5229ac2884356c723234928, NAME =&gt; 'hbase:namesp
 229ac2884356c723234928.                   ace,,1542080421925.833f0607b5229ac2884356c723234928.', STARTKEY =&gt; '', ENDKEY =&gt; ''}
 hbase:namespace,,1542080421925.833f0607b5 column=info:seqnumDuringOpen, timestamp=1542143164503, value=\x00\x00\x00\x00\x00\x00\x00
 229ac2884356c723234928.
 hbase:namespace,,1542080421925.833f0607b5 column=info:server, timestamp=1542143164503, value=hadoop03:16020
 229ac2884356c723234928.
 hbase:namespace,,1542080421925.833f0607b5 column=info:serverstartcode, timestamp=1542143164503, value=1542142884167
 229ac2884356c723234928.
2 row(s) in 0.0440 seconds
</code></pre>
<h3><a id="hbase_48"></a>hbase的读取流程</h3>
<ol>
<li>客户端根据表名和rowkey 找到对应的region</li>
<li>在zookeeper中记录了meta表中的region信息, 客户端就可以从zookeeper中得到region信息</li>
<li>在客户端拿到region信息之后, 就可以得到regionserver信息, 就可以访问regionserver</li>
<li>客户端去找对应的region</li>
</ol>
<h3><a id="hbase_55"></a>hbase的写入流程</h3>
<ol>
<li>
<p>客户端根据表名和rowkey找到对就的region</p>
</li>
<li>
<p>在zookeeper中存储了region的信息表, 客户端从meta表中得到对应的region信息</p>
</li>
<li>
<p>预写日志 : WAL (write ahead log)找到对应的regionserver ，不是直接写在region上, 写到region中的store中的memstore中. 所有的客户端的命令都会记录在这个日志里, 他的作用是为了防止在进行数据写入的时候, 服务器突然宕机 , 造成数据的丢失, 这样可以从hlog中进行恢复</p>
</li>
<li>
<p>每次用户写入的时候, 同时写入数据到hlog和memstore, 并且在regionserver上只有一个hlog</p>
</li>
<li>
<p>当memstore达到一个阈值的时候, 会进行flush操作, 就会把memstore中的数据写到storeFile文件中, 该文件是保存到HDFS上的.</p>
</li>
<li>
<p>当storeFile文件数量增长到一定数量时, 会进行合并操作, 会形成更大的storeFile, 在合并文件的过程中存在三种合并机制minor, major, campaction</p>
</li>
<li>
<p>当region上的文件过大, 达到一定大小的时候会进行等分, 会进行split操作, 分裂成两个region, 其中一个region会由master分配到其他的 regionserver 进行负载均衡</p>
</li>
</ol>
<ul>
<li>
<p>在hbase上对于文件的删除</p>
<p>先给要删除的文件数据打上删除标记, 在合并的过程中, 进行删除, 例如版本号较老的数据, 在hbase上会存在保存时间, 当达到一定时间之后, 版本老的数据将会在合并的过程中删除</p>
</li>
</ul>
<h3><a id="hbaseJava_API_73"></a>hbase的Java API</h3>
<p>对于hbase的API操作, 其实就是调用hbase中的接口, 通过java进行数据库的操作, 类似于jdbc的操作</p>
<ol>
<li>创建namespace</li>
</ol>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">createNamespace</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
    <span class="token comment">// 获得配置信息</span>
    Configuration conf <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得连接</span>
    Connection connection <span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得管理</span>
    Admin admin <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getAdmin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// namespace描述</span>
    NamespaceDescriptor nsDesc <span class="token operator">=</span> NamespaceDescriptor<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token string">"hadoop"</span><span class="token punctuation">)</span><span class="token punctuation">.</span><span class="token function">build</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 创建namespace</span>
    admin<span class="token punctuation">.</span><span class="token function">createNamespace</span><span class="token punctuation">(</span>nsDesc<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<ol start="2">
<li>创建表, 以及列簇</li>
</ol>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">creaetTable</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
    <span class="token comment">// 获得配置信息</span>
    Configuration conf <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得连接</span>
    Connection connection <span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得管理</span>
    Admin admin <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getAdmin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加表名</span>
    TableName tableName <span class="token operator">=</span> TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span><span class="token string">"hadoop:student"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加表描述</span>
    HTableDescriptor hTableDesc <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HTableDescriptor</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加列簇描述</span>
    HColumnDescriptor hcol <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">HColumnDescriptor</span><span class="token punctuation">(</span><span class="token string">"f1"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 将列簇添加到表中</span>
    hTableDesc<span class="token punctuation">.</span><span class="token function">addFamily</span><span class="token punctuation">(</span>hcol<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 创建表</span>
    admin<span class="token punctuation">.</span><span class="token function">createTable</span><span class="token punctuation">(</span>hTableDesc<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<ol start="3">
<li>删除表</li>
</ol>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">delTable</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
    <span class="token comment">// 获得配置信息</span>
    Configuration conf <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得连接</span>
    Connection connection <span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得管理</span>
    Admin admin <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getAdmin</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加表名</span>
    TableName tableName <span class="token operator">=</span> TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span><span class="token string">"hadoop:student"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 禁用表</span>
    admin<span class="token punctuation">.</span><span class="token function">disableTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 删除表</span>
    admin<span class="token punctuation">.</span><span class="token function">deleteTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<ol start="4">
<li>添加数据</li>
</ol>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">putData</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
    <span class="token comment">// 获得配置信息</span>
    Configuration conf <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得连接</span>
    Connection connection <span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加表名</span>
    TableName tableName <span class="token operator">=</span> TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span><span class="token string">"hadoop:student"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得表</span>
    Table table <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加rowkey</span>
    Put put <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Put</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"1001"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 要添加的数据</span>
    put<span class="token punctuation">.</span><span class="token function">addColumn</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"f1"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">)</span><span class="token punctuation">,</span> Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"zhangsan"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 执行</span>
    table<span class="token punctuation">.</span><span class="token function">put</span><span class="token punctuation">(</span>put<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<ol start="5">
<li>删除数据</li>
</ol>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">delData</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
    <span class="token comment">// 获得配置信息</span>
    Configuration conf <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得连接</span>
    Connection connection <span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加表名</span>
    TableName tableName <span class="token operator">=</span> TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span><span class="token string">"hadoop:student"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得表</span>
    Table table <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加rowkey	</span>
    Delete del <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Delete</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"1001"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 执行删除</span>
    table<span class="token punctuation">.</span><span class="token function">delete</span><span class="token punctuation">(</span>del<span class="token punctuation">)</span><span class="token punctuation">;</span>
<span class="token punctuation">}</span>
</code></pre>
<ol start="6">
<li>查询数据</li>
</ol>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">getData</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>
    <span class="token comment">// 获得配置信息</span>
    Configuration conf <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得连接</span>
    Connection connection <span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加表名</span>
    TableName tableName <span class="token operator">=</span> TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span><span class="token string">"hadoop:student"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 获得表</span>
    Table table <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 添加rowkey</span>
    Get get <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Get</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"1001"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 拿到值</span>
    Result result <span class="token operator">=</span> table<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span>get<span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 放到单元格里</span>
    Cell<span class="token punctuation">[</span><span class="token punctuation">]</span> cells <span class="token operator">=</span> result<span class="token punctuation">.</span><span class="token function">rawCells</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 遍历</span>
    <span class="token keyword">for</span> <span class="token punctuation">(</span>Cell cell <span class="token operator">:</span> cells<span class="token punctuation">)</span> <span class="token punctuation">{</span>
    <span class="token comment">// 列簇</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneFamily</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 列名</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneQualifier</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// rowkey</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneRow</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 值</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneValue</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token comment">// 时间戳</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>cell<span class="token punctuation">.</span><span class="token function">getTimestamp</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></pre>
<ol start="7">
<li>通过scan进行查询</li>
</ol>
<pre><code class="prism language-java"><span class="token annotation punctuation">@Test</span>
<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">scanDate</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token keyword">throws</span> IOException <span class="token punctuation">{</span>

    <span class="token comment">// 读取配置文件; 先读取默认的配置文件， 在读取自定义的配置文件</span>
    Configuration conf <span class="token operator">=</span> HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// 建立hbase的连接</span>
    Connection connection <span class="token operator">=</span> ConnectionFactory<span class="token punctuation">.</span><span class="token function">createConnection</span><span class="token punctuation">(</span>conf<span class="token punctuation">)</span><span class="token punctuation">;</span>

    TableName tableName <span class="token operator">=</span> TableName<span class="token punctuation">.</span><span class="token function">valueOf</span><span class="token punctuation">(</span><span class="token string">"hadoop25:stuinfo"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    Table table <span class="token operator">=</span> connection<span class="token punctuation">.</span><span class="token function">getTable</span><span class="token punctuation">(</span>tableName<span class="token punctuation">)</span><span class="token punctuation">;</span>

    Scan scan <span class="token operator">=</span> <span class="token keyword">new</span> <span class="token class-name">Scan</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// 当表中存在多个列簇的时候，可以指定列簇进行查询</span>
    scan<span class="token punctuation">.</span><span class="token function">addFamily</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toBytes</span><span class="token punctuation">(</span><span class="token string">"info"</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">//			scan.addColumn(family, qualifier)</span>
    <span class="token comment">// family： 列簇</span>
    <span class="token comment">// qualifier： 列</span>
    ResultScanner scanner <span class="token operator">=</span> table<span class="token punctuation">.</span><span class="token function">getScanner</span><span class="token punctuation">(</span>scan<span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">for</span> <span class="token punctuation">(</span>Result result <span class="token operator">:</span> scanner<span class="token punctuation">)</span> <span class="token punctuation">{</span>

    Cell<span class="token punctuation">[</span><span class="token punctuation">]</span> cells <span class="token operator">=</span> result<span class="token punctuation">.</span><span class="token function">rawCells</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token keyword">for</span> <span class="token punctuation">(</span>Cell cell <span class="token operator">:</span> cells<span class="token punctuation">)</span> <span class="token punctuation">{</span>

    <span class="token comment">// rowkey</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneRow</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// 列簇</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneFamily</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// 列</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneQualifier</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// 数据内容</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneValue</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>

    <span class="token comment">// 时间戳</span>
    System<span class="token punctuation">.</span>out<span class="token punctuation">.</span><span class="token function">println</span><span class="token punctuation">(</span>cell<span class="token punctuation">.</span><span class="token function">getTimestamp</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
    <span class="token punctuation">}</span>
    <span class="token punctuation">}</span>

<span class="token punctuation">}</span>
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
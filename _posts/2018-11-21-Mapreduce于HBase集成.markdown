---
layout:     post
title:      Mapreduce于HBase集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="MapreduceHBase_0"></a>Mapreduce于HBase集成</h1>
<h3><a id="_1"></a>集成的模式</h3>
<ul>
<li>从hbase中读取数据,Hbase的数据作为map的输入,输出可以任意指定.</li>
<li>将数据写入Hbase,将hbase作为reduce的输出,输入可以任意指定.</li>
<li>从hbase中读 最终在写入到hbase中,场景： 数据迁移.</li>
</ul>
<p>在hbase中已经封装好了jar包：<strong>hbase-server-0.98.6-cdh5.3.6.jar</strong></p>
<h3><a id="1_8"></a>1、环境的配置</h3>
<p>将hbase中环境的jar包放到hadoop中，所有hbasejar包都放进去会造成jar包的冲突。<br>
Hadoop中需要hbase中的哪些jar包就导入哪些。<br>
hbase mapredcp 通过该命令可以列出hadoop需要的jar包。</p>
<pre><code class="language-java"><code class="prism  language-java"><span class="token punctuation">[</span>hadoop<span class="token annotation punctuation">@hh</span> hbase<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">]</span>$ bin<span class="token operator">/</span>hbase mapredcp
<span class="token comment">//这样可以列出来hadoop需要的jar包</span>
</code></code></pre>
<pre><code class="language-java"><code class="prism  language-java">export HBASE_HOME<span class="token operator">=</span><span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hbase<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span>
export HADOOP_CLASSPATH<span class="token operator">=</span>$HADOOP_CLASSPATH<span class="token operator">:</span>`$HBASE_HOME<span class="token operator">/</span>bin<span class="token operator">/</span>hbase mapredcp`
<span class="token comment">//在hbase下运行这两句代码，可以临时修改环境变量，使hadoop暂时拥有这些包的路径.</span>
</code></code></pre>
<pre><code class="language-java"><code class="prism  language-java">export HADOOP_CLASSPATH<span class="token operator">=</span>$HADOOP_CLASSPATH<span class="token operator">:</span><span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hbase<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>lib<span class="token comment">/*
//这个在hadoop中的etc/hadoop/hadoop-env.sh中配置的.
</span></code></code></pre>
<p>CellCounter: 统计多少个单元格<br>
completebulkload: 加载Hfile的数据文件<br>
copytable: 把一个表从一个集群拷贝到另一个集群<br>
export: 将表导出到HDFS<br>
import:<br>
importtsv: 导入一个TSV的格式文件<br>
rowcounter: 统计rowkey<br>
verifyrep: 比较两个不同集群中表的数据</p>
<p>测试：统计rowKey</p>
<pre><code class="language-java"><code class="prism  language-java"><span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hadoop<span class="token operator">-</span><span class="token number">2.5</span><span class="token number">.0</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>bin<span class="token operator">/</span>yarn jar <span class="token punctuation">.</span><span class="token punctuation">.</span>/lib<span class="token operator">/</span>hbase<span class="token operator">-</span>server<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar  rowcounter stu<span class="token operator">:</span>stu_t 
<span class="token comment">//结果:</span>
 org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>mapreduce<span class="token punctuation">.</span>RowCounter$RowCounterMapper$Counters
                ROWS<span class="token operator">=</span><span class="token number">1</span>
</code></code></pre>
<hr>
<h3><a id="2HBaseMapReduce_45"></a>2、HBase上的MapReduce：</h3>
<p><strong>在HBase中创建一个表，读取的表：stu_info   写入的表 stu_info_copy</strong></p>
<pre><code class="language-java"><code class="prism  language-java"><span class="token comment">//建表：</span>
create <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'info'</span><span class="token punctuation">,</span><span class="token string">'degree'</span><span class="token punctuation">,</span><span class="token string">'work'</span>
<span class="token comment">//插入数据：6个rowkey 3个列簇</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10001'</span><span class="token punctuation">,</span><span class="token string">'degree:xueli'</span><span class="token punctuation">,</span><span class="token string">'benke'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10001'</span><span class="token punctuation">,</span><span class="token string">'info:age'</span><span class="token punctuation">,</span><span class="token string">'18'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10001'</span><span class="token punctuation">,</span><span class="token string">'info:sex'</span><span class="token punctuation">,</span><span class="token string">'male'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10001'</span><span class="token punctuation">,</span><span class="token string">'info:name'</span><span class="token punctuation">,</span><span class="token string">'tom'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10001'</span><span class="token punctuation">,</span><span class="token string">'work:job'</span><span class="token punctuation">,</span><span class="token string">'bigdata'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10002'</span><span class="token punctuation">,</span><span class="token string">'degree:xueli'</span><span class="token punctuation">,</span><span class="token string">'gaozhong'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10002'</span><span class="token punctuation">,</span><span class="token string">'info:age'</span><span class="token punctuation">,</span><span class="token string">'22'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10002'</span><span class="token punctuation">,</span><span class="token string">'info:sex'</span><span class="token punctuation">,</span><span class="token string">'female'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10002'</span><span class="token punctuation">,</span><span class="token string">'info:name'</span><span class="token punctuation">,</span><span class="token string">'jack'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10003'</span><span class="token punctuation">,</span><span class="token string">'info:age'</span><span class="token punctuation">,</span><span class="token string">'22'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10003'</span><span class="token punctuation">,</span><span class="token string">'info:name'</span><span class="token punctuation">,</span><span class="token string">'leo'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10004'</span><span class="token punctuation">,</span><span class="token string">'info:age'</span><span class="token punctuation">,</span><span class="token string">'18'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10004'</span><span class="token punctuation">,</span><span class="token string">'info:name'</span><span class="token punctuation">,</span><span class="token string">'peter'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10005'</span><span class="token punctuation">,</span><span class="token string">'info:age'</span><span class="token punctuation">,</span><span class="token string">'19'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10005'</span><span class="token punctuation">,</span><span class="token string">'info:name'</span><span class="token punctuation">,</span><span class="token string">'jim'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10006'</span><span class="token punctuation">,</span><span class="token string">'info:age'</span><span class="token punctuation">,</span><span class="token string">'20'</span>
put <span class="token string">'stu_info'</span><span class="token punctuation">,</span><span class="token string">'20170222_10006'</span><span class="token punctuation">,</span><span class="token string">'info:name'</span><span class="token punctuation">,</span><span class="token string">'zhangsan'</span>
</code></code></pre>
<p>MapReduce的实现：</p>
<pre><code class="language-java"><code class="prism  language-java"><span class="token keyword">package</span> org<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>test<span class="token punctuation">;</span>

<span class="token keyword">import</span> java<span class="token punctuation">.</span>io<span class="token punctuation">.</span>IOException<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>Cell<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>CellUtil<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>client<span class="token punctuation">.</span>Put<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>client<span class="token punctuation">.</span>Result<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>io<span class="token punctuation">.</span>ImmutableBytesWritable<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>mapreduce<span class="token punctuation">.</span>TableMapper<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>util<span class="token punctuation">.</span>Bytes<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>mapreduce<span class="token punctuation">.</span>Mapper<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">TestHBaseMapper</span> <span class="token keyword">extends</span> <span class="token class-name">TableMapper</span><span class="token generics function"><span class="token punctuation">&lt;</span>ImmutableBytesWritable<span class="token punctuation">,</span> Put<span class="token punctuation">&gt;</span></span><span class="token punctuation">{</span>
	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">protected</span> <span class="token keyword">void</span> <span class="token function">map</span><span class="token punctuation">(</span>ImmutableBytesWritable key<span class="token punctuation">,</span> Result value<span class="token punctuation">,</span>
			Mapper<span class="token generics function"><span class="token punctuation">&lt;</span>ImmutableBytesWritable<span class="token punctuation">,</span> Result<span class="token punctuation">,</span> ImmutableBytesWritable<span class="token punctuation">,</span> Put<span class="token punctuation">&gt;</span></span><span class="token punctuation">.</span>Context context<span class="token punctuation">)</span>
			<span class="token keyword">throws</span> IOException<span class="token punctuation">,</span> InterruptedException <span class="token punctuation">{</span>
		<span class="token comment">// TODO Auto-generated method stub</span>
		Put put<span class="token operator">=</span><span class="token keyword">new</span> <span class="token class-name">Put</span><span class="token punctuation">(</span>key<span class="token punctuation">.</span><span class="token function">get</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		<span class="token keyword">for</span><span class="token punctuation">(</span>Cell cell<span class="token operator">:</span>value<span class="token punctuation">.</span><span class="token function">rawCells</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
			
			<span class="token keyword">if</span><span class="token punctuation">(</span><span class="token string">"info"</span><span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneFamily</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
				
				<span class="token keyword">if</span><span class="token punctuation">(</span><span class="token string">"name"</span><span class="token punctuation">.</span><span class="token function">equals</span><span class="token punctuation">(</span>Bytes<span class="token punctuation">.</span><span class="token function">toString</span><span class="token punctuation">(</span>CellUtil<span class="token punctuation">.</span><span class="token function">cloneQualifier</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
					
					put<span class="token punctuation">.</span><span class="token function">add</span><span class="token punctuation">(</span>cell<span class="token punctuation">)</span><span class="token punctuation">;</span>
				<span class="token punctuation">}</span>
			<span class="token punctuation">}</span>			
		<span class="token punctuation">}</span>		
		context<span class="token punctuation">.</span><span class="token function">write</span><span class="token punctuation">(</span>key<span class="token punctuation">,</span> put<span class="token punctuation">)</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>
</code></code></pre>
<p><strong>驱动类：</strong></p>
<pre><code class="language-java"><code class="prism  language-java"><span class="token keyword">package</span> org<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>test<span class="token punctuation">;</span>

<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>conf<span class="token punctuation">.</span>Configuration<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>HBaseConfiguration<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>client<span class="token punctuation">.</span>Put<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>client<span class="token punctuation">.</span>Scan<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>io<span class="token punctuation">.</span>ImmutableBytesWritable<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>hbase<span class="token punctuation">.</span>mapreduce<span class="token punctuation">.</span>TableMapReduceUtil<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>mapreduce<span class="token punctuation">.</span>Job<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>util<span class="token punctuation">.</span>Tool<span class="token punctuation">;</span>
<span class="token keyword">import</span> org<span class="token punctuation">.</span>apache<span class="token punctuation">.</span>hadoop<span class="token punctuation">.</span>util<span class="token punctuation">.</span>ToolRunner<span class="token punctuation">;</span>

<span class="token keyword">public</span> <span class="token keyword">class</span> <span class="token class-name">TestDriver</span> <span class="token keyword">implements</span> <span class="token class-name">Tool</span><span class="token punctuation">{</span>

	Configuration conf<span class="token punctuation">;</span>
	
	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">void</span> <span class="token function">setConf</span><span class="token punctuation">(</span>Configuration conf<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// TODO Auto-generated method stub</span>
		<span class="token keyword">this</span><span class="token punctuation">.</span>conf<span class="token operator">=</span>conf<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> Configuration <span class="token function">getConf</span><span class="token punctuation">(</span><span class="token punctuation">)</span> <span class="token punctuation">{</span>
		<span class="token comment">// TODO Auto-generated method stub</span>
		<span class="token keyword">return</span> <span class="token keyword">this</span><span class="token punctuation">.</span>conf<span class="token punctuation">;</span>
	<span class="token punctuation">}</span>

	<span class="token annotation punctuation">@Override</span>
	<span class="token keyword">public</span> <span class="token keyword">int</span> <span class="token function">run</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token keyword">throws</span> Exception <span class="token punctuation">{</span>
		<span class="token comment">// TODO Auto-generated method stub</span>
		Configuration conf<span class="token operator">=</span><span class="token keyword">this</span><span class="token punctuation">.</span><span class="token function">getConf</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		Job job<span class="token operator">=</span>Job<span class="token punctuation">.</span><span class="token function">getInstance</span><span class="token punctuation">(</span>conf<span class="token punctuation">,</span> <span class="token string">"wmc"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		job<span class="token punctuation">.</span><span class="token function">setJarByClass</span><span class="token punctuation">(</span>TestDriver<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		Scan scan<span class="token operator">=</span><span class="token keyword">new</span> <span class="token class-name">Scan</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		TableMapReduceUtil<span class="token punctuation">.</span><span class="token function">initTableMapperJob</span><span class="token punctuation">(</span><span class="token string">"stu_info"</span><span class="token punctuation">,</span> scan<span class="token punctuation">,</span>TestHBaseMapper<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span>ImmutableBytesWritable<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> Put<span class="token punctuation">.</span><span class="token keyword">class</span><span class="token punctuation">,</span> job<span class="token punctuation">)</span><span class="token punctuation">;</span>
	
        TableMapReduceUtil<span class="token punctuation">.</span><span class="token function">initTableReducerJob</span><span class="token punctuation">(</span><span class="token string">"stu_info_copy"</span><span class="token punctuation">,</span> null<span class="token punctuation">,</span> job<span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		job<span class="token punctuation">.</span><span class="token function">setNumReduceTasks</span><span class="token punctuation">(</span><span class="token number">1</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		

		<span class="token keyword">return</span> job<span class="token punctuation">.</span><span class="token function">waitForCompletion</span><span class="token punctuation">(</span><span class="token boolean">true</span><span class="token punctuation">)</span><span class="token operator">?</span><span class="token number">0</span><span class="token operator">:</span><span class="token number">1</span><span class="token punctuation">;</span>
	<span class="token punctuation">}</span>
	
	<span class="token keyword">public</span> <span class="token keyword">static</span> <span class="token keyword">void</span> <span class="token function">main</span><span class="token punctuation">(</span>String<span class="token punctuation">[</span><span class="token punctuation">]</span> args<span class="token punctuation">)</span> <span class="token punctuation">{</span>
		
		Configuration conf<span class="token operator">=</span>HBaseConfiguration<span class="token punctuation">.</span><span class="token function">create</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		
		<span class="token keyword">try</span> <span class="token punctuation">{</span>
			<span class="token keyword">int</span> status<span class="token operator">=</span>ToolRunner<span class="token punctuation">.</span><span class="token function">run</span><span class="token punctuation">(</span>conf<span class="token punctuation">,</span> <span class="token keyword">new</span> <span class="token class-name">TestDriver</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">,</span>args<span class="token punctuation">)</span><span class="token punctuation">;</span>
			System<span class="token punctuation">.</span><span class="token function">exit</span><span class="token punctuation">(</span>status<span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span> <span class="token keyword">catch</span> <span class="token punctuation">(</span><span class="token class-name">Exception</span> e<span class="token punctuation">)</span> <span class="token punctuation">{</span>
			<span class="token comment">// TODO Auto-generated catch block</span>
			e<span class="token punctuation">.</span><span class="token function">printStackTrace</span><span class="token punctuation">(</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
		<span class="token punctuation">}</span>	
	<span class="token punctuation">}</span>
<span class="token punctuation">}</span>

</code></code></pre>
<p><strong>步骤：</strong> 将两个类导出成一个包，然后导入到本地。</p>
<p>**这个包的作用：**使用这个包可以将’stu_info’这张表的内容复制到’stu_info_copy’这张表中。</p>
<hr>
<h3><a id="3HBase_importtsv_178"></a>3、HBase importtsv工具的使用</h3>
<p>importtsv -Dimporttsv.columns=a,b,c  </p>
<p>1、将数据从HDFS上导入到一张HBase表中（分隔符为’\t’）：</p>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hadoop<span class="token operator">-</span><span class="token number">2.5</span><span class="token number">.0</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>bin<span class="token operator">/</span>yarn jar <span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hbase<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>lib<span class="token operator">/</span>hbase<span class="token operator">-</span>server<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar importtsv <span class="token operator">-</span>Dimporttsv<span class="token punctuation">.</span><span class="token keyword">columns</span><span class="token operator">=</span>HBASE_ROW_KEY<span class="token punctuation">,</span>info:name<span class="token punctuation">,</span>info:age<span class="token punctuation">,</span>info:location stu_1 <span class="token operator">/</span>impdemo<span class="token punctuation">.</span>tsv
</code></code></pre>
<p>2、将数据从HDFS上导入到一张HBase表中（指定分隔符）：</p>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hadoop<span class="token operator">-</span><span class="token number">2.5</span><span class="token number">.0</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>bin<span class="token operator">/</span>yarn jar <span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hbase<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>lib<span class="token operator">/</span>hbase<span class="token operator">-</span>server<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar importtsv <span class="token operator">-</span>Dimporttsv<span class="token punctuation">.</span>separator<span class="token operator">=</span><span class="token punctuation">,</span> <span class="token operator">-</span>Dimporttsv<span class="token punctuation">.</span><span class="token keyword">columns</span><span class="token operator">=</span>HBASE_ROW_KEY<span class="token punctuation">,</span>info:name<span class="token punctuation">,</span>info:age<span class="token punctuation">,</span>info:location stu_1 <span class="token operator">/</span>importdemo<span class="token punctuation">.</span>tsv
</code></code></pre>
<p>3、将数据从本地导入到HBase表中：</p>
<ul>
<li>将数据转换为HFile</li>
</ul>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hadoop<span class="token operator">-</span><span class="token number">2.5</span><span class="token number">.0</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>bin<span class="token operator">/</span>yarn jar <span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hbase<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>lib<span class="token operator">/</span>hbase<span class="token operator">-</span>server<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar importtsv <span class="token operator">-</span>Dimporttsv<span class="token punctuation">.</span><span class="token keyword">columns</span><span class="token operator">=</span>HBASE_ROW_KEY<span class="token punctuation">,</span>info:name<span class="token punctuation">,</span>info:age<span class="token punctuation">,</span>info:location <span class="token operator">-</span>Dimporttsv<span class="token punctuation">.</span><span class="token keyword">bulk</span><span class="token punctuation">.</span>output<span class="token operator">=</span><span class="token operator">/</span>testHfile importtsv <span class="token operator">/</span>impdemo<span class="token punctuation">.</span>tsv
</code></code></pre>
<ul>
<li>将HFile中的数据导入到HBase表中：</li>
</ul>
<pre><code class="language-sql"><code class="prism  language-sql"><span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hadoop<span class="token operator">-</span><span class="token number">2.5</span><span class="token number">.0</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>bin<span class="token operator">/</span>yarn jar <span class="token operator">/</span>opt<span class="token operator">/</span>app<span class="token operator">/</span>hbase<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token operator">/</span>lib<span class="token operator">/</span>hbase<span class="token operator">-</span>server<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar completebulkload <span class="token operator">/</span>testHfile stu_2 
</code></code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
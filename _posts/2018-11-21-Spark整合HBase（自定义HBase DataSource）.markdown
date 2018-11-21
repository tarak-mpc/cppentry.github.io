---
layout:     post
title:      Spark整合HBase（自定义HBase DataSource）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/UUfFO/article/details/79243644				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="背景">背景</h2>

<p>Spark支持多种数据源，但是Spark对HBase 的读写都没有相对优雅的api，但spark和HBase整合的场景又比较多，故通过spark的DataSource API自己实现了一套比较方便操作HBase的API。</p>



<h2 id="写-hbase">写 HBase</h2>

<p>写HBase会根据Dataframe的schema写入对应数据类型的数据到Hbase，先上使用示例：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-keyword">import</span> spark<span class="hljs-built_in">.</span>implicits<span class="hljs-built_in">.</span>_
<span class="hljs-keyword">import</span> org<span class="hljs-built_in">.</span>apache<span class="hljs-built_in">.</span>hack<span class="hljs-built_in">.</span>spark<span class="hljs-built_in">.</span>_
val df <span class="hljs-subst">=</span> spark<span class="hljs-built_in">.</span>createDataset(Seq((<span class="hljs-string">"ufo"</span>,  <span class="hljs-string">"play"</span>), (<span class="hljs-string">"yy"</span>,  <span class="hljs-string">""</span>)))<span class="hljs-built_in">.</span>toDF(<span class="hljs-string">"name"</span>, <span class="hljs-string">"like"</span>)
<span class="hljs-comment">// 方式一</span>
val options <span class="hljs-subst">=</span> <span class="hljs-built_in">Map</span>(
            <span class="hljs-string">"rowkey.filed"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"name"</span>,
            <span class="hljs-string">"startKey"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"aaaaa"</span>,
            <span class="hljs-string">"endKey"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"zzzzz"</span>,
            <span class="hljs-string">"numReg"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"12"</span>,
            <span class="hljs-string">"bulkload.enable"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"false"</span>
        )
df<span class="hljs-built_in">.</span>saveToHbase(<span class="hljs-string">"hbase_table"</span>, Some(<span class="hljs-string">"XXX:2181"</span>), options)
<span class="hljs-comment">// 方式二</span>
df1<span class="hljs-built_in">.</span>write<span class="hljs-built_in">.</span>format(<span class="hljs-string">"org.apache.spark.sql.execution.datasources.hbase"</span>)
            <span class="hljs-built_in">.</span>options(<span class="hljs-built_in">Map</span>(
                <span class="hljs-string">"rowkey.filed"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"name"</span>,
                <span class="hljs-string">"outputTableName"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"hbase_table"</span>,
                <span class="hljs-string">"hbase.zookeeper.quorum"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"XXX:2181"</span>,
                <span class="hljs-string">"startKey"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"aaaaa"</span>,
                <span class="hljs-string">"endKey"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"zzzzz"</span>,
                <span class="hljs-string">"numReg"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"12"</span>,
                <span class="hljs-string">"bulkload.enable"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"false"</span>
            ))<span class="hljs-built_in">.</span>save()</code></pre>

<p>上面两种方式实现的效果是一样的，下面解释一下每个参数的含义：</p>

<ul>
<li>rowkey.field：指定dataframe的哪个字段作为HBase表的rowkey，默认使用第一个字段</li>
<li>outputTableName：HBase表名</li>
<li>hbase.zookeeper.quorum：zookeeper地址，也可写成 zk</li>
<li>startKey，endKey，numReg：这三个配置是一家的，当对应的HBase表不存在时会先创建表，默认只有一个分区，通过这三个配置可以进行预分区，从字面意思可知分别对应，分区的起始key，结束key，分区数，另外列族名通过<code>family</code>设定，默认是 info。</li>
<li>bulkload.enable：写HBase可走HBase写入，也可BulkLoad直接生成HBase需要的Hfile，在数据量大的情况下会快很多，默认是使用BulkLoad</li>
</ul>



<h2 id="读-hbase">读 HBase</h2>

<p>示例代码如下：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">// 方式一</span>
<span class="hljs-keyword">import</span> org<span class="hljs-built_in">.</span>apache<span class="hljs-built_in">.</span>hack<span class="hljs-built_in">.</span>spark<span class="hljs-built_in">.</span>_
 val options <span class="hljs-subst">=</span> <span class="hljs-built_in">Map</span>(
    <span class="hljs-string">"spark.table.schema"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"appid:String,appstoreid:int,firm:String"</span>,
    <span class="hljs-string">"hbase.table.schema"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">":rowkey,info:appStoreId,info:firm"</span>
)
spark<span class="hljs-built_in">.</span>hbaseTableAsDataFrame(<span class="hljs-string">"hbase_table"</span>, Some(<span class="hljs-string">"XXX:2181"</span>))<span class="hljs-built_in">.</span>show(<span class="hljs-literal">false</span>)
<span class="hljs-comment">// 方式二</span>
spark<span class="hljs-built_in">.</span>read<span class="hljs-built_in">.</span>format(<span class="hljs-string">"org.apache.spark.sql.execution.datasources.hbase"</span>)<span class="hljs-built_in">.</span>
            options(<span class="hljs-built_in">Map</span>(
            <span class="hljs-string">"spark.table.schema"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"appid:String,appstoreid:int,firm:String"</span>,
            <span class="hljs-string">"hbase.table.schema"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">":rowkey,info:appStoreId,info:firm"</span>,
            <span class="hljs-string">"hbase.zookeeper.quorum"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"XXX:2181"</span>,
            <span class="hljs-string">"inputTableName"</span> <span class="hljs-subst">-&gt; </span><span class="hljs-string">"hbase_table"</span>
        ))<span class="hljs-built_in">.</span>load<span class="hljs-built_in">.</span>show(<span class="hljs-literal">false</span>)  </code></pre>

<p>spark和hbase表的schema映射关系指定不是必须的，默认会生成rowkey和content两个字段，content是由所有字段组成的json字符串，可通过<code>field.type.fieldname</code>对单个字段设置数据类型，默认都是StringType。这样映射出来还得通过spark程序转一下才是你想要的样子，而且所有字段都会去扫描，相对来说不是特别高效。</p>

<p>故我们可自定义schema映射来获取数据：</p>

<ul>
<li>hbase.table.schema：hbase表的数据结构，<code>:rowkey,cm:fieldname1,cm:fieldname2</code>，一看就明白</li>
<li>spark.table.schema：spark DataFrame对应的schema，<code>name_x:fieldType,name_y:fieldType,name_z:fieldType</code> <br>
注意这两个schema是一一对应的，Hbase只会扫描<code>hbase.table.schema</code>对应的列。</li>
</ul>

<blockquote>
  <p>源码在我的 <a href="https://github.com/SOBIGUFO/blog/issues" rel="nofollow">GitHub</a>，以后在GitHub上跟新，欢迎star</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
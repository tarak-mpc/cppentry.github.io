---
layout:     post
title:      HIVE与HBase的集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="_0"></a>作用：</h3>
<ul>
<li>将HBase的数据映射到HIVE表中。</li>
</ul>
<h3><a id="HIVE_2"></a>HIVE表类型：</h3>
<ul>
<li>管理表：在HIVE表中建表的同时在HBase中不存在该表，HBase创建相同的表·，数据不是在HIVE中而是在HBase中。</li>
<li>外部表：在HIVE中建表，同时HBase的表要提前存在，数据同样存储在HBase中。（你创建的外部表的数据来源于HBase中）</li>
</ul>
<h3><a id="jar_5"></a>框架之间的继承操作，首先考虑的是jar包：</h3>
<p>可以把HIVE集成HBase需要的jar包拷贝到hive  lib目录下面：</p>
<pre><code class="language-java"><code class="prism  language-java"><span class="token number">1</span>  hbase<span class="token operator">-</span>server<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar 
<span class="token number">2</span>  hbase<span class="token operator">-</span>client<span class="token operator">-</span><span class="token number">0.986</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar
<span class="token number">3</span>  hbase<span class="token operator">-</span>potocol<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar 
<span class="token number">4</span>  hbase<span class="token operator">-</span>it<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar
<span class="token number">5</span> hbase<span class="token operator">-</span>hadoop2<span class="token operator">-</span>compat<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar 
<span class="token number">6</span> hbase<span class="token operator">-</span>hadoop<span class="token operator">-</span>compat<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar
<span class="token number">7</span>  high<span class="token operator">-</span>scale<span class="token operator">-</span>lib<span class="token operator">-</span><span class="token number">1.1</span><span class="token number">.1</span><span class="token punctuation">.</span>jar 
<span class="token number">8</span> hbase<span class="token operator">-</span>common<span class="token operator">-</span><span class="token number">0.98</span><span class="token number">.6</span><span class="token operator">-</span>cdh5<span class="token punctuation">.</span><span class="token number">3.6</span><span class="token punctuation">.</span>jar
</code></code></pre>
<p>把以上的jar包拷贝到hive中lib目录下面。</p>
<h3><a id="HIVEhivesitexmlzookeeper_19"></a>在HIVE中hive-site.xml文件中配置zookeeper信息</h3>
<pre class=" language-xml"><code class="prism  language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
		  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>hbase.zookeeper.quorum<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
		  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>hh<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<h3><a id="_27"></a>创建表</h3>
<ul>
<li>创建内部表：</li>
</ul>
<pre><code class="language-java"><code class="prism  language-java">CREATE TABLE <span class="token function">hbase_table_3</span><span class="token punctuation">(</span>key <span class="token keyword">int</span><span class="token punctuation">,</span> value string<span class="token punctuation">)</span> 
STORED BY <span class="token string">'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</span>
WITH SERDEPROPERTIES <span class="token punctuation">(</span><span class="token string">"hbase.columns.mapping"</span> <span class="token operator">=</span> <span class="token string">":key,cf1:val"</span><span class="token punctuation">)</span>
TBLPROPERTIES <span class="token punctuation">(</span><span class="token string">"hbase.table.name"</span> <span class="token operator">=</span> <span class="token string">"xyz3"</span><span class="token punctuation">,</span> <span class="token string">"hbase.mapred.output.outputtable"</span> <span class="token operator">=</span> <span class="token string">"xyz3"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></code></pre>
<p>以上的方式创建表为HIVE中的管理表。<br>
特点：HBase中没有该表的前提下，创建之后，HBase中存在这个表。<br>
在创建之后，需要导入数据，<strong>不能用load data local inpath…</strong> ,需要跑MR程序，所以要使用insert overwrite，将一张表中的数据加载到HIVE表中，加载后数据也不在HIVE中，而是在HBase中。</p>
<ul>
<li>创建外部表：</li>
</ul>
<pre><code class="language-java"><code class="prism  language-java">CREATE EXTERNAL TABLE <span class="token function">hbase_table_4</span><span class="token punctuation">(</span>key <span class="token keyword">int</span><span class="token punctuation">,</span> value string<span class="token punctuation">)</span> 
STORED BY <span class="token string">'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</span>
WITH SERDEPROPERTIES <span class="token punctuation">(</span><span class="token string">"hbase.columns.mapping"</span> <span class="token operator">=</span> <span class="token string">"cf1:val"</span><span class="token punctuation">)</span>
<span class="token function">TBLPROPERTIES</span><span class="token punctuation">(</span><span class="token string">"hbase.table.name"</span> <span class="token operator">=</span> <span class="token string">"xyz3"</span><span class="token punctuation">,</span> <span class="token string">"hbase.mapred.output.outputtable"</span> <span class="token operator">=</span> <span class="token string">"xyz3"</span><span class="token punctuation">)</span><span class="token punctuation">;</span>
</code></code></pre>
<p>创建之后HBase不存在这张表，这张表是在HIVE中的。<br>
用普通的创建外部表的方式，创建出来的外部表是没有数据的，因为你的HIVE中的数据存放在HBase中而不是在HIVE中。</p>
<h3><a id="__50"></a>内部表 与外部表的区别？</h3>
<p><strong>内部表：</strong> 删除表 对应的数据文件会被删除<br>
<strong>外部表：</strong> 删除表 对应的数据文件不会被删除</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
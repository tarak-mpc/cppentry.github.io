---
layout:     post
title:      【七】HBase集成Hive（Hive On HBase）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：转载注明出处					https://blog.csdn.net/jy02268879/article/details/81390755				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>使用场景</h3>

<p>1.通过Hive把数据加载到HBase中，数据源可以是文件也能是表。HBase集成Hive后，hive表数据增加的同时，HBase中的数据也会增加。</p>

<p>2.HBase的查询不支持join和group by。可以通过Hive整合HBase的方式让HBase支持这些操作（先把HBase的数据加载到Hive中，通过Hive的语法来join和group by）。</p>

<p>3.HBase实时的插入数据，或者周期性的加载数据文件，通过Hive直接查询这些数据，Hive不用再加载一次数据了。</p>

<p><span style="color:#f33b45;"><strong>4.结合Hive的数据分析和HBase实时数据的优点。</strong></span></p>

<h3>Hive映射HBase表</h3>

<p><a href="https://cwiki.apache.org/confluence/display/hive/hbaseintegration" rel="nofollow">官网介绍</a></p>

<p>Storage Handlers ：Hive的字段和HBase中的列都是通过Storage Handler来维护的。创捷Hive表把存储格式指定为Storage Handler。这个程序被编译成一个独立的模块，在Java中它就是一个独立的jar包hive-hbase-handler-X.X.X.jar，Hive的客户端必须要能识别到这个jar包，通过--auxpath来指定。</p>

<p>Hive启动方式：直接拷贝HBase所有lib下Jar包到Hive lib中。然后直接启动hive。</p>

<pre class="has">
<code class="language-bash">cd /app/hive/lib

cp /app/hbase/lib/* /app/hive/lib</code></pre>

<pre class="has">
<code class="language-bash">cd /app/hive/bin
hive</code></pre>

<p>Hive集成HBase可以有效利用HBase的存储特性，如更新和列索引等。</p>

<p>Hive表与HBase表之间需要建立映射关系</p>

<p>每个在Hive表中的域都存在HBase中，而在Hive表中不需要包含所有HBase中的列</p>

<pre class="has">
<code class="language-sql">create table tablename(
key int,
value string
)stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
with serdeproperties("hbase.columns.mapping"=":rowkey,cf1:value")
tblproperties("hbase.table.name"="xyz")</code></pre>

<p><strong><span style="color:#f33b45;">不用提前在HBase中创建表，它会自动创建。</span></strong></p>

<p>创建hive表指定hbase映射的例子。</p>

<pre class="has">
<code class="language-sql">create table hiveonhbase(
key int,
value string
)stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
with serdeproperties("hbase.columns.mapping"=":key,cf1:value")
tblproperties("hbase.table.name"="hiveonhbase")</code></pre>

<p> </p>

<p><img alt="" class="has" height="297" src="https://img-blog.csdn.net/20180803173356392?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>查看HBase中是否有了这张表。</p>

<p><img alt="" class="has" height="114" src="https://img-blog.csdn.net/20180803173703662?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p><strong><span style="color:#f33b45;">这张hive表已经映射了hbase的表，所以往这张hive表写数据要用insert select的方式。整合的表不能修改。</span></strong></p>

<p>创建一张表test，load数据进去，之后hiveonhbase表会insert select from test表的数据</p>

<pre class="has">
<code class="language-sql">create table testhiveonhbase(
key int,
value string
)
ROW FORMAT DELIMITED
   FIELDS TERMINATED BY '\t';</code></pre>

<p><img alt="" class="has" height="132" src="https://img-blog.csdn.net/20180803174114537?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="311"></p>

<pre class="has">
<code class="language-sql">load data local inpath '/app/hive/testData/testhiveonhbase.txt' overwrite into table testhiveonhbase;</code></pre>

<p><img alt="" class="has" height="84" src="https://img-blog.csdn.net/20180803174438159?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="770"></p>

<p>通过查询的方式把test表的数据加载到hiveonhbse</p>

<pre class="has">
<code class="language-sql">insert overwrite table hiveonhbase select * from testhiveonhbase;</code></pre>

<p>查看hive中hiveonhbase表是否有数据</p>

<pre class="has">
<code class="language-sql">select * from hiveonhbase;
</code></pre>

<p><img alt="" class="has" height="258" src="https://img-blog.csdn.net/20180803182921135?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>查看hbase中hiveonhbase表是否有数据</p>

<pre class="has">
<code class="language-bash">scan 'hiveonhbase'
</code></pre>

<p><img alt="" class="has" height="119" src="https://img-blog.csdn.net/2018080318294416?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="754"></p>

<p><strong>对于HBase中已有的表做Hive映射</strong></p>

<p><img alt="" class="has" height="277" src="https://img-blog.csdn.net/2018080318300925?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="976"></p>

<p>这样创建外表</p>

<pre class="has">
<code class="language-sql">create external table hiveonhbase_wc(
key string,
word string,
value string
)stored by 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
with serdeproperties("hbase.columns.mapping"=":key,cf:word,cf:value")
tblproperties("hbase.table.name"="wc");</code></pre>

<p>查看hive上是否有HBase已经建立好的表的数据</p>

<pre class="has">
<code class="language-sql">select * from hiveonhbase_wc;</code></pre>

<p><img alt="" class="has" height="130" src="https://img-blog.csdn.net/20180803183957307?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2p5MDIyNjg4Nzk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="359"></p>            </div>
                </div>
---
layout:     post
title:      Hive（三）常用语法（Hive QL）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u013963380/article/details/56285371				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.hive创建数据库</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">DATABASE</span>|<span class="hljs-keyword">SCHEMA</span> [<span class="hljs-keyword">IF</span> <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">EXISTS</span>] &lt;<span class="hljs-keyword">database</span> name&gt;;</span></code></pre>

<p>2.hive创建表 <br>
hive里一般有两种表的结构，表和外部表，以下分别是两种表的创建代码：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> phone_info(id <span class="hljs-keyword">int</span>,name String,storage String,price <span class="hljs-keyword">double</span>)
<span class="hljs-keyword">ROW</span> FORMAT DELIMITED //代表一行是一条记录
FIELDS TERMINATED <span class="hljs-keyword">BY</span> <span class="hljs-string">'\t'</span>//列是按照<span class="hljs-keyword">table</span>键分开
STORED <span class="hljs-keyword">AS</span> TEXTFILE[SEQUENCEFILE];</span>//二种最常见的存储格式，一般可以不写</code></pre>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">EXTERNAL</span> <span class="hljs-keyword">TABLE</span> phone_external(id <span class="hljs-keyword">int</span>,name String,price <span class="hljs-keyword">double</span>)
<span class="hljs-keyword">ROW</span> FORMAT DELIMITED 
FIELDS TERMINATED <span class="hljs-keyword">BY</span> <span class="hljs-string">'\t'</span>
STORED <span class="hljs-keyword">AS</span> TEXTFILE
LOCATION <span class="hljs-string">'&lt;/xudong/phone.txt&gt;'</span>;</span>//这里填写外部表数据的hdfs地址</code></pre>

<p>3.hive表中导入数据 <br>
hive表中导入数据有四种常见的方式： <br>
（1）从本地文件系统中导入到hive表 <br>
假设本地文件系统中文件存在的目录为/home/xudong</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">load</span> data <span class="hljs-keyword">local</span> inpath <span class="hljs-string">'/home/xudong/xxx.txt'</span> <span class="hljs-keyword">into</span> <span class="hljs-keyword">table</span> phone_info;</span></code></pre>

<p>（2）从hdfs上导入数据到hive表 <br>
假设hdfs上的目录为/user/xudong/data</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">load</span> data inpath <span class="hljs-string">'/user/xudong/data/xxx.txt'</span> <span class="hljs-keyword">into</span> <span class="hljs-keyword">table</span> phone_info;</span></code></pre>

<p><em>注：和本地文件导入不同是没有local关键字</em> <br>
（3）从别的表中查询出相应的数据并导入hive表中</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">INSERT</span> <span class="hljs-keyword">INTO</span> phone_info_like <span class="hljs-keyword">SELECT</span> * <span class="hljs-keyword">FROM</span> phone_info;</span></code></pre>

<p>（4）在创建表的时候通过别的表查询出相应的记录并插入到所创建的表中。</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> temp_info
<span class="hljs-keyword">AS</span>
<span class="hljs-keyword">SELECT</span> id phone_id，name phone_name,price <span class="hljs-keyword">FROM</span> phone_info
SORT <span class="hljs-keyword">BY</span> phone_id;</span></code></pre>

<p>4.hive删除表</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">DROP</span> <span class="hljs-keyword">TABLE</span> <span class="hljs-keyword">IF</span> <span class="hljs-keyword">EXISTS</span> phone_info;</span></code></pre>

<p>5.hive创建临时表存储中间结果</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> temp_info
<span class="hljs-keyword">AS</span>
<span class="hljs-keyword">SELECT</span> id phone_id，name phone_name,price <span class="hljs-keyword">FROM</span> phone_info
SORT <span class="hljs-keyword">BY</span> phone_id;</span>
</code></pre>

<p>6.hive简单的查询语句</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">SELECT</span> * <span class="hljs-keyword">FROM</span> temp_info;</span>
<span class="hljs-operator"><span class="hljs-keyword">SELECT</span> id phone_id,name phone_name <span class="hljs-keyword">FROM</span> phone_info;</span>
<span class="hljs-operator"><span class="hljs-keyword">SELECT</span> a.ip,a.name,b.username <span class="hljs-keyword">FROM</span> phone_info a <span class="hljs-keyword">INNER</span> <span class="hljs-keyword">JOIN</span> <span class="hljs-keyword">user</span> b <span class="hljs-keyword">on</span> (a.ip=b.ip);</span>
</code></pre>

<p>7.hive批量插入数据到表</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> phone_info_like <span class="hljs-keyword">LIKE</span> phone_info;</span>//复制表的结构
<span class="hljs-operator"><span class="hljs-keyword">INSERT</span> <span class="hljs-keyword">INTO</span> phone_info_like <span class="hljs-keyword">SELECT</span> * <span class="hljs-keyword">FROM</span> phone_info;</span>
<span class="hljs-operator"><span class="hljs-keyword">INSERT</span> OVERWRITE phoen_info_like <span class="hljs-keyword">SELECT</span> * <span class="hljs-keyword">FROM</span> phone_info;</span>//into是追加数据，overwrite是覆盖以及存在的数据，属于重复性校验
</code></pre>

<p>8.hive分区表</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> part_table(id <span class="hljs-keyword">int</span>,name String,ip String,city String,<span class="hljs-keyword">date</span> String)
PARTITIONED <span class="hljs-keyword">BY</span> (part_flag String)//这里的分区字段可以是表中字段也可以是指定的字段
<span class="hljs-keyword">ROW</span> FORMAT DELIMITED FIELDS TERMINATED <span class="hljs-keyword">BY</span> <span class="hljs-string">','</span>;</span>

<span class="hljs-operator"><span class="hljs-keyword">load</span> data <span class="hljs-keyword">local</span> inpath <span class="hljs-string">'/home/xudong/test.txt'</span> <span class="hljs-keyword">into</span> <span class="hljs-keyword">table</span> part_table partition(part_flag=<span class="hljs-string">'part1'</span>);</span>
<span class="hljs-operator"><span class="hljs-keyword">load</span> data <span class="hljs-keyword">local</span> inpath <span class="hljs-string">'/home/xudong/test1.txt'</span> <span class="hljs-keyword">into</span> <span class="hljs-keyword">table</span> part_table partition(part_flag=<span class="hljs-string">'part2'</span>);</span>

<span class="hljs-operator"><span class="hljs-keyword">select</span> * <span class="hljs-keyword">from</span> part_table <span class="hljs-keyword">where</span> part_flag=<span class="hljs-string">'part1'</span>;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
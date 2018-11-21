---
layout:     post
title:      高可靠性、高性能、可伸缩、分布式、基于列存储的非关系型（NoSQL）数据库——Hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-tomorrow-night">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4><a id="Hbase_0"></a>一、什么是Hbase</h4>
<h4><a id="Hbase_2"></a>二、Hbase分布式集群搭建</h4>
<h4><a id="MysqlHbase_4"></a>Mysql和Hbase的区别：</h4>
<p><img src="http://39.105.70.212/wp-content/uploads/2018/11/QQ%E6%88%AA%E5%9B%BE20181115093827.png" alt=""></p>
<h4><a id="HBase__7"></a>三、HBase 表存储结构</h4>
<h6><a id="HBase__8"></a>HBase 表逻辑视图</h6>
<p><img src="http://39.105.70.212/wp-content/uploads/2018/11/1.png" alt=""><br>
表的形式存储数据，表由行和列组成。列划分为若干个列簇 (Column Family)。</p>
<h4><a id="2HBase__12"></a>2、HBase 表结构组成</h4>
<h5><a id="RowKey_13"></a>行键（RowKey）</h5>
<pre><code class="prism language-bash">作用：用来检索记录的主键
访问 HBase 表中的行，有三种方式：
- 通过单个 RowKey 访问
- 通过 RowKey 的 range（正则）
- 全表扫描

可以是任意字符串。在 HBase 内部， RowKey 保存为字节数组（byte<span class="token punctuation">[</span><span class="token punctuation">]</span>）。

存储时，表中数据按照 RowKey 排序（字典顺序）
设计key时，利用排序存储这个特性，将经常一起读取的行放到一起。
</code></pre>
<h5><a id="Column_Family_26"></a>列簇（Column Family）</h5>
<pre><code class="prism language-bash">列簇包含列，列簇是表的 schema 的一部分（而列不是），必须在使用表之前定义
列名都以列簇作为前缀。例如：courses:history，courses:math 都属于 courses 这个列簇
尽量不要设置过多列簇
因为列簇越多，在取一行数据时所要参与 IO、搜寻的文件就越多
</code></pre>
<h5><a id="TimeStamp_33"></a>时间戳（TimeStamp）</h5>
<pre><code class="prism language-bash">可以通过 row 和 columns 确定一个存储单元（Cell）
每个 Cell 都保存着同一份数据的多个版本。
版本通过时间戳来索引
类型：64 位整型
HBase（在数据写入时自动）赋值
此时间戳是精确到毫秒的当前系统时间
客户显式赋值
顺序：每个 Cell中，不同版本的数据按照时间戳降序排序，即最新的数据排在最前面。
数据版本回收方式
- 保存数据的最后 n 个版本
- 保存最近一段时间内的版本（设置数据的生命周期 TTL）
可以针对每个列簇进行设置
</code></pre>
<h5><a id="Cell_48"></a>单元（Cell）</h5>
<pre><code class="prism language-bash">由<span class="token punctuation">{</span>RowKey, column<span class="token punctuation">(</span> <span class="token operator">=</span><span class="token operator">&lt;</span>family<span class="token operator">&gt;</span> + <span class="token operator">&lt;</span>label<span class="token operator">&gt;</span><span class="token punctuation">)</span>,version<span class="token punctuation">}</span> 确定唯一单元
Cell 中的数据是没有类型的，全部是字节码形式存储。
</code></pre>
<h5><a id="Version_53"></a>版本（Version）</h5>
<pre><code class="prism language-bash">有很多单元（Cell）的行和列是相同的，使用版本来区分不同的单元
类型：用一个长整型表示
顺序：按照时间戳降序排列
因此当读取这个文件的时候，最先找到的是最近的版本
</code></pre>
<h4><a id="HBase__60"></a>HBase 表的特点</h4>
<pre><code class="prism language-bash">	（1）大
	（2）面向列： 面向列（簇）的存储和权限控制，列（簇）独立检索。
	（3）稀疏：对于为空（null）的列，并不占用存储空间。
	（4）无模式：每一行都有一个可以排序的主键和任意多的列，列可以根据需要动态增加，同一张表中不同的行可以有截然不同的列。
	（5）数据多版本：每个单元中的数据可以有多个版本，默认情况下，版本号自动分配，版本号就是单元格插入时的时间戳。
	（6）数据类型单一：HBase 中的数据都是字符串，没有类型。
</code></pre>
<h4><a id="Hbase_69"></a>四、Hbase命令行操作</h4>
<h5><a id="1_HBase__70"></a>1、进入 HBase 命令行界面</h5>
<h6><a id="_71"></a>基本操作</h6>
<pre><code class="prism language-bash"><span class="token punctuation">[</span>xutao@Hadoop1 conf<span class="token punctuation">]</span>$ hbase shell
- 任意一台安装HBase的服务器节点
</code></pre>
<h6><a id="_76"></a>查看帮助信息</h6>
<pre><code class="prism language-bash">获取所有命令的帮助信息
	hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:001:0<span class="token operator">&gt;</span> <span class="token function">help</span>
获取一组命令的帮助信息
	hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:001:0<span class="token operator">&gt;</span> <span class="token function">help</span> <span class="token string">"ddl"</span>
获取单独命令的帮助信息
	hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:001:0<span class="token operator">&gt;</span> <span class="token function">help</span> <span class="token string">"list"</span>
</code></pre>
<h6><a id="_86"></a>查询服务器状态</h6>
<pre><code class="prism language-bash">hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:001:0<span class="token operator">&gt;</span> status
</code></pre>
<h6><a id="_HBase__90"></a>查询 HBase 版本</h6>
<pre><code class="prism language-bash">方法一：hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:001:0<span class="token operator">&gt;</span> version
方法二：<span class="token punctuation">[</span>shiny@hadoop02 ~<span class="token punctuation">]</span>$ hbase version
</code></pre>
<h6><a id="_HBase__95"></a>退出 HBase 命令行界面</h6>
<pre><code class="prism language-bash">方法一：hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:008:0<span class="token operator">&gt;</span> <span class="token keyword">exit</span>
方法二：hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:001:0<span class="token operator">&gt;</span> quit
</code></pre>
<h3><a id="DDL_100"></a>DDL（数据定义语言）操作</h3>
<h6><a id="_101"></a>创建表</h6>
<pre><code class="prism language-bash">1、默认版本:语法（默认版本号为1）
create<span class="token string">'table_name'</span>,<span class="token string">'column_family1'</span>,<span class="token string">'column_family2'</span>,<span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>
示例：
创建表students，有两个列簇：base_info，course_info
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:002:0<span class="token operator">&gt;</span> create <span class="token string">'students'</span>,<span class="token string">'base_info'</span>, <span class="token string">'course_info'</span>
2、自己设置版本号
语法
create <span class="token string">'table_name'</span>, <span class="token punctuation">{</span>NAME <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'column_family'</span>, VERSIONS <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'versions'</span><span class="token punctuation">}</span>,<span class="token punctuation">..</span><span class="token punctuation">..</span><span class="token punctuation">..</span>
示例：创建表student，有两个列簇：base_info，course_info，版本号前者为3，后者为1
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:001:0<span class="token operator">&gt;</span> create <span class="token string">'student'</span>,<span class="token punctuation">{</span>NAME <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'base_info'</span>,VERSIONS <span class="token operator">=</span><span class="token operator">&gt;</span> 3<span class="token punctuation">}</span>,<span class="token punctuation">{</span>NAME <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'course_info'</span>,VERSIONS <span class="token operator">=</span><span class="token operator">&gt;</span> 1<span class="token punctuation">}</span>
</code></pre>
<h6><a id="_114"></a>查看表</h6>
<pre><code class="prism language-bash">命令：hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:003:0<span class="token operator">&gt;</span> list
</code></pre>
<h6><a id="_118"></a>查看表结构</h6>
<pre><code class="prism language-bash">语法:describe <span class="token string">'table_name'</span>
示例：查看student的表结构
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:005:0<span class="token operator">&gt;</span> describe <span class="token string">'student'</span>
</code></pre>
<h6><a id="_124"></a>修改表结构</h6>
<pre><code class="prism language-bash">语法:alter <span class="token string">'table_name'</span>, <span class="token punctuation">{</span>NAME <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'column_family1'</span>, VERSIONS <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'versions'</span><span class="token punctuation">}</span>
示例：修改students列簇base_info的版本号
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:009:0<span class="token operator">&gt;</span> alter <span class="token string">'students'</span>,<span class="token punctuation">{</span>NAME <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'base_info'</span>, VERSIONS <span class="token operator">=</span><span class="token operator">&gt;</span> 3<span class="token punctuation">}</span>
</code></pre>
<h6><a id="_130"></a>删除列簇</h6>
<pre><code class="prism language-bash">语法:alter <span class="token string">'table_name'</span>, <span class="token punctuation">{</span>NAME <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'column_family1'</span>, METHOD <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'delete'</span><span class="token punctuation">}</span>
示例：将表students中的列簇base_info删除
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:016:0<span class="token operator">&gt;</span> alter <span class="token string">'students'</span>,<span class="token punctuation">{</span>NAME <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'base_info'</span>,METHOD <span class="token operator">=</span><span class="token operator">&gt;</span> <span class="token string">'delete'</span><span class="token punctuation">}</span>
查看是否删除成功：hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:017:0<span class="token operator">&gt;</span> describe <span class="token string">'students'</span>
</code></pre>
<h6><a id="_137"></a>删除表</h6>
<pre><code class="prism language-bash">步骤:
disable（禁用）表
drop（删除）表
示例：将表students删除
disable表:
	hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:019:0<span class="token operator">&gt;</span> disable <span class="token string">'students'</span>
drop表:
	hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:020:0<span class="token operator">&gt;</span> drop <span class="token string">'students'</span>
</code></pre>
<h6><a id="_148"></a>查询表是否存在</h6>
<pre><code class="prism language-bash">语法:exists <span class="token string">'table_name'</span>
示例：查询student表是否存在
	hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:005:0<span class="token operator">&gt;</span> exists <span class="token string">'student'</span>
</code></pre>
<h6><a id="_154"></a>判断表是否禁用</h6>
<pre><code class="prism language-bash">语法:is_disabled <span class="token string">'table_name'</span>
示例：判断student表是否禁用
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:026:0<span class="token operator">&gt;</span> is_disabled <span class="token string">'student'</span>
</code></pre>
<h6><a id="_160"></a>判断表是否启用</h6>
<pre><code class="prism language-bash">示例：判断student表是否启用
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:005:0<span class="token operator">&gt;</span> is_enabled <span class="token string">'student'</span>
</code></pre>
<h3><a id="DML_165"></a>DML（数据操作语言）操作</h3>
<h4><a id="_166"></a>添加数据</h4>
<pre><code class="prism language-bash">语法:
put <span class="token string">'table_name'</span>,<span class="token string">'rowkey'</span>,<span class="token string">'column_family:column'</span>,<span class="token string">'value'</span>,<span class="token string">'timestamp'</span>
示例：为表student添加数据
put <span class="token string">'student'</span>,<span class="token string">'1001'</span>,<span class="token string">'base_info:name'</span>,<span class="token string">'shiny'</span>
</code></pre>
<h4><a id="_173"></a>查询数据</h4>
<h5><a id="_174"></a>扫描整个表</h5>
<ul>
<li>语法<br>
scan ‘table_name’,{COLUMNS =&gt; [ ‘column_family:column’,… ], LIMIT =&gt; num}</li>
<li>示例
<ul>
<li>（1）扫描表student，base_info:name的前2条数据<br>
hbase(main):004:0&gt; scan ‘student’,{COLUMNS =&gt; ‘base_info:name’,LIMIT =&gt; 2}</li>
<li>（2）扫描表student的前2条数据<br>
hbase(main):005:0&gt; scan ‘student’,{LIMIT =&gt; 2}</li>
</ul>
</li>
</ul>
<h5><a id="_182"></a>查询某行记录</h5>
<pre><code class="prism language-bash">语法
（1）根据行键：get <span class="token string">'table_name'</span>,<span class="token string">'rowkey'</span>
（2）根据行键和列簇：get <span class="token string">'table_name'</span>,<span class="token string">'rowkey'</span>,<span class="token string">'column_family'</span>
（3）根据行键、列簇和列：get <span class="token string">'table_name'</span>,<span class="token string">'rowkey'</span>,<span class="token string">'column_family:column'</span>
</code></pre>
<pre><code class="prism language-bash">示例
1）查询表student，行键1001中所有的数据
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:073:0<span class="token operator">&gt;</span> get <span class="token string">'student'</span>,<span class="token string">'1001'</span>
（2）查询表student，行键1001中的course_info下所有的列值
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:001:0<span class="token operator">&gt;</span> get <span class="token string">'student'</span>,<span class="token string">'1001'</span>,<span class="token string">'course_info'</span>
（3）查询表student，行键1001中的base_info下的name值
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:072:0<span class="token operator">&gt;</span> get <span class="token string">'student'</span>,<span class="token string">'1001'</span>,<span class="token string">'course_info:Math'</span>
</code></pre>
<h5><a id="_198"></a>查询表中数据行数</h5>
<pre><code class="prism language-bash">语法
count <span class="token string">'table_name'</span>
示例：查询表student的行数（行键总数）
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:006:0<span class="token operator">&gt;</span> count <span class="token string">'student'</span>
</code></pre>
<h5><a id="_205"></a>修改数据</h5>
<pre><code class="prism language-bash">语法
put <span class="token string">'table_name'</span>,<span class="token string">'rowkey'</span>,<span class="token string">'column_family:column'</span>,<span class="token string">'value'</span> 
示例：将表student，1001中course_info:Math的值修改为100
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:009:0<span class="token operator">&gt;</span> put <span class="token string">'student'</span>,<span class="token string">'1001'</span>,<span class="token string">'course_info:Math'</span>,<span class="token string">'100'</span>
</code></pre>
<h5><a id="_212"></a>查询多个版本数据</h5>
<pre><code class="prism language-bash">（1）显示最新版本的数据
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:072:0<span class="token operator">&gt;</span> get <span class="token string">'student'</span>,<span class="token string">'1001'</span>,<span class="token string">'base_info:name'</span>
（2）通过timestamp来获取对应版本的数据
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:013:0<span class="token operator">&gt;</span> get <span class="token string">'student'</span>,<span class="token string">'1001'</span>,<span class="token punctuation">{</span>COLUMN<span class="token operator">=</span><span class="token operator">&gt;</span><span class="token string">'course_info:Math'</span>,TIMESTAMP <span class="token operator">=</span><span class="token operator">&gt;</span> 1500542224220<span class="token punctuation">}</span>
（3）通过versions来获取多个版本的数据
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:019:0<span class="token operator">&gt;</span> get <span class="token string">'student'</span>,<span class="token string">'1001'</span>,<span class="token punctuation">{</span>COLUMN<span class="token operator">=</span><span class="token operator">&gt;</span><span class="token string">'course_info:Math'</span>,VERSIONS <span class="token operator">=</span><span class="token operator">&gt;</span> 2<span class="token punctuation">}</span>
</code></pre>
<h5><a id="_221"></a>删除数据</h5>
<h6><a id="_222"></a>删除行中某个列值</h6>
<pre><code class="prism language-bash">语法
delete <span class="token string">'table_name'</span>,<span class="token string">'rowkey'</span>,<span class="token string">'column_family:column'</span>,<span class="token string">'timestamp'</span>
示例：删除表student，1001中的course_info:Math的数据（所有版本）
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:015:0<span class="token operator">&gt;</span> delete <span class="token string">'student'</span>,<span class="token string">'1001'</span>,<span class="token string">'course_info:Math'</span>
</code></pre>
<h6><a id="_229"></a>删除行</h6>
<pre><code class="prism language-bash">语法
deleteall <span class="token string">'table_name'</span>,<span class="token string">'rowkey'</span>
示例：删除表student，1001的所有数据
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:018:0<span class="token operator">&gt;</span> deleteall <span class="token string">'student'</span>,<span class="token string">'1001'</span>
</code></pre>
<h6><a id="_236"></a>清空表</h6>
<pre><code class="prism language-bash">语法
truncate <span class="token string">'table_name'</span>
示例：清空表student
hbase<span class="token punctuation">(</span>main<span class="token punctuation">)</span>:020:0<span class="token operator">&gt;</span> truncate <span class="token string">'student'</span>
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
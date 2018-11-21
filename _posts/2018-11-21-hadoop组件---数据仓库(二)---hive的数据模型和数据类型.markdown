---
layout:     post
title:      hadoop组件---数据仓库(二)---hive的数据模型和数据类型
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/q383965374/article/details/79016446				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><a href="http://my.525.life/article?id=1510739741946" rel="nofollow" title="我们">我们</a>在上一章中已经大概了解了Hive有四种数据模型，本章就来详细了解它们的使用。 <br>
<a href="http://my.525.life/article?id=1510739741945" rel="nofollow" title="Hadoop组件---数据仓库(一)---Hive简介">Hadoop组件—数据仓库(一)—Hive简介</a></p>

<h1 id="数据模型">数据模型</h1>

<p>Hive 没有专门的数据存储格式，也没有为数据建立索引，用户可以非常自由的组织 Hive 中的表，只需要在创建表的时候告诉 Hive 数据中的列分隔符和行分隔符，Hive 就可以解析数据。Hive 中所有的数据都存储在 HDFS 中，Hive 中包含以下数据模型：表(Table)，外部表(External Table)，分区(Partition)，桶(Bucket)。</p>

<p>注：如果不指定分割符的话，Hive默认的分隔符是\001</p>

<p>如图所示，表=》分区=》桶，它们的对数据的划分粒度越来越小。 <br>
<img src="http://image.525.life/FrzcZO5BxdS9YBWpF9dHTnfWymkL" alt="" title=""></p>

<h2 id="表">表</h2>

<p>Hive 中的 Table 和数据库中的 Table 在概念上是类似的，每一个 Table 在 Hive 中都有一个相应的目录存储数据。例如，一个表 pvs，它在 HDFS 中的路径为：/wh/pvs，其中，wh 是在 Hive-site.xml 中由 ${Hive.metastore.warehouse.dir} 指定的数据仓库的目录，所有的 Table 数据（不包括 External Table）都保存在这个目录中。</p>

<p>创建表</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">Create</span> <span class="hljs-keyword">table</span> test_tb1 (flied string);</span>
<span class="hljs-operator"><span class="hljs-keyword">Load</span> data <span class="hljs-keyword">local</span> inpath ‘home/Hadoop/test.txt’ <span class="hljs-keyword">into</span> <span class="hljs-keyword">table</span> test_tb1;</span></code></pre>

<h2 id="外部表">外部表</h2>

<p>Table的创建过程和数据加载过程（这两个过程可以在同一个语句中完成），在加载数据的过程中，实际数据会被移动到数据仓库目录中；之后对数据对访问将会直接在数据仓库目录中完成。删除表时，表中的数据和元数据将会被同时删除。</p>

<p>External Table 指向已经在 HDFS 中存在的数据，可以创建 Partition。它和 Table 在元数据的组织上是相同的，而实际数据的存储则有较大的差异。External Table 只有一个过程，加载数据和创建表同时完成（CREATE EXTERNAL TABLE ……LOCATION），实际数据是存储在 LOCATION 后面指定的 HDFS 路径中，并不会移动到数据仓库目录中。当删除一个 External Table 时，仅删除元数据，表中的数据不会真正被删</p>

<p>创建外部表</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">Create</span> <span class="hljs-keyword">external</span> <span class="hljs-keyword">table</span> external_tbl (flied string)
Location  ‘/home/Hadoop/external_table’;</span>
<span class="hljs-operator"><span class="hljs-keyword">Load</span> data <span class="hljs-keyword">local</span> inpath ‘home/Hadoop/test.txt’ <span class="hljs-keyword">into</span> <span class="hljs-keyword">table</span> external_tbl;</span></code></pre>

<p>大家看到了创建外部表时候table之前要加关键字external，同时还要用location命令指定文件存储的路径，如果不使用locaction数据文件也会放置到Hive的数据仓库里。</p>

<p>这两种表在使用的区别主drop命令上，drop是Hive删除表的命令，托管表执行drop命令的时候，会删除元数据和存储的数据，而外部表执行drop命令时候只删除元数据库里的数据，而不会删除存储的数据。另外我还要谈谈表的load命令，Hive加载数据时候不会对元数据进行任何检查，只是简单的移动文件的位置，如果源文件格式不正确，也只有在做查询操作时候才能发现，那个时候错误格式的字段会以NULL来显示。</p>

<h2 id="分区">分区</h2>

<p>Partition 对应于数据库中的 Partition 列的密集索引，但是 Hive 中 Partition 的组织方式和数据库中的很不相同。在 Hive 中，表中的一个 Partition 对应于表下的一个目录，所有的 Partition 的数据都存储在对应的目录中。例如：pvs 表中包含 ds 和 city 两个 Partition，则对应于 ds = 20090801, ctry = US 的 HDFS 子目录为：/wh/pvs/ds=20090801/ctry=US；对应于 ds = 20090801, ctry = CA 的 HDFS 子目录为；/wh/pvs/ds=20090801/ctry=CA <br>
注意：表中可以不包括Partition字段</p>

<p>创建分区</p>



<pre class="prettyprint"><code class=" hljs lua">Create <span class="hljs-built_in">table</span> logs(ts bigint,line <span class="hljs-built_in">string</span>)
Partitioned by (dt <span class="hljs-built_in">string</span>,country <span class="hljs-built_in">string</span>);</code></pre>

<p>加载数据：</p>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-built_in">Local</span> <span class="hljs-built_in">data</span> <span class="hljs-built_in">local</span> inpath ‘/home/Hadoop/par/file01<span class="hljs-built_in">.</span>txt’ <span class="hljs-keyword">into</span> table logs partition (dt<span class="hljs-subst">=</span>’<span class="hljs-number">2018</span><span class="hljs-subst">-</span><span class="hljs-number">01</span><span class="hljs-subst">-</span><span class="hljs-number">09</span>’,country<span class="hljs-subst">=</span>’<span class="hljs-literal">cn</span>’);</code></pre>

<p>在Hive数据仓库里实际存储的路径如下所示：</p>



<pre class="prettyprint"><code class=" hljs avrasm">/user/Hive/warehouse/logs/dt=<span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">09</span>/country=cn/file1<span class="hljs-preprocessor">.txt</span>
/user/Hive/warehouse/logs/dt=<span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">09</span>/country=cn/file2<span class="hljs-preprocessor">.txt</span>
/user/Hive/warehouse/logs/dt=<span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">09</span>/country=us/file3<span class="hljs-preprocessor">.txt</span>
/user/Hive/warehouse/logs/dt=<span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">09</span>/country=us/file4<span class="hljs-preprocessor">.txt</span></code></pre>

<p><a href="http://my.525.life/article?id=1510739741946" rel="nofollow" title="我们">我们</a>看到在表logs的目录下有了两层子目录dt=2018-01-09和country=cn</p>

<p>查询操作：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">Select</span> ts,dt,line  <span class="hljs-keyword">from</span> logs <span class="hljs-keyword">where</span> country=’cn’,</span></code></pre>

<p>这个时候<a href="http://my.525.life/article?id=1510739741946" rel="nofollow" title="我们">我们</a>的查询操作只会扫描file1.txt和file2.txt文件。</p>

<h2 id="桶">桶</h2>

<p>Buckets 对指定列计算 hash，根据 hash 值切分数据，目的是为了并行，每一个 Bucket 对应一个文件。例如将 user 列分散至 32 个 bucket，首先对 user 列的值计算 hash，对应 hash 值为 0 的 HDFS 目录为：/wh/pvs/ds=20180109/ctry=US/part-00000；hash 值为 20 的 HDFS 目录为：/wh/pvs/ds=20180109/ctry=US/part-00020</p>

<p>上面的table和partition都是目录级别的拆分数据，bucket则是对数据源数据文件本身来拆分数据。使用桶的表会将源数据文件按一定规律拆分成多个文件，要使用bucket，<a href="http://my.525.life/article?id=1510739741946" rel="nofollow" title="我们">我们</a>首先要打开Hive对桶的控制，命令如下：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-keyword">set</span> Hive<span class="hljs-preprocessor">.enforce</span><span class="hljs-preprocessor">.bucketing</span> = true</code></pre>

<p>示例： <br>
建临时表student_tmp，并导入数据：</p>



<pre class="prettyprint"><code class=" hljs vbscript">Hive&gt; desc student_tmp;
OK
id      <span class="hljs-built_in">int</span>
age     <span class="hljs-built_in">int</span>
name    <span class="hljs-built_in">string</span>
stat_date       <span class="hljs-built_in">string</span>
<span class="hljs-built_in">Time</span> taken: <span class="hljs-number">0.102</span> seconds
Hive&gt; <span class="hljs-keyword">select</span> * from student_tmp;
OK
<span class="hljs-number">1</span>       <span class="hljs-number">27</span>      zzq     <span class="hljs-number">20180109</span>
<span class="hljs-number">2</span>       <span class="hljs-number">19</span>      ly      <span class="hljs-number">20180109</span>
<span class="hljs-number">3</span>       <span class="hljs-number">26</span>      lmz     <span class="hljs-number">20180109</span>
<span class="hljs-number">4</span>       <span class="hljs-number">27</span>      gsq     <span class="hljs-number">20180109</span>
<span class="hljs-number">5</span>       <span class="hljs-number">27</span>      ysl     <span class="hljs-number">20180109</span>
<span class="hljs-number">6</span>       <span class="hljs-number">26</span>      yys     <span class="hljs-number">20180109</span>
<span class="hljs-number">7</span>       <span class="hljs-number">27</span>      xg      <span class="hljs-number">20180109</span>
<span class="hljs-built_in">Time</span> taken: <span class="hljs-number">0.116</span> seconds</code></pre>

<p>建student表：</p>



<pre class="prettyprint"><code class=" hljs vbnet">Hive&gt;create table student(id INT, age INT, name <span class="hljs-built_in">STRING</span>)
       &gt;partitioned <span class="hljs-keyword">by</span>(stat_date <span class="hljs-built_in">STRING</span>) 
       &gt;clustered <span class="hljs-keyword">by</span>(id) sorted <span class="hljs-keyword">by</span>(age) <span class="hljs-keyword">into</span> <span class="hljs-number">2</span> bucket
       &gt;row format delimited fields terminated <span class="hljs-keyword">by</span> <span class="hljs-comment">',';</span></code></pre>

<p>设置环境变量：</p>



<pre class="prettyprint"><code class=" hljs avrasm">       &gt;<span class="hljs-keyword">set</span> Hive<span class="hljs-preprocessor">.enforce</span><span class="hljs-preprocessor">.bucketing</span> = true<span class="hljs-comment">;</span></code></pre>

<p>插入数据：</p>

<pre><code>   &gt;from student_tmp 
   &gt;insert overwrite table student partition(stat_date="20180203") 
   &gt;select id,age,name where stat_date="20180109" sort by age;
</code></pre>

<p>查看文件目录：</p>



<pre class="prettyprint"><code class=" hljs lasso">$ Hadoop fs <span class="hljs-attribute">-ls</span> /user/Hive/warehouse/studentstat_date<span class="hljs-subst">=</span><span class="hljs-number">20180203</span><span class="hljs-subst">/</span>
Found <span class="hljs-number">2</span> items
<span class="hljs-attribute">-rw</span><span class="hljs-attribute">-r</span><span class="hljs-subst">--</span>r<span class="hljs-subst">--</span>   <span class="hljs-number">1</span> work joe         <span class="hljs-number">31</span> <span class="hljs-number">2018</span><span class="hljs-subst">-</span><span class="hljs-number">01</span><span class="hljs-subst">-</span><span class="hljs-number">09</span> <span class="hljs-number">19</span>:<span class="hljs-number">07</span> /user/Hive/warehouse/student/stat_date<span class="hljs-subst">=</span><span class="hljs-number">20180203</span>/<span class="hljs-number">000000</span>_0
<span class="hljs-attribute">-rw</span><span class="hljs-attribute">-r</span><span class="hljs-subst">--</span>r<span class="hljs-subst">--</span>   <span class="hljs-number">1</span> work joe         <span class="hljs-number">39</span> <span class="hljs-number">2018</span><span class="hljs-subst">-</span><span class="hljs-number">01</span><span class="hljs-subst">-</span><span class="hljs-number">09</span> <span class="hljs-number">19</span>:<span class="hljs-number">07</span> /user/Hive/warehouse/student/stat_date<span class="hljs-subst">=</span><span class="hljs-number">20180203</span>/<span class="hljs-number">000001</span>_0</code></pre>

<p>物理上，每个桶就是表（或分区）目录里的一个文件，桶文件是按指定字段值进行hash，然后除以桶的个数例如上面例子2，最后去结果余数，因为整数的hash值就是整数本身，上面例子里，字段hash后的值还是字段本身，所以2的余数只有两个0和1，所以<a href="http://my.525.life/article?id=1510739741946" rel="nofollow" title="我们">我们</a>看到产生文件的后缀是*0_0和*1_0,文件里存储对应计算出来的元数据。</p>

<p>Hive的桶，我个人认为没有特别的场景或者是特别的查询，<a href="http://my.525.life/article?id=1510739741946" rel="nofollow" title="我们">我们</a>可以没有必要使用，也就是不用开启Hive的桶的配置。因为桶运用的场景有限，一个是做map连接的运算，一个就是取样操作：</p>

<p>查看sampling数据：</p>



<pre class="prettyprint"><code class=" hljs vhdl">Hive&gt; <span class="hljs-keyword">select</span> * from student tablesample(bucket <span class="hljs-number">1</span> <span class="hljs-keyword">out</span> <span class="hljs-keyword">of</span> <span class="hljs-number">2</span> <span class="hljs-keyword">on</span> id);                                                                               
Total MapReduce jobs = <span class="hljs-number">1</span>
Launching Job <span class="hljs-number">1</span> <span class="hljs-keyword">out</span> <span class="hljs-keyword">of</span> <span class="hljs-number">1</span>
.......
OK
<span class="hljs-number">2</span>       <span class="hljs-number">19</span>      ly      <span class="hljs-number">20180109</span>
<span class="hljs-number">6</span>       <span class="hljs-number">26</span>      yys     <span class="hljs-number">20180109</span>
<span class="hljs-number">4</span>       <span class="hljs-number">27</span>      gsq     <span class="hljs-number">20180109</span>
<span class="hljs-typename">Time</span> taken: <span class="hljs-number">18.253</span> seconds</code></pre>

<p>tablesample是抽样语句，语法：TABLESAMPLE(BUCKET x OUT OF y) <br>
y必须是table总bucket数的倍数或者因子。Hive根据y的大小，决定抽样的比例。例如，table总共分了64份，当y=32时，抽取 (64/32=)2个bucket的数据，当y=128时，抽取(64/128=)1/2个bucket的数据。x表示从哪个bucket开始抽取。例 如，table总bucket数为32，tablesample(bucket 3 out of 16)，表示总共抽取（32/16=）2个bucket的数据，分别为第3个bucket和第（3+16=）19个bucket的数据。</p>

<h1 id="数据类型">数据类型</h1>

<p>Hive支持两种数据类型，一类叫原子数据类型，一类叫复杂数据类型。</p>

<h2 id="原子类型">原子类型</h2>

<p>原子数据类型包括数值型、布尔型和字符串类型，具体如下表所示： <br>
<img src="http://image.525.life/FhOKJ6ZOOvfXHUPEGALVGVh7QGpm" alt="" title=""> <br>
由上表<a href="http://my.525.life/article?id=1510739741946" rel="nofollow" title="我们">我们</a>看到Hive不支持日期类型，在Hive里日期都是用字符串来表示的，而常用的日期格式转化操作则是通过自定义函数进行操作。</p>

<p>Hive是用java开发的，Hive里的基本数据类型和java的基本数据类型也是一一对应的，除了string类型。有符号的整数类型：TINYINT、SMALLINT、INT和BIGINT分别等价于java的byte、short、int和long原子类型，它们分别为1字节、2字节、4字节和8字节有符号整数。Hive的浮点数据类型FLOAT和DOUBLE,对应于java的基本类型float和double类型。而Hive的BOOLEAN类型相当于java的基本数据类型boolean。</p>

<p>对于Hive的String类型相当于数据库的varchar类型，该类型是一个可变的字符串，不过它不能声明其中最多能存储多少个字符，理论上它可以存储2GB的字符数。</p>

<p>Hive支持基本类型的转换，低字节的基本类型可以转化为高字节的类型，例如TINYINT、SMALLINT、INT可以转化为FLOAT，而所有的整数类型、FLOAT以及STRING类型可以转化为DOUBLE类型，这些转化可以从java语言的类型转化考虑，因为Hive就是用java编写的。当然也支持高字节类型转化为低字节类型，这就需要使用Hive的自定义函数CAST了。</p>

<h2 id="复杂类型">复杂类型</h2>

<p>复杂数据类型包括数组（ARRAY）、映射（MAP）和结构体（STRUCT），具体如下表所示： <br>
<img src="http://image.525.life/Fjd_CkQ2PvZussf5vUxPM15LEoen" alt="" title=""></p>

<p>下面<a href="http://my.525.life/article?id=1510739741946" rel="nofollow" title="我们">我们</a>看看Hive使用复杂数据类型的实例，建表：</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">Create</span> <span class="hljs-keyword">table</span> complex(col1 ARRAY&lt;<span class="hljs-keyword">INT</span>&gt;,
Col2 MAP&lt;STRING,<span class="hljs-keyword">INT</span>&gt;,
Col3 STRUCT&lt;a:STRING,b :<span class="hljs-keyword">INT</span>,c:<span class="hljs-keyword">DOUBLE</span>&gt;);</span></code></pre>

<p>查询语句：</p>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">Select</span> col1[<span class="hljs-number">0</span>],col2[‘b’],col3.c <span class="hljs-keyword">from</span> complex;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
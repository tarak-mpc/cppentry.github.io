---
layout:     post
title:      HBase ImportTsv导入数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wolf_soul/article/details/52174554				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="一hbase介绍">一、HBase介绍</h4>

<p>HBase是Apache Hadoop中的一个子项目，依托于Hadoop的HDFS作为最基本存储基础单元，通过使用hadoop的命令就可以看到HBase数据存储文件夹的目录结构,还可以通过Map/Reduce对HBase进行操作。HBase是一个适合于非结构化数据存储的数据库.所谓非结构化数据存储就是说HBase是基于列的而不是基于行的模式。HBase是介于Map Entry(key &amp; value)和DB Row之间的一种数据存储方式。你很可能需要存储多个属性的数据结构，但没有传统数据库表中那么多的关联关系，这就是所谓的松散数据。简单来说，你在HBase中的表创建的可以看做是一张很大的表，而这个表的属性可以根据需求去动态增加，在HBase中没有表与表之间关联查询。你只需要告诉你的数据存储到HBase的那个column family就可以了，不需要指定具体类型，需要注意的是HBase中不包含事务的功能。</p>



<h4 id="二importtsv介绍">二、ImportTsv介绍</h4>

<p>将数据导入HBase，通常HBase用户会使用HBase API进行数据导入，但是如果一次性导入大批量数据，可能会占用大量Regionserver资源，影响存储在该Regionserver上其他表的操作，或者性能低下，那么怎么才能高效地将数据导入HBase呢？ <br>
ImportTsv是HBase官方提供了基于mapreduce进行批量数据导入的工具，ImportTsv可以将HDFS上的TSV格式（默认制表符分隔\t，或者自定义分隔符的其他格式数据文件也可，逗号‘,’分隔的txt亲测可以）的数据文件通过命令简单方便地导入到HBase中，对于大数据量的导入非常实用。</p>



<h4 id="三数据导入">三、数据导入</h4>

<p>ImportTsv进行数据导入有两种方式： <br>
   1、直接导入（使用TableOutputFormat在map中插入） <br>
使用方法：</p>

<pre class="prettyprint"><code class=" hljs avrasm">$ bin/hbase org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.ImportTsv</span> -Dimporttsv<span class="hljs-preprocessor">.columns</span>=a,b,c &lt;tablename&gt; &lt;hdfs-inputdir&gt;</code></pre>

<p>其中： <br>
-Dimporttsv.columns即导入的列，例如：-Dimporttsv.columns=cf:col1,cf:col2…  <br>
hdfs-inputdir即数据文件在HDFS的路径，例如：/hbase_tsv_input <br>
如果使用的不是制表符\t分隔，请指定参数：’-Dimporttsv.separator=,’ <br>
命令举例：</p>



<pre class="prettyprint"><code class=" hljs avrasm"> $ bin/hbase org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.ImportTsv</span> -Dimporttsv<span class="hljs-preprocessor">.columns</span>=cf:col1,cf:col2 test /hbase_tsv_input</code></pre>

<p>至此，第一种方式导入数据成功，在控制台会看到执行一些map任务，执行完成之后数据导入完成。</p>

<p>总结：这种方式，使用简单，一条命令搞定，但是在导入大批量数据的时候有可能会存在问题，尤其是column比较多的宽表导入的时候，会出现RegionTooBusyException，导致数据丢失，因此建议在数据量不是特别大并且column不是特别多的情况下使用。</p>

<p>2、  使用bulk-load方式（通过mapreduce先生成HFile格式的文件，再执行CompleteBulkLoad命令将文件move到HBase相应目录。） <br>
使用方法： <br>
第一步：</p>

<pre class="prettyprint"><code class=" hljs avrasm">$ bin/hbase org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.ImportTsv</span> -Dimporttsv<span class="hljs-preprocessor">.columns</span>=a,b,c -Dimporttsv<span class="hljs-preprocessor">.bulk</span><span class="hljs-preprocessor">.output</span>=hdfs://storefile-outputdir &lt;tablename&gt; &lt;hdfs-data-inputdir&gt;</code></pre>

<p>第一步中，在1的基础上添加了-Dimporttsv.bulk.output参数来指定output目录，即HFile的输出目录，此目录在执行前不能存在，如果存在请删除。</p>

<p>第二步：</p>

<pre class="prettyprint"><code class=" hljs avrasm">$ bin/hbase org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.LoadIncrementalHFiles</span> &lt;hdfs://storefileoutput&gt; &lt;tablename&gt;</code></pre>

<p>第二步就是讲HFile move到HBase相应的目录。第一个参数就是第一步中的output目录，tableName和1中相同，就是表名。</p>

<p>总结：bulk-load导入方式完成，等待执行结束即可，第一步可能会花费很长时间（62column1000万数据大约花费20分钟），第二步瞬间完成。这种方式对Regionserver更加友好一些，加载数据几乎不占用Regionserver的计算资源，因为只是在HDFS上生成HFile，移动HFile文件，然后通知HMaster将该Regionserver的一个或多个region上线，大数据量导入推荐使用此方式。</p>



<h4 id="四碰到的问题">四、碰到的问题</h4>

<p>在使用的过程中，可能会出现如下异常：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">Encountered unrecoverable error <span class="hljs-built_in">from</span> region server, additional details: row <span class="hljs-string">' '</span> <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">table</span> <span class="hljs-string">'test'</span></span></code></pre>

<p>出现上面异常的原因是执行命令的用户权限不够： <br>
两种方案： <br>
1.给output目录赋予权限</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-chmod</span> <span class="hljs-attribute">-R</span> <span class="hljs-number">777</span> <span class="hljs-variable">$output</span></code></pre>

<p>2.切换成有权限操做的用户执行。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
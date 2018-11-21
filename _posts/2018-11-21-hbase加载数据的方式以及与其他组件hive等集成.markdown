---
layout:     post
title:      hbase加载数据的方式以及与其他组件hive等集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hbase与其他组件集成">hbase与其他组件集成</h2>



<h3 id="hbase与mapreduce集成">hbase与MapReduce集成</h3>

<ul>
<li>设置HBase、Hadoop环境变量(hbase目录下) <br>
export HBASE_HOME=/opt/modules/hbase-0.98.6-hadoop2 <br>
export HADOOP_HOME=/opt/modules/hadoop-nn  </li>
<li>设置Hadoop_classpath环境变量</li>
</ul>



<pre class="prettyprint"><code class=" hljs bash">HADOOP_CLASSPATH=`<span class="hljs-variable">${HBASE_HOME}</span>/bin/hbase mapredcp`</code></pre>

<ul>
<li>HBase默认集成了一些Mapreduce程序 <br>
<ul><li>启动hdfs，yarn，historyserver</li>
<li>运行rowcounterk</li></ul></li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso">  HADOOP_CLASSPATH<span class="hljs-subst">=</span><span class="hljs-string">`${HBASE_HOME}/bin/hbase mapredcp`</span> <span class="hljs-variable">$HADOOP_HOME</span>/bin/yarn jar <span class="hljs-variable">$HBASE_HOME</span>/lib/hbase<span class="hljs-attribute">-server</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar rowcounter people</code></pre>



<h3 id="hbase的数据迁移及importtsv功能">HBase的数据迁移及importTsv功能</h3>

<p>ImportTsv是HBase官方提供的基于Mapreduce的批量数据导入工具。同时ImportTsv是Hbase提供的一个命令行工具，可以将存储在HDFS上的自定义分隔符（默认\t）的数据文件，通过一条命令方便的导入到HBase表中，对于大数据量导入非常实用 <br>
- 创建数据文件</p>



<pre class="prettyprint"><code class=" hljs ">10001 zhangsan  35
10002 lisi  32
10003 wangwu  29</code></pre>

<ul>
<li>上传到hdfs <br>
bin/hdfs dfs -mkdir -p /user/hbase/importtsv <br>
bin/hdfs dfs -put /opt/student.tsv /user/hbase/importtsv</li>
<li>在hbase上创建student表 <br>
create ‘student’,’info’</li>
<li>开始运行MapReduce</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-constant">HADOOP_CLASSPATH</span>=`<span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/bin/hbase mapredcp`<span class="hljs-symbol">:</span><span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/conf \
<span class="hljs-variable">${</span><span class="hljs-constant">HADOOP_HOME</span>}/bin/yarn jar \
<span class="hljs-variable">${</span><span class="hljs-constant">HBASE_HOME</span>}/lib/hbase-server-<span class="hljs-number">0</span>.<span class="hljs-number">98.6</span>-hadoop2.jar importtsv \
-<span class="hljs-constant">Dimporttsv</span>.columns=<span class="hljs-constant">HBASE_ROW_KEY</span>,<span class="hljs-symbol">info:</span>name,<span class="hljs-symbol">info:</span>age \
student \
<span class="hljs-symbol">hdfs:</span>/<span class="hljs-regexp">/node-1:8020/user</span><span class="hljs-regexp">/hbase/importtsv</span></code></pre>



<h3 id="用bulkload加载数据">用BulkLoad加载数据</h3>

<p>按步骤</p>



<pre class="prettyprint"><code class=" hljs smalltalk">查看所需要的jar包
<span class="hljs-char">${</span><span class="hljs-class">HBASE_HOME</span>} bin/hbase mapredcp
<span class="hljs-number">1.</span>临时设置环境变量
export <span class="hljs-class">HBASE_HOME</span>=/opt/modules/hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.6</span>-hadoop2
export <span class="hljs-class">HADOOP_HOME</span>=/opt/modules/hadoop-nn
export <span class="hljs-class">HADOOP_CLASSPATH</span>=`<span class="hljs-char">${</span><span class="hljs-class">HBASE_HOME</span>}/bin/hbase mapredcp`
注意：任务在那个窗口提交，那么环境变量便在哪个窗口设置
<span class="hljs-number">2.</span>启动<span class="hljs-class">Hadoop</span>的hdfs与yarn的进程
<span class="hljs-char">$ </span>sbin/start-dfs.sh
<span class="hljs-char">$ </span>sbin/start-yarn.sh
<span class="hljs-number">3.</span>启动历史服务器 jobhistoryserver
<span class="hljs-char">$ </span>sbin/mr-jobhistory-daemon.sh start jobhistoryserver
<span class="hljs-number">4.</span>启动zookeeper
<span class="hljs-char">$ </span>bin/zkServer.sh start
启动<span class="hljs-class">HMaster</span>和<span class="hljs-class">HRegionserver</span>
<span class="hljs-char">$ </span>bin/start-hbase.sh


<span class="hljs-number">1.</span>将csv的file文件编程hfile文集那，然后在加载，已处理大数据量的问题
# 表会自动创建，输出目录也会自动创建
<span class="hljs-char">${</span><span class="hljs-class">HADOOP_HOME</span>}/bin/yarn jar \
<span class="hljs-char">${</span><span class="hljs-class">HBASE_HOME</span>}/lib/hbase-server-<span class="hljs-number">0.98</span><span class="hljs-number">.6</span>-hadoop2.jar \
importtsv -<span class="hljs-class">Dimporttsv</span>.bulk.output=/user/hbase/exporthfile \
-<span class="hljs-class">Dimporttsv</span>.columns=<span class="hljs-class">HBASE_ROW_KEY</span>,<span class="hljs-method">t:</span>name,<span class="hljs-method">t:</span>age \
student2 /user/hbase/importtsv
<span class="hljs-number">2.</span>将hfile文件导入到<span class="hljs-class">Hbase</span>表中
<span class="hljs-char">${</span><span class="hljs-class">HADOOP_HOME</span>}/bin/yarn jar \
<span class="hljs-char">${</span><span class="hljs-class">HBASE_HOME</span>}/lib/hbase-server-<span class="hljs-number">0.98</span><span class="hljs-number">.6</span>-hadoop2.jar  completebulkload  \
<span class="hljs-method">hdfs:</span>//node-<span class="hljs-number">1</span>:<span class="hljs-number">8020</span>/user/hbase/exporthfile student2
<span class="hljs-number">3.</span>查看<span class="hljs-class">Hbase</span>中student2表中的数据
hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'student2'</span></code></pre>



<h3 id="hbase和hive集成">hbase和hive集成</h3>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-number">1.</span>设置拷贝的jar包
    <span class="hljs-subst">*</span>使用软连接的方式：
            ln <span class="hljs-attribute">-s</span> 源文件 目标文件
        只会在选定的位置上生成一个文件的镜像，不会占用空间
        相当于windows的一个快捷方式去
export HBASE_HOME<span class="hljs-subst">=</span>/opt/modules/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span>
export HIVE_HOME<span class="hljs-subst">=</span>/opt/modules/hive<span class="hljs-subst">-</span><span class="hljs-number">0.13</span><span class="hljs-number">.1</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.3</span><span class="hljs-number">.6</span>
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/hbase<span class="hljs-attribute">-common</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar  <span class="hljs-variable">$HIVE_HOME</span>/lib/hbase<span class="hljs-attribute">-common</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/hbase<span class="hljs-attribute">-server</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib/base<span class="hljs-attribute">-server</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/hbase<span class="hljs-attribute">-client</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib/hbase<span class="hljs-attribute">-client</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/hbase<span class="hljs-attribute">-protocol</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib/hbase<span class="hljs-attribute">-protocol</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/hbase<span class="hljs-attribute">-it</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-cdh5</span><span class="hljs-number">.3</span><span class="hljs-number">.3</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib/hbase<span class="hljs-attribute">-it</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/htrace<span class="hljs-attribute">-core</span><span class="hljs-subst">-</span><span class="hljs-number">2.04</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib/htrace<span class="hljs-attribute">-core</span><span class="hljs-subst">-</span><span class="hljs-number">2.04</span><span class="hljs-built_in">.</span>jar
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/hbase<span class="hljs-attribute">-hadoop2</span><span class="hljs-attribute">-compat</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib/hbase<span class="hljs-attribute">-hadoop2</span><span class="hljs-attribute">-compat</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/hbase<span class="hljs-attribute">-hadoop</span><span class="hljs-attribute">-compat</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib/hbase<span class="hljs-attribute">-hadoop2</span><span class="hljs-attribute">-compat</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar
ln <span class="hljs-attribute">-s</span> <span class="hljs-variable">$HBASE_HOME</span>/lib/high<span class="hljs-attribute">-scale</span><span class="hljs-attribute">-lib</span><span class="hljs-subst">-</span><span class="hljs-number">1.1</span><span class="hljs-number">.1</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib/hbase<span class="hljs-attribute">-hadoop2</span><span class="hljs-attribute">-compat</span><span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.6</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-built_in">.</span>jar

<span class="hljs-number">2.</span>修改hive<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span>
    <span class="hljs-subst">&lt;</span>property<span class="hljs-subst">&gt;</span>
      <span class="hljs-subst">&lt;</span>name<span class="hljs-subst">&gt;</span>hbase<span class="hljs-built_in">.</span>zookeeper<span class="hljs-built_in">.</span>quorum<span class="hljs-subst">&lt;</span>/name<span class="hljs-subst">&gt;</span>
      <span class="hljs-subst">&lt;</span>value<span class="hljs-subst">&gt;</span>node<span class="hljs-subst">-</span><span class="hljs-number">1</span>,node<span class="hljs-subst">-</span><span class="hljs-number">2</span>,node<span class="hljs-subst">-</span><span class="hljs-number">3</span><span class="hljs-subst">&lt;</span>/value<span class="hljs-subst">&gt;</span>
    <span class="hljs-subst">&lt;</span>/property<span class="hljs-subst">&gt;</span>

<span class="hljs-number">3.</span>在hive中创建表并映射到bbase中的表
    <span class="hljs-number">1.</span>启动Hadoop的hdfs与yarn的进程
    <span class="hljs-number">2.</span>启动zookeeper
    需要启动metastore服务
    $ bin/hive <span class="hljs-subst">--</span>service metastore <span class="hljs-subst">&amp;</span>
    <span class="hljs-number">3.</span>启动hbase</code></pre>

<ul>
<li>示例,在hive中创建hbase表，并与之关联 <br>
hive表被删除，则hbase表也没了</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> hive_hbase_table(
<span class="hljs-keyword">no</span> <span class="hljs-keyword">int</span>,
name string,
age string
)STORED <span class="hljs-keyword">BY</span> <span class="hljs-string">'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</span>
<span class="hljs-keyword">WITH</span> SERDEPROPERTIES (<span class="hljs-string">"hbase.columns.mapping"</span> = <span class="hljs-string">":key,info:name,info:age"</span>)
TBLPROPERTIES (<span class="hljs-string">"hbase.table.name"</span> = <span class="hljs-string">"student3"</span>);</span></code></pre>

<ul>
<li>在hive中创建外部表来关联hbase表使用MapReduce对数据进行清洗，将数据保存到Hbase中 <br>
hive表被删除，不影响hbase表</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">EXTERNAL</span> <span class="hljs-keyword">TABLE</span> hive_hbase_table1(
<span class="hljs-keyword">no</span> <span class="hljs-keyword">int</span>,
name string,
age string
) STORED <span class="hljs-keyword">BY</span> <span class="hljs-string">'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</span>
<span class="hljs-keyword">WITH</span> SERDEPROPERTIES (<span class="hljs-string">"hbase.columns.mapping"</span> = <span class="hljs-string">":key,info:name,info:age"</span>)
TBLPROPERTIES (<span class="hljs-string">"hbase.table.name"</span> = <span class="hljs-string">"student3"</span>);</span></code></pre>



<h3 id="hbase与sqoop集成">hbase与sqoop集成</h3>

<ul>
<li>在mysql创建一张表，插入数据</li>
<li>修改配置文件sqoop-env.sh添加hbase的环境变量</li>
<li>在sqoop的主目录下导入环境变量 <br>
<code>export HBASE_HOME=/opt/modules/hbase-0.98.6-hadoop2</code></li>
<li>将mysql数据导入hbase</li>
</ul>



<pre class="prettyprint"><code class=" hljs haml">bin/sqoop import \
-<span class="ruby">-connect <span class="hljs-symbol">jdbc:</span><span class="hljs-symbol">mysql:</span>/<span class="hljs-regexp">/node-1:3306/test</span> \
</span>-<span class="ruby">-username root \
</span>-<span class="ruby">-password <span class="hljs-number">123456</span> \
</span>-<span class="ruby">-table my_user \
</span>-<span class="ruby">-columns <span class="hljs-string">"id,account,passwd"</span> \
</span>-<span class="ruby">-column-family <span class="hljs-string">"info"</span> \
</span>-<span class="ruby">-hbase-create-table \
</span>-<span class="ruby">-hbase-row-key <span class="hljs-string">"id"</span> \
</span>-<span class="ruby">-hbase-table <span class="hljs-string">"hbasesqoop"</span> \
</span>-<span class="ruby">-num-mappers <span class="hljs-number">1</span> \
</span>-<span class="ruby">-split-by id</span></code></pre>



<h3 id="hbase到hbase表自定义mr">hbase到hbase表(自定义mr)</h3>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-preprocessor">## 实现从将一个hbase的表（student）的数据导入到另一个hbase表（student_copy）</span>
<span class="hljs-preprocessor">## 先在hbase中创建原表student，导入数据</span>
<span class="hljs-preprocessor">## 再创建空表student_copy</span>
<span class="hljs-preprocessor">## 运行代码</span>
使用MapReduce 操作Hbase，复制Hbase中的表
<span class="hljs-preprocessor">####将student表中的数据复制到student_copy表中</span>
<span class="hljs-number">1.</span>Hbase中已存在student表
<span class="hljs-number">2.</span>创建一个student_copy 空的表
    create <span class="hljs-string">'student_copy'</span>,<span class="hljs-string">'info'</span>

<span class="hljs-number">3.</span>编码

import java.io.IOException;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.conf.Configured;
import org.apache.hadoop.hbase.Cell;
import org.apache.hadoop.hbase.CellUtil;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.<span class="hljs-keyword">client</span>.Put;
import org.apache.hadoop.hbase.<span class="hljs-keyword">client</span>.Result;
import org.apache.hadoop.hbase.<span class="hljs-keyword">client</span>.Scan;
import org.apache.hadoop.hbase.io.ImmutableBytesWritable;
import org.apache.hadoop.hbase.mapreduce.TableMapReduceUtil;
import org.apache.hadoop.hbase.mapreduce.TableMapper;
import org.apache.hadoop.hbase.mapreduce.TableReducer;
import org.apache.hadoop.hbase.util.Bytes;
import org.apache.hadoop.io.NullWritable;
import org.apache.hadoop.mapreduce.Job;
import org.apache.hadoop.util.Tool;
import org.apache.hadoop.util.ToolRunner;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">User2StudentMapReduce</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">Configured</span> <span class="hljs-inheritance"><span class="hljs-keyword">implements</span></span> <span class="hljs-title">Tool</span>{</span>

    <span class="hljs-comment">// 使用TableMapper读取HBase表的数据</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ReadUserMapper</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span>
            //这里定义的是<span class="hljs-title">Map</span>的输出
            //<span class="hljs-title">ImmutableBytesWritable</span> -- 》<span class="hljs-title">key</span>   
            //<span class="hljs-title">Put</span>  ---》<span class="hljs-title">Result</span> <span class="hljs-title">row</span>
            <span class="hljs-title">TableMapper</span>&lt;<span class="hljs-title">ImmutableBytesWritable</span>, <span class="hljs-title">Put</span>&gt; {</span>

        <span class="hljs-comment">//读取student表，每行作为一个输入，并取出了rowkey</span>
        <span class="hljs-comment">//key是 表的rowkey      Result row --》是rowkey对应的结果</span>
        <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> map(ImmutableBytesWritable key, Result row,
                Context context) throws IOException, InterruptedException {
            <span class="hljs-comment">//根据rowkey 构建Put对象</span>
            Put put = <span class="hljs-keyword">new</span> Put(key.get());
            <span class="hljs-comment">//Result对象的访问方法rawCells()获取到多个单元格的方法</span>
            Cell[] rawCells = row.rawCells();
            <span class="hljs-keyword">for</span> (Cell cell : rawCells) {
            <span class="hljs-comment">//从每个单元格中判断列簇info 是否存存在，如果存在则取出字段对应的值</span>
                <span class="hljs-keyword">if</span> (<span class="hljs-string">"info"</span>.equals(Bytes.toString(CellUtil.cloneFamily(cell)))) {
                    <span class="hljs-keyword">if</span> (<span class="hljs-string">"name"</span>.equals(Bytes.toString(CellUtil.cloneQualifier(cell)))) {
                        put.add(cell);<span class="hljs-comment">//将info:name列放入put</span>
                        <span class="hljs-comment">// CellUtil.cloneValue(cell)</span>
                        <span class="hljs-comment">// put.add(family, qualifier, value) ;</span>
                    }
                    <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (<span class="hljs-string">"age"</span>.equals(Bytes.toString(CellUtil.cloneQualifier(cell)))) {
                        put.add(cell);<span class="hljs-comment">//将info:age列放入put</span>
                    }<span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span>(<span class="hljs-string">"address"</span>.equals(Bytes.toString(CellUtil.cloneQualifier(cell)))){
                        put.add(cell);<span class="hljs-comment">//将info:address列放入put</span>
                    }
                }
            }
            <span class="hljs-comment">// mapper output</span>
            context.write(key, put);
        }
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WriteStudentReducer</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span>
            <span class="hljs-title">TableReducer</span>&lt;<span class="hljs-title">ImmutableBytesWritable</span>, <span class="hljs-title">Put</span>, <span class="hljs-title">NullWritable</span>&gt; {</span>

        @Override
        <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> reduce(ImmutableBytesWritable key, Iterable&lt;Put&gt; puts,
                Context context) throws IOException, InterruptedException {
            <span class="hljs-keyword">for</span> (Put put : puts) {
                <span class="hljs-comment">// reducer output</span>
                context.write(NullWritable.get(), put);
            }
        }
    }

    <span class="hljs-comment">//运行</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">int</span> run(String[] args) throws Exception {

        Configuration conf = <span class="hljs-keyword">this</span>.getConf();
        Job job = Job.getInstance(conf, <span class="hljs-keyword">this</span>.getClass().getSimpleName());<span class="hljs-comment">//job名任意</span>
        job.setJarByClass(User2StudentMapReduce.class);
        job.setNumReduceTasks(<span class="hljs-number">1</span>); <span class="hljs-comment">//reducer个数</span>

        Scan scan = <span class="hljs-keyword">new</span> Scan();
        scan.setCacheBlocks(<span class="hljs-keyword">false</span>); <span class="hljs-comment">//MR的时候为非热点数据，不需要缓存</span>
        scan.setCaching(<span class="hljs-number">500</span>); <span class="hljs-comment">//每次从服务器端读取的行数</span>

        TableMapReduceUtil.initTableMapperJob(<span class="hljs-string">"student"</span>, <span class="hljs-comment">//输入表</span>
                scan,
                ReadUserMapper.class, <span class="hljs-comment">// mapper class</span>
                ImmutableBytesWritable.class, <span class="hljs-comment">// mapper output key</span>
                Put.class, <span class="hljs-comment">// mapper output value</span>
                job);

        TableMapReduceUtil.initTableReducerJob(<span class="hljs-string">"student_copy"</span>, <span class="hljs-comment">//输出表</span>
                WriteStudentReducer.class, <span class="hljs-comment">// reducer class</span>
                job);

        <span class="hljs-keyword">boolean</span> isSuccess = job.waitForCompletion(<span class="hljs-keyword">true</span>);
        <span class="hljs-keyword">return</span> isSuccess ? <span class="hljs-number">0</span> : <span class="hljs-number">1</span>;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> main(String[] args) throws Exception {
        Configuration conf = HBaseConfiguration.create();
        <span class="hljs-keyword">int</span> status = ToolRunner.run(conf, <span class="hljs-keyword">new</span> User2StudentMapReduce(), args);
        System.exit(status);
    }
}
<span class="hljs-number">4.</span><span class="hljs-comment">//引入环境classpath</span>
 export HBASE_HOME=/opt/modules/hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.6</span>-hadoop2
 export HADOOP_HOME=/opt/modules/hadoop-<span class="hljs-number">2.5</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.3</span><span class="hljs-number">.6</span>
 export HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase mapredcp`
<span class="hljs-number">5.</span>执行
<span class="hljs-preprocessor">#######将工程导出jar文件</span>
执行
$HADOOP_HOME/bin/yarn jar /opt/h.jar
查看结果
<span class="hljs-number">6.</span>bin/hbase shell
    scan <span class="hljs-string">'student_copy'</span></code></pre>



<h3 id="hbase与hue集成">hbase与hue集成</h3>



<pre class="prettyprint"><code class=" hljs vhdl">[hbase]
  # Comma-separated list <span class="hljs-keyword">of</span> HBase Thrift servers <span class="hljs-keyword">for</span> clusters <span class="hljs-keyword">in</span> the format <span class="hljs-keyword">of</span> '(name|host:<span class="hljs-keyword">port</span>)'.
  # <span class="hljs-keyword">Use</span> full hostname <span class="hljs-keyword">with</span> security.
  hbase_clusters=(Cluster|bigdata.com:<span class="hljs-number">9090</span>)

  # HBase <span class="hljs-keyword">configuration</span> directory, where hbase-site.xml <span class="hljs-keyword">is</span> located.
  hbase_conf_dir=/opt/modules/hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.6</span>-hadoop2/conf
<span class="hljs-number">2</span>、启动HBase的thrift
$ bin/hbase-daemon.sh start thrift

<span class="hljs-number">3</span>、启动Hue进程,并访问
$ build/env/bin/supervisor
http://bigdata-hive:<span class="hljs-number">8888</span></code></pre>



<h3 id="namespace">namespace</h3>

<p>类似数据库，每一个namespace可以存储表 <br>
- 创造一个namespace <br>
create namespace ‘ns2’ <br>
- 查看ns2命名空间下有哪些表 <br>
list_namespace_tables ‘ns2’ <br>
- 查看描述信息 <br>
describe_namespace ‘ns2’ <br>
- 删除命名空间，命名空间中没有表才能将其删除 <br>
drop_namespace ‘ns2’</p>



<h5 id="在指定的命名空间中创造一张表">在指定的命名空间中创造一张表</h5>

<ul>
<li>create ‘ns2:t11’,’f1’ <br>
在默认的命名空间下创建一张t2表，列簇为f1，版本号1，有效期为2592000 ，启用缓存 <br>
create ‘t2’, {NAME =&gt; ‘f1’, VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt; true}</li>
<li>删除表 <br>
disable ‘ns2:t11’ <br>
drop ‘ns2:t11’</li>
</ul>



<h3 id="创建预分区">创建预分区</h3>

<ul>
<li>创建预分区表 <br>
<code>create 't1','f1',SPLITS=&gt;['10','20','30','40']</code></li>
<li>使用16进制字符生成分区 <br>
<code>create 't2', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit'}</code></li>
<li>向分区表插入数据</li>
<li>插入数据后可以在web页面查看 <br>
在此页面<a href="http://node-1:60010/master-status" rel="nofollow">http://node-1:60010/master-status</a></li>
</ul>



<h3 id="scan-扩展区间查询">scan 扩展，区间查询</h3>

<ul>
<li>从指定row key开始查询指定数量，数量不足则查最多数量 <br>
scan ‘student’, {LIMIT =&gt; 10, STARTROW =&gt; ‘10001’}</li>
<li>在指定区间查找前几条，不查询stoprow所在的行 <br>
scan ‘student’, {LIMIT =&gt; 2, STARTROW =&gt; ‘10001’,STOPROW=&gt;’10004’}</li>
<li>查询指定字段 <br>
scan ‘user’, {COLUMNS =&gt; [‘info:name’, ‘info:age’], LIMIT =&gt; 10, STARTROW =&gt; ‘100010005’}</li>
</ul>



<h3 id="使用通配符">使用通配符</h3>

<p>scan ‘use’, {LIMIT =&gt; 100, STARTROW =&gt; ‘201801*’,STOPROW=&gt;’201812*’} <br>
查询2018年1月到11月的100条信息</p>



<h3 id="scan使用filter">scan使用filter</h3>

<p>binary是 等于 <br>
substring 是 含有就可以 <br>
- 在use表中查询，过滤字段值为zhangsan的数据 <br>
<code>scan 'use', FILTER=&gt;"ValueFilter(=,'binary:zhangsan')"</code> <br>
- 查询  字段值包含32的值 <br>
<code>scan 'use', FILTER=&gt;"ValueFilter(=,'substring:32')"</code> <br>
- 通过查询user表，并且指定要过滤的字段为name,和name的值包含10005，的数据 <br>
<code>scan 'use', FILTER=&gt;"ColumnPrefixFilter('name') AND ValueFilter(=,'substring:zhangsan')"</code> <br>
- 查询user表，指定过滤name字段，字段值包含321或者232的数据 <br>
<code>scan 'use', FILTER=&gt;"ColumnPrefixFilter('name') AND (ValueFilter(=,'substring:321') OR ValueFilter(=,'substring:232'))"</code></p>



<h3 id="count">count</h3>

<ul>
<li>统计一个表的行数 <br>
count ‘use’ <br>
count ‘ns1:use’</li>
</ul>



<h2 id="hbase-与-hive实例">hbase 与 hive实例</h2>

<ul>
<li>创造hbase表 <br>
create ‘userTelphone’,’info’</li>
<li>加载数据，这里手动加入一些</li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937646_20151001082013'</span>,<span class="hljs-string">'info:area'</span>,<span class="hljs-string">'shanghai'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937646_20151001082053'</span>,<span class="hljs-string">'info:area'</span>,<span class="hljs-string">'shanghai'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937646_20151001082013'</span>,<span class="hljs-string">'info:active'</span>,<span class="hljs-string">'zhujiao'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937646_20151024092018'</span>,<span class="hljs-string">'info:area'</span>,<span class="hljs-string">'shanghai'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937646_20151024092018'</span>,<span class="hljs-string">'info:active'</span>,<span class="hljs-string">'zhujiao'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937646_20151227092018'</span>,<span class="hljs-string">'info:area'</span>,<span class="hljs-string">'shanghai'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937646_20151227092018'</span>,<span class="hljs-string">'info:active'</span>,<span class="hljs-string">'zhujiao'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937648_20151124092018'</span>,<span class="hljs-string">'info:area'</span>,<span class="hljs-string">'shanghai'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'userTelphone'</span>,<span class="hljs-string">'182600937648_20151124092018'</span>,<span class="hljs-string">'info:active'</span>,<span class="hljs-string">'zhujiao'</span></code></pre>

<ul>
<li>创造hive表保存最终结果</li>
</ul>



<pre class="prettyprint"><code class=" hljs vbnet">CREATE  TABLE hive_hbase_res(
telphone <span class="hljs-built_in">string</span>,
teltime <span class="hljs-built_in">string</span>,
area <span class="hljs-built_in">string</span>,
active <span class="hljs-built_in">string</span>,
phone <span class="hljs-built_in">string</span>,
talktime <span class="hljs-built_in">string</span>,
mode <span class="hljs-built_in">string</span>,
price <span class="hljs-built_in">string</span>)
row format delimited fields terminated <span class="hljs-keyword">by</span> <span class="hljs-comment">'\t';</span></code></pre>

<ul>
<li>创造外部表映射到hbase中的表</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">EXTERNAL</span> <span class="hljs-keyword">TABLE</span> Tel_hbase_ext(
telphone_teltime string ,
area string ,
active string
) STORED <span class="hljs-keyword">BY</span> <span class="hljs-string">'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</span>
<span class="hljs-keyword">WITH</span> SERDEPROPERTIES (<span class="hljs-string">"hbase.columns.mapping"</span> = <span class="hljs-string">":key,info:area,info:active"</span>)
TBLPROPERTIES (<span class="hljs-string">"hbase.table.name"</span> = <span class="hljs-string">"|"</span>);</span></code></pre>

<ul>
<li>和hive处理日志的步骤差不多</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">insert</span> overwrite <span class="hljs-keyword">table</span> hive_hbase_res1
<span class="hljs-keyword">select</span> split(telphone_teltime,<span class="hljs-string">"_"</span>)[<span class="hljs-number">0</span>],split(telphone_teltime,<span class="hljs-string">"_"</span>)[<span class="hljs-number">1</span>] <span class="hljs-keyword">from</span> Tel_hbase_ext;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
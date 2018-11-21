---
layout:     post
title:      HBase 相关API操练(三)：MapReduce操作HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎大家转载交流，但必须在博文明显位置标明转载地址。否则维权必究！					https://blog.csdn.net/py_123456/article/details/80644214				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="mapreduce-操作-hbase">MapReduce 操作 HBase</h2>

<p>在 HBase 系统上运行批处理运算，最方便和实用的模型依然是 MapReduce，如下图所示。</p>

<p>HBase Table 和 Region 的关系类似 HDFS File 和 Block 的关系，HBase提供配套的 TableInputFormat 和 TableOutputFormat API，可以方便地将 HBase Table 作为 Hadoop MapReduce 的Source 和 Sink。对于 MapReduce Job 应用开发人员来说，基本不需要关注 HBase 系统本身的细节。</p>

<p>org.apache.hadoop.hbase.mapreduce 包中的类和工具用来将 HBase 作为 MapReduce 作业的输出</p>

<p><strong>1、 HBaseConfiguration 类</strong></p>

<pre class="prettyprint"><code class=" hljs avrasm"> org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HbaseConfiguration</span></code></pre>

<p>继承了 org.apache.hadoop.conf.Configuration 类， 创建一个 HBaseConfiguration 对象实例，会返回读入 CLASSPATH 下 hbase-site.xml 文件和hbase-default.xml 文件中 HBase 配置信息的一个 Configuration，该Configuration 接下来会用于 创建 HBaseAdmin 和 HTable 实例。</p>

<p>HBaseAdmin 和 HTable 两个类在 org.apache.hadoop.hbase.client 包中，HBaseAdmin 用于管理 HBase 集群、添加和删除表，HTable 用于访问指定的表，Configuration 实例指向了执行这些代码的集群上的这些类。</p>

<p><strong>2、 HtableDescriptor 类和 HColumnDescriptor 类</strong></p>

<p>HBase 中表结构由 HTableDescriptor 描述（包括 HColumnDescriptor），对表的新增、修改、删除操作在接口 HMasterInterface 中定义，而该接口由 HMaster 实现。</p>

<p>HTabledescriptor包含：</p>

<p>1)表名、byte[] 和 String 格式。</p>

<p>2)表的元信息，以Key-Value形式存储。存储信息包括文件最大的大小（默认256MB）、是否只读、Flush 时内存占用大小（默认64MB）、是否 root 或 Meta Region、DEFERRED_LOG_FLUSH。</p>

<p>3)表的各 Family 描述 HColumnDescriptor。</p>

<p>HColumnDescriptor 描述 Column Family 的信息，包括：</p>

<p>1)压缩格式（不压缩或仅压缩 Value、压缩 Block 中的一系列记录）。</p>

<p>2)数据的版本数量。</p>

<p>3)Block 的大小。</p>

<p>4)是否在内存中。</p>

<p>5)是否 Cache Block。</p>

<p>6)是否使用 bloomfilter。</p>

<p>7)Cell 内容的存活时间。</p>

<p>8)是否复制。</p>

<p>当一个 Column Family 创建后，其参数不能修改，除非删除该 Column Family 后新建一个， 但删除 Column Family 也会删除该 Column Family 下的数据。另外，HTableDescriptor 中包含 ROOT_TABLEDESC 和 META_TABLEDESC 两个实例以描述-ROOT- 和 .META 表。</p>

<p>1)ROOT_TABLEDESC 包含一个 info 的 Column Family。</p>

<p>2)META_TABLEDESC 包含一个 info 和 historian，两个 Column Family。</p>

<p><strong>3、 CreateTable 方法</strong></p>

<p>如果指定 Split Key 为该 Table 按指定键初始创建多个 Region，否则仅创建一个 Region，过程如下。</p>

<p>1) 为 Table 创建 HRegionInfo</p>

<p>2) 判断是不是所有的 Meta Region 都 online（由 RegionManager 的 MetaScanner 扫描线程分配 Meta Region）。</p>

<p>3) 判断 ServerManager 是否有足够 Region Server 来创建 table。</p>

<p>从 RegionManager 的 online MetaRegion 查找该 HRegionInfo 应放入哪一个 MetaRegion 中，在 onlineMetaRegion 中查找仅比RegionName 小的 MetaRegion，而RegionName 由 tableName、起始 Key 和 regionId（root 为0，meta 为1，user 当前时间）组成，同时 Master 的 ServerConnection 获取 HRegionInterface 代理连接到该 MetaRegion，并查找对应该 Table 为 Key 的记录是否存在，若存在则报错该表已经存在，由 RegionManager 根据 HRegionInfo 创建新的 user。Region 在 rootDir 目录下 新建以 tableName 为名的目录，在 tableName 目录下新建一个 Region 的目录（经编码后的 RegionName），并新建一个 HRegion 对象。</p>

<p>disable、enable 和 delete 等操作封装在继承自 TableOperation 的类中，该类先获得要操作表的所有 MetaRegion，扫描这些 MetaRegion 中 所有该表的 user Region 信息并做相应处理，最后处理 MetaRegion。</p>

<p><strong>4、 TableInputFormat 类</strong></p>

<p>通过设置 conf.set(TableInputFormat.INPUT_TABLE,”tableName”) 设定 HBase 的输入表，tableName 为表名。</p>

<p>设置 conf.set(TableInputFormat.SCAN,TableMRUtil.convertScanToString(scan))，设定对 HBase 输入表的 scan 方式。</p>

<pre class="prettyprint"><code class=" hljs vhdl">setTable(<span class="hljs-keyword">new</span> HTable(<span class="hljs-keyword">new</span> <span class="hljs-keyword">Configuration</span>(conf),tableName));</code></pre>

<p>通过 TableInputFormat.setConf(Configuration conf) 方法初始化 scan 对象；scan 对象是从 Job 中设置的对象，以字符串的形式传给 TableInputFormat，在 TableInputFormat 内部将 scan 字符串转换为 scan 对象，操作如下。</p>

<pre class="prettyprint"><code class=" hljs avrasm">scan = TableMapReduceUtil<span class="hljs-preprocessor">.convertStringtoScan</span>(conf<span class="hljs-preprocessor">.get</span>(SCAN))<span class="hljs-comment">;</span></code></pre>

<p>TableInputFormat 继承 TableInputFormatBase 实现了 InputFormat 抽象类的两个抽象方法 getSplits() 和 createRecordReader()。 <br>
        getSplits() 判定输入对象的切分原则，原则是对于 TableInputFormatBase，会遍历HBase 相应表的所有 HRegion，每一个 HRegion 都会被切分成一个 Split，所以切分的块数与表中 HRegion 的数目是相同的，代码如下。</p>

<p>InputSplit split = new TableSplit(table.getTableName(),splitStart,splitStop,regionLocation);  <br>
   在 Split 中只会记载 HRegion 的起始 Row Key 和 结束Row Key，具体去读取这片区域的数据是 createRecordReader() 实现的。</p>

<p>对于一个 Map 任务，JobTracker 会考虑 TaskTracker 的网络位置，并选取一个距离其输入分片文件的最近的 TaskTracker。在理想情况下，任务是数据本地化的（data-local）， 也就是任务运行在输入分片所在的节点上。同样，任务也可能是机器本地化的；任务和输入分片在同一个机架，但不在同一个节点上。</p>

<p>对于Reduce 任务，JobTracker 简单地从待运行的 Reduce 任务列表中选取下一个来运行，用不着考虑数据段本地化。</p>

<p>createRecordReader() 按照必然格式读取响应数据，接受 Split 块，返回读取记录的结果，操作代码如下。</p>

<pre class="prettyprint"><code class=" hljs avrasm">public RecordReader&lt; ImmutableBytesWritable,Result&gt; createRecordReader(InputSplit split,TaskAttemptContext context){
　　Scan scan = new Scan(this<span class="hljs-preprocessor">.scan</span>)<span class="hljs-comment">;</span>
　　scan<span class="hljs-preprocessor">.setStartRow</span>(tableSplit<span class="hljs-preprocessor">.getStartRow</span>())<span class="hljs-comment">;</span>
　　scan<span class="hljs-preprocessor">.setStopRow</span>(tableSplit<span class="hljs-preprocessor">.getEndRow</span>())<span class="hljs-comment">;</span>
　　tableRecorderReader<span class="hljs-preprocessor">.setScan</span>(scan)<span class="hljs-comment">;</span>
　　tableRecorderReader<span class="hljs-preprocessor">.setHTable</span>(table)<span class="hljs-comment">;</span>
　　tableRecorderReader<span class="hljs-preprocessor">.init</span>()<span class="hljs-comment">;</span>
　　return tableRecorderReader<span class="hljs-comment">;</span>
}</code></pre>

<p>　　tableRecorderReader.init() 返回的是这个分块的起始 Row Key 的记录；RecordReader 将一个 Split 解析成 &lt; Key, Value&gt; 的形式 提供给 map 函数，Key 就是Row Key，Value 就是对应的一行数据。</p>

<p>RecorderReader 用于在划分中读取 &lt; Key，Value&gt; 对。RecorderReader 有5 个虚方法，下面分别进行介绍。</p>

<p>1、initialize：初始化，输入参数包括该 Reader 工作的数据划分 InputSplit 和 Job 的上下文 context。</p>

<p>2、nextKey：得到输入的下一个　Key，如果数据划分已经没有新的记录，返回空。</p>

<p>3、nextValue：得到 Key 对应的 Value，必须在调用 nextKey 后调用。</p>

<p>4、 getProgress：得到现在的进度。</p>

<p>5、close：来自 java.io 的Closeable 接口，用于清理 RecorderReader。</p>

<p>在 MapReduce 驱动中调用 TableInputFormat 的类：</p>

<pre class="prettyprint"><code class=" hljs avrasm"> job<span class="hljs-preprocessor">.setInputFormatClass</span>(TableInputFormat<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>
　　使用以下方法就不需要再单独定义。

　tableMapReduceUtil<span class="hljs-preprocessor">.initTableReducerJob</span>(<span class="hljs-string">"daily_result"</span>, DailyReduce<span class="hljs-preprocessor">.class</span>, job)<span class="hljs-comment">;</span>
　　initTableReducerJob() 方法完成一系列操作。</code></pre>

<p>1) job.setOutputFormatClass(TableOutputFormat.class);设置输出格式。 <br>
2)conf.set(TableOutputFormat.OUTPUT_TABLE,table);设置输出表。 <br>
3) 初始化 partition。</p>

<h2 id="向-hbase-中写入数据">向 HBase 中写入数据</h2>

<p>本节介绍利用 MapReduce 操作 HBase，首先介绍如何上传数据，借助最熟悉的 WordCount 案例，将 WordCount 的结果存储到 HBase 而不是 HDFS。</p>

<p>输入文件 test.txt 的内容为：</p>

<pre class="prettyprint"><code class=" hljs cs">hello hadoop
hadoop <span class="hljs-keyword">is</span> easy</code></pre>

<p><strong>1)      编写 Mapper 函数</strong>。</p>

<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountMapperHbase</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">Mapper</span>&lt; <span class="hljs-title">Object</span>, <span class="hljs-title">Text</span>, <span class="hljs-title">ImmutableBytesWritable</span>, <span class="hljs-title">IntWritable</span>&gt; {</span>
               <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">static</span> IntWritable one = <span class="hljs-keyword">new</span> IntWritable(<span class="hljs-number">1</span>);
               <span class="hljs-keyword">private</span> Text word = <span class="hljs-keyword">new</span> Text();

               <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> map(Object key, Text value, Context context) throws IOException, InterruptedException {
                        StringTokenizer itr = <span class="hljs-keyword">new</span> StringTokenizer(value.toString());
                        <span class="hljs-keyword">while</span> (itr.hasMoreTokens()) {
                                word.set(itr.nextToken());
                                <span class="hljs-comment">//输出到hbase的key类型为ImmutableBytesWritable</span>
                                context.write(<span class="hljs-keyword">new</span> ImmutableBytesWritable(Bytes.toBytes(word.toString())), one);
                        }
               }
       }</code></pre>

<p><strong>2)      编写 Reducer 类</strong>。</p>

<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">WordCountReducerHbase</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">TableReducer</span>&lt; <span class="hljs-title">ImmutableBytesWritable</span>, <span class="hljs-title">IntWritable</span>, <span class="hljs-title">ImmutableBytesWritable</span>&gt; {</span>  
               <span class="hljs-keyword">private</span> IntWritable result = <span class="hljs-keyword">new</span> IntWritable();
        <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> reduce(ImmutableBytesWritable key, Iterable&lt; IntWritable&gt; values, Context context)  
                throws IOException, InterruptedException {  
                <span class="hljs-keyword">int</span> <span class="hljs-keyword">sum</span> = <span class="hljs-number">0</span>;
                        <span class="hljs-keyword">for</span> (IntWritable val : values) {
                                <span class="hljs-keyword">sum</span> += val.get();
                        }
                        Put put = <span class="hljs-keyword">new</span> Put(key.get());<span class="hljs-comment">//put 实例化  key代表主键，每个单词存一行</span>
                        <span class="hljs-comment">//三个参数分别为  列簇为content，列修饰符为count，列值为词频</span>
                        put.add(Bytes.toBytes(<span class="hljs-string">"content"</span>), Bytes.toBytes(<span class="hljs-string">"count"</span>), Bytes.toBytes(String.valueOf(<span class="hljs-keyword">sum</span>)));
                        context.write(key , put);
        }  
    }</code></pre>

<p><strong>3)      编写驱动类。</strong></p>

<pre class="prettyprint"><code class=" hljs avrasm">import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.StringTokenizer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HColumnDescriptor</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HTableDescriptor</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.HBaseAdmin</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Put</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.ImmutableBytesWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.TableMapReduceUtil</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.TableReducer</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IntWritable</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.Text</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Job</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.Mapper</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.mapreduce</span><span class="hljs-preprocessor">.lib</span><span class="hljs-preprocessor">.input</span><span class="hljs-preprocessor">.FileInputFormat</span><span class="hljs-comment">;</span>
<span class="hljs-comment">/**
 * 
 *  将hdfs中的数据导入hbase
 *
 */</span>
public class MapReduceWriteHbaseDriver {
public static void main(String[] args)throws Exception {  
               String tableName = <span class="hljs-string">"wordcount"</span><span class="hljs-comment">;//hbase 数据库表名</span>
        Configuration conf=HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">; //实例化Configuration </span>
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"dajiangtai"</span>)<span class="hljs-comment">; </span>
           conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.property.clientPort"</span>, <span class="hljs-string">"2181"</span>)<span class="hljs-comment">;//端口号</span>

           //如果表已经存在就先删除
           HBaseAdmin admin = new HBaseAdmin(conf)<span class="hljs-comment">;</span>
           if(admin<span class="hljs-preprocessor">.tableExists</span>(tableName)){
                admin<span class="hljs-preprocessor">.disableTable</span>(tableName)<span class="hljs-comment">;</span>
                admin<span class="hljs-preprocessor">.deleteTable</span>(tableName)<span class="hljs-comment">;</span>
           }

           HTableDescriptor htd = new HTableDescriptor(tableName)<span class="hljs-comment">;</span>
           HColumnDescriptor hcd = new HColumnDescriptor(<span class="hljs-string">"content"</span>)<span class="hljs-comment">;</span>
           htd<span class="hljs-preprocessor">.addFamily</span>(hcd)<span class="hljs-comment">;//创建列簇</span>
           admin<span class="hljs-preprocessor">.createTable</span>(htd)<span class="hljs-comment">;//创建表</span>

       Job job=new Job(conf,<span class="hljs-string">"import from hdfs to hbase"</span>)<span class="hljs-comment">;  </span>
        job<span class="hljs-preprocessor">.setJarByClass</span>(MapReduceWriteHbaseDriver<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;  </span>

        job<span class="hljs-preprocessor">.setMapperClass</span>(WordCountMapperHbase<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;  </span>

        //设置插入hbase时的相关操作
        TableMapReduceUtil<span class="hljs-preprocessor">.initTableReducerJob</span>(tableName, WordCountReducerHbase<span class="hljs-preprocessor">.class</span>, job, null, null, null, null, false)<span class="hljs-comment">;</span>

        job<span class="hljs-preprocessor">.setMapOutputKeyClass</span>(ImmutableBytesWritable<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;  </span>
        job<span class="hljs-preprocessor">.setMapOutputValueClass</span>(IntWritable<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;  </span>

        job<span class="hljs-preprocessor">.setOutputKeyClass</span>(ImmutableBytesWritable<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;  </span>
       job<span class="hljs-preprocessor">.setOutputValueClass</span>(Put<span class="hljs-preprocessor">.class</span>)<span class="hljs-comment">;</span>

        FileInputFormat<span class="hljs-preprocessor">.addInputPaths</span>(job, <span class="hljs-string">"hdfs://master:9000/handoop/test/test.txt"</span>)<span class="hljs-comment">;  </span>
        System<span class="hljs-preprocessor">.exit</span>(job<span class="hljs-preprocessor">.waitForCompletion</span>(true) ? <span class="hljs-number">0</span> : <span class="hljs-number">1</span>)<span class="hljs-comment">;  </span>

    }
}</code></pre>

<p>从hbase读取数据，输出到hdfs的数据如下所示。</p>

<pre class="prettyprint"><code class=" hljs cs">easy    <span class="hljs-number">1</span>
hadoop  <span class="hljs-number">2</span>
hello   <span class="hljs-number">1</span>
<span class="hljs-keyword">is</span>      <span class="hljs-number">1</span></code></pre>

<p><strong>以上就是博主为大家介绍的这一板块的主要内容，这都是博主自己的学习过程，希望能给大家带来一定的指导作用，有用的还望大家点个支持，如果对你没用也望包涵，有错误烦请指出。如有期待可关注博主以第一时间获取更新哦，谢谢</strong>！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
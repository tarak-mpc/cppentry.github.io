---
layout:     post
title:      HBASE(五 MapReduce)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hr787753/article/details/78521346				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Hbase 也可以做一些MapReduce操作 </p>

<p>Hbase的MaprReuce 无非三种 ：</p>

<ol>
<li>HDFS 中的数据   成为 Hbase 的某个表的某一列</li>
<li>HBase中的某一列 成为HDFS 中的数据</li>
<li>HBase某一表某列  加工  流入   HBase另一表中某列 </li>
</ol>

<p>实现Demo如下 ：</p>

<p><strong>1.创建两个表  插入模板数据</strong> </p>



<pre class="prettyprint"><code class=" hljs avrasm">public class HbaseMR {

    private static Configuration conf<span class="hljs-comment">;</span>
    private static Connection conn<span class="hljs-comment">;</span>
    private static Admin admin<span class="hljs-comment">;</span>

    static{
        conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>,<span class="hljs-string">"hadoop01:2181,hadoop02:2181,hadoop03:2181"</span>)<span class="hljs-comment">;</span>
        try {
          conn = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(conf)<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
    }


    public static void initTable(){
        try {
            //创建两个表
            admin = conn<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>
            HTableDescriptor word = new HTableDescriptor(TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"word"</span>))<span class="hljs-comment">;</span>
            HTableDescriptor stat = new HTableDescriptor(TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"stat"</span>))<span class="hljs-comment">;</span>
            HColumnDescriptor content = new HColumnDescriptor(<span class="hljs-string">"content"</span>)<span class="hljs-comment">;</span>
            word<span class="hljs-preprocessor">.addFamily</span>(content)<span class="hljs-comment">;</span>
            stat<span class="hljs-preprocessor">.addFamily</span>(content)<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.createTable</span>(word)<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.createTable</span>(stat)<span class="hljs-comment">;</span>
            //初始化第一个表的数据
            Table table = conn<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"word"</span>))<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.setAutoFlushTo</span>(false)<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.setWriteBufferSize</span>(<span class="hljs-number">5</span>)<span class="hljs-comment">;</span>
            List&lt;Put&gt; lp = new ArrayList&lt;Put&gt;()<span class="hljs-comment">;</span>
            Put p1 = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"1"</span>))<span class="hljs-comment">;</span>
            p1<span class="hljs-preprocessor">.add</span>(<span class="hljs-string">"content"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"info"</span><span class="hljs-preprocessor">.getBytes</span>(), (<span class="hljs-string">"The Apache Hadoop software library is a framework"</span>)<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            lp<span class="hljs-preprocessor">.add</span>(p1)<span class="hljs-comment">;</span>
            Put p2 = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"2"</span>))<span class="hljs-comment">;</span>
            p2<span class="hljs-preprocessor">.add</span>(<span class="hljs-string">"content"</span><span class="hljs-preprocessor">.getBytes</span>(),<span class="hljs-string">"info"</span><span class="hljs-preprocessor">.getBytes</span>(),(<span class="hljs-string">"The common utilities that support the other Hadoop modules"</span>)<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            lp<span class="hljs-preprocessor">.add</span>(p2)<span class="hljs-comment">;</span>
            Put p3 = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"3"</span>))<span class="hljs-comment">;</span>
            p3<span class="hljs-preprocessor">.add</span>(<span class="hljs-string">"content"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"info"</span><span class="hljs-preprocessor">.getBytes</span>(),(<span class="hljs-string">"Hadoop by reading the documentation"</span>)<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            lp<span class="hljs-preprocessor">.add</span>(p3)<span class="hljs-comment">;</span>
            Put p4 = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"4"</span>))<span class="hljs-comment">;</span>
            p4<span class="hljs-preprocessor">.add</span>(<span class="hljs-string">"content"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"info"</span><span class="hljs-preprocessor">.getBytes</span>(),(<span class="hljs-string">"Hadoop from the release page"</span>)<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            lp<span class="hljs-preprocessor">.add</span>(p4)<span class="hljs-comment">;</span>
            Put p5 = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"5"</span>))<span class="hljs-comment">;</span>
            p5<span class="hljs-preprocessor">.add</span>(<span class="hljs-string">"content"</span><span class="hljs-preprocessor">.getBytes</span>(), <span class="hljs-string">"info"</span><span class="hljs-preprocessor">.getBytes</span>(),(<span class="hljs-string">"Hadoop on the mailing list"</span>)<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            lp<span class="hljs-preprocessor">.add</span>(p5)<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.put</span>(lp)<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.flushCommits</span>()<span class="hljs-comment">;</span>
        } catch (Exception e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
    }
    }</code></pre>

<p>**2.Mapper 要继承TableMapper</p>

<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HbaseMapper</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">TableMapper</span>&lt;<span class="hljs-title">Text</span>,<span class="hljs-title">IntWritable</span>&gt; {</span>

    @Override
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> map(ImmutableBytesWritable key, Result value, Context context) throws IOException, InterruptedException {
    <span class="hljs-comment">//通过 value获取某一列族中的某一列 进行加工 </span>
        <span class="hljs-keyword">byte</span>[] l = value.getValue(Bytes.toBytes(<span class="hljs-string">"content"</span>), Bytes.toBytes(<span class="hljs-string">"info"</span>));
        String line = <span class="hljs-keyword">new</span> String(l);
        String[] split = line.split(<span class="hljs-string">" "</span>);
        <span class="hljs-keyword">for</span> (String s : split) {
            context.write(<span class="hljs-keyword">new</span> Text(s),<span class="hljs-keyword">new</span> IntWritable(<span class="hljs-number">1</span>));
        }
    }
}</code></pre>

<p>**3.Reduce 类 要继承TableReduce</p>

<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HbaseReduce</span> <span class="hljs-inheritance"><span class="hljs-keyword">extends</span></span> <span class="hljs-title">TableReducer</span>&lt;<span class="hljs-title">Text</span>,<span class="hljs-title">IntWritable</span>,<span class="hljs-title">ImmutableBytesWritable</span>&gt;{</span>

    @Override
    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> reduce(Text key, Iterable&lt;IntWritable&gt; values, Context context) throws IOException, InterruptedException {
        <span class="hljs-keyword">int</span> <span class="hljs-keyword">sum</span> = <span class="hljs-number">0</span>;
        <span class="hljs-keyword">for</span> (IntWritable value : values) {
            <span class="hljs-keyword">int</span> i  = Integer.parseInt(value.toString());
            <span class="hljs-keyword">sum</span> =<span class="hljs-keyword">sum</span>+i;
        }
        Put put = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(key.toString()));
        put.add(Bytes.toBytes(<span class="hljs-string">"content"</span>),Bytes.toBytes(<span class="hljs-string">"info"</span>),Bytes.toBytes(String.valueOf(<span class="hljs-keyword">sum</span>)));

        context.write(<span class="hljs-keyword">new</span> ImmutableBytesWritable(Bytes.toBytes(key.toString())),put);

    }
}</code></pre>

<p><strong>4.重点:Driver类</strong> </p>



<pre class="prettyprint"><code class=" hljs actionscript">
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> main(String[] args) throws Exception {

        Configuration conf = HBaseConfiguration.create();
        conf.<span class="hljs-keyword">set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>,<span class="hljs-string">"hadoop01:2181,hadoop02:2181,hadoop03:2181"</span>);

        Job job = Job.getInstance(conf);

        job.setJarByClass(HbaseDriver.<span class="hljs-keyword">class</span>);

        <span class="hljs-comment">//初始化mapper任务 相当于设置mapper类 </span>
        <span class="hljs-comment">//参数 分别是: Hbase来源表名，new Scan（）,Mapper类，输出key，输出value，job</span>
        TableMapReduceUtil.initTableMapperJob(<span class="hljs-string">"word"</span>,<span class="hljs-keyword">new</span> Scan(),HbaseMapper.<span class="hljs-keyword">class</span>,Text.<span class="hljs-keyword">class</span>,IntWritable.<span class="hljs-keyword">class</span>,job);

        <span class="hljs-comment">//初始化reduce任务 相当于设置reduce类 </span>
        <span class="hljs-comment">//参数 分别是: Hbase目的表名，Reduce类，job </span>
        jobTableMapReduceUtil.initTableReducerJob(<span class="hljs-string">"stat"</span>,HbaseReduce.<span class="hljs-keyword">class</span>,job);


       job.waitForCompletion(<span class="hljs-literal">true</span>);

    }</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
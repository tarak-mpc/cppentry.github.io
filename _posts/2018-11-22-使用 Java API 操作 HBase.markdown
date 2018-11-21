---
layout:     post
title:      使用 Java API 操作 HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lanchunhui/article/details/50822357				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>使用 Java API 操作 HBase 数据库，就类似HBase Shell，本质上一个是Java 代码，一个是Shell 命令。（hadoop 的文件系统莫不如此，可用Java API 的方式操作hdfs，也可用shell 命令）。</p>
</blockquote>

<p>在<a href="http://blog.csdn.net/lanchunhui/article/details/50821087" rel="nofollow" target="_blank">hbase 操作 </a>一文我们介绍了使用命令行（HBase shell）的方式操作hbase，如果 Java 也可操作MySQL一样，本文我们着重介绍如何使用 Java API 操作 HBase。</p>

<p>hadoop hbase API 提供了两个核心的 API 用以操作hbase 数据库：</p>

<ul>
<li><p>HBaseAdmin：</p>

<ul><li>创建表：create </li>
<li>删除表：disable、drop</li></ul></li>
<li><p>HTable：</p>

<ul><li>插入记录：put</li>
<li>删除记录：delete、deleteall</li>
<li>遍历所有记录：scan</li></ul></li>
</ul>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> Test {

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> final String TABLE_NAME = <span class="hljs-string">"students"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> final String FAMILY_1 = <span class="hljs-string">"stu_id"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> final String FAMILY_2 = <span class="hljs-string">"addr"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> final String FAMILY_3 = <span class="hljs-string">"info"</span>;

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) throws Exception {

        Configuration conf = HBaseConfiguration.create();
        conf.<span class="hljs-keyword">set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://hadoop0:hbase/"</span>);
        conf.<span class="hljs-keyword">set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"hadoop0"</span>);

        HBaseAdmin admin = <span class="hljs-keyword">new</span> HBaseAdmin(conf);

        <span class="hljs-keyword">if</span> (!admin.tableExists(TABLE_NAME)){
            HTableDescriptor desc = <span class="hljs-keyword">new</span> HTableDescriptor(TABLE_NAME);
            desc.addFamily(<span class="hljs-keyword">new</span> HColumnDescriptor(FAMILY_1));
            desc.addFamily(<span class="hljs-keyword">new</span> HColumnDescriptor(FAMILY_2));
            desc.addFamily(<span class="hljs-keyword">new</span> HColumnDescriptor(FAMILY_3));
            admin.createTable(desc);
                                                    <span class="hljs-comment">// 等价于 hbase shell 中的：</span>
                                                    <span class="hljs-comment">// create 'students', 'stu_id', 'addr', 'info'</span>
        }

        HTable table = <span class="hljs-keyword">new</span> HTable(conf, TABLE_NAME);
        Put put = <span class="hljs-keyword">new</span> Put(<span class="hljs-string">"xiaoming"</span>.getBytes());
        put.add(FAMILY_3.getBytes(), <span class="hljs-string">"age"</span>.getBytes(), <span class="hljs-string">"24"</span>.getBytes());
                                                    <span class="hljs-comment">// 一个 add 表示插入一个记录</span>
                                                    <span class="hljs-comment">// put 'students', 'xiaoming', 'info:age', "24"</span>
        put.add(FAMILY_3.getBytes(), <span class="hljs-string">"birthday"</span>.getBytes(), <span class="hljs-string">"1987-06-17"</span>.getBytes());
        put.add(FAMILY_3.getBytes(), <span class="hljs-string">"company"</span>.getBytes(), <span class="hljs-string">"alibba"</span>.getBytes());
        put.add(FAMILY_2.getBytes(), <span class="hljs-string">"contry"</span>.getBytes(), <span class="hljs-string">"china"</span>.getBytes());
        put.add(FAMILY_2.getBytes(), <span class="hljs-string">"province"</span>.getBytes(), <span class="hljs-string">"zhejiang"</span>.getBytes());
        put.add(FAMILY_2.getBytes(), <span class="hljs-string">"city"</span>.getBytes(), <span class="hljs-string">"hangzhou"</span>.getBytes());

        table.put(put);

        put = <span class="hljs-keyword">new</span> Put(<span class="hljs-string">"zhangyifei"</span>.getBytes());
        put.add(FAMILY_3.getBytes(), <span class="hljs-string">"birthday"</span>.getBytes(), <span class="hljs-string">"1987-04-17"</span>.getBytes());
        put.add(FAMILY_2.getBytes(), <span class="hljs-string">"city"</span>.getBytes(), <span class="hljs-string">"jieyang"</span>.getBytes());

        table.put(put);

        <span class="hljs-comment">// 查询</span>
        <span class="hljs-comment">//  get 'students', 'xiaoming', 'info:company'</span>
        Get <span class="hljs-keyword">get</span> = <span class="hljs-keyword">new</span> Get(<span class="hljs-string">"xiaoming"</span>.getBytes());
        Result result = table.<span class="hljs-keyword">get</span>(<span class="hljs-keyword">get</span>);             <span class="hljs-comment">// 得到的是整条记录</span>

        System.<span class="hljs-keyword">out</span>.println(<span class="hljs-keyword">new</span> String(result.getValue(FAMILY_3.getBytes(), <span class="hljs-string">"company"</span>.getBytes())));
<span class="hljs-comment">//      System.out.println(result);</span>

        <span class="hljs-comment">// 查询全部信息，scan 'students'</span>

        Scan scan = <span class="hljs-keyword">new</span> Scan();
        ResultScanner scanner = table.getScanner(scan);
        <span class="hljs-keyword">for</span> (Result res : scanner) {
            System.<span class="hljs-keyword">out</span>.println(res);
        }
        table.close();
    }
}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
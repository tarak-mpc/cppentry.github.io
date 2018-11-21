---
layout:     post
title:      Java与Hbase数据库
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/longge0508/article/details/80591920				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="hbase">Hbase</h3>

<p>HBase是一个分布式的、面向列的开源数据库，可伸缩的分布式存储系统。 <br>
Hbase在Hadoop架构中的位置如下图所示： <br>
<img src="https://img-blog.csdn.net/20180606101916875?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvbmdnZTA1MDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
在Hadoop体系中，HBase位于结构化存储层，HBase利用Hadoop MapReduce来处理HBase中的海量数据。利用Zookeeper作为相同服务，Zookeeper为HBase提供了稳定服务和failover机制。 <br>
Hadoop HDFS为HBase提供了高可靠性的底层存储支持。</p>

<hr>

<h3 id="java程序操作hbase数据库">Java程序操作Hbase数据库</h3>

<p>首先Java程序操作数据库需要在项目中导入Hbase与hadoop 相关的Jar包。并且配置<code>winutils.exe</code> 的环境变量路径。 <br>
<code>winutils.exe</code> 的环境变量配置如下（<code>winutils.exe位于hadoop/bin/winutils.exe</code>）： <br>
<img src="https://img-blog.csdn.net/20180606104011980?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvbmdnZTA1MDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
Maven工程中添加相关依赖： <br>
<img src="https://img-blog.csdn.net/20180606105422459?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xvbmdnZTA1MDg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
（如果不能成功加载，请手动相关jar包放入maven本地仓库）</p>

<hr>

<p>使用Java与HBase集群交互时，需要创建一个HTable实例，使用该实例提供的相关方法进行插入/删除/查询操作。 <br>
那么如何创建一个HTable实例呢？ <br>
1、先要创建一个Configuration对象（该对象）</p>

<pre class="prettyprint"><code class=" hljs avrasm">Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
Conf<span class="hljs-preprocessor">.set</span>(“hbase<span class="hljs-preprocessor">.zookeeper</span><span class="hljs-preprocessor">.property</span><span class="hljs-preprocessor">.clientPort</span>”,”<span class="hljs-number">2181</span>”)<span class="hljs-comment">;</span></code></pre>

<p>2、利用conf对象得到HConnection对象</p>



<pre class="prettyprint"><code class=" hljs fix"><span class="hljs-attribute">conn </span>=<span class="hljs-string"> HConnectionManager.getConnection(conf);</span></code></pre>

<p>3、利用conn对象得到HTable对象</p>



<pre class="prettyprint"><code class=" hljs fix"><span class="hljs-attribute">HTable table </span>=<span class="hljs-string"> conn.getTable(tableName);</span></code></pre>

<hr>

<p><strong>注意</strong>： <br>
HConnection实际上去连接的并不是HBase集群本身，而是维护其关键数据信息的Zookeeper（简称ZK）集群 <br>
HTable的父类HTableInterface是非线程安全的，而且创建该HtableInterface的过程是轻量级的，<strong>只要保证我们用的是一个Configuration对象是同一个，那么HConnection也是同一个，使用同一个Confuguration的HTable都会共用一个HConnection</strong>。这样我们就可以利用单例模式获得全局唯一的实例Configuration和HConnection对象，然后以多线程的形式创建HTable对象，这样就实现了HBase连接池。</p>

<hr>

<p>查询操作：</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-comment">//创建一个空的Scan实例</span>
    Scan scan= <span class="hljs-keyword">new</span> Scan();
    <span class="hljs-number">2</span>、<span class="hljs-comment">//在行上获取遍历器</span>
           ResultScanner rs= table.getScanner(scan);
    <span class="hljs-number">3</span>、<span class="hljs-comment">//打印行的值</span>
        <span class="hljs-keyword">for</span> (Result r: rs) {
            System.<span class="hljs-keyword">out</span>.println(r);
        }</code></pre>

<p>向指定表中插入数据： </p>



<pre class="prettyprint"><code class=" hljs avrasm"> Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName<span class="hljs-preprocessor">.getBytes</span>()))<span class="hljs-comment">;</span>
                  Put put = new Put(rowkey<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;  </span>
                  put<span class="hljs-preprocessor">.addColumn</span>(family<span class="hljs-preprocessor">.getBytes</span>(),qualifier<span class="hljs-preprocessor">.getBytes</span>(), value<span class="hljs-preprocessor">.toString</span>()<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;  </span>
                 table<span class="hljs-preprocessor">.put</span>(put)<span class="hljs-comment">;  </span>
</code></pre>

<p>代码未完待续。。。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
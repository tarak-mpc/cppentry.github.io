---
layout:     post
title:      Hadoop集群间HBase数据迁移
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>背景：HBase Cluster1 -&gt; HBase Cluster2 <br>
【利用HBase的Snapshots功能，进行集群之间的HBase数据前移】</p>



<h4 id="1开启snapshots功能095及以上版本默认开启了">1.开启Snapshots功能（0.95及以上版本默认开启了）</h4>



<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.snapshot.enabled<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>



<h4 id="2-创建snapshot在cluster1上操作">2. 创建Snapshot(在Cluster1上操作)</h4>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hbase shell
hbase&gt; snapshot <span class="hljs-string">'myTable'</span>, <span class="hljs-string">'myTableSnapshot-122112'</span></code></pre>



<h4 id="3-执行迁移">3. 执行迁移</h4>



<h5 id="31-推式在cluster1上操作">3.1 推式（在Cluster1上操作）</h5>



<pre class="prettyprint"><code class=" hljs avrasm">$ hbase org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.snapshot</span><span class="hljs-preprocessor">.ExportSnapshot</span> -snapshot MySnapshot -copy-to
<span class="hljs-label">hdfs:</span>//cluster1:<span class="hljs-number">8020</span>/hbase_root_dir -mappers <span class="hljs-number">16</span></code></pre>



<h5 id="32-拉式在cluster2上操作">3.2 拉式（在Cluster2上操作）</h5>



<pre class="prettyprint"><code class=" hljs lasso">$ hbase org<span class="hljs-built_in">.</span>apache<span class="hljs-built_in">.</span>hadoop<span class="hljs-built_in">.</span>hbase<span class="hljs-built_in">.</span>snapshot<span class="hljs-built_in">.</span>ExportSnapshot <span class="hljs-attribute">-snapshot</span> MySnapshot <span class="hljs-attribute">-copy</span><span class="hljs-attribute">-from</span> hdfs:<span class="hljs-comment">//cluster1:8020/hbase_root_dir </span>
<span class="hljs-attribute">-copy</span><span class="hljs-attribute">-to</span> hdfs:<span class="hljs-comment">//cluster2:8020/hbase_root_dir -mappers 16</span></code></pre>

<h4 id="4-恢复数据在cluster2上操作">4. 恢复数据（在Cluster2上操作）</h4>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hbase shell
hbase&gt; restore_snapshot <span class="hljs-string">'myTableSnapshot-122112'</span></code></pre>



<h6 id="httpsdocshortonworkscomhdpdocumentshdp2hdp-234bkhbasesnapshotsguidecontentchhbasesnapshotschapterhtml"><a href="https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.4/bk_hbase_snapshots_guide/content/ch_hbase_snapshots_chapter.html" rel="nofollow">https://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.4/bk_hbase_snapshots_guide/content/ch_hbase_snapshots_chapter.html</a></h6>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      【Python3实战Spark大数据分析及调度】Spark Core 课程笔记（2）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p id="main-toc"><strong>目录</strong></p>

<p id="Spark%20Cache%E8%AF%A6%E8%A7%A3-toc" style="margin-left:40px;"><a href="#Spark%20Cache%E8%AF%A6%E8%A7%A3" rel="nofollow">Spark Cache详解</a></p>

<p id="Cache%E7%9A%84%E4%BD%9C%E7%94%A8-toc" style="margin-left:80px;"><a href="#Cache%E7%9A%84%E4%BD%9C%E7%94%A8" rel="nofollow">Cache的作用</a></p>

<p id="Spark%E7%BC%93%E5%AD%98%E6%A6%82%E8%BF%B0-toc" style="margin-left:80px;"><a href="#Spark%E7%BC%93%E5%AD%98%E6%A6%82%E8%BF%B0" rel="nofollow">Spark缓存概述</a></p>

<p id="Spark%20%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5-toc" style="margin-left:80px;"><a href="#Spark%20%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5" rel="nofollow">Spark 缓存策略</a></p>

<p id="Spark%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5%E9%80%89%E6%8B%A9%E4%BE%9D%E6%8D%AE-toc" style="margin-left:80px;"><a href="#Spark%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5%E9%80%89%E6%8B%A9%E4%BE%9D%E6%8D%AE" rel="nofollow">Spark缓存策略选择依据</a></p>

<hr id="hr-toc"><h2 id="Spark%20Cache%E8%AF%A6%E8%A7%A3">Spark Cache详解</h2>

<h3 id="Cache%E7%9A%84%E4%BD%9C%E7%94%A8">Cache的作用</h3>

<p>rdd.cache(): StorageLevel</p>

<p>如果一个RDD在后续的计算中可能会被使用到，那么建议cache</p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181114200417865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3poYW5nMTU5NTM3MDk5MTM=,size_16,color_FFFFFF,t_70"></p>

<h3 id="Spark%E7%BC%93%E5%AD%98%E6%A6%82%E8%BF%B0">Spark缓存概述</h3>

<p><a href="https://spark.apache.org/docs/latest/rdd-programming-guide.html#rdd-persistence" rel="nofollow">https://spark.apache.org/docs/latest/rdd-programming-guide.html#rdd-persistence</a></p>

<p>One of the most important capabilities in Spark is <span style="color:#7c79e5;"><em>persisting</em></span> (or <span style="color:#7c79e5;"><em>caching</em></span>) a dataset in memory across operations. When you persist an RDD, each node stores any partitions of it that it computes in memory and reuses them in other actions on that dataset (or datasets derived from it). This allows future actions to be much faster (often by more than 10x). Caching is a key tool for <span style="color:#7c79e5;">iterative</span> algorithms and fast interactive use.</p>

<p>You can mark an RDD to be persisted using the <span style="color:#f33b45;"><code>persist()</code></span><span style="color:#f33b45;"> </span><span style="color:#f33b45;">or</span><span style="color:#f33b45;"> </span><span style="color:#f33b45;"><code>cache()</code></span><span style="color:#f33b45;"> </span>methods on it.  The first time it is computed in an action, it will be kept in memory on the nodes. Spark’s cache is<span style="color:#f33b45;"> fault-tolerant</span> – if any partition of an RDD is lost, it will automatically be recomputed using the transformations that originally created it.</p>

<p><span style="color:#f33b45;">cache() &lt;=&gt; persist(storageLevel=StorageLevel.MEMORY_ONLY)</span></p>

<pre class="has">
<code class="language-python">def cache(self):
    self.is_cached = True
    self.persist(StorageLevel.MEMORY_ONLY)
    return self

def persist(self, storageLevel=StorageLevel.MEMORY_ONLY):
    self.is_cached = True
    javaStorageLevel = self.....
    self._jrdd.persist(...)
    return self
</code></pre>

<table><tbody><tr><th>Storage Level</th>
			<th>Meaning</th>
		</tr><tr><td>MEMORY_ONLY</td>
			<td>Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, some partitions will not be cached and will be recomputed on the fly each time they're needed. This is the default level.</td>
		</tr><tr><td>MEMORY_AND_DISK</td>
			<td>Store RDD as deserialized Java objects in the JVM. If the RDD does not fit in memory, store the partitions that don't fit on disk, and read them from there when they're needed.</td>
		</tr><tr><td>MEMORY_ONLY_SER <br>
			(Java and Scala)</td>
			<td>Store RDD as <em>serialized</em> Java objects (one byte array per partition). This is generally more space-efficient than deserialized objects, especially when using a <a href="https://spark.apache.org/docs/latest/tuning.html" rel="nofollow">fast serializer</a>, but more CPU-intensive to read.</td>
		</tr><tr><td>MEMORY_AND_DISK_SER <br>
			(Java and Scala)</td>
			<td>Similar to MEMORY_ONLY_SER, but spill partitions that don't fit in memory to disk instead of recomputing them on the fly each time they're needed.</td>
		</tr><tr><td>DISK_ONLY</td>
			<td>Store the RDD partitions only on disk.</td>
		</tr><tr><td>MEMORY_ONLY_2, MEMORY_AND_DISK_2, etc.</td>
			<td>Same as the levels above, but replicate each partition on two cluster nodes.</td>
		</tr><tr><td>OFF_HEAP (experimental)</td>
			<td>Similar to MEMORY_ONLY_SER, but store the data in <a href="https://spark.apache.org/docs/latest/configuration.html#memory-management" rel="nofollow">off-heap memory</a>. This requires off-heap memory to be enabled.</td>
		</tr></tbody></table><p><strong>Note:</strong> <em>In <span style="color:#f33b45;">Python</span>, stored objects<span style="color:#f33b45;"> will always be serialized</span> with the <a href="https://docs.python.org/2/library/pickle.html" rel="nofollow">Pickle</a> library, so it does not matter whether you choose a serialized level. </em></p>

<p>Spark also <span style="color:#f33b45;">automatically</span> persists some intermediate data in <span style="color:#f33b45;">shuffle operations </span>(e.g. <code>reduceByKey</code>), even without users calling <code>persist</code>. This is done to avoid recomputing the entire input if a node fails during the shuffle. We still recommend users call <code>persist</code> on the resulting RDD if they plan to reuse it.</p>

<p> </p>

<h3 id="Spark%20%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5">Spark 缓存策略</h3>

<p>rdd.persist()  # 懒加载 （遇到action才执行）</p>

<p>rdd.unpersist()  #立即执行</p>

<h3 id="Spark%E7%BC%93%E5%AD%98%E7%AD%96%E7%95%A5%E9%80%89%E6%8B%A9%E4%BE%9D%E6%8D%AE">Spark缓存策略选择依据</h3>

<p id="which-storage-level-to-choose"><strong>Which Storage Level to Choose?</strong></p>

<p>Spark’s storage levels are meant to provide different trade-offs between memory usage and CPU efficiency. We <span style="color:#f33b45;">recommend</span> going through the following process to select one:</p>

<ul><li>
	<p>If your RDDs fit comfortably with the default storage level (<span style="color:#f33b45;"><code>MEMORY_ONLY</code></span>), leave them that way. This is the most CPU-efficient option, allowing operations on the RDDs to run as fast as possible. 首先尝试MEMORY_ONLY, 这是最CPU-efficient的策略</p>
	</li>
	<li>
	<p>If not, try using <code>MEMORY_ONLY_SER</code> and <a href="https://spark.apache.org/docs/latest/tuning.html" rel="nofollow">selecting a fast serialization library</a> to make the objects much more space-efficient, but still reasonably fast to access. (Java and Scala)</p>
	</li>
	<li>
	<p>Don’t spill to disk unless the functions that computed your datasets are expensive, or they filter a large amount of the data. Otherwise, recomputing a partition may be as fast as reading it from disk. 除非计算昂贵或者是filter大量数据，否则重新计算可能比从disk读取要快</p>
	</li>
	<li>
	<p>Use the replicated storage levels if you want fast fault recovery (e.g. if using Spark to serve requests from a web application). <em>All</em> the storage levels provide full fault tolerance by recomputing lost data, but the replicated ones let you continue running tasks on the RDD without waiting to recompute a lost partition. （不建议）</p>
	</li>
</ul>            </div>
                </div>
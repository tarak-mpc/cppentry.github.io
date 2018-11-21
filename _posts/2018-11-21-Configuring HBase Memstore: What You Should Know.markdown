---
layout:     post
title:      Configuring HBase Memstore: What You Should Know
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
In this post we discuss what HBase users should know about one of the internal parts of HBase: the Memstore. Understanding underlying processes related to Memstore will help to configure HBase cluster towards better performance.</p>
<h2 style="color:rgb(51,51,51);font-family:Arial, Tahoma, Verdana;font-weight:normal;font-size:20px;border-bottom-width:1px;border-bottom-style:dotted;border-bottom-color:rgb(204,204,204);line-height:20px;">
HBase Memstore</h2>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Let’s take a look at the write and read paths in HBase to understand what Memstore is, where how and why it is used.</p>
<div id="attachment_2257" class="wp-caption alignnone" style="background-color:rgb(245,245,245);text-align:center;border:1px solid rgb(228,228,228);color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;width:600px;">
<img src="http://my.csdn.net/uploads/201207/22/1342965151_2542.png" alt="" width="600"><br><p class="wp-caption-text" style="font-size:11px;line-height:12px;">
Memstore Usage in HBase Read/Write Paths</p>
</div>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
(picture was taken from <a href="http://two.rdaili.com/baidu.com.php?u=b399377ff62fdf48Oi8vYmxvZy5zZW1hdGV4dC5jb20vMjAxMi8wNy8wOS9pbnRyby10by1oYmFzZS1pbnRlcm5hbHMtYW5kLXNjaGVtYS1kZXNpZy8%3D&amp;b=3" rel="nofollow" style="color:rgb(0,141,207);text-decoration:none;">Intro to HBase
 Internals and Schema Design presentation</a>)</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
When RegionServer (RS) receives write request, it directs the request to specific Region. Each Region stores set of rows. Rows data can be separated in multiple column families (CFs). Data of particular CF is stored in HStore which consists of Memstore and
 a set of HFiles. Memstore is kept in RS main memory, while HFiles are written to HDFS. When write request is processed, data is first written into the Memstore. Then, when certain thresholds are met (obviously, main memory is well-limited) Memstore data gets
 flushed into HFile.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
The main reason for using Memstore is the need to store data on DFS ordered by row key. As HDFS is designed for sequential reads/writes, with no file modifications allowed, HBase cannot efficiently write data to disk as it is being received: the written data
 will not be sorted (when the input is not sorted) which means not optimized for future retrieval. To solve this problem HBase buffers last received data in memory (in Memstore), “sorts” it before flushing, and then writes to HDFS using fast sequential writes.
 Note that in reality HFile is not just a simple list of sorted rows, it is <a href="http://two.rdaili.com/baidu.com.php?u=b399377ff62fdf48Oi8vd3d3LmNsb3VkZXJhLmNvbS9ibG9nLzIwMTIvMDYvaGJhc2UtaW8taGZpbGUtaW5wdXQtb3V0cHV0Lw%3D%3D&amp;b=3" rel="nofollow" style="color:rgb(0,141,207);text-decoration:none;">much
 more than that</a>.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Apart from solving the “non-ordered” problem, Memstore also has other benefits, e.g.:</p>
<ul style="list-style-type:square;color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;"><li style="list-style-type:square;">It acts as a in-memory cache which keeps recently added data. This is useful in numerous cases when last written data is accessed more frequently than older data</li><li style="list-style-type:square;">There are certain optimizations that can be done to rows/cells when they are stored in memory before writing to persistent store. E.g. when it is configured to store one version of a cell
 for certain CF and Memstore contains multiple updates for that cell, only most recent one can be kept and older ones can be omitted (and never written to HFile).</li></ul><p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Important thing to note is that <strong>every Memstore flush creates one HFile</strong> per CF.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
On the reading end things are simple: HBase first checks if requested data is in Memstore, then goes to HFiles and returns merged result to the user.</p>
<h2 style="color:rgb(51,51,51);font-family:Arial, Tahoma, Verdana;font-weight:normal;font-size:20px;border-bottom-width:1px;border-bottom-style:dotted;border-bottom-color:rgb(204,204,204);line-height:20px;">
What to Care about</h2>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
There are number of reasons HBase users and/or administrators should be aware of what Memstore is and how it is used:</p>
<ul style="list-style-type:square;color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;"><li style="list-style-type:square;">There are number of configuration options for Memstore one can use to achieve better performance and avoid issues. HBase will not adjust settings for you based on usage pattern.</li><li style="list-style-type:square;">Frequent Memstore flushes can affect reading performance and can bring additional load to the system</li><li style="list-style-type:square;">The way Memstore flushes work may affect your schema design</li></ul><p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Let’s take a closer look at these points.</p>
<h3 style="color:rgb(51,51,51);font-family:Arial, Tahoma, Verdana;font-weight:normal;font-size:18px;line-height:20px;">
Configuring Memstore Flushes</h3>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Basically, there are two groups of configuraion properties (leaving out region pre-close flushes):</p>
<ul style="list-style-type:square;color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;"><li style="list-style-type:square;">First determines when flush should be triggered</li><li style="list-style-type:square;">Second determines when flush should be triggered <em>and updates should be blocked during flushing</em></li></ul><p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
First  group is about triggering “regular” flushes which happen in parallel with serving write requests. The properties for configuring flush thresholds are:</p>
<ul style="list-style-type:square;color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;"><li style="list-style-type:square;">hbase.hregion.memstore.flush.size</li></ul><pre style="background-color:rgb(245,245,245);border:1px solid rgb(230,230,230);width:593px;overflow:auto;color:rgb(85,85,85);line-height:20px;">&lt;property&gt;
 &lt;name&gt;hbase.hregion.memstore.flush.size&lt;/name&gt;
 &lt;value&gt;134217728&lt;/value&gt;
 &lt;description&gt;
 Memstore will be flushed to disk if size of the memstore
 exceeds this number of bytes. Value is checked by a thread that runs
 every hbase.server.thread.wakefrequency.
 &lt;/description&gt;
&lt;/property&gt;</pre>
<ul style="list-style-type:square;color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;"><li style="list-style-type:square;">base.regionserver.global.memstore.lowerLimit</li></ul><pre style="background-color:rgb(245,245,245);border:1px solid rgb(230,230,230);width:593px;overflow:auto;color:rgb(85,85,85);line-height:20px;">&lt;property&gt;
 &lt;name&gt;hbase.regionserver.global.memstore.lowerLimit&lt;/name&gt;
 &lt;value&gt;0.35&lt;/value&gt;
 &lt;description&gt;Maximum size of all memstores in a region server before
 flushes are forced. Defaults to 35% of heap.
 This value equal to hbase.regionserver.global.memstore.upperLimit causes
 the minimum possible flushing to occur when updates are blocked due to
 memstore limiting.
 &lt;/description&gt;
&lt;/property&gt;</pre>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Note that the first setting is the size <em>per Memstore</em>. I.e. when you define it you should take into account the number of regions served by each RS. When number of RS grows (and you configured the setting when there were few of them) Memstore flushes
 are likely to be triggered by the second threshold earlier.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Second group of settings is for safety reasons: sometimes write load is so high that flushing cannot keep up with it and since we  don’t want memstore to grow without a limit, in this situation <span style="color:rgb(153,51,0);"><strong>writes are blocked unless
 memstore has “manageable” size</strong></span>. These thresholds are configured with:</p>
<ul style="list-style-type:square;color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;"><li style="list-style-type:square;">hbase.regionserver.global.memstore.upperLimit</li></ul><pre style="background-color:rgb(245,245,245);border:1px solid rgb(230,230,230);width:593px;overflow:auto;color:rgb(85,85,85);line-height:20px;">&lt;property&gt;
 &lt;name&gt;hbase.regionserver.global.memstore.upperLimit&lt;/name&gt;
 &lt;value&gt;0.4&lt;/value&gt;
 &lt;description&gt;Maximum size of all memstores in a region server before new
 updates are blocked and flushes are forced. Defaults to 40% of heap.
 Updates are blocked and flushes are forced until size of all memstores
 in a region server hits hbase.regionserver.global.memstore.lowerLimit.
 &lt;/description&gt;
&lt;/property&gt;</pre>
<ul style="list-style-type:square;color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;"><li style="list-style-type:square;">hbase.hregion.memstore.block.multiplier</li></ul><div style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
<pre style="background-color:rgb(245,245,245);border:1px solid rgb(230,230,230);width:593px;overflow:auto;">&lt;property&gt;
 &lt;name&gt;hbase.hregion.memstore.block.multiplier&lt;/name&gt;
 &lt;value&gt;2&lt;/value&gt;
 &lt;description&gt;
 Block updates if memstore has hbase.hregion.block.memstore
 time hbase.hregion.flush.size bytes. Useful preventing
 runaway memstore during spikes in update traffic. Without an
 upper-bound, memstore fills such that when it flushes the
 resultant flush files take a long time to compact or split, or
 worse, we OOME.
 &lt;/description&gt;
&lt;/property&gt;</pre>
</div>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Blocking writes on particular RS on its own may be a big issue, but there’s more to that. Since in HBase by design<em>one Region is served by single RS</em> when write load is evenly distributed over the cluster (over Regions) <span style="color:rgb(153,51,0);"><strong>having
 one such “slow” RS will make the whole cluster work slower</strong></span> (basically, at its speed).</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
<span style="color:rgb(51,153,102);"><strong>Hint</strong>: watch for Memstore Size and Memstore Flush Queue size</span>. Memstore Size ideally should not reach upper Memstore limit and Memstore Flush Queue size should not constantly grow.</p>
<h3 style="color:rgb(51,51,51);font-family:Arial, Tahoma, Verdana;font-weight:normal;font-size:18px;line-height:20px;">
Frequent Memstore Flushes</h3>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Since we want to avoid blocking writes it may seem a good approach to flush earlier when we are far from “writes-blocking” thresholds. However, this will cause too frequent flushes which can affect read performance and bring additional load to the cluster.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Every time Memstore flush happens one HFile created for each CF. Frequent flushes may create tons of HFiles. Since during reading HBase will have to look at many HFiles, the read speed can suffer.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
To prevent opening too many HFiles and avoid read performance deterioration there’s HFiles compaction process. HBase will periodically (when certain <em>configurable</em> thresholds are met) compact multiple smaller HFiles into a big one. Obviously, the more
 files created by Memstore flushes, the more work (extra load) for the system. More to that: while compaction process is usually performed in parallel with serving other requests, <span style="color:rgb(153,51,0);"><strong>when HBase cannot keep up with compacting
 HFiles</strong></span> (yes, there are configured thresholds for that too;)) <span style="color:rgb(153,51,0);"><strong>it will block writes on RS</strong> </span>again. As mentioned above, this is highly undesirable.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
<span style="color:rgb(51,153,102);"><strong>Hint</strong>: watch for Compaction Queue size on RSs</span>. In case it is constantly growing you should take actions before it will cause problems.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
More on HFiles creation &amp; Compaction can be found <a href="http://outerthought.org/blog/465-ot.html" rel="nofollow" style="color:rgb(0,141,207);text-decoration:none;">here</a>.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
So, ideally Memstore should use as much memory as it can (as configured, not all RS heap: there are also in-memory caches), but not cross the upper limit. This picture (screenshot was taken from our <a href="http://sematext.com/spm/index.html" rel="nofollow" style="color:rgb(0,141,207);text-decoration:none;">SPM
 monitoring service</a>) shows somewhat good situation:</p>
<div id="attachment_2273" class="wp-caption alignnone" style="background-color:rgb(245,245,245);text-align:center;border:1px solid rgb(228,228,228);color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;width:640px;">
<img src="http://my.csdn.net/uploads/201207/22/1342965183_2242.png" alt=""><br><p class="wp-caption-text" style="font-size:11px;line-height:12px;">
Memstore Size: Good Situation</p>
</div>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
“Somewhat”, because we could configure lower limit to be closer to upper, since we barely ever go over it.</p>
<h3 style="color:rgb(51,51,51);font-family:Arial, Tahoma, Verdana;font-weight:normal;font-size:18px;line-height:20px;">
Multiple Column Families &amp; Memstore Flush</h3>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Memstores of all column families are flushed together (this <a href="https://issues.apache.org/jira/browse/HBASE-3149" rel="nofollow" style="color:rgb(0,141,207);text-decoration:none;">might change</a>). This means creating N HFiles per flush, one for each CF. Thus, uneven
 data amount in CF will cause too many HFiles to be created: when Memstore of one CF reaches threshold all Memstores of other CFs are flushed too. As stated above too frequent flush operations and too many HFiles may affect cluster performance.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
<span style="color:rgb(51,153,102);"><strong>Hint</strong>: in many cases having one CF is the best schema design</span>.</p>
<p style="color:rgb(85,85,85);font-family:Arial, Tahoma, Verdana;line-height:20px;">
Ref: <a href="http://blog.sematext.com/2012/07/16/hbase-memstore-what-you-should-know/" rel="nofollow">http://blog.sematext.com/2012/07/16/hbase-memstore-what-you-should-know/</a></p>
            </div>
                </div>
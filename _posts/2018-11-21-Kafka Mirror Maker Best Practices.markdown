---
layout:     post
title:      Kafka Mirror Maker Best Practices
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="short-description span11" style="min-height:30px;margin-left:23.45px;color:rgb(51,51,51);font-family:Cabin, 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;background-color:rgb(242,242,242);">
<h4 style="font-family:inherit;line-height:21px;color:inherit;font-size:17.5px;">
Short Description:</h4>
Best Practices in running Kafka Mirror Maker</div>
<div class="kbentry-body node-body span11" style="min-height:30px;margin-left:23.45px;color:rgb(51,51,51);font-family:Cabin, 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;background-color:rgb(242,242,242);">
<h4 style="font-family:inherit;line-height:21px;color:inherit;font-size:17.5px;">
Article</h4>
<p>Kafka's mirroring feature makes it possible to maintain a replica of an existing Kafka cluster. This tool uses Kafka consumer to consume messages from the source cluster, and re-publishes those messages to the target
 cluster using an embedded Kafka producer.</p>
<h2 style="font-family:inherit;line-height:37.8px;color:inherit;font-size:31.5px;">
<strong>Running Mirror Maker</strong></h2>
<p><strong></strong></p>
<p>To set up a mirror, run <code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">kafka.tools.MirrorMaker</code>.
 The following table lists configuration options.</p>
<p>At a minimum, MirrorMaker requires one or more consumer configuration files, a producer configuration file, and either a whitelist or a blacklist of topics. In the consumer and producer configuration files, point
 the consumer to the the source cluster, and point the producer to the destination (mirror) cluster, respectively.</p>
<pre class="prettyprint linenums" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;line-height:20px;background-color:rgb(245,245,245);overflow:auto;"></pre><ol><li class="L0" style="line-height:20px;list-style-type:none;"><span class="pln" style="color:rgb(0,0,0);">bin</span><span class="pun" style="color:rgb(102,102,0);">/</span><span class="pln" style="color:rgb(0,0,0);">kafka</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">run</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="kwd" style="color:rgb(0,0,136);">class</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">sh kafka</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">tools</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="typ" style="color:rgb(102,0,102);">MirrorMaker</span><span class="pln" style="color:rgb(0,0,0);"> </span><span class="pun" style="color:rgb(102,102,0);">--</span><span class="pln" style="color:rgb(0,0,0);">consumer</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">config sourceCluster1Consumer</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">config </span><span class="pun" style="color:rgb(102,102,0);">--</span><span class="pln" style="color:rgb(0,0,0);">consumer</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">config sourceCluster2Consumer</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">config </span><span class="pun" style="color:rgb(102,102,0);">--</span><span class="pln" style="color:rgb(0,0,0);">num</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">streams </span><span class="lit" style="color:rgb(0,102,102);">2</span><span class="pln" style="color:rgb(0,0,0);"> </span><span class="pun" style="color:rgb(102,102,0);">--</span><span class="pln" style="color:rgb(0,0,0);">producer</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">config targetClusterProducer</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">config </span><span class="pun" style="color:rgb(102,102,0);">--</span><span class="pln" style="color:rgb(0,0,0);">whitelist</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="str" style="color:rgb(0,136,0);">".*"</span></li><li class="L1" style="line-height:20px;list-style-type:none;background:rgb(238,238,238);"><span class="pln" style="color:rgb(0,0,0);"> </span></li></ol>
<table style="background-color:transparent;border-spacing:0px;border-width:1px 1px 1px 0px;border-top-style:solid;border-right-style:solid;border-bottom-style:solid;border-top-color:rgb(221,221,221);border-right-color:rgb(221,221,221);border-bottom-color:rgb(221,221,221);"><tbody><tr><td style="line-height:20px;vertical-align:top;border-top:0px;border-left:1px solid rgb(221,221,221);">
Parameter</td>
<td style="line-height:20px;vertical-align:top;border-top:0px;border-left:1px solid rgb(221,221,221);">
Description</td>
<td style="line-height:20px;vertical-align:top;border-top:0px;border-left:1px solid rgb(221,221,221);">
Examples</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--consumer.config</code></p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p>Specifies a file that contains configuration settings for the source cluster. For more information about this file, see the "Consumer Configuration File" subsection.</p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--consumer.config
 hdp1-consumer.properties</code></p>
</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--producer.config</code></p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p>Specifies the file that contains configuration settings for the target cluster. For more information about this file, see the "Producer Configuration File" subsection.</p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--producer.config
 hdp1-producer.properties</code></p>
</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--whitelist</code></p>
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--blacklist</code></p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p>(Optional) For a partial mirror, you can specify exactly one comma-separated list of topics to include (--whitelist) or exclude (--blacklist).</p>
<p>In general, these options accept Java <a href="http://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html" rel="nofollow" style="color:rgb(61,181,230);">regex patterns</a>. For
 caveats, see the note after this table.</p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--whitelist
 my-topic</code></p>
</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--num.streams</code></p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p>Specifies the number of consumer stream threads to create.</p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--num.streams
 4</code></p>
</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--num.producers</code></p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p>Specifies the number of producer instances. Setting this to a value greater than one establishes a producer pool that can increase throughput.</p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--num.producers
 2</code></p>
</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--queue.size</code></p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p>Queue size: number of messages that are buffered, in terms of number of messages between the consumer and producer. Default = 10000.</p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--queue.size
 2000</code></p>
</td>
</tr><tr><td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p><code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--help</code></p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
<p>List MirrorMaker command-line options.</p>
</td>
<td style="line-height:20px;vertical-align:top;border-top:1px solid rgb(221,221,221);border-left:1px solid rgb(221,221,221);">
 </td>
</tr></tbody></table><ul><li style="line-height:20px;">A comma (',') is interpreted as the regex-choice symbol ('|') for convenience.</li><li style="line-height:20px;">If you specify -<code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">-white-list=".*"</code>,
 MirrorMaker tries to fetch data from the system-level topic <code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">__consumer-offsets</code> and
 produce that data to the target cluster. Make sure you added <code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">exclude.internal.topics=true</code> in
 consumer properties
<p>Workaround: Specify topic names, or to replicate all topics, specify <code style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:12px;color:rgb(221,17,68);background-color:rgb(247,247,249);border:1px solid rgb(225,225,232);">--blacklist="__consumer-offsets"</code>.</p>
</li></ul><h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
<strong></strong></h3>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
<strong>Example Consumer config file</strong></h3>
<p>Consumer config bootstrap.servers should point to source cluster<strong></strong></p>
<p><strong></strong></p>
<p>Here is a sample consumer configuration file:</p>
<pre class="prettyprint linenums" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;line-height:20px;background-color:rgb(245,245,245);overflow:auto;"></pre><ol><li class="L0" style="line-height:20px;list-style-type:none;"><span class="pln" style="color:rgb(0,0,0);">bootstrap</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">servers</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="pln" style="color:rgb(0,0,0);">kafka</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">source</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">server1</span><span class="pun" style="color:rgb(102,102,0);">:</span><span class="lit" style="color:rgb(0,102,102);">6667</span><span class="pun" style="color:rgb(102,102,0);">,</span><span class="pln" style="color:rgb(0,0,0);">kafka</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">source</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">server2</span><span class="pun" style="color:rgb(102,102,0);">:</span><span class="lit" style="color:rgb(0,102,102);">6667</span><span class="pun" style="color:rgb(102,102,0);">,</span><span class="pln" style="color:rgb(0,0,0);">kafka</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">source</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">server3</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="lit" style="color:rgb(0,102,102);">6667</span></li><li class="L1" style="line-height:20px;list-style-type:none;background:rgb(238,238,238);"><span class="pln" style="color:rgb(0,0,0);">groupid</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="pln" style="color:rgb(0,0,0);">dp</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="typ" style="color:rgb(102,0,102);">MirrorMaker</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="kwd" style="color:rgb(0,0,136);">group</span></li><li class="L2" style="line-height:20px;list-style-type:none;"><span class="pln" style="color:rgb(0,0,0);">exclude</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="kwd" style="color:rgb(0,0,136);">internal</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">topics</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="kwd" style="color:rgb(0,0,136);">true</span></li><li class="L3" style="line-height:20px;list-style-type:none;background:rgb(238,238,238);"><span class="pln" style="color:rgb(0,0,0);">mirror</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">topics</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">whitelist</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="pln" style="color:rgb(0,0,0);">app_log</span></li><li class="L4" style="line-height:20px;list-style-type:none;"><span class="pln" style="color:rgb(0,0,0);">client</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">id</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="pln" style="color:rgb(0,0,0);">mirror_maker_consumer</span></li></ol>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
<strong></strong></h3>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
<strong>Example Producer Configuration File</strong></h3>
<p>Producer config bootstrap.servers should point to target cluster</p>
<p>Here is a sample producer configuration file:</p>
<pre class="prettyprint linenums" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;line-height:20px;background-color:rgb(245,245,245);overflow:auto;"></pre><ol><li class="L0" style="line-height:20px;list-style-type:none;"><span class="pln" style="color:rgb(0,0,0);">bootstrap</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">servers</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="pln" style="color:rgb(0,0,0);">kafka</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">target</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">server1</span><span class="pun" style="color:rgb(102,102,0);">:</span><span class="lit" style="color:rgb(0,102,102);">6667</span><span class="pun" style="color:rgb(102,102,0);">,</span><span class="pln" style="color:rgb(0,0,0);">kafka</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">target</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">server2</span><span class="pun" style="color:rgb(102,102,0);">:</span><span class="lit" style="color:rgb(0,102,102);">6667</span><span class="pun" style="color:rgb(102,102,0);">,</span><span class="pln" style="color:rgb(0,0,0);">kafka</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">target</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="pln" style="color:rgb(0,0,0);">server3</span><span class="pun" style="color:rgb(102,102,0);">-</span><span class="lit" style="color:rgb(0,102,102);">6667</span></li><li class="L1" style="line-height:20px;list-style-type:none;background:rgb(238,238,238);"><span class="pln" style="color:rgb(0,0,0);">acks</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="lit" style="color:rgb(0,102,102);">1</span></li><li class="L2" style="line-height:20px;list-style-type:none;"><span class="pln" style="color:rgb(0,0,0);">batch</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">size</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="lit" style="color:rgb(0,102,102);">100</span></li><li class="L3" style="line-height:20px;list-style-type:none;background:rgb(238,238,238);"><span class="pln" style="color:rgb(0,0,0);">client</span><span class="pun" style="color:rgb(102,102,0);">.</span><span class="pln" style="color:rgb(0,0,0);">id</span><span class="pun" style="color:rgb(102,102,0);">=</span><span class="pln" style="color:rgb(0,0,0);">mirror_maker_producer</span></li></ol>
<h2 style="font-family:inherit;line-height:37.8px;color:inherit;font-size:31.5px;">
</h2>
<h2 style="font-family:inherit;line-height:37.8px;color:inherit;font-size:31.5px;">
</h2>
<h2 style="font-family:inherit;line-height:37.8px;color:inherit;font-size:31.5px;">
Best Practices</h2>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
</h3>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
Create topics in target cluster</h3>
<p>If you have consumers that are going to consume data from target cluster and your parallelism requirement for a consumer is same as your source cluster, Its important that you create a same topic in target cluster
 with same no.of partitions.</p>
<p>Example:</p>
<p>If you have a topic name called "click-logs" with 6 partitions in source cluster , make sure you have same no.of partitions in the target cluster. If you are using a target cluster as more of a backup, not active
 this might not need to be same.</p>
<p>If users didn't create a topic in target cluster, producer in mirrormaker will attempt to create a topic and target cluster broker will create a topic with configured num.partitions and num.replicas, this may not
 be the partitions and replication that the user wants.</p>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
</h3>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
Where to run MirrorMaker</h3>
<p>We recommend to run MirrorMaker on target cluster.</p>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
</h3>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
No data loss</h3>
<p>Make sure you've following configs in consumer config and producer config for No data loss.</p>
<p>For Consumer, set auto.commit.enabled=false in consumer.properties</p>
<p>For Producer</p>
<ol><li style="line-height:20px;">max.in.flight.requests.per.connection=1</li><li style="line-height:20px;">retries=Int.MaxValue</li><li style="line-height:20px;">acks=-1</li><li style="line-height:20px;">block.on.buffer.full=true</li></ol><p>For MirrorMaker, set --abortOnSendFail</p>
<p>The following actions will be taken by MirrorMaker</p>
<ul><li style="line-height:20px;">Mirror maker will only send one request to a broker at any given point.</li><li style="line-height:20px;">If any exception is caught in mirror maker thread, mirror maker will try to commit the acked offsets then exit immediately.</li><li style="line-height:20px;">For RetriableException in producer, producer will retry indefinitely. If retry did not work, eventually the entire mirror maker will block on producer buffer full.</li><li style="line-height:20px;">For None-retriable exception, if --abort.on.send.fail is specified, stops the mirror maker. Otherwise producer callback will record the message that was not successfully sent but let the mirror maker move on. <strong>In this case,
 that message will be lost in target cluster.</strong></li></ul><p>As the last point stated if there is any error occurred your mirror maker process will be killed. So users are recommend to use a watchdog process like supervisord to restart the killed mirrormaker process.</p>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
</h3>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
</h3>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
Mirror Maker Sizing</h3>
<h4 style="font-family:inherit;line-height:21px;color:inherit;font-size:17.5px;">
num.streams</h4>
<p>num.streams option in mirror-maker allows you to create specified no.of consumers.</p>
<p>Mirror-Maker deploys the specified no.of threads in num.streams</p>
<ol><li style="line-height:20px;">Each thread instantiates and uses one consumer. That is a <em>1:1</em> mapping between mirror-maker threads and consumers.</li><li style="line-height:20px;">Each thread shares the same producer. That is a <em>N:1</em> mapping between threads and producers.</li></ol><p>Keep in mind, a topic-partition is the unit of parallelism in Kafka. If you have a topic called "click-logs" with 6 partitions then max no.of consumers you can run is 6. If you run more than 6 , additional consumers
 will be idle and if you run less than 6 , all 6 partitions will be distributed among available consumers. More partitions leads to more throughput.</p>
<p>So before going further into num.streams, we recommend you to run multiple instances of mirror-maker across the machines with same "groupId" in consumer.config. This will help if a mirror-maker process goes down
 for any reason and the topic-partitions owned by killed mirror-maker will be re-balanced among other running mirror-maker processes. So this will give high-availability of mirror-maker.</p>
<p>Coming back to choosing num.streams. Lets say you've 3 topics with 4 partitions each and you are running 3 mirror maker processes. You can choose 4 as your num.streams this way each instance of mirror-maker starts
 4 consumers reading 4 topic-partitions each and writing to target cluster.</p>
<p>If you just run 1 mirror maker by choosing 4 as num.streams then 4 consumers will be reading from all 12 topic-partitions . This means lot more traffic into a single machine and it will be slower. Also if the mirror-maker
 process is stopped there are no other mirror-maker processes to take over.</p>
<p>For maximum performance, total number of num.streams should match all of the topic partitions that the mirror maker trying to copy to target cluster.</p>
<h4 style="font-family:inherit;line-height:21px;color:inherit;font-size:17.5px;">
co-locate</h4>
<p>One co-locate more than one mirror maker in a single machine. Always run more than one mirror-maker processes. Make sure you use the same groupId in consumer config.</p>
<h4 style="font-family:inherit;line-height:21px;color:inherit;font-size:17.5px;">
Socket buffer sizes</h4>
<p>In general, you should set a high value for the socket buffer size on the mirror-maker's consumer configuration (<em>socket.buffersize</em>) and the source cluster's broker configuration (socket.send.buffer). Also,
 the mirror-maker consumer's fetch size (<em>fetch.size</em>) should be higher than the consumer's socket buffer size. Note that the socket buffer size configurations are a hint to the underlying platform's networking code.</p>
<h4 style="font-family:inherit;line-height:21px;color:inherit;font-size:17.5px;">
Check the health of Mirror-Maker</h4>
<p>The consumer offset checker tool is useful to gauge how well your mirror is keeping up with the source cluster. Note that the <em>--zkconnect</em> argument should point to the source cluster's ZooKeeper. Also, if
 the topic is not specified, then the tool prints information for all topics under the given consumer group.</p>
<p>For example:</p>
<p>bin/kafka-run-class.sh kafka.tools.ConsumerOffsetChecker --group KafkaMirror --zkconnect dc1-zookeeper:2181 --topic test-topic</p>
<p>minimal lag here indicates healthy Mirror-Maker</p>
<h3 style="font-family:inherit;line-height:29.4px;color:inherit;font-size:24.5px;">
Running in Secure Clusters</h3>
<h4 style="font-family:inherit;line-height:21px;color:inherit;font-size:17.5px;">
SSL</h4>
<p>We recommend to use SSL in mirror-maker from kafka 0.10.x version onwards. You can learn more details about setting SSL for brokers, producers and consumers here</p>
<p><a href="http://kafka.apache.org/documentation.html#security_ssl" rel="nofollow" style="color:rgb(61,181,230);">http://kafka.apache.org/documentation.html#security_ssl</a> .</p>
<p>Users can share the same key &amp; trust stores for both producer &amp; consumer in Mirror-Maker. Make sure you give read &amp; write permissions for the certificate/hostname if you are using a authorizer with SSL.</p>
<h4 style="font-family:inherit;line-height:21px;color:inherit;font-size:17.5px;">
KERBEROS</h4>
<p>In kafka 0.9.x and 0.10.0.1, 0.10.1.0 , consumers and producers in mirror-maker cannot run with different principals/keytabs as they both run inside a single JVM. So the users need to use single principal to configure
 both consumer and producer. This means same principal needs to have at least read &amp; describe access on the source cluster topics and write &amp; describe access to topics on target cluster.</p>
<p>In future version of kafka users can configure different principal/keytabs for consumer &amp; producer in mirror-maker.</p>
</div>
            </div>
                </div>
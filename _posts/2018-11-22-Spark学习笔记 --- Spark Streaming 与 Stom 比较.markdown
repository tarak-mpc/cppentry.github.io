---
layout:     post
title:      Spark学习笔记 --- Spark Streaming 与 Stom 比较
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/54630089				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>
</p><table border="0" cellspacing="0" cellpadding="0" style="border:1px solid #C0C0C0;border-collapse:collapse;font-family:georgia, Verdana, Helvetica, Arial;width:672px;"><colgroup><col width="224"><col width="224"><col width="224"></colgroup><tbody><tr><td class="oa1" width="224" height="40" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">对比点</span></p>
</td>
<td class="oa1" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">Storm</span></p>
</td>
<td class="oa1" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">Spark Streaming</span></p>
</td>
</tr><tr><td class="oa2" width="224" height="67" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">实时计算模型</span></p>
</td>
<td class="oa2" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">纯实时，来一条数据，处理一条数据</span></p>
</td>
<td class="oa2" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">准实时，对一个时间段内的数据收集起来，作为一个RDD，再处理</span></p>
</td>
</tr><tr><td class="oa3" width="224" height="67" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">实时计算延迟度</span></p>
</td>
<td class="oa3" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">毫秒级</span></p>
</td>
<td class="oa3" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">秒级</span></p>
</td>
</tr><tr><td class="oa4" width="224" height="67" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">吞吐量</span></p>
</td>
<td class="oa4" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">低</span></p>
</td>
<td class="oa4" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">高</span></p>
</td>
</tr><tr><td class="oa3" width="224" height="67" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">事务机制</span></p>
</td>
<td class="oa3" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">支持完善</span></p>
</td>
<td class="oa3" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">支持，但不够完善</span></p>
</td>
</tr><tr><td class="oa4" width="224" height="67" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">健壮性 / 容错性</span></p>
</td>
<td class="oa4" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">ZooKeeper，Acker，非常强</span></p>
</td>
<td class="oa4" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">Checkpoint，WAL，一般</span></p>
</td>
</tr><tr><td class="oa3" width="224" height="67" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">动态调整并行度</span></p>
</td>
<td class="oa3" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">支持</span></p>
</td>
<td class="oa3" width="224" style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;line-height:19.5px;border:1px solid #C0C0C0;border-collapse:collapse;">
<p><span style="font-size:18px;color:#ff0000;">不支持</span></p>
</td>
</tr></tbody></table><span style="font-family:georgia, Verdana, Helvetica, Arial;font-size:14px;"><br></span>
<p><span style="font-family:georgia, Verdana, Helvetica, Arial;"></span></p>
<p style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;">
<strong><span style="color:#aaaaaa;"> </span>Spark Streaming与Storm的应用场景</strong></p>
<p style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;">
 </p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:24px;">对于Storm来说：</span><br><span style="font-size:18px;">1、建议在那种需要纯实时，不能忍受1秒以上延迟的场景下使用，比如实时金融系统，要求纯实时进行金融交易和分析<br>
2、此外，如果对于实时计算的功能中，要求可靠的事务机制和可靠性机制，即数据的处理完全精准，一条也不能多，一条也不能少，也可以考虑使用Storm<br>
3、如果还需要针对高峰低峰时间段，动态调整实时计算程序的并行度，以最大限度利用集群资源（通常是在小型公司，集群资源紧张的情况），也可以考虑用Storm</span></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:18px;">4、如果一个大数据应用系统，它就是纯粹的实时计算，不需要在中间执行SQL交互式查询、复杂的transformation算子等，那么用Storm是比较好的选择</span></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:13px;"><br></span></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:13px;"><br></span></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:24px;">对于Spark Streaming来说：</span></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:18px;"><br>
1、如果对上述适用于Storm的三点，一条都不满足的实时场景，即，不要求纯实时，不要求强大可靠的事务机制，不要求动态调整并行度，那么可以考虑使用Spark Streaming<br>
2、考虑使用Spark Streaming最主要的一个因素，应该是针对整个项目进行宏观的考虑，即，如果一个项目除了实时计算之外，还包括了离线批处理、交互式查询等业务功能，而且实时计算中，可能还会牵扯到高延迟批处理、交互式查询等功能，那么就应该首选Spark生态，用Spark Core开发离线批处理，用Spark SQL开发交互式查询，用Spark Streaming开发实时计算，三者可以无缝整合，给系统提供非常高的可扩展性</span></p>
<p style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;">
<strong><br></strong></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><strong><span style="font-size:24px;"> Spark Streaming与Storm的优劣分析</span></strong></p>
<p style="font-size:13px;font-family:georgia, Verdana, Helvetica, Arial;">
<strong><br></strong></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:18px;">事实上，Spark Streaming绝对谈不上比Storm优秀。这两个框架在实时计算领域中，都很优秀，只是擅长的细分场景并不相同。</span></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:18px;">Spark Streaming仅仅在吞吐量上比Storm要优秀，而吞吐量这一点，也是历来挺Spark Streaming，贬Storm的人着重强调的。但是问题是，是不是在所有的实时计算场景下，都那么注重吞吐量？不尽然。因此，通过吞吐量说Spark Streaming强于Storm，不靠谱。</span></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:18px;">事实上，Storm在实时延迟度上，比Spark Streaming就好多了，前者是纯实时，后者是准实时。而且，Storm的事务机制、健壮性 / 容错性、动态调整并行度等特性，都要比Spark Streaming更加优秀。</span></p>
<p style="font-family:georgia, Verdana, Helvetica, Arial;"><span style="font-size:18px;">Spark Streaming，有一点是Storm绝对比不上的，就是：它位于Spark生态技术栈中，因此Spark Streaming可以和Spark Core、Spark SQL无缝整合，也就意味着，我们可以对实时处理出来的中间数据，立即在程序中无缝进行延迟批处理、交互式查询等操作。这个特点大大增强了Spark
 Streaming的优势和功能。</span></p>
<br>            </div>
                </div>
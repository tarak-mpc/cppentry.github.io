---
layout:     post
title:      Hbase的region优化
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:15px;line-height:35px;color:rgb(51,51,51);font-family:Verdana, sans-serif;">
问题描述：线上程序在写<a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">Hbase</a>时，<a href="http://lib.csdn.net/base/hbase" rel="nofollow" class="replace_word" title="Hbase知识库" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">hbase</a>有时候会挂掉。<br>
问题详细现象：程序写hbase时写压力都集中到某一个region server上。<br>
基于以上现象研究了hbase region split机制。在默认情况下hbase会根据自动splitting机制进行region分片。当一个table刚被创建的时候，Hbase默认的分配一个region给table。也就是说这个时候，所有的读写请求都会访问到同一个regionServer的同一个region中，这个时候就达不到负载均衡的效果了，集群中的其他regionServer就可能会处于比较空闲的状态。解决这个问题可以用pre-splitting,在创建table的时候就配置好，生成多个region。<br>
Hbase自带了两种pre-split的<a href="http://lib.csdn.net/base/datastructure" rel="nofollow" class="replace_word" title="算法与数据结构知识库" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">算法</a>，分别是 HexStringSplit 和 UniformSplit 。如果我们的row
 key是十六进制的字符串作为前缀的，就比较适合用HexStringSplit，作为pre-split的算法。例如，我们使用HexHash(prefix)作为row key的前缀，其中Hexhash为最终得到十六进制字符串的hash算法。我们也可以用我们自己的split算法。<br>
pre-splitting方式有多种，下面是两种hbase shell 方式进行pre-splitting：<br>
方式一：<br></p>
<pre style="font-size:15px;line-height:35px;border:1px solid rgb(226,226,226);color:rgb(51,51,51);background-color:rgb(250,250,250);">在hbase shell 下：
hbase org.apache.hadoop.hbase.util.RegionSplitter pre_split_table HexStringSplit -c 10 -f f1
-c 10 的意思为，最终的region数目为10个；-f  f1为创建一个那么为f1的 column family.
</pre>
<br style="font-size:15px;line-height:35px;color:rgb(51,51,51);font-family:Verdana, sans-serif;"><span style="font-size:15px;line-height:35px;color:rgb(51,51,51);font-family:Verdana, sans-serif;">方式二：</span><br style="font-size:15px;line-height:35px;color:rgb(51,51,51);font-family:Verdana, sans-serif;"><pre style="font-size:15px;line-height:35px;border:1px solid rgb(226,226,226);color:rgb(51,51,51);background-color:rgb(250,250,250);">在hbase shell下：
create 't3','f1', { NUMREGIONS =&gt; 5, SPLITALGO =&gt; 'HexStringSplit' }
</pre>
<br style="font-size:15px;line-height:35px;color:rgb(51,51,51);font-family:Verdana, sans-serif;"><p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">采用方式二解决了线上hbase挂掉的问题</span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;"><br></span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">参考资料：</span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">１.　https://yq.aliyun.com/articles/43539</span></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">２.　http://hbase.apache.org/book.html#shell</span></p>
            </div>
                </div>
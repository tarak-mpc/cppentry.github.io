---
layout:     post
title:      Hbase踩坑-pre-splitting
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载务必注明出处，否则追求法律责任					https://blog.csdn.net/yanshu2012/article/details/53997371				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">问题描述：线上程序在写hbase时，hbase有时候会挂掉。<br>
问题详细现象：程序写hbase时写压力都集中到某一个region server上。<br>
基于以上现象研究了hbase region split机制。在默认情况下hbase会根据自动splitting机制进行region分片。当一个table刚被创建的时候，Hbase默认的分配一个region给table。也就是说这个时候，所有的读写请求都会访问到同一个regionServer的同一个region中，这个时候就达不到负载均衡的效果了，集群中的其他regionServer就可能会处于比较空闲的状态。解决这个问题可以用pre-splitting,在创建table的时候就配置好，生成多个region。<br>
Hbase自带了两种pre-split的算法，分别是 HexStringSplit 和 UniformSplit 。如果我们的row key是十六进制的字符串作为前缀的，就比较适合用HexStringSplit，作为pre-split的算法。例如，我们使用HexHash(prefix)作为row key的前缀，其中Hexhash为最终得到十六进制字符串的hash算法。我们也可以用我们自己的split算法。<br>
pre-splitting方式有多种，下面是两种hbase shell 方式进行pre-splitting：<br>
方式一：<br></p>
<pre style="background-color:rgb(250,250,250);border:1px solid rgb(226,226,226);color:rgb(51,51,51);">在hbase shell 下：
hbase org.apache.hadoop.hbase.util.RegionSplitter pre_split_table HexStringSplit -c 10 -f f1
-c 10 的意思为，最终的region数目为10个；-f  f1为创建一个那么为f1的 column family.
</pre>
<br style="color:rgb(51,51,51);font-family:Verdana, sans-serif;"><span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">方式二：</span><br style="color:rgb(51,51,51);font-family:Verdana, sans-serif;"><pre style="background-color:rgb(250,250,250);border:1px solid rgb(226,226,226);color:rgb(51,51,51);">在hbase shell下：
create 't3','f1', { NUMREGIONS =&gt; 5, SPLITALGO =&gt; 'HexStringSplit' }
</pre>
<br style="color:rgb(51,51,51);font-family:Verdana, sans-serif;"><p><span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">采用方式二解决了线上hbase挂掉的问题</span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;"><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">参考资料：</span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">１.　https://yq.aliyun.com/articles/43539</span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;">２.　http://hbase.apache.org/book.html#shell</span></p>
<p><span style="color:rgb(51,51,51);font-family:Verdana, sans-serif;"><br></span></p>
            </div>
                </div>
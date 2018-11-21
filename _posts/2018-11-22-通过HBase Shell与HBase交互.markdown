---
layout:     post
title:      通过HBase Shell与HBase交互
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 class="article-title" style="font-size:20px;color:rgb(32,120,169);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;line-height:19.5px;">
出处：<a href="http://www.taobaotest.com/blogs/1604" rel="nofollow">http://www.taobaotest.com/blogs/1604</a><br></h3>
<h3 class="article-title" style="font-size:20px;color:rgb(32,120,169);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;line-height:19.5px;">
业务开发测试HBase之旅二：通过HBase Shell与HBase交互</h3>
<div class="article-info" style="color:rgb(153,153,153);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;line-height:19.5px;">
<span><a href="http://www.taobaotest.com/users/qa/blogs?uid=222" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;">yedu</a></span> <span>发表于：2011-10-11</span> <span>浏览：9029次</span> <span>评论：<a href="http://www.taobaotest.com/blogs/qa?bid=13871#reply-comment" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;">1次</a></span> <span>所属分类： <a href="http://www.taobaotest.com/categories/12/blogs" rel="nofollow" title="分布式系统测试" style="color:rgb(19,85,135);text-decoration:none;">分布式系统测试</a></span></div>
<div class="article-body cssbase" style="line-height:1.8em;vertical-align:top;color:rgb(51,51,51);font-family:'微软雅黑', Verdana, Arial, Helvetica, sans-serif;font-size:13px;">
<strong>引言</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
HBase提供了丰富的访问接口。
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
• HBase Shell
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
• Java clietn API
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
• Jython、Groovy DSL、Scala
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
• REST
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
• Thrift（Ruby、Python、Perl、C++…）
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
• MapReduce
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
• Hive/Pig
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
其中HBase Shell是常用的便捷方式，我们将结合本系列<a href="http://www.taobaotest.com/blogs/1582" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;">上一篇文章</a>的理论分析来实践一把，依然采用<a href="http://www.taobaotest.com/files/attachments/qa_1604_1.jpg" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;">blog表示例</a>。
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
首先你需要一个HBase的环境，如果需要自己搭建可以参考http://hbase.apache.org/book/quickstart.html 和http://hbase.apache.org/book/notsoquick.html。 如果你在windows环境下配置cygwin及ssh遇到问题可以参考 http://www.taobaotest.com/blogs/1688。
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<strong>进入HBase shell控制台</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt;bin/hbase shell</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<a href="http://www.taobaotest.com/files/attachments/qa_1604_2.jpg" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;"><img src="http://www.taobaotest.com/files/attachments/qa_1604_2.jpg" alt="" width="697" height="148" class="alignnone size-full wp-image-13876"></a>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
看来控制台是靠jruby语言解析的。输入“help”可以快速扫描下支持那些命令。
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<strong>创建表</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt; create 'blog','article','author'</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<span style="color:#008000;">知识点回顾：Column Family是schema的一部分，而Column不是。这里的article和author是Column Family</span>。
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<strong>增加记录</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt;put 'blog','1','article:title,' Head First HBase '</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
&gt;put 'blog','1','article:content','HBase is the Hadoop database. Use it when you need random, realtime read/write access to your Big Data.'
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
&gt; put 'blog','1','article:tags','Hadoop,HBase,NoSQL'
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
&gt; put 'blog','1','author:name','hujinjun'
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
&gt; put 'blog','1','author:nickname',’一叶渡江’
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<span style="color:#008000;">知识点回顾：Column完全动态扩展，每行可以有不同的Columns。</span>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<strong>根据RowKey查询</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt; get 'blog','1'</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<a href="http://www.taobaotest.com/files/attachments/qa_1604_3.jpg" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;"><img src="http://www.taobaotest.com/files/attachments/qa_1604_3.jpg" alt="" width="1261" height="171" class="alignnone size-full wp-image-13882"></a>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<span style="color:#008000;">知识点回顾：HTable按RowKey字典序（1,10,100,11,2）自动排序，每行包含任意数量</span>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
的Columns，Columns按ColumnKey（article:content,article:tags,article:title,author:name,author:nickname）自动排序
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<strong>更新练习</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
查询下更新前的值:
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt; get ‘blog’,’1’,’author:nickname’</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<a href="http://www.taobaotest.com/files/attachments/qa_1604_4.jpg" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;"><img src="http://www.taobaotest.com/files/attachments/qa_1604_4.jpg" alt="" width="1045" height="85" class="alignnone size-full wp-image-13886"></a>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
更新nickname为’yedu’:
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt; put ‘blog’,’1’,’ahthor:nickname’,’yedu’</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
查询更新后的结果：
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt; get ‘blog’,’1’,’author:nickname’</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<a href="http://www.taobaotest.com/files/attachments/qa_1604_5.jpg" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;"><img src="http://www.taobaotest.com/files/attachments/qa_1604_5.jpg" alt="" width="652" height="80" class="alignnone size-full wp-image-13887"></a>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<span style="color:#008000;">知识点回顾：查询默认返回最近的值。</span>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
查询nickname的多个(本示例为2个)版本值
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt; get 'blog','1',{COLUMN =&gt; 'author:nickname',VERSIONS =&gt; 2}</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<a href="http://www.taobaotest.com/files/attachments/qa_1604_6.jpg" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;"><img src="http://www.taobaotest.com/files/attachments/qa_1604_6.jpg" alt="" width="1041" height="104" class="alignnone size-full wp-image-13888"></a>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<span style="color:#008000;">知识点回顾：每个Column可以有任意数量的Values，按Timestamp倒序自动排序。</span>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
如何只查询到以前的旧版本呢，需要借助Timestamp
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt;get 'blog','1',{COLUMN =&gt; 'author:nickname', TIMESTAMP =&gt; 1317180070811}</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<a href="http://www.taobaotest.com/files/attachments/qa_1604_7.jpg" rel="nofollow" style="color:rgb(19,85,135);text-decoration:none;"><img src="http://www.taobaotest.com/files/attachments/qa_1604_7.jpg" alt="" width="1040" height="93" class="alignnone size-full wp-image-13889"></a>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<span style="color:#008000;">知识点回顾：TabelName+RowKey+Column+Timestamp=&gt;Value</span>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<strong>删除记录</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
delete只能删除一个column
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt;delete 'blog','1','author:nickname'</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
删除RowKey的所有column用deleteall
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt;deleteall ‘blog’,’1’</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<strong>删除表</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
练习完毕，把练习表删了吧，删除之前需要先disable
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<code style="border:1px dotted rgb(211,211,211);background-color:rgb(233,233,233);display:block;font-size:11px;">&gt;disable ‘blog’</code>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
&gt;drop ‘blog’
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
<strong>小结</strong>
<div class="qa-gap" style="clear:both;line-height:1px;">
</div>
本文演示了通过HBase shell创建、删除表及对记录的增删改查，可以参照操作结果对回顾的知识点进一步理解掌握，在本系列下一篇文章中讲演示如何通过Java api来与HBase交互。</div>
            </div>
                </div>
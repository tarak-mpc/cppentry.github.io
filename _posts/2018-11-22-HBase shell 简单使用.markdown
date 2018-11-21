---
layout:     post
title:      HBase shell 简单使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:Arial;line-height:25px;"></span>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>引言</strong><br>
HBase提供了丰富的访问接口。<br>
　　• HBase Shell<br>
　　• Java clietn API<br>
　　• Jython、Groovy DSL、Scala<br>
　　• REST<br>
　　• Thrift（Ruby、Python、Perl、C++…）<br>
　　• MapReduce<br>
　　• Hive/Pig<br>
其中HBase Shell是常用的便捷方式</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
首先你需要一个HBase的环境，如果需要自己搭建可以参考http://hbase.apache.org/book/quickstart.html 和<a href="http://hbase.apache.org/book/notsoquick.html" rel="nofollow" style="color:#1d58d1;text-decoration:none;">http://hbase.apache.org/book/notsoquick.html</a>。</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
如果你在windows环境下配置cygwin及ssh遇到问题可以参考 http://qa.taobao.com/?p=10633。</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>进入HBase shell控制台</strong><br>
&gt;bin/hbase shell<br><a href="http://qa.taobao.com/wp-content/uploads/2011/10/HBase-shellConsole.jpg" rel="nofollow" style="color:#1d58d1;text-decoration:none;"> </a>输入“help”可以快速扫描下支持那些命令。</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>创建表</strong><br>
&gt; create 'blog','article','author'<br>
知识点回顾：Column Family是schema的一部分，而Column不是。这里的article和author是Column Family。</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>查询所有表</strong></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
&gt;list</p>
<p style="margin-left:auto;text-indent:0px;">
<img src="http://pic002.cnblogs.com/images/2012/387542/2012040615373977.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>增加记录</strong><br>
&gt;put 'blog','1','article:title,' Head First HBase '<br>
&gt;put 'blog','1','article:content','HBase is the Hadoop database. Use it when you need random, realtime read/write access to your Big Data.'<br>
&gt; put 'blog','1','article:tags','Hadoop,HBase,NoSQL'<br>
&gt; put 'blog','1','author:name','hujinjun'<br>
&gt; put 'blog','1','author:nickname',’一叶渡江’<br>
知识点回顾：Column完全动态扩展，每行可以有不同的Columns。</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>根据RowKey查询</strong><br>
&gt; get 'blog','1'</p>
<p style="margin-left:auto;text-indent:0px;">
<img src="http://pic002.cnblogs.com/images/2012/387542/2012040615085088.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
知识点回顾：HTable按RowKey字典序（1,10,100,11,2）自动排序，每行包含任意数量<br>
的Columns，Columns按ColumnKey（article:content,article:tags,article:title,author:name,author:nickname）自动排序</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>查询列族</strong></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
&gt;get 'blog','1','author</p>
<p style="margin-left:auto;text-indent:0px;">
<img src="http://pic002.cnblogs.com/images/2012/387542/2012040615423859.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>查看表内容</strong></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
&gt;scan 'blog'</p>
<p style="margin-left:auto;text-indent:0px;">
<img src="http://pic002.cnblogs.com/images/2012/387542/2012040615401217.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>更新练习</strong></p>
<ul style="list-style-type:disc;margin-left:45px;"><li style="list-style:inherit;">查询下更新前的值:</li></ul><p align="left" style="margin-left:auto;text-indent:0px;">
&gt; get ‘blog’,’1’,’author:nickname’</p>
<p style="margin-left:auto;text-indent:0px;">
<img src="http://pic002.cnblogs.com/images/2012/387542/2012040615115240.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<ul style="list-style-type:disc;margin-left:45px;"><li style="list-style:inherit;">更新nickname为’yedu’:</li></ul><p align="left" style="margin-left:auto;text-indent:0px;">
&gt; put ‘blog’,’1’,’ahthor:nickname’,’yedu’</p>
<ul style="list-style-type:disc;margin-left:45px;"><li style="list-style:inherit;">查询更新后的结果：</li></ul><p align="left" style="margin-left:auto;text-indent:0px;">
&gt; get ‘blog’,’1’,’author:nickname’</p>
<p style="margin-left:auto;text-indent:0px;">
<img src="http://pic002.cnblogs.com/images/2012/387542/2012040615143191.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
知识点回顾：查询默认返回最近的值。</p>
<ul style="list-style-type:disc;margin-left:45px;"><li style="list-style:inherit;">查询nickname的多个(本示例为2个)版本值</li></ul><p align="left" style="margin-left:auto;text-indent:0px;">
&gt; get 'blog','1',{COLUMN =&gt; 'author:nickname',VERSIONS =&gt; 2}</p>
<p style="margin-left:auto;text-indent:0px;">
<img src="http://pic002.cnblogs.com/images/2012/387542/2012040615172768.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"><br>
知识点回顾：每个Column可以有任意数量的Values，按Timestamp倒序自动排序。</p>
<p style="margin-left:auto;text-indent:0px;">
当修改多次（大于3次，修改2次时也就有3个VERSIONS）时：VERSIONS默认最高为3</p>
<p style="margin-left:auto;text-indent:0px;">
&gt; get 'blog','1',{COLUMN =&gt; 'author:nickname',VERSIONS =&gt; 4}</p>
<p style="margin-left:auto;text-indent:0px;">
<img src="http://pic002.cnblogs.com/images/2012/387542/2012040615215412.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<ul style="list-style-type:disc;margin-left:45px;"><li style="list-style:inherit;">如何只查询到以前的旧版本呢，需要借助Timestamp</li></ul><p align="left" style="margin-left:auto;text-indent:0px;">
&gt;get 'blog','1',{COLUMN =&gt; 'author:nickname', TIMESTAMP =&gt; 1333690828877}<br><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615344026.png" alt="" style="border-top-width:0px;border-right-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
知识点回顾：TabelName+RowKey+Column+Timestamp=&gt;Value</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>删除记录</strong></p>
<ul style="list-style-type:disc;margin-left:45px;"><li style="list-style:inherit;">delete只能删除一个column</li></ul><p align="left" style="margin-left:auto;text-indent:0px;">
&gt;delete 'blog','1','author:nickname'</p>
<ul style="list-style-type:disc;margin-left:45px;"><li style="list-style:inherit;">删除RowKey的所有column用deleteall</li></ul><p align="left" style="margin-left:auto;text-indent:0px;">
&gt;deleteall ‘blog’,’1’<br><strong>删除表</strong><br>
练习完毕，把练习表删了吧，删除之前需要先disable<br>
&gt;disable ‘blog’<br>
&gt;drop ‘blog’</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<strong>小结</strong><br>
本文演示了通过HBase shell创建、删除表及对记录的增删改查，可以参照操作结果对回顾的知识点进一步理解掌握，在本系列下一篇文章中讲演示如何通过Java api来与HBase交互。</p>
<p align="left" style="margin-left:auto;text-indent:0px;">
<br></p>
<p align="left" style="margin-left:auto;text-indent:0px;">
该博文转载自<a href="http://www.cnblogs.com/heyCoding/archive/2012/11/09/2762334.html" rel="nofollow">http://www.cnblogs.com/heyCoding/archive/2012/11/09/2762334.html</a><br></p>
            </div>
                </div>
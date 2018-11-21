---
layout:     post
title:      HBase shell 简单使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2></h2>
<div class="postText">
<div id="cnblogs_post_body">
<div><span style="line-height:25px;font-family:Arial;"></span>
<p align="left"><strong>引言</strong><br>
HBase提供了丰富的访问接口。<br>
　　• HBase Shell<br>
　　• Java clietn API<br>
　　• Jython、Groovy DSL、Scala<br>
　　• REST<br>
　　• Thrift（Ruby、Python、Perl、C++…）<br>
　　• MapReduce<br>
　　• Hive/Pig<br>
其中HBase Shell是常用的便捷方式</p>
<p align="left">首先你需要一个HBase的环境，如果需要自己搭建可以参考http://hbase.apache.org/book/quickstart.html 和<a href="http://hbase.apache.org/book/notsoquick.html" rel="nofollow" style="color:#1d58d1;text-decoration:none;">http://hbase.apache.org/book/notsoquick.html</a>。</p>
<p align="left">如果你在windows环境下配置cygwin及ssh遇到问题可以参考 http://qa.taobao.com/?p=10633。</p>
<p align="left"><strong>进入HBase shell控制台</strong><br>
&gt;bin/hbase shell<br><a href="http://qa.taobao.com/wp-content/uploads/2011/10/HBase-shellConsole.jpg" rel="nofollow" style="color:#1d58d1;text-decoration:none;"> </a>输入“help”可以快速扫描下支持那些命令。</p>
<p align="left"><strong>创建表</strong><br>
&gt; create 'blog','article','author'<br>
知识点回顾：Column Family是schema的一部分，而Column不是。这里的article和author是Column Family。</p>
<p align="left"><strong>查询所有表</strong></p>
<p align="left">&gt;list</p>
<p><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615373977.png" style="border-right-width:0px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left"><strong>增加记录</strong><br>
&gt;put 'blog','1','article:title,' Head First HBase '<br>
&gt;put 'blog','1','article:content','HBase is the Hadoop database. Use it when you need random, realtime read/write access to your Big Data.'<br>
&gt; put 'blog','1','article:tags','Hadoop,HBase,NoSQL'<br>
&gt; put 'blog','1','author:name','hujinjun'<br>
&gt; put 'blog','1','author:nickname',’一叶渡江’<br>
知识点回顾：Column完全动态扩展，每行可以有不同的Columns。</p>
<p align="left"><strong>根据RowKey查询</strong><br>
&gt; get 'blog','1'</p>
<p><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615085088.png" style="border-right-width:0px;width:655px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left">知识点回顾：HTable按RowKey字典序（1,10,100,11,2）自动排序，每行包含任意数量<br>
的Columns，Columns按ColumnKey（article:content,article:tags,article:title,author:name,author:nickname）自动排序</p>
<p align="left"><strong>查询列族</strong></p>
<p align="left">&gt;get 'blog','1','author</p>
<p><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615423859.png" style="border-right-width:0px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left"><strong>查看表内容</strong></p>
<p align="left">&gt;scan 'blog'</p>
<p><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615401217.png" style="border-right-width:0px;width:655px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left"><strong>更新练习</strong></p>
<ul style="margin-left:45px;"><li style="list-style:inherit;">查询下更新前的值:</li></ul><p align="left">&gt; get ‘blog’,’1’,’author:nickname’</p>
<p><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615115240.png" style="border-right-width:0px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<ul style="margin-left:45px;"><li style="list-style:inherit;">更新nickname为’yedu’:</li></ul><p align="left">&gt; put ‘blog’,’1’,’ahthor:nickname’,’yedu’</p>
<ul style="margin-left:45px;"><li style="list-style:inherit;">查询更新后的结果：</li></ul><p align="left">&gt; get ‘blog’,’1’,’author:nickname’</p>
<p><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615143191.png" style="border-right-width:0px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left">知识点回顾：查询默认返回最近的值。</p>
<ul style="margin-left:45px;"><li style="list-style:inherit;">查询nickname的多个(本示例为2个)版本值</li></ul><p align="left">&gt; get 'blog','1',{COLUMN =&gt; 'author:nickname',VERSIONS =&gt; 2}</p>
<p><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615172768.png" style="border-right-width:0px;width:655px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"><br>
知识点回顾：每个Column可以有任意数量的Values，按Timestamp倒序自动排序。</p>
<p>当修改多次（大于3次，修改2次时也就有3个VERSIONS）时：VERSIONS默认最高为3</p>
<p>&gt; get 'blog','1',{COLUMN =&gt; 'author:nickname',VERSIONS =&gt; 4}</p>
<p><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615215412.png" style="border-right-width:0px;width:655px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<ul style="margin-left:45px;"><li style="list-style:inherit;">如何只查询到以前的旧版本呢，需要借助Timestamp</li></ul><p align="left">&gt;get 'blog','1',{COLUMN =&gt; 'author:nickname', TIMESTAMP =&gt; 1333690828877}<br><img alt="" src="http://pic002.cnblogs.com/images/2012/387542/2012040615344026.png" style="border-right-width:0px;width:655px;border-top-width:0px;border-bottom-width:0px;border-left-width:0px;"></p>
<p align="left">知识点回顾：TabelName+RowKey+Column+Timestamp=&gt;Value</p>
<p align="left"><strong>删除记录</strong></p>
<ul style="margin-left:45px;"><li style="list-style:inherit;">delete只能删除一个column</li></ul><p align="left">&gt;delete 'blog','1','author:nickname'</p>
<ul style="margin-left:45px;"><li style="list-style:inherit;">删除RowKey的所有column用deleteall</li></ul><p align="left">&gt;deleteall ‘blog’,’1’<br><strong>删除表</strong><br>
练习完毕，把练习表删了吧，删除之前需要先disable<br>
&gt;disable ‘blog’<br>
&gt;drop ‘blog’</p>
<p align="left"><strong>小结</strong><br>
本文演示了通过HBase shell创建、删除表及对记录的增删改查，可以参照操作结果对回顾的知识点进一步理解掌握，在本系列下一篇文章中讲演示如何通过Java api来与HBase交互。</p>
<p>内容主要来自：<a href="http://hi.baidu.com/jrckkyy/blog/item/b5e0204595994e2086947370.html" rel="nofollow" style="color:#1d58d1;text-decoration:none;">http://hi.baidu.com/jrckkyy/blog/item/b5e0204595994e2086947370.html</a></p>
</div>
</div>
</div>
            </div>
                </div>
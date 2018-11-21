---
layout:     post
title:      Hbase shell详情
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
HBase 为用户提供了一个非常方便的使用方式, 我们称之为“HBase Shell”。<br>
HBase Shell 提供了大多数的 HBase 命令, 通过 HBase Shell 用户可以方便地创建、删除及修改表, 还可以向表中添加数据、列出表中的相关信息等。<br>
备注:写错 HBase Shell 命令时用键盘上的“Delete”进行删除,“Backspace”不起作用。<br>
在启动 HBase 之后,用户可以通过下面的命令进入 HBase Shell 之中,命令如下所示:</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;">
<pre style="font-family:'Courier New' !important;">hadoop@ubuntu:~<span style="line-height:1.5 !important;">$ hbase shell
HBase Shell; enter </span><span style="color:rgb(128,0,0);line-height:1.5 !important;">'</span><span style="color:rgb(128,0,0);line-height:1.5 !important;">help&lt;RETURN&gt;</span><span style="color:rgb(128,0,0);line-height:1.5 !important;">'</span> <span style="color:rgb(0,0,255);line-height:1.5 !important;">for</span><span style="line-height:1.5 !important;"> list of supported commands.
Type </span><span style="color:rgb(128,0,0);line-height:1.5 !important;">"</span><span style="color:rgb(128,0,0);line-height:1.5 !important;">exit&lt;RETURN&gt;</span><span style="color:rgb(128,0,0);line-height:1.5 !important;">"</span><span style="line-height:1.5 !important;"> to leave the HBase Shell
Version </span><span style="color:rgb(128,0,128);line-height:1.5 !important;">0.94</span>.<span style="color:rgb(128,0,128);line-height:1.5 !important;">3</span>, r1408904, Wed Nov <span style="color:rgb(128,0,128);line-height:1.5 !important;">14</span> <span style="color:rgb(128,0,128);line-height:1.5 !important;">19</span>:<span style="color:rgb(128,0,128);line-height:1.5 !important;">55</span>:<span style="color:rgb(128,0,128);line-height:1.5 !important;">11</span> UTC <span style="color:rgb(128,0,128);line-height:1.5 !important;">2012</span><span style="line-height:1.5 !important;">

hbase(main):</span><span style="color:rgb(128,0,128);line-height:1.5 !important;">001</span>:<span style="color:rgb(128,0,128);line-height:1.5 !important;">0</span>&gt; </pre>
</div>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
具体的 HBase Shell 命令如下表 1.1-1 所示:</p>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<img src="http://images.cnitblog.com/blog/270324/201303/08100657-8ce1d945bd7f4d6cad72918b8fa937ef.png" alt="" style="border:0px;"></p>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
下面我们将以“一个学生成绩表”的例子来详细介绍常用的 HBase 命令及其使用方法。</p>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<img src="http://images.cnitblog.com/blog/270324/201303/08115010-abeef6be3ee646d6aeab4b7ac28fa074.png" alt="" style="border:0px;"><br>
这里 grad 对于表来说是一个列,course 对于表来说是一个列族,这个列族由三个列组成 china、math 和 english,当然我们可以根据我们的需要在 course 中建立更多的列族,如computer,physics 等相应的列添加入 course 列族。(备注:列族下面的列也是可以没有名字的。)<br><span style="line-height:1.5;color:rgb(0,0,255);">1). create 命令</span><br>
创建一个具有两个列族“grad”和“course”的表“scores”。其中表名、行和列都要用单引号括起来,并以逗号隔开。<br>
hbase(main):012:0&gt; create 'scores', 'name', 'grad', 'course'<br><br><span style="line-height:1.5;color:rgb(0,0,255);">2). list 命令</span><br>
查看当前 HBase 中具有哪些表。<br>
hbase(main):012:0&gt; list<br><br><span style="line-height:1.5;color:rgb(0,0,255);">3). describe 命令</span><br>
查看表“scores”的构造。<br>
hbase(main):012:0&gt; describe 'scores'<br><br><span style="line-height:1.5;color:rgb(0,0,255);">4). put 命令</span><br>
使用 put 命令向表中插入数据,参数分别为表名、行名、列名和值,其中列名前需要列族最为前缀,时间戳由系统自动生成。<br>
格式: put 表名,行名,列名([列族:列名]),值<br>
例子：<br>
a. 加入一行数据,行名称为“xiapi”,列族“grad”的列名为”(空字符串)”,值位 1。<br>
hbase(main):012:0&gt; put 'scores', 'xiapi', 'grad:', '1'<br>
hbase(main):012:0&gt; put 'scores', 'xiapi', 'grad:', '2' --修改操作(update)<br>
b. 给“xiapi”这一行的数据的列族“course”添加一列“&lt;china,97&gt;”。<br>
hbase(main):012:0&gt; put 'scores', 'xiapi',  'course:china', '97'<br>
hbase(main):012:0&gt; put 'scores', 'xiapi',  'course:math', '128'<br>
hbase(main):012:0&gt; put 'scores', 'xiapi',  'course:english', '85'<br><br><span style="line-height:1.5;color:rgb(0,0,255);">5). get 命令</span><br>
a.查看表“scores”中的行“xiapi”的相关数据。<br>
hbase(main):012:0&gt; get 'scores', 'xiapi'<br>
b.查看表“scores”中行“xiapi”列“course :math”的值。<br>
hbase(main):012:0&gt; get 'scores', 'xiapi', 'course :math'<br>
或者<br>
hbase(main):012:0&gt; get 'scores', 'xiapi', {COLUMN=&gt;'course:math'}<br>
hbase(main):012:0&gt; get 'scores', 'xiapi', {COLUMNS=&gt;'course:math'}<br>
备注:COLUMN 和 COLUMNS 是不同的,scan 操作中的 COLUMNS 指定的是表的列族, get操作中的 COLUMN 指定的是特定的列,COLUMNS 的值实质上为“列族:列修饰符”。<strong><span style="line-height:1.5;color:rgb(255,0,0);">COLUMN 和 COLUMNS 必须为大写。</span></strong><br><br><span style="line-height:1.5;color:rgb(0,0,255);">6). scan 命令</span><br>
a. 查看表“scores”中的所有数据。<br>
hbase(main):012:0&gt; scan 'scores'<br>
注意:<br>
scan 命令可以指定 startrow,stoprow 来 scan 多个 row。<br>
例如:<br>
scan 'user_test',{COLUMNS =&gt;'info:username',LIMIT =&gt;10, STARTROW =&gt; 'test', STOPROW=&gt;'test2'}<br>
b.查看表“scores”中列族“course”的所有数据。<br>
hbase(main):012:0&gt; scan  'scores', {COLUMN =&gt; 'grad'}<br>
hbase(main):012:0&gt; scan  'scores', {COLUMN=&gt;'course:math'}<br>
hbase(main):012:0&gt; scan  'scores', {COLUMNS =&gt; 'course'}<br>
hbase(main):012:0&gt; scan  'scores', {COLUMNS =&gt; 'course'}<br><br><span style="line-height:1.5;color:rgb(0,0,255);">7). count 命令</span><br>
hbase(main):068:0&gt; count 'scores'<br><br><span style="line-height:1.5;color:rgb(0,0,255);">8). exists 命令</span><br>
hbase(main):071:0&gt; exists 'scores'<br><br><span style="line-height:1.5;color:rgb(0,0,255);">9). incr 命令(赋值)</span><br><br><span style="line-height:1.5;color:rgb(0,0,255);">10). delete 命令</span><br>
删除表“scores”中行为“xiaoxue”, 列族“course”中的“math”。<br>
hbase(main):012:0&gt;  delete 'scores', 'xiapi', 'course:math'<br><br><span style="line-height:1.5;color:rgb(0,0,255);">11). truncate 命令</span><br>
hbase(main):012:0&gt;  truncate 'scores'<br><br><span style="line-height:1.5;color:rgb(0,0,255);">12). disbale、drop 命令</span><br>
通过“disable”和“drop”命令删除“scores”表。<br>
hbase(main):012:0&gt;  disable 'scores' --enable 'scores' <br>
hbase(main):012:0&gt;  drop 'scores'<br><br><span style="line-height:1.5;color:rgb(0,0,255);">13).  status命令</span><br>
hbase(main):072:0&gt; status<br><br><span style="line-height:1.5;color:rgb(0,0,255);">14).  version命令</span><br>
hbase(main):073:0&gt; version<br><br>
另外,在 shell 中,常量不需要用引号引起来,但二进制的值需要双引号引起来,而其他值则用单引号引起来。HBase Shell 的常量可以通过在 shell 中输入“Object.constants”。<br></p>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<br></p>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
<br></p>
<p style="line-height:19px;color:rgb(75,75,75);font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;font-size:13px;">
</p>
<div id="cnblogs_post_body" style="color:rgb(51,51,51);font-family:Verdana, Arial, sans-serif, 'Lucida Grande';font-size:13px;background-color:rgb(214,211,214);line-height:1.8 !important;">
<div><span style="font-family:Arial;line-height:25px;"></span>
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
<p align="left">首先你需要一个HBase的环境，如果需要自己搭建可以参考http://hbase.apache.org/book/quickstart.html 和<a href="http://hbase.apache.org/book/notsoquick.html" rel="nofollow" style="color:rgb(29,88,209);text-decoration:none;">http://hbase.apache.org/book/notsoquick.html</a>。</p>
<p align="left">如果你在windows环境下配置cygwin及ssh遇到问题可以参考 http://qa.taobao.com/?p=10633。</p>
<p align="left"><strong>进入HBase shell控制台</strong><br>
&gt;bin/hbase shell<br><a href="http://qa.taobao.com/wp-content/uploads/2011/10/HBase-shellConsole.jpg" rel="nofollow" style="color:rgb(29,88,209);text-decoration:none;"> </a>输入“help”可以快速扫描下支持那些命令。</p>
<p align="left"><strong>创建表</strong><br>
&gt; create 'blog','article','author'<br>
知识点回顾：Column Family是schema的一部分，而Column不是。这里的article和author是Column Family。</p>
<p align="left"><strong>查询所有表</strong></p>
<p align="left">&gt;list</p>
<p><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615373977.png" alt="" style="border:0px;"></p>
<p align="left"><strong>增加记录</strong><br>
&gt;put 'blog','1','article:title,' Head First HBase '<br>
&gt;put 'blog','1','article:content','HBase is the Hadoop database. Use it when you need random, realtime read/write access to your Big Data.'<br>
&gt; put 'blog','1','article:tags','Hadoop,HBase,NoSQL'<br>
&gt; put 'blog','1','author:name','hujinjun'<br>
&gt; put 'blog','1','author:nickname',’一叶渡江’<br>
知识点回顾：Column完全动态扩展，每行可以有不同的Columns。</p>
<p align="left"><strong>根据RowKey查询</strong><br>
&gt; get 'blog','1'</p>
<p><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615085088.png" alt="" style="border:0px;width:655px;"></p>
<p align="left">知识点回顾：HTable按RowKey字典序（1,10,100,11,2）自动排序，每行包含任意数量<br>
的Columns，Columns按ColumnKey（article:content,article:tags,article:title,author:name,author:nickname）自动排序</p>
<p align="left"><strong>查询列族</strong></p>
<p align="left">&gt;get 'blog','1','author</p>
<p><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615423859.png" alt="" style="border:0px;"></p>
<p align="left"><strong>查看表内容</strong></p>
<p align="left">&gt;scan 'blog'</p>
<p><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615401217.png" alt="" style="border:0px;width:655px;"></p>
<p align="left"><strong>更新练习</strong></p>
<ul style="margin-left:45px;"><li style="font-size:10pt;list-style:inherit;">查询下更新前的值:</li></ul><p align="left">&gt; get ‘blog’,’1’,’author:nickname’</p>
<p><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615115240.png" alt="" style="border:0px;"></p>
<ul style="margin-left:45px;"><li style="font-size:10pt;list-style:inherit;">更新nickname为’yedu’:</li></ul><p align="left">&gt; put ‘blog’,’1’,’ahthor:nickname’,’yedu’</p>
<ul style="margin-left:45px;"><li style="font-size:10pt;list-style:inherit;">查询更新后的结果：</li></ul><p align="left">&gt; get ‘blog’,’1’,’author:nickname’</p>
<p><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615143191.png" alt="" style="border:0px;"></p>
<p align="left">知识点回顾：查询默认返回最近的值。</p>
<ul style="margin-left:45px;"><li style="font-size:10pt;list-style:inherit;">查询nickname的多个(本示例为2个)版本值</li></ul><p align="left">&gt; get 'blog','1',{COLUMN =&gt; 'author:nickname',VERSIONS =&gt; 2}</p>
<p><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615172768.png" alt="" style="border:0px;width:655px;"><br>
知识点回顾：每个Column可以有任意数量的Values，按Timestamp倒序自动排序。</p>
<p>当修改多次（大于3次，修改2次时也就有3个VERSIONS）时：VERSIONS默认最高为3</p>
<p>&gt; get 'blog','1',{COLUMN =&gt; 'author:nickname',VERSIONS =&gt; 4}</p>
<p><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615215412.png" alt="" style="border:0px;width:655px;"></p>
<ul style="margin-left:45px;"><li style="font-size:10pt;list-style:inherit;">如何只查询到以前的旧版本呢，需要借助Timestamp</li></ul><p align="left">&gt;get 'blog','1',{COLUMN =&gt; 'author:nickname', TIMESTAMP =&gt; 1333690828877}<br><img src="http://pic002.cnblogs.com/images/2012/387542/2012040615344026.png" alt="" style="border:0px;width:655px;"></p>
<p align="left">知识点回顾：TabelName+RowKey+Column+Timestamp=&gt;Value</p>
<p align="left"><strong>删除记录</strong></p>
<ul style="margin-left:45px;"><li style="font-size:10pt;list-style:inherit;">delete只能删除一个column</li></ul><p align="left">&gt;delete 'blog','1','author:nickname'</p>
<ul style="margin-left:45px;"><li style="font-size:10pt;list-style:inherit;">删除RowKey的所有column用deleteall</li></ul><p align="left">&gt;deleteall ‘blog’,’1’<br><strong>删除表</strong><br>
练习完毕，把练习表删了吧，删除之前需要先disable<br>
&gt;disable ‘blog’<br>
&gt;drop ‘blog’</p>
<p align="left"><strong>小结</strong><br>
本文演示了通过HBase shell创建、删除表及对记录的增删改查，可以参照操作结果对回顾的知识点进一步理解掌握，在本系列下一篇文章中讲演示如何通过Java api来与HBase交互。</p>
<p>内容主要来自：<a href="http://hi.baidu.com/jrckkyy/blog/item/b5e0204595994e2086947370.html" rel="nofollow" style="color:rgb(29,88,209);text-decoration:none;">http://hi.baidu.com/jrckkyy/blog/item/b5e0204595994e2086947370.html</a></p>
<div><br></div>
</div>
</div>
<div class="clear" style="clear:both;color:rgb(51,51,51);font-family:Verdana, Arial, sans-serif, 'Lucida Grande';font-size:13px;line-height:19px;background-color:rgb(214,211,214);">
</div>
<div id="blog_post_info_block" style="color:rgb(51,51,51);font-family:Verdana, Arial, sans-serif, 'Lucida Grande';font-size:13px;line-height:19px;background-color:rgb(214,211,214);">
</div>
<br><p></p>
            </div>
                </div>
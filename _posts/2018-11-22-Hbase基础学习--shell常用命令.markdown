---
layout:     post
title:      Hbase基础学习--shell常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/ASIA_kobe/article/details/50817871				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>1、启动Hbase shell</h2>
<div>进入HBase安装目录，输入 ./hbase shell</div>
<div><pre><code class="language-html">[hadoop@Master bin]$ ./hbase shell
2016-03-07 11:07:02,174 WARN  [main] util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/home/hadoop/soft/hbase-1.2.0/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/home/hadoop/soft/hadoop-2.7.2/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 1.2.0, r25b281972df2f5b15c426c8963cbf77dd853a5ad, Thu Feb 18 23:01:49 CST 2016
</code></pre>
<h2>2、常用命令</h2>
<div>status<br>
命令返回包括在系统上运行的服务器的细节和系统的状态。<br></div>
</div>
<div><pre><code class="language-html">hbase(main):002:0&gt; status
1 active master, 0 backup masters, 2 servers, 0 dead, 1.0000 average load
</code></pre><br>
version<br>
该命令返回HBase系统使用的版本。<br></div>
<div><pre><code class="language-html">hbase(main):003:0&gt; version
1.2.0, r25b281972df2f5b15c426c8963cbf77dd853a5ad, Thu Feb 18 23:01:49 CST 2016
</code></pre><br>
table_help<br>
此命令将引导如何使用表引用的命令。<br></div>
<div><pre><code class="language-html">hbase(main):001:0&gt; table_help
Help for table-reference commands.

You can either create a table via 'create' and then manipulate the table via commands like 'put', 'get', etc.
See the standard help information for how to use each of these commands.

However, as of 0.96, you can also get a reference to a table, on which you can invoke commands.
For instance, you can get create a table and keep around a reference to it via:

   hbase&gt; t = create 't', 'cf'

Or, if you have already created the table, you can get a reference to it:

   hbase&gt; t = get_table 't'

You can do things like call 'put' on the table:

  hbase&gt; t.put 'r', 'cf:q', 'v'

which puts a row 'r' with column family 'cf', qualifier 'q' and value 'v' into table t.

To read the data out, you can scan the table:

  hbase&gt; t.scan

which will read all the rows in table 't'.

Essentially, any command that takes a table name can also be done via table reference.
Other commands include things like: get, delete, deleteall,
get_all_columns, get_counter, count, incr. These functions, along with
the standard JRuby object methods are also available via tab completion.

For more information on how to use each of these commands, you can also just type:

   hbase&gt; t.help 'scan'

which will output more information on how to use that command.

You can also do general admin actions directly on a table; things like enable, disable,
flush and drop just by typing:

   hbase&gt; t.enable
   hbase&gt; t.flush
   hbase&gt; t.disable
   hbase&gt; t.drop

Note that after dropping a table, your reference to it becomes useless and further usage
is undefined (and not recommended).
</code></pre>
<div><br></div>
whoami<br>
该命令返回HBase用户详细信息。</div>
<div><pre><code class="language-html">hbase(main):002:0&gt; whoami
hadoop (auth:SIMPLE)
    groups: hadoop
</code></pre><br><p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
可以使用命令创建一个表，在这里必须指定表名和列族名。在HBase shell中创建表的语法如下所示。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:496.188px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">create </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">table name</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,’&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">column family</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span></pre>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
下面给出的是一个表名为emp的样本模式。它有两个列族：“personal data”和“professional data”。</p>
<table class="table table-bordered" style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14px;border-collapse:collapse;border-spacing:0px;border:1px solid rgb(221,221,221);vertical-align:top;color:rgb(49,49,49);width:496px;line-height:22px;background-color:transparent;"><tbody style="font-size:16px;"><tr><th style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;border-collapse:collapse;border:1px solid rgb(221,221,221);line-height:1.42857;vertical-align:top;background:rgb(238,238,238);">
Row key</th>
<th style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;border-collapse:collapse;border:1px solid rgb(221,221,221);line-height:1.42857;vertical-align:top;background:rgb(238,238,238);">
personal data</th>
<th style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;border-collapse:collapse;border:1px solid rgb(221,221,221);line-height:1.42857;vertical-align:top;background:rgb(238,238,238);">
professional data</th>
</tr><tr><td style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;line-height:1.42857;vertical-align:top;border:1px solid rgb(221,221,221);">
 </td>
<td style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;line-height:1.42857;vertical-align:top;border:1px solid rgb(221,221,221);">
 </td>
<td style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;line-height:1.42857;vertical-align:top;border:1px solid rgb(221,221,221);">
 </td>
</tr><tr><td style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;line-height:1.42857;vertical-align:top;border:1px solid rgb(221,221,221);">
 </td>
<td style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;line-height:1.42857;vertical-align:top;border:1px solid rgb(221,221,221);">
 </td>
<td style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:11pt;line-height:1.42857;vertical-align:top;border:1px solid rgb(221,221,221);">
 <br></td>
</tr></tbody></table><pre><code class="language-html">hbase(main):008:0&gt; create 'emp','personal data','professional data'
0 row(s) in 27.2160 seconds

=&gt; Hbase::Table - emp
hbase(main):009:0&gt; list
TABLE                                                                                                      
emp                                                                                                        
1 row(s) in 0.1840 seconds

=&gt; ["emp"]
</code></pre><br><span style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;font-weight:700;color:rgb(83,91,96);line-height:22px;">list</span><span style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;"> 是用来列出HBase中所有表的命令。下面给出了 </span><span style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;font-weight:700;color:rgb(83,91,96);line-height:22px;">list </span><span style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">命令的语法。</span><br style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;"><br style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;"><pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:496.188px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">001</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> list</span></pre>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
要删除表或改变其设置，首先需要使用 disable 命令关闭表。使用 enable 命令，可以重新启用它。</p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
下面给出的语法是用来禁用一个表：</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">disable </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">emp</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’</span></pre>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
下面给出的是一个例子，说明如何禁用表。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">025</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> disable </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'emp'</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">
</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> row</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">s</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">)</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">in</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">1.2760</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> seconds</span></pre>
<h2 style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;color:rgb(83,85,92);border-bottom-style:none;">
验证</h2>
<h2 style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;color:rgb(83,85,92);border-bottom-style:none;">
<span style="color:rgb(83,91,96);font-size:16px;line-height:22px;">禁用表之后，仍然可以通过 list 和exists命令查看到。无法扫描到它存在，它会给下面的错误。</span></h2>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">028</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> scan </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'emp'</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">

ROW         COLUMN</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">+</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">CELL

ERROR</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> emp </span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">is</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> disabled</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">.</span></pre>
<h2 style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;color:rgb(83,85,92);border-bottom-style:none;">
is_disabled</h2>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
这个命令是用来查看表是否被禁用。它的语法如下。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> is_disabled </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'table name'</span></pre>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
下面的例子验证表名为emp是否被禁用。如果禁用，它会返回true，如果没有，它会返回false。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">031</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> is_disabled </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'emp'</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">

</span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">true</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">

</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> row</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">s</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">)</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">in</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0.0440</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> seconds</span></pre>
<h2 style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;color:rgb(83,85,92);border-bottom-style:none;">
disable_all</h2>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
此命令用于禁用所有匹配给定正则表达式的表。disable_all命令的语法如下。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> disable_all </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'r.*'</span></pre>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
假设有5个表在HBase，即raja, rajani, rajendra, rajesh 和 raju。下面的代码将禁用所有以 raj 开始的表。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">002</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> disable_all </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'raj.*'</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">

raja
rajani
rajendra
rajesh
raju
</span><span class="typ" style="font-size:14px;color:rgb(127,0,85);">Disable</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> the above </span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">5</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> tables </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">y</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">/</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">n</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">)?</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">

y

</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">5</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> tables successfully disabled</span></pre>
<br><p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14px;line-height:24px;text-align:justify;">
启用表的语法：</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">enable </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">emp</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’</span></pre>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
给出下面是一个例子，使一个表启用。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">005</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> enable </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'emp'</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">
</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> row</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">s</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">)</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">in</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0.4580</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> seconds</span></pre>
<span style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">可以使用exists命令验证表的存在。下面的示例演示了如何使用这个命令。</span></div>
<div><span style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;"><span style="font-size:16px;line-height:22px;"></span></span><pre><code class="language-html">hbase(main):015:0&gt; exists 'emp'
Table emp does exist                                                                                       
0 row(s) in 0.0880 seconds
</code></pre><br><p style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14px;line-height:24px;text-align:justify;">
用drop命令可以删除表。在删除一个表之前必须先将其禁用。</p>
<pre class="prettyprint notranslate prettyprinted" style="color:rgb(49,49,49);font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:496.188px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">018</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> disable </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'emp'</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">
</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> row</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">s</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">)</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">in</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">1.4580</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> seconds


hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">019</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> drop </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'emp'</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">
</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> row</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">s</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">)</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">in</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0.3060</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> seconds</span></pre>
<p style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">
使用exists 命令验证表是否被删除。</p>
<pre class="prettyprint notranslate prettyprinted" style="color:rgb(49,49,49);font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:496.188px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">main</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">):</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">020</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> exists </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'emp'</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">
</span><span class="typ" style="font-size:14px;color:rgb(127,0,85);">Table</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> emp does </span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">not</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> exist

</span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> row</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">(</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">s</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">)</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">in</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="lit" style="font-size:14px;color:rgb(0,102,102);">0.0730</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> seconds</span></pre>
<h2 style="color:rgb(83,85,92);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;border-bottom-style:none;">
drop_all</h2>
<p style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">
<br></p>
<p style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">
<br></p>
<p style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">
这个命令是用来在给出删除匹配“regex”表。它的语法如下：</p>
<pre class="prettyprint notranslate prettyprinted" style="color:rgb(49,49,49);font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:496.188px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> drop_all </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">t</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">.*’</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span></pre>
<p style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">
<span style="font-weight:700;">注意：</span>要删除表，则必须先将其禁用。</p>
<p style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">
使用put命令，可以插入行到一个表。它的语法如下：</p>
<pre class="prettyprint notranslate prettyprinted" style="color:rgb(49,49,49);font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">put </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">table name</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,’</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">row1</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’,’&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">colfamily</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">colname</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,’&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">value</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’</span></pre>
<pre><code class="language-html">&lt;span style="color: rgb(83, 91, 96);"&gt;hbase(main):017:0&gt; put 'emp','1','personal data:name','asia'
0 row(s) in 0.4790 seconds

hbase(main):018:0&gt; put 'emp','1','personal data:city','Beijing'
0 row(s) in 0.0250 seconds

hbase(main):019:0&gt; put 'emp','1','professional data:designation','manager'
0 row(s) in 0.1160 seconds

hbase(main):020:0&gt; put 'emp','1','professional data:salary','10000'
0 row(s) in 0.0230 seconds

hbase(main):021:0&gt; put 'emp','2','personal data:name','kobe'
0 row(s) in 0.0270 seconds

hbase(main):022:0&gt; put 'emp','2','personal data:city','NewYork'
0 row(s) in 0.0080 seconds

hbase(main):023:0&gt; put 'emp','2','professional data:designation','hr'
0 row(s) in 0.0840 seconds

hbase(main):024:0&gt; put 'emp','2','professional data:salary','5000'
0 row(s) in 0.0150 seconds

hbase(main):025:0&gt; scan 'emp'
ROW                         COLUMN+CELL                                                                    
 1                          column=personal data:city, timestamp=1457330959378, value=Beijing              
 1                          column=personal data:name, timestamp=1457330941995, value=asia                 
 1                          column=professional data:designation, timestamp=1457331038117, value=manager   
 1                          column=professional data:salary, timestamp=1457331069327, value=10000          
 2                          column=personal data:city, timestamp=1457331142543, value=NewYork              
 2                          column=personal data:name, timestamp=1457331124334, value=&lt;/span&gt;&lt;span style="color:#ff6666;"&gt;kobe &lt;/span&gt;&lt;span style="color:#535b60;"&gt;                
 2                          column=professional data:designation, timestamp=1457331166755, value=hr        
 2                          column=professional data:salary, timestamp=1457331187014, value=5000           
2 row(s) in 0.1840 seconds
&lt;/span&gt;</code></pre><br><p style="color:rgb(83,91,96);font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14px;line-height:24px;text-align:justify;">
可以使用put命令更新现有的单元格值。按照下面的语法，并注明新值，如下图所示。</p>
<pre class="prettyprint notranslate prettyprinted" style="color:rgb(49,49,49);font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">put </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">table name</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’,’</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">row </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’,</span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'Column family:column name'</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">,’</span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">new</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> value</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’</span></pre>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;line-height:22px;">
</p><pre><code class="language-html">&lt;span style="color:#535b60;"&gt;hbase(main):026:0&gt; put 'emp','2','personal data:name','James'
0 row(s) in 0.0490 seconds

hbase(main):027:0&gt; scan 'emp'
ROW                         COLUMN+CELL                                                                    
 1                          column=personal data:city, timestamp=1457330959378, value=Beijing              
 1                          column=personal data:name, timestamp=1457330941995, value=asia                 
 1                          column=professional data:designation, timestamp=1457331038117, value=manager   
 1                          column=professional data:salary, timestamp=1457331069327, value=10000          
 2                          column=personal data:city, timestamp=1457331142543, value=NewYork              
 2                          column=personal data:name, timestamp=1457331564816, value=&lt;/span&gt;&lt;span style="color:#ff0000;"&gt;James&lt;/span&gt;&lt;span style="color:#535b60;"&gt;                
 2                          column=professional data:designation, timestamp=1457331166755, value=hr        
 2                          column=professional data:salary, timestamp=1457331187014, value=5000           
2 row(s) in 0.2330 seconds
&lt;/span&gt;</code></pre><br><p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14px;line-height:24px;text-align:justify;">
get命令和HTable类的get()方法用于从HBase表中读取数据。使用 get 命令，可以同时获取一行数据。它的语法如下：</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">get</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">table name</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,’</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">row1</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’</span></pre>
<pre><code class="language-html">hbase(main):028:0&gt; get 'emp','1'
COLUMN                      CELL                                                                           
 personal data:city         timestamp=1457330959378, value=Beijing                                         
 personal data:name         timestamp=1457330941995, value=asia                                            
 professional data:designat timestamp=1457331038117, value=manager                                         
 ion                                                                                                       
 professional data:salary   timestamp=1457331069327, value=10000                                           
4 row(s) in 0.3580 seconds
</code></pre><br><h2 style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;color:rgb(83,85,92);border-bottom-style:none;">
读取指定列</h2>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
下面给出的是语法，使用get方法读取指定列。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">hbase</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;</span><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">get</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="str" style="font-size:14px;color:rgb(0,136,0);">'table name'</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">,</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">rowid</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’,</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">{</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">COLUMN </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">=&gt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">column family</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">:</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">column name </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">’}</span></pre>
<pre><code class="language-html">hbase(main):030:0&gt; get 'emp','1','personal data:name'
COLUMN                      CELL                                                                           
 personal data:name         timestamp=1457330941995, value=asia                                            
1 row(s) in 0.0450 seconds
</code></pre>
<h2 style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;color:rgb(83,85,92);border-bottom-style:none;">
从表删除特定单元格</h2>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
使用 delete 命令，可以在一个表中删除特定单元格。 delete 命令的语法如下：</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="kwd" style="font-size:14px;color:rgb(0,0,136);">delete</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">table name</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">row</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">column name </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">time stamp</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’</span></pre>
<pre><code class="language-html">hbase(main):033:0&gt; delete 'emp','2','personal data:city',1457331142543
0 row(s) in 0.0610 seconds
</code></pre><br><pre><code class="language-html">hbase(main):035:0&gt; delete 'emp','2','professional data:salary'
0 row(s) in 0.0090 seconds

hbase(main):036:0&gt; scan 'emp'
ROW                         COLUMN+CELL                                                                    
 1                          column=personal data:city, timestamp=1457330959378, value=Beijing              
 1                          column=personal data:name, timestamp=1457330941995, value=asia                 
 1                          column=professional data:designation, timestamp=1457331038117, value=manager   
 1                          column=professional data:salary, timestamp=1457331069327, value=10000          
 2                          column=personal data:name, timestamp=1457331564816, value=James                
 2                          column=professional data:designation, timestamp=1457331166755, value=hr        
2 row(s) in 0.0570 seconds
</code></pre><br><h2 style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;color:rgb(83,85,92);border-bottom-style:none;">
删除表的所有单元格</h2>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
使用“deleteall”命令，可以删除一行中所有单元格。下面给出是 deleteall 命令的语法。</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">deleteall </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">table name</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">row</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’,</span></pre>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<span style="font-weight:700;">scan</span> 命令用于查看HTable数据。使用 <span style="font-weight:700;">scan </span>命令可以得到表中的数据。它的语法如下：</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">scan </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">table name</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span></pre>
<h2 style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:14pt;line-height:1.5em;color:rgb(83,85,92);border-bottom-style:none;">
count</h2>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
<br></p>
<p style="font-family:'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif, sans-serif;font-size:16px;color:rgb(83,91,96);line-height:22px;">
可以使用count命令计算表的行数量。它的语法如下：</p>
<pre class="prettyprint notranslate prettyprinted" style="font-family:'Courier New', Courier, monospace;overflow:auto;line-height:16px;color:rgb(49,49,49);border-width:1px 1px 1px 3px;border-style:solid;border-color:rgb(204,204,204);width:604px;background-color:rgb(245,245,245);"><span class="pln" style="font-size:14px;color:rgb(0,0,0);">count </span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">‘&lt;</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);">table name</span><span class="pun" style="font-size:14px;color:rgb(102,102,0);">&gt;’</span><span class="pln" style="font-size:14px;color:rgb(0,0,0);"> </span></pre>
<br><br><br></div>
            </div>
                </div>
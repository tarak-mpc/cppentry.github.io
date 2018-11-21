---
layout:     post
title:      Hbase shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="postTitle"><a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/linjiqin/archive/2013/03/08/2949339.html" rel="nofollow">Hbase shell详情</a></h1>
<div id="cnblogs_post_body">
<p>HBase 为用户提供了一个非常方便的使用方式, 我们称之为“HBase Shell”。<br>
HBase Shell 提供了大多数的 HBase 命令, 通过 HBase Shell 用户可以方便地创建、删除及修改表, 还可以向表中添加数据、列出表中的相关信息等。<br>
备注:写错 HBase Shell 命令时用键盘上的“Delete”进行删除,“Backspace”不起作用。<br>
在启动 HBase 之后,用户可以通过下面的命令进入 HBase Shell 之中,命令如下所示:</p>
<div class="cnblogs_code">
<pre></pre><pre><code class="language-java">hadoop@ubuntu:~$ hbase shell
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 0.94.3, r1408904, Wed Nov 14 19:55:11 UTC 2012

hbase(main):001:0&gt; </code></pre>
</div>
<p>具体的 HBase Shell 命令如下表 1.1-1 所示:</p>
<p><img src="https://img-blog.csdn.net/20140626111804296?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva3V5dXlpbmd6aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>下面我们将以“一个学生成绩表”的例子来详细介绍常用的 HBase 命令及其使用方法。</p>
<p><img src="https://img-blog.csdn.net/20140626111835078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQva3V5dXlpbmd6aQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br>
这里 grad 对于表来说是一个列,course 对于表来说是一个列族,这个列族由三个列组成 china、math 和 english,当然我们可以根据我们的需要在 course 中建立更多的列族,如computer,physics 等相应的列添加入 course 列族。(备注:列族下面的列也是可以没有名字的。)<br><span style="color:#0000ff;">1). create 命令</span><br>
创建一个具有两个列族“grad”和“course”的表“scores”。其中表名、行和列都要用单引号括起来,并以逗号隔开。<br>
hbase(main):012:0&gt; create 'scores', 'name', 'grad', 'course'<br><br><span style="color:#0000ff;">2). list 命令</span><br>
查看当前 HBase 中具有哪些表。<br>
hbase(main):012:0&gt; list<br><br><span style="color:#0000ff;">3). describe 命令</span><br>
查看表“scores”的构造。<br>
hbase(main):012:0&gt; describe 'scores'<br><br><span style="color:#0000ff;">4). put 命令</span><br>
使用 put 命令向表中插入数据,参数分别为表名、行名、列名和值,其中列名前需要列族最为前缀,时间戳由系统自动生成。<br>
格式: put 表名,行名,列名([列族:列名]),值<br>
例子：<br>
a. 加入一行数据,行名称为“xiapi”,列族“grad”的列名为”(空字符串)”,值位 1。<br>
hbase(main):012:0&gt; put 'scores', 'xiapi', 'grad:', '1'<br>
hbase(main):012:0&gt; put 'scores', 'xiapi', 'grad:', '2' --修改操作(update)<br>
b. 给“xiapi”这一行的数据的列族“course”添加一列“&lt;china,97&gt;”。<br>
hbase(main):012:0&gt; put 'scores', 'xiapi',  'course:china', '97'<br>
hbase(main):012:0&gt; put 'scores', 'xiapi',  'course:math', '128'<br>
hbase(main):012:0&gt; put 'scores', 'xiapi',  'course:english', '85'<br><br><span style="color:#0000ff;">5). get 命令</span><br>
a.查看表“scores”中的行“xiapi”的相关数据。<br>
hbase(main):012:0&gt; get 'scores', 'xiapi'<br>
b.查看表“scores”中行“xiapi”列“course :math”的值。<br>
hbase(main):012:0&gt; get 'scores', 'xiapi', 'course :math'<br>
或者<br>
hbase(main):012:0&gt; get 'scores', 'xiapi', {COLUMN=&gt;'course:math'}<br>
hbase(main):012:0&gt; get 'scores', 'xiapi', {COLUMNS=&gt;'course:math'}<br>
备注:COLUMN 和 COLUMNS 是不同的,scan 操作中的 COLUMNS 指定的是表的列族, get操作中的 COLUMN 指定的是特定的列,COLUMNS 的值实质上为“列族:列修饰符”。<strong><span style="color:#ff0000;">COLUMN 和 COLUMNS 必须为大写。</span></strong><br><br><span style="color:#0000ff;">6). scan 命令</span><br>
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
hbase(main):012:0&gt; scan  'scores', {COLUMNS =&gt; 'course'}<br><br><span style="color:#0000ff;">7). count 命令</span><br>
hbase(main):068:0&gt; count 'scores'<br><br><span style="color:#0000ff;">8). exists 命令</span><br>
hbase(main):071:0&gt; exists 'scores'<br><br><span style="color:#0000ff;">9). incr 命令(赋值)</span><br><br><span style="color:#0000ff;">10). delete 命令</span><br>
删除表“scores”中行为“xiaoxue”, 列族“course”中的“math”。<br>
hbase(main):012:0&gt;  delete 'scores', 'xiapi', 'course:math'<br><br><span style="color:#0000ff;">11). truncate 命令</span><br>
hbase(main):012:0&gt;  truncate 'scores'<br><br><span style="color:#0000ff;">12). disbale、drop 命令</span><br>
通过“disable”和“drop”命令删除“scores”表。<br>
hbase(main):012:0&gt;  disable 'scores' --enable 'scores' <br>
hbase(main):012:0&gt;  drop 'scores'<br><br><span style="color:#0000ff;">13).  status命令</span><br>
hbase(main):072:0&gt; status<br><br><span style="color:#0000ff;">14).  version命令</span><br>
hbase(main):073:0&gt; version<br><br>
另外,在 shell 中,常量不需要用引号引起来,但二进制的值需要双引号引起来,而其他值则用单引号引起来。HBase Shell 的常量可以通过在 shell 中输入“Object.constants”。</p>
<p><br></p>
<p>原文链接：http://www.cnblogs.com/linjiqin/archive/2013/03/08/2949339.html<br><br></p>
</div>
            </div>
                </div>
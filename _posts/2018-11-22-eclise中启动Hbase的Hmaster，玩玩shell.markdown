---
layout:     post
title:      eclise中启动Hbase的Hmaster，玩玩shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;color:#ff0000;"><strong>首先master:</strong></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20140330024337203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHV5ZWUyMDEw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><span style="font-size:18px;color:#ff0000;"><strong>参数：</strong></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20140330024405609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHV5ZWUyMDEw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><span style="font-size:18px;color:#ff0000;">运行 hbase shell</span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20140330024435390?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHV5ZWUyMDEw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><span style="font-size:18px;color:#ff0000;"><strong>hbase shell参数</strong></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20140330024525468?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHV5ZWUyMDEw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><span style="font-size:18px;color:#ff0000;"><strong>运行起来后hbase的shell</strong></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20140330024941640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbHV5ZWUyMDEw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><span style="font-size:18px;"><span style="color:#ff0000;"><strong>跑几个命令试试发现可以了：</strong></span><br></span></p>
<p></p>
<pre><code class="language-html"><span style="font-size:18px;">create 't_person','no','name'
create 't_person','no','name'
0 row(s) in 2.5020 seconds


Hbase::Table - t_person
list
list
TABLE
t_person
1 row(s) in 0.1270 seconds


["t_person"]
put 't_person','r1','no','1234'
put 't_person','r1','no','1234'
0 row(s) in 0.3280 seconds


put 't_person','r1','name','xiaoming'
put 't_person','r1','name','xiaoming'
0 row(s) in 0.0160 seconds


scan 't_person'
scan 't_person'
ROW  COLUMN+CELL
 r1 column=name:, timestamp=1396118296355, value=xiaoming
 r1 column=no:, timestamp=1396118271612, value=1234</span></code></pre><span style="font-size:18px;"><br><br></span>
<p></p>
<span style="font-size:18px;"><br><br></span>
<p></p>
            </div>
                </div>
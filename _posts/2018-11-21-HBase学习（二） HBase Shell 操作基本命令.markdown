---
layout:     post
title:      HBase学习（二） HBase Shell 操作基本命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>通过使用HBase Shell，用命令行和HBase进行交互。</p>
<p>1、启动HBase Shell</p>
<p></p>
<pre><code class="language-html">[xxxx@localhost bin]$ ./hbase shell
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 0.94.24, r68ed793a1d89226f8fb1ee1ee618c1e7779d46cd, Mon Sep 29 23:09:31 UTC 2014

hbase(main):001:0&gt; 
</code></pre><br>
2、查看Hbase中的表
<p></p>
<p></p>
<pre><code class="language-html">hbase(main):001:0&gt; list
TABLE                                                                                                                               
mytable                                                                                                                             
mytable2                                                                                                                            
2 row(s) in 0.4670 seconds

hbase(main):002:0&gt; </code></pre><br>
3、创建表,testtable：表名,cf：列族
<p></p>
<p></p>
<pre><code class="language-html">hbase(main):002:0&gt; create 'testtable','cf'
0 row(s) in 1.4170 seconds

hbase(main):003:0&gt; list
TABLE                                                                                                                               
mytable                                                                                                                             
mytable2                                                                                                                            
testtable                                                                                                                           
3 row(s) in 0.0240 seconds

hbase(main):004:0&gt; </code></pre><br>
4、写数据
<p></p>
<p></p>
<pre><code class="language-html">hbase(main):004:0&gt; put 'testtable','first','cf:message','hello Hbase'
0 row(s) in 0.0610 seconds

hbase(main):005:0&gt; put 'testtable','second','cf:foo',0x0
0 row(s) in 0.0160 seconds

hbase(main):006:0&gt; put 'testtable','third','cf:bar',3.1415926
0 row(s) in 0.0230 seconds

hbase(main):007:0&gt; </code></pre><br>
5、读数据
<p></p>
<p></p>
<pre><code class="language-html">hbase(main):013:0&gt; get 'testtable','first'
COLUMN                             CELL                                                                                             
 cf:message                        timestamp=1419268133154, value=hello Hbase                                                       
1 row(s) in 0.0150 seconds

hbase(main):014:0&gt; scan 'testtable'
ROW                                COLUMN+CELL                                                                                      
 first                             column=cf:message, timestamp=1419268133154, value=hello Hbase                                    
 second                            column=cf:foo, timestamp=1419268974405, value=0                                                  
 third                             column=cf:bar, timestamp=1419268992995, value=3.1415926                                          
3 row(s) in 0.0470 seconds

hbase(main):015:0&gt; </code></pre><br><br><p></p>
            </div>
                </div>
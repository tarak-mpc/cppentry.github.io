---
layout:     post
title:      hive的Specified key was too long; max key length is 767 bytes问题解决
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>上次安装hive时遇到很多问题，最后遗留下一个没有解决的问题，最后通过询问得以解决在这记录下：方便遇到同样问题的人：</p>
<h1>问题阐述</h1>
<p><span style="font-size:16px;">在安装完hive后，执行：</span></p>
<p></p><pre><code class="language-plain"><span style="font-size:16px;">[root@localhost hive]# hive
Hive history file=/tmp/root/hive_job_log_root_201112071228_90457717.txt
hive&gt; </span></code></pre><span style="font-size:16px;">创建表：</span>
<p></p><pre><code class="language-plain"><span style="font-size:16px;">hive&gt;  CREATE TABLE test ( time string, ip string, url string);     
OK
Time taken: 0.303 seconds</span></code></pre><span style="font-size:16px;">删除表报错：</span>
<p></p><pre><code class="language-plain"><span style="font-size:16px;">NestedThrowables:
com.mysql.jdbc.exceptions.jdbc4.MySQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask</span></code></pre><br><h1>解决方法：</h1>
<p><span style="font-size:16px;">一般是由于mysql的最大索引长度导致，看了一个人的文章颇有感触下面给出链接：</span></p>
<p><span style="font-size:16px;"><a href="http://blog.csdn.net/cindy9902/article/details/6215769" rel="nofollow">http://blog.csdn.net/cindy9902/article/details/6215769</a><br></span></p>
<p><span style="font-size:16px;">解决方法如下：</span></p>
<p><span style="font-size:16px;">1、数据库的字符集除了system为utf8，其他最好为latin1，否则可能出现如上异常。</span></p>
<p><span style="font-size:16px;">2、<span style="font-family:monospace;background-color:rgb(240,240,240);">set global binlog_format='MIXED';READ-COMMITTED需要把bin-log以mixed方式来记录 <br></span></span></p>
<p><span style="font-family:monospace;font-size:16px;"><span>改完上述两种方法后，我还是会出现以上问题，我把mysql的metastore_db里面的所有表删除，hadoop重启，然后重新启动hive之后就能够正常删除表了。</span></span></p>
<p><span style="font-family:monospace;font-size:16px;"><span>问题解决。</span></span></p>
<p><span style="font-family:monospace;background-color:rgb(240,240,240);"><br></span><br><br></p>
            </div>
                </div>
---
layout:     post
title:      shell定时创建Hive表分区
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：感兴趣请关注网站: https://www.haoshidaojia.com   https://haoshidaojia.com					https://blog.csdn.net/zhangbiaobiaobiao/article/details/46502279				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p>首先看一下hive 的help命令：</p>
<p></p>
<pre><code class="language-html">[hdfs@xxx hive]$ hive  -h
Missing argument for option: h
usage: hive
 -d,--define &lt;key=value&gt;          Variable subsitution to apply to hive
                                  commands. e.g. -d A=B or --define A=B
    --database &lt;databasename&gt;     Specify the database to use
 -e &lt;quoted-query-string&gt;         SQL from command line
 -f &lt;filename&gt;                    SQL from files
 -H,--help                        Print help information
 -h &lt;hostname&gt;                    connecting to Hive Server on remote host
    --hiveconf &lt;property=value&gt;   Use value for given property
    --hivevar &lt;key=value&gt;         Variable subsitution to apply to hive
                                  commands. e.g. --hivevar A=B
 -i &lt;filename&gt;                    Initialization SQL file
 -p &lt;port&gt;                        connecting to Hive Server on port number
 -S,--silent                      Silent mode in interactive shell
 -v,--verbose                     Verbose mode (echo executed SQL to the
                                  console)
[hdfs@xxxx &lt;span style="font-family: Arial, Helvetica, sans-serif;"&gt;hive]$ &lt;/span&gt;</code></pre><br><br><p></p>
<p>使用crontab定时给hive表增加表分区：</p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><strong>方法1：</strong></p>
<pre><code class="language-html"> -e &lt;quoted-query-string&gt;         SQL from command line</code></pre>
<p></p>
<p><br></p>
<p><strong>执行命令：</strong></p>
<p><strong>[hdfs@xxxx hive]$ hive  --database gcld   -e  'alter table gcldlog  add partition (dt=\\'2015-06-14\\') location \\'2015-06-14\\'';<br><br><br>
Logging initialized using configuration in file:/etc/hive/conf.dist/hive-log4j.properties<br>
OK<br>
Time taken: 1.097 seconds</strong><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><strong>方法2：</strong></p>
<p></p>
<pre><code class="language-html">-i &lt;filename&gt;                    Initialization SQL file</code></pre><br><p></p>
<p>编辑好sql命令：</p>
<p>alter table gcldlog  add partition (dt=‘2015-06-10') location '2015-06-10';</p>
<p>exit;(记得加上退出命令)</p>
<p><br></p>
<p><strong>执行命令：</strong></p>
<p><strong>hive  --database gcld   -i /script/gcld/test.sql ;<br></strong></p>
<p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong><br></strong></p>
<p>这样就可以实现shell动态给hive表增加分区表的操作。</p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
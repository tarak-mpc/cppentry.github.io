---
layout:     post
title:      hive cli 常用操作(-e,-f.-i)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="text-align:center;"><span style="font-size:24px;color:#ff0000;">hive cli 常用操作(-e,-f.-i)</span></p>
<p>官网：<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli</a></p>
<p><span style="font-size:18px;color:#ff0000;">1、bin/hive -help</span></p>
<p></p><pre><code class="language-html">bin/hive -help
15/10/18 06:34:46 WARN conf.HiveConf: DEPRECATED: hive.metastore.ds.retry.* no longer has any effect.  Use hive.hmshandler.retry.* instead
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
 -v,--verbose                     Verbose mode (echo executed SQL to the</code></pre><br><br><p><span style="font-size:18px;color:#ff0000;">2、hive -e 用法</span></p>
<p></p><pre><code class="language-html">[root@cloud1 hive-0.13.1]# bin/hive -e 'select *  from default.student'
15/10/18 06:55:27 WARN conf.HiveConf: DEPRECATED: hive.metastore.ds.retry.* no longer has any effect.  Use hive.hmshandler.retry.* instead


Logging initialized using configuration in jar:file:/opt/hive-0.13.1/lib/hive-common-0.13.1.jar!/hive-log4j.properties
OK
1	xiaoming
2	zhangsan
3	lisi
Time taken: 21.298 seconds, Fetched: 3 row(s)</code></pre><br><br><p><span style="font-size:18px;color:#ff0000;">3、hive -f 用法</span></p>
<p></p>
<pre><code class="language-html">[root@cloud1 hive-0.13.1]# touch hfile.sql
[root@cloud1 hive-0.13.1]# vi hfile.sql
select * from default.student ; 
bin/hive -f /opt/hive-0.13.1/hfile.sql
[root@cloud1 hive-0.13.1]# bin/hive -f /opt/hive-0.13.1/hfile.sql
15/10/18 07:00:12 WARN conf.HiveConf: DEPRECATED: hive.metastore.ds.retry.* no longer has any effect.  Use hive.hmshandler.retry.* instead


Logging initialized using configuration in jar:file:/opt/hive-0.13.1/lib/hive-common-0.13.1.jar!/hive-log4j.properties
OK
1	xiaoming
2	zhangsan
3	lisi
Time taken: 13.048 seconds, Fetched: 3 row(s)

或者： bin/hive -f hdfs://192.168.80.20:8020/user/hive/hfile.sql</code></pre><br><p></p>
<p><br></p>
<p><span style="font-size:18px;color:#ff0000;">4、hive -i 用法：(这里占时还没有例子)</span></p>
<p>* bin/hive -i xx<br><span></span>-i &lt;filename&gt;                    Initialization SQL file<br><span></span>eg:<br><span></span>add jar xxx.jar<br><span></span>add file xxx<br><span></span>create temporary function my_lower as ....<br><span></span>bin/hive -i hive-init.sql<br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
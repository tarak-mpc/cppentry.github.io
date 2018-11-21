---
layout:     post
title:      Hive Shell 常用操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/accptanggang/article/details/77370768				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:18px;">[beifeng@hadoop-senior hive-0.13.1]$ <span style="color:#ff0000;">
bin/hive -help</span><br>
usage: hive<br>
 -d,--define &lt;key=value&gt;          Variable subsitution to apply to hive<br>
                                  commands. e.g. -d A=B or --define A=B<br>
    --database &lt;databasename&gt;     Specify the database to use<br>
 -e &lt;quoted-query-string&gt;         SQL from command line<br>
 -f &lt;filename&gt;                    SQL from files<br>
 -H,--help                        Print help information<br>
 -h &lt;hostname&gt;                    connecting to Hive Server on remote host<br>
    --hiveconf &lt;property=value&gt;   Use value for given property<br>
    --hivevar &lt;key=value&gt;         Variable subsitution to apply to hive<br>
                                  commands. e.g. --hivevar A=B<br>
 -i &lt;filename&gt;                    Initialization SQL file<br>
 -p &lt;port&gt;                        connecting to Hive Server on port number<br>
 -S,--silent                      Silent mode in interactive shell<br>
 -v,--verbose                     Verbose mode (echo executed SQL to the<br>
                                 console)<br><br><br>
* bin/hive -e &lt;quoted-query-string&gt;<br>
eg:<br><span></span>bin/hive -e "select * from db_hive.student ;"<br><br><br>
* bin/hive -f &lt;filename&gt;<br>
eg:<br><span></span>$ touch hivef.sql<br><span></span>select * from db_hive.student ;<br><span></span>$ bin/hive -f /opt/datas/hivef.sql <br><span></span>$ bin/hive -f /opt/datas/hivef.sql &gt; /opt/datas/hivef-res.txt<br><br><br>
* bin/hive -i &lt;filename&gt;<br><span></span>与用户udf相互使用<br><br><br>
在hive cli命令窗口中如何查看hdfs文件系统<br><span></span>hive (default)&gt; dfs -ls / ;  <br><br><br>
在hive cli命令窗口中如何查看本地文件系统<br><span></span>hive (default)&gt; !ls /opt/datas ；<br><span></span><br>
查看Hive操作历史记录：<br><span></span>$HOME/.hivehistory</span>
            </div>
                </div>
---
layout:     post
title:      使用hive中beeline引擎来访问hive数据库
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>hive中beeline引擎连接hive访问hive引擎</p>

<p><br>
注意,往下阅读前,必须先确保hive&gt;能连接mysql数据库</p>

<p><br>
我们知道:<br>
hive中的引擎有两个:<br>
hive&gt;<br>
beeline&gt;</p>

<p><br>
现在使用第二个beeline引擎<br>
1.启动元数据库(用来确保hive和mysql的通信连接)<br>
hive --service metastore<br>
2.启动hiveserver2服务(如果想使用spark下面的beeline引擎,那么就是启动thrift服务)</p>

<p> # nohup hive --service hiveserver2 &amp; </p>

<p>ps -ef | grep Hive 能看到Hiveserver2已启动<br>
3.在路径/home/appleyuchi/bigdata/apache-hive-3.0.0-bin/bin下面<br><strong>./beeline -u jdbc:hive2://</strong><br><strong>--------------------------------------------------------------------</strong><br>
第3步骤中注意两点,之所以命令是<br>
./beeline -u jdbc:hive2://<br>
而不是<br>
beeline -u jdbc:hive2://<br>
是因为,spark和hive中都有beeline的引擎,我们这里使用hive中的beeline引擎.<br>
因为.bashrc中我们同时设置了SPARK_HOME和HIVE_HOME,所以导致系统会自动选用spark或hive的beeline&gt;引擎<br>
另外这个命令中,我们再hive2://后面什么都不写,因为这种启动方式会有很多的debug信息跑出来,有利于我们调试.</p>

<p>另外,这里的hive2是因为使用了hive中的sever2,不需要进行修改</p>

<p><br>
最终运行结果:<br>
0: jdbc:hive2://&gt; show databases;<br>
OK<br>
+----------------+<br>
| database_name  |<br>
+----------------+<br>
| default        |<br>
| hive_yuchi     |<br>
+----------------+<br>
2 rows selected (1.576 seconds)</p>

<p>参考文章:<br>
https://blog.csdn.net/wqhlmark64/article/details/77894026(这篇文章对配置讲得非常到位,但是需要把里面的slave01改为host,另外,这篇参考文章中的启动方式非常不利于调试,只会报错,而不会给出详细的错误信息,所以启动方式使用本文中的记载为佳)</p>

<p><br>
 </p>            </div>
                </div>
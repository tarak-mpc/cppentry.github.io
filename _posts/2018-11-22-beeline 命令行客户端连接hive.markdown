---
layout:     post
title:      beeline 命令行客户端连接hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014236541/article/details/78245924				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-plain">[hadoop@mina0 bin]$ ll
总用量 888
-rwxr-xr-x. 1 hadoop hadoop   1031 4月  30 2015 beeline
drwxrwxr-x. 3 hadoop hadoop   4096 10月 15 23:41 ext
-rwxr-xr-x. 1 hadoop hadoop   7844 5月   8 2015 hive
-rwxr-xr-x. 1 hadoop hadoop   1900 4月  30 2015 hive-config.sh
-rwxr-xr-x. 1 hadoop hadoop    885 4月  30 2015 hiveserver2
-rwxr-xr-x. 1 hadoop hadoop    832 4月  30 2015 metatool
-rw-r--r--. 1 hadoop hadoop 875336 10月 15 15:56 mysql-connector-java-5.1.28.jar
-rwxr-xr-x. 1 hadoop hadoop    884 4月  30 2015 schematool
[hadoop@mina0 bin]$ cd ..
[hadoop@mina0 hive]$ cd bin
[hadoop@mina0 bin]$ ./beeline
Beeline version 1.2.1 by Apache Hive</code></pre><pre><code class="language-plain">beeline&gt; !connect jdbc:hive2://localhost:10000
Connecting to jdbc:hive2://localhost:10000
Enter username for jdbc:hive2://localhost:10000: hadoop
Enter password for jdbc:hive2://localhost:10000: 
Connected to: Apache Hive (version 1.2.1)
Driver: Hive JDBC (version 1.2.1)
Transaction isolation: TRANSACTION_REPEATABLE_READ
0: jdbc:hive2://localhost:10000&gt; show databases
0: jdbc:hive2://localhost:10000&gt; ;
+----------------+--+
| database_name  |
+----------------+--+
| default        |
| shizhan03      |
+----------------+--+
2 rows selected (1.801 seconds)
0: jdbc:hive2://localhost:10000&gt; show databases;
+----------------+--+
| database_name  |
+----------------+--+
| default        |
| shizhan03      |
+----------------+--+
2 rows selected (0.055 seconds)
0: jdbc:hive2://localhost:10000&gt; use shizhan03;
No rows affected (0.083 seconds)
0: jdbc:hive2://localhost:10000&gt; show tables;
+-----------+--+
| tab_name  |
+-----------+--+
| t_sz01    |
+-----------+--+
1 row selected (0.062 seconds)
0: jdbc:hive2://localhost:10000&gt;</code></pre><br><br>            </div>
                </div>
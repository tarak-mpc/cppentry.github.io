---
layout:     post
title:      beeline通过HiveServer2访问Hive的配置和操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wqhlmark64/article/details/77894026				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:18px;">１.　前言</span></strong></p>
<p>作为数据仓库的工具，hive提供了两种ETL运行方式，分别是通过Hive 命令行和beeline客户端；</p>
<p>命令行方式即通过hive进入命令模式后通过执行不同的HQL命令得到对应的结果；相当于胖客户端模式，即客户机中需要安装JRE环境和Hive程序。</p>
<p>beeline客户端方式相当于瘦客户端模式，采用JDBC方式借助于Hive Thrift服务访问Hive数据仓库。</p>
<p>HiveThrift(HiveServer)是Hive中的组件之一，设计目的是为了实现跨语言轻量级访问Hive数据仓库，有Hiveserver和 Hiveserver2两个版本，两者不兼容，使用中要注意区分。体现在启动HiveServer的参数和jdbc:hiveX的参数上。</p>
<p><br></p>
<p><strong><span style="font-size:18px;">２.　beeline相关的Server.Thrift配置  </span></strong></p>
<p>主要是hive/conf/hive-site.xml中hive.server2.thrift相关的一些配置项，但要注意一致性</p>
<p>  &lt;property&gt;<br>
    &lt;name&gt;<span style="color:#ff0000;">hive.server2.thrift.bind.host</span>&lt;/name&gt;<br>
    &lt;value&gt;<span style="color:#ff0000;">slave01</span>&lt;/value&gt;<br>
    &lt;description&gt;Bind host on which to run the HiveServer2 Thrift service.&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p>  &lt;property&gt;<br>
    &lt;name&gt;<span style="color:#ff0000;">hive.server2.thrift.port</span>&lt;/name&gt;<br>
    &lt;value&gt;<span style="color:#ff0000;">10000</span>&lt;/value&gt;<br>
    &lt;description&gt;Port number of HiveServer2 Thrift interface when hive.server2.transport.mode is 'binary'.&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p>  &lt;property&gt;<br>
    &lt;name&gt;hive.server2.thrift.http.port&lt;/name&gt;<br>
    &lt;value&gt;10001&lt;/value&gt;<br>
    &lt;description&gt;Port number of HiveServer2 Thrift interface when hive.server2.transport.mode is 'http'.&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p><br></p>
<p>进入beeline连接数据库后，因为要访问的文件在HDFS上，对应的路径有访问权限限制，所以，这里要设成hadoop中的用户名，实例中用户名即为'hadoop’。如果使用其它用户名，可能会报权限拒绝的错误。或通过修改hadoop中的配置项hadoop.proxyuser.ＸＸ为“*”　来放宽用户名和权限，如示例。</p>
<p>  &lt;property&gt;<br>
    &lt;name&gt;hive.server2.thrift.client.user&lt;/name&gt;<br>
    &lt;value&gt;<span style="color:#ff0000;">hadoop</span>&lt;/value&gt;<br>
    &lt;description&gt;Username to use against thrift client&lt;/description&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;hive.server2.thrift.client.password&lt;/name&gt;<br>
    &lt;value&gt;<span style="color:#ff0000;">hadoop</span>&lt;/value&gt;<br>
    &lt;description&gt;Password to use against thrift client&lt;/description&gt;<br>
  &lt;/property&gt;<br></p>
<p><br></p>
<p><span style="font-size:12px;">hadoop/etc/hadoop/core-site.xml</span></p>
<p>    &lt;property&gt;<br>
    &lt;name&gt;<span style="color:#ff0000;">hadoop.proxyuser.hadoop.hosts</span>&lt;/name&gt;<br>
    &lt;!--value&gt;<span style="color:#ff0000;">master</span>&lt;/value--&gt;<br>
    &lt;value&gt;*&lt;/value&gt;<br>
    &lt;/property&gt;<br><br>
    &lt;property&gt;<br>
    &lt;name&gt;<span style="color:#ff0000;">hadoop.proxyuser.hadoop.groups</span>&lt;/name&gt;<br>
    &lt;!--value&gt;<span style="color:#ff0000;">hadoop</span>&lt;/value--&gt;<br>
    &lt;value&gt;*&lt;/value&gt;</p>
<p>    &lt;/property&gt;<br></p>
<p><br></p>
<p><strong><span style="font-size:18px;">３.　启动beeline并访问Hive</span></strong></p>
<p><span style="font-size:12px;">slave01上启动hiveserver2,   # nohup hive --service hiveserver2 &amp; </span></p>
<p><span style="font-size:12px;">ps -ef | grep Hive 能看到Hiveserver2已启动</span></p>
<p><span style="font-size:12px;"><br></span></p>
<p><span style="font-size:12px;">master机器上执行beeline并访问hive</span></p>
<p>hadoop@master:~/bigdata/hive$ beeline<br>
Beeline version 1.2.1.spark2 by Apache Hive<br>
beeline&gt; <br></p>
<p><br></p>
<p>beeline&gt;<span style="color:#ff0000;"> !connect jdbc:hive</span><span style="color:#3333ff;">2</span><span style="color:#ff0000;">://slave01:10000       // 2中配置项的host:port ，</span><span style="color:#3366ff;">因为启动的是hiveserver2，所以参数中是hive2</span><br>
Connecting to jdbc:hive2://ndh-slave01:10000<br>
Enter username for jdbc:hive2://ndh-slave01:10000: <span style="color:#ff0000;">hadoop</span><br>
Enter password for jdbc:hive2://ndh-slave01:10000: <span style="color:#ff0000;">******        //2中配置项的user/password </span><br>
17/09/08 14:39:27 INFO jdbc.Utils: Supplied authorities: ndh-slave01:10000<br>
17/09/08 14:39:27 INFO jdbc.Utils: Resolved authority: ndh-slave01:10000<br>
17/09/08 14:39:27 INFO jdbc.HiveConnection: Will try to open client transport with JDBC Uri: jdbc:hive2://slave01:10000<br>
Connected to: Apache Hive (version 2.1.1)<br>
Driver: Hive JDBC (version 1.2.1.spark2)<br>
Transaction isolation: TRANSACTION_REPEATABLE_READ<br>
0: jdbc:hive2://slave01:10000&gt; <br></p>
<p><br></p>
<p>0: jdbc:hive2://slave01:10000&gt; show databases;<br>
+----------------+--+<br>
| database_name  |<br>
+----------------+--+<br>
| default        |<br>
| feigu3         |<br>
| wordcount      |<br>
+----------------+--+<br>
3 rows selected (0.379 seconds)<br></p>
<p><br></p>
<p>0: jdbc:hive2://slave01:10000&gt; select * from wordcount order by count desc limit 50;<br></p>
<p>............................................</p>
<p>.............................................</p>
<p><br></p>
<p>看到结果后，进入hadoop webui http://master:8088/cluster/apps/FINISHED　可看到刚执行的任务。</p>
<p><br></p>
<p><br></p>
<p>0: jdbc:hive2://slave01:10000&gt;<span style="color:#ff0000;"> !q</span>　　　　//// 退出beeline<br></p>
<p><br></p>
<p><strong><span style="font-size:18px;">4. 期间遇到的问题和解决方法</span></strong></p>
<p>4.1    hadoop is not allowed to impersonate hive // hadoop是hadoop中配置的用户名，解决方法见２中说明；</p>
<p>4.2   "'serverProtocolVersion' is unset!"   　　// 解决方法同4.1</p>
<p>4.3    java.net.ConnectException: Connection refused (state=08S01,code=0)    // 先是因为命令行中输入的host:port参数不对，后是4.1,4.2的问题</p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
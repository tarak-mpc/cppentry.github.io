---
layout:     post
title:      hbase-shell + hbase的java api
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="postTitle" style="font-size:14.7px;font-weight:bold;color:rgb(75,75,75);font-family:Verdana, Arial, Helvetica, sans-serif;">
<h1 style="font-size:14.7px;color:rgb(102,102,102);"><a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/zlslch/p/5951104.html" rel="nofollow" style="text-decoration:none;color:#808080;">5 hbase-shell + hbase的java api</a></h1>
</div>
<div id="cnblogs_post_body" style="color:rgb(75,75,75);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:13px;line-height:20.799999237060547px;">
<p><span style="font-size:18pt;"><span>本博文的主要内容有</span></span></p>
<p><span style="font-size:18pt;"><span>　　.HBase的单机模式（1节点）安装</span></span></p>
<p><span style="font-size:18pt;"><span>　　.HBase的单机模式（1节点）的启动</span></span></p>
<p><span style="font-size:18pt;"><span>　　.HBase的伪分布模式（1节点）安装</span></span></p>
<p> 　　<span style="font-size:18pt;"><span>  .HBase的伪分布模式（1节点）的启动</span></span></p>
<p><span>　　   <span style="font-size:18pt;"> .HBase的分布模式（3、5节点）安装</span></span></p>
<p><span style="font-size:18pt;"> 　  <span>.HBase的分布模式（3、5节点）的启动</span></span></p>
<h1 style="font-size:28px;color:rgb(102,102,102);line-height:1.5;">
 　　　　　　见博客：     <a class="postTitle2" href="http://www.cnblogs.com/zlslch/p/5952869.html" rel="nofollow" style="text-decoration:none;color:rgb(52,104,164);">HBase HA的分布式集群部署</a></h1>
<p><span>　 　<span style="font-size:18pt;">　.HBase环境搭建60010端口无法访问问题解决方案</span></span></p>
<p><span>　　　　-------------　　　</span>注意　HBase1.X版本之后，没60010了。      <span> -------------　</span></p>
<p><br>
　　　　　　　　参考：http://blog.csdn.net/tian_li/article/details/50601210</p>
<p> 　　   <span style="font-size:18pt;"><span>.进入HBase Shell</span></span></p>
<p>　　    <span style="font-size:18pt;"><span>.为什么在HBase，需要使用zookeeper？</span></span></p>
<p> 　　  <span style="font-size:18pt;"><span> .关于HBase的更多技术细节，强烈必多看</span></span></p>
<p> 　<span style="font-size:18pt;"><span>　　   .获取命令列表：help帮助命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.创建表：create命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.向表中加入行：put命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.从表中检索行：get命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.读取多行：scan命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.统计表中的行数：count命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.删除行：delete命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.清空表：truncate命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.删除表:drop命令</span></span></p>
<p><span style="font-size:18pt;"><span>　　　　.更换表 ：alter命令</span></span></p>
<p> </p>
<p> </p>
<p> </p>
<p><span style="font-size:24px;"><span>想说的是，</span></span></p>
<pre class="best-text mb-10">HBase的安装包里面有自带zookeeper的。很多系统部署也是直接启动上面的zookeeper。 本来也是没有问题的，想想吧，系统里也只有hbase在用zookeeper。<br>先启动zookeeper，再将hbase起来就好了 。<br>但是今天遇到了一个很蛋疼的问题。和同事争论了很久。  因为我们是好多hbase集群共用一个zookeeper的，其中一个集群需要从hbase 0.90.2 升级到hbase 0.92上，自然，包也要更新。<br>但是其中一台regionserver上面同时也有跑zookeeper，而zookeeper还是用hbase 0.90.2 自带的zookeeper在跑。 <br>现在好了，升级一个regionserver，连着zookeeper也要受到牵连，看来必须要重启，不然，jar包替换掉，可能会影响到zk正在跑的经常。<br>但是重启zk毕竟对正在连接这个zk的client端会有短暂的影响。 <br>真是蛋疼。本来只是升级hbase，zk却强耦合了。  <br>虽然后来证明zookeeper只要启动了，哪怕jar包删除也不会影响到正在跑的zk进程，但是这样的不规范带来的风险，实在是没有必要。<br> 所以作为运维，我强烈建议zk 和hbase分开部署，就直接部署官方的zk 好了，因为zk本身就是一个独立的服务，没有必要和hbase 耦合在一起。<br>在分布式的系统部署上面，一个角色就用一个专门的文件夹管理，不要用同一个目录下，这样子真的容易出问题。 <br>当然datanode和tasktracker另当别论，他们本身关系密切。</pre>
<p> </p>
<p>　　当然，这里，我是玩的单节点的集群，来安装HBase而已，只是来玩玩。所以，完全，只需用HBase的安装包里自带的zookeeper就好了。</p>
<p>除非，是多节点的分布式集群，最好用外部的zookeeper。</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>HDFS的版本，不同，HBase里的内部也不一样。</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224522515-1282003072.png" alt="" style="border:0px;"></p>
<p> </p>
<p><a href="http://hbase.apache.org/" rel="nofollow" style="color:rgb(52,104,164);">http://hbase.apache.org/</a></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224554281-902472284.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p> </p>
<p><a href="https://issues.apache.org/jira/browse/HBASE/?selectedTab=com.atlassian.jira.jira-projects-plugin:summary-panel" rel="nofollow" style="color:rgb(52,104,164);">https://issues.apache.org/jira/browse/HBASE/?selectedTab=com.atlassian.jira.jira-projects-plugin:summary-panel</a></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224613359-1414140208.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p><span style="font-size:18pt;"><span>　.HBase的单机模式安装</span></span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012125205421-1755319167.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 app]$ ls<br>
hadoop-2.4.1 hbase-0.96.2-hadoop2 hive-0.12.0 jdk1.7.0_65<br>
[hadoop@weekend110 app]$ cd hbase-0.96.2-hadoop2/<br>
[hadoop@weekend110 hbase-0.96.2-hadoop2]$ ls<br>
bin CHANGES.txt conf docs hbase-webapps lib LICENSE.txt logs NOTICE.txt README.txt<br>
[hadoop@weekend110 hbase-0.96.2-hadoop2]$ cd conf/<br>
[hadoop@weekend110 conf]$ ls<br>
hadoop-metrics2-hbase.properties hbase-env.cmd hbase-env.sh hbase-policy.xml hbase-site.xml log4j.properties regionservers<br>
[hadoop@weekend110 conf]$ <span style="color:rgb(255,0,0);">vim hbase-env.sh</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012125246609-1639309895.png" alt="" style="border:0px;"></p>
<p># Tell HBase whether it should manage it's own instance of Zookeeper or not.<br><span style="color:rgb(255,0,0);">export HBASE_MANAGES_ZK=true</span></p>
<p> 设HBASE_MANAGES_ZK=true，在启动HBase时，HBase把Zookeeper作为自身的一部分运行。</p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012125326812-265335651.png" alt="" style="border:0px;"></p>
<p><span style="color:rgb(255,0,0);"> export JAVA_HOME=/home/hadoop/app/jdk1.7.0_65</span></p>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012125359484-1147119676.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 conf]$ ls<br>
hadoop-metrics2-hbase.properties hbase-env.cmd hbase-env.sh hbase-policy.xml hbase-site.xml log4j.properties regionservers<br>
[hadoop@weekend110 conf]$<span style="color:rgb(255,0,0);"> vim hbase-site.xml</span></p>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012125420875-1237931297.png" alt="" style="border:0px;"></p>
<p>&lt;configuration&gt;<br>
　　&lt;property&gt;<br>
　　　　&lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
　　　　&lt;value&gt;<span style="color:rgb(255,0,0);">file:///tmp/hbase-hadoo</span>p/hbase&lt;/value&gt;<br>
　　&lt;/property&gt;<br>
　　&lt;property&gt;<br>
　　　　&lt;name&gt;dfs.replication&lt;/name&gt;<br>
　　　　&lt;value&gt;1&lt;/value&gt;<br>
　　&lt;/property&gt;<br>
&lt;/configuration&gt;</p>
<p> </p>
<p> 在这里，有些资料上说，file:///tmp/hbase-${user.name}/hbase</p>
<p>　　可以看到，默认情况下HBase的数据存储在根目录下的tmp文件夹下的。熟悉Linux的人知道，此文件夹为临时文件夹。也就是说，当系统重启的时候，此文件夹中的内容将被清空。这样用户保存在HBase中的数据也会丢失，这当然是用户不想看到的事情。因此，用户需要将HBase数据的存储位置修改为自己希望的存储位置。</p>
<p> 　　比如，可以，/home/hadoop/data/hbase，当然，我这里，是因为，伪分布模式和分布式模式，都玩过了。方便，练习加强HBase的shell操作。而已，拿单机模式玩玩。</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>　　<span style="font-size:18pt;"><span>.HBase的单机模式的启动</span></span></p>
<p><span style="font-size:18pt;"><span>总结就是：先启动hadoop集群的进程，再启动hbase的进程</span></span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012124748828-1825666852.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 hbase-0.96.2-hadoop2]$ cd bin<br>
[hadoop@weekend110 bin]$ ls<br>
get-active-master.rb hbase-common.sh hbase-jruby region_mover.rb start-hbase.cmd thread-pool.rb<br>
graceful_stop.sh hbase-config.cmd hirb.rb regionservers.sh start-hbase.sh zookeepers.sh<br>
hbase hbase-config.sh local-master-backup.sh region_status.rb stop-hbase.cmd<br>
hbase-cleanup.sh hbase-daemon.sh local-regionservers.sh replication stop-hbase.sh<br>
hbase.cmd hbase-daemons.sh master-backup.sh rolling-restart.sh test<br>
[hadoop@weekend110 bin]$ jps<br>
2443 NameNode<br>
2970 NodeManager<br>
2539 DataNode<br>
2729 SecondaryNameNode<br>
2866 ResourceManager<br>
4634 Jps<br>
[hadoop@weekend110 bin]$<span style="color:rgb(255,0,0);"> ./start-hbase.sh</span><br>
starting master, logging to /home/hadoop/app/hbase-0.96.2-hadoop2/logs/hbase-hadoop-master-weekend110.out<br>
[hadoop@weekend110 bin]$ jps<br>
2443 NameNode<br>
2970 NodeManager<br>
2539 DataNode<br>
2729 SecondaryNameNode<br>
2866 ResourceManager<br>
4740 HMaster<br>
4819 Jps<br>
[hadoop@weekend110 bin]$ <span style="color:rgb(255,0,0);">hbase shell</span><br>
2016-10-12 12:43:11,095 INFO [main] Configuration.deprecation: hadoop.native.lib is deprecated. Instead, use io.native.lib.available<br>
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.<br>
Type "exit&lt;RETURN&gt;" to leave the HBase Shell<br>
Version 0.96.2-hadoop2, r1581096, Mon Mar 24 16:03:18 PDT 2014</p>
<p>hbase(main):001:0&gt; list<br>
TABLE <br>
SLF4J: Class path contains multiple SLF4J bindings.<br>
SLF4J: Found binding in [jar:file:/home/hadoop/app/hbase-0.96.2-hadoop2/lib/slf4j-log4j12-1.6.4.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: Found binding in [jar:file:/home/hadoop/app/hadoop-2.4.1/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br>
0 row(s) in 3.8200 seconds</p>
<p>=&gt; []<br>
hbase(main):002:0&gt; create 'mygirls', {NAME =&gt; 'base_info',VERSION =&gt; 3},{NAME =&gt; 'extra_info'}<br>
Unknown argument ignored for column family base_info: 1.8.7<br>
0 row(s) in 1.1560 seconds</p>
<p>=&gt; Hbase::Table - mygirls<br>
hbase(main):003:0&gt;</p>
<p> </p>
<p> </p>
<p> 测试</p>
<p> <a href="http://weekend110:60010/" rel="nofollow" style="color:rgb(52,104,164);">http://weekend110:60010/</a></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012131627859-1538251939.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p><span style="font-size:18pt;"><span>.HBase的伪分布模式（1节点）安装</span></span></p>
<p><span>1、  hbase-0.96.2-hadoop2-bin.tar.gz压缩包的上传</span></p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224653250-1982626133.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224655609-404580700.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224657718-700041204.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224700203-476991815.png" alt="" style="border:0px;"></p>
<p>sftp&gt; <span style="color:rgb(255,0,0);">cd /home/hadoop/app</span></p>
<p>sftp&gt; <span style="color:rgb(255,0,0);">put c:/hbase-0.96.2-hadoop2-bin.tar.gz</span></p>
<p>Uploading hbase-0.96.2-hadoop2-bin.tar.gz to /home/hadoop/app/hbase-0.96.2-hadoop2-bin.tar.gz</p>
<p>  100% 77507KB  19376KB/s 00:00:04    </p>
<p>c:/hbase-0.96.2-hadoop2-bin.tar.gz: 79367504 bytes transferred in 4 seconds (19376 KB/s)</p>
<p>sftp&gt;</p>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224731234-382781725.png" alt="" style="border:0px;"></p>
<p>或者，通过</p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224742265-128713052.png" alt="" style="border:0px;"></p>
<p>这里不多赘述。具体，可以看我的其他博客</p>
<p> </p>
<p> </p>
<p><span>2、 hbase-0.96.2-hadoop2-bin.tar.gz压缩包的解压</span></p>
<p><span><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224819453-1828454023.png" alt="" style="border:0px;"></span></p>
<p>[hadoop@weekend110 app]$ ls</p>
<p>hadoop-2.4.1  hbase-0.96.2-hadoop2-bin.tar.gz  hive-0.12.0  jdk1.7.0_65  zookeeper-3.4.6</p>
<p>[hadoop@weekend110 app]$ ll</p>
<p>total 77524</p>
<p>drwxr-xr-x. 11 hadoop hadoop     4096 Jul 18 20:11 hadoop-2.4.1</p>
<p>-rw-r--r--.  1 root   root   79367504 May 20 13:51 hbase-0.96.2-hadoop2-bin.tar.gz</p>
<p>drwxrwxr-x. 10 hadoop hadoop     4096 Oct 10 21:30 hive-0.12.0</p>
<p>drwxr-xr-x.  8 hadoop hadoop     4096 Jun 17  2014 jdk1.7.0_65</p>
<p>drwxr-xr-x. 10 hadoop hadoop     4096 Jul 30 10:28 zookeeper-3.4.6</p>
<p>[hadoop@weekend110 app]$ <span style="color:rgb(255,0,0);">tar -zxvf hbase-0.96.2-hadoop2-bin.tar.gz</span></p>
<p> </p>
<p> </p>
<p>3、<span>删除压缩包hbase-0.96.2-hadoop2-bin.tar.gz</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224856843-1175854140.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p>4、<span>将HBase文件权限赋予给hadoop用户，这一步，不需。</span></p>
<p>5、<span>HBase的配置</span></p>
<p><span><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011224919937-571366331.png" alt="" style="border:0px;"></span></p>
<p>　　<span style="color:rgb(255,0,0);"><span>注意啦，在hbase-0.96.2-hadoop2的目录下，有hbase-webapps，即，说明，可以通过web网页来访问HBase。</span></span></p>
<p> </p>
<p> </p>
<p>[hadoop@weekend110 app]$ ls</p>
<p>hadoop-2.4.1  hbase-0.96.2-hadoop2  hive-0.12.0  jdk1.7.0_65  zookeeper-3.4.6</p>
<p>[hadoop@weekend110 app]$ <span style="color:rgb(255,0,0);">cd hbase-0.96.2-hadoop2/</span></p>
<p>[hadoop@weekend110 hbase-0.96.2-hadoop2]$ ll</p>
<p>total 436</p>
<p>drwxr-xr-x.  4 hadoop hadoop   4096 Mar 25  2014 bin</p>
<p>-rw-r--r--.  1 hadoop hadoop 403242 Mar 25  2014 CHANGES.txt</p>
<p>drwxr-xr-x.  2 hadoop hadoop   4096 Mar 25  2014 conf</p>
<p>drwxr-xr-x. 27 hadoop hadoop   4096 Mar 25  2014 docs</p>
<p>drwxr-xr-x.  7 hadoop hadoop   4096 Mar 25  2014 hbase-webapps</p>
<p>drwxrwxr-x.  3 hadoop hadoop   4096 Oct 11 17:49 lib</p>
<p>-rw-r--r--.  1 hadoop hadoop  11358 Mar 25  2014 LICENSE.txt</p>
<p>-rw-r--r--.  1 hadoop hadoop    897 Mar 25  2014 NOTICE.txt</p>
<p>-rw-r--r--.  1 hadoop hadoop   1377 Mar 25  2014 README.txt</p>
<p>[hadoop@weekend110 hbase-0.96.2-hadoop2]$ <span style="color:rgb(255,0,0);">cd conf/</span></p>
<p>[hadoop@weekend110 conf]$ ls</p>
<p>hadoop-metrics2-hbase.properties  hbase-env.cmd  hbase-env.sh  hbase-policy.xml  hbase-site.xml  log4j.properties  regionservers</p>
<p>[hadoop@weekend110 conf]$</p>
<p> </p>
<p> </p>
<p><span><span style="color:rgb(255,0,0);">对于，多节点里，安装HBase，这里不多说了。具体，可以看我的博客</span></span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225012968-1757088946.png" alt="" style="border:0px;"></p>
<p>1.上传hbase安装包</p>
<p> </p>
<p>2.解压</p>
<p> </p>
<p>3.配置hbase集群，要修改3个文件（首先zk集群已经安装好了）</p>
<p>         注意：要把hadoop的hdfs-site.xml和core-site.xml 放到hbase/conf下</p>
<p>        </p>
<p>         3.1修改hbase-env.sh</p>
<p>         export JAVA_HOME=/usr/java/jdk1.7.0_55</p>
<p>         //告诉hbase使用外部的zk</p>
<p>         export HBASE_MANAGES_ZK=false</p>
<p>        </p>
<p>         vim hbase-site.xml</p>
<p>         &lt;configuration&gt;</p>
<p>                   &lt;!-- 指定hbase在HDFS上存储的路径 --&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p>                &lt;value&gt;hdfs://ns1/hbase&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>                   &lt;!-- 指定hbase是分布式的 --&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</p>
<p>                &lt;value&gt;true&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>                   &lt;!-- 指定zk的地址，多个用“,”分割 --&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;</p>
<p>                &lt;value&gt;weekend04:2181,weekend05:2181,weekend06:2181&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>         &lt;/configuration&gt;</p>
<p>        </p>
<p>         vim regionservers</p>
<p>         weekend03</p>
<p>         weekend04</p>
<p>         weekend05</p>
<p>         weekend06</p>
<p>        </p>
<p>         3.2拷贝hbase到其他节点</p>
<p>                   scp -r /weekend/hbase-0.96.2-hadoop2/ weekend02:/weekend/</p>
<p>                   scp -r /weekend/hbase-0.96.2-hadoop2/ weekend03:/weekend/</p>
<p>                   scp -r /weekend/hbase-0.96.2-hadoop2/ weekend04:/weekend/</p>
<p>                   scp -r /weekend/hbase-0.96.2-hadoop2/ weekend05:/weekend/</p>
<p>                   scp -r /weekend/hbase-0.96.2-hadoop2/ weekend06:/weekend/</p>
<p>4.将配置好的HBase拷贝到每一个节点并同步时间。</p>
<p> </p>
<p>5.启动所有的hbase</p>
<p>         分别启动zk</p>
<p>                   ./zkServer.sh start</p>
<p>         启动hbase集群</p>
<p>                   start-dfs.sh</p>
<p>         启动hbase，在主节点上运行：</p>
<p>                   start-hbase.sh</p>
<p>6.通过浏览器访问hbase管理页面</p>
<p>         192.168.1.201:60010</p>
<p>7.为保证集群的可靠性，要启动多个HMaster</p>
<p>         hbase-daemon.sh start master</p>
<p>         </p>
<p> </p>
<p> </p>
<p><span>我这里，因，考虑到自己玩玩，伪分布集群里安装HBase。</span></p>
<p><span>hbase-env.sh</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225043093-777148979.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 conf]$ ls</p>
<p>hadoop-metrics2-hbase.properties  hbase-env.cmd  hbase-env.sh  hbase-policy.xml  hbase-site.xml  log4j.properties  regionservers</p>
<p>[hadoop@weekend110 conf]$ <span style="color:rgb(255,0,0);">vim hbase-env.sh</span></p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225112234-107857730.png" alt="" style="border:0px;"></p>
<p>/home/hadoop/app/jdk1.7.0_65</p>
<p> </p>
<p><span style="color:rgb(255,0,0);"><span>单节点的hbase-env.sh，需要修改2处。</span></span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225132796-1831017960.png" alt="" style="border:0px;"></p>
<p>export JAVA_HOME=<span style="color:rgb(255,0,0);">/home/hadoop/app/jdk1.7.0_65 </span></p>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225206078-71854040.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225208312-449113325.png" alt="" style="border:0px;"></p>
<p>export HBASE_MANAGES_ZK=<span style="color:rgb(255,0,0);">false</span></p>
<p> </p>
<p><span style="font-size:18pt;"><span>.为什么在HBase，需要使用zookeeper？</span></span></p>
<p> </p>
<p>　　大家，很多人，都有一个疑问，为什么在HBase，需要使用zookeeper？至于为什么最好使用外部安装的zookeeper，而不是HBase自带的zookeeper，这里，我实在是不多赘述了。</p>
<p>　　zookeeper存储的是HBase中ROOT表和META表的位置。此外，zookeeper还负责监控多个机器的状态（每台机器到zookeeper中注册一个实例）。当某台机器发生故障时</p>
<p>，zookeeper会第一时间感知到，并通知HBase Master进行相应的处理。同时，当HBase Master发生故障的时候，zookeeper还负责HBase Master的恢复工作，能够保证还在同一时刻系统中只有一台HBase Master提供服务。</p>
<p>　　具体例子，见</p>
<h1 style="font-size:28px;color:rgb(102,102,102);line-height:1.5;">
                  <a class="postTitle2" href="http://www.cnblogs.com/zlslch/p/5952869.html" rel="nofollow" style="text-decoration:none;color:rgb(52,104,164);">HBase HA的分布式集群部署</a>  的最低端。</h1>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225234625-1832741852.png" alt="" style="border:0px;"></p>
<p><span>hbase-site.xml</span></p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225254593-1451531639.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 conf]$ ls</p>
<p>hadoop-metrics2-hbase.properties  hbase-env.cmd  hbase-env.sh  hbase-policy.xml  hbase-site.xml  log4j.properties  regionservers</p>
<p>[hadoop@weekend110 conf]$ <span style="color:rgb(255,0,0);">vim hbase-site.xml</span></p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225345953-48679416.png" alt="" style="border:0px;"></p>
<p>&lt;configuration&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;</p>
<p>                &lt;value&gt;/home/hadoop/data/zookeeper/zkdata&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;hbase.tmp.dir&lt;/name&gt;</p>
<p>                &lt;value&gt;/home/hadoop/data/tmp/hbase&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;</p>
<p>                &lt;value&gt;2181&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p>                &lt;value&gt;hdfs://weekend110:9000/hbase&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</p>
<p>                &lt;value&gt;false&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>        &lt;property&gt;</p>
<p>                &lt;name&gt;dfs.replication&lt;/name&gt;</p>
<p>                &lt;value&gt;1&lt;/value&gt;</p>
<p>        &lt;/property&gt;</p>
<p>&lt;/configuration&gt;</p>
<p> </p>
<p> </p>
<p><span>新建目录</span></p>
<p><span>/home/hadoop/data/zookeeper/zkdata</span></p>
<p><span>/home/hadoop/data/tmp/hbase</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225422812-1049791150.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 conf]$ pwd</p>
<p>/home/hadoop/app/hbase-0.96.2-hadoop2/conf</p>
<p>[hadoop@weekend110 conf]$ <span style="color:rgb(255,0,0);">mkdir -p /home/hadoop/data/zookeeper/zkdata</span></p>
<p>[hadoop@weekend110 conf]$ <span style="color:rgb(255,0,0);">mkdir -p /home/hadoop/data/tmp/hbase</span></p>
<p>[hadoop@weekend110 conf]$</p>
<p> </p>
<p> </p>
<p> </p>
<p><span>regionservers</span></p>
<p><span><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225459953-371336573.png" alt="" style="border:0px;"></span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225502062-1208275925.png" alt="" style="border:0px;"></p>
<p><span style="color:rgb(255,0,0);">weekend110</span></p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225523000-577266793.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 conf]$ ls</p>
<p>hadoop-metrics2-hbase.properties  hbase-env.cmd  hbase-env.sh  hbase-policy.xml  hbase-site.xml  log4j.properties  regionservers</p>
<p>[hadoop@weekend110 conf]$ <span style="color:rgb(255,0,0);">cp /home/hadoop/app/hadoop-2.4.1/etc/hadoop/{core-site.xml,hdfs-site.xml} ./</span></p>
<p>[hadoop@weekend110 conf]$ ls</p>
<p>core-site.xml                     hbase-env.cmd  hbase-policy.xml  hdfs-site.xml     regionservers</p>
<p>hadoop-metrics2-hbase.properties  hbase-env.sh   hbase-site.xml    log4j.properties</p>
<p>[hadoop@weekend110 conf]$</p>
<p> </p>
<p> </p>
<p><span>vi /etc/profile</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225550234-1108231439.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 conf]$ su root</p>
<p>Password:</p>
<p>[root@weekend110 conf]#<span style="color:rgb(255,0,0);"> vim /etc/profile</span></p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225608046-1248013869.png" alt="" style="border:0px;"></p>
<p>export JAVA_HOME=/home/hadoop/app/jdk1.7.0_65</p>
<p>export HADOOP_HOME=/home/hadoop/app/hadoop-2.4.1</p>
<p>export ZOOKEEPER_HOME=/home/hadoop/app/zookeeper-3.4.6</p>
<p>export HIVE_HOME=/home/hadoop/app/hive-0.12.0</p>
<p><span style="color:rgb(255,0,0);">export HBASE_HOME=/home/hadoop/app/hbase-0.96.2-hadoop2</span></p>
<p>export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$ZOOKEEPER_HOME/bin:$HIVE_HOME/bin<span style="color:rgb(255,0,0);">:$HBASE_HOME/bin</span></p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225637875-1406581010.png" alt="" style="border:0px;"></p>
<p>[root@weekend110 conf]# <span style="color:rgb(255,0,0);">source /etc/profile</span></p>
<p>[root@weekend110 conf]# <span style="color:rgb(255,0,0);">su hadoop</span></p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225722312-219204552.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p><span style="font-size:18pt;"><span>.HBase的伪分布模式的启动</span></span></p>
<p>由于伪分布模式的运行基于HDFS，因此在运行HBase之前首先需要启动HDFS，</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225806703-1896026583.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 hadoop-2.4.1]$ jps</p>
<p>5802 Jps</p>
<p>[hadoop@weekend110 hadoop-2.4.1]$ sbin/start-all.sh</p>
<p>This script is Deprecated. Instead use start-dfs.sh and start-yarn.sh</p>
<p>Starting namenodes on [weekend110]</p>
<p>weekend110: starting namenode, logging to /home/hadoop/app/hadoop-2.4.1/logs/hadoop-hadoop-namenode-weekend110.out</p>
<p>weekend110: starting datanode, logging to /home/hadoop/app/hadoop-2.4.1/logs/hadoop-hadoop-datanode-weekend110.out</p>
<p>Starting secondary namenodes [0.0.0.0]</p>
<p>0.0.0.0: starting secondarynamenode, logging to /home/hadoop/app/hadoop-2.4.1/logs/hadoop-hadoop-secondarynamenode-weekend110.out</p>
<p>starting yarn daemons</p>
<p>starting resourcemanager, logging to /home/hadoop/app/hadoop-2.4.1/logs/yarn-hadoop-resourcemanager-weekend110.out</p>
<p>weekend110: starting nodemanager, logging to /home/hadoop/app/hadoop-2.4.1/logs/yarn-hadoop-nodemanager-weekend110.out</p>
<p>[hadoop@weekend110 hadoop-2.4.1]$ jps</p>
<p>6022 DataNode</p>
<p>6149 SecondaryNameNode</p>
<p>5928 NameNode</p>
<p>6287 ResourceManager</p>
<p>6426 Jps</p>
<p>6387 NodeManager</p>
<p>[hadoop@weekend110 hadoop-2.4.1]$</p>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225846515-1277701735.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 hbase-0.96.2-hadoop2]$ pwd</p>
<p>/home/hadoop/app/hbase-0.96.2-hadoop2</p>
<p>[hadoop@weekend110 hbase-0.96.2-hadoop2]$ ls</p>
<p>bin  CHANGES.txt  conf  docs  hbase-webapps  lib  LICENSE.txt  NOTICE.txt  README.txt</p>
<p>[hadoop@weekend110 hbase-0.96.2-hadoop2]$ cd bin</p>
<p>[hadoop@weekend110 bin]$ ls</p>
<p>get-active-master.rb  hbase-common.sh   hbase-jruby             region_mover.rb     start-hbase.cmd  thread-pool.rb</p>
<p>graceful_stop.sh      hbase-config.cmd  hirb.rb                 regionservers.sh    start-hbase.sh   zookeepers.sh</p>
<p>hbase                 hbase-config.sh   local-master-backup.sh  region_status.rb    stop-hbase.cmd</p>
<p>hbase-cleanup.sh      hbase-daemon.sh   local-regionservers.sh  replication         stop-hbase.sh</p>
<p>hbase.cmd             hbase-daemons.sh  master-backup.sh        rolling-restart.sh  test</p>
<p>[hadoop@weekend110 bin]$ ./start-hbase.sh</p>
<p>starting master, logging to /home/hadoop/app/hbase-0.96.2-hadoop2/logs/hbase-hadoop-master-weekend110.out</p>
<p>[hadoop@weekend110 bin]$ jps</p>
<p>6022 DataNode</p>
<p>6149 SecondaryNameNode</p>
<p>5928 NameNode</p>
<p>6707 Jps</p>
<p>6287 ResourceManager</p>
<p><span>6530 HMaster</span></p>
<p>6387 NodeManager</p>
<p>[hadoop@weekend110 bin]$</p>
<p> </p>
<p><a href="http://weekend110:60010/" rel="nofollow" style="color:rgb(52,104,164);">http://weekend110:60010/</a></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225910531-215514190.png" alt="" style="border:0px;"></p>
<p>参考博客：<a href="http://blog.csdn.net/u013575812/article/details/46919011" rel="nofollow" style="color:rgb(52,104,164);">http://blog.csdn.net/u013575812/article/details/46919011</a></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225924578-1616860180.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p>[hadoop@weekend110 bin]$ pwd</p>
<p>/home/hadoop/app/hbase-0.96.2-hadoop2/bin</p>
<p>[hadoop@weekend110 bin]$ <span><span style="color:rgb(255,0,0);">hadoop dfsadmin -safemode leave</span></span></p>
<p>DEPRECATED: Use of this script to execute hdfs command is deprecated.</p>
<p>Instead use the hdfs command for it.</p>
<p> </p>
<p>Safe mode is OFF</p>
<p>[hadoop@weekend110 bin]$ jps</p>
<p>6022 DataNode</p>
<p>7135 Jps</p>
<p>6149 SecondaryNameNode</p>
<p>5928 NameNode</p>
<p>6287 ResourceManager</p>
<p>6387 NodeManager</p>
<p>[hadoop@weekend110 bin]$<span style="color:rgb(255,0,0);"> ./start-hbase.sh</span></p>
<p>starting master, logging to /home/hadoop/app/hbase-0.96.2-hadoop2/logs/hbase-hadoop-master-weekend110.out</p>
<p>[hadoop@weekend110 bin]$ jps</p>
<p>6022 DataNode</p>
<p>7245 HMaster</p>
<p>6149 SecondaryNameNode</p>
<p>5928 NameNode</p>
<p>6287 ResourceManager</p>
<p>6387 NodeManager</p>
<p>7386 Jps</p>
<p>[hadoop@weekend110 bin]$</p>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161011225956812-2070019674.png" alt="" style="border:0px;"></p>
<p>依旧如此，继续...解决!</p>
<p> </p>
<p>参考博客:http://www.th7.cn/db/nosql/201510/134214.shtml</p>
<p> </p>
<p>　　在安装hbase-0.96.2-hadoop2时发现一个问题，<span style="color:rgb(255,0,0);">hbase能够正常使用，hbase shell 完全可用，但是60010页面却打不开</span>，最后找到问题，是因为很多版本的hbase的master web 默认是不运行的，所以需要自己配置默认端口。</p>
<p>　　</p>
<p>配置如下</p>
<p>　　在hbase-site.xml中加入一下内容即可</p>
<p>　　　　&lt;property&gt;<br>
　　　　　　&lt;name&gt;hbase.master.info.port&lt;/name&gt;<br>
　　　　　　&lt;value&gt;60010&lt;/value&gt;<br>
　　　　&lt;/property&gt;</p>
<p> 　　　 &lt;property&gt;<br>
　　　　　　&lt;name&gt;hbase.regionserver .info.port&lt;/name&gt;<br>
　　　　　　&lt;value&gt;60020&lt;/value&gt;<br>
　　　　&lt;/property&gt;</p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012092838078-1029265891.png" alt="" style="border:0px;"></p>
<p>&lt;configuration&gt;<br>
　　&lt;property&gt;<br>
　　　　&lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;<br>
　　　　&lt;value&gt;/home/hadoop/data/zookeeper/zkdata&lt;/value&gt;<br>
　　&lt;/property&gt;<br>
　　&lt;property&gt;<br>
　　　　&lt;name&gt;hbase.tmp.dir&lt;/name&gt;<br>
　　　　&lt;value&gt;/home/hadoop/data/tmp/hbase&lt;/value&gt;<br>
　　&lt;/property&gt;<br>
　　&lt;property&gt;<br>
　　　　&lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;<br>
　　　　&lt;value&gt;2181&lt;/value&gt;<br>
　　&lt;/property&gt;<br>
　　<span style="color:rgb(255,0,0);">&lt;property&gt;</span><br><span style="color:rgb(255,0,0);">　　　　&lt;name&gt;hbase.master.info.port&lt;/name&gt;</span><br><span style="color:rgb(255,0,0);">　　　　&lt;value&gt;60010&lt;/value&gt;</span><br><span style="color:rgb(255,0,0);">　　&lt;/property&gt;</span><br><span style="color:rgb(255,0,0);">　　&lt;property&gt;</span><br><span style="color:rgb(255,0,0);">　　　　&lt;name&gt;hbase.regionserver.info.port&lt;/name&gt;</span><br><span style="color:rgb(255,0,0);">　　　　&lt;value&gt;60020&lt;/value&gt;</span><br><span style="color:rgb(255,0,0);">　　&lt;/property&gt;</span><br>
　　&lt;property&gt;<br>
　　　　&lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
　　　　&lt;value&gt;hdfs://weekend110:9000/hbase&lt;/value&gt;<br>
　　&lt;/property&gt;<br>
　　&lt;property&gt;<br>
　　　　&lt;name&gt;hbase.cluster.distributed&lt;/name&gt;<br>
　　　　&lt;value&gt;false&lt;/value&gt;<br>
　　&lt;/property&gt;<br>
　　&lt;property&gt;<br>
　　　　&lt;name&gt;dfs.replication&lt;/name&gt;<br>
　　　　&lt;value&gt;1&lt;/value&gt;<br>
　　&lt;/property&gt;<br>
&lt;/configuration&gt;</p>
<p> </p>
<p> </p>
<p><span style="font-size:18pt;"><span>进入HBase Shell</span></span></p>
<p> </p>
<p>    进入hbase命令行<br>
./hbase shell</p>
<p>    显示hbase中的表<br>
list</p>
<p>    创建user表，包含info、data两个列族<br>
create 'user', 'info1', 'data1'<br>
create 'user', {NAME =&gt; 'info', VERSIONS =&gt; '3'}</p>
<p>     向<span style="color:rgb(0,0,255);">user是表</span>中插入信息，<span style="color:rgb(0,0,255);">row key是rk0001</span>，<span style="color:rgb(0,0,255);">列族是info</span>中添加<span style="color:rgb(0,0,255);">name是列修饰符（标识符）</span>，<span style="color:rgb(0,0,255);">值是zhangsan</span><br>
put 'user', 'rk0001', 'info:name', 'zhangsan'</p>
<p>     向user表中插入信息，row key是rk0001，列族info中添加gender列标示符，值是female<br>
put 'user', 'rk0001', 'info:gender', 'female'</p>
<p>     向user表中插入信息，row key是rk0001，列族info中添加age列标示符，值是20<br>
put 'user', 'rk0001', 'info:age', 20</p>
<p>     向user表中插入信息，row key是rk0001，列族data中添加pic列标示符，值是picture<br>
put 'user', 'rk0001', 'data:pic', 'picture'</p>
<p>     <span style="color:rgb(0,0,255);">获取</span>user表中row key为rk0001的所有信息<br>
get 'user', 'rk0001'</p>
<p>     获取user表中row key是rk0001，info列族的所有信息<br>
get 'user', 'rk0001', 'info'</p>
<p>     获取user表中row key是rk0001，info列族的name、age列标示符的信息<br>
get 'user', 'rk0001', 'info:name', 'info:age'</p>
<p>     获取user表中row key是rk0001，info、data列族的信息<br>
get 'user', 'rk0001', 'info', 'data'<br>
get 'user', 'rk0001', {COLUMN =&gt; ['info', 'data']}</p>
<p>get 'user', 'rk0001', {COLUMN =&gt; ['info:name', 'data:pic']}</p>
<p>   获取user表中row key是rk0001，列族是info，版本号最新5个的信息<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info', VERSIONS =&gt; 2}<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info:name', VERSIONS =&gt; 5}<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info:name', VERSIONS =&gt; 5, TIMERANGE =&gt; [1392368783980, 1392380169184]}</p>
<p>    获取user表中row key是rk0001，cell的值是zhangsan的信息<br>
get 'people', 'rk0001', {FILTER =&gt; "ValueFilter(=, 'binary:图片')"}</p>
<p>   获取user表中row key是rk0001，列标示符中含有a的信息<br>
get 'people', 'rk0001', {FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}</p>
<p>put 'user', 'rk0002', 'info:name', 'fanbingbing'<br>
put 'user', 'rk0002', 'info:gender', 'female'<br>
put 'user', 'rk0002', 'info:nationality', '中国'<br>
get 'user', 'rk0002', {FILTER =&gt; "ValueFilter(=, 'binary:中国')"}</p>
<p><br>
    查询user表中的所有信息<br>
scan 'user'</p>
<p>    查询user表中列族为info的信息<br>
scan 'user', {COLUMNS =&gt; 'info'}<br>
scan 'user', {COLUMNS =&gt; 'info', RAW =&gt; true, VERSIONS =&gt; 5}<br>
scan 'persion', {COLUMNS =&gt; 'info', RAW =&gt; true, VERSIONS =&gt; 3}<br>
   查询user表中列族为info和data的信息<br>
scan 'user', {COLUMNS =&gt; ['info', 'data']}<br>
scan 'user', {COLUMNS =&gt; ['info:name', 'data:pic']}</p>
<p><br>
    查询user表中列族是info、列标示符是name的信息<br>
scan 'user', {COLUMNS =&gt; 'info:name'}</p>
<p>    查询user表中列族是info、列标示符是name的信息,并且版本最新的5个<br>
scan 'user', {COLUMNS =&gt; 'info:name', VERSIONS =&gt; 5}</p>
<p>    查询user表中列族为info和data且列标示符中含有a字符的信息<br>
scan 'user', {COLUMNS =&gt; ['info', 'data'], FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}</p>
<p>    查询user表中列族为info，rk范围是[rk0001, rk0003)的数据<br>
scan 'people', {COLUMNS =&gt; 'info', STARTROW =&gt; 'rk0001', ENDROW =&gt; 'rk0003'}</p>
<p>     查询user表中row key以rk字符开头的<br>
scan 'user',{FILTER=&gt;"PrefixFilter('rk')"}</p>
<p>     查询user表中指定范围的数据<br>
scan 'user', {TIMERANGE =&gt; [1392368783980, 1392380169184]}</p>
<p><br>
      删除user表row key为rk0001，列标示符为info:name的数据<br>
delete 'people', 'rk0001', 'info:name'<br>
      删除user表row key为rk0001，列标示符为info:name，timestamp为1392383705316的数据<br>
delete 'user', 'rk0001', 'info:name', 1392383705316</p>
<p><br>
      清空user表中的数据<br>
truncate 'people'</p>
<p> </p>
<p> </p>
<p>修改表结构<br>
    首先停用user表（新版本不用）<br>
disable 'user'</p>
<p>    添加两个列族f1和f2<br>
alter 'people', NAME =&gt; 'f1'<br>
alter 'user', NAME =&gt; 'f2'<br>
    启用表<br>
enable 'user'</p>
<p><br>
###disable 'user'(新版本不用)<br>
      删除一个列族：<br>
alter 'user', NAME =&gt; 'f1', METHOD =&gt; 'delete' 或 alter 'user', 'delete' =&gt; 'f1'</p>
<p>      添加列族f1同时删除列族f2<br>
alter 'user', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2', METHOD =&gt; 'delete'}</p>
<p>      将user表的f1列族版本号改为5<br>
alter 'people', NAME =&gt; 'info', VERSIONS =&gt; 5<br>
     启用表<br>
enable 'user'</p>
<p><br>
    删除表<br>
disable 'user'<br>
drop 'user'</p>
<p><br>
get 'person', 'rk0001', {FILTER =&gt; "ValueFilter(=, 'binary:中国')"}<br>
get 'person', 'rk0001', {FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}<br>
scan 'person', {COLUMNS =&gt; 'info:name'}<br>
scan 'person', {COLUMNS =&gt; ['info', 'data'], FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}<br>
scan 'person', {COLUMNS =&gt; 'info', STARTROW =&gt; 'rk0001', ENDROW =&gt; 'rk0003'}</p>
<p>scan 'person', {COLUMNS =&gt; 'info', STARTROW =&gt; '20140201', ENDROW =&gt; '20140301'}<br>
scan 'person', {COLUMNS =&gt; 'info:name', TIMERANGE =&gt; [1395978233636, 1395987769587]}<br>
delete 'person', 'rk0001', 'info:name'</p>
<p>alter 'person', NAME =&gt; 'ffff'<br>
alter 'person', NAME =&gt; 'info', VERSIONS =&gt; 10</p>
<p><br>
get 'user', 'rk0002', {COLUMN =&gt; ['info:name', 'data:pic']}</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012100938218-1881308940.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 bin]$ pwd<br>
/home/hadoop/app/hbase-0.96.2-hadoop2/bin<br>
[hadoop@weekend110 bin]$ ./hbase shell<br>
2016-10-12 10:09:42,925 INFO [main] Configuration.deprecation: hadoop.native.lib is deprecated. Instead, use io.native.lib.available<br>
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.<br>
Type "exit&lt;RETURN&gt;" to leave the HBase Shell<br>
Version 0.96.2-hadoop2, r1581096, Mon Mar 24 16:03:18 PDT 2014</p>
<p>hbase(main):001:0&gt;</p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012101136703-1534637199.png" alt="" style="border:0px;"></p>
<p>hbase(main):001:0&gt; help<br>
HBase Shell, version 0.96.2-hadoop2, r1581096, Mon Mar 24 16:03:18 PDT 2014<br>
Type 'help "COMMAND"', (e.g. 'help "get"' -- the quotes are necessary) for help on a specific command.<br>
Commands are grouped. Type 'help "COMMAND_GROUP"', (e.g. 'help "general"') for help on a command group.</p>
<p><span>COMMAND GROUPS:     //罗列出了所有的命令</span><br>
Group name: <span>general     //通常命令，这些命令将返回集群级的通用信息。</span><br>
Commands: status, table_help, version, whoami</p>
<p>Group name: <span>ddl           //ddl操作命令，这些命令会创建、更换和删除HBase表</span><br>
Commands: alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, get_table, is_disabled, is_enabled, list, show_filters</p>
<p>Group name:<span> namespace    //namespace命令</span><br>
Commands: alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables</p>
<p>Group name: <span>dml　　　　　　//dml操作命令，这些命令会新增、修改和删除HBase表中的数据</span><br>
Commands: count, delete, deleteall, get, get_counter, incr, put, scan, truncate, truncate_preserve</p>
<p>Group name: <span>tools　　　　    //tools命令，这些命令可以维护HBase集群</span><br>
Commands: assign, balance_switch, balancer, catalogjanitor_enabled, catalogjanitor_run, catalogjanitor_switch, close_region, compact, flush, hlog_roll, major_compact, merge_region, move, split, trace, unassign, zk_dump</p>
<p>Group name: <span>replication　　　　//replication命令，这些命令可以增加和删除集群的节点</span><br>
Commands: add_peer, disable_peer, enable_peer, list_peers, list_replicated_tables, remove_peer</p>
<p>Group name: <span>snapshot　　　　   //snapshot命令，这些命令用于对HBase集群进行快照以便备份和恢复集群</span><br>
Commands: clone_snapshot, delete_snapshot, list_snapshots, rename_snapshot, restore_snapshot, snapshot</p>
<p>Group name: <span>security　　　　　　//security命令，这些命令可以控制HBase的安全性</span><br>
Commands: grant, revoke, user_permission</p>
<p>SHELL USAGE:<br>
Quote all names in HBase Shell such as table and column names. Commas delimit<br>
command parameters. Type &lt;RETURN&gt; after entering a command to run it.<br>
Dictionaries of configuration used in the creation and alteration of tables are<br>
Ruby Hashes. They look like this:</p>
<p>{'key1' =&gt; 'value1', 'key2' =&gt; 'value2', ...}</p>
<p>and are opened and closed with curley-braces. Key/values are delimited by the<br>
'=&gt;' character combination. Usually keys are predefined constants such as<br>
NAME, VERSIONS, COMPRESSION, etc. Constants do not need to be quoted. Type<br>
'Object.constants' to see a (messy) list of all constants in the environment.</p>
<p>If you are using binary keys or values and need to enter them in the shell, use<br>
double-quote'd hexadecimal representation. For example:</p>
<p>hbase&gt; get 't1', "key\x03\x3f\xcd"<br>
hbase&gt; get 't1', "key\003\023\011"<br>
hbase&gt; put 't1', "test\xef\xff", 'f1:', "\x01\x33\x40"</p>
<p>The HBase shell is the (J)Ruby IRB with the above HBase-specific commands added.<br>
For more on the HBase Shell, see http://hbase.apache.org/docs/current/book.html<br>
hbase(main):002:0&gt;</p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012101359906-868897550.png" alt="" style="border:0px;"></p>
<p>hbase(main):002:0&gt; version<br>
0.96.2-hadoop2, r1581096, Mon Mar 24 16:03:18 PDT 2014</p>
<p>hbase(main):003:0&gt; <span style="color:rgb(255,0,0);">create</span></p>
<p>ERROR: wrong number of arguments (0 for 1)</p>
<p>Here is some help for this command:<br>
Creates a table. Pass a table name, and a set of column family<br>
specifications (at least one), and, optionally, table configuration.<br>
Column specification can be a simple string (name), or a dictionary<br>
(dictionaries are described below in main help output), necessarily <br>
including NAME attribute. <br><span>Examples:    //这里，有例子</span></p>
<p>Create a table with namespace=ns1 and table qualifier=t1<br>
hbase&gt; create 'ns1:t1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}</p>
<p>Create a table with namespace=default and table qualifier=t1<br>
hbase&gt; create 't1', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2'}, {NAME =&gt; 'f3'}<br>
hbase&gt; # The above in shorthand would be the following:<br>
hbase&gt; create 't1', 'f1', 'f2', 'f3'<br>
hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 1, TTL =&gt; 2592000, BLOCKCACHE =&gt; true}<br>
hbase&gt; create 't1', {NAME =&gt; 'f1', CONFIGURATION =&gt; {'hbase.hstore.blockingStoreFiles' =&gt; '10'}}<br><br>
Table configuration options can be put at the end.<br><span>Examples:   //<span>这里，有例子</span></span></p>
<p>hbase&gt; create 'ns1:t1', 'f1', SPLITS =&gt; ['10', '20', '30', '40']<br>
hbase&gt; create 't1', 'f1', SPLITS =&gt; ['10', '20', '30', '40']<br>
hbase&gt; create 't1', 'f1', SPLITS_FILE =&gt; 'splits.txt', OWNER =&gt; 'johndoe'<br>
hbase&gt; create 't1', {NAME =&gt; 'f1', VERSIONS =&gt; 5}, METADATA =&gt; { 'mykey' =&gt; 'myvalue' }<br>
hbase&gt; # Optionally pre-split the table into NUMREGIONS, using<br>
hbase&gt; # SPLITALGO ("HexStringSplit", "UniformSplit" or classname)<br>
hbase&gt; create 't1', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit'}<br>
hbase&gt; create 't1', 'f1', {NUMREGIONS =&gt; 15, SPLITALGO =&gt; 'HexStringSplit', CONFIGURATION =&gt; {'hbase.hregion.scan.loadColumnFamiliesOnDemand' =&gt; 'true'}}</p>
<p>You can also keep around a reference to the created table:</p>
<p>hbase&gt; t1 = create 't1', 'f1'</p>
<p>Which gives you a reference to the table named 't1', on which you can then<br>
call methods.</p>
<p><br>
hbase(main):004:0&gt;</p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012154744640-1166143921.png" alt="" style="border:0px;"></p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012155403593-1196676345.png" alt="" style="border:0px;"></p>
<p>[hadoop@weekend110 bin]$ jps<br>
2443 NameNode<br>
2970 NodeManager<br>
7515 Jps<br>
2539 DataNode<br>
2729 SecondaryNameNode<br>
2866 ResourceManager<br>
[hadoop@weekend110 bin]$ pwd<br>
/home/hadoop/app/hbase-0.96.2-hadoop2/bin<br>
[hadoop@weekend110 bin]$ ./start-hbase.sh <br>
starting master, logging to /home/hadoop/app/hbase-0.96.2-hadoop2/logs/hbase-hadoop-master-weekend110.out<br>
[hadoop@weekend110 bin]$ jps<br>
2443 NameNode<br>
7623 HMaster<br>
2970 NodeManager<br>
2539 DataNode<br>
2729 SecondaryNameNode<br>
2866 ResourceManager<br>
7686 Jps<br>
[hadoop@weekend110 bin]$ ./hbase shell<br>
2016-10-12 15:53:46,394 INFO [main] Configuration.deprecation: hadoop.native.lib is deprecated. Instead, use io.native.lib.available<br>
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.<br>
Type "exit&lt;RETURN&gt;" to leave the HBase Shell<br>
Version 0.96.2-hadoop2, r1581096, Mon Mar 24 16:03:18 PDT 2014</p>
<p>hbase(main):001:0&gt; list<br>
TABLE <br>
SLF4J: Class path contains multiple SLF4J bindings.<br>
SLF4J: Found binding in [jar:file:/home/hadoop/app/hbase-0.96.2-hadoop2/lib/slf4j-log4j12-1.6.4.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: Found binding in [jar:file:/home/hadoop/app/hadoop-2.4.1/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]<br>
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.<br>
0 row(s) in 2.8190 seconds</p>
<p>=&gt; []<br>
hbase(main):002:0&gt; <span style="color:rgb(255,0,0);">create 'mygirls', {NAME =&gt; 'base_info',VERSIONS =&gt; 3},{NAME =&gt; 'extra_info'}</span><br>
0 row(s) in 1.1080 seconds</p>
<p>=&gt; Hbase::Table - mygirls<br>
hbase(main):003:0&gt;</p>
<p> </p>
<p> </p>
<p><span> describe </span></p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012155546000-1963964340.png" alt="" style="border:0px;"></p>
<p>hbase(main):010:0&gt; <span style="color:rgb(255,0,0);">describe 'mygirls'</span><br>
DESCRIPTION ENABLED <br>
'mygirls', {NAME =&gt; 'base_info', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROW', true <br>
REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0 <br>
', TTL =&gt; '2147483647', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =&gt; '65536', IN_MEMOR <br>
Y =&gt; 'false', BLOCKCACHE =&gt; 'true'}, {NAME =&gt; 'extra_info', DATA_BLOCK_ENCODING =&gt; 'N <br>
ONE', BLOOMFILTER =&gt; 'ROW', REPLICATION_SCOPE =&gt; '0', VERSIONS =&gt; '1', COMPRESSION =&gt; <br>
'NONE', MIN_VERSIONS =&gt; '0', TTL =&gt; '2147483647', KEEP_DELETED_CELLS =&gt; 'false', BLO <br>
CKSIZE =&gt; '65536', IN_MEMORY =&gt; 'false', BLOCKCACHE =&gt; 'true'} <br>
1 row(s) in 0.0980 seconds</p>
<p>hbase(main):011:0&gt;</p>
<p> </p>
<p> </p>
<p><span>disable 和 drop  ，先得disable（下线），再才能drop（删掉）</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012154648187-1527485373.png" alt="" style="border:0px;"></p>
<p>hbase(main):002:0&gt; <span style="color:rgb(255,0,0);">disable 'mygirls'</span><br>
0 row(s) in 1.2030 seconds</p>
<p>hbase(main):003:0&gt; <span style="color:rgb(255,0,0);">drop 'mygirls'</span><br>
0 row(s) in 0.4270 seconds</p>
<p>hbase(main):004:0&gt;</p>
<p> </p>
<p> </p>
<p><span>put</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012155942046-726117467.png" alt="" style="border:0px;"></p>
<p>hbase(main):011:0&gt; put</p>
<p>ERROR: wrong number of arguments (0 for 4)</p>
<p>Here is some help for this command:<br>
Put a cell 'value' at specified table/row/column and optionally<br>
timestamp coordinates. To put a cell value into table 'ns1:t1' or 't1'<br>
at row 'r1' under column 'c1' marked with the time 'ts1', do:</p>
<p>hbase&gt; put 'ns1:t1', 'r1', 'c1', 'value', ts1   <span> //例子</span></p>
<p>The same commands also can be run on a table reference. Suppose you had a reference<br>
t to table 't1', the corresponding command would be:</p>
<p>hbase&gt; t.put 'r1', 'c1', 'value', ts1  <span>  //例子</span></p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012160605234-1673258651.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012160623875-440632633.png" alt="" style="border:0px;"></p>
<p>hbase(main):012:0&gt;<span style="color:rgb(255,0,0);"> put 'mygirls','0001','base_info:name','fengjie'</span><br>
0 row(s) in 0.3240 seconds</p>
<p>hbase(main):013:0&gt; <span style="color:rgb(255,0,0);">put 'mygirls','0001','base_info:age','18'</span><br>
0 row(s) in 0.0170 seconds</p>
<p>hbase(main):014:0&gt;<span style="color:rgb(255,0,0);"> put 'mygirls','0001','base_info:sex','jipinnvren'</span><br>
0 row(s) in 0.0130 seconds</p>
<p>hbase(main):015:0&gt; <span style="color:rgb(255,0,0);">put 'mygirls','0001','base_info:boyfriend','huangxiaoming'</span><br>
0 row(s) in 0.0590 seconds</p>
<p> </p>
<p> </p>
<p><span>get</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012160744453-873938285.png" alt="" style="border:0px;"></p>
<p>hbase(main):016:0&gt; <span style="color:rgb(255,0,0);">get</span></p>
<p>ERROR: wrong number of arguments (0 for 2)</p>
<p>Here is some help for this command:<br>
Get row or cell contents; pass table name, row, and optionally<br>
a dictionary of column(s), timestamp, timerange and versions. <span>Examples:    //例子</span></p>
<p>hbase&gt; get 'ns1:t1', 'r1'      <br>
hbase&gt; get 't1', 'r1'<br>
hbase&gt; get 't1', 'r1', {TIMERANGE =&gt; [ts1, ts2]}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'c1'}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; ['c1', 'c2', 'c3']}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'c1', TIMESTAMP =&gt; ts1}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'c1', TIMERANGE =&gt; [ts1, ts2], VERSIONS =&gt; 4}<br>
hbase&gt; get 't1', 'r1', {COLUMN =&gt; 'c1', TIMESTAMP =&gt; ts1, VERSIONS =&gt; 4}<br>
hbase&gt; get 't1', 'r1', {FILTER =&gt; "ValueFilter(=, 'binary:abc')"}<br>
hbase&gt; get 't1', 'r1', 'c1'<br>
hbase&gt; get 't1', 'r1', 'c1', 'c2'<br>
hbase&gt; get 't1', 'r1', ['c1', 'c2']</p>
<p>Besides the default 'toStringBinary' format, 'get' also supports custom formatting by<br>
column. A user can define a FORMATTER by adding it to the column name in the get<br>
specification. The FORMATTER can be stipulated:</p>
<p>1. either as a org.apache.hadoop.hbase.util.Bytes method name (e.g, toInt, toString)<br>
2. or as a custom class followed by method name: e.g. 'c(MyFormatterClass).format'.</p>
<p>Example formatting cf:qualifier1 and cf:qualifier2 both as Integers: <br>
hbase&gt; get 't1', 'r1' {COLUMN =&gt; ['cf:qualifier1:toInt',<br>
'cf:qualifier2:c(org.apache.hadoop.hbase.util.Bytes).toInt'] }</p>
<p>Note that you can specify a FORMATTER by column only (cf:qualifer). You cannot specify<br>
a FORMATTER for all columns of a column family.<br><br>
The same commands also can be run on a reference to a table (obtained via get_table or<br>
create_table). Suppose you had a reference t to table 't1', the corresponding commands<br>
would be:</p>
<p>hbase&gt; t.get 'r1'<br>
hbase&gt; t.get 'r1', {TIMERANGE =&gt; [ts1, ts2]}<br>
hbase&gt; t.get 'r1', {COLUMN =&gt; 'c1'}<br>
hbase&gt; t.get 'r1', {COLUMN =&gt; ['c1', 'c2', 'c3']}<br>
hbase&gt; t.get 'r1', {COLUMN =&gt; 'c1', TIMESTAMP =&gt; ts1}<br>
hbase&gt; t.get 'r1', {COLUMN =&gt; 'c1', TIMERANGE =&gt; [ts1, ts2], VERSIONS =&gt; 4}<br>
hbase&gt; t.get 'r1', {COLUMN =&gt; 'c1', TIMESTAMP =&gt; ts1, VERSIONS =&gt; 4}<br>
hbase&gt; t.get 'r1', {FILTER =&gt; "ValueFilter(=, 'binary:abc')"}<br>
hbase&gt; t.get 'r1', 'c1'<br>
hbase&gt; t.get 'r1', 'c1', 'c2'<br>
hbase&gt; t.get 'r1', ['c1', 'c2']</p>
<p><br>
hbase(main):017:0&gt;</p>
<p> </p>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012160900968-1155331624.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012160923781-1970592026.png" alt="" style="border:0px;"></p>
<p>hbase(main):017:0&gt; <span style="color:rgb(255,0,0);">get 'mygirls','0001'</span><br>
COLUMN CELL <br>
base_info:age timestamp=1476259587999, value=18 <br>
base_info:boyfriend timestamp=1476259597469, value=huangxiaoming <br>
base_info:name timestamp=1476259582217, value=fengjie <br>
base_info:sex timestamp=1476259593138, value=jipinnvren <br>
4 row(s) in 0.0320 seconds</p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012161056125-89491324.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012161130390-1307575874.png" alt="" style="border:0px;"></p>
<p>hbase(main):018:0&gt; <span style="color:rgb(255,0,0);">put 'mygirls','0001','base_info:name','fengbaobao'</span><br>
0 row(s) in 0.0140 seconds</p>
<p>hbase(main):019:0&gt; <span style="color:rgb(255,0,0);">get 'mygirls','0001'</span><br>
COLUMN CELL <br>
base_info:age timestamp=1476259587999, value=18 <br>
base_info:boyfriend timestamp=1476259597469, value=huangxiaoming <br>
base_info:name timestamp=1476259871197, value=fengbaobao <br>
base_info:sex timestamp=1476259593138, value=jipinnvren <br>
4 row(s) in 0.0480 seconds</p>
<p> </p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012161349312-439563823.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012161414046-1063779591.png" alt="" style="border:0px;"></p>
<p>hbase(main):020:0&gt; <span style="color:rgb(255,0,0);">get 'mygirls','0001',{COLUMN =&gt; 'base_info:name',VERSIONS =&gt; 10}</span><br>
COLUMN CELL <br>
base_info:name timestamp=1476259871197, value=fengbaobao <br>
base_info:name timestamp=1476259582217, value=fengjie <br>
2 row(s) in 0.0700 seconds</p>
<p>得到，2个版本。意思是，最多可得到10个版本</p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012161552250-983531251.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012161609218-1535811663.png" alt="" style="border:0px;"></p>
<p>hbase(main):021:0&gt;<span style="color:rgb(255,0,0);"> put 'mygirls','0001','base_info:name','fengfeng'</span><br>
0 row(s) in 0.0240 seconds</p>
<p>hbase(main):022:0&gt; <span style="color:rgb(255,0,0);">get 'mygirls','0001',{COLUMN =&gt; 'base_info:name',VERSIONS =&gt; 10}</span><br>
COLUMN CELL <br>
base_info:name timestamp=1476260199839, value=fengfeng <br>
base_info:name timestamp=1476259871197, value=fengbaobao <br>
base_info:name timestamp=1476259582217, value=fengjie <br>
3 row(s) in 0.0550 seconds</p>
<p> </p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012161659593-1573076264.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012161832046-696140962.png" alt="" style="border:0px;"></p>
<p>hbase(main):023:0&gt; put 'mygirls','0001','base_info:name','qinaidefeng'<br>
0 row(s) in 0.0160 seconds</p>
<p>hbase(main):024:0&gt; get 'mygirls','0001',{COLUMN =&gt; 'base_info:name',VERSIONS =&gt; 10}<br>
COLUMN CELL <br>
base_info:name timestamp=1476260274142, value=qinaidefeng <br>
base_info:name timestamp=1476260199839, value=fengfeng <br>
base_info:name timestamp=1476259871197, value=fengbaobao <br>
3 row(s) in 0.0400 seconds</p>
<p>只存，最近的3个版本。</p>
<p> </p>
<p> </p>
<p><span>scan</span></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012162119859-177779317.png" alt="" style="border:0px;"></p>
<p>hbase(main):025:0&gt; <span style="color:rgb(255,0,0);">scan</span></p>
<p>ERROR: wrong number of arguments (0 for 1)</p>
<p>Here is some help for this command:<br>
Scan a table; pass table name and optionally a dictionary of scanner<br>
specifications. Scanner specifications may include one or more of:<br>
TIMERANGE, FILTER, LIMIT, STARTROW, STOPROW, TIMESTAMP, MAXLENGTH,<br>
or COLUMNS, CACHE</p>
<p>If no columns are specified, all columns will be scanned.<br>
To scan all members of a column family, leave the qualifier empty as in<br>
'col_family:'.</p>
<p>The filter can be specified in two ways:<br>
1. Using a filterString - more information on this is available in the<br>
Filter Language document attached to the HBASE-4176 JIRA<br>
2. Using the entire package name of the filter.</p>
<p>Some examples:     <span>//例子</span></p>
<p>hbase&gt; scan 'hbase:meta'<br>
hbase&gt; scan 'hbase:meta', {COLUMNS =&gt; 'info:regioninfo'}<br>
hbase&gt; scan 'ns1:t1', {COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; 'xyz'}<br>
hbase&gt; scan 't1', {COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; 'xyz'}<br>
hbase&gt; scan 't1', {COLUMNS =&gt; 'c1', TIMERANGE =&gt; [1303668804, 1303668904]}<br>
hbase&gt; scan 't1', {FILTER =&gt; "(PrefixFilter ('row2') AND<br>
(QualifierFilter (&gt;=, 'binary:xyz'))) AND (TimestampsFilter ( 123, 456))"}<br>
hbase&gt; scan 't1', {FILTER =&gt;<br>
org.apache.hadoop.hbase.filter.ColumnPaginationFilter.new(1, 0)}</p>
<p>For experts, there is an additional option -- CACHE_BLOCKS -- which<br>
switches block caching for the scanner on (true) or off (false). By<br>
default it is enabled. Examples:</p>
<p>hbase&gt; scan 't1', {COLUMNS =&gt; ['c1', 'c2'], CACHE_BLOCKS =&gt; false}</p>
<p>Also for experts, there is an advanced option -- RAW -- which instructs the<br>
scanner to return all cells (including delete markers and uncollected deleted<br>
cells). This option cannot be combined with requesting specific COLUMNS.<br>
Disabled by default. Example:</p>
<p>hbase&gt; scan 't1', {RAW =&gt; true, VERSIONS =&gt; 10}</p>
<p>Besides the default 'toStringBinary' format, 'scan' supports custom formatting<br>
by column. A user can define a FORMATTER by adding it to the column name in<br>
the scan specification. The FORMATTER can be stipulated:</p>
<p>1. either as a org.apache.hadoop.hbase.util.Bytes method name (e.g, toInt, toString)<br>
2. or as a custom class followed by method name: e.g. 'c(MyFormatterClass).format'.</p>
<p>Example formatting cf:qualifier1 and cf:qualifier2 both as Integers: <br>
hbase&gt; scan 't1', {COLUMNS =&gt; ['cf:qualifier1:toInt',<br>
'cf:qualifier2:c(org.apache.hadoop.hbase.util.Bytes).toInt'] }</p>
<p>Note that you can specify a FORMATTER by column only (cf:qualifer). You cannot<br>
specify a FORMATTER for all columns of a column family.</p>
<p>Scan can also be used directly from a table, by first getting a reference to a<br>
table, like such:</p>
<p>hbase&gt; t = get_table 't'<br>
hbase&gt; t.scan</p>
<p>Note in the above situation, you can still provide all the filtering, columns,<br>
options, etc as described above.</p>
<p> </p>
<p>hbase(main):026:0&gt;</p>
<p> </p>
<p> </p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012162243812-2029572224.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012162259093-1270719718.png" alt="" style="border:0px;"></p>
<p><br>
hbase(main):026:0&gt; <span style="color:rgb(255,0,0);">scan 'mygirls'</span><br>
ROW COLUMN+CELL <br>
0001 column=base_info:age, timestamp=1476259587999, value=18 <br>
0001 column=base_info:boyfriend, timestamp=1476259597469, value=huangxiaoming <br>
0001 column=base_info:name, timestamp=1476260274142, value=qinaidefeng <br>
0001 column=base_info:sex, timestamp=1476259593138, value=jipinnvren <br>
1 row(s) in 0.1040 seconds</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012162604218-466844852.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012162722171-2271163.png" alt="" style="border:0px;"></p>
<p>hbase(main):027:0&gt; <span style="color:rgb(255,0,0);">scan 'mygirls',{RAW =&gt; true,VERSIONS =&gt; 10}</span><br>
ROW COLUMN+CELL <br>
0001 column=base_info:age, timestamp=1476259587999, value=18 <br>
0001 column=base_info:boyfriend, timestamp=1476259597469, value=huangxiaoming <br>
0001 column=base_info:name, timestamp=1476260274142, value=qinaidefeng <br>
0001 column=base_info:name, timestamp=1476260199839, value=fengfeng <br>
0001 column=base_info:name, timestamp=1476259871197, value=fengbaobao <br>
0001 column=base_info:name, timestamp=1476259582217, value=fengjie <br>
0001 column=base_info:sex, timestamp=1476259593138, value=jipinnvren <br>
1 row(s) in 0.0660 seconds</p>
<p>hbase(main):028:0&gt; <span style="color:rgb(255,0,0);">get 'mygirls','0001',{COLUMN =&gt; 'base_info:name',VERSIONS =&gt; 10,RAW =&gt; true}</span><br>
COLUMN CELL <br>
base_info:name timestamp=1476260274142, value=qinaidefeng <br>
base_info:name timestamp=1476260199839, value=fengfeng <br>
base_info:name timestamp=1476259871197, value=fengbaobao <br>
3 row(s) in 0.0170 seconds</p>
<p> </p>
<p> </p>
<p> </p>
<p>为什么，scan能把老版本都显示出来？<br>
答：原因是，你最新的操作，并没有真正刷到Hadoop文件里。</p>
<p>最新的操作，是在HLog里。</p>
<p>要等到，把这个HLog刷到MeroStore里或HFile里，才能把那些过期的数据给清除掉。</p>
<p>那多久才会刷呢?有个默认机制。    还有，立即把HBase停掉。</p>
<p> </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012163146593-717929933.jpg" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>其实啊，HBase Shell操作，在平常工作中，都不会这么操作。因为，不建议，很不好用，不方便。！！！</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012163543421-164430725.png" alt="" style="border:0px;"></p>
<p>hbase&gt; scan 't1', {FILTER =&gt; "(PrefixFilter ('row2') AND<br>
(QualifierFilter (&gt;=, 'binary:xyz'))) AND (TimestampsFilter ( 123, 456))"}<br>
hbase&gt; scan 't1', {FILTER =&gt;<br>
org.apache.hadoop.hbase.filter.ColumnPaginationFilter.new(1, 0)}</p>
<p> </p>
<p> </p>
<p> </p>
<p>更多，自行去玩玩。这里，不多赘述。</p>
<p>进入hbase命令行<br>
./hbase shell</p>
<p>显示hbase中的表<br>
list</p>
<p>创建user表，包含info、data两个列族<br>
create 'user', 'info1', 'data1'<br>
create 'user', {NAME =&gt; 'info', VERSIONS =&gt; '3'}</p>
<p>向user表中插入信息，row key为rk0001，列族info中添加name列标示符，值为zhangsan<br>
put 'user', 'rk0001', 'info:name', 'zhangsan'</p>
<p>向user表中插入信息，row key为rk0001，列族info中添加gender列标示符，值为female<br>
put 'user', 'rk0001', 'info:gender', 'female'</p>
<p>向user表中插入信息，row key为rk0001，列族info中添加age列标示符，值为20<br>
put 'user', 'rk0001', 'info:age', 20</p>
<p>向user表中插入信息，row key为rk0001，列族data中添加pic列标示符，值为picture<br>
put 'user', 'rk0001', 'data:pic', 'picture'</p>
<p>获取user表中row key为rk0001的所有信息<br>
get 'user', 'rk0001'</p>
<p>获取user表中row key为rk0001，info列族的所有信息<br>
get 'user', 'rk0001', 'info'</p>
<p>获取user表中row key为rk0001，info列族的name、age列标示符的信息<br>
get 'user', 'rk0001', 'info:name', 'info:age'</p>
<p>获取user表中row key为rk0001，info、data列族的信息<br>
get 'user', 'rk0001', 'info', 'data'<br>
get 'user', 'rk0001', {COLUMN =&gt; ['info', 'data']}</p>
<p>get 'user', 'rk0001', {COLUMN =&gt; ['info:name', 'data:pic']}</p>
<p>获取user表中row key为rk0001，列族为info，版本号最新5个的信息<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info', VERSIONS =&gt; 2}<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info:name', VERSIONS =&gt; 5}<br>
get 'user', 'rk0001', {COLUMN =&gt; 'info:name', VERSIONS =&gt; 5, TIMERANGE =&gt; [1392368783980, 1392380169184]}</p>
<p>获取user表中row key为rk0001，cell的值为zhangsan的信息<br>
get 'people', 'rk0001', {FILTER =&gt; "ValueFilter(=, 'binary:图片')"}</p>
<p>获取user表中row key为rk0001，列标示符中含有a的信息<br>
get 'people', 'rk0001', {FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}</p>
<p>put 'user', 'rk0002', 'info:name', 'fanbingbing'<br>
put 'user', 'rk0002', 'info:gender', 'female'<br>
put 'user', 'rk0002', 'info:nationality', '中国'<br>
get 'user', 'rk0002', {FILTER =&gt; "ValueFilter(=, 'binary:中国')"}</p>
<p><br>
查询user表中的所有信息<br>
scan 'user'</p>
<p>查询user表中列族为info的信息<br>
scan 'user', {COLUMNS =&gt; 'info'}<br>
scan 'user', {COLUMNS =&gt; 'info', RAW =&gt; true, VERSIONS =&gt; 5}<br>
scan 'persion', {COLUMNS =&gt; 'info', RAW =&gt; true, VERSIONS =&gt; 3}<br>
查询user表中列族为info和data的信息<br>
scan 'user', {COLUMNS =&gt; ['info', 'data']}<br>
scan 'user', {COLUMNS =&gt; ['info:name', 'data:pic']}</p>
<p><br>
查询user表中列族为info、列标示符为name的信息<br>
scan 'user', {COLUMNS =&gt; 'info:name'}</p>
<p>查询user表中列族为info、列标示符为name的信息,并且版本最新的5个<br>
scan 'user', {COLUMNS =&gt; 'info:name', VERSIONS =&gt; 5}</p>
<p>查询user表中列族为info和data且列标示符中含有a字符的信息<br>
scan 'user', {COLUMNS =&gt; ['info', 'data'], FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}</p>
<p>查询user表中列族为info，rk范围是[rk0001, rk0003)的数据<br>
scan 'people', {COLUMNS =&gt; 'info', STARTROW =&gt; 'rk0001', ENDROW =&gt; 'rk0003'}</p>
<p>查询user表中row key以rk字符开头的<br>
scan 'user',{FILTER=&gt;"PrefixFilter('rk')"}</p>
<p>查询user表中指定范围的数据<br>
scan 'user', {TIMERANGE =&gt; [1392368783980, 1392380169184]}</p>
<p>删除数据<br>
删除user表row key为rk0001，列标示符为info:name的数据<br>
delete 'people', 'rk0001', 'info:name'<br>
删除user表row key为rk0001，列标示符为info:name，timestamp为1392383705316的数据<br>
delete 'user', 'rk0001', 'info:name', 1392383705316</p>
<p><br>
清空user表中的数据<br>
truncate 'people'</p>
<p><br>
修改表结构<br>
首先停用user表（新版本不用）<br>
disable 'user'</p>
<p>添加两个列族f1和f2<br>
alter 'people', NAME =&gt; 'f1'<br>
alter 'user', NAME =&gt; 'f2'<br>
启用表<br>
enable 'user'</p>
<p><br>
###disable 'user'(新版本不用)<br>
删除一个列族：<br>
alter 'user', NAME =&gt; 'f1', METHOD =&gt; 'delete' 或 alter 'user', 'delete' =&gt; 'f1'</p>
<p>添加列族f1同时删除列族f2<br>
alter 'user', {NAME =&gt; 'f1'}, {NAME =&gt; 'f2', METHOD =&gt; 'delete'}</p>
<p>将user表的f1列族版本号改为5<br>
alter 'people', NAME =&gt; 'info', VERSIONS =&gt; 5<br>
启用表<br>
enable 'user'</p>
<p><br>
删除表<br>
disable 'user'<br>
drop 'user'</p>
<p><br>
get 'person', 'rk0001', {FILTER =&gt; "ValueFilter(=, 'binary:中国')"}<br>
get 'person', 'rk0001', {FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}<br>
scan 'person', {COLUMNS =&gt; 'info:name'}<br>
scan 'person', {COLUMNS =&gt; ['info', 'data'], FILTER =&gt; "(QualifierFilter(=,'substring:a'))"}<br>
scan 'person', {COLUMNS =&gt; 'info', STARTROW =&gt; 'rk0001', ENDROW =&gt; 'rk0003'}</p>
<p>scan 'person', {COLUMNS =&gt; 'info', STARTROW =&gt; '20140201', ENDROW =&gt; '20140301'}<br>
scan 'person', {COLUMNS =&gt; 'info:name', TIMERANGE =&gt; [1395978233636, 1395987769587]}<br>
delete 'person', 'rk0001', 'info:name'</p>
<p>alter 'person', NAME =&gt; 'ffff'<br>
alter 'person', NAME =&gt; 'info', VERSIONS =&gt; 10</p>
<p><br>
get 'user', 'rk0002', {COLUMN =&gt; ['info:name', 'data:pic']}</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p><span style="font-size:18pt;"><span> hbase的java api</span></span></p>
<p> 1、建立hbase工程</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012164854062-1927048506.png" alt="" style="border:0px;"></p>
<p>　　推荐http://hbase.apache.org/</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012165207781-2095827300.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p> </p>
<p><span style="font-size:18pt;"><span>.关于HBase的更多技术细节，强烈必多看</span></span></p>
<p>http://abloz.com/hbase/book.html</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012165237546-2087610051.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p> </p>
<p> 　2、　weekend110-hbase   -&gt;   Build Path   -&gt;  Configure Build Path </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012165705734-1257462875.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012165725890-5922282.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012165737937-1846934669.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012165753937-150068958.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012165929250-1641346287.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012165958609-741097361.png" alt="" style="border:0px;"></p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012170406812-580878835.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012170435718-1102453575.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012170905156-781382106.png" alt="" style="border:0px;"></p>
<p>　　我们是hadoop-2.4.1.jar，但是，hbase-0.96.2-hadoop2-bin.tar.gz自带的是，hadoop-2.2.0.jar。</p>
<p> </p>
<p><span style="color:rgb(255,0,0);">这里，我参考了《Hadoop 实战》，陆嘉恒老师编著的。P249页，</span></p>
<p><span style="color:rgb(255,0,0);">　　注意：安装Hadoop的时候，要注意HBase的版本。也就是说，需要注意Hadoop和HBase之间的版本关系，如果不匹配，很可能会影响HBase系统的稳定性。在HBase的lin目录下可以看到对应的Hadoop的JAR文件。默认情况下，HBase的lib文件夹下对应的Hadoop版本相对稳定。如果用户想要使用其他的Hadoop版本，那么需要将Hadoop系统安装目录hadoop-*.*.*-core.jar文件和hadoop-*.*.*-test.jar文件复制到HBase的lib文件夹下，以替换其他版本的Hadoop文件。</span></p>
<p> 　　<img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012170929203-530409210.png" alt="" style="border:0px;"></p>
<p> 　　</p>
<p>　　 这里，我们为了方便，直接把D:\SoftWare\hbase-0.96.2-hadoop2\lib的所有jar包，都弄进来。</p>
<p>也参考了网上一些博客资料说，不需这么多。此外，程序可能包含一些间接引用，以后再逐步逐个，下载，添加就是。复制粘贴到 hbase-0.96.2-hadoop2lib 里。</p>
<p> 去 <a href="http://mvnrepository.com/" rel="nofollow" style="color:rgb(52,104,164);">http://mvnrepository.com/</a> 。</p>
<p>　　参考我的博客</p>
<h1 style="font-size:28px;color:rgb(102,102,102);line-height:1.5;">
                         <a class="postTitle2" href="http://www.cnblogs.com/zlslch/p/5882567.html" rel="nofollow" style="text-decoration:none;color:rgb(52,104,164);">Eclipse下新建Maven项目、自动打依赖jar包</a></h1>
<p> 　　参考 : http://blog.csdn.net/hetianguang/article/details/51371713</p>
<p>　　　　     http://www.cnblogs.com/NicholasLee/archive/2012/09/13/2683432.html</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012171056796-1019723617.png" alt="" style="border:0px;"></p>
<p> <img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012171905343-1066173198.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012171924093-180920974.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012171935734-2126529741.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012172000812-1393115425.png" alt="" style="border:0px;"></p>
<p> </p>
<p>　　3、新建包cn.itcast.bigdata.hbase</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012172134265-343442481.png" alt="" style="border:0px;"></p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012172154687-207547964.png" alt="" style="border:0px;"></p>
<p> </p>
<p>　　4、新建类 HbaseDao.java </p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012172240484-516792488.png" alt="" style="border:0px;"></p>
<p> </p>
<p> </p>
<p>　　这里，我就以分布式集群的配置，附上代码。工作中，就是这么干的！</p>
<p>package cn.itcast.bigdata.hbase;</p>
<p>import java.io.IOException;<br>
import java.util.ArrayList;</p>
<p>import org.apache.hadoop.conf.Configuration;<br>
import org.apache.hadoop.hbase.HBaseConfiguration;<br>
import org.apache.hadoop.hbase.HColumnDescriptor;<br>
import org.apache.hadoop.hbase.HTableDescriptor;<br>
import org.apache.hadoop.hbase.TableName;<br>
import org.apache.hadoop.hbase.client.HBaseAdmin;<br>
import org.apache.hadoop.hbase.client.HTable;<br>
import org.apache.hadoop.hbase.client.Put;<br>
import org.apache.hadoop.hbase.util.Bytes;<br>
import org.junit.Test;</p>
<p>public class HbaseDao {</p>
<p><br>
@Test<br>
public void insertTest() throws Exception{<br><br>
　　Configuration conf = HBaseConfiguration.create();<br>
　　conf.set("hbase.zookeeper.quorum", "weekend05:2181,weekend06:2181,weekend07:2181");<br><br>
　　HTable nvshen = new HTable(conf, "nvshen");<br><br>
　　Put name = new Put(Bytes.toBytes("rk0001"));<br>
　　name.add(Bytes.toBytes("base_info"), Bytes.toBytes("name"), Bytes.toBytes("angelababy"));<br><br>
　　Put age = new Put(Bytes.toBytes("rk0001"));<br>
　　age.add(Bytes.toBytes("base_info"), Bytes.toBytes("age"), Bytes.toBytes(18));<br><br>
　　ArrayList&lt;Put&gt; puts = new ArrayList&lt;&gt;();<br>
　　puts.add(name);<br>
　　puts.add(age);<br><br>
　　nvshen.put(puts);<br><br>
}<br><br><br><br><br>
public static void main(String[] args) throws Exception {<br><br>
　　Configuration conf = HBaseConfiguration.create();<br>
　　conf.set("hbase.zookeeper.quorum", "weekend05:2181,weekend06:2181,weekend07:2181");<br><br>
　　HBaseAdmin admin = new HBaseAdmin(conf);<br><br>
　　TableName name = TableName.valueOf("nvshen");<br><br><br>
　　HTableDescriptor desc = new HTableDescriptor(name);<br><br><br>
　　HColumnDescriptor base_info = new HColumnDescriptor("base_info");<br>
　　HColumnDescriptor extra_info = new HColumnDescriptor("extra_info");<br>
　　base_info.setMaxVersions(5);<br><br>
　　desc.addFamily(base_info);<br>
　　desc.addFamily(extra_info);<br><br>
　　admin.createTable(desc);<br><br><br>
　　}<br><br><br><br>
}</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p>或者 HbaseDemo.java</p>
<p><img src="http://images2015.cnblogs.com/blog/855959/201610/855959-20161012172510046-142763331.png" alt="" style="border:0px;"></p>
<p>package cn.itcast.bigdata.hbase;</p>
<p>import java.io.IOException;<br>
import java.util.ArrayList;</p>
<p>import org.apache.hadoop.conf.Configuration;<br>
import org.apache.hadoop.hbase.HBaseConfiguration;<br>
import org.apache.hadoop.hbase.HColumnDescriptor;<br>
import org.apache.hadoop.hbase.HTableDescriptor;<br>
import org.apache.hadoop.hbase.TableName;<br>
import org.apache.hadoop.hbase.client.HBaseAdmin;<br>
import org.apache.hadoop.hbase.client.HTable;<br>
import org.apache.hadoop.hbase.client.Put;<br>
import org.apache.hadoop.hbase.util.Bytes;<br>
import org.junit.Test;</p>
<p>public class HbaseDao {</p>
<p><br>
　　@Test<br>
　　public void insertTest() throws Exception{<br><br>
　　　　Configuration conf = HBaseConfiguration.create();<br>
　　　　conf.set("hbase.zookeeper.quorum", "weekend05:2181,weekend06:2181,weekend07:2181");<br><br>
　　　　HTable nvshen = new HTable(conf, "nvshen");<br><br>
　　　　Put name = new Put(Bytes.toBytes("rk0001"));<br>
　　　　name.add(Bytes.toBytes("base_info"), Bytes.toBytes("name"), Bytes.toBytes("angelababy"));<br><br>
　　　　Put age = new Put(Bytes.toBytes("rk0001"));<br>
　　　　age.add(Bytes.toBytes("base_info"), Bytes.toBytes("age"), Bytes.toBytes(18));<br><br>
　　　　ArrayList&lt;Put&gt; puts = new ArrayList&lt;&gt;();<br>
　　　　puts.add(name);<br>
　　　　puts.add(age);<br><br>
　　　　nvshen.put(puts);<br><br>
}<br><br><br><br><br>
public static void main(String[] args) throws Exception {<br><br>
　　Configuration conf = HBaseConfiguration.create();<br>
　　conf.set("hbase.zookeeper.quorum", "weekend05:2181,weekend06:2181,weekend07:2181");<br><br>
　　HBaseAdmin admin = new HBaseAdmin(conf);<br><br>
　　TableName name = TableName.valueOf("nvshen");<br><br><br>
　　HTableDescriptor desc = new HTableDescriptor(name);<br><br><br>
　　HColumnDescriptor base_info = new HColumnDescriptor("base_info");<br>
　　HColumnDescriptor extra_info = new HColumnDescriptor("extra_info");<br>
　　base_info.setMaxVersions(5);<br><br>
　　desc.addFamily(base_info);<br>
　　desc.addFamily(extra_info);<br><br>
　　admin.createTable(desc);<br><br><br>
　　}<br><br><br><br></p>
<p>}</p>
<p><br></p>
<p>原文地址：http://www.cnblogs.com/zlslch/p/5951104.html</p>
</div>
            </div>
                </div>
---
layout:     post
title:      自制hadoop及hbase集群同步命令样例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/BLUE__YEAH/article/details/40950101				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、cp2slave-hadoop.sh</p>
<p>scp -r /home/hadoop/hadoop-2.2.0 hadoop@xiaoping:~/<br>
scp -r /home/hadoop/hadoop-2.2.0 hadoop@hadoop-01:~/<br>
scp -r /home/hadoop/hadoop-2.2.0 hadoop@hadoop-02:~/<br>
scp -r /home/hadoop/hadoop-2.2.0 hadoop@hadoop-03:~/<br></p>
<p><br></p>
<p>2、cp2slave-hbase.sh</p>
<p>scp -r /home/hadoop/hbase-0.96.2-hadoop2 hadoop@xiaoping:~/<br>
scp -r /home/hadoop/hbase-0.96.2-hadoop2 hadoop@hadoop-01:~/<br>
scp -r /home/hadoop/hbase-0.96.2-hadoop2 hadoop@hadoop-02:~/<br>
scp -r /home/hadoop/hbase-0.96.2-hadoop2 hadoop@hadoop-03:~/</p>
<p><br></p>
<p>3、cp2slave-zookeeper.sh</p>
<p>scp -r /home/hadoop/zookeeper hadoop@xiaoping:~/<br>
scp -r /home/hadoop/zookeeper hadoop@hadoop-01:~/<br>
scp -r /home/hadoop/zookeeper hadoop@hadoop-02:~/<br>
scp -r /home/hadoop/zookeeper hadoop@hadoop-03:~/<br><br>
cp /home/hadoop/zookeeper/myid-taotao /home/hadoop/zookeeper/myid<br>
ssh hadoop@xiaoping cp /home/hadoop/zookeeper/myid-xiaoping /home/hadoop/zookeeper/myid<br>
ssh hadoop@hadoop-01 cp /home/hadoop/zookeeper/myid-hadoop-01 /home/hadoop/zookeeper/myid<br>
ssh hadoop@hadoop-02 cp /home/hadoop/zookeeper/myid-hadoop-02 /home/hadoop/zookeeper/myid<br>
ssh hadoop@hadoop-03 cp /home/hadoop/zookeeper/myid-hadoop-03 /home/hadoop/zookeeper/myid<br></p>
<p><br></p>
<p>4、cpConf2slave-hadoop.sh</p>
<p>scp /home/hadoop/hadoop-2.2.0/etc/hadoop/slaves hadoop@xiaoping:~/hadoop-2.2.0/etc/hadoop/slaves<br>
scp /home/hadoop/hadoop-2.2.0/etc/hadoop/slaves hadoop@hadoop-01:~/hadoop-2.2.0/etc/hadoop/slaves<br>
scp /home/hadoop/hadoop-2.2.0/etc/hadoop/slaves hadoop@hadoop-02:~/hadoop-2.2.0/etc/hadoop/slaves<br>
scp /home/hadoop/hadoop-2.2.0/etc/hadoop/slaves hadoop@hadoop-03:~/hadoop-2.2.0/etc/hadoop/slaves<br><br>
scp /home/hadoop/hadoop-2.2.0/etc/hadoop/core-site.xml hadoop@xiaoping:~/hadoop-2.2.0/etc/hadoop/core-site.xml<br>
scp /home/hadoop/hadoop-2.2.0/etc/hadoop/core-site.xml hadoop@hadoop-01:~/hadoop-2.2.0/etc/hadoop/core-site.xml<br>
scp /home/hadoop/hadoop-2.2.0/etc/hadoop/core-site.xml hadoop@hadoop-02:~/hadoop-2.2.0/etc/hadoop/core-site.xml<br>
scp /home/hadoop/hadoop-2.2.0/etc/hadoop/core-site.xml hadoop@hadoop-03:~/hadoop-2.2.0/etc/hadoop/core-site.xml<br></p>
<p><br></p>
<p>5、cpConf2slave-hbase.sh</p>
<p>scp /home/hadoop/hbase-0.96.2-hadoop2/conf/hbase-env.sh hadoop@xiaoping:~/hbase-0.96.2-hadoop2/conf/hbase-env.sh<br>
scp /home/hadoop/hbase-0.96.2-hadoop2/conf/hbase-env.sh hadoop@hadoop-01:~/hbase-0.96.2-hadoop2/conf/hbase-env.sh<br>
scp /home/hadoop/hbase-0.96.2-hadoop2/conf/hbase-env.sh hadoop@hadoop-02:~/hbase-0.96.2-hadoop2/conf/hbase-env.sh<br>
scp /home/hadoop/hbase-0.96.2-hadoop2/conf/hbase-env.sh hadoop@hadoop-03:~/hbase-0.96.2-hadoop2/conf/hbase-env.sh<br><br><br>
scp /home/hadoop/hbase-0.96.2-hadoop2/conf/hbase-site.xml hadoop@xiaoping:~/hbase-0.96.2-hadoop2/conf/hbase-site.xml<br>
scp /home/hadoop/hbase-0.96.2-hadoop2/conf/hbase-site.xml hadoop@hadoop-01:~/hbase-0.96.2-hadoop2/conf/hbase-site.xml<br>
scp /home/hadoop/hbase-0.96.2-hadoop2/conf/hbase-site.xml hadoop@hadoop-02:~/hbase-0.96.2-hadoop2/conf/hbase-site.xml<br>
scp /home/hadoop/hbase-0.96.2-hadoop2/conf/hbase-site.xml hadoop@hadoop-03:~/hbase-0.96.2-hadoop2/conf/hbase-site.xml<br></p>
<p><br></p>
<p>6、format-datanode</p>
<p>ssh hadoop@xiaoping rm -rf ~/dfs<br>
ssh hadoop@xiaoping rm -rf ~/temp<br>
ssh hadoop@xiaoping rm -rf ~/tmp<br><br><br>
ssh hadoop@hadoop-01 rm -rf ~/dfs<br>
ssh hadoop@hadoop-01 rm -rf ~/temp<br>
ssh hadoop@hadoop-01 rm -rf ~/tmp<br><br><br>
ssh hadoop@hadoop-02 rm -rf ~/dfs<br>
ssh hadoop@hadoop-02 rm -rf ~/temp<br>
ssh hadoop@hadoop-02 rm -rf ~/tmp<br><br><br>
ssh hadoop@hadoop-03 rm -rf ~/dfs<br>
ssh hadoop@hadoop-03 rm -rf ~/temp<br>
ssh hadoop@hadoop-03 rm -rf ~/tmp  <br><br><br>
rm -rf /home/hadoop/dfs/data/*<br>
rm -rf /home/hadoop/dfs/name/*<br>
rm -rf /home/temp/*<br>
rm -rf /home/tmp/*<br><br><br>
scp -r /home/hadoop/dfs hadoop@xiaoping:~/<br>
scp -r /home/hadoop/dfs hadoop@hadoop-01:~/<br>
scp -r /home/hadoop/dfs hadoop@hadoop-02:~/<br>
scp -r /home/hadoop/dfs hadoop@hadoop-03:~/<br>
scp -r /home/hadoop/temp hadoop@xiaoping:~/<br>
scp -r /home/hadoop/temp hadoop@hadoop-01:~/<br>
scp -r /home/hadoop/temp hadoop@hadoop-02:~/<br>
scp -r /home/hadoop/temp hadoop@hadoop-03:~/<br>
scp -r /home/hadoop/tmp hadoop@xiaoping:~/<br>
scp -r /home/hadoop/tmp hadoop@hadoop-01:~/<br>
scp -r /home/hadoop/tmp hadoop@hadoop-02:~/<br>
scp -r /home/hadoop/tmp hadoop@hadoop-03:~/<br></p>
<p><br></p>
<p>7、start-all-zookeeper.sh</p>
<p>ssh hadoop@taotao /home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh start<br>
#/home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh start<br>
ssh hadoop@xiaoping /home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh start <br>
#ssh hadoop@hadoop-01 source /etc/profile;/home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh start-foreground <br>
ssh hadoop@hadoop-02 /home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh start<br>
ssh hadoop@hadoop-03 /home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh start<br></p>
<p><br></p>
<p>8.stop-all-zookeeper.sh</p>
<p>#/bin/sh -x<br>
/home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh stop<br>
ssh hadoop@xiaoping /home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh stop<br>
#ssh hadoop@hadoop-01 /home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh stop<br>
ssh hadoop@hadoop-02 /home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh stop<br>
ssh hadoop@hadoop-03 /home/hadoop/zookeeper/zookeeper-3.4.5/bin/zkServer.sh stop<br></p>
<p><br></p>
            </div>
                </div>
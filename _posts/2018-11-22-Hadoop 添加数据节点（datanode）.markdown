---
layout:     post
title:      Hadoop 添加数据节点（datanode）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-weight:bold;">前提条件：</span></p>
<p>安装jdk-6u18</p>
<p> </p>
<p><strong>实现目的：</strong></p>
<p>在hadoop集群中添加一个新增数据节点。</p>
<p> </p>
<p> </p>
<p><strong>1. 创建目录和用户</strong></p>
<p style="background:#D9D9D9;">mkdir -p /app/hadoop</p>
<p style="background:#D9D9D9;">groupadd hadoop</p>
<p style="background:#D9D9D9;">useradd licz -g hadoop -d /app/hadoop</p>
<p style="background:#D9D9D9;">chown licz:hadoop /app/hadoop</p>
<p style="background:#D9D9D9;">passwd licz</p>
<p>注：如果出现下面的问题</p>
<p>[root@dbserver22 ~]# su - licz<br>
-bash-3.2$ <br></p>
<p>解决办法：</p>
<p>cp -a /etc/skel/. /app/hadoop </p>
<p><br></p>
<p><strong>2. 修改环境变量</strong></p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ vi .bash_profile</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">PATH=$PATH:$HOME/bin</p>
<p style="background:#D9D9D9;">export LANG=zh_CN</p>
<p style="background:#D9D9D9;">export PATH</p>
<p style="background:#D9D9D9;">unset USERNAME</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">export HADOOP_HOME=/app/hadoop/hadoop-1.2.1</p>
<p style="background:#D9D9D9;">export JAVA_HOME=/usr/java/jdk1.6.0_18</p>
<p style="background:#D9D9D9;">export HIVE_HOME=/app/hadoop/hive-0.11.0</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">export PIG_HOME=/app/hadoop/pig-0.12.0</p>
<p style="background:#D9D9D9;">export PIG_CLASSPATH=/app/hadoop/pig-0.12.0/conf</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">PATH=$JAVA_HOME/bin:$PATH:$HOME/bin:$HADOOP_HOME/bin:$PIG_HOME/bin:$HIVE_HOME/bin</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">export PATH</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">export HADOOP_HOME_WARN_SUPPRESS=1</p>
<p> </p>
<p> </p>
<p> </p>
<p><strong>3. 修改host文件，添加服务器</strong></p>
<p style="background:#D9D9D9;">[root@server123 ~]# vi /etc/hosts</p>
<p style="background:#D9D9D9;">10.1.32.91             nticket1</p>
<p style="background:#D9D9D9;">10.1.32.93             nticket2</p>
<p style="background:#D9D9D9;">10.1.32.95             nticket3</p>
<p style="background:#D9D9D9;">10.1.5.123             server123</p>
<p> </p>
<p>同样在其它各节点都添加新的server123服务器</p>
<p> </p>
<p><strong>4. 配置ssh免密码连入</strong></p>
<p>步骤为：</p>
<p>-&gt;在新节点上生成自己的密钥</p>
<p>-&gt;把原集群中的密钥复制添加到新节点的密钥当中</p>
<p>-&gt;再把新节点上的新密钥复制（覆盖）到原集群中的新节点</p>
<p> </p>
<p>--首先，为了避免误操作，操作之前要先备份原集群的密钥文件</p>
<p style="background:#D9D9D9;">[licz@nticket1 .ssh]$ cp authorized_keys authorized_keys.bak</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ ssh-keygen -t rsa</p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ cat ~/.ssh/id_rsa.pub &gt;&gt;~/.ssh/authorized_keys</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ ssh nticket1 cat ~/.ssh/authorized_keys &gt;&gt; ~/.ssh/authorized_keys</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ scp ~/.ssh/authorized_keys nticket1:~/.ssh/authorized_keys</p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ ssh nticket1 date</p>
<p style="background:#D9D9D9;">2014年 02月 12日 星期三 11:31:08 CST</p>
<p style="background:#D9D9D9;">[licz@nticket1 .ssh]$ ssh server123 date</p>
<p style="background:#D9D9D9;">三  2月 1211:25:57 CST 2014</p>
<p> </p>
<p>--同样把新新密钥复制（覆盖）到原集群中的新节点</p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ scp ~/.ssh/authorized_keys nticket2:~/.ssh/authorized_keys</p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ scp ~/.ssh/authorized_keys nticket3:~/.ssh/authorized_keys</p>
<p> </p>
<p><strong>5. 修改hadoop配置文件</strong></p>
<p>--在各节点修改hadoop的配置文件</p>
<p style="background:#D9D9D9;">[licz@nticket1 conf]$ vi slaves </p>
<p style="background:#D9D9D9;">nticket2</p>
<p style="background:#D9D9D9;">nticket3</p>
<p style="background:#D9D9D9;"><strong>server123</strong></p>
<p> </p>
<p><strong>6. 安装hadoop</strong></p>
<p>--把集群中的hadoop复制到新节点</p>
<p style="background:#D9D9D9;">[licz@nticket2~]$ scp -r hadoop-1.2.1/server123:/app/hadoop</p>
<p> </p>
<p><strong>7. 在新节点上启动datanode和tasktracker </strong></p>
<p style="background:#D9D9D9;">[licz@server123~]$ <span style="color:#FF0000;">hadoop-daemon.sh start datanode</span></p>
<p style="background:#D9D9D9;">startingdatanode, logging to /app/hadoop/hadoop-1.2.1/libexec/../logs/hadoop-licz-datanode-server123.out</p>
<p style="background:#D9D9D9;">[licz@server123~]$ <span style="color:#FF0000;">hadoop-daemon.sh start tasktracker</span></p>
<p style="background:#D9D9D9;">startingtasktracker, logging to /app/hadoop/hadoop-1.2.1/libexec/../logs/hadoop-licz-tasktracker-server123.out</p>
<p style="background:#D9D9D9;"> </p>
<p style="background:#D9D9D9;">--测试安装成功</p>
<p style="background:#D9D9D9;">[licz@server123 ~]$ jps</p>
<p style="background:#D9D9D9;">18356 DataNode</p>
<p style="background:#D9D9D9;">18517 TaskTracker</p>
<p style="background:#D9D9D9;">18780 Jps</p>
<p style="background:#D9D9D9;"> </p>
<p> </p>
<p> </p>
<p><strong>8. 进行block块的均衡 </strong></p>
<p>--在hdfs-site.xml中增加设置balance的带宽，默认只有1M： </p>
<p style="background:#D9D9D9;">&lt;property&gt;</p>
<p style="background:#D9D9D9;">   &lt;name&gt;dfs.balance.bandwidthPerSec&lt;/name&gt; </p>
<p style="background:#D9D9D9;">    &lt;value&gt;10485760&lt;/value&gt; </p>
<p style="background:#D9D9D9;">    &lt;description&gt; </p>
<p style="background:#D9D9D9;">        Specifies the maximum bandwidth thateach datanode can utilize for the balancing purpose in term of the number ofbytes per second.</p>
<p style="background:#D9D9D9;">    &lt;/description&gt; </p>
<p style="background:#D9D9D9;">&lt;/property&gt;</p>
<p> </p>
<p>运行以下命令：</p>
<p style="background:#D9D9D9;">[licz@server123conf]$ <span style="color:#FF0000;">start-balancer.sh -threshold 5</span></p>
<p style="background:#D9D9D9;">startingbalancer, logging to /app/hadoop/hadoop-1.2.1/libexec/../logs/hadoop-licz-balancer-server123.out</p>
<p style="background:#D9D9D9;">Time Stamp               Iteration#  Bytes Already Moved  Bytes Left To Move  Bytes Being Moved<br>
2014-2-20 17:55:14                0                 0 KB            14.12 GB           14.06 GB<br></p>
<p> </p>
<p>--测试</p>
<p style="background:#D9D9D9;">[licz@server123~]$ hadoop dfs -ls /user/hive</p>
<p style="background:#D9D9D9;">Found 1 items</p>
<p style="background:#D9D9D9;">drwxr-xr-x   - licz supergroup          0 2014-02-10 11:25/user/hive/warehouse</p>
<p style="background:#D9D9D9;"><br></p>
<p style="background:#D9D9D9;">[licz@nticket1 ~]$ hadoop dfsadmin -report<br>
Configured Capacity: 2588293705728 (2.35 TB)<br>
Present Capacity: 2027166097408 (1.84 TB)<br>
DFS Remaining: 2026681536512 (1.84 TB)<br>
DFS Used: 484560896 (462.11 MB)<br>
DFS Used%: 0.02%<br>
Under replicated blocks: 9<br>
Blocks with corrupt replicas: 0<br>
Missing blocks: 0<br><br><br>
-------------------------------------------------<br>
Datanodes available: 3 (3 total, 0 dead)<br><br><br>
Name: 10.1.32.95:50010<br>
Decommission Status : Normal<br>
Configured Capacity: 1041225043968 (969.72 GB)<br>
DFS Used: 242110464 (230.89 MB)<br>
Non DFS Used: 102109831168 (95.1 GB)<br>
DFS Remaining: 938873102336(874.39 GB)<br>
DFS Used%: 0.02%<br>
DFS Remaining%: 90.17%<br>
Last contact: Fri Feb 14 09:49:02 CST 2014<br><br><br><br><br>
Name: 10.1.32.93:50010<br>
Decommission Status : Normal<br>
Configured Capacity: 1041225043968 (969.72 GB)<br>
DFS Used: 242143232 (230.93 MB)<br>
Non DFS Used: 57774628864 (53.81 GB)<br>
DFS Remaining: 983208271872(915.68 GB)<br>
DFS Used%: 0.02%<br>
DFS Remaining%: 94.43%<br>
Last contact: Fri Feb 14 09:49:02 CST 2014<br><br><br><br><br>
Name: 10.1.5.123:50010<br>
Decommission Status : Normal<br>
Configured Capacity: 505843617792 (471.1 GB)<br>
DFS Used: 307200 (300 KB)<br>
Non DFS Used: 401243148288 (373.69 GB)<br>
DFS Remaining: 104600162304(97.42 GB)<br>
DFS Used%: 0%<br>
DFS Remaining%: 20.68%<br>
Last contact: Fri Feb 14 09:49:03 CST 2014<br></p>
<p> </p>
            </div>
                </div>
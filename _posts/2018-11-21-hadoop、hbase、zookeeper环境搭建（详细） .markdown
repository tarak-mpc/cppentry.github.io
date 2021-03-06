---
layout:     post
title:      hadoop、hbase、zookeeper环境搭建（详细） 
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div id="blog_text" class="cnt">
<p>一 机器<br>192.168.0.203 hd203： hadoop namenode &amp; hbase HMaster<br>192.168.0.204 hd204： hadoop datanode &amp; hbase HRegionServer &amp; zookeeper<br>192.168.0.205 hd205： hadoop datanode &amp; hbase HRegionServer &amp; zookeeper<br>192.168.0.206 hd206： hadoop datanode &amp; hbase HRegionServer &amp; zookeeper<br>192.168.0.202 hd202： hadoop second namenode &amp; hbase HMaster<br>共 5 台机器 (生产环境需要把zookeeper单独安装)<br><br>二 操作系统和软件版本<br>1 操作系统:CentOS release 5.6 (Final)x64，关闭防火墙<br>2 相关软件:<br>jdk-6u24-linux-x64.bin<br>hadoop-0.20.2-cdh3u0.tar.gz<br>hbase-0.90.1-cdh3u0.tar.gz<br>zookeeper-3.3.3-cdh3u0.tar.gz<br><br>三 安装<br><br>1 安装jdk (所有机器)<br>mkdir /usr/java<br>mv jdk-6u24-linux-x64.bin /usr/java<br>chmod 744 jdk-6u24-linux-x64.bin<br>./jdk-6u24-linux-x64.bin<br>ln -s jdk1.6.0_24 default<br><br>2 建立集群平台用户 (所有机器)<br>useradd cbcloud<br>passwd cbcloud 密码设为123456<br><br><br>3 编辑/etc/hosts (所有机器)<br>192.168.0.203 hd203<br>192.168.0.204 hd204<br>192.168.0.205 hd205<br>192.168.0.206 hd206<br>192.168.0.202 hd202<br><br>4 hd203登录集群所有机器免密码设置 (hd203)<br>su - cbcloud<br>ssh登录本机也要免密码 如下设置<br><br> $ ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa<br> $ cat ~/.ssh/id_dsa.pub &gt;&gt; ~/.ssh/authorized_keys<br><br>将hd203上的id_dsa.pub 文件追加到所有机器的cbcloud用户的authorized_keys文件内<br>细节不在详述<br>ssh 所有机器 第一次需要确认一下<br><br>5 安装hadoop<br>5.1 建立目录 (所有机器)<br>mkdir /home/cbcloud/hdtmp<br>mkdir /home/cbcloud/hddata<br>mkdir /home/cbcloud/hdconf<br>chown -R cbcloud:cbcloud 以上目录<br>tar zxvf hadoop-0.20.2-cdh3u0.tar.gz -C /home/cbcloud<br>cd /home/cbcloud<br>mv hadoop-0.20.2-cdh3u0 hadoop<br>chown -R cbcloud:cbcloud hadoop/<br><br><br>5.2 配置环境变量 vi /etc/profile (所有机器)<br>export JAVA_HOME=/usr/java/default<br>export CLASSPATH=$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib<br>export PATH=$JAVA_HOME/bin:$JAVA_HOME/lib:$JAVA_HOME/jre/bin:$PATH:$HOME/bin<br>export HADOOP_HOME=/home/cbcloud/hadoop<br>export HADOOP_CONF_DIR=/home/cbcloud/hdconf<br>export PATH=$PATH:$HADOOP_HOME/bin<br><br>把hadoop的配置文件目录从源目录拿出来，方便以后升级hadoop<br>mv hadoop的conf目录内文件到/home/cbcloud/hdconf内<br><br>5.3 编辑 hadoop 配置文件 core-site.xml<br>加入<br>&lt;configuration&gt;<br>&lt;property&gt;<br>&lt;name&gt;fs.default.name&lt;/name&gt;<br>&lt;value&gt;hdfs://hd203:9000&lt;/value&gt;<br>&lt;/property&gt;<br>&lt;property&gt;<br>  &lt;name&gt;fs.checkpoint.dir&lt;/name&gt;<br>  &lt;value&gt;/home/cbcloud/hdtmp/dfs/namesecondary&lt;/value&gt;<br>  &lt;description&gt;Determines where on the local filesystem the DFS secondary<br>      name node should store the temporary images to merge.<br>      If this is a comma-delimited list of directories then the image is<br>      replicated in all of the directories for redundancy.<br>  &lt;/description&gt;<br>&lt;/property&gt;<br>&lt;property&gt;<br>  &lt;name&gt;fs.checkpoint.period&lt;/name&gt;<br>  &lt;value&gt;60&lt;/value&gt;<br>  &lt;description&gt;Determines where on the local filesystem the DFS secondary<br>      name node should store the temporary images to merge.<br>      If this is a comma-delimited list of directories then the image is<br>      replicated in all of the directories for redundancy.<br>  &lt;/description&gt;<br>&lt;/property&gt;<br><br>5.4 编辑hdfs-site.xml<br>加入<br>&lt;property&gt;<br>       &lt;name&gt;dfs.replication&lt;/name&gt;<br>       &lt;value&gt;3&lt;/value&gt;<br>    &lt;/property&gt;<br><br>&lt;property&gt;<br>       &lt;name&gt;dfs.data.dir&lt;/name&gt;<br>       &lt;value&gt;/home/cbcloud/hddata&lt;/value&gt;<br>    &lt;/property&gt;<br><br>    &lt;property&gt;<br>       &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;<br>       &lt;value&gt;/home/cbcloud/hdtmp/&lt;/value&gt;<br>    &lt;/property&gt;<br><br>&lt;property&gt;<br>    &lt;name&gt;dfs.balance.bandwidthPerSec&lt;/name&gt;<br>    &lt;value&gt;10485760&lt;/value&gt;<br>&lt;/property&gt;<br>&lt;property&gt;<br>    &lt;name&gt;dfs.hosts.exclude&lt;/name&gt;<br>    &lt;value&gt;/home/cbcloud/hdconf/excludes&lt;/value&gt;<br>    &lt;final&gt;true&lt;/final&gt;<br>&lt;/property&gt;<br><br>5.5 编辑mapred-site.xml<br>加入<br>&lt;property&gt;<br>        &lt;name&gt;mapred.job.tracker&lt;/name&gt;<br>        &lt;value&gt;hd203:9001&lt;/value&gt;<br>    &lt;/property&gt;<br><br>5.6 编辑 hadoop-env.sh<br>export JAVA_HOME=/usr/java/default<br><br>5.7  编辑masters 该文件指定 secondary name 机器，<br>加入<br>hd202<br>编辑 slaves<br>加入<br>hd204<br>hd205<br>hd206<br><br>5.8 拷贝hd203的hadoop和hdconf到所有机器<br># scp -r /home/cbcloud/hadoop cbcloud@hd204:/home/cbcloud<br># scp -r /home/cbcloud/hdconf cbcloud@hd204:/home/cbcloud<br><br>完成后，在hd203 格式化hadoop文件系统 <br>执行<br> hadoop namenode -format<br>启动<br> start-all.sh <br>查看集群内datanode的机器 <br>执行jps<br>5764 Jps<br>18142 DataNode<br>18290 TaskTracker<br>看到以上结果 说明启动正确<br>web方式<br>http://hd203:50070/dfshealth.jsp <br>注意 本地PC hosts文件也要配置<br>192.168.0.203 hd203<br>192.168.0.204 hd204<br>192.168.0.205 hd205<br>192.168.0.206 hd206<br>192.168.0.202 hd202<br>web方式可以查看集群状态和job状态等，至此hadoop安装完毕<br><br>6 安装zookeeper (hd203)<br>tar zxvf zookeeper-3.3.3-cdh3u0.tar.gz -C /home/cbcloud<br>在hd204-hd206上<br>mkdir /home/cbcloud/zookeeperdata<br><br>chown -R cbcloud:cbcloud /home/cbcloud/zookeeperdata<br>chown -R cbcloud:cbcloud /home/cbcloud/zookeeper-3.3.3-cdh3u0<br><br>编辑 /home/cbcloud/zookeeper-3.3.3-cdh3u0/conf/zoo.cfg <br><br># The number of milliseconds of each tick<br>tickTime=2000<br># The number of ticks that the initial<br># synchronization phase can take<br>initLimit=10<br># The number of ticks that can pass between<br># sending a request and getting an acknowledgement<br>syncLimit=5<br># the directory where the snapshot is stored.<br>dataDir=/home/cbcloud/zookeeperdata<br># the port at which the clients will connect<br>clientPort=2181<br>server.1=hd204:2888:3888<br>server.2=hd205:2888:3888<br>server.3=hd206:2888:3888<br><br>scp hd203的zookeeper到hd204,hd205，hd206<br># scp -r /home/cbcloud/zookeeper-3.3.3-cdh3u0/ cbcloud@hd205:/home/cbcloud/ <br>在hd204-206 的/home/cbcloud/zookeeperdata目录touch myid文件，<br>内容分别为1,2,3 和server编号一致 chown   cbcloud:cbcloud myid<br><br>启动zookeeper，在hd204-206上bin目录下 执行<br># zkServer.sh start<br>启动后 通过<br># zkServer.sh status<br>查看状态 注意 在centos5.6上 执行这个会报错<br>Error contacting service. It is probably not running.<br>通过查看脚本 是因为<br>echo stat | nc -q 1 localhost<br>nc版本不同，没有-q的参数，更改脚本去掉-q 1 即可<br>另外 可以通过<br>echo stat | nc   localhost 2181来查看状态<br><br>7 安装hbase<br>7.1 建立目录 (所有机器)<br>mkdir /home/cbcloud/hbconf<br>chown -R cbcloud:cbcloud /home/cbcloud/hbconf<br>tar zxvf hbase-0.90.1-cdh3u0.tar.gz -C /home/cbcloud<br>cd /home/cbcloud<br>mv hbase-0.90.1-cdh3u0  hbase<br>chown -R cbcloud:cbcloud hbase/<br><br>7.2 配置环境变量 <br>vi /etc/profile (所有机器) 追加如下内容<br>export HBASE_CONF_DIR=/home/cbcloud/hbconf<br>export HBASE_HOME=/home/hadoop/hbase<br><br>把hbase的配置文件目录从源目录拿出来，方便以后升级hbase<br>mv hbase的conf目录内文件到/home/cbcloud/hbconf内<br><br>7.3 编辑 hbase-env.sh<br>export HBASE_OPTS="$HBASE_OPTS -XX:+HeapDumpOnOutOfMemoryError -XX:+UseConcMarkSweepGC -XX:+CMSIncrementalMode"<br>export JAVA_HOME=/usr/java/default<br>export HBASE_MANAGES_ZK=false<br>export HBASE_HOME=/home/cbcloud/hbase<br>export HADOOP_HOME=/home/cbcloud/hadoop<br><br>7.4 编辑hbase-site.xml<br>加入<br>&lt;property&gt;<br> &lt;name&gt;hbase.rootdir&lt;/name&gt;<br> &lt;value&gt;hdfs://hd203:9000/hbase&lt;/value&gt;<br>&lt;/property&gt;<br>&lt;property&gt;<br> &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;<br> &lt;value&gt;true&lt;/value&gt;<br>&lt;/property&gt;<br>&lt;property&gt;<br>&lt;name&gt;hbase.master&lt;/name&gt;<br>&lt;value&gt;hd203:60000&lt;/value&gt;<br>&lt;/property&gt;<br> &lt;property&gt;<br> &lt;name&gt;hbase.master.port&lt;/name&gt;<br> &lt;value&gt;60000&lt;/value&gt;<br> &lt;description&gt;The port master should bind to.&lt;/description&gt;<br> &lt;/property&gt;<br> <br> &lt;property&gt;<br>   &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;<br>   &lt;value&gt;hd204,hd205,hd206&lt;/value&gt;<br> &lt;/property&gt;<br><br>7.5 编辑regionservers<br>加入<br>hd204<br>hd205<br>hd206<br><br>scp hd203 的hbase到hd204-206,202<br># scp -r /home/cbcloud/hbase/ cbcloud@hd204:/home/cbcloud<br># scp -r /home/cbcloud/hbconf/ cbcloud@hd204:/home/cbcloud<br><br>su - cbcloud<br>启动hbase<br>在hd203上hbase的bin目录执行<br>$ ./start-hbase.sh<br>启动hbase的第二个HMaster<br>在202上执行<br>$ ./hbase-daemon.sh start master<br><br>查看Master:http://hd203:60010/master.jsp <br><br>此时查看datanode 服务器 jps<br>5764 Jps<br>32091 HRegionServer<br>18142 DataNode<br>11221 QuorumPeerMain <br>18290 TaskTracker<br>以上结果说明启动正常<br><br>至此 hadoop+zookeeper+hbase安装完成<br>启动顺序<br>1.hadoop<br>2.zookeeper<br>3.hbase<br>4.第二个HMaster<br><br>停止顺序<br>1.第二个HMaster, kill-9删除<br>2.hbase<br>3.zookeeper<br>4.hadoop<br>注意 一定要按顺序停止，<br>如果先停zookeeper再停hbase的话，基本停不下来(自己测试结果)</p>
<p>接下来的文章 将对集群的使用进行发布</p>
<p> </p>
<p><a href="http://zhli986-yahoo-cn.iteye.com/blog/1204199" rel="nofollow">http://zhli986-yahoo-cn.iteye.com/blog/1204199</a></p>
<p> </p>
<p>===========================================================</p>
<p>并且已经安装成功Hadoop-0.20.2及以上版本。</p>
<p>安装包准备</p>
<p>需要安装包：</p>
<p>zookeeper-3.3.2.tar.gz（stable版本）</p>
<p>hbase-0.20.2.tar.gz（stable版本）</p>
<p>安装步骤</p>
<p>安装和配置ZooKeeper</p>
<p>HBase从0.20.0开始，需要首先安装ZooKeeper。从apache上下载zookeeper-3.2.1.tar.gz（Stable版本），解压到/home/hdfs/目录下。 </p>
<p><br>（1）在namenode节点新建zookeeper目录，在该目录下新建myid文件。</p>
<p>（2）在zookeeper-3.2.1/conf目录下，拷贝zoo_sample.cfg为zoo.cfg。在zoo.cfg中将dataDir改为/home/hdfs/zookeeper，在文件末位添加所有的主机：</p>
<p>server.1=10.192.1.1:2888:3888</p>
<p>server.2=10.192.1.2:2888:3888</p>
<p>server.3=10.192.1.3:2888:3888</p>
<p> </p>
<p>（3）用scp命令将namenode节点的的/home/hdfs/ zookeeper-3.2.1和/home/hdfs/ zookeeper拷贝到其余所有主机的/home/hdfs目录下。</p>
<p>（4）参照zoo.cfg中的配置，在各主机myid文件中写入各自的编号。如：10.192.1.1入1,10.192.1.2写入2</p>
<p>（5）在所有节点上执行bin/zkServer.sh start，分别启动。</p>
<p>执行bin/zkCli.sh -server xxx.xxx.xxx.xxx:2181，检查指定服务器是否成功启动。</p>
<p><br>vi /etc/profile<br>export HBASE_HOME=/hadoop/hbase<br>export PATH=$PATH:$HBASE_HOME/bin</p>
<p>export HADOOP_HOME=/hadoop/hadoop<br>export PATH=$PATH:$HADOOP_HOME/bin</p>
<p> </p>
<p><br>安装和配置HBase</p>
<p>tar -zxvf hbase-0.20.6.tar.gz</p>
<p>cd hbase-0.20.6</p>
<p>mv * /hadoop/hbase</p>
<p>下载HBase0.20.1版本，解压到namenode节点的/home/hdfs目录下。</p>
<p>配置说明</p>
<p>（1）系统所有配置项的默认设置在hbase-default.xml中查看，如果需要修改配置项的值，在hbase-site.xml中添加配置项。</p>
<p>在分布式模式下安装HBase，需要添加的最基本的配置项如下：</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p>&lt;value&gt;hdfs://hadoop2-namenode:9000/hbase&lt;/value&gt;</p>
<p>&lt;description&gt;The directory shared by region servers.&lt;/description&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</p>
<p>&lt;value&gt;true&lt;/value&gt;</p>
<p>&lt;description&gt;The mode the cluster will be in. Possible values are</p>
<p>false: standalone and pseudo-distributed setups with managed Zookeeper</p>
<p>true: fully-distributed with unmanaged Zookeeper Quorum (see hbase-env.sh)</p>
<p>&lt;/description&gt;</p>
<p>&lt;/property&gt;<br>&lt;property&gt;<br>      &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;<br>      &lt;value&gt;/hadoop/zookeeper&lt;/value&gt;<br>      &lt;description&gt;Property from ZooKeeper's config zoo.cfg.<br>      The directory where the snapshot is stored.<br>      &lt;/description&gt;<br>    &lt;/property&gt;<br> &lt;property&gt;<br>      &lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;<br>      &lt;value&gt;2222&lt;/value&gt;<br>      &lt;description&gt;Property from ZooKeeper's config zoo.cfg.<br>      The port at which the clients will connect.<br>      &lt;/description&gt;<br>    &lt;/property&gt;</p>
<p>&lt;property&gt;<br>      &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;<br>      &lt;value&gt;hadoop2-namenode,hadoop2-datanode1,hadoop2-datanode2&lt;/value&gt;<br>      &lt;description&gt;Comma separated list of servers in the ZooKeeper Quorum.<br>      For example, "host1.mydomain.com,host2.mydomain.com,host3.mydomain.com".<br>      By default this is set to localhost for local and pseudo-distributed modes<br>      of operation. For a fully-distributed setup, this should be set to a full<br>      list of ZooKeeper quorum servers. If HBASE_MANAGES_ZK is set in hbase-env.sh<br>      this is the list of servers which we will start/stop ZooKeeper on.<br>      &lt;/description&gt;<br>    &lt;/property&gt;</p>
<p><br> </p>
<p><br>（2）在conf/hbase-env.sh中修改添加配置项：<br>export JAVA_HOME=/usr/java/jdk1.6.0_22</p>
<p> </p>
<p> export HBASE_MANAGES_ZK=true</p>
<p><br> <br>export HADOOP_CONF_DIR=/hadoop/hadoop-config</p>
<p><br>HADOOP_CLASSPATH 要設hbase 的環境，而HBASE_CLASSPATH要設hadoop的環境，有了這行可以解決編譯hbase 程式時出現run time error</p>
<p>并把~/hadoop-0.20.1/conf/hdfs-site.xml拷贝至~/hbase-3.2.1/conf/目录下。</p>
<p>（3）将ZooKeeper的配置文件zoo.cfg添加到HBase所有主机的CLASSPATH中。</p>
<p>（4）在conf/regionservers中添加hadoop-0.20.1/conf/slaves中所有的datanode节点。<br>hadoop2-datanode1<br>hadoop2-datanode2</p>
<p>启动</p>
<p>Hadoop、ZooKeeper和HBase之间应该按照顺序启动和关闭：启动Hadoop—&gt;启动ZooKeeper集群—&gt;启动HBase—&gt;停止HBase—&gt;停止ZooKeeper集</p>
<p>群—&gt;停止Hadoop。</p>
<p>在namenode节点执行bin/hbase-daemon.sh，启动master。执行bin/start-hbase.sh和bin/stop-hbase.sh 脚本启动和停止HBase服务。</p>
<p>/hadoop/hbase/bin/hbase-daemon.sh start master<br>/hadoop/hbase/bin/hbase-daemon.sh stop master<br>/hadoop/hbase/bin/start-hbase.sh<br>/hadoop/hbase/bin/stop-hbase.sh</p>
<p><br>/hadoop/hbase/bin/hbase shell</p>
<p> </p>
<p>本篇文章来源于 Linux公社网站(<a href="http://www.linuxidc.com" rel="nofollow">www.linuxidc.com</a>)  原文链接：<a href="http://www.linuxidc.com/Linux/2012-01/52223.htm" rel="nofollow">http://www.linuxidc.com/Linux/2012-01/52223.htm</a></p>
<p> </p>
<p>=============================================================</p>
</div>            </div>
                </div>
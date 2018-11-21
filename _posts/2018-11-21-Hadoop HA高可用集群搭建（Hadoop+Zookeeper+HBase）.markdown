---
layout:     post
title:      Hadoop HA高可用集群搭建（Hadoop+Zookeeper+HBase）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、服务器环境 <br>
主机名 IP  用户名 密码  安装目录 <br>
master188   192.168.29.188  hadoop  hadoop  /home/hadoop/ <br>
master189   192.168.29.189  hadoop  hadoop  /home/hadoop/ <br>
slave190    192.168.29.190  hadoop  hadoop  /home/hadoop/ <br>
二、集群规划 <br>
master188   master189   slave190 <br>
NameNode    NameNode <br>
DataNode    DataNode    DataNode <br>
Zookeeper   Zookeeper   Zookeeper <br>
ResourceManager ResourceManager  <br>
NodeManager NodeManager NodeManager <br>
HMaster HMasetr  <br>
RegionServer    RegionServer    RegionServer <br>
JDK、Hadoop、HBase版本兼容性(NS:不兼容；NT：未测试；Y：兼容)： <br>
HBase Version   JDK 6   JDK 7   JDK 8 <br>
2.0 X   X   Y <br>
1.3 X   Y   Y <br>
1.2 X   Y   Y <br>
1.1 X   Y   NT <br>
1.0 X   Y   NT <br>
0.98    Y   Y   NT <br>
0.94    Y   Y   N <br>
Hadoop\HBase    HBase-1.1.x HBase-1.2.x HBase-1.3.x HBase-2.0.x <br>
Hadoop-2.0.x-alpha  X   X   X   X <br>
Hadoop-2.1.0-beta   X   X   X   X <br>
Hadoop-2.2.0    NT  X   X   X <br>
Hadoop-2.3.x    NT  X   X   X <br>
Hadoop-2.4.x    S   S   S   X <br>
Hadoop-2.5.x    S   S   S   X <br>
Hadoop-2.6.0    X   X   X   X <br>
Hadoop-2.6.1+   NT  S   S   S <br>
Hadoop-2.7.0    X   X   X   X <br>
Hadoop-2.7.1+   NT  S   S   S <br>
Hadoop-2.8.0    X   X   X   X <br>
Hadoop-2.8.1        X   X   X <br>
Hadoop-3.0.0-alphax NT  NT  NT  NT <br>
考虑到HBase与JDK、Hadoop各版本兼容性，我们采用的组件版本如下：</p>

<p>组件  版本 <br>
JDK 1.8 <br>
Hadoop  2.6.5 <br>
Zookeeper   3.4.11 <br>
HBase   1.3.1 <br>
三、安装配置Zookeeper <br>
1、下载及安装 <br>
下载地址：<a href="http://mirrors.hust.edu.cn/apache/zookeeper/zookeeper-3.4.11/zookeeper-3.4.11.tar.gz" rel="nofollow">http://mirrors.hust.edu.cn/apache/zookeeper/zookeeper-3.4.11/zookeeper-3.4.11.tar.gz</a></p>

<p>在master188机器上，下载后解压到/home/hadoop/目录下：</p>

<p>tar -zxvf zookeeper-3.4.11.tar.gz -C /home/hadoop/ <br>
1 <br>
2、拷贝 zoo_sample.cfg <br>
进入zookeeper的conf目录，拷贝zoo_sample.cfg并重命名为zoo.cfg ：</p>

<p>cd zookeeper-3.4.11/conf/</p>

<p>cp zoo_sample.cfg zoo.cfg</p>

<p>1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
3、修改 zoo.cfg <br>
vi zoo.cfg <br>
1 <br>
修改如下，若原文件没有dataDir则直接添加：</p>

<p>dataDir=/home/hadoop/zookeeper-3.4.11/data/zkData</p>

<p>//在最后添加，指定zookeeper集群主机及端口，机器数必须为奇数 <br>
server.1=master188:2888:3888 <br>
server.2=master189:2888:3888 <br>
server.3=slave190:2888:3888 <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
4、创建并编辑myid <br>
//在zookeeper根目录下创建zoo.cfg中配置的目录 <br>
mkdir data/zkData/ -p</p>

<p>//创建并编辑文件 <br>
vi myid</p>

<p>//输入1，即表示当前机器为在zoo.cfg中指定的server.1 <br>
1</p>

<p>//保存退出 <br>
:wq <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
9 <br>
10 <br>
11 <br>
5、拷贝zookeeper到其他机器 <br>
上述操作是在master188机器上进行的，要将zookeeper拷贝到其他zookeeper集群机器上：</p>

<p>cd /home/hadoop</p>

<p>scp -r zookeeper-3.4.11/ hadoop@master189:/home/hadoop/</p>

<p>scp -r zookeeper-3.4.11/ hadoop@slave190:/home/hadoop/ <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
集群中各组件的安装目录最好保持一致。</p>

<p>6、修改其他机器的myid文件 <br>
myid文件是作为当前机器在zookeeper集群的标识，这些标识在zoo.cfg文件中已经配置好了，但是之前在master188这台机器上配置的myid为1，所以还需要修改其他机器的myid文件：</p>

<p>//在master189机器上 <br>
echo 2 &gt; /home/hadoop/zookeeper-3.4.11/data/zkData/myid <br>
//在slave190机器上 <br>
echo 3 &gt; /home/hadoop/zookeeper-3.4.11/data/zkData/myid <br>
1 <br>
2 <br>
3 <br>
4 <br>
7、启动zookeeper集群 <br>
cd zookeeper-3.4.11/bin/ <br>
//分别在master188、master189、slave190上启动 <br>
./zkServer.sh start</p>

<p>//查看状态 <br>
./zkServer.sh status <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
三台机器的zookeeper状态必须只有一个leader，其他都是follower。</p>

<p>//查看进程，若有QuorumpeerMain，则启动成功 <br>
jps <br>
1 <br>
2 <br>
四、安装配置Hadoop <br>
1、下载及安装 <br>
下载地址：<a href="http://mirrors.hust.edu.cn/apache/hadoop/common/hadoop-2.6.5/hadoop-2.6.5.tar.gz" rel="nofollow">http://mirrors.hust.edu.cn/apache/hadoop/common/hadoop-2.6.5/hadoop-2.6.5.tar.gz</a></p>

<p>在master88机器上，解压到/home/hadoop/目录下：</p>

<p>tar -zxcf hadoop-2.6.5.tar.gz -C /home/hadoop/ <br>
1 <br>
2、配置 <br>
进入配置文件目录，修改配置文件</p>

<p>cd hadoop-2.6.5/etc/hadoop/ <br>
1 <br>
1）vi hadoop-env.sh <br>
配置JDK安装路径：</p>

<p>JAVA_HOME=/opt/jdk <br>
1 <br>
2）vi core-site.xml <br>
 <br>
  <!-- hdfs&#22320;&#22336;&#65292;ha&#27169;&#24335;&#20013;&#26159;&#36830;&#25509;&#21040;nameservice  --> <br>
   <br>
    fs.defaultFS <br>
    hdfs://ns1 <br>
   <br>
  <!-- &#36825;&#37324;&#30340;&#36335;&#24452;&#40664;&#35748;&#26159;NameNode&#12289;DataNode&#12289;JournalNode&#31561;&#23384;&#25918;&#25968;&#25454;&#30340;&#20844;&#20849;&#30446;&#24405;&#65292;&#20063;&#21487;&#20197;&#21333;&#29420;&#25351;&#23450; --> <br>
   <br>
    hadoop.tmp.dir <br>
    /home/hadoop/hadoop-2.6.5/tmp <br>
  </p>

<p><!-- &#25351;&#23450;ZooKeeper&#38598;&#32676;&#30340;&#22320;&#22336;&#21644;&#31471;&#21475;&#12290;&#27880;&#24847;&#65292;&#25968;&#37327;&#19968;&#23450;&#26159;&#22855;&#25968;&#65292;&#19988;&#19981;&#23569;&#20110;&#19977;&#20010;&#33410;&#28857;--> <br>
   <br>
    ha.zookeeper.quorum <br>
    master188:2181,master189:2181,slave190:2181 <br>
  </p>

<p> <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
9 <br>
10 <br>
11 <br>
12 <br>
13 <br>
14 <br>
15 <br>
16 <br>
17 <br>
18 <br>
19 <br>
20 <br>
3）vi hdfs-site.xml <br>
 <br>
  <!-- &#25351;&#23450;&#21103;&#26412;&#25968;&#65292;&#19981;&#33021;&#36229;&#36807;&#26426;&#22120;&#33410;&#28857;&#25968;  --> <br>
   <br>
    dfs.replication <br>
    3 <br>
  </p>

<p><!-- &#20026;namenode&#38598;&#32676;&#23450;&#20041;&#19968;&#20010;services name --> <br>
   <br>
    dfs.nameservices <br>
    ns1 <br>
  </p>

<p><!-- nameservice &#21253;&#21547;&#21738;&#20123;namenode&#65292;&#20026;&#21508;&#20010;namenode&#36215;&#21517; --> <br>
   <br>
    dfs.ha.namenodes.ns1 <br>
    master188,master189 <br>
  </p>

<p><!-- &#21517;&#20026;master188&#30340;namenode&#30340;rpc&#22320;&#22336;&#21644;&#31471;&#21475;&#21495;&#65292;rpc&#29992;&#26469;&#21644;datanode&#36890;&#35759; --> <br>
   <br>
    dfs.namenode.rpc-address.ns1.master188 <br>
    master188:9000 <br>
  </p>

<p><!-- &#21517;&#20026;master189&#30340;namenode&#30340;rpc&#22320;&#22336;&#21644;&#31471;&#21475;&#21495;&#65292;rpc&#29992;&#26469;&#21644;datanode&#36890;&#35759; --> <br>
   <br>
    dfs.namenode.rpc-address.ns1.master189 <br>
    master189:9000 <br>
  </p>

<p><!--&#21517;&#20026;master188&#30340;namenode&#30340;http&#22320;&#22336;&#21644;&#31471;&#21475;&#21495;&#65292;&#29992;&#26469;&#21644;web&#23458;&#25143;&#31471;&#36890;&#35759; --> <br>
   <br>
    dfs.namenode.http-address.ns1.master188 <br>
    master188:50070 <br>
  </p>

<p><!-- &#21517;&#20026;master189&#30340;namenode&#30340;http&#22320;&#22336;&#21644;&#31471;&#21475;&#21495;&#65292;&#29992;&#26469;&#21644;web&#23458;&#25143;&#31471;&#36890;&#35759; --> <br>
   <br>
    dfs.namenode.http-address.ns1.master189 <br>
    master189:50070 <br>
  </p>

<p><!-- namenode&#38388;&#29992;&#20110;&#20849;&#20139;&#32534;&#36753;&#26085;&#24535;&#30340;journal&#33410;&#28857;&#21015;&#34920; --> <br>
   <br>
    dfs.namenode.shared.edits.dir <br>
    qjournal://master188:8485;master189:8485;slave190:8485/ns1 <br>
  </p>

<p><!-- &#25351;&#23450;&#35813;&#38598;&#32676;&#20986;&#29616;&#25925;&#38556;&#26102;&#65292;&#26159;&#21542;&#33258;&#21160;&#20999;&#25442;&#21040;&#21478;&#19968;&#21488;namenode --> <br>
   <br>
    dfs.ha.automatic-failover.enabled.ns1 <br>
    true <br>
  </p>

<p><!-- journalnode &#19978;&#29992;&#20110;&#23384;&#25918;edits&#26085;&#24535;&#30340;&#30446;&#24405; --> <br>
   <br>
    dfs.journalnode.edits.dir <br>
    /home/hadoop/hadoop-2.6.5/tmp/data/dfs/journalnode <br>
  </p>

<p><!-- &#23458;&#25143;&#31471;&#36830;&#25509;&#21487;&#29992;&#29366;&#24577;&#30340;NameNode&#25152;&#29992;&#30340;&#20195;&#29702;&#31867; --> <br>
   <br>
    dfs.client.failover.proxy.provider.ns1 <br>
    org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider <br>
  </p>

<p><!-- &#19968;&#26086;&#38656;&#35201;NameNode&#20999;&#25442;&#65292;&#20351;&#29992;ssh&#26041;&#24335;&#36827;&#34892;&#25805;&#20316; --> <br>
   <br>
    dfs.ha.fencing.methods <br>
    sshfence <br>
  </p>

<p><!-- &#22914;&#26524;&#20351;&#29992;ssh&#36827;&#34892;&#25925;&#38556;&#20999;&#25442;&#65292;&#20351;&#29992;ssh&#36890;&#20449;&#26102;&#29992;&#30340;&#23494;&#38053;&#23384;&#20648;&#30340;&#20301;&#32622; --> <br>
   <br>
    dfs.ha.fencing.ssh.private-key-files <br>
    /home/hadoop/.ssh/id_rsa <br>
  </p>

<p><!-- connect-timeout&#36229;&#26102;&#26102;&#38388; --> <br>
   <br>
    dfs.ha.fencing.ssh.connect-timeout <br>
    30000 <br>
   <br>
 <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
9 <br>
10 <br>
11 <br>
12 <br>
13 <br>
14 <br>
15 <br>
16 <br>
17 <br>
18 <br>
19 <br>
20 <br>
21 <br>
22 <br>
23 <br>
24 <br>
25 <br>
26 <br>
27 <br>
28 <br>
29 <br>
30 <br>
31 <br>
32 <br>
33 <br>
34 <br>
35 <br>
36 <br>
37 <br>
38 <br>
39 <br>
40 <br>
41 <br>
42 <br>
43 <br>
44 <br>
45 <br>
46 <br>
47 <br>
48 <br>
49 <br>
50 <br>
51 <br>
52 <br>
53 <br>
54 <br>
55 <br>
56 <br>
57 <br>
58 <br>
59 <br>
60 <br>
61 <br>
62 <br>
63 <br>
64 <br>
65 <br>
66 <br>
67 <br>
68 <br>
69 <br>
70 <br>
71 <br>
72 <br>
73 <br>
74 <br>
75 <br>
76 <br>
77 <br>
78 <br>
79 <br>
80 <br>
81 <br>
82 <br>
83 <br>
84 <br>
85 <br>
86 <br>
4）vi mapred-site.xml <br>
<!-- &#37319;&#29992;yarn&#20316;&#20026;mapreduce&#30340;&#36164;&#28304;&#35843;&#24230;&#26694;&#26550; --> <br>
 <br>
   <br>
    mapreduce.framework.name <br>
    yarn <br>
   <br>
 <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
5）vi yarn-site.xml <br>
</p>

<p><!-- &#21551;&#29992;HA&#39640;&#21487;&#29992;&#24615; --> <br>
   <br>
    yarn.resourcemanager.ha.enabled <br>
    true <br>
  </p>

<p><!-- &#25351;&#23450;resourcemanager&#30340;&#21517;&#23383; --> <br>
   <br>
    yarn.resourcemanager.cluster-id <br>
    yrc <br>
  </p>

<p><!-- &#20351;&#29992;&#20102;2&#20010;resourcemanager,&#20998;&#21035;&#25351;&#23450;Resourcemanager&#30340;&#22320;&#22336; --> <br>
   <br>
    yarn.resourcemanager.ha.rm-ids <br>
    rm1,rm2 <br>
  </p>

<p><!-- &#25351;&#23450;rm1&#30340;&#22320;&#22336; --> <br>
   <br>
    yarn.resourcemanager.hostname.rm1 <br>
    master188 <br>
  </p>

<p><!-- &#25351;&#23450;rm2&#30340;&#22320;&#22336;  --> <br>
   <br>
    yarn.resourcemanager.hostname.rm2 <br>
    master189 <br>
  </p>

<p><!-- &#25351;&#23450;&#24403;&#21069;&#26426;&#22120;master188&#20316;&#20026;rm1 --> <br>
   <br>
    yarn.resourcemanager.ha.id <br>
    rm1 <br>
  </p>

<p><!-- &#25351;&#23450;zookeeper&#38598;&#32676;&#26426;&#22120; --> <br>
   <br>
    yarn.resourcemanager.zk-address <br>
    master188:2181,master189:2181,slave190:2181 <br>
  </p>

<p><!-- NodeManager&#19978;&#36816;&#34892;&#30340;&#38468;&#23646;&#26381;&#21153;&#65292;&#40664;&#35748;&#26159;mapreduce_shuffle --> <br>
   <br>
    yarn.nodemanager.aux-services <br>
    mapreduce_shuffle <br>
  </p>

<p> <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
9 <br>
10 <br>
11 <br>
12 <br>
13 <br>
14 <br>
15 <br>
16 <br>
17 <br>
18 <br>
19 <br>
20 <br>
21 <br>
22 <br>
23 <br>
24 <br>
25 <br>
26 <br>
27 <br>
28 <br>
29 <br>
30 <br>
31 <br>
32 <br>
33 <br>
34 <br>
35 <br>
36 <br>
37 <br>
38 <br>
39 <br>
40 <br>
41 <br>
42 <br>
43 <br>
44 <br>
45 <br>
46 <br>
47 <br>
48 <br>
49 <br>
50 <br>
51 <br>
52 <br>
6）vi slaves <br>
master188 <br>
master189 <br>
slave190 <br>
1 <br>
2 <br>
3 <br>
3、拷贝hadoop到其他机器 <br>
1）拷贝 <br>
scp -r hadoop-2.6.5 hadoop@master189:/home/hadoop/</p>

<p>scp -r hadoop-2.6.5 hadoop@slave190:/home/hadoop/ <br>
1 <br>
2 <br>
3 <br>
2）修改yarn-site.xml <br>
在master189机器，即ResourceManager备用主节点上修改如下属性，表示当前机器作为rm2:：</p>

<p> <br>
    yarn.resourcemanager.ha.id <br>
    rm2 <br>
   <br>
1 <br>
2 <br>
3 <br>
4 <br>
同时删除slave190机器上的该属性对，因为slave190机器并不作为ResourceManager。</p>

<p>3、启动Hadoop <br>
1)启动Journalnode <br>
cd hadoop-2.6.5/sbin/</p>

<p>./hadoop-daemon.sh start </p>

<p>//查看进程JouralNode是否启动 <br>
jps <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
2）格式化 NameNode和ZKFC <br>
在master188机器上，执行格式化操作：</p>

<p>cd hadoop-2.6.5/bin</p>

<p>./hdfs namenode -format</p>

<p>./hdfs zkfc -formatZK <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
3）备用主节点同步主节点元数据 <br>
在master189（备用主节点）机器上，执行同步操作：</p>

<p>cd hadoop-2.6.5/bin</p>

<p>./hdfs namenode -bootstrapStanby <br>
1 <br>
2 <br>
3 <br>
4）安装fuster <br>
若服务器是最小化安装centeros时，有可能系统没有fuster程序，那么跳过这个安装步骤直接进行后面的操作时，将有可能出现以下问题：</p>

<p>master188作为主节点时，kill掉master188上的NameNode和ResourceManager进程时，可以实现故障转移，master189将从stanby状态自动变成active状态；但是当master189作为主节点时，若kill掉master189上的进程，master188上的进程状态却还是stanby，并不能实现故障自动转移。原因是我们在 hdfs-site.xml中配置了当集群需要故障自动转移时采用SSH方式进行，而因为缺少fuster程序，将在zkfc的日志文件中发现如下错误：</p>

<p>PATH=$PATH:/sbin:/usr/sbin fuser -v -k -n tcp 9000 via ssh: bash: fuser: 未找到命令 <br>
Unable to fence service by any configured method <br>
java.lang.RuntimeException: Unable to fence NameNode at master189/192.168.29.189:9000 <br>
1 <br>
2 <br>
3 <br>
提示未找到fuster程序，导致无法进行fence，所以可以通过如下命令来安装，Psmisc软件包中包含了fuster程序：</p>

<p>//分别在master188、master189、slave190上执行 <br>
sudo yum install psmisc <br>
1 <br>
2 <br>
5）启动HDFS、YARN、ZookeeperFailoverController <br>
在master188机器上：</p>

<p>cd hadoop-2.6.5/sbin</p>

<p>./start-dfs.sh</p>

<p>//验证，显示NameNode和DataNode <br>
jps</p>

<p>./start-yarn.sh</p>

<p>//验证，显示ResourceManager和NodeManager <br>
jps</p>

<p>./hadoop-daemon.sh start zkfc</p>

<p>//验证，显示ZookeeperFailoverController <br>
jps <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
9 <br>
10 <br>
11 <br>
12 <br>
13 <br>
14 <br>
15 <br>
16 <br>
17 <br>
在master189机器上，启动ResourceManager，备用主节点的ResourceManager需要手动启动：</p>

<p>cd hadoop-2.6.5/sbin</p>

<p>yarn-daemon.sh start resourcemanager <br>
1 <br>
2 <br>
3 <br>
6）查看Namenode、ResourceManager状态 <br>
在master188机器上执行：</p>

<p>hdfs haadmin -getServiceState master188 <br>
yarn rmadmin -getServiceState rm1 </p>

<p>hdfs haadmin -getServiceState master189 <br>
yarn rmadmin -getServiceState rm2 <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
也可以通过Web界面来查看，浏览器中输入 ip:50070 查看HDFS，输入 ip:8088/cluster/cluster 查看YARN。</p>

<p>7）测试高可用 <br>
a.主节点—&gt;备用主节点 <br>
kill掉主节点的namenode，查看备用主节点的namenode状态是否切换为active；</p>

<p>kill掉主节点的ResourceManager，查看备用主节点的ResourceManager是否切换为active；</p>

<p>b.备用主节点—&gt;主节点 <br>
若上述操作执行成功，那么再测试反向故障自动转移</p>

<p>先启动被杀死的原主节点的namenode和ResourceManager</p>

<p>hadoop-daemon.sh start namenode </p>

<p>yarn-daemon.sh start resourcemanager <br>
1 <br>
2 <br>
3 <br>
4 <br>
再kill备用主节点的namenode和ResourceManager，查看主节点的状态，若能切换为active，那么Hadoop HA高可用集群搭建完成。</p>

<p>五、安装配置HBase <br>
1、下载及安装 <br>
下载地址：<a href="http://mirrors.hust.edu.cn/apache/hbase/1.3.1/hbase-1.3.1-bin.tar.gz" rel="nofollow">http://mirrors.hust.edu.cn/apache/hbase/1.3.1/hbase-1.3.1-bin.tar.gz</a></p>

<p>在master188机器上，解压到/home/hadoop/目录下：</p>

<p>tar -zxvf hbase-1.3.1-bin.tar.gz  <br>
1 <br>
2、配置 <br>
进入hbase-1.3.1/conf/目录，修改配置文件：</p>

<p>cd hbase-1.3.1/conf/ <br>
1 <br>
1）vi hbase-env.sh <br>
//配置JDK <br>
export JAVA_HOME=/opt/jdk</p>

<p>//保存pid文件 <br>
export HBASE_PID_DIR=/home/hadoop/data/hbase/pids</p>

<p>//修改HBASE_MANAGES_ZK，禁用HBase自带的Zookeeper，因为我们是使用独立的Zookeeper <br>
export HBASE_MANAGES_ZK=false <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
2）vi hbase-site.xml <br>
 <br>
  <!-- &#35774;&#32622;HRegionServers&#20849;&#20139;&#30446;&#24405;&#65292;&#35831;&#21152;&#19978;&#31471;&#21475;&#21495; --> <br>
   <br>
    hbase.rootdir <br>
    hdfs://master188:9000/hbase <br>
  </p>

<p><!-- &#25351;&#23450;HMaster&#20027;&#26426; --> <br>
   <br>
    hbase.master <br>
    hdfs://master188:60000 <br>
  </p>

<p><!-- &#21551;&#29992;&#20998;&#24067;&#24335;&#27169;&#24335; --> <br>
   <br>
    hbase.cluster.distributed <br>
    true <br>
  </p>

<p><!-- &#25351;&#23450;Zookeeper&#38598;&#32676;&#20301;&#32622; --> <br>
   <br>
    hbase.zookeeper.quorum <br>
    master188:2181,master189:2181,slave190:2181 <br>
  </p>

<p><!-- &#25351;&#23450;&#29420;&#31435;Zookeeper&#23433;&#35013;&#36335;&#24452; --> <br>
   <br>
    hbase.zookeeper.property.dataDir <br>
    /home/hadoop/zookeeper-3.4.11 <br>
  </p>

<p><!-- &#25351;&#23450;ZooKeeper&#38598;&#32676;&#31471;&#21475; --> <br>
   <br>
    hbase.zookeeper.property.clientPort <br>
    2181 <br>
   <br>
 <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
9 <br>
10 <br>
11 <br>
12 <br>
13 <br>
14 <br>
15 <br>
16 <br>
17 <br>
18 <br>
19 <br>
20 <br>
21 <br>
22 <br>
23 <br>
24 <br>
25 <br>
26 <br>
27 <br>
28 <br>
29 <br>
30 <br>
31 <br>
32 <br>
33 <br>
34 <br>
35 <br>
36 <br>
37 <br>
38 <br>
3）vi regionservers <br>
修改regionservers文件，因为当前是使用独立的Zookeeper集群，所以要指定RegionServers所在机器：</p>

<p>master188 <br>
master189 <br>
slave190 <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
4）创建pid文件保存目录 <br>
在/home/hadoop/目录下：</p>

<p>mkdir data/hbase/pids -p <br>
1 <br>
3、拷贝HBase到其他机器 <br>
cd /home/hadoop/</p>

<p>scp -r hbase-1.3.1 hadoop@master189:/home/hadoop/</p>

<p>scp -r  hbase-1.3.1 hadoop@slave190:/home/hadoop/ <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
4、启动HBase <br>
在主节点上启动HBase（这里的主节点是指NameNode状态为active的节点，而非指文中对本实验的机器声明）：</p>

<p>cd hbase-1.3.1/bin</p>

<p>./start-hbase.sh</p>

<p>//查看HMaster、Regionserver进程是否启动 <br>
jps <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
注意：此时Hadoop集群应处于启动状态，并且是在主节点执行start-hbase.sh启动HBase集群，否则HMaster进程将在启动几秒后消失，而备用的HMaster进程需要在备用主节点单独启动，命令是：./hbase-daemon.sh start master。</p>

<p>在备用主节点启动HMaster进程，作为备用HMaster：</p>

<p>cd hbase-1.3.1/bin</p>

<p>./hbase-daemon.sh start master <br>
1 <br>
2 <br>
3 <br>
5、HA高可用测试 <br>
在浏览器中输入 ip:16010 ，查看主节点和备用主节点上的HMaster的状态，在备用主节点的web界面中，可以看到“Current Active Master: master188”，表示当前HBase主节点是master188机器；</p>

<p>主节点—&gt;备用主节点 <br>
这里的主节点指使用start-hbase.sh命令启动HBase集群的机器</p>

<p>kill掉主节点的HMaster进程，在浏览器中查看备用主节点的HBase是否切换为active；</p>

<p>若上述操作成功，则在主节点启动被杀死的HMaster进程：</p>

<p>cd hbase-1.3.1/bin/</p>

<p>./hbase-daemon.sh start master <br>
1 <br>
2 <br>
3 <br>
4 <br>
然后，kill掉备用主节点的HMaster进程，在浏览器中查看主节点的HBase是否切换为active，若操作成功，则HBase高可用集群搭建完成；</p>

<p>6、HBase基本操作 <br>
//启动HBase <br>
[root@vnet ~] start-hbase.sh</p>

<p>//进入HBase Shell <br>
[root@vnet ~] hbase shell</p>

<p>//查看当前HBase有哪些表 <br>
hbase(main):&gt; list</p>

<p>//创建表t_user，cf1和cf2是列族，列族一般不超过3个 <br>
hbase(main):&gt; create ‘t_user’,’cf1’,’cf2’</p>

<p>//获得表t_user的描述信息 <br>
hbase(main):&gt; describe ‘t_user’</p>

<p>//禁用表 <br>
hbase(main):&gt; disable ‘t_user’</p>

<p>//删除表，删除表之前要先把表禁用掉 <br>
hbase(main):&gt; drop ‘t_user’</p>

<p>//查询表是否存在 <br>
hbase(main):&gt; exists ‘t_user’</p>

<p>//查看全表数据 <br>
hbase(main):&gt; scan ‘t_user’</p>

<p>//插入数据，分别是表名、key、列（列族：具体列）、值。HBase是面向列的数据库，列可无限扩充 <br>
hbase(main):&gt; put ‘t_user’ ,’001’,’cf1:name’,’chenxj’ <br>
hbase(main):&gt; put ‘t_user’ ,’001’,’cf1:age’,’18’ <br>
hbase(main):&gt; put ‘t_user’ ,’001’,’cf2:sex’,’man’ <br>
hbase(main):&gt; put ‘t_user’ ,’002’,’cf1:name’,’chenxj’ <br>
hbase(main):&gt; put ‘t_user’ ,’002’,’cf1:address’,’fuzhou’ <br>
hbase(main):&gt; put ‘t_user’ ,’002’,’cf2:sex’,’man’</p>

<p>//获取数据，可根据key、key和列族等进行查询 <br>
hbase(main):&gt; get ‘t_user’,’001’ <br>
hbase(main):&gt; get ‘t_user’,’002’,’cf1’ <br>
hbase(main):&gt; get ‘t_user’,’001’,’cf1:age’ <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
7 <br>
8 <br>
9 <br>
10 <br>
11 <br>
12 <br>
13 <br>
14 <br>
15 <br>
16 <br>
17 <br>
18 <br>
19 <br>
20 <br>
21 <br>
22 <br>
23 <br>
24 <br>
25 <br>
26 <br>
27 <br>
28 <br>
29 <br>
30 <br>
31 <br>
32 <br>
33 <br>
34 <br>
35 <br>
36 <br>
37 <br>
38 <br>
39 <br>
40 <br>
六、集群启动结果 <br>
Hadoop + Zookeeper + HBase 高可用集群启动后，进程状态如下：</p>

<p>描述  master188   master189   slave190 <br>
HDFS主   NameNode    NameNode <br>
HDFS从   DataNode    DataNode    DataNode <br>
YARN主   ResourceManager ResourceManager  <br>
YARN从   NodeManager NodeManager NodeManager <br>
HBase主  HMaster HMaster  <br>
HBase从  HRegionServer   HRegionServer   HRegionServer <br>
Zookeeper独立进程   QuorumPeerMain  QuorumPeerMain  QuorumPeerMain <br>
NameNodes数据同步   JournalNode JournalNode JournalNode <br>
主备故障切换  DFSZKFailoverController DFSZKFailoverController  <br>
七、总结 <br>
需要注意的地方：</p>

<p>1）备用节点上的NameNode、ResourceManager、HMaster均需单独启动；</p>

<p>hadoop-daemon.sh start namenode</p>

<p>yarn-daemon.sh start resourcemanager</p>

<p>hbase-daemon.sh start master  <br>
1 <br>
2 <br>
3 <br>
4 <br>
5 <br>
6 <br>
2）可以使用-forcemanual参数强制切换主节点与备用主节点，但强制切换后集群的自动故障转移将会失效，需要重新格式化zkfc：hdfs zdfc -formatZK;</p>

<p>hdfs haadmin -transitionToActive/transitionToStandby  -forcemanual  master189 <br>
yarn rmadmin -transitionToActive/transitionToStandby  -forcemanual  rm2 <br>
1 <br>
2 <br>
3）在备用主节点同步主节点的元数据时，主节点的HDFS必须已经启动；</p>

<p>4）无法查看standby状态的节点上的hdfs；</p>

<p>5）格式化namenode时要先启动各个JournalNode机器上的journalnode进程：hadoop-daemon.sh start journalnode；</p>

<p>6）若遇到问题，可以先考虑是哪个组件出现问题，然后查看该组件或与该组件相关的组件的日志信息；若各组件web页面无法访问，或存在其他连接问题，可以从「防火墙是否关闭」、「端口是否被占用」、「SSH」、「集群机器是否处于同一网段」内等角度考虑； <br>
转载： <a href="https://blog.csdn.net/qq_26907251/article/details/78813798" rel="nofollow">https://blog.csdn.net/qq_26907251/article/details/78813798</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      8hadoop&zookeeper&kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Hadoop 安装<br>
yum -y install java-1.8.0-openjdk  java-1.8.0-openjdk-devel<br>
tar -xf hadoop-2.7.3.tar.gz<br>
mv hadoop-2.7.3  /usr/local/hadoop<br>
java -version</p>

<p>vim /usr/local/hadoop/etc/hadoop/hadoop-env.sh<br>
25 export JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.131-11.b12.el7.x86_64/jre"<br>
33 export HADOOP_CONF_DIR="/usr/local/hadoop/etc/hadoop"</p>

<p>cd /usr/local/hadoop<br>
./bin/hadoop version</p>

<p>统计文件中单次出现频率：<br>
cd /usr/local/hadoop<br>
mkdir cc<br>
cp *.txt  cc<br>
./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar wordcount cc output<br>
cd output<br>
cat part-r-00000</p>

<p>部署模式：<br>
单机<br>
伪分布式<br>
完全分布式<br>
cd /usr/local/hadoop/etc/hadoop/<br>
四个核心配置文件：<br>
hadoop.apache.org  //官网<br>
xml格式：</p>

<p>1<br>
core-site.xml------》hadoop的核心配置文件<br>
hadoop关键参数：<br>
fs.defaultFS        #file:///  或 hdfs://namenode.ip:9000<br>
hadoop.tmp.dir     #定义数据存储路径  //hadoop的根目录</p>

<p>2<br>
hdfs-site.xml------》hdfs的核心配置文件<br>
dfs.namenode.http-addreess    # namenode的ip和端口号默认50070<br>
dfs.namenode.secondary.http-address  #namenode.secondary的ip和端口号默认50090<br>
dfs.replication  #数据冗余，默认冗余3份<br>
dfs.namenode.name.dir  #定义文件映向数据存放位置，备份数据备份此目录<br>
dfs.datanode.data.dir  #决定集群大小，存储块具体数据的路径<br>
dfs.webhdfs.enable    #<br>
 <br>
3<br>
mapred-site.xml.template---&gt;mapred的核心配置文件<br>
mapreduce.frame.name   #分布式计算框架使用模式，local或者yarn<br>
使用local模式下，参数：</p>

<p>4<br>
yarn-site.xml-----》yarn的核心配置文件<br>
yarn.resourcemanager.hostname  # 指定resourcemanager地址<br>
yarn.nodemanager.aux-services   #指定nodemanager计算框架<br>
**************************<br>
配置完全分布式：<br>
规划：6台虚拟机<br>
cat /etc/hosts<br>
192.168.4.20  nn01<br>
192.168.4.21  node1<br>
192.168.4.22  node2<br>
192.168.4.23  node3</p>

<p>4台机安装<br>
yum -y install java-1.8.0-openjdk-devel<br>
java -version<br>
vim /etc/ssh/ssh_config <br>
 56 Host *<br>
 57         GSSAPIAuthentication no<br>
 58         StrictHostKeyChecking no</p>

<p>配置ssh信任环境便p20操作：<br>
ssh-keygen<br>
149  ssh-copy-id -i ./id_rsa.pub root@192.168.4.20<br>
149  ssh-copy-id -i ./id_rsa.pub root@192.168.4.21<br>
151  ssh-copy-id -i ./id_rsa.pub root@192.168.4.22<br>
152  ssh-copy-id -i ./id_rsa.pub root@192.168.4.23</p>

<p>p20操作：<br>
yum -y install java-1.8.0-openjdk  java-1.8.0-openjdk-devel<br>
tar -xf hadoop-2.7.3.tar.gz<br>
mv hadoop-2.7.3  /usr/local/hadoop<br>
java -version</p>

<p>vim /usr/local/hadoop/etc/hadoop/hadoop-env.sh<br>
25 export JAVA_HOME="/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.131-11.b12.el7.x86_64/jre"<br>
33 export HADOOP_CONF_DIR="/usr/local/hadoop/etc/hadoop"</p>

<p>cd /usr/local/hadoop<br>
./bin/hadoop version</p>

<p>cd /usr/local/hadoop/etc/hadoop/</p>

<p>第一个配置文件<br>
vim core-site.xml<br>
&lt;configuration&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;fs.defaultFS&lt;/name&gt;<br>
     &lt;value&gt;hdfs://nn01:9000&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;hadopp.tmp.dir&lt;/name&gt;<br>
     &lt;value&gt;/var/hadoop&lt;/value&gt;<br>
  &lt;/property&gt;<br>
&lt;/configuration&gt;<br>
mkdir /var/hadoop</p>

<p>第二个配置文件<br>
vim hdfs-site.xml<br>
&lt;configuration&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;dfs.namenode.http-address&lt;/name&gt;<br>
     &lt;value&gt;nn01:50070&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;dfs.namenode.secondary.http-address&lt;/name&gt;<br>
     &lt;value&gt;nn01:50090&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;dfs.replication&lt;/name&gt;<br>
     &lt;value&gt;2&lt;/value&gt;<br>
  &lt;/property&gt;<br>
&lt;/configuration&gt;</p>

<p>第三个配置文件<br>
vim slaves<br>
node1<br>
node2<br>
node3<br>
*******************************<br>
p21 p22 p23 同步 p20的 /usr/local/hadoop</p>

<p>scp -r /usr/local/hadoop/  root@192.168.4.21:/usr/local<br>
scp -r /usr/local/hadoop/  root@192.168.4.22:/usr/local<br>
scp -r /usr/local/hadoop/  root@192.168.4.23:/usr/local</p>

<p>*****************************************<br>
p20操作：<br>
cd /usr/local/hadoop<br>
./bin/hdfs namenode -format    //格式化<br>
./sbin/start-dfs.sh     //启动服务<br>
p20----p23验证：<br>
]#jps<br>
./bin/hdfs  dfsadmin -report</p>

<p>**************************************<br>
hdfs 使用：<br>
./bin/hadoop fs -命令  路径</p>

<p>***********************************<br>
yarn 安装<br>
p20操作<br>
cd /usr/local/hadoop/etc/hadoop/<br>
mv mapred-site.xml.template mapred-site.xml<br>
vim mapred-site.xml<br>
&lt;configuration&gt;<br>
 &lt;property&gt;<br>
     &lt;name&gt;mapreduce.framework.name&lt;/name&gt;<br>
     &lt;value&gt;yarn&lt;/value&gt;<br>
  &lt;/property&gt;<br>
&lt;/configuration&gt;</p>

<p>vim yarn-site.xml</p>

<p>&lt;configuration&gt;</p>

<p>&lt;!-- Site specific YARN configuration properties --&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;<br>
    &lt;value&gt;mapreduce_shuffle&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;yarn.resourcemanager.hostname&lt;/name&gt;<br>
    &lt;value&gt;nn01&lt;/value&gt;<br>
  &lt;/property&gt;</p>

<p>&lt;/configuration&gt;</p>

<p>./sbin/start-yarn.sh</p>

<p>http:192.168.4.20:50070   namenode<br>
http:192.168.4.20:50090   secondary namenode<br>
http:192.168.4.20:50070   resourcemanager</p>

<p>http:192.168.4.11:50075   datanode<br>
http:192.168.4.12:80421   nodemanager</p>

<p>./bin/hadoop fs -put *.txt /a1<br>
./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar wordcount /a1 /a2<br>
./bin/hadoop fs -ls /a2<br>
./bin/hadoop fs -cat /a2/part-r-00000</p>

<p>****************************************<br>
HDFS进阶应用<br>
1<br>
[root@nn01 hadoop]# ./sbin/stop-all.sh<br>
[root@nn01 hadoop]# useradd -u 500 -g 100 nfsuser<br>
[root@nn01 hadoop]# id nfsuser<br>
uid=500(nfsuser) gid=100(users) 组=100(users)</p>

<p><br>
2<br>
[root@nfsgw ~]# useradd -u 500 -g 100 nfsuser<br>
[root@nfsgw ~]# id nfsuser<br>
uid=500(nfsuser) gid=100(users) 组=100(users)</p>

<p>3<br>
[root@nn01 ~]# vim /etc/hosts</p>

<p>192.168.4.20  nn01<br>
192.168.4.21  node1<br>
192.168.4.22  node2<br>
192.168.4.23  node3<br>
192.168.4.25  nfsgw<br>
[root@nn01 ~]# scp /etc/hosts  192.168.4.21:/etc/hosts<br>
[root@nn01 ~]# scp /etc/hosts  192.168.4.22:/etc/hosts<br>
[root@nn01 ~]# scp /etc/hosts  192.168.4.23:/etc/hosts</p>

<p>[root@nn01 ~]#cd /usr/local/hadoop/etc/hadoop<br>
vim core-site.xml<br>
  &lt;property&gt;<br>
     &lt;name&gt;hadoop.proxyuser.nfsuser.groups&lt;/name&gt;<br>
     &lt;value&gt;*&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;hadopp.proxyuser.nfsuser.hosts&lt;/name&gt;<br>
     &lt;value&gt;*&lt;/value&gt;<br>
  &lt;/property&gt;</p>

<p>scp /usr/local/hadoop/etc/hadoop/core-site.xml 192.168.4.21:/usr/local/hadoop/etc/hadoop/<br>
scp /usr/local/hadoop/etc/hadoop/core-site.xml 192.168.4.22:/usr/local/hadoop/etc/hadoop/<br>
scp /usr/local/hadoop/etc/hadoop/core-site.xml 192.168.4.23:/usr/local/hadoop/etc/hadoop/<br>
scp -r /usr/local/hadoop/  192.168.4.25:/usr/local/<br>
[root@nn01~]# cd /usr/local/hadoop<br>
./sbin/start-dfs.sh<br>
./bin/hdfs dfsadmin -report<br>
jps</p>

<p>4<br>
[root@nfsgw ~]# mkdir /var/hadoop<br>
yum -y install java-1.8.0-openjdk-devel<br>
java -version<br>
cd /usr/local/hadoop/etc/hadoop<br>
vim hdfs-site.xml   //添加以下模块<br>
  &lt;property&gt;<br>
     &lt;name&gt;nfs.exports.allowed.hosts&lt;/name&gt;<br>
     &lt;value&gt;* rw&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;dfs.namenode.accesstime.precision&lt;/name&gt;<br>
     &lt;value&gt;3600000&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;nfs.dump.dir&lt;/name&gt;<br>
     &lt;value&gt;/var/nfsfile&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;nfs.rtmax&lt;/name&gt;<br>
     &lt;value&gt;4194304&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;nfs.wtmax&lt;/name&gt;<br>
     &lt;value&gt;1048576&lt;/value&gt;<br>
  &lt;/property&gt;<br>
  &lt;/property&gt;<br>
  &lt;property&gt;<br>
     &lt;name&gt;nfs.port.monitoring.disabled&lt;/name&gt;<br>
     &lt;value&gt;false&lt;/value&gt;<br>
  &lt;/property&gt;</p>

<p>rpm -qa | grep rpcbind<br>
yum -y remove rpcbind<br>
yum -y remove nfs-utils<br>
cd /usr/local/hadoop<br>
setfacl -m user:nfsuser:rwx logs<br>
getfacl logs</p>

<p><br>
root 执行：<br>
cd /usr/local/hadoop<br>
./sbin/hadoop-daemon.sh --script ./bin/hdfs  start portmap<br>
jps</p>

<p>su - nfsuser <br>
执行：<br>
cd /usr/local/hadoop<br>
./sbin/hadoop-daemon.sh --script ./bin/hdfs start nfs3<br>
jps</p>

<p><br>
验证：p24</p>

<p>yum -y install nfs-utils<br>
[root@p24 ~]# mount -t nfs -o vers=3,proto=tcp,nolock,noacl,noatime,sync  192.168.4.25:/ /mnt</p>

<p>验证：<br>
df -h<br>
cd /mnt<br>
ls</p>

<p>hadoop节点管理：<br>
1 hdfs增加节点：<br>
nn01操作：<br>
vim /etc/hosts<br>
192.168.4.20  nn01<br>
192.168.4.21  node1<br>
192.168.4.22  node2<br>
192.168.4.23  node3<br>
192.168.4.24  new<br>
192.168.4.25  nfsgw</p>

<p>vim slaves<br>
node1<br>
node2<br>
node3<br>
new</p>

<p>scp /etc/hosts 192.168.4.21:/etc<br>
scp /etc/hosts 192.168.4.22:/etc<br>
scp /etc/hosts 192.168.4.23:/etc<br>
scp /etc/hosts 192.168.4.24:/etc<br>
scp /etc/hosts 192.168.4.25:/etc</p>

<p>scp -r /usr/local/hadoop/ root@new:/usr/local</p>

<p>./bin/hdfs  dfsadmin -report</p>

<p>2new节点操作<br>
yum -y install java-1.8.0-openjdk-devel<br>
java -version<br>
echo new &gt; /etc/hostname<br>
mkdir /var/hadoop<br>
./sbin/hadoop-daemon.sh start datanode<br>
./bin/hdfs dfsadmin -setBalancerBandwidth 60000000<br>
./sbin/start-balancer.sh -threshold 5</p>

<p>节点修复<br>
节点删除<br>
******************************************<br>
zookeeper 集群<br>
1 nn01操作<br>
tar -xf zookeeper-3.4.10.tar.gz<br>
mv zookeeper-3.4.10 /usr/local/zookeeper<br>
cd /usr/local/zookeeper/conf<br>
cp zoo_sample.cfg zoo.cfg</p>

<p>vim zoo.cfg<br>
server.1=node1:2888:3888<br>
server.2=node2:2888:3888<br>
server.3=node3:2888:3888<br>
server.4=nn01:2888:3888:observer</p>

<p>scp -r /usr/local/zookeeper/  node1:/usr/local/<br>
scp -r /usr/local/zookeeper/  node2:/usr/local/<br>
scp -r /usr/local/zookeeper/  node3:/usr/local/<br>
mkdir /tmp/zookeeper<br>
echo 4 &gt; /tmp/zookeeper/myid<br>
/usr/local/zookeeper/bin/zkServer.sh start<br>
/usr/local/zookeeper/bin/zkServer.sh status</p>

<p>2 node1-----node3<br>
mkdir /tmp/zookeeper<br>
echo 1 &gt; /tmp/zookeeper/myid<br>
echo 2 &gt; /tmp/zookeeper/myid<br>
echo 3 &gt; /tmp/zookeeper/myid<br>
/usr/local/zookeeper/bin/zkServer.sh start<br>
/usr/local/zookeeper/bin/zkServer.sh status</p>

<p>*********************************************<br>
kafka 配置安装：</p>

<p>node1配置<br>
tar -xf kafka_2.10-0.10.2.1.tgz<br>
mv kafka_2.10-0.10.2.1 /usr/local/kafka<br>
cd /usr/local/kafka/config<br>
vim server.properties</p>

<p>21 broker.id=11<br>
119 zookeeper.connect=node1:2181,node2:2181,node3:2181</p>

<p>scp -r /usr/local/kafka/     192.168.4.22:/usr/local<br>
scp -r /usr/local/kafka/     192.168.4.23:/usr/local</p>

<p>cd /usr/local/kafka/<br>
./bin/kafka-server-start.sh -daemon  config/server.properties<br>
jps</p>

<p>node2-----node3 配置<br>
只需修改配置文件broker.id，三台节点必须不同就可以<br>
./bin/kafka-server-start.sh -daemon  config/server.properties<br>
jps</p>

<p>nn01操作：<br>
rsync -azSH node1:/usr/local/kafka  /usr/local<br>
./bin/kafka-topics.sh --create --partitions 1 --replication-factor 1 --zookeeper node3:2181 --topic nsd1710<br>
./bin/kafka-topics.sh --list --zookeeper node2:2181<br>
./bin/kafka-topics.sh --describe --zookeeper node1:2181 --topic nsd1710<br>
./bin/kafka-console-producer.sh --broker-list node1:9092 --topic nsd1710</p>

<p>另开终端：nn01<br>
./bin/kafka-console-consumer.sh --bootstrap-server node3:9092 --topic nsd1710 <br>
./bin/kafka-console-consumer.sh --bootstrap-server node3:9092 --topic nsd1710 --from-beginning</p>            </div>
                </div>
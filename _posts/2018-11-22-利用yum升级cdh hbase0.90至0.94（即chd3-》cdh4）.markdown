---
layout:     post
title:      利用yum升级cdh hbase0.90至0.94（即chd3-》cdh4）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>一. 集群布局</h3>
<p>haoop06                thrift server、hbase master、zookeeper server</p>
<p>hadoop01~04      thrift server、hbase regionserver、zookeeper</p>
<p>hadoop07、hadoop08    thrift server、hbase regionserver、zookeeper</p>
<p><br></p>
<h3>二. 编辑repository文件</h3>
<p>#<span style="color:#339999;"> vi /etc/yum.repos.d/cloudera-cdh4.repo</span><br></p>
<p><em>[cloudera-cdh4]<br>
name=Cloudera's Distribution for Hadoop, Version 4<br>
baseurl=http://archive.cloudera.com/cdh4/redhat/5/x86_64/cdh/4/<br>
gpgkey = http://archive.cloudera.com/cdh4/redhat/5/x86_64/cdh/RPM-GPG-KEY-cloudera<br>
gpgcheck = 1</em><br></p>
<p>或到以下网址去下载所需的repo文件</p>
<p><a href="http://www.cloudera.com/content/cloudera-content/cloudera-docs/CDH4/latest/CDH-Version-and-Packaging-Information/cdhvd_topic_2.html" rel="nofollow">http://www.cloudera.com/content/cloudera-content/cloudera-docs/CDH4/latest/CDH-Version-and-Packaging-Information/cdhvd_topic_2.html</a><br></p>
<p><br></p>
<h3>三. 关闭并删除已有hbase</h3>
<p>1. 停止<span style="font-family:Arial, sans-serif;"><span style="line-height:18.96875px;">thrift server和clients，停止集群</span></span></p>
<p># <span style="color:#339999;">service hadoop-hbase-thrift stop</span></p>
<p>#<span style="color:#339999;"> service hadoop-hbase-master stop</span></p>
<p># <span style="color:#339999;">service hadoop-hbase-regionserver stop</span></p>
<p>2. 停止zookeeper server</p>
<p># <span style="color:#339999;">service hadoop-zookeeper-server stop</span></p>
<p>3. 将zookeeper从cdh3升级至cdh4</p>
<p>（1）删除zookeeper</p>
<p>#<span style="color:#339999;"> service hadoop-zookeeper-server stop </span>（hadoop06 only）</p>
<p># <span style="color:#339999;">yum remove hadoop-zookeeper-server</span>   (hadoop06 only)</p>
<p># <span style="color:#339999;">yum remove hadoop-zookeeper</span></p>
<p>（2）安装zookeeper基础包</p>
<p>#<span style="color:#339999;"> yum install zookeeper</span></p>
<p>（3）安装zookeeper server</p>
<p>#<span style="color:#339999;"> yum install zookeeper-server</span>  （hadoop06 only）</p>
<p>（4）编辑/etc/zookeeper/conf/zoo.cfg文件</p>
<p># <span style="color:#339999;">vi /etc/zookeeper/conf/zoo.cfg</span></p>
<p><em># The number of milliseconds of each tick<br>
tickTime=2000<br>
# The number of ticks that the initial<br>
# synchronization phase can take<br>
initLimit=10<br>
# The number of ticks that can pass between<br>
# sending a request and getting an acknowledgement<br>
syncLimit=5<br>
# the directory where the snapshot is stored.<br>
dataDir=/var/zookeeper<br>
# the port at which the clients will connect<br>
clientPort=2181<br>
server.0=hadoop06.ihep.ac.cn:2888:3888</em><br></p>
<p>（5）启动zookeeper server</p>
<p><br></p>
<h3>四. 安装cdh4版本的hbase</h3>
<p>1. 安装hbase</p>
<p>#  <span style="color:#339999;">yum install hbase</span></p>
<p>2. 安装master （hadoop06 only）</p>
<p># <span style="color:#339999;">yum install hbase-master</span></p>
<p>3. 安装regionserver （except hadoop06）</p>
<p>#  <span style="color:#339999;">yum install hbase-regionserver</span></p>
<p>4. 安装thriftserver</p>
<p>#  <span style="color:#339999;">yum install hbase-thrift</span></p>
<p>5. 安装hbase-rest</p>
<p># <span style="color:#339999;">yum install hbase-rest</span></p>
<p>6. 配置hbase-site.xml文件 （cdh4的该文件位于/etc/hbase/conf.dist/目录下，将该目录下的文件拷贝到/etc/hbase/conf下）</p>
<p># <span style="color:#339999;">vi /etc/hbase/conf/hbase-site.xml</span></p>
<p><em>&lt;configuration&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
        &lt;value&gt;hdfs://hadoop06.ihep.ac.cn:8008/hbase&lt;/value&gt;<br>
        &lt;description&gt;The directory shared by region servers and into<br>
        which HBase persists.  The URL should be 'fully-qualified'<br>
        to include the filesystem scheme.  For example, to specify the<br>
        HDFS directory '/hbase' where the HDFS instance's namenode is<br>
        running at namenode.example.org on port 9000, set this value to:<br>
        hdfs://namenode.example.org:9000/hbase.  By default HBase writes<br>
        into /tmp.  Change this configuration else all data will be lost<br>
        on machine restart.<br>
        &lt;/description&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.master.port&lt;/name&gt;<br>
        &lt;value&gt;60000&lt;/value&gt;<br>
        &lt;description&gt;The port the HBase Master should bind to.&lt;/description&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;<br>
        &lt;value&gt;true&lt;/value&gt;<br>
        &lt;description&gt;The mode the cluster will be in. Possible values are<br>
        false for standalone mode and true for distributed mode.  If<br>
        false, startup will run all HBase and ZooKeeper daemons together<br>
        in the one JVM.<br>
        &lt;/description&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;<br>
        &lt;value&gt;hadoop06.ihep.ac.cn&lt;/value&gt;<br>
        &lt;description&gt;Comma separated list of servers in the ZooKeeper Quorum.<br>
        For example, "host1.mydomain.com,host2.mydomain.com,host3.mydomain.com".<br>
        By default this is set to localhost for local and pseudo-distributed modes<br>
        of operation. For a fully-distributed setup, this should be set to a full<br>
        list of ZooKeeper quorum servers. If HBASE_MANAGES_ZK is set in hbase-env.sh<br>
        this is the list of servers which we will start/stop ZooKeeper on.<br>
        &lt;/description&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.zookeeper.sission.timeout&lt;/name&gt;<br>
        &lt;value&gt;60000&lt;/value&gt;<br>
    &lt;/property&gt;<br></em></p>
<p><em>     &lt;property&gt;<br>
        &lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;<br>
        &lt;value&gt;2181&lt;/value&gt;<br>
        &lt;description&gt;Property from ZooKeeper's config zoo.cfg.<br>
        The port at which the clients will connect.<br>
        &lt;/description&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.master&lt;/name&gt;<br>
        &lt;value&gt;hadoop06.ihep.ac.cn&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.master.info.port&lt;/name&gt;<br>
        &lt;value&gt;60010&lt;/value&gt;<br>
        &lt;description&gt;The port for the HBase Master web UI.<br>
        Set to -1 if you do not want a UI instance run.<br>
        &lt;/description&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.master.info.bindAddress&lt;/name&gt;<br>
        &lt;value&gt;0.0.0.0&lt;/value&gt;<br>
        &lt;description&gt;The bind address for the HBase Master web UI<br>
        &lt;/description&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.regionserver.lease.period&lt;/name&gt;<br>
        &lt;value&gt;60000&lt;/value&gt;<br>
        &lt;description&gt;HRegion server lease period in milliseconds. Default is<br>
        60 seconds. Clients must report in within this period else they are<br>
        considered dead.&lt;/description&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.rpc.timeout&lt;/name&gt;<br>
        &lt;value&gt;60000&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
        &lt;name&gt;hbase.rest.port&lt;/name&gt;<br>
        &lt;value&gt;60050&lt;/value&gt;<br>
    &lt;/property&gt;<br>
&lt;/configuration&gt;</em></p>
<p># <span style="color:#339999;">vi /etc/hbase/conf/regionservers</span></p>
<p><em>hadoop01.ihep.ac.cn<br>
hadoop02.ihep.ac.cn<br>
hadoop03.ihep.ac.cn<br>
hadoop04.ihep.ac.cn<br>
hadoop07.ihep.ac.cn<br>
hadoop08.ihep.ac.cn</em><br></p>
<p>7. 启动hbase</p>
<p># <span style="color:#339999;">service zookeeper-server start</span></p>
<p># <span style="color:#339999;">service hbase-master</span></p>
<p># <span style="color:#339999;">service hbase-regionserver</span></p>
<p># <span style="color:#339999;">service hbase-thrift</span></p>
<p>8. 命令调整</p>
<p># <span style="color:#339999;">mv /usr/bin/hbase /usr/bin/hbase.bak </span>（或将其删除）</p>
<p># <span style="color:#339999;">export PATH=$PATH:/usr/lib/hbase/bin</span> （或将其写入~/.bash_profile文件中）</p>
<p>9. 测试安装是否成功（任选其一即可）</p>
<p>（1）打开http://hadoop06.ihep.ac.cn:60010/master-status网页</p>
<p>（2）hbase shell</p>
<p><br></p>
<p>查看hadoop版本：hadoop version</p>
<p>手动更新locate数据库：updatedb</p>
<p>yum查询已安装软件：yum list installed</p>
<p>启动图形界面：/usr/sbin/gdm start</p>
<p>查看hdfs中文件大小：hadoop fs -du hdfs路径</p>
            </div>
                </div>
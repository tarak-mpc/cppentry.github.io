---
layout:     post
title:      Hive Hbase integration (整合) 出现zk始终连接 localhost:2181
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div class="iteye-blog-content-contain" style="font-size:14px;">
<p>前提是 hive客户端和zk没在一台机器，hive只是作为客户端使用，没有和hadoop集群在一起。</p>
<p>hive 和 hbase 整合(integration) 的时候,在创建hive关联Hbase表的时候出现如下 zk始终连接localhost:2181,</p>
<p> </p>
<p>建表语句如下：</p>
<p>---------------------------------------------------------------------------------------------------</p>
<p> create external table h_table_user3(key int,</p>
<p>     name string,age int,city string)</p>
<p>     STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'</p>
<p>     WITH SERDEPROPERTIES('hbase.columns.mapping'=':key,info:name,info:age,address:city') TBLPROPERTIES ("hbase.table.name" = "user");</p>
<p> </p>
<p>-----------------------------------------------------------------------------------------------------</p>
<p>hive目录 conf下的hive-site.xml里面和zk相关的全部都已经改过后还是无效。</p>
<p>----------------------------------------------------------------------------------------------------------</p>
<p>&lt;property&gt;</p>
<p>  &lt;name&gt;hive.cluster.delegation.token.store.zookeeper.connectString&lt;/name&gt;</p>
<p>  &lt;value&gt;l-hdfsgl2.bi.prod.cn1:2181&lt;/value&gt;</p>
<p>  &lt;description&gt;The ZooKeeper token store connect string.&lt;/description&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>  &lt;name&gt;hive.zookeeper.quorum&lt;/name&gt;</p>
<p>  &lt;value&gt;l-hdfsgl1.bi.prod.cn1,l-hdfsgl2.bi.prod.cn1,l-hdfscc1.bi.prod.cn1&lt;/value&gt;</p>
<p>  &lt;description&gt;The list of zookeeper servers to talk to. This is only needed for read/write locks.&lt;/description&gt;</p>
<p>&lt;/property&gt;</p>
<p> </p>
<p>&lt;property&gt;</p>
<p>  &lt;name&gt;hive.zookeeper.client.port&lt;/name&gt;</p>
<p>  &lt;value&gt;2181&lt;/value&gt;</p>
<p>  &lt;description&gt;The port of zookeeper servers to talk to. This is only needed for read/write locks.&lt;/description&gt;</p>
<p>&lt;/property&gt;</p>
<p>--------------------------------------------------------------------------------------------------------------------------</p>
<p>以上3个配置全部配置了还是依旧出现连接 localhost:2181</p>
<p>...............................................................................................................................................................</p>
<p>b/stax-api-1.0.1.jar:/usr/local/hive-0.12.0-cdh5.1.2/lib/stringtemplate-3.2.1.jar:/usr/local/hive-0.12.0-cdh5.1.2/lib/tempus-fugit-1.1.jar:/usr/local/hive-0.12.0-cdh5.1.2/lib/velocity-1.5.jar:/usr/local/hive-0.12.0-cdh5.1.2/lib/xz-1.0.jar:/usr/local/hive-0.12.0-cdh5.1.2/lib/zookeeper-3.4.5-cdh5.1.2.jar::/usr/local/hadoop-2.3.0-cdh5.1.2/contrib/capacity-scheduler/*.jar</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:java.library.path=/usr/local/hadoop-2.3.0-cdh5.1.2/lib/native</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:java.io.tmpdir=/tmp</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:java.compiler=&lt;NA&gt;</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:os.name=Linux</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:os.arch=amd64</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:os.version=2.6.32-358.23.2.el6.x86_64</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:user.name=hadoop</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:user.home=/home/hadoop</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Client environment:user.dir=/home/hadoop</p>
<p>15/03/11 18:19:04 INFO zookeeper.ZooKeeper: Initiating client connection, connectString=localhost:2181 sessionTimeout=90000 watcher=hconnection-0x4b8264fb, quorum=localhost:2181, baseZNode=/hbase</p>
<p>15/03/11 18:19:04 DEBUG zookeeper.ClientCnxn: zookeeper.disableAutoWatchReset is false</p>
<p>15/03/11 18:19:04 INFO zookeeper.RecoverableZooKeeper: Process identifier=hconnection-0x4b8264fb connecting to ZooKeeper ensemble=localhost:2181</p>
<p>15/03/11 18:19:04 INFO zookeeper.ClientCnxn: Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)</p>
<p>15/03/11 18:19:04 INFO zookeeper.ClientCnxn: Socket connection established to localhost/127.0.0.1:2181, initiating session</p>
<p>15/03/11 18:19:04 DEBUG zookeeper.ClientCnxn: Session establishment request sent on localhost/127.0.0.1:2181</p>
<p>15/03/11 18:19:04 INFO zookeeper.ClientCnxn: Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x24bee862026002d, negotiated timeout = 40000</p>
<p>15/03/11 18:19:04 DEBUG zookeeper.ZooKeeperWatcher: hconnection-0x4b8264fb, quorum=localhost:2181, baseZNode=/hbase Received ZooKeeper Event, type=None, state=SyncConnected, path=null</p>
<p>15/03/11 18:19:04 DEBUG zookeeper.ClientCnxn: Reading reply sessionid:0x24bee862026002d, packet:: clientPath:null serverPath:null finished:false header:: 1,3  replyHeader:: 1,4294978417,-101  request:: '/hbase/hbaseid,F  response::  </p>
<p>15/03/11 18:19:04 DEBUG zookeeper.ZooKeeperWatcher: hconnection-0x4b8264fb-0x24bee862026002d connected</p>
<p>15/03/11 18:19:04 INFO client.ZooKeeperRegistry: ClusterId read in ZooKeeper is null</p>
<p>.....................................................................................................................................................</p>
<p> </p>
<p>最后发现两种解决方案</p>
<p>1(不推荐)</p>
<p>  将该机器的 hosts里面的localhost 指向 真实 zk的ip地址(只能是一个临时方案)</p>
<p>vi /etc/hosts</p>
<p><span style="color:#0000ff;">--------------------------------------------------------------------------------------------------------</span></p>
<p>#127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4</p>
<p>#::1         localhost localhost.localdomain localhost6 localhost6.localdomain6</p>
<p>10.1.29.1   l-hdfsgl1.bi.prod.cn1</p>
<p> </p>
<p>10.1.29.1   localhost</p>
<p><span style="color:#0000ff;">-------------------------------------------------------------------------------------------------------</span></p>
<p> </p>
<p>2(推荐)</p>
<p>需要在hive的conf hive-site.xml加入一个hbase zk的属性</p>
<p><span style="color:#ff6600;">------------------------------------------------------------------------------------------------------</span></p>
<p>&lt;property&gt;</p>
<p>     &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;</p>
<p>    &lt;value&gt;l-hdfsgl1.bi.prod.cn1,l-hdfsgl2.bi.prod.cn1,l-hdfscc1.bi.prod.cn1&lt;/value&gt;</p>
<p>      &lt;description&gt;&lt;/description&gt;</p>
<p> &lt;/property&gt;</p>
<p><span style="color:#ff6600;">--------------------------------------------------------------------------------------------------------</span></p>
<p> </p>
<p>退出hive后重新进入执行建表语句即可执行成功。</p>
<p> </p>
<p>   </p>
<p>  </p>
<p> </p>
<p> </p>
<p> </p>
</div>            </div>
                </div>
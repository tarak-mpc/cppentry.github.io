---
layout:     post
title:      Hive学习之Metastore及其配置管理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p></p>
<h1 class="csdn_top" style="font-size:24px;font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:38px;color:rgb(44,48,51);">
Hive学习之Metastore及其配置管理</h1>
<br><p></p>
<p><br></p>
<p></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
在学习Hive的配置管理参数时，曾将参数分为四类：Hive管理参数、Hive元存储（Metastore）管理参数、与Hadoop交互的管理参数、用于传递运行时信息的参数，当时并没有对Metastore参数进行深入的学习，现在就开始学习这部分的内容。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       Hive中表和分区的所有元数据都存储在Hive的元存储（Metastore）中。元数据使用JPOX（Java Persistent Objects）对象关系映射解决方案进行持久化，所以任何被JPOX支持的存储都可以被Hive使用。大多数商业关系型数据库和许多开源的数据存储都被支持，所以就可以被Hive使用存储元数据。Hive支持三种不同的元存储服务器，分别为：内嵌式元存储、本地元存储、远程元存储，每种存储方式使用不同的配置参数，相关的配置参数如下表所示：</p>
<div align="center" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
<table border="1" cellspacing="0" cellpadding="0" width="558" style="border-collapse:collapse;border-spacing:0px;background-color:transparent;"><tbody><tr><td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span>配置参数</span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span>描述</span></p>
</td>
</tr><tr><td valign="top">
<p>
javax.jdo.option.ConnectionURL</p>
</td>
<td valign="top">
<p>
包含元数据的数据存储的JDBC连接字符串。默认值为jdbc:derby:;databaseName=metastore_db;create=true。</p>
</td>
</tr><tr><td valign="top">
<p>
javax.jdo.option.ConnectionDriverName</p>
</td>
<td valign="top">
<p>
包含元数据的数据存储的JDBC驱动类名称。默认值为org.apache.derby.jdbc.EmbeddedDriver。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.uris</p>
</td>
<td valign="top">
<p>
Hive连接到该URI请求远程元存储的元数据。默认值为空。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.local</p>
</td>
<td valign="top">
<p>
本地或者远程数据存储 (Hive-0.10已经不再使用该参数: 如果<code style="font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;font-size:14.4px;color:rgb(199,37,78);background-color:rgb(249,242,244);">hive.metastore.uris</code>为空值则为本地模式，否则为远程模式)。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.warehouse.dir</p>
</td>
<td valign="top">
<p>
本地表的默认位置，默认值为/user/hive/warehouse。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.metadb.dir</p>
</td>
<td valign="top">
<p>
 </p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.rawstore.impl</p>
</td>
<td valign="top">
<p>
实现org.apache.hadoop.hive.metastore.rawstore接口的类，该类用于存储和检索行元数据对象，如表、数据库。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.ds.connection.url.hook</p>
</td>
<td valign="top">
<p>
提取JDO连接URL的钩子名称，若为空，则使用javax.jdo.option.ConnectionURL中的值。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.server.min.threads</p>
</td>
<td valign="top">
<p>
Thrift服务器池中工作线程的最小数量，默认值为200。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.server.max.threads</p>
</td>
<td valign="top">
<p>
Thrift 服务器池中工作线程的最大数量，默认值为10000。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.hmshandler.retry.attempts</p>
</td>
<td valign="top">
<p>
在JDO数据存储出现错误后尝试连接的次数默认值为1。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.hmshandler.retry.interval</p>
</td>
<td valign="top">
<p>
尝试间隔的毫秒数，默认值为1000。</p>
</td>
</tr></tbody></table></div>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       前面提到Hive支持三种元存储方式，默认方式为内嵌式元存储。下面分别对三种存储方式如何配置进行学习。</p>
<h2 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:2.15em;">
<a name="t0" style="color:rgb(79,161,219);background:transparent;"></a>内嵌式元存储</h2>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       内嵌式元存储主要用于单元测试，在该模式下每次只有一个进程可以连接到元存储，Derby是内嵌式元存储的默认数据库。内嵌式元存储的配置如下：</p>
<table border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border-spacing:0px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;"><thead><tr><td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">配置参数</span></span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">值</span></span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">描述</span></span></p>
</td>
</tr></thead><tbody><tr><td valign="top">
<p>
javax.jdo.option.ConnectionURL</p>
</td>
<td valign="top">
<p>
jdbc:derby:;databaseName=metastore_db;create=true</p>
</td>
<td valign="top">
<p>
包含元数据的数据存储的JDBC连接字符串。</p>
</td>
</tr><tr><td valign="top">
<p>
javax.jdo.option.ConnectionDriverName</p>
</td>
<td valign="top">
<p align="left">
org.apache.derby.jdbc.EmbeddedDriver</p>
</td>
<td valign="top">
<p>
JDBC驱动类。</p>
</td>
</tr><tr><td valign="top">
<p>
hive.metastore.warehouse.dir</p>
</td>
<td valign="top">
<p align="left">
/user/hive/warehouse</p>
</td>
<td valign="top">
<p>
本地表的默认位置。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
hive.metastore.uris</p>
</td>
<td valign="top">
<p>
空值</p>
</td>
<td valign="top">
<p>
内嵌式也是本地模式，所以为空值。</p>
</td>
</tr></tbody></table><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
 </p>
<h2 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:2.15em;">
<a name="t1" style="color:rgb(79,161,219);background:transparent;"></a>本地元存储</h2>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       在本地模式下，每个Hive客户端都会打开到数据存储的连接并在该连接上请求SQL查询。下表展示了如何在MySQL数据库服务器中建立元存储，确保在Hive查询被执行的机器上是可以访问该数据库服务器的，同时确保JDBC类库在Hive客户端的类路径中。</p>
<table border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border-spacing:0px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;"><thead><tr><td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">配置参数</span></span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">值</span></span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">描述</span></span></p>
</td>
</tr></thead><tbody><tr><td valign="top">
<p align="left">
javax.jdo.option.ConnectionURL</p>
</td>
<td valign="top">
<p>
jdbc:mysql://&lt;host name&gt;/&lt;database name&gt;?createDatabaseIfNotExist=true</p>
</td>
<td valign="top">
<p>
元数据存储在MySQL数据库中。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
javax.jdo.option.ConnectionDriverName</p>
</td>
<td valign="top">
<p>
com.mysql.jdbc.Driver</p>
</td>
<td valign="top">
<p>
MySQL JDBC驱动类。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
javax.jdo.option.ConnectionUserName</p>
</td>
<td valign="top">
<p align="left">
&lt;user name&gt;</p>
</td>
<td valign="top">
<p>
连接MySQL的用户名。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
javax.jdo.option.ConnectionPassword</p>
</td>
<td valign="top">
<p align="left">
&lt;password&gt;</p>
</td>
<td valign="top">
<p align="left">
连接MySQL的密码。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
hive.metastore.uris</p>
</td>
<td valign="top">
<p align="left">
空值</p>
</td>
<td valign="top">
<p align="left">
本地模式下为空值。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
hive.metastore.warehouse.dir</p>
</td>
<td valign="top">
<p>
/user/hive/warehouse</p>
</td>
<td valign="top">
<p>
Hive表的默认位置。</p>
</td>
</tr></tbody></table><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
 </p>
<h2 style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-weight:100;line-height:1.1;color:rgb(69,69,69);font-size:2.15em;">
<a name="t2" style="color:rgb(79,161,219);background:transparent;"></a>远程元存储</h2>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       在远程模式下，所有的Hive客户端都将打开一个到元数据服务器的连接，该服务器依次查询元数据。元数据服务器和客户端之间使用Thrift协议通信，从Hive-0.5.0开始，可以执行下面的命令启动Thrift服务器：</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       hive--service metastore</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       下面以MySQL为例，分别学习服务器端和客户端的配置，服务器端的配置如下表所示：</p>
<table border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border-spacing:0px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;"><thead><tr><td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">配置参数</span></span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">值</span></span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">描述</span></span></p>
</td>
</tr></thead><tbody><tr><td valign="top">
<p align="left">
javax.jdo.option.ConnectionURL</p>
</td>
<td valign="top">
<p>
jdbc:mysql://&lt;host name&gt;/&lt;database name&gt;?createDatabaseIfNotExist=true</p>
</td>
<td valign="top">
<p>
元数据存储在MySQL数据库中。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
javax.jdo.option.ConnectionDriverName</p>
</td>
<td valign="top">
<p>
com.mysql.jdbc.Driver</p>
</td>
<td valign="top">
<p>
MySQL JDBC驱动类。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
javax.jdo.option.ConnectionUserName</p>
</td>
<td valign="top">
<p align="left">
&lt;user name&gt;</p>
</td>
<td valign="top">
<p>
连接MySQL的用户名。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
javax.jdo.option.ConnectionPassword</p>
</td>
<td valign="top">
<p align="left">
&lt;password&gt;</p>
</td>
<td valign="top">
<p align="left">
连接MySQL的密码。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
hive.metastore.warehouse.dir</p>
</td>
<td valign="top">
<p>
/user/hive/warehouse</p>
</td>
<td valign="top">
<p>
Hive表的默认位置。</p>
</td>
</tr></tbody></table><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       客户端的配置为：</p>
<table border="1" cellspacing="0" cellpadding="0" style="border-collapse:collapse;border-spacing:0px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;"><thead><tr><td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">配置参数</span></span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">值</span></span></p>
</td>
<td valign="top" style="background:rgb(240,240,240);">
<p align="center">
<span><span style="font-weight:normal;color:rgb(51,51,51);">描述</span></span></p>
</td>
</tr></thead><tbody><tr><td valign="top">
<p align="left">
hive.metastore.uris</p>
</td>
<td valign="top">
<p>
thrift://&lt;host_name&gt;:&lt;port&gt;</p>
</td>
<td valign="top">
<p>
Thrift元存储服务器的主机和端口号。</p>
</td>
</tr><tr><td valign="top">
<p align="left">
hive.metastore.warehouse.dir</p>
</td>
<td valign="top">
<p>
/user/hive/warehouse</p>
</td>
<td valign="top">
<p>
Hive表的默认位置。</p>
</td>
</tr></tbody></table><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
       在启动客户端和元存储服务器之前需要将JDBC驱动类库放到$HIVE_HOME/lib目录下。</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
<br></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
<br></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:24px;">
<br></p>
<br>            </div>
                </div>
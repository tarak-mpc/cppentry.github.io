---
layout:     post
title:      hive remote安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hive安装需要使用mysql，mysql安装参考上一篇。</p>
<p>Mysql配置</p>
-- 创建 hive 数据库<br>
mysql&gt; CREATE DATABASE hive;<br><br><br>
-- 创建 hive 用户，并赋予访问 hive 数据库的权限<br>
grant select on 数据库.* to 用户名@登录主机 identified by “密码”<br>
(优先选择第三句）<br>
grant all privileges on *.* to 'hive'@'192.168.0.104' identified by  'hive'  with grant option; <br>
grant all privileges on *.* to  hive@'%' identified by  'hive' ;<br>
grant all privileges on *.* to 'hive'@'%' identified by 'hive' with grant option;<br><br><br>
mysql&gt; FLUSH PRIVILEGES;<br><br><br>
(下面六句与配置无关）<br>
&lt;--设置 binary log 的格式：<br>
mysql&gt; set global binlog_format=MIXED;<br>
--查看mysql端口配置<br>
show global variables like 'port';<br>
收回权限<br>
REVOKE ALL PRIVILEGES ON *.* FROM hive; --&gt;<br><br><br>
1、进入目录<br>
/usr/soft/hive<br>
2、vi /etc/environment<br>
HIVE_HOME=/usr/soft/hive/apache-hive-1.2.2-bin<br><br><br>
path增加：<br>
:$HIVE_HOME/bin:$HIVE_HOME/hcatalog/bin:$HIVE_HOME/hcatalog/sbin<br><br><br>
3、配置HIVE<br>
cd /usr/soft/hive/apache-hive-1.2.2-bin<br>
cp conf/hive-default.xml.template conf/hive-site.xml<br>
cp conf/hive-env.sh.template conf/hive-env.sh<br>
cp conf/hive-exec-log4j.properties.template conf/hive-exec-log4j.properties<br>
cp conf/hive-log4j.properties.template conf/hive-log4j.properties<br><br><br>
修改hive-env.sh<br>
(1)添加参数(对应自己的JDK、HADOOP、HIVE的目录<br>
export JAVA_HOME=/usr/soft/jdk1.8.0_121<br>
export HADOOP_HOME=/usr/soft/hadoop-2.8.0<br>
export HIVE_HOME=/usr/soft/hive/apache-hive-1.2.2-bin<br>
export HIVE_CONF_DIR=$HIVE_HOME/conf<br><br><br><br><br>
修改：<br>
vi hive-log4j.properties<br>
hive.log.dir=/usr/soft/hive<br>
----<br>
vi hcat_server.sh<br>
HCAT_LOG_DIR=/usr/soft/hive/logs<br><br><br><br><br>
hive.metastore.warehouse.dir<br>
使用的目录，启动hadoop，然后在hadoop上创建(自己设定，后续配置更改相应目录就行)<br>
hadoop fs -mkdir /tmp<br>
hadoop fs -mkdir -p /data/hive/warehouse<br>
hadoop fs -mkdir -p /data/hive/log<br>
hadoop fs -chmod g+w /data/hive/warehouse<br>
hadoop fs -chmod g+w /data/hive/log<br>
hadoop fs -chmod g+w /tmp<br><br><br>
配置hive-site.xml<br>
服务端和配置端区别:<br><br><br>
服务端配置文件：<br>
&lt;?xml version="1.0" encoding="UTF-8" standalone="no"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br><br><br>
&lt;configuration&gt;<br><br><br>
&lt;property&gt;<br>
&lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;<br>
&lt;value&gt;/data/hive/warehouse&lt;/value&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
&lt;value&gt;jdbc:mysql://127.0.0.1:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;<br>
&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
&lt;value&gt;hive&lt;/value&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;<br>
&lt;value&gt;hive&lt;/value&gt;<br>
&lt;/property&gt;<br>
&lt;/configuration&gt;<br><br><br><br><br>
2）、客户端配置文件<br>
&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br><br><br>
&lt;configuration&gt;<br><br><br>
&lt;property&gt;<br>
&lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;<br>
&lt;value&gt;/data/hive/warehouse&lt;/value&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
&lt;name&gt;hive.metastore.local&lt;/name&gt;<br>
&lt;value&gt;false&lt;/value&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
&lt;name&gt;hive.metastore.uris&lt;/name&gt;<br>
&lt;value&gt;thrift://ubuntu1:9083&lt;/value&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;/configuration&gt;<br><br><br><br><br>
我配置的是当前环境变量/etc/environment，以后每次启动都需要运行下<br>
source /etc/environment<br><br><br>
初始化数据库<br>
schematool -initSchema -dbType mysql<br><br><br>
初始化成功后，以后可以直接使用和操作<br>
hive<br><br><br><br>            </div>
                </div>
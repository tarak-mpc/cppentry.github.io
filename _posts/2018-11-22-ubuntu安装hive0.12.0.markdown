---
layout:     post
title:      ubuntu安装hive0.12.0
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<br><br>
本机环境：<br>
ubuntu 12.04<br>
hadoop 1.1.2<br>
mysql 5.5.35 <br>
安装hive版本0.12.0<br><br><br>
一、创建用户hive，并授权:<br>
root用户登录mysql创建hive用户并授权，执行命令：<br>
use mysql;<br>
insert into user(Host,User,Password) values("localhost","hive",password("hive"));<br>
FLUSH PRIVILEGES;<br><br><br>
GRANT ALL PRIVILEGES ON *.*  TO 'hive'@'localhost' IDENTIFIED BY 'hive';<br>
FLUSH PRIVILEGES;<br><br><br>
二、安装配置hive<br>
1.下载hive-0.12.0.tar.gz<br>
2.解压tar -xzvf hive-0.12.0.tar.gz（我的hive文件放在/usr/local下）<br>
3.配置环境变量，在etc/profile文件末尾添加以下内容：<br>
export HIVE_HOME=/usr/localhive-0.12.0<br>
export PATH=$HIVE_HOME/bin:$PATH<br>
执行source /etc/profile更新环境变量<br>
4.拷贝模板配置文件并修改<br>
[root@hadoop1 hive-0.12.0]# cd conf/<br>
[root@hadoop1 conf]# cp hive-env.sh.template hive-env.sh<br>
[root@hadoop1 conf]# cp hive-default.xml.template hive-site.xml<br>
5.修改hive-env.sh文件，指定HADOOP_HOME及HIVE_CONF_DIR的路径如下： <br>
HADOOP_HOME=/usr/local/hadoop <br>
export HIVE_CONF_DIR=/usr/local/hive-0.12.0/conf<br>
6.修改hive-site.xml文件，指定MySQL数据库驱动、数据库名、用户名及密码，修改的内容如下所示：<br>
&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
  &lt;value&gt;jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;<br>
  &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;<br>
  &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;<br>
  &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
  &lt;value&gt;hive&lt;/value&gt;<br>
  &lt;description&gt;username to use against metastore database&lt;/description&gt;<br>
&lt;/property&gt;<br><br><br>
&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;<br>
  &lt;value&gt;hive&lt;/value&gt;<br>
  &lt;description&gt;password to use against metastore database&lt;/description&gt;<br>
&lt;/property&gt;<br><br><br><p>7.拷贝MySQL驱动mysql-connector-java-5.1.30.jar到hive-0.12.0/lib目录中</p>
<p></p>
<p style="font-family:Helvetica, Arial, sans-serif;font-size:14px;line-height:22.68000030517578px;">
8.在HDFS中创建/tmp和/user/hive/warehouse并设置权限</p>
<div id="highlighter_164706" class="syntaxhighlighter" style="font-size:14px;border:0px !important;vertical-align:baseline !important;line-height:1.1em !important;font-family:Consolas, 'Bitstream Vera Sans Mono', 'Courier New', Courier, monospace !important;min-height: !important;">
<div class="lines" style="border:0px !important;vertical-align:baseline !important;line-height:1.1em !important;font-size:1em !important;min-height: !important;">
<div class="line alt1" style="border:0px !important;vertical-align:baseline !important;line-height:1.1em !important;font-size:1em !important;min-height: !important;">
[rot@hadoop1 bin]# pwd<br>
/work/hadoop/hadoop-1.0.4/bin<br>
[root@hadoop1 bin]# ./hadoop fs -mkdir /tmp<br>
[root@hadoop1 bin]# ./hadoop fs -mkdir /user/hive/warehouse<br>
[root@hadoop1 bin]# ./hadoop fs -chmod g+w /tmp<br>
[root@hadoop1 bin]# ./hadoop fs -chmod g+w /user/hive/warehouse<br></div>
</div>
</div>
<br>
9.测试hive<br>
zcf@zcf-K42JZ:~$ hive<br><br><br>
Logging initialized using configuration in jar:file:/usr/local/hive-0.12.0/lib/hive-common-0.12.0.jar!/hive-log4j.properties<br>
hive&gt; show tables;<br>
OK<br>
Time taken: 3.578 seconds<br><br><br>
安装成功。<br>
三、错误及解决<br><br><br>
1.hive&gt; show tables;<br>
FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.metastore.HiveMetaStoreClient<br>
导致这个错误的原因很多，有必要查看详细错误信息。退出hive,以debug模式启动hive并将信息显示到控制台：hive -hiveconf hive.root.logger=DEBUG,console<br>
发现以上错误是由Caused by: MetaException(message:Version information not found in metastore. )导致的。解决方法将hive-site.xml 里面 hive.metastore.schema.verification 的值改为 false后，就没出现错误了。<br>
2.[Fatal Error] hive-site.xml:2000:16: The element type "value" must be terminated by the matching end-tag "&lt;/value&gt;".<br>
14/04/14 19:34:36 FATAL conf.Configuration: error parsing conf file: org.xml.sax.SAXParseException: The element type "value" must be terminated by the matching end-tag "&lt;/value&gt;".报错很明显hive-site.xml 2000行有错，查看发现2000行竟是这样的&lt;value&gt;auth&lt;/auth&gt;能没错吗，将&lt;/auth&gt;改为&lt;/value&gt;错误解决。<br><br>            </div>
                </div>
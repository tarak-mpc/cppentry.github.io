---
layout:     post
title:      使用Sqoop把Oracle表导入Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>安装环境：</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Hive: hive-0.11.0</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><a href="http://www.linuxidc.com/topicnews.aspx?tid=13" rel="nofollow" title="Hadoop" style="color:rgb(179,43,213);">Hadoop</a>: Hadoop 1.2.1</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Sqoop: sqoop-1.4.4.bin__hadoop-1.0.0</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Oralce driver: ojdbc6.jar</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>软件下载：</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Hive: <a href="http://apache.dataguru.cn/hive/" rel="nofollow" style="color:rgb(179,43,213);">http://apache.dataguru.cn/hive/</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Hadoop: <a href="http://apache.dataguru.cn/hadoop/" rel="nofollow" style="color:rgb(179,43,213);">http://apache.dataguru.cn/hadoop/</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Sqoop: <a href="http://apache.dataguru.cn/sqoop/" rel="nofollow" style="color:rgb(179,43,213);">http://apache.dataguru.cn/sqoop/</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Ojdbc: <a href="http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html" rel="nofollow" style="color:rgb(179,43,213);">http://www.oracle.com/technetwork/database/features/jdbc/index-091264.html</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>操作步骤：</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">前提确保hadoop已经启动</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>1. 安装sqoop</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">--解压</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">tar zvxf sqoop-1.4.4.bin__hadoop-1.0.0.tar.gz</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">cp -r sqoop-1.4.4.bin__hadoop-1.0.0/app/hadoop</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">chown -Rlicz:hadoop /app/hadoop/sqoop-1.4.4.bin__hadoop-1.0.0</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>2. 复制<a href="http://www.linuxidc.com/topicnews.aspx?tid=12" rel="nofollow" title="Oracle" style="color:rgb(179,43,213);">Oracle</a> JDBC的驱动放到Sqoop的lib文件夹下</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">cp ojdbc6.jar/app/hadoop/sqoop-1.4.4.bin__hadoop-1.0.0/lib</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>3. 修改环境变量</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">[licz@nticket1~]$ vi .bash_profile</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">export HADOOP_HOME=/app/hadoop/hadoop-1.2.1</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">export JAVA_HOME=/usr/java/jdk1.6.0_18</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">export HIVE_HOME=/app/hadoop/hive-0.11.0</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>export SQOOP_HOME=/app/hadoop/sqoop-1.4.4.bin__hadoop-1.0.0</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">PATH=$JAVA_HOME/bin:$PATH:$HOME/bin:$HADOOP_HOME/bin:$PIG_HOME/bin:$HIVE_HOME/bin:$SQOOP_HOME/bin</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">export PATH</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>4. 导入oracle数据</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">--导入方法</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">$ sqoop import--hive-import --connect jdbc:oracle:thin:@192.168.56.111:1521:DBNAME --username USERNAME --password PASSWORD --verbose -m 1 --table TABLENAME</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">说明：</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">DBNAME：数据库实例名</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">USERNAME：用户名</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">PASSWORD：密码</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">TABLENAME：表名</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">-m：导入数据的进程的并发数，默认是4。如果导入的数据不大的话，不妨设置成1，这样导入更快。一般来说Sqoop会使用主键来平均地分割数据。并发导入的时候可以设置相关的分割列等等，具体的做法参考官方的文档。</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">官方文档：</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<a href="http://sqoop.apache.org/docs/1.4.4/index.html" rel="nofollow" style="color:rgb(179,43,213);background-color:rgb(255,255,255);">http://sqoop.apache.org/docs/1.4.4/index.html</a></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">参数说明参考：<a href="http://www.linuxidc.com/Linux/2014-02/96677.htm" rel="nofollow" style="color:rgb(179,43,213);">http://www.linuxidc.com/Linux/2014-02/96677.htm</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>注意:</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">l <strong><span style="color:#FF0000;">用户名和表名</span></strong>必需是大写的，除非它们在创建的时候是名字是放在引号中的大小写混合的形式。</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">l Sqoop会把Oracle中的表自动在hive中创建，不用手工创建</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">--导入实例</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">sqoop import--hive-import --connect jdbc:oracle:thin:@172.16.80.115:1521:CUSTOMER2--username WEBMEMBER --password webmem123 --verbose -m 1 --table MEMBERS</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<img src="http://www.linuxidc.com/upload/2014_02/140215192334161.jpg" alt="" width="581" vspace="5" style="border:0px;display:block;"></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>5. 增量导入</strong></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">另外一个问题，如果我们要导入某个表中的新加入的部分，可以使用--check-column、--incremental、--last-value三个参数，--check-column指定要检查的列，--incremental指定某种增加的模式，只有两个合法的值，append 和lastmodified。如果--incremental为append，则Sqoop会导入--check-column指定的列的值大于--last-value所指定的值的记录。如果--incremental为lastmodified，则Sqoop会导入--check-column指定的列的值（这是一个时间戳）近于--last-value所指定的时间戳的记录。</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">例如下面的命令可以导入列ID的值大于4的所有记录。</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">$ sqoop import--hive-import --connect jdbc:oracle:thin:@192.168.56.111:1521:DBNAME --usernameUSERNAME --password PASSWORD --verbose -m 1 --table TABLENAME --check-column ID--incremental append --last-value 4</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>遇到的问题</strong>：</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">1.导入hive时报错</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="color:#FF0000;background-color:rgb(255,255,255);">ERROR tool.ImportTool:Imported Failed: Attempted to generate class with no columns</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"> </span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">原因和解决办法：</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">是因导入时表名或用户名没有大写所致</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">sqoop import --hive-import --connectjdbc:oracle:thin:@172.16.80.115:1521:CUSTOMER2 --username WEBMEMBER --passwordwebmem123 --verbose -m 1 --table<span style="color:#FF0000;">members</span></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><span style="color:rgb(255,0,0);"><strong>Sqoop 的详细介绍</strong></span>：<a title="Sqoop" href="http://www.linuxidc.com/Linux/2012-04/58022.htm" rel="nofollow" style="color:rgb(179,43,213);">请点这里</a><br><span style="color:rgb(255,0,0);"><strong>Sqoop 的下载地址</strong></span>：<a href="http://www.linuxidc.com/down.aspx?id=559" rel="nofollow" style="color:rgb(179,43,213);">请点这里</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);"><strong>相关阅读</strong>：</span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">通过Sqoop实现Mysql / Oracle 与HDFS / Hbase互导数据 <a href="http://www.linuxidc.com/Linux/2013-06/85817.htm" rel="nofollow" style="color:rgb(179,43,213);">http://www.linuxidc.com/Linux/2013-06/85817.htm</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">[Hadoop] Sqoop安装过程详解 <a href="http://www.linuxidc.com/Linux/2013-05/84082.htm" rel="nofollow" style="color:rgb(179,43,213);">http://www.linuxidc.com/Linux/2013-05/84082.htm</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">用Sqoop进行MySQL和HDFS系统间的数据互导 <a href="http://www.linuxidc.com/Linux/2013-04/83447.htm" rel="nofollow" style="color:rgb(179,43,213);">http://www.linuxidc.com/Linux/2013-04/83447.htm</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Hadoop Oozie学习笔记 Oozie不支持Sqoop问题解决 <a href="http://www.linuxidc.com/Linux/2012-08/67027.htm" rel="nofollow" style="color:rgb(179,43,213);">http://www.linuxidc.com/Linux/2012-08/67027.htm</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Hadoop生态系统搭建（hadoop hive hbase zookeeper oozie Sqoop） <a href="http://www.linuxidc.com/Linux/2012-03/55721.htm" rel="nofollow" style="color:rgb(179,43,213);">http://www.linuxidc.com/Linux/2012-03/55721.htm</a></span></p>
<p style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;line-height:22px;text-align:justify;">
<span style="background-color:rgb(255,255,255);">Hadoop学习全程记录——使用Sqoop将MySQL中数据导入到Hive中 <a href="http://www.linuxidc.com/Linux/2012-01/51993.htm" rel="nofollow" style="color:rgb(179,43,213);">http://www.linuxidc.com/Linux/2012-01/51993.htm</a></span></p>
            </div>
                </div>
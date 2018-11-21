---
layout:     post
title:      1002-将Hive的默认数据库Derby改为MySQL
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<div><span style="font-family:'仿宋';">一、MySQL的安装</span></div>
<span><span style="font-family:'仿宋';">1、上传服务端和客户端安装文件</span></span>
<div><span style="font-family:'仿宋';">上传文件</span></div>
<div><span style="font-family:'仿宋';">MySQL-client-5.1.73-1.glibc23.i386.rpm</span></div>
<div><span style="font-family:'仿宋';">MySQL-server-5.1.73-1.glibc23.i386.rpm</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">2、安装服务端和客户端</span></div>
<div><span style="font-family:'仿宋';">2.1 安装服务端<br><strong>[hadoop@cloud04 ~]$ sudo rpm -ivh MySQL-server-5.1.73-1.glibc23.i386.rpm</strong> </span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">[sudo] password for hadoop:<br>
Preparing...                ########################################### [100%]<br>
     file /usr/share/mysql/charsets/README from install of <span style="color:#FF0000;">MySQL-server-5.1.73-1.glibc23.i386</span> conflicts with file from package <span style="color:#FF0000;">mysql-libs-5.1.66-2.el6_3.i686</span><br>
     file /usr/share/mysql/charsets/Index.xml from install of MySQL-server-5.1.73-1.glibc23.i386 conflicts with file from package mysql-libs-5.1.66-2.el6_3.i686<br>
     file /usr/share/mysql/charsets/armscii8.xml from install of MySQL-server-5.1.73-1.glibc23.i386 conflicts with file from package mysql-libs-5.1.66-2.el6_3.i68</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">发现 <span>mysql-libs-5.1.66-2.el6_3.i686</span>文件存在冲突。解决冲突的步骤： </span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">#检查冲突</span></div>
<div><span style="font-family:'仿宋';"><strong>[hadoop@cloud04 ~]$ sudo rpm -qa | grep mysql</strong><br>
mysql-libs-5.1.66-2.el6_3.i686</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">#删除冲突文件</span></div>
<div><strong><span style="font-family:'仿宋';">[hadoop@cloud04 ~]$ sudo rpm -e mysql-libs-5.1.66-2.el6_3.i686 --nodeps</span></strong></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">#重新执行安装</span></div>
<div><span style="font-family:'仿宋';"><strong>[hadoop@cloud04 ~]$ sudo rpm -ivh MySQL-server-5.1.73-1.glibc23.i386.rpm</strong><br>
Preparing...                ########################################### [100%]<br>
   1:MySQL-server           ########################################### [100%]<br><br>
PLEASE REMEMBER TO SET A PASSWORD FOR THE MySQL root USER !<br>
To do so, start the server, then issue the following commands:<br><br>
/usr/bin/mysqladmin -u root password 'new-password'<br>
/usr/bin/mysqladmin -u root -h cloud04 password 'new-password'<br><br>
Alternatively you can run:<br><strong>/usr/bin/mysql_secure_installation</strong><br><br>
which will also give you the option of removing the test<br>
databases and anonymous user created by default.  This is<br>
strongly recommended for production servers.<br><br>
See the manual for more instructions.<br><br>
Please report any problems with the /usr/bin/mysqlbug script!<br><br>
Starting MySQL.. SUCCESS!</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">2.2 安装客户端</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><strong><span style="font-family:'仿宋';"> [hadoop@cloud04 ~]$ sudo rpm -ivh MySQL-client-5.1.73-1.glibc23.i386.rpm </span></strong></div>
<span><span style="background-color:rgb(255,255,255);">Preparing...                ########################################### [100%]</span><br style="background-color:rgb(255,255,255);"><span style="background-color:rgb(255,255,255);">   1:MySQL-client           ########################################### [100%</span></span>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><br><br>
3、设置MySQL的密码<br><strong>[hadoop@cloud04 ~]$ /usr/bin/mysql_secure_installation</strong></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL<br>
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!<br><br><br>
In order to log into MySQL to secure it, we'll need the current<br>
password for the root user.  If you've just installed MySQL, and<br>
you haven't set the root password yet, the password will be blank,<br>
so you should just press enter here.<br><br>
Enter current password for root (enter for none):<br>
OK, successfully used password, moving on...<br><br>
Setting the root password ensures that nobody can log into the MySQL<br>
root user without the proper authorisation.<br><br><strong>Set root password? [Y/n] Y</strong><br>
New password:<br>
Re-enter new password:<br>
Password updated successfully!<br>
Reloading privilege tables..<br>
... Success!<br><br><br>
By default, a MySQL installation has an anonymous user, allowing anyone<br>
to log into MySQL without having to have a user account created for<br>
them.  This is intended only for testing, and to make the installation<br>
go a bit smoother.  You should remove them before moving into a<br>
production environment.<br><br><strong>Remove anonymous users? [Y/n] Y</strong><br>
... Success!<br><br>
Normally, root should only be allowed to connect from 'localhost'.  This<br>
ensures that someone cannot guess at the root password from the network.<br><br><strong>Disallow root login remotely? [Y/n] n</strong><br>
... skipping.<br><br>
By default, MySQL comes with a database named 'test' that anyone can<br>
access.  This is also intended only for testing, and should be removed<br>
before moving into a production environment.<br><br><strong>Remove test database and access to it? [Y/n] Y</strong><br>
- Dropping test database...<br>
... Success!<br>
- Removing privileges on test database...<br>
... Success!<br><br>
Reloading the privilege tables will ensure that all changes made so far<br>
will take effect immediately.<br><br><strong>Reload privilege tables now? [Y/n] Y</strong><br>
... Success!<br><br>
Cleaning up...<br><br>
All done!  If you've completed all of the above steps, your MySQL<br>
installation should now be secure.<br><br>
Thanks for using MySQL</span></div>
<div><span style="font-family:'仿宋';"><br></span>
<div><span style="font-family:'仿宋';">4、登录MySQL</span></div>
<div><span style="font-family:'仿宋';">用户名称： root，密码： 123</span></div>
<div><span style="font-family:'仿宋';">[hadoop@cloud04 ~]$ mysql -u<span style="color:#FF0000;">root</span> -p<span style="color:#FF0000;">123</span></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">二、<span style="line-height:26px;text-align:left;">配置Metastore用mysql数据库</span></span></div>
<div><span style="font-family:'仿宋';">1、在/home/hadoop/app/hive-0.12.0/conf目录下配置<strong style="color:rgb(227,0,0);"> hive-site.xml</strong></span></div>
<div><span style="font-family:'仿宋';"><span style="color:#E30000;"><strong>[hadoop@cloud04 conf]$ vi hive-site.xml</strong></span><br></span></div>
<div><span style="font-family:'仿宋';">&lt;configuration&gt;<br></span></div>
</div>
<blockquote>
<div><span style="font-family:'仿宋';">&lt;property&gt;</span></div>
<div><span style="font-family:'仿宋';">     &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</span></div>
<div><span style="font-family:'仿宋';">     &lt;value&gt;jdbc:mysql://cloud04:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;</span></div>
<div><span style="font-family:'仿宋';">&lt;/property&gt;</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">&lt;property&gt;</span></div>
<div><span style="font-family:'仿宋';">     &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</span></div>
<div><span style="font-family:'仿宋';">     &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</span></div>
<div><span style="font-family:'仿宋';">&lt;/property&gt;</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">&lt;property&gt;</span></div>
<div><span style="font-family:'仿宋';">     &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</span></div>
<div><span style="font-family:'仿宋';">     &lt;value&gt;root&lt;/value&gt;</span></div>
<div><span style="font-family:'仿宋';">&lt;/property&gt;</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">&lt;property&gt;</span></div>
<div><span style="font-family:'仿宋';">     &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</span></div>
<div><span style="font-family:'仿宋';">     &lt;value&gt;123&lt;/value&gt;</span></div>
<div><span style="font-family:'仿宋';">&lt;/property&gt;</span></div>
</blockquote>
<div><span style="font-family:'仿宋';">&lt;/configuration&gt;</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">注意： 通过Hive创建的表存储在什么位置？ Hive创建的数据库存储在HDFS上，Hive创建的表存储在TLBS表中，另外数据存储在什么位置呢？</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">2、登录hive</span></div>
<div><span style="font-family:'仿宋';color:#E30000;"><strong>[hadoop@cloud04 bin]$ ./hive</strong></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">3、登录hive后进行如下操作</span></div>
<div><span style="font-family:'仿宋';"><strong>hive&gt; show databases;</strong><br>
OK<br>
default<br>
Time taken: 0.037 seconds, Fetched: 1 row(s)<br><strong>hive&gt; create table hive02(id int,name string);</strong><br>
OK<br>
Time taken: 0.834 seconds<br><strong>hive&gt; drop table hive02;</strong><br>
OK<br>
Time taken: 0.697 seconds<br><strong>hive&gt; show databases;</strong><br>
OK<br>
default<br>
Time taken: 0.031 seconds, Fetched: 1 row(s)<br><strong>hive&gt; create database jf01; </strong><br>
OK<br>
Time taken: 0.048 seconds<br><strong>hive&gt; use jf01;</strong><br>
OK<br>
Time taken: 0.027 seconds<br><strong>hive&gt; create table t_order (id int ,name string);</strong><br>
OK<br>
Time taken: 0.062 seconds<br><strong>hive&gt; select * from t_order;</strong><br>
OK<br>
Time taken: 0.341 seconds</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">可以发现  <strong>jf01，t_order 元数据存储在hdfs上，在mysql上也会存储</strong></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';">三、登录MySQL后，查看相关数据</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><strong><span style="font-family:'仿宋';">[hadoop@cloud04 hive-0.12.0]$ mysql -uroot -p123;</span></strong></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><strong>mysql&gt; select * from DBS;</strong><br>
+-------+-----------------------+----------------------------------------+---------+<br>
| DB_ID | DESC                  | DB_LOCATION_URI                        | NAME    |<br>
+-------+-----------------------+----------------------------------------+---------+<br>
|     1 | Default Hive database | <strong>hdfs://ns1/user/hive/warehouse</strong>                  | default |<br>
|     2 | NULL                          | <strong>hdfs://ns1/user/hive/warehouse/jf01.db</strong>      | <strong>jf01   </strong> |<br>
+-------+-----------------------+----------------------------------------+---------+<br>
2 rows in set (0.00 sec)</span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><br></span></div>
<div><span style="font-family:'仿宋';"><strong>mysql&gt; select * from TBLS;</strong><br>
+--------+-------------+-------+------------------+--------+-----------+-------+----------+---------------+--------------------+--------------------+<br>
| TBL_ID | CREATE_TIME | DB_ID | LAST_ACCESS_TIME | OWNER  | RETENTION | SD_ID | TBL_NAME | TBL_TYPE      | VIEW_EXPANDED_TEXT | VIEW_ORIGINAL_TEXT |<br>
+--------+-------------+-------+------------------+--------+-----------+-------+----------+---------------+--------------------+--------------------+<br>
|      2 |  1430147252 |     2 |                0 | hadoop |         0 |     2 | <strong>t_order</strong>  | MANAGED_TABLE | NULL               | NULL               |<br>
+--------+-------------+-------+------------------+--------+-----------+-------+----------+---------------+--------------------+--------------------+<br>
1 row in set (0.00 sec)</span></div>
</div>
            </div>
                </div>
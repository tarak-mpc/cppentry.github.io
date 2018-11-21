---
layout:     post
title:      hive  数据源 使用mysql; hive 启动报错; 加载数据  建表等基本命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hzdxw/article/details/51963621				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-sql">-bash: se: command not found
[root@cdh1 local]# service mysqld status
mysqld is stopped
[root@cdh1 local]# service mysqld start
Starting mysqld:                                           [  OK  ]
[root@cdh1 local]# service mysqld status
mysqld (pid  16760) is running...
[root@cdh1 local]# mysql -u root -p
Enter password: 
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
[root@cdh1 local]# mysql -u root -p
Enter password: 
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
[root@cdh1 local]# /usr/bin/mysql_secure_installation

NOTE: RUNNING ALL PARTS OF THIS SCRIPT IS RECOMMENDED FOR ALL MySQL
      SERVERS IN PRODUCTION USE!  PLEASE READ EACH STEP CAREFULLY!

In order to log into MySQL to secure it, we'll need the current
password for the root user.  If you've just installed MySQL, and
you haven't set the root password yet, the password will be blank,
so you should just press enter here.

Enter current password for root (enter for none): 
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
Enter current password for root (enter for none): 【回车】
OK, successfully used password, moving on...

Setting the root password ensures that nobody can log into the MySQL
root user without the proper authorisation.

Set root password? [Y/n] y
New password: 
Re-enter new password: 
Password updated successfully!
Reloading privilege tables..
 ... Success!


By default, a MySQL installation has an anonymous user, allowing anyone
to log into MySQL without having to have a user account created for
them.  This is intended only for testing, and to make the installation
go a bit smoother.  You should remove them before moving into a
production environment.

Remove anonymous users? [Y/n] n
 ... skipping.

Normally, root should only be allowed to connect from 'localhost'.  This
ensures that someone cannot guess at the root password from the network.

Disallow root login remotely? [Y/n] n
 ... skipping.

By default, MySQL comes with a database named 'test' that anyone can
access.  This is also intended only for testing, and should be removed
before moving into a production environment.

Remove test database and access to it? [Y/n] n
 ... skipping.

Reloading the privilege tables will ensure that all changes made so far
will take effect immediately.

Reload privilege tables now? [Y/n] y
 ... Success!

Cleaning up...



All done!  If you've completed all of the above steps, your MySQL
installation should now be secure.

Thanks for using MySQL!


[root@cdh1 local]# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 9
Server version: 5.1.47 Source distribution

Copyright (c) 2000, 2010, Oracle and/or its affiliates. All rights reserved.
This software comes with ABSOLUTELY NO WARRANTY. This is free software,
and you are welcome to modify and redistribute it under the GPL v2 license

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql&gt; show tables; 
ERROR 1046 (3D000): No database selected
mysql&gt; show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| test               |
+--------------------+
3 rows in set (0.00 sec)

mysql&gt; create database hivedb;
Query OK, 1 row affected (0.00 sec)

mysql&gt; create user 'hiveuser' identified by 'hivepwd';
Query OK, 0 rows affected (0.00 sec)

mysql&gt; select user();
+----------------+
| user()         |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)

mysql&gt; grant all privileges on *.* to hiveuser@"localhost" identified by "hivepwd" with grant option;
Query OK, 0 rows affected (0.00 sec)

mysql&gt; select user();
+----------------+
| user()         |
+----------------+
| root@localhost |
+----------------+
1 row in set (0.00 sec)

mysql&gt; exit;
Bye
[root@cdh1 local]# mysql -u hiveuser -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 10
Server version: 5.1.47 Source distribution

Copyright (c) 2000, 2010, Oracle and/or its affiliates. All rights reserved.
This software comes with ABSOLUTELY NO WARRANTY. This is free software,
and you are welcome to modify and redistribute it under the GPL v2 license

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql&gt; select user();
+--------------------+
| user()             |
+--------------------+
| hiveuser@localhost |
+--------------------+
1 row in set (0.00 sec)

mysql&gt; show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| hivedb             |
| mysql              |
| test               |
+--------------------+
4 rows in set (0.00 sec)

mysql&gt; show tables; 
ERROR 1046 (3D000): No database selected
mysql&gt; use hivedb;
Database changed
mysql&gt; show tables; 
Empty set (0.00 sec)

mysql&gt; show tables; 
Empty set (0.00 sec)

mysql&gt; exit;
Bye
[root@cdh1 local]# hive
Cannot find hadoop installation: $HADOOP_HOME or $HADOOP_PREFIX must be set or hadoop must be in the path
[root@cdh1 local]# echo $HADOOP_HOME
/user/local/hadoop-2.6.0
[root@cdh1 local]# pwd
/user/local
[root@cdh1 local]# cat /etc/profile
# /etc/profile

# System wide environment and startup programs, for login setup
# Functions and aliases go in /etc/bashrc

# It's NOT good idea to change this file unless you know what you
# are doing. Much better way is to create custom.sh shell script in
# /etc/profile.d/ to make custom changes to environment. This will
# prevent need for merging in future updates.

pathmunge () {
    case ":${PATH}:" in
        *:"$1":*)
            ;;
        *)
            if [ "$2" = "after" ] ; then
                PATH=$PATH:$1
            else
                PATH=$1:$PATH
            fi
    esac
}


if [ -x /usr/bin/id ]; then
    if [ -z "$EUID" ]; then
        # ksh workaround
        EUID=`id -u`
        UID=`id -ru`
    fi
    USER="`id -un`"
    LOGNAME=$USER
    MAIL="/var/spool/mail/$USER"
fi

# Path manipulation
if [ "$EUID" = "0" ]; then
    pathmunge /sbin
    pathmunge /usr/sbin
    pathmunge /usr/local/sbin
else
    pathmunge /usr/local/sbin after
    pathmunge /usr/sbin after
    pathmunge /sbin after
fi

HOSTNAME=`/bin/hostname 2&gt;/dev/null`
HISTSIZE=1000
if [ "$HISTCONTROL" = "ignorespace" ] ; then
    export HISTCONTROL=ignoreboth
else
    export HISTCONTROL=ignoredups
fi

export PATH USER LOGNAME MAIL HOSTNAME HISTSIZE HISTCONTROL

for i in /etc/profile.d/*.sh ; do
    if [ -r "$i" ]; then
        if [ "$PS1" ]; then
            . $i
        else
            . $i &gt;/dev/null 2&gt;&amp;1
        fi
    fi
done

unset i
unset pathmunge

#Hadoop Env
export HADOOP_HOME_WARN_SUPPRESS=1
export JAVA_HOME=/user/local/jdk
#export HADOOP_HOME=/user/local/hadoop
#export PATH=$JAVA_HOME/bin:$HADOOP_HOME:/bin:$PATH
export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH

#Hadoop Env
export HADOOP_HOME_WARN_SUPPRESS=1
export JAVA_HOME=/user/local/jdk
export HADOOP_HOME=/user/local/hadoop-2.6.0
export HIVE_HOME=/usr/local/hive
export PATH=$JAVA_HOME/bin:$HADOOP_HOME:/bin:$PATH
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib
#export TOMCAT_HOME=/root/solr/apache-tomcat-6.0.37
#export JRE_HOME=$JAVA_HOME/jre
#export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HIVE_HOME/bin:$PATH
export PATH=$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HIVE_HOME/bin:$PATH

#FLUME
#export FLUME_HOME=/usr/local/hadoop/flume/apache-flume-1.5.0-bin
#export FLUME_CONF_DIR=$FLUME_HOME/conf
#export PATH=$PATH:$FLUME_HOME/bin

#mvn
export MAVEN_HOME=/user/local/apache-maven-3.3.9
export PATH=$PATH:$MAVEN_HOME/bin

#scala
export SCALA_HOME=/user/local/scala-2.9.3  
export PATH=$PATH:$SCALA_HOME/bin
#spark
export SPARK_HOME=/user/local/spark-1.4.0-bin-hadoop2.6  
export PATH=$PATH:$SPARK_HOME/bin

#hbase
export HBASE_HOME=/user/local/hbase-0.98.20-hadoop2
export PATH=$PATH:$HBASE_HOME/bin

#zk
export ZOOKEEPER_HOME=/user/local/zookeeper-3.4.6
export PATH=$PATH:$ZOOKEEPER_HOME/bin

#storm
export STORM_HOME=/user/local/apache-storm-0.9.2-incubating
export PATH=$PATH:$STORM_HOME/bin

#kafaka
export KAFKA_HOME=/user/local/kafka_2.9.2-0.8.1.1
export PATH=$PATH:$KAFKA_HOME/bin
[root@cdh1 local]# source /etc/profile
hive 启动报错 
[root@cdh1 local]# hive
Cannot find hadoop installation: $HADOOP_HOME or $HADOOP_PREFIX must be set or hadoop must be in the path
[root@cdh1 local]# cd $HIVE_HOME
[root@cdh1 hive]# pwd
/usr/local/hive
[root@cdh1 hive]# ll
total 232
drwxr-xr-x. 4 1106  592   4096 Mar 10 18:27 bin
drwxr-xr-x. 2 1106  592   4096 Jun 11 09:30 conf
drwxr-xr-x. 6 1106  592   4096 Jul 12  2014 docs
drwxr-xr-x. 4 1106  592   4096 Jul 12  2014 examples
drwxr-xr-x. 7 1106  592   4096 Jul 12  2014 hcatalog
drwxr-xr-x. 4 1106  592   4096 Mar 10 17:57 lib
-rw-r--r--. 1 1106  592  23828 Jul 12  2014 LICENSE
-rw-r--r--. 1 1106  592   1559 Jul 12  2014 NOTICE
-rw-r--r--. 1 1106  592   3838 Jul 12  2014 README.txt
-rw-r--r--. 1 1106  592 166452 Jul 12  2014 RELEASE_NOTES.txt
drwxr-xr-x. 3 1106  592   4096 Jul 12  2014 scripts
drwxr-xr-x. 2 root root   4096 Jul  4 10:32 yc_test
[root@cdh1 hive]# cd conf/
[root@cdh1 conf]# ll
total 200
-rw-r--r--. 1 1106  592 83116 Jul 12  2014 hive-default.xml
-rw-r--r--. 1 1106  592  2378 Jul 12  2014 hive-env.sh
-rw-r--r--. 1 1106  592  2651 Jul 12  2014 hive-exec-log4j.properties
-rw-r--r--. 1 1106  592  3494 Jul 12  2014 hive-log4j.properties
-rw-r--r--. 1 yc   yc   82038 Mar 10 18:13 hive-site.xml
-rw-r--r--. 1 root root 11287 Jun 11 09:30 tore.local
[root@cdh1 conf]# less hive-env.sh 
[root@cdh1 conf]# cat hive-env.sh 
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Set Hive and Hadoop environment variables here. These variables can be used
# to control the execution of Hive. It should be used by admins to configure
# the Hive installation (so that users do not have to set environment variables
# or set command line parameters to get correct behavior).
#
# The hive service being invoked (CLI/HWI etc.) is available via the environment
# variable SERVICE


# Hive Client memory usage can be an issue if a large number of clients
# are running at the same time. The flags below have been useful in 
# reducing memory usage:
#
# if [ "$SERVICE" = "cli" ]; then
#   if [ -z "$DEBUG" ]; then
#     export HADOOP_OPTS="$HADOOP_OPTS -XX:NewRatio=12 -Xms10m -XX:MaxHeapFreeRatio=40 -XX:MinHeapFreeRatio=15 -XX:+UseParNewGC -XX:-UseGCOverheadLimit"
#   else
#     export HADOOP_OPTS="$HADOOP_OPTS -XX:NewRatio=12 -Xms10m -XX:MaxHeapFreeRatio=40 -XX:MinHeapFreeRatio=15 -XX:-UseGCOverheadLimit"
#   fi
# fi

# The heap size of the jvm stared by hive shell script can be controlled via:
#
# export HADOOP_HEAPSIZE=1024
#
# Larger heap size may be required when running queries over large number of files or partitions. 
# By default hive shell scripts use a heap size of 256 (MB).  Larger heap size would also be 
# appropriate for hive server (hwi etc).


# Set HADOOP_HOME to point to a specific hadoop install directory
# HADOOP_HOME=${bin}/../../hadoop

# Hive Configuration Directory can be controlled by:
# export HIVE_CONF_DIR=

# Folder containing extra ibraries required for hive compilation/execution can be controlled by:
# export HIVE_AUX_JARS_PATH=
[root@cdh1 conf]# vim hive-env.sh 
[root@cdh1 conf]# cat hive-env.sh 
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

# Set Hive and Hadoop environment variables here. These variables can be used
# to control the execution of Hive. It should be used by admins to configure
# the Hive installation (so that users do not have to set environment variables
# or set command line parameters to get correct behavior).
#
# The hive service being invoked (CLI/HWI etc.) is available via the environment
# variable SERVICE


# Hive Client memory usage can be an issue if a large number of clients
# are running at the same time. The flags below have been useful in 
# reducing memory usage:
#
# if [ "$SERVICE" = "cli" ]; then
#   if [ -z "$DEBUG" ]; then
#     export HADOOP_OPTS="$HADOOP_OPTS -XX:NewRatio=12 -Xms10m -XX:MaxHeapFreeRatio=40 -XX:MinHeapFreeRatio=15 -XX:+UseParNewGC -XX:-UseGCOverheadLimit"
#   else
#     export HADOOP_OPTS="$HADOOP_OPTS -XX:NewRatio=12 -Xms10m -XX:MaxHeapFreeRatio=40 -XX:MinHeapFreeRatio=15 -XX:-UseGCOverheadLimit"
#   fi
# fi

# The heap size of the jvm stared by hive shell script can be controlled via:
#
# export HADOOP_HEAPSIZE=1024
#
# Larger heap size may be required when running queries over large number of files or partitions. 
# By default hive shell scripts use a heap size of 256 (MB).  Larger heap size would also be 
# appropriate for hive server (hwi etc).


# Set HADOOP_HOME to point to a specific hadoop install directory
# HADOOP_HOME=${bin}/../../hadoop 这里要配置HADOOP_HOME的
  export HADOOP_HOME=/user/local/hadoop-2.6.0
# Hive Configuration Directory can be controlled by:
# export HIVE_CONF_DIR=

# Folder containing extra ibraries required for hive compilation/execution can be controlled by:
# export HIVE_AUX_JARS_PATH=
[root@cdh1 conf]# hive
16/07/19 21:11:21 INFO Configuration.deprecation: mapred.input.dir.recursive is deprecated. Instead, use mapreduce.input.fileinputformat.input.dir.recursive
16/07/19 21:11:21 INFO Configuration.deprecation: mapred.max.split.size is deprecated. Instead, use mapreduce.input.fileinputformat.split.maxsize
16/07/19 21:11:21 INFO Configuration.deprecation: mapred.min.split.size is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize
16/07/19 21:11:21 INFO Configuration.deprecation: mapred.min.split.size.per.rack is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize.per.rack
16/07/19 21:11:21 INFO Configuration.deprecation: mapred.min.split.size.per.node is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize.per.node
16/07/19 21:11:21 INFO Configuration.deprecation: mapred.reduce.tasks is deprecated. Instead, use mapreduce.job.reduces
16/07/19 21:11:21 INFO Configuration.deprecation: mapred.reduce.tasks.speculative.execution is deprecated. Instead, use mapreduce.reduce.speculative
16/07/19 21:11:22 WARN conf.HiveConf: DEPRECATED: hive.metastore.ds.retry.* no longer has any effect.  Use hive.hmshandler.retry.* instead

Logging initialized using configuration in file:/usr/local/hive/conf/hive-log4j.properties
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/user/local/hadoop-2.6.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hive/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
hive&gt; 
    &gt;  create table hivetest(id int,name string);
OK
Time taken: 8.987 seconds
hive&gt; 
    &gt; show tables;
OK
hivetest
Time taken: 0.224 seconds, Fetched: 1 row(s)
hive&gt; set hive.cli.print.current.db=true;
hive (default)&gt; show tables;                       
OK
hivetest
Time taken: 0.047 seconds, Fetched: 1 row(s)
hive (default)&gt; select * from hivetest;
OK
Time taken: 0.664 seconds
hive (default)&gt; create table if not exists ljh_emp(
              &gt; name string,
              &gt; salary float,
              &gt; gender string)
              &gt; comment 'basic information of a employee'
              &gt; row format delimited fields terminated by ',';
OK
Time taken: 0.096 seconds
hive (default)&gt; show tables;                                  
OK
hivetest
ljh_emp
Time taken: 0.024 seconds, Fetched: 2 row(s)
hive (default)&gt; select * from ljh_emp;
OK
Time taken: 0.062 seconds
hive (default)&gt; load data local inpath '/usr/local/hive/yc_test/test' into table ljh_emp;
Copying data from file:/usr/local/hive/yc_test/test
Copying file: file:/usr/local/hive/yc_test/test
Loading data to table default.ljh_emp
Table default.ljh_emp stats: [num_partitions: 0, num_files: 1, num_rows: 0, total_size: 51, raw_data_size: 0]
OK
Time taken: 13.199 seconds
hive (default)&gt; select * from ljh_emp;                                                   
OK
ljh	25000.0	male
jediael	25000.0	male
llq	15000.0	female
Time taken: 0.144 seconds, Fetched: 3 row(s)
hive (default)&gt; create table if not exists ljh_emp_test(
              &gt; name string,
              &gt; salary float,
              &gt; gender string)
              &gt; comment 'basic information of a employee';
OK
Time taken: 0.181 seconds
hive (default)&gt; show tables;
OK
hivetest
ljh_emp
ljh_emp_test
Time taken: 0.025 seconds, Fetched: 3 row(s)
hive (default)&gt; create table if not exists ljh_emp(
              &gt; name string,
              &gt; salary float,
              &gt; gender string)
              &gt; comment 'basic information of a employee'
              &gt; row format delimited fields terminated by ',Exception in thread "main" java.io.IOException: invalid UTF-8 first byte: -95
	at jline.UnixTerminal$ReplayPrefixOneCharInputStream.setInputUTF8(UnixTerminal.java:403)
	at jline.UnixTerminal$ReplayPrefixOneCharInputStream.setInput(UnixTerminal.java:384)
	at jline.UnixTerminal.readVirtualKey(UnixTerminal.java:172)
	at jline.ConsoleReader.readVirtualKey(ConsoleReader.java:1453)
	at jline.ConsoleReader.readBinding(ConsoleReader.java:654)
	at jline.ConsoleReader.readLine(ConsoleReader.java:494)
	at jline.ConsoleReader.readLine(ConsoleReader.java:448)
	at org.apache.hadoop.hive.cli.CliDriver.executeDriver(CliDriver.java:784)
	at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:684)
	at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:623)
	at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
	at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
	at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
	at java.lang.reflect.Method.invoke(Method.java:606)
	at org.apache.hadoop.util.RunJar.run(RunJar.java:221)
	at org.apache.hadoop.util.RunJar.main(RunJar.java:136)
[root@cdh1 conf]# .
-bash: . command not found
[root@cdh1 conf]# hive
16/07/19 21:28:23 INFO Configuration.deprecation: mapred.input.dir.recursive is deprecated. Instead, use mapreduce.input.fileinputformat.input.dir.recursive
16/07/19 21:28:23 INFO Configuration.deprecation: mapred.max.split.size is deprecated. Instead, use mapreduce.input.fileinputformat.split.maxsize
16/07/19 21:28:23 INFO Configuration.deprecation: mapred.min.split.size is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize
16/07/19 21:28:23 INFO Configuration.deprecation: mapred.min.split.size.per.rack is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize.per.rack
16/07/19 21:28:23 INFO Configuration.deprecation: mapred.min.split.size.per.node is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize.per.node
16/07/19 21:28:23 INFO Configuration.deprecation: mapred.reduce.tasks is deprecated. Instead, use mapreduce.job.reduces
16/07/19 21:28:23 INFO Configuration.deprecation: mapred.reduce.tasks.speculative.execution is deprecated. Instead, use mapreduce.reduce.speculative
16/07/19 21:28:23 WARN conf.HiveConf: DEPRECATED: hive.metastore.ds.retry.* no longer has any effect.  Use hive.hmshandler.retry.* instead

Logging initialized using configuration in file:/usr/local/hive/conf/hive-log4j.properties
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/user/local/hadoop-2.6.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hive/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
hive&gt; show tables;                             
OK
hivetest
ljh_emp
ljh_emp_test
Time taken: 3.827 seconds, Fetched: 3 row(s)
hive&gt; set hive.cli.print.current.db=true;                                      
hive (default)&gt; show tables;                       
OK
hivetest
ljh_emp
ljh_emp_test
Time taken: 0.04 seconds, Fetched: 3 row(s)
hive (default)&gt; create table if not exists ljh_emp(
              &gt; name string,
              &gt; salary float,
              &gt; gender string)
              &gt; comment 'basic information of a employee'
              &gt; row format delimited fields terminated by ',';
OK
Time taken: 0.139 seconds
hive (default)&gt; show tables;                                  
OK
hivetest
ljh_emp
ljh_emp_test
Time taken: 0.048 seconds, Fetched: 3 row(s)
hive (default)&gt; create table if not exists ljh_emp2(
              &gt; name string,
              &gt; salary float,
              &gt; gender string)
              &gt; comment 'basic information of a employee'
              &gt; row format delimited fields terminated by ',';
OK
Time taken: 0.777 seconds
hive (default)&gt; show tables;                                  
OK
hivetest
ljh_emp
ljh_emp2
ljh_emp_test
Time taken: 0.041 seconds, Fetched: 4 row(s)
hive (default)&gt; select * from ljh_emp;
OK
ljh	25000.0	male
jediael	25000.0	male
llq	15000.0	female
Time taken: 0.436 seconds, Fetched: 3 row(s)
hive (default)&gt; select * from ljh_emp where 1=1;     
Total MapReduce jobs = 1
Launching Job 1 out of 1
Number of reduce tasks is set to 0 since there's no reduce operator
Starting Job = job_1468930192790_0001, Tracking URL = http://cdh1:8088/proxy/application_1468930192790_0001/
Kill Command = /user/local/hadoop-2.6.0/bin/hadoop job  -kill job_1468930192790_0001
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0
2016-07-19 21:32:07,053 Stage-1 map = 0%,  reduce = 0%
2016-07-19 21:32:15,673 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.74 sec
2016-07-19 21:32:16,728 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.74 sec
MapReduce Total cumulative CPU time: 1 seconds 740 msec
Ended Job = job_1468930192790_0001
MapReduce Jobs Launched: 
Job 0: Map: 1   Cumulative CPU: 1.74 sec   HDFS Read: 254 HDFS Write: 57 SUCCESS
Total MapReduce CPU Time Spent: 1 seconds 740 msec
OK
ljh	25000.0	male
jediael	25000.0	male
llq	15000.0	female
Time taken: 24.427 seconds, Fetched: 3 row(s)
hive (default)&gt; show tables;                    
OK
hivetest
ljh_emp
ljh_emp2
ljh_emp_test
Time taken: 0.046 seconds, Fetched: 4 row(s)
hive (default)&gt; select * from ljh_emp;          
OK
ljh	25000.0	male
jediael	25000.0	male
llq	15000.0	female
Time taken: 0.077 seconds, Fetched: 3 row(s)
hive (default)&gt; select * from ljh_emp where 1=1;
Total MapReduce jobs = 1
Launching Job 1 out of 1
Number of reduce tasks is set to 0 since there's no reduce operator
Starting Job = job_1468930192790_0002, Tracking URL = http://cdh1:8088/proxy/application_1468930192790_0002/
Kill Command = /user/local/hadoop-2.6.0/bin/hadoop job  -kill job_1468930192790_0002
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0
2016-07-19 21:51:35,688 Stage-1 map = 0%,  reduce = 0%
2016-07-19 21:51:43,365 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 2.03 sec
2016-07-19 21:51:44,412 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 2.03 sec
MapReduce Total cumulative CPU time: 2 seconds 30 msec
Ended Job = job_1468930192790_0002
MapReduce Jobs Launched: 
Job 0: Map: 1   Cumulative CPU: 2.03 sec   HDFS Read: 254 HDFS Write: 57 SUCCESS
Total MapReduce CPU Time Spent: 2 seconds 30 msec
OK
ljh	25000.0	male
jediael	25000.0	male
llq	15000.0	female
Time taken: 18.777 seconds, Fetched: 3 row(s)
hive (default)&gt; select * from ljh_emp where 1=1;
Total MapReduce jobs = 1
Launching Job 1 out of 1
Number of reduce tasks is set to 0 since there's no reduce operator
Starting Job = job_1468930192790_0003, Tracking URL = http://cdh1:8088/proxy/application_1468930192790_0003/
Kill Command = /user/local/hadoop-2.6.0/bin/hadoop job  -kill job_1468930192790_0003
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 0
2016-07-19 21:52:04,406 Stage-1 map = 0%,  reduce = 0%
2016-07-19 21:52:11,859 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.82 sec
2016-07-19 21:52:12,926 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.82 sec
MapReduce Total cumulative CPU time: 1 seconds 820 msec
Ended Job = job_1468930192790_0003
MapReduce Jobs Launched: 
Job 0: Map: 1   Cumulative CPU: 1.82 sec   HDFS Read: 254 HDFS Write: 57 SUCCESS
Total MapReduce CPU Time Spent: 1 seconds 820 msec
OK
ljh	25000.0	male
jediael	25000.0	male
llq	15000.0	female
Time taken: 18.887 seconds, Fetched: 3 row(s)
hive (default)&gt; 

</code></pre><br>            </div>
                </div>
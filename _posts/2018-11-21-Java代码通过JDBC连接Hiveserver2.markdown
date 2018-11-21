---
layout:     post
title:      Java代码通过JDBC连接Hiveserver2
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:'微软雅黑';font-size:14px;line-height:21px;color:rgb(51,51,51);background-color:rgb(249,249,249);"><span style="font-family:'Microsoft Yahei';background-color:inherit;">用Java代码通过JDBC连接Hiveserver2 </span></span>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;color:rgb(51,51,51);">
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">      我们可以通过CLI、Client、Web UI等Hive提供的用户接口来和Hive通信，但这三种方式最常用的是CLI；Client 是Hive的客户端，用户连接至 Hive Server。在启动 Client 模式的时候，需要指出Hive
 Server所在节点，并且在该节点启动 Hive Server。 WUI 是通过浏览器访问 Hive。今天我们来谈谈怎么通过HiveServer来操作Hive。</span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<div style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">在$HIVE_HOME/conf目录下，新建一个hive-site.xml,<span style="background-color:inherit;"> </span><span style="background-color:inherit;">配置Hive元数据的存储方式（我用的是mysql存储）</span></span></div>
<div style="background-color:inherit;"><span style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">注意下面配置的是hive默认的是mysql的hive数据库，用户名是hive，密码是hive。所以在进行下列操作前要用root登录mysql创建hive数据库并且分配给用户hive。</span></span></div>
<div style="background-color:inherit;">
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">&lt;?xml version="1.0"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;
 
&lt;configuration&gt;
&lt;property&gt;
  &lt;name&gt;hive.metastore.local&lt;/name&gt;
  &lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;
 
&lt;property&gt;
  &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
  &lt;value&gt;jdbc:mysql://localhost:3306/hive&lt;/value&gt;
&lt;/property&gt;
 
&lt;property&gt;
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
  &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
&lt;/property&gt;
 
&lt;property&gt;
  &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
  &lt;value&gt;hive&lt;/value&gt;
&lt;/property&gt;
 
&lt;property&gt;
  &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
  &lt;value&gt;hive&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;datanucleus.fixedDatastore&lt;/name&gt;
  &lt;value&gt;false&lt;/value&gt;
&lt;/property&gt;
 
&lt;/configuration&gt;</span></pre>
<span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></div>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">　　Hive提供了jdbc驱动，使得我们可以用Java代码来连接Hive并进行一些类关系型数据库的sql语句查询等操作。同关系型数据库一样，我们也需要将Hive的服务打开；在Hive 0.11.0版本之前，只有HiveServer服务可用，你得在程序操作Hive之前，必须在Hive安装的服务器上打开HiveServer服务，如下：</span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">[wyp@localhost /home/q/hive-0.11.0]$ bin/hive --service hiveserver -p 10002
Starting Hive Thrift Server</span></pre>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">上面代表你已经成功的在端口为10002（默认的端口是10000）启动了hiveserver服务。这时候，你就可以通过Java代码来连接hiveserver，代码如下：</span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">package com.wyp;

import java.sql.SQLException;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.DriverManager;
 
public class HiveJdbcTest {
    
  private static String driverName =
                   "org.apache.hadoop.hive.jdbc.HiveDriver";
	   
	    public static void main(String[] args)	                            throws SQLException {
	        try {
	            Class.forName(driverName);
	        } catch (ClassNotFoundException e) {
	            e.printStackTrace();
	            System.exit(1);
	        }
	 
	        Connection con = DriverManager.getConnection(
	                           "jdbc:hive://localhost:10002/default", "wyp", "");
	        Statement stmt = con.createStatement();
	        String tableName = "wyphao";
	        stmt.execute("drop table if exists " + tableName);
	        stmt.execute("create table " + tableName +
	                                     " (key int, value string)");
	        System.out.println("Create table success!");
	        // show tables
	        String sql = "show tables '" + tableName + "'";
	        System.out.println("Running: " + sql);
	        ResultSet res = stmt.executeQuery(sql);
	        if (res.next()) {
	            System.out.println(res.getString(1));
	        }
	 
	        // describe table
	        sql = "describe " + tableName;
	        System.out.println("Running: " + sql);
	        res = stmt.executeQuery(sql);
	        while (res.next()) {
	            System.out.println(res.getString(1) + "\t" + res.getString(2));
	        }
	 
	 
	        sql = "select * from " + tableName;
	        res = stmt.executeQuery(sql);
	        while (res.next()) {
	            System.out.println(String.valueOf(res.getInt(1)) + "\t"
	                                               + res.getString(2));
	        }
	 
	        sql = "select count(1) from " + tableName;
	        System.out.println("Running: " + sql);
	        res = stmt.executeQuery(sql);
	        while (res.next()) {
	            System.out.println(res.getString(1));
	        }
	    }
	}
</span></pre>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">编译上面的代码，之后就可以运行(我是在集成开发环境下面运行这个程序的)，结果如下：</span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">Create table success!
Running: show tables 'wyphao'
wyphao
Running: describe wyphao
key                     int                
value                   string             
Running: select count(1) from wyphao
0 
Process finished with exit code 0</span></pre>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">　　如果你想在脚本里面运行，请将上面的程序打包成jar文件，并将上面的依赖库放在/home/wyp/lib/（这个根据你自己的情况弄）中，同时加入到运行的环境变量，脚本如下：</span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">#!/bin/bash
HADOOP_HOME=/home/q/hadoop-2.2.0
HIVE_HOME=/home/q/hive-0.11.0-bin
 
CLASSPATH=$CLASSPATH:
 
for i in /home/wyp/lib/*.jar ; do
    CLASSPATH=$CLASSPATH:$i
done
 
echo $CLASSPATH
/home/q/java/jdk1.6.0_20/bin/java -cp  \
   $CLASSPATH:/export1/tmp/yangping.wu/OutputText.jar  com.wyp.HiveJdbcTest</span></pre>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">上面是用Java连接HiveServer，而HiveServer本身存在很多问题（比如：安全性、并发性等）；针对这些问题，Hive0.11.0版本提供了一个全新的服务：HiveServer2，这个很好的解决HiveServer存在的安全性、并发性等问题。这个服务启动程序在${HIVE_HOME}/bin/hiveserver2里面，你可以通过下面的方式来启动HiveServer2服务：</span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">$HIVE_HOME/bin/hiveserver2</span></pre>
<p style="background-color:inherit;"><span style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> <br style="background-color:inherit;"></span></span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">也可以通过下面的方式启动HiveServer2</span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">$HIVE_HOME/bin/hive --service hiveserver2</span></pre>
<p style="background-color:inherit;"><span style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> <br style="background-color:inherit;"></span></span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">两种方式效果都一样的。但是以前的程序需要修改两个地方，如下所示：</span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">	private static String driverName = "org.apache.hadoop.hive.jdbc.HiveDriver";
	改为
	private static String driverName = "org.apache.hive.jdbc.HiveDriver";

Connection con = DriverManager.getConnection(
	                           "jdbc:hive://localhost:10002/default", "wyp", "");
	改为
	Connection con = DriverManager.getConnection(
	                           "jdbc:hive2://localhost:10002/default", "wyp", "");</span></pre>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">其他的不变就可以了。</span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">　　这里顺便说说本程序所依赖的jar包，一共有以下几个：</span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">hadoop-2.2.0/share/hadoop/common/hadoop-common-2.2.0.jar
$HIVE_HOME/lib/hive-exec-0.11.0.jar
$HIVE_HOME/lib/hive-jdbc-0.11.0.jar
$HIVE_HOME/lib/hive-metastore-0.11.0.jar 
$HIVE_HOME/lib/hive-service-0.11.0.jar  
$HIVE_HOME/lib/libfb303-0.9.0.jar  
$HIVE_HOME/lib/commons-logging-1.0.4.jar 
$HIVE_HOME/lib/slf4j-api-1.6.1.jar</span></pre>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;"> </span></p>
<p style="background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">　　如果你是用Maven，加入以下依赖</span></p>
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:inherit;"><span style="font-family:'Microsoft Yahei';background-color:inherit;">&lt;dependency&gt;
	        &lt;groupId&gt;org.apache.hive&lt;/groupId&gt;
	        &lt;artifactId&gt;hive-jdbc&lt;/artifactId&gt;
	        &lt;version&gt;0.11.0&lt;/version&gt;
	&lt;/dependency&gt;
	 
	&lt;dependency&gt;
	        &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
	        &lt;artifactId&gt;hadoop-common&lt;/artifactId&gt;
        &lt;version&gt;2.2.0&lt;/version&gt;
&lt;/dependency&gt;</span></pre>
<div><span style="font-family:'Microsoft Yahei';background-color:inherit;"><br></span></div>
</div>
            </div>
                </div>
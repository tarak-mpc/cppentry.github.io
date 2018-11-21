---
layout:     post
title:      Java通过JDBC操作Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1. hadoop的配置文件core-site.xml中</p>
<p></p><pre><code class="language-html">&lt;configuration&gt;  
 
 &lt;property&gt;      
      &lt;name&gt;fs.defaultFS&lt;/name&gt;      
      &lt;value&gt;hdfs://hadoop1:9000&lt;/value&gt;      
 &lt;/property&gt;  
 
 &lt;property&gt;  
      &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;  
      &lt;value&gt;/usr/local/softinstall/hadoop/workspace/hdfs/temp&lt;/value&gt;  
 &lt;/property&gt;     
                            
 &lt;property&gt;      
      &lt;name&gt;io.file.buffer.size&lt;/name&gt;      
      &lt;value&gt;4096&lt;/value&gt;      
 &lt;/property&gt;  


&lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.root.hosts&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.root.groups&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;
&lt;/property&gt;

 
 &lt;/configuration&gt; </code></pre>不然会有报错情况
<p></p><pre><code class="language-plain">Error: Failed to open new session: java.lang.RuntimeException: org.apache.hadoop.ipc.RemoteException</code></pre>
<p>具体细节参考：http://blog.csdn.net/swing2008/article/details/53230145</p>
<p>2. 启动hadoop集群，启动hive的服务端  ：</p>
<p></p><pre><code class="language-java">hive --service hiveserver2 &gt;/dev/null &amp; </code></pre>
<p>如果不启动hive服务端，则报错解决链接。</p>
<p>3.maven项目中添加依赖</p>
<p></p><pre><code class="language-html">&lt;!-- https://mvnrepository.com/artifact/org.apache.hive/hive-jdbc --&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.apache.hive&lt;/groupId&gt;
    &lt;artifactId&gt;hive-jdbc&lt;/artifactId&gt;
    &lt;version&gt;2.1.0&lt;/version&gt;
&lt;/dependency&gt;

      &lt;!-- https://mvnrepository.com/artifact/org.apache.hive/hive-metastore --&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.apache.hive&lt;/groupId&gt;
    &lt;artifactId&gt;hive-metastore&lt;/artifactId&gt;
    &lt;version&gt;2.1.0&lt;/version&gt;
&lt;/dependency&gt;

&lt;!-- https://mvnrepository.com/artifact/org.apache.hive/hive-service --&gt;
&lt;dependency&gt;
    &lt;groupId&gt;org.apache.hive&lt;/groupId&gt;
    &lt;artifactId&gt;hive-service&lt;/artifactId&gt;
    &lt;version&gt;2.1.0&lt;/version&gt;
&lt;/dependency&gt;
</code></pre><br>
4.编写HiveJdbcCli.java
<p></p><pre><code class="language-java">package com.eastcom.first.spark.data.hive;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class HiveJdbcCli {

	// TODO Auto-generated method stub
	private final static Logger logger = LoggerFactory.getLogger(HiveJdbcCli.class);
	// 网上写 org.apache.hadoop.hive.jdbc.HiveDriver ,新版本不能这样写
	private static String driverName = "org.apache.hive.jdbc.HiveDriver";

	// 这里是hive2，网上其他人都写hive,在高版本中会报错
	private static String url = "jdbc:hive2://192.168.31.130:10000/default";
	private static String user = "root";
	private static String password = "root";
	private static String sql = "";
	private static ResultSet res;

	private static Connection conn;
	private static Statement stmt;

	public static void main(String[] args) throws ClassNotFoundException {

		try {
			init();
			// 第一步:存在就先删除
			// String tableName = dropTable(stmt);
			String tableName = "testhive.tbdate";

			// 第二步:不存在就创建
			// createTable(stmt, tableName);

			// 第三步:查看创建的表
			// showTables(stmt, tableName);

			// 执行describe table操作
			// describeTables(stmt, tableName);

			// 执行load data into table操作
			// loadData(stmt, tableName);

			// 执行 select * query 操作
			selectData(stmt, tableName);

			// 执行 regular hive query 统计操作
			countData(stmt, tableName);

			destroy();

		} catch (SQLException e) {
			e.printStackTrace();
			logger.error("Connection error!", e);
			System.exit(1);
		}
	}

	private static void countData(Statement stmt, String tableName) throws SQLException {
		sql = "select count(1) from " + tableName;
		System.out.println("Running:" + sql);
		res = stmt.executeQuery(sql);
		System.out.println("执行“regular hive query”运行结果:");
		while (res.next()) {
			System.out.println("count ------&gt;" + res.getString(1));
		}
	}

	private static void selectData(Statement stmt, String tableName) throws SQLException {
		sql = "select * from " + tableName + " limit 10 ";
		System.out.println("Running:" + sql);
		res = stmt.executeQuery(sql);
		System.out.println("执行 select * query 运行结果:");
		while (res.next()) {
			System.out.println(res.getString(1) + "\t" + res.getString(2) + res.getString(3) + "\t" + res.getString(4));
		}
	}

	private static void loadData(Statement stmt, String tableName) throws SQLException {
		// 目录 ，我的是hive安装的机子的虚拟机的home目录下
		String filepath = "user.txt";
		sql = "load data local inpath '" + filepath + "' into table " + tableName;
		System.out.println("Running:" + sql);
		stmt.execute(sql);
	}

	private static void describeTables(Statement stmt, String tableName) throws SQLException {
		sql = "describe " + tableName;
		System.out.println("Running:" + sql);
		res = stmt.executeQuery(sql);
		System.out.println("执行 describe table 运行结果:");
		while (res.next()) {
			System.out.println(res.getString(1) + "\t" + res.getString(2));
		}
	}

	private static void showTables(Statement stmt, String tableName) throws SQLException {
		sql = "show tables '" + tableName + "'";
		System.out.println("Running:" + sql);
		res = stmt.executeQuery(sql);
		System.out.println("执行 show tables 运行结果:");
		if (res.next()) {
			System.out.println(res.getString(1));
		}
	}

	private static void createTable(Statement stmt, String tableName) throws SQLException {
		sql = "create table " + tableName + " (key int, value string)  row format delimited fields terminated by '\t'";
		stmt.execute(sql);
	}

	private static String dropTable(Statement stmt) throws SQLException {
		// 创建的表名
		String tableName = "testHive";
		sql = "drop table  " + tableName;
		stmt.execute(sql);
		return tableName;
	}

	private static Connection getConn() throws ClassNotFoundException, SQLException {
		Class.forName(driverName);
		Connection conn = DriverManager.getConnection(url, user, password);
		return conn;
	}

	private static void init() throws ClassNotFoundException, SQLException {

		conn = getConn();
		stmt = conn.createStatement();
	}

	private static void destroy() throws ClassNotFoundException, SQLException {
		if (stmt != null) {
			stmt.close();
		}
		if (conn != null) {
			conn.close();
		}
	}

}
</code></pre><br>
5.运行结果
<p></p><pre><code class="language-plain">SLF4J: Found binding in [jar:file:/C:/Users/Administrator/.m2/repository/org/apache/activemq/activemq-all/5.15.0/activemq-all-5.15.0.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/C:/Users/Administrator/.m2/repository/org/slf4j/slf4j-log4j12/1.7.21/slf4j-log4j12-1.7.21.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/C:/Users/Administrator/.m2/repository/org/apache/logging/log4j/log4j-slf4j-impl/2.4.1/log4j-slf4j-impl-2.4.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
 INFO | Supplied authorities: 192.168.31.130:10000
 INFO | Resolved authority: 192.168.31.130:10000
Running:select * from testhive.tbdate limit 10 
执行 select * query 运行结果:
2003-1-1	2003012003	1
2003-1-2	2003012003	1
2003-1-3	2003012003	1
2003-1-4	2003012003	1
2003-1-5	2003012003	1
2003-1-6	2003012003	1
2003-1-7	2003012003	1
2003-1-8	2003012003	1
2003-1-9	2003012003	1
2003-1-10	2003012003	1
Running:select count(1) from testhive.tbdate
执行“regular hive query”运行结果:
count ------&gt;4383</code></pre><br>
参考链接：<pre><code class="language-java">http://www.cnblogs.com/netbloomy/p/6688670.html

http://blog.csdn.net/tanggao1314/article/details/51510844

http://blog.csdn.net/fz1989/article/details/51489498

</code></pre><br><p><br></p>
<p><br></p>
<br><br><p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<br><br>            </div>
                </div>
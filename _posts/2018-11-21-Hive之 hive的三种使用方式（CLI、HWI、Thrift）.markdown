---
layout:     post
title:      Hive之 hive的三种使用方式（CLI、HWI、Thrift）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，可以转载，但必须以链接形式声明出处。					https://blog.csdn.net/zhang123456456/article/details/78033681				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
Hive有三种使用方式——CLI命令行，HWI（hie web interface）浏览器 以及 Thrift客户端连接方式。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
1、hive  命令行模式</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
直接输入/hive/bin/hive的执行程序，或者输入 hive –service cli<br>
       用于linux平台命令行查询，查询语句基本跟MySQL查询语句类似</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
2、hive  web界面的启动方式</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hive –service hwi  用于通过浏览器来访问hive</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
如果lib目录下没有hive-hwi-{version}.war包，我们要自己打包</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
官网下载源码包（比如1.10版本）</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
解压</p>
<pre style="font-family:'Courier New';"><code>$ tar zxvf apache-hive-1.1.0.src.tar.gz</code></pre>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
再进入 hwi 目录，打包 war 文件（注意命令末尾有一个点.）</p>
<pre style="font-family:'Courier New';"><code>#cd apache-hive-1.1.0-src/hwi
#jar cvfM0 hive-hwi-1.1.0.war -C web/ .</code></pre>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
 打包完成后，有了我们需要的 war 文件，再复制到 $HIVE_HOME/lib 目录下</p>
<pre style="font-family:'Courier New';"><code>#cp hive-hwi-1.1.0.war /usr/local/hive-1.1.0/lib</code></pre>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
另外我们还需要拷贝一个 Java 的 tools.jar 到 $HIVE_HOME/lib 目录下</p>
<pre style="font-family:'Courier New';"><code> cp /usr/local/jdk1.7.0_67/lib/tools.jar /usr/local/hive-1.1.0/lib</code></pre>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
否则会出现类似于下面的错误（因为 JAVA_HOME 指到$JAVA_HOME/jre 下了，而其 lib下的 tools.jar 跟$JAVA_HOME/lib/tools.jar 不一样，编译的时候需要用到后者） </p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
<img src="http://pic.w2bc.com/upload/201703/28/201703282218096513.jpg" alt="" width="785" height="174" style="border:0px;"></p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
最后，我们将 hive-site.xml 文件修改为</p>
<pre style="font-family:'Courier New';"><code>&lt;property&gt;
    &lt;name&gt;hive.hwi.listen.host&lt;/name&gt;
    &lt;value&gt;0.0.0.0&lt;/value&gt;
    &lt;description&gt;监听的地址&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hive.hwi.listen.port&lt;/name&gt;
    &lt;value&gt;9999&lt;/value&gt;
    &lt;description&gt;监听的端口号&lt;/description&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hive.hwi.war.file&lt;/name&gt;
    &lt;value&gt;lib/hive-hwi-1.1.0.war&lt;/value&gt;
    &lt;description&gt;war包所在的地址，注意这里不支持绝对路径，坑！&lt;/description&gt;
  &lt;/property&gt;</code></pre>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
启动 hwi<br>
在 $HIVE_HOME/bin 目录下，启动 hwi（由于我们之前已经修改了 Derby 为 MySQL 数据库，所以在启动 hwi 之前，请确保 MySQL 和 Hadoop 已经成功启动）：</p>
<pre style="font-family:'Courier New';"><code>nohup bin/hive --service hwi &gt; /dev/null 2&gt; /dev/null &amp;</code></pre>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
web访问</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
我们可以在浏览器中打开网络接口的地址：localhost:9999/hwi, 启动成功</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
<img src="http://pic.w2bc.com/upload/201703/28/201703282218145568.jpg" alt="" width="1347" height="653" style="border:0px;"></p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
3、jdbc远程连接hiveserver2</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
在之前的学习和实践Hive中，使用的都是CLI或者hive –e的方式，该方式仅允许使用HiveQL执行查询、更新等操作，并且该方式比较笨拙单一。幸好Hive提供了轻客户端的实现，通过HiveServer或者HiveServer2，客户端可以在不启动CLI的情况下对Hive中的数据进行操作，两者都允许远程客户端使用多种编程语言如Java、Python向Hive提交请求，取回结果。HiveServer或者HiveServer2都是基于Thrift的，但HiveSever有时被称为Thrift<br>
server，而HiveServer2却不会。既然已经存在HiveServer为什么还需要HiveServer2呢？这是因为HiveServer不能处理多于一个客户端的并发请求，这是由于HiveServer使用的Thrift接口所导致的限制，不能通过修改HiveServer的代码修正。因此在Hive-0.11.0版本中重写了HiveServer代码得到了HiveServer2，进而解决了该问题。HiveServer2支持多客户端的并发和认证，为开放API客户端如JDBC、ODBC提供了更好的支持。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
配置</p>
<pre style="font-family:'Courier New';"><code>&lt;property&gt;
  &lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;
  &lt;value&gt;/usr/hive/warehouse&lt;/value&gt;               //（hive中的数据库和表在HDFS中存放的文件夹的位置）
  &lt;description&gt;location of default database for the warehouse&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;hive.server2.thrift.port&lt;/name&gt;
  &lt;value&gt;10000&lt;/value&gt;                               //（HiveServer2远程连接的端口，默认为10000）
  &lt;description&gt;Port number of HiveServer2 Thrift interface.
  Can be overridden by setting $HIVE_SERVER2_THRIFT_PORT&lt;/description&gt;
&lt;/property&gt;
 
&lt;property&gt;
  &lt;name&gt;hive.server2.thrift.bind.host&lt;/name&gt;
  &lt;value&gt;**.**.**.**&lt;/value&gt;                          //（hive所在集群的IP地址）
  &lt;description&gt;Bind host on which to run the HiveServer2 Thrift interface.
  Can be overridden by setting $HIVE_SERVER2_THRIFT_BIND_HOST&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;hive.server2.long.polling.timeout&lt;/name&gt;
  &lt;value&gt;5000&lt;/value&gt;                                // (默认为5000L,此处修改为5000，不然程序会报错)
  &lt;description&gt;Time in milliseconds that HiveServer2 will wait, before responding to asynchronous calls that use long polling&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
  &lt;value&gt;jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;  //（Hive的元数据库，我采用的是本地Mysql作为元数据库）
  &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;
&lt;/property&gt;
 
&lt;property&gt;                         
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;          //（连接元数据的驱动名）
  &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
  &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;             //（连接元数据库用户名）
  &lt;value&gt;hive&lt;/value&gt;
  &lt;description&gt;username to use against metastore database&lt;/description&gt;
&lt;/property&gt;
 
&lt;property&gt;
  &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;             // （连接元数据库密码）
  &lt;value&gt;hive&lt;/value&gt;
  &lt;description&gt;password to use against metastore database&lt;/description&gt;
&lt;/property&gt;</code></pre>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
先启动元数据库，在命令行中键入：hive --service metastore &amp; </p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
接下来开启hiveserver2服务：<br>
在命令行中键入：hive --service hiveserver2 &amp;<br>
注意查看日志是否报错。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
javaapi操作hive实例</p>
<pre style="font-family:'Courier New';"><code>package com.berg.hive.test1.api;

import java.sql.Connection;  
import java.sql.DriverManager;  
import java.sql.ResultSet;  
import java.sql.SQLException;  
import java.sql.Statement;  

import org.apache.log4j.Logger;  

/** 
 * Hive的JavaApi 
 *  
 * 启动hive的远程服务接口命令行执行：hive --service hiveserver &amp; 
 *  
 * @author 汤高 
 *  
 */  
public class HiveJdbcCli {  
    //网上写 org.apache.hadoop.hive.jdbc.HiveDriver ,新版本不能这样写
    private static String driverName = "org.apache.hive.jdbc.HiveDriver";  

  //这里是hive2，网上其他人都写hive,在高版本中会报错
    private static String url = "jdbc:hive2://master:10000/default"; 
    private static String user = "hive";  
    private static String password = "hive";  
    private static String sql = "";  
    private static ResultSet res;  
    private static final Logger log = Logger.getLogger(HiveJdbcCli.class);  

    public static void main(String[] args) {  
        Connection conn = null;  
        Statement stmt = null;  
        try {  
            conn = getConn();  
            stmt = conn.createStatement();  

            // 第一步:存在就先删除  
            String tableName = dropTable(stmt);  

            // 第二步:不存在就创建  
            createTable(stmt, tableName);  

            // 第三步:查看创建的表  
            showTables(stmt, tableName);  

            // 执行describe table操作  
            describeTables(stmt, tableName);  

            // 执行load data into table操作  
            loadData(stmt, tableName);  

            // 执行 select * query 操作  
            selectData(stmt, tableName);  

            // 执行 regular hive query 统计操作  
            countData(stmt, tableName);  

        } catch (ClassNotFoundException e) {  
            e.printStackTrace();  
            log.error(driverName + " not found!", e);  
            System.exit(1);  
        } catch (SQLException e) {  
            e.printStackTrace();  
            log.error("Connection error!", e);  
            System.exit(1);  
        } finally {  
            try {  
                if (conn != null) {  
                    conn.close();  
                    conn = null;  
                }  
                if (stmt != null) {  
                    stmt.close();  
                    stmt = null;  
                }  
            } catch (SQLException e) {  
                e.printStackTrace();  
            }  
        }  
    }  

    private static void countData(Statement stmt, String tableName)  
            throws SQLException {  
        sql = "select count(1) from " + tableName;  
        System.out.println("Running:" + sql);  
        res = stmt.executeQuery(sql);  
        System.out.println("执行“regular hive query”运行结果:");  
        while (res.next()) {  
            System.out.println("count ------&gt;" + res.getString(1));  
        }  
    }  

    private static void selectData(Statement stmt, String tableName)  
            throws SQLException {  
        sql = "select * from " + tableName;  
        System.out.println("Running:" + sql);  
        res = stmt.executeQuery(sql);  
        System.out.println("执行 select * query 运行结果:");  
        while (res.next()) {  
            System.out.println(res.getInt(1) + "\t" + res.getString(2));  
        }  
    }  

    private static void loadData(Statement stmt, String tableName)  
            throws SQLException {  
        //目录 ，我的是hive安装的机子的虚拟机的home目录下
        String filepath = "user.txt";  
        sql = "load data local inpath '" + filepath + "' into table "  
                + tableName;  
        System.out.println("Running:" + sql);  
         stmt.execute(sql);  
    }  

    private static void describeTables(Statement stmt, String tableName)  
            throws SQLException {  
        sql = "describe " + tableName;  
        System.out.println("Running:" + sql);  
        res = stmt.executeQuery(sql);  
        System.out.println("执行 describe table 运行结果:");  
        while (res.next()) {  
            System.out.println(res.getString(1) + "\t" + res.getString(2));  
        }  
    }  

    private static void showTables(Statement stmt, String tableName)  
            throws SQLException {  
        sql = "show tables '" + tableName + "'";  
        System.out.println("Running:" + sql);  
        res = stmt.executeQuery(sql);  
        System.out.println("执行 show tables 运行结果:");  
        if (res.next()) {  
            System.out.println(res.getString(1));  
        }  
    }  

    private static void createTable(Statement stmt, String tableName)  
            throws SQLException {  
        sql = "create table "  
                + tableName  
                + " (key int, value string)  row format delimited fields terminated by '\t'";  
        stmt.execute(sql);  
    }  

    private static String dropTable(Statement stmt) throws SQLException {  
        // 创建的表名  
        String tableName = "testHive";  
        sql = "drop table  " + tableName;  
        stmt.execute(sql);  
        return tableName;  
    }  

    private static Connection getConn() throws ClassNotFoundException,  
            SQLException {  
        Class.forName(driverName);  
        Connection conn = DriverManager.getConnection(url, user, password);  
        return conn;  
    }  

}  </code></pre>
            </div>
                </div>
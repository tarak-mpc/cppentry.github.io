---
layout:     post
title:      HIVE---HIve的JavaAPI操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>基于hadoop的Hive数据仓库JavaAPI简单调用的实例，关于Hive的简介在此不赘述。hive提供了三种用户接口：CLI，JDBC/ODBC和 WebUI <br>
CLI，即Shell命令行 <br>
JDBC/ODBC 是 Hive 的Java，与使用传统数据库JDBC的方式类似 <br>
WebGUI是通过浏览器访问 Hive <br>
本文主要介绍的就是第二种用户接口，直接进入正题。 <br>
 1、Hive 安装： <br>
        1）hive的安装请参考网上的相关文章，测试时只在hadoop一个节点上安装hive即可。 <br>
        2）测试数据data文件’\t’分隔： <br>
              1   zhangsan <br>
              2    lisi <br>
              3   wangwu <br>
        3）将测试数据data上传到linux目录下，我放置在：/home/hadoop01/data  <br>
 2、在使用 JDBC 开发 Hive 程序时,  必须首先开启 Hive 的远程服务接口。使用下面命令进行开启:</p>

<p>Java代码  收藏代码 <br>
hive –service hiveserver &gt;/dev/null 2&gt;/dev/null &amp;  </p>

<p>3、测试代码：</p>

<p>Java代码  收藏代码 <br>
import java.sql.Connection; <br>
import java.sql.DriverManager; <br>
import java.sql.ResultSet; <br>
import java.sql.SQLException; <br>
import java.sql.Statement;  </p>

<p>import org.apache.log4j.Logger;  </p>

<p>/**  <br>
 * Hive的JavaApi  <br>
 * <br>
 * 启动hive的远程服务接口命令行执行：hive –service hiveserver &gt;/dev/null 2&gt;/dev/null &amp;  <br>
 * <br>
 * @author 吖大哥  <br>
 * <br>
 */ <br>
public class HiveJdbcCli {  </p>

<pre><code>private static String driverName = "org.apache.hadoop.hive.jdbc.HiveDriver";  
private static String url = "jdbc:hive://hadoop3:10000/default";  
private static String user = "hive";  
private static String password = "mysql";  
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
    String filepath = "/home/hadoop01/data";  
    sql = "load data local inpath '" + filepath + "' into table "  
            + tableName;  
    System.out.println("Running:" + sql);  
    res = stmt.executeQuery(sql);  
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
    stmt.executeQuery(sql);  
}  

private static String dropTable(Statement stmt) throws SQLException {  
    // 创建的表名  
    String tableName = "testHive";  
    sql = "drop table " + tableName;  
    stmt.executeQuery(sql);  
    return tableName;  
}  

private static Connection getConn() throws ClassNotFoundException,  
        SQLException {  
    Class.forName(driverName);  
    Connection conn = DriverManager.getConnection(url, user, password);  
    return conn;  
}  
</code></pre>

<p>}  </p>

<p>4、测试结果</p>

<p>5、终端查询结果： <br>
hive&gt; select * from testHive; <br>
OK <br>
1       zhangsan <br>
2       lisi <br>
3       wangwu <br>
Time taken: 11.232 seconds <br>
hive&gt; </p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
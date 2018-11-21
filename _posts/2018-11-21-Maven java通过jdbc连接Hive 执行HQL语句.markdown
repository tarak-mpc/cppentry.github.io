---
layout:     post
title:      Maven java通过jdbc连接Hive 执行HQL语句
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">HADOOP HIVE 版本</span></p>
<p></p><pre><code class="language-python">lrwxrwxrwx.  1 hadoop hadoop        12 May 14 09:53 hadoop -&gt; hadoop-2.2.0
drwxr-xr-x. 10 hadoop hadoop      4096 May 14 16:45 hadoop-2.2.0
lrwxrwxrwx.  1 hadoop hadoop        15 May 16 15:38 hive -&gt; hive-0.12.0-bin
drwxrwxr-x.  8 hadoop hadoop      4096 May 16 15:38 hive-0.12.0-bin
</code></pre><br>
pom.xml
<p></p><pre><code class="language-python">&lt;project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
	&lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;
	&lt;groupId&gt;com.cn.hive&lt;/groupId&gt;
	&lt;artifactId&gt;HiveTest&lt;/artifactId&gt;
	&lt;version&gt;0.0.1-SNAPSHOT&lt;/version&gt;
	&lt;dependencies&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
			&lt;artifactId&gt;hadoop-common&lt;/artifactId&gt;
			&lt;version&gt;2.2.0&lt;/version&gt;
			&lt;classifier&gt;tests&lt;/classifier&gt;
		&lt;/dependency&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
			&lt;artifactId&gt;hadoop-core&lt;/artifactId&gt;
			&lt;version&gt;1.2.1&lt;/version&gt;
		&lt;/dependency&gt;
		&lt;dependency&gt;
			&lt;groupId&gt;org.apache.hive&lt;/groupId&gt;
			&lt;artifactId&gt;hive-jdbc&lt;/artifactId&gt;
			&lt;version&gt;0.12.0&lt;/version&gt;
		&lt;/dependency&gt;
	&lt;/dependencies&gt;
&lt;/project&gt;	</code></pre><br><span style="font-size:18px;">网上说的只需要添加hadoop-common跟hive-jdbc的依赖 我试过是不行的</span>
<p><span style="font-size:18px;">否则会报找不到hadoop writable classnotfound的错误</span></p>
<p><span style="font-size:18px;">  需要加上hadoop-core</span></p>
<p><span style="font-size:18px;">然后启动 hiveserver<br></span></p>
<p><span style="font-size:18px;"></span></p><pre><code class="language-python">[hadoop@localhost Desktop]$  hive --service hiveserver -p 10002
Starting Hive Thrift Server
14/06/06 15:25:43 INFO Configuration.deprecation: mapred.input.dir.recursive is deprecated. Instead, use mapreduce.input.fileinputformat.input.dir.recursive
14/06/06 15:25:43 INFO Configuration.deprecation: mapred.max.split.size is deprecated. Instead, use mapreduce.input.fileinputformat.split.maxsize
14/06/06 15:25:43 INFO Configuration.deprecation: mapred.min.split.size is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize
14/06/06 15:25:43 INFO Configuration.deprecation: mapred.min.split.size.per.rack is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize.per.rack
14/06/06 15:25:43 INFO Configuration.deprecation: mapred.min.split.size.per.node is deprecated. Instead, use mapreduce.input.fileinputformat.split.minsize.per.node
14/06/06 15:25:43 INFO Configuration.deprecation: mapred.reduce.tasks is deprecated. Instead, use mapreduce.job.reduces
14/06/06 15:25:43 INFO Configuration.deprecation: mapred.reduce.tasks.speculative.execution is deprecated. Instead, use mapreduce.reduce.speculative
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/home/hadoop/hadoop-2.2.0/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/home/hadoop/hive-0.12.0-bin/lib/slf4j-log4j12-1.6.1.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
</code></pre><br>
程序HiveJDBCTest.java:
<p><span style="font-size:18px;"></span></p><pre><code class="language-python">import java.sql.SQLException;
import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;
import java.sql.DriverManager;

public class HiveJDBCTest {
    
    private static String driverName = 
                   "org.apache.hadoop.hive.jdbc.HiveDriver";
  
    public static void main(String[] args) 
                            throws SQLException {
        try {
            Class.forName(driverName);
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
            System.exit(1);
        }

        Connection con = DriverManager.getConnection(
                           "jdbc:hive://localhost:10002/default", "hadoop", "");
        Statement stmt = con.createStatement();
        String tableName = "jdbcTest";
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
</code></pre><br>
右键 run as java application
<p><span style="font-size:18px;"></span></p><pre><code class="language-python">OK
OK
OK
OK
OK
Total MapReduce jobs = 1
Launching Job 1 out of 1
Number of reduce tasks determined at compile time: 1
In order to change the average load for a reducer (in bytes):
  set hive.exec.reducers.bytes.per.reducer=&lt;number&gt;
In order to limit the maximum number of reducers:
  set hive.exec.reducers.max=&lt;number&gt;
In order to set a constant number of reducers:
  set mapred.reduce.tasks=&lt;number&gt;
Starting Job = job_1402018629600_0001, Tracking URL = http://localhost:8088/proxy/application_1402018629600_0001/
Kill Command = /home/hadoop/hadoop/bin/hadoop job  -kill job_1402018629600_0001
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
2014-06-06 15:45:43,176 Stage-1 map = 0%,  reduce = 0%
2014-06-06 15:46:20,535 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:22,443 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:23,479 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:24,513 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:25,547 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:26,735 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:27,771 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:28,806 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:29,841 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:30,881 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:31,916 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:32,952 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:33,986 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:35,033 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:36,070 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:37,123 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:38,161 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:39,193 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:40,229 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:41,276 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:42,316 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:43,351 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:44,394 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:45,432 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:46,467 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.28 sec
2014-06-06 15:46:47,569 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.88 sec
2014-06-06 15:46:48,614 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.88 sec
2014-06-06 15:46:49,658 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.88 sec
2014-06-06 15:46:50,693 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.88 sec
2014-06-06 15:46:51,737 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.88 sec
2014-06-06 15:46:52,784 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.88 sec
2014-06-06 15:46:53,825 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.88 sec
MapReduce Total cumulative CPU time: 2 seconds 880 msec
Ended Job = job_1402018629600_0001
MapReduce Jobs Launched: 
Job 0: Map: 1  Reduce: 1   Cumulative CPU: 2.88 sec   HDFS Read: 263 HDFS Write: 2 SUCCESS
Total MapReduce CPU Time Spent: 2 seconds 880 msec
OK
OK
OK
OK
OK
OK
Total MapReduce jobs = 1
Launching Job 1 out of 1
Number of reduce tasks determined at compile time: 1
In order to change the average load for a reducer (in bytes):
  set hive.exec.reducers.bytes.per.reducer=&lt;number&gt;
In order to limit the maximum number of reducers:
  set hive.exec.reducers.max=&lt;number&gt;
In order to set a constant number of reducers:
  set mapred.reduce.tasks=&lt;number&gt;
Starting Job = job_1402018629600_0002, Tracking URL = http://localhost:8088/proxy/application_1402018629600_0002/
Kill Command = /home/hadoop/hadoop/bin/hadoop job  -kill job_1402018629600_0002
Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
2014-06-06 16:12:55,686 Stage-1 map = 0%,  reduce = 0%
2014-06-06 16:13:11,881 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:12,924 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:13,961 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:14,995 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:16,032 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:17,069 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:18,108 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:19,178 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:20,225 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:21,268 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:22,308 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:23,353 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:24,401 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 1.36 sec
2014-06-06 16:13:25,447 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.95 sec
2014-06-06 16:13:26,486 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 2.95 sec
MapReduce Total cumulative CPU time: 2 seconds 950 msec
Ended Job = job_1402018629600_0002
MapReduce Jobs Launched: 
Job 0: Map: 1  Reduce: 1   Cumulative CPU: 2.95 sec   HDFS Read: 264 HDFS Write: 2 SUCCESS
Total MapReduce CPU Time Spent: 2 seconds 950 msec
OK

</code></pre>控制台输出：
<p><span style="font-size:18px;"></span></p><pre><code class="language-python">Create table success!
Running: show tables 'jdbcTest'
jdbctest
Running: describe jdbcTest
key                 	int                 
value               	string              
Running: select count(1) from jdbcTest
0
</code></pre><br>
OK！大功告成～～～
<p><span style="font-size:18px;"><br></span><br></p>
<p><br><span style="font-size:18px;"></span></p>
            </div>
                </div>
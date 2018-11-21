---
layout:     post
title:      用Java代码通过JDBC连接Hiveserver
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span></span>我们可以通过CLI、Client、Web UI等Hive提供的方式来和Hive通信，但这三种方式中最常用的是CLI；Client是Hive的客户端，用户连接至Hive Server。在启动Client模式的时候，需要指出Hive Server所在的节点，并且在该节点启动Hive Server。Web UI的方式是通过浏览器访问Hive。今天我们来谈谈怎么通过HiveServer来操作Hive。</p>
<p><span></span>Hive提供了JDBC驱动，使得我们可以用Java代码来连接Hive并进行一些类似于关系型数据库的sql语句查询操作。同关系型数据库一样，我们也需要将Hive的服务打开；在Hive0.11.0版本之前，只有HiveServer服务可用，你得在程序操作Hive之前，在Hive安装的服务器上打开HiveServer服务，如下：</p>
<p></p><pre><code class="language-java">[wyp@localhost /home/q/hive-0.11.0]$ bin/hive --service hiveserver -p 10001
</code></pre>上面的命令表示你已经成功打开的端口为10001(默认的端口是10000)启动了HiveServer服务。这时候，你就可以通过Java代码来连接HiveServer，代码如下：
<p></p><pre><code class="language-java">public class HiveJdbcTest {

	//定义HiveJDBC驱动名
	private static String driverName = "org.apache.hadoop.hive.jdbc.HiveDriver";
	
	public static void main(String[] args) {
		
		try {
			//加载驱动
			Class.forName(driverName);
			//获取连接
			Connection connection = DriverManager.getConnection("jdbc:hive://master:10001/hive", "root", "1111");
			//获取Statement
			Statement statement = connection.createStatement();
			
			//定义运行的HQL语句
			String queryHQL = "select name,salary from employees";
			
			//执行查询语句
			ResultSet res = statement.executeQuery(queryHQL);
			
			//遍历集合取数据
			while (res.next()){
				System.out.println(res.getString(1) +"\t"+ res.getFloat(2));
			}
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
}
</code></pre>程序运行结果：
<p><img src="https://img-blog.csdn.net/20150125201121542" alt=""></p>
<p><br></p>
<p><span></span>上面是用Java连接HiveServer，而HiveServer本身存在很多问题(比如：安全性、并发性等)；针对这些问题，Hive0.11.0版本提供了一个全新的服务：HiveServer2，这个服务很好的解决了HiveServer存在的安全性、并发性等问题。这个服务的启动程序在${HIVE_HOME}/bin/hiveserver2里面，你可以通过下面的方式来启动HiveServer2服务：</p>
<p></p><pre><code class="language-java">$HIVE_HOME/bin/hiveserver2
</code></pre><br>
也可以通过下面的方式启动HiveServer2
<p></p><pre><code class="language-java">$HIVE_HOME/bin/hive --service hiveserver2
</code></pre><br>
两种方式效果都是一样的。但是以前的程序需要修改两个地方，如下所示：
<p></p><pre><code class="language-java">private static String driverName = "org.apache.hadoop.hive.jdbc.HiveDriver";
改为
private static String driverName = "org.apache.hive.jdbc.HiveDriver";


Connection con = DriverManager.getConnection("jdbc:hive://master:10001/hive", "root", "1111");
改为
Connection con = DriverManager.getConnection("jdbc:hive2://master:10001/hive", "root", "1111");</code></pre><br>
其它的不变就可以了。
<p><br></p>
<p>项目所使用的Maven依赖：</p>
<p></p><pre><code class="language-java">&lt;dependency&gt;
        &lt;groupId&gt;org.apache.hive&lt;/groupId&gt;
        &lt;artifactId&gt;hive-jdbc&lt;/artifactId&gt;
        &lt;version&gt;0.11.0&lt;/version&gt;
&lt;/dependency&gt;

&lt;dependency&gt;
        &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
        &lt;artifactId&gt;hadoop-common&lt;/artifactId&gt;
        &lt;version&gt;2.2.0&lt;/version&gt;
&lt;/dependency&gt;</code></pre><br><br><p>文章来自<a href="http://www.iteblog.com/" rel="nofollow">过往记忆</a>：<a href="http://www.iteblog.com/archives/846" rel="nofollow">http://www.iteblog.com/archives/846</a></p>
<p><br><br><br></p>
            </div>
                </div>
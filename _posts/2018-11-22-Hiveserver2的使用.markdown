---
layout:     post
title:      Hiveserver2的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hive的版本是hive-2.1.1</p>
<p>日志放在哪？</p>
<p><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">Hive中的日志分为两种</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">1. 系统日志，记录了hive的运行情况，错误状况。</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">2. Job 日志，记录了Hive 中job的执行的历史过程。</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">系统日志存储在什么地方呢 ？</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">在hive/conf/ hive-log4j.properties 文件中记录了Hive日志的存储情况，</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">默认的存储情况：</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">hive.root.logger=WARN,DRFA</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">hive.log.dir=/tmp/${user.name} # 默认的存储位置</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">hive.log.file=hive.log  # 默认的文件名</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">Job日志又存储在什么地方呢 ？</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">//Location of Hive run time structured log file</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">    HIVEHISTORYFILELOC("hive.querylog.location", "/tmp/" + System.getProperty("user.name")),</span><br style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;"><span style="color:rgb(152,131,76);font-family:Arial, Helvetica, simsun, u5b8bu4f53;font-size:14px;">默认存储与 /tmp/{user.name}目录下。</span><br></p>
<p><br></p>
<p><span style="color:#ff0000;">注意：在建立JDBC连接时候的连接是 hive2,hive从此以后的hiveserver是hive2了</span></p>
<p><span style="color:#ff0000;">出现了错误：</span><span style="color:rgb(52,152,219);font-family:'微软雅黑';font-size:18px;">User: hadoop is not allowed to impersonate anonymous</span></p>
<p><span style="color:rgb(52,152,219);font-family:'微软雅黑';font-size:18px;">解决方案</span></p>
<p><span style="color:rgb(52,152,219);font-family:'微软雅黑';font-size:18px;"></span></p>
<pre style="color:rgb(51,51,51);font-size:14px;font-weight:bold;font-family:ConfluenceInstalledFont, monospace;line-height:1.3;background-color:rgb(255,255,255);"><span class="quote_div" style="font-family:'ff-tisa-web-pro-1', 'ff-tisa-web-pro-2', 'Lucida Grande', 'Helvetica Neue', Helvetica, Arial, 'Hiragino Sans GB', 'Hiragino Sans GB W3', 'Microsoft YaHei UI', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif;line-height:20px;">&lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.root.hosts&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;</span></pre>
<pre style="color:rgb(51,51,51);font-size:14px;font-weight:bold;font-family:ConfluenceInstalledFont, monospace;line-height:1.3;background-color:rgb(255,255,255);"><span class="quote_div" style="font-family:'ff-tisa-web-pro-1', 'ff-tisa-web-pro-2', 'Lucida Grande', 'Helvetica Neue', Helvetica, Arial, 'Hiragino Sans GB', 'Hiragino Sans GB W3', 'Microsoft YaHei UI', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif;line-height:20px;">
&lt;/property&gt;
&lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.root.groups&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;</span></pre>
<pre style="color:rgb(51,51,51);font-size:14px;font-weight:bold;font-family:ConfluenceInstalledFont, monospace;line-height:1.3;background-color:rgb(255,255,255);"><span class="quote_div" style="font-family:'ff-tisa-web-pro-1', 'ff-tisa-web-pro-2', 'Lucida Grande', 'Helvetica Neue', Helvetica, Arial, 'Hiragino Sans GB', 'Hiragino Sans GB W3', 'Microsoft YaHei UI', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif;line-height:20px;">
&lt;/property&gt;</span></pre>
<br><p></p>
<p><span style="color:rgb(52,152,219);font-family:'微软雅黑';font-size:18px;"><br></span></p>
<p>JDBCUtil.java</p>
<p></p>
<pre><code class="language-java">import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class JDBCUtils {
	private static String driver = "org.apache.hive.jdbc.HiveDriver";
	private static String url = "jdbc:hive2://192.168.56.100:10000/default";
	
	
	//注册驱动
	static {
		try {
			Class.forName(driver);
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
	}
	//获取连接
	public static Connection getConnection(){
		try {
			return DriverManager.getConnection(url,"root","root");
		} catch (SQLException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}
		return null;
	}
	
	//释放资源
	public static void release(Connection conn,Statement st,ResultSet rs){
		if(rs != null){
			try {
				rs.close();	
			} catch (Exception e) {
				// TODO: handle exception
				e.printStackTrace();
			}finally {
				rs = null;
			}

		}
		if(st != null){
			try {
				st.close();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}finally {
				st = null;
			}
		}
		if(conn != null){
			try {
				conn.close();
			} catch (SQLException e) {
				// TODO Auto-generated catch block
				e.printStackTrace();
			}finally {
				conn = null;
			}
		}
	}	
			
}</code></pre><br>
HiveJDBCDemon.java
<p></p>
<p></p>
<pre><code class="language-java">import java.sql.Connection;
import java.sql.ResultSet;
import java.sql.Statement;

public class HIveJDBCDemon {
	public static void main(String[] args) {
		Connection conn = null;
		Statement st = null;
		ResultSet rs = null;
		String sql = "select * from word_counts";
		
		
		try {
			//获取连接
			conn = JDBCUtils.getConnection();
			
			System.out.println(conn);
			//创建运行环境
			st = conn.createStatement();
			//运行HSQL
			rs = st.executeQuery(sql);
			
			//处理数据
			while(rs.next()){
			
				String word = rs.getString(1);
				int count = rs.getInt(2);
				System.out.println(word + "\t" + count);
			}
		} catch (Exception e) {
			// TODO: handle exception
			e.printStackTrace();
		}finally {
			JDBCUtils.release(conn, st, rs);
		}
	}
}</code></pre>
<p></p>
<p><br></p>
<p>//下面是hadoop 官网的详解</p>
<p>http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-common/Superusers.html<br></p>
<p></p>
<pre><code class="language-plain">Configurations

You can configure proxy user using properties hadoop.proxyuser.$superuser.hosts along with either or both of hadoop.proxyuser.$superuser.groups and hadoop.proxyuser.$superuser.users.

By specifying as below in core-site.xml, the superuser named super can connect only from host1 and host2 to impersonate a user belonging to group1 and group2.

   &lt;property&gt;
     &lt;name&gt;hadoop.proxyuser.super.hosts&lt;/name&gt;
     &lt;value&gt;host1,host2&lt;/value&gt;
   &lt;/property&gt;
   &lt;property&gt;
     &lt;name&gt;hadoop.proxyuser.super.groups&lt;/name&gt;
     &lt;value&gt;group1,group2&lt;/value&gt;
   &lt;/property&gt;
If these configurations are not present, impersonation will not be allowed and connection will fail.

If more lax security is preferred, the wildcard value * may be used to allow impersonation from any host or of any user. For example, by specifying as below in core-site.xml, user named oozie accessing from any host can impersonate any user belonging to any group.

  &lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.oozie.hosts&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hadoop.proxyuser.oozie.groups&lt;/name&gt;
    &lt;value&gt;*&lt;/value&gt;
  &lt;/property&gt;
The hadoop.proxyuser.$superuser.hosts accepts list of ip addresses, ip address ranges in CIDR format and/or host names. For example, by specifying as below, user named super accessing from hosts in the range 10.222.0.0-15 and 10.113.221.221 can impersonate user1 and user2.

   &lt;property&gt;
     &lt;name&gt;hadoop.proxyuser.super.hosts&lt;/name&gt;
     &lt;value&gt;10.222.0.0/16,10.113.221.221&lt;/value&gt;
   &lt;/property&gt;
   &lt;property&gt;
     &lt;name&gt;hadoop.proxyuser.super.users&lt;/name&gt;
     &lt;value&gt;user1,user2&lt;/value&gt;
   &lt;/property&gt;</code></pre><br><br><p></p>
<p><br></p>
<br><br><p><br></p>
            </div>
                </div>
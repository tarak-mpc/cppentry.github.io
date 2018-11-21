---
layout:     post
title:      Hbase的SQL接口之Phoenix使用总结（1）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sela01/article/details/8707898				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;">#最近在写操作HBase的接口，顺便研究了一下Phoenix，简单的介绍下Phoenix，Phoenix用Java实现，人们通过Phoenix，可以用自己所熟悉的SQL语言来操作HBase,当然它是开源的。</span></p>
<p style="color:rgb(0,0,0);"><span style="font-size:14px;"><br></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;">1.如何让HBase支持Phoenix？</span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;"><br></span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;">将phoenix-1.1.jar复制到HBase集群每个节点的<span style="font-size:14px;"><span style="color:#000000;">HBase文件夹下的lib目录，然后重启HBase</span></span></span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;"><span style="font-size:14px;"><span style="color:#000000;"><img src="http://img.my.csdn.net/uploads/201303/22/1363955939_9716.png" alt=""></span></span></span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;"><span style="font-size:14px;"><span style="color:#000000;">2.客户端怎么通过Phoenix访问或操作Hbase?</span></span></span></span></p>
<p style="color:rgb(0,102,0);"><br><span style="font-size:14px;"><span style="color:#000000;"></span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;">在你自己的Java项目下，引用phoenix-1.1-client.jar</span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;"><br></span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;"><img src="http://img.my.csdn.net/uploads/201303/22/1363956319_9333.png" alt=""></span></span></p>
<p style="color:rgb(0,102,0);"><br><span style="font-size:14px;"><span style="color:#000000;"></span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;">下面给出使用Phoenix基本的代码：</span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;"></span></span></p>
<pre><code class="language-java">public class HBaseUtility {

	static {
		try {
			Class.forName("com.salesforce.phoenix.jdbc.PhoenixDriver");
		} catch (ClassNotFoundException e) {
			throw new HBaseException(e);
		}
	}

	public static Connection getConnection() {
		String getDBConnectionString = "jdbc:phoenix:hadoop.master"; // 从配置文件中读取链接字符串
		try {
			Connection _Connection = DriverManager
					.getConnection(getDBConnectionString);
			return _Connection;
		} catch (SQLException e) {
			throw new HBaseException(e.getMessage(), e);
		}
	}

}</code></pre>
<p></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><br><span style="color:#000000;"></span></span></p>
<pre><code class="language-java">public class HBaseHelper {

	static HBaseHelper _HBaseHelper = null;
	Connection _Connection = null;
	Statement _Statement = null;
	PreparedStatement _PreparedStatement = null;
	String _getExceptionInfoString = "";
	String _getDBConnectionString = "";

	private HBaseHelper() {}		

	/*
	 * Initialization
	 */
	public static HBaseHelper getInstanceBaseHelper() {
		if (_HBaseHelper == null)
			synchronized (HBaseHelper.class) {
				if(_HBaseHelper==null)
					_HBaseHelper = new HBaseHelper();
			}			
		return _HBaseHelper;
	}

	/*
	 * Insert , Delete , Update
	 */
	public Object ExcuteNonQuery(String sql) {
		int n = 0;
		try {
			_Connection =HBaseUtility.getConnection();
			_Statement = _Connection.createStatement();
			n = _Statement.executeUpdate(sql);
			_Connection.commit();
		} catch (Exception e) {
			Dispose();
			throw new HBaseException(e.getMessage(),e);
		} 
		return n;
	}

	public Object ExcuteNonQuery(String sql, Object[] args) {
		int n = 0;
		try {
			_Connection =HBaseUtility.getConnection();
			_PreparedStatement = _Connection.prepareStatement(sql);
			for (int i = 0; i &lt; args.length; i++)
				_PreparedStatement.setObject(i + 1, args[i]);
			n = _PreparedStatement.executeUpdate();
			_Connection.commit();
		} catch (SQLException e) {
			Dispose();
			throw new HBaseException(e.getMessage(),e);
		}
		return n;
	}

	/*
	 * Query
	 */
	public ResultSet ExecuteQuery(String sql) {
		ResultSet rsResultSet = null;
		try {
			_Connection =HBaseUtility.getConnection();
			_Statement = _Connection.createStatement();
			rsResultSet = _Statement.executeQuery(sql);
		} catch (Exception e) {
			Dispose();
			throw new HBaseException(e.getMessage(),e);
		} 
		return rsResultSet;
	}

	public ResultSet ExceteQuery(String sql, Object[] args) {
		ResultSet rsResultSet = null;
		try {
			_Connection =HBaseUtility.getConnection();
			_PreparedStatement = _Connection.prepareStatement(sql);
			for (int i = 0; i &lt; args.length; i++)
				_PreparedStatement.setObject(i + 1, args[i]);
			rsResultSet = _PreparedStatement.executeQuery();

		} catch (Exception e) {
			Dispose();
			throw new HBaseException(e.getMessage(),e);
		} 
		return rsResultSet;
	}

	public void Dispose() {
		try {
			if (_Connection != null)
				_Connection.close();
			if (_Statement != null)
				_Statement.close();
		} catch (Exception e) {
			// TODO: handle exception
			_getExceptionInfoString = e.getMessage();
		}
	}
}
</code></pre>
<p></p>
<p style="color:rgb(0,102,0);"><br><span style="font-size:14px;"><span style="color:#000000;"></span></span></p>
<p style="color:rgb(0,102,0);"><span style="font-size:14px;"><span style="color:#000000;">以上是我写的一个基本的DBHelper类。因为自己不太会写Java代码，如果有不足之处，请各位指出。</span></span></p>
<p style="color:rgb(0,102,0);"><br><span style="font-size:14px;"><span style="color:#000000;"></span></span></p>
<p><span style="font-size:14px;">关于Phoenix的详细信息，请参考：<a href="http://blog.csdn.net/ricohzhanglong/article/details/8587493" rel="nofollow"><br></a></span></p>
<p><span style="font-size:14px;">1.<a href="http://blog.csdn.net/ricohzhanglong/article/details/8587493" rel="nofollow">http://blog.csdn.net/ricohzhanglong/article/details/8587493</a></span></p>
<p><span style="font-size:14px;">2.<a href="https://github.com/forcedotcom/phoenix" rel="nofollow">https://github.com/forcedotcom/phoenix</a></span></p>
<p><br></p>
<p><span style="font-size:14px;"><span style="color:#000000;">待续。。。。</span></span><br><a href="http://blog.csdn.net/ricohzhanglong/article/details/8587493" rel="nofollow"></a></p>
            </div>
                </div>
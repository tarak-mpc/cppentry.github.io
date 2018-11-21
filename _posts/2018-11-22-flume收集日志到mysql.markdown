---
layout:     post
title:      flume收集日志到mysql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>flume基本应用请参考:<a href="http://blog.csdn.net/liangrui1988/article/details/71774663" rel="nofollow">http://blog.csdn.net/liangrui1988/article/details/71774663</a></p>
<p>下面这个是将收集到的日志写到mysql中</p>
<p>github例子:<a href="https://github.com/liangrui1988/flume_mysql" rel="nofollow">https://github.com/liangrui1988/flume_mysql</a></p>
<p><br></p>
<p>pom:</p>
<p></p>
<pre><code class="language-html">&lt;?xml version="1.0" encoding="UTF-8"?&gt;
  &lt;project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"&gt;
  &lt;modelVersion&gt;4.0.0&lt;/modelVersion&gt;

  &lt;groupId&gt;com.flume.dome&lt;/groupId&gt;
  &lt;artifactId&gt;flume_mysql&lt;/artifactId&gt;
  &lt;version&gt;0.0.1&lt;/version&gt;
&lt;!--   &lt;packaging&gt;jar&lt;/packaging&gt; --&gt;
  &lt;name&gt;flume_mysql&lt;/name&gt;
  &lt;url&gt;http://maven.apache.org&lt;/url&gt;
  &lt;properties&gt;
    &lt;project.build.sourceEncoding&gt;UTF-8&lt;/project.build.sourceEncoding&gt;
              &lt;version.flume&gt;1.7.0&lt;/version.flume&gt;
  &lt;/properties&gt;
      &lt;dependencies&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.flume&lt;/groupId&gt;
            &lt;artifactId&gt;flume-ng-core&lt;/artifactId&gt;
            &lt;version&gt;${version.flume}&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.flume&lt;/groupId&gt;
            &lt;artifactId&gt;flume-ng-configuration&lt;/artifactId&gt;
            &lt;version&gt;${version.flume}&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java --&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;mysql&lt;/groupId&gt;
            &lt;artifactId&gt;mysql-connector-java&lt;/artifactId&gt;
            &lt;version&gt;5.1.42&lt;/version&gt;
        &lt;/dependency&gt;
        	&lt;dependency&gt;
			&lt;groupId&gt;junit&lt;/groupId&gt;
			&lt;artifactId&gt;junit&lt;/artifactId&gt;
			&lt;version&gt;4.8.2&lt;/version&gt;
			&lt;scope&gt;test&lt;/scope&gt;
		&lt;/dependency&gt;
    &lt;/dependencies&gt;
&lt;/project&gt;
</code></pre><br><br><p></p>
<p>自定义一个sink写入到mysql: java代码</p>
<p></p>
<pre><code class="language-java">package com.flume.dome.mysink;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
import java.util.List;

import org.apache.flume.Channel;
import org.apache.flume.Context;
import org.apache.flume.Event;
import org.apache.flume.EventDeliveryException;
import org.apache.flume.Transaction;
import org.apache.flume.conf.Configurable;
import org.apache.flume.sink.AbstractSink;
import org.mortbay.log.Log;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.google.common.base.Preconditions;
import com.google.common.base.Throwables;
import com.google.common.collect.Lists;

public class MysqlSink extends AbstractSink implements Configurable {

	private Logger LOG = LoggerFactory.getLogger(MysqlSink.class);
	private String hostname;
	private String port;
	private String databaseName;
	private String tableName;
	private String user;
	private String password;
	private PreparedStatement preparedStatement;
	private Connection conn;
	private int batchSize;// 批处理数量

	public MysqlSink() {
		LOG.info("MysqlSink start...");
	}

	public Status process() throws EventDeliveryException {
		Status result = Status.READY;
		Channel channel = getChannel();
		Transaction transaction = channel.getTransaction();
		Event event;
		String content;

		// 数据集合
		List&lt;String&gt; actions = Lists.newArrayList();
		transaction.begin();
		try {
			for (int i = 0; i &lt; batchSize; i++) {
				event = channel.take();// 从通道中获取数据
				if (event != null) {
					content = new String(event.getBody());
					actions.add(content);
				} else {
					result = Status.BACKOFF;
					break;
				}
			}
			if (actions.size() &gt; 0) {
				preparedStatement.clearBatch();
				for (String temp : actions) {
					Log.info("actions temp:{}", temp);
					// 对占位符设置值，占位符顺序从1开始，第一个参数是占位符的位置，第二个参数是占位符的值。
					preparedStatement.setString(1, temp);
					preparedStatement.setLong(2, System.currentTimeMillis());
					preparedStatement.addBatch();
				}
				preparedStatement.executeBatch();
				conn.commit();
			}
			transaction.commit();
		} catch (Throwable e) {
			try {
				transaction.rollback();
			} catch (Exception e2) {
				LOG.error("Exception in rollback. Rollback might not have been"
						+ "successful.", e2);
			}
			LOG.error("Failed to commit transaction."
					+ "Transaction rolled back.", e);
			Throwables.propagate(e);
		} finally {
			transaction.close();
		}
		return result;
	}

	public void configure(Context context) {
		hostname = context.getString("hostname");
		Preconditions.checkNotNull(hostname, "hostname must be set!!");
		port = context.getString("port");
		Preconditions.checkNotNull(port, "port must be set!!");
		databaseName = context.getString("databaseName");
		Preconditions.checkNotNull(databaseName, "databaseName must be set!!");
		tableName = context.getString("tableName");
		Preconditions.checkNotNull(tableName, "tableName must be set!!");
		user = context.getString("user");
		Preconditions.checkNotNull(user, "user must be set!!");
		password = context.getString("password");
		Preconditions.checkNotNull(password, "password must be set!!");
		batchSize = context.getInteger("batchSize", 100);
		Preconditions.checkNotNull(batchSize &gt; 0,
				"batchSize must be a positive number!!");

	}

	@Override
	public synchronized void start() {
		super.start();
		try {
			// 调用Class.forName()方法加载驱动程序
			Class.forName("com.mysql.jdbc.Driver");
		} catch (ClassNotFoundException e) {
			e.printStackTrace();
		}
		String url = "jdbc:mysql://" + hostname + ":" + port + "/"
				+ databaseName;
		Log.info("mysql start url:{}", url);
		// 调用DriverManager对象的getConnection()方法，获得一个Connection对象
		try {
			conn = DriverManager.getConnection(url, user, password);
			conn.setAutoCommit(false);
			// 创建一个Statement对象
			preparedStatement = conn.prepareStatement("insert into "
					+ tableName + " (context,time) values (?,?)");
		} catch (SQLException e) {
			e.printStackTrace();
			System.exit(1);
		}
	}

	@Override
	public synchronized void stop() {
		super.stop();
		if (preparedStatement != null) {
			try {
				preparedStatement.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}

		if (conn != null) {
			try {
				conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}

}
</code></pre><br><br><p></p>
<p><br><br>
mvn clean <br>
mvn install<br>
将代码打成jar包后，上传到flume安装目录下的lib文件夹中，同时需要上传MySQL的驱动jar包<br><br>
-------------测试 Spooling Directory Source--------------------------<br>
conf：mysql_sink.conf<br></p>
<pre><code class="language-html"># Name the components on this agent
a1.sources = r1
a1.sinks = k1
a1.channels = c1


# Describe/configure the source
a1.sources.r1.type = spooldir
a1.sources.r1.spoolDir = /home/rui/log/flumespool
a1.sources.r1.fileHeader = true
a1.sources.r1.channels = c1


# Describe the sink
a1.sinks.k1.type = com.flume.dome.mysink.MysqlSink
a1.sinks.k1.hostname = 192.168.254.1
a1.sinks.k1.port = 3306
a1.sinks.k1.databaseName = flume_db
a1.sinks.k1.tableName = log_data
a1.sinks.k1.user = liang
a1.sinks.k1.password = 123456
a1.sinks.k1.channel = c1


# Use a channel which buffers events in memory
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100
</code></pre><br>
启动flume agent al<br>
bin/flume-ng agent --conf conf --conf-file conf/mysql_sink.conf --name a1 -Dflume.root.logger=INFO,console<br><br>
创间表：<br><pre><code class="language-sql">CREATE TABLE `log_data` (
`id`  int(11) NOT NULL AUTO_INCREMENT ,
`context`  varchar(255) CHARACTER SET latin1 COLLATE latin1_swedish_ci NULL DEFAULT NULL ,
`time`  bigint(20) NULL DEFAULT NULL ,
PRIMARY KEY (`id`)
)
ENGINE=InnoDB
DEFAULT CHARACTER SET=latin1 COLLATE=latin1_swedish_ci
AUTO_INCREMENT=1
ROW_FORMAT=COMPACT
;
</code></pre><br>
测试:<br>
cd /home/rui/log/flumespool<br>
echo "hello world" &gt; test.log<br><br>
数据写入成功!<br><br><p></p>
            </div>
                </div>
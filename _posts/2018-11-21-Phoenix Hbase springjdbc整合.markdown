---
layout:     post
title:      Phoenix Hbase springjdbc整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/buyaore_wo/article/details/83512272				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>1.安装hbase</strong></p>

<p>下载解压hbase即可</p>

<p><strong>2.hbase整合Phoenix </strong></p>

<p>下载hbase对应版本的Phoenix,解压后拷贝bin目录下 phoenix-xxx-HBase-xx-server.jar到lbase/lib目录下，重启hbase</p>

<p><strong>3.jdbc template配置</strong></p>

<pre class="has">
<code class="language-java">package com.example.demo;

import org.apache.commons.dbcp.BasicDataSource;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.jdbc.core.JdbcTemplate;

@Configuration
public class PhoenixConfig {

	@Bean
	public BasicDataSource phoenixDataSource() {
		BasicDataSource basicDataSource = new BasicDataSource();
		basicDataSource.setDriverClassName("org.apache.phoenix.jdbc.PhoenixDriver");
		basicDataSource.setUrl("jdbc:phoenix:10-9-38-75:2181");
		basicDataSource.setUsername("");
		basicDataSource.setPassword("");
		basicDataSource.setInitialSize(20);
		basicDataSource.setMaxActive(100);
		basicDataSource.setDefaultAutoCommit(true);
		return basicDataSource;
	}

	@Autowired
	@Bean
	public JdbcTemplate phoenixJdbcTemplate(BasicDataSource phoenixDataSource) {
		return new JdbcTemplate(phoenixDataSource);
	}

}</code></pre>

<p> </p>

<p><strong>4.使用</strong></p>

<p>DaoSupport</p>

<pre class="has">
<code class="language-java">package com.example.demo.dao.support;

import java.util.List;

import javax.annotation.Resource;

import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapperResultSetExtractor;

public abstract class DaoSupport&lt;T&gt; {

	@Resource
	private JdbcTemplate jdbcTemplate;

	/**
	 * 由子类实现得到持久对象类, 即此dao操作是哪个持久类对应的数据表
	 * 
	 * @return
	 */
	public abstract Class&lt;T&gt; getEntityClass();

	public abstract String table();

	protected RowMapperResultSetExtractor&lt;T&gt; rowMapper() {
		BeanPropertyRowMapper&lt;T&gt; beanPropertyRowMapper = new BeanPropertyRowMapper&lt;&gt;(getEntityClass());
		RowMapperResultSetExtractor&lt;T&gt; extractor = new RowMapperResultSetExtractor&lt;T&gt;(beanPropertyRowMapper);
		return extractor;
	}

	public List&lt;T&gt; findAll() {
		return jdbcTemplate.query("select * from " + table(), rowMapper());
	}

	public T findOne(String sql) {
		return jdbcTemplate.queryForObject(sql, new BeanPropertyRowMapper&lt;&gt;(getEntityClass()), null);
	}

	public void execute(String sql) {
		jdbcTemplate.execute(sql);
	}

}
</code></pre>

<p>DemoService</p>

<pre class="has">
<code class="language-java">package com.example.demo.service;

import java.util.Iterator;
import java.util.List;

import javax.annotation.PostConstruct;
import javax.annotation.Resource;

import org.springframework.data.hadoop.hbase.HbaseTemplate;
import org.springframework.jdbc.core.BeanPropertyRowMapper;
import org.springframework.jdbc.core.RowMapperResultSetExtractor;
import org.springframework.stereotype.Service;

import com.example.demo.dao.UserInfoDao;
import com.example.demo.support.HBaseResultBuilder;
import com.example.demo.support.PutExtension;
import com.example.demo.vo.UserInfo;

@Service
public class DemoService {
	public static final String TABLE_NAME = "user_info";

	@Resource
	private HbaseTemplate hbaseTemplate;
	@Resource
	UserInfoDao userInfoDao;

	public String rowKey = "chinfeng1";
	public String familyName = "base";

	@PostConstruct
	public void test() {
		userInfoDao.execute("upsert into user_info(username,password,population) values('test','123456',1070)");

		BeanPropertyRowMapper&lt;UserInfo&gt; beanPropertyRowMapper = new BeanPropertyRowMapper&lt;&gt;(UserInfo.class);

		RowMapperResultSetExtractor&lt;UserInfo&gt; extractor = new RowMapperResultSetExtractor&lt;&gt;(beanPropertyRowMapper);

		List&lt;UserInfo&gt; infos = userInfoDao.findAll();

		for (Iterator iterator = infos.iterator(); iterator.hasNext();) {
			UserInfo userInfo = (UserInfo) iterator.next();
			System.out.println(userInfo.getUsername());
		}

		// testPut();
		// UserInfo userInfo = testFind();
		// System.out.println(userInfo.getUsername() + "-----------------------" +
		// userInfo.getPassword());
	}

	public UserInfo testFind() {
		// List&lt;String&gt; rows = hbaseTemplate.find(TABLE_NAME, "username", "name", new
		// RowMapper&lt;String&gt;() {
		// public String mapRow(Result result, int i) throws Exception {
		// return result.toString();
		// }
		// });
		// System.out.println("------" + rows);
		// hbaseTemplate.fin

		return (UserInfo) hbaseTemplate.get(TABLE_NAME, rowKey, familyName,
				(result, i) -&gt; new HBaseResultBuilder&lt;&gt;(familyName, result, UserInfo.class).build("username")
						.build("password").fetch());
	}

	public void testPut() {
		// hbaseTemplate.put(TABLE_NAME, "1", "username", "name",
		// Bytes.toBytes("chinfeng"));
		hbaseTemplate.execute(TABLE_NAME, (table) -&gt; {
			PutExtension putExtension = new PutExtension(familyName, rowKey.getBytes());
			putExtension.build("username", "chinfeng").build("password", "123456");
			table.put(putExtension);
			return true;
		});

		System.out.println("=======put done");
	}
}
</code></pre>

<p>5.表结构</p>

<p>CREATE TABLE IF NOT EXISTS user_info (</p>

<p>username VARCHAR NOT NULL,</p>

<p>password VARCHAR NOT NULL,</p>

<p>population BIGINT CONSTRAINT my_pk PRIMARY KEY (username, password)</p>

<p>);</p>

<p>----------------插入数据语句</p>

<p>upsert into user_info(username,password,population) values('test','123456',1070);</p>

<p> </p>

<p>源码:</p>

<p><a href="https://download.csdn.net/download/buyaore_wo/10751652" rel="nofollow">https://download.csdn.net/download/buyaore_wo/10751652</a></p>

<p>（不想要分，但貌似不能设置csdn下载分为0分）</p>            </div>
                </div>
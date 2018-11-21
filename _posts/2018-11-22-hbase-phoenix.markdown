---
layout:     post
title:      hbase-phoenix
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/tansuoliming/article/details/79986908				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>一、Phoenix概述</h3><span>	</span>HBase基础上架构的SQL中间件<br><p><span>	</span>让我们可以通过SQL/JDBC来操作HBase</p><h3>二、Phoenix安装配置</h3><pre><code class="language-plain">1.到apache下载Phoenix安装包，要注意版本和HBase匹配
		Phoenix 2.x – HBase 0.94.x
		Phoenix 3.x – HBase 0.94.x
		Phoenix 4.x – HBase 0.98.1+
	2.上传到linux进行解压

	3.将如下两个jar包，传到hbase的lib目录下
		phoenix-4.8.1-HBase-0.98-server.jar
		phoenix-4.8.1-HBase-0.98-client.jar
	4.配置环境变量
		export HBASE_HOME=/root/work/hbase-0.98.17-hadoop2
		
	5.测试
		执行 ./sqlline.py hadoop01,hadoop02,hadoop03:2181
		#如果进不去命令行，重启即可
		#如果进入Phoenix命令行状态，则证明Phoenix安装完成</code></pre><p>资源：<a href="https://download.csdn.net/download/tansuoliming/10357162" rel="nofollow">phoenix-4.8.1-HBase-0.98-server.jar</a></p><p>资源：<a href="https://download.csdn.net/my" rel="nofollow">phoenix-4.8.1-HBase-0.98-client.jar</a></p><h3>三、Phoenix使用 - 可以在命令行客户端下使用</h3><pre><code class="language-plain">1.创建表
		create table tab1(
			id integer primary key not null,
			name varchar
		);
		create table tab2(
			id varchar primary key not null,
			"info"."name" varchar
		);
		结论1:在phoenix中创建的表在hbase中会同时创建出表
		结论2:在phoenix中创建的表在hbase中表名会变为大写，如果不想让他自动变化，则需要将表明用双引号引起来
		结论3:phoenix表中的主键会成为hbase表的行键
		结论4:如果不声明phoenix表中的普通列会成为hbase表中默认列族（名字叫0）中的普通列
		结论5:也可以在建phoenix表时，来声明底层的列族的名称。
		
	2.插入数据
		upsert into test values (1,'Hello');
		upsert into test values (2,'World!');

	3.查询数据
		select * from xxx where xxxx;

	4.删除数据
		delete from test where xxxx;

	5.创建表来关联hbase中已经存在的hbase表
		只要创建表和hbase表同名，列一定要声明完全即可。

	6.删除表
		drop table tabx；#要注意，会同时删除hbase底层的表

	7.视图操作
		创建视图：
			create view tab3_view as select * from tab3;
		查询视图：
			select * from tab3_view where xxxx;
		删除视图：
			drop view tab3_view;
		创建视图关联hbase中已经存在的表：
			create view "tab4" (id varchar primary key,"cf1"."c1" varchar,"cf2"."c2" varchar);

		##表关联管理hbase中的数据，删除表的时候，hbase表也被删除
		##视图关联管理hbase中的数据，删除视图的时候，hbase表不会被删除	</code></pre><h3>四、Phoenix使用 - 可以在jdbc中使用</h3><p>资源：<a href="https://download.csdn.net/my" rel="nofollow">phoenix的源码包</a></p><p>1.创建java工程导入连接Phoenix的驱动<br><span>		</span>phoenix-4.8.1-HBase-0.98-client.jar<br></p><p>2.编写jdbc代码即可操作Phoenix中的数据</p><pre><code class="language-java">import java.sql.Connection;
import java.sql.Driver;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;

public class PhoenixDemo {
	public static void main(String[] args) throws Exception {
		Connection conn = DriverManager.getConnection("jdbc:phoenix:hadoop01,hadoop02,hadoop03:2181");
		Statement statement = conn.createStatement();
		ResultSet rs = statement.executeQuery("select * from \"javaTable\"");
		while (rs.next()) {
			String string = rs.getString("\"c1\"");
			System.out.println(string);
		}
		rs.close();
		conn.close();
	}
}
</code></pre><h2>五、Phoenix使用 - 可以在图形化客户端工具中使用</h2><pre><code class="language-plain">	连接Phoenix的工具Squirrel


	a)	Squirrel是一个连接数据库的客户端工具，一般支持JDBC的数据库都可以用它来连接，如连接mysql
	b)	下载Squirrel SQL Client，java –jar squirrel-sql-3.7.1-standard.jar即可
	c)	连接Phoenix在Squirrel的安装目录的lib下添加phoenix-4.8.1-HBase-0.98-client.jar包</code></pre><p>资源：<a href="https://pan.baidu.com/s/1fffa4fh3A0lOURLSQflogQ" rel="nofollow">squirrel</a>：1kjs</p><p>1Java –jar 名字（命令窗口下安装）</p><p><img src="https://img-blog.csdn.net/20180418120007275?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p>可以同装软件一样<br></p><p><img src="https://img-blog.csdn.net/20180418120021582?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p><img src="https://img-blog.csdn.net/20180418120034299?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p><img src="https://img-blog.csdn.net/20180418120054491?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p></p><p>其余步骤点击“next”即可，安装完成后如下图</p><p><img src="https://img-blog.csdn.net/20180418120113696?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><p></p><p>2将Phoenix-4.8.1-Habase-0.98-client.jar拷贝到squirrel的lib包下</p><p>3新建驱动</p><p><img src="https://img-blog.csdn.net/20180418120130114?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><p><img src="https://img-blog.csdn.net/20180418120138798?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p></p><p>创建链接</p><p><img src="https://img-blog.csdn.net/20180418120153815?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p><img src="https://img-blog.csdn.net/20180418120202205?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p></p><p>创建好链接后，双击打开</p><p><img src="https://img-blog.csdn.net/20180418120219279?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p></p><p>查看创建的表</p><img src="https://img-blog.csdn.net/20180418120232503?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RhbnN1b2xpbWluZw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br><p><br></p>            </div>
                </div>
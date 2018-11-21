---
layout:     post
title:      zookeeper：spark-project项目的hive和mysql配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/weixin_38750084/article/details/82763703				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>安装hive</h1>

<p>1、将hive-0.13.1-cdh5.3.6.tar.gz使用WinSCP上传到sparkproject1的/usr/local目录下。</p>

<p>2、解压缩hive安装包：tar -zxvf hive-0.13.1-cdh5.3.6.tar.gz</p>

<p>3、重命名hive目录：mv hive-0.13.1-cdh5.3.6 hive</p>

<p>4、配置hive相关的环境变量</p>

<p>vi ~/.bashrc</p>

<p>export HIVE_HOME=/usr/local/hive</p>

<p>export PATH=$HIVE_HOME/bin</p>

<p>source ~/.bashrc</p>

<h1>安装mysql</h1>

<p>1、在sparkproject1上安装mysql。</p>

<p>2、使用yum安装mysql server。</p>

<p>yum install -y mysql-server</p>

<p>service mysqld start</p>

<p>chkconfig mysqld on</p>

<p>3、使用yum安装mysql connector</p>

<p>yum install -y mysql-connector-java</p>

<p>4、将mysql connector拷贝到hive的lib包中 cp /usr/share/java/mysql-connector-java-5.1.17.jar /usr/local/hive/lib</p>

<p>5、在mysql上创建hive元数据库，创建hive账号，并进行授权</p>

<p>create database if not exists hive_metadata;</p>

<p>grant all privileges on hive_metadata.* to 'hive'@'%' identified by 'hive';</p>

<p>grant all privileges on hive_metadata.* to 'hive'@'localhost' identified by 'hive';</p>

<p>grant all privileges on hive_metadata.* to 'hive'@'spark1' identified by 'hive';</p>

<p>flush privileges;</p>

<p>use hive_metadata;</p>

<p>grant all privileges on hive_metadata.* to 'hive'@'sparkproject1' identified by 'hive';</p>

<p> </p>

<h2>配置hive相关配置文件</h2>

<table border="1" cellpadding="1" cellspacing="1"><tbody><tr><td>
			<p><span style="color:#f33b45;"><strong>配置hive-site.xml</strong></span></p>

			<p>mv hive-default.xml.template hive-site.xml</p>

			<p><span style="color:#f33b45;">//hive元数据存放在mysql中，这里不使用hive原带的debry</span></p>

			<p>&lt;property&gt;  </p>

			<p>&lt;name&gt;<span style="color:#f33b45;">javax.jdo.option.ConnectionURL</span>&lt;/name&gt;  </p>

			<p>&lt;value&gt;jdbc:mysql://spark1:3306/hive_metadata?createDatabaseIfNotExist=true&lt;/value&gt;</p>

			<p>&lt;/property&gt;</p>

			<p><span style="color:#f33b45;">//包含元数据的数据存储的JDBC驱动类名称。默认值为org.apache.derby.jdbc.Embedded，这里配置jdbc，需要连接mysql。</span></p>

			<p>&lt;property&gt;  </p>

			<p>&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;  </p>

			<p>&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</p>

			<p>&lt;/property&gt;</p>

			<p><span style="color:#f33b45;">//数据库用户名</span></p>

			<p>&lt;property&gt;  </p>

			<p>&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;  </p>

			<p>&lt;value&gt;hive&lt;/value&gt;</p>

			<p>&lt;/property&gt;</p>

			<p><span style="color:#f33b45;">//数据库用户名的密码</span></p>

			<p>&lt;property&gt;  </p>

			<p>&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;  </p>

			<p>&lt;value&gt;hive&lt;/value&gt;</p>

			<p>&lt;/property&gt;</p>

			<p> </p>

			<p>扩展：</p>

			<p>Hive 将元数据存储在 RDBMS 中，一般常用 MySQL 和 Derby。默认情况下，Hive 元数据保存在内嵌的 Derby 数据库中，只能允许一个会话连接，只适合简单的测试。实际生产环境中不适用， 为了支持多用户会话，则需要一个独立的元数据库，使用 MySQL 作为元数据库，Hive 内部对 MySQL 提供了很好的支持。<br>
			内置的derby主要问题是并发性能很差，可以理解为单线程操作。<br>
			Derby还有一个特性。更换目录执行操作，会找不到相关表等。<br>
			比如在/usr下执行创建表，在/usr下可以找到这个表。在/etc下执行查找这个表，就会找不到 。</p>
			</td>
		</tr></tbody></table><table border="1" cellpadding="1" cellspacing="1"><tbody><tr><td>
			<p><strong><span style="color:#f33b45;">配置hive-env.sh和hive-config.sh</span></strong></p>

			<p>创建hive-env.sh</p>

			<p>mv hive-env.sh.template hive-env.sh</p>

			<p>修改hive-config.sh</p>

			<p>vi /usr/local/hive/bin/hive-config.sh</p>

			<p>export JAVA_HOME=/usr/java/latest</p>

			<p>export HIVE_HOME=/usr/local/hive</p>

			<p>export HADOOP_HOME=/usr/local/hadoop</p>
			</td>
		</tr></tbody></table><table border="1" cellpadding="1" cellspacing="1"><tbody><tr><td>
			<p><span style="color:#f33b45;"><strong>验证hive是否安装成功</strong></span></p>

			<p>直接输入hive命令，可以进入hive命令行</p>

			<p>create table users(id int, name string)</p>

			<p>load data local inpath '/usr/local/users.txt' into table users</p>

			<p>select name from users </p>
			</td>
		</tr></tbody></table><p>users.txt </p>

<p><img alt="" class="has" height="172" src="https://img-blog.csdn.net/20180918205804853?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl8zODc1MDA4NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="455"></p>            </div>
                </div>
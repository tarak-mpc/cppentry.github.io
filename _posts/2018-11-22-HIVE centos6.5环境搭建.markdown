---
layout:     post
title:      HIVE centos6.5环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>1.关于hive的几个网站</h2>

<p>官网：<a href="https://cwiki.apache.org/confluence/display/Hive/GettingStarted" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/GettingStarted</a></p>

<p>GitHub：<a href="https://github.com/apache/hive" rel="nofollow">https://github.com/apache/hive</a></p>

<p>下载路径：<a href="http://archive.apache.org/dist/hive/" rel="nofollow">http://archive.apache.org/dist/hive/</a></p>

<h2>2.解压</h2>

<p>hive版本 0.13.1</p>

<p>hadoop版本 2.5.0</p>

<p>JDK 1.7</p>

<p>mysql</p>

<h2>3.启动job history</h2>

<p> sbin/mr-jobhistory-daemon.sh start historyserver</p>

<p>万一任务出错可以在8088端口查看</p>

<h2>4.配置 (使用notepad++)</h2>

<p>1.修改hive-env.sh 配置hadoop和hive的配置文件目录</p>

<p> </p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180912153715552?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzYxMDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>2.依照官网创建文件夹，并修改权限</p>

<p>注意：第二个路径是多级目录 需要添加 -p</p>

<h2><img alt="" class="has" src="https://img-blog.csdn.net/20180912111338255?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzYxMDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></h2>

<p> </p>

<p> </p>

<p>3.启动</p>

<p>bin/hive</p>

<h2>5.初步使用</h2>

<p>1.创建表</p>

<p>create table stu(id int,name string) ;</p>

<p>2.select count(*) from stu;</p>

<p> <img alt="" class="has" src="https://img-blog.csdn.net/20180912163158770?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzYxMDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>8088端口查看信息：</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180912163237354?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzYxMDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<h2>6.mysql配置</h2>

<p>derby内存数据库，只能供一个人使用。</p>

<p>1.解压：unzip mysql-libs.zip</p>

<p>2. 查看原来有没有安装mysql</p>

<p>rpm -qa|grep mysql</p>

<p>3.如果有，先卸载</p>

<p>rpm -e --nodeps</p>

<p>4.安装server</p>

<p>rpm -ivh MySQL-server-5.6.24-1.el6.x86_64.rpm </p>

<p>修改mysql密码：</p>

<p>service mysql start 启动数据库<br>
cat /root/.mysql_secret查看初始密码<br>
mysql -uroot -p初始密码<br>
set PASSWORD=PASSWORD('123456')；<br>
exit</p>

<p>mysql -uroot -p123456</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180912165503399?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzYxMDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>5.将mysql的jar包 复制到hive的lib包里面</p>

<p>cp mysql-connector-java-5.1.27-bin.jar /opt/software/hive-0.13.1-cdh5.3.6/lib</p>

<h2>7.创建hive-site.xml</h2>

<p>官网链接：<a href="https://cwiki.apache.org/confluence/display/Hive/AdminManual+MetastoreAdmin" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/AdminManual+MetastoreAdmin</a></p>

<p>具体链接：<a href="https://cwiki.apache.org/confluence/display/Hive/AdminManual+MetastoreAdmin" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/AdminManual+MetastoreAdmin</a></p>

<pre class="has">
<code class="language-java">&lt;?xml version="1.0"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;

&lt;configuration&gt;
	&lt;!-- URL    --&gt;
	&lt;property&gt;
	  &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
	  &lt;value&gt;jdbc:mysql://192.168.200.137:3306/metastore?createDatabaseIfNotExist=true&lt;/value&gt;
	  &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;
	&lt;/property&gt;
	&lt;!-- DriverName    --&gt;
	&lt;property&gt;
	  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
	  &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
	  &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;
	&lt;/property&gt;
	&lt;!-- UserName    --&gt;
	&lt;property&gt;
	  &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
	  &lt;value&gt;root&lt;/value&gt;
	  &lt;description&gt;username to use against metastore database&lt;/description&gt;
	&lt;/property&gt;
	&lt;!-- Password    --&gt;
	&lt;property&gt;
	  &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
	  &lt;value&gt;123456&lt;/value&gt;
	  &lt;description&gt;password to use against metastore database&lt;/description&gt;
	&lt;/property&gt;
	&lt;!-- hive cli界面的行头提示    --&gt;
	&lt;property&gt;
	  &lt;name&gt;hive.cli.print.header&lt;/name&gt;
	  &lt;value&gt;true&lt;/value&gt;
	  &lt;description&gt;Whether to print the names of the columns in query output.&lt;/description&gt;
	&lt;/property&gt;
	&lt;!-- hive cli界面的当前数据库的提示    --&gt;
	&lt;property&gt;
	  &lt;name&gt;hive.cli.print.current.db&lt;/name&gt;
	  &lt;value&gt;true&lt;/value&gt;
	  &lt;description&gt;Whether to include the current database in the Hive prompt.&lt;/description&gt;
	&lt;/property&gt;
	&lt;property&gt;
  &lt;name&gt;hive.fetch.task.conversion&lt;/name&gt;
  &lt;value&gt;more&lt;/value&gt;
 
&lt;/property&gt;

&lt;/configuration&gt;
</code></pre>

<p>建议先在linux环境下编辑一下hive-site.xml这个文件，避免编码的问题</p>

<h2>8.再次启动</h2>

<p>hive 启动：bin/hive</p>

<p>mysql启动：mysql -uroot -p123456</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180912171906566?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzYxMDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>看到mysql里面多了metastore数据库，存储元数据的，那么就完全配置好啦。</p>

<p>元数据存在metastore数据库的表里面</p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180912172405566?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMzMzYxMDgw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<h2>9.笔者此次安装hive和mysql在同一台机器上。</h2>            </div>
                </div>
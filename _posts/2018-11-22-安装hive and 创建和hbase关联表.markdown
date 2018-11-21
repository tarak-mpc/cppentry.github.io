---
layout:     post
title:      安装hive and 创建和hbase关联表
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="安装hive-and-和hbase关联">安装hive and 和hbase关联</h1>



<h2 id="前置文章">前置文章</h2>

<p>1.redhat上安装hadoop2 <br>
<a href="http://blog.csdn.net/huanghuazsp/article/details/43837453" rel="nofollow">http://blog.csdn.net/huanghuazsp/article/details/43837453</a> <br>
2.安装hadoop2集群 <br>
<a href="http://blog.csdn.net/huanghuazsp/article/details/43865623" rel="nofollow">http://blog.csdn.net/huanghuazsp/article/details/43865623</a> <br>
3.安装hbase集群 <br>
<a href="http://blog.csdn.net/huanghuazsp/article/details/43867931" rel="nofollow">http://blog.csdn.net/huanghuazsp/article/details/43867931</a></p>

<h2 id="1下载hive">（1）下载hive</h2>

<p><a href="http://www.eu.apache.org/dist/hive/stable/" rel="nofollow">http://www.eu.apache.org/dist/hive/stable/</a> <br>
本文下载的版本是 <br>
apache-hive-1.0.0-bin.tar.gz(<a href="http://pan.baidu.com/s/1c0eup4c" rel="nofollow">http://pan.baidu.com/s/1c0eup4c</a>)</p>

<h2 id="2安装hive">（2）安装hive</h2>

<p>1.修改  /me/apache-hive-1.0.0-bin/conf/hive-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>  
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>  
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>  
 <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>  
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:derby:;databaseName=metastore_db;create=true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>  
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>  
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>  
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>  
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.derby.jdbc.EmbeddedDriver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>  
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>  
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>  
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.metastore.local<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>  
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>  
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span> 
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>  
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.metastore.warehouse.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>  
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/me/hive/warehouse<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>  
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>  
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>  </code></pre>

<p>2.修改  /me/apache-hive-1.0.0-bin/conf/hive-env.sh</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> HADOOP_HOME=/me/hadoop-<span class="hljs-number">2.4</span>.<span class="hljs-number">1</span></code></pre>

<p>3.测试</p>

<p>cd  /me/apache-hive-1.0.0-bin/bin</p>

<p>./hive</p>

<p>create table tb_person(id int, name string);</p>

<p>show tables; <br>
<img src="https://img-blog.csdn.net/20150218000124314" alt="这里写图片描述" title=""></p>



<h2 id="3hive整合hbase">（3）hive整合hbase</h2>

<p>1.把hbase lib目录下的 hbase开头的和htrace-core-2.04.jar <br>
复制到hive的lib目录下 <br>
/me/apache-hive-1.0.0-bin/lib</p>

<p>2.测试 <br>
CREATE TABLE hbase_test1(key int, value string) STORED BY ‘org.apache.hadoop.hive.hbase.HBaseStorageHandler’ WITH SERDEPROPERTIES (“hbase.columns.mapping” = “:key,cf1:val”) TBLPROPERTIES (“hbase.table.name” = “hive_test1”);</p>

<p>这时候hive下多了一个 hbase_test1 表 <br>
<img src="https://img-blog.csdn.net/20150218133900766" alt="这里写图片描述" title=""> <br>
hbase下多了一个hive_test1 表 <br>
<img src="https://img-blog.csdn.net/20150218133900425" alt="这里写图片描述" title=""></p>

<p>往hbase插入一条数据</p>

<p>put ‘hive_test1’,’2’,’cf1:val’,’huanghua’ <br>
<img src="https://img-blog.csdn.net/20150218134707783" alt="这里写图片描述" title=""></p>

<p>用hive的表可以查询到数据 <br>
<img src="https://img-blog.csdn.net/20150218134816283" alt="这里写图片描述" title=""></p>



<h1 id="以下是修改hive的元数据为mysql">以下是修改hive的元数据为mysql</h1>



<h2 id="4安装mysql">（4）安装mysql</h2>

<p>mysql-5.1.72.tar.gz <br>
(<a href="http://pan.baidu.com/s/1mgA8axU" rel="nofollow">http://pan.baidu.com/s/1mgA8axU</a>)</p>

<p>groupadd mysql <br>
useradd -r -g mysql mysql <br>
./mysql_install_db –user=mysql –basedir=/me/mysql-5.1.72 –datadir=/me/mysql-5.1.72/data <br>
cp support-files/my-medium.cnf /etc/my.cnf <br>
将mysqld服务加入开机自启动项。 <br>
修改 mysql.server文件的内容  /usr/local/mysql 为/me/mysql-5.1.72 <br>
cp support-files/mysql.server /etc/init.d/mysqld <br>
chkconfig –add mysqld <br>
chkconfig –list mysqld <br>
service mysqld start <br>
./bin/mysql</p>

<p>grant all PRIVILEGES on <em>.</em> to hive@’main’ Identified by ‘hive’;</p>

<p>create database hive;</p>

<h2 id="5修改hive-sitexml">（5）修改hive-site.xml</h2>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>  

<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>  
 <span class="hljs-comment">&lt;!--
&lt;property&gt;  
 &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;  
  &lt;value&gt;jdbc:derby:;databaseName=metastore_db;create=true&lt;/value&gt;  
&lt;/property&gt;  

&lt;property&gt;  
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;  
  &lt;value&gt;org.apache.derby.jdbc.EmbeddedDriver&lt;/value&gt;  
&lt;/property&gt;  

&lt;property&gt;  
  &lt;name&gt;hive.metastore.local&lt;/name&gt;  
  &lt;value&gt;true&lt;/value&gt;  
&lt;/property&gt;  

&lt;property&gt;  
  &lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;  
  &lt;value&gt;/me/hive/warehouse&lt;/value&gt;  
&lt;/property&gt;  
 --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.metastore.local<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://main:3306/hive?characterEncoding=UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.mysql.jdbc.Driver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>  

</code></pre>



<h2 id="6复制mysql的驱动包到hive的lib目录">（6）复制mysql的驱动包到hive的lib目录</h2>



<h2 id="7测试">（7）测试</h2>

<p>进入hive <br>
./hive</p>

<p>create table tb_person(id int, name string); <br>
退出</p>

<p>进入mysql</p>

<p><img src="https://img-blog.csdn.net/20150222135418140" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
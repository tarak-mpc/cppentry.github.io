---
layout:     post
title:      Hadoop--HIVE所谓“集群”
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="postTitle" style="font-size:28px;font-weight:400;line-height:1.8;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;">
<a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/linbingdong/p/5829369.html" rel="nofollow" style="color:rgb(51,51,51);text-decoration:none;">Hive安装配置指南（含Hive Metastore详解）</a></h1>
<div class="postBody" style="color:rgb(125,139,141);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;line-height:25px;">
<div id="cnblogs_post_body" class="cnblogs-markdown" style="color:rgb(51,51,51);line-height:1.8;">
<p>本文介绍Hive安装配置的整个过程，包括MySQL、Hive及Metastore的安装配置，并分析了Metastore三种配置方式的区别。网上有很多介绍Hive Metastore三种配置方式的文章，但是理解都不对，给读者造成了很多误导。本人详细阅读Apache和CDH官方文档中关于Hive Metastore的部分，并经过实践，终于填好各种坑，安装配置成功，遂记录下本文，供大家参考。</p>
<h2 id="相关概念" style="font-size:21px;line-height:1.5;">
1. 相关概念</h2>
<p>Hive Metastore有三种配置方式，分别是：</p>
<ol><li style="list-style-type:decimal;">Embedded Metastore Database (Derby) 内嵌模式</li><li style="list-style-type:decimal;">Local Metastore Server 本地元存储</li><li style="list-style-type:decimal;">Remote Metastore Server 远程元存储</li></ol><h3 id="metadatametastore作用" style="font-size:16px;line-height:1.5;">
1.1 Metadata、Metastore作用</h3>
<ul><li style="list-style-type:disc;">
<p>metadata即元数据。元数据包含用Hive创建的database、tabel等的元信息。<br>
元数据存储在关系型数据库中。如Derby、MySQL等。</p>
</li><li style="list-style-type:disc;">
<p>Metastore的作用是：客户端连接metastore服务，metastore再去连接MySQL数据库来存取元数据。有了metastore服务，就可以有多个客户端同时连接，而且这些客户端不需要知道MySQL数据库的用户名和密码，只需要连接metastore 服务即可。</p>
</li></ul><h3 id="三种配置方式区别" style="font-size:16px;line-height:1.5;">
1.2三种配置方式区别</h3>
<ul><li style="list-style-type:disc;">
<p>内嵌模式使用的是内嵌的Derby数据库来存储元数据，也不需要额外起Metastore服务。这个是默认的，配置简单，但是一次只能一个客户端连接，适用于用来实验，不适用于生产环境。</p>
</li><li style="list-style-type:disc;">
<p>本地元存储和远程元存储都采用外部数据库来存储元数据，目前支持的数据库有：MySQL、Postgres、Oracle、MS SQL Server.在这里我们使用MySQL。</p>
</li><li style="list-style-type:disc;">
<p>本地元存储和远程元存储的区别是：本地元存储不需要单独起metastore服务，用的是跟hive在同一个进程里的metastore服务。远程元存储需要单独起metastore服务，然后每个客户端都在配置文件里配置连接到该metastore服务。远程元存储的metastore服务和hive运行在不同的进程里。</p>
</li></ul><p>在生产环境中，建议用远程元存储来配置Hive Metastore。</p>
<h2 id="集群规划" style="font-size:21px;line-height:1.5;">
2. 集群规划</h2>
<p>本教程Hadoop相关软件全部基于CDH5.5.1，用yum安装，系统环境如下：</p>
<ul><li style="list-style-type:disc;">操作系统：CentOS 7.2</li><li style="list-style-type:disc;">Hadoop 2.6.0</li><li style="list-style-type:disc;">Hive1.1.0</li><li style="list-style-type:disc;">Spark1.5.0</li><li style="list-style-type:disc;">MySQL 5.6</li><li style="list-style-type:disc;">JDK 1.8</li><li style="list-style-type:disc;">Maven 3.3.3</li><li style="list-style-type:disc;">Scala 2.10</li></ul><p>各节点规划如下：</p>
<pre><code class="hljs css" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
192<span class="hljs-selector-class">.168</span><span class="hljs-selector-class">.117</span><span class="hljs-selector-class">.51</span>     <span class="hljs-selector-tag" style="color:rgb(0,0,255);">Goblin01</span>           <span class="hljs-selector-tag" style="color:rgb(0,0,255);">nn1</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">jn1</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">rm1</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">worker</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">master</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">hive</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">metastore</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">mysql</span>
192<span class="hljs-selector-class">.168</span><span class="hljs-selector-class">.117</span><span class="hljs-selector-class">.52</span>     <span class="hljs-selector-tag" style="color:rgb(0,0,255);">Goblin02</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">zk2</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">nn2</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">jn2</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">rm2</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">worker</span>          <span class="hljs-selector-tag" style="color:rgb(0,0,255);">hive</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">metastore</span>
192<span class="hljs-selector-class">.168</span><span class="hljs-selector-class">.117</span><span class="hljs-selector-class">.53</span>     <span class="hljs-selector-tag" style="color:rgb(0,0,255);">Goblin03</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">zk3</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">dn1</span>  <span class="hljs-selector-tag" style="color:rgb(0,0,255);">jn3</span>       <span class="hljs-selector-tag" style="color:rgb(0,0,255);">worker</span>          <span class="hljs-selector-tag" style="color:rgb(0,0,255);">hive</span>
192<span class="hljs-selector-class">.168</span><span class="hljs-selector-class">.117</span><span class="hljs-selector-class">.54</span>     <span class="hljs-selector-tag" style="color:rgb(0,0,255);">Goblin04</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">zk4</span>    <span class="hljs-selector-tag" style="color:rgb(0,0,255);">dn2</span>            <span class="hljs-selector-tag" style="color:rgb(0,0,255);">worker</span>          <span class="hljs-selector-tag" style="color:rgb(0,0,255);">hive</span>
</code></pre>
<p>说明：Goblin01~04是每台机器的hostname，zk代表zookeeper，nn代表hadoop的namenode，dn代表datanode，jn代表journalnode，rm代表resourcemanager，worker代表Spark的slaves，master代表Spark的master</p>
<p>如果不需要Hive on Spark，只需要Hive on MR，则不需要安装Spark、Maven和Scala。</p>
<p>我们把metastore服务和MySQL都装在51上（装在哪一台都可以），51-54都安装Hive，这样多个客户端可以同时执行Hive命令。</p>
<p>在执行以下步骤之前，请确保已经安装了Hadoop集群</p>
<h2 id="安装mysql" style="font-size:21px;line-height:1.5;">
3. 安装MySQL</h2>
<ul><li style="list-style-type:disc;">下载mysql的repo源</li></ul><pre><code class="hljs groovy" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ wget <span class="hljs-string" style="color:rgb(163,21,21);">http:</span><span class="hljs-comment" style="color:#008000;">//repo.mysql.com/mysql-community-release-el7-5.noarch.rpm</span>
</code></pre>
<ul><li style="list-style-type:disc;">安装mysql-community-release-el7-5.noarch.rpm包</li></ul><pre><code class="hljs smalltalk" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
<span class="hljs-string" style="color:rgb(163,21,21);">$ </span>sudo rpm -ivh mysql-community-release-el7<span class="hljs-number">-5.</span>noarch.rpm
</code></pre>
<p>安装这个包后，会获得两个mysql的yum repo源：/etc/yum.repos.d/mysql-community.repo，/etc/yum.repos.d/mysql-community-source.repo。</p>
<ul><li style="list-style-type:disc;">安装mysql</li></ul><pre><code class="hljs sql" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ sudo yum <span class="hljs-keyword" style="color:rgb(0,0,255);">install</span> mysql-<span class="hljs-keyword" style="color:rgb(0,0,255);">server</span>
</code></pre>
<h2 id="配置mysql和metastore" style="font-size:21px;line-height:1.5;">
4. 配置MySQL和metastore</h2>
<p>Step 1: Install and start MySQL if you have not<br>
already done so</p>
<pre><code class="hljs sql" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ sudo yum <span class="hljs-keyword" style="color:rgb(0,0,255);">install</span> mysql-<span class="hljs-keyword" style="color:rgb(0,0,255);">server</span>
$ sudo service mysqld <span class="hljs-keyword" style="color:rgb(0,0,255);">start</span>
</code></pre>
<p>Step 2: Configure the MySQL Service and Connector</p>
<p>因为使用MySQL作为存储元数据的数据库，所以需要把连接MySQL的jar包放入或链接到$HIVE_HOME/lib目录下。</p>
<pre><code class="hljs sql" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ sudo yum <span class="hljs-keyword" style="color:rgb(0,0,255);">install</span> mysql-connector-<span class="hljs-keyword" style="color:rgb(0,0,255);">java</span>
$ <span class="hljs-keyword" style="color:rgb(0,0,255);">ln</span> -s /usr/<span class="hljs-keyword" style="color:rgb(0,0,255);">share</span>/<span class="hljs-keyword" style="color:rgb(0,0,255);">java</span>/mysql-connector-<span class="hljs-keyword" style="color:rgb(0,0,255);">java</span>.jar /usr/lib/hive/lib/mysql-connector-<span class="hljs-keyword" style="color:rgb(0,0,255);">java</span>.jar
</code></pre>
<p>To set the MySQL root password:</p>
<pre><code class="hljs sql" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ sudo /usr/bin/mysql_secure_installation
[...]
Enter current password for root (enter for none):
OK, successfully used password, moving on...
[...]
<span class="hljs-keyword" style="color:rgb(0,0,255);">Set</span> root <span class="hljs-keyword" style="color:rgb(0,0,255);">password</span>? [Y/<span class="hljs-keyword" style="color:rgb(0,0,255);">n</span>] y
<span class="hljs-keyword" style="color:rgb(0,0,255);">New</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">password</span>:
Re-enter <span class="hljs-keyword" style="color:rgb(0,0,255);">new</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">password</span>:
Remove anonymous <span class="hljs-keyword" style="color:rgb(0,0,255);">users</span>? [Y/<span class="hljs-keyword" style="color:rgb(0,0,255);">n</span>] Y
[...]
<span class="hljs-keyword" style="color:rgb(0,0,255);">Disallow</span> root login remotely? [Y/<span class="hljs-keyword" style="color:rgb(0,0,255);">n</span>] <span class="hljs-keyword" style="color:rgb(0,0,255);">N</span>
[...]
Remove <span class="hljs-keyword" style="color:rgb(0,0,255);">test</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">database</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">and</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">access</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">to</span> it [Y/<span class="hljs-keyword" style="color:rgb(0,0,255);">n</span>] Y
[...]
Reload privilege <span class="hljs-keyword" style="color:rgb(0,0,255);">tables</span> <span class="hljs-keyword" style="color:rgb(0,0,255);">now</span>? [Y/<span class="hljs-keyword" style="color:rgb(0,0,255);">n</span>] Y
All done!
</code></pre>
<p>To make sure the MySQL server starts at boot:</p>
<pre><code class="hljs ruby" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ sudo /sbin/chkconfig mysqld on
$ sudo /sbin/chkconfig --list mysqld
mysqld          <span class="hljs-number">0</span><span class="hljs-symbol" style="color:rgb(0,176,232);">:off</span>   <span class="hljs-number">1</span><span class="hljs-symbol" style="color:rgb(0,176,232);">:off</span>   <span class="hljs-number">2</span><span class="hljs-symbol" style="color:rgb(0,176,232);">:on</span>    <span class="hljs-number">3</span><span class="hljs-symbol" style="color:rgb(0,176,232);">:on</span>    <span class="hljs-number">4</span><span class="hljs-symbol" style="color:rgb(0,176,232);">:on</span>    <span class="hljs-number">5</span><span class="hljs-symbol" style="color:rgb(0,176,232);">:on</span>    <span class="hljs-number">6</span><span class="hljs-symbol" style="color:rgb(0,176,232);">:off</span>
</code></pre>
<p>Step 3. Create the Database and User</p>
<pre><code class="hljs delphi" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ mysql -u root -p
Enter password:
mysql&gt; CREATE DATABASE metastore;
mysql&gt; USE metastore;
mysql&gt; SOURCE /usr/lib/hive/scripts/metastore/upgrade/mysql/hive-schema-<span class="hljs-number">0.12</span>.<span class="hljs-number">0</span>.mysql.sql;

mysql&gt; CREATE USER <span class="hljs-string" style="color:rgb(163,21,21);">'hive'</span>@<span class="hljs-string" style="color:rgb(163,21,21);">'metastorehost'</span> IDENTIFIED BY <span class="hljs-string" style="color:rgb(163,21,21);">'mypassword'</span>;
...
mysql&gt; REVOKE ALL PRIVILEGES, GRANT OPTION FROM <span class="hljs-string" style="color:rgb(163,21,21);">'hive'</span>@<span class="hljs-string" style="color:rgb(163,21,21);">'metastorehost'</span>;
mysql&gt; GRANT ALL <span class="hljs-keyword" style="color:rgb(0,0,255);">ON</span> metastore.* <span class="hljs-keyword" style="color:rgb(0,0,255);">TO</span> <span class="hljs-string" style="color:rgb(163,21,21);">'hive'</span>@<span class="hljs-string" style="color:rgb(163,21,21);">'metastorehost'</span> IDENTIFIED BY <span class="hljs-string" style="color:rgb(163,21,21);">'hive'</span>;
mysql&gt; GRANT ALL <span class="hljs-keyword" style="color:rgb(0,0,255);">ON</span> metastore.* <span class="hljs-keyword" style="color:rgb(0,0,255);">TO</span> <span class="hljs-string" style="color:rgb(163,21,21);">'hive'</span>@<span class="hljs-string" style="color:rgb(163,21,21);">'%'</span> IDENTIFIED BY <span class="hljs-string" style="color:rgb(163,21,21);">'hive'</span>;
mysql&gt; FLUSH PRIVILEGES;
mysql&gt; ALTER DATABASE metastore CHARACTER <span class="hljs-keyword" style="color:rgb(0,0,255);">SET</span> latin1;
mysql&gt; quit;
</code></pre>
<p>Step 4. Format the Database</p>
<pre><code class="hljs smalltalk" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
<span class="hljs-string" style="color:rgb(163,21,21);">$ </span>cd /usr/lib/hive/bin
<span class="hljs-string" style="color:rgb(163,21,21);">$ </span>./schematool --dbType mysql --initSchema
</code></pre>
<h2 id="hive配置" style="font-size:21px;line-height:1.5;">
5. Hive配置</h2>
<h3 id="hdfs存储位置配置" style="font-size:16px;line-height:1.5;">
5.1 HDFS存储位置配置</h3>
<p>Hive配置文件里要用到HDFS的一些路径，需要先手动创建。</p>
<pre><code class="hljs perl" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
hdfs dfs -<span class="hljs-keyword" style="color:rgb(0,0,255);">mkdir</span> -p /usr/hive/warehouse
hdfs dfs -<span class="hljs-keyword" style="color:rgb(0,0,255);">mkdir</span> -p /usr/hive/tmp
hdfs dfs -<span class="hljs-keyword" style="color:rgb(0,0,255);">mkdir</span> -p /usr/hive/<span class="hljs-keyword" style="color:rgb(0,0,255);">log</span>
hdfs dfs -<span class="hljs-keyword" style="color:rgb(0,0,255);">chmod</span> g+w /usr/hive/warehouse
hdfs dfs -<span class="hljs-keyword" style="color:rgb(0,0,255);">chmod</span> g+w /usr/hive/tmp
hdfs dfs -<span class="hljs-keyword" style="color:rgb(0,0,255);">chmod</span> g+w /usr/hive/<span class="hljs-keyword" style="color:rgb(0,0,255);">log</span>
</code></pre>
<p>上述语句涉及hive-site.xml hive.metastore.warehouse.dir等，表示数据在hdfs中的存储位置</p>
<h3 id="hive-env.sh-所有节点" style="font-size:16px;line-height:1.5;">
5.2 hive-env.sh (所有节点）</h3>
<pre><code class="hljs javascript" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> HADOOP_HOME=<span class="hljs-regexp">/usr/</span>lib/hadoop
<span class="hljs-keyword" style="color:rgb(0,0,255);">export</span> HIVE_CONF_DIR=<span class="hljs-regexp">/usr/</span>lib/hive/conf
</code></pre>
<h3 id="hive-log4j.properties所有节点" style="font-size:16px;line-height:1.5;">
5.3 hive-log4j.properties（所有节点）</h3>
<p>首先创建log存放的文件夹</p>
<pre><code class="hljs dts" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
mkdir <span class="hljs-meta-keyword">/usr/</span>lib<span class="hljs-meta-keyword">/hive/</span>logs
</code></pre>
<p>然后配置hive-log4j.properties</p>
<pre><code class="hljs dts" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
hive.log.dir=<span class="hljs-meta-keyword">/usr/</span>lib<span class="hljs-meta-keyword">/hive/</span>logs
</code></pre>
<h3 id="服务端hive-site.xml" style="font-size:16px;line-height:1.5;">
5.4 服务端hive-site.xml</h3>
<p>服务端指的是Metastore服务所在的机器，即安装metastore的机器，这里是51和52。</p>
<pre><code class="hljs dts" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>jdbc:mysql:<span class="hljs-comment" style="color:#008000;">//Goblin01:3306/metastore?createDatabaseIfNotExist=true&lt;/value&gt;</span>
  <span class="hljs-params">&lt;description&gt;</span>the URL of the MySQL database<span class="hljs-params">&lt;/description&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>com.mysql.jdbc.Driver<span class="hljs-params">&lt;/value&gt;</span>
  <span class="hljs-params">&lt;description&gt;</span>Driver class name for a JDBC metastore<span class="hljs-params">&lt;/description&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>hive<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>hive<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
 <span class="hljs-params">&lt;name&gt;</span>hive.metastore.warehouse.dir<span class="hljs-params">&lt;/name&gt;</span>
 <span class="hljs-params">&lt;value&gt;</span><span class="hljs-meta-keyword">/usr/</span>hive/warehouse<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
<span class="hljs-params">&lt;name&gt;</span>hive.exec.scratchdir<span class="hljs-params">&lt;/name&gt;</span>
<span class="hljs-params">&lt;value&gt;</span><span class="hljs-meta-keyword">/usr/</span>hive/tmp<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
<span class="hljs-params">&lt;name&gt;</span>hive.querylog.location<span class="hljs-params">&lt;/name&gt;</span>
<span class="hljs-params">&lt;value&gt;</span><span class="hljs-meta-keyword">/usr/</span>hive/log<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
</code></pre>
<h3 id="客户端hive-site.xml" style="font-size:16px;line-height:1.5;">
5.5 客户端hive-site.xml</h3>
<p>这里指的是53和54。</p>
<pre><code class="hljs dts" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
<span class="hljs-params">&lt;property&gt;</span>
  <span class="hljs-params">&lt;name&gt;</span>hive.metastore.uris<span class="hljs-params">&lt;/name&gt;</span>
  <span class="hljs-params">&lt;value&gt;</span>thrift:<span class="hljs-comment" style="color:#008000;">//Goblin01:9083,Goblin02:9083&lt;/value&gt;</span>
  <span class="hljs-params">&lt;description&gt;</span>IP address (or fully-qualified domain name) and port of the metastore host<span class="hljs-params">&lt;/description&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
 <span class="hljs-params">&lt;name&gt;</span>hive.metastore.warehouse.dir<span class="hljs-params">&lt;/name&gt;</span>
 <span class="hljs-params">&lt;value&gt;</span><span class="hljs-meta-keyword">/usr/</span>hive/warehouse<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
 <span class="hljs-params">&lt;name&gt;</span>hive.exec.scratchdir<span class="hljs-params">&lt;/name&gt;</span>
 <span class="hljs-params">&lt;value&gt;</span><span class="hljs-meta-keyword">/usr/</span>hive/tmp<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>

<span class="hljs-params">&lt;property&gt;</span>
 <span class="hljs-params">&lt;name&gt;</span>hive.querylog.location<span class="hljs-params">&lt;/name&gt;</span>
 <span class="hljs-params">&lt;value&gt;</span><span class="hljs-meta-keyword">/usr/</span>hive/log<span class="hljs-params">&lt;/value&gt;</span>
<span class="hljs-params">&lt;/property&gt;</span>
</code></pre>
<h2 id="启动hive" style="font-size:21px;line-height:1.5;">
6. 启动Hive</h2>
<ol><li style="list-style-type:decimal;">启动MySQL</li></ol><pre><code class="hljs dos" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ service mysqld <span class="hljs-built_in" style="color:rgb(0,0,255);">start</span>
</code></pre>
<ol><li style="list-style-type:decimal;">启动metastore服务</li></ol><pre><code class="hljs dos" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
$ service hive-metastore <span class="hljs-built_in" style="color:rgb(0,0,255);">start</span>
</code></pre>
<ol><li style="list-style-type:decimal;">启动Hive CLI</li></ol><p>因为在4台机器上都安装了hive，并且作了相关的配置，所有四台机器均可以启动Hive CLI（Hive交互式shell）</p>
<pre><code class="hljs smalltalk" style="line-height:1.5 !important;vertical-align:middle;display:block;font-family:'Courier New', sans-serif !important;font-size:12px !important;border:1px solid rgb(204,204,204) !important;color:rgb(0,0,0);background:rgb(255,255,255);">
<span class="hljs-string" style="color:rgb(163,21,21);">$ </span>hive
</code></pre>
<h2 id="参考资料" style="font-size:21px;line-height:1.5;">
7. 参考资料</h2>
<ul><li style="list-style-type:disc;">
<p><a href="https://cwiki.apache.org/confluence/display/Hive/AdminManual+MetastoreAdmin#AdminManualMetastoreAdmin-RemoteMetastoreDatabase" rel="nofollow" class="uri" style="color:rgb(0,0,0);">https://cwiki.apache.org/confluence/display/Hive/AdminManual+MetastoreAdmin#AdminManualMetastoreAdmin-RemoteMetastoreDatabase</a></p>
</li><li style="list-style-type:disc;">
<p><a href="http://www.cloudera.com/documentation/archive/cdh/4-x/4-2-0/CDH4-Installation-Guide/cdh4ig_topic_18_4.html" rel="nofollow" class="uri" style="color:rgb(0,0,0);">http://www.cloudera.com/documentation/archive/cdh/4-x/4-2-0/CDH4-Installation-Guide/cdh4ig_topic_18_4.html</a></p>
</li></ul><div>FROM:http://www.cnblogs.com/linbingdong/p/5829369.html</div>
<div><br></div>
</div>
</div>
            </div>
                </div>
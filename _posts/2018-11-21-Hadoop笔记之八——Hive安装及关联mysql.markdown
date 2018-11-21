---
layout:     post
title:      Hadoop笔记之八——Hive安装及关联mysql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1> 1、下载hive压缩包</h1>

<p><a href="https://mirrors.tuna.tsinghua.edu.cn/apache/hive/hive-2.3.3/apache-hive-2.3.3-bin.tar.gz" rel="nofollow">https://mirrors.tuna.tsinghua.edu.cn/apache/hive/hive-2.3.3/apache-hive-2.3.3-bin.tar.gz</a></p>

<h1> 2、解压hive</h1>

<p>tar -zxvf apache-hive-2.3.3-bin.tar.gz</p>

<p>                             </p>

<h1> 3、安装mysql</h1>

<p><br>
    ***************注意：关闭selinux，这个安全子系统会和很多服务冲突。<br>
    查看之前是否安装过mysql： rpm -qa | grep -i mysql<br>
    [beifeng@hadoop-senior ~]$ su - root<br>
    # rpm -ivh MySQL-server-5.6.24-1.el6.x86_64.rpm  --nodeps --force（后面这两个参数的意思是强制安装，也可以使用yum安装）    <br>
    # rpm -ivh MySQL-client-5.6.24-1.el6.x86_64.rpm （ 安装mysql-commit包之后，    启用某些版本yum-config-manager --disable mysql56-community yum-config-manager --enable mysql57-community-dmr    再安装yum install mysql-community-server）<br>
    # sudo grep 'temporary password' /var/log/mysqld.log（查看密码,得到密码rSR6RgWWnsKxseSN）<br>
    # service mysql start<br>
    # mysql -uroot -prSR6RgWWnsKxseSN<br>
        mysql&gt; set password=password('123456');<br>
        mysql&gt; grant all on *.* to master@'master' identified by '123456' ;    远程授权登陆语句。这里注意@后面跟着的主机名，要写自己的主机名或者ip地址<br>
        mysql&gt; flush privileges;<br>
        </p>

<h1>4、Hive关联mysql</h1>

<p><br>
        拷贝驱动包，放到hive的lib目录瞎<br>
        $ tar zxf mysql-connector-java-5.1.27.tar.gz<br>
        $ cp mysql-connector-java-5.1.27-bin.jar  /opt/modules/apache-hive-0.13.1-bin/lib<br>
    <br>
        <br>
        1）、配置下hadoop路径，在hive-env.sh文件中</p>

<p>        2）、copy并修改hive-site.xml<br>
        &lt;property&gt;<br>
          &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
          &lt;value&gt;jdbc:mysql://god:3306/metastore?createDatabaseIfNotExist=true&lt;/value&gt;<br>
        &lt;/property&gt;<br>
        &lt;property&gt;<br>
          &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;<br>
          &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;<br>
        &lt;/property&gt;</p>

<p>        &lt;property&gt;<br>
          &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
          &lt;value&gt;root&lt;/value&gt;<br>
        &lt;/property&gt;</p>

<p>        &lt;property&gt;<br>
          &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;<br>
          &lt;value&gt;123456&lt;/value&gt;<br>
        &lt;/property&gt;</p>

<p> </p>

<h1>5、HDFS配置</h1>

<p> </p>

<p>$ bin/hdfs dfs -mkdir /tmp        <br>
$ bin/hdfs dfs -mkdir -p /user/hive/warehouse<br>
$ bin/hdfs dfs -chmod g+w /tmp        <br>
$ bin/hdfs dfs -chmod g+w /user/hive/warehouse        </p>

<h1><br>
6、简单使用：</h1>

<p><br>
$ bin    /hive        --第一次使用会去mysql数据库创建metastore</p>

<p>//之后每次使用都要先启动metastore<br>
bin/hive --service metastore &amp;</p>

<p>hive&gt;   <br>
hive&gt; show databases;        <br>
OK<br>
default                default是默认的数据库<br>
 </p>            </div>
                </div>
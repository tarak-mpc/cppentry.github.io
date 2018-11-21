---
layout:     post
title:      HIVE元数据库改为mysql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
1.先安装mysql，并启动服务<br>
2.为Hive建立相应的MySQL账户,并赋予足够的权限,执行命令如下:<br>
mysql -uroot -p123456<br>
mysql&gt; CREATE USER 'hive' IDENTIFIED BY 'hive';<br>
mysql&gt; GRANT ALL PRIVILEGES ON *.* TO 'hive'@'%' WITH GRANT OPTION;<br>
mysql&gt; flush privileges;<br><br>
3.创建数据库hive<br>
mysql&gt; create database hive;<br>
 <br>
4.创建hive用户,并授权<br><p>mysql&gt; grant all on hive.* to hive@'%' identified by 'hive'; </p>
mysql&gt; grant all on hive.* to hive@'gc.localdomain' identified by 'hive';<br><p>mysql&gt; grant all on hive.* to hive@'localhost' identified by 'hive';</p>
<p>mysql&gt; flush privileges;</p>
<p>需要注意的是：这里的hive.*的hive是指database，hive@‘%’这里的hive是指dbuser<br></p>
<p><br></p>
<p>5.hive的conf配置问题<br>
 A)bin/hive-config.sh<br>
  HADOOP_HOME<br>
  HADOOP_CONF_DIR<br>
  HIVE_HOME<br>
  HIVE_CONF_DIR<br>
 B) conf/hive-default.xml</p>
<p> &lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
  &lt;value&gt;jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;<br>
  &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;<br>
&lt;/property&gt;<br><br>
&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;<br>
  &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;<br>
  &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;<br>
&lt;/property&gt;<br><br>
&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
  &lt;value&gt;hive&lt;/value&gt;<br>
  &lt;description&gt;username to use against metastore database&lt;/description&gt;<br>
&lt;/property&gt;<br><br>
&lt;property&gt;<br>
  &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;<br>
  &lt;value&gt;hive&lt;/value&gt;<br>
  &lt;description&gt;password to use against metastore database&lt;/description&gt;<br>
&lt;/property&gt;</p>
<p><br>
碰到的问题：</p>
<p>1. lower_case_table_names = 1参数设置问题</p>
<p>   mysql和linux一样区分大小写，如果没有在/etc/my.cnf中设置这个参数，默认数据库表是大写，如果工程中需要读取某表是会提示找不到表找不到。</p>
<p>   建议在mysql安装完毕后，修改my.cnf加入该参数，后续创建的database中的table均会不区分大小写（加入参数以前创建的database还是会有影响）。</p>
<p>2. Error in metadata Communications link failure</p>
<p>    原因是在hive-default.xml中jdbc:mysql://hive:3306/<br>
    而这里实际上在mysql创建create user 'hive'@'hive' identified by 'hive' 是会出错的<br><br>
3. 出现 org.apache.hadoop.hive.ql.hooks.PreExecutePrinter 错误<br>
    原因未知，解决方法是将原来HIVE的目录删除或备份，重新创建HIVE，然后按照步骤一步步来解决的。怀疑是什么地方的配置问题</p>
<p>一般来讲，hive出现问题在数据不重要的情况下可以通过删除hdfs::/user/hive/worehouse，重新创建其所用database来解决；</p>
<p><br>
备注：hive中不支持truncate table 表名的形式存在(也包括 delete from 表名)，可通过 hive&gt;dfs -rmr /user/hive/warehouse/表名来清空该表下的数据，以便保持表元数据信息不丢失；或者通过create table 表名 like 表名，也可以。</p>
<p><br></p>
            </div>
                </div>
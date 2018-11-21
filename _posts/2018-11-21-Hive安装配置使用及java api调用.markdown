---
layout:     post
title:      Hive安装配置使用及java api调用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">mysql -u root -p root;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">建立</span><span style="color:rgb(88,88,88);">hive</span><span style="color:rgb(88,88,88);">用户，</span><span style="color:rgb(88,88,88);">hive</span><span style="color:rgb(88,88,88);">数据库，并授权。</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">create database hive;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">grant all on hive.* to hive@'%'  identified by 'hive';</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">grant all on hive.* to hive@'localhost'  identified by 'hive';</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">flush privileges; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">退出</span><span style="color:rgb(51,51,51);">mysql </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">exit</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">验证</span><span style="color:rgb(51,51,51);">hive</span><span style="color:rgb(51,51,51);">用户</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">mysql -u hive -p hive</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">show databases;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">看到如下反馈信息，则说明创建成功</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">mysql&gt; show databases;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">+--------------------+</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">| Database          |</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">+--------------------+</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">| information_schema |</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">| hive              |</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">| test              |</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">+--------------------+</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">3 rows in set (0.00 sec)</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">退出</span><span style="color:rgb(51,51,51);">mysql</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">exit</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">三，安装</span><span style="color:rgb(51,51,51);">hive</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">1</span><span style="color:rgb(51,51,51);">，解压安装包</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">cd  ~</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">tar -zxvf apache-hive-1.0.1-bin.tar.gz</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">2</span><span style="color:rgb(51,51,51);">，建立软连接</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">ln -s apache-hive-1.0.1-bin hive</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">3</span><span style="color:rgb(51,51,51);">，添加环境变量</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">vi  .bash_profile</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">导入下面的环境变量</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">export HIVE_HOME=/home/Hadoop/software/hive</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">export PATH=$PATH:$HIVE_HOME/bin</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">使其有效</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">source .bash_profile</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">4</span><span style="color:rgb(51,51,51);">，</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">cphive/conf/hive-default.xml.template hive/conf/hive-site.xml</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">编辑hive-site.xml</span><span style="color:rgb(51,51,51);">修改以下参数</span><span style="color:rgb(51,51,51);">:</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">&lt;property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">   &lt;name&gt;javax.jdo.option.ConnectionURL &lt;/name&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">   &lt;value&gt;jdbc:mysql://localhost:3306/hive &lt;/value&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">&lt;/property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;name&gt;javax.jdo.option.ConnectionDriverName &lt;/name&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;value&gt;com.mysql.jdbc.Driver &lt;/value&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;/property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);"><br><br></span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;name&gt;javax.jdo.option.ConnectionPassword &lt;/name&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;value&gt;hive &lt;/value&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;/property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;name&gt;hive.hwi.listen.port &lt;/name&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;value&gt;9999 &lt;/value&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;description&gt;This is the port the Hive Web Interface will listen on &lt;/descript ion&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;/property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);"><br><br></span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;name&gt;datanucleus.autoCreateSchema &lt;/name&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;value&gt;true&lt;/value&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;/property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;name&gt;datanucleus.fixedDatastore &lt;/name&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">   &lt;value&gt;false&lt;/value&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;/property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">&lt;/property&gt; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);"><br><br></span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">  &lt;property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;value&gt;hive&lt;/value&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;description&gt;Username to use against metastoredatabase&lt;/description&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">  &lt;/property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);"><br><br></span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">  &lt;property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;name&gt;hive.exec.local.scratchdir&lt;/name&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;value&gt;/home/hdpsrc/hive/iotmp&lt;/value&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;description&gt;Local scratch space for Hivejobs&lt;/description&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">  &lt;/property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">  &lt;property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;name&gt;hive.downloaded.resources.dir&lt;/name&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;value&gt;/home/hdpsrc/hive/iotmp&lt;/value&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;description&gt;Temporary local directory for addedresources in the remote file system.&lt;/description&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">  &lt;/property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">  &lt;property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;name&gt;hive.querylog.location&lt;/name&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;value&gt;/home/hdpsrc/hive/iotmp&lt;/value&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">    &lt;description&gt;Location of Hive run time structured logfile&lt;/description&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);background:rgb(250,250,250);">  &lt;/property&gt;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">5,</span><span style="color:rgb(51,51,51);">拷贝mysql-connector-java-5.1.6-bin.jar </span>到hive 的lib下面</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(51,51,51);">mv/home/hdpsrc/Desktop/mysql-connector-java-5.1.6-bin.jar /home/hdpsrc/hive/lib/</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive</span><span style="color:rgb(88,88,88);">常用命令</span><span style="color:rgb(88,88,88);">     </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">创建新表</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; CREATE TABLE t_hive (a int, b int, c int) ROW FORMAT DELIMITEDFIELDS TERMINATED BY ',';</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">导入数据</span><span style="color:rgb(88,88,88);">t_hive.txt</span><span style="color:rgb(88,88,88);">到</span><span style="color:rgb(88,88,88);">t_hive</span><span style="color:rgb(88,88,88);">表</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; LOAD DATA LOCAL INPATH '/home/hadoop/software/test/t_hive.txt'OVERWRITE INTO TABLE t_hive;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">正则匹配表名</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt;show tables '*t*';</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">增加一个字段</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; ALTER TABLE t_hive ADD COLUMNS (new_col String);</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">重命令表名</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; ALTER TABLE t_hive RENAME TO t_hadoop;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">从</span><span style="color:rgb(88,88,88);">HDFS</span><span style="color:rgb(88,88,88);">加载数据</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; LOAD DATA INPATH '/user/hive/warehouse/t_hive/t_hive.txt'OVERWRITE INTO TABLE t_hive2;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">从其他表导入数据</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; INSERT OVERWRITE TABLE t_hive2 SELECT * FROM t_hive ;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">创建表并从其他表导入数据</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; CREATE TABLE t_hive AS SELECT * FROM t_hive2 ;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">仅复制表结构不导数据</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; CREATE TABLE t_hive3 LIKE t_hive;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">通过</span><span style="color:rgb(88,88,88);">Hive</span><span style="color:rgb(88,88,88);">导出到本地文件系统</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; INSERT OVERWRITE LOCAL DIRECTORY '/tmp/t_hive' SELECT * FROMt_hive;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#Hive</span><span style="color:rgb(88,88,88);">查询</span><span style="color:rgb(88,88,88);">HiveQL</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">from ( select b,c as c2 from t_hive) t select t.b, t.c2 limit 2;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">select b,c from t_hive limit 2;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">创建视图</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; CREATE VIEW v_hive AS SELECT a,b FROM t_hive;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">删表</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">drop table if exists t_hft;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">创建分区表</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">DROP TABLE IF EXISTS t_hft;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">CREATE TABLE t_hft(</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">SecurityID STRING,</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">tradeTime STRING,</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">PreClosePx DOUBLE</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">) PARTITIONED BY (tradeDate INT)</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">ROW FORMAT DELIMITED FIELDS TERMINATED BY ',';</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">导入分区数据</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; load data local inpath '/home/BlueBreeze/data/t_hft_1.csv'overwrite into table t_hft partition(tradeDate=20130627);</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">#</span><span style="color:rgb(88,88,88);">查看分区表</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">hive&gt; SHOW PARTITIONS t_hft;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">api</span><span style="color:rgb(88,88,88);">调用：</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">nohup hive --service hiveserver2 &amp;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(102,102,102);">或者bin/hive --service hiveserver -p 10002</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">代表你已经成功的在端口为</span><span style="color:rgb(88,88,88);">10002</span><span style="color:rgb(88,88,88);">（默认的端口是</span><span style="color:rgb(88,88,88);">10000</span><span style="color:rgb(88,88,88);">）启动了</span><span style="color:rgb(88,88,88);">hiveserver</span><span style="color:rgb(88,88,88);">服务。这时候，你就可以通过</span><span style="color:rgb(88,88,88);">Java</span><span style="color:rgb(88,88,88);">代码来连接</span><span style="color:rgb(88,88,88);">hiveserver</span><span style="color:rgb(88,88,88);">，</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">问题</span><span style="color:rgb(88,88,88);">1</span><span style="color:rgb(88,88,88);">：无法启动，因为用的是</span><span style="color:rgb(88,88,88);">hive2,</span><span style="color:rgb(88,88,88);">应该使用命令</span><span style="color:rgb(88,88,88);"> hive
 –service hiveserver2</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">问题</span><span style="color:rgb(88,88,88);">2</span><span style="color:rgb(88,88,88);">：</span><span style="color:rgb(88,88,88);">Nosuitable
 driver found for jdbc:hive://192.168.184.169:10000/default </span><span style="color:rgb(88,88,88);">，原因，因为用的版本是</span><span style="color:rgb(88,88,88);">hive-1.0.1,</span><span style="color:rgb(88,88,88);">把</span><span style="color:rgb(88,88,88);">url</span><span style="color:rgb(88,88,88);">应该改成</span><span style="color:rgb(88,88,88);">jdbc:hive2://192.168.184.169:10000/default</span><span style="color:rgb(88,88,88);">即可</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">代码如下：</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">import java.sql.Connection; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">import java.sql.DriverManager; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">import java.sql.ResultSet; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">import java.sql.SQLException; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">import java.sql.Statement; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">import org.apache.log4j.Logger; </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span>/**</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">  </span>* Hive<span style="color:rgb(88,88,88);">版本：</span><span style="color:rgb(88,88,88);">1.0.1</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">  </span>*</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span>* Hive<span style="color:rgb(88,88,88);">的</span><span style="color:rgb(88,88,88);">JavaApi</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span>* </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span>* <span style="color:rgb(88,88,88);">启动</span><span style="color:rgb(88,88,88);">hive</span><span style="color:rgb(88,88,88);">的远程服务接口命令行执行：</span><span style="color:rgb(88,88,88);">bin/hive
 --service hiveserver2 &amp;</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span>* </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span>* @author ycblus</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">*/ </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span>public class HiveJdbcClient { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>static Logger log =Logger.getLogger("HiveJdbcClient");</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">  </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>private static String driverName= "org.apache.hive.jdbc.HiveDriver"; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>private static String url ="jdbc:hive2://192.168.184.158:10000/default"; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">  </span> private static String user ="hadoop";  //<span style="color:rgb(88,88,88);">主机的用户名</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>private static String password ="hadoop";  //<span style="color:rgb(88,88,88);">远程主机密码</span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>private static String sql =""; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>private static ResultSetres; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>public static void main(String[]args) { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">      </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>Connection conn = null; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>Statement stmt = null; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>try { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>conn = getConn(); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>stmt =conn.createStatement(); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>String tableName ="t_hive";</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>// <span style="color:rgb(88,88,88);">第一步</span><span style="color:rgb(88,88,88);">:</span><span style="color:rgb(88,88,88);">存在就先删除</span><span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>dropTable(stmt); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>// <span style="color:rgb(88,88,88);">第二步</span><span style="color:rgb(88,88,88);">:</span><span style="color:rgb(88,88,88);">不存在就创建</span><span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>createTable(stmt,tableName); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>// <span style="color:rgb(88,88,88);">第三步</span><span style="color:rgb(88,88,88);">:</span><span style="color:rgb(88,88,88);">查看创建的表</span><span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>showTables(stmt,tableName); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>// <span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);">describe table</span><span style="color:rgb(88,88,88);">操作</span><span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>describeTables(stmt,tableName); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>// <span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);">load data into table</span><span style="color:rgb(88,88,88);">操作</span><span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>loadData(stmt,tableName); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>// <span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);"> select * query </span><span style="color:rgb(88,88,88);">操作</span><span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>//selectData(stmt,tableName); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>// <span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);"> regular hive query </span><span style="color:rgb(88,88,88);">统计操作</span><span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>countData(stmt,tableName);</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>} catch(ClassNotFoundException e) { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>e.printStackTrace(); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>log.error(driverName +" not found!", e); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>System.exit(1); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>} catch (SQLException e){ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>e.printStackTrace(); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>log.error("Connectionerror!", e); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>System.exit(1); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>} finally { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>try {</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">               </span>if(res != null){</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">                   </span>res.close();</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">               </span>}</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">              </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">               </span>if (stmt != null){ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">                   </span>stmt.close(); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">               </span>}</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">              </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">               </span>if (conn != null){ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">                  </span>conn.close();  </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">               </span>}</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>} catch (SQLException e){ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">               </span>e.printStackTrace();</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>}</p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>private static voidcountData(Statement stmt, String tableName) </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>throws SQLException{ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>sql = "select count(1)from " + tableName; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>log.info("Running:"+ sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>res =stmt.executeQuery(sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>log.info("<span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);">“regular hive query”</span><span style="color:rgb(88,88,88);">运行结果</span><span style="color:rgb(88,88,88);">:"); </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>while (res.next()) { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>log.info("count------&gt;" + res.getString(1)); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>private static voidselectData(Statement stmt, String tableName) </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>throws SQLException{ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>sql = "select * from" + tableName; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>log.info("Running:"+ sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>res =stmt.executeQuery(sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>log.info("<span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);"> select * query </span><span style="color:rgb(88,88,88);">运行结果</span><span style="color:rgb(88,88,88);">:"); </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>while (res.next()) { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>log.info(res.getString(1)+ "\t" + res.getString(2)); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">   </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">  </span>  private static void loadData(Statement stmt,String tableName) </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">            </span>throws SQLException{ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>String filepath ="/home/hadoop/software/test/t_hive.txt"; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>sql = "load data localinpath '" + filepath + "' into table " </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">            </span>    + tableName; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>log.info("Running:" + sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>stmt.execute(sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>private static voiddescribeTables(Statement stmt, String tableName) </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">            </span>throws SQLException{ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>sql = "describe "+ tableName; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>log.info("Running:" + sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>res =stmt.executeQuery(sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>log.info("<span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);"> describe table </span><span style="color:rgb(88,88,88);">运行结果</span><span style="color:rgb(88,88,88);">:"); </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>while (res.next()) { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>log.info(res.getString(1) + "\t" + res.getString(2)); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>private static voidshowTables(Statement stmt, String tableName) </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">            </span>throws SQLException{ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>sql = "show tables'" + tableName + "'"; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>log.info("Running:" + sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>res =stmt.executeQuery(sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>log.info("<span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);"> show tables </span><span style="color:rgb(88,88,88);">运行结果</span><span style="color:rgb(88,88,88);">:"); </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>if (res.next()) { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">           </span>log.info(res.getString(1)); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>private static voidcreateTable(Statement stmt, String tableName) </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">            </span>throws SQLException{ </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>log.info("<span style="color:rgb(88,88,88);">执行</span><span style="color:rgb(88,88,88);"> create tables:"+tableName); </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>sql = "create table" </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">                </span>+ tableName </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">                </span>+ " (a string,b string,c string)  row format delimitedfields terminated by '+'"; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>stmt.execute(sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>private static StringdropTable(Statement stmt) throws SQLException { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>// <span style="color:rgb(88,88,88);">创建的表名</span><span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>String tableName ="t_hive"; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>sql = "drop table" + tableName; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>stmt.execute(sql); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>return tableName; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>private static ConnectiongetConn() throws ClassNotFoundException, </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">            </span>SQLException { </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">       </span>Class.forName(driverName); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>Connection conn =DriverManager.getConnection(url, user, password); </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">        </span>return conn; </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">    </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span></p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);"> </span>} </p>
<p align="left" style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="color:rgb(88,88,88);">用到的</span><span style="color:rgb(88,88,88);">jar</span><span style="color:rgb(88,88,88);">包如下：</span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<img src="https://img-blog.csdn.net/20170220142109695?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmx1c3lhbmdqaXdlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;"><br></p>
<div><br></div>
            </div>
                </div>
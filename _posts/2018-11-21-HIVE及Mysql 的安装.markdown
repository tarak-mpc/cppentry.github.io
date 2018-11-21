---
layout:     post
title:      HIVE及Mysql 的安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div id="sina_keyword_ad_area2" class="articalContent newfont_family">
			<p style="text-align:center;">Hive的安装</p>
<ol><li>
<div style="text-align:justify;">解压缩 :tar –zxvf
hive-0.9.0.tar.gz</div>
</li>
<li>
<div style="text-align:justify;">重命名:mv hive-0.9.0 hive</div>
</li>
</ol><p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<ol><li>
<div style="text-align:justify;">设置环境变量 vi /etc/profile</div>
</li>
</ol><p>export HIVE_HOME=/opt/hive</p>
<p>在path中加入：$HIVE_HOME/bin</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<p>source /etc/profile</p>
<ol><li>
<div style="text-align:justify;">
在hive文件下的conf/下对hive-default.xml.template 重命名为hive-site.xml</div>
</li>
</ol><p>对hive-env.sh.template 重命名hive-env.sh</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<ol><li>
<div style="text-align:justify;">修改hadoop下的hadoop-env.sh
内容如下：</div>
</li>
</ol><p>export
HADOOP_ClASSPATH=.:$CLASSPATH:HADOOP_CLASSPATH:$HADOOP_HOME/bin</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<ol><li>
<div style="text-align:justify;">
在目录$HIVE_HOME/bin下面，修改文件hive-config.sh 增加以下内容：</div>
</li>
</ol><p>export JAVA_HOME=/opt/jdk</p>
<p>export HIVE_HOME=/opt/hive</p>
<p>exportHADOOP_HOME=/opt/hadoop</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<ol><li>
<div style="text-align:justify;">Hive 的启动：执行hive</div>
</li>
</ol><p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<ol><li>
<div style="text-align:justify;"><span style="color:#FF0000;">hive在hdfs中的默认位置是/user/hive/warehouse
是由配置文件hive-site.xml中属性hive.metastor.warehouse.dir决定的。</span></div>
</li>
<li>
<div style="text-align:justify;"><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></div>
</li>
<li>
<div style="text-align:justify;">
安装mysql代替hive自带的数据库（因为他不支持多个客户端同时读写）</div>
<p>方法一：</p>
<ol><li>
<div style="text-align:justify;">删除Centos自带的数据库mysql</div>
</li>
</ol></li>
</ol><p>用命令rpm –qa |grep mysql 检查是否安装了mysql</p>
<p>执行命令rpm –e XXXXXXXX --nodeps 删除数据库</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<ol><li>
<div style="text-align:justify;">执行命令安装mysql的服务端 执行命令rpm –i
mysql-server-XXXXXX 安装mysql的服务端</div>
</li>
</ol><p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<ol><li>
<div style="text-align:justify;">执行命令mysqld_safe &amp;
启动mysql</div>
</li>
</ol><p>4.执行命令 rpm -i mysql-client-******** 安装mysql客户端</p>
<p>5．执行命令mysql_secure_installation设置root用户密码</p>
<p>6.. 使用mysql作为hive的metastore</p>
<p>(1)把mysql的jdbc驱动放置到hive的lib目录下</p>
<p>(2)修改hive-site.xml文件，修改内容如下：</p>
<p>
    &lt;property&gt;</p>
<p>
        &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</p>
<p>
        &lt;value&gt;jdbc:mysql://hadoop0:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;</p>
<p>
    &lt;/property&gt;</p>
<p>
    &lt;property&gt;</p>
<p>
        &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</p>
<p>
        &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</p>
<p>
    &lt;/property&gt;</p>
<p>
    &lt;property&gt;</p>
<p>
        &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</p>
<p>
        &lt;value&gt;root&lt;/value&gt;</p>
<p>
    &lt;/property&gt;</p>
<p>
    &lt;property&gt;</p>
<p>
        &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</p>
<p>
        &lt;value&gt;admin&lt;/value&gt;</p>
<p>
    &lt;/property&gt;</p>
<p> </p>
<p> </p>
<p> </p>
<p>方法二：</p>
<p>(我是直接下载的rpm 包安装的mysql5.60 参考<a href="http://blog.csdn.net/mw08091020/article/details/39234207" rel="nofollow">http://blog.csdn.net/mw08091020/article/details/39234207</a>)</p>
<p>方法三：</p>
<p>1.采用在线安装的方式：yun –y install mysql –server</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<p>2.修改mysql的配置文件</p>
<p>vi /etc/my.cnf</p>
<p>在第一行[mysqld]下添加</p>
<p>default-character-set=utf8</p>
<p>在后面添加：</p>
<p>[mysql]</p>
<p>default-character-set=utf8</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<p>设置mysql的自启动：chkconfig mysqld on</p>
<p>设置mysql的密码：第一次登陆密码为空：</p>
<p> </p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<p>set password for root@localhost=password('root');</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<p>如果不是第一次安装，有密码，使用下面命令修改密码：</p>
<p>/usr/bin/mysqladmin –uroot –p pssword root</p>
<p> </p>
<p>创建hive库，并设置编码：</p>
<p>creat database hive;</p>
<p>alter database hive character set latin1</p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<p>把Mysql的JDBC驱动包放入Hive的lib下：</p>
<p>cp mysql-connector-java- /opt/hive/bin</p>
<p> </p>
<p>在hive-site.xml中修改或添加：</p>
<p><span style="font-size:12pt;">&lt;property&gt;</span></p>
<p><span style="font-size:12pt;">&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</span></p>
<p><span style="font-size:12pt;">&lt;value&gt;jdbc:mysql://mysql_server_host:3306/hive?createDatabaselfNotExist=true&amp;amp;useUnicode=false&amp;amp;characterEncoding=latin1&lt;/value&gt;</span></p>
<p><span style="font-size:12pt;">&lt;/property&gt;</span></p>
<p><span style="font-size:12pt;">&lt;property&gt;</span></p>
<p><span style="font-size:12pt;">&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</span></p>
<p><span style="font-size:12pt;">&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</span></p>
<p><span style="font-size:12pt;">&lt;/property&gt;</span></p>
<p><span style="font-size:12pt;">&lt;property&gt;</span></p>
<p><span style="font-size:12pt;">&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</span></p>
<p><span style="font-size:12pt;">&lt;value&gt;root&lt;/value&gt;</span></p>
<p><span style="font-size:12pt;">&lt;/property&gt;</span></p>
<p><span style="font-size:12pt;">&lt;property&gt;</span></p>
<p><span style="font-size:12pt;">&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</span></p>
<p><span style="font-size:12pt;">&lt;value&gt;root&lt;/value&gt;</span></p>
<p><span style="font-size:12pt;">&lt;/property&gt;</span></p>
<p> </p>
<p>设置mysql的远程连接：</p>
<p style="background:#fbfaf9;"><span style="color:#333333;font-family:Consolas;font-size:12pt;">mysql&gt;
use mysql;</span></p>
<p style="background:#fbfaf9;"><span style="color:#333333;font-family:Consolas;font-size:12pt;">mysql&gt;
select host,user,password from user;</span></p>
<p style="background:#fbfaf9;"><span style="color:#333333;font-family:Consolas;font-size:12pt;">mysql&gt;
update user set host='%' where user='root' and
host='localhost';</span></p>
<p style="background:#fbfaf9;"><span style="color:#333333;font-family:Consolas;font-size:12pt;">mysql&gt;
flush privileges;</span></p>
<p style="background:#fbfaf9;"><span style="color:#333333;font-family:Consolas;font-size:12pt;">mysql&gt;
exit;</span></p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>
<p><img src="http://simg.sinajs.cn/blog7style/images/common/sg_trans.gif" alt="" title="HIVE及Mysql &lt;wbr&gt;的安装"></p>							
		</div>            </div>
                </div>
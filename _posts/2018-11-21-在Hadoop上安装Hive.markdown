---
layout:     post
title:      在Hadoop上安装Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Hive是一个基于Hadoop文件系统上的数据仓库架构，它为数据仓库的管理提供了许多功能，同时还定义了类SQL的语言-Hive QL,Hive QL允许用户尽心感和SQL相似的操作。Hive QL还允许开发人员方便的使用mapper和reducer操作，这对MapReduce框架是一个强有力的支持，下面在Hadoop上安装Hive:
<p>一、解压Hive安装包</p>
<p><img src="" alt=""></p>
<p> </p>
<p>二、修改bin/hive-config.sh的文件内容</p>
<p><img src="" alt=""></p>
<p><img src="" alt=""></p>
<p> </p>
<p>三、修改hive安装目录下的conf/hive-site.xml</p>
<p>　　由于conf里没有hive-site.xml所以我们需要复制一份hive-default.xml，主要配置如下：</p>
<p><img src="" alt=""></p>
<p><img src="" alt=""></p>
<p>ConnectionURL:元数据连接字符串；</p>
<p>ConnectionDriverName:驱动名称；</p>
<p>UserName:DB登录用户名；</p>
<p>Password:DB登录密码；</p>
<p> </p>
<p>四、配置环境变量</p>
<p><img src="" alt=""></p>
<p>然后在终端输入：hive，如果看到以下，则证明hive已经安装成功：</p>
<p><img src="" alt=""></p>
<p> </p>
<p>五、简单测试</p>
<p>在终端hive&gt;创建一个简单的表：</p>
<p><img src="" alt=""></p>
<p>代表创建成功，如果有如下报错：</p>
<p>FAILED: Error in metadata: javax.jdo.JDOFatalDataStoreException: null,  message from server: "Host 'BJXX-LIBININFO.360buyAD.local' is not allowed to connect to this MySQL server"<br>
说明，MySQL没有开启远程连接，解决方法：<br>
1。 改表法。可能是你的帐号不允许从远程登陆，只能在localhost。这个时候只要在localhost的那台电脑，登入mysql后，更改 "mysql" 数据库里的 "user" 表里的 "host" 项，从"localhost"改称"%"<br>
mysql -u root -pvmwaremysql&gt;use mysql;mysql&gt;update user set host = '%' where user = 'root';mysql&gt;select host, user from user;</p>
<p>如果这一步走过之后还有如下报错：</p>
<p>NestedThrowables:<br>
com.mysql.jdbc.exceptions.MySQLSyntaxErrorException: Specified key was too long; max key length is 767 bytes</p>
<p>这个问题是因为hive对mysql的UTF-8编码方式有限制，解决方法：修改一下mysql的编码方式即可：alter database name character set latin1;  重启服务；</p>
            </div>
                </div>
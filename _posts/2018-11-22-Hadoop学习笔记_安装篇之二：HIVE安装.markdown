---
layout:     post
title:      Hadoop学习笔记_安装篇之二：HIVE安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/rumengjian/article/details/20545187				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>1、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">安装</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>cd /tools</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>tar -zxvf hive-0.11.0.tar.gz</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>mv hive-0.11.0 /usr/java/hive</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>cd /usr/java/</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>chown -R hadoop:hadoop hive/</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>2、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">配置</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive<span>   
</span></span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">增加</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HIVE_HOME</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>vi /etc/profile</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>       
</span>export HIVE_HOME=/usr/java/hive</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>       
</span>PATH</span><span style="font-size:9pt;font-family:'宋体';">后添加</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"> :$HIVE_HOME/bin:$HIVE_HOME/conf</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">增加配置文档</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(</span><span style="font-size:9pt;font-family:'宋体';">默认该文档不存在</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">)</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">cd $HIVE_HOME/conf</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">cp hive-default.xml.template hive-site.xml</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(3)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">在配置文档中指定</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span><span style="font-size:9pt;font-family:'宋体';">的数据存储目录（默认为</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HDFS</span><span style="font-size:9pt;font-family:'宋体';">上的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">/user/hive/warehose</span><span style="font-size:9pt;font-family:'宋体';">）</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;value&gt;/hive/warehose&lt;/value&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/property&gt;</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(4)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">在配置文档中指定</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span><span style="font-size:9pt;font-family:'宋体';">的数据临时目录（默认为</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">/tmp/hive-${user.name}</span><span style="font-size:9pt;font-family:'宋体';">）</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;name&gt;hive.exec.scratchdir&lt;/name&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;value&gt;/tmp/hive-${user.name}&lt;/value&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;description&gt;Scratch space for Hive jobs&lt;/description&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/property&gt;</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(5)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">在配置文档中指定</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span><span style="font-size:9pt;font-family:'宋体';">的数据库连接（默认为</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Derby</span><span style="font-size:9pt;font-family:'宋体';">数据库，以</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">javax.jdo</span><span style="font-size:9pt;font-family:'宋体';">开头）</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span><span style="font-size:9pt;font-family:'宋体';">的存储是建立在</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hadoop</span><span style="font-size:9pt;font-family:'宋体';">文件系统上的，</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span><span style="font-size:9pt;font-family:'宋体';">本身没有专门的数据存储格式，也不能建索引</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span><span style="font-size:9pt;font-family:'宋体';">的元数据是存储在</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">RDBMS</span><span style="font-size:9pt;font-family:'宋体';">上的。</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">配置</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql</span><span style="font-size:9pt;font-family:'宋体';">连接：</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;name&gt;hive.metastore.local&lt;/name&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;value&gt;true&lt;/value&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"> </span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;value&gt;jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;description&gt;JDBC MySQL</span><span style="font-size:9pt;font-family:'宋体';">连接字符串</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/description&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"> </span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;description&gt;JDBC</span><span style="font-size:9pt;font-family:'宋体';">　</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">MySQL Classname&lt;/description&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"> </span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;value&gt;hive&lt;/value&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;description&gt;username&lt;/description&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"> </span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;value&gt;hive&lt;/value&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;description&gt;password&lt;/description&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/property&gt;</span></p>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">复制</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql JDBC</span><span style="font-size:9pt;font-family:'宋体';">驱动到</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive/lib</span><span style="font-size:9pt;font-family:'宋体';">中</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">cp mysql-connector-java-5.1.10-bin.jar /usr/java/hive/lib/</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>3、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">MySQL</span><span style="font-size:9pt;font-family:'宋体';">配置</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)</span></span><span style="font-size:9pt;font-family:'宋体';">安装</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql</span></h5>
<p class="MsoNormal"><span style="font-size:9pt;font-family:'宋体';">在</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Redhat</span><span style="font-size:9pt;font-family:'宋体';">安装包中选中</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Server</span><span style="font-size:9pt;font-family:'宋体';">中的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysqlServer</span><span style="font-size:9pt;font-family:'宋体';">下所有包，进行安装</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)</span></span><span style="font-size:9pt;font-family:'宋体';">启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysqld</span><span style="font-size:9pt;font-family:'宋体';">服务</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">/usr/bin/ mysqld_safe -uroot &amp;</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(3)</span></span><span style="font-size:9pt;font-family:'宋体';">运行</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">setup</span><span style="font-size:9pt;font-family:'宋体';">或</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">ntsysv</span><span style="font-size:9pt;font-family:'宋体';">将</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysqld</span><span style="font-size:9pt;font-family:'宋体';">设置自动启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(4)</span></span><span style="font-size:9pt;font-family:'宋体';">启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql -uroot -p</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(5)</span></span><span style="font-size:9pt;font-family:'宋体';">修改</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql</span><span style="font-size:9pt;font-family:'宋体';">的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">root</span><span style="font-size:9pt;font-family:'宋体';">用户密码</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysqladmin -u root -p password 123456</span></p>
<p class="MsoNormal"><span style="font-size:9pt;font-family:'宋体';">在</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Enter Password</span><span style="font-size:9pt;font-family:'宋体';">时直接回车</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(</span><span style="font-size:9pt;font-family:'宋体';">要求输入原密码，</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">root</span><span style="font-size:9pt;font-family:'宋体';">初始密码为空</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">)</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(6)</span></span><span style="font-size:9pt;font-family:'宋体';">登陆</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql -uroot -p</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(7)</span></span><span style="font-size:9pt;font-family:'宋体';">创建</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">用户</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">create user 'hive'@'%' identified by 'hive';</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(8)</span></span><span style="font-size:9pt;font-family:'宋体';">赋予权限</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">grant all privileges on *.* to 'hive'@'%' with grant option;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">grant all privileges on *.* to 'root'@'%' with grant option;</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(9)</span></span><span style="font-size:9pt;font-family:'宋体';">刷新系统权限相关表</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">,</span><span style="font-size:9pt;font-family:'宋体';">让设置立刻生效</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">flush privileges;</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(10)</span></span><span style="font-size:9pt;font-family:'宋体';">查看</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">user</span><span style="font-size:9pt;font-family:'宋体';">表</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">user mysql;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">select user,password,host from user;</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(11)</span></span><span style="font-size:9pt;font-family:'宋体';">修改</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">root</span><span style="font-size:9pt;font-family:'宋体';">远程访问密码</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(root</span><span style="font-size:9pt;font-family:'宋体';">远程访问密码为空</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">)</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">update user set password=PASSWORD('wddgmy') where user='root';</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">flush privileges;</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(12)</span></span><span style="font-size:9pt;font-family:'宋体';">允许远程用户访问</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(</span><span style="font-size:9pt;font-family:'宋体';">修改</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">/etc/my.conf)</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">vi /etc/my.conf</span></p>
<p class="MsoNormal"><span style="font-size:9pt;font-family:'宋体';">注释</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">bind-address</span><span style="font-size:9pt;font-family:'宋体';">行</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">#bind-address = 127.0.0.1</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(13)</span></span><span style="font-size:9pt;font-family:'宋体';">用</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">用户登陆</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql -uhive -p</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(14)</span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">用户登陆报错</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">ERROR 1045 (28000): Access denied for user 'hive'@'localhost' (using password: YES)</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(15)</span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">用户登陆报错处理</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span style="font-size:9pt;font-family:'宋体';">增加</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">访问</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">localhost</span><span style="font-size:9pt;font-family:'宋体';">权限</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">grant all privileges on *.* to 'hive'@'localhost';</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">update user set password=PASSWORD('hive') where user='hive';</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">flush privileges;</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>4、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>hive</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>5、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">报错</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">WARN conf.HiveConf: DEPRECATED: Configuration property hive.metastore.local no longer has any effect. Make sure to provide a valid value
 for hive.metastore.uris if you are connecting to a remote metastore.</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>6、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">设置</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">错误日志记录级别</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(log4j)</span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">bin/hive -hiveconf hive.root.logger=DEBUG,console</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>7、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">查看</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">的参数设置</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive&gt; SET -v</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>8、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive</span><span style="font-size:9pt;font-family:'宋体';">报错处理</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive 0.10</span><span style="font-size:9pt;font-family:'宋体';">和</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">0.11</span><span style="font-size:9pt;font-family:'宋体';">之后的版本不在使用</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive.metastore.local</span><span style="font-size:9pt;font-family:'宋体';">属性，删除该设置</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;property&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;name&gt;hive.metastore.local&lt;/name&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>&lt;value&gt;true&lt;/value&gt;</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">&lt;/property&gt;</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>9、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">验证</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>show tables</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>10、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">验证报错</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal" align="left" style="text-align:left;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">FAILED: Error in metadata: java.lang.RuntimeException: Unable to instantiate org.apache.hadoop.hive.metastore.HiveMetaStoreClient</span></p>
<p class="MsoNormal" align="left" style="text-align:left;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>11、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">验证报错处理</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span style="font-size:9pt;font-family:'宋体';">没有</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql jdbc</span><span style="font-size:9pt;font-family:'宋体';">驱动包，复制</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">mysql
 JDBC</span><span style="font-size:9pt;font-family:'宋体';">驱动到</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive/lib</span><span style="font-size:9pt;font-family:'宋体';">中</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">cp mysql-connector-java-5.1.10-bin.jar /usr/java/hive/lib/</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>12、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">验证</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hive</span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>show tables</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>13、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">示例</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">建表</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal" align="left" style="text-align:left;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">create table tatol(DATECOLUMN<span> 
</span>int,DATECOUNT </span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">int</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">) row format delimited fields terminated by ',';</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">导入数据</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>   
</span>load data inpath '/user/hadoop/TOTAL/part-m-0000' overwrite into table tatol;</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(3)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">查看</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HDFS</span><span style="font-size:9pt;font-family:'宋体';">中数据</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">数据保存在</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">/user/hive/warehouse/tatol</span><span style="font-size:9pt;font-family:'宋体';">目录中，</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive.metastore.warehouse.dir</span><span style="font-size:9pt;font-family:'宋体';">设置没有生效</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">经检查，发现</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive-site.xml</span><span style="font-size:9pt;font-family:'宋体';">中有</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">2</span><span style="font-size:9pt;font-family:'宋体';">个</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive.metastore.warehouse.dir</span><span style="font-size:9pt;font-family:'宋体';">属性，自己设置的在前，系统默认的在后，被默认的覆盖了。</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">删除系统默认</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hive.metastore.warehouse.dir</span><span style="font-size:9pt;font-family:'宋体';">属性</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
            </div>
                </div>
---
layout:     post
title:      Hadoop渐进八：Hive配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/CDUT100/article/details/71218845				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span>一、Hive简述</span></p>
<p><span>Hive：基于Hadoop文件系统上的数据仓库架构。Hive提供了一系列工具用于数据提取、转化、加载，它是一种可以存储、查询和分析存储在Hadoop中的大规模数据的机制。Hive还定义了类SQL语言—Hive QL，Hive QL允许用户进行SQL相似的操作。</span></p>
<p><span>二、Hive配置：基于Mysql</span></p>
<p><span>默认情况下，Hive metadata数据保存在内嵌的Derby数据库，只能实现一个会话连接，一般适合于简单测试。但为了支持多用户多会话功能，我们使用MySQL作为元数据库，Hive内部也对MySQL提供了很好的支持。</span></p>
<p><span>1、配置mysql：</span></p>
<p><span>sudo apt-get install mysql-server<span></span>          //Mysql server </span></p>
<p><span>sudo apt-get install mysql-client           //Mysql client</span></p>
<p><span>sudo apt-get install libmysqlclient-dev  //Mysql dev</span></p>
<p><span>sudo netstat -tap | grep mysql              //Is it success?</span></p>
<p><span>sudo mysql -u root -p<span> </span>
                          //Login mysql</span></p>
<p><span>……</span></p>
<p><span>另外，在Hive安装成功运行的情况下，可以查看MySQL数据库中的一部分Hive元数据格式： </span></p>
<p><span></span><img src="https://img-blog.csdn.net/20170505180627108?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0RVVDEwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span>2、创建hive用户，并赋予相应权限</span></p>
<p><span>mysql -r root -p</span></p>
<p><span>mysql&gt; create user ‘hive’ identified by ‘hive’;</span></p>
<p><span>mysql&gt; grant all privileges on *.* to ‘hive’ with grant option;</span></p>
<p><span>mysql&gt; flush privileges;</span></p>
<p><span>……</span></p>
<p><span>3、hive下载配置</span></p>
<p><span>下载hive：<a href="http://apache.fayea.com/hive/hive-1.2.2/" rel="nofollow"><span>http://apache.fayea.com/hive/hive-1.2.2/</span></a></span></p>
<p><span>解压hive到：/usr/hadoop/</span></p>
<p><span>重命名hive：hive-1.2.2</span></p>
<p><span>下载Mysql connector java：<a href="https://dev.mysql.com/downloads/connector/j/" rel="nofollow"><span>https://dev.mysql.com/downloads/connector/j/</span></a></span></p>
<p><span>解压Mysql connector java到：/usr/hadoop/hive-1.2.2/lib</span></p>
<p><span>4、hive环境变量</span></p>
<p><span>sudo vim ~/.bashrc </span></p>
<p><span><img src="https://img-blog.csdn.net/20170505180749109?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0RVVDEwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span>其中：/usr/hadoop/hive-1.2.2为hive安装目录</span></p>
<p><span>5、hive conf配置</span></p>
<p><span>(1)、hive-site.xml：若无这个文件，新建一个就好，配置如下： </span></p>
<p><span><img src="https://img-blog.csdn.net/20170505180920266?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0RVVDEwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span></span></p>
<p><span>hive.metastore.warehouse.dir：Hive在HDFS上数据目录</span></p>
<p><span>hive.exec.scratchdir：Hive在HDFS上临时目录</span></p>
<p><span>javax.jdo.option.ConnectionURL：元数据连接字串</span></p>
<p><span>java.jdo.option.ConnectionDriverName：DB连接引擎</span></p>
<p><span>(2)、配置hive环境文件hive-env.sh：sudo cp hive-env.sh.template hive-env.sh</span></p>
<p><span>(3)、配置hive日志文件hive-log4j.properties：sudo cp hive-log4j.properties.template hive-log4j.properties</span></p>
<p><span>7、在hadoop分布式集群正常运行的情况下</span></p>
<p><span>$sudo start-all.sh</span></p>
<p><span>$schematool -initSchema -dbType mysql //初始化元数据</span></p>
<p><span>$hive</span></p>
<p><span>hive&gt; show tables </span></p>
<p><span><img src="https://img-blog.csdn.net/20170505181026941?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0RVVDEwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span>如出现错误：org.apache.hadoop.hive.metastore.HiveMetaException:Failed to load driver，则可能是缺少JDBC驱动，添加mysql-connectot-java-5.1.42-bin.jar</span></p>
<p><span>8、hive的web配置(HWI是Hive Web Interface的简称，是hive cli的一个web替换方案)</span></p>
<p><span>(1)、安装ANT：sudo apt-get install ant</span></p>
<p><span>(2)、官网下载hive对应版本(我这里是1.2.2)的src文件，解压进入src文件下src/hwi，使用命令将web打包成war格式压缩包：</span></p>
<p><span>tar -zxvf hive-1.2.2-src.tar.gz</span></p>
<p><span>cd hive-1.2.2-src/hwi</span></p>
<p><span>jar cfM hive-hwi-1.2.2.war -C web ./</span></p>
<p><span>(3)、将*.war压缩包拷贝到/usr/hadoop/hive-1.2.2/lib目录下(我的hive安装目录)</span></p>
<p><span>(4)、配置hive.site.xml，如下： </span></p>
<p><span></span><img src="https://img-blog.csdn.net/20170505181105410?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0RVVDEwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span>(5)、启动hwi服务：hive --service hwi</span></p>
<p><span>若出现错误：NO JSP Support for /hwi, did not find org.apache.jasper.servlet.JspServlet</span></p>
<p><span>—&gt;拷贝jdk/lib/tools.jar到/hive-1.2.2/lib</span></p>
<p><span>—&gt;拷贝hbase/lib/jasper-compiler-5.5.23.jar到/hive-1.2.2/lib</span></p>
<p><span>—&gt;拷贝hbase/lib/jasper-runtime-5.5.23.jar到/hive-1.2.2/lib</span></p>
<p><span>—&gt;拷贝hbase/lib/common-el-1.0.jar</span></p>
<p><span>注：首先在系统中查找*.jar，找不到的情况下才去网上下载</span></p>
<p><span>(6)、在浏览器输入：http://192.168.0.100:9999/hwi </span></p>
<p><img src="https://img-blog.csdn.net/20170505181144739?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvQ0RVVDEwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
            </div>
                </div>
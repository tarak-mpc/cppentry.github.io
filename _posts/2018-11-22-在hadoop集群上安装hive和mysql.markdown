---
layout:     post
title:      在hadoop集群上安装hive和mysql
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主女朋友允许不得转载。					https://blog.csdn.net/qq_26442553/article/details/75944132				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div><div><span style="font-size:18px;">一：hive使用前提<span style="line-height:1.45;">   </span></span></div><div style="background-color:rgb(251,250,248);"><div><span style="font-size:16px;color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">  hive的运行要在hadoop集群上。hive相当于mapreduce的客户端。此外还需要mysql等数据库存储元数据。所以依赖于hadoop，jdk.</span><span style="font-size:16px;color:rgb(45,79,201);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;"><strong>所以使用hive时，必须先启动hdfs。（因为只要其读取操作文件。hive的文件实际存储在hdfs上）</strong></span></div><div><span style="font-size:16px;color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">- 内嵌模式：元数据保持在内嵌的derby模式，只允许一个会话连接（默认 hive带的数据库）</span></div><div><span style="font-size:16px;color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">- 本地独立模式：在本地安装Mysql，数把元据放到mySql内</span></div><div><span style="font-size:16px;color:rgb(51,51,51);font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;">- 远程模式：元数据放置在远程的Mysql数据库</span></div></div></div><div><span style="font-size:18px;">二：Hive的部署与安装</span></div><div>        1、解压Hive到安装目录</div><div>            $ tar -zxf /opt/softwares/hive-0.13.1-cdh5.3.6.tar.gz   -C  /opt/modules/</div><div>        2、重命名配置文件</div><div>            $ mv hive-default.xml.template   hive-site.xml</div><div>            $ mv hive-env.sh.template     hive-env.sh</div><div>        3、在hive-env.sh 配置如下环境变量</div><div>            JAVA_HOME=/opt/modules/jdk1.8.0_121</div><div>            HADOOP_HOME=/opt/modules/cdh/hadoop-2.5.0-cdh5.3.6/</div><div>            export HIVE_CONF_DIR=/opt/modules/cdh/hive-0.13.1-cdh5.3.6/conf</div><div>        4、在linux里在线安装Mysql（<span style="color:#ff0000;"><strong>前提是可以连外网，虚拟机的话要能连的了外网，ping的通外网。）</strong></span></div><div>             1.$ su - root  。需要切换到root权限</div><div>             2.依次运行如下命令，从网络下载安装mysql服务端和客户端</div><div>            # yum -y install mysql  mysql-server  mysql-devel</div><div>            # wget http://dev.mysql.com/get/mysql-community-release-el7-5.noarch.rpm</div><div>            # rpm -ivh mysql-community-release-el7-5.noarch.rpm</div><div>            # yum -y install mysql-community-server</div><div>            尖叫提示：如果使用离线绿色版本（免安装版本）需要手动初始化Mysql数据库</div><div>        5、配置Mysql</div><div>            ** 开启Mysql服务</div><div>                # systemctl start mysqld.service</div><div>            ** 设置root用户密码</div><div>                # <strong><span style="color:rgb(255,0,0);">mysqladmin  -uroot  password '123456'</span></strong></div><div>            ** 为用户以及其他机器节点授权</div><div>                 在mysql里面运行如下命令：</div><div>                <strong><span style="color:rgb(255,0,0);">grant all on *.* to root@'hadoop102' identified by '123456';</span></strong></div><div>                grant：授权</div><div>                all：所有权限</div><div>                *.*：数据库名称.表名称</div><div>                root：操作mysql的用户</div><div>                @''：主机名</div><div>                密码：123456</div><div>                <span style="color:rgb(227,0,0);">如果单独使用mysql -uroot -p123456可以登录mysql表明mysql安装成功</span></div><div><span style="color:rgb(227,0,0);">                 退出当前账户用 exit;   主要不要忽略了;</span></div><div>        <span style="color:rgb(255,0,0);">  6.关联mysql和hive.先配置<span style="color:rgb(79,0,154);">修改</span>hive里的hive-site.xml文件</span>        </div><div><pre><code class="language-html">1. hive-site.xml
 &lt;-远程用hive连接数据库，主要修改hive里的四个配置文件-&gt;
  &lt;1.hadoop102连接mysql,并且在mysql里面创建metastore数据库。&gt;
  &lt;2.添加mysql驱动路径到hive中，前提是已在hive的lib目录下，上传了mysql连接驱动的jar包&gt;
  &lt;3和4是hive连接mysql数据库的账户和密码&gt;

&lt;property&gt;
     &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
     &lt;value&gt;jdbc:mysql://hadoop102:3306/metastore?createDatabaseIfNotExist=true&lt;/value&gt;     
     &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;
&lt;/property&gt;

&lt;property&gt;
      &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
      &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
      &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;
&lt;/property&gt;

&lt;property&gt;
       &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
       &lt;value&gt;root&lt;/value&gt;
       &lt;description&gt;username to use against metastore database&lt;/description&gt;
&lt;/property&gt;

&lt;property&gt;
       &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
       &lt;value&gt;123456&lt;/value&gt;
       &lt;description&gt;password to use against metastore database&lt;/description&gt;
&lt;/property&gt;         </code></pre></div><div><div style="font-weight:bold;color:rgb(47,47,47);"><span style="font-size:24px;">三：</span><span style="font-size:21px;">修改Hive日志信息</span></div><div><div><span style="font-size:16px;"><span><span style="color:rgb(47,47,47);"><span style="font-weight:700;">            6.重命名配置文件：</span></span></span></span>hive-log4j.properties （选择性修改）</div><div>                    在/opt/modules/cdh/hive-0.13.1-cdh5.3.6/下  ：</div><div>                    mkdir logs<br>                    hive.log.dir=/opt/modules/hive-0.13.1-cdh5.3.6/logs</div></div></div><div>            <strong><span style="color:rgb(79,0,154);">7 。 先驱动包，再把里面的解压拷贝数据库驱动包到Hive根目录下的lib文件夹</span></strong></div><div>             <span style="font-size:12px;">   $ cp -a mysql-connector-java-5.1.27-bin.jar    /opt/modules/cdh/hive-0.13.1-cdh5.3.6/lib/</span>             </div><div>          <span style="color:rgb(255,0,0);"><strong>  8. 启动Hive和测试是否安装成功</strong></span><br></div><div>              1.  $ bin/hive   出现下面字样则表示全部安装成功</div><div>         <span style="font-size:12px;">17/07/22 09:04:02 WARN conf.HiveConf: DEPRECATED: hive.metastore.ds.retry.* no longer   has any effect.  Use hive.hmshandler.retry.*  nstead Logging initialized using configuration in jar:file:/opt/modules/hive-0.13.1-cdh5.3.6/lib/hive-common-0.13.1-cdh5.3.6.jar!/hive-log4j.properties</span></div><div><span style="font-size:16px;">             2.在登录的hive中输入show databases 如果成功，则表示hive成功搭建 退出hive<strong><span style="color:rgb(45,79,201);"><span style="font-size:21px;">用exit;</span></span></strong></span>            </div>            </div>
                </div>
---
layout:     post
title:      Hive部署（包括集成Hbase和Sqoop）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>1 安装环境</h1>
<h2>1.1 系统环境</h2>
<p>    主要是选择软件版本。</p>
<ul><li>Hadoop 1.0.3</li><li>Hbase 0.94.0</li><li>Hive 0.8.1</li><li>zookeeper-3.3.5</li><li>Mysql 5.5.28</li><li>JDK 1.6</li><li>Sqoop-1.4.2</li></ul><h2><strong>1.2 环境变量</strong></h2>
<ul><li>$HDOOP_HOME：/home/hadoop/hadoop</li><li>$HBASE_HOME：/home/hadoop/hbase</li><li>$HIVE_HOME：/home/hadoop/hive </li></ul><h1><strong>2 下载</strong></h1>
<p><a href="http://www.fayea.com/apache-mirror/hive/hive-0.8.1/hive-0.8.1.tar.gz" rel="nofollow">http://mirror.bit.edu.cn/apache/hive/hive-0.8.1/</a></p>
<h1><strong><span style="color:#000000;">3 </span>解压</strong></h1>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>tar –xf hive-0.8.1.tar.gz  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">tar –xf hive-0.8.1.tar.gz</pre>
<p>将解压后的hive-0.8.1文件放在系统的/home/hadoop/hive/中。</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>mkdir /home/hadoop/hive  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">mkdir /home/hadoop/hive</pre>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>mv /home/hadoop/hive-0.8.1 /home/hadoop/hive  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">mv /home/hadoop/hive-0.8.1 /home/hadoop/hive</pre>
<h1>4 修改配置文件</h1>
<h2>4.1 设置HADOOP_HOME</h2>
<p>修改hive-0.8.1目录下/conf/hive-env.sh.template中的HADOOP_HOME为实际的Hadoop安装目录。</p>
<p>步骤一：进入/home/hadoop/hive/conf。</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>cd /home/hadoop/hive/conf  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">cd /home/hadoop/hive/conf</pre>
<p>步骤二：复制hive-env.sh.template命名为hive-env.sh。</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>cp hive-env.sh.template hive-env.sh  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">cp hive-env.sh.template hive-env.sh</pre>
<p>步骤三：修改HADOOP_HOME目录为/home/hadoop/hadoop。</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>vim hive-env.sh  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">vim hive-env.sh</pre>
<p>修改完成之后的效果如图所示。<br><img alt="" src="http://dl.iteye.com/upload/attachment/0083/1304/9fa51646-5feb-3d81-a172-6ca5424bdb3f.jpg"></p>
<p>配置设置HADOOP_HOME结束。</p>
<h2>4.2 在HDFS中创建/tmp/user/hive/warehouse并设置权限</h2>
<p>步骤如下所示：</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>bin/hadoop fs -mkdir /tmp   </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">bin/hadoop fs -mkdir /tmp </pre>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>bin/hadoop fs -mkdir /user/hive/warehouse   </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">bin/hadoop fs -mkdir /user/hive/warehouse </pre>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>bin/hadoop fs -chmod g+w /tmp  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">bin/hadoop fs -chmod g+w /tmp</pre>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>bin/hadoop fs -chmod g+w /user/hive/warehouse  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">bin/hadoop fs -chmod g+w /user/hive/warehouse</pre>
<p>修改完成之后的效果如图所示:<br><img alt="" src="http://dl.iteye.com/upload/attachment/0083/1306/2539cfa8-4c8a-3309-b72e-fffbeb1c2cfb.jpg"><br><img alt="" src="http://dl.iteye.com/upload/attachment/0083/1308/4991b9ec-6652-3644-8dd9-364a6d24bfaa.jpg"><br></p>
<p>创建/tmp和/user/hive/warehouse结束。</p>
<h2>4.3 修改hive-site.xml</h2>
<p>hive-site.xml主要配置项说明如下所示：</p>
<ul><li>hive.metastore.warehouse.dir：数据存放目录，默认路径为/user/hive/warehouse</li><li>hive.exec.scratchdir：临时文件目录，默认路径为/tmp</li></ul><p>步骤一：创建hive-default.xml、hive-site.xml</p>
<p>将conf/hive-default.xml.template复制两份，分别命名为hive-default.xml（用于保留默认配置）和hive-site.xml（用于个性化配置，可覆盖默认配置）。命令如下所示：</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>cd /home/hadoop/hive/conf  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">cd /home/hadoop/hive/conf</pre>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>cp hive-default.xml.template hive-default.xml  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">cp hive-default.xml.template hive-default.xml</pre>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>cp hive-default.xml.template hive-site.xml  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">cp hive-default.xml.template hive-site.xml</pre>
<p>步骤二：修改hive-site.xml，命令如下所示：</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>cd /home/hadoop/hive/conf  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">cd /home/hadoop/hive/conf</pre>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>vim hive-site.xml  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">vim hive-site.xml</pre>
<p>下面是hive-site.xml文件的配置示例：</p>
<div>
<div>
<div>Xml代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>&lt;property&gt;  </li><li>&lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;  </li><li>&lt;value&gt;/user/hive/warehouse&lt;/value&gt;  </li><li>&lt;description&gt;location of default database for the warehouse  </li><li>&lt;/description&gt;  </li><li>&lt;/property&gt;  </li><li>  </li><li>&lt;property&gt;  </li><li>&lt;name&gt;hive.exec.scratchdir&lt;/name&gt;  </li><li>&lt;value&gt;/tmp&lt;/value&gt;  </li><li>&lt;description&gt;Scratch space for Hive jobs&lt;/description&gt;  </li><li>&lt;/property&gt;  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">&lt;property&gt;&lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;&lt;value&gt;/user/hive/warehouse&lt;/value&gt;&lt;description&gt;location of default database for the warehouse&lt;/description&gt;&lt;/property&gt;&lt;property&gt;&lt;name&gt;hive.exec.scratchdir&lt;/name&gt;&lt;value&gt;/tmp&lt;/value&gt;&lt;description&gt;Scratch space for Hive jobs&lt;/description&gt;&lt;/property&gt;</pre>
<p>修改hive-site.xml结束。</p>
<h2>4.4 测试</h2>
<p>在终端输入bin/hive，当你看到“hive&gt;”，那么恭喜你，Hive已经正确安装，可以运行了。</p>
<h1><span style="color:#000000;">5<br></span></h1>
<h1>5 设置Hive元数据存储</h1>
<p>由于Hive的元数据可能要面临不断的更新、修改和读取，所以它显然不适合使用Hadoop文件系统进行存储。目前Hive将元数据存储在RDBMS中，比如MySQL、Derby中。本文选择MySQL。</p>
<h2>5.1 连接数据库配置参数</h2>
<ul><li>javax.jdo.option.ConnectionURL：<span style="color:#000000;">元数据连接字串</span> </li><li>javax.jdo.option.ConnectionDriverName：<span style="color:#000000;">DB</span><span style="color:#000000;">连接引擎</span></li><li>javax.jdo.option.ConnectionUserName：<span style="color:#000000;">DB</span><span style="color:#000000;">连接用户名</span></li><li>javax.jdo.option.ConnectionPassword：<span style="color:#000000;">DB</span><span style="color:#000000;">连接密码</span></li></ul><h2>5.2 修改hive-site.xml</h2>
<p>代码如下所示：</p>
<div>
<div>
<div>Xml代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>&lt;property&gt;  </li><li>&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;  </li><li>&lt;value&gt;  </li><li>jdbc:mysql://10.2.0.254:3306/hive?createDatabaseIfNotExist=true  </li><li>&lt;/value&gt;  </li><li>&lt;description&gt;  </li><li>JDBC connect string for a JDBC metastore  </li><li>&lt;/description&gt;  </li><li>&lt;/property&gt;  </li><li>  </li><li>&lt;property&gt;  </li><li>&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;  </li><li>&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;  </li><li>&lt;description&gt;  </li><li>Driver class name for a JDBC metastore  </li><li>&lt;/description&gt;  </li><li>&lt;/property&gt;  </li><li>  </li><li>&lt;property&gt;  </li><li>&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;  </li><li>&lt;value&gt;hive&lt;/value&gt;  </li><li>&lt;description&gt;  </li><li>username to use against metastore database  </li><li>&lt;/description&gt;  </li><li>&lt;/property&gt;  </li><li>  </li><li>&lt;property&gt;  </li><li>&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;  </li><li>&lt;value&gt;exhive&lt;/value&gt;  </li><li>&lt;description&gt;  </li><li>password to use against metastore database  </li><li>&lt;/description&gt;  </li><li>&lt;/property&gt;  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">&lt;property&gt;&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;&lt;value&gt;jdbc:mysql://10.2.0.254:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;&lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;&lt;/property&gt;&lt;property&gt;&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;&lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;&lt;/property&gt;&lt;property&gt;&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;&lt;value&gt;hive&lt;/value&gt;&lt;description&gt;username to use against metastore database&lt;/description&gt;&lt;/property&gt;&lt;property&gt;&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;&lt;value&gt;exhive&lt;/value&gt;&lt;description&gt;password to use against metastore database&lt;/description&gt;&lt;/property&gt;</pre>
<h2>5.3 拷贝MySQL的JDBC驱动包</h2>
<p>把MySQL的JDBC驱动包（mysql-connector-java-5.1.21.jar）复制到Hive的lib目录下。</p>
<p>Hive元数据配置结束。</p>
<h2>5.4 测试</h2>
<p>在Hive中创建表成功后，数据库中自动创建表格，即说明配置成功。如图所示：</p>
<p><br><br><img alt="" src="http://dl.iteye.com/upload/attachment/0083/1310/beac98a9-c151-3d15-9381-c6cfe3051478.jpg"><br><br></p>
<h1><span style="color:#000000;">6<br></span></h1>
<h1>6 Hive集成Hbase</h1>
<h2>6.1 前置条件及原理</h2>
<p>Hadoop、hive和hbase环境搭建完成。Hive与Hbase的整合功能的实现是利用两者本身对外的API接口互相进行通信，相互通信主要是依靠hive-hbase-handler.jar工具类（Hive Storage Handlers）。</p>
<h2>6.2 配置hbase与hive结合包</h2>
<p>将hbase-0.94.0.jar 、zookeeper-3.3.5.jar包copy至所有节点$HDOOP_HOME/lib及$HIVE_HOME/lib下。注意$HIVE_HOME/lib含有hbase包需要删除。</p>
<p>将protobuf-java-2.4.0a.jar包copy至所有节点$HADOOP_HOME/lib下，并需要重启集群（不重启该包不会被加载进classpath）。</p>
<p>将hbase-site.xml文件copy至所有节点$HDOOP_HOME/conf下。</p>
<h2>6.3 配置hive-site.xml</h2>
<p>代码如下所示：</p>
<div>
<div>
<div>Xml代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>&lt;property&gt;  </li><li>&lt;name&gt;hive.aux.jars.path&lt;/name&gt;  </li><li>&lt;value&gt;  </li><li>file:///home/hadoop/hive/lib/hive-hbase-handler-0.8.1jar,  </li><li>file:/// home/hadoop/hive/lib/hbase-0.94.0.jar,  </li><li>file:/// home/hadoop/hive/lib/zookeeper-3.4.5.jar  </li><li>&lt;/value&gt;  </li><li>&lt;/property&gt;  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">&lt;property&gt;&lt;name&gt;hive.aux.jars.path&lt;/name&gt;&lt;value&gt;file:///home/hadoop/hive/lib/hive-hbase-handler-0.8.1jar,file:/// home/hadoop/hive/lib/hbase-0.94.0.jar,file:/// home/hadoop/hive/lib/zookeeper-3.4.5.jar&lt;/value&gt;&lt;/property&gt;</pre>
<p>Hive与hbase集成到这里就结束了。</p>
<h2>6.4 测试</h2>
<p>1、启动hive，创建表。代码如下：</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>hive&gt;CREATE TABLE hbase_hive(key string, value string)  </li><li>STORED BY  </li><li>'org.apache.hadoop.hive.hbase.HBaseStorageHandler'  </li><li>WITH SERDEPROPERTIES  </li><li>("hbase.columns.mapping"=":key,info:value")  </li><li>TBLPROPERTIES  ("hbase.table.name"= "hbase_hive");  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">hive&gt;CREATE TABLE hbase_hive(key string, value string)STORED BY'org.apache.hadoop.hive.hbase.HBaseStorageHandler'WITH SERDEPROPERTIES("hbase.columns.mapping"=":key,info:value")TBLPROPERTIES  ("hbase.table.name"= "hbase_hive");</pre>
<p>创建表成功，表名为：hbase_hive。</p>
<p>2、在hive和hbase查询表hbase_hive是否存在</p>
<p>在hive中：</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>hive&gt; show tables;  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">hive&gt; show tables;</pre>
<p><strong>hbase_hive</strong></p>
<p>在hbase中：</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>hbase(main):004:0&gt; list  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">hbase(main):004:0&gt; list</pre>
<p>TABLE                                                                </p>
<p><strong>hbase_hive                                                                                                                                       
<br></strong></p>
<p>Hive和Hbase中都出现了hbase_hive这张表，表明Hive集成Hbase成功。</p>
<h1><span style="color:#000000;">7<br></span></h1>
<h1>7 安装sqoop</h1>
<h2>7.1 下载sqoop</h2>
<p><a href="http://www.apache.org/dist/sqoop/1.4.2/sqoop-1.4.2.bin__hadoop-0.20.tar.gz" rel="nofollow">http://www.apache.org/dist/sqoop/1.4.2/sqoop-1.4.2.bin__hadoop-0.20.tar.gz</a></p>
<h2>7.2 解压</h2>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>tar –xf sqoop-1.4.2.bin__hadoop-0.20.tar.gz  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">tar –xf sqoop-1.4.2.bin__hadoop-0.20.tar.gz</pre>
<p>将解压后的sqoop-1.4.2.bin__hadoop-0.20文件放在系统的/home/hadoop/sqoop/中。</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>mkdir /home/hadoop/sqoop  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">mkdir /home/hadoop/sqoop</pre>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>mv /home/hadoop/sqoop-1.4.2.bin__hadoop-0.20 /home/hadoop/sqoop  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">mv /home/hadoop/sqoop-1.4.2.bin__hadoop-0.20 /home/hadoop/sqoop</pre>
<h2>7.3 修改配置文件</h2>
<p>修改sqoop目录下/conf/sqoop-env.sh.template中的HADOOP_HOME和HIVE_HOME安装目录。</p>
<p>步骤一：进入/home/hadoop/sqoop/conf。</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>cd /home/hadoop/sqoop/conf  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">cd /home/hadoop/sqoop/conf</pre>
<p>步骤二：复制sqoop-env.sh.template命名为sqoop-env.sh。</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>cp sqoop-env.sh.template sqoop-env.sh   </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">cp sqoop-env.sh.template sqoop-env.sh </pre>
<p>步骤三：修改HADOOP_HOME目录为/home/hadoop/hadoop。</p>
<div>
<div>
<div>Linux代码 <a title="复制代码" href="http://free9277.iteye.com/blog/1847094#" rel="nofollow">
<img alt="复制代码" src="http://free9277.iteye.com/images/icon_copy.gif"></a> <a title="收藏这段代码" href="" rel="nofollow">
<img alt="收藏代码" src="http://free9277.iteye.com/images/icon_star.png"><img src="http://free9277.iteye.com/images/spinner.gif" alt=""></a></div>
</div>
<ol><li>vim sqoop-env.sh  </li></ol></div>
<pre title="Hive部署（包括集成Hbase和Sqoop）">vim sqoop-env.sh</pre><img alt="" src="http://dl.iteye.com/upload/attachment/0083/1312/d176e1fd-716e-39a9-aac8-0c6df6cc48c7.jpg"><br><p>修改完成之后的效果如图所示。<br>
    到这里，sqoop就已经安装成功。  </p>
<p><span style="color:#000000;">如果您认为此文章有用，请点击底端的【顶】让其他人也了解此文章。</span></p>
<p>此博客为原创，转载请保留此出处，谢谢。<a href="http://free9277.iteye.com/blog/1847094" rel="nofollow">http://free9277.iteye.com/blog/1847094</a></p>
            </div>
                </div>
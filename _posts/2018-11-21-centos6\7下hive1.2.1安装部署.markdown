---
layout:     post
title:      centos6\7下hive1.2.1安装部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u014453536/article/details/52117908				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><strong>前置条件：</strong>正确安装hadoop、MySQL、jdk.</span></p>
<p><span style="line-height:21px;font-size:14px;font-family:'微软雅黑';">1、配置MySQL：在MySQL创建一个用户提供给hive使用</span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></span></p>
<pre><code class="language-html">$ mysql -uroot -proot
mysql&gt; create user 'hive' identified by 'hive';
mysql&gt; grant all privileges on *.* to 'hive' with grant option;
mysql&gt; flush privileges;</code></pre><br>
2、下载hive:<a href="http://apache.fayea.com/hive/" rel="nofollow">http://apache.fayea.com/hive/</a> 
<p></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;">3、解压安装</span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></span></p>
<pre><code class="language-html">$ tar -zxvf apache-hive-1.2.1-bin.tar.gz</code></pre>4、配置hive
<p></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(1)在/etc/profile 添加以下配置</span></p>
<p><span style="font-size:14px;line-height:21px;"></span></p>
<pre><code class="language-html">export HIVE_HOME=/usr/java/apache-hive-1.2.1-bin
export PATH=$PATH:$HIVE_HOME/bin
export CLASSPATH=$CLASSPATH:$HIVE_HOME/bin</code></pre>
<p></p>
<p><span style="font-size:14px;line-height:21px;"><span style="font-size:14px;line-height:20.3px;"><span style="font-family:'Microsoft YaHei';color:#660066;">source</span></span><span style="font-family:'Source Code Pro', monospace;color:rgb(51,51,51);font-size:14px;line-height:20.3px;">
 /etc/profile 是文件生效</span><br></span></p>
<span style="font-family:'微软雅黑';">(2)拷贝文件，执行以下命令</span><pre><code class="language-html">cp hive-default.xml.template hive-default.xml
cp hive-default.xml.template hive-site.xml
cp hive-env.sh.template hive-env.sh
cp hive-log4j.properties.template hive-log4j.properties
cp hive-exec-log4j.properties.template hive-exec-log4j.properties</code></pre><span style="font-family:'微软雅黑';">(3)修改hive-site.xml文件。</span><pre><code class="language-html">cd /usr/java/apache-hive-1.2.1-bin/conf</code></pre><pre><code class="language-html">vim hive-site.xml</code></pre><span style="font-family:'微软雅黑';">或者用拷贝到Windows中用Notepad++等编辑器编辑后再替换。</span>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;">增加如下，主要设置${</span><span style="font-size:14px;line-height:21px;font-family:'微软雅黑';background-color:rgb(240,240,240);">system:java.io.tmpdir</span><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;">}的值。</span></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></span></p>
<pre><code class="language-html">  &lt;property&gt;
    &lt;name&gt;system:java.io.tmpdir&lt;/name&gt;
    &lt;value&gt;/usr/java/apache-hive-1.2.1-bin/iotmp&lt;/value&gt;
    &lt;description/&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;system:user.name&lt;/name&gt;
    &lt;value&gt;username&lt;/value&gt;
    &lt;description/&gt;
  &lt;/property&gt;</code></pre>
<p></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
如果不进行修改，会报一下错误：<br>
Relative path in absoluteURI:${system:java.io.tmpdir%7D/$%7Bsystem:user.name%7D<br></p>
(4)由于步骤(3)使用到/usr/java/apache-hive-1.2.1-bin/iotmp，所以要在这个路径下新建iotmp文件夹<pre><code class="language-html">mkdir iotmp</code></pre>(5)在hive-site.xml中根据name值查找对应修改其value值<pre><code class="language-html">  &lt;!--Server2连接的主机名，Hive安装在Master机上 --&gt;
  &lt;property&gt;
    &lt;name&gt;hive.server2.thrift.bind.host&lt;/name&gt;
    &lt;value&gt;Master&lt;/value&gt;
    &lt;description&gt;Bind host on which to run the HiveServer2 Thrift service.&lt;/description&gt;
  &lt;/property&gt;</code></pre><pre><code class="language-html">&lt;/pre&gt;&lt;pre name="code" class="html"&gt;  &lt;!--所连接的数据库实例 --&gt;</code></pre><pre><code class="language-html">  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
    &lt;value&gt;jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;
    &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;
  &lt;/property&gt;
</code></pre><pre><code class="language-html">&lt;pre name="code" class="html"&gt;  &lt;!--数据库驱动 --&gt;
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
    &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
    &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;
  &lt;/property&gt;</code></pre><pre><code class="language-html">  &lt;!--数据库连接用户名--&gt;
  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
    &lt;value&gt;hive&lt;/value&gt;
    &lt;description&gt;Username to use against metastore database&lt;/description&gt;
  &lt;/property&gt;</code></pre>
<pre></pre>
<pre><code class="language-html">  &lt;!--数据库连接密码 --&gt;</code></pre><pre><code class="language-html">  &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
    &lt;value&gt;hive&lt;/value&gt;
    &lt;description&gt;password to use against metastore database&lt;/description&gt;
  &lt;/property&gt;
</code></pre>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></span></p>
(6)下载数据库连接jar包，这里是用MySQL的：<a href="http://dev.mysql.com/downloads/connector/j/" rel="nofollow">http://dev.mysql.com/downloads/connector/j/</a><br>
解压出mysql-connector-java-5.1.39-bin.jar包拷贝到/usr/java/apache-hive-1.2.1-bin/lib下
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br>
(7)修改hive-env.sh</span></p>
<pre><code class="language-html"># set java environment
exportJAVA_HOME=/usr/java/jdk1.8.0_65
export JRE_HOME=/usr/java/jdk1.8.0_65/jre
exportPATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
exportCLASSPATH=$CLASSPATH:.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib

# set hadoop path
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$PATH
 
# set hive environment
exportHIVE_HOME=/usr/local/apache-hive-1.2.1-bin/
export PATH=$HIVE_HOME/bin:$PATH
export HIVE_AUX_JARS_PATH=$HIVE_HOME/bin
export HIVE_CONF_DIR=$HIVE_HOME/conf
export HADOOP_USER_CLASSPATH_FIRST=true</code></pre>5、启动hive
<p></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"></span></p>
<pre><code class="language-html">cd /usr/java/apache-hive-1.2.1-bin</code></pre><pre><code class="language-html">bin/hive</code></pre><pre><code class="language-html">成功提示如下：</code></pre><pre><code class="language-html">[root@localhost apache-hive-1.2.1-bin]# bin/hive
Logging initialized using configuration in file:/usr/java/apache-hive-1.2.1-bin/conf/hive-log4j.properties
hive&gt; SHOW TABLES
</code></pre><br><br>
参考博文：http://blog.csdn.net/wang_zhenwei/article/details/50563718<br><br><br><br><br><p></p>
<p><span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br><br></span></p>
            </div>
                </div>
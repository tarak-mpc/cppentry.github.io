---
layout:     post
title:      Mac OS 安装 Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/king_old_six/article/details/81171960				</div>
								            <div id="content_views" class="markdown_views prism-tomorrow-night-eighties">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="mac-os-安装-hive">Mac OS 安装 Hive</h1>



<h4 id="一环境说明">一、环境说明</h4>

<ul>
<li>操作系统：macOS High Sierra 10.13.2</li>
<li>hadoop版本：apache hadoop 2.6.0</li>
<li>mysql版本：mysql 5.7.22</li>
<li>hive版本：apache hive 1.2.2</li>
</ul>



<h4 id="二安装步骤">二、安装步骤</h4>

<ol>
<li><p>在mysql数据库创建hive用户</p>

<pre><code>mysql&gt; create user 'hive' identified by 'hive';
</code></pre></li>
<li><p>将mysql的所有权限授权给hive用户</p>

<pre><code>mysql&gt; grant all on *.* to 'hive'@'localhost' identified by 'hive';
</code></pre></li>
<li><p>刷新mysql使1、2步骤生效</p>

<pre><code>mysql&gt; flush privileges;
</code></pre></li>
<li><p>输入sql语句查询hive用户是否存在</p>

<pre><code>   mysql&gt; select host,user,authentication_string from mysql.user;
    +-----------+---------------+-------------------------------------------+
    | host      | user          | authentication_string                     |
    +-----------+---------------+-------------------------------------------+
    | localhost | root          | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9 |
    | localhost | mysql.session | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
    | localhost | mysql.sys     | *THISISNOTAVALIDPASSWORDTHATCANBEUSEDHERE |
    | %         | hive          | *4DF1D66463C18D44E3B001A8FB1BBFBEA13E27FC |
    | localhost | hive          | *4DF1D66463C18D44E3B001A8FB1BBFBEA13E27FC |
    +-----------+---------------+-------------------------------------------+
    5 rows in set (0.00 sec)
</code></pre></li>
<li><p>使用hive用户登录mysql</p>

<pre><code>wudejin:~ oldsix$ mysql -u hive -p
Enter password: hive
mysql&gt; 
</code></pre></li>
<li><p>创建hive数据库</p>

<pre><code>mysql&gt; create database hive;
</code></pre></li>
<li><p>查看是否创建成功</p>

<pre><code>mysql&gt; show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| feature_ai         |
| hive               |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
6 rows in set (0.00 sec)
</code></pre>

<p>至此，前期的准备工作已完成，接下来，我们进入hive的安装过程。</p></li>
<li><p>下载hive安装包，并解压</p></li>
</ol>

<p><a href="https://mirrors.tuna.tsinghua.edu.cn/apache/hive/" rel="nofollow">apache-hive安装包下载点击这里</a></p>

<p>下载完成后，通过命令行解压：</p>

<pre><code>tar -zxvf apache-hive-1.2.2-bin.tar.gz
</code></pre>

<p>解压完成之后，对解压出来的文件夹重命名：</p>

<pre><code>mv apache-hive-1.2.2-bin hive1.2.2
</code></pre>

<p>删除没用的hive压缩包：</p>

<pre><code>rm -rf apache-hive-1.2.2-bin.tar.gz
</code></pre>

<ol>
<li>修改hive配置</li>
</ol>

<p>进入hive1.2.2目录下的bin目录下，修改hive-site.xml配置文件</p>

<p>bin目录下不存在hive-site.xml文件，我们需要先复制一份：</p>

<pre><code>cp hive-default.xml.template hive-site.xml
</code></pre>

<p>打开hive-site.xml文件：(找到对应name配置项，修改对应的value属性值)</p>

<ul>
<li><p>修改数据库连接驱动名：</p>

<pre><code>&lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
    &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
    &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;
&lt;/property&gt;
</code></pre></li>
<li><p>修改数据库连接URL：</p>

<pre><code>&lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
    &lt;value&gt;jdbc:mysql://localhost:3306/hive?characterEncoding=UTF-8&lt;/value&gt;
    &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;
&lt;/property&gt;
</code></pre></li>
<li><p>修改数据库连接用户名：</p>

<pre><code> &lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
    &lt;value&gt;hive&lt;/value&gt;
    &lt;description&gt;Username to use against metastore database&lt;/description&gt;
&lt;/property&gt;
</code></pre></li>
<li><p>修改数据库连接密码：</p>

<pre><code>&lt;property&gt;
    &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
    &lt;value&gt;hive&lt;/value&gt;
    &lt;description&gt;password to use against metastore database&lt;/description&gt;
&lt;/property&gt;
</code></pre></li>
<li><p>修改hive数据目录（三处）：</p>

<pre><code>&lt;property&gt;
    &lt;name&gt;hive.querylog.location&lt;/name&gt;
    &lt;value&gt;/Users/oldsix/MyApp/data/hive/iotmp&lt;/value&gt;
    &lt;description&gt;Location of Hive run time structured log file&lt;/description&gt;
&lt;/property&gt;

&lt;property&gt;
    &lt;name&gt;hive.exec.local.scratchdir&lt;/name&gt;
    &lt;value&gt;/Users/oldsix/MyApp/data/hive/iotmp&lt;/value&gt;
    &lt;description&gt;Local scratch space for Hive jobs&lt;/description&gt;
&lt;/property&gt;

&lt;property&gt;
    &lt;name&gt;hive.downloaded.resources.dir&lt;/name&gt;
    &lt;value&gt;/Users/oldsix/MyApp/data/hive/iotmp&lt;/value&gt;
    &lt;description&gt;Temporary local directory for added resources in the remote file system.&lt;/description&gt;
&lt;/property&gt;
</code></pre></li>
</ul>

<p>注：<em>所修改hive数据目录需提前创建好</em></p>

<ol>
<li><p>配置hive环境变量</p>

<pre><code>cd ~
sudo vi .base_profile
</code></pre></li>
</ol>

<p>设置HIVE_HOME,并添加到PATH</p>

<pre><code>export HIVE_HOME=/Users/oldsix/MyApp/hive
export PATH=$PATH:$HIVE_HOME/bin
</code></pre>

<p>保存退出，并使环境变量生效</p>

<pre><code>source .base_profile
</code></pre>

<ol>
<li>将对应数据库的驱动包放到hive目录下的lib目录下</li>
</ol>

<p>下载mysql-connector-java-5.1.40.jar，并上传至hive的lib目录下</p>

<ol>
<li><p>进入hadoop安装目录，启动hadoop：</p>

<pre><code>/sbin/start-all.sh
</code></pre></li>
<li><p>命令行输入 <code>jps</code> 命令，查看hadoop是否启动成功 </p></li>
<li><p>启动hive</p></li>
</ol>

<p>进入hive的bin目录下，执行命令：   <code>./hive</code></p>

<p>再执行<code>show tables;</code>、<code>show databases;</code>语句</p>

<p>若出现如下面所示，说明hive启动成功：</p>

<pre><code>wudejin:~ oldsix$ hive
readlink: illegal option -- f
usage: readlink [-n] [file ...]

Logging initialized using configuration in jar:file:/Users/oldsix/MyApp/hive/lib/hive-common-1.2.2.jar!/hive-log4j.properties
Mon Jul 16 15:15:09 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Mon Jul 16 15:15:09 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Mon Jul 16 15:15:09 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Mon Jul 16 15:15:09 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Mon Jul 16 15:15:10 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Mon Jul 16 15:15:10 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Mon Jul 16 15:15:10 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
Mon Jul 16 15:15:10 CST 2018 WARN: Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
hive&gt; show tables;
OK
Time taken: 2.122 seconds
hive&gt; show databases;
OK
default
Time taken: 0.047 seconds, Fetched: 1 row(s)
hive&gt; 
</code></pre>

<p>若出现ERROR，hive将启动失败，下面记录了自己在启动hive时遇到的错误以及解决方案：</p>

<ul>
<li>Found class jline.Terminal, but interface was expected：</li>
</ul>

<p>问题描述：</p>

<pre><code>[ERROR] Terminal initialization failed; falling back to unsupported
java.lang.IncompatibleClassChangeError: Found class jline.Terminal, but interface was expected
        at jline.TerminalFactory.create(TerminalFactory.java:101)
        at jline.TerminalFactory.get(TerminalFactory.java:158)
        at jline.console.ConsoleReader.&lt;init&gt;(ConsoleReader.java:229)
        at jline.console.ConsoleReader.&lt;init&gt;(ConsoleReader.java:221)
        at jline.console.ConsoleReader.&lt;init&gt;(ConsoleReader.java:209)
        at org.apache.hadoop.hive.cli.CliDriver.getConsoleReader(CliDriver.java:773)
        at org.apache.hadoop.hive.cli.CliDriver.executeDriver(CliDriver.java:715)
        at org.apache.hadoop.hive.cli.CliDriver.run(CliDriver.java:675)
        at org.apache.hadoop.hive.cli.CliDriver.main(CliDriver.java:615)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:57)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:606)
        at org.apache.hadoop.util.RunJar.main(RunJar.java:212)
</code></pre>

<p>问题原因：</p>

<p>hadoop2.6.0目录中存在较低版本的jline jar包（share/hadoop/yarn/lib/jline-0.9.94.jar),与hive1.2.2的lib目录下的jline jar包版本不符</p>

<p>解决办法：</p>

<p>删除hadoop的share/hadoop/yarn/lib目录下的jline-0.9.94.jar jar包，</p>

<p>将hive的lib目录下的jline-2.12.jar jar包复制到hadoop的share/hadoop/yarn/lib目录下</p>

<p>重启hadoop，再执行hive启动命令</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
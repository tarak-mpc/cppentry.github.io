---
layout:     post
title:      centos7安装Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="Hive_0"></a>Hive安装</h1>
<p>Hive的安装需要Mysql的参与，centos7安装mysql见下一篇文章：<a href="https://blog.csdn.net/qianhuihan/article/details/83474559" rel="nofollow">centos7安装mysql8.0</a>。</p>
<h3><a id="Hive_2"></a>下载Hive</h3>
<p>下载地址：<a href="http://hive.apache.org/downloads.html" rel="nofollow">http://hive.apache.org/downloads.html</a><br>
版本：apache-hive-2.3.3-bin.tar.gz</p>
<h3><a id="Hive_6"></a>安装Hive</h3>
<p>解压压缩文件，目录“/home/hadoop/apps/”</p>
<pre><code>tar -zxvf apache-hive-2.3.3-bin.tar.gz 
</code></pre>
<h5><a id="hive_11"></a>配置hive环境变量</h5>
<pre><code>vi /etc/profile

HIVE_HOME=/home/hadoop/apps/apache-hive-2.3.3-bin
HIVE_CONF_DIR=$HIVE_HOME/conf
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_HOME/bin:$HIVE_HOME/bin
export HADOOP_HOME JAVA_HOME JRE_HOME HIVE_HOME HIVE_CONF_DIR PATH CLASSPATH
</code></pre>
<p>使用<code>source /etc/profile</code>使设置生效。</p>
<h3><a id="hive_22"></a>配置hive</h3>
<p>要在Hadoop集群新建/user/hive/warehouse目录：</p>
<pre><code>hadoop fs -mkdir -p  /user/hive/warehouse
hadoop fs -chmod -R 777 /user/hive/warehouse
hadoop fs -mkdir -p /tmp/hive
hadoop fs -chmod -R 777 /tmp/hive
</code></pre>
<p>在“/home/hadoop/apps/apache-hive-2.3.3-bin”目录下：</p>
<pre><code>mkdir tmp
chmod -R 777 tmp/
</code></pre>
<p>在“/home/hadoop/apps/apache-hive-2.3.3-bin/conf”目录下：</p>
<pre><code>cp hive-default.xml.template hive-site.xml
cp hive-env.sh.template hive-env.sh 
</code></pre>
<h5><a id="hiveenvsh_41"></a>配置hive-env.sh文件</h5>
<pre><code>vi hive-env.sh

export JAVA_HOME=/home/hadoop/apps/jdk1.8.0_181
export HADOOP_HOME=/home/hadoop/apps/hadoop-2.7.5
export HIVE_HOME=/home/hadoop/apps/apache-hive-2.3.3-bin
export HIVE_CONF_DIR=$HIVE_HOME/conf
export HIVE_AUX_JARS_PATH=$HIVE_HOME/lib
</code></pre>
<h5><a id="hivesitexml_51"></a>配置hive-site.xml</h5>
<p>将所有的<code>${system:java.io.tmpdir}</code>变为<code>/home/hadoop/apps/apache-hive-2.3.3-bin/tmp</code>；<br>
将所有的<code>${system:user.name}</code>都替换为<code>root</code>。</p>
<p>其他替换，将相应的name替换为如下value：</p>
<pre><code>&lt;property&gt;
  &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
  &lt;value&gt;com.mysql.cj.jdbc.Driver&lt;/value&gt;
&lt;/property&gt; 

 &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
 &lt;value&gt;jdbc:mysql://192.168.186.101:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;
 
 &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
&lt;value&gt;root&lt;/value&gt;

&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;&lt;value&gt;qhh666888&lt;/value&gt;

</code></pre>
<p>将mysql-connector-java-8.0.11.jar（版本与mysql版本匹配）文件放置Hive的lib目录下：</p>
<pre><code> cp /var/ftp/mysql-connector-java-8.0.11.jar /home/hadoop/apps/apache-hive-2.3.3-bin/lib/
</code></pre>
<h3><a id="_75"></a>启动和测试</h3>
<h5><a id="MySQL_76"></a>对MySQL数据库初始化</h5>
<p>在hive的bin目录下：/home/hadoop/apps/apache-hive-2.3.3-bin/bin</p>
<pre><code>schematool -initSchema -dbType mysql
</code></pre>
<p><img src="https://image.ibb.co/eENUqq/18102501.png" alt="image"><br>
之后进入mysql，查看数据库，发现已经有hive数据库：<br>
<img src="https://image.ibb.co/jrOr3A/18102502.png" alt="image"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
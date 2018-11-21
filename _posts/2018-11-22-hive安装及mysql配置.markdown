---
layout:     post
title:      hive安装及mysql配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/agent_x/article/details/78660341				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="一hive安装">一、Hive安装</h2>

<p>1.解压hive，移动到指定目录下 <br>
配置环境变量</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> HIVE_HOME=/usr/local/hive
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HIVE_HOME</span>/bin</code></pre>

<p>2.在hive/conf目录下</p>

<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-keyword">cp</span> hive-env<span class="hljs-preprocessor">.sh</span><span class="hljs-preprocessor">.template</span> hive-env<span class="hljs-preprocessor">.sh</span>
<span class="hljs-keyword">cp</span> hive-default<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.template</span> hive-site<span class="hljs-preprocessor">.xml</span>
<span class="hljs-keyword">cp</span> hive-log4j2<span class="hljs-preprocessor">.properties</span><span class="hljs-preprocessor">.template</span> hive-log4j2<span class="hljs-preprocessor">.properties</span>
<span class="hljs-keyword">cp</span> hive-exec-log4j2<span class="hljs-preprocessor">.properties</span><span class="hljs-preprocessor">.template</span> hive-exec-log4j2<span class="hljs-preprocessor">.properties</span></code></pre>

<p>3.修改hive-env.sh <br>
因为 Hive 使用了 Hadoop, 需要在 hive-env.sh 文件中指定 Hadoop 安装路径：</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/usr/java    <span class="hljs-comment">##Java路径</span>
<span class="hljs-keyword">export</span> HADOOP_HOME=/usr/local/hadoop   <span class="hljs-comment">##Hadoop安装路径</span>
<span class="hljs-keyword">export</span> HIVE_HOME=/usr/local/hive    <span class="hljs-comment">##Hive安装路径</span>
<span class="hljs-keyword">export</span> HIVE_CONF_DIR=/usr/local/hive/conf    <span class="hljs-comment">##Hive配置文件路径</span></code></pre>



<h2 id="二mysql安装">二、MySQL安装</h2>

<p>CentOS7默认数据库是mariadb,配置等用着不习惯,因此决定改成mysql,但是CentOS7的yum源中默认好像是没有mysql的。为了解决这个问题，我们要先下载mysql的repo源。</p>

<p>1.下载mysql的repo源</p>

<pre class="prettyprint"><code class=" hljs avrasm">wget http://repo<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>/mysql-community-release-el7-<span class="hljs-number">5.</span>noarch<span class="hljs-preprocessor">.rpm</span></code></pre>

<p>2.安装mysql-community-release-el7-5.noarch.rpm包</p>

<pre class="prettyprint"><code class=" hljs lasso">$ sudo rpm <span class="hljs-attribute">-ivh</span> mysql<span class="hljs-attribute">-community</span><span class="hljs-attribute">-release</span><span class="hljs-attribute">-el7</span><span class="hljs-subst">-</span><span class="hljs-number">5.</span>noarch<span class="hljs-built_in">.</span>rpm</code></pre>

<p>安装这个包后，会获得两个mysql的yum repo源：</p>



<pre class="prettyprint"><code class=" hljs avrasm">/etc/yum<span class="hljs-preprocessor">.repos</span><span class="hljs-preprocessor">.d</span>/mysql-community<span class="hljs-preprocessor">.repo</span>
/etc/yum<span class="hljs-preprocessor">.repos</span><span class="hljs-preprocessor">.d</span>/mysql-community-source<span class="hljs-preprocessor">.repo</span></code></pre>

<p>3.安装mysql</p>

<pre class="prettyprint"><code class=" hljs bash">$ <span class="hljs-built_in">sudo</span> yum install mysql-server</code></pre>

<p>根据提示安装就可以了,不过安装完成后没有密码,需要重置密码</p>

<p>4.重置mysql密码</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>mysql -u root</code></pre>

<p>登录时有可能报这样的错：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">ERROR <span class="hljs-number">2002</span> (HY000): Can‘t connect <span class="hljs-built_in">to</span> <span class="hljs-built_in">local</span> MySQL server through <span class="hljs-built_in">socket</span> ‘/var/lib/mysql/mysql.sock‘ (<span class="hljs-number">2</span>)</code></pre>

<p>原因是/var/lib/mysql的访问权限问题。下面的命令把/var/lib/mysql的拥有者改为当前用户：</p>



<pre class="prettyprint"><code class=" hljs lasso">$ sudo chown <span class="hljs-attribute">-R</span> root:root /<span class="hljs-built_in">var</span>/lib/mysql</code></pre>

<p>5.重启mysql服务</p>

<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>service mysqld restart</code></pre>

<p>6.接下来登录重置</p>

<pre class="prettyprint"><code class=" hljs php">$ mysql -u root  <span class="hljs-comment">//直接回车进入mysql控制台</span>
mysql &gt; <span class="hljs-keyword">use</span> <span class="hljs-title">mysql</span>;
mysql &gt; update user set password=password(<span class="hljs-string">'123456'</span>) where user=<span class="hljs-string">'root'</span>;
mysql &gt; <span class="hljs-keyword">exit</span>;</code></pre>



<h2 id="三创建hdfs目录">三、创建HDFS目录</h2>

<p>在 Hive 中创建表之前需要创建以下 HDFS 目录并给它们赋相应的权限。</p>

<pre class="prettyprint"><code class=" hljs lasso">hdfs dfs <span class="hljs-attribute">-mkdir</span> <span class="hljs-attribute">-p</span> /user/hive/warehouse
hdfs dfs <span class="hljs-attribute">-mkdir</span> <span class="hljs-attribute">-p</span> /user/hive/tmp
hdfs dfs <span class="hljs-attribute">-mkdir</span> <span class="hljs-attribute">-p</span> /user/hive/<span class="hljs-keyword">log</span>
hdfs dfs <span class="hljs-attribute">-chmod</span> g<span class="hljs-subst">+</span>w /user/hive/warehouse
hdfs dfs <span class="hljs-attribute">-chmod</span> g<span class="hljs-subst">+</span>w /usr/hive/tmp
hdfs dfs <span class="hljs-attribute">-chmod</span> g<span class="hljs-subst">+</span>w /usr/hive/<span class="hljs-keyword">log</span></code></pre>



<h2 id="四配置jdbc的驱动">四、配置jdbc的驱动</h2>

<p>MySQL Java 连接器添加到 $HIVE_HOME/lib 目录下。我安装时使用的是 <a href="http://download.csdn.net/download/agent_x/10136751" rel="nofollow">mysql-connector-java-5.1.43.jar</a>。</p>

<h2 id="五hive-metastore配置">五、Hive Metastore配置</h2>

<p>默认情况下, Hive 的元数据保存在内嵌的 Derby 数据库里, 但一般情况下生产环境会使用 MySQL 来存放 Hive 元数据。</p>

<p>1.创建数据库和用户</p>

<p>假定你已经安装好 MySQL。下面创建一个 hive 数据库用来存储 Hive 元数据，且数据库访问的用户名和密码都为 hive。</p>

<pre class="prettyprint"><code class=" hljs oxygene">mysql&gt; <span class="hljs-keyword">CREATE</span> DATABASE hive; 
mysql&gt; USE hive; 
mysql&gt; <span class="hljs-keyword">CREATE</span> USER <span class="hljs-string">'hive'</span>@<span class="hljs-string">'localhost'</span> IDENTIFIED <span class="hljs-keyword">BY</span> <span class="hljs-string">'hive'</span>;
mysql&gt; GRANT ALL <span class="hljs-keyword">ON</span> hive.* <span class="hljs-keyword">TO</span> <span class="hljs-string">'hive'</span>@<span class="hljs-string">'localhost'</span> IDENTIFIED <span class="hljs-keyword">BY</span> <span class="hljs-string">'hive'</span>; 
mysql&gt; GRANT ALL <span class="hljs-keyword">ON</span> hive.* <span class="hljs-keyword">TO</span> <span class="hljs-string">'hive'</span>@<span class="hljs-string">'%'</span> IDENTIFIED <span class="hljs-keyword">BY</span> <span class="hljs-string">'hive'</span>; 
mysql&gt; FLUSH PRIVILEGES; 
mysql&gt; quit;</code></pre>

<p>2.修改hive-site.xml</p>

<p>然后在配置文件hive-site.xml中，把所有的<code>${system:java.io.tmpdir}</code> 都替换为/usr/local/hive/tmp，把所有的<code>${system:user.name}</code>替换为<code>${user.name}</code> <br>
直接修改为下面这样也可以，注意用户名和密码，对应上一步中mysql创建的用户名和密码</p>

<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.exec.scratchdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/user/hive/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.metastore.warehouse.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/user/hive/warehouse<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.querylog.location<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/user/hive/log<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&amp;amp;characterEncoding=UTF-8&amp;amp;useSSL=false<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.mysql.jdbc.Driver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>3.创建/usr/local/hive/tmp文件夹</p>

<pre class="prettyprint"><code class=" hljs perl"><span class="hljs-keyword">mkdir</span> /usr/<span class="hljs-keyword">local</span>/hive/tmp</code></pre>

<p>4.运行Hive</p>

<p>在命令行运行 hive 命令时必须保证以下两点：</p>

<ul>
<li>HDFS 已经启动。可以使用 start-dfs.sh 脚本来启动 HDFS。</li>
<li><p>运行 schematool 命令来执行初始化操作。</p>

<pre><code>schematool -dbType mysql -initSchema
</code></pre></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
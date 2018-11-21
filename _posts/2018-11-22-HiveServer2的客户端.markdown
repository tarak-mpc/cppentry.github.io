---
layout:     post
title:      HiveServer2的客户端
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文翻译自hive官网 <br>
<a href="https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients" rel="nofollow">https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients</a> <br>
前面的我都用过的，但是从kerberos认证之后，我就没使用过了。如果感觉翻译有疑问，请看源文档哈。 <br>
Beeline-一个新命令行客户端 <br>
  Beeline的列子 <br>
  Beeline的命令 <br>
  Beeline的hive命令  <br>
  Beeline命令行参数 <br>
  分割符输出格式 <br>
  HiveServer2的日志 <br>
JDBC <br>
  连接的urls <br>
    连接URL的格式 <br>
    远程连接URL或者本地模式 <br>
    当HiverServer2运行在HTTP模式中的连接URL <br>
    当在HiverServer2的SSL已经启动的时候的连接URL <br>
  使用JDBC <br>
    JDBC客户端的简单代码 <br>
    运行JDBC客户端简单代码 <br>
  JDBC的数据类型 <br>
  对于安全的集群，JDBC的客户端设置 <br>
    多用户场景和自动登录到Kerberos kdc <br>
     使用kerberos的预认证 <br>
Python客户端 <br>
Ruby客户端 <br>
与SQuirrel Sql集成 <br>
与Dbvissoftware的Dbvisualizer集成 <br>
集成其他工具的高级特征 <br>
  在HTTP模式中支持Cookie的重置 <br>
  在HTTP模式中可以使用双向的ssl <br>
  通过jdbc的驱动，传输http头的key-value数据</p>

<p>这篇文章描述了不同的支持hiveserver2的客户端 <br>
 <strong>Beeline-一个新命令行客户端</strong> <br>
 hiveserver2支持一个新的shell客户端。它是基于SQLline的客户端的一个jbdc客户端。这里有详细描述<a href="http://sqlline.sourceforge.net/#manual" rel="nofollow">documentation</a>，也有利于理解beeline。 <br>
 beeline客户端支持远程模式和本地模式。在本地模式中，它运行一个本地的hive（类似hive 客户端），然后原创模式中，通过thrift连接一个分离的hiveserver2的进程。在hive0.14之后，在Hiveserver2环境使用beeline，它也从hiverserv2中执行输出标准错误，会打印此日志信息。 <br>
 ps：在远程模式中，hiveserver2仅仅只支持thrift认证的连接。甚至HTTP模式中，这个消息的body也要包含thrift. <br>
<strong>Beeline的列子</strong>   </p>

<pre class="prettyprint"><code class=" hljs smalltalk">% bin/beeline
<span class="hljs-class">Hive</span> version <span class="hljs-number">0.11</span><span class="hljs-number">.0</span>-<span class="hljs-class">SNAPSHOT</span> by <span class="hljs-class">Apache</span>
beeline&gt; !connect <span class="hljs-method">jdbc:</span><span class="hljs-method">hive2:</span>//<span class="hljs-method">localhost:</span><span class="hljs-number">10000</span> scott tiger org.apache.hive.jdbc.<span class="hljs-class">HiveDriver</span>
!connect <span class="hljs-method">jdbc:</span><span class="hljs-method">hive2:</span>//<span class="hljs-method">localhost:</span><span class="hljs-number">10000</span> scott tiger org.apache.hive.jdbc.<span class="hljs-class">HiveDriver</span>
<span class="hljs-class">Connecting</span> to <span class="hljs-method">jdbc:</span><span class="hljs-method">hive2:</span>//<span class="hljs-method">localhost:</span><span class="hljs-number">10000</span>
<span class="hljs-class">Connected</span> <span class="hljs-method">to:</span> <span class="hljs-class">Hive</span> (version <span class="hljs-number">0.10</span><span class="hljs-number">.0</span>)
<span class="hljs-class">Driver</span>: <span class="hljs-class">Hive</span> (version <span class="hljs-number">0.10</span><span class="hljs-number">.0</span>-<span class="hljs-class">SNAPSHOT</span>)
<span class="hljs-class">Transaction</span> <span class="hljs-method">isolation:</span> <span class="hljs-class">TRANSACTION_REPEATABLE_READ</span>
<span class="hljs-number">0</span>: <span class="hljs-method">jdbc:</span><span class="hljs-method">hive2:</span>//<span class="hljs-method">localhost:</span><span class="hljs-number">10000</span>&gt; show tables;
show tables;
+-------------------+
<span class="hljs-localvars">|     tab_name      |</span>
+-------------------+
<span class="hljs-localvars">| primitives        |</span>
<span class="hljs-localvars">| src               |</span>
<span class="hljs-localvars">| src1              |</span>
<span class="hljs-localvars">| src_json          |</span>
<span class="hljs-localvars">| src_sequencefile  |</span>
<span class="hljs-localvars">| src_thrift        |</span>
<span class="hljs-localvars">| srcbucket         |</span>
<span class="hljs-localvars">| srcbucket2        |</span>
<span class="hljs-localvars">| srcpart           |</span>
+-------------------+
<span class="hljs-number">9</span> rows selected (<span class="hljs-number">1.079</span> seconds)</code></pre>

<p>ps:使用没有安全认证的beeline连接，如果你想通过 NOSASL模式连接，你必须明确的指定认证模式</p>



<pre class="prettyprint"><code class=" hljs xml">% bin/beeline
beeline&gt; !connect jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>/<span class="hljs-tag">&lt;<span class="hljs-title">db</span>&gt;</span>;auth=noSasl hiveuser pass org.apache.hive.jdbc.HiveDriver</code></pre>

<p><strong>Beeline的命令</strong></p>

<table>
<thead>
<tr>
  <th>command</th>
  <th align="center">description</th>
</tr>
</thead>
<tbody><tr>
  <td>!&lt;’sqlline command’&gt;</td>
  <td align="center">sqlline的命令，看如下连接的详细信息<a href="http://sqlline.sourceforge.net/" rel="nofollow">sqlline</a>,!quit是退出beeline客户端</td>
</tr>
</tbody></table>


<p><strong>Beeline的hive命令 </strong> <br>
在hive的jdbc区中被使用的时候，hive指定命令（和hive客户端命令一样）能够在beeline客户端运行。使用”;”来区分命令。在脚本中的命令使用”–”来识别。</p>

<table>
<thead>
<tr>
  <th>command</th>
  <th align="center">description</th>
</tr>
</thead>
<tbody><tr>
  <td>reset</td>
  <td align="center">重置默认的配置</td>
</tr>
<tr>
  <td>set key value</td>
  <td align="center">设置特别的配置变量，如果你的配置变量的名称写错了，beeline客户端不会报错</td>
</tr>
<tr>
  <td>set -v</td>
  <td align="center">打印出hadoop和hive的配置变量</td>
</tr>
<tr>
  <td>add files filepath,add jars filepath,add archives filepath</td>
  <td align="center">添加一个或者多个文件，jar包，archives包到分布式缓存的资源列表中。看<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli#LanguageManualCli-HiveResources" rel="nofollow">hive resources</a>详情</td>
</tr>
<tr>
  <td>list files,list jars,list archive</td>
  <td align="center">列出已经添加进分布式缓存中的资源</td>
</tr>
<tr>
  <td>list files filepath,list jars filepath,list archives filepath</td>
  <td align="center">查看是否已经添加进分布式缓存中</td>
</tr>
<tr>
  <td>delete files path,delete jars path,delete archives path</td>
  <td align="center">从分布式缓存中移除资源</td>
</tr>
<tr>
  <td>delete/add files ivyurl,delete/add jars ivyurl,delete/add archives ivyurl</td>
  <td align="center">ivy是apache的依赖管理工具，从hive1.2.0之后，就是可以使用ivy来管理依赖包</td>
</tr>
<tr>
  <td>reload</td>
  <td align="center">从hive0.14.0之后，是的hiveserver2明确的知道jar包的变化（在指定的配置文件路径中）hive.reloadable.aux.jars.path。这个变化可能是add,remove,update jar包</td>
</tr>
<tr>
  <td>query string</td>
  <td align="center">执行hive的查询语句，然后打印结果到标准输出流</td>
</tr>
</tbody></table>


<p><strong>Beeline命令行参数，常用的</strong></p>

<table>
<thead>
<tr>
  <th>选项</th>
  <th align="center">描述</th>
</tr>
</thead>
<tbody><tr>
  <td>-u db_url</td>
  <td align="center">jdbc的url，列如:beeline -u db_url</td>
</tr>
<tr>
  <td>-n username</td>
  <td align="center">db连接串的用户名，列如:beeline -n username</td>
</tr>
<tr>
  <td>-p password</td>
  <td align="center">db连接串的密码，列如:beeline -p password</td>
</tr>
<tr>
  <td>-d driver class</td>
  <td align="center">连接的驱动,列如:beeline -d org.apache.hive.jdbc.HiveDriver</td>
</tr>
<tr>
  <td>-e query</td>
  <td align="center">被执行的查询语句，查询语句中需要使用双引号和单引号，列如:beeline -e “query_string”</td>
</tr>
<tr>
  <td>-f filepath</td>
  <td align="center">运行文件中的脚本</td>
</tr>
<tr>
  <td>–hiveconf property=value</td>
  <td align="center">指定配置变量的值，在hive.conf.restricted.list中列出的配置变量是不能够被–hiveconf重置的。看详情<a href="https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties#ConfigurationProperties-RestrictedListandWhitelist" rel="nofollow">restricted list and whitelist</a></td>
</tr>
<tr>
  <td>–hivevar name=value</td>
  <td align="center">hive的变量，这个可以在hive-specific设置中被指定，可以在session级别指定</td>
</tr>
<tr>
  <td>–showHeader true/false</td>
  <td align="center">在查询结果中是否显示列的名字</td>
</tr>
</tbody></table>


<p><strong>分割符输出格式</strong> <br>
  在hive0.14之后，在输出SV格式上有所提升，有DSV，CSV2和TSV2。这些格式比标准的CSV（在包含特殊字符-分隔符，单引号的时候，会在每列中加单引号）好很多。从分布符区分不同，有3中输出格式，逗号-CSV2，tab-TSV2，通过delimiterForDsv属性配置-DSV。 <br>
  CSV和TSV格式可以向后兼容，但是在输出的每一列中有单引号围绕。 <br>
   <strong>HiveServer2的日志</strong> <br>
   在hive0.14.0之后，hiveserver2的操作日志对于beeline客户端是可见的。这些配置日志信息如下： <br>
hive.server2.logging.operation.enabled <br>
hive.server2.logging.operation.log.location <br>
hive.server2.logging.operation.verbose (Hive 0.14 to 1.1) <br>
hive.server2.logging.operation.level (Hive 1.2 onward) <br>
在hive2.0.0中添加了日志queryid和sessionid的支持。在配置文件中使用edit/add %X{queryId} and %X{sessionId} 这种通配符查询它。 <br>
<strong>JDBC</strong> <br>
hiveserver2有一个新的jdbc驱动，它支持本地和远程模式接入hiveserver2。 <br>
<strong>连接URL的格式</strong> <br>
hiveserver的url格式如下:</p>



<pre class="prettyprint"><code class=" hljs xml">jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host1</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port1</span>&gt;</span>,<span class="hljs-tag">&lt;<span class="hljs-title">host2</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port2</span>&gt;</span>/dbName;sess_var_list?hive_conf_list#hive_var_list</code></pre>

<p>其中： <br>
host:port1,host:port2是通过逗号分隔的服务器实列，如果开启的了HA，自动切换服务，如果为null，自动使用本地模式连接。 <br>
db_name：是数据库的名字。 <br>
sess_var_list：是通过分号区分的 key=value定义，这个是seesion级别的变量，比如user=foo;password=bar <br>
hive_conf_list：分号分隔，设置session级别的配置变量 <br>
hive_var_list:分号分隔，设置session基本的hive变量 <br>
<strong>远程连接URL或者本地模式</strong> <br>
Jbdc的连接url，有前缀jdbc:hive2://,驱动类为org.apache.hive.jdbc.HiveDriver,注意下这个驱动类和老的hiveserver不同。 <br>
在远程模式中，这个url格式如下:jdbc:hive2://host:port/db（默认的端口为10000） <br>
在本地模式中，这个url格式如下: jdbc:hive2://(没有写host和端口) <br>
<strong>当HiverServer2运行在HTTP模式中的连接URL</strong> <br>
jbdc连接url，</p>

<pre class="prettyprint"><code class=" hljs xml">jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>/<span class="hljs-tag">&lt;<span class="hljs-title">db</span>&gt;</span>;transportMode=http;httpPath=<span class="hljs-tag">&lt;<span class="hljs-title">http_endpoint</span>&gt;</span></code></pre>

<p>其中 <br>
http_endpoint这个对应的HTTP endpoint配置是在hive-site.xml。默认是cliservice。这个模式是基于http传输数据的。 <br>
http传输模式的端口默认为10001 <br>
在0.14之前的版本中，使用hive.server2.transport.mode和hive.server2.thrift.http.path来分别使用，这是hive_conf_list中的一部分参数。这些版本已经弃用新版的支持了（这些都进入sess_var_list的部分了），但是现在还是可以工作。就是说这个两个参数已经是sess_var_list了。以后设置的话，要在sess_var_list中。 <br>
<strong>当在HiverServer2的SSL已经启动的时候的连接URL</strong> <br>
jdbc连接串url：</p>



<pre class="prettyprint"><code class=" hljs xml">dbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>/<span class="hljs-tag">&lt;<span class="hljs-title">db</span>&gt;</span>;ssl=true;sslTrustStore=<span class="hljs-tag">&lt;<span class="hljs-title">trust_store_path</span>&gt;</span>;trustStorePassword=<span class="hljs-tag">&lt;<span class="hljs-title">trust_store_password</span>&gt;</span></code></pre>

<p>其中 <br>
trust_store_path：是客户端truststore文件的路径 <br>
trust_store_password:是接入truststore的密码 <br>
在http模式中：</p>



<pre class="prettyprint"><code class=" hljs xml">jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>/<span class="hljs-tag">&lt;<span class="hljs-title">db</span>&gt;</span>;ssl=true;sslTrustStore=<span class="hljs-tag">&lt;<span class="hljs-title">trust_store_path</span>&gt;</span>;trustStorePassword=<span class="hljs-tag">&lt;<span class="hljs-title">trust_store_password</span>&gt;</span>;transportMode=http;httpPath=<span class="hljs-tag">&lt;<span class="hljs-title">http_endpoint</span>&gt;</span></code></pre>

<p>就是说在HTTP模式下，连接串如上。 <br>
<strong>JDBC</strong> <br>
你能够使用jdbc来接入数据的存储，步骤如下 <br>
1加载hiveserver2的jdbc驱动，在hive1.2.0之后，加载驱动使用的是Class.forName(),列如：</p>



<pre class="prettyprint"><code class=" hljs vbnet"><span class="hljs-keyword">Class</span>.forName(<span class="hljs-string">"org.apache.hive.jdbc.HiveDriver"</span>);</code></pre>

<p>2通过创建连接连接数据库，例如</p>



<pre class="prettyprint"><code class=" hljs xml">Connection cnct = DriverManager.getConnection("jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>", "<span class="hljs-tag">&lt;<span class="hljs-title">user</span>&gt;</span>", "<span class="hljs-tag">&lt;<span class="hljs-title">password</span>&gt;</span>");</code></pre>

<p>默认的端口是10000,在非安全模式下，指定一个用户名就可以运行了。在安全模式下，password可以忽略的。如下：</p>

<pre class="prettyprint"><code class=" hljs avrasm">Connection cnct = DriverManager<span class="hljs-preprocessor">.getConnection</span>(<span class="hljs-string">"jdbc:hive2://&lt;host&gt;:&lt;port&gt;"</span>, <span class="hljs-string">"&lt;user&gt;"</span>, <span class="hljs-string">""</span>)<span class="hljs-comment">;</span></code></pre>

<p>在kerberos安全模式中，用户信息是基于kerberos认证的。 <br>
3通过创建一个statement对象来提交查询语句，使用executeQuery()执行。列如:</p>



<pre class="prettyprint"><code class=" hljs avrasm">Statement stmt = cnct<span class="hljs-preprocessor">.createStatement</span>()<span class="hljs-comment">;</span>
ResultSet rset = stmt<span class="hljs-preprocessor">.executeQuery</span>(<span class="hljs-string">"SELECT foo FROM bar"</span>)<span class="hljs-comment">;</span></code></pre>

<p>4如果需要，就遍历这个结果集。 <br>
这些步骤在如下的一个简单的列子中。</p>



<pre class="prettyprint"><code class=" hljs avrasm">import java<span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.SQLException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.ResultSet</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.Statement</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.sql</span><span class="hljs-preprocessor">.DriverManager</span><span class="hljs-comment">;</span>

public class HiveJdbcClient {
  private static String driverName = <span class="hljs-string">"org.apache.hive.jdbc.HiveDriver"</span><span class="hljs-comment">;</span>

  <span class="hljs-comment">/**
   * @param args
   * @throws SQLException
   */</span>
  public static void main(String[] args) throws SQLException {
      try {
      Class<span class="hljs-preprocessor">.forName</span>(driverName)<span class="hljs-comment">;</span>
    } catch (ClassNotFoundException e) {
      // TODO Auto-generated catch block
      e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
      System<span class="hljs-preprocessor">.exit</span>(<span class="hljs-number">1</span>)<span class="hljs-comment">;</span>
    }
    //replace <span class="hljs-string">"hive"</span> here with the name of the user the queries should run as
    Connection con = DriverManager<span class="hljs-preprocessor">.getConnection</span>(<span class="hljs-string">"jdbc:hive2://localhost:10000/default"</span>, <span class="hljs-string">"hive"</span>, <span class="hljs-string">""</span>)<span class="hljs-comment">;</span>
    Statement stmt = con<span class="hljs-preprocessor">.createStatement</span>()<span class="hljs-comment">;</span>
    String tableName = <span class="hljs-string">"testHiveDriverTable"</span><span class="hljs-comment">;</span>
    stmt<span class="hljs-preprocessor">.execute</span>(<span class="hljs-string">"drop table if exists "</span> + tableName)<span class="hljs-comment">;</span>
    stmt<span class="hljs-preprocessor">.execute</span>(<span class="hljs-string">"create table "</span> + tableName + <span class="hljs-string">" (key int, value string)"</span>)<span class="hljs-comment">;</span>
    // show tables
    String sql = <span class="hljs-string">"show tables '"</span> + tableName + <span class="hljs-string">"'"</span><span class="hljs-comment">;</span>
    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Running: "</span> + sql)<span class="hljs-comment">;</span>
    ResultSet res = stmt<span class="hljs-preprocessor">.executeQuery</span>(sql)<span class="hljs-comment">;</span>
    if (res<span class="hljs-preprocessor">.next</span>()) {
      System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(res<span class="hljs-preprocessor">.getString</span>(<span class="hljs-number">1</span>))<span class="hljs-comment">;</span>
    }
       // describe table
    sql = <span class="hljs-string">"describe "</span> + tableName<span class="hljs-comment">;</span>
    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Running: "</span> + sql)<span class="hljs-comment">;</span>
    res = stmt<span class="hljs-preprocessor">.executeQuery</span>(sql)<span class="hljs-comment">;</span>
    while (res<span class="hljs-preprocessor">.next</span>()) {
      System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(res<span class="hljs-preprocessor">.getString</span>(<span class="hljs-number">1</span>) + <span class="hljs-string">"\t"</span> + res<span class="hljs-preprocessor">.getString</span>(<span class="hljs-number">2</span>))<span class="hljs-comment">;</span>
    }

    // load data into table
    // NOTE: filepath has to be local to the hive server
    // NOTE: /tmp/a<span class="hljs-preprocessor">.txt</span> is a ctrl-A separated file with two fields per line
    String filepath = <span class="hljs-string">"/tmp/a.txt"</span><span class="hljs-comment">;</span>
    sql = <span class="hljs-string">"load data local inpath '"</span> + filepath + <span class="hljs-string">"' into table "</span> + tableName<span class="hljs-comment">;</span>
    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Running: "</span> + sql)<span class="hljs-comment">;</span>
    stmt<span class="hljs-preprocessor">.execute</span>(sql)<span class="hljs-comment">;</span>

    // select * query
    sql = <span class="hljs-string">"select * from "</span> + tableName<span class="hljs-comment">;</span>
    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Running: "</span> + sql)<span class="hljs-comment">;</span>
    res = stmt<span class="hljs-preprocessor">.executeQuery</span>(sql)<span class="hljs-comment">;</span>
    while (res<span class="hljs-preprocessor">.next</span>()) {
      System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(String<span class="hljs-preprocessor">.valueOf</span>(res<span class="hljs-preprocessor">.getInt</span>(<span class="hljs-number">1</span>)) + <span class="hljs-string">"\t"</span> + res<span class="hljs-preprocessor">.getString</span>(<span class="hljs-number">2</span>))<span class="hljs-comment">;</span>
    }

    // regular hive query
    sql = <span class="hljs-string">"select count(1) from "</span> + tableName<span class="hljs-comment">;</span>
    System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Running: "</span> + sql)<span class="hljs-comment">;</span>
    res = stmt<span class="hljs-preprocessor">.executeQuery</span>(sql)<span class="hljs-comment">;</span>
    while (res<span class="hljs-preprocessor">.next</span>()) {
      System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(res<span class="hljs-preprocessor">.getString</span>(<span class="hljs-number">1</span>))<span class="hljs-comment">;</span>
    }
  }
}</code></pre>

<p><strong>运行JDBC客户端简单代码</strong></p>

<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># Then on the command-line</span>
$ javac HiveJdbcClient.java

<span class="hljs-preprocessor"># To run the program using remote hiveserver in non-kerberos mode, we need the following jars in the classpath</span>
<span class="hljs-preprocessor"># from hive/build/dist/lib</span>
<span class="hljs-preprocessor">#     hive-jdbc*.jar</span>
<span class="hljs-preprocessor">#     hive-service*.jar</span>
<span class="hljs-preprocessor">#     libfb303-0.9.0.jar</span>
<span class="hljs-preprocessor">#     libthrift-0.9.0.jar</span>
<span class="hljs-preprocessor">#     log4j-1.2.16.jar</span>
<span class="hljs-preprocessor">#     slf4j-api-1.6.1.jar</span>
<span class="hljs-preprocessor">#     slf4j-log4j12-1.6.1.jar</span>
<span class="hljs-preprocessor">#     commons-logging-1.0.4.jar</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># To run the program using kerberos secure mode, we need the following jars in the classpath</span>
<span class="hljs-preprocessor">#     hive-exec*.jar</span>
<span class="hljs-preprocessor">#     commons-configuration-1.6.jar (This is not needed with Hadoop 2.6.x and later).</span>
<span class="hljs-preprocessor">#  and from hadoop</span>
<span class="hljs-preprocessor">#     hadoop-core*.jar</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># To run the program in embedded mode, we need the following additional jars in the classpath</span>
<span class="hljs-preprocessor"># from hive/build/dist/lib</span>
<span class="hljs-preprocessor">#     hive-exec*.jar</span>
<span class="hljs-preprocessor">#     hive-metastore*.jar</span>
<span class="hljs-preprocessor">#     antlr-runtime-3.0.1.jar</span>
<span class="hljs-preprocessor">#     derby.jar</span>
<span class="hljs-preprocessor">#     jdo2-api-2.1.jar</span>
<span class="hljs-preprocessor">#     jpox-core-1.2.2.jar</span>
<span class="hljs-preprocessor">#     jpox-rdbms-1.2.2.jar</span>
<span class="hljs-preprocessor"># and from hadoop/build</span>
<span class="hljs-preprocessor">#     hadoop-core*.jar</span>
<span class="hljs-preprocessor"># as well as hive/build/dist/conf, any HIVE_AUX_JARS_PATH set, </span>
<span class="hljs-preprocessor"># and hadoop jars necessary to run MR jobs (eg lzo codec)</span>

$ java -cp $CLASSPATH HiveJdbcClient</code></pre>

<p>上面也了连个中模式下，运行需要的jar包。 <br>
或者，你可以通过脚本方式运行，在提交jar文件到客户端前，你需要构建你的classpath和准好好数据文件。这个脚本添加所有的依赖jar包，为了在本地模式也可以可以hiveserver2。</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-shebang">#!/bin/bash</span>
HADOOP_HOME=/your/path/to/hadoop
HIVE_HOME=/your/path/to/hive

<span class="hljs-built_in">echo</span> <span class="hljs-operator">-e</span> <span class="hljs-string">'1\x01foo'</span> &gt; /tmp/a.txt
<span class="hljs-built_in">echo</span> <span class="hljs-operator">-e</span> <span class="hljs-string">'2\x01bar'</span> &gt;&gt; /tmp/a.txt

HADOOP_CORE=$(ls <span class="hljs-variable">$HADOOP_HOME</span>/hadoop-core*.jar)
CLASSPATH=.:<span class="hljs-variable">$HIVE_HOME</span>/conf:$(hadoop classpath)

<span class="hljs-keyword">for</span> i <span class="hljs-keyword">in</span> <span class="hljs-variable">${HIVE_HOME}</span>/lib/*.jar ; <span class="hljs-keyword">do</span>
    CLASSPATH=<span class="hljs-variable">$CLASSPATH</span>:<span class="hljs-variable">$i</span>
<span class="hljs-keyword">done</span>

java -cp <span class="hljs-variable">$CLASSPATH</span> HiveJdbcClient</code></pre>

<p><strong>JDBC的数据类型</strong> <br>
下面是表中的字段数据类型：</p>

<table>
<thead>
<tr>
  <th>hive类型</th>
  <th align="center">java类型</th>
  <th align="center">说明</th>
</tr>
</thead>
<tbody><tr>
  <td>tinyint</td>
  <td align="center">byte</td>
  <td align="center">1字节</td>
</tr>
<tr>
  <td>smallint</td>
  <td align="center">short</td>
  <td align="center">2字节</td>
</tr>
<tr>
  <td>int</td>
  <td align="center">int</td>
  <td align="center">4字节</td>
</tr>
<tr>
  <td>bigint</td>
  <td align="center">long</td>
  <td align="center">8字节</td>
</tr>
<tr>
  <td>float</td>
  <td align="center">double</td>
  <td align="center">7位数字</td>
</tr>
<tr>
  <td>double</td>
  <td align="center">double</td>
  <td align="center">15位数字</td>
</tr>
<tr>
  <td>decimal</td>
  <td align="center">java.math.bigdecimal</td>
  <td align="center">固定精度的10进制</td>
</tr>
<tr>
  <td>boolean</td>
  <td align="center">boolean</td>
  <td align="center">0 or 1</td>
</tr>
<tr>
  <td>string</td>
  <td align="center">string</td>
  <td align="center">字符串</td>
</tr>
<tr>
  <td>timestamp</td>
  <td align="center">java.sql.timestamp</td>
  <td align="center">日期和时间值</td>
</tr>
<tr>
  <td>binray</td>
  <td align="center">string</td>
  <td align="center">binary</td>
</tr>
<tr>
  <td>复杂类型</td>
  <td align="center"></td>
  <td align="center"></td>
</tr>
<tr>
  <td>array</td>
  <td align="center">string-json</td>
  <td align="center">数组</td>
</tr>
<tr>
  <td>map</td>
  <td align="center">string-json</td>
  <td align="center">key-value值</td>
</tr>
<tr>
  <td>struct</td>
  <td align="center">string-json</td>
  <td align="center">类似结构体</td>
</tr>
</tbody></table>


<p><strong>对于安全的集群，JDBC的客户端设置</strong> <br>
当使用kerberso认证连接hiveserver2的时候，连接url格式如下：</p>



<pre class="prettyprint"><code class=" hljs xml">jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>/<span class="hljs-tag">&lt;<span class="hljs-title">db</span>&gt;</span>;principal=<span class="hljs-tag">&lt;<span class="hljs-title">Server_Principal_of_HiveServer2</span>&gt;</span></code></pre>

<p>在连接hiverserver2的时候，客户端需要拥有一个认证的kerberos ticket。 <br>
注意：如果你在端口后面没有”/”，jdbc驱动不会解析hostname,还会终止hiveserver2，使用本地模式连接。如果你指定了hostname，确保你在端口后面，有”/”. <br>
在LDAP（轻量级目录访问协议）中，CUSTOM和PAM认证，客户端需要一个认证的用户米和密码，jdbc来连接。</p>

<pre class="prettyprint"><code class=" hljs fix"><span class="hljs-attribute">jdbc:hive://hostname/dbname;sasl.qop</span>=<span class="hljs-string">auth-int</span></code></pre>

<p>看详情<a href="https://cwiki.apache.org/confluence/display/Hive/Setting+Up+HiveServer2" rel="nofollow">setting up hiveserver2</a> <br>
<strong>多用户场景和自动登录到Kerberos kdc</strong> <br>
在当前使用kerberos的方式中，在连接之前，在ticket缓存中，你需要拥有一个认证的kerberos ticket。Kerberos需要3种方式登录（kinit,key tab和ticketcache），一个kerberos用户的限制登录一个客户端。这些限制限制了在中间系统中和多用户场景中的使用。在多用户场景张，客户端需要自动登录到kerberos的kdc。 <br>
解决多用户场景限制的一种方式是，安全代理用户（详情<a href="https://issues.apache.org/jira/browse/HIVE-5155" rel="nofollow">hive-515</a>）。在hive0.13.0之后，有2个组件支持代理用户： <br>
1通过oozie来管理连接的token。这个是hadoop生态圈组件的共性。 <br>
2直接使用代理接入到hadoop用户的权限中。这个能是有权限的用户直接指定一个交替的seesion用户-在连接的时候。如果连接用户拥有hadoop级别的权限（模拟的请求userid）,hiveserver2将会给这个请求用户运行一个session。 <br>
还有种方式是使用 pre-authenticated Kerberos（详看<a href="https://issues.apache.org/jira/browse/HIVE-6486" rel="nofollow">hive-6486</a>）。在这种模式中，住hive0.13.0，这个jbdc的驱动客户端使用pre-authenticated Kerberos来认证用户连接hiveserver2。这能使中间系统如在运行hive客户端那样运行查询语句。 <br>
 <strong>使用kerberos的预认证</strong> <br>
使用预认证，你需要做下面几个改变： <br>
1增加hive-exec*.jar到classpath中，（commons-configuration-1.6.jar and hadoop-core*.jar不需要） <br>
2增加auth=kerberos和kerberosAuthType=fromSubject的jdbc连接串的属性 <br>
3使用Subject.doAs()，打开连接。 <br>
下面是简单的代码使用，详情看<a href="https://issues.apache.org/jira/browse/HIVE-6486" rel="nofollow">hive-6486</a>和<a href="https://issues.apache.org/jira/secure/attachment/12633984/TestCase_HIVE-6486.java" rel="nofollow">test case</a>：</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">static</span> Connection getConnection( Subject signedOnUserSubject ) <span class="hljs-keyword">throws</span> Exception{
       Connection conn = (Connection) Subject.doAs(signedOnUserSubject, <span class="hljs-keyword">new</span> PrivilegedExceptionAction&lt;Object&gt;()
           {
               <span class="hljs-keyword">public</span> Object <span class="hljs-title">run</span>()
               {
                       Connection con = <span class="hljs-keyword">null</span>;
                       String JDBC_DB_URL = <span class="hljs-string">"jdbc:hive2://HiveHost:10000/default;"</span> ||
                                              <span class="hljs-string">"principal=hive/localhost.localdomain@EXAMPLE.COM;"</span> ||
                                              <span class="hljs-string">"kerberosAuthType=fromSubject"</span>;
                       <span class="hljs-keyword">try</span> {
                               Class.forName(JDBC_DRIVER);
                               con =  DriverManager.getConnection(JDBC_DB_URL);
                       } <span class="hljs-keyword">catch</span> (SQLException e) {
                               e.printStackTrace();
                       } <span class="hljs-keyword">catch</span> (ClassNotFoundException e) {
                               e.printStackTrace();
                       }
                       <span class="hljs-keyword">return</span> con;
               }
           });
       <span class="hljs-keyword">return</span> conn;
}</code></pre>

<p><strong>Python客户端</strong> <br>
python客户端的驱动在<a href="https://github.com/BradRuderman/pyhs2" rel="nofollow">github</a>，安装，请看<a href="https://cwiki.apache.org/confluence/display/Hive/Setting+Up+HiveServer2#SettingUpHiveServer2-PythonClientDriver" rel="nofollow">setting up hiverserver2:python client driver</a> <br>
<strong>Ruby客户端</strong> <br>
ruby的客户端驱动在github上，<a href="https://github.com/forward3d/rbhive" rel="nofollow">https://github.com/forward3d/rbhive</a> <br>
<strong>与SQuirrel Sql集成</strong> <br>
SQuirrel Sql是一个开源的java客户端工具，能够使用任意的jdbc连接数据库。 <br>
1下载，安装，运行SQuirrel SQL，<a href="http://squirrel-sql.sourceforge.net/" rel="nofollow">SQuirrel SQL website</a> <br>
输入driver的名字和url</p>



<pre class="prettyprint"><code class=" hljs haskell">
<span class="hljs-type">Name</span>: <span class="hljs-type">Hive</span>
<span class="hljs-type">Example</span> <span class="hljs-type">URL</span>: jdbc:hive2://localhost:<span class="hljs-number">10000</span>/<span class="hljs-default"><span class="hljs-keyword">default</span></span></code></pre>

<p>3选择extra class path-&gt;add来增加下面的jar包，在本地hive和分布式hadoop环境中。</p>



<pre class="prettyprint"><code class=" hljs axapta">HIVE_HOME/lib/hive-jdbc-*-standalone.jar
HADOOP_HOME/share/hadoop/<span class="hljs-keyword">common</span>/hadoop-<span class="hljs-keyword">common</span>-*.jar</code></pre>

<p>4选择listDriver，会让SQuirrel使用jdbc驱动解析你的jar包，可能会花几分钟。再class name中填入hive的驱动来连接hiveserver2</p>



<pre class="prettyprint"><code class=" hljs avrasm">org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hive</span><span class="hljs-preprocessor">.jdbc</span><span class="hljs-preprocessor">.HiveDriver</span></code></pre>

<p>5点击OK来完成驱动的注册 <br>
6选择Aliases-&gt;add alias来创建连接hiveserver2实列的别名 <br>
a.在Name方框中填入连接的别名 <br>
b.从”Driver”中,选择hive的驱动 <br>
c.修改url,连接hiveserver2实列 <br>
d.输入用户名和密码点击ok保存连接别名 <br>
e.双击别名，点击连接来连接上hiveserver2 <br>
当连接已经建立了，你将会在日志客户端看到错误。可能会有一个警告:the driver is not jdbc3.0兼容。这是因为jdbc的元数据apiyet-to-be-implemented ，这个可以忽略。为了测试连接，在命名行运行show tables. <br>
注意，当查询开始时，对于取消的支持还没有做。 <br>
<strong>与Dbvissoftware的Dbvisualizer集成</strong> <br>
Dbvisualizer是一个基于jdbc的数据库管理和分析的工具。 <br>
1下载，安装，运行免费的或者购买pro.<a href="https://www.dbvis.com/" rel="nofollow">https://www.dbvis.com/</a> <br>
2根据<a href="https://github.com/cyanfr/dbvis_to_hortonworks_hiveserver2/wiki/How-I-Connected-DBVisualizer-9.2.2-on-Windows-to-Hortonwork-HiveServer2" rel="nofollow">github</a>来做。 <br>
<strong>集成其他工具的高级特征</strong> <br>
下面这些特性，在hive1.2.0之后就支持了。 <br>
  <strong>在HTTP模式中支持Cookie的重置</strong> <br>
<a href="https://issues.apache.org/jira/browse/HIVE-9709" rel="nofollow">hive-9709</a>介绍了jdbc驱动的cookie缓存支持。 <br>
jdbc的连接url，下成下面这样是有效的。</p>

<pre class="prettyprint"><code class=" hljs xml">jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>/<span class="hljs-tag">&lt;<span class="hljs-title">db</span>&gt;</span>?transportMode=http;httpPath=<span class="hljs-tag">&lt;<span class="hljs-title">http_endpoint</span>&gt;</span>;cookieAuth=true;cookieName=<span class="hljs-tag">&lt;<span class="hljs-title">cookie_name</span>&gt;</span></code></pre>

<p>cookieAuth模式设为为true。 <br>
cookieName：如果安逸的cookies的key匹配上了cookieName，那么jdbc驱动不会发送任何登录凭证和kerberos ticket给服务器。这个客户端可以单独发送cookie给服务器认证。默认的cookieName是hive.server2.auth(这个是hiveserver2的cookie名字)。 <br>
为了运行cookie的replay，cookieAuth=false必须使用在url中。 <br>
<strong>在HTTP模式中可以使用双向的ssl</strong> <br>
<a href="https://issues.apache.org/jira/browse/HIVE-10447" rel="nofollow">hive-10447</a>能够使jdbc驱动，在hhtp模式中，支持双向的ssl。注意hiveserver2当前是不支持双向的ssl的。这个特性，在中间服务器中， 比如knox,且knox需要客户端支持双向的ssl，才是可用的。 <br>
jdbc，url</p>



<pre class="prettyprint"><code class=" hljs xml">jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>/<span class="hljs-tag">&lt;<span class="hljs-title">db</span>&gt;</span>;ssl=true;twoWay=true;sslTrustStore=<span class="hljs-tag">&lt;<span class="hljs-title">trust_store_path</span>&gt;</span>;trustStorePassword=<span class="hljs-tag">&lt;<span class="hljs-title">trust_store_password</span>&gt;</span>;sslKeyStore=<span class="hljs-tag">&lt;<span class="hljs-title">key_store_path</span>&gt;</span>;keyStorePassword=<span class="hljs-tag">&lt;<span class="hljs-title">key_store_password</span>&gt;</span>?transportMode=http;httpPath=<span class="hljs-tag">&lt;<span class="hljs-title">http_endpoint</span>&gt;</span></code></pre>

<p>trust_store_path:是客户端truststore文件的路径，不能为null。 <br>
trust_store_password：是访问truststore的密码。 <br>
key_store_path:是是客户端keystore文件的路径，不能为null。 <br>
key_store_password:是接入keystore的密码 <br>
<strong>通过jdbc的驱动，传输http头的key-value数据</strong> <br>
这个特性在hive1.2.0之后才有 <br>
<a href="https://issues.apache.org/jira/browse/HIVE-10339" rel="nofollow">hive-10339</a>介绍了一个客户端的选项，用来用户自定义http的header,头信息被发送到底层服务器上。 <br>
jdbc的url：</p>

<pre class="prettyprint"><code class=" hljs xml">jdbc:hive2://<span class="hljs-tag">&lt;<span class="hljs-title">host</span>&gt;</span>:<span class="hljs-tag">&lt;<span class="hljs-title">port</span>&gt;</span>/<span class="hljs-tag">&lt;<span class="hljs-title">db</span>&gt;</span>;transportMode=http;httpPath=<span class="hljs-tag">&lt;<span class="hljs-title">http_endpoint</span>&gt;</span>;http.header.<span class="hljs-tag">&lt;<span class="hljs-title">name1</span>&gt;</span>=<span class="hljs-tag">&lt;<span class="hljs-title">value1</span>&gt;</span>;http.header.<span class="hljs-tag">&lt;<span class="hljs-title">name2</span>&gt;</span>=<span class="hljs-tag">&lt;<span class="hljs-title">value2</span>&gt;</span></code></pre>

<p>在上面的url被指定，beeline将会调用底层的request来增加一个http的header，header中设置 name1,value1和name2,value2.当用户需要在http中发送验证信息时，这个功能是很有用的，比如通过beeline来验证的Knox。</p>

<pre class="prettyprint"><code class=" hljs xml">http.header.USERNAME=<span class="hljs-tag">&lt;<span class="hljs-title">value1</span>&gt;</span>;http.header.PASSWORD=<span class="hljs-tag">&lt;<span class="hljs-title">value2</span>&gt;</span></code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
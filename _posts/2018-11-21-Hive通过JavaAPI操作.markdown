---
layout:     post
title:      Hive通过JavaAPI操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_38799155/article/details/78319168				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Java 想要访问Hive，需要通过beeline的方式连接Hive，hiveserver2提供了一个新的命令行工具beeline，hiveserver2 对 之前的hive做了升级，功能更加强大，它增加了权限控制，要使用beeline需要先启动hiverserver2，再使用beeline连接</p>

<p>基于hadoop的Hive数据仓库JavaAPI简单调用的实例，关于Hive的简介在此不赘述。hive提供了三种用户接口：CLI，JDBC/ODBC和 WebUI</p>

<ol>
<li>CLI，即Shell命令行</li>
<li>JDBC/ODBC 是 Hive 的Java，与使用传统数据库JDBC的方式类似</li>
<li>WebGUI是通过浏览器访问 Hive <br>
本文主要介绍的就是第二种用户接口及Beeline的简单使用。</li>
</ol>



<h2 id="1beeline的使用"><strong>1.Beeline的使用</strong></h2>

<p>1.启动hiveserver2</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$ </span>hiveserver2</code></pre>

<p>2.启动beeline连接hive</p>



<pre class="prettyprint"><code class=" hljs lasso">$ beeline <span class="hljs-attribute">-u</span> jdbc:hive2:<span class="hljs-comment">//192.168.150.1:10000 -n hadoop -p</span></code></pre>

<p><img src="https://img-blog.csdn.net/20171023215232684?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzg3OTkxNTU=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><strong>参数的解释:</strong> <br>
-u：连接url，可以使用IP，也可以使用主机名，端口默认为10000  <br>
-n：连接的用户名（注：不是登录hive的用户名，是hive所在服务器登录用户名）  <br>
-p：密码，可以不用输入</p>

<p>可以使用如下命令来修改端口号(默认端口号是:10000):</p>



<pre class="prettyprint"><code class=" hljs avrasm">hiveserver2 --hiveconf hive<span class="hljs-preprocessor">.server</span>2<span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.port</span>=<span class="hljs-number">10001</span></code></pre>

<p>如果不知道beeline怎么使用，可以使用如下命令来查看beeline的使用帮助</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">$</span> <span class="hljs-comment">beeline</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">help</span></code></pre>

<p>具体如下:</p>



<pre class="prettyprint"><code class=" hljs haml">[hadoop@hadoop ~]$ beeline --help
Usage: java org.apache.hive.cli.beeline.BeeLine 
   -<span class="ruby">u &lt;database url&gt;               the <span class="hljs-constant">JDBC</span> <span class="hljs-constant">URL</span> to connect to
</span>   -<span class="ruby">r                              reconnect to last saved connect url (<span class="hljs-keyword">in</span> conjunction with !save)
</span>   -<span class="ruby">n &lt;username&gt;                   the username to connect as
</span>   -<span class="ruby">p &lt;password&gt;                   the password to connect as
</span>   -<span class="ruby">d &lt;driver <span class="hljs-class"><span class="hljs-keyword">class</span>&gt;               <span class="hljs-title">the</span> <span class="hljs-title">driver</span> <span class="hljs-title">class</span> <span class="hljs-title">to</span> <span class="hljs-title">use</span></span>
</span>   -<span class="ruby">i &lt;init file&gt;                  script file <span class="hljs-keyword">for</span> initialization
</span>   -<span class="ruby">e &lt;query&gt;                      query that should be executed
</span>   -<span class="ruby">f &lt;exec file&gt;                  script file that should be executed
</span>   -<span class="ruby">w (<span class="hljs-keyword">or</span>) --password-file &lt;password file&gt;  the password file to read password from
</span>   -<span class="ruby">-hiveconf property=value       <span class="hljs-constant">Use</span> value <span class="hljs-keyword">for</span> given property
</span>   -<span class="ruby">-hivevar name=value            hive variable name <span class="hljs-keyword">and</span> value
</span>                                   This is Hive specific settings in which variables
                                   can be set at session level and referenced in Hive
                                   commands or queries.
   -<span class="ruby">-property-file=&lt;property-file&gt; the file to read connection properties (url, driver, user, password) from
</span>   -<span class="ruby">-color=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]            control whether color is used <span class="hljs-keyword">for</span> display
</span>   -<span class="ruby">-showHeader=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]       show column names <span class="hljs-keyword">in</span> query results
</span>   -<span class="ruby">-headerInterval=<span class="hljs-constant">ROWS</span>;          the interval between which heades are displayed
</span>   -<span class="ruby">-fastConnect=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]      skip building table/column list <span class="hljs-keyword">for</span> tab-completion
</span>   -<span class="ruby">-autoCommit=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]       enable/disable automatic transaction commit
</span>   -<span class="ruby">-verbose=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]          show verbose error messages <span class="hljs-keyword">and</span> debug info
</span>   -<span class="ruby">-showWarnings=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]     display connection warnings
</span>   -<span class="ruby">-showDbInPrompt=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]   display the current database name <span class="hljs-keyword">in</span> the prompt
</span>   -<span class="ruby">-showNestedErrs=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]   display nested errors
</span>   -<span class="ruby">-numberFormat=[pattern]        format numbers using <span class="hljs-constant">DecimalFormat</span> pattern
</span>   -<span class="ruby">-force=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]            continue running script even after errors
</span>   -<span class="ruby">-maxWidth=<span class="hljs-constant">MAXWIDTH</span>             the maximum width of the terminal
</span>   -<span class="ruby">-maxColumnWidth=<span class="hljs-constant">MAXCOLWIDTH</span>    the maximum width to use <span class="hljs-keyword">when</span> displaying columns
</span>   -<span class="ruby">-silent=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]           be more silent
</span>   -<span class="ruby">-autosave=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]         automatically save preferences
</span>   -<span class="ruby">-outputformat=[table/vertical/csv2/tsv2/dsv/csv/tsv]  format mode <span class="hljs-keyword">for</span> result display
</span>                                   Note that csv, and tsv are deprecated - use csv2, tsv2 instead
   -<span class="ruby">-incremental=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]      <span class="hljs-constant">Defaults</span> to <span class="hljs-keyword">false</span>. <span class="hljs-constant">When</span> set to <span class="hljs-keyword">false</span>, the entire result set
</span>                                   is fetched and buffered before being displayed, yielding optimal
                                   display column sizing. When set to true, result rows are displayed
                                   immediately as they are fetched, yielding lower latency and
                                   memory usage at the price of extra display column padding.
                                   Setting --incremental=true is recommended if you encounter an OutOfMemory
                                   on the client side (due to the fetched result set size being large).
                                   Only applicable if --outputformat=table.
   -<span class="ruby">-incrementalBufferRows=<span class="hljs-constant">NUMROWS</span> the number of rows to buffer <span class="hljs-keyword">when</span> printing rows on stdout,
</span>                                   defaults to 1000; only applicable if --incremental=true
                                   and --outputformat=table
   -<span class="ruby">-truncateTable=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]    truncate table column <span class="hljs-keyword">when</span> it exceeds length
</span>   -<span class="ruby">-delimiterForDSV=<span class="hljs-constant">DELIMITER</span>     specify the delimiter <span class="hljs-keyword">for</span> delimiter-separated values output format (<span class="hljs-function">default: </span>|)
</span>   -<span class="ruby">-isolation=<span class="hljs-constant">LEVEL</span>               set the transaction isolation level
</span>   -<span class="ruby">-nullemptystring=[<span class="hljs-keyword">true</span>/<span class="hljs-keyword">false</span>]  set to <span class="hljs-keyword">true</span> to get historic behavior of printing null as empty string
</span>   -<span class="ruby">-maxHistoryRows=<span class="hljs-constant">MAXHISTORYROWS</span> <span class="hljs-constant">The</span> maximum number of rows to store beeline history.
</span>   -<span class="ruby">-help                          display this message
</span>
   Example:
    1. Connect using simple authentication to HiveServer2 on localhost:10000
    $ beeline -u jdbc:hive2://localhost:10000 username password

    2. Connect using simple authentication to HiveServer2 on hs.local:10000 using -n for username and -p for password
    $ beeline -n username -p password -u jdbc:hive2://hs2.local:10012

    3. Connect using Kerberos authentication with hive/localhost@mydomain.com as HiveServer2 principal
    $ beeline -u "jdbc:hive2://hs2.local:10013/default;principal=hive/localhost@mydomain.com"

    4. Connect using SSL connection to HiveServer2 on localhost at 10000
    $ beeline "jdbc:hive2://localhost:10000/default;ssl=true;sslTrustStore=/usr/local/truststore;trustStorePassword=mytruststorepassword"

    5. Connect using LDAP authentication
    $ beeline -u jdbc:hive2://hs2.local:10013/default &lt;ldap-username&gt; &lt;ldap-password&gt;
</code></pre>

<p>如果使用beeline连接时报了如下错:</p>



<pre class="prettyprint"><code class=" hljs vbnet">hadoop <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> allowed <span class="hljs-keyword">to</span> impersonate hadoop (state=<span class="hljs-number">08</span>S01,code=<span class="hljs-number">0</span>)</code></pre>

<p>原因：hiveserver2增加了权限控制，需要在hadoop的配置文件中配置  <br>
解决方法：在hadoop的<code>core-site.xml</code>中添加如下内容，然后重启hadoop，再使用beeline连接即可</p>

<p>参考官网： <a href="https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/Superusers.html" rel="nofollow" target="_blank">https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/Superusers.html</a></p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.proxyuser.hadoop.hosts<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.proxyuser.hadoop.groups<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>连接成功后，和执行hive后相同执行shell命令即可，如果想要退出连接使用 !q 或 !quit 命令</p>



<h2 id="2java-api-操作-hive-的例子"><strong>2.Java API 操作 Hive 的例子</strong></h2>

<p>用idea工具创建一个maven项目，pom.xml文件配置如下:</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>com.bigdata.hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hive<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hive<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hive-jdbc<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>2.3.0<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>4.9<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">build</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">plugins</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.maven.plugins<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>maven-compiler-plugin<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>3.5.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">source</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">source</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">target</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">target</span>&gt;</span>
                <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">plugins</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">build</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>

<p>创建测试类HiveJDBC，代码如下  <br>
官网参考：<a href="https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients" rel="nofollow" target="_blank">https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients</a></p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.bigdata.hadoop.hive;

<span class="hljs-keyword">import</span> org.junit.After;
<span class="hljs-keyword">import</span> org.junit.Before;
<span class="hljs-keyword">import</span> org.junit.Test;

<span class="hljs-keyword">import</span> java.sql.*;

<span class="hljs-javadoc">/**
 * JDBC 操作 Hive（注：JDBC 访问 Hive 前需要先启动HiveServer2）
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HiveJDBC</span> {</span>

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String driverName = <span class="hljs-string">"org.apache.hive.jdbc.HiveDriver"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String url = <span class="hljs-string">"jdbc:hive2://hdpcomprs:10000/db_comprs"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String user = <span class="hljs-string">"hadoop"</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String password = <span class="hljs-string">""</span>;

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Connection conn = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Statement stmt = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> ResultSet rs = <span class="hljs-keyword">null</span>;

    <span class="hljs-comment">// 加载驱动、创建连接</span>
    <span class="hljs-annotation">@Before</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span>() <span class="hljs-keyword">throws</span> Exception {
        Class.forName(driverName);
        conn = DriverManager.getConnection(url,user,password);
        stmt = conn.createStatement();
    }

    <span class="hljs-comment">// 创建数据库</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createDatabase</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"create database hive_jdbc_test"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        stmt.execute(sql);
    }

    <span class="hljs-comment">// 查询所有数据库</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">showDatabases</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"show databases"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        rs = stmt.executeQuery(sql);
        <span class="hljs-keyword">while</span> (rs.next()) {
            System.out.println(rs.getString(<span class="hljs-number">1</span>));
        }
    }

    <span class="hljs-comment">// 创建表</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createTable</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"create table emp(\n"</span> +
                        <span class="hljs-string">"empno int,\n"</span> +
                        <span class="hljs-string">"ename string,\n"</span> +
                        <span class="hljs-string">"job string,\n"</span> +
                        <span class="hljs-string">"mgr int,\n"</span> +
                        <span class="hljs-string">"hiredate string,\n"</span> +
                        <span class="hljs-string">"sal double,\n"</span> +
                        <span class="hljs-string">"comm double,\n"</span> +
                        <span class="hljs-string">"deptno int\n"</span> +
                        <span class="hljs-string">")\n"</span> +
                     <span class="hljs-string">"row format delimited fields terminated by '\\t'"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        stmt.execute(sql);
    }

    <span class="hljs-comment">// 查询所有表</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">showTables</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"show tables"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        rs = stmt.executeQuery(sql);
        <span class="hljs-keyword">while</span> (rs.next()) {
            System.out.println(rs.getString(<span class="hljs-number">1</span>));
        }
    }

    <span class="hljs-comment">// 查看表结构</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">descTable</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"desc emp"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        rs = stmt.executeQuery(sql);
        <span class="hljs-keyword">while</span> (rs.next()) {
            System.out.println(rs.getString(<span class="hljs-number">1</span>) + <span class="hljs-string">"\t"</span> + rs.getString(<span class="hljs-number">2</span>));
        }
    }

    <span class="hljs-comment">// 加载数据</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">loadData</span>() <span class="hljs-keyword">throws</span> Exception {
        String filePath = <span class="hljs-string">"/home/hadoop/data/emp.txt"</span>;
        String sql = <span class="hljs-string">"load data local inpath '"</span> + filePath + <span class="hljs-string">"' overwrite into table emp"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        stmt.execute(sql);
    }

    <span class="hljs-comment">// 查询数据</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">selectData</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"select * from emp"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        rs = stmt.executeQuery(sql);
        System.out.println(<span class="hljs-string">"员工编号"</span> + <span class="hljs-string">"\t"</span> + <span class="hljs-string">"员工姓名"</span> + <span class="hljs-string">"\t"</span> + <span class="hljs-string">"工作岗位"</span>);
        <span class="hljs-keyword">while</span> (rs.next()) {
            System.out.println(rs.getString(<span class="hljs-string">"empno"</span>) + <span class="hljs-string">"\t\t"</span> + rs.getString(<span class="hljs-string">"ename"</span>) + <span class="hljs-string">"\t\t"</span> + rs.getString(<span class="hljs-string">"job"</span>));
        }
    }

    <span class="hljs-comment">// 统计查询（会运行mapreduce作业）</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">countData</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"select count(1) from emp"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        rs = stmt.executeQuery(sql);
        <span class="hljs-keyword">while</span> (rs.next()) {
            System.out.println(rs.getInt(<span class="hljs-number">1</span>) );
        }
    }

    <span class="hljs-comment">// 删除数据库</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">dropDatabase</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"drop database if exists hive_jdbc_test"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        stmt.execute(sql);
    }

    <span class="hljs-comment">// 删除数据库表</span>
    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deopTable</span>() <span class="hljs-keyword">throws</span> Exception {
        String sql = <span class="hljs-string">"drop table if exists emp"</span>;
        System.out.println(<span class="hljs-string">"Running: "</span> + sql);
        stmt.execute(sql);
    }

    <span class="hljs-comment">// 释放资源</span>
    <span class="hljs-annotation">@After</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">destory</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">if</span> ( rs != <span class="hljs-keyword">null</span>) {
            rs.close();
        }
        <span class="hljs-keyword">if</span> (stmt != <span class="hljs-keyword">null</span>) {
            stmt.close();
        }
        <span class="hljs-keyword">if</span> (conn != <span class="hljs-keyword">null</span>) {
            conn.close();
        }
    }
}</code></pre>

<p><strong>推荐博客:</strong> <br>
[Hive]HiveServer2配置详解: <br>
<a href="http://m.blog.csdn.net/SunnyYoona/article/details/75322224" rel="nofollow">http://m.blog.csdn.net/SunnyYoona/article/details/75322224</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
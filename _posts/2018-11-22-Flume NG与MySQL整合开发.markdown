---
layout:     post
title:      Flume NG与MySQL整合开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011462328/article/details/51151721				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1 pom文件配置</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-configuration<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.5.2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-core<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.5.2<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>mysql<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>mysql-connector-java<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>5.1.26<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>com.alibaba<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>druid<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0.27<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span></code></pre>

<p>2 java代码实现</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> cn.gcks;

<span class="hljs-keyword">import</span> java.sql.Connection;
<span class="hljs-keyword">import</span> java.sql.DriverManager;
<span class="hljs-keyword">import</span> java.sql.PreparedStatement;
<span class="hljs-keyword">import</span> java.sql.SQLException;
<span class="hljs-keyword">import</span> java.util.List;
<span class="hljs-keyword">import</span> java.util.regex.Pattern;

<span class="hljs-keyword">import</span> org.apache.flume.Channel;
<span class="hljs-keyword">import</span> org.apache.flume.Context;
<span class="hljs-keyword">import</span> org.apache.flume.Event;
<span class="hljs-keyword">import</span> org.apache.flume.EventDeliveryException;
<span class="hljs-keyword">import</span> org.apache.flume.Transaction;
<span class="hljs-keyword">import</span> org.apache.flume.conf.Configurable;
<span class="hljs-keyword">import</span> org.apache.flume.sink.AbstractSink;
<span class="hljs-keyword">import</span> org.slf4j.Logger;
<span class="hljs-keyword">import</span> org.slf4j.LoggerFactory;

<span class="hljs-keyword">import</span> com.google.common.base.Preconditions;
<span class="hljs-keyword">import</span> com.google.common.base.Throwables;
<span class="hljs-keyword">import</span> com.google.common.collect.Lists;

<span class="hljs-javadoc">/**
 * Flume收集文件到MySQL
 * 
 *<span class="hljs-javadoctag"> @author</span> 林志明
 *
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MySQLSink</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">AbstractSink</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Configurable</span> {</span>

    <span class="hljs-keyword">private</span> Logger logger = LoggerFactory.getLogger(MySQLSink.class);
    <span class="hljs-keyword">private</span> String hostname;
    <span class="hljs-keyword">private</span> String port;
    <span class="hljs-keyword">private</span> String databaseName;
    <span class="hljs-keyword">private</span> String tableName;
    <span class="hljs-keyword">private</span> String user;
    <span class="hljs-keyword">private</span> String password;
    <span class="hljs-keyword">private</span> PreparedStatement preparedStatement;
    <span class="hljs-keyword">private</span> Connection conn;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> batchSize;
    <span class="hljs-keyword">private</span> String columnName;
    <span class="hljs-keyword">private</span> String fieldSeparator;
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">int</span> columnNumber;

    <span class="hljs-keyword">public</span> <span class="hljs-title">MySQLSink</span>() {
        logger.info(<span class="hljs-string">"MysqlSink start..."</span>);
    }

    Pattern pattern = <span class="hljs-keyword">null</span>;

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configure</span>(Context context) {
        hostname = context.getString(<span class="hljs-string">"hostname"</span>);
        Preconditions.checkNotNull(hostname, <span class="hljs-string">"hostname must be set!!"</span>);
        port = context.getString(<span class="hljs-string">"port"</span>);
        Preconditions.checkNotNull(port, <span class="hljs-string">"port must be set!!"</span>);
        databaseName = context.getString(<span class="hljs-string">"databaseName"</span>);
        Preconditions.checkNotNull(databaseName, <span class="hljs-string">"databaseName must be set!!"</span>);
        tableName = context.getString(<span class="hljs-string">"tableName"</span>);
        Preconditions.checkNotNull(tableName, <span class="hljs-string">"tableName must be set!!"</span>);
        user = context.getString(<span class="hljs-string">"user"</span>);
        Preconditions.checkNotNull(user, <span class="hljs-string">"user must be set!!"</span>);
        password = context.getString(<span class="hljs-string">"password"</span>);
        Preconditions.checkNotNull(password, <span class="hljs-string">"password must be set!!"</span>);
        batchSize = context.getInteger(<span class="hljs-string">"batchSize"</span>, <span class="hljs-number">100</span>);
        Preconditions.checkNotNull(batchSize &gt; <span class="hljs-number">0</span>, <span class="hljs-string">"batchSize must be a positive number!!"</span>);
        columnName = context.getString(<span class="hljs-string">"column_name"</span>);
        Preconditions.checkNotNull(columnName, <span class="hljs-string">"column_name must be set!!"</span>);
        fieldSeparator = context.getString(<span class="hljs-string">"field_separator"</span>);
        Preconditions.checkNotNull(fieldSeparator, <span class="hljs-string">"field_separator must be set!!"</span>);
        pattern = Pattern.compile(fieldSeparator);
    }

    <span class="hljs-keyword">private</span> String sql = <span class="hljs-keyword">null</span>;

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">start</span>() {
        <span class="hljs-keyword">super</span>.start();

        String url = CommonUtils.append(<span class="hljs-string">"jdbc:mysql://"</span>, hostname, <span class="hljs-string">":"</span>, port, <span class="hljs-string">"/"</span>, databaseName,
                <span class="hljs-string">"?Unicode=true&amp;characterEncoding=UTF-8"</span>);

        <span class="hljs-keyword">try</span> {
            conn = DataSourceFactory.getConnection(url, user, password);
            conn.setAutoCommit(<span class="hljs-keyword">false</span>);
            <span class="hljs-comment">// 创建一个Statement对象</span>
            String[] columns = columnName.split(<span class="hljs-string">","</span>);
            String values = <span class="hljs-keyword">null</span>;
            StringBuffer stringBuffer = <span class="hljs-keyword">new</span> StringBuffer();
            <span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>;
            columnNumber = columns.length;
            <span class="hljs-keyword">while</span> (i &lt; columnNumber) {
                stringBuffer.append(<span class="hljs-string">"?"</span>).append(<span class="hljs-string">","</span>);
                i++;
            }
            values = stringBuffer.substring(<span class="hljs-number">0</span>, stringBuffer.length() - <span class="hljs-number">1</span>).toString();
            sql = <span class="hljs-string">"insert into "</span> + tableName + <span class="hljs-string">" ("</span> + columnName + <span class="hljs-string">") values ("</span> + values + <span class="hljs-string">")"</span>;
        } <span class="hljs-keyword">catch</span> (SQLException e) {
            e.printStackTrace();
            System.exit(<span class="hljs-number">1</span>);
        }

    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">stop</span>() {
        <span class="hljs-keyword">super</span>.stop();
        DataSourceFactory.closeCon(preparedStatement, conn);
    }

    <span class="hljs-annotation">@Override</span>
    <span class="hljs-keyword">public</span> Status <span class="hljs-title">process</span>() <span class="hljs-keyword">throws</span> EventDeliveryException {
        <span class="hljs-keyword">try</span> {
            preparedStatement = conn.prepareStatement(sql);
            logger.info(<span class="hljs-string">"===preparedStatement Alive==="</span>);
        } <span class="hljs-keyword">catch</span> (SQLException e1) {
            e1.printStackTrace();
        }
        Status result = Status.READY;
        Channel channel = getChannel();
        Transaction transaction = channel.getTransaction();
        Event event;
        String content;

        List&lt;String&gt; actions = Lists.newArrayList();
        transaction.begin();
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; batchSize; i++) {
                event = channel.take();
                <span class="hljs-keyword">if</span> (event != <span class="hljs-keyword">null</span>) {
                    content = <span class="hljs-keyword">new</span> String(event.getBody());
                    actions.add(content);
                } <span class="hljs-keyword">else</span> {
                    result = Status.BACKOFF;
                    <span class="hljs-keyword">break</span>;
                }
            }

            <span class="hljs-keyword">if</span> (actions.size() &gt; <span class="hljs-number">0</span>) {
                preparedStatement.clearBatch();
                <span class="hljs-keyword">for</span> (String temp : actions) {
                    String[] datas = {};
                    <span class="hljs-keyword">if</span> (temp != <span class="hljs-keyword">null</span> &amp;&amp; temp.trim().length() != <span class="hljs-number">0</span>) {
                        datas = pattern.split(temp);
                    }
                    <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; columnNumber; i++) {
                        <span class="hljs-keyword">if</span> (i &gt; datas.length - <span class="hljs-number">1</span>) {
                            preparedStatement.setString(i + <span class="hljs-number">1</span>, <span class="hljs-keyword">null</span>);
                        } <span class="hljs-keyword">else</span> {
                            preparedStatement.setString(i + <span class="hljs-number">1</span>, datas[i]);
                        }
                    }
                    preparedStatement.addBatch();
                }
                preparedStatement.executeBatch();

                conn.commit();
            }
            transaction.commit();
        } <span class="hljs-keyword">catch</span> (Throwable e) {
            <span class="hljs-keyword">try</span> {
                transaction.rollback();
            } <span class="hljs-keyword">catch</span> (Exception e2) {
                logger.error(<span class="hljs-string">"Exception in rollback. Rollback might not have been"</span> + <span class="hljs-string">"successful."</span>, e2);
            }
            logger.error(<span class="hljs-string">"Failed to commit transaction."</span> + <span class="hljs-string">"Transaction rolled back."</span>, e);
            Throwables.propagate(e);
        } <span class="hljs-keyword">finally</span> {
            transaction.close();
        }

        <span class="hljs-keyword">return</span> result;
    }
}</code></pre>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> cn.gcks;

<span class="hljs-keyword">import</span> java.sql.Connection;
<span class="hljs-keyword">import</span> java.sql.PreparedStatement;
<span class="hljs-keyword">import</span> java.sql.ResultSet;

<span class="hljs-keyword">import</span> org.apache.log4j.Logger;

<span class="hljs-keyword">import</span> com.alibaba.druid.pool.DruidDataSource;

<span class="hljs-javadoc">/**
 * 数据库连接
 * 
 *<span class="hljs-javadoctag"> @author</span> linzm
 *
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">DataSourceFactory</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Logger log = Logger.getLogger(DataSourceFactory.class);
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> DruidDataSource dataSource = <span class="hljs-keyword">null</span>;

    <span class="hljs-javadoc">/**
     * 创建数据源
     * 
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> DruidDataSource <span class="hljs-title">getDataSource</span>(String url, String userName, String password) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">if</span> (dataSource == <span class="hljs-keyword">null</span>) {
            log.info(<span class="hljs-string">"数据库连接信息：[url:"</span> + url + <span class="hljs-string">",userName:"</span> + userName + <span class="hljs-string">",password:"</span> + password + <span class="hljs-string">"]"</span>);
            dataSource = <span class="hljs-keyword">new</span> DruidDataSource();
            dataSource.setUrl(url);
            dataSource.setUsername(userName);
            dataSource.setPassword(password);
            dataSource.setMaxActive(<span class="hljs-number">15</span>);<span class="hljs-comment">// 设置最大并发数</span>
            dataSource.setInitialSize(<span class="hljs-number">2</span>);<span class="hljs-comment">// 数据库初始化时，创建的连接个数</span>
            dataSource.setMaxWait(<span class="hljs-number">60000</span>);
            dataSource.setMinIdle(<span class="hljs-number">1</span>);<span class="hljs-comment">// 最小空闲连接数</span>
            dataSource.setTimeBetweenEvictionRunsMillis(<span class="hljs-number">5</span> * <span class="hljs-number">60</span> * <span class="hljs-number">1000</span>);<span class="hljs-comment">// 5分钟检测一次是否有死掉的线程</span>
            dataSource.setMinEvictableIdleTimeMillis(<span class="hljs-number">300000</span>);<span class="hljs-comment">// 空闲连接60秒中后释放</span>
            dataSource.setTestWhileIdle(<span class="hljs-keyword">true</span>);
            <span class="hljs-comment">// 检测连接有效性</span>
            dataSource.setTestOnBorrow(<span class="hljs-keyword">true</span>);
            dataSource.setValidationQuery(<span class="hljs-string">"select 1"</span>);
            dataSource.setPoolPreparedStatements(<span class="hljs-keyword">true</span>);
            dataSource.setMaxOpenPreparedStatements(<span class="hljs-number">15</span>);
        }
        <span class="hljs-keyword">return</span> dataSource;
    }

    <span class="hljs-javadoc">/**
     * 释放数据源
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">shutDownDataSource</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-keyword">if</span> (dataSource != <span class="hljs-keyword">null</span>) {
            dataSource.close();
        }
    }

    <span class="hljs-javadoc">/**
     * 获取数据库连接
     * 
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> Connection <span class="hljs-title">getConnection</span>(String url, String userName, String password) {
        Connection con = <span class="hljs-keyword">null</span>;
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">if</span> (dataSource != <span class="hljs-keyword">null</span>) {
                con = dataSource.getConnection();
            } <span class="hljs-keyword">else</span> {
                con = getDataSource(url, userName, password).getConnection();
            }
        } <span class="hljs-keyword">catch</span> (Exception e) {
            log.error(e.getMessage(), e);
        }
        <span class="hljs-keyword">return</span> con;
    }

    <span class="hljs-javadoc">/**
     * 关闭连接
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">closeCon</span>(PreparedStatement ps, Connection con) {
        <span class="hljs-keyword">if</span> (ps != <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">try</span> {
                ps.close();
            } <span class="hljs-keyword">catch</span> (Exception e) {
                log.error(<span class="hljs-string">"预编译SQL语句对象PreparedStatement关闭异常！"</span> + e.getMessage(), e);
            }
        }
        <span class="hljs-keyword">if</span> (con != <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">try</span> {
                con.close();
            } <span class="hljs-keyword">catch</span> (Exception e) {
                log.error(<span class="hljs-string">"关闭连接对象Connection异常！"</span> + e.getMessage(), e);
            }
        }
    }
}</code></pre>



<pre class="prettyprint"><code class=" hljs coffeescript">package cn.gcks;

<span class="hljs-reserved">import</span> java.text.ParseException;
<span class="hljs-reserved">import</span> java.text.SimpleDateFormat;
<span class="hljs-reserved">import</span> java.util.Date;
<span class="hljs-reserved">import</span> java.util.Locale;
<span class="hljs-reserved">import</span> java.util.regex.Matcher;
<span class="hljs-reserved">import</span> java.util.regex.Pattern;

public <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">CommonUtils</span> {</span>

    public static final String EMPTY = <span class="hljs-string">""</span>;

    /**
     * Represents a failed index search.
     *
     * <span class="hljs-property">@since</span> <span class="hljs-number">2.1</span>
     */
    public static final int INDEX_NOT_FOUND = -<span class="hljs-number">1</span>;

    /**
     * &lt;p&gt;
     * The maximum size to which the padding constant(s) can expand.
     * &lt;/p&gt;
     */
    <span class="hljs-regexp">//</span> private static final int PAD_LIMIT = <span class="hljs-number">8192</span>;

    /**
     * &lt;p&gt;
     * Checks <span class="hljs-keyword">if</span> a String <span class="hljs-keyword">is</span> whitespace, empty (<span class="hljs-string">""</span>) <span class="hljs-keyword">or</span> <span class="hljs-literal">null</span>.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.isBlank(<span class="hljs-literal">null</span>)      = <span class="hljs-literal">true</span>
     * StringUtils.isBlank(<span class="hljs-string">""</span>)        = <span class="hljs-literal">true</span>
     * StringUtils.isBlank(<span class="hljs-string">" "</span>)       = <span class="hljs-literal">true</span>
     * StringUtils.isBlank(<span class="hljs-string">"bob"</span>)     = <span class="hljs-literal">false</span>
     * StringUtils.isBlank(<span class="hljs-string">"  bob  "</span>) = <span class="hljs-literal">false</span>
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> str the String to check, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@return</span> &lt;code&gt;<span class="hljs-literal">true</span>&lt;/code&gt; <span class="hljs-keyword">if</span> the String <span class="hljs-keyword">is</span> <span class="hljs-literal">null</span>, empty <span class="hljs-keyword">or</span> whitespace
     * <span class="hljs-property">@since</span> <span class="hljs-number">2.0</span>
     */
    public static boolean isBlank(String str) {
        int strLen;
        <span class="hljs-keyword">if</span> (str == <span class="hljs-literal">null</span> || (strLen = str.length()) == <span class="hljs-number">0</span>) {
            <span class="hljs-keyword">return</span> <span class="hljs-literal">true</span>;
        }
        <span class="hljs-keyword">for</span> (int i = <span class="hljs-number">0</span>; i &lt; strLen; i++) {
            <span class="hljs-keyword">if</span> ((Character.isWhitespace(str.charAt(i)) == <span class="hljs-literal">false</span>)) {
                <span class="hljs-keyword">return</span> <span class="hljs-literal">false</span>;
            }
        }
        <span class="hljs-keyword">return</span> <span class="hljs-literal">true</span>;
    }

    /**
     * &lt;p&gt;
     * Checks <span class="hljs-keyword">if</span> a String <span class="hljs-keyword">is</span> whitespace, empty (<span class="hljs-string">""</span>) <span class="hljs-keyword">or</span> <span class="hljs-literal">null</span>.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.isBlank(<span class="hljs-literal">null</span>)      = <span class="hljs-literal">true</span>
     * StringUtils.isBlank(<span class="hljs-string">""</span>)        = <span class="hljs-literal">true</span>
     * StringUtils.isBlank(<span class="hljs-string">" "</span>)       = <span class="hljs-literal">true</span>
     * StringUtils.isBlank(<span class="hljs-string">"bob"</span>)     = <span class="hljs-literal">false</span>
     * StringUtils.isBlank(<span class="hljs-string">"  bob  "</span>) = <span class="hljs-literal">false</span>
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> str the String to check, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@return</span> &lt;code&gt;<span class="hljs-literal">true</span>&lt;/code&gt; <span class="hljs-keyword">if</span> the String <span class="hljs-keyword">is</span> <span class="hljs-literal">null</span>, empty <span class="hljs-keyword">or</span> whitespace
     * <span class="hljs-property">@since</span> <span class="hljs-number">2.0</span>
     */
    public static boolean isNotBlank(String str) {
        <span class="hljs-keyword">return</span> !isBlank(str);
    }

    public static Long parseLong(String str) {
        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">return</span> Long.parseLong(str);
        } <span class="hljs-keyword">catch</span> (Exception ex) {
            <span class="hljs-keyword">return</span> <span class="hljs-number">0</span>L;
        }
    }

    public static String toCNDateTimeString(Date date) {
        SimpleDateFormat sf = <span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyyy-MM-dd HH:mm:ss"</span>);
        <span class="hljs-keyword">return</span> sf.format(date);
    }

    public static int strLengthContainBlank(String str) {
        <span class="hljs-keyword">return</span> isBlank(str) ? <span class="hljs-number">0</span> : str.length();
    }

    /**
     * &lt;p&gt;
     * Gets a substring from the specified String avoiding exceptions.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A negative start position can be used to start/end &lt;code&gt;n&lt;/code&gt;
     * characters from the end <span class="hljs-keyword">of</span> the String.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * The returned substring starts <span class="hljs-reserved">with</span> the character <span class="hljs-keyword">in</span> the
     * &lt;code&gt;start&lt;/code&gt; position <span class="hljs-keyword">and</span> ends before the &lt;code&gt;end&lt;/code&gt;
     * position. All position counting <span class="hljs-keyword">is</span> zero-based -- i.e., to start at the
     * beginning <span class="hljs-keyword">of</span> the string use &lt;code&gt;start = <span class="hljs-number">0</span>&lt;/code&gt;. Negative start <span class="hljs-keyword">and</span>
     * end positions can be used to specify offsets relative to the end <span class="hljs-keyword">of</span> the
     * String.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * If &lt;code&gt;start&lt;/code&gt; <span class="hljs-keyword">is</span> <span class="hljs-keyword">not</span> strictly to the left <span class="hljs-keyword">of</span> &lt;code&gt;end&lt;/code&gt;, <span class="hljs-string">""</span>
     * <span class="hljs-keyword">is</span> returned.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.substring(<span class="hljs-literal">null</span>, *, *)    = <span class="hljs-literal">null</span>
     * StringUtils.substring(<span class="hljs-string">""</span>, * ,  *)    = <span class="hljs-string">""</span>;
     * StringUtils.substring(<span class="hljs-string">"abc"</span>, <span class="hljs-number">0</span>, <span class="hljs-number">2</span>)   = <span class="hljs-string">"ab"</span>
     * StringUtils.substring(<span class="hljs-string">"abc"</span>, <span class="hljs-number">2</span>, <span class="hljs-number">0</span>)   = <span class="hljs-string">""</span>
     * StringUtils.substring(<span class="hljs-string">"abc"</span>, <span class="hljs-number">2</span>, <span class="hljs-number">4</span>)   = <span class="hljs-string">"c"</span>
     * StringUtils.substring(<span class="hljs-string">"abc"</span>, <span class="hljs-number">4</span>, <span class="hljs-number">6</span>)   = <span class="hljs-string">""</span>
     * StringUtils.substring(<span class="hljs-string">"abc"</span>, <span class="hljs-number">2</span>, <span class="hljs-number">2</span>)   = <span class="hljs-string">""</span>
     * StringUtils.substring(<span class="hljs-string">"abc"</span>, -<span class="hljs-number">2</span>, -<span class="hljs-number">1</span>) = <span class="hljs-string">"b"</span>
     * StringUtils.substring(<span class="hljs-string">"abc"</span>, -<span class="hljs-number">4</span>, <span class="hljs-number">2</span>)  = <span class="hljs-string">"ab"</span>
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> str   the String to get the substring from, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@param</span> start the position to start from, negative means count back from the
     *              end <span class="hljs-keyword">of</span> the String <span class="hljs-keyword">by</span> <span class="hljs-keyword">this</span> many characters
     * <span class="hljs-property">@param</span> end   the position to end at (exclusive), negative means count back
     *              from the end <span class="hljs-keyword">of</span> the String <span class="hljs-keyword">by</span> <span class="hljs-keyword">this</span> many characters
     * <span class="hljs-property">@return</span> substring from start position to end positon, &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;
     * <span class="hljs-keyword">if</span> <span class="hljs-literal">null</span> String input
     */
    public static String substring(String str, int start, int end) {
        <span class="hljs-keyword">if</span> (str == <span class="hljs-literal">null</span>) {
            <span class="hljs-keyword">return</span> <span class="hljs-literal">null</span>;
        }

        <span class="hljs-regexp">//</span> handle negatives
        <span class="hljs-keyword">if</span> (end &lt; <span class="hljs-number">0</span>) {
            end = str.length() + end; <span class="hljs-regexp">//</span> remember end <span class="hljs-keyword">is</span> negative
        }
        <span class="hljs-keyword">if</span> (start &lt; <span class="hljs-number">0</span>) {
            start = str.length() + start; <span class="hljs-regexp">//</span> remember start <span class="hljs-keyword">is</span> negative
        }

        <span class="hljs-regexp">//</span> check length next
        <span class="hljs-keyword">if</span> (end &gt; str.length()) {
            end = str.length();
        }

        <span class="hljs-regexp">//</span> <span class="hljs-keyword">if</span> start <span class="hljs-keyword">is</span> greater than end, <span class="hljs-keyword">return</span> <span class="hljs-string">""</span>
        <span class="hljs-keyword">if</span> (start &gt; end) {
            <span class="hljs-keyword">return</span> EMPTY;
        }

        <span class="hljs-keyword">if</span> (start &lt; <span class="hljs-number">0</span>) {
            start = <span class="hljs-number">0</span>;
        }
        <span class="hljs-keyword">if</span> (end &lt; <span class="hljs-number">0</span>) {
            end = <span class="hljs-number">0</span>;
        }

        <span class="hljs-keyword">return</span> str.substring(start, end);
    }

    public static String append(String... strings) {
        StringBuffer stringBuffer = <span class="hljs-keyword">new</span> StringBuffer();
        <span class="hljs-keyword">for</span> (String string : strings) {
            stringBuffer.append(string);
        }
        <span class="hljs-keyword">return</span> stringBuffer.toString();
    }

    public static String appendWithOperator(String operator, Object... objs) {
        StringBuffer stringBuffer = <span class="hljs-keyword">new</span> StringBuffer();
        <span class="hljs-keyword">for</span> (Object obj : objs) {
            <span class="hljs-keyword">if</span> (obj <span class="hljs-keyword">instanceof</span> Object[]) {
                Object[] arr = (Object[]) obj;
                <span class="hljs-keyword">for</span> (Object obj2 : arr) {
                    stringBuffer.append(obj2).append(operator);
                }

            } <span class="hljs-keyword">else</span> {
                stringBuffer.append(obj).append(operator);
            }
        }
        <span class="hljs-keyword">return</span> substring(stringBuffer.toString(), <span class="hljs-number">0</span>, -<span class="hljs-number">1</span>);
    }

    public static String appendWithOperatorReplaceString(String operator, String nullReplaceString, Object... objs) {
        StringBuffer stringBuffer = <span class="hljs-keyword">new</span> StringBuffer();
        <span class="hljs-keyword">for</span> (Object obj : objs) {
            <span class="hljs-keyword">if</span> (obj != <span class="hljs-literal">null</span> &amp;&amp; isBlank(obj.toString())) {
                obj = nullReplaceString;
            }

            <span class="hljs-keyword">if</span> (obj <span class="hljs-keyword">instanceof</span> Object[]) {
                Object[] arr = (Object[]) obj;
                <span class="hljs-keyword">for</span> (Object obj2 : arr) {
                    <span class="hljs-keyword">if</span> (obj2 == <span class="hljs-literal">null</span>) {
                        obj2 = nullReplaceString;
                    }
                    stringBuffer.append(obj2).append(operator);
                }

            } <span class="hljs-keyword">else</span> {
                stringBuffer.append(obj).append(operator);
            }
        }
        <span class="hljs-keyword">return</span> substring(stringBuffer.toString(), <span class="hljs-number">0</span>, -<span class="hljs-number">1</span>);
    }

    public static boolean equals(String str1, String str2) {
        <span class="hljs-keyword">return</span> str1 == <span class="hljs-literal">null</span> ? str2 == <span class="hljs-literal">null</span> : str1.equals(str2);
    }

    public static boolean notEquals(String str1, String str2) {
        <span class="hljs-keyword">return</span> !(str1 == <span class="hljs-literal">null</span> ? str2 == <span class="hljs-literal">null</span> : str1.equals(str2));
    }

    /**
     * &lt;p&gt;
     * Checks <span class="hljs-keyword">if</span> String contains a search String, handling &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;.
     * This method uses {<span class="hljs-property">@link</span> String<span class="hljs-comment">#indexOf(String)}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; String will <span class="hljs-keyword">return</span> &lt;code&gt;<span class="hljs-literal">false</span>&lt;/code&gt;.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.contains(<span class="hljs-literal">null</span>, *)     = <span class="hljs-literal">false</span>
     * StringUtils.contains(*, <span class="hljs-literal">null</span>)     = <span class="hljs-literal">false</span>
     * StringUtils.contains(<span class="hljs-string">""</span>, <span class="hljs-string">""</span>)      = <span class="hljs-literal">true</span>
     * StringUtils.contains(<span class="hljs-string">"abc"</span>, <span class="hljs-string">""</span>)   = <span class="hljs-literal">true</span>
     * StringUtils.contains(<span class="hljs-string">"abc"</span>, <span class="hljs-string">"a"</span>)  = <span class="hljs-literal">true</span>
     * StringUtils.contains(<span class="hljs-string">"abc"</span>, <span class="hljs-string">"z"</span>)  = <span class="hljs-literal">false</span>
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> str       the String to check, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@param</span> searchStr the String to find, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@return</span> <span class="hljs-literal">true</span> <span class="hljs-keyword">if</span> the String contains the search String, <span class="hljs-literal">false</span> <span class="hljs-keyword">if</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">or</span>
     * &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; string input
     * <span class="hljs-property">@since</span> <span class="hljs-number">2.0</span>
     */
    public static boolean contains(String str, String searchStr) {
        <span class="hljs-keyword">if</span> (str == <span class="hljs-literal">null</span> || searchStr == <span class="hljs-literal">null</span>) {
            <span class="hljs-keyword">return</span> <span class="hljs-literal">false</span>;
        }
        <span class="hljs-keyword">return</span> str.indexOf(searchStr) &gt;= <span class="hljs-number">0</span>;
    }

    public static String[] split(String str, String operator, Pattern pattern) {
        <span class="hljs-keyword">if</span> (str == <span class="hljs-literal">null</span> || operator == <span class="hljs-literal">null</span>) {
            <span class="hljs-keyword">return</span> <span class="hljs-literal">null</span>;
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">if</span> (contains(str, operator)) {
                <span class="hljs-keyword">return</span> pattern.split(str);
            } <span class="hljs-keyword">else</span> {
                <span class="hljs-keyword">return</span> <span class="hljs-literal">null</span>;
            }
        }
    }

    <span class="hljs-regexp">//</span> StripAll
    <span class="hljs-regexp">//</span> -----------------------------------------------------------------------

    /**
     * &lt;p&gt;
     * Strips whitespace from the start <span class="hljs-keyword">and</span> end <span class="hljs-keyword">of</span> every String <span class="hljs-keyword">in</span> an array.
     * Whitespace <span class="hljs-keyword">is</span> defined <span class="hljs-keyword">by</span> {<span class="hljs-property">@link</span> Character<span class="hljs-comment">#isWhitespace(char)}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A <span class="hljs-keyword">new</span> array <span class="hljs-keyword">is</span> returned each time, except <span class="hljs-keyword">for</span> length zero. A
     * &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; array will <span class="hljs-keyword">return</span> &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;. An empty array
     * will <span class="hljs-keyword">return</span> itself. A &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; array entry will be ignored.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.stripAll(<span class="hljs-literal">null</span>)             = <span class="hljs-literal">null</span>
     * StringUtils.stripAll([])               = []
     * StringUtils.stripAll([<span class="hljs-string">"abc"</span>, <span class="hljs-string">"  abc"</span>]) = [<span class="hljs-string">"abc"</span>, <span class="hljs-string">"abc"</span>]
     * StringUtils.stripAll([<span class="hljs-string">"abc  "</span>, <span class="hljs-literal">null</span>])  = [<span class="hljs-string">"abc"</span>, <span class="hljs-literal">null</span>]
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> strs the array to remove whitespace from, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@return</span> the stripped Strings, &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; <span class="hljs-keyword">if</span> <span class="hljs-literal">null</span> array input
     */
    public static String[] stripAll(String[] strs) {
        <span class="hljs-keyword">return</span> stripAll(strs, <span class="hljs-literal">null</span>);
    }

    /**
     * &lt;p&gt;
     * Strips any <span class="hljs-keyword">of</span> a set <span class="hljs-keyword">of</span> characters from the start <span class="hljs-keyword">and</span> end <span class="hljs-keyword">of</span> every String
     * <span class="hljs-keyword">in</span> an array.
     * &lt;/p&gt;
     * Whitespace <span class="hljs-keyword">is</span> defined <span class="hljs-keyword">by</span> {<span class="hljs-property">@link</span> Character<span class="hljs-comment">#isWhitespace(char)}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A <span class="hljs-keyword">new</span> array <span class="hljs-keyword">is</span> returned each time, except <span class="hljs-keyword">for</span> length zero. A
     * &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; array will <span class="hljs-keyword">return</span> &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;. An empty array
     * will <span class="hljs-keyword">return</span> itself. A &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; array entry will be ignored. A
     * &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; stripChars will strip whitespace as defined <span class="hljs-keyword">by</span>
     * {<span class="hljs-property">@link</span> Character<span class="hljs-comment">#isWhitespace(char)}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.stripAll(<span class="hljs-literal">null</span>, *)                = <span class="hljs-literal">null</span>
     * StringUtils.stripAll([], *)                  = []
     * StringUtils.stripAll([<span class="hljs-string">"abc"</span>, <span class="hljs-string">"  abc"</span>], <span class="hljs-literal">null</span>) = [<span class="hljs-string">"abc"</span>, <span class="hljs-string">"abc"</span>]
     * StringUtils.stripAll([<span class="hljs-string">"abc  "</span>, <span class="hljs-literal">null</span>], <span class="hljs-literal">null</span>)  = [<span class="hljs-string">"abc"</span>, <span class="hljs-literal">null</span>]
     * StringUtils.stripAll([<span class="hljs-string">"abc  "</span>, <span class="hljs-literal">null</span>], <span class="hljs-string">"yz"</span>)  = [<span class="hljs-string">"abc  "</span>, <span class="hljs-literal">null</span>]
     * StringUtils.stripAll([<span class="hljs-string">"yabcz"</span>, <span class="hljs-literal">null</span>], <span class="hljs-string">"yz"</span>)  = [<span class="hljs-string">"abc"</span>, <span class="hljs-literal">null</span>]
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> strs       the array to remove characters from, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@param</span> stripChars the characters to remove, <span class="hljs-literal">null</span> treated as whitespace
     * <span class="hljs-property">@return</span> the stripped Strings, &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; <span class="hljs-keyword">if</span> <span class="hljs-literal">null</span> array input
     */
    public static String[] stripAll(String[] strs, String stripChars) {
        int strsLen;
        <span class="hljs-keyword">if</span> (strs == <span class="hljs-literal">null</span> || (strsLen = strs.length) == <span class="hljs-number">0</span>) {
            <span class="hljs-keyword">return</span> strs;
        }
        String[] newArr = <span class="hljs-keyword">new</span> String[strsLen];
        <span class="hljs-keyword">for</span> (int i = <span class="hljs-number">0</span>; i &lt; strsLen; i++) {
            newArr[i] = strip(strs[i], stripChars);
        }
        <span class="hljs-keyword">return</span> newArr;
    }

    /**
     * &lt;p&gt;
     * Strips any <span class="hljs-keyword">of</span> a set <span class="hljs-keyword">of</span> characters from the start <span class="hljs-keyword">and</span> end <span class="hljs-keyword">of</span> a String.
     * This <span class="hljs-keyword">is</span> similar to {<span class="hljs-property">@link</span> String<span class="hljs-comment">#trim()} but allows the characters to be</span>
     * stripped to be controlled.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; input String returns &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;. An empty
     * string (<span class="hljs-string">""</span>) input returns the empty string.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * If the stripChars String <span class="hljs-keyword">is</span> &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;, whitespace <span class="hljs-keyword">is</span> stripped as
     * defined <span class="hljs-keyword">by</span> {<span class="hljs-property">@link</span> Character<span class="hljs-comment">#isWhitespace(char)}. Alternatively use</span>
     * {<span class="hljs-property">@link</span> <span class="hljs-comment">#strip(String)}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.strip(<span class="hljs-literal">null</span>, *)          = <span class="hljs-literal">null</span>
     * StringUtils.strip(<span class="hljs-string">""</span>, *)            = <span class="hljs-string">""</span>
     * StringUtils.strip(<span class="hljs-string">"abc"</span>, <span class="hljs-literal">null</span>)      = <span class="hljs-string">"abc"</span>
     * StringUtils.strip(<span class="hljs-string">"  abc"</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">"abc"</span>
     * StringUtils.strip(<span class="hljs-string">"abc  "</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">"abc"</span>
     * StringUtils.strip(<span class="hljs-string">" abc "</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">"abc"</span>
     * StringUtils.strip(<span class="hljs-string">"  abcyx"</span>, <span class="hljs-string">"xyz"</span>) = <span class="hljs-string">"  abc"</span>
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> str        the String to remove characters from, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@param</span> stripChars the characters to remove, <span class="hljs-literal">null</span> treated as whitespace
     * <span class="hljs-property">@return</span> the stripped String, &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; <span class="hljs-keyword">if</span> <span class="hljs-literal">null</span> String input
     */
    public static String strip(String str, String stripChars) {
        <span class="hljs-keyword">if</span> (isEmpty(str)) {
            <span class="hljs-keyword">return</span> str;
        }
        str = stripStart(str, stripChars);
        <span class="hljs-keyword">return</span> stripEnd(str, stripChars);
    }

    /**
     * &lt;p&gt;
     * Strips any <span class="hljs-keyword">of</span> a set <span class="hljs-keyword">of</span> characters from the start <span class="hljs-keyword">of</span> a String.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; input String returns &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;. An empty
     * string (<span class="hljs-string">""</span>) input returns the empty string.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * If the stripChars String <span class="hljs-keyword">is</span> &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;, whitespace <span class="hljs-keyword">is</span> stripped as
     * defined <span class="hljs-keyword">by</span> {<span class="hljs-property">@link</span> Character<span class="hljs-comment">#isWhitespace(char)}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.stripStart(<span class="hljs-literal">null</span>, *)          = <span class="hljs-literal">null</span>
     * StringUtils.stripStart(<span class="hljs-string">""</span>, *)            = <span class="hljs-string">""</span>
     * StringUtils.stripStart(<span class="hljs-string">"abc"</span>, <span class="hljs-string">""</span>)        = <span class="hljs-string">"abc"</span>
     * StringUtils.stripStart(<span class="hljs-string">"abc"</span>, <span class="hljs-literal">null</span>)      = <span class="hljs-string">"abc"</span>
     * StringUtils.stripStart(<span class="hljs-string">"  abc"</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">"abc"</span>
     * StringUtils.stripStart(<span class="hljs-string">"abc  "</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">"abc  "</span>
     * StringUtils.stripStart(<span class="hljs-string">" abc "</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">"abc "</span>
     * StringUtils.stripStart(<span class="hljs-string">"yxabc  "</span>, <span class="hljs-string">"xyz"</span>) = <span class="hljs-string">"abc  "</span>
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> str        the String to remove characters from, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@param</span> stripChars the characters to remove, <span class="hljs-literal">null</span> treated as whitespace
     * <span class="hljs-property">@return</span> the stripped String, &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; <span class="hljs-keyword">if</span> <span class="hljs-literal">null</span> String input
     */
    public static String stripStart(String str, String stripChars) {
        int strLen;
        <span class="hljs-keyword">if</span> (str == <span class="hljs-literal">null</span> || (strLen = str.length()) == <span class="hljs-number">0</span>) {
            <span class="hljs-keyword">return</span> str;
        }
        int start = <span class="hljs-number">0</span>;
        <span class="hljs-keyword">if</span> (stripChars == <span class="hljs-literal">null</span>) {
            <span class="hljs-keyword">while</span> ((start != strLen) &amp;&amp; Character.isWhitespace(str.charAt(start))) {
                start++;
            }
        } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (stripChars.length() == <span class="hljs-number">0</span>) {
            <span class="hljs-keyword">return</span> str;
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">while</span> ((start != strLen) &amp;&amp; (stripChars.indexOf(str.charAt(start)) != INDEX_NOT_FOUND)) {
                start++;
            }
        }
        <span class="hljs-keyword">return</span> str.substring(start);
    }

    /**
     * &lt;p&gt;
     * Strips any <span class="hljs-keyword">of</span> a set <span class="hljs-keyword">of</span> characters from the end <span class="hljs-keyword">of</span> a String.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; input String returns &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;. An empty
     * string (<span class="hljs-string">""</span>) input returns the empty string.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * If the stripChars String <span class="hljs-keyword">is</span> &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;, whitespace <span class="hljs-keyword">is</span> stripped as
     * defined <span class="hljs-keyword">by</span> {<span class="hljs-property">@link</span> Character<span class="hljs-comment">#isWhitespace(char)}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.stripEnd(<span class="hljs-literal">null</span>, *)          = <span class="hljs-literal">null</span>
     * StringUtils.stripEnd(<span class="hljs-string">""</span>, *)            = <span class="hljs-string">""</span>
     * StringUtils.stripEnd(<span class="hljs-string">"abc"</span>, <span class="hljs-string">""</span>)        = <span class="hljs-string">"abc"</span>
     * StringUtils.stripEnd(<span class="hljs-string">"abc"</span>, <span class="hljs-literal">null</span>)      = <span class="hljs-string">"abc"</span>
     * StringUtils.stripEnd(<span class="hljs-string">"  abc"</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">"  abc"</span>
     * StringUtils.stripEnd(<span class="hljs-string">"abc  "</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">"abc"</span>
     * StringUtils.stripEnd(<span class="hljs-string">" abc "</span>, <span class="hljs-literal">null</span>)    = <span class="hljs-string">" abc"</span>
     * StringUtils.stripEnd(<span class="hljs-string">"  abcyx"</span>, <span class="hljs-string">"xyz"</span>) = <span class="hljs-string">"  abc"</span>
     * StringUtils.stripEnd(<span class="hljs-string">"120.00"</span>, <span class="hljs-string">".0"</span>)   = <span class="hljs-string">"12"</span>
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> str        the String to remove characters from, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@param</span> stripChars the set <span class="hljs-keyword">of</span> characters to remove, <span class="hljs-literal">null</span> treated as whitespace
     * <span class="hljs-property">@return</span> the stripped String, &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; <span class="hljs-keyword">if</span> <span class="hljs-literal">null</span> String input
     */
    public static String stripEnd(String str, String stripChars) {
        int end;
        <span class="hljs-keyword">if</span> (str == <span class="hljs-literal">null</span> || (end = str.length()) == <span class="hljs-number">0</span>) {
            <span class="hljs-keyword">return</span> str;
        }

        <span class="hljs-keyword">if</span> (stripChars == <span class="hljs-literal">null</span>) {
            <span class="hljs-keyword">while</span> ((end != <span class="hljs-number">0</span>) &amp;&amp; Character.isWhitespace(str.charAt(end - <span class="hljs-number">1</span>))) {
                end--;
            }
        } <span class="hljs-keyword">else</span> <span class="hljs-keyword">if</span> (stripChars.length() == <span class="hljs-number">0</span>) {
            <span class="hljs-keyword">return</span> str;
        } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">while</span> ((end != <span class="hljs-number">0</span>) &amp;&amp; (stripChars.indexOf(str.charAt(end - <span class="hljs-number">1</span>)) != INDEX_NOT_FOUND)) {
                end--;
            }
        }
        <span class="hljs-keyword">return</span> str.substring(<span class="hljs-number">0</span>, end);
    }

    <span class="hljs-regexp">//</span> Empty checks
    <span class="hljs-regexp">//</span> -----------------------------------------------------------------------

    /**
     * &lt;p&gt;
     * Checks <span class="hljs-keyword">if</span> a String <span class="hljs-keyword">is</span> empty (<span class="hljs-string">""</span>) <span class="hljs-keyword">or</span> <span class="hljs-literal">null</span>.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.isEmpty(<span class="hljs-literal">null</span>)      = <span class="hljs-literal">true</span>
     * StringUtils.isEmpty(<span class="hljs-string">""</span>)        = <span class="hljs-literal">true</span>
     * StringUtils.isEmpty(<span class="hljs-string">" "</span>)       = <span class="hljs-literal">false</span>
     * StringUtils.isEmpty(<span class="hljs-string">"bob"</span>)     = <span class="hljs-literal">false</span>
     * StringUtils.isEmpty(<span class="hljs-string">"  bob  "</span>) = <span class="hljs-literal">false</span>
     * &lt;/pre&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * <span class="hljs-attribute">NOTE</span>: This method changed <span class="hljs-keyword">in</span> Lang version <span class="hljs-number">2.0</span>. It <span class="hljs-literal">no</span> longer trims the
     * String. That functionality <span class="hljs-keyword">is</span> available <span class="hljs-keyword">in</span> isBlank().
     * &lt;/p&gt;
     *
     * <span class="hljs-property">@param</span> str the String to check, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@return</span> &lt;code&gt;<span class="hljs-literal">true</span>&lt;/code&gt; <span class="hljs-keyword">if</span> the String <span class="hljs-keyword">is</span> empty <span class="hljs-keyword">or</span> <span class="hljs-literal">null</span>
     */
    public static boolean isEmpty(String str) {
        <span class="hljs-keyword">return</span> str == <span class="hljs-literal">null</span> || str.length() == <span class="hljs-number">0</span>;
    }

    <span class="hljs-regexp">//</span> Stripping
    <span class="hljs-regexp">//</span> -----------------------------------------------------------------------

    /**
     * &lt;p&gt;
     * Strips whitespace from the start <span class="hljs-keyword">and</span> end <span class="hljs-keyword">of</span> a String.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * This <span class="hljs-keyword">is</span> similar to {<span class="hljs-property">@link</span> (String)} but removes whitespace.
     * Whitespace <span class="hljs-keyword">is</span> defined <span class="hljs-keyword">by</span> {<span class="hljs-property">@link</span> Character<span class="hljs-comment">#isWhitespace(char)}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; input String returns &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.strip(<span class="hljs-literal">null</span>)     = <span class="hljs-literal">null</span>
     * StringUtils.strip(<span class="hljs-string">""</span>)       = <span class="hljs-string">""</span>
     * StringUtils.strip(<span class="hljs-string">"   "</span>)    = <span class="hljs-string">""</span>
     * StringUtils.strip(<span class="hljs-string">"abc"</span>)    = <span class="hljs-string">"abc"</span>
     * StringUtils.strip(<span class="hljs-string">"  abc"</span>)  = <span class="hljs-string">"abc"</span>
     * StringUtils.strip(<span class="hljs-string">"abc  "</span>)  = <span class="hljs-string">"abc"</span>
     * StringUtils.strip(<span class="hljs-string">" abc "</span>)  = <span class="hljs-string">"abc"</span>
     * StringUtils.strip(<span class="hljs-string">" ab c "</span>) = <span class="hljs-string">"ab c"</span>
     * &lt;/pre&gt;
     *
     * <span class="hljs-property">@param</span> str the String to remove whitespace from, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@return</span> the stripped String, &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; <span class="hljs-keyword">if</span> <span class="hljs-literal">null</span> String input
     */
    public static String strip(String str) {
        <span class="hljs-keyword">return</span> strip(str, <span class="hljs-literal">null</span>);
    }

    /**
     * &lt;p&gt;
     * Converts a String to lower <span class="hljs-reserved">case</span> as per {<span class="hljs-property">@link</span> String<span class="hljs-comment">#toLowerCase()}.</span>
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * A &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; input String returns &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt;.
     * &lt;/p&gt;
     * &lt;p&gt;
     * &lt;pre&gt;
     * StringUtils.lowerCase(<span class="hljs-literal">null</span>)  = <span class="hljs-literal">null</span>
     * StringUtils.lowerCase(<span class="hljs-string">""</span>)    = <span class="hljs-string">""</span>
     * StringUtils.lowerCase(<span class="hljs-string">"aBc"</span>) = <span class="hljs-string">"abc"</span>
     * &lt;/pre&gt;
     * &lt;p&gt;
     * &lt;p&gt;
     * &lt;strong&gt;<span class="hljs-attribute">Note</span>:&lt;/strong&gt; As described <span class="hljs-keyword">in</span> the documentation <span class="hljs-keyword">for</span>
     * {<span class="hljs-property">@link</span> String<span class="hljs-comment">#toLowerCase()}, the result of this method is affected by</span>
     * the current locale. For platform-independent <span class="hljs-reserved">case</span> transformations, the
     * method {<span class="hljs-property">@link</span> (String, Locale)} should be used <span class="hljs-reserved">with</span> a specific
     * locale (e.g. {<span class="hljs-property">@link</span> Locale<span class="hljs-comment">#ENGLISH}).</span>
     * &lt;/p&gt;
     *
     * <span class="hljs-property">@param</span> str the String to lower <span class="hljs-reserved">case</span>, may be <span class="hljs-literal">null</span>
     * <span class="hljs-property">@return</span> the lower cased String, &lt;code&gt;<span class="hljs-literal">null</span>&lt;/code&gt; <span class="hljs-keyword">if</span> <span class="hljs-literal">null</span> String input
     */
    public static String toLowerCase(String str) {
        <span class="hljs-keyword">if</span> (isBlank(str)) {
            <span class="hljs-keyword">return</span> str;
        }
        <span class="hljs-keyword">return</span> str.toLowerCase();
    }

    public static String normalDateToConnectorDate (String time){
        SimpleDateFormat format =  <span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyyy-MM-dd HH:mm:ss"</span>);
        SimpleDateFormat format2 =  <span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyyyMMddHHmmss"</span>);

        Date date = <span class="hljs-literal">null</span>;
        <span class="hljs-keyword">try</span> {
            date = format.parse(time);
        } <span class="hljs-keyword">catch</span> (ParseException e) {
            e.toString();
            <span class="hljs-keyword">return</span> <span class="hljs-literal">null</span>;
        }
        <span class="hljs-keyword">return</span> format2.format(date);
    }
}
</code></pre>

<p>3 打包java代码，上传jar包和mysql驱动包到flume的lib目录 <br>
     进入源码的根目录执行mvn install -DskipTests -Dtar <br>
 4 创建MySQL表</p>

<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">CREATE</span> <span class="hljs-keyword">TABLE</span> <span class="hljs-string">`test_flume_sink`</span> (
  <span class="hljs-string">`id`</span> <span class="hljs-keyword">int</span>(<span class="hljs-number">11</span>) <span class="hljs-keyword">NOT</span> <span class="hljs-keyword">NULL</span> AUTO_INCREMENT,
  <span class="hljs-string">`content`</span> <span class="hljs-keyword">varchar</span>(<span class="hljs-number">255</span>) <span class="hljs-keyword">DEFAULT</span> <span class="hljs-keyword">NULL</span>,
  <span class="hljs-string">`log2`</span> bigint(<span class="hljs-number">25</span>) <span class="hljs-keyword">DEFAULT</span> <span class="hljs-keyword">NULL</span>,
  <span class="hljs-keyword">PRIMARY</span> <span class="hljs-keyword">KEY</span> (<span class="hljs-string">`id`</span>)
) ENGINE=InnoDB AUTO_INCREMENT=<span class="hljs-number">1</span> <span class="hljs-keyword">DEFAULT</span> CHARSET=utf8;</span>
</code></pre>

<p>5 修改flume配置文件</p>



<pre class="prettyprint"><code class=" hljs avrasm">agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.type</span> = cn<span class="hljs-preprocessor">.bfire</span><span class="hljs-preprocessor">.MySQLSink</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.hostname</span>=<span class="hljs-number">1.1</span><span class="hljs-number">.1</span><span class="hljs-number">.1</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.port</span>=<span class="hljs-number">3306</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.databaseName</span>=bigdata
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.tableName</span>=test_flume_sink
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.user</span>=mi
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.password</span>=<span class="hljs-number">11111111</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.column</span>_name=content,log2
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.mysqlSink</span><span class="hljs-preprocessor">.field</span>_separator=\\|</code></pre>

<p>6 flume收集文件内容</p>

<pre><code>abc|ddd
eee|fff
aaa,ggg
ddd|fff
sss|jjj

fff,fff
aaaaaa|gg|fffff
aaaaa,bbbbbbbbbb,ccccc
dddddd|eeeeeee|aa
xxxx|eeee
zzz|aaa,bbb|xxzz
</code></pre>

<p>7 MySQL表收集结果 <br>
 <img src="https://img-blog.csdn.net/20160414150328885" alt="MySQL表收集结果" title=""> <br>
8 遇到的问题【如果使用flume1.6.0下面的内容属于无聊】</p>

<p>错误日志：</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">java.lang.IllegalStateException:</span> File should not roll when commit is outstanding.
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.avro</span><span class="hljs-preprocessor">.ReliableSpoolingFileEventReader</span><span class="hljs-preprocessor">.readEvents</span>(ReliableSpoolingFileEventReader<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">221</span>)
    at org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.source</span><span class="hljs-preprocessor">.SpoolDirectorySource</span>$SpoolDirectoryRunnable<span class="hljs-preprocessor">.run</span>(SpoolDirectorySource<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">227</span>)
    at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.Executors</span>$RunnableAdapter<span class="hljs-preprocessor">.call</span>(Executors<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">511</span>)
    at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.FutureTask</span><span class="hljs-preprocessor">.runAndReset</span>(FutureTask<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">308</span>)
    at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ScheduledThreadPoolExecutor</span>$ScheduledFutureTask<span class="hljs-preprocessor">.access</span>$301(ScheduledThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">180</span>)
    at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ScheduledThreadPoolExecutor</span>$ScheduledFutureTask<span class="hljs-preprocessor">.run</span>(ScheduledThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">294</span>)
    at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ThreadPoolExecutor</span><span class="hljs-preprocessor">.runWorker</span>(ThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">1142</span>)
    at java<span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.concurrent</span><span class="hljs-preprocessor">.ThreadPoolExecutor</span>$Worker<span class="hljs-preprocessor">.run</span>(ThreadPoolExecutor<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">617</span>)
    at java<span class="hljs-preprocessor">.lang</span><span class="hljs-preprocessor">.Thread</span><span class="hljs-preprocessor">.run</span>(Thread<span class="hljs-preprocessor">.java</span>:<span class="hljs-number">745</span>)</code></pre>

<p>原因 ： flume收集目录中有文件大小为0 <br>
<img src="https://img-blog.csdn.net/20160414172135347" alt="这里写图片描述" title=""> <br>
不过，这是flume1.6.0之前版本的BUG <br>
<a href="https://issues.apache.org/jira/browse/FLUME-1934" rel="nofollow">https://issues.apache.org/jira/browse/FLUME-1934</a> <br>
<a href="http://flume.apache.org/releases/1.6.0.html" rel="nofollow">http://flume.apache.org/releases/1.6.0.html</a> <br>
<img src="https://img-blog.csdn.net/20160414172322285" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
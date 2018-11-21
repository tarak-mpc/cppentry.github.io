---
layout:     post
title:      Spark与Mysql(JdbcRDD)整合开发(zh)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a></span>的功能是非常强大，在本博客的文章中，我们讨论了<a title="" href="http://www.iteblog.com/archives/1051" rel="nofollow">《Spark和Hbase整合》</a>、<a title="" href="http://www.iteblog.com/archives/1063" rel="nofollow">《Spark和Flume-ng整合》</a>以及<a title="" href="http://www.iteblog.com/archives/1090" rel="nofollow">《和Hive的整合》</a>。今天我们的主题是聊聊<span class="wp_keywordlink_affiliate"><a title="" href="http://www.iteblog.com/archives/tag/spark" rel="nofollow">Spark</a></span>和Mysql的组合开发。
<div class="wp-caption aligncenter" style="line-height:22px;color:#FF0000;font-size:12px;">
<a title="" href="http://iteblog.qiniudn.com/pic/Spark_Mysql.png?watermark/1/image/aHR0cDovL2l0ZWJsb2cucWluaXVkbi5jb20vcGljL2xvZ28ucG5n/dissolve/100/gravity/SouthEast/dx/20/dy/20" rel="nofollow"><img alt="" src="http://iteblog.qiniudn.com/pic/Spark_Mysql.png?watermark/1/image/aHR0cDovL2l0ZWJsb2cucWluaXVkbi5jb20vcGljL2xvZ28ucG5n/dissolve/100/gravity/SouthEast/dx/20/dy/20"></a><br>
如果想及时了解Spark、Hadoop或者Hbase相关的文章，欢迎关注微信公共帐号：<strong>iteblog_hadoop</strong></div>
<p>　　在Spark中提供了一个JdbcRDD类，该RDD就是读取JDBC中的数据并转换成RDD，之后我们就可以对该RDD进行各种的操作。我们先看看该类的构造函数：</p>
<div id="highlighter_683426" class="syntaxhighlighter java">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1113#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1113#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1113#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="java plain">JdbcRDD[T: ClassTag]( </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">sc: SparkContext,
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">getConnection: () =&gt; Connection,
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">sql: String,
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">lowerBound: Long,
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>6</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">upperBound: Long,
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>7</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">numPartitions: Int,
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>8</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">mapRow: (ResultSet) =&gt; T = JdbcRDD.resultSetToObjectArray _)</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>　　这个类带了很多参数，关于这个函数的各个参数的含义，我觉得直接看英文就可以很好的理解，如下：</p>
<blockquote>
<p>@param getConnection a function that returns an open Connection.<br>
The RDD takes care of closing the connection.<br>
@param sql the text of the query.<br>
The query must contain two ? placeholders for parameters used to partition the results.<br>
E.g. “select title, author from books where ? &lt; = id and id &lt;= ?”<br>
@param lowerBound the minimum value of the first placeholder<br>
@param upperBound the maximum value of the second placeholder<br>
The lower and upper bounds are inclusive.<br>
@param numPartitions the number of partitions.<br>
Given a lowerBound of 1, an upperBound of 20, and a numPartitions of 2,<br>
the query would be executed twice, once with (1, 10) and once with (11, 20)<br>
@param mapRow a function from a ResultSet to a single row of the desired result type(s).<br>
This should only call getInt, getString, etc; the RDD takes care of calling next.<br>
The default maps a ResultSet to an array of Object. </p>
</blockquote>
<div class="note">　　上面英文看不懂？？那好吧，我给你翻译一下。<br>
　　1、getConnection 返回一个已经打开的结构化数据库连接，JdbcRDD会自动维护关闭。<br>
　　2、sql 是查询语句，此查询语句必须包含两处占位符?来作为分割数据库ResulSet的参数，例如：”select title, author from books where ? &lt; = id and id &lt;= ?”<br>
　　3、lowerBound, upperBound, numPartitions 分别为第一、第二占位符，partition的个数。例如，给出lowebound 1，upperbound 20， numpartitions 2，则查询分别为(1, 10)与(11, 20)<br>
　　4、mapRow 是转换函数，将返回的ResultSet转成RDD需用的单行数据，此处可以选择Array或其他，也可以是自定义的case class。默认的是将ResultSet 转换成一个Object数组。
</div>
<p>下面我们说明如何使用该类。</p>
<div id="highlighter_428008" class="syntaxhighlighter java">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1113#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1113#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1113#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="java keyword">package</code> <code class="java plain">
scala </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="java keyword">import</code> <code class="java plain">
java.sql.DriverManager </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="java keyword">import</code> <code class="java plain">
org.apache.spark.SparkContext </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="java keyword">import</code> <code class="java plain">
org.apache.spark.rdd.JdbcRDD </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="java preprocessor">/** </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces"> </code><code class="java preprocessor">* User: 过往记忆
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="spaces"> </code><code class="java preprocessor">* Date: 14-9-10
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="spaces"> </code><code class="java preprocessor">* Time: 下午13:16
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces"> </code><code class="java preprocessor">* bolg:
<a href="http://www.iteblog.com/" rel="nofollow">http://www.iteblog.com</a> </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="spaces"> </code><code class="java preprocessor">* 本文地址：<a href="http://www.iteblog.com/archives/1113" rel="nofollow">http://www.iteblog.com/archives/1113</a>
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="spaces"> </code><code class="java preprocessor">* 过往记忆博客，专注于hadoop、hive、spark、shark、flume的技术博客，大量的干货
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="spaces"> </code><code class="java preprocessor">* 过往记忆博客微信公共帐号：iteblog_hadoop
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="spaces"> </code><code class="java preprocessor">*/</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="java plain">object SparkToJDBC { </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="spaces">  </code><code class="java plain">def main(args: Array[String]) {
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">val sc =
</code><code class="java keyword">new</code> <code class="java plain">SparkContext(</code><code class="java string">"local"</code><code class="java plain">,
</code><code class="java string">"mysql"</code><code class="java plain">) </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">val rdd =
</code><code class="java keyword">new</code> <code class="java plain">JdbcRDD( </code>
</td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>22</code></td>
<td class="content"><code class="spaces">      </code><code class="java plain">sc,
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>23</code></td>
<td class="content"><code class="spaces">      </code><code class="java plain">() =&gt; {
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>24</code></td>
<td class="content"><code class="spaces">        </code><code class="java plain">Class.forName(</code><code class="java string">"com.mysql.jdbc.Driver"</code><code class="java plain">).newInstance()
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>25</code></td>
<td class="content"><code class="spaces">        </code><code class="java plain">DriverManager.getConnection(</code><code class="java string">"jdbc:<a href="" rel="nofollow">mysql://localhost:3306/db</a>"</code><code class="java plain">,
</code><code class="java string">"root"</code><code class="java plain">, </code><code class="java string">"123456"</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>26</code></td>
<td class="content"><code class="spaces">      </code><code class="java plain">},
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>27</code></td>
<td class="content"><code class="spaces">      </code><code class="java string">"SELECT content FROM mysqltest WHERE ID &gt;= ? AND ID &lt;= ?"</code><code class="java plain">,
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>28</code></td>
<td class="content"><code class="spaces">      </code><code class="java value">1</code><code class="java plain">,
</code><code class="java value">100</code><code class="java plain">, </code><code class="java value">3</code><code class="java plain">,
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>29</code></td>
<td class="content"><code class="spaces">      </code><code class="java plain">r =&gt; r.getString(</code><code class="java value">1</code><code class="java plain">)).cache()
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>30</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>31</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">print(rdd.filter(_.contains(</code><code class="java string">"success"</code><code class="java plain">)).count())
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>32</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">sc.stop()
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>33</code></td>
<td class="content"><code class="spaces">  </code><code class="java plain">} </code>
</td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>34</code></td>
<td class="content"><code class="java plain">}</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>代码比较简短，主要是读mysqltest 表中的数据，并统计ID&gt;=1 &amp;&amp; ID &lt; = 100 &amp;&amp; content.contains("success")的记录条数。我们从代码中可以看出JdbcRDD的sql参数要带有两个？的占位符，而这两个占位符是给参数lowerBound和参数upperBound定义where语句的上下边界的。从JdbcRDD类的构造函数可以知道，参数lowerBound和参数upperBound都只能是Long类型的，并不支持其他类型的比较，这个使得JdbcRDD使用场景比较有限。而且在使用过程中sql参数必须有类似
 ID &gt;= ? AND ID &lt; = ?这样的where语句，如果你写成下面的形式：</p>
<div id="highlighter_17632" class="syntaxhighlighter java">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1113#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1113#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1113#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="java plain">val rdd = </code><code class="java keyword">new</code>
<code class="java plain">JdbcRDD( </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="spaces">  </code><code class="java plain">sc, </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="spaces">  </code><code class="java plain">() =&gt; {
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">Class.forName(</code><code class="java string">"com.mysql.jdbc.Driver"</code><code class="java plain">).newInstance()
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">DriverManager.getConnection(</code><code class="java string">"jdbc:<a href="" rel="nofollow">mysql://localhost:3306/db</a>"</code><code class="java plain">,
</code><code class="java string">"root"</code><code class="java plain">, </code><code class="java string">"123456"</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>6</code></td>
<td class="content"><code class="spaces">  </code><code class="java plain">}, </code>
</td>
</tr></tbody></table></div>
<div class="line alt1 highlighted">
<table><tbody><tr><td class="number"><code>7</code></td>
<td class="content"><code class="spaces">  </code><code class="java string">"SELECT content FROM mysqltest"</code><code class="java plain">,
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>8</code></td>
<td class="content"><code class="spaces">  </code><code class="java value">1</code><code class="java plain">,
</code><code class="java value">100</code><code class="java plain">, </code><code class="java value">3</code><code class="java plain">,
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>9</code></td>
<td class="content"><code class="spaces">  </code><code class="java plain">r =&gt; r.getString(</code><code class="java value">1</code><code class="java plain">)).cache()</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>那不好意思，运行的时候会出现以下的错误：</p>
<div id="highlighter_79716" class="syntaxhighlighter java">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1113#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1113#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1113#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="java value">2014</code><code class="java plain">-</code><code class="java value">09</code><code class="java plain">-</code><code class="java value">10</code>
<code class="java value">15</code><code class="java plain">:</code><code class="java value">47</code><code class="java plain">:</code><code class="java value">45</code><code class="java plain">,</code><code class="java value">621</code>
<code class="java plain">(Executor task launch worker-</code><code class="java value">0</code><code class="java plain">) [ERROR - 
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="java plain">org.apache.spark.Logging$</code><code class="java keyword">class</code><code class="java plain">.logError(Logging.scala:</code><code class="java value">95</code><code class="java plain">)] Exception in task ID
</code><code class="java value">1</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="java plain">java.sql.SQLException: Parameter index out of range (</code><code class="java value">1</code>
<code class="java plain">&gt; number of parameters, which is </code><code class="java value">0</code><code class="java plain">).
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:</code><code class="java value">1074</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:</code><code class="java value">988</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:</code><code class="java value">974</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:</code><code class="java value">919</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at com.mysql.jdbc.PreparedStatement.checkBounds(PreparedStatement.java:</code><code class="java value">3813</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at com.mysql.jdbc.PreparedStatement.setInternal(PreparedStatement.java:</code><code class="java value">3795</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at com.mysql.jdbc.PreparedStatement.setInternal(PreparedStatement.java:</code><code class="java value">3840</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at com.mysql.jdbc.PreparedStatement.setLong(PreparedStatement.java:</code><code class="java value">3857</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.rdd.JdbcRDD$$anon$</code><code class="java value">1</code><code class="java plain">.&lt;init&gt;(JdbcRDD.scala:</code><code class="java value">84</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.rdd.JdbcRDD.compute(JdbcRDD.scala:</code><code class="java value">70</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.rdd.JdbcRDD.compute(JdbcRDD.scala:</code><code class="java value">50</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.rdd.RDD.computeOrReadCheckpoint(RDD.scala:</code><code class="java value">262</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.CacheManager.getOrCompute(CacheManager.scala:</code><code class="java value">77</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.rdd.RDD.iterator(RDD.scala:</code><code class="java value">227</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.rdd.FilteredRDD.compute(FilteredRDD.scala:</code><code class="java value">34</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.rdd.RDD.computeOrReadCheckpoint(RDD.scala:</code><code class="java value">262</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>20</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.rdd.RDD.iterator(RDD.scala:</code><code class="java value">229</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>21</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.scheduler.ResultTask.runTask(ResultTask.scala:</code><code class="java value">111</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>22</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.scheduler.Task.run(Task.scala:</code><code class="java value">51</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>23</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:</code><code class="java value">183</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>24</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.util.concurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:</code><code class="java value">886</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>25</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:</code><code class="java value">908</code><code class="java plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>26</code></td>
<td class="content"><code class="spaces">    </code><code class="java plain">at java.lang.Thread.run(Thread.java:</code><code class="java value">619</code><code class="java plain">)</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>看下JdbcRDD类的compute函数实现就知道了：</p>
<div id="highlighter_404283" class="syntaxhighlighter scala">
<div class="bar">
<div class="toolbar"><a class="item viewSource" title="查看源代码" href="http://www.iteblog.com/archives/1113#viewSource" rel="nofollow" style="width:16px;">查看源代码</a>
<div class="item copyToClipboard"></div>
<a class="item printSource" title="打印" href="http://www.iteblog.com/archives/1113#printSource" rel="nofollow" style="width:16px;">打印</a><a class="item about" title="帮助" href="http://www.iteblog.com/archives/1113#about" rel="nofollow" style="width:16px;">帮助</a></div>
</div>
<div class="lines no-wrap">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="scala keyword">override</code> <code class="scala keyword">
def</code> <code class="scala plain">compute(thePart</code><code class="scala keyword">:</code>
<code class="scala plain">Partition, context</code><code class="scala keyword">:</code>
<code class="scala plain">TaskContext) </code><code class="scala keyword">=</code>
<code class="scala keyword">new</code> <code class="scala plain">NextIterator[T] {
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">context.addOnCompleteCallback{ ()
</code><code class="scala keyword">=</code><code class="scala plain">&gt; closeIfNeeded() }
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">part </code><code class="scala keyword">=</code> <code class="scala plain">
thePart.asInstanceOf[JdbcPartition] </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">conn </code><code class="scala keyword">=</code> <code class="scala plain">
getConnection() </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">val</code>
<code class="scala plain">stmt </code><code class="scala keyword">=</code> <code class="scala plain">
conn.prepareStatement(sql, ResultSet.TYPE</code><code class="scala keyword">_</code><code class="scala plain">FORWARD</code><code class="scala keyword">_</code><code class="scala plain">ONLY,
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"><code class="spaces">                                </code><code class="scala plain">ResultSet.CONCUR</code><code class="scala keyword">_</code><code class="scala plain">READ</code><code class="scala keyword">_</code><code class="scala plain">ONLY)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="spaces">    </code><code class="scala keyword">if</code>
<code class="scala plain">(conn.getMetaData.getURL.matches(</code><code class="scala string">"jdbc:mysql:.*"</code><code class="scala plain">)) {
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">stmt.setFetchSize(Integer.MIN</code><code class="scala keyword">_</code><code class="scala plain">VALUE)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="spaces">      </code><code class="scala plain">logInfo(</code><code class="scala string">"statement fetch size set to: "</code>
<code class="scala plain">+ stmt.getFetchSize +  </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="spaces">                                </code><code class="scala string">" to force mySQL streaming "</code><code class="scala plain">)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">} </code>
</td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">stmt.setLong(</code><code class="scala value">1</code><code class="scala plain">, part.lower)
</code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="spaces">    </code><code class="scala plain">stmt.setLong(</code><code class="scala value">2</code><code class="scala plain">, part.upper)
</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="spaces"> </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="scala plain">...........................</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>　　不过值得高兴的是，我们可以自定义一个JdbcRDD，修改上面的计算思路，这样就可以得到符合我们自己要求的JdbcRDD。<br>
　　PS: 在写本文的时候，本来我想提供一个JAVA例子，但是JdbcRDD类的最后一个参数很不好传，网上有个哥们是这么说的：</p>
<blockquote>
<p>I don’t think there is a completely Java-friendly version of this class. However you should be able to get away with passing something generic like “ClassTag.MODULE.&lt;k&gt;apply(Object.class)” There’s probably something even simpler.<br>
下面是我发邮件到Spark开发组询问如何在Java中使用JdbcRDD，开发人员给我的回复信息如下：</p>
<blockquote>
<p>The two parameters which might cause you some difficulty in Java are</p>
<p>getConnection, which is a Function0[Connection], i.e. a 0 argument function that returns a jdbc connection<br>
and<br>
mapRow, which is a Function1[ResultSet, T], i.e. a 1 argument function that takes a result set and returns whatever type you want to convert it to.</p>
<p>You could try and include the scala library as a compile-time dependency in your project, and make your own instances of classes that implement the Function0 and Function1 interfaces. I’ve never tried that, so your mileage may vary. The mapRow parameter
 might be skippable – it has as a default a function that just returns an array of object, which you could then cast.
</p>
<p>It would probably be easy to make the JdbcRDD interface more usable from Java8, but I don’t know how much demand there is for that.</p>
</blockquote>
<p>所以也只能放弃了。如果你知道怎么用Java实现，欢迎留言。</p>
</blockquote>
            </div>
                </div>
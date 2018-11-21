---
layout:     post
title:      ubuntu中spark使用mysql  图文教程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>系统和软件配置</h1>
<p>安装ubuntu16.04</p>
<p><a href="http://blog.csdn.net/wyx100/article/details/51582617" rel="nofollow">http://blog.csdn.net/wyx100/article/details/51582617</a></p>
<p><br>
安装hadoop（ubuntu16.04）<br>
安装spark（ubuntu16.04）<br>
安装mysql（ubuntu16.04）</p>
<h2>
</h2><p>mysql安装和检验见：</p>
<p><a href="http://blog.csdn.net/fighter_yy/article/details/40753889" rel="nofollow">http://blog.csdn.net/fighter_yy/article/details/40753889</a></p>
安装idea（ubuntu16.04）
<h1>建立数据库</h1>
<div>使用数据库参考：<a href="http://c.biancheng.net/cpp/html/1444.html" rel="nofollow">http://c.biancheng.net/cpp/html/1444.html</a></div>
<div><br></div>
<div>数据库名：sparkdb</div>
<div>账号：root</div>
<div>密码：root</div>
<div><br></div>
<div>建立数据库表：sparktest</div>
<div><br></div>
<div>
<table border="1" width="200" cellspacing="1" cellpadding="1"><tbody><tr><td>id</td>
<td>name</td>
</tr><tr><td>1</td>
<td>a</td>
</tr><tr><td>2</td>
<td>b</td>
</tr></tbody></table><br></div>
<div>id数据类型：long</div>
<div>name数据类型：string</div>
<div><br></div>
<h2>建立数据库代码</h2>
<div><pre><code class="language-java">mysql&gt; CREATE DATABASE sparkdb;

mysql&gt; USE sparkdb;

mysql&gt; CREATE TABLE sparktest (ID long, NAME VARCHAR(20));

mysql&gt;insert sparktest (ID,NAME) values(1,'a'),(2,'b');</code></pre></div>
<h1>scala代码</h1>
<div>详解可参考：<a href="http://www.iteblog.com/archives/1275" rel="nofollow">http://www.iteblog.com/archives/1275</a></div>
<div><br></div>
<div><pre><code class="language-java">package scala
 
import java.sql.DriverManager
 
import org.apache.spark.SparkContext
import org.apache.spark.rdd.JdbcRDD
 

object SparkToJDBC {
 
  def main(args: Array[String]) {
    val sc = new SparkContext("local", "mysql")
    val rdd = new JdbcRDD(
      sc,
      () =&gt; {
        Class.forName("com.mysql.jdbc.Driver").newInstance()
        DriverManager.getConnection("jdbc:mysql://localhost:3306/sparkdb", "root", "root")
      },
      "SELECT name FROM sparktest WHERE ID &gt;= ? AND ID &lt;= ?",
      1, 100, 3,
      r =&gt; r.getString(1)).cache()
 
    print(rdd.filter(_.contains("success")).count())
    sc.stop()
  }
}</code></pre>
<div><br></div>
<div><pre><code class="language-java">print(rdd.filter(_.contains("success")).count())</code></pre>执行结果是：0</div>
<div>rdd.filter(_.contains("success")).count()详解见：<a href="http://blog.csdn.net/wyx100/article/details/51973712" rel="nofollow">http://blog.csdn.net/wyx100/article/details/51973712</a></div>
<br></div>
<p><br></p>
            </div>
                </div>
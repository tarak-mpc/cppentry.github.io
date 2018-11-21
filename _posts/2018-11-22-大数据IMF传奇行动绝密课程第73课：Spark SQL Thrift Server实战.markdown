---
layout:     post
title:      大数据IMF传奇行动绝密课程第73课：Spark SQL Thrift Server实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-sql-thrift-server实战">Spark SQL Thrift Server实战</h1>

<p>通过JDBC/ODBC-&gt;Thirft Server-&gt;Spark SQL-&gt;Hive取代传统数据库为后台的系统 <br>
启动hive：</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">hive</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">service</span> <span class="hljs-comment">metastore</span> <span class="hljs-comment">&amp;</span>
<span class="hljs-comment">hive</span></code></pre>

<p>服务端：启动thrift服务端</p>



<pre class="prettyprint"><code class=" hljs avrasm">./start-thriftserver<span class="hljs-preprocessor">.sh</span> --master spark://Master:<span class="hljs-number">7077</span> --hiveconf hive<span class="hljs-preprocessor">.server</span>2<span class="hljs-preprocessor">.transport</span><span class="hljs-preprocessor">.mode</span>=http --hiveconf hive<span class="hljs-preprocessor">.server</span>2<span class="hljs-preprocessor">.thrift</span><span class="hljs-preprocessor">.http</span><span class="hljs-preprocessor">.path</span>=cliservice</code></pre>

<p>Java代码</p>

<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
 * Java通过JDBC访问Thrift Server，进而访问Hive，这是企业级开发中最为常见的方式
 */</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">SparkSQLJDBC2ThriftServer</span> {</span>

    <span class="hljs-javadoc">/**
     *<span class="hljs-javadoctag"> @param</span> args
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {

        Connection conn = <span class="hljs-keyword">null</span>;
        ResultSet rs = <span class="hljs-keyword">null</span>;
        String sql = <span class="hljs-string">"select * from people where age = ?"</span>;
        <span class="hljs-keyword">try</span> {
            Class.forName(<span class="hljs-string">"org.apache.hive.jdbc.HiveDriver"</span>);
            conn = DriverManager.getConnection(<span class="hljs-string">"jdbc:hive2://Master:10001/default?"</span>     <span class="hljs-comment">//10001为thrift默认端口，default为hive'中的库</span>
                    + <span class="hljs-string">"hive.server2.transport.mode=http;hive.server2.thrift.http.path=cliservice"</span>,
                    <span class="hljs-string">"root"</span>,<span class="hljs-string">""</span>);
            PreparedStatement stmt = conn.prepareStatement(sql);
            stmt.setInt(<span class="hljs-number">1</span>, <span class="hljs-number">30</span>);
            rs = stmt.executeQuery();
            <span class="hljs-keyword">while</span>(rs.next()){
                System.out.println(rs.getString(<span class="hljs-number">1</span>)); <span class="hljs-comment">//数据应保存成parquet</span>
            }
        } <span class="hljs-keyword">catch</span> (ClassNotFoundException e) {
            <span class="hljs-comment">// TODO Auto-generated catch block</span>
            e.printStackTrace();
        } <span class="hljs-keyword">catch</span> (SQLException e) {
            <span class="hljs-comment">// TODO Auto-generated catch block</span>
            e.printStackTrace();
        } <span class="hljs-keyword">finally</span> {
            <span class="hljs-keyword">try</span> {
                rs.close();
                conn.close();
            } <span class="hljs-keyword">catch</span> (SQLException e) {
                <span class="hljs-comment">// TODO Auto-generated catch block</span>
                e.printStackTrace();
            }
        }
    }
}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
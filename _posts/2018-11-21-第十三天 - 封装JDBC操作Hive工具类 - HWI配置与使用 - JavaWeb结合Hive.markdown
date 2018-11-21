---
layout:     post
title:      第十三天 - 封装JDBC操作Hive工具类 - HWI配置与使用 - JavaWeb结合Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="第十三天-封装jdbc操作hive工具类-hwi配置与使用-javaweb结合hive">第十三天 - 封装JDBC操作Hive工具类 - HWI配置与使用 - JavaWeb结合Hive</h1>

<p></p><div class="toc">
<ul>
<li><a href="#%E7%AC%AC%E5%8D%81%E4%B8%89%E5%A4%A9-%E5%B0%81%E8%A3%85jdbc%E6%93%8D%E4%BD%9Chive%E5%B7%A5%E5%85%B7%E7%B1%BB-hwi%E9%85%8D%E7%BD%AE%E4%B8%8E%E4%BD%BF%E7%94%A8-javaweb%E7%BB%93%E5%90%88hive" rel="nofollow">第十三天 - 封装JDBC操作Hive工具类 - HWI配置与使用 - JavaWeb结合Hive</a><ul>
<li><ul>
<li><a href="#%E4%B8%80hive-sql%E8%AF%AD%E5%8F%A5%E8%A1%A5%E5%85%85" rel="nofollow">一、Hive Sql语句补充</a></li>
<li><a href="#%E4%BA%8C%E5%B0%81%E8%A3%85jdbc%E6%93%8D%E4%BD%9Chive%E5%B7%A5%E5%85%B7%E7%B1%BB" rel="nofollow">二、封装JDBC操作Hive工具类</a></li>
<li><a href="#%E4%B8%89hivewebinterface-hive%E7%9A%84web%E9%A1%B5%E9%9D%A2%E6%8E%A5%E5%8F%A3" rel="nofollow">三、HiveWebInterface - Hive的Web页面接口</a><ul>
<li><ul>
<li><a href="#hwi%E6%BA%90%E7%A0%81%E6%89%93%E5%8C%85%E9%85%8D%E7%BD%AE" rel="nofollow">HWI源码打包配置</a></li>
<li><a href="#hwi%E9%85%8D%E7%BD%AE" rel="nofollow">HWI配置</a></li>
<li><a href="#hwi%E4%BD%BF%E7%94%A8" rel="nofollow">HWI使用</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E5%9B%9Bjavaweb%E7%BB%93%E5%90%88hive%E4%B8%80" rel="nofollow">四、JavaWeb结合Hive(一)</a><ul>
<li><ul>
<li><a href="#%E6%B3%A8%E6%84%8F%E7%82%B9" rel="nofollow">注意点</a></li>
<li><a href="#%E5%87%86%E5%A4%87%E5%B7%A5%E4%BD%9C" rel="nofollow">准备工作</a></li>
<li><a href="#%E7%BC%96%E5%86%99%E4%BB%A3%E7%A0%81" rel="nofollow">编写代码</a></li>
<li><a href="#%E8%BF%90%E8%A1%8C%E7%BB%93%E6%9E%9C" rel="nofollow">运行结果</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</div>




<h3 id="一hive-sql语句补充">一、Hive Sql语句补充</h3>

<p>通过创建中间表来存储查询出的信息</p>

<ul>
<li><blockquote>
  <p>use test;</p>
  
  <p>show tables;</p>
</blockquote>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536836805291.png" alt="1536836805291" title=""></p>

<blockquote>
  <p>select * from make limit 10;</p>
</blockquote>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536836827766.png" alt="1536836827766" title=""></p>

<blockquote>
  <p>create table t1 as select * from make limit 10;</p>
</blockquote>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536819857138.png" alt="1536819857138" title=""></p>

<blockquote>
  <p>select * from t1;</p>
</blockquote></li>
</ul>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536819864785.png" alt="1536819864785" title=""></p>

<ul>
<li><blockquote>
  <p>create table t2 as</p>
  
  <p>select floor(p.age/10) as age,sum(o.retail) as sum</p>
  
  <p>from orders o left join purchaser p</p>
  
  <p>on o.puechaserId = p.id</p>
  
  <p>group by floor(p.age/10);</p>
</blockquote>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536819885162.png" alt="1536819885162" title=""></p>

<blockquote>
  <p>select * from t2;</p>
</blockquote>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536819894947.png" alt="1536819894947" title=""></p></li>
</ul>



<h3 id="二封装jdbc操作hive工具类">二、封装JDBC操作Hive工具类</h3>

<p>在<a href="https://blog.csdn.net/cry970795248/article/details/82668597#%E5%9B%9Bhivejdbc%E6%93%8D%E4%BD%9Chive" rel="nofollow">HiveJDBC操作Hive</a>的基础上封装工具类</p>

<p>HiveUtil.java</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.sql.Connection;
<span class="hljs-keyword">import</span> java.sql.DriverManager;
<span class="hljs-keyword">import</span> java.sql.ResultSet;
<span class="hljs-keyword">import</span> java.sql.SQLException;
<span class="hljs-keyword">import</span> java.sql.Statement;
<span class="hljs-keyword">import</span> java.util.ArrayList;
<span class="hljs-keyword">import</span> java.util.List;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HiveUtil</span> {</span>

    <span class="hljs-keyword">private</span> Statement statement = <span class="hljs-keyword">null</span>;

    <span class="hljs-keyword">public</span> <span class="hljs-title">HiveUtil</span>() {
        open();
    }

    <span class="hljs-keyword">static</span> {
        <span class="hljs-comment">// 1.加载驱动</span>
        <span class="hljs-keyword">try</span> {
            Class.forName(<span class="hljs-string">"org.apache.hive.jdbc.HiveDriver"</span>);
        } <span class="hljs-keyword">catch</span> (ClassNotFoundException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
    }

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">void</span> <span class="hljs-title">open</span>() {
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">// 2.打开连接</span>
            Connection connection = DriverManager.getConnection(<span class="hljs-string">"jdbc:hive2://SZ01:10010/test"</span>);
            <span class="hljs-comment">// 3.获得操作对象</span>
            statement = connection.createStatement();
        } <span class="hljs-keyword">catch</span> (Exception e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
    }
    <span class="hljs-javadoc">/**
     * 创建数据库 - 用户注册时调用
     *<span class="hljs-javadoctag"> @param</span> databaseName 根据用户标识生成的数据库名称
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createDatabase</span>(String databaseName) {
        <span class="hljs-keyword">try</span> {
            statement.execute(<span class="hljs-string">"create database "</span> + databaseName);
        } <span class="hljs-keyword">catch</span> (SQLException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
    }
    <span class="hljs-javadoc">/**
     * 切换数据库 - 只对当前会话有效
     *<span class="hljs-javadoctag"> @param</span> databaseName 目标数据库名称
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">changeDatabase</span>(String databaseName) {
        <span class="hljs-keyword">try</span> {
            statement.execute(<span class="hljs-string">"use "</span> + databaseName);
        } <span class="hljs-keyword">catch</span> (SQLException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
    }
    <span class="hljs-javadoc">/**
     * 获得当前数据库中的数据列表 - 注意切换数据库
     *<span class="hljs-javadoctag"> @return</span> 数据表名称的集合
     */</span>
    <span class="hljs-keyword">public</span> List&lt;String&gt; <span class="hljs-title">getTableList</span>() {
        List&lt;String&gt; list = <span class="hljs-keyword">new</span> ArrayList&lt;&gt;();
        <span class="hljs-keyword">try</span> {
            ResultSet rs = statement.executeQuery(<span class="hljs-string">"show tables"</span>);
            <span class="hljs-keyword">while</span>(rs.next()) {
                list.add(rs.getString(<span class="hljs-number">1</span>));
            }
        } <span class="hljs-keyword">catch</span> (SQLException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> list;
    }
    <span class="hljs-javadoc">/**
     * 获得数据表的简要信息
     *<span class="hljs-javadoctag"> @param</span> tableName 数据表名称
     *<span class="hljs-javadoctag"> @return</span> 列名及列的数据类型
     */</span>
    <span class="hljs-keyword">public</span> List&lt;String&gt; <span class="hljs-title">descTable</span>(String tableName){
        List&lt;String&gt; list = <span class="hljs-keyword">new</span> ArrayList&lt;&gt;();
        <span class="hljs-keyword">try</span> {
            ResultSet rs = statement.executeQuery(<span class="hljs-string">"desc "</span> + tableName);
            <span class="hljs-keyword">while</span>(rs.next()) {
                list.add(rs.getString(<span class="hljs-number">1</span>) + <span class="hljs-string">"\t"</span> + rs.getString(<span class="hljs-number">2</span>));
            }
        } <span class="hljs-keyword">catch</span> (SQLException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> list;
    }
    <span class="hljs-javadoc">/**
     * 获取数据表前十条的预览数据
     *<span class="hljs-javadoctag"> @param</span> tableName 数据表名称
     *<span class="hljs-javadoctag"> @return</span> 数据表预览数据
     */</span>
    <span class="hljs-keyword">public</span> List&lt;String&gt; <span class="hljs-title">tableMsg</span>(String tableName){
        <span class="hljs-keyword">int</span> tableSize = descTable(tableName).size();
        List&lt;String&gt; list = <span class="hljs-keyword">new</span> ArrayList&lt;&gt;();
        <span class="hljs-keyword">try</span> {
            ResultSet rs = statement.executeQuery(<span class="hljs-string">"select * from "</span>+ tableName +<span class="hljs-string">" limit 10"</span>);
            <span class="hljs-keyword">while</span>(rs.next()) {
                String a = <span class="hljs-string">""</span>;
                <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i = <span class="hljs-number">1</span>; i &lt;= tableSize; i++) {
                    a += rs.getString(i) + <span class="hljs-string">"\t"</span>;
                }
                list.add(a);
            }
        } <span class="hljs-keyword">catch</span> (SQLException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> list;
    }
    <span class="hljs-javadoc">/**
     * 获得查询sql执行后的返回结果
     *<span class="hljs-javadoctag"> @param</span> sql 用户自定义sql
     *<span class="hljs-javadoctag"> @return</span> sql执行结果集中的所有数据
     */</span>
    <span class="hljs-keyword">public</span> List&lt;String&gt; <span class="hljs-title">getResultData</span>(String sql){
        List&lt;String&gt; list = <span class="hljs-keyword">new</span> ArrayList&lt;&gt;();
        <span class="hljs-comment">// 生成一个对于当前流程唯一的中间表名称</span>
        <span class="hljs-comment">// 如果流程会反复执行则先删除该表再创建</span>
        String tableName = <span class="hljs-string">"data_flow"</span>;
        sql = <span class="hljs-string">"create table "</span> + tableName + <span class="hljs-string">" as "</span> + sql;
        <span class="hljs-keyword">try</span> {
            <span class="hljs-comment">// 执行查询语句，同时使用一个表进行记录</span>
            statement.execute(sql);
            <span class="hljs-comment">// 获得中间表的列信息 - 取决于用户执行sql的结果集结构</span>
            <span class="hljs-keyword">int</span> tableSize = descTable(tableName).size();
            ResultSet rs = statement.executeQuery(<span class="hljs-string">"select * from "</span> + tableName);
            <span class="hljs-keyword">while</span>(rs.next()) {
                String a = <span class="hljs-string">""</span>;
                <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i = <span class="hljs-number">1</span>; i &lt;= tableSize; i++) {
                    a += rs.getString(i) + <span class="hljs-string">"\t"</span>;
                }
                list.add(a);
            }
        } <span class="hljs-keyword">catch</span> (SQLException e) {
            <span class="hljs-comment">// TODO 自动生成的 catch 块</span>
            e.printStackTrace();
        }
        <span class="hljs-keyword">return</span> list;
    }
}</code></pre>

<p>HiveUtilTest.java</p>



<pre class="prettyprint"><code class=" hljs cs">import java.util.List;

import com.cry.hive.util.HiveUtil;

<span class="hljs-keyword">public</span> <span class="hljs-keyword">class</span> HiveUtilTest {
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) {
        <span class="hljs-keyword">int</span> userId = <span class="hljs-number">1</span>;
        HiveUtil hiveUtil = <span class="hljs-keyword">new</span> HiveUtil();
        <span class="hljs-comment">// 创建库</span>
        hiveUtil.createDatabase(<span class="hljs-string">"user"</span> + userId);
        <span class="hljs-comment">// 切换库</span>
        hiveUtil.changeDatabase(<span class="hljs-string">"test"</span>);
        <span class="hljs-comment">// 获取当前库中的表列表</span>
        List&lt;String&gt; tables = hiveUtil.getTableList();
        <span class="hljs-keyword">for</span> (String table : tables) {
            System.<span class="hljs-keyword">out</span>.println(table);
            <span class="hljs-comment">// 获取表结构信息</span>
            List&lt;String&gt; columns = hiveUtil.descTable(table);
            <span class="hljs-keyword">for</span> (String <span class="hljs-keyword">string</span> : columns) {
                System.<span class="hljs-keyword">out</span>.println(<span class="hljs-keyword">string</span>);
            }
            <span class="hljs-comment">// 获取表内数据信息</span>
            List&lt;String&gt; tableMsg = hiveUtil.tableMsg(table);
            <span class="hljs-keyword">for</span> (String <span class="hljs-keyword">string</span> : tableMsg) {
                System.<span class="hljs-keyword">out</span>.println(<span class="hljs-keyword">string</span>);
            }
            System.<span class="hljs-keyword">out</span>.println();
        }
        <span class="hljs-comment">// 执行sql语句并获得返回结果</span>
        String sql = <span class="hljs-string">"SELECT t.typeName,SUM(o.retail) as sum FROM orders o LEFT JOIN type t ON o.typeId = t.id GROUP BY o.typeId,t.typeName"</span>;
        List&lt;String&gt; list = hiveUtil.getResultData(sql);
        <span class="hljs-keyword">for</span> (String line : list) {
            System.<span class="hljs-keyword">out</span>.println(line);
        }
    }
}</code></pre>



<h3 id="三hivewebinterface-hive的web页面接口">三、HiveWebInterface - Hive的Web页面接口</h3>

<p>在<a href="https://blog.csdn.net/cry970795248/article/details/82632114#hive" rel="nofollow">Hive架构图</a>中，CLI和JDBC都已经使用过，还有一种操作Hive的方式WebGUI，即hwi</p>



<h5 id="hwi源码打包配置">HWI源码打包配置</h5>

<p>hwi依赖hive-hwi-1.2.2.war，但是hive安装包目录的lib目录下没有提供，所以需要下载hive的源码包自行打包。</p>

<ol>
<li><p>从apache官网下载apache-hive-1.2.2-src.tar.gz</p></li>
<li><p>通过Xftp将源码包上传至CentOS</p></li>
<li><p>解压缩</p>

<blockquote>
  <p>tar -zxvf apache-hive-1.2.2-src.tar.gz</p>
</blockquote></li>
<li><p>进入解压缩出来的文件夹中hwi/web目录下</p>

<blockquote>
  <p>cd apache-hive-1.2.2-src/hwi/web</p>
</blockquote>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536822896066.png" alt="1536822896066" title=""></p></li>
<li><p>使用命令打包web文件夹</p>

<blockquote>
  <p>jar cvfM hive-hwi-1.2.2.war -C web .</p>
</blockquote></li>
<li><p>将打包完成的war文件移动至hive安装目录的lib目录下</p>

<blockquote>
  <p>mv hive-hwi-1.2.2.war $HIVE_HOME/lib</p>
</blockquote></li>
</ol>



<h5 id="hwi配置">HWI配置</h5>

<p>移动完成后，尝试启动hwi服务，此时启动报错，原因是为在hive-site.xml中没有指定hwi目录</p>

<blockquote>
  <p>hive –service hwi</p>
</blockquote>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536823211313.png" alt="1536823211313" title=""></p>

<p>此时修改配置文件hive-site.xml，添加以下内容</p>

<blockquote>
  <p>  </p>
  
  <p>hive.hwi.war.file  </p>
  
  <p>​ lib/hive-hwi-1.2.2.war</p>
  
  <p></p>
</blockquote>

<p>注：apache官网中建议配置三项，其中两项默认即可，一是主机0.0.0.0，二是端口号9999，第三项hive.hwi.war.file需指定</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536823307212.png" alt="1536823307212" title=""></p>

<p>再次尝试启动服务</p>

<blockquote>
  <p>hive –service hwi</p>
</blockquote>

<p>发现依旧此时报错，原因是hive缺少jsp依赖</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536823402476.png" alt="1536823402476" title=""></p>

<p>此时可以访问9999端口，但是报错</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536823446211.png" alt="1536823446211" title=""></p>

<p>缺少的jar包可通过Maven下载，<a href="http://mvnrepository.com/" rel="nofollow">点击进入</a></p>

<p>搜索jasper，下载Jasper Runtime和Jasper Compiler</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536823600090.png" alt="1536823600090" title=""></p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536823694742.png" alt="1536823694742" title=""></p>

<p>还缺少commons-el的jar包，一起下载即可</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536823832124.png" alt="1536823832124" title=""></p>

<p>还缺少tools.jar，此jar包在jdk的lib中</p>

<p>总共需要的jar包如下图</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536838201277.png" alt="1536838201277" title=""></p>

<p>将这四个jar包通过Xftp上传至CentOS的$HIVE_HOME/lib目录下</p>

<p>重启hwi服务，即可正常使用</p>

<blockquote>
  <p>Ctrl + c</p>
  
  <p>hive –service hwi</p>
</blockquote>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536823926585.png" alt="1536823926585" title=""></p>

<p>hive –service hwi命令需要停在当前窗口会话，如果ctrl+c或者关闭会话，服务则无法使用，此时执行以下命令</p>

<blockquote>
  <p>nohup hive –service hwi &gt; /dev/null 2&gt;&amp;1 &amp; </p>
</blockquote>

<p>此命令的含义是开启后台进程并且丢弃nohup.out文件</p>



<h5 id="hwi使用">HWI使用</h5>

<ul>
<li><p>通过Browse Schema可以浏览库中的信息</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536824025792.png" alt="1536824025792" title=""></p></li>
<li><p>通过Create Session创建一个会话，可以执行hql语句</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536824973429.png" alt="1536824973429" title=""></p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536825058985.png" alt="1536825058985" title=""></p>

<p>任务完成后，查看结果</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536825128514.png" alt="1536825128514" title=""></p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536825138476.png" alt="1536825138476" title=""></p>

<p>也可以在指定的文件中查看结果</p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536825181665.png" alt="1536825181665" title=""></p></li>
</ul>



<h3 id="四javaweb结合hive一">四、JavaWeb结合Hive(一)</h3>



<h5 id="注意点">注意点</h5>

<ol>
<li><p>在Web中操作Hive，需要给每个用户分配独有的空间，否则容易发生冲突，而且会把数据库内弄得杂乱，不好管理</p></li>
<li><p>用户注册，相当于在HDFS创建相应的目录(MapReduce)，在Hive中新建相应的库</p></li>
<li><p>数据库的切换有两种方式：</p>

<p>(1)打开连接时指定数据库</p>

<p>(2)使用前切换至相应的数据库下</p></li>
<li><p>数据流管理：数据源在经过多次处理后才能得出最终结果，所以在数据库中需专门建立数据库存放在操作过程中生成的中间表，避免数据库中杂乱无章</p></li>
</ol>



<h5 id="准备工作">准备工作</h5>

<ul>
<li>创建Maven项目，注意需要在pom.xml中配置hadoop、hive、json、jstl等依赖配置</li>
<li>创建普通java项目，则需要手动导入hadoop、hive、json、jstl等等依赖包</li>
<li>导入前文编写的工具类HiveUtil.java</li>
<li>导入jquery</li>
</ul>



<h5 id="编写代码">编写代码</h5>

<p>目标功能：在页面中显示一个按钮，点击后展示数据库中的所有表</p>

<p>datasource.jsp</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="vbscript">&lt;%@ page language=<span class="hljs-string">"java"</span> contentType=<span class="hljs-string">"text/html; charset=UTF-8"</span>
    pageEncoding=<span class="hljs-string">"UTF-8"</span>%&gt;</span>
<span class="hljs-doctype">&lt;!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd"&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">html</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">head</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">meta</span> <span class="hljs-attribute">http-equiv</span>=<span class="hljs-value">"Content-Type"</span> <span class="hljs-attribute">content</span>=<span class="hljs-value">"text/html; charset=UTF-8"</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">title</span>&gt;</span>Insert title here<span class="hljs-tag">&lt;/<span class="hljs-title">title</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">script</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text/javascript"</span> <span class="hljs-attribute">src</span>=<span class="hljs-value">"js/jquery-1.7.1.min.js"</span>&gt;</span><span class="javascript"></span><span class="hljs-tag">&lt;/<span class="hljs-title">script</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">script</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"text/javascript"</span> <span class="hljs-attribute">src</span>=<span class="hljs-value">"js/datasource.js"</span>&gt;</span><span class="javascript"></span><span class="hljs-tag">&lt;/<span class="hljs-title">script</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">head</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">body</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">input</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"showTables"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"button"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"显示数据表"</span> /&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">div</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"tableList"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">div</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">div</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"tableInfo"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">div</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">div</span> <span class="hljs-attribute">class</span>=<span class="hljs-value">"tableData"</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">div</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">body</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">html</span>&gt;</span></code></pre>

<p>datasource.js</p>



<pre class="prettyprint"><code class=" hljs javascript">$(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span> {</span>
    $(<span class="hljs-string">".showTables"</span>).click(<span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">()</span> {</span>
        <span class="hljs-keyword">var</span> databaseName = <span class="hljs-string">"test"</span>;
        $.ajax({
            url : <span class="hljs-string">"DataSourceServlet"</span>,
            type : <span class="hljs-string">"post"</span>,
            data : {
                databaseName : databaseName
            },
            dataType : <span class="hljs-string">"json"</span>,
            success : <span class="hljs-function"><span class="hljs-keyword">function</span><span class="hljs-params">(data)</span> {</span>
                <span class="hljs-keyword">var</span> content = $(<span class="hljs-string">".tableList"</span>);
                <span class="hljs-keyword">for</span> (index <span class="hljs-keyword">in</span> data) {
                    <span class="hljs-keyword">var</span> tableName = data[index];
                    content.append(<span class="hljs-string">"&lt;div&gt;&lt;span&gt;"</span>+ tableName +<span class="hljs-string">"&lt;/span&gt;&lt;input type='button' value='预览数据' /&gt;&lt;input type='button' value='结构信息' /&gt;&lt;/div&gt;"</span>);
                }
            }
        })
    })
})</code></pre>

<p>DataSourceServlet.java</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.io.PrintWriter;
<span class="hljs-keyword">import</span> java.util.List;

<span class="hljs-keyword">import</span> javax.servlet.ServletException;
<span class="hljs-keyword">import</span> javax.servlet.annotation.WebServlet;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServlet;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletRequest;
<span class="hljs-keyword">import</span> javax.servlet.http.HttpServletResponse;

<span class="hljs-keyword">import</span> com.sand.util.HiveUtil;

<span class="hljs-keyword">import</span> net.sf.json.JSONArray;
<span class="hljs-annotation">@WebServlet</span>(<span class="hljs-string">"/DataSourceServlet"</span>)
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">DataSourceServlet</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">HttpServlet</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> serialVersionUID = <span class="hljs-number">1</span>L;

    <span class="hljs-keyword">public</span> <span class="hljs-title">DataSourceServlet</span>() {
        <span class="hljs-keyword">super</span>();
        <span class="hljs-comment">// TODO Auto-generated constructor stub</span>
    }

    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doGet</span>(HttpServletRequest request, HttpServletResponse response) <span class="hljs-keyword">throws</span> ServletException, IOException {
        response.setCharacterEncoding(<span class="hljs-string">"UTF-8"</span>);
        PrintWriter out = response.getWriter();
        <span class="hljs-comment">// 获取数据库名</span>
        String databaseName = request.getParameter(<span class="hljs-string">"databaseName"</span>);
        HiveUtil hiveUtil = <span class="hljs-keyword">new</span> HiveUtil();
        <span class="hljs-comment">// 通过数据库名切换数据库</span>
        hiveUtil.changeDatabase(databaseName);
        <span class="hljs-comment">// 获得表集合</span>
        List&lt;String&gt; list = hiveUtil.getTableList();
        <span class="hljs-keyword">for</span> (String line : list) {
            System.out.println(line);
        }
        <span class="hljs-comment">// 将结果作为json数组传回到页面</span>
        String result = JSONArray.fromObject(list).toString();
        out.print(result);
        out.close();
    }

    <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doPost</span>(HttpServletRequest request, HttpServletResponse response) <span class="hljs-keyword">throws</span> ServletException, IOException {
        doGet(request, response);
    }

}</code></pre>



<h5 id="运行结果">运行结果</h5>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536839283655.png" alt="1536839283655" title=""></p>

<p><img src="https://day13-1253629415.cos.ap-guangzhou.myqcloud.com/1536839364239.png" alt="1536839364239" title=""></p>

<p>待实现功能：点击预览数据能获得表内的前十条数据，点击结构信息能获得表结构信息，并且都是在当前页面展示</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
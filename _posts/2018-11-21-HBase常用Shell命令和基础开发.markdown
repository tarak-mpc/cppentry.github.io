---
layout:     post
title:      HBase常用Shell命令和基础开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_38265137/article/details/80663549				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hbase常用shell命令">HBase常用Shell命令</h1>



<h2 id="hbase中用create命令创建表">HBase中用create命令创建表：</h2>



<pre class="prettyprint"><code class="language-shell hljs sql"><span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'Student'</span>,<span class="hljs-string">'Num'</span>,<span class="hljs-string">'Name'</span>,<span class="hljs-string">'Sex'</span>,<span class="hljs-string">'Age'</span></span></code></pre>

<p>运行结果如下：</p>



<pre class="prettyprint"><code class="language-shell hljs coffeescript">hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'Student'</span>,<span class="hljs-string">'Num'</span>,<span class="hljs-string">'Name'</span>,<span class="hljs-string">'Sex'</span>,<span class="hljs-string">'Age'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">2.4910</span> seconds<span class="hljs-function">
=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - Student
hbase(main):<span class="hljs-number">009</span>:<span class="hljs-number">0</span>&gt; </code></pre>

<p>此时，即创建了一个’Student’表，属性有：Num,Name,Sex,Age。因为HBase的表中会有一个系统默认的属性作为行键，无需自行创建，默认为put命令操作中表名后第一个数据。创建完“student”表后，可通过describe命令查看“student”表的基本信息。</p>



<h2 id="hbase数据库基本操作">HBase数据库基本操作：</h2>



<h3 id="添加数据-put">添加数据-put：</h3>

<p>HBase中用put命令添加数据，注意：一次只能为一个表的一行数据的一个列，也就是一个单元格添加一个数据，所以直接用shell命令插入数据效率很低，在实际应用中，一般都是利用编程操作数据。</p>

<p>在添加数据时，HBase会自动为添加的数据添加一个时间戳，故在需要修改数据时，只需直接添加数据，HBase即会生成一个新的版本，从而完成“改”操作，旧的版本依旧保留，系统会定时回收垃圾数据，只留下最新的几个版本，保存的版本数可以在创建表的时候指定。</p>

<p>当运行命令：put ‘Student’,’1001’,’ZhangSan’,male’,’23’时，即为Student表添加了学号为1001，姓名为ZhangSan，性别男，年龄23的一条数据。行键为1001.</p>



<pre class="prettyprint"><code class="language-shell hljs livecodeserver"><span class="hljs-built_in">put</span> <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'ZhangSan'</span>,male<span class="hljs-string">','</span><span class="hljs-number">23</span><span class="hljs-string">'</span></code></pre>



<h3 id="删除数据">删除数据：</h3>

<p>在HBase中用delete以及deleteall命令进行删除数据操作，它们的区别是：1. delete用于删除一个数据，是put的反向操作；2. deleteall操作用于删除一行数据。</p>



<pre class="prettyprint"><code class="language-shell hljs vala">delete <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'Age'</span>
<span class="hljs-preprocessor">#删除学号为1001的年龄。</span>

delete <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>
<span class="hljs-preprocessor">#删除学号为1001的所示数据。</span></code></pre>



<h3 id="查看数据">查看数据：</h3>

<p>HBase中有两个用于查看数据的命令：1. get命令，用于查看表的某一行数据；2. scan命令用于查看某个表的全部数据</p>



<pre class="prettyprint"><code class="language-shell hljs vala"><span class="hljs-keyword">get</span> <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>
<span class="hljs-preprocessor">#查看学生学号为1001的信息</span>

scan <span class="hljs-string">'student'</span>
<span class="hljs-preprocessor">#查看整个Student表的信息。</span></code></pre>



<h3 id="删除表">删除表：</h3>

<p>删除表有两步，第一步先让该表不可用，第二步删除表。</p>



<pre class="prettyprint"><code class="language-shell hljs vala">disable <span class="hljs-string">'Student'</span>
<span class="hljs-preprocessor">#先禁用表</span>

drop <span class="hljs-string">'Student'</span>
<span class="hljs-preprocessor">#删除表</span></code></pre>



<h2 id="查询表历史数据">查询表历史数据：</h2>

<p>查询表的历史版本，需要两步。 <br>
1、在创建表的时候，指定保存的版本数（假设指定为5）</p>



<pre class="prettyprint"><code class="language-shell hljs php">create <span class="hljs-string">'Student'</span>,{NAME=&gt;<span class="hljs-string">'username'</span>,VERSIONS=&gt;<span class="hljs-number">5</span>}</code></pre>

<p>2、插入数据然后更新数据，使其产生历史版本数据，注意：这里插入数据和更新数据都是用put命令</p>



<pre class="prettyprint"><code class="language-shell hljs livecodeserver"><span class="hljs-built_in">put</span> <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'username'</span>,<span class="hljs-string">'Mary'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'username'</span>,<span class="hljs-string">'Mary1'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'username'</span>,<span class="hljs-string">'Mary2'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>,<span class="hljs-string">'username'</span>,<span class="hljs-string">'Mary3'</span></code></pre>

<p>3、查询时，指定查询的历史版本数。默认会查询出最新的数据。（有效取值为1到5）</p>



<pre class="prettyprint"><code class="language-shell hljs php"> get <span class="hljs-string">'Student'</span>,<span class="hljs-string">'1001'</span>,{COLUMN=&gt;<span class="hljs-string">'username'</span>,VERSIONS=&gt;<span class="hljs-number">5</span>}</code></pre>



<h2 id="退出hbase数据库表操作">退出HBase数据库表操作：</h2>

<p><strong>最后退出数据库操作，输入exit命令即可退出，</strong>注意：这里退出HBase数据库是退出对数据库表的操作，而不是停止启动HBase数据库后台运行。</p>



<h1 id="hbase-api详解">HBase API详解</h1>

<blockquote>
  <p>写HBase程序，需导入hbase安装目录中的lib文件中的所有jar包就行。</p>
</blockquote>



<h2 id="hbase常用api介绍">HBase常用API介绍：</h2>



<h3 id="admin">Admin:</h3>

<p>HBase的管理API。从connecgetadmin（）获取一个实例，然后调用close（）。</p>

<p>Admin可用于创建、删除、列出、启用和禁用表、添加和删除表列家庭和其他行政操作。</p>

<p>Admin的常用方法：</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-comment">//判断表是否存在</span>
<span class="hljs-keyword">boolean</span> tableExists(TableName tableName)
             <span class="hljs-keyword">throws</span> IOException

<span class="hljs-comment">//这个对象使用的连接。</span>
Connection getConnection()

<span class="hljs-comment">//列出与给定模式匹配的所有用户空间表。</span>
HTableDescriptor[] listTables(Pattern pattern)

  <span class="hljs-comment">//列出所有用户表的空间</span>
 TableName[] listTableNames(String regex)

<span class="hljs-comment">//获得表描述符的方法</span>
HTableDescriptor getTableDescriptor(TableName tableName)

<span class="hljs-comment">//创建一个新表。同步操作</span>
<span class="hljs-keyword">void</span> createTable(HTableDescriptor desc)

<span class="hljs-comment">//删除一个表。同步操作。</span>
<span class="hljs-keyword">void</span> deleteTable(TableName tableName)</code></pre>



<h3 id="connection">Connection:</h3>

<p>一个集群连接，将较低层次的个人连接封装到实际的服务器上，并连接到zookeeper。连接通过ConnectionFactory类实例化。连接的生命周期由调用者管理，后者必须close()连接以释放资源。</p>

<p>连接对象包含找到master的逻辑，定位集群上的区域，保留一个位置的缓存，然后知道如何在移动后重新校准。与服务器、元缓存、zookeeper连接等的个人连接都是由Table和Admin从该连接获得的管理实例共享的.</p>

<p>连接创建是一个重量级的操作。连接实现是线程安全的，因此客户端可以创建一次连接，并与不同的线程共享它。另一方面，<strong>Admin</strong>和Table实例是轻量级的，并且不是线程安全的。通常，每个客户端应用程序的单个连接被实例化，并且每个线程都将获得它自己的表实例。不建议对Table和Admin进行缓存或合用。</p>

<p>Connection的常用方法：</p>



<pre class="prettyprint"><code class="language-java hljs ">  <span class="hljs-keyword">void</span> close()<span class="hljs-comment">//关闭连接</span>
  Admin getAdmin()  <span class="hljs-comment">//检索管理实现来管理HBase集群。</span>

  <span class="hljs-comment">//这个连接实例正在使用的配置实例</span>
  org.apache.hadoop.conf.Configuration getConfiguration()  

  <span class="hljs-comment">//检索表实现来访问表。返回的表不是线程安全的，应该为每个使用线程创建一个新的实例。这是一个轻量级的操作，既不需要也不需要对返回的表进行池或缓存。</span>
  Table getTable(TableName tableName)

  <span class="hljs-comment">//检索管理实现来管理HBase集群。返回的管理员不能保证是线程安全的。每一个使用线程都应该创建一个新的实例。这是一个轻量级操作。不建议对返回的管理员进行池或缓存。</span>
  Admin getAdmin()</code></pre>



<h3 id="table">Table:</h3>

<p>用于与单个HBase表进行通信。从连接中获取一个实例，然后调用close（）.</p>

<p>表格可用于从表中获取、放置、删除或扫描数据。</p>



<h3 id="htabledescriptor">HTableDescriptor：</h3>

<p>HTableDescriptor包含关于HBase表的详细信息，例如所有列家族的描述符，是表a目录表，-根-或HBase:meta，如果表只读取，memstore的最大大小，当区域拆分时，与之关联的协处理器等等。</p>



<h2 id="hbase编程示例">HBase编程示例：</h2>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.*;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.*;
<span class="hljs-keyword">import</span> java.io.IOException;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">ExampleForHbase</span>{</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> Configuration configuration;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> Connection connection;
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> Admin admin;

    <span class="hljs-comment">//主函数中的语句请逐句执行，只需删除其前的//即可，如：执行insertRow时请将其他语句注释</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args)<span class="hljs-keyword">throws</span> IOException{
        <span class="hljs-comment">//创建一个表，表名为Score，列族为sname,course</span>
        createTable(<span class="hljs-string">"Score"</span>,<span class="hljs-keyword">new</span> String[]{<span class="hljs-string">"sname"</span>,<span class="hljs-string">"course"</span>});

        <span class="hljs-comment">//在Score表中插入一条数据，其行键为95001,sname为Mary（因为sname列族下没有子列所以第四个参数为空）</span>
        <span class="hljs-comment">//等价命令：put 'Score','95001','sname','Mary'</span>
        <span class="hljs-comment">//insertRow("Score", "95001", "sname", "", "Mary");</span>
        <span class="hljs-comment">//在Score表中插入一条数据，其行键为95001,course:Math为88（course为列族，Math为course下的子列）</span>
        <span class="hljs-comment">//等价命令：put 'Score','95001','score:Math','88'</span>
        <span class="hljs-comment">//insertRow("Score", "95001", "course", "Math", "88");</span>
        <span class="hljs-comment">//在Score表中插入一条数据，其行键为95001,course:English为85（course为列族，English为course下的子列）</span>
        <span class="hljs-comment">//等价命令：put 'Score','95001','score:English','85'</span>
        <span class="hljs-comment">//insertRow("Score", "95001", "course", "English", "85");</span>

        <span class="hljs-comment">//1、删除Score表中指定列数据，其行键为95001,列族为course，列为Math</span>
        <span class="hljs-comment">//执行这句代码前请deleteRow方法的定义中，将删除指定列数据的代码取消注释注释，将删除制定列族的代码注释</span>
        <span class="hljs-comment">//等价命令：delete 'Score','95001','score:Math'</span>
        <span class="hljs-comment">//deleteRow("Score", "95001", "course", "Math");</span>

        <span class="hljs-comment">//2、删除Score表中指定列族数据，其行键为95001,列族为course（95001的Math和English的值都会被删除）</span>
        <span class="hljs-comment">//执行这句代码前请deleteRow方法的定义中，将删除指定列数据的代码注释，将删除制定列族的代码取消注释</span>
        <span class="hljs-comment">//等价命令：delete 'Score','95001','score'</span>
        <span class="hljs-comment">//deleteRow("Score", "95001", "course", "");</span>

        <span class="hljs-comment">//3、删除Score表中指定行数据，其行键为95001</span>
        <span class="hljs-comment">//执行这句代码前请deleteRow方法的定义中，将删除指定列数据的代码注释，以及将删除制定列族的代码注释</span>
        <span class="hljs-comment">//等价命令：deleteall 'Score','95001'</span>
        <span class="hljs-comment">//deleteRow("Score", "95001", "", "");</span>

        <span class="hljs-comment">//查询Score表中，行键为95001，列族为course，列为Math的值</span>
        <span class="hljs-comment">//getData("Score", "95001", "course", "Math");</span>
        <span class="hljs-comment">//查询Score表中，行键为95001，列族为sname的值（因为sname列族下没有子列所以第四个参数为空）</span>
        <span class="hljs-comment">//getData("Score", "95001", "sname", "");</span>

        <span class="hljs-comment">//删除Score表</span>
        <span class="hljs-comment">//deleteTable("Score");</span>
    }

    <span class="hljs-comment">//建立连接</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">init</span>(){
        configuration  = HBaseConfiguration.create();
        configuration.set(<span class="hljs-string">"hbase.rootdir"</span>,<span class="hljs-string">"hdfs://localhost:9000/hbase"</span>);
        <span class="hljs-keyword">try</span>{
            connection = ConnectionFactory.createConnection(configuration);
            admin = connection.getAdmin();
        }<span class="hljs-keyword">catch</span> (IOException e){
            e.printStackTrace();
        }
    }
    <span class="hljs-comment">//关闭连接</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">close</span>(){
        <span class="hljs-keyword">try</span>{
            <span class="hljs-keyword">if</span>(admin != <span class="hljs-keyword">null</span>){
                admin.close();
            }
            <span class="hljs-keyword">if</span>(<span class="hljs-keyword">null</span> != connection){
                connection.close();
            }
        }<span class="hljs-keyword">catch</span> (IOException e){
            e.printStackTrace();
        }
    }

    <span class="hljs-javadoc">/**
     * 建表。HBase的表中会有一个系统默认的属性作为主键，主键无需自行创建，默认为put命令操作中表名后第一个数据，因此此处无需创建id列
     *<span class="hljs-javadoctag"> @param</span> myTableName 表名
     *<span class="hljs-javadoctag"> @param</span> colFamily 列族名
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createTable</span>(String myTableName,String[] colFamily) <span class="hljs-keyword">throws</span> IOException {

        init();
        TableName tableName = TableName.valueOf(myTableName);

        <span class="hljs-keyword">if</span>(admin.tableExists(tableName)){
            System.out.println(<span class="hljs-string">"talbe is exists!"</span>);
        }<span class="hljs-keyword">else</span> {
            HTableDescriptor hTableDescriptor = <span class="hljs-keyword">new</span> HTableDescriptor(tableName);
            <span class="hljs-keyword">for</span>(String str:colFamily){
                HColumnDescriptor hColumnDescriptor = <span class="hljs-keyword">new</span> HColumnDescriptor(str);
                hTableDescriptor.addFamily(hColumnDescriptor);
            }
            admin.createTable(hTableDescriptor);
            System.out.println(<span class="hljs-string">"create table success"</span>);
        }
        close();
    }
    <span class="hljs-javadoc">/**
     * 删除指定表
     *<span class="hljs-javadoctag"> @param</span> tableName 表名
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteTable</span>(String tableName) <span class="hljs-keyword">throws</span> IOException {
        init();
        TableName tn = TableName.valueOf(tableName);
        <span class="hljs-keyword">if</span> (admin.tableExists(tn)) {
            admin.disableTable(tn);
            admin.deleteTable(tn);
        }
        close();
    }

    <span class="hljs-javadoc">/**
     * 查看已有表
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">listTables</span>() <span class="hljs-keyword">throws</span> IOException {
        init();
        HTableDescriptor hTableDescriptors[] = admin.listTables();
        <span class="hljs-keyword">for</span>(HTableDescriptor hTableDescriptor :hTableDescriptors){
            System.out.println(hTableDescriptor.getNameAsString());
        }
        close();
    }
    <span class="hljs-javadoc">/**
     * 向某一行的某一列插入数据
     *<span class="hljs-javadoctag"> @param</span> tableName 表名
     *<span class="hljs-javadoctag"> @param</span> rowKey 行键
     *<span class="hljs-javadoctag"> @param</span> colFamily 列族名
     *<span class="hljs-javadoctag"> @param</span> col 列名（如果其列族下没有子列，此参数可为空）
     *<span class="hljs-javadoctag"> @param</span> val 值
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">insertRow</span>(String tableName,String rowKey,String colFamily,String col,String val) <span class="hljs-keyword">throws</span> IOException {
        init();
        Table table = connection.getTable(TableName.valueOf(tableName));
        Put put = <span class="hljs-keyword">new</span> Put(rowKey.getBytes());
        put.addColumn(colFamily.getBytes(), col.getBytes(), val.getBytes());
        table.put(put);
        table.close();
        close();
    }

    <span class="hljs-javadoc">/**
     * 删除数据
     *<span class="hljs-javadoctag"> @param</span> tableName 表名
     *<span class="hljs-javadoctag"> @param</span> rowKey 行键
     *<span class="hljs-javadoctag"> @param</span> colFamily 列族名
     *<span class="hljs-javadoctag"> @param</span> col 列名
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteRow</span>(String tableName,String rowKey,String colFamily,String col) <span class="hljs-keyword">throws</span> IOException {
        init();
        Table table = connection.getTable(TableName.valueOf(tableName));
        Delete delete = <span class="hljs-keyword">new</span> Delete(rowKey.getBytes());
        <span class="hljs-comment">//删除指定列族的所有数据</span>
        <span class="hljs-comment">//delete.addFamily(colFamily.getBytes());</span>
        <span class="hljs-comment">//删除指定列的数据</span>
        <span class="hljs-comment">//delete.addColumn(colFamily.getBytes(), col.getBytes());</span>

        table.delete(delete);
        table.close();
        close();
    }
    <span class="hljs-javadoc">/**
     * 根据行键rowkey查找数据
     *<span class="hljs-javadoctag"> @param</span> tableName 表名
     *<span class="hljs-javadoctag"> @param</span> rowKey 行键
     *<span class="hljs-javadoctag"> @param</span> colFamily 列族名
     *<span class="hljs-javadoctag"> @param</span> col 列名
     *<span class="hljs-javadoctag"> @throws</span> IOException
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getData</span>(String tableName,String rowKey,String colFamily,String col)<span class="hljs-keyword">throws</span>  IOException{
        init();
        Table table = connection.getTable(TableName.valueOf(tableName));
        Get get = <span class="hljs-keyword">new</span> Get(rowKey.getBytes());
        get.addColumn(colFamily.getBytes(),col.getBytes());
        Result result = table.get(get);
        showCell(result);
        table.close();
        close();
    }
    <span class="hljs-javadoc">/**
     * 格式化输出
     *<span class="hljs-javadoctag"> @param</span> result
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">showCell</span>(Result result){
        Cell[] cells = result.rawCells();
        <span class="hljs-keyword">for</span>(Cell cell:cells){
            System.out.println(<span class="hljs-string">"RowName:"</span>+<span class="hljs-keyword">new</span> String(CellUtil.cloneRow(cell))+<span class="hljs-string">" "</span>);
            System.out.println(<span class="hljs-string">"Timetamp:"</span>+cell.getTimestamp()+<span class="hljs-string">" "</span>);
            System.out.println(<span class="hljs-string">"column Family:"</span>+<span class="hljs-keyword">new</span> String(CellUtil.cloneFamily(cell))+<span class="hljs-string">" "</span>);
            System.out.println(<span class="hljs-string">"row Name:"</span>+<span class="hljs-keyword">new</span> String(CellUtil.cloneQualifier(cell))+<span class="hljs-string">" "</span>);
            System.out.println(<span class="hljs-string">"value:"</span>+<span class="hljs-keyword">new</span> String(CellUtil.cloneValue(cell))+<span class="hljs-string">" "</span>);
        }
    }
}</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
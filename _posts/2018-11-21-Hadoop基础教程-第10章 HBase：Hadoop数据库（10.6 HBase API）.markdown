---
layout:     post
title:      Hadoop基础教程-第10章 HBase：Hadoop数据库（10.6 HBase API）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/chengyuqiang/article/details/76358783				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="第10章-hbasehadoop数据库">第10章 HBase：Hadoop数据库</h2>



<h2 id="106-hbase-api-新特性">10.6 HBase API (新特性)</h2>

<p>本节所有代码可以从<a href="https://github.com/ihadron/hbase.git" rel="nofollow">https://github.com/ihadron/hbase.git</a>下载。</p>

<hr>



<h3 id="1061-hbase-api介绍">10.6.1 HBase API介绍</h3>

<p>前面我们已经学习了通过HBase Shell命令来操作HBase，本质上是通过Java API进行操作的。所以Java API操作HBase是最直接、最原生的方式。</p>

<p><a href="https://hbase.apache.org/devapidocs/index.html" rel="nofollow">https://hbase.apache.org/devapidocs/index.html</a> <br>
<strong>（1）Configuration</strong></p>

<table>
<thead>
<tr>
  <th>返回值</th>
  <th>方法</th>
  <th>说明</th>
</tr>
</thead>
<tbody><tr>
  <td>Table</td>
  <td>getTable(TableName tableName)</td>
  <td>获取表对象</td>
</tr>
<tr>
  <td>Admin</td>
  <td>getAdmin()</td>
  <td>获取Admin对象，管理HBase集群</td>
</tr>
<tr>
  <td>void</td>
  <td>close()</td>
  <td>关闭连接</td>
</tr>
</tbody></table>


<p><strong>（2）Admin</strong> <br>
The administrative API for HBase. Obtain an instance from an Connection.getAdmin() and call close() afterwards. Admin can be used to create, drop, list, enable and disable tables, add and drop table column families and other administrative operations. <br>
HBase的管理接口。从Connection.getAdmin()获取一个实例，然后调用close()。Admin可用于创建，删除，列表，启用和禁用表，添加和删除表列列和其他管理操作。</p>

<table>
<thead>
<tr>
  <th>返回值</th>
  <th>方法</th>
  <th>说明</th>
</tr>
</thead>
<tbody><tr>
  <td>boolean</td>
  <td>tableExists(TableName tableName)</td>
  <td>判定表是否存在</td>
</tr>
<tr>
  <td>List</td>
  <td>listTableDescriptors()</td>
  <td>列出所有的用户空间的数据表</td>
</tr>
<tr>
  <td>TableName[]</td>
  <td>listTableNames()</td>
  <td>列出所有的用户空间的数据表的表名</td>
</tr>
<tr>
  <td>void</td>
  <td>createTable(TableDescriptor desc)</td>
  <td>创建一个新表</td>
</tr>
<tr>
  <td>void</td>
  <td>deleteTable(TableName tableName)</td>
  <td>删除一个表</td>
</tr>
</tbody></table>


<p>（3）</p>

<p><strong>HBase API程序设计步骤</strong> <br>
（1）创建一个Configuration 对象 <br>
（2）通过Configuration 对象的getTable方法获取Table对象 <br>
（3）执行相应的put 、get 、delete 、scan 等操作 <br>
（4）释放各种资源</p>



<h3 id="1062-windowseclipsemavenhbase">10.6.2 Windows+Eclipse+Maven+HBase</h3>

<p>（1）编辑Windows系统的hosts文件</p>

<p>C:\Windows\System32\drivers\etc\hosts</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># localhost name resolution is handled within DNS itself.</span>
<span class="hljs-preprocessor">#   127.0.0.1       localhost</span>
<span class="hljs-preprocessor">#   ::1             localhost</span>
<span class="hljs-number">192.168</span><span class="hljs-number">.80</span><span class="hljs-number">.131</span>  node1
<span class="hljs-number">192.168</span><span class="hljs-number">.80</span><span class="hljs-number">.132</span>  node2
<span class="hljs-number">192.168</span><span class="hljs-number">.80</span><span class="hljs-number">.133</span>  node3</code></pre>

<p>（2）在Windows系统下，JDK和Maven已经安装配置，具体内容请参考<a href="http://blog.csdn.net/chengyuqiang/article/details/72082149" rel="nofollow">4.1节</a>和 <a href="http://blog.csdn.net/chengyuqiang/article/details/72229796" rel="nofollow">4.2节</a>内容。 <br>
（3）打开Eclipse创建maven项目，项目名称取hhase <br>
（4）编译pom.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span> <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
    <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>cn.hadron<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hbase<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>0.0.1-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">packaging</span>&gt;</span>jar<span class="hljs-tag">&lt;/<span class="hljs-title">packaging</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">url</span>&gt;</span>http://maven.apache.org<span class="hljs-tag">&lt;/<span class="hljs-title">url</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>UTF-8<span class="hljs-tag">&lt;/<span class="hljs-title">project.build.sourceEncoding</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>junit<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>3.8.1<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>test<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-comment">&lt;!-- https://mvnrepository.com/artifact/org.apache.hbase/hbase-client --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.hbase<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>hbase-client<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.2.6<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>jdk.tools<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>jdk.tools<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">scope</span>&gt;</span>system<span class="hljs-tag">&lt;/<span class="hljs-title">scope</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">systemPath</span>&gt;</span>${JAVA_HOME}/lib/tools.jar<span class="hljs-tag">&lt;/<span class="hljs-title">systemPath</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span>
</code></pre>



<h3 id="1063-创建表">10.6.3 创建表</h3>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hadron</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.dao</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HColumnDescriptor</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HTableDescriptor</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Admin</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>
public class CreateDemo {
    public static void main(String[] args)throws Exception{
        Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://cetc/hbase"</span>)<span class="hljs-comment">;</span>
        // 设置Zookeeper,直接设置IP地址
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"node1,node2,node3"</span>)<span class="hljs-comment">;</span>
        //建立HBase连接
        Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(conf)<span class="hljs-comment">;</span>
        //表管理类
        Admin admin = connection<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>
        //定义表名
        String tablename=<span class="hljs-string">"test1"</span><span class="hljs-comment">;</span>
        TableName tableNameObj = TableName<span class="hljs-preprocessor">.valueOf</span>(tablename)<span class="hljs-comment">;</span>
        //判断表是否存在
        if (admin<span class="hljs-preprocessor">.tableExists</span>(tableNameObj)) {
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Table exists!"</span>)<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.exit</span>(<span class="hljs-number">0</span>)<span class="hljs-comment">;</span>
        } else {
            //定义表结构
            HTableDescriptor tableDesc = new HTableDescriptor(TableName<span class="hljs-preprocessor">.valueOf</span>(tablename))<span class="hljs-comment">;</span>
            //添加列族
            tableDesc<span class="hljs-preprocessor">.addFamily</span>(new HColumnDescriptor(<span class="hljs-string">"info"</span>))<span class="hljs-comment">;</span>
            //创建表
            admin<span class="hljs-preprocessor">.createTable</span>(tableDesc)<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"create table success!"</span>)<span class="hljs-comment">;</span>
        }
        admin<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
    }
}
</code></pre>

<p>Eclipse执行结果</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.Groups</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span>
create table success!</code></pre>

<p><img src="https://img-blog.csdn.net/20170730212250560?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>通过HBase Shell 查看结果</p>



<pre class="prettyprint"><code class=" hljs coffeescript">[root<span class="hljs-property">@node2</span> ~]<span class="hljs-comment"># hbase shell</span>
<span class="hljs-attribute">SLF4J</span>: Class path contains multiple SLF4J bindings.
<span class="hljs-attribute">SLF4J</span>: Found binding <span class="hljs-keyword">in</span> [<span class="hljs-attribute">jar</span>:<span class="hljs-attribute">file</span>:/opt/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.5</span>.jar!/org/slf4j/impl/StaticLoggerBinder.class]
<span class="hljs-attribute">SLF4J</span>: Found binding <span class="hljs-keyword">in</span> [<span class="hljs-attribute">jar</span>:<span class="hljs-attribute">file</span>:/opt/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/share/hadoop/common/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.10</span>.jar!/org/slf4j/impl/StaticLoggerBinder.class]
<span class="hljs-attribute">SLF4J</span>: See <span class="hljs-attribute">http</span>:<span class="hljs-regexp">//</span>www.slf4j.org/codes.html<span class="hljs-comment">#multiple_bindings for an explanation.</span>
<span class="hljs-attribute">SLF4J</span>: Actual binding <span class="hljs-keyword">is</span> <span class="hljs-keyword">of</span> type [org.slf4j.impl.Log4jLoggerFactory]
HBase Shell; enter <span class="hljs-string">'help&lt;RETURN&gt;'</span> <span class="hljs-keyword">for</span> list <span class="hljs-keyword">of</span> supported commands.
Type <span class="hljs-string">"exit&lt;RETURN&gt;"</span> to leave the HBase Shell
Version <span class="hljs-number">1.2</span><span class="hljs-number">.6</span>, rUnknown, Mon May <span class="hljs-number">29</span> <span class="hljs-number">02</span>:<span class="hljs-number">25</span>:<span class="hljs-number">32</span> CDT <span class="hljs-number">2017</span>

hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; list
TABLE                                                                                                                                                                                        
<span class="hljs-attribute">mydb</span>:test                                                                                                                                                                                    
t1                                                                                                                                                                                           
test1                                                                                                                                                                                        
<span class="hljs-number">3</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.6070</span> seconds<span class="hljs-function">

=&gt;</span> [<span class="hljs-string">"mydb:test"</span>, <span class="hljs-string">"t1"</span>, <span class="hljs-string">"test1"</span>]
hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; desc <span class="hljs-string">'test1'</span>
Table test1 <span class="hljs-keyword">is</span> ENABLED                                                                                                                                                                       
test1                                                                                                                                                                                        
COLUMN FAMILIES DESCRIPTION                                                                                                                                                                  
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'info'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERS
IONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>}                                                                                                           
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.2110</span> seconds

hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; 
</code></pre>



<h3 id="1064-插入数据">10.6.4 插入数据</h3>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hadron</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.dao</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Table</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Put</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>
public class PutDemo {
    public static void main(String[] args)throws Exception{
        Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://cetc/hbase"</span>)<span class="hljs-comment">;</span>
        // 设置Zookeeper,直接设置IP地址
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"node1,node2,node3"</span>)<span class="hljs-comment">;</span>
        //建立连接
        Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(conf)<span class="hljs-comment">;</span>
        //获取表
        Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"test1"</span>))<span class="hljs-comment">;</span>
        //通过rowKey实例化Put
        Put put = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"001"</span>))<span class="hljs-comment">;</span>
        //指定列族名、列名和值
        String family=<span class="hljs-string">"info"</span><span class="hljs-comment">;</span>
        String qualifier=<span class="hljs-string">"name"</span><span class="hljs-comment">;</span>
        String value=<span class="hljs-string">"hadron"</span><span class="hljs-comment">;</span>
        put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(family), Bytes<span class="hljs-preprocessor">.toBytes</span>(qualifier), Bytes<span class="hljs-preprocessor">.toBytes</span>(value))<span class="hljs-comment">;</span>
        //执行Put
        table<span class="hljs-preprocessor">.put</span>(put)<span class="hljs-comment">;</span>
        //关闭表和连接
        table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"ok!"</span>)<span class="hljs-comment">;</span>
    }
}
</code></pre>

<p>Eclipse运行结果</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.Groups</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span>
ok!</code></pre>

<p>HBase Shell查看结果</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'test1'</span>
ROW                                              COLUMN+CELL                                                                                                                                 
 <span class="hljs-number">001</span>                                             column=info:name, timestamp=<span class="hljs-number">1501421890863</span>, <span class="hljs-built_in">value</span>=hadron                                                                                     
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.2060</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt;</code></pre>



<h3 id="1065-读取数据">10.6.5 读取数据</h3>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hadron</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.dao</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Table</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Get</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Result</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>
public class GetDemo {
    public static void main(String[] args)throws Exception{
        Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://cetc/hbase"</span>)<span class="hljs-comment">;</span>
        // 设置Zookeeper,直接设置IP地址
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"node1,node2,node3"</span>)<span class="hljs-comment">;</span>
        //建立连接
        Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(conf)<span class="hljs-comment">;</span>
        Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"test1"</span>))<span class="hljs-comment">;</span>
        //通过rowKey实例化Get
        Get get = new Get(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"001"</span>))<span class="hljs-comment">;</span>
        //添加列族名和列名条件
        String family=<span class="hljs-string">"info"</span><span class="hljs-comment">;</span>
        String qualifier=<span class="hljs-string">"name"</span><span class="hljs-comment">;</span>
        get<span class="hljs-preprocessor">.addColumn</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        //执行Get，返回结果
        Result result=table<span class="hljs-preprocessor">.get</span>(get)<span class="hljs-comment">;</span>
        //提取结果
        String value=Bytes<span class="hljs-preprocessor">.toString</span>(result<span class="hljs-preprocessor">.getValue</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>()))<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"value="</span>+value)<span class="hljs-comment">;</span>
        //关闭表和连接
        table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
    }
}
</code></pre>

<p>Eclipse运行结果</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.Groups</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span>
value=hadron</code></pre>



<h3 id="1066-扫描">10.6.6 扫描</h3>

<p>准备数据</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'test1'</span>,<span class="hljs-string">'002'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'abc'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.2870</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'test1'</span>,<span class="hljs-string">'003'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'xyz'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0280</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'test1'</span>,<span class="hljs-string">'004'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'qiang'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0200</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">007</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'test1'</span>,<span class="hljs-string">'005'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'test'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0430</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'test1'</span>,<span class="hljs-string">'005'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'20'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0240</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">009</span>:<span class="hljs-number">0</span>&gt; 

</code></pre>

<p>Java代码</p>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hadron</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.dao</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Table</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Get</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Result</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ResultScanner</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Scan</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>
public class ScanDemo {
    public static void main(String[] args)throws Exception{
        Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://cetc/hbase"</span>)<span class="hljs-comment">;</span>
        // 设置Zookeeper,直接设置IP地址
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"node1,node2,node3"</span>)<span class="hljs-comment">;</span>
        //建立连接
        Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(conf)<span class="hljs-comment">;</span>
        Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"test1"</span>))<span class="hljs-comment">;</span>
        //初始化Scan
        Scan scan = new Scan()<span class="hljs-comment">;</span>
<span class="hljs-comment">/*        //指定开始的rowKey
        scan.setStartRow("001".getBytes());
        //指定结束的rowKey
        scan.setStopRow("005".getBytes());*/</span>
        //添加过滤条件
        String family=<span class="hljs-string">"info"</span><span class="hljs-comment">;</span>
        String qualifier=<span class="hljs-string">"name"</span><span class="hljs-comment">;</span>
        scan<span class="hljs-preprocessor">.addColumn</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        //执行scan返回结果
        ResultScanner result=table<span class="hljs-preprocessor">.getScanner</span>(scan)<span class="hljs-comment">;</span>
        //迭代提取结果
        String value=<span class="hljs-string">""</span><span class="hljs-comment">;</span>
        for(Result r:result){
             value=Bytes<span class="hljs-preprocessor">.toString</span>(r<span class="hljs-preprocessor">.getValue</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>()))<span class="hljs-comment">;     </span>
             System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"value="</span>+value)<span class="hljs-comment">;</span>
        }   
        //关闭表和连接
        table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
    }
}
</code></pre>

<p>Eclipse执行结果</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.Groups</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span>
value=hadron
value=abc
value=xyz
value=qiang
value=test</code></pre>

<p>添加rowKey开始和结束条件，筛选出[start,stop)返回的数据。</p>



<pre class="prettyprint"><code class=" hljs avrasm">        //指定开始的rowKey
        scan<span class="hljs-preprocessor">.setStartRow</span>(<span class="hljs-string">"001"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        //指定结束的rowKey
        scan<span class="hljs-preprocessor">.setStopRow</span>(<span class="hljs-string">"005"</span><span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span></code></pre>

<p>保存代码，重新执行结果</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.Groups</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span>
value=hadron
value=abc
value=xyz
value=qiang</code></pre>



<h3 id="1067-删除数据">10.6.7 删除数据</h3>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hadron</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.dao</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Table</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Delete</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Put</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>
public class DeleteDemo {
    public static void main(String[] args)throws Exception{
        Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://cetc/hbase"</span>)<span class="hljs-comment">;</span>
        // 设置Zookeeper,直接设置IP地址
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"node1,node2,node3"</span>)<span class="hljs-comment">;</span>
        //建立连接
        Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(conf)<span class="hljs-comment">;</span>
        //获取表
        Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"test1"</span>))<span class="hljs-comment">;</span>
        //通过rowKey实例化Delete
        Delete delete=new Delete(Bytes<span class="hljs-preprocessor">.toBytes</span>(<span class="hljs-string">"001"</span>))<span class="hljs-comment">;</span>
        //指定列族名、列名和值
        String family=<span class="hljs-string">"info"</span><span class="hljs-comment">;</span>
        String qualifier=<span class="hljs-string">"name"</span><span class="hljs-comment">;</span>
        delete<span class="hljs-preprocessor">.addColumn</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
        //执行Delete
        table<span class="hljs-preprocessor">.delete</span>(delete)<span class="hljs-comment">;</span>
        //关闭表和连接
        table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"ok!"</span>)<span class="hljs-comment">;</span>
    }
}
</code></pre>

<p>Eclipse运行结果</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.Groups</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span>
ok!</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'test1'</span>,<span class="hljs-string">'001'</span>
COLUMN                                           CELL                                                                                                                                        
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0730</span> <span class="hljs-built_in">seconds</span>
hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'test1'</span>
ROW                                              COLUMN+CELL                                                                                                                                 
 <span class="hljs-number">002</span>                                             column=info:name, timestamp=<span class="hljs-number">1501424329079</span>, <span class="hljs-built_in">value</span>=abc                                                                                        
 <span class="hljs-number">003</span>                                             column=info:name, timestamp=<span class="hljs-number">1501424339893</span>, <span class="hljs-built_in">value</span>=xyz                                                                                        
 <span class="hljs-number">004</span>                                             column=info:name, timestamp=<span class="hljs-number">1501424362260</span>, <span class="hljs-built_in">value</span>=qiang                                                                                      
 <span class="hljs-number">005</span>                                             column=info:age, timestamp=<span class="hljs-number">1501424541777</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">20</span>                                                                                          
 <span class="hljs-number">005</span>                                             column=info:name, timestamp=<span class="hljs-number">1501424381141</span>, <span class="hljs-built_in">value</span>=test                                                                                       
<span class="hljs-number">4</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0500</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; 
</code></pre>



<h3 id="1068-删除表">10.6.8 删除表</h3>

<p>编写Java类</p>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hadron</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.dao</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Admin</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>
public class DropDemo {
    public static void main(String[] args)throws Exception{
        Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://cetc/hbase"</span>)<span class="hljs-comment">;</span>
        // 设置Zookeeper,直接设置IP地址
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"node1,node2,node3"</span>)<span class="hljs-comment">;</span>
        //建立连接
        Connection connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(conf)<span class="hljs-comment">;</span>
        //表管理类
        Admin admin = connection<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>
        //定义表名
        TableName table = TableName<span class="hljs-preprocessor">.valueOf</span>(<span class="hljs-string">"test1"</span>)<span class="hljs-comment">;</span>
        //先禁用
        admin<span class="hljs-preprocessor">.disableTable</span>(table)<span class="hljs-comment">;</span>
        //再删除
        admin<span class="hljs-preprocessor">.deleteTable</span>(table)<span class="hljs-comment">;</span>
        //关闭
        admin<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Successfully deleted data table！"</span>)<span class="hljs-comment">;</span>
    }
}
</code></pre>

<p>Eclipse执行结果</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.Groups</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span>
Successfully deleted data table！</code></pre>

<p>通过HBase Shell查询表，发现test1已经被删除了。</p>



<pre class="prettyprint"><code class=" hljs haml">hbase(main):005:0&gt; list
TABLE                                                                                                                                                                                        
mydb:test                                                                                                                                                                                    
t1                                                                                                                                                                                           
2 row(s) in 0.0440 seconds

=<span class="ruby">&gt; [<span class="hljs-string">"mydb:test"</span>, <span class="hljs-string">"t1"</span>]
</span>hbase(main):006:0&gt; 
</code></pre>



<h3 id="1069-封装类">10.6.9 封装类</h3>



<pre class="prettyprint"><code class=" hljs avrasm">package cn<span class="hljs-preprocessor">.hadron</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.dao</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.IOException</span><span class="hljs-comment">;</span>
import java<span class="hljs-preprocessor">.util</span>.*<span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.Cell</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.CellUtil</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HColumnDescriptor</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HTableDescriptor</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.util</span><span class="hljs-preprocessor">.Bytes</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Table</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.filter</span><span class="hljs-preprocessor">.FirstKeyOnlyFilter</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ConnectionFactory</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Delete</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Get</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Put</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Result</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.ResultScanner</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Scan</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Admin</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.Connection</span><span class="hljs-comment">;</span>

public class HBaseDao {

    private static Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
    private static Connection connection =null<span class="hljs-comment">;</span>
    private static Admin admin=null<span class="hljs-comment">;</span>

    static {
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.rootdir"</span>, <span class="hljs-string">"hdfs://cc/hbase"</span>)<span class="hljs-comment">;</span>
        // 设置Zookeeper,直接设置IP地址
        conf<span class="hljs-preprocessor">.set</span>(<span class="hljs-string">"hbase.zookeeper.quorum"</span>, <span class="hljs-string">"192.168.80.131,192.168.80.132,192.168.80.133"</span>)<span class="hljs-comment">;</span>
        try {
            connection = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(conf)<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
        try {
            admin = connection<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        } 
    }

    // 创建表
    public static void createTable(String tablename, String columnFamily) {
        TableName tableNameObj = TableName<span class="hljs-preprocessor">.valueOf</span>(tablename)<span class="hljs-comment">;</span>
        try {
            if (admin<span class="hljs-preprocessor">.tableExists</span>(tableNameObj)) {
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"Table exists!"</span>)<span class="hljs-comment">;</span>
                System<span class="hljs-preprocessor">.exit</span>(<span class="hljs-number">0</span>)<span class="hljs-comment">;</span>
            } else {
                HTableDescriptor tableDesc = new HTableDescriptor(TableName<span class="hljs-preprocessor">.valueOf</span>(tablename))<span class="hljs-comment">;</span>
                tableDesc<span class="hljs-preprocessor">.addFamily</span>(new HColumnDescriptor(columnFamily))<span class="hljs-comment">;</span>
                admin<span class="hljs-preprocessor">.createTable</span>(tableDesc)<span class="hljs-comment">;</span>
                System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"create table success!"</span>)<span class="hljs-comment">;</span>
            }
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }

    }

    // 删除表
    public static void deleteTable(String tableName) {
        try {
            TableName table = TableName<span class="hljs-preprocessor">.valueOf</span>(tableName)<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.disableTable</span>(table)<span class="hljs-comment">;</span>
            admin<span class="hljs-preprocessor">.deleteTable</span>(table)<span class="hljs-comment">;</span>
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"delete table "</span> + tableName + <span class="hljs-string">" ok."</span>)<span class="hljs-comment">;</span>
        } catch (IOException e) {
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"删除表出现异常！"</span>)<span class="hljs-comment">;</span>
        }
    }


    // 插入一行记录
    public static void put(String tableName, String rowKey, String family, String qualifier, String value){
        try {
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            Put put = new Put(Bytes<span class="hljs-preprocessor">.toBytes</span>(rowKey))<span class="hljs-comment">;</span>
            put<span class="hljs-preprocessor">.addColumn</span>(Bytes<span class="hljs-preprocessor">.toBytes</span>(family), Bytes<span class="hljs-preprocessor">.toBytes</span>(qualifier), Bytes<span class="hljs-preprocessor">.toBytes</span>(value))<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.put</span>(put)<span class="hljs-comment">;</span>
            table<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
            //System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"insert recored "</span> + rowKey + <span class="hljs-string">" to table "</span> + tableName + <span class="hljs-string">" ok."</span>)<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
    }

    //查询数据
    public static String get(String tableName, String rowKey, String family, String qualifier){
        try{
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            //通过rowKey实例化Get
            Get get = new Get(Bytes<span class="hljs-preprocessor">.toBytes</span>(rowKey))<span class="hljs-comment">;</span>
            //添加列族名和列名条件
            get<span class="hljs-preprocessor">.addColumn</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            //执行Get，返回结果
            Result result=table<span class="hljs-preprocessor">.get</span>(get)<span class="hljs-comment">;</span>
            //返回结果
            return Bytes<span class="hljs-preprocessor">.toString</span>(result<span class="hljs-preprocessor">.getValue</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>()))<span class="hljs-comment">;</span>
        }catch(IOException e){
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            return null<span class="hljs-comment">;</span>
        }
    }
    //统计记录数
    public static long count(String tableName){
        try{
            final long[] rowCount = {<span class="hljs-number">0</span>}<span class="hljs-comment">;</span>
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            Scan scan = new Scan()<span class="hljs-comment">;</span>
            scan<span class="hljs-preprocessor">.setFilter</span>(new FirstKeyOnlyFilter())<span class="hljs-comment">;</span>
            ResultScanner resultScanner = table<span class="hljs-preprocessor">.getScanner</span>(scan)<span class="hljs-comment">;</span>
            resultScanner<span class="hljs-preprocessor">.forEach</span>(result -&gt; {
                rowCount[<span class="hljs-number">0</span>] += result<span class="hljs-preprocessor">.size</span>()<span class="hljs-comment">;//result.size()是int型</span>
            })<span class="hljs-comment">;</span>
            return rowCount[<span class="hljs-number">0</span>]<span class="hljs-comment">;</span>
        }catch(IOException e){
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            return -<span class="hljs-number">1</span><span class="hljs-comment">;</span>
        }
    }

    //扫描表
    public static List&lt;String&gt; scan(String tableName, String startRow,String stopRow,String family, String qualifier){
        try {
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            //初始化Scan
            Scan scan = new Scan()<span class="hljs-comment">;</span>
            //指定开始的rowKey
            scan<span class="hljs-preprocessor">.setStartRow</span>(startRow<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            //指定结束的rowKey
            scan<span class="hljs-preprocessor">.setStopRow</span>(stopRow<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            scan<span class="hljs-preprocessor">.addColumn</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            //执行scan返回结果
            ResultScanner result=table<span class="hljs-preprocessor">.getScanner</span>(scan)<span class="hljs-comment">;</span>
            List&lt;String&gt; list=new ArrayList&lt;&gt;()<span class="hljs-comment">;</span>
            String value=null<span class="hljs-comment">;</span>
            for(Result r:result){
                value=Bytes<span class="hljs-preprocessor">.toString</span>(r<span class="hljs-preprocessor">.getValue</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>()))<span class="hljs-comment">;  </span>
                list<span class="hljs-preprocessor">.add</span>(value)<span class="hljs-comment">; </span>
            } 
            return list<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            return null<span class="hljs-comment">;</span>
        } 
    }
    //扫描表
    public static List&lt;String&gt; scan(String tableName,String family, String qualifier){
        try {
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            //初始化Scan
            Scan scan = new Scan()<span class="hljs-comment">;</span>
            scan<span class="hljs-preprocessor">.addColumn</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            //执行scan返回结果
            ResultScanner result=table<span class="hljs-preprocessor">.getScanner</span>(scan)<span class="hljs-comment">;</span>
            List&lt;String&gt; list=new ArrayList&lt;&gt;()<span class="hljs-comment">;</span>
            String value=null<span class="hljs-comment">;</span>
            for(Result r:result){
                value=Bytes<span class="hljs-preprocessor">.toString</span>(r<span class="hljs-preprocessor">.getValue</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>()))<span class="hljs-comment">;  </span>
                list<span class="hljs-preprocessor">.add</span>(value)<span class="hljs-comment">; </span>
            } 
            return list<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            return null<span class="hljs-comment">;</span>
        } 
    }

    public static List&lt;String&gt; scan(String tableName){
        List&lt;String&gt; list=new ArrayList&lt;&gt;()<span class="hljs-comment">;</span>
        try {
            //获取表
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            Scan scan = new Scan()<span class="hljs-comment">;</span>
            ResultScanner resultScanner = table<span class="hljs-preprocessor">.getScanner</span>(scan)<span class="hljs-comment">;</span>
            StringBuffer sb=null<span class="hljs-comment">;</span>
            for (Result result : resultScanner) {
                List&lt;Cell&gt; cells = result<span class="hljs-preprocessor">.listCells</span>()<span class="hljs-comment">;</span>
                for (Cell cell : cells) {
                    sb=new StringBuffer()<span class="hljs-comment">;</span>
                    sb<span class="hljs-preprocessor">.append</span>(<span class="hljs-string">"rowKey:"</span>)<span class="hljs-preprocessor">.append</span>(Bytes<span class="hljs-preprocessor">.toString</span>(CellUtil<span class="hljs-preprocessor">.cloneRow</span>(cell)))<span class="hljs-preprocessor">.append</span>(<span class="hljs-string">"\t"</span>)<span class="hljs-comment">;</span>
                    sb<span class="hljs-preprocessor">.append</span>(<span class="hljs-string">"family:"</span>)<span class="hljs-preprocessor">.append</span>(Bytes<span class="hljs-preprocessor">.toString</span>(CellUtil<span class="hljs-preprocessor">.cloneFamily</span>(cell)))<span class="hljs-preprocessor">.append</span>(<span class="hljs-string">","</span>)<span class="hljs-comment">;</span>
                    sb<span class="hljs-preprocessor">.append</span>(Bytes<span class="hljs-preprocessor">.toString</span>(CellUtil<span class="hljs-preprocessor">.cloneQualifier</span>(cell)))<span class="hljs-preprocessor">.append</span>(<span class="hljs-string">"="</span>)<span class="hljs-comment">;</span>
                    sb<span class="hljs-preprocessor">.append</span>(Bytes<span class="hljs-preprocessor">.toString</span>(CellUtil<span class="hljs-preprocessor">.cloneValue</span>(cell)))<span class="hljs-comment">;</span>
                    list<span class="hljs-preprocessor">.add</span>(sb<span class="hljs-preprocessor">.toString</span>())<span class="hljs-comment">;</span>
                }
            }
            return list<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
            return null<span class="hljs-comment">;</span>
        }
    }

    public static void delete(String tableName,String rowKey,String family, String qualifier){
        try {
            //获取表
            Table table = connection<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(tableName))<span class="hljs-comment">;</span>
            //通过rowKey实例化Delete
            Delete delete=new Delete(Bytes<span class="hljs-preprocessor">.toBytes</span>(rowKey))<span class="hljs-comment">;</span>
            //指定列族名、列名和值
            delete<span class="hljs-preprocessor">.addColumn</span>(family<span class="hljs-preprocessor">.getBytes</span>(), qualifier<span class="hljs-preprocessor">.getBytes</span>())<span class="hljs-comment">;</span>
            //执行Delete
            table<span class="hljs-preprocessor">.delete</span>(delete)<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
    }


    //关闭
    public static void close(){
        try {
            admin<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
        try {
            connection<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
        } catch (IOException e) {
            e<span class="hljs-preprocessor">.printStackTrace</span>()<span class="hljs-comment">;</span>
        }
    }
    //测试
    public static void main(String[] args) {
        HBaseDao<span class="hljs-preprocessor">.deleteTable</span>(<span class="hljs-string">"testA"</span>)<span class="hljs-comment">;</span>
        HBaseDao<span class="hljs-preprocessor">.createTable</span>(<span class="hljs-string">"testA"</span>, <span class="hljs-string">"info"</span>)<span class="hljs-comment">;</span>
        //循环插入<span class="hljs-number">10</span>条数据
        for(int i=<span class="hljs-number">0</span><span class="hljs-comment">;i&lt;10;i++){</span>
            HBaseDao<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"testA"</span>, <span class="hljs-string">"00"</span>+i, <span class="hljs-string">"info"</span>, <span class="hljs-string">"name"</span>, <span class="hljs-string">"test"</span>+i)<span class="hljs-comment">;</span>
            HBaseDao<span class="hljs-preprocessor">.put</span>(<span class="hljs-string">"testA"</span>, <span class="hljs-string">"00"</span>+i, <span class="hljs-string">"info"</span>, <span class="hljs-string">"age"</span>, i+<span class="hljs-string">""</span>)<span class="hljs-comment">;</span>
        }
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"count="</span>+HBaseDao<span class="hljs-preprocessor">.count</span>(<span class="hljs-string">"testA"</span>))<span class="hljs-comment">;</span>
        String value=HBaseDao<span class="hljs-preprocessor">.get</span>(<span class="hljs-string">"testA"</span>, <span class="hljs-string">"001"</span>,<span class="hljs-string">"info"</span>, <span class="hljs-string">"name"</span>)<span class="hljs-comment">;</span>
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"value="</span>+value)<span class="hljs-comment">;</span>
        //扫描
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"------------------sacn(testA,000,004,info,name)"</span>)<span class="hljs-comment">;</span>
        List&lt;String&gt; list=HBaseDao<span class="hljs-preprocessor">.scan</span>(<span class="hljs-string">"testA"</span>, <span class="hljs-string">"000"</span>, <span class="hljs-string">"004"</span>, <span class="hljs-string">"info"</span>, <span class="hljs-string">"name"</span>)<span class="hljs-comment">;</span>
        for(String s:list){
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(s)<span class="hljs-comment">;</span>
        }
        //扫描
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"------------------sacn(testA,info,name)"</span>)<span class="hljs-comment">;</span>
        list=HBaseDao<span class="hljs-preprocessor">.scan</span>(<span class="hljs-string">"testA"</span>, <span class="hljs-string">"info"</span>, <span class="hljs-string">"name"</span>)<span class="hljs-comment">;</span>
        for(String s:list){
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(s)<span class="hljs-comment">;</span>
        }
        list<span class="hljs-preprocessor">.clear</span>()<span class="hljs-comment">;</span>
        //扫描
        System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(<span class="hljs-string">"------------------sacn(testA)"</span>)<span class="hljs-comment">;</span>
        list=HBaseDao<span class="hljs-preprocessor">.scan</span>(<span class="hljs-string">"testA"</span>)<span class="hljs-comment">;</span>
        for(String s:list){
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(s)<span class="hljs-comment">;</span>
        }
        HBaseDao<span class="hljs-preprocessor">.close</span>()<span class="hljs-comment">;</span>
    }

}
</code></pre>

<p>Eclipse执行结果</p>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.security</span><span class="hljs-preprocessor">.Groups</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span>
删除表出现异常！
create table success!
count=<span class="hljs-number">10</span>
value=test1
------------------sacn(testA,<span class="hljs-number">000</span>,<span class="hljs-number">004</span>,info,name)
test0
test1
test2
test3
------------------sacn(testA,info,name)
test0
test1
test2
test3
test4
test5
test6
test7
test8
test9
------------------sacn(testA)
<span class="hljs-label">rowKey:</span><span class="hljs-number">000</span>  family:info,age=<span class="hljs-number">0</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">000</span>  family:info,name=test0
<span class="hljs-label">rowKey:</span><span class="hljs-number">001</span>  family:info,age=<span class="hljs-number">1</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">001</span>  family:info,name=test1
<span class="hljs-label">rowKey:</span><span class="hljs-number">002</span>  family:info,age=<span class="hljs-number">2</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">002</span>  family:info,name=test2
<span class="hljs-label">rowKey:</span><span class="hljs-number">003</span>  family:info,age=<span class="hljs-number">3</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">003</span>  family:info,name=test3
<span class="hljs-label">rowKey:</span><span class="hljs-number">004</span>  family:info,age=<span class="hljs-number">4</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">004</span>  family:info,name=test4
<span class="hljs-label">rowKey:</span><span class="hljs-number">005</span>  family:info,age=<span class="hljs-number">5</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">005</span>  family:info,name=test5
<span class="hljs-label">rowKey:</span><span class="hljs-number">006</span>  family:info,age=<span class="hljs-number">6</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">006</span>  family:info,name=test6
<span class="hljs-label">rowKey:</span><span class="hljs-number">007</span>  family:info,age=<span class="hljs-number">7</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">007</span>  family:info,name=test7
<span class="hljs-label">rowKey:</span><span class="hljs-number">008</span>  family:info,age=<span class="hljs-number">8</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">008</span>  family:info,name=test8
<span class="hljs-label">rowKey:</span><span class="hljs-number">009</span>  family:info,age=<span class="hljs-number">9</span>
<span class="hljs-label">rowKey:</span><span class="hljs-number">009</span>  family:info,name=test9</code></pre>

<p>HBase Shell查询结果</p>



<pre class="prettyprint"><code class=" hljs ocaml">hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">list</span>
TABLE                                                                                                                  
mydb:test                                                                                                              
t1                                                                                                                     
testA                                                                                                                  
<span class="hljs-number">3</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0570</span> seconds

=&gt; [<span class="hljs-string">"mydb:test"</span>, <span class="hljs-string">"t1"</span>, <span class="hljs-string">"testA"</span>]
hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'testA'</span>
ROW                            COLUMN+CELL                                                                             
 <span class="hljs-number">000</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506036</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">0</span>                                       
 <span class="hljs-number">000</span>                           column=info:name, timestamp=<span class="hljs-number">1501921505995</span>, <span class="hljs-keyword">value</span>=test0                                  
 <span class="hljs-number">001</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506053</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">1</span>                                       
 <span class="hljs-number">001</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506046</span>, <span class="hljs-keyword">value</span>=test1                                  
 <span class="hljs-number">002</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506066</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">2</span>                                       
 <span class="hljs-number">002</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506059</span>, <span class="hljs-keyword">value</span>=test2                                  
 <span class="hljs-number">003</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506078</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">3</span>                                       
 <span class="hljs-number">003</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506072</span>, <span class="hljs-keyword">value</span>=test3                                  
 <span class="hljs-number">004</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506089</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">4</span>                                       
 <span class="hljs-number">004</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506084</span>, <span class="hljs-keyword">value</span>=test4                                  
 <span class="hljs-number">005</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506101</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">5</span>                                       
 <span class="hljs-number">005</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506095</span>, <span class="hljs-keyword">value</span>=test5                                  
 <span class="hljs-number">006</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506112</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">6</span>                                       
 <span class="hljs-number">006</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506106</span>, <span class="hljs-keyword">value</span>=test6                                  
 <span class="hljs-number">007</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506144</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">7</span>                                       
 <span class="hljs-number">007</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506117</span>, <span class="hljs-keyword">value</span>=test7                                  
 <span class="hljs-number">008</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506154</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">8</span>                                       
 <span class="hljs-number">008</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506149</span>, <span class="hljs-keyword">value</span>=test8                                  
 <span class="hljs-number">009</span>                           column=info:age, timestamp=<span class="hljs-number">1501921506164</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">9</span>                                       
 <span class="hljs-number">009</span>                           column=info:name, timestamp=<span class="hljs-number">1501921506159</span>, <span class="hljs-keyword">value</span>=test9                                  
<span class="hljs-number">10</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.3550</span> seconds

hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; 
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
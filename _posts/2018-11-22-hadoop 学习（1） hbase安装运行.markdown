---
layout:     post
title:      hadoop 学习（1） hbase安装运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、首先现在hbase安装包，解压</p>
<p></p>
<pre><code class="language-html">tar -zxvf hbase-2.7.tar.gz</code></pre><br><p></p>
<p>2、配置环境变量</p>
<p></p>
<pre><code class="language-html">export HBASE_HOME=/home/songjinbin/tools/hbase-1.0.0
</code></pre>
<p></p>
<p><br></p>
3、添加hbase的配置
<p></p>
<pre><code class="language-html">&lt;configuration&gt;
   &lt;property&gt;
    &lt;name&gt;hbase.rootdir&lt;/name&gt;
    &lt;value&gt;/home/songjinbin/hadoop/hbase&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;
    &lt;value&gt;/home/songjinbin/hadoop/zookeeper&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</code></pre><br><p></p>
<p>hbase.rootDIr 配置的是hbase写入的数据信息，默认是在/tmp下面，</p>
<p>注：本此配置的是单机模式，用的hbase自带的zookeeper</p>
<p><br>
3、启动hbase</p>
<p>(1)启动hbase之前必须保证hadoop相关启动，先启动start_hdfs.sh start_yield.sh </p>
<p>(2)执行</p>
<p></p>
<pre><code class="language-html">hbase shell</code></pre>
<p><br></p>
<p>正常返回结果后我们就可以操作hbase命令了。</p>
<p></p>
<pre><code class="language-html">SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/home/songjinbin/tools/hbase-1.0.0/lib/slf4j-log4j12-1.7.7.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/home/songjinbin/tools/hadoop-2.7.1/share/hadoop/common/lib/slf4j-log4j12-1.7.10.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
SLF4J: Actual binding is of type [org.slf4j.impl.Log4jLoggerFactory]
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 1.0.0, r6c98bff7b719efdb16f71606f3b7d8229445eb81, Sat Feb 14 19:49:22 PST 2015
</code></pre><br><br><p></p>
<p>这样我们可以使用命令模式使用Hbase,该命令封装 了JAVA客户端的api的JRuby应用程序。</p>
<p><br></p>
<p><br></p>
<p>4、hbase基本命令行操作</p>
<p><br></p>
<p>（1）创建数据库</p>
<p></p>
<pre><code class="language-html">create 'users','info'</code></pre>
<p></p>
<p>hbase是一种面向列的数据库，按照行和列进行组织，列组成列族，users指的是表名，info就是列族。</p>
<p>（2）查询数据库</p>
<p>list</p>
<p></p>
<pre><code class="language-html">TABLE                                                                           
test                                                                            
users                                                                           
2 row(s) in 0.0080 seconds

=&gt; ["test", "users"]
hbase(main):003:0&gt; </code></pre>describe 命令，可以查询表内更多默认参数
<p></p>
<p><span></span></p>
<pre><code class="language-html">hbase(main):004:0&gt; describe 'users'
Table users is ENABLED                                                          
users                                                                           
COLUMN FAMILIES DESCRIPTION                                                     
{NAME =&gt; 'info', DATA_BLOCK_ENCODING =&gt; 'NONE', BLOOMFILTER =&gt; 'ROW', REPLICATIO
N_SCOPE =&gt; '0', VERSIONS =&gt; '1', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL
 =&gt; 'FOREVER', KEEP_DELETED_CELLS =&gt; 'FALSE', BLOCKSIZE =&gt; '65536', IN_MEMORY =&gt;
 'false', BLOCKCACHE =&gt; 'true'}                                                 
1 row(s) in 0.0880 seconds</code></pre>
<p></p>
5、使用java操作hbase
<p>（1）添加maven依赖包</p>
<p></p>
<pre><code class="language-html">	&lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-common&lt;/artifactId&gt;
            &lt;version&gt;2.5.1&lt;/version&gt;
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-hdfs&lt;/artifactId&gt;
            &lt;version&gt;2.5.1&lt;/version&gt;
        &lt;/dependency&gt;
         &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hbase&lt;/groupId&gt;
            &lt;artifactId&gt;hbase-client&lt;/artifactId&gt;
             &lt;version&gt;0.98.0-hadoop2&lt;/version&gt; 
        &lt;/dependency&gt;
        &lt;dependency&gt;
            &lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
            &lt;artifactId&gt;hadoop-client&lt;/artifactId&gt;
            &lt;version&gt;2.5.1&lt;/version&gt;
        &lt;/dependency&gt;</code></pre>（2）连接操作
<p></p>
<p>我们一般使用HBASE连接池创建连接，代码如下：</p>
<p></p>
<pre><code class="language-html">package com.hadoop.hbase;

import org.apache.hadoop.hbase.client.HTableInterface;
import org.apache.hadoop.hbase.client.HTablePool;
import org.apache.hadoop.hbase.client.Put;
import org.apache.hadoop.hbase.util.Bytes;

public class Test {

	public static void main(String[] args) {
		// TODO Auto-generated method stub
		HTablePool pool =new HTablePool();
		HTableInterface userTable =pool.getTable("users");
	}

}
</code></pre>（2）数据操作
<p></p>
<p> 我们知道一般关系型数据库中都有主键的概念，同样，hbase数据库一般使用一个行健的概念特制一行数据的唯一标示，<span></span></p>
<p>hbase api通过put（放入）get（读）delete（删除）scan（扫描）increment（递增）几个接口操作数据库<br><br><strong>首先创建一个行健</strong></p>
<p></p>
<pre><code class="language-html">	Put p =new Put(Bytes.toBytes("testse"));</code></pre><br><p>应为hbase只允许所有数据通过原始java字节类型数据传输，所以提供了一个公用类<span style="background-color:rgb(240,240,240);">Bytes来处理</span></p>
<p><span style="background-color:rgb(240,240,240);">存入数据</span></p>
<p></p><pre><code class="language-html">		HTableInterface userTable =pool.getTable("users");
		Put p =new Put(Bytes.toBytes("testse"));
		p.add(Bytes.toBytes("info"), Bytes.toBytes("name"),Bytes.toBytes("jinbin"));
		p.add(Bytes.toBytes("info"), Bytes.toBytes("username"),Bytes.toBytes("jinbin"));
		p.add(Bytes.toBytes("info"), Bytes.toBytes("password"),Bytes.toBytes("12345"));
		userTable.put(p);
		userTable.close();</code></pre><br><br><p><span style="background-color:rgb(240,240,240);"><br></span></p>
            </div>
                </div>
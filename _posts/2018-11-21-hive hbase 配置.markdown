---
layout:     post
title:      hive hbase 配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="hive_hbase__0"></a>hive hbase 配置</h1>
<p>标签（空格分隔）： hive</p>
<hr>
<p>##Hive2.x 版本的安装及配置参考</p>
<blockquote>
<p><a href="http://blog.csdn.net/cds86333774/article/details/51135954" rel="nofollow">Hive2.x 版本的安装及配置</a><br>
<a href="http://lxw1234.com/archives/2016/05/675.htm" rel="nofollow">HiveServer2的高可用-HA配置</a><br>
<a href="http://blog.csdn.net/zhoudetiankong/article/details/52573137" rel="nofollow">hive metastore ha 配置</a></p>
</blockquote>
<p>##hive2.x 安装<br>
###环境变量</p>
<pre><code>export HIVE_HOME=/application/hive
export PATH=$HIVE_HOME/bin:$PATH
</code></pre>
<p>##hive和hbase整合</p>
<blockquote>
<p>在hive-site.xml中添加，$HIVE_HOME改成hive的目录，不能使用HIVE_HOME，system:user.name替换成自己的用户名</p>
</blockquote>
<pre><code>&lt;property&gt;
    &lt;name&gt;system:java.io.tmpdir&lt;/name&gt;
    &lt;value&gt;$HIVE_HOME/tmpdir&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
    &lt;name&gt;system:user.name&lt;/name&gt;
    &lt;value&gt;hadoop&lt;/value&gt;
&lt;/property&gt;
</code></pre>
<blockquote>
<p>加入hive-hbase-handler引用</p>
</blockquote>
<pre><code>  &lt;property&gt;
    &lt;name&gt;hive.aux.jars.path&lt;/name&gt;
    &lt;value&gt;/home/hadoop/application/hive/lib/hive-hbase-handler-2.3.2.jar&lt;/value&gt;
</code></pre>
<blockquote>
<p>在hive 2.3.2的lib目录中已包含hbase相关的jar包，如果没有，参考以下配置</p>
</blockquote>
<pre><code>&lt;property&gt;
    &lt;name&gt;hive.aux.jars.path&lt;/name&gt;
    &lt;value&gt;
        file:///home/hadoop/hive/lib/hive-hbase-handler-2.3.2.jar,
        file:///home/hadoop/hive/lib/guava-14.0.1.jar,
        file:///home/hadoop/hbase/lib/hbase-common-1.1.4.jar,
        file:///home/hadoop/hbase/lib/hbase-client-1.1.4.jar,
        file:///home/hadoop/hbase/lib/hbase-server-1.1.4.jar,
        file:///home/hadoop/hbase/lib/hbase-hadoop2-compat-1.1.4.ja,
        file:///home/hadoop/hbase/lib/netty-all-4.0.23.Final.jar,
        file:///home/hadoop/hbase/lib/hbase-protocol-1.1.4.jar,
        file:///home/hadoop/zookeeper/zookeeper-3.4.5.jar
    &lt;/value&gt;
&lt;/property&gt;
</code></pre>
<p>###创建表</p>
<blockquote>
<p>hive内部表</p>
</blockquote>
<pre><code>create table hive2(row_key string, fpdm string, fphm string, kprq string, xhdwsbh string);
</code></pre>
<blockquote>
<p>hbase外部表</p>
</blockquote>
<pre><code>CREATE EXTERNAL TABLE hive_test1(row_key string, fpdm string,fphm string ,kprq string,xhdwsbh string)
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping"=":key, i:fpdm, i:fphm, i:kprq, i:xhdwsbh")
TBLPROPERTIES("hbase.table.name" = "iw:test01");
</code></pre>
<p>##HIVE ON SPARK</p>
<blockquote>
<p>hive2.x官方已经不推荐使用Hive on MR，于是集成Spark<br>
<a href="http://blog.csdn.net/jsin31/article/details/69372148og.csdn.net/zhoudetiankong/article/details/52573137" rel="nofollow">基于Spark 2.0.0搭建Hive on Spark环境</a><br>
<a href="https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark:+Getting+Startedin31/article/details/69372148og.csdn.net/zhoudetiankong/article/details/52573137" rel="nofollow">Hive on Spark: Getting Started</a><br>
hive只支持特定关系的spark版本，详细可以看官方文档Version Compatibility章节，本文使用hive-2.3.2、spark-2.0.2和hadoop-2.6<br>
###编译spark<br>
spark官网提供的spark已经集成了hive-1.2.1，使用hive on spark必须编译一个不集成hive的版本，编译命令如下</p>
</blockquote>
<pre><code>./dev/make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.6,parquet-provided"
</code></pre>
<p>##问题记录<br>
###1、hive日志路径</p>
<blockquote>
<p>根据启动的用户不同，位于一下路径：<br>
/tmp/hadoop/hive.log<br>
/tmp/root/hive.log</p>
</blockquote>
<p>###2、java.lang.RuntimeException: java.lang.RuntimeException: org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.security.authorize.AuthorizationException): User: hadoop is not allowed to impersonate hadoop</p>
<blockquote>
<p>beeline无法连接时有多种可能，首先进入beeline使用**!connect jdbc:hive2:// hadoop**命令，密码为空，测试是否能连接上，如果不行则hiveserver没有启动<br>
本地连接成功后，远程连接仍然报错，需要配置hadoop的core-site.xml，以下为使用通配的方式。</p>
</blockquote>
<pre class=" language-xml"><code class="prism  language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>hadoop.proxyuser.hadoop.groups<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>*<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>description</span><span class="token punctuation">&gt;</span></span>Allow the superuser oozie to impersonate any members of the group group1 and group2<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>description</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>hadoop.proxyuser.hadoop.hosts<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>*<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>description</span><span class="token punctuation">&gt;</span></span>The superuser can connect only from host1 and host2 to impersonate a user<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>description</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>或者配置具体的用户和hosts。</p>
<pre class=" language-xml"><code class="prism  language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>hadoop.proxyuser.hadoop.groups<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>hadoop<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>description</span><span class="token punctuation">&gt;</span></span>Allow the superuser oozie to impersonate any members of the group group1 and group2<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>description</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>hadoop.proxyuser.hadoop.hosts<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>127.0.0.1,localhost<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
	<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>description</span><span class="token punctuation">&gt;</span></span>The superuser can connect only from host1 and host2 to impersonate a user<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>description</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<blockquote>
<p>然后再使用!connect jdbc:hive2://ip:port/default</p>
</blockquote>
<p>###3、json serde</p>
<blockquote>
<p>hive2提供自带的json serde，使用命令可以临时加入会话中<br>
ADD JAR /HIVE_HOME/hcatalog/share/hcatalog/hive-hcatalog-core-2.1.1.jar;<br>
<a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-RowFormats&amp;SerDe" rel="nofollow">hive2自带的serde</a></p>
</blockquote>
<p>###4、永久add jar</p>
<blockquote>
<p>在hive根目录下创建目录auxlib，将jar包放入该目录下<br>
重启hiveserver</p>
</blockquote>
<p>###5、Hbase integration (整合) 出现zk始终连接 localhost:2181<br>
需要在hive的conf hive-site.xml加入一个hbase zk的属性</p>
<pre><code>&lt;property&gt;
    &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
    &lt;value&gt;host1,host2&lt;/value&gt;
    &lt;description&gt;&lt;/description&gt;
&lt;/property&gt;
</code></pre>
<p>###6、hive可以访问base，beeline不行，提示错误ConnectionClosingException</p>
<blockquote>
<p>权限问题造成，修改hive-site.xml</p>
</blockquote>
<pre><code>  &lt;property&gt;
    &lt;name&gt;hive.server2.enable.doAs&lt;/name&gt;
    &lt;value&gt;false&lt;/value&gt;
  &lt;/property&gt;
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
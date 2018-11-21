---
layout:     post
title:      使用flume对FTP服务器进行文件的获取解析入库mysql数据库
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="使用flume对ftp服务器进行文件的获取解析入库mysql数据库">使用flume对FTP服务器进行文件的获取解析入库mysql数据库</h1>

<ul>
<li><strong>flume的基本介绍</strong></li>
<li><strong>flume的搭建和启动</strong></li>
<li><strong>FTP做source的插件使用</strong></li>
<li><strong>自定义sink将数据导入mysql中</strong></li>
</ul>

<hr>

<h2 id="flume的基本介绍">flume的基本介绍</h2>

<p>系统描述： <br>
Flume是一个分布式的、可靠的、高可用的海量日志采集、聚合和传输的系统。 <br>
数据流模型：Source-Channel-Sink。 <br>
当 Flume 来源收到事件后，它会将这些事件存储在一个或多个通道 中。通道是一种被动存储，它将事件保留到被 Flume 接收器 使用为止。例如，一个文件通道使用了本地文件系统；接收器从通道提取事件，并将它放在一个外部存储库（比如 HDFS）中，或者将它转发到流中下一个 Flume 代理（下一个跃点）的 Flume 来源；给定代理中的来源和接收器与暂存在通道中的事件同步运行。 <br>
架构图： <br>
<img src="https://img-blog.csdn.net/20180905183436655?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQwMDE1NzU5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
网上很多关于flume的介绍我就不多说了。 <br>
Flume基本组件： <br>
Event：消息的基本单位，有header和body组成 <br>
Agent：JVM进程，负责将一端外部来源产生的消息转 发到另一端外部的目的地 <br>
Source：从外部来源读入event，并写入channel <br>
Channel：event暂存组件，source写入后，event将会一直保存, <br>
Sink：从channel读入event，并写入目的地 <br>
1.3.1Source意为来源、源头。 <br>
主要作用：从外界采集各种类型的数据，将数据传递给Channel。 <br>
   比如:监控某个文件只要增加数据就立即采集新增的数据、监控某个目录一旦有新文件产生就采集新文件的内容、监控某个端口等等。 <br>
常见采集的数据类型： <br>
   Exec Source、Avro Source、NetCat Source、Spooling Directory Source等 <br>
Source具体作用： <br>
- **AvroSource：监听一个avro服务端口，采集Avro数据序列化后的数据； <br>
- **Thrift Source：监听一个Thrift 服务端口，采集Thrift数据序列化后的数据； <br>
- **Exec Source：基于Unix的command在标准输出上采集数据；tail -F 和tail -f 区别。基于log4j切割文件时的能否读取问题。 <br>
- **JMS Source：Java消息服务数据源，Java消息服务是一个与具体平台无关的API，这是支持jms规范的数据源采集； <br>
- **Spooling Directory Source：通过文件夹里的新增的文件作为数据源的采集； <br>
- **Kafka Source：从kafka服务中采集数据。 <br>
- **NetCat Source： 绑定的端口（tcp、udp），将流经端口的每一个文本行数据作为Event输入 <br>
- **HTTP Source：监听HTTP POST和 GET产生的数据的采集 <br>
 Channel： <br>
 一个数据的存储池，中间通道。 <br>
主要作用：接受source传出的数据，向sink指定的目的地传输。Channel中的数据直到进入到下一个channel中或者进入终端才会被删除。当sink写入失败后，可以自动重写，不会造成数据丢失，因此很可靠。 <br>
channel的类型很多比如:内存中、jdbc数据源中、文件形式存储等。 <br>
常见采集的数据类型： <br>
- **Memory Channel <br>
- **File Channel <br>
- **Spillable Memory Channel等</p>

<p>Channel具体作用： <br>
- **Memory Channel：使用内存作为数据的存储。速度快 <br>
- **File Channel：使用文件来作为数据的存储。安全可靠 <br>
- **Spillable Memory Channel：使用内存和文件作为数据的存储，即：先存在内存中，如果内存中数据达到阀值则flush到文件中。 <br>
- **JDBC Channel：使用jdbc数据源来作为数据的存储。 <br>
- **Kafka Channel：使用kafka服务来作为数据的存储。</p>

<p>Sink： <br>
主要作用：接受channel写入的数据以指定的形式表现出来（或存储或展示）。 <br>
sink的表现形式很多比如:打印到控制台、hdfs上、avro服务中、文件中等。 <br>
常见采集的数据类型： <br>
      HDFS Sink <br>
      Hive Sink <br>
      Logger Sink <br>
      Avro Sink <br>
      Thrift Sink <br>
      File Roll Sink <br>
      HBaseSink <br>
      Kafka Sink等 <br>
详细查看： <br>
HDFSSink需要有hdfs的配置文件和类库。一般采取多个sink汇聚到一台采集机器负责推送到hdfs。</p>

<p>Sink具体作用： <br>
Logger Sink：将数据作为日志处理（根据flume中的设置的日志的级别显示）。 <br>
HDFS Sink：将数据传输到hdfs集群中。 <br>
Avro Sink：数据被转换成Avro Event，然后发送到指定的服务端口上。 <br>
Thrift Sink：数据被转换成Thrift Event，然后发送到指定的的服务端口上。 <br>
File Roll Sink：数据传输到本地文件中。 <br>
Hive Sink：将数据传输到hive的表中。 <br>
IRC Sink：数据向指定的IRC服务和端口中发送。 <br>
Null Sink：取消数据的传输，即不发送到任何目的地。 <br>
HBaseSink：将数据发往hbase数据库中。 <br>
MorphlineSolrSink：数据发送到Solr搜索服务器（集群）。 <br>
ElasticSearchSink：数据发送到Elastic Search搜索服务器（集群）。 <br>
Kafka Sink：将数据发送到kafka服务中。（注意依赖类库）</p>



<h3 id="flume搭建和启动">Flume搭建和启动</h3>

<p>首先官网下载二进制的安装包 <br>
<a href="http://flume.apache.org/download.html" rel="nofollow">http://flume.apache.org/download.html</a> <br>
直接解压 运行需要有jdk的环境，根据flume的版本不同所需要的jdk版本也不一样 1.7的flume至少需要的是1.7以上的jdk</p>

<p>然后到flume的bin目录下面就可以启动flume了 <br>
如：./flume-ng agent -c ../conf -f ../conf/g01-taildir-avro-sink.conf -n agent1 -Dflume.root.logger=INFO,console</p>

<p>-c/–conf 后跟配置目录，-f/–conf-file 后跟具体的配置文件，-n/–name 指定agent的名称</p>



<h2 id="使用ftp做source">使用FTP做source</h2>

<p>因为我的需求是从客户的ftp服务器中获取需要的文件数据所以需要有一个source从ftp获取，但是由于flime内置的souce中没有ftp，所以我们从github中down一个frp做source的插件使用 <br>
<a href="https://github.com/keedio/flume-ftp-source" rel="nofollow">https://github.com/keedio/flume-ftp-source</a> <br>
!!!!感谢大神提供的插件啊！ <br>
下载之后解压然后打包生成jar包并且把另外两个依赖包 <br>
commons-net-3.3.jar和jsch-0.1.54.jar 加入之前解压的flume中的lib 中 <br>
然后编写配置文件 按照 插件里面的配置文件模板编写自己的soure的配置 <br>
    如： <br>
agent.sources.ftp2.type = org.keedio.flume.source.ftp.source.Source <br>
agent.sources.ftp2.client.source = ftp <br>
agent.sources.ftp2.name.server = 172.168.27.98 //ftp服务地址 <br>
agent.sources.ftp2.port = 21            //端口 <br>
agent.sources.ftp2.user = ftp_huge                  //ftp用户名 <br>
agent.sources.ftp2.password = 2970667           //密码 <br>
agent.sources.ftp2.working.directory = /OCS/CRM/bill/in     //文件所在目录 （相对于根目录） <br>
agent.sources.ftp2.filter.pattern = .+\.unl            //文件的名称匹配 （java正则表达式） <br>
agent.sources.ftp2.folder = /home/ftp_test              //ftp的根目录 <br>
agent.sources.ftp2.file.name = ftp2-status-file.ser <br>
agent.sources.ftp2.run.discover.delay=5000 <br>
agent.sources.ftp2.flushlines = true <br>
agent.sources.ftp2.search.recursive = true <br>
agent.sources.ftp2.processInUse = false <br>
agent.sources.ftp2.processInUseTimeout = 30 <br>
agent.sources.ftp2.channels = ch2</p>



<h2 id="自定义sink将数据导入mysql中">自定义sink将数据导入mysql中</h2>

<p>有了之前ftp插件引用的经验 现在我们需要自己定义一个sink将数据存入MySQL中</p>

<p>首先新建maven 项目，pom.xml 文件如下：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">project</span> <span class="hljs-attribute">xmlns</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0"</span>
         <span class="hljs-attribute">xmlns:xsi</span>=<span class="hljs-value">"http://www.w3.org/2001/XMLSchema-instance"</span>
         <span class="hljs-attribute">xsi:schemaLocation</span>=<span class="hljs-value">"http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd"</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">modelVersion</span>&gt;</span>4.0.0<span class="hljs-tag">&lt;/<span class="hljs-title">modelVersion</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>com.us<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flumeDemo<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>1.0-SNAPSHOT<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">properties</span>&gt;</span>        <span class="hljs-tag">&lt;<span class="hljs-title">maven.compiler.target</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">maven.compiler.target</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">maven.compiler.source</span>&gt;</span>1.8<span class="hljs-tag">&lt;/<span class="hljs-title">maven.compiler.source</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">version.flume</span>&gt;</span>1.7.0<span class="hljs-tag">&lt;/<span class="hljs-title">version.flume</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">properties</span>&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">dependencies</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-core<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${version.flume}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>org.apache.flume<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>flume-ng-configuration<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>${version.flume}<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
        <span class="hljs-comment">&lt;!-- https://mvnrepository.com/artifact/mysql/mysql-connector-java --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">dependency</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">groupId</span>&gt;</span>mysql<span class="hljs-tag">&lt;/<span class="hljs-title">groupId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">artifactId</span>&gt;</span>mysql-connector-java<span class="hljs-tag">&lt;/<span class="hljs-title">artifactId</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">version</span>&gt;</span>6.0.5<span class="hljs-tag">&lt;/<span class="hljs-title">version</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">dependency</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">dependencies</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">project</span>&gt;</span></code></pre>

<p>废话不多说直接上码</p>

<p>入口：MySqlSink </p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.tesnik.flume.sink;

<span class="hljs-keyword">import</span> com.google.common.base.Preconditions;
<span class="hljs-keyword">import</span> java.util.HashMap;
<span class="hljs-keyword">import</span> java.util.Map;
<span class="hljs-keyword">import</span> org.apache.flume.Channel;
<span class="hljs-keyword">import</span> org.apache.flume.ChannelException;
<span class="hljs-keyword">import</span> org.apache.flume.Context;
<span class="hljs-keyword">import</span> org.apache.flume.CounterGroup;
<span class="hljs-keyword">import</span> org.apache.flume.Event;
<span class="hljs-keyword">import</span> org.apache.flume.EventDeliveryException;
<span class="hljs-keyword">import</span> org.apache.flume.Transaction;
<span class="hljs-keyword">import</span> org.apache.flume.Sink.Status;
<span class="hljs-keyword">import</span> org.apache.flume.conf.Configurable;
<span class="hljs-keyword">import</span> org.apache.flume.sink.AbstractSink;
<span class="hljs-keyword">import</span> org.slf4j.Logger;
<span class="hljs-keyword">import</span> org.slf4j.LoggerFactory;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">MySqlSink</span> <span class="hljs-keyword">extends</span> <span class="hljs-title">AbstractSink</span> <span class="hljs-keyword">implements</span> <span class="hljs-title">Configurable</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = LoggerFactory.getLogger(MySqlSink.class);
    <span class="hljs-keyword">private</span> String databaseName;
    <span class="hljs-keyword">private</span> String tableName;
    <span class="hljs-keyword">private</span> String partition;
    <span class="hljs-keyword">private</span> String iscustom;
    <span class="hljs-keyword">private</span> String password;
    <span class="hljs-keyword">private</span> String user;
    <span class="hljs-keyword">private</span> String driver;
    <span class="hljs-keyword">private</span> String db_url;
    <span class="hljs-keyword">private</span> CounterGroup counterGroup = <span class="hljs-keyword">new</span> CounterGroup();
    DAOClass daoClass = <span class="hljs-keyword">new</span> DAOClass();

    <span class="hljs-keyword">public</span> <span class="hljs-title">MySqlSink</span>() {
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">configure</span>(Context context) {
        <span class="hljs-keyword">this</span>.db_url = context.getString(<span class="hljs-string">"url"</span>);
        <span class="hljs-keyword">this</span>.password = context.getString(<span class="hljs-string">"password"</span>);
        <span class="hljs-keyword">this</span>.user = context.getString(<span class="hljs-string">"user"</span>);
        <span class="hljs-keyword">this</span>.driver = context.getString(<span class="hljs-string">"driver"</span>);
        <span class="hljs-keyword">this</span>.tableName = context.getString(<span class="hljs-string">"tableName"</span>);
        <span class="hljs-keyword">this</span>.partition = context.getString(<span class="hljs-string">"partition"</span>);
        <span class="hljs-keyword">this</span>.iscustom = context.getString(<span class="hljs-string">"iscustom"</span>);
        <span class="hljs-keyword">this</span>.databaseName = context.getString(<span class="hljs-string">"databaseName"</span>);
        Preconditions.checkState(<span class="hljs-keyword">this</span>.password != <span class="hljs-keyword">null</span>, <span class="hljs-string">"No password specified"</span>);
        Preconditions.checkState(<span class="hljs-keyword">this</span>.user != <span class="hljs-keyword">null</span>, <span class="hljs-string">"No user specified"</span>);
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">start</span>() {
        logger.info(<span class="hljs-string">"Mysql sink starting"</span>);

        <span class="hljs-keyword">try</span> {
            <span class="hljs-keyword">this</span>.daoClass.createConnection(<span class="hljs-keyword">this</span>.driver, <span class="hljs-keyword">this</span>.db_url, <span class="hljs-keyword">this</span>.user, <span class="hljs-keyword">this</span>.password);
        } <span class="hljs-keyword">catch</span> (Exception var2) {
            logger.error(<span class="hljs-string">"Unable to create MySQL client using url:"</span> + <span class="hljs-keyword">this</span>.db_url + <span class="hljs-string">" username:"</span> + <span class="hljs-keyword">this</span>.user + <span class="hljs-string">". Exception follows."</span>, var2);
            <span class="hljs-keyword">this</span>.daoClass.destroyConnection(<span class="hljs-keyword">this</span>.db_url, <span class="hljs-keyword">this</span>.user);
            <span class="hljs-keyword">return</span>;
        }

        <span class="hljs-keyword">super</span>.start();
        logger.debug(<span class="hljs-string">"MySQL sink {} started"</span>, <span class="hljs-keyword">this</span>.getName());
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">stop</span>() {
        logger.info(<span class="hljs-string">"MySQL sink {} stopping"</span>, <span class="hljs-keyword">this</span>.getName());
        <span class="hljs-keyword">this</span>.daoClass.destroyConnection(<span class="hljs-keyword">this</span>.db_url, <span class="hljs-keyword">this</span>.user);
        <span class="hljs-keyword">super</span>.stop();
        logger.debug(<span class="hljs-string">"MySQL sink {} stopped. Metrics:{}"</span>, <span class="hljs-keyword">this</span>.getName(), <span class="hljs-keyword">this</span>.counterGroup);
    }

    <span class="hljs-keyword">public</span> Status <span class="hljs-title">process</span>() <span class="hljs-keyword">throws</span> EventDeliveryException {
        Status status = Status.READY;
        Channel channel = <span class="hljs-keyword">this</span>.getChannel();
        Transaction transaction = channel.getTransaction();

        <span class="hljs-keyword">try</span> {
            transaction.begin();
            <span class="hljs-keyword">this</span>.daoClass.createConnection(<span class="hljs-keyword">this</span>.driver, <span class="hljs-keyword">this</span>.db_url, <span class="hljs-keyword">this</span>.user, <span class="hljs-keyword">this</span>.password);
            Event event = channel.take();
            <span class="hljs-keyword">if</span> (event == <span class="hljs-keyword">null</span>) {
                <span class="hljs-keyword">this</span>.counterGroup.incrementAndGet(<span class="hljs-string">"event.empty"</span>);
                status = Status.BACKOFF;
            } <span class="hljs-keyword">else</span> {
                Map&lt;String, String&gt; params = <span class="hljs-keyword">new</span> HashMap();
                params.put(<span class="hljs-string">"tableName"</span>, <span class="hljs-keyword">this</span>.tableName);
                params.put(<span class="hljs-string">"partition"</span>, <span class="hljs-keyword">this</span>.partition);
                params.put(<span class="hljs-string">"iscustom"</span>, <span class="hljs-keyword">this</span>.iscustom);
                params.put(<span class="hljs-string">"databaseName"</span>, <span class="hljs-keyword">this</span>.databaseName);
                <span class="hljs-keyword">this</span>.daoClass.insertData(event, params);
                <span class="hljs-keyword">this</span>.counterGroup.incrementAndGet(<span class="hljs-string">"event.mysql"</span>);
            }

            transaction.commit();
        } <span class="hljs-keyword">catch</span> (ChannelException var10) {
            transaction.rollback();
            logger.error(<span class="hljs-string">"Unable to get event from channel. Exception follows."</span>, var10);
            status = Status.BACKOFF;
        } <span class="hljs-keyword">catch</span> (Exception var11) {
            transaction.rollback();
            logger.error(<span class="hljs-string">"Unable to communicate with MySQL server. Exception follows."</span>, var11);
            status = Status.BACKOFF;
            <span class="hljs-keyword">this</span>.daoClass.destroyConnection(<span class="hljs-keyword">this</span>.db_url, <span class="hljs-keyword">this</span>.user);
        } <span class="hljs-keyword">finally</span> {
            transaction.close();
        }

        <span class="hljs-keyword">return</span> status;
    }
}</code></pre>

<p>具体操作类 ClassDao</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">//</span>
<span class="hljs-comment">// Source code recreated from a .class file by IntelliJ IDEA</span>
<span class="hljs-comment">// (powered by Fernflower decompiler)</span>
<span class="hljs-comment">//</span>

<span class="hljs-keyword">package</span> com.tesnik.flume.sink;

<span class="hljs-keyword">import</span> com.tesnik.flume.sink.util.ImvnoUtil;
<span class="hljs-keyword">import</span> java.io.IOException;
<span class="hljs-keyword">import</span> java.sql.Connection;
<span class="hljs-keyword">import</span> java.sql.DriverManager;
<span class="hljs-keyword">import</span> java.sql.PreparedStatement;
<span class="hljs-keyword">import</span> java.sql.ResultSet;
<span class="hljs-keyword">import</span> java.sql.SQLException;
<span class="hljs-keyword">import</span> java.sql.Statement;
<span class="hljs-keyword">import</span> java.text.SimpleDateFormat;
<span class="hljs-keyword">import</span> java.util.ArrayList;
<span class="hljs-keyword">import</span> java.util.Date;
<span class="hljs-keyword">import</span> java.util.HashMap;
<span class="hljs-keyword">import</span> java.util.List;
<span class="hljs-keyword">import</span> java.util.Map;
<span class="hljs-keyword">import</span> org.apache.flume.Event;
<span class="hljs-keyword">import</span> org.slf4j.Logger;
<span class="hljs-keyword">import</span> org.slf4j.LoggerFactory;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">DAOClass</span> {</span>
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">final</span> Logger logger = LoggerFactory.getLogger(DAOClass.class);
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> Map&lt;String, String&gt; columnsMap = <span class="hljs-keyword">new</span> HashMap();
    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String INSERT_QUERY;
    <span class="hljs-keyword">private</span> Connection connection;

    <span class="hljs-keyword">public</span> <span class="hljs-title">DAOClass</span>() {
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">insertData</span>(Event event, Map&lt;String, String&gt; params) {
        <span class="hljs-keyword">try</span> {
            String body = <span class="hljs-keyword">new</span> String(event.getBody());
            Map&lt;String, String&gt; headers = event.getHeaders();
            String fileName = (String)headers.get(<span class="hljs-string">"fileName"</span>);
            String databaseName = (String)params.get(<span class="hljs-string">"databaseName"</span>);
            String tableName = (String)params.get(<span class="hljs-string">"tableName"</span>);
            String partition = (String)params.get(<span class="hljs-string">"partition"</span>);
            String iscustom = (String)params.get(<span class="hljs-string">"iscustom"</span>);
            <span class="hljs-keyword">if</span> (!<span class="hljs-string">"false"</span>.equals(iscustom)) {
                <span class="hljs-keyword">if</span> (<span class="hljs-string">"true"</span>.equals(iscustom)) {
                    ;
                }
            } <span class="hljs-keyword">else</span> {
                String columns = <span class="hljs-string">""</span>;
                Statement st = <span class="hljs-keyword">this</span>.connection.createStatement();
                String nowDate;
                <span class="hljs-keyword">if</span> (columnsMap.get(tableName) == <span class="hljs-keyword">null</span>) {
                    nowDate = <span class="hljs-string">"select COLUMN_NAME from INFORMATION_SCHEMA.Columns where table_name='"</span> + tableName + <span class="hljs-string">"' and  table_schema='"</span> + databaseName + <span class="hljs-string">"'"</span>;

                    <span class="hljs-keyword">for</span>(ResultSet resultSet = st.executeQuery(nowDate); resultSet.next(); columns = columns + resultSet.getString(<span class="hljs-string">"COLUMN_NAME"</span>) + <span class="hljs-string">","</span>) {
                        ;
                    }

                    columns = columns.substring(<span class="hljs-number">0</span>, columns.length() - <span class="hljs-number">1</span>);
                    columnsMap.put(tableName, columns);
                } <span class="hljs-keyword">else</span> {
                    columns = (String)columnsMap.get(tableName);
                }

                nowDate = getNowDate();
                String[] datas = body.split(partition);
                List&lt;String&gt; dataList = <span class="hljs-keyword">new</span> ArrayList();
                String IID = ImvnoUtil.getIID();
                dataList.add(IID);
                String[] arr$ = datas;
                <span class="hljs-keyword">int</span> i1 = datas.length;

                <span class="hljs-keyword">for</span>(<span class="hljs-keyword">int</span> i$ = <span class="hljs-number">0</span>; i$ &lt; i1; ++i$) {
                    String data = arr$[i$];
                    dataList.add(data);
                }

                dataList.add(fileName);
                dataList.add(nowDate);
                String indexs = <span class="hljs-string">""</span>;
                i1 = <span class="hljs-number">0</span>;

                <span class="hljs-keyword">while</span>(<span class="hljs-keyword">true</span>) {
                    <span class="hljs-keyword">if</span> (i1 &gt;= dataList.size()) {
                        indexs = indexs.substring(<span class="hljs-number">0</span>, indexs.length() - <span class="hljs-number">1</span>);
                        INSERT_QUERY = <span class="hljs-string">"INSERT INTO "</span> + tableName + <span class="hljs-string">"("</span> + columns + <span class="hljs-string">") values ("</span> + indexs + <span class="hljs-string">")"</span>;
                        <span class="hljs-keyword">break</span>;
                    }

                    String ind = <span class="hljs-string">""</span>;
                    <span class="hljs-keyword">if</span> (((String)dataList.get(i1)).equals(<span class="hljs-string">""</span>)) {
                        ind = <span class="hljs-string">"null,"</span>;
                    } <span class="hljs-keyword">else</span> {
                        ind = <span class="hljs-string">"'"</span> + (String)dataList.get(i1) + <span class="hljs-string">"',"</span>;
                    }

                    indexs = indexs + ind;
                    ++i1;
                }
            }

            PreparedStatement insertStmnt = <span class="hljs-keyword">this</span>.connection.prepareStatement(INSERT_QUERY);
            insertStmnt.execute();
        } <span class="hljs-keyword">catch</span> (SQLException var20) {
            var20.printStackTrace();
        }

    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">createConnection</span>(String driver, String db_url, String user, String password) <span class="hljs-keyword">throws</span> IOException {
        <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.connection == <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">try</span> {
                Class.forName(driver);
                <span class="hljs-keyword">this</span>.connection = DriverManager.getConnection(db_url, user, password);
            } <span class="hljs-keyword">catch</span> (ClassNotFoundException var6) {
                var6.printStackTrace();
            } <span class="hljs-keyword">catch</span> (SQLException var7) {
                var7.printStackTrace();
            }
        }

    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">destroyConnection</span>(String db_url, String user) {
        <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.connection != <span class="hljs-keyword">null</span>) {
            logger.debug(<span class="hljs-string">"Destroying connection to: {}:{}"</span>, db_url, user);

            <span class="hljs-keyword">try</span> {
                <span class="hljs-keyword">this</span>.connection.close();
            } <span class="hljs-keyword">catch</span> (SQLException var4) {
                var4.printStackTrace();
            }
        }

        <span class="hljs-keyword">this</span>.connection = <span class="hljs-keyword">null</span>;
    }

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">escapeSQL</span>(String s) {
        <span class="hljs-keyword">return</span> s.replaceAll(<span class="hljs-string">"'"</span>, <span class="hljs-string">"\\'"</span>);
    }

    <span class="hljs-keyword">public</span> Connection <span class="hljs-title">getConnection</span>() {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">this</span>.connection;
    }

    <span class="hljs-keyword">private</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">getNowDate</span>() {
        SimpleDateFormat df = <span class="hljs-keyword">new</span> SimpleDateFormat(<span class="hljs-string">"yyyy-MM-dd HH:mm:ss"</span>);
        String INSERT_DATE = df.format(<span class="hljs-keyword">new</span> Date());
        <span class="hljs-keyword">return</span> INSERT_DATE;
    }
}


</code></pre>

<p>大功告成  </p>

<p>最后打包扔进lib 并且加入mysql 驱动jar包 <br>
编写配置文件内容（根据自己业务情况）</p>



<pre class="prettyprint"><code class=" hljs avrasm">
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ch</span>1<span class="hljs-preprocessor">.type</span> = memory
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ch</span>1<span class="hljs-preprocessor">.capacity</span> = <span class="hljs-number">100000000</span>
agent<span class="hljs-preprocessor">.channels</span><span class="hljs-preprocessor">.ch</span>1<span class="hljs-preprocessor">.transactionCapacity</span> = <span class="hljs-number">100000000</span>

agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.type</span> = <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.tesnik</span><span class="hljs-preprocessor">.flume</span><span class="hljs-preprocessor">.sink</span><span class="hljs-preprocessor">.MySqlSink</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.url</span> = jdbc:mysql://<span class="hljs-number">172.168</span><span class="hljs-number">.27</span><span class="hljs-number">.58</span>:<span class="hljs-number">3306</span>/imvno?autoReconnect=true&amp;failOverReadOnly=false
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.user</span>= root
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.password</span>= <span class="hljs-number">123456</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.driver</span>= <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.jdbc</span><span class="hljs-preprocessor">.Driver</span>
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.databaseName</span> = imvno
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.tableName</span> = mvno_cdma_prepaid
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.partition</span> = \\|
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.iscustom</span> = false
agent<span class="hljs-preprocessor">.sinks</span><span class="hljs-preprocessor">.k</span>1<span class="hljs-preprocessor">.channel</span> = ch1
</code></pre>

<p>运行起来 ok</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      hadoop入门之与hive及hbase集成配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p> 公司最近要做运营分析系统，刚接触hadoop不久，至少先搞清楚hadoop生态系统中的工具，搞了2个星期，现在记录一些关于hive和hbase和hadoop 集成的配置。</p>
<p> 在<a href="http://blog.csdn.net/huyou1983216/article/details/50518347" rel="nofollow" style="list-style:none;text-decoration:none;color:rgb(59,89,152);font-family:Verdana, 'Lucida Grande', Arial, Helvetica, sans-serif;">Hadoop部署配置笔记</a> 中先搞明白了hadoop的集群部署，现在又来做和hive及hbase
 的集成，我的终极目标是 hbase 和phoenix 集成，使用phoenix 用java 代码采用jdbc 方式查询hbase ，搞明白presto 和 hive 集成 及 spark 和 hbase 集成，那么hive 和hbase的配置必不可少的要学会它；</p>
<p>         一、准备工作</p>
<p>                 apache  hive1.0.1</p>
<p> <span> </span>         apache hbase1.0.1.1<br><span></span>mysql 5.17</p>
<p>         二、环境配置  </p>
<p><span></span> 如果没有vim命令可以先用yum install vim 安装下 ，mysql 的安装就不说了，网上有很多，我这里配置了maven 的环境变量是为了编译presto和spark，此处hive 我只安装在namenode上，只是测试。</p>
<p>             </p>
<pre><code class="language-plain"> vim /etc/profile</code></pre><br><p></p>
<p></p>
<pre><code class="language-plain">export JAVA_HOME=/java/jdk1.8.0_65
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib
export PATH=${JAVA_HOME}/bin:$PATH
export HADOOP_HOME=/home/hadoop/hadoop
export HIVE_HOME=/home/hadoop/hive
export PATH=$PATH:$HADOOP_HOME/bin:$HIVE_HOME/bin
export MYSQL_HOME=/usr/local/mysql
export PATH=$PATH:$MYSQL_HOME/bin
export MAVEN_HOME=/usr/local/maven
export PATH=$PATH:${MAVEN_HOME}/bin
export HBASE_HOME=/home/hadoop/hbase
export PATH=$PATH:$HBASE_HOME/bin</code></pre>
<p></p>
<p><br></p>
<p>   三、配置文件</p>
<p>    在修改配置文件之前 还是要先创建一些hive需要使用的文件夹,这之前在mysql 数据库中先创建一个数据库，我这数据库名字使用hive，这个mysql数据库是存放hive的元数据，也可以使用其他关系型数据库，我这使用mysql，将mysql-connector-java-5.1.38-bin.jar拷贝到/home/hadoop/hive/lib下，因为需要mysql驱动去创建hive的元数据</p>
<p> hive 配置文件配置  <br></p>
<pre><code class="language-plain">mkdir /home/hadoop/hive/warehouse &amp;&amp; mkdir /home/hadoop/hive/tmp </code></pre><pre><code class="language-plain">cp /home/hadoop/mysql-connector-java-5.1.38-bin.jar /home/hadoop/hive/lib  </code></pre><pre><code class="language-plain">vim / home/hadoop/hive/conf/hive-site.xml</code></pre><pre><code class="language-html">&lt;span style="font-family: Arial, Helvetica, sans-serif;"&gt;&lt;pre name="code" class="plain"&gt;&lt;?xml version="1.0" encoding="UTF-8" standalone="no"?&gt;&lt;/span&gt;</code></pre><pre><code class="language-html">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;
&lt;configuration&gt;
        &lt;property&gt;
                &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
                &lt;value&gt;jdbc:mysql://master:3306/hive&lt;/value&gt;
        &lt;/property&gt;
&lt;property&gt; 
   &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt; 
&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt; 
&lt;description&gt;驱动名&lt;/description&gt; 
&lt;/property&gt;
 &lt;property&gt; 
&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt; 
&lt;value&gt;hive&lt;/value&gt; 
&lt;description&gt;用户名&lt;/description&gt;
 &lt;/property&gt;
 &lt;property&gt;
 &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
 &lt;value&gt;hive&lt;/value&gt; 
&lt;description&gt;密码&lt;/description&gt; 
&lt;/property&gt; 
&lt;property&gt; 
&lt;name&gt;hive.metastore.warehouse.dir&lt;/name&gt;
 &lt;value&gt;hdfs://master:9000/home/hadoop/hive/warehouse&lt;/value&gt; 
&lt;description&gt;数据路径（相对hdfs）&lt;/description&gt; 
&lt;/property&gt; 
&lt;property&gt; 
&lt;name&gt;hive.exec.scratchdir&lt;/name&gt; 
&lt;value&gt;hdfs://master:9000/home/hadoop/hive/warehouse&lt;/value&gt;
 &lt;/property&gt;
 &lt;property&gt;
 &lt;name&gt;hive.querylog.location&lt;/name&gt; 
&lt;value&gt;/home/hadoop/hive/log&lt;/value&gt; 
&lt;/property&gt; 
&lt;property&gt;
 &lt;name&gt;hive.aux.jars.path&lt;/name&gt;
 &lt;value&gt;file:///home/hadoop/hive/lib/hive-hbase-handler-1.2.1.jar, file:///home/hadoop/hbase/lib/protobuf-java-2.5.0.jar, file:///home/hadoop/hbase/lib/hbase-common-1.0.1.1.jar, file:///home/hadoop/hbase/lib/hbase-client-1.0.1.1.jar, file:///home/hadoop/hbase/lib/zookeeper-3.4.6.jar, file:///home/hadoop/hbase/lib/guava-12.0.1.jar&lt;/value&gt;
 &lt;/property&gt; 
&lt;property&gt; 
&lt;name&gt;hive.metastore.uris&lt;/name&gt; 
&lt;value&gt;thrift://192.168.200.96:9083&lt;/value&gt;
 &lt;description&gt;运行hive得主机地址及端口&lt;/description&gt;
 &lt;/property&gt;
&lt;/configuration&gt;</code></pre><br><br><pre></pre>
<p></p>
<pre></pre>
<p></p>
<p></p>
<pre style="overflow:auto;color:rgb(77,77,79);font-size:13px;line-height:20px;background-color:rgb(249,249,212);">修改日志配置文件</pre>
<p></p>
<pre><code class="language-html">cp hive-log4j.properties.template hive-log4j.properties</code></pre><pre><code class="language-html">vim hive-log4j.properties</code></pre>
<pre style="overflow:auto;color:rgb(77,77,79);font-size:13px;line-height:20px;background-color:rgb(249,249,212);"></pre>
<pre><code class="language-html">log4j.appender.EventCounter=org.apache.hadoop.log.metrics.EventCounter</code></pre><br><br><br><br><br><br><p>重点说一下</p>
<pre><code class="language-html">hive.aux.jars.path ，这个是运行hive的cli时引入的jar包，如果要使用hive创建hbase的表，那么就需要引入 上面的jar包</code></pre><pre><code class="language-html">&lt;/pre&gt;&lt;p&gt;&lt;/p&gt;hbase 配置文件配置&lt;p&gt;hbase-site.xml&lt;/p&gt;&lt;p&gt;&lt;/p&gt;&lt;pre name="code" class="html"&gt;&lt;configuration&gt;
 &lt;property&gt;
  &lt;name&gt;hbase.rootdir&lt;/name&gt;
  &lt;value&gt;hdfs://master:9000/hbase&lt;/value&gt;
 &lt;/property&gt;

 &lt;property&gt;
  &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
  &lt;value&gt;true&lt;/value&gt;
 &lt;/property&gt;

 &lt;property&gt;
   &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
   &lt;value&gt;master,node1,node2&lt;/value&gt;
 &lt;/property&gt;

 &lt;property&gt;
   &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;
   &lt;value&gt;/home/hadoop/hadoop/zookeeper&lt;/value&gt;
 &lt;/property&gt;

 &lt;property&gt;
    &lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;
    &lt;value&gt;2181&lt;/value&gt;
  &lt;/property&gt;

  &lt;property&gt;
        &lt;name&gt;hbase.coprocessor.user.region.classes&lt;/name&gt;
        &lt;value&gt;org.apache.hadoop.hbase.coprocessor.AggregateImplementation&lt;/value&gt;
&lt;/property&gt;</code></pre><br><br>
hbase-env.sh 更改jdk环境变量
<p></p>
<p> </p>
<pre><code class="language-plain">export JAVA_HOME=/java/jdk1.8.0_65/</code></pre>
<p></p>
<p><br></p>
<p>然后将hbase 文件夹分发node1 和node 2上</p>
<p>scp -r hbase hadoop@node1:/home/hadoop</p>
<p>scp -r hbase hadoop@node2:/home/hadoop</p>
<br>
按照上面简单配置hbase 和hive 就配置完了，不是说整合吗？哪里整合了，下面继续
<p><br></p>
<p>四、 启动hbase 和hive </p>
<p></p>
<pre><code class="language-plain">[hadoop@master bin]$  start-hbase.sh</code></pre><br>
查看hbase 启动日志 如果里面没有乱七八糟的错误堆栈信息，那么在去看下hbase的进程启动情况
<p></p>
<p>master 进程</p>
<p></p>
<pre><code class="language-plain">[hadoop@master bin]$ jps
19881 NameNode
23118 JobTracker
10233 HMaster
19188 Jps
14066 SecondaryNameNode</code></pre><br>
node1 和node2 上
<p></p>
<p></p>
<pre><code class="language-plain">[hadoop@node1 ~]$ jps
21685 HRegionServer
21608 HQuorumPeer
19581 DataNode
21748 Jps
19679 TaskTracker</code></pre><br>
如果进程都启动了，在master 上
<p></p>
<p><span style="font-family:Arial;font-size:14px;line-height:26px;">执行hbsae shell命令，进入hbase控制台</span><br></p>
<p><span style="font-family:Arial;font-size:14px;line-height:26px;">输入： hbase shell 进入控制台，然后输入list 显示hbase 下的表 说明hbase 部署成功了</span></p>
<p><span style="font-family:Arial;font-size:14px;line-height:26px;"><img src="https://img-blog.csdn.net/20160115094235442?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></span></p>
<p><br></p>
<p>用hbase 去创建表比较麻烦不如sql 语句来的直观，下面我们在hive 里面进行创建hbase 表的实验</p>
<p>hive 启动</p>
<p>此次我们只是使用hive的元数据，所以启动matestore 服务,加&amp;是在后台启动</p>
<p></p>
<pre><code class="language-plain">hive --service metastore &amp;</code></pre><pre><code class="language-plain">&lt;/pre&gt;&lt;pre name="code" class="plain"&gt;ps aux|grep hive </code></pre><br><img src="https://img-blog.csdn.net/20160115104220475?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p></p>
<p><br></p>
<p>hive metastore 启动后会在mysql 数据库初始化之前创建的hive 数据库</p>
<p><img src="https://img-blog.csdn.net/20160115105157717?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><br></p>
<p>然后进入hive 命令行进行操作</p>
<p></p>
<pre><code class="language-html">hive</code></pre><br><img src="" alt=""><pre><code class="language-html">show tables;</code></pre>输入 show tables ; 查看当前默认数据库下的表
<p></p>
<p><br></p>
<p>输入下面建表语句，创建 hive 和 hbase 关联的表，如果不出现异常，则说明hive 和 hbase 已经可以关联了</p>
<p></p>
<pre><code class="language-plain">CREATE  TABLE default.order4(
id BIGINT,
code STRING,
name STRING,
status STRING,
quantity FLOAT,
ordertype STRING,
detailsize FLOAT,
companyid BIGINT,
creater STRING,
create_time DATE,
updater STRING,
update_time DATE
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.hbase.HBaseSerDe'
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping"=":key,data:code,data:name,data:status,data:quantity,data:ordertype,data:detailsize,data:companyid,data:creater,data:create_time,data:updater,data:update_time")
TBLPROPERTIES("hbase.table.name" = "order4")
;</code></pre><br>
我们可以导入数据进行测试下，
<p></p>
<p>先put 一个 txt 文件到 hdfs 中</p>
<p></p>
<pre><code class="language-plain">hadoop dfs -put 111.txt /tmp</code></pre><br><br><p></p>
<p>111.txt文件内容为，随便搞几条</p>
<p><img src="https://img-blog.csdn.net/20160115111305974?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p> </p>
<p>使用hbase  ImportTsv 命令进行导入</p>
<p>导入形式分为2种：</p>
<p> 直接导入： </p>
<pre><code class="language-plain">hbase org.apache.hadoop.hbase.mapreduce.ImportTsv  -Dimporttsv.separator=',' -Dimporttsv.columns=HBASE_ROW_KEY,data:code,data:name,data:status,data:quantity,data:ordertype,data:detailsize,data:companyid,data:creater,data:create_time,data:updater,data:update_time  order4 /tmp/111.txt</code></pre><br><p></p>
<p></p>
<pre><code class="language-plain">HBASE_ROW_KEY 这个是rowkey 后面跟着是烈簇和列格式为 colFamily:col,....</code></pre><br><pre><code class="language-plain">-Dimporttsv.separator=',' 这个是使用逗号分隔数据   </code></pre><pre><code class="language-plain">&lt;/pre&gt;&lt;pre name="code" class="plain"&gt;后面结尾处order4 是表名 和刚才 put 到hdfs 中的文件</code></pre><br>
bulk load 方式：<br><p></p>
<p></p>
<pre><code class="language-html">hbase org.apache.hadoop.hbase.mapreduce.ImportTsv  -Dimporttsv.separator=',' -Dimporttsv.bulk.output=/home/hadoop/  -Dimporttsv.columns=HBASE_ROW_KEY,data:code,data:name,data:status,data:quantity,data:ordertype,data:detailsize,data:companyid,data:creater,data:create_time,data:updater,data:update_time  order4 /tmp/111.txt   这个是先生成hfile文件</code></pre><pre><code class="language-html">&lt;/pre&gt;&lt;pre name="code" class="html"&gt;然后在通过：</code></pre><pre><code class="language-html">hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /home/hadoop/order4 order4 在将hfile导入到hbase中
</code></pre>
<p></p>
<p><br></p>
然后就是一大堆 map执行输出的日志 后面打印的结果 有一行是badLine 是0 说明就导入成功了
<p><br></p>
<p>然后进入hive 命令行 查看数据</p>
<p>select * from order3 limit 10;</p>
<p><br></p>
<p><br><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p>                          </p>
            </div>
                </div>
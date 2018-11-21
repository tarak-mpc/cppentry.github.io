---
layout:     post
title:      Hadoop完全分布式安装Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：每个人都是自己的上帝。如果你自己都放弃自己了，还有谁会救你？------《肖申克的救赎》本文为博主原创文章，欢迎转载（记得注明出处，拜托~）。					https://blog.csdn.net/hu_belif/article/details/83308004				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>   编译安装 Hive</h1>

<p>如果需要直接安装 Hive，可以跳过编译步骤，从 Hive 的官网下载编译好的安装包，下载地址为<a href="http://hive.apache.org/downloads.html" rel="nofollow">http://hive.apache.org/downloads.html</a>。</p>

<p>Hive的环境配置需要MySQL的支持，所以首先需要安装MySQL，没有安装MySQL的先看上一篇：<a href="https://blog.csdn.net/hu_belif/article/details/83338961" rel="nofollow">安装MySQL</a></p>

<h2><br>
C.1 编译Hive</h2>

<h3>C.1.1 下载 Hive 源代码包</h3>

<p>在 Hive 的官网下载页面上下载，为加快下载速度选择中国境内的镜像 ，并下载apache-hive-1.2.1-</p>

<h3>C.1.2 编译 Hive</h3>

<p>编译 Hive 源代码的时候，需要从网上下载依赖包，所以整个编译过程机器必须保证在联网状态。编译执行如下脚本：</p>

<pre class="has">
<code>$cd /app/compile/hive-1.2.1-src
$export MAVEN_OPTS="-Xmx2g -XX:MaxPermSize=512M -XX:ReservedCodeCacheSize= 512m"
$mvn -Phadoop-2-Pdist-DskipTests -Dmaven.javadoc.skip=trueclean package</code></pre>

<p>在编译过程中可能出现速度慢或者中断，可以再次启动编译，编译程序会在上次的编译中断处继续进行编译，整个编译过程耗时与网速紧密相关，网速较快的情况需要 1h 左右（图附录C-1 的时间是重复多次下载依赖包，然后编译成功的界面），最终编打包的文件为$HIVE_HOME/packaging/target/apache-hive-1.2.1-bin.tar.gz。<br><img alt="" class="has" height="421" src="https://img-blog.csdn.net/20181024095019395?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="483"><br>
通过如下命令查看最终编译完成整个目录大小，可以看到大小为 350MB 左右：</p>

<pre class="has">
<code>$du -s /app/compile/hive-1.2.1-src</code></pre>

<h2>C.2 安装 Hive</h2>

<h3>C.2.1 解压缩并迁移</h3>

<p>使用上一步骤编译好的 Hive 编译包移动到安装目录上，用如下命令解压缩 hive 安装文件：</p>

<pre class="has">
<code>$cd /app/compile/hive-1.2.1-src/packaging/target/
$mv apache-hive-1.2.1-bin.tar.gz /home/spark/work/
$cd /home/spark/work/
$tar -zxf hive-1.2.1-bin.tar.gz</code></pre>

<p>改名并迁移到/usr/local目录下：<br>
 </p>

<pre class="has">
<code>$cd /usr/local
$mv apache-hive-1.2.1-bin /usr/local/hive-1.2.1
$ll /usr/local</code></pre>

<p><br><img alt="" class="has" height="203" src="https://img-blog.csdn.net/20181024094936972?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="731"><br><br>
登录 Mysql 官网进入下载页面 <a href="http://dev.mysql.com/downloads/connector/j/" rel="nofollow">http://dev.mysql.com/downloads/connector/j/</a>，默认情况下是Windows 安装包，这里需要选择Platform Independent 版本下载 zip 格式的文件， 如图附录 C-3所示。<br><img alt="" class="has" height="377" src="https://img-blog.csdn.net/20181024094842802?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="838"><br>
把下载的 hive 安装包和 mysql 驱动包，使用如下命令放到 Hive 的 lib 目录下：</p>

<pre class="has">
<code>$cd /home/spark/work
$mv mysql-connector-java-5.1.34-bin.jar /usr/local/hive-1.2.1/lib</code></pre>

<h3><br>
C.2.3 配置/etc/profile 环境变量</h3>

<p>使用如下命令打开/etc/profile 文件，设置如下参数：</p>

<pre class="has">
<code>export HIVE_HOME=/usr/local/hive-1.2.1
export PATH=$PATH:$HIVE_HOME/bin
export CLASSPATH=$CLASSPATH:$HIVE_HOME/bin</code></pre>

<p>配置完毕后，需要编译该配置文件或重新登录以生效该配置：</p>

<pre class="has">
<code>$source /etc/profile</code></pre>

<h3>C.2.4 设置 hive-env.sh 配置文件</h3>

<p>进入 hive-1.2.1/conf 目录，复制 hive-env.sh.templaete 为 hive-env.sh 并进行配置：</p>

<pre class="has">
<code>$cd /usr/local/hive-1.2.1/conf
$cp hive-env.sh.template hive-env.sh
$sudo vi hive-env.sh</code></pre>

<p>分别设置 HADOOP_HOME 和 HIVE_CONF_DIR 两个值：</p>

<pre class="has">
<code># Set HADOOP_HOME to point to a specific hadoop install directory
exportHADOOP_HOME=/usr/local/hadoop-2.7.2
# Hive Configuration Directory can be controlled by:
export HIVE_CONF_DIR=/usr/local/hive-1.2.1/conf</code></pre>

<h3><br>
C.2.5 设置 hive-site.xml 配置文件</h3>

<p>创建 hive-site.xml 配置文件，在该配置文件中加入以下配置内容：</p>

<pre class="has">
<code>$touch hive-site.xml
$sudo vi hive-site.xml</code></pre>

<p><br>
hive 默认为 derby 数据库， derby 数据只运行单个用户进行连接，所以这里<span style="color:#f33b45;">需要调整为 Mysql 数据库</span>，以下为修改配置内容：</p>

<pre class="has">
<code>&lt;?xml version="1.0" encoding="UTF-8" standalone="no"?&gt;
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;
&lt;configuration&gt;
&lt;property&gt;
&lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;
&lt;value&gt;jdbc:mysql://master:3306/hive?createDatabaseIfNotExsit=true;chara
cterEncoding=UTF-8&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;
&lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;
&lt;value&gt;hive&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;
&lt;value&gt;hive&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;datanucleus.readOnlyDatastore&lt;/name&gt;
&lt;value&gt;false&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;datanucleus.fixedDatastore&lt;/name&gt;
&lt;value&gt;false&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;datanucleus.autoCreateSchema&lt;/name&gt;
&lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;datanucleus.autoCreateTables&lt;/name&gt;
&lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;datanucleus.autoCreateColumns&lt;/name&gt;
&lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;</code></pre>

<h2><br>
C.3 启动 Hive 并验证</h2>

<h3>C.3.1 启动 Hive</h3>

<p>实际使用时，一般通过后台启动 metastore 和 hiveserver 实现服务，命令如下：</p>

<pre class="has">
<code>$hive --service metastore &amp;
$hive --service hiveserver2&amp;</code></pre>

<p>启动用通过 jps 命令可以看到两个服务在后台运行， 如图附录 C-4 所示。<br><br><img alt="" class="has" height="336" src="https://img-blog.csdn.net/20181024094723429?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="491"></p>

<h3><br>
C.3.2 验证安装</h3>

<p>登录 hive，在 hive 创建表并查看该表，如图附录 C-5 所示，命令如下：</p>

<pre class="has">
<code>$hive
hive&gt; create table test(a string, b int);
hive&gt; show tables;
hive&gt; desc test;</code></pre>

<p>登录 Mysql，在 TBLS 表中查看新增 test 表：</p>

<pre class="has">
<code>$mysql -uhive -phive
mysql&gt; use hive;
mysql&gt; select TBL_ID, CREATE_TIME, DB_ID, OWNER, TBL_NAME,TBL_TYPE from TBLS;</code></pre>

<p><br><img alt="" class="has" height="327" src="https://img-blog.csdn.net/20181024094644751?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="490"><br><br><img alt="" class="has" height="365" src="https://img-blog.csdn.net/2018102409460291?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="493"></p>

<h2>C.4 Hive 实例演示</h2>

<h3>C.4.1 准备数据</h3>

<p><strong>1． 上传数据</strong><br>
交易数据存放在该系列配套资源的/saledata 目录下，把这些数据文件上传到 master 节点的/home/spark/word 目录下。<br><strong>2． 启动 Hive 并创建数据库</strong><br>
启动 HDFS、 YARN 和 Hive，启动完毕后创建 Hive 数据库：</p>

<pre class="has">
<code>$hive --service metastore &amp;
$hive
hive&gt; create database hive;
hive&gt; show databases;
hive&gt; use hive;</code></pre>

<p><strong>3． 在 Hive 创建表</strong><br>
启动 Hadoop 集群，进入 Hive 命令行操作界面，使用如下命令创建 3 张数据表。</p>

<ul><li>tbDate：定义了日期的分类，将每天分别赋予所属的月份、星期、季度等属性，字段</li>
	<li>分别为日期、年月、年、月、日、周几、第几周、季度、旬、半月。</li>
	<li>tbStock：定义了订单表头，字段分别为订单号、交易位置、交易日期。</li>
	<li>tbStockDetail：定义了订单明细，该表和 tbStock 以交易号进行关联，字段分别为订单</li>
	<li>号、行号、货品、数量、金额。</li>
</ul><blockquote>
<p>hive&gt; CREATE TABLE tbDate(dateID string,theyearmonth string,theyear string,<br>
themonth string,thedate string,theweek string,theweeks string,thequot string,<br>
thetenday string,thehalfmonth string) ROW FORMAT DELIMITED FIELDS TERMINATED<br>
BY ＇ ,＇ LINES TERMINATED BY ＇ \n＇ ;<br>
Time taken: 1.121 seconds<br>
hive&gt; CREATE TABLE tbStock(ordernumber STRING,locationid string,dateID string)<br>
ROW FORMAT DELIMITED FIELDS TERMINATED BY ＇ ,＇ LINES TERMINATED BY ＇ \n＇ ;<br>
Time taken: 0.166 seconds<br>
hive&gt; CREATE TABLE tbStockDetail(ordernumber STRING,rownum int,itemid string,qty<br>
int,price int ,amount int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ＇ ,＇ LINES<br>
TERMINATED BY ＇ \n＇ ;<br>
Time taken: 0.267 seconds<br><br>
hive&gt; show tables;<br>
OK<br>
tbdate<br>
tbstock<br>
tbstockdetail<br>
Time taken: 0.089 seconds, Fetched: 3 row(s)</p>
</blockquote>

<p><strong>4． 导入数据</strong><br>
从本地操作系统分别加载日期、交易信息和交易详细信息表数据：</p>

<blockquote>
<p>hive&gt; LOAD DATA LOCAL INPATH ＇ /home/spark/work/saledata/tbDate.txt＇ INTO TABLE<br>
tbDate;<br>
Loading data to table hive.tbdate<br>
OK<br>
Time taken: 2.784 seconds<br>
hive&gt; LOAD DATA LOCAL INPATH ＇ /home/spark/work/saledata/tbStock.txt＇ INTO TABLE<br>
tbStock;<br>
Loading data to table hive.tbstock<br>
OK<br>
Time taken: 0.648 seconds<br>
hive&gt;LOAD DATA LOCAL INPATH ＇ /home/spark/work/saledata/tbStockDetail.txt＇ INTO<br>
TABLE tbStockDetail;<br>
Loading data to table hive.tbstockdetail<br>
OK<br>
Time taken: 1.44 seconds</p>
</blockquote>

<p>查看 HDFS 中相关 SALEDATA 数据库中增加了 3 个文件夹，分别对应 3 张表：</p>

<blockquote>
<p>[spark@master ~]$ hadoop fs -ls /user/hive/warehouse/hive.db<br>
Found 3 items<br>
drwxr-xr-x- spark 2016-04-1415:18 /user/hive/warehouse/hive.db/ tbdate<br>
drwxr-xr-x- spark 2016-04-1415:18 /user/hive/warehouse/ hive.db/ tbstock<br>
drwxr-xr-x- spark 2016-04-14 15:18 /user/hive/warehouse/hive.db/ tbstockdetail</p>
</blockquote>

<h3>C.4.2 计算所有订单每年的总金额</h3>

<p><strong>1． 算法分析</strong><br>
要计算所有订单每年的总金额，首先需要获取所有订单的订单号、订单日期和订单金信息，<br>
然后把这些信息和日期表进行关联，获取年份信息，最后根据这 4 个列按年份归组统计获取所<br>
有订单每年的总金额。<br><br><strong>2． 执行 HSQL 语句</strong><br>
 </p>

<pre class="has">
<code>hive&gt; use hive;
hive&gt; select c.theyear, sum(b.amount) from tbStock a,tbStockDetail b,tbDate c
where a.ordernumber=b.ordernumber and a.dateid=c.dateid group by c.theyear
order by c.theyear;</code></pre>

<p><br>
运 行 过 程 中 创 建 两 个 Job ， 分 别 为 application_1460617800545_0001 和 application_1460617800545_000，在 YARN 的资源管理器界面中（默认 http://master:8088/），可以看到如图附录 C-7 所示的界面。</p>

<h3><img alt="" class="has" height="464" src="https://img-blog.csdn.net/20181024094427113?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1022"><br>
3． 查看结果<br>
整个计算过程使用了 175.25s，结果如图附录 C-8 所示。<br><br><img alt="" class="has" height="400" src="https://img-blog.csdn.net/20181024094355298?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="784"><br>
C.4.3 计算所有订单每年最大金额订单的销售额</h3>

<p><strong>1． 算法分析</strong><br>
该算法分为两步：<br>
（1）按照日期和订单号进行归组计算，获取所有订单每天的销售数据。<br>
（2）把第一步获取的数据和日期表进行关联获取的年份信息，然后按照年份进行归组，使用 Max 函数，获取所有订单每年最大金额订单的销售额。<br><strong>2． 执行 HSQL 语句</strong><br>
//第一步：按照日期和订单号进行归组计算，获取所有订单每天的销售数据</p>

<pre class="has">
<code>hive&gt; select a.dateid,a.ordernumber,sum(b.amount) as sumofamount from tbStock
a, tbStockDetail b where a.ordernumber=b.ordernumber group by a.dateid,
a.ordernumber;</code></pre>

<p>//第二步: 按照年份进行归组，使用 Max 函数，获取所有订单每年最大金额订单的销售额</p>

<pre class="has">
<code>hive&gt; select c.theyear,max(d.sumofamount) from tbDate c,(select a.dateid,
a.ordernumber,sum(b.amount) as sumofamount from tbStock a,tbStockDetail b
where a.ordernumber=b.ordernumber group by a.dateid,a.ordernumber) d where
c.dateid=d.dateid group by c.theyear sort by c.theyear;</code></pre>

<p>运行过程中创建两个 Job，分别为 job_1437659442092_0004 和 job_1437659442092_0005，在 YARN 的监控界面中可以看到如图附录 C-9 所示的界面。<img alt="" class="has" height="375" src="https://img-blog.csdn.net/20181024094242355?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1020"><br><strong>3． 查看结果</strong><br>
整个计算过程使用了 171.41s，结果如图附录 C-10 所示。<br><img alt="" class="has" height="431" src="https://img-blog.csdn.net/20181024094205640?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2h1X2JlbGlm/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="887"></p>            </div>
                </div>
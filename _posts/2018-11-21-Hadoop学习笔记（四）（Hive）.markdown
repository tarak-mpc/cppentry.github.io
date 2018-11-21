---
layout:     post
title:      Hadoop学习笔记（四）（Hive）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/victorzzzz/article/details/82261106				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>Hive：</strong>解决结构化日志数据统计问题，构建在数据仓库中，定义了HQL语言（类似SQL）</p>

<ul><li>通常进行离线数据处理</li>
	<li>统一元数据管理</li>
	<li>有超大数据集设计的计算，存储扩展能力</li>
</ul><p>      <br><strong>Hive底层的执行引擎有：</strong><u><em>MapReduce</em></u>、<u><em>Tez</em></u>、<u><em>Spark</em></u><br>
    Hive on MapReduce<br>
    Hive on Tez<br>
    Hive on Spark</p>

<p>Hive支持多种压缩格式、存储格式<br><strong>压缩：</strong>GZIP、LZO、Snappy、BZIP2..<br><strong>存储：</strong>TextFile、SequenceFile、RCFile、ORC、Parquet<br><strong>UDF：</strong>自定义函数</p>

<p><strong>（hive架构图...）</strong></p>

<p><strong>Hive环境搭建</strong><br><strong>1）Hive下载：</strong>http://archive.cloudera.com/cdh5/cdh/5/<br>
    wget http://archive.cloudera.com/cdh5/cdh/5/hive-1.1.0-cdh5.7.0.tar.gz</p>

<p><strong>2）解压</strong><br>
    tar -zxvf hive-1.1.0-cdh5.7.0.tar.gz -C ~/app/</p>

<p><strong>3）配置</strong><br>
    系统环境变量(~/.bash_profile)<br>
        export HIVE_HOME=/home/hadoop/app/hive-1.1.0-cdh5.7.0<br>
        export PATH=$HIVE_HOME/bin:$PATH</p>

<p>    实现安装一个mysql， yum install xxx<br>
    1)cp hive-env.sh.template hive-env.sh<br>
    设置HADOOP_HOME<br>
    <br>
    2)直接创建<br>
    hive-site.xml<br>
    &lt;?xml version="1.0"?&gt;<br>
    &lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br>
    &lt;configuration&gt;<br>
    &lt;property&gt;<br>
          &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;<br>
        &lt;value&gt;jdbc:mysql://localhost:3306/sparksql?createDatabaseIfNotExist=true&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    <br>
    &lt;property&gt;<br>
        &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;<br>
        &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;<br>
       &lt;/property&gt;</p>

<p>    &lt;property&gt;<br>
          &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;<br>
        &lt;value&gt;root&lt;/value&gt;<br>
    &lt;/property&gt;</p>

<p>    &lt;property&gt;<br>
          &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;<br>
        &lt;value&gt;123&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;/configuration&gt;</p>

<p><strong>4）拷贝mysql驱动到$HIVE_HOME/lib/<br>
5 ) ./hive --service metastore &amp;<br>
6）启动hive: $HIVE_HOME/bin/hive</strong></p>

<p><span style="color:#f33b45;">出现的问题：</span>FAILED: Execution Error, return code 1 from org.apache.hadoop.hive.ql.exec.DDLTask. MetaException(message:For direct MetaStore DB connections, we don't support retries at the client level.)<br><strong><span style="color:#ffbb66;">解决：</span></strong>mysql&gt; alter database hive character set latin1;</p>

<p><br><strong>创建表</strong><br>
1）CREATE  TABLE table_name<br>
  [(col_name data_type [COMMENT col_comment])]<br>
 </p>

<p>实例：create table hive_wordcount(context string);</p>

<p><strong>加载数据到hive表</strong><br>
2）LOAD DATA LOCAL INPATH 'filepath' INTO TABLE tablename</p>

<p>实例：load data local inpath '/home/hadoop/data/hello.txt' into table hive_wordcount;</p>

<p><br>
select word, count(1) from hive_wordcount lateral view explode(split(context,'\t')) wc as word group by word;<br>
//lateral view explode(): 是把每行记录按照指定分隔符进行拆解</p>

<p>hive ql提交执行以后会生成mr（mapreduce）作业，并在yarn上运行</p>

<p>另一个例子：<br>
create table emp(<br>
empno int,<br>
ename string,<br>
job string,<br>
mgr int,<br>
hiredate string,<br>
sal double,<br>
comm double,<br>
deptno int<br>
) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';</p>

<p>create table dept(<br>
deptno int,<br>
dname string,<br>
location string<br>
) ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t';</p>

<p>load data local inpath '/home/hadoop/data/emp.txt' into table emp;<br>
load data local inpath '/home/hadoop/data/dept.txt' into table dept;</p>

<p>求每个部门的人数<br>
select deptno, count(1) from emp group by deptno;</p>            </div>
                </div>
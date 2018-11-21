---
layout:     post
title:      [Hadoop] CentOS7 安装flume-ng-1.6.0-cdh5.7.0
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wawa8899/article/details/82897499				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2> </h2>

<h2>1. Flume</h2>

<p>Flume官方文档：<a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow">http://flume.apache.org/FlumeUserGuide.html</a><br>
Flume官方文档(CDH指定版本)：<a href="http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/" rel="nofollow">http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0/</a></p>

<p>Flume是一个分布式的日志收集框架，针对日志数据进行采集汇总，把日志从A地方搬运到B地方去。</p>

<p><strong>使用场景：</strong></p>

<ul><li>RDBMS ==&gt; Sqoop ==&gt; Hadoop</li>
	<li>分散在各个服务器上的日志 ==&gt; Flume ==&gt; Hadoop</li>
</ul><p><strong>Flume三大组件：</strong></p>

<ul><li>collecting 采集 source</li>
	<li>aggregating 聚合 channel （找个地方把采集过来的数据暂存下）</li>
	<li>moving 移动 sink</li>
</ul><p><strong>Flume开发</strong>： 编写配置文件，组合source、channel、sink三者之间的关系。而Agent就是由source、channel、sink组成，所以编写flume的配置文件其实就是配置agent的构成。</p>

<p><a href="https://user-images.githubusercontent.com/13764902/43403027-a5cb9494-9446-11e8-94d7-f310433d51eb.png" rel="nofollow"><img alt="image" class="has" src="https://user-images.githubusercontent.com/13764902/43403027-a5cb9494-9446-11e8-94d7-f310433d51eb.png"></a></p>

<p> </p>

<p> </p>

<h2>2. Flume 安装部署</h2>

<p>根据官方文档描述，市面上的Flume主流版本有两个：0.9.x and 1.x。这两个版本差异非常非常大，旧版本已经被淘汰了，要用的话就使用新版本。当然本文中既定版本为cdh的ng版本（flume-ng-1.6.0-cdh5.7.0）。</p>

<p>系统要求：</p>

<p><img alt="image" class="has" src="https://user-images.githubusercontent.com/13764902/43403817-b273a0a4-9448-11e8-9e88-b35e51f90898.png"></p>

<p> 下载安装</p>

<pre class="has">
<code>#  下载解压
[hadoop@hadoop01 software]$ wget http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.6.0-cdh5.7.0.tar.gz
[hadoop@hadoop01 software]$ tar -zxvf flume-ng-1.6.0-cdh5.7.0.tar.gz -C ~/app/

# 配置环境变量（使用root用户）
[root@hadoop01 ~]# vi /etc/profile
export FLUME_HOME=/home/hadoop/app/apache-flume-1.6.0-cdh5.7.0-bin
export PATH=$FLUME_HOME/bin:$PATH
[root@hadoop01 ~]# source /etc/profile

# 修改配置文件
[hadoop@hadoop01 software]$ cd ~/app/apache-flume-1.6.0-cdh5.7.0-bin/
[hadoop@hadoop01 apache-flume-1.6.0-cdh5.7.0-bin]$ cp conf/flume-env.sh.template conf/flume-env.sh
[hadoop@hadoop01 apache-flume-1.6.0-cdh5.7.0-bin]$ vi conf/flume-env.sh
export JAVA_HOME=/usr/java/jdk1.8.0_45

# 验证安装完成
[hadoop@hadoop000 ~]$ flume-ng version
Flume 1.6.0-cdh5.7.0
Source code repository: https://git-wip-us.apache.org/repos/asf/flume.git
Revision: 8f5f5143ae30802fe79f9ab96f893e6c54a105d1
Compiled by jenkins on Wed Mar 23 11:38:48 PDT 2016
From source with checksum 50b533f0ffc32db9246405ac4431872e
[hadoop@hadoop000 ~]$</code></pre>

<h2> </h2>

<h2>3. Flume 使用</h2>

<p><strong>概念解释</strong></p>

<p>Flume的使用包含两个步骤：1-建立一个agent（写配置文件）；2-启动agent。<br>
(1) agent配置文件名词解释</p>

<ul><li>a1：&lt;agent_name&gt;，agent的名称</li>
	<li>r1：&lt;source_name&gt;</li>
	<li>k1：&lt;sink_name&gt;</li>
	<li>c1：&lt;channel_name&gt;</li>
</ul><p>(2) Flume支持的常用source、channel、sink有哪些</p>

<ul><li>
	<p>source<br>
	-- avro ==&gt; 监听一个指定的Avro端口，通过Avro端口可以获取到Avro client发送过来的文件。只要应用程序通过Avro端口发送文件，source组件就可以获取到该文件中的内容（同thrift类似，都是一种RPC服务框架）<br>
	-- exec ==&gt; 监听一个指定的命令，获取一条命令的结果作为它的数据源。常用的是tail -F file指令，即只要应用程序向日志（文件）里面写数据，source组件就可以获取到日志（文件）中最新的内容。<br>
	-- Spooling Directory ==&gt; 监听一个指定的目录，只要应用程序向这个指定的目录中添加新的文件，source组件就可以获取到该信息，并解析该文件的内容，然后写入到channel。写入完成后，标记该文件已完成或者删除该文件。已完成的文件不能再更新，否则source组件监控不到；也不能向文件夹下放入文件名相同的文件，且文件夹下面不能有子文件夹。<br>
	-- Taildir ==&gt; 相当于前面两个的整合，既可以监控文件也可以监控文件夹，生产上95%以上都是这个场景<br>
	-- netcat ==&gt; 监听一个指定的网络端口，只要应用程序向这个端口里面写数据，这个source组件就可以获取到信息。</p>
	</li>
	<li>
	<p>sink<br>
	-- hdfs<br>
	-- logger ==&gt; 表示打印到控制台<br>
	-- avro ==&gt; 配合avro source使用<br>
	-- kafka</p>
	</li>
	<li>
	<p>channel<br>
	-- memory ==&gt; 数据过来会存在内存的队列里面<br>
	-- file ==&gt; 数据过来存在本地文件</p>
	</li>
</ul><p>配置agent，就是各种组合source、channel、sink之间的关系。source出来可以去多个channel，一个sink只能对应一个channel。</p>

<p>(3) 需求分析过程<br>
-- 需求：把一个文件中新增的内容收集到HDFS上去<br>
-- 分析：exec - memory - hdfs</p>

<p>-- 需求：一个文件夹<br>
-- 分析：spooling -memory - hdfs</p>

<p>-- 需求：文件数据写入kafka<br>
-- 分析：exec - memory - kafka<br>
其中，ETL过程可以放在：exec - memory - hdfs ==&gt; spark/hive/mr ETL ==&gt; hdfs(新的位置) &lt;== 分析。Flume的定位是日志采集，数据清洗的过程不由flume完成，数据丢失了无法处理。</p>

<p> </p>

<p>参考：<a href="https://github.com/cindysz110/blog/issues/31" rel="nofollow">https://github.com/cindysz110/blog/issues/31</a></p>            </div>
                </div>
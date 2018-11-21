---
layout:     post
title:      HBase学习之路——HBase单机安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>一、HBase简介</h3>

<p>HBase是一个分布式的、面向列的开源数据库，一个结构化数据的分布式存储系统。</p>

<p>HBase是Apache的Hadoop项目的子项目，HBase不同与一般的关系数据库，它适合于结构化数据存储的数据库，且HBase基于列而非基于行的模式。</p>

<p>HBase——Hadoop Database，分布式存储系统，利用HBase技术可在廉价PC Server上搭建起大规模结构化存储集群。</p>

<p>HBase利用Hadoop HDFS作为其文件存储系统，利用Hadoop MapReduce处理HBase的海量数据，利用Zookeeper作为协同服务。</p>

<p>HBase位于结构化存储层，Hadoop HDFS为HBase提供了高可靠性的底层存储支持，Hadoop MapReduce为HBase提供高性能的计算能力，Zookeeper为HBase提供稳定服务和failover机制。Pig和Hive为HBase提供高层语言支持，是的在HBase上进行数据统计处理变简单。Sqoop为HBase提供方便的RDBMS数据导入功能，使传统数据库数据向HBase选中前移变方便。</p>

<p><strong>注：HDFS即Hadoop分布式文件系统，Zookeeper即分布式应用程序协调服务，Sqoop即一个用于在Hadoop和传统数据库间数据传递的开源工具，Hive即基于Hadoop的数据仓库工具，适合数据仓库的统计分析，Pig即一种数据流语言和运行环境，用于检索非常大的数据集</strong></p>

<p><strong>注：failover为容错机制，失败自动切换，即当出现失败，重试其他服务器，通常用于读操作。重试会带来更长延迟。</strong></p>

<p><strong>常见容错机制：failover，failsafe（失败安全，即出现异常直接忽略，通常用于写入审计日志操作。可用于生产环境Monitor），failfalse（快速失败，即只发起一次调用，失败立即报错，通常用于非幂等性的写操作。若机器正在重启可能出现调用失败），forking（并行调用多个服务器，即只要一个成功即返回，通常用于实时性要求较高的读操作，需浪费较多服务资源），failback（失败自动恢复，即后台记录失败请求，定时重发，通常用于消息通知操作。可用于生产环境Registry）</strong></p>

<p>HBase中的所有数据文件都存储在Hadoop HDFS文件系统上，主要包括HFile和HLog File。</p>

<h3>二、安装准备</h3>

<ul><li>
	<p>Hadoop2.8.3</p>
	</li>
	<li>
	<p>winutils-master-hadoop2.8.3</p>
	</li>
	<li>
	<p>jdk1.8</p>
	</li>
	<li>
	<p>HBase2.0.0</p>
	</li>
	<li>
	<p>jline-2.12.jar</p>
	</li>
</ul><h3>三、单机部署</h3>

<ol><li>
	<p>完成jdk的配置，配置成功条件cmd输出java -version显示jdk版本。具体步骤自行百度</p>
	</li>
	<li>
	<p>解压hadoop压缩包，具体配置更改自行百度，在windows环境下部署需要将winutils-master对应的hadoop版本的bin复制到解压好的hadoop目录下覆盖即可</p>
	</li>
	<li>
	<p>修改HBase下的conf/hbase-env.cmd</p>

	<ul><li>
		<p>set JAVA_HOME=F:\java\jdk</p>
		</li>
		<li>
		<p>set HBASE_MANAGES_ZK=true</p>
		</li>
	</ul><p><strong>注：具体地址有自己电脑jdk的安装目录决定</strong></p>
	</li>
	<li>
	<p>修改HBase下的conf/hbase-site.xml</p>

	<pre>
  
  &lt;configuration&gt;
        &lt;property&gt;  
          &lt;name&gt;hbase.rootdir&lt;/name&gt;  
          &lt;value&gt;file:///E:/hbase-2.0.0/tmp/hbase/root&lt;/value&gt;  
      &lt;/property&gt;  
      &lt;property&gt;  
          &lt;name&gt;hbase.tmp.dir&lt;/name&gt;  
          &lt;value&gt;E:/hbase-2.0.0/tmp/hbase/tmp&lt;/value&gt;  
      &lt;/property&gt;  
      &lt;property&gt;  
          &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;  
          &lt;value&gt;127.0.0.1&lt;/value&gt;  
      &lt;/property&gt;  
      &lt;property&gt;  
          &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;  
          &lt;value&gt;E:/hbase-2.0.0/tmp/hbase/zoo&lt;/value&gt;  
      &lt;/property&gt;  
      &lt;property&gt;  
          &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;  
          &lt;value&gt;false&lt;/value&gt;  
      &lt;/property&gt;
  &lt;/configuration&gt;</pre>
	</li>
</ol><h3>四、部署测试</h3>

<p>4.1 单机部署测试</p>

<ol><li>
	<p>启动HBase</p>

	<p>cmd cd进入hbase/bin，输入start-hbase.cmd，启动HBase，或者直接双击start-hbase.cmd启动。                                       <img alt="" class="has" height="498" src="https://img-blog.csdn.net/20180724191401756?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTg2Mzc4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="972"></p>
	</li>
	<li>
	<p>测试Shell</p>

	<p>在cmd中进入hbase\bin，输入hbase shell测试  如果显示jline的错误，需要将hive中的jline.jar包导入hadoop的share/hadoop/yarn/lib中     <strong>注：jline是用来处理控制台输入的 </strong>                                                                                               <img alt="" class="has" height="308" src="https://img-blog.csdn.net/20180724190411448?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTg2Mzc4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="922"></p>
	</li>
	<li>
	<p>打开Hbase主页，正常打开即为部署成功，网址为<a href="http://127.0.0.1:16010/master-status" rel="nofollow">http://127.0.0.1:16010/master-status</a></p>
	</li>
</ol><p><img alt="" class="has" height="331" src="https://img-blog.csdn.net/20180724191041853?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM4NTg2Mzc4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1200"></p>

<p>到此，HBase单机安装部署完成。</p>

<p> </p>            </div>
                </div>
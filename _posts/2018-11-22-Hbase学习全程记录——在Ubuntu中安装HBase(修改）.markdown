---
layout:     post
title:      Hbase学习全程记录——在Ubuntu中安装HBase(修改）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>转载：http://blog.fens.me/linux-hbase-install/<br></h2>
<h2>1 在Ubuntu中环境准备</h2>
<p></p>
<p>系统环境：</p>
<ul><li>Linux Ubuntu 14.04.2 LTS 64bit server</li><li>Java 1.7.0_75<br></li><li>Hadoop 1.2.1</li></ul><p></p>
<h2>HBase安装</h2>
<p></p>
<p><strong>2.1 下载HBase</strong></p>
<pre><code>
下载地址：http://mirror.tcpdiag.net/apache/hbase/

# 解压HBase
　tar xvf hbase-0.94.18.tar.gz

# 移动HBase目录到文件夹
　mv hbase-0.94.18/ /home/liujianfei/hadoop/
   cp -rf  hbase-0.94.18/ /home/liujianfei/hadoop/hbase-0.94.18
 # 进入目录
   cd /home/liujianfei/hadoop/hbase-0.94.18

</code></pre><pre><code></code></pre><p><strong>2.2 配置HBase</strong></p><p>2.2.1 修改启动文件hbase-env.sh</p><p>创建目录：
</p><pre><code>mkdir /home/liujianfei/hadoop/hdata

 vim conf/hbase-env.sh

#打开注释
export JAVA_HOME=/home/conan/toolkit/jdk16
export HBASE_CLASSPATH=/home/conan/hadoop/hadoop-1.1.2/conf
export HBASE_MANAGES_ZK=true
</code></pre><p>2.2.2 修改配置文件 hbase-site.xml</p><pre><code>
~ vi conf/hbase-site.xml

&lt;configuration&gt;
&lt;property&gt;
&lt;name&gt;hbase.rootdir&lt;/name&gt;
&lt;value&gt;hdfs://localhost:9000/hbase&lt;/value&gt;
&lt;/property&gt;

&lt;property&gt;
&lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
&lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;

&lt;property&gt;
&lt;name&gt;dfs.replication&lt;/name&gt;
&lt;value&gt;1&lt;/value&gt;
&lt;/property&gt;

&lt;property&gt;
&lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
&lt;value&gt;localhost&lt;/value&gt;
&lt;/property&gt;

&lt;property&gt;
&lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;
&lt;value&gt;2181&lt;/value&gt;
&lt;/property&gt;

&lt;property&gt;
&lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;
&lt;value&gt;/home/liujianfei/hadoop/hdata&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;
</code><code>
</code></pre><p><strong>2.3 启动hadoop和hbase</strong></p><pre><code>
  ./home/liujianfei/hadoop/hadoop-1.2.1/bin/start-all.sh
  ./home/liujianfei/hadoop/hbase-0.94.18/bin/start-hbase.sh

# 查看hbase进程
~ jps</code>
4647 JobTracker
4824 TaskTracker
4556 SecondaryNameNode
7589 HQuorumPeer
4356 DataNode
7660 HMaster
8248 Main
9007 Jps
3859 org.eclipse.equinox.launcher_1.3.0.dist.jar
4076 NameNode
7797 HRegionServer

<strong>2.4 打开HBase命令行客户端访问Hbase</strong>
进入
cd /home/liujianfei/hadoop/hbase-0.94.18/bin
./hbase shell
</pre><p><strong>2.5 HBase简单命令操作</strong></p><pre><code>
#创建一个新表student
hbase(main):003:0&gt; create 'student','info'
0 row(s) in 1.2680 seconds

#查看所有的表
hbase(main):004:0&gt; list
TABLE
student
1 row(s) in 0.0330 seconds

#查看student的表结构
hbase(main):005:0&gt; describe 'student'
DESCRIPTION                                                 ENABLED
 'student', {NAME =&gt; 'info', DATA_BLOCK_ENCODING =&gt; 'NONE', true
  BLOOMFILTER =&gt; 'NONE', REPLICATION_SCOPE =&gt; '0', VERSIONS
  =&gt; '3', COMPRESSION =&gt; 'NONE', MIN_VERSIONS =&gt; '0', TTL =
 &gt; '2147483647', KEEP_DELETED_CELLS =&gt; 'false', BLOCKSIZE =
 &gt; '65536', IN_MEMORY =&gt; 'false', ENCODE_ON_DISK =&gt; 'true',
  BLOCKCACHE =&gt; 'true'}
1 row(s) in 0.1100 seconds

#同student表中插入一条数据
hbase(main):007:0&gt; put 'student','mary','info:age','19'
0 row(s) in 0.0490 seconds

#从student表中取出mary的数据
hbase(main):008:0&gt; get 'student','mary'
COLUMN                   CELL
 info:age                timestamp=1396366643298, value=19
1 row(s) in 0.0190 seconds

#让student表失效
hbase(main):009:0&gt; disable 'student'
0 row(s) in 1.2400 seconds

#列出所有表
hbase(main):010:0&gt; list
TABLE
student
1 row(s) in 0.0310 seconds

#删除student表
hbase(main):013:0&gt;  drop 'student'
0 row(s) in 1.1100 seconds

#列出所有表
hbase(main):014:0&gt; list
TABLE
0 row(s) in 0.0400 seconds
</code></pre> 
            </div>
                </div>
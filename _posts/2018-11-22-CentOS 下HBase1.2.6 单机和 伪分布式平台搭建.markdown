---
layout:     post
title:      CentOS 下HBase1.2.6 单机和 伪分布式平台搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/RD_moon/article/details/79057151				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>前篇文章搭建了 hadoop</p>
<p>传送门  <a href="http://blog.csdn.net/rd_moon/article/details/79056778" rel="nofollow">
centos 上hadoop2.7.3安装部署 单机版<br></a></p>
<p>hbase 1.2.6 安装</p>
<p>一 hbase 安装</p>
<p>1. 下载解压 <br></p>
<p><a href="http://mirrors.hust.edu.cn/apache/hbase/" rel="nofollow">hbase 下载</a></p>
<p>解压到 /usr/hbase/</p>
<p>2. 修改环境</p>
<p>命令 ： </p>
<pre><code class="language-html">vi /etc/profile</code></pre>
添加
<pre><code class="language-html">export HBASE_HOME=/usr/hbase/hbase-1.2.6</code></pre>
<pre>#在path增加
:$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$HBASE_HOME/bin</pre>
<pre><code class="language-html">export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop

export HADOOP_MAPRED_HOME=${HADOOP_HOME}
export HADOOP_COMMON_HOME=${HADOOP_HOME}
export HADOOP_HDFS_HOME=${HADOOP_HOME}
export YARN_HOME=${HADOOP_HOME}
export HADOOP_COMMON_LIB_NATIVE_DIR=${HADOOP_HOME}/lib/natvie
export HADOOP_OPTS="-Djava.library.path=${HADOOP_HOME}/lib:${HADOOP_HOME}/lib/native"</code></pre>
<span>　二、Hbase单机模式</span>
<p></p>
<p></p>
<p>1、修改配置文件/usr/hbase/hbase-1.2.6/conf/hbase-env.sh <br></p>
<p>去掉export前#号 修改<br></p>
<div class="linuxidc_code">
<pre> export JAVA_HOME=/mnt/jdk1.8.0_151
</pre>
</div>
<pre><span style="color:#000000;">export HBASE_MANAGES_ZK</span>=<span>true</span></pre>
<p>　2、修改配置文件/usr/hbase/hbase-1.2.6/conf/hbase-site.xml</p>
<div class="linuxidc_code">
<pre>&lt;configuration&gt;
      &lt;property&gt;
        &lt;name&gt;hbase.rootdir&lt;/name&gt;
        &lt;value&gt;file:/usr/hbase/hbase-tmp&lt;/value&gt;
    &lt;/property&gt;
</pre><pre class="hljs xml"><code class="xml"><span class="hljs-tag">&lt;<span class="hljs-name">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-name">name</span>&gt;</span>hbase.zookeeper.property.dataDir<span class="hljs-tag">&lt;/<span class="hljs-name">name</span>&gt;</span> 
           <span class="hljs-tag">&lt;<span class="hljs-name">value</span>&gt;</span>/usr/hbase/data/zookeeper<span class="hljs-tag">&lt;/<span class="hljs-name">value</span>&gt;</span>
       <span class="hljs-tag">&lt;/<span class="hljs-name">property</span>&gt;</span></code>
   &lt;/configuration&gt;</pre>
</div>
<p>3、启动 Hbase</p>
<div class="linuxidc_code">
<pre>[root@node1 bin]# ./start-hbase.sh<span style="color:#000000;"></span></pre>
</div>
遇到 jar 冲突问题 删除一个即可
<p></p>
<div class="linuxidc_code">
<pre><span style="color:#000000;"></span><span>[root@node1 bin]# ./start-hbase.sh
starting master, logging to /usr/hbase/hbase-1.2.6/logs/hbase-root-master-node1.out
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option PermSize=128m; support was removed in 8.0
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=128m; support was removed in 8.0</span></pre>
</div>
<p></p>
<p>jps下多了一个HMaster进程　　</p>
<div class="linuxidc_code">
<pre><span style="color:#000000;"></span>[root@node1 sbin]# jps
15452 ResourceManager
15289 SecondaryNameNode
14978 NameNode
18111 Jps
15561 NodeManager
15079 DataNode<span style="color:#000000;">
</span><span>3933</span> HMaster</pre>
</div>
<pre><code class="language-html">停止：
[root@node1 bin]# ./start-hbase.sh</code></pre>
<p>单机安装完毕！</p>
<p>三 伪分布式安装</p>
<p><span>伪分布式和单机模式的区别主要是配置文件的不同</span></p>
<p><span>　1、修改配置文件/usr/hbase/hbase-1.2.6/conf/hbase-env.sh</span></p>
<p><span></span></p>
<div class="linuxidc_code">
<pre> export JAVA_HOME=/mnt/jdk1.8.0_151
</pre>
</div>
<pre><span style="color:#000000;">export HBASE_MANAGES_ZK</span>=<span>true</span></pre>
<pre><span style="color:#000000;"># export HBASE_CLASSPATH</span>=<span style="color:#000000;">   修改为
</span>export HBASE_CLASSPATH=/usr/hadoop/hadoop-2.7.3/etc/hadoop</pre>
<span>伪分布式zookeeper使用Hbase自带的即可。</span>
<p></p>
<p></p>
<p>2、修改配置文件 <span>/usr/hbase/hbase-1.2.6/conf</span>/hbase-site.xml</p>
<div class="linuxidc_code">
<pre>&lt;configuration&gt;
      &lt;property&gt;
        &lt;name&gt;hbase.rootdir&lt;/name&gt;
        &lt;value&gt;hdfs://node1:9000/hbase&lt;/value&gt;
    &lt;/property&gt;
</pre><pre class="hljs xml"><code class="xml"><span class="hljs-tag">&lt;<span class="hljs-name">property</span>&gt;</span>
           <span class="hljs-tag">&lt;<span class="hljs-name">name</span>&gt;</span>hbase.zookeeper.property.dataDir<span class="hljs-tag">&lt;/<span class="hljs-name">name</span>&gt;</span> 
           <span class="hljs-tag">&lt;<span class="hljs-name">value</span>&gt;</span>/usr/hbase/data/zookeeper<span class="hljs-tag">&lt;/<span class="hljs-name">value</span>&gt;</span>
 <span class="hljs-tag">&lt;/<span class="hljs-name">property</span>&gt;</span></code>
  &lt;property&gt;
     &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
     &lt;value&gt;true&lt;/value&gt;
 &lt;/property&gt;
  &lt;/configuration&gt;</pre>
</div>
<p>3、启动Hbase<br></p>
<div class="linuxidc_code">
<pre><code class="language-html">[root@node1 bin]# ./start-hbase.sh
localhost: starting zookeeper, logging to /usr/hbase/hbase-1.2.6/bin/../logs/hbase-root-zookeeper-node1.out
starting master, logging to /usr/hbase/hbase-1.2.6/logs/hbase-root-master-node1.out
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option PermSize=128m; support was removed in 8.0
Java HotSpot(TM) 64-Bit Server VM warning: ignoring option MaxPermSize=128m; support was removed in 8.0
starting regionserver, logging to /usr/hbase/hbase-1.2.6/logs/hbase-root-1-regionserver-node1.out
</code></pre>
</div>
<p>jps查看进程多了 HMaster和 HRegionServer   HQuorumPeer</p>
<div class="linuxidc_code">
<pre>[root@node1 sbin]# jps
15452 ResourceManager
18417 HMaster
15289 SecondaryNameNode
18564 HRegionServer
18347 HQuorumPeer
19075 Jps
14978 NameNode
15561 NodeManager
15079 DataNode
</pre>
</div>
hbase shell 测试 <br><p></p>
<pre><code class="language-html">[root@node1 bin]# ./hbase shell
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 1.2.6, rUnknown, Mon May 29 02:25:32 CDT 2017

hbase(main):001:0&gt; </code></pre>
<pre><code class="language-html">hbase(main):001:0&gt; status
1 active master, 0 backup masters, 1 servers, 0 dead, 3.0000 average load</code></pre>
停止 hbase <br><p></p>
<pre><code class="language-html">[root@node1 bin]# ./stop-hbase.sh
stopping hbase..................
localhost: stopping zookeeper.</code></pre>
<br><p>停止顺序 先停止hbase然后停止hadoop ，与之启动相反！</p>
<p><br></p>
<p><br></p>
            </div>
                </div>
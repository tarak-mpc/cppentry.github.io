---
layout:     post
title:      hbase 分布式 安装配置 整合到hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>系统 linux centos6.5</strong></p>
<p><strong><br></strong></p>
<p><strong>hbase安装建立在hadoop安装完成的基础上，包括服务器的配置，参考上一篇 hadoop集群配置</strong></p>
<p><strong><br></strong></p>
<p><strong>hbase下载地址http://www.apache.org/dyn/closer.cgi/hbase/</strong></p>
<p><strong><br></strong></p>
<p><strong>这里hadoop版本用的是hadoop-2.6.0，hbase版本hbase-0.98.15。</strong></p>
<p><br></p>
<p>1.机器配置：hosts 和 系统时间</p>
<p>hosts:  我这里用master.hadoop，slave1.hadoop，slave2.hadoop 三台做集群</p>
<p></p><pre><code class="language-plain">[root@master hbase]# vi /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

172.16.1.90  master.hadoop
172.16.1.91  slave1.hadoop
172.16.1.92  slave2.hadoop
172.16.1.93  slave3.hadoop
172.16.1.94  slave4.hadoop</code></pre><br>
 
<p>系统时间：开机同步时间</p>
<p></p><pre><code class="language-plain">[root@master hbase]# vi /etc/rc.local 
#...
ntpdate time.nist.gov</code></pre><br><br><p><br></p>
<p>2.把下载好的hbase-0.98.15-hadoop2-bin.tar.gz，解压到本地统一配置后再上传到集群服务器上，配置文件在~/hbase-0.98.15/conf中</p>
<p>hbase-env.sh 中加入<br></p>
<p></p><pre><code class="language-plain">&lt;pre name="code" class="plain"&gt;export JAVA_HOME=/usr/local/jdk1.7.0_79
#用hbase自带的zookeeper
export HBASE_MANAGES_ZK=true</code></pre>
<pre></pre>
<p></p>
<p><br></p>
<p>hbase-site.xml<br></p>
<p></p><pre><code class="language-html">&lt;configuration&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.rootdir&lt;/name&gt;
		&lt;value&gt;hdfs://master.hadoop:8020/hbase&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.tmp.dir&lt;/name&gt;
		&lt;value&gt;file:/root/hbase/tmp&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.master&lt;/name&gt;
		&lt;value&gt;hdfs://master.hadoop:60000&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;
		&lt;value&gt;file:/root/hbase/zookeeper&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.cluster.distributed &lt;/name&gt;
		&lt;value&gt;true&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
		&lt;value&gt;master.hadoop,slave1.hadoop,slave2.hadoop&lt;/value&gt;
	&lt;/property&gt;
	&lt;property&gt;
		&lt;name&gt;hbase.zookeeper.property.clientPort&lt;/name&gt;
		&lt;value&gt;2181&lt;/value&gt;
	&lt;/property&gt;
&lt;/configuration&gt;</code></pre><br><br><p>regionservers<br></p>
<p></p><pre><code class="language-plain">slave1.hadoop
slave2.hadoop</code></pre><br><br><p>3.为了版本兼容，替换jar包，把~/hbase-0.98.15/lib下的 hadoop开头的jar包都删掉。</p>
<p>   然后把~/hadoop-2.6.0/share/hadoop  目录下的 hadoop开头的jar包 复制到~/hbase-0.98.15/lib目录下<br></p>
<p><img src="https://img-blog.csdn.net/20151104105527498?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p>4.压缩配置好的hbase-0.98.15，得到hbase-0.98.15.zip，依次上传到集群服务器的 /usr/local 目录中</p>
<p><br></p>
<p>5.在集群服务器上依次新建文件夹  /root/hbase/tem，/root/hbase/zookeeper</p>
<p><br></p>
<p>6.依次解压hbase-0.98.15.zip到 /usr/local/hbase-0.98.15目录中，环境变量新加/usr/local/hbase-0.98.15/bin</p>
<p><br></p>
<p>7.在hadoop启动的后，启动hbase</p>
<p>sh /usr/local/hbase-0.98.15/bin/start-hbase.sh</p>
<p><br></p>
<p>8.验证 jps：</p>
<p>master，比之前多了 HMaster，HQuorumPeer</p>
<p></p><pre><code class="language-plain">[root@master bin]# jps
13459 HQuorumPeer
2930 NameNode
13535 HMaster
3365 ResourceManager
14486 Jps
3214 SecondaryNameNode
[root@master bin]# </code></pre><br>
slave1，比之前多了HQuorumPeer，HRegionServer
<p></p><pre><code class="language-plain">[root@slave1 temp]# jps
3524 HRegionServer
1520 DataNode
1631 NodeManager
4119 Jps
3444 HQuorumPeer
[root@slave1 temp]# </code></pre><br>
slave2，比之前多了HQuorumPeer，HRegionServer
<p></p><pre><code class="language-plain">[root@slave2 ~]# jps
3598 HQuorumPeer
4312 Jps
1615 NodeManager
3683 HRegionServer
1519 DataNode
[root@slave2 ~]# </code></pre><br><br><p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
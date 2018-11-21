---
layout:     post
title:      Hadoop基础教程-第10章 HBase：Hadoop数据库（10.3 HBase安装与配置）（草稿）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载。					https://blog.csdn.net/chengyuqiang/article/details/75928827				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="第10章-hbasehadoop数据库">第10章 HBase：Hadoop数据库</h2>



<h2 id="103-hbase安装与配置">10.3 HBase安装与配置</h2>

<hr>

<p>HBase服务规划</p>

<table>
<thead>
<tr>
  <th>机器名</th>
  <th>HMaster</th>
  <th>HRegionServer</th>
  <th>Zookeeper</th>
</tr>
</thead>
<tbody><tr>
  <td>node1</td>
  <td>Y</td>
  <td>Y</td>
  <td>Y</td>
</tr>
<tr>
  <td>node2</td>
  <td>Y</td>
  <td>Y</td>
  <td>Y</td>
</tr>
<tr>
  <td>node3</td>
  <td>N</td>
  <td>Y</td>
  <td>Y</td>
</tr>
</tbody></table>


<p>注意，如果集群较大，HMaster可以大于2。</p>

<p>首先通过物理机（Windows系统）下载HBase软件包，其次通过XShell上传到node1节点，然后在node1节点上进行HBase相关配置，最后分发到node2和node3节点上。按照这个思路，下面来演示HBase的安装与配置。</p>

<h3 id="1031-hbase下载">10.3.1 HBase下载</h3>

<p>HBase官方网址：<a href="http://hbase.apache.org/" rel="nofollow">http://hbase.apache.org/</a> <br>
<img src="https://img-blog.csdn.net/20170723165203694?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170723165301927?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170723170415078?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>下载推荐的当前稳定版1.2.6</p>

<h3 id="1032-hbase安装">10.3.2 HBase安装</h3>

<p>（1）上传软件包。通过XShell将HBase软件包上传到集群中的一个节点，比如node1 <br>
<img src="https://img-blog.csdn.net/20170723171038720?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
（2）解压缩软件包</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># tar -zxvf hbase-1.2.6-bin.tar.gz -C /opt</span></code></pre>

<p>（3）进入HBase根目录</p>



<pre class="prettyprint"><code class=" hljs avrasm">[root@node1 ~]<span class="hljs-preprocessor"># cd /opt/hbase-1.2.6/</span>
[root@node1 hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>]<span class="hljs-preprocessor"># ls</span>
bin  CHANGES<span class="hljs-preprocessor">.txt</span>  conf  docs  hbase-webapps  LEGAL  lib  LICENSE<span class="hljs-preprocessor">.txt</span>  NOTICE<span class="hljs-preprocessor">.txt</span>  README<span class="hljs-preprocessor">.txt</span>
[root@node1 hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>]<span class="hljs-preprocessor"># </span></code></pre>



<h3 id="1033-hbase集群模式配置">10.3.3 HBase集群模式配置</h3>

<p><strong>1、配置hbase-env.sh</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>]<span class="hljs-comment"># vi conf/hbase-env.sh </span></code></pre>

<p>需要设置4处 <br>
（1）JAVA_HOME</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment"># The java implementation to use.  Java 1.7+ required. </span>
<span class="hljs-keyword">export</span> JAVA_HOME=/opt/jdk1.<span class="hljs-number">8.0</span>_112</code></pre>

<p>（2）针对JDK8，移除下面两项</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># Configure PermSize. Only needed in JDK7. You can safely remove it for JDK8+</span>
<span class="hljs-preprocessor">#export HBASE_MASTER_OPTS="$HBASE_MASTER_OPTS -XX:PermSize=128m -XX:MaxPermSize=128m"</span>
<span class="hljs-preprocessor">#export HBASE_REGIONSERVER_OPTS="$HBASE_REGIONSERVER_OPTS -XX:PermSize=128m -XX:MaxPermSize=128m"</span></code></pre>

<p>（3）设置HBASE_PID_DIR</p>



<pre class="prettyprint"><code class=" hljs oxygene"># The directory <span class="hljs-keyword">where</span> pid files are stored. /tmp <span class="hljs-keyword">by</span> <span class="hljs-keyword">default</span>.
export HBASE_PID_DIR=/<span class="hljs-keyword">var</span>/run</code></pre>

<p>（4） 不使用Hbase自带的zookeeper</p>



<pre class="prettyprint"><code class=" hljs vhdl"># Tell HBase whether it should manage it<span class="hljs-attribute">'s</span> own instance <span class="hljs-keyword">of</span> Zookeeper <span class="hljs-keyword">or</span> <span class="hljs-keyword">not</span>.
export HBASE_MANAGES_ZK=false
</code></pre>

<p><strong>2、配置hbase-site.xml</strong></p>



<pre class="prettyprint"><code class=" hljs xml">[root@node1 hbase-1.2.6]# vi conf/hbase-site.xml 
[root@node1 hbase-1.2.6]# cat conf/hbase-site.xml 
<span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>
<span class="hljs-pi">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
   <span class="hljs-comment">&lt;!--
        这里必须跟core-site.xml中的配置一致
        namenode是HA时，hbase.rootdir=hdfs://nameservices/hbase
        namenode非HA时，hbase.rootdir=hdfs://namenode节点:9000/hbase
   --&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.rootdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://cetc/hbase<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
   <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-comment">&lt;!--指定Hbase是分布式的--&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.cluster.distributed<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
   <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-comment">&lt;!--hbase.master:The port the HBase Master should bind to, Default 16000--&gt;</span>
   <span class="hljs-comment">&lt;!--hbase.master.info.port:The port for the HBase Master web UI. Default 16010--&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.support.append<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
   <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.quorum<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>node1:2181,node2:2181,node3:2181<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
   <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.property.dataDir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
         <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/var/lib/zookeeper<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
   <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
[root@node1 hbase-1.2.6]# </code></pre>

<p>提示，hbase-site.xml的参数hbase.zookeeper.property.dataDirZooKeeper与Zookeeper的zoo.conf中的dataDir对应。</p>

<p><strong>3、配置regionservers</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>]<span class="hljs-comment"># vi conf/regionservers </span>
[root<span class="hljs-variable">@node1</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>]<span class="hljs-comment"># cat conf/regionservers </span>
node1
node2
node3
[root<span class="hljs-variable">@node1</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>]<span class="hljs-comment">#</span></code></pre>

<p><strong>4、设置环境变量</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>]<span class="hljs-comment"># cd</span>
[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># vi /etc/profile.d/custom.sh</span></code></pre>

<p>增加如下内容</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment">#Hbase path</span>
<span class="hljs-keyword">export</span> HBASE_HOME=/opt/hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HBASE_HOME</span>/bin
</code></pre>

<p><strong>5、分发文件</strong> <br>
（1）分发Hbase软件包</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># scp -r /opt/hbase-1.2.6/ node2:/opt</span>
[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># scp -r /opt/hbase-1.2.6/ node3:/opt      </span></code></pre>

<p>（2）分发环境变量文件</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># scp /etc/profile.d/custom.sh node2:/etc/profile.d/</span>
custom.sh                                                                                                                                                  <span class="hljs-number">100</span>%  <span class="hljs-number">410</span>     <span class="hljs-number">0</span>.<span class="hljs-number">4</span>KB/s   <span class="hljs-number">00</span><span class="hljs-symbol">:</span><span class="hljs-number">00</span>    
[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># scp /etc/profile.d/custom.sh node3:/etc/profile.d/</span>
custom.sh </code></pre>

<p>（3）source</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># source /etc/profile.d/custom.sh</span>
[root<span class="hljs-variable">@node2</span> ~]<span class="hljs-comment"># source /etc/profile.d/custom.sh</span>
[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># source /etc/profile.d/custom.sh</span></code></pre>



<h3 id="1033-hbase运行">10.3.3 HBase运行</h3>

<p>（1）不失一般性，选择node1节点启动HBase</p>



<pre class="prettyprint"><code class=" hljs avrasm">[root@node1 ~]<span class="hljs-preprocessor"># start-hbase.sh</span>
starting master, logging to /opt/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>/logs/hbase-root-master-node1<span class="hljs-preprocessor">.out</span>
<span class="hljs-label">node2:</span> starting regionserver, logging to /opt/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>/logs/hbase-root-regionserver-node2<span class="hljs-preprocessor">.out</span>
<span class="hljs-label">node3:</span> starting regionserver, logging to /opt/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>/logs/hbase-root-regionserver-node3<span class="hljs-preprocessor">.out</span>
<span class="hljs-label">node1:</span> starting regionserver, logging to /opt/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>/logs/hbase-root-regionserver-node1<span class="hljs-preprocessor">.out</span></code></pre>

<p>（2）然后选择另一个节点启动备份Hmaster</p>



<pre class="prettyprint"><code class=" hljs cs">[root@node3 ~]<span class="hljs-preprocessor"># hbase-daemon.sh start master</span>
starting master, logging to /opt/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>/logs/hbase-root-master-node3.<span class="hljs-keyword">out</span></code></pre>

<p>（3）jps，查看进程 <br>
node1</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># jps</span>
<span class="hljs-number">2176</span> <span class="hljs-constant">QuorumPeerMain</span>
<span class="hljs-number">11488</span> <span class="hljs-constant">HRegionServer</span>
<span class="hljs-number">3667</span> <span class="hljs-constant">ResourceManager</span>
<span class="hljs-number">11908</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">3542</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">11368</span> <span class="hljs-constant">HMaster</span>
<span class="hljs-number">2764</span> <span class="hljs-constant">JournalNode</span>
<span class="hljs-number">2924</span> <span class="hljs-constant">DFSZKFailoverController</span>
<span class="hljs-number">3164</span> <span class="hljs-constant">NodeManager</span>
<span class="hljs-number">2541</span> <span class="hljs-constant">DataNode</span></code></pre>

<p>node2</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node2</span> ~]<span class="hljs-comment"># jps</span>
<span class="hljs-number">2176</span> <span class="hljs-constant">QuorumPeerMain</span>
<span class="hljs-number">2359</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">2423</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">2649</span> <span class="hljs-constant">DFSZKFailoverController</span>
<span class="hljs-number">5817</span> <span class="hljs-constant">HRegionServer</span>
<span class="hljs-number">6041</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">2525</span> <span class="hljs-constant">JournalNode</span>
<span class="hljs-number">2734</span> <span class="hljs-constant">NodeManager</span></code></pre>

<p>node3</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node3</span> ~]<span class="hljs-comment"># jps</span>
<span class="hljs-number">2177</span> <span class="hljs-constant">QuorumPeerMain</span>
<span class="hljs-number">2561</span> <span class="hljs-constant">NodeManager</span>
<span class="hljs-number">3763</span> <span class="hljs-constant">HMaster</span>
<span class="hljs-number">3911</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">2460</span> <span class="hljs-constant">JournalNode</span>
<span class="hljs-number">3532</span> <span class="hljs-constant">HRegionServer</span>
<span class="hljs-number">2366</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">2703</span> <span class="hljs-constant">ResourceManager</span></code></pre>



<h3 id="1034-web">10.3.4 Web</h3>

<p><img src="https://img-blog.csdn.net/20170723224544908?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170723224558323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="1035-测试hmaster高可用性">10.3.5 测试HMaster高可用性</h3>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># jps|grep HMaster</span>
<span class="hljs-number">11368</span> <span class="hljs-constant">HMaster</span>
[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># kill 11368</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170723224943316?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20170723225004063?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>重新启动node1节点是HMaster</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># hbase-daemon.sh start master</span>
starting master, logging to /opt/hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">6</span>/logs/hbase-root-master-node1.out
[root<span class="hljs-variable">@node1</span> ~]<span class="hljs-comment"># jps</span>
<span class="hljs-number">2176</span> <span class="hljs-constant">QuorumPeerMain</span>
<span class="hljs-number">11488</span> <span class="hljs-constant">HRegionServer</span>
<span class="hljs-number">3667</span> <span class="hljs-constant">ResourceManager</span>
<span class="hljs-number">12579</span> <span class="hljs-constant">HMaster</span>
<span class="hljs-number">3542</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">12647</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">2764</span> <span class="hljs-constant">JournalNode</span>
<span class="hljs-number">3164</span> <span class="hljs-constant">NodeManager</span>
<span class="hljs-number">2541</span> <span class="hljs-constant">DataNode</span>
</code></pre>

<p><img src="https://img-blog.csdn.net/20170723225249257?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
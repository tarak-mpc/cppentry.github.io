---
layout:     post
title:      HBase基于Hadoop2.5完全分布式HA环境搭架
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="服务器-节点分布图">服务器 节点分布图</h2>

<p><img src="https://img-blog.csdn.net/20170713150024801?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="hbase完全分布式模式">HBase完全分布式模式</h2>

<p>hbase 完全分布式搭架之前，保证hadoop集群上做HDFS 确认是运行着的，hadoop的集群搭建参考  <a href="http://blog.csdn.net/liulihui1988/article/details/73189148" rel="nofollow" target="_blank">http://blog.csdn.net/liulihui1988/article/details/73189148</a></p>

<ul>
<li>下载解压最新版本：</li>
</ul>

<p>HBase下载地址 ：<a href="http://archive.apache.org/dist/hbase/" rel="nofollow" target="_blank">http://archive.apache.org/dist/hbase/</a> 点击stable目录选择不同版本，然后下载后缀为 .tar.gz 的文件; 例如 hbase-0.98.9-hadoop2-bin.tar.gz，解压缩，然后解压到指定的目录.</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># tar -zxvf hbase-0.98.9-hadoop2-bin.tar.gz -C /usr/local</span></code></pre>

<ul>
<li>修改文件 conf/hbase-env.sh  </li>
</ul>

<p>如果你在命令行键入java有反应说明你安装了Java。如果没有装，你需要先安装，然后编辑conf/hbase-env.sh，将其中的JAVA_HOME指向到你Java的安装目录。</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># vi conf/hbase-env.sh</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170713150904676?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>修改配置文件 conf/hbase-site.xml</li>
</ul>

<p>要想运行完全分布式模式，你要进行如下配置，先在 hbase-site.xml, 加一个属性 hbase.cluster.distributed 设置为 true 然后把 hbase.rootdir 设置为HDFS的NameNode的位置。 例如，你的namenode运行在namenode.example.org，端口是9000 你期望的目录是 /hbase,使用如下的配置</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.rootdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://bjsxt/hbase<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>The directory shared by RegionServers.
    <span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.cluster.distributed<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>The mode the cluster will be in. Possible values are
      false: standalone and pseudo-distributed setups with managed Zookeeper
      true: fully-distributed with unmanaged Zookeeper Quorum (see hbase-env.sh)
    <span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170713152640910?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>修改配置文件 conf/regionservers</li>
</ul>

<p>完全分布式模式的还需要修改conf/regionservers, 列出了你希望运行的全部 HRegionServer，一行写一个host (就像Hadoop里面的 slaves 一样). 列在这里的server会随着集群的启动而启动，集群的停止而停止.</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># vi conf/regionservers</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170713152402673?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="zookeeper-和-hbase">- ZooKeeper 和 HBase </h2>

<p>一个分布式运行的HBase依赖一个zookeeper集群。所有的节点和客户端都必须能够访问zookeeper。默认的情况下HBase会管理一个zookeep集群。这个集群会随着HBase的启动而启动。当然，你也可以自己管理一个zookeeper集群，但需要配置HBase。你需要修改conf/hbase-env.sh里面的HBASE_MANAGES_ZK 来切换。这个值默认是true的，作用是让HBase启动的时候同时也启动zookeeper 。这里我们自己管理zookeeper</p>

<ul>
<li>修改配置文件  conf/hbase-env.sh</li>
</ul>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># vi conf/hbase-env.sh</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170713154011305?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>当HBase管理zookeeper的时候，你可以通过修改zoo.cfg来配置zookeeper，一个更加简单的方法是在 conf/hbase-site.xml里面修改zookeeper的配置。Zookeep的配置是作为property写在 hbase-site.xml里面的，这里指定zookeeper的集群服务器</p>

<ul>
<li>修改配置文件 conf/hbase-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># conf/hbase-site.xml</span></code></pre>



<pre class="prettyprint"><code class=" hljs xml"> <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.quorum<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>centos-node6,centos-node7,centos-node8<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.property.dataDir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/zookeeper<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170713154854952?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>HDFS客户端配置</li>
</ul>

<p>如果你希望Hadoop集群上做HDFS 客户端配置 ，例如你的HDFS客户端的配置和服务端的不一样。按照如下的方法配置，HBase就能看到你的配置信息</p>

<p>在${HBASE_HOME}/conf下面加一个 hdfs-site.xml (或者 hadoop-site.xml) ，最好是软连接</p>



<pre class="prettyprint"><code class=" hljs lua"># cp /usr/<span class="hljs-keyword">local</span>/hadoop-<span class="hljs-number">2.5</span><span class="hljs-number">.1</span>/etc/hadoop/hdfs-site.xml  /usr/<span class="hljs-keyword">local</span>/hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.9</span>-hadoop2/conf/</code></pre>

<p><img src="https://img-blog.csdn.net/20170713161352944?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="运行和确认你的安装">运行和确认你的安装</h2>

<p>首先确认你的HDFS是运行着的。你可以运行HADOOP_HOME中的 bin/start-hdfs.sh 来启动HDFS.你可以通过put命令来测试放一个文件，然后有get命令来读这个文件。通常情况下HBase是不会运行mapreduce的。所以比不需要检查这些。</p>

<p>如果你自己管理ZooKeeper集群，你需要确认它是运行着的。如果是HBase托管，ZoopKeeper会随HBase启动。</p>



<h2 id="hbase配置同步到指定服务器">- HBase配置同步到指定服务器</h2>

<p>– 以上HBase配置信息，只在服务器centos-node6已完成，需要同步到，centos-node7,centos-node8,centos-node9服务器上，保证HBase配置信息一致。</p>

<p>分别在 centos-node7,centos-node8,centos-node9 解压 </p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># tar -zxvf hbase-0.98.9-hadoop2-bin.tar.gz -C /usr/local</span></code></pre>

<p>拷贝centos-node6上hbase-0.98.9-hadoop2/conf 目录下所有文件到 centos-node7,centos-node8,centos-node9 服务器下 hbase-0.98.9-hadoop2/conf 中，保证hbase配置信息一致。</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@centos</span>-node6 conf]<span class="hljs-comment"># scp ./* root<span class="hljs-yardoctag">@centos</span>-node9:/usr/local/hbase-0.98.9-hadoop2/conf/</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170713164126892?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="启动hbase">启动HBase</h2>

<p>hbase启动之前，首先确认你的HDFS是运行着的，如果你自己管理ZooKeeper集群，你需要确认它是运行着的。</p>

<ul>
<li>HBase 在那台服务器上启动，那台服务器就默认为 HMaster,也可以单独 启动HMaster</li>
</ul>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># ./start-hbase.sh</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170713164818839?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>启动多个Master</li>
</ul>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># ./hbase-daemon.sh start master</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170713165108223?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h2 id="访问hbase-映射的tcp端口60010">访问HBase 映射的tcp端口：60010</h2>

<ul>
<li>centos-node6 服务器</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170713165548349?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>centos-node7 </li>
</ul>

<p><img src="https://img-blog.csdn.net/20170713165732504?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>关闭 centos-node6 上的HMaster后</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170713170027306?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>访问 centos-node7:60010 </li>
</ul>

<p><img src="https://img-blog.csdn.net/20170713170214542?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>可以看出，cnetos-node6上的HMaster断机后，HMasterz自动切换到centos-node7服务器上。Hbase 高可用搭建完成！</p>



<h2 id="hbase-shell">HBase Shell</h2>

<ul>
<li>HBase shell 对hbase操作</li>
</ul>

<p>在任意一台服务器上操作HBase shell，都是可以的,执行命令 hbase shell 回车，可以看到 hbase shell 链接成功</p>



<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># ./hbase shell</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170714112218884?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>查看表 list 命令</li>
</ul>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:001</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">list</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170714112406328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>创建一个表</li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t_class'</span>,<span class="hljs-string">'cF1'</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170714112756444?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>t_class插入数据</li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t_class'</span>,<span class="hljs-string">'ROW1'</span>,<span class="hljs-string">'cF1:NAME'</span>,<span class="hljs-string">'English'</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170714113326781?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>执行  flush ‘t_class’ 将内存中的数据写入到hdfs中</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170714113820264?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<ul>
<li>hbase 表中的数据查看</li>
</ul>

<p>在hbase中bin/目录下执行./hbase可以看到关于hbase的相关命令</p>



<pre class="prettyprint"><code class=" hljs applescript">[root@centos-node8 bin]<span class="hljs-comment"># ./hbase</span>
Usage: hbase [&lt;options&gt;] &lt;command&gt; [&lt;args&gt;]
Options:
  <span class="hljs-comment">--config DIR    Configuration direction to use. Default: ./conf</span>
  <span class="hljs-comment">--hosts HOSTS   Override the list in 'regionservers' file</span>

Commands:
Some commands take arguments. Pass no args <span class="hljs-keyword">or</span> -h <span class="hljs-keyword">for</span> usage.
  shell           Run <span class="hljs-keyword">the</span> HBase shell
  hbck            Run <span class="hljs-keyword">the</span> hbase 'fsck' tool
  hlog            Write-ahead-<span class="hljs-command">log</span> analyzer
  hfile           Store <span class="hljs-type">file</span> analyzer
  zkcli           Run <span class="hljs-keyword">the</span> ZooKeeper shell
  upgrade         Upgrade hbase
  master          Run an HBase HMaster node
  regionserver    Run an HBase HRegionServer node
  zookeeper       Run a Zookeeper server
  <span class="hljs-property">rest</span>            Run an HBase REST server
  thrift          Run <span class="hljs-keyword">the</span> HBase Thrift server
  thrift2         Run <span class="hljs-keyword">the</span> HBase Thrift2 server
  clean           Run <span class="hljs-keyword">the</span> HBase clean up <span class="hljs-keyword">script</span>
  classpath       Dump hbase CLASSPATH
  mapredcp        Dump CLASSPATH entries required <span class="hljs-keyword">by</span> mapreduce
  pe              Run PerformanceEvaluation
  ltt             Run LoadTestTool
  <span class="hljs-property">version</span>         Print <span class="hljs-keyword">the</span> <span class="hljs-property">version</span>
  CLASSNAME       Run <span class="hljs-keyword">the</span> <span class="hljs-type">class</span> named CLASSNAME</code></pre>

<ul>
<li>hbase 命令查看hdfs中表 ‘t_class’文件内容，执行 ./hbase hfile 可以看到相关的命令</li>
</ul>

<pre class="prettyprint"><code class=" hljs haml">[root@centos-node8 bin]# ./hbase hfile
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding in [jar:file:/usr/local/hbase-0.98.9-hadoop2/lib/slf4j-log4j12-1.6.4.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding in [jar:file:/usr/local/hadoop-2.5.1/share/hadoop/common/lib/slf4j-log4j12-1.7.5.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings for an explanation.
2017-07-13 23:58:09,430 INFO  [main] Configuration.deprecation: fs.default.name is deprecated. Instead, use fs.defaultFS
usage: HFile [-a] [-b] [-e] [-f &lt;arg&gt; | -r &lt;arg&gt;] [-h] [-k] [-m] [-p]
       [-s] [-v] [-w &lt;arg&gt;]
 -<span class="ruby">a,--checkfamily         <span class="hljs-constant">Enable</span> family check
</span> -<span class="ruby">b,--printblocks         <span class="hljs-constant">Print</span> block index meta data
</span> -<span class="ruby">e,--printkey            <span class="hljs-constant">Print</span> keys
</span> -<span class="ruby">f,--file &lt;arg&gt;          <span class="hljs-constant">File</span> to scan. <span class="hljs-constant">Pass</span> full-path; e.g.
</span>                          hdfs://a:9000/hbase/hbase:meta/12/34
 -<span class="ruby">h,--printblockheaders   <span class="hljs-constant">Print</span> block headers <span class="hljs-keyword">for</span> each block.
</span> -<span class="ruby">k,--checkrow            <span class="hljs-constant">Enable</span> row order check; looks <span class="hljs-keyword">for</span> out-of-order
</span>                          keys
 -<span class="ruby">m,--printmeta           <span class="hljs-constant">Print</span> meta data of file
</span> -<span class="ruby">p,--printkv             <span class="hljs-constant">Print</span> key/value pairs
</span> -<span class="ruby">r,--region &lt;arg&gt;        <span class="hljs-constant">Region</span> to scan. <span class="hljs-constant">Pass</span> region name; e.g.
</span>                          'hbase:meta,,1'
 -<span class="ruby">s,--stats               <span class="hljs-constant">Print</span> statistics
</span> -<span class="ruby">v,--verbose             <span class="hljs-constant">Verbose</span> output; emits file <span class="hljs-keyword">and</span> meta data
</span>                          delimiters
 -<span class="ruby">w,--seekToRow &lt;arg&gt;     <span class="hljs-constant">Seek</span> to this row <span class="hljs-keyword">and</span> print all the kvs <span class="hljs-keyword">for</span> this
</span>                          row only</code></pre>

<ul>
<li>拷贝hdfs下t_class文件路径 执行一下命令</li>
</ul>

<p><img src="https://img-blog.csdn.net/20170714150956130?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<pre class="prettyprint"><code class=" hljs vala"><span class="hljs-preprocessor"># ./hbase hfile -p -f /hbase/data/default/t_class/7a64e48f6e64cc7deb13b72f7d1aa89e/cF1/0e619ff0dd1a422cb5176f909e2ff977</span></code></pre>

<p><img src="https://img-blog.csdn.net/20170714151249428?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGl1bGlodWkxOTg4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>执行到这里，HBase基于Hadoop2.5完全分布式HA环境搭架 完成了，此文只作为学习期间的笔记</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
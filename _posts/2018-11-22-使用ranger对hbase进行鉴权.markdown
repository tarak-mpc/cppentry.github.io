---
layout:     post
title:      使用ranger对hbase进行鉴权
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="使用ranger对hbase进行鉴权">使用ranger对hbase进行鉴权</h1>

<p>本文使用ranger安全框架对hbase进行权限管理，可以对hbase的table（namespace）、column-family、column设置权限。Hbase版本为1.1.3，Ranger版本为0.5.3.</p>

<ul>
<li><p><strong>Hbase完全分布式安装</strong></p></li>
<li><p><strong>Ranger-hbase-plugin-0.5.3安装</strong></p></li>
<li><p><strong>hbase鉴权测试</strong></p></li>
</ul>

<hr>



<h2 id="hbase完全分布式安装">Hbase完全分布式安装</h2>

<p>本文是将Hbase安装在单节点上。</p>



<h4 id="1zookeeper安装">1、zookeeper安装</h4>

<p>由于hbase依赖于zookeeper，这里使用独立的zookeeper，首先安装zookeeper。解压zookeeper 的tar包，配置zoo.cfg文件，具体配置如下：</p>



<pre class="prettyprint"><code class=" hljs vala">
<span class="hljs-preprocessor"># The number of milliseconds of each tick</span>
tickTime=<span class="hljs-number">2000</span>
<span class="hljs-preprocessor"># The number of ticks that the initial </span>
<span class="hljs-preprocessor"># synchronization phase can take</span>
initLimit=<span class="hljs-number">10</span>
<span class="hljs-preprocessor"># The number of ticks that can pass between </span>
<span class="hljs-preprocessor"># sending a request and getting an acknowledgement</span>
syncLimit=<span class="hljs-number">5</span>
<span class="hljs-preprocessor"># the directory where the snapshot is stored.</span>
<span class="hljs-preprocessor"># do not use /tmp for storage, /tmp here is just </span>
<span class="hljs-preprocessor"># example sakes.</span>
dataDir=/tmp/zookeeper
<span class="hljs-preprocessor"># the port at which the clients will connect</span>
clientPort=<span class="hljs-number">2181</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># Be sure to read the maintenance section of the </span>
<span class="hljs-preprocessor"># administrator guide before turning on autopurge.</span>
<span class="hljs-preprocessor">#</span>
<span class="hljs-preprocessor"># http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance</span>

</code></pre>

<p>启动zookeeper： ./zkServer.sh start  <br>
查看进程执行 jps</p>



<pre class="prettyprint"><code class=" hljs ">2404 QuorumPeerMain</code></pre>



<h4 id="2hbase安装">2、hbase安装</h4>



<h4 id="21-解压hbase-tar包">2.1 解压hbase tar包</h4>



<h4 id="22-修改配置文件hbase-sitexml">2.2 修改配置文件hbase-site.xml</h4>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.rootdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://xhhtest:8020/hbase113<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.cluster.distributed<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.quorum<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>xhhtest:2181<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>



<h4 id="23-启动hbase">2.3 启动hbase</h4>

<p>./start-hbase.sh</p>



<h4 id="24-查看进程-jps">2.4 查看进程 jps</h4>



<h2 id="ranger-hbase-plugin-053安装">Ranger-hbase-plugin-0.5.3安装</h2>



<h4 id="1解压包tar-zxvf-ranger-053-hbase-plugintargz">1、解压包：tar –zxvf ranger-0.5.3-hbase-plugin.tar.gz</h4>



<h4 id="2修改installproperties文件">2、修改install.properties文件</h4>



<pre class="prettyprint"><code class=" hljs avrasm">POLICY_MGR_URL=http://xhhtest:<span class="hljs-number">6080</span>  （Ranger-admin 访问地址）
REPOSITORY_NAME=hbasedev   （服务名称）
XAAUDIT<span class="hljs-preprocessor">.DB</span><span class="hljs-preprocessor">.IS</span>_ENABLED=true
XAAUDIT<span class="hljs-preprocessor">.DB</span><span class="hljs-preprocessor">.FLAVOUR</span>=MYSQL
XAAUDIT<span class="hljs-preprocessor">.DB</span><span class="hljs-preprocessor">.HOSTNAME</span>= xhhtest
XAAUDIT<span class="hljs-preprocessor">.DB</span><span class="hljs-preprocessor">.DATABASE</span>_NAME=ranger_audit
XAAUDIT<span class="hljs-preprocessor">.DB</span><span class="hljs-preprocessor">.USER</span>_NAME=root
XAAUDIT<span class="hljs-preprocessor">.DB</span><span class="hljs-preprocessor">.PASSWORD</span>=<span class="hljs-number">123456</span>
</code></pre>



<h4 id="3添加软连接">3、添加软连接</h4>

<p>假设hbase-1.1.3 安装目录为：/usr/local/ hbase-1.1.3 <br>
ranger-0.5.3-hbase-plugin 安装目录为：/usr/local/ranger-0.5.3-hbase-plugin <br>
则执行：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-built_in">ln</span> -s   /usr/<span class="hljs-built_in">local</span>/hbase-<span class="hljs-number">1.1</span><span class="hljs-number">.3</span>/conf/   /usr/<span class="hljs-built_in">local</span>/hbase
<span class="hljs-built_in">ln</span> -s   /usr/<span class="hljs-built_in">local</span>/hbase-<span class="hljs-number">1.1</span><span class="hljs-number">.3</span>/lib/   /usr/<span class="hljs-built_in">local</span>/hbase</code></pre>



<h4 id="4执行-enable-hbase-pluginsh">4、执行 ./enable-hbase-plugin.sh</h4>

<p>之所以执行软连接，是因为执行此脚本时需要知道hbase-1.1.3的conf 和lib所在位置。</p>



<h4 id="5重启hbase服务">5、重启hbase服务</h4>



<h2 id="hbase鉴权测试">hbase鉴权测试</h2>



<h4 id="1在ranger-admin界面添加hbase-service">1、在ranger-admin界面添加hbase service</h4>

<p><img src="https://img-blog.csdn.net/20170322165140692?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VkYXhoaA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h4 id="2查看插件是否成功">2、查看插件是否成功</h4>

<p><img src="https://img-blog.csdn.net/20170322165321569?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VkYXhoaA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h4 id="3测试">3、测试</h4>

<p>（1）用root用户执行 ./hbase shell 进入hbase，创建表user</p>



<pre class="prettyprint"><code class=" hljs sql"> <span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'user'</span>，<span class="hljs-string">'info'</span></span></code></pre>

<p>（2）用lisi用户执行 ./hbase shell 进入hbase，查看表user</p>



<pre class="prettyprint"><code class=" hljs bash">  scan  <span class="hljs-string">'user'</span></code></pre>

<p>报权限错误如下：</p>



<pre class="prettyprint"><code class=" hljs applescript">hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; scan 'user'
ROW                                           COLUMN+CELL                                                                                                                        

ERROR: org.apache.hadoop.hbase.security.AccessDeniedException: Insufficient permissions <span class="hljs-keyword">for</span> user ‘lisi',action: scannerOpen, tableName:user, family:info.

Here <span class="hljs-keyword">is</span> <span class="hljs-keyword">some</span> help <span class="hljs-keyword">for</span> this command:
Scan a table; pass table <span class="hljs-property">name</span> <span class="hljs-keyword">and</span> optionally a dictionary <span class="hljs-keyword">of</span> scanner
specifications.  Scanner specifications may include one <span class="hljs-keyword">or</span> more <span class="hljs-keyword">of</span>:
TIMERANGE, FILTER, LIMIT, STARTROW, STOPROW, ROWPREFIXFILTER, TIMESTAMP,
MAXLENGTH <span class="hljs-keyword">or</span> COLUMNS, CACHE <span class="hljs-keyword">or</span> RAW, VERSIONS

If no columns are specified, all columns will be scanned.
To scan all members <span class="hljs-keyword">of</span> a column family, leave <span class="hljs-keyword">the</span> qualifier empty <span class="hljs-keyword">as</span> <span class="hljs-keyword">in</span>
'col_family'.

The filter can be specified <span class="hljs-keyword">in</span> two ways:
<span class="hljs-number">1.</span> Using a filterString - more information <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> this <span class="hljs-keyword">is</span> available <span class="hljs-keyword">in</span> <span class="hljs-keyword">the</span>
Filter Language document attached <span class="hljs-keyword">to</span> <span class="hljs-keyword">the</span> HBASE-<span class="hljs-number">4176</span> JIRA
<span class="hljs-number">2.</span> Using <span class="hljs-keyword">the</span> entire package <span class="hljs-property">name</span> <span class="hljs-keyword">of</span> <span class="hljs-keyword">the</span> filter.
</code></pre>

<p>（3）设置policy，增加权限</p>

<p><img src="https://img-blog.csdn.net/20170322165547136?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VkYXhoaA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>再次执行ok.</p>



<h2 id="总结">总结</h2>

<p>本文讲述了ranger鉴权hbase的过程，从安装部署、测试两个方面进行了阐述。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
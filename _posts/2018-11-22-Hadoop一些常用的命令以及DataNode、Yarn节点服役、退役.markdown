---
layout:     post
title:      Hadoop一些常用的命令以及DataNode、Yarn节点服役、退役
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Jorocco/article/details/80623082				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>1、Hadoop常用命令</strong></p>



<pre class="prettyprint"><code class=" hljs mel">hadoop verion       <span class="hljs-comment">//版本</span>
hadoop fs       <span class="hljs-comment">//文件系统客户端.</span>
hadoop jar      <span class="hljs-comment">//</span>
hadoop classpath    <span class="hljs-comment">//查看hadoop类路径</span>
hadoop checknative  <span class="hljs-comment">//检查压缩库本地安装情况</span>

hadoop启动命令
<span class="hljs-variable">$&gt;</span>start-all.sh     <span class="hljs-comment">//start-dfs.sh + start-yarn.sh</span>

<span class="hljs-variable">$&gt;</span>start-dfs.sh 相当于后面两个 <span class="hljs-comment">//hadoop-daemon.sh start namenode</span>
            <span class="hljs-comment">//hadoop-daemons.sh start datanode</span>

<span class="hljs-variable">$&gt;</span>start-yarn.sh        <span class="hljs-comment">//yarn-daemon.sh start resourcemanager</span>
            <span class="hljs-comment">//yarn-daemons.sh start nodemanager</span></code></pre>

<p><strong>2、HDFS常用命令</strong></p>



<pre class="prettyprint"><code class=" hljs mel">hdfs常用命令
--------------------
<span class="hljs-variable">$&gt;</span>hdfs dfs -mkdir /user/centos/hadoop   <span class="hljs-comment">//创建目录</span>
<span class="hljs-variable">$&gt;</span>hdfs dfs -<span class="hljs-keyword">ls</span> -R /user/centos/hadoop  <span class="hljs-comment">//递归查看目录中的文件</span>
<span class="hljs-variable">$&gt;</span>hdfs dfs -lsr /user/centos/hadoop    <span class="hljs-comment">//递归查看目录中的文件</span>
<span class="hljs-variable">$&gt;</span>hdfs dfs -put index.html /user/centos/hadoop <span class="hljs-comment">//上传文件到HDFS中</span>
<span class="hljs-variable">$&gt;</span>hdfs dfs -get /user/centos/hadoop/index.html a.html  <span class="hljs-comment">//下载文件到本地</span>
<span class="hljs-variable">$&gt;</span>hdfs dfs -rm -r -f /user/centos/hadoop   <span class="hljs-comment">//递归删除目录</span>

注意：hdfs dfs  相当于 Hadoop dfs即操作文件系统
——————————————————————————————————————————————————
对hadoop中name中的镜像文件、编辑日志文件的相关操作

使用oiv命令查询hadoop中name中的镜像文件
hdfs oiv -i fsimage -o a.xml -p XML <span class="hljs-comment">//查看镜像文件，将其转化为xml格式查看</span>

hdfs dfsadmin管理命令
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin            <span class="hljs-comment">//查看帮助</span>
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin    -<span class="hljs-keyword">help</span> rollEdits <span class="hljs-comment">//查看指定命令的帮助</span>
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -rollEdits <span class="hljs-comment">//滚动编辑日志</span>
启动hdfs时，镜像文件编辑日志会进行融合，编辑日志滚动

查看hdfs是否在安全模式，安全模式不能对日志文件等进行删除和修改
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -safemode  enter       <span class="hljs-comment">//进入</span>
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -safemode  get         <span class="hljs-comment">//查看</span>
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -safemode  leave       <span class="hljs-comment">//退出</span>
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -safemode  wait            <span class="hljs-comment">//等待</span>

保存名字空间（一般用来创建检查点）,需要进入安全模式，启动完成自动进入安全模式
<span class="hljs-variable">$&gt;</span>hdfs dfsamdin -saveNamespace
——————————————————————————————————————
配额管理(quota)
[目录配额]使得目录下的存储文件总数得到一定的控制
计算目录下的所有文件的总个数。如果<span class="hljs-number">1</span>，表示空目录。
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -setQuota <span class="hljs-number">1</span> dir1 dir2      <span class="hljs-comment">//设置目录配额</span>
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -clrQuota <span class="hljs-number">1</span> dir1 dir2      <span class="hljs-comment">//清除配额管理</span>

[空间配额]
计算目录下的所有文件的总大小.包括副本数.
空间配置至少消耗<span class="hljs-number">3</span>倍的空间大小(目录本身会占用<span class="hljs-number">3</span>倍的空间)。
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -setSpaceQuota <span class="hljs-number">3</span> data
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -clrSpaceQuota dir1        <span class="hljs-comment">//清除配额管理</span>

————————————————————————————————————————
快照管理
<span class="hljs-number">1.</span>描述
迅速对文件(夹)进行备份。不产生新文件，使用差值存储。
默认是禁用快照,先启用。

<span class="hljs-number">2.</span>命令
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -allowSnapShot dir1    <span class="hljs-comment">//在dir1启用快照</span>
<span class="hljs-variable">$&gt;</span>hdfs dfsadmin -disallowSnapShot dir1 <span class="hljs-comment">//在dir1禁用快照</span>
<span class="hljs-variable">$&gt;</span>hdfs dfs -createSnapshot dir ss1 <span class="hljs-comment">//创建快照</span>
<span class="hljs-variable">$&gt;</span>hdfs dfs -renameSnapshot dir ss1 ss2 <span class="hljs-comment">//重命名</span>
<span class="hljs-variable">$&gt;</span>hdfs dfs -deleteSnapshot dir ss1 <span class="hljs-comment">//删除快照</span></code></pre>

<p><strong>3、HDFS默认为64M,一般设置为128M</strong> <br>
之所以设置为128M是为了提高效率，让寻址时间占用读取时间的1%，寻址时间大概为10ms左右，而磁盘的读取时间一般为100M/s,所以为了满足寻址时间占用读取时间的1%，一般设置为128M。当文件切割得太小会使得寻址大于读取时间的1%,不利于效率的提升。</p>

<p>HDFS不适合存储大量的小文件，因为小文件会使得namenode增大，namenode只负责datanode的归档管理，存储的是datanode块中文件的索引，每个文件大概索引字节为152个，不管大小，都一样，所以当文件过小，存储的数据少但是耗费的资源却一样，存储的性价比高。并且namenode是运行在内存中的，内存资源相对宝贵，理应让它发挥最大的效益。</p>

<p><strong>配置hadoop的最小blocksize，必须是512的倍数。</strong></p>

<pre class="prettyprint"><code class=" hljs avrasm">[hdfs-site<span class="hljs-preprocessor">.xml</span>]
dfs<span class="hljs-preprocessor">.namenode</span><span class="hljs-preprocessor">.fs</span>-limits<span class="hljs-preprocessor">.min</span>-block-size=<span class="hljs-number">1024</span>
write,进行校验，<span class="hljs-number">512</span>进行一次校验。

默认最小块是<span class="hljs-number">1</span>M，当我们通过API进行设置时也要大于<span class="hljs-number">1</span>M,除非手动修改hadoop的默认配置文件，即hdfs-default<span class="hljs-preprocessor">.xml</span>，一般不建议这么做。</code></pre>

<p><strong>单独配置辅助名称节点</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">[hdfs-site<span class="hljs-preprocessor">.xml</span>]
dfs<span class="hljs-preprocessor">.namenode</span><span class="hljs-preprocessor">.secondary</span><span class="hljs-preprocessor">.http</span>-address=s206:<span class="hljs-number">50090</span></code></pre>

<p><strong>4、DataNode、Yarn节点服役、退役</strong></p>

<p>DataNode的服役：当某个DataNode节点挂掉之后，需要有个新节点自动补上，以达到设置的节点副本数（一般设置为3个副本）。通过黑白名单以及Slaves文件来对它进行控制，Slaves文件只是在集群启动的时候，集群会通过读取Slaves文件来启动相应的DataNode，而并非直接由Slaves控制。</p>

<p>黑白名单的组合情况，不需要分发到其他节点上，只要存储在namenode节点上即可。</p>



<pre class="prettyprint"><code class=" hljs php"><span class="hljs-keyword">include</span>     <span class="hljs-comment">//dfs.hosts    白名单</span>
exclude     <span class="hljs-comment">//dfs.hosts.exclude 黑名单</span>

<span class="hljs-keyword">include</span>     exclude     Interpretation
No          No          不能连接
No          Yes         不能连接
Yes         No          可以连接
Yes         Yes         可以连接,将会退役状态。</code></pre>

<p><strong>节点的服役和退役(hdfs)</strong></p>

<p><strong>[服役]</strong></p>

<p>[添加新节点] <br>
1.在dfs.include文件中包含新节点名称,该文件在nn的本地目录。</p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-attr_selector">[白名单]</span>
<span class="hljs-attr_selector">[s201:/soft/hadoop/etc/dfs.include.txt]</span>
<span class="hljs-tag">s202</span>
<span class="hljs-tag">s203</span>
<span class="hljs-tag">s204</span>
<span class="hljs-tag">s205</span></code></pre>

<p>2.在hdfs-site.xml文件中添加属性.（这个是早就配置好了的）</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.hosts<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/soft/hadoop/etc/dfs.include.txt<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>3.在nn上刷新节点（当要添加新节点的时候，而又不能停止集群运行，只能通过黑白名单的方式来维护3个副本的存在）</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$&gt;</span>hdfs dfsadmin -refreshNodes</code></pre>

<p>4.在slaves文件中添加新节点ip(主机名)(以便后面重新启动集群的时候自动寻找新服役的节点)</p>



<pre class="prettyprint"><code class=" hljs cs">s202
s203
s204
s205        <span class="hljs-comment">//新添加的</span></code></pre>

<p>5.单独在新节点的机器上启动新的节点中的datanode</p>



<pre class="prettyprint"><code class=" hljs ruby">[s205]
<span class="hljs-variable">$&gt;</span>hadoop-daemon.sh start datanode</code></pre>

<p><strong>[退役]</strong> <br>
1.添加退役节点的ip到黑名单,不要更新白名单.</p>



<pre class="prettyprint"><code class=" hljs avrasm">[/soft/hadoop/etc/dfs<span class="hljs-preprocessor">.hosts</span><span class="hljs-preprocessor">.exclude</span><span class="hljs-preprocessor">.txt</span>]
s205</code></pre>

<p>2.配置hdfs-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.hosts.exclude<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/soft/hadoop/etc/dfs.hosts.exclude.txt<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>3.刷新nn节点</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$&gt;</span>hdfs dfsadmin -refreshNodes</code></pre>

<p>4.查看<code>webui</code>,节点状态在<code>decommisstion in progress.</code> <br>
5.当所有的要退役的节点都报告为<code>Decommissioned</code>,数据转移工作已经完成。 <br>
6.从白名单删除节点,并刷新节点</p>



<pre class="prettyprint"><code class=" hljs r">[s201:/soft/hadoop/etc/dfs.include.txt]
        <span class="hljs-keyword">...</span>

$&gt;hdfs dfsadmin -refreshNodes</code></pre>

<p>7.从slaves文件中删除退役节点</p>

<p><strong>节点的服役和退役(yarn)</strong> <br>
[添加新节点] <br>
1.在dfs.include文件中包含新节点名称,该文件在nn的本地目录。   </p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-attr_selector">[白名单]</span>
<span class="hljs-attr_selector">[s201:/soft/hadoop/etc/yarn.include.txt]</span>
<span class="hljs-tag">s202</span>
<span class="hljs-tag">s203</span>
<span class="hljs-tag">s204</span>
<span class="hljs-tag">s205</span></code></pre>

<p>2.在yarn-site.xml文件中添加属性.</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.nodes.include-path<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/soft/hadoop/etc/dfs.include.txt<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>3.在nn上刷新节点</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$&gt;</span>yarn rmadmin -refreshNodes</code></pre>

<p>4.在slaves文件中添加新节点ip(主机名)</p>



<pre class="prettyprint"><code class=" hljs cs">s202
s203
s204
s205        <span class="hljs-comment">//新添加的</span></code></pre>

<p>5.单独启动新的节点中的<code>nodemananger</code></p>



<pre class="prettyprint"><code class=" hljs ruby">[s205]
<span class="hljs-variable">$&gt;</span>yarn-daemon.sh start nodemananger</code></pre>

<p>[退役] <br>
1.添加退役节点的ip到黑名单,不要更新白名单.</p>



<pre class="prettyprint"><code class=" hljs ruby">[<span class="hljs-regexp">/soft/hadoop</span><span class="hljs-regexp">/etc/yarn</span>.exclude.txt]
s205</code></pre>

<p>2.配置yarn-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.nodes.exclude-path<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/soft/hadoop/etc/dfs.hosts.exclude.txt<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>3.刷新rm节点</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$&gt;</span>yarn rmadmin -refreshNodes</code></pre>

<p>4.查看<code>webui</code>,节点状态在<code>decommisstion in progress.</code></p>

<p>5.当所有的要退役的节点都报告为<code>Decommissioned</code>,数据转移工作已经完成。</p>

<p>6.从白名单删除节点,并刷新节点</p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-variable">$&gt;</span>yarn rmadmin -refreshNodes</code></pre>

<p>7.从slaves文件中删除退役节点</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
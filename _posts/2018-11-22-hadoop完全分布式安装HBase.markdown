---
layout:     post
title:      hadoop完全分布式安装HBase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_33624294/article/details/53000275				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p><strong>安装环境：</strong> <br>
  OS: Centos 6.6 <br>
  JDK: jdk1.7.0_71 <br>
  Hadoop: hadoop-2.5.2 <br>
  Hbase: hbase-0.98.7</p>
</blockquote>

<p>下载HBase安装包,hbase-0.98.7-hadoop2-bin.tar,HBase后面的数字代表HBase的版本号，紧跟在后的hadoop[x]表示支持的Hadoop版本，我的Hadoop版本为2.X所以选择hbase-0.98.7-hadoop2-bin.tar这一版本下载。步骤如下：</p>

<blockquote>
  <p><strong>将hbase-0.98.7-hadoop2-bin.tar上传到集群,我是上传到/home/gznc/下面，并解压，解压命令：</strong></p>
</blockquote>

<pre class="prettyprint"><code class=" hljs lasso">tar  <span class="hljs-attribute">-zxvf</span>  hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span><span class="hljs-attribute">-bin</span><span class="hljs-built_in">.</span>tar</code></pre>

<p><img src="https://img-blog.csdn.net/20161101181331890" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20161101181511627" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>进入HBase配置文件目录进行配置,命令：</strong></p>
</blockquote>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> /home/gznc/hbase-<span class="hljs-number">0.98</span>.<span class="hljs-number">7</span>-hadoop2/conf/
ls</code></pre>

<p><img src="https://img-blog.csdn.net/20161101185245610" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>配置主节点(master)上的hbase-site.xml，命令:</strong></p>
</blockquote>

<pre class="prettyprint"><code class=" hljs lasso">vim hbase<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span></code></pre>

<p><img src="https://img-blog.csdn.net/20161101185648602" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>修改为如下内容</strong></p>
</blockquote>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.rootdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://master:9000/hbase<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>                                  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.property.dataDir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/home/gznc/zookeeper<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.cluster.distributed<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.quorum<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master,slave01,slave02,slave03<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><img src="https://img-blog.csdn.net/20161101190909757" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>在主节点上(master)配置从节点(slave)结点列表，命令：</strong></p>
</blockquote>



<pre class="prettyprint"><code class=" hljs ">vim regionservers</code></pre>

<p><img src="https://img-blog.csdn.net/20161101191635318" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>删掉默认的localhost,并添加想要运行RegionServer的节点名，一个节点一行</strong></p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20161101191838555" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>设置从节点(slave01)为运行HMaster的备用节点，命令：</strong></p>
</blockquote>



<pre class="prettyprint"><code class=" hljs lasso">vim backup<span class="hljs-attribute">-masters</span></code></pre>

<p><img src="https://img-blog.csdn.net/20161101192717426" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20161101193637611" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>注意：backup-masters文件并不存在，使用上述命令设置保存后会生成该文件。这里直接添加一行填上相应的主机名即可，我填的是从节点slave01</strong></p>
</blockquote>

<p>配置HBase上jdk的路径，命令：</p>



<pre class="prettyprint"><code class=" hljs avrasm">vim hbase-env<span class="hljs-preprocessor">.sh</span></code></pre>

<p><img src="https://img-blog.csdn.net/20161101194344026" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>修改为这样</strong></p>
</blockquote>

<pre class="prettyprint"><code class=" hljs rust">The java implementation to <span class="hljs-keyword">use</span>.  Java <span class="hljs-number">1.6</span> required.
<span class="hljs-keyword">export</span> JAVA_HOME=/usr/java/jdk1.<span class="hljs-number">7.0_71</span></code></pre>

<p><img src="https://img-blog.csdn.net/20161101194400010" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>在主节点上(master)分发HBase到各个从节点(slave)，命令：</strong></p>
</blockquote>

<pre class="prettyprint"><code class=" hljs lasso">cd
scp  <span class="hljs-attribute">-r</span>  /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>  slave01:/home/gznc<span class="hljs-subst">/</span></code></pre>

<p><img src="https://img-blog.csdn.net/20161101194907564" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>配置HBase环境变量，命令：</strong></p>
</blockquote>



<pre class="prettyprint"><code class=" hljs avrasm">vi  /home/gznc/<span class="hljs-preprocessor">.bash</span>_profile</code></pre>

<p><img src="https://img-blog.csdn.net/20161101195448947" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>添加如下两行</strong></p>
</blockquote>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> HBASE_HOME=/home/gznc/hbase-<span class="hljs-number">0.98</span>.<span class="hljs-number">7</span>-hadoop2
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$HBASE_HOME</span>/bin:<span class="hljs-variable">$PATH</span></code></pre>

<p><img src="https://img-blog.csdn.net/20161101195804217" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>主节点(master)分发到各个从节点(slave),命令：</strong></p>
</blockquote>



<pre class="prettyprint"><code class=" hljs javascript">scp -r /home/gznc/.bash_profile  slave01:<span class="hljs-regexp">/home/g</span>znc</code></pre>

<p><img src="https://img-blog.csdn.net/20161101200129827" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>在主节点上(master)启动HBase，命令：</strong></p>
</blockquote>

<pre class="prettyprint"><code class=" hljs lasso">cd /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span>
start<span class="hljs-attribute">-hbase</span><span class="hljs-built_in">.</span>sh </code></pre>

<p><img src="https://img-blog.csdn.net/20161101200431429" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>输出信息如下：</strong></p>
</blockquote>

<pre class="prettyprint"><code class=" hljs lasso">slave03: starting zookeeper, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span><span class="hljs-built_in">..</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-zookeeper</span><span class="hljs-attribute">-slave03</span><span class="hljs-built_in">.</span>out
slave01: starting zookeeper, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span><span class="hljs-built_in">..</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-zookeeper</span><span class="hljs-attribute">-slave01</span><span class="hljs-built_in">.</span>out
slave02: starting zookeeper, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span><span class="hljs-built_in">..</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-zookeeper</span><span class="hljs-attribute">-slave02</span><span class="hljs-built_in">.</span>out
master: starting zookeeper, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span><span class="hljs-built_in">..</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-zookeeper</span><span class="hljs-attribute">-master</span><span class="hljs-built_in">.</span>out
starting master, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-master</span><span class="hljs-attribute">-master</span><span class="hljs-built_in">.</span>out
slave03: starting regionserver, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span><span class="hljs-built_in">..</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-regionserver</span><span class="hljs-attribute">-slave03</span><span class="hljs-built_in">.</span>out
slave01: starting regionserver, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span><span class="hljs-built_in">..</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-regionserver</span><span class="hljs-attribute">-slave01</span><span class="hljs-built_in">.</span>out
slave02: starting regionserver, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span><span class="hljs-built_in">..</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-regionserver</span><span class="hljs-attribute">-slave02</span><span class="hljs-built_in">.</span>out
slave01: starting master, logging <span class="hljs-keyword">to</span> /home/gznc/hbase<span class="hljs-subst">-</span><span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>/bin<span class="hljs-subst">/</span><span class="hljs-built_in">..</span>/logs/hbase<span class="hljs-attribute">-gznc</span><span class="hljs-attribute">-master</span><span class="hljs-attribute">-slave01</span><span class="hljs-built_in">.</span>out</code></pre>

<p><img src="https://img-blog.csdn.net/20161101200835050" alt="这里写图片描述" title=""></p>

<blockquote>
  <p><strong>各个节点上执行jps命令</strong></p>
  
  <p>主节点出现如下红色部分 <br>
  <img src="https://img-blog.csdn.net/20161101201036895" alt="这里写图片描述" title=""></p>
  
  <p>从节点出现如下紫色部分 <br>
  第一个从节点 <br>
  <img src="https://img-blog.csdn.net/20161101201714336" alt="第一个从节点" title=""> <br>
  第二个从节点 <br>
  <img src="https://img-blog.csdn.net/20161101201257599" alt="第二个从节点" title=""> <br>
  第三个从节点 <br>
  <img src="https://img-blog.csdn.net/20161101201955573" alt="第三个从节点" title=""></p>
  
  <p>在Linux上的浏览器地址栏中输入主节点主机名:端口号，我的是master:60010 <br>
  <img src="https://img-blog.csdn.net/20161101203423063" alt="这里写图片描述" title=""> <br>
  这样就表明HBase安装成功了</p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
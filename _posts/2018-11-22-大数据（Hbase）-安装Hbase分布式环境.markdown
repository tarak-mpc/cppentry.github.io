---
layout:     post
title:      大数据（Hbase）-安装Hbase分布式环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhangdong2012/article/details/79955754				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<ul>
<li>下载Hbase安装包 <br>
<a href="http://archive.apache.org/dist/hbase/hbase-1.2.6" rel="nofollow">http://archive.apache.org/dist/hbase/hbase-1.2.6</a></li>
<li>上传到待安装机器</li>
<li>解压  <br>
node01:/home/hadoop/application/hbase/hbase <br>
node02:/home/hadoop/application/hbase/hbase <br>
node03:/home/hadoop/application/hbase/hbase</li>
<li>修改node01/node02/node03环境变量</li>
</ul>

<pre class="prettyprint"><code class=" hljs bash">&gt;su root
&gt;vim /etc/profile
<span class="hljs-keyword">export</span> HBASE_HOME=/home/hadoop/application/hbase/hbase-<span class="hljs-number">0.94</span>.<span class="hljs-number">6</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HBASE_HOME</span>/bin
&gt;<span class="hljs-built_in">source</span> /etc/profile
&gt;su hadoop</code></pre>

<ul>
<li>修改配置文件 hbase-env.sh</li>
</ul>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/home/hadoop/application/jdk/jdk1.<span class="hljs-number">8.0</span>_151
<span class="hljs-keyword">export</span> JAVA_CLASSPATH=.:<span class="hljs-variable">$JAVA_HOME</span>/lib/dt.jar:<span class="hljs-variable">$JAVA_HOME</span>/lib/tools.jar
<span class="hljs-keyword">export</span> HBASE_MANAGES_ZK=<span class="hljs-literal">false</span></code></pre>

<ul>
<li>创建zk数据目录</li>
</ul>

<pre class="prettyprint"><code class=" hljs perl">&gt;<span class="hljs-keyword">mkdir</span> /home/hadoop/application/hbase/zkdata</code></pre>

<ul>
<li><p>集成HDFS <br>
复制<code>hdfs-site.xml</code>和<code>core-site.xml</code> 到hbase_home/conf目录下</p></li>
<li><p>修改配置文件 hbase-size.xml</p></li>
</ul>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.master<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>node01:60000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.rootdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://ns/hbase<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.cluster.distributed<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span> 
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.quorum<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>node01,node02,node03<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hbase.zookeeper.property.dataDir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/home/hadoop/application/hbase/zkdata<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><code>hdfs://ns/hbase</code>中的<code>ns</code>为HA HDFS中的名称空间</p>

<ul>
<li>修改配置文件 regionservers</li>
</ul>

<pre class="prettyprint"><code class=" hljs ">node01
node02</code></pre>

<ul>
<li>启动 hbase</li>
</ul>



<pre class="prettyprint"><code class=" hljs livecodeserver">&gt;hbase <span class="hljs-built_in">shell</span></code></pre>

<ul>
<li>遇到的问题 <br>
1.HDFS双namenode都为standby模块 <br>
格式化hdfs在Zookeeper上的数据:<code>hdfs zkfd -formatZK</code> <br>
2.HMaster启动失败，不能失败hdfs namespace <br>
版本问题，开始用0.94.6版本，不行，换成1.2.6就可以了</li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
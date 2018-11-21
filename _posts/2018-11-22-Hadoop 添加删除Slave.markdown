---
layout:     post
title:      Hadoop 添加删除Slave
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq1010885678/article/details/50519025				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hadoop-添加删除slave">Hadoop 添加删除Slave</h1>

<p>@(Hadoop)</p>

<hr>

<!--&#10;author: &#23567;&#40657;&#10;date: 2016-01-14&#10;title: Hadoop&#28155;&#21152;&#21024;&#38500;Slave&#10;tags: Hadoop&#10;category: Hadoop&#10;status: publish&#10;summary: Hadoop&#28155;&#21152;&#21024;&#38500;Slave&#10;-->

<p>在hdfs-site.xml文件中添加如下配置：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.hosts<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/root/jiangyuande/hadoop/etc/hadoop/datanode-allow.list<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.hosts.exclude<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/root/jiangyuande/hadoop/etc/hadoop/datanode-deny.list<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>分别是允许Slave连接Namenode的列表和拒绝连接的列表。</p>



<h2 id="删除节点"><strong>删除节点</strong></h2>

<p>在对应目录下新建datanode-deny.list文件，内容为需要删除的Slave的主机名，一行一个。</p>

<p>执行命令重新读取配置：</p>



<pre class="prettyprint"><code class=" hljs lasso">hdfs dfsadmin <span class="hljs-attribute">-refreshNodes</span></code></pre>

<p>使用</p>



<pre class="prettyprint"><code class=" hljs vhdl">hdfs dfsadmin -<span class="hljs-keyword">report</span></code></pre>

<p>可以看到该节点会处于Decommission Status : Decommission in progress的状态。</p>

<p>等待数据迁移完成之后，该状态变为Decommission Status : Decommissioned。</p>

<p>在该节点上停止进程：</p>



<pre class="prettyprint"><code class=" hljs vbnet">hadoop-daemon.sh <span class="hljs-keyword">stop</span> datanode</code></pre>

<p>删除slaves文件中的对应主机名即可。</p>

<p><strong>注意，副本数量要小于或者等于正常节点的数量，否则删除失败</strong></p>

<p><strong>【已解决】删除节点时，该节点长期处于Decommission Status : Decommission in progress状态，由于数据量太大，导致复制的时间很久，使用新集群测试时瞬间下线该节点</strong></p>



<h2 id="重新添加删除的节点"><strong>重新添加删除的节点</strong></h2>

<p>在slaves文件添加对应主机名，删除datanode-deny.list对应的主机名</p>

<p>执行命令：</p>



<pre class="prettyprint"><code class=" hljs lasso">hdfs dfsadmin <span class="hljs-attribute">-refreshNodes</span></code></pre>

<p>在该节点上启动进程：</p>



<pre class="prettyprint"><code class=" hljs sql">hadoop-daemon.sh <span class="hljs-operator"><span class="hljs-keyword">start</span> datanode</span></code></pre>



<h2 id="添加新节点"><strong>添加新节点</strong></h2>

<p>新机器配置完之后，slave和datanode-allow.list文件加入该主机名。</p>

<p>该节点上启动进程：</p>



<pre class="prettyprint"><code class=" hljs sql">hadoop-daemon.sh <span class="hljs-operator"><span class="hljs-keyword">start</span> datanode</span></code></pre>



<h2 id="安全模式"><strong>安全模式</strong></h2>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hdfs</span> <span class="hljs-tag">dfsadmin</span> <span class="hljs-tag">-safemode</span> <span class="hljs-attr_selector">[arg]</span></code></pre>

<p>enter - 进入安全模式 <br>
leave - 强制NameNode离开安全模式 <br>
get   - 返回安全模式是否开启的信息 <br>
wait  - 等待，一直到安全模式结束。</p>

<p>作者：<a href="http://www.xiaohei.info" rel="nofollow">@小黑</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
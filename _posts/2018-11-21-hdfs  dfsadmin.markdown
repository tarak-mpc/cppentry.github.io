---
layout:     post
title:      hdfs  dfsadmin
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xuxiuning/article/details/51162617				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="binhdfs-dfsadmin">bin/hdfs  dfsadmin</h2>

<p>管理HDFS</p>



<pre class="prettyprint"><code class=" hljs r">Hagrid01@Hagrid01:[/Hagrid/hadoop-<span class="hljs-number">2.6</span><span class="hljs-number">.2</span>]bin/hdfs dfsadmin
Usage: hdfs dfsadmin
Note: Administrative commands can only be run as the HDFS superuser.
        **[-report [-live] [-dead] [-decommissioning]] 报告集群的状况**
        [-safemode &lt;enter | leave | get | wait&gt;]
        [-saveNamespace]
        [-rollEdits]
        [-restoreFailedStorage true|false|check]
        [-refreshNodes]
        [-setQuota &lt;quota&gt; &lt;dirname&gt;<span class="hljs-keyword">...</span>&lt;dirname&gt;]
        [-clrQuota &lt;dirname&gt;<span class="hljs-keyword">...</span>&lt;dirname&gt;]
        [-setSpaceQuota &lt;quota&gt; &lt;dirname&gt;<span class="hljs-keyword">...</span>&lt;dirname&gt;]
        [-clrSpaceQuota &lt;dirname&gt;<span class="hljs-keyword">...</span>&lt;dirname&gt;]
        [-finalizeUpgrade]
        [-rollingUpgrade [&lt;query|prepare|finalize&gt;]]
        [-refreshServiceAcl]
        [-refreshUserToGroupsMappings]
        [-refreshSuperUserGroupsConfiguration]
        [-refreshCallQueue]
        [-refresh &lt;host:ipc_port&gt; &lt;key&gt; [arg1..argn]
        [-reconfig &lt;datanode|<span class="hljs-keyword">...</span>&gt; &lt;host:ipc_port&gt; &lt;start|status&gt;]
        [-printTopology]
        [-refreshNamenodes datanode_host:ipc_port]
        [-deleteBlockPool datanode_host:ipc_port blockpoolId [force]]
        [-setBalancerBandwidth &lt;bandwidth <span class="hljs-keyword">in</span> bytes per second&gt;]
        [-fetchImage &lt;local directory&gt;]
        [-allowSnapshot &lt;snapshotDir&gt;]
        [-disallowSnapshot &lt;snapshotDir&gt;]
        [-shutdownDatanode &lt;datanode_host:ipc_port&gt; [upgrade]]
        [-getDatanodeInfo &lt;datanode_host:ipc_port&gt;]
        [-metasave filename]
        [-setStoragePolicy path policyName]
        [-getStoragePolicy path]
        [-triggerBlockReport [-incremental] &lt;datanode_host:ipc_port&gt;]
        [-help [cmd]]</code></pre>

<pre><code>**[-report [-live] [-dead] [-decommissioning]] 报告集群的状况**
</code></pre>

<p><em>* Hadoop dfsadmin –refreshNodes*</em> <br>
• 删除节点时使用 <br>
这个文件在Hadoop启动时就要配置好才行！</p>

<pre class="prettyprint"><code class=" hljs xml">    – <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.hosts.exclude<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>exclude_file<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<pre><code>– Exclude_file中加入要去除的节点
– 执行refreshNodes
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
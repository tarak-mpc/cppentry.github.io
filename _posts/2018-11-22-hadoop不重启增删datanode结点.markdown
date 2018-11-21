---
layout:     post
title:      hadoop不重启增删datanode结点
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhang0558/article/details/53954481				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>动态删除节点：</strong></p>

<p>进入hadoop目录的etc/hadoop/目录</p>

<p>1.新建excludes文件，每行写一个要删除的节点的主机名</p>

<p>2.在hdfs-site.xml中增加</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.hosts.exclude<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/data/hadoop/etc/hadoop/excludes<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>//全路径
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>3.在mapred-site.xml中增加</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapred.hosts.exclude<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/data/hadoop/etc/hadoop/excludes<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">final</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">final</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>执行命令：</p>

<p>hadoop dfsadmin -refreshNodes <br>
查看节点状态hadoop dfsadmin -report或者web管理端页面，可以看到该节点是decomissioning状态，等它变化为decomissioned，就可以把这个节点拿掉了。拿掉后，该节点上进程还是都在的，直接kill即可。</p>

<p><strong>动态新增节点：</strong></p>

<p>启动datanode</p>

<p>hadoop-daemon.sh –config /data/hadoop/etc/hadoop/ –script hdfs start nodemanager <br>
然后启动nodemanager</p>

<p>yarn-daemons.sh –config /data/hadoop/etc/hadoop/ start nodemanager</p>

<p>或以下命令启动：</p>

<p>hadoop-daemon.sh start datanode <br>
yarn-daemon.sh start nodemanager</p>

<p>然后，删除前文中的excludes文件中该节点的主机名，执行刷新操作即可。</p>

<p>hadoop dfsadmin -refreshNodes <br>
至此，可以看到已经新增了该节点的datanode。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
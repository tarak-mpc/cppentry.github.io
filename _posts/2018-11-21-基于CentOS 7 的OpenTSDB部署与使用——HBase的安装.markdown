---
layout:     post
title:      基于CentOS 7 的OpenTSDB部署与使用——HBase的安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zx711166/article/details/80762958				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>OpenTSDB 是基于 HBase 存储时间序列数据的一个开源数据库，对于存储监控系统采集的数据来说非常合适，不仅在写入查询上有很高的效率，而且节省存储空间。</p>

<h4 id="安装hbase">安装HBase</h4>

<p>因为 OpenTSDB 的后端存储使用的是 HBase，所以我们需要先安装 HBase。 <br>
参考文档： <a href="https://hbase.apache.org/book.html#quickstart" rel="nofollow">Quick Start - Standalone HBase</a> <br>
这里简单搭建了一个单机的 HBase 环境：</p>

<ol>
<li>安装 JDK 环境，centos 上可以直接通过 yum 安装（本人使用的是JDK1.8）。</li>
<li>下载 HBase，<a href="http://apache.fayea.com/hbase/stable" rel="nofollow">http://apache.fayea.com/hbase/stable</a>，这里我们选择下载 stable 的 1.2.6 版本，文件名为 hbase-1.2.6-bin.tar.gz，解压到/data目录下。</li>
<li>修改 conf/hbase-env.sh ，设置 export JAVA_HOME=/usr/java/jdk1.8.0_45，这个是 /bin/java 所在的目录，通过 which java 查看。 <br>
<img src="https://img-blog.csdn.net/20180621152055243?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
配置export HBASE_MANAGES_ZK=true，这个设置是使用hbase自带的zookeeper还是使用外部的zookeeper，设置true是使用自带的zookeeper。 <br>
<img src="https://img-blog.csdn.net/20180621152626784?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
<li>修改 conf/hbase-site.xml， 设置 hbase 的数据存储目录以及 zookeeper 的数据存储目录，默认会放到 /tmp 目录下，这个目录机器重启后会清空，所以需要更改目录，配置如下： <br>
 <img src="https://img-blog.csdn.net/20180621153040591?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
<li>执行 bin/start-hbase.sh 启动 HBase，之后可以通过 jps 命令来查看 HMaster 进程是否启动成功。 bin/stop-hbase.sh 用于关闭 HBase。 <br>
<img src="https://img-blog.csdn.net/20180621153900225?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
</ol>

<h4 id="hbase的基础操作命令行">HBase的基础操作命令行</h4>

<h5 id="连接到hbase">连接到HBase</h5>

<pre class="prettyprint"><code class="language-linux hljs livecodeserver">./bin/hbase <span class="hljs-built_in">shell</span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/2018062115423694?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h5 id="创建一张表">创建一张表</h5>

<pre class="prettyprint"><code class="language-linux hljs sql"><span class="hljs-operator"><span class="hljs-keyword">create</span>  <span class="hljs-string">'test'</span>, <span class="hljs-string">'cf'</span></span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/20180621154741148?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h5 id="查看表信息">查看表信息</h5>

<pre class="prettyprint"><code class="language-linux hljs php"><span class="hljs-keyword">list</span> <span class="hljs-string">'test'</span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/20180621154925260?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h5 id="插入数据">插入数据</h5>



<pre class="prettyprint"><code class="language-linux hljs livecodeserver"><span class="hljs-built_in">put</span> <span class="hljs-string">'test'</span>, <span class="hljs-string">'row1'</span>, <span class="hljs-string">'cf:a'</span>, <span class="hljs-string">'value1'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'test'</span>, <span class="hljs-string">'row2'</span>, <span class="hljs-string">'cf:b'</span>, <span class="hljs-string">'value2'</span>
<span class="hljs-built_in">put</span> <span class="hljs-string">'test'</span>, <span class="hljs-string">'row3'</span>, <span class="hljs-string">'cf:c'</span>, <span class="hljs-string">'value3'</span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/20180621155130937?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h5 id="查看表中的所有数据">查看表中的所有数据</h5>

<pre class="prettyprint"><code class="language-linux hljs bash">scan <span class="hljs-string">'test'</span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/2018062115533799?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h5 id="查看指定行的数据">查看指定行的数据</h5>

<pre class="prettyprint"><code class="language-linux hljs cs"><span class="hljs-keyword">get</span> <span class="hljs-string">'test'</span>, <span class="hljs-string">'row1'</span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/20180621161611996?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h5 id="禁用指定表删除表或修改表设置前需要先禁用该表">禁用指定表（删除表或修改表设置前需要先禁用该表）</h5>

<pre class="prettyprint"><code class="language-linux hljs mel"><span class="hljs-keyword">disable</span> <span class="hljs-string">'test'</span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/20180621164614962?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h5 id="恢复指定表">恢复指定表</h5>

<pre class="prettyprint"><code class="language-linux hljs bash">enable <span class="hljs-string">'test'</span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/20180621164828897?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<h5 id="删除表">删除表</h5>

<pre class="prettyprint"><code class="language-linux hljs rust"><span class="hljs-keyword">drop</span> <span class="hljs-string">'test'</span></code></pre>

<p>结果下图： <br>
<img src="https://img-blog.csdn.net/20180621165148751?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3p4NzExMTY2/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
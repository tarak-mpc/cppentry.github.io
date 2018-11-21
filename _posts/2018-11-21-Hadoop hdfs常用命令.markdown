---
layout:     post
title:      Hadoop hdfs常用命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/yan88888888888888888/article/details/83240738				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>以下是2.6版本以后的命令</p>

<h2>1. 文件操作</h2>

<p>(1) 列出HDFS下的文件</p>

<blockquote>
<pre>
<code class="language-html hljs"> hdfs dfs -ls
</code></pre>
</blockquote>

<p>(2) 列出HDFS文件下名为in的文档中的文件</p>

<blockquote>
<pre>
<code class="language-html hljs">hdfs dfs -ls in</code></pre>
</blockquote>

<p>(3) 上传文件</p>

<p>将当前目录下的test1文件上传到HDFS上并重命名为test：</p>

<blockquote>
<pre>
<code class="language-html hljs">hdfs dfs -put test1 test</code></pre>
</blockquote>

<p>(4) 文件被复制到本地系统中</p>

<p>将HDFS中的in文件复制到本地系统并命名为getin：</p>

<blockquote>
<pre>
<code class="language-html hljs">hdfs dfs -get in getin</code></pre>
</blockquote>

<p>(5) 删除文档</p>

<p>删除HDFS下名为out的文档：</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/hadoop dfs -rmr out</code></pre>

<p>(6) 查看文件</p>

<p>查看HDFS下in文件中的内容：</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/hadoop dfs -cat in/*</code></pre>

<p>(7) 建立目录</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/hadoop dfs -mkdir /user/hadoop/examples（目录/目录名）</code></pre>

<p>只能一级一级的建目录。</p>

<p>(8) 复制文件</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/hadoop dfs -copyFromLocal 源路径 路径</code></pre>

<p>(9)通过Hadoop命令把两个文件的内容合并起来</p>

<pre>
<code class="language-html hljs">hdfs dfs -getmerge 位于hdfs中的原文件（里面有多个文件） 合并后的文件名
例如：
hdfs dfs -getmerge hdfs://Master:9000/data/SogouResult.txt CombinedResult</code></pre>

<p> 注：合并后的文件位于当前目录，不在hdfs中，是本地文件</p>

<h2>2. 管理与更新</h2>

<p>(1) 执行基本信息</p>

<p>查看HDFS的基本统计信息：</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/hadoop dfsadmin -report</code></pre>

<p>(2) 退出安全模式</p>

<p>NameNode在启动时会自动进入安全模式。安全模式是NameNode的一种状态，在这个阶段，文件系统不允许有任何修改。</p>

<p>系统显示Name node in safe mode，说明系统正处于安全模式，这时只需要等待十几秒即可，也可通过下面的命令退出安全模式：</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/hadoop dfsadmin -safemode leave</code></pre>

<p>(3) 进入安全模式</p>

<p>在必要情况下，可以通过以下命令把HDFS置于安全模式：</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/hadoop dfsadmin -safemode enter</code></pre>

<p>(4)节点添加</p>

<p>添加一个新的DataNode节点，先在新加节点上安装好Hadoop，要和NameNode使用相同的配置（可以直接从NameNode复制），修改$HADOOP_HOME/conf/master文件，加入NameNode主机名。然后在NameNode节点上修改$HADOOP_HOME/conf/slaves文件，加入新节点名，再建立新加节点无密码的SSH连接，运行启动命令为：</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/start-all.sh</code></pre>

<p>(5)负载均衡</p>

<p>HDFS的数据在各个DataNode中的分布可能很不均匀，尤其是在DataNode节点出现故障或新增DataNode节点时。新增数据块时NameNode对DataNode节点的选择策略也有可能导致数据块分布不均匀。用户可以使用命令重新平衡DataNode上的数据块的分布：</p>

<pre>
<code class="language-html hljs">/usr/local/hadoop$bin/start-balancer.sh</code></pre>

<p> </p>            </div>
                </div>
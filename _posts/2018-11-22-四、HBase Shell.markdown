---
layout:     post
title:      四、HBase Shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>HBase Shell</h2>

<p>HBase包含可以与HBase进行通信的Shell。 HBase使用Hadoop文件系统来存储数据。它拥有一个主服务器和区域服务器。数据存储将在区域(表)的形式。这些区域被分割并存储在区域服务器。</p>

<p>主服务器管理这些区域服务器，所有这些任务发生在HDFS。下面给出的是一些由HBase Shell支持的命令。</p>

<h2>通用命令</h2>

<ul><li>
	<p><strong>status: </strong>提供HBase的状态，例如，服务器的数量。</p>
	</li>
	<li>
	<p><strong>version: </strong>提供正在使用HBase版本。</p>
	</li>
	<li>
	<p><strong>table_help: </strong>表引用命令提供帮助。</p>
	</li>
	<li>
	<p><strong>whoami: </strong>提供有关用户的信息。</p>
	</li>
</ul><h2>数据定义语言</h2>

<p>这些是关于HBase在表中操作的命令。</p>

<ul><li><strong>create: </strong>创建一个表。</li>
	<li><strong>list: </strong>列出HBase的所有表。</li>
	<li><strong>disable: </strong>禁用表。</li>
	<li><strong>is_disabled: </strong>验证表是否被禁用。</li>
	<li><strong>enable: </strong>启用一个表。</li>
	<li><strong>is_enabled: </strong>验证表是否已启用。</li>
	<li><strong>describe: </strong>提供了一个表的描述。</li>
	<li><strong>alter: </strong>改变一个表。</li>
	<li><strong>exists: </strong>验证表是否存在。</li>
	<li><strong>drop: </strong>从HBase中删除表。</li>
	<li><strong>drop_all: </strong>丢弃在命令中给出匹配“regex”的表。</li>
	<li><strong>Java Admin API: </strong>在此之前所有的上述命令，Java提供了一个通过API编程来管理实现DDL功能。在这个org.apache.hadoop.hbase.client包中有HBaseAdmin和HTableDescriptor 这两个重要的类提供DDL功能。</li>
</ul><h2>数据操纵语言</h2>

<ul><li><strong>put: </strong>把指定列在指定的行中单元格的值在一个特定的表。</li>
	<li><strong>get: </strong>取行或单元格的内容。</li>
	<li><strong>delete: </strong>删除表中的单元格值。</li>
	<li><strong>deleteall:</strong> 删除给定行的所有单元格。</li>
	<li><strong>scan:</strong> 扫描并返回表数据。</li>
	<li><strong>count: </strong>计数并返回表中的行的数目。</li>
	<li><strong>truncate: </strong>禁用，删除和重新创建一个指定的表。</li>
	<li><strong>Java client API: </strong>在此之前所有上述命令，Java提供了一个客户端API来实现DML功能，CRUD（创建检索更新删除）操作更多的是通过编程，在org.apache.hadoop.hbase.client包下。 在此包HTable 的 Put和Get是重要的类。</li>
</ul><h2>启动 HBase Shell</h2>

<p>要访问HBase shell，必须导航进入到HBase的主文件夹。</p>

<pre>
<code class="language-html hljs">cd /usr/localhost/
cd Hbase</code></pre>

<p>可以使用“hbase shell”命令来启动HBase的交互shell，如下图所示。</p>

<pre>
<code class="language-html hljs">./bin/hbase shell</code></pre>

<p>如果已成功在系统中安装HBase，那么它会给出 HBase shell 提示符，如下图所示。</p>

<pre>
<code class="language-html hljs">HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.
Type "exit&lt;RETURN&gt;" to leave the HBase Shell
Version 0.94.23, rf42302b28aceaab773b15f234aa8718fff7eea3c, Wed Aug 27
00:54:09 UTC 2014

hbase(main):001:0&gt;</code></pre>

<p>要退出交互shell命令，在任何时候键入 exit 或使用&lt;Ctrl + C&gt;。进一步处理检查shell功能之前，使用 list 命令用于列出所有可用命令。list是用来获取所有HBase 表的列表。首先，验证安装HBase在系统中使用如下所示。</p>

<pre>
<code class="language-html hljs">hbase(main):001:0&gt; list</code></pre>

<p>当输入这个命令，它给出下面的输出。</p>

<pre>
<code class="language-html hljs">hbase(main):001:0&gt; list
TABLE</code></pre>            </div>
                </div>
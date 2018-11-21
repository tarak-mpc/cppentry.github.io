---
layout:     post
title:      hbase（三）-基本的hbase shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎转载，转载时请注明出处。欢迎留言讨论，大数据讨论qq群201011292					https://blog.csdn.net/Dante_003/article/details/79135142				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HBase 为用户提供了一个非常方便的使用方式, 我们称之为“HBase Shell”。 <br>
HBase Shell 提供了大多数的 HBase 命令, 通过 HBase Shell 用户可以方便地创建、删除及修改表, 还可以向表中添加数据、列出表中的相关信息等、对region进行相关操作等。</p>

<p>在hbase启动之后，在命令里面输入“hbase shell”可以进入hbase shell的操作界面.</p>

<pre class="prettyprint"><code class="language-bash hljs ">[root@ht05 ~]<span class="hljs-comment"># hbase shell</span>
Version <span class="hljs-number">1.3</span>.<span class="hljs-number">1</span>, r930b9a55528fe45d8edce7af42fef2d35e77677a, Thu Apr  <span class="hljs-number">6</span> <span class="hljs-number">19</span>:<span class="hljs-number">36</span>:<span class="hljs-number">54</span> PDT <span class="hljs-number">2017</span>

hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt;</code></pre>

<p>同时可以输入help命令查看hbase里面所有的命令</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">help</span>
HBase Shell, version <span class="hljs-number">1.3</span>.<span class="hljs-number">1</span>, r930b9a55528fe45d8edce7af42fef2d35e77677a, Thu Apr  <span class="hljs-number">6</span> <span class="hljs-number">19</span>:<span class="hljs-number">36</span>:<span class="hljs-number">54</span> PDT <span class="hljs-number">2017</span>
Type <span class="hljs-string">'help "COMMAND"'</span>, (e.g. <span class="hljs-string">'help "get"'</span> -- the quotes are necessary) <span class="hljs-keyword">for</span> <span class="hljs-built_in">help</span> on a specific command.
Commands are grouped. Type <span class="hljs-string">'help "COMMAND_GROUP"'</span>, (e.g. <span class="hljs-string">'help "general"'</span>) <span class="hljs-keyword">for</span> <span class="hljs-built_in">help</span> on a command group.

COMMAND GROUPS:
  Group name: general
  Commands: status, table_<span class="hljs-built_in">help</span>, version, whoami

  Group name: ddl
  Commands: alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, get_table, is_disabled, is_enabled, list, locate_region, show_filters

  Group name: namespace
  Commands: alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables

  Group name: dml
  Commands: append, count, delete, deleteall, get, get_counter, get_splits, incr, put, scan, truncate, truncate_preserve

  Group name: tools
  Commands: assign, balance_switch, balancer, balancer_enabled, catalogjanitor_enabled, catalogjanitor_run, catalogjanitor_switch, close_region, compact, compact_rs, flush, major_compact, merge_region, move, normalize, normalizer_enabled, normalizer_switch, split, splitormerge_enabled, splitormerge_switch, trace, unassign, wal_roll, zk_dump

  Group name: replication
  Commands: add_peer, append_peer_tableCFs, disable_peer, disable_table_replication, enable_peer, enable_table_replication, get_peer_config, list_peer_configs, list_peers, list_replicated_tables, remove_peer, remove_peer_tableCFs, <span class="hljs-keyword">set</span>_peer_tableCFs, show_peer_tableCFs

  Group name: snapshots
  Commands: clone_snapshot, delete_all_snapshot, delete_snapshot, delete_table_snapshots, list_snapshots, list_table_snapshots, restore_snapshot, snapshot

  Group name: configuration
  Commands: update_all_config, update_config

  Group name: quotas
  Commands: list_quotas, <span class="hljs-keyword">set</span>_quota

  Group name: security
  Commands: grant, list_security_capabilities, revoke, user_permission

  Group name: procedures
  Commands: abort_procedure, list_procedures

  Group name: visibility labels
  Commands: add_labels, clear_auths, get_auths, list_labels, <span class="hljs-keyword">set</span>_auths, <span class="hljs-keyword">set</span>_visibility</code></pre>

<p><strong>这里只讲一些简单的、和数据增删改查有关的命令，一些其它的命令后面再说，例如region的维护等命令。</strong></p>



<h2 id="1create">1.create</h2>

<p>创建表</p>



<pre class="prettyprint"><code class="language-bash hljs ">创建一个具有两个列族“grad”和“course”的表“scores”。其中表名、行和列都要用单引号括起来,并以逗号隔开。
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'scores'</span>, <span class="hljs-string">'name'</span>, <span class="hljs-string">'grad'</span>, <span class="hljs-string">'course'</span></code></pre>



<h2 id="2list-命令">2.list 命令</h2>

<p>查看当前 HBase 中具有哪些表。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; list</code></pre>



<h2 id="3describe-命令">3.describe 命令</h2>

<p>查看表“scores”的构造。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'scores'</span></code></pre>



<h2 id="4put-命令">4.put 命令</h2>

<p>使用 put 命令向表中插入数据,参数分别为表名、行名、列名和值,其中列名前需要列族最为前缀,时间戳由系统自动生成。 <br>
格式: put 表名,行名,列名([列族:列名]),值 <br>
例子： <br>
a. 加入一行数据,行名称为“xiapi”,列族“grad”的列名为”(空字符串)”,值位 1。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>, <span class="hljs-string">'grad:'</span>, <span class="hljs-string">'1'</span>
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>, <span class="hljs-string">'grad:'</span>, <span class="hljs-string">'2'</span> --修改操作(update)</code></pre>

<p>b. 给“xiapi”这一行的数据的列族“course”添加一列“</p>

<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>,  <span class="hljs-string">'course:china'</span>, <span class="hljs-string">'97'</span>
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>,  <span class="hljs-string">'course:math'</span>, <span class="hljs-string">'128'</span>
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>,  <span class="hljs-string">'course:english'</span>, <span class="hljs-string">'85'</span></code></pre>



<h2 id="5get-命令">5.get 命令</h2>

<p><strong>get是一种特殊的scan，可以理解为hbase里面查询数据的方式只有scan一种</strong></p>

<p>a.查看表“scores”中的行“xiapi”的相关数据。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; get <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span></code></pre>

<p>b.查看表“scores”中行“xiapi”列“course :math”的值。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; get <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>, <span class="hljs-string">'course :math'</span></code></pre>

<p>或者</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; get <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>, {COLUMN=&gt;<span class="hljs-string">'course:math'</span>}
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; get <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>, {COLUMNS=&gt;<span class="hljs-string">'course:math'</span>}</code></pre>

<p>备注:COLUMN 和 COLUMNS 是不同的,scan 操作中的 COLUMNS 指定的是表的列族, get操作中的 COLUMN 指定的是特定的列,COLUMNS 的值实质上为“列族:列修饰符”。COLUMN 和 COLUMNS 必须为大写。</p>



<h2 id="6scan-命令">6.scan 命令</h2>

<p>a. 查看表“scores”中的所有数据。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'scores'</span></code></pre>

<p>注意: <br>
scan 命令可以指定 startrow,stoprow 来 scan 多个 row。 <br>
例如:</p>



<pre class="prettyprint"><code class="language-bash hljs ">scan <span class="hljs-string">'user_test'</span>,{COLUMNS =&gt;<span class="hljs-string">'info:username'</span>,LIMIT =&gt;<span class="hljs-number">10</span>, STARTROW =&gt; <span class="hljs-string">'test'</span>, STOPROW=&gt;<span class="hljs-string">'test2'</span>}</code></pre>

<p>b.查看表“scores”中列族“course”的所有数据。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; scan  <span class="hljs-string">'scores'</span>, {COLUMN =&gt; <span class="hljs-string">'grad'</span>}
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; scan  <span class="hljs-string">'scores'</span>, {COLUMN=&gt;<span class="hljs-string">'course:math'</span>}
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; scan  <span class="hljs-string">'scores'</span>, {COLUMNS =&gt; <span class="hljs-string">'course'</span>}
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; scan  <span class="hljs-string">'scores'</span>, {COLUMNS =&gt; <span class="hljs-string">'course'</span>}</code></pre>



<h2 id="7-count-命令">7. count 命令</h2>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">068</span>:<span class="hljs-number">0</span>&gt; count <span class="hljs-string">'scores'</span></code></pre>



<h2 id="8exists-命令">8.exists 命令</h2>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">071</span>:<span class="hljs-number">0</span>&gt; exists <span class="hljs-string">'scores'</span></code></pre>



<h2 id="9delete-命令">9.delete 命令</h2>

<p>删除表“scores”中行为“xiaoxue”, 列族“course”中的“math”。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt;  delete <span class="hljs-string">'scores'</span>, <span class="hljs-string">'xiapi'</span>, <span class="hljs-string">'course:math'</span></code></pre>



<h2 id="10truncate-命令">10.truncate 命令</h2>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt;  truncate <span class="hljs-string">'scores'</span></code></pre>



<h2 id="11disbaledrop-命令">11.disbale、drop 命令</h2>

<p>通过“disable”和“drop”命令删除“scores”表。</p>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt;  disable <span class="hljs-string">'scores'</span> --enable <span class="hljs-string">'scores'</span> 
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt;  drop <span class="hljs-string">'scores'</span></code></pre>



<h2 id="12-status命令">12. status命令</h2>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">072</span>:<span class="hljs-number">0</span>&gt; status</code></pre>



<h2 id="13version命令">13.version命令</h2>



<pre class="prettyprint"><code class="language-bash hljs ">hbase(main):<span class="hljs-number">073</span>:<span class="hljs-number">0</span>&gt; version</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
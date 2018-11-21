---
layout:     post
title:      Hadoop基础教程-第10章 HBase：Hadoop数据库（10.5 HBase Shell）（草稿）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div id="article_content" class="article_content clearfix csdn-tracking-statistics"><div class="markdown_views" style="font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;"><h2 id="第10章-hbasehadoop数据库">第10章 HBase：Hadoop数据库</h2><h2 id="105-hbase-shell"><a name="t1"></a>10.5 HBase Shell</h2><hr><h3 id="1051-官方快速入门教程"><a name="t2"></a>10.5.1 官方快速入门教程</h3><p><a href="http://hbase.apache.org/book.html#quickstart" rel="nofollow">http://hbase.apache.org/book.html#quickstart</a></p><p>Procedure: Use HBase For the First Time <br>Connect to HBase</p><p><img src="https://img-blog.csdn.net/20170725233422771?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmd5dXFpYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p><h3 id="1052-连接hbase"><a name="t3"></a>10.5.2 连接HBase</h3><p>进入HBase Shell</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs avrasm has-numbering">[root@node1 ~]<span class="hljs-preprocessor"># hbase shell</span>
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/opt/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.6</span>/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.5</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/opt/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/share/hadoop/common/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.10</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-label">SLF4J:</span> Actual binding is of type [org<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.impl</span><span class="hljs-preprocessor">.Log</span>4jLoggerFactory]
HBase Shell<span class="hljs-comment">; enter 'help&lt;RETURN&gt;' for list of supported commands.</span>
Type <span class="hljs-string">"exit&lt;RETURN&gt;"</span> to leave the HBase Shell
Version <span class="hljs-number">1.2</span><span class="hljs-number">.6</span>, rUnknown, Mon May <span class="hljs-number">29</span> <span class="hljs-number">02</span>:<span class="hljs-number">25</span>:<span class="hljs-number">32</span> CDT <span class="hljs-number">2017</span>

hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt;</code></pre><p>查看版本</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs css has-numbering"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:001</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">version</span>
1<span class="hljs-class">.2</span><span class="hljs-class">.6</span>, <span class="hljs-tag">rUnknown</span>, <span class="hljs-tag">Mon</span> <span class="hljs-tag">May</span> 29 02<span class="hljs-pseudo">:25</span><span class="hljs-pseudo">:32</span> <span class="hljs-tag">CDT</span> 2017

<span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:002</span><span class="hljs-pseudo">:0</span>&gt;</code></pre><p>查看状态</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs css has-numbering"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:002</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">status</span>
1 <span class="hljs-tag">active</span> <span class="hljs-tag">master</span>, 1 <span class="hljs-tag">backup</span> <span class="hljs-tag">masters</span>, 3 <span class="hljs-tag">servers</span>, 0 <span class="hljs-tag">dead</span>, 0<span class="hljs-class">.6667</span> <span class="hljs-tag">average</span> <span class="hljs-tag">load</span>

<span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:003</span><span class="hljs-pseudo">:0</span>&gt; </code></pre><p>查看当前用户</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs css has-numbering"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:003</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">whoami</span>
<span class="hljs-tag">root</span> (<span class="hljs-tag">auth</span><span class="hljs-pseudo">:SIMPLE)</span>
    <span class="hljs-tag">groups</span>: <span class="hljs-tag">root</span>

<span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:004</span><span class="hljs-pseudo">:0</span>&gt;</code></pre><p>查看帮助</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs lasso has-numbering">hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span><span class="hljs-subst">&gt;</span> help
HBase Shell, version <span class="hljs-number">1.2</span><span class="hljs-number">.6</span>, rUnknown, Mon May <span class="hljs-number">29</span> <span class="hljs-number">02</span>:<span class="hljs-number">25</span>:<span class="hljs-number">32</span> CDT <span class="hljs-number">2017</span>
<span class="hljs-keyword">Type</span> <span class="hljs-string">'help "COMMAND"'</span>, (e<span class="hljs-built_in">.</span>g<span class="hljs-built_in">.</span> <span class="hljs-string">'help "get"'</span> <span class="hljs-subst">--</span> the quotes are necessary) for help <span class="hljs-keyword">on</span> a specific command<span class="hljs-built_in">.</span>
Commands are grouped<span class="hljs-built_in">.</span> <span class="hljs-keyword">Type</span> <span class="hljs-string">'help "COMMAND_GROUP"'</span>, (e<span class="hljs-built_in">.</span>g<span class="hljs-built_in">.</span> <span class="hljs-string">'help "general"'</span>) for help <span class="hljs-keyword">on</span> a command <span class="hljs-keyword">group</span><span class="hljs-built_in">.</span>

COMMAND GROUPS:
  <span class="hljs-keyword">Group</span> name: general
  Commands: status, table_help, version, whoami

  <span class="hljs-keyword">Group</span> name: ddl
  Commands: alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, get_table, is_disabled, is_enabled, <span class="hljs-built_in">list</span>, locate_region, show_filters

  <span class="hljs-keyword">Group</span> name: namespace
  Commands: alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables

  <span class="hljs-keyword">Group</span> name: dml
  Commands: append, count, delete, deleteall, get, get_counter, get_splits, incr, put, scan, truncate, truncate_preserve

  <span class="hljs-keyword">Group</span> name: tools
  Commands: assign, balance_switch, balancer, balancer_enabled, catalogjanitor_enabled, catalogjanitor_run, catalogjanitor_switch, close_region, compact, compact_rs, flush, major_compact, merge_region, move, normalize, normalizer_enabled, normalizer_switch, split, trace, unassign, wal_roll, zk_dump

  <span class="hljs-keyword">Group</span> name: replication
  Commands: add_peer, append_peer_tableCFs, disable_peer, disable_table_replication, enable_peer, enable_table_replication, list_peers, list_replicated_tables, remove_peer, remove_peer_tableCFs, set_peer_tableCFs, show_peer_tableCFs

  <span class="hljs-keyword">Group</span> name: snapshots
  Commands: clone_snapshot, delete_all_snapshot, delete_snapshot, list_snapshots, restore_snapshot, snapshot

  <span class="hljs-keyword">Group</span> name: configuration
  Commands: update_all_config, update_config

  <span class="hljs-keyword">Group</span> name: quotas
  Commands: list_quotas, set_quota

  <span class="hljs-keyword">Group</span> name: security
  Commands: grant, list_security_capabilities, revoke, user_permission

  <span class="hljs-keyword">Group</span> name: procedures
  Commands: abort_procedure, list_procedures

  <span class="hljs-keyword">Group</span> name: visibility labels
  Commands: add_labels, clear_auths, get_auths, list_labels, set_auths, set_visibility

SHELL USAGE:
Quote <span class="hljs-literal">all</span> names <span class="hljs-keyword">in</span> HBase Shell such as table <span class="hljs-literal">and</span> column names<span class="hljs-built_in">.</span>  Commas delimit
command parameters<span class="hljs-built_in">.</span>  <span class="hljs-keyword">Type</span> <span class="hljs-subst">&lt;</span><span class="hljs-keyword">RETURN</span><span class="hljs-subst">&gt;</span> after entering a command <span class="hljs-keyword">to</span> run it<span class="hljs-built_in">.</span>
Dictionaries of configuration used <span class="hljs-keyword">in</span> the creation <span class="hljs-literal">and</span> alteration of tables are
Ruby Hashes<span class="hljs-built_in">.</span> They look like this:

  {<span class="hljs-string">'key1'</span> <span class="hljs-subst">=&gt;</span> <span class="hljs-string">'value1'</span>, <span class="hljs-string">'key2'</span> <span class="hljs-subst">=&gt;</span> <span class="hljs-string">'value2'</span>, <span class="hljs-attribute">...</span>}

<span class="hljs-literal">and</span> are opened <span class="hljs-literal">and</span> closed <span class="hljs-keyword">with</span> curley<span class="hljs-attribute">-braces</span><span class="hljs-built_in">.</span>  Key/values are delimited <span class="hljs-keyword">by</span> the
<span class="hljs-string">'=&gt;'</span> character combination<span class="hljs-built_in">.</span>  Usually keys are predefined constants such as
NAME, VERSIONS, COMPRESSION, etc<span class="hljs-built_in">.</span>  Constants <span class="hljs-keyword">do</span> <span class="hljs-literal">not</span> need <span class="hljs-keyword">to</span> be quoted<span class="hljs-built_in">.</span>  <span class="hljs-keyword">Type</span>
<span class="hljs-string">'Object.constants'</span> <span class="hljs-keyword">to</span> see a (messy) <span class="hljs-built_in">list</span> of <span class="hljs-literal">all</span> constants <span class="hljs-keyword">in</span> the environment<span class="hljs-built_in">.</span>

<span class="hljs-keyword">If</span> you are using binary keys <span class="hljs-literal">or</span> values <span class="hljs-literal">and</span> need <span class="hljs-keyword">to</span> enter them <span class="hljs-keyword">in</span> the shell, use
double<span class="hljs-attribute">-quote</span><span class="hljs-string">'d hexadecimal representation. For example:

  hbase&gt; get '</span>t1<span class="hljs-string">', "key\x03\x3f\xcd"
  hbase&gt; get '</span>t1<span class="hljs-string">', "key\003\023\011"
  hbase&gt; put '</span>t1<span class="hljs-string">', "test\xef\xff", '</span>f1:<span class="hljs-string">', "\x01\x33\x40"

The HBase shell is the (J)Ruby IRB with the above HBase-specific commands added.
For more on the HBase Shell, see http://hbase.apache.org/book.html
hbase(main):005:0&gt; </span></code></pre><h3 id="1053-创建数据表"><a name="t4"></a>10.5.3 创建数据表</h3><p>（1）查看create帮助</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">create</span>

ERROR: wrong <span class="hljs-built_in">number</span> <span class="hljs-operator">of</span> arguments (<span class="hljs-number">0</span> <span class="hljs-keyword">for</span> <span class="hljs-number">1</span>)

Here is some help <span class="hljs-keyword">for</span> this <span class="hljs-command"><span class="hljs-keyword">command</span>:</span>
Creates <span class="hljs-operator">a</span> table. Pass <span class="hljs-operator">a</span> table name, <span class="hljs-operator">and</span> <span class="hljs-operator">a</span> <span class="hljs-built_in">set</span> <span class="hljs-operator">of</span> column family
specifications (<span class="hljs-keyword">at</span> least <span class="hljs-constant">one</span>), <span class="hljs-operator">and</span>, optionally, table configuration.
Column specification can be <span class="hljs-operator">a</span> simple <span class="hljs-keyword">string</span> (name), <span class="hljs-operator">or</span> <span class="hljs-operator">a</span> dictionary
(dictionaries are described below <span class="hljs-operator">in</span> main help output), necessarily 
including NAME attribute. 
Examples:

Create <span class="hljs-operator">a</span> table <span class="hljs-operator">with</span> namespace=ns1 <span class="hljs-operator">and</span> table qualifier=t1
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'ns1:t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">5</span>}

Create <span class="hljs-operator">a</span> table <span class="hljs-operator">with</span> namespace=default <span class="hljs-operator">and</span> table qualifier=t1
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>}, {NAME =&gt; <span class="hljs-string">'f2'</span>}, {NAME =&gt; <span class="hljs-string">'f3'</span>}
  hbase&gt; <span class="hljs-comment"># The above in shorthand would be the following:</span>
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, <span class="hljs-string">'f2'</span>, <span class="hljs-string">'f3'</span>
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">1</span>, TTL =&gt; <span class="hljs-number">2592000</span>, BLOCKCACHE =&gt; <span class="hljs-constant">true</span>}
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, CONFIGURATION =&gt; {<span class="hljs-string">'hbase.hstore.blockingStoreFiles'</span> =&gt; <span class="hljs-string">'10'</span>}}

Table configuration options can be <span class="hljs-built_in">put</span> <span class="hljs-keyword">at</span> <span class="hljs-operator">the</span> <span class="hljs-function"><span class="hljs-keyword">end</span>.</span>
Examples:

  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'ns1:t1'</span>, <span class="hljs-string">'f1'</span>, SPLITS =&gt; [<span class="hljs-string">'10'</span>, <span class="hljs-string">'20'</span>, <span class="hljs-string">'30'</span>, <span class="hljs-string">'40'</span>]
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, SPLITS =&gt; [<span class="hljs-string">'10'</span>, <span class="hljs-string">'20'</span>, <span class="hljs-string">'30'</span>, <span class="hljs-string">'40'</span>]
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, SPLITS_FILE =&gt; <span class="hljs-string">'splits.txt'</span>, OWNER =&gt; <span class="hljs-string">'johndoe'</span>
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">5</span>}, METADATA =&gt; { <span class="hljs-string">'mykey'</span> =&gt; <span class="hljs-string">'myvalue'</span> }
  hbase&gt; <span class="hljs-comment"># Optionally pre-split the table into NUMREGIONS, using</span>
  hbase&gt; <span class="hljs-comment"># SPLITALGO ("HexStringSplit", "UniformSplit" or classname)</span>
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, {NUMREGIONS =&gt; <span class="hljs-number">15</span>, SPLITALGO =&gt; <span class="hljs-string">'HexStringSplit'</span>}
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, {NUMREGIONS =&gt; <span class="hljs-number">15</span>, SPLITALGO =&gt; <span class="hljs-string">'HexStringSplit'</span>, REGION_REPLICATION =&gt; <span class="hljs-number">2</span>, CONFIGURATION =&gt; {<span class="hljs-string">'hbase.hregion.scan.loadColumnFamiliesOnDemand'</span> =&gt; <span class="hljs-string">'true'</span>}}
  hbase&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, DFS_REPLICATION =&gt; <span class="hljs-number">1</span>}

You can also keep around <span class="hljs-operator">a</span> reference <span class="hljs-built_in">to</span> <span class="hljs-operator">the</span> created table:

  hbase&gt; t1 = <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>

Which gives you <span class="hljs-operator">a</span> reference <span class="hljs-built_in">to</span> <span class="hljs-operator">the</span> table named <span class="hljs-string">'t1'</span>, <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">which</span> <span class="hljs-title">you</span> <span class="hljs-title">can</span> <span class="hljs-title">then</span></span>
call methods.


hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt;</code></pre><p>（2）创建表 <br>语法：create ‘表名’, ‘列簇’</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs coffeescript has-numbering">hbase(main):<span class="hljs-number">007</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'t1'</span> ,<span class="hljs-string">'cf1'</span>,<span class="hljs-string">'cf2'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">4.5550</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - t1
hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt;</code></pre><p>（3）列举表</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs haml has-numbering">hbase(main):008:0&gt; list
TABLE                                                                                                                                                                                        
t1                                                                                                                                                                                           
1 row(s) in 0.0570 seconds

=<span class="ruby">&gt; [<span class="hljs-string">"t1"</span>]
</span>hbase(main):009:0&gt;</code></pre><p>（4）查看表结构</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs coffeescript has-numbering">hbase(main):<span class="hljs-number">009</span>:<span class="hljs-number">0</span>&gt; desc <span class="hljs-string">'t1'</span>
Table t1 <span class="hljs-keyword">is</span> ENABLED                                                                                                                                                                          
t1                                                                                                                                                                                           
COLUMN FAMILIES DESCRIPTION                                                                                                                                                                  
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'cf1'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSI
ONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>}                                                                                                            
{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'cf2'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FALSE'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSI
ONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>}                                                                                                            
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.3030</span> seconds

hbase(main):<span class="hljs-number">010</span>:<span class="hljs-number">0</span>&gt; </code></pre><p>（5）表是否存在</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">010</span>:<span class="hljs-number">0</span>&gt; exists <span class="hljs-string">'t1'</span>
Table t1 does exist                                                                                                                                                                          
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.5200</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">011</span>:<span class="hljs-number">0</span>&gt; </code></pre><h3 id="1054-操作数据表"><a name="t5"></a>10.5.4 操作数据表</h3><p><span style="font-weight:700;">（1）添加数据</span> <br>添加4行</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">010</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'r1'</span>,<span class="hljs-string">'cf1:name'</span>,<span class="hljs-string">'aa'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1800</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">011</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'r2'</span>,<span class="hljs-string">'cf2:age'</span>,<span class="hljs-string">'22'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0620</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'r3'</span>,<span class="hljs-string">'cf1:name'</span>,<span class="hljs-string">'bb'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0230</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">013</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'r4'</span>,<span class="hljs-string">'cf2:age'</span>,<span class="hljs-string">'23'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0190</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">014</span>:<span class="hljs-number">0</span>&gt; </code></pre><p>添加1行</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">014</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'cf1:name'</span>,<span class="hljs-string">'bb'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0210</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'cf1:age'</span>,<span class="hljs-string">'23'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0220</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">016</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'cf2:cid'</span>,<span class="hljs-string">'1001'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0220</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">017</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'cf2:dept'</span>,<span class="hljs-string">'bigdata'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0210</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">018</span>:<span class="hljs-number">0</span>&gt; </code></pre><p><span style="font-weight:700;">（2）查询</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">018</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>
COLUMN                                           CELL                                                                                                                                        
 cf1:age                                         timestamp=<span class="hljs-number">1501251921190</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">23</span>                                                                                                           
 cf1:name                                        timestamp=<span class="hljs-number">1501251900543</span>, <span class="hljs-built_in">value</span>=bb                                                                                                           
 cf2:cid                                         timestamp=<span class="hljs-number">1501251958895</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1001</span>                                                                                                         
 cf2:dept                                        timestamp=<span class="hljs-number">1501252040732</span>, <span class="hljs-built_in">value</span>=bigdata                                                                                                      
<span class="hljs-number">4</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1210</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">019</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'cf1:name'</span>
COLUMN                                           CELL                                                                                                                                        
 cf1:name                                        timestamp=<span class="hljs-number">1501251900543</span>, <span class="hljs-built_in">value</span>=bb                                                                                                           
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0440</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">020</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'cf2:dept'</span>
COLUMN                                           CELL                                                                                                                                        
 cf2:dept                                        timestamp=<span class="hljs-number">1501252040732</span>, <span class="hljs-built_in">value</span>=bigdata                                                                                                      
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0200</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">021</span>:<span class="hljs-number">0</span>&gt; </code></pre><p><span style="font-weight:700;">（3）扫描表</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">021</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'t1'</span>
ROW                                              COLUMN+CELL                                                                                                                                 
 r1                                              column=cf1:name, timestamp=<span class="hljs-number">1501251794870</span>, <span class="hljs-built_in">value</span>=aa                                                                                          
 r2                                              column=cf2:age, timestamp=<span class="hljs-number">1501251806541</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                                                                                           
 r3                                              column=cf1:name, timestamp=<span class="hljs-number">1501251815736</span>, <span class="hljs-built_in">value</span>=bb                                                                                          
 r4                                              column=cf2:age, timestamp=<span class="hljs-number">1501251824433</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">23</span>                                                                                           
 rk1                                             column=cf1:age, timestamp=<span class="hljs-number">1501251921190</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">23</span>                                                                                           
 rk1                                             column=cf1:name, timestamp=<span class="hljs-number">1501251900543</span>, <span class="hljs-built_in">value</span>=bb                                                                                          
 rk1                                             column=cf2:cid, timestamp=<span class="hljs-number">1501251958895</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1001</span>                                                                                         
 rk1                                             column=cf2:dept, timestamp=<span class="hljs-number">1501252040732</span>, <span class="hljs-built_in">value</span>=bigdata                                                                                     
<span class="hljs-number">5</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0900</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">022</span>:<span class="hljs-number">0</span>&gt; </code></pre><p><span style="font-weight:700;">（4）删除数据</span></p><p>删除整行数据</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">014</span>:<span class="hljs-number">0</span>&gt; deleteall <span class="hljs-string">'t1'</span>,<span class="hljs-string">'r1'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1620</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'t1'</span>
ROW                                              COLUMN+CELL                                                                                                                                 
 r2                                              column=cf2:age, timestamp=<span class="hljs-number">1501251806541</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                                                                                           
 r3                                              column=cf1:name, timestamp=<span class="hljs-number">1501251815736</span>, <span class="hljs-built_in">value</span>=bb                                                                                          
 r4                                              column=cf2:age, timestamp=<span class="hljs-number">1501251824433</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">23</span>                                                                                           
 rk1                                             column=cf1:age, timestamp=<span class="hljs-number">1501251921190</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">23</span>                                                                                           
 rk1                                             column=cf1:name, timestamp=<span class="hljs-number">1501251900543</span>, <span class="hljs-built_in">value</span>=bb                                                                                          
 rk1                                             column=cf2:cid, timestamp=<span class="hljs-number">1501251958895</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1001</span>                                                                                         
 rk1                                             column=cf2:dept, timestamp=<span class="hljs-number">1501252040732</span>, <span class="hljs-built_in">value</span>=bigdata                                                                                     
<span class="hljs-number">4</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.2190</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">016</span>:<span class="hljs-number">0</span>&gt;</code></pre><p>删除指定行的列</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">016</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">delete</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'cf2:dept'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0260</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">017</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>,<span class="hljs-string">'rk1'</span>
COLUMN                                           CELL                                                                                                                                        
 cf1:age                                         timestamp=<span class="hljs-number">1501251921190</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">23</span>                                                                                                           
 cf1:name                                        timestamp=<span class="hljs-number">1501251900543</span>, <span class="hljs-built_in">value</span>=bb                                                                                                           
 cf2:cid                                         timestamp=<span class="hljs-number">1501251958895</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1001</span>                                                                                                         
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0840</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">018</span>:<span class="hljs-number">0</span>&gt;</code></pre><p><span style="font-weight:700;">（5）删除表</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs coffeescript has-numbering">hbase(main):<span class="hljs-number">018</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'t2'</span>,<span class="hljs-string">'info'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">2.4840</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - t2
hbase(main):<span class="hljs-number">019</span>:<span class="hljs-number">0</span>&gt; list
TABLE                                                                                                                                                                                        
t1                                                                                                                                                                                           
t2                                                                                                                                                                                           
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0250</span> seconds<span class="hljs-function">

=&gt;</span> [<span class="hljs-string">"t1"</span>, <span class="hljs-string">"t2"</span>]
hbase(main):<span class="hljs-number">020</span>:<span class="hljs-number">0</span>&gt;</code></pre><p><span style="font-weight:700;">分两步，首先disable，然后drop</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs ocaml has-numbering">hbase(main):<span class="hljs-number">020</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'t2'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">2.2960</span> seconds

hbase(main):<span class="hljs-number">021</span>:<span class="hljs-number">0</span>&gt; drop <span class="hljs-string">'t2'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.3080</span> seconds

hbase(main):<span class="hljs-number">022</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">list</span>
TABLE                                                                                                                                                                                        
t1                                                                                                                                                                                           
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0130</span> seconds

=&gt; [<span class="hljs-string">"t1"</span>]
hbase(main):<span class="hljs-number">023</span>:<span class="hljs-number">0</span>&gt;</code></pre><p><span style="font-weight:700;">（6）截断表</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs lua has-numbering">hbase(main):<span class="hljs-number">023</span>:<span class="hljs-number">0</span>&gt; count <span class="hljs-string">'t1'</span>
<span class="hljs-number">4</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0670</span> seconds

=&gt; <span class="hljs-number">4</span>
hbase(main):<span class="hljs-number">024</span>:<span class="hljs-number">0</span>&gt; truncate <span class="hljs-string">'t1'</span>
Truncating <span class="hljs-string">'t1'</span> <span class="hljs-built_in">table</span> (it may take a <span class="hljs-keyword">while</span>):
 - Disabling <span class="hljs-built_in">table</span>...
 - Truncating <span class="hljs-built_in">table</span>...
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">4.2030</span> seconds

hbase(main):<span class="hljs-number">025</span>:<span class="hljs-number">0</span>&gt; count <span class="hljs-string">'t1'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1620</span> seconds

=&gt; <span class="hljs-number">0</span>
hbase(main):<span class="hljs-number">026</span>:<span class="hljs-number">0</span>&gt; </code></pre><p><span style="font-weight:700;">（7）修改表结构</span></p><p>先disable后enable</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">026</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'t1'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">2.3110</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">027</span>:<span class="hljs-number">0</span>&gt; alter <span class="hljs-string">'t1'</span>,READONLY
Updating all regions <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">new</span> schema...
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">1.9830</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">028</span>:<span class="hljs-number">0</span>&gt; enable <span class="hljs-string">'t1'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">1.3060</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">029</span>:<span class="hljs-number">0</span>&gt; </code></pre><h3 id="1055-命名空间"><a name="t6"></a>10.5.5 命名空间</h3><p>关系数据库系统中，命名空间namespace是表的逻辑分组,同一组中的表有类似的用途。</p><p>hbase的表也有命名空间的管理方式，命名空间的概念为即将到来的多租户特性打下基础：</p><ul style="list-style:none;"><li>配额管理（ Quota Management (HBASE-8410)）：限制一个namespace可以使用的资源，资源包括region和table等；</li><li>命名空间安全管理（ Namespace Security Administration (HBASE-9206)）：提供了另一个层面的多租户安全管理；</li><li>Region服务器组（Region server groups (HBASE-6721)）：一个命名空间或一张表，可以被固定到一组 regionservers上，从而保证了数据隔离性。</li></ul><p><span style="font-weight:700;">（1）缺省命名空间</span> <br>HBase系统默认定义了两个缺省的namespace</p><ul style="list-style:none;"><li>hbase：系统内建表，包括namespace和meta表</li><li>default：用户建表时未指定namespace的表都创建在此</li></ul><p>可以通过list_namespace命令查看命名空间</p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs css has-numbering"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:029</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">list_namespace</span>
<span class="hljs-tag">NAMESPACE</span>                                                                                                                                                                                    
<span class="hljs-tag">default</span>                                                                                                                                                                                      
<span class="hljs-tag">hbase</span>                                                                                                                                                                                        
2 <span class="hljs-tag">row</span>(<span class="hljs-tag">s</span>) <span class="hljs-tag">in</span> 0<span class="hljs-class">.2170</span> <span class="hljs-tag">seconds</span>

<span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:030</span><span class="hljs-pseudo">:0</span>&gt;</code></pre><p><span style="font-weight:700;">（2）创建namespace</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">030</span>:<span class="hljs-number">0</span>&gt; create_namespace <span class="hljs-string">'mydb'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1070</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">031</span>:<span class="hljs-number">0</span>&gt; list_namespace
NAMESPACE                                                                                                                                                                                    
default                                                                                                                                                                                      
hbase                                                                                                                                                                                        
mydb                                                                                                                                                                                         
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0400</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">032</span>:<span class="hljs-number">0</span>&gt; </code></pre><p><span style="font-weight:700;">（3）查看namespace结构</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">032</span>:<span class="hljs-number">0</span>&gt; describe_namespace <span class="hljs-string">'mydb'</span>
DESCRIPTION                                                                                                                                                                                  
{NAME =&gt; <span class="hljs-string">'mydb'</span>}                                                                                                                                                                             
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0330</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">033</span>:<span class="hljs-number">0</span>&gt; </code></pre><p><span style="font-weight:700;">（4）在指定namespace下创建表</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs coffeescript has-numbering">hbase(main):<span class="hljs-number">033</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'mydb:test'</span>,{NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'f1'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-number">5</span>}
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.2490</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - <span class="hljs-attribute">mydb</span>:test
hbase(main):<span class="hljs-number">034</span>:<span class="hljs-number">0</span>&gt;</code></pre><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs haml has-numbering">hbase(main):034:0&gt; list
TABLE                                                                                                                                                                                        
mydb:test                                                                                                                                                                                    
t1                                                                                                                                                                                           
2 row(s) in 0.0390 seconds

=<span class="ruby">&gt; [<span class="hljs-string">"mydb:test"</span>, <span class="hljs-string">"t1"</span>]
</span>hbase(main):035:0&gt;</code></pre><p><span style="font-weight:700;">（5）添加数据</span></p><pre class="prettyprint" style="font-size:14px;line-height:22px;"><code class="hljs livecodeserver has-numbering">hbase(main):<span class="hljs-number">035</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'mydb:test'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'f1:name'</span>,<span class="hljs-string">'abc'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1310</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">036</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'mydb:test'</span>,<span class="hljs-string">'rk1'</span>,<span class="hljs-string">'f1:age'</span>,<span class="hljs-string">'2'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0220</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">037</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'mydb:test'</span>
ROW                                              COLUMN+CELL                                                                                                                                 
 rk1                                             column=f1:age, timestamp=<span class="hljs-number">1501335244278</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">2</span>                                                                                             
 rk1                                             column=f1:name, timestamp=<span class="hljs-number">1501335234503</span>, <span class="hljs-built_in">value</span>=abc                                                                                          
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0970</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">038</span>:<span class="hljs-number">0</span>&gt;</code></pre></div></div><div class="article-bar-bottom" style="border-bottom:1px solid rgb(227,227,227);color:rgb(51,51,51);font-family:'SF Pro Display', Roboto, Noto, Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', sans-serif;font-size:14px;"><div class="article-copyright" style="font-size:12px;color:rgb(153,153,153);">灰常灰常感谢原博主的辛苦工作，为防止删博，所以转载，只供学习使用，不做其他任何商业用途。	https://blog.csdn.net/chengyuqiang/article/details/76100377</div></div>            </div>
                </div>
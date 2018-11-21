---
layout:     post
title:      HBase Shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_33624294/article/details/53002230				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>当HBase集群配置好启动后，就可以使用HBase的Shell对HBase进行各种操作，如建表、添加记录、添加列族和删除表等。需使用HBase的bin目录下的HBase命令才能进入，具体操作如下：</p>
</blockquote>

<p><strong>进入HBase</strong></p>



<pre class="prettyprint"><code class=" hljs vhdl">[gznc@master ~]$ cd /home/gznc/hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.7</span>-hadoop2
[gznc@master hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.7</span>-hadoop2]$ bin/hbase shell
<span class="hljs-number">2016</span>-<span class="hljs-number">11</span>-<span class="hljs-number">01</span> <span class="hljs-number">19</span>:<span class="hljs-number">40</span>:<span class="hljs-number">21</span>,<span class="hljs-number">367</span> INFO  [main] <span class="hljs-keyword">Configuration</span>.deprecation: hadoop.native.lib <span class="hljs-keyword">is</span> deprecated. Instead, <span class="hljs-keyword">use</span> io.native.lib.available
HBase Shell; enter <span class="hljs-attribute">'help</span>&lt;<span class="hljs-keyword">RETURN</span>&gt;' <span class="hljs-keyword">for</span> list <span class="hljs-keyword">of</span> supported commands.
<span class="hljs-keyword">Type</span> <span class="hljs-string">"exit&lt;RETURN&gt;"</span> <span class="hljs-keyword">to</span> leave the HBase Shell
Version <span class="hljs-number">0.98</span><span class="hljs-number">.7</span>-hadoop2, r800c23e2207aa3f9bddb7e9514d8340bcfb89277, Wed Oct  <span class="hljs-number">8</span> <span class="hljs-number">15</span>:<span class="hljs-number">58</span>:<span class="hljs-number">11</span> PDT <span class="hljs-number">2014</span>
hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span>&gt; </code></pre>

<p><strong>帮助命令help</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">hbase(main):<span class="hljs-number">001</span>:<span class="hljs-number">0</span><span class="hljs-subst">&gt;</span> help
HBase Shell, version <span class="hljs-number">0.98</span><span class="hljs-number">.7</span><span class="hljs-attribute">-hadoop2</span>, r800c23e2207aa3f9bddb7e9514d8340bcfb89277, Wed Oct  <span class="hljs-number">8</span> <span class="hljs-number">15</span>:<span class="hljs-number">58</span>:<span class="hljs-number">11</span> PDT <span class="hljs-number">2014</span>
<span class="hljs-keyword">Type</span> <span class="hljs-string">'help "COMMAND"'</span>, (e<span class="hljs-built_in">.</span>g<span class="hljs-built_in">.</span> <span class="hljs-string">'help "get"'</span> <span class="hljs-subst">--</span> the quotes are necessary) for help <span class="hljs-keyword">on</span> a specific command<span class="hljs-built_in">.</span>
Commands are grouped<span class="hljs-built_in">.</span> <span class="hljs-keyword">Type</span> <span class="hljs-string">'help "COMMAND_GROUP"'</span>, (e<span class="hljs-built_in">.</span>g<span class="hljs-built_in">.</span> <span class="hljs-string">'help "general"'</span>) for help <span class="hljs-keyword">on</span> a command <span class="hljs-keyword">group</span><span class="hljs-built_in">.</span>

COMMAND GROUPS:
  <span class="hljs-keyword">Group</span> name: general
  Commands: status, table_help, version, whoami

  <span class="hljs-keyword">Group</span> name: ddl
  Commands: alter, alter_async, alter_status, create, describe, disable, disable_all, drop, drop_all, enable, enable_all, exists, get_table, is_disabled, is_enabled, <span class="hljs-built_in">list</span>, show_filters

  <span class="hljs-keyword">Group</span> name: namespace
  Commands: alter_namespace, create_namespace, describe_namespace, drop_namespace, list_namespace, list_namespace_tables

  <span class="hljs-keyword">Group</span> name: dml
  Commands: append, count, delete, deleteall, get, get_counter, incr, put, scan, truncate, truncate_preserve

  <span class="hljs-keyword">Group</span> name: tools
  Commands: assign, balance_switch, balancer, catalogjanitor_enabled, catalogjanitor_run, catalogjanitor_switch, close_region, compact, flush, hlog_roll, major_compact, merge_region, move, split, trace, unassign, zk_dump

  <span class="hljs-keyword">Group</span> name: replication
  Commands: add_peer, disable_peer, enable_peer, list_peers, list_replicated_tables, remove_peer, set_peer_tableCFs, show_peer_tableCFs

  <span class="hljs-keyword">Group</span> name: snapshots
  Commands: clone_snapshot, delete_snapshot, list_snapshots, rename_snapshot, restore_snapshot, snapshot

  <span class="hljs-keyword">Group</span> name: security
  Commands: grant, revoke, user_permission

  <span class="hljs-keyword">Group</span> name: visibility labels
  Commands: add_labels, clear_auths, get_auths, set_auths, set_visibility

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
For more on the HBase Shell, see http://hbase.apache.org/docs/current/book.html
hbase(main):002:0&gt; </span></code></pre>

<p><strong>查看HBaseRegionServer运行状态</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">hbase(main):<span class="hljs-number">002</span>:<span class="hljs-number">0</span>&gt; status
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/home/gznc/hbase-<span class="hljs-number">0.98</span><span class="hljs-number">.7</span>-hadoop2/lib/slf4j-log4j12-<span class="hljs-number">1.6</span><span class="hljs-number">.4</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/home/gznc/hadoop-<span class="hljs-number">2.5</span><span class="hljs-number">.2</span>/share/hadoop/common/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.5</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-number">2016</span>-<span class="hljs-number">11</span>-<span class="hljs-number">01</span> <span class="hljs-number">19</span>:<span class="hljs-number">44</span>:<span class="hljs-number">41</span>,<span class="hljs-number">122</span> WARN  [main] util<span class="hljs-preprocessor">.NativeCodeLoader</span>: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
<span class="hljs-number">3</span> servers, <span class="hljs-number">0</span> dead, <span class="hljs-number">0.6667</span> average load

hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; </code></pre>

<p><strong>查看HBase版本</strong></p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:004</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">version</span>
0<span class="hljs-class">.98</span><span class="hljs-class">.7-hadoop2</span>, <span class="hljs-tag">r800c23e2207aa3f9bddb7e9514d8340bcfb89277</span>, <span class="hljs-tag">Wed</span> <span class="hljs-tag">Oct</span>  8 15<span class="hljs-pseudo">:58</span><span class="hljs-pseudo">:11</span> <span class="hljs-tag">PDT</span> 2014</code></pre>

<p><strong>查看当前用户</strong></p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:005</span><span class="hljs-pseudo">:0</span>&gt; <span class="hljs-tag">whoami</span>
<span class="hljs-tag">gznc</span> (<span class="hljs-tag">auth</span><span class="hljs-pseudo">:SIMPLE)</span>
    <span class="hljs-tag">groups</span>: <span class="hljs-tag">gznc</span>
</code></pre>

<p><strong>Hbase数据库操作命令</strong> <br>
<strong>创建表</strong></p>

<table>
    <tbody><tr>
        <th rowspan="2">行健</th>
        <th colspan="5">basic_info</th>
        <th colspan="3">school_info</th>
    </tr>
    <tr>
        <td>name</td>
        <td>gender</td>
        <td>birthday</td>
         <td>connect</td>
        <td>address</td>
        <td>college</td>
         <td>class</td>
        <td>subject</td>
    </tr>
     <tr>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
         <td></td>
        <td></td>
        <td></td>
    </tr>
     <tr>
        </tr><tr>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
         <td></td>
        <td></td>
        <td></td>
    </tr>
    
     <tr>
      </tr><tr>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
        <td></td>
         <td></td>
        <td></td>
        <td></td>
    </tr>
</tbody></table>

<blockquote>
  <p>该表有两个列族，分别是basic_info和school_info,分别用来存储基本信息和学校信息。basic_info包含5个列name、gender、birthday、connect、address，分别代表姓名、性别、出生年月、联系方式、住址；school_info包含三个列college、class、subject,分别代表所属学院、班级、专业。</p>
</blockquote>

<p><strong>创建表students，格式如下：</strong> <br>
create  ‘表名’   ‘列族1’，’列族2’，…..‘列族n’</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'students'</span> ,<span class="hljs-string">'stu_id'</span>,<span class="hljs-string">'basic_info'</span>,<span class="hljs-string">'school_info'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.0970</span> seconds<span class="hljs-function">
=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - students</code></pre>

<p><strong>查看所有表</strong></p>



<pre class="prettyprint"><code class=" hljs php">hbase(main):<span class="hljs-number">009</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">list</span>
TABLE                                                                           
students                                                                        
<span class="hljs-number">1</span> row(s) in <span class="hljs-number">0.0660</span> seconds
=&gt; [<span class="hljs-string">"students"</span>]</code></pre>

<p><strong>查看表结构</strong></p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'students'</span>
DESCRIPTION                                          ENABLED                    
 <span class="hljs-string">'students'</span>, {NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'basic_info'</span>, DATA_BLOCK_ENCOD <span class="hljs-literal">true</span>                       
 ING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SC                            
 OPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>,                            
  MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETE                            
 D_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function">                            
  =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}, {NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'schoo                            
 l_info'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function">                            
  =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1                            
 '</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function">                             
 =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKS                            
 IZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span>                            
  <span class="hljs-string">'true'</span>}, {NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'stu_id'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span>                            
  <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span>                            
  <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_V                            
 ERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELL                            
 S<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'f                            
 alse'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}                                                   
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.2400</span> seconds
</code></pre>

<p><strong>删除列族stu_id</strong></p>

<blockquote>
  <p>删除列族要使用alter命令，用alter命令删除列族的格式有如下几种： <br>
  alter  ‘表名’ ， NAME=&gt;’列族名’，METHOD=&gt;’delete’ <br>
  alter  ‘表名’ ， {NAME=&gt;’列族名’，METHOD=&gt;’delete’} <br>
  alter  ‘表名’ ， ‘delete’=&gt; ‘列族名’</p>
</blockquote>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; alter <span class="hljs-string">'students'</span>,<span class="hljs-string">'delete'</span>=&gt;<span class="hljs-string">'stu_id'</span>
Updating all regions <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">new</span> schema...
<span class="hljs-number">0</span>/<span class="hljs-number">1</span> regions updated.
<span class="hljs-number">1</span>/<span class="hljs-number">1</span> regions updated.
Done.
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">2.4690</span> <span class="hljs-built_in">seconds</span></code></pre>

<p>此版本的HBase中，删除一个表的列族不需要使表在非使用的状态可以直接进行操作，在之前的某些版本中则不行，直接删除会出现以下错误</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">ERROR:Table memberis enable.Disable <span class="hljs-keyword">it</span> <span class="hljs-keyword">first</span> <span class="hljs-keyword">before</span> altering</code></pre>

<p>这时需要先disable要操作的表，删除后，在进行enable操作，格式为disable/enable   ‘表名’</p>

<p><strong>注意：ddl命令是区分大小写的，像ddl中的alter,create, drop, enable等都必需用小写。而{}中的属性名都必需用大写。</strong> <br>
查看列族stu_id删除是否成功</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'students'</span>
DESCRIPTION                                          ENABLED                    
 <span class="hljs-string">'students'</span>, {NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'basic_info'</span>, DATA_BLOCK_ENCOD <span class="hljs-literal">true</span>                       
 ING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SC                            
 OPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1'</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>,                            
  MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETE                            
 D_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKSIZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function">                            
  =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'true'</span>}, {NAME<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'schoo                            
 l_info'</span>, DATA_BLOCK_ENCODING<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, BLOOMFILTER<span class="hljs-function">                            
  =&gt;</span> <span class="hljs-string">'ROW'</span>, REPLICATION_SCOPE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'1                            
 '</span>, COMPRESSION<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'NONE'</span>, MIN_VERSIONS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'0'</span>, TTL<span class="hljs-function">                             
 =&gt;</span> <span class="hljs-string">'FOREVER'</span>, KEEP_DELETED_CELLS<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKS                            
 IZE<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'65536'</span>, IN_MEMORY<span class="hljs-function"> =&gt;</span> <span class="hljs-string">'false'</span>, BLOCKCACHE<span class="hljs-function"> =&gt;</span>                            
  <span class="hljs-string">'true'</span>}                                                                       
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1090</span> seconds
</code></pre>

<p><strong>删除一个表</strong> <br>
删除一个表必须先disable,否则会出现错误，删除表使用drop命令，格式为</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">011</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'t'</span>,<span class="hljs-string">'s'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.5130</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - t
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; list
TABLE                                                                           
students                                                                        
t                                                                               
<span class="hljs-number">2</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0710</span> seconds<span class="hljs-function">

=&gt;</span> [<span class="hljs-string">"students"</span>, <span class="hljs-string">"t"</span>]

hbase(main):<span class="hljs-number">014</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'t'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.4960</span> seconds

hbase(main):<span class="hljs-number">015</span>:<span class="hljs-number">0</span>&gt; drop <span class="hljs-string">'t'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.2660</span> seconds</code></pre>

<p><strong>查询一个表是否存在</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">016</span>:<span class="hljs-number">0</span>&gt; exists <span class="hljs-string">'t'</span>
Table t does <span class="hljs-operator">not</span> exist                                                          
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0470</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">017</span>:<span class="hljs-number">0</span>&gt; exists <span class="hljs-string">'students'</span>
Table students does exist                                                       
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0750</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>查询一个表的使用状态</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">019</span>:<span class="hljs-number">0</span>&gt; is_enabled <span class="hljs-string">'students'</span>
<span class="hljs-constant">true</span>                                                                            
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0580</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">020</span>:<span class="hljs-number">0</span>&gt; is_disabled <span class="hljs-string">'students'</span>
<span class="hljs-constant">false</span>                                                                           
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0570</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>dml操作</strong> <br>
dml操作主要是对表的记录的操作，如插入记录、查询记录等。 <br>
1.使用put命令向students表中插入记录，put命令格式如下： <br>
put  ‘表名’   ‘行键’，‘列族:列’，‘值’</p>



<pre class="prettyprint"><code class=" hljs coffeescript">hbase(main):<span class="hljs-number">021</span>:<span class="hljs-number">0</span>&gt; stu = get_table <span class="hljs-string">'students'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0040</span> seconds<span class="hljs-function">

=&gt;</span> <span class="hljs-attribute">Hbase</span>::Table - students
hbase(main):<span class="hljs-number">022</span>:<span class="hljs-number">0</span>&gt; stu.put <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:name'</span>,<span class="hljs-string">'YangMing'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.4390</span> seconds
hbase(main):<span class="hljs-number">023</span>:<span class="hljs-number">0</span>&gt; stu.put <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:gender'</span>,<span class="hljs-string">'male'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0130</span> seconds
hbase(main):<span class="hljs-number">024</span>:<span class="hljs-number">0</span>&gt; stu.put <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:birthday'</span>,<span class="hljs-string">'1988-05-23'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0260</span> seconds
hbase(main):<span class="hljs-number">025</span>:<span class="hljs-number">0</span>&gt; stu.put <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:connect'</span>,<span class="hljs-string">'13911111111'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0240</span> seconds
hbase(main):<span class="hljs-number">026</span>:<span class="hljs-number">0</span>&gt; stu.put <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:address'</span>,<span class="hljs-string">'SiChuan-Chengdu'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0440</span> seconds
hbase(main):<span class="hljs-number">027</span>:<span class="hljs-number">0</span>&gt; stu.put <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'school_info:college'</span>,<span class="hljs-string">'ChengXing'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0340</span> seconds
hbase(main):<span class="hljs-number">028</span>:<span class="hljs-number">0</span>&gt; stu.put <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'school_info:class'</span>,<span class="hljs-string">'class 1 grade 2'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0120</span> seconds
hbase(main):<span class="hljs-number">029</span>:<span class="hljs-number">0</span>&gt; stu.put <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'school_info:object'</span>,<span class="hljs-string">'Software'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0130</span> seconds</code></pre>

<p><strong>注意：</strong>第一步创建了students表的引用stu,这样操作更加方便 <br>
<strong>2.使用get获取数据，get命令的格式如下：</strong></p>

<p>get 表名’ ， ‘行健’[, ‘列族[:列]’]  其中[ ] 为可选内容</p>

<pre class="prettyprint"><code class=" hljs ocaml">hbase(main):<span class="hljs-number">033</span>:<span class="hljs-number">0</span>&gt; get <span class="hljs-string">'students'</span>,<span class="hljs-string">'2016111'</span>
COLUMN                CELL                                                      
 basic_info:address   timestamp=<span class="hljs-number">1478006969468</span>, <span class="hljs-keyword">value</span>=SiChuan-Chengdu            
 basic_info:birthday  timestamp=<span class="hljs-number">1478006906499</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">23</span>                 
 basic_info:connect   timestamp=<span class="hljs-number">1478006935108</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">13911111111</span>                
 basic_info:gender    timestamp=<span class="hljs-number">1478006880018</span>, <span class="hljs-keyword">value</span>=male                       
 basic_info:name      timestamp=<span class="hljs-number">1478006860715</span>, <span class="hljs-keyword">value</span>=YangMing                   
 school_info:<span class="hljs-keyword">class</span>    timestamp=<span class="hljs-number">1478007026804</span>, <span class="hljs-keyword">value</span>=<span class="hljs-keyword">class</span> <span class="hljs-number">1</span> grade <span class="hljs-number">2</span>            
 school_info:college  timestamp=<span class="hljs-number">1478007005614</span>, <span class="hljs-keyword">value</span>=ChengXing                  
 school_info:<span class="hljs-keyword">object</span>   timestamp=<span class="hljs-number">1478007066226</span>, <span class="hljs-keyword">value</span>=Software                   
<span class="hljs-number">8</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1400</span> seconds</code></pre>

<p>获取一个行键的所有数据：</p>

<pre class="prettyprint"><code class=" hljs ocaml">hbase(main):<span class="hljs-number">034</span>:<span class="hljs-number">0</span>&gt; stu.get <span class="hljs-string">'2016111'</span>
COLUMN                CELL                                                      
 basic_info:address   timestamp=<span class="hljs-number">1478006969468</span>, <span class="hljs-keyword">value</span>=SiChuan-Chengdu            
 basic_info:birthday  timestamp=<span class="hljs-number">1478006906499</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">23</span>                 
 basic_info:connect   timestamp=<span class="hljs-number">1478006935108</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">13911111111</span>                
 basic_info:gender    timestamp=<span class="hljs-number">1478006880018</span>, <span class="hljs-keyword">value</span>=male                       
 basic_info:name      timestamp=<span class="hljs-number">1478006860715</span>, <span class="hljs-keyword">value</span>=YangMing                   
 school_info:<span class="hljs-keyword">class</span>    timestamp=<span class="hljs-number">1478007026804</span>, <span class="hljs-keyword">value</span>=<span class="hljs-keyword">class</span> <span class="hljs-number">1</span> grade <span class="hljs-number">2</span>            
 school_info:college  timestamp=<span class="hljs-number">1478007005614</span>, <span class="hljs-keyword">value</span>=ChengXing                  
 school_info:<span class="hljs-keyword">object</span>   timestamp=<span class="hljs-number">1478007066226</span>, <span class="hljs-keyword">value</span>=Software                   
<span class="hljs-number">8</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0960</span> seconds</code></pre>

<p>从以上输出可以看出，各列族数据是按列排好序的。 <br>
获取一个行键某列族的所有数据：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">035</span>:<span class="hljs-number">0</span>&gt; stu.<span class="hljs-built_in">get</span> <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info'</span>
COLUMN                CELL                                                      
 basic_info:address   timestamp=<span class="hljs-number">1478006969468</span>, <span class="hljs-built_in">value</span>=SiChuan-Chengdu            
 basic_info:birthday  timestamp=<span class="hljs-number">1478006906499</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">23</span>                 
 basic_info:connect   timestamp=<span class="hljs-number">1478006935108</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">13911111111</span>                
 basic_info:gender    timestamp=<span class="hljs-number">1478006880018</span>, <span class="hljs-built_in">value</span>=male                       
 basic_info:name      timestamp=<span class="hljs-number">1478006860715</span>, <span class="hljs-built_in">value</span>=YangMing                   
<span class="hljs-number">5</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0380</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>获取一个行键某列族最新的数据</strong></p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">036</span>:<span class="hljs-number">0</span>&gt; stu.<span class="hljs-built_in">get</span> <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:name'</span>
COLUMN                CELL                                                      
 basic_info:name      timestamp=<span class="hljs-number">1478006860715</span>, <span class="hljs-built_in">value</span>=YangMing                   
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0260</span> <span class="hljs-built_in">seconds</span></code></pre>

<p><strong>3.为某条数据增加一个版本</strong></p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">037</span>:<span class="hljs-number">0</span>&gt; stu.<span class="hljs-built_in">put</span> <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:connect'</span>,<span class="hljs-string">'Tel:13252367856'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0250</span> <span class="hljs-built_in">seconds</span>
hbase(main):<span class="hljs-number">038</span>:<span class="hljs-number">0</span>&gt; stu.<span class="hljs-built_in">get</span> <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:connect'</span>
COLUMN                CELL                                                      
 basic_info:connect   timestamp=<span class="hljs-number">1478019142670</span>, <span class="hljs-built_in">value</span>=Tel:<span class="hljs-number">13252367856</span>            
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0220</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p><strong>4.通过时间戳获取两个版本的数据</strong> <br>
花括号里面的属性必须要大写</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">039</span>:<span class="hljs-number">0</span>&gt; stu.<span class="hljs-built_in">get</span> <span class="hljs-string">'2016111'</span>,{COLUMN=&gt;<span class="hljs-string">'basic_info:connect'</span>,TIMESTAMP=&gt;<span class="hljs-number">1478019142670</span>}
COLUMN                CELL                                                      
 basic_info:connect   timestamp=<span class="hljs-number">1478019142670</span>, <span class="hljs-built_in">value</span>=Tel:<span class="hljs-number">13252367856</span>            
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0250</span> <span class="hljs-built_in">seconds</span>
hbase(main):<span class="hljs-number">040</span>:<span class="hljs-number">0</span>&gt; stu.<span class="hljs-built_in">get</span> <span class="hljs-string">'2016111'</span>,{COLUMN=&gt;<span class="hljs-string">'basic_info:connect'</span>,TIMESTAMP=&gt;<span class="hljs-number">1478006935108</span>}
COLUMN                CELL                                                      
 basic_info:connect   timestamp=<span class="hljs-number">1478006935108</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">13911111111</span>                
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0800</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p><strong>5.全表扫描</strong></p>

<pre class="prettyprint"><code class=" hljs ocaml">hbase(main):<span class="hljs-number">041</span>:<span class="hljs-number">0</span>&gt; stu.scan
ROW                   COLUMN+CELL                                               
 <span class="hljs-number">2016111</span>              column=basic_info:address, timestamp=<span class="hljs-number">1478006969468</span>, <span class="hljs-keyword">value</span>=
                      SiChuan-Chengdu                                           
 <span class="hljs-number">2016111</span>              column=basic_info:birthday, timestamp=<span class="hljs-number">1478006906499</span>, <span class="hljs-keyword">value</span>
                      =<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">23</span>                                               
 <span class="hljs-number">2016111</span>              column=basic_info:connect, timestamp=<span class="hljs-number">1478019142670</span>, <span class="hljs-keyword">value</span>=
                      Tel:<span class="hljs-number">13252367856</span>                                           
 <span class="hljs-number">2016111</span>              column=basic_info:gender, timestamp=<span class="hljs-number">1478006880018</span>, <span class="hljs-keyword">value</span>=m
                      ale                                                       
 <span class="hljs-number">2016111</span>              column=basic_info:name, timestamp=<span class="hljs-number">1478006860715</span>, <span class="hljs-keyword">value</span>=Yan
                      gMing                                                     
 <span class="hljs-number">2016111</span>              column=school_info:<span class="hljs-keyword">class</span>, timestamp=<span class="hljs-number">1478007026804</span>, <span class="hljs-keyword">value</span>=c
                      lass <span class="hljs-number">1</span> grade <span class="hljs-number">2</span>                                            
 <span class="hljs-number">2016111</span>              column=school_info:college, timestamp=<span class="hljs-number">1478007005614</span>, <span class="hljs-keyword">value</span>
                      =ChengXing                                                
 <span class="hljs-number">2016111</span>              column=school_info:<span class="hljs-keyword">object</span>, timestamp=<span class="hljs-number">1478007066226</span>, <span class="hljs-keyword">value</span>=
                      Software                                                  
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0850</span> seconds</code></pre>

<p><strong>6.删除某行键类的某列</strong></p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; stu.<span class="hljs-built_in">delete</span> <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info:connect'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.4540</span> <span class="hljs-built_in">seconds</span>
hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; stu.<span class="hljs-built_in">get</span> <span class="hljs-string">'2016111'</span>,<span class="hljs-string">'basic_info'</span>
COLUMN                CELL                                                      
 basic_info:address   timestamp=<span class="hljs-number">1478006969468</span>, <span class="hljs-built_in">value</span>=SiChuan-Chengdu            
 basic_info:birthday  timestamp=<span class="hljs-number">1478006906499</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">1988</span>-<span class="hljs-number">05</span>-<span class="hljs-number">23</span>                 
 basic_info:gender    timestamp=<span class="hljs-number">1478006880018</span>, <span class="hljs-built_in">value</span>=male                       
 basic_info:name      timestamp=<span class="hljs-number">1478006860715</span>, <span class="hljs-built_in">value</span>=YangMing                   
<span class="hljs-number">4</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.1330</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p><strong>7.以行键为单位，查询表有多少行</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">hbase(main):<span class="hljs-number">007</span>:<span class="hljs-number">0</span>&gt; stu<span class="hljs-preprocessor">.count</span>
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.1070</span> seconds
=&gt; <span class="hljs-number">1</span></code></pre>

<p><strong>8.清空整张表</strong></p>



<pre class="prettyprint"><code class=" hljs lua">hbase(main):<span class="hljs-number">008</span>:<span class="hljs-number">0</span>&gt; truncate <span class="hljs-string">'students'</span>
Truncating <span class="hljs-string">'students'</span> <span class="hljs-built_in">table</span> (it may take a <span class="hljs-keyword">while</span>):
 - Disabling <span class="hljs-built_in">table</span>...
 - Dropping <span class="hljs-built_in">table</span>...
 - Creating <span class="hljs-built_in">table</span>...
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">2.9440</span> seconds</code></pre>

<p><strong>注意：</strong> <br>
truncate 操作其实是先disable某张表，然后删除表，在根据表的结构重新创建同名称的表</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
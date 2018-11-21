---
layout:     post
title:      hbase初识---hbase shell操作以及对应java API开发
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="hbase说白了就是数据库那么数据库一般都有增删改查操作我们下面就通过hbase-shell-和java-api-对比看看他们分别是怎么操作hbase的">hbase说白了就是数据库，那么数据库一般都有增、删、改、查操作，我们下面就通过hbase shell 和java API ，对比看看他们分别是怎么操作hbase的。</h3>

<p>启动hbase：</p>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@dev</span>-<span class="hljs-number">02</span> bin]<span class="hljs-comment"># ./start-hbase.sh </span></code></pre>

<p>使用shell连接你的hbase：</p>



<pre class="prettyprint"><code class=" hljs vhdl">[zhangshk@fonova-hadoop1 ~]$ hbase shell
Java HotSpot(TM) <span class="hljs-number">64</span>-<span class="hljs-typename">Bit</span> Server VM warning: Using incremental CMS <span class="hljs-keyword">is</span> deprecated <span class="hljs-keyword">and</span> will likely be removed <span class="hljs-keyword">in</span> a future <span class="hljs-keyword">release</span>
<span class="hljs-number">17</span>/<span class="hljs-number">12</span>/<span class="hljs-number">17</span> <span class="hljs-number">12</span>:<span class="hljs-number">10</span>:<span class="hljs-number">46</span> INFO <span class="hljs-keyword">Configuration</span>.deprecation: hadoop.native.lib <span class="hljs-keyword">is</span> deprecated. Instead, <span class="hljs-keyword">use</span> io.native.lib.available
HBase Shell; enter <span class="hljs-attribute">'help</span>&lt;<span class="hljs-keyword">RETURN</span>&gt;' <span class="hljs-keyword">for</span> list <span class="hljs-keyword">of</span> supported commands.
<span class="hljs-keyword">Type</span> <span class="hljs-string">"exit&lt;RETURN&gt;"</span> <span class="hljs-keyword">to</span> leave the HBase Shell
Version <span class="hljs-number">1.0</span><span class="hljs-number">.0</span>-cdh5<span class="hljs-number">.5</span><span class="hljs-number">.2</span>, rUnknown, Mon Jan <span class="hljs-number">25</span> <span class="hljs-number">16</span>:<span class="hljs-number">27</span>:<span class="hljs-number">11</span> PST <span class="hljs-number">2016</span>
并列出所有的表：
hbase(main):<span class="hljs-number">012</span>:<span class="hljs-number">0</span>&gt; list</code></pre>

<p>java API连接hbase的代码为： <br>
并列出所有的table</p>



<pre class="prettyprint"><code class=" hljs avrasm">
package <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.zhangshk</span><span class="hljs-comment">;</span>

import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.conf</span><span class="hljs-preprocessor">.Configuration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.HBaseConfiguration</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.TableName</span><span class="hljs-comment">;</span>
import org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.hbase</span><span class="hljs-preprocessor">.client</span><span class="hljs-preprocessor">.HBaseAdmin</span><span class="hljs-comment">;</span>

public class TestConnectionToHbase {
    public static Configuration conf = HBaseConfiguration<span class="hljs-preprocessor">.create</span>()<span class="hljs-comment">;</span>
    public static void main(String[] args) throws Exception{
        HBaseAdmin hBaseAdmin = new HBaseAdmin(conf)<span class="hljs-comment">;</span>
        TableName[] tableNames = hBaseAdmin<span class="hljs-preprocessor">.listTableNames</span>()<span class="hljs-comment">;</span>
        for (TableName tableName:
             tableNames) {
            System<span class="hljs-preprocessor">.out</span><span class="hljs-preprocessor">.println</span>(tableName<span class="hljs-preprocessor">.toString</span>())<span class="hljs-comment">;</span>
        }
    }
}</code></pre>

<p>hbase shell 创建一张表：</p>



<pre class="prettyprint"><code class=" hljs sql">我们可以先看一下<span class="hljs-operator"><span class="hljs-keyword">create</span>的语法：   hbase shell命令行下输入<span class="hljs-keyword">create</span>
hbase(main):<span class="hljs-number">030</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">create</span>

Examples:

<span class="hljs-keyword">Create</span> a <span class="hljs-keyword">table</span> <span class="hljs-keyword">with</span> namespace=ns1 <span class="hljs-keyword">and</span> <span class="hljs-keyword">table</span> qualifier=t1
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'ns1:t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">5</span>}

<span class="hljs-keyword">Create</span> a <span class="hljs-keyword">table</span> <span class="hljs-keyword">with</span> namespace=<span class="hljs-keyword">default</span> <span class="hljs-keyword">and</span> <span class="hljs-keyword">table</span> qualifier=t1
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>}, {NAME =&gt; <span class="hljs-string">'f2'</span>}, {NAME =&gt; <span class="hljs-string">'f3'</span>}
  hbase&gt; # The above <span class="hljs-keyword">in</span> shorthand would be the following:
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, <span class="hljs-string">'f2'</span>, <span class="hljs-string">'f3'</span>
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">1</span>, TTL =&gt; <span class="hljs-number">2592000</span>, BLOCKCACHE =&gt; <span class="hljs-keyword">true</span>}
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, CONFIGURATION =&gt; {<span class="hljs-string">'hbase.hstore.blockingStoreFiles'</span> =&gt; <span class="hljs-string">'10'</span>}}

<span class="hljs-keyword">Table</span> configuration options can be put <span class="hljs-keyword">at</span> the <span class="hljs-keyword">end</span>.
Examples:

  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'ns1:t1'</span>, <span class="hljs-string">'f1'</span>, SPLITS =&gt; [<span class="hljs-string">'10'</span>, <span class="hljs-string">'20'</span>, <span class="hljs-string">'30'</span>, <span class="hljs-string">'40'</span>]
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, SPLITS =&gt; [<span class="hljs-string">'10'</span>, <span class="hljs-string">'20'</span>, <span class="hljs-string">'30'</span>, <span class="hljs-string">'40'</span>]
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, SPLITS_FILE =&gt; <span class="hljs-string">'splits.txt'</span>, OWNER =&gt; <span class="hljs-string">'johndoe'</span>
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, {NAME =&gt; <span class="hljs-string">'f1'</span>, VERSIONS =&gt; <span class="hljs-number">5</span>}, METADATA =&gt; { <span class="hljs-string">'mykey'</span> =&gt; <span class="hljs-string">'myvalue'</span> }
  hbase&gt; # Optionally pre-split the <span class="hljs-keyword">table</span> <span class="hljs-keyword">into</span> NUMREGIONS, <span class="hljs-keyword">using</span>
  hbase&gt; # SPLITALGO (<span class="hljs-string">"HexStringSplit"</span>, <span class="hljs-string">"UniformSplit"</span> <span class="hljs-keyword">or</span> classname)
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, {NUMREGIONS =&gt; <span class="hljs-number">15</span>, SPLITALGO =&gt; <span class="hljs-string">'HexStringSplit'</span>}
  hbase&gt; <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>, {NUMREGIONS =&gt; <span class="hljs-number">15</span>, SPLITALGO =&gt; <span class="hljs-string">'HexStringSplit'</span>, REGION_REPLICATION =&gt; <span class="hljs-number">2</span>, CONFIGURATION =&gt; {<span class="hljs-string">'hbase.hregion.scan.loadColumnFamiliesOnDemand'</span> =&gt; <span class="hljs-string">'true'</span>}}

You can also keep around a reference <span class="hljs-keyword">to</span> the created <span class="hljs-keyword">table</span>:

  hbase&gt; t1 = <span class="hljs-keyword">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>

Which gives you a reference <span class="hljs-keyword">to</span> the <span class="hljs-keyword">table</span> named <span class="hljs-string">'t1'</span>, <span class="hljs-keyword">on</span> which you can <span class="hljs-keyword">then</span>
<span class="hljs-keyword">call</span> methods.</span></code></pre>

<p>语法十分的详细，我们下面开始创建一张表，表明为zhangshk：tb9,    columnfamily为info</p>



<pre class="prettyprint"><code class=" hljs http">

<span class="sql">hbase(main):014:0* <span class="hljs-operator"><span class="hljs-keyword">create</span> <span class="hljs-string">'zhangshk:tb9'</span>,<span class="hljs-string">'info'</span>
<span class="hljs-number">0</span> <span class="hljs-keyword">row</span>(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.4140</span> seconds

=&gt; Hbase::<span class="hljs-keyword">Table</span> - zhangshk:tb9

验证表是否存在
hbase(main):<span class="hljs-number">019</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">exists</span> <span class="hljs-string">'zhangshk:tb9'</span>
<span class="hljs-keyword">Table</span> zhangshk:tb9 does exist                                                                         
<span class="hljs-number">0</span> <span class="hljs-keyword">row</span>(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0200</span> seconds
</span></span></code></pre>

<p>对应的JAVA API为：</p>



<pre class="prettyprint"><code class=" hljs java">注意：hbase shell创建表的时候必须至少需要制定column family 而java api可以不需要指定，只创建一个空表。
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        HBaseAdmin hBaseAdmin = <span class="hljs-keyword">new</span> HBaseAdmin(conf);
        createTable(hBaseAdmin,<span class="hljs-string">"zhangshk:tb10"</span>);

    }
    <span class="hljs-javadoc">/**
     * 创建一张hbase表，并返回表名
     *
     *<span class="hljs-javadoctag"> @param</span> tableName
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">createTable</span>(HBaseAdmin hBaseAdmin,String tableName)<span class="hljs-keyword">throws</span> Exception{

        <span class="hljs-keyword">if</span> (!hBaseAdmin.tableExists(tableName)){
            hBaseAdmin.createTable(<span class="hljs-keyword">new</span> HTableDescriptor(tableName));
        }
        <span class="hljs-keyword">return</span> tableName;
    }</code></pre>

<p>我们往刚刚创建的表里面put一下数据：下面查一下put的用法</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">034</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">create</span> <span class="hljs-string">'zhangshk:tb11'</span>

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

You can also keep around <span class="hljs-operator">a</span> reference <span class="hljs-built_in">to</span> <span class="hljs-operator">the</span> created table:

  hbase&gt; t1 = <span class="hljs-built_in">create</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'f1'</span>

Which gives you <span class="hljs-operator">a</span> reference <span class="hljs-built_in">to</span> <span class="hljs-operator">the</span> table named <span class="hljs-string">'t1'</span>, <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">which</span> <span class="hljs-title">you</span> <span class="hljs-title">can</span> <span class="hljs-title">then</span></span>
call methods.


hbase(main):<span class="hljs-number">035</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span>

ERROR: wrong <span class="hljs-built_in">number</span> <span class="hljs-operator">of</span> arguments (<span class="hljs-number">0</span> <span class="hljs-keyword">for</span> <span class="hljs-number">4</span>)

Here is some help <span class="hljs-keyword">for</span> this <span class="hljs-command"><span class="hljs-keyword">command</span>:</span>
Put <span class="hljs-operator">a</span> cell <span class="hljs-string">'value'</span> <span class="hljs-keyword">at</span> specified table/row/column <span class="hljs-operator">and</span> optionally
timestamp coordinates.  To <span class="hljs-built_in">put</span> <span class="hljs-operator">a</span> cell <span class="hljs-built_in">value</span> <span class="hljs-keyword">into</span> table <span class="hljs-string">'ns1:t1'</span> <span class="hljs-operator">or</span> <span class="hljs-string">'t1'</span>
<span class="hljs-keyword">at</span> row <span class="hljs-string">'r1'</span> under column <span class="hljs-string">'c1'</span> marked <span class="hljs-operator">with</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">time</span> <span class="hljs-string">'ts1'</span>, <span class="hljs-built_in">do</span>:

  hbase&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'ns1:t1'</span>, <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'value'</span>
  hbase&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'value'</span>
  hbase&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'value'</span>, ts1
  hbase&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'value'</span>, {ATTRIBUTES=&gt;{<span class="hljs-string">'mykey'</span>=&gt;<span class="hljs-string">'myvalue'</span>}}
  hbase&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'value'</span>, ts1, {ATTRIBUTES=&gt;{<span class="hljs-string">'mykey'</span>=&gt;<span class="hljs-string">'myvalue'</span>}}
  hbase&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'value'</span>, ts1, {VISIBILITY=&gt;<span class="hljs-string">'PRIVATE|SECRET'</span>}

The same commands also can be run <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">a</span> <span class="hljs-title">table</span> <span class="hljs-title">reference</span>. <span class="hljs-title">Suppose</span> <span class="hljs-title">you</span> <span class="hljs-title">had</span> <span class="hljs-title">a</span> <span class="hljs-title">reference</span></span>
t <span class="hljs-built_in">to</span> table <span class="hljs-string">'t1'</span>, <span class="hljs-operator">the</span> corresponding <span class="hljs-command"><span class="hljs-keyword">command</span> <span class="hljs-title">would</span> <span class="hljs-title">be</span>:</span>

  hbase&gt; t.<span class="hljs-built_in">put</span> <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'value'</span>, ts1, {ATTRIBUTES=&gt;{<span class="hljs-string">'mykey'</span>=&gt;<span class="hljs-string">'myvalue'</span>}}
</code></pre>

<p>语法十分的详细，我们下面开始给zhangshk：tb9表，put一些数据。</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">
hbase(main):<span class="hljs-number">038</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'zhangshk:tb9'</span>,<span class="hljs-string">'10002'</span>,<span class="hljs-string">'info:age'</span>,<span class="hljs-string">'22'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0170</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">039</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'zhangshk:tb9'</span>,<span class="hljs-string">'10003'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'zhangshk'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0130</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">040</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'zhangshk:tb9'</span>,<span class="hljs-string">'10002'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'zhangshk'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0120</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">041</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'zhangshk:tb9'</span>,<span class="hljs-string">'10002'</span>,<span class="hljs-string">'info:sex'</span>,<span class="hljs-string">'male'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0120</span> <span class="hljs-built_in">seconds</span>

查看执行结果：
hbase(main):<span class="hljs-number">043</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'zhangshk:tb9'</span>
ROW                        COLUMN+CELL                                                                 
 <span class="hljs-number">10001</span>                     column=info:age, timestamp=<span class="hljs-number">1513486347992</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">10</span>                          
 <span class="hljs-number">10002</span>                     column=info:age, timestamp=<span class="hljs-number">1513487143038</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                          
 <span class="hljs-number">10002</span>                     column=info:name, timestamp=<span class="hljs-number">1513487176361</span>, <span class="hljs-built_in">value</span>=zhangshk                   
 <span class="hljs-number">10002</span>                     column=info:sex, timestamp=<span class="hljs-number">1513487191856</span>, <span class="hljs-built_in">value</span>=male                        
 <span class="hljs-number">10003</span>                     column=info:name, timestamp=<span class="hljs-number">1513487165412</span>, <span class="hljs-built_in">value</span>=zhangshk                   
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0130</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p>如果用java API  put数据的方式为：</p>



<pre class="prettyprint"><code class=" hljs java"> <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        HBaseAdmin hBaseAdmin = <span class="hljs-keyword">new</span> HBaseAdmin(conf);
        <span class="hljs-comment">//createTable(hBaseAdmin,"zhangshk:tb11");</span>
        HTable hTable = <span class="hljs-keyword">new</span> HTable(conf,<span class="hljs-string">"zhangshk:tb9"</span>);
        putData(hTable);

    }

    <span class="hljs-javadoc">/**
     * 插入数据到hbase表中
     *<span class="hljs-javadoctag"> @param</span> htable
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">putData</span>(HTable htable) <span class="hljs-keyword">throws</span> Exception{
        Put put = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(<span class="hljs-string">"10003"</span>));
        put.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>),Bytes.toBytes(<span class="hljs-string">"age"</span>),Bytes.toBytes(<span class="hljs-string">"18"</span>));
        htable.put(put);
    }
查看执行结果：
hbase(main):<span class="hljs-number">047</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'zhangshk:tb9'</span>
ROW                        COLUMN+CELL                                                                 
 <span class="hljs-number">10001</span>                     column=info:age, timestamp=<span class="hljs-number">1513486347992</span>, value=<span class="hljs-number">10</span>                          
 <span class="hljs-number">10002</span>                     column=info:age, timestamp=<span class="hljs-number">1513487143038</span>, value=<span class="hljs-number">22</span>                          
 <span class="hljs-number">10002</span>                     column=info:name, timestamp=<span class="hljs-number">1513487176361</span>, value=zhangshk                   
 <span class="hljs-number">10002</span>                     column=info:sex, timestamp=<span class="hljs-number">1513487191856</span>, value=male                        
 <span class="hljs-number">10003</span>                     column=info:age, timestamp=<span class="hljs-number">1513491759194</span>, value=<span class="hljs-number">18</span>                          
 <span class="hljs-number">10003</span>                     column=info:name, timestamp=<span class="hljs-number">1513491742964</span>, value=zhangshk                   
<span class="hljs-number">3</span> row(s) in <span class="hljs-number">0.0180</span> seconds
</code></pre>

<p>scan 和get都可以查询数据，scan是全表扫描或者范围扫描，  所以我们一般不会用这种方式查询数据，而是用get的方式，通过添加条件，这样查询就比较高效。</p>

<p>先来看看scan的hbase shell 使用说明：</p>

<pre class="prettyprint"><code class=" hljs oxygene">hbase(main):<span class="hljs-number">048</span>:<span class="hljs-number">0</span>&gt; scan

Here <span class="hljs-keyword">is</span> some help <span class="hljs-keyword">for</span> this command:
Scan a table; pass table name <span class="hljs-keyword">and</span> optionally a dictionary <span class="hljs-keyword">of</span> scanner
specifications.  Scanner specifications may include one <span class="hljs-keyword">or</span> more <span class="hljs-keyword">of</span>:
TIMERANGE, FILTER, LIMIT, STARTROW, STOPROW, TIMESTAMP, MAXLENGTH,
<span class="hljs-keyword">or</span> COLUMNS, CACHE <span class="hljs-keyword">or</span> RAW, VERSIONS

<span class="hljs-keyword">If</span> no columns are specified, all columns will be scanned.
<span class="hljs-keyword">To</span> scan all members <span class="hljs-keyword">of</span> a column family, leave the qualifier <span class="hljs-keyword">empty</span> <span class="hljs-keyword">as</span> <span class="hljs-keyword">in</span>
<span class="hljs-string">'col_family:'</span>.

The filter can be specified <span class="hljs-keyword">in</span> two ways:
<span class="hljs-number">1</span>. <span class="hljs-keyword">Using</span> a filterString - more information <span class="hljs-keyword">on</span> this <span class="hljs-keyword">is</span> available <span class="hljs-keyword">in</span> the
Filter Language document attached <span class="hljs-keyword">to</span> the HBASE-<span class="hljs-number">4176</span> JIRA
<span class="hljs-number">2</span>. <span class="hljs-keyword">Using</span> the entire package name <span class="hljs-keyword">of</span> the filter.

Some examples:

  hbase&gt; scan <span class="hljs-string">'hbase:meta'</span>
  hbase&gt; scan <span class="hljs-string">'hbase:meta'</span>, <span class="hljs-comment">{COLUMNS =&gt; 'info:regioninfo'}</span>
  hbase&gt; scan <span class="hljs-string">'ns1:t1'</span>, <span class="hljs-comment">{COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; 'xyz'}</span>
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{COLUMNS =&gt; ['c1', 'c2'], LIMIT =&gt; 10, STARTROW =&gt; 'xyz'}</span>
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{COLUMNS =&gt; 'c1', TIMERANGE =&gt; [1303668804, 1303668904]}</span>
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{REVERSED =&gt; true}</span>
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{FILTER =&gt; "(PrefixFilter ('row2') AND
    (QualifierFilter (&gt;=, 'binary:xyz'))) AND (TimestampsFilter ( 123, 456))"}</span>
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{FILTER =&gt;
    org.apache.hadoop.hbase.filter.ColumnPaginationFilter.new(1, 0)}</span>
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{CONSISTENCY =&gt; 'TIMELINE'}</span>
<span class="hljs-keyword">For</span> setting the Operation Attributes 
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{ COLUMNS =&gt; ['c1', 'c2'], ATTRIBUTES =&gt; {'mykey' =&gt; 'myvalue'}</span>}
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{ COLUMNS =&gt; ['c1', 'c2'], AUTHORIZATIONS =&gt; ['PRIVATE','SECRET']}</span>
<span class="hljs-keyword">For</span> experts, there <span class="hljs-keyword">is</span> an additional option -- CACHE_BLOCKS -- which
switches <span class="hljs-keyword">block</span> caching <span class="hljs-keyword">for</span> the scanner <span class="hljs-keyword">on</span> (<span class="hljs-keyword">true</span>) <span class="hljs-keyword">or</span> off (<span class="hljs-keyword">false</span>).  <span class="hljs-keyword">By</span>
<span class="hljs-keyword">default</span> it <span class="hljs-keyword">is</span> enabled.  Examples:

  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{COLUMNS =&gt; ['c1', 'c2'], CACHE_BLOCKS =&gt; false}</span>

Also <span class="hljs-keyword">for</span> experts, there <span class="hljs-keyword">is</span> an advanced option -- RAW -- which instructs the
scanner <span class="hljs-keyword">to</span> return all cells (including delete markers <span class="hljs-keyword">and</span> uncollected deleted
cells). This option cannot be combined <span class="hljs-keyword">with</span> requesting specific COLUMNS.
Disabled <span class="hljs-keyword">by</span> <span class="hljs-keyword">default</span>.  Example:

  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{RAW =&gt; true, VERSIONS =&gt; 10}</span>

Besides the <span class="hljs-keyword">default</span> <span class="hljs-string">'toStringBinary'</span> format, <span class="hljs-string">'scan'</span> supports custom formatting
<span class="hljs-keyword">by</span> column.  A user can define a FORMATTER <span class="hljs-keyword">by</span> adding it <span class="hljs-keyword">to</span> the column name <span class="hljs-keyword">in</span>
the scan specification.  The FORMATTER can be stipulated: 

 <span class="hljs-number">1</span>. either <span class="hljs-keyword">as</span> a org.apache.hadoop.hbase.util.Bytes <span class="hljs-function"><span class="hljs-keyword">method</span> <span class="hljs-title">name</span> <span class="hljs-params">(e.g, toInt, toString)</span>
 2. <span class="hljs-title">or</span> <span class="hljs-title">as</span> <span class="hljs-title">a</span> <span class="hljs-title">custom</span> <span class="hljs-title">class</span> <span class="hljs-title">followed</span> <span class="hljs-title">by</span> <span class="hljs-title">method</span> <span class="hljs-title">name</span>:</span> e.g. <span class="hljs-string">'c(MyFormatterClass).format'</span>.

Example formatting cf:qualifier1 <span class="hljs-keyword">and</span> cf:qualifier2 both <span class="hljs-keyword">as</span> Integers: 
  hbase&gt; scan <span class="hljs-string">'t1'</span>, <span class="hljs-comment">{COLUMNS =&gt; ['cf:qualifier1:toInt',
    'cf:qualifier2:c(org.apache.hadoop.hbase.util.Bytes).toInt'] }</span> 

Note that you can specify a FORMATTER <span class="hljs-keyword">by</span> column only (cf:qualifier).  You cannot
specify a FORMATTER <span class="hljs-keyword">for</span> all columns <span class="hljs-keyword">of</span> a column family.

Scan can also be used directly <span class="hljs-keyword">from</span> a table, <span class="hljs-keyword">by</span> first getting a <span class="hljs-keyword">reference</span> <span class="hljs-keyword">to</span> a
table, like such:

  hbase&gt; t = get_table <span class="hljs-string">'t'</span>
  hbase&gt; t.scan

Note <span class="hljs-keyword">in</span> the above situation, you can still provide all the filtering, columns,
options, etc <span class="hljs-keyword">as</span> described above.
</code></pre>

<p>使用尝试：</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">051</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'zhangshk:tb9'</span>
ROW                        COLUMN+CELL                                                                 
 <span class="hljs-number">10001</span>                     column=info:age, timestamp=<span class="hljs-number">1513486347992</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">10</span>                          
 <span class="hljs-number">10002</span>                     column=info:age, timestamp=<span class="hljs-number">1513487143038</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                          
 <span class="hljs-number">10002</span>                     column=info:name, timestamp=<span class="hljs-number">1513487176361</span>, <span class="hljs-built_in">value</span>=zhangshk                   
 <span class="hljs-number">10002</span>                     column=info:sex, timestamp=<span class="hljs-number">1513487191856</span>, <span class="hljs-built_in">value</span>=male                        
 <span class="hljs-number">10003</span>                     column=info:age, timestamp=<span class="hljs-number">1513491759194</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">18</span>                          
 <span class="hljs-number">10003</span>                     column=info:name, timestamp=<span class="hljs-number">1513491742964</span>, <span class="hljs-built_in">value</span>=zhangshk                   
<span class="hljs-number">3</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0170</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">054</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'zhangshk:tb9'</span>,{LIMIT=&gt;<span class="hljs-number">2</span>}
ROW                        COLUMN+CELL                                                                 
 <span class="hljs-number">10001</span>                     column=info:age, timestamp=<span class="hljs-number">1513486347992</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">10</span>                          
 <span class="hljs-number">10002</span>                     column=info:age, timestamp=<span class="hljs-number">1513487143038</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                          
 <span class="hljs-number">10002</span>                     column=info:name, timestamp=<span class="hljs-number">1513487176361</span>, <span class="hljs-built_in">value</span>=zhangshk                   
 <span class="hljs-number">10002</span>                     column=info:sex, timestamp=<span class="hljs-number">1513487191856</span>, <span class="hljs-built_in">value</span>=male                        
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0140</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">055</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'zhangshk:tb9'</span>,{LIMIT=&gt;<span class="hljs-number">2</span>,STARTROW=&gt;<span class="hljs-string">'10002'</span>}
ROW                        COLUMN+CELL                                                                 
 <span class="hljs-number">10002</span>                     column=info:age, timestamp=<span class="hljs-number">1513487143038</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                          
 <span class="hljs-number">10002</span>                     column=info:name, timestamp=<span class="hljs-number">1513487176361</span>, <span class="hljs-built_in">value</span>=zhangshk                   
 <span class="hljs-number">10002</span>                     column=info:sex, timestamp=<span class="hljs-number">1513487191856</span>, <span class="hljs-built_in">value</span>=male                        
 <span class="hljs-number">10003</span>                     column=info:age, timestamp=<span class="hljs-number">1513491759194</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">18</span>                          
 <span class="hljs-number">10003</span>                     column=info:name, timestamp=<span class="hljs-number">1513491742964</span>, <span class="hljs-built_in">value</span>=zhangshk                   
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0250</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">057</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'zhangshk:tb9'</span>,{LIMIT=&gt;<span class="hljs-number">2</span>,STARTROW=&gt;<span class="hljs-string">'10002'</span>,COLUMNS=&gt;<span class="hljs-string">'info:age'</span>}
ROW                        COLUMN+CELL                                                                 
 <span class="hljs-number">10002</span>                     column=info:age, timestamp=<span class="hljs-number">1513487143038</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                          
 <span class="hljs-number">10003</span>                     column=info:age, timestamp=<span class="hljs-number">1513491759194</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">18</span>                          
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0300</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">058</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'zhangshk:tb9'</span>,{LIMIT=&gt;<span class="hljs-number">2</span>,STARTROW=&gt;<span class="hljs-string">'10002'</span>,STOPROW=&gt;<span class="hljs-string">'10003'</span>,COLUMNS=&gt;<span class="hljs-string">'info:age'</span>}
ROW                        COLUMN+CELL                                                                 
 <span class="hljs-number">10002</span>                     column=info:age, timestamp=<span class="hljs-number">1513487143038</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">22</span>                          
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0200</span> <span class="hljs-built_in">seconds</span>


</code></pre>

<p>java API操作</p>



<pre class="prettyprint"><code class=" hljs java">
 <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        HBaseAdmin hBaseAdmin = <span class="hljs-keyword">new</span> HBaseAdmin(conf);
        <span class="hljs-comment">//createTable(hBaseAdmin,"zhangshk:tb11");</span>
        HTable hTable = <span class="hljs-keyword">new</span> HTable(conf,<span class="hljs-string">"zhangshk:tb9"</span>);
        <span class="hljs-comment">//putData(hTable);</span>
        scanTable(hTable);

    }
<span class="hljs-javadoc">/**

     * 通过scan ，全表扫描数据
     *<span class="hljs-javadoctag"> @param</span> hTable
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">scanTable</span>(HTable hTable)<span class="hljs-keyword">throws</span> Exception{

        Scan scan = <span class="hljs-keyword">new</span> Scan();
        scan.setStartRow(Bytes.toBytes(<span class="hljs-string">"10002"</span>)).setStopRow(Bytes.toBytes(<span class="hljs-string">"10003"</span>)).addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>),Bytes.toBytes(<span class="hljs-string">"age"</span>));
        ResultScanner scanner = hTable.getScanner(scan);
        Result results = scanner.next();
        <span class="hljs-keyword">for</span> (Cell cell:
             results.rawCells()) {

            System.out.println(
                    Bytes.toString(CellUtil.cloneFamily(cell)) + <span class="hljs-string">"-&gt;"</span> + Bytes.toString(CellUtil.cloneQualifier(cell))
                            + <span class="hljs-string">"-&gt;"</span> + Bytes.toString(CellUtil.cloneValue(cell)) + <span class="hljs-string">"-&gt;"</span> + cell.getTimestamp());

        }

    }

结果为：
info-&gt;age-&gt;<span class="hljs-number">22</span>-&gt;<span class="hljs-number">1513487143038</span>

和hbase shell 查询结果一致。</code></pre>

<p>下面看看get查询： <br>
先看看帮助文档：</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">059</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span>

Here is some help <span class="hljs-keyword">for</span> this <span class="hljs-command"><span class="hljs-keyword">command</span>:</span>
Get row <span class="hljs-operator">or</span> cell contents; pass table name, row, <span class="hljs-operator">and</span> optionally
<span class="hljs-operator">a</span> dictionary <span class="hljs-operator">of</span> column(s), timestamp, timerange <span class="hljs-operator">and</span> versions. Examples:

  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'ns1:t1'</span>, <span class="hljs-string">'r1'</span>
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {TIMERANGE =&gt; [ts1, ts2]}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {COLUMN =&gt; [<span class="hljs-string">'c1'</span>, <span class="hljs-string">'c2'</span>, <span class="hljs-string">'c3'</span>]}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>, TIMESTAMP =&gt; ts1}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>, TIMERANGE =&gt; [ts1, ts2], VERSIONS =&gt; <span class="hljs-number">4</span>}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>, TIMESTAMP =&gt; ts1, VERSIONS =&gt; <span class="hljs-number">4</span>}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {FILTER =&gt; <span class="hljs-string">"ValueFilter(=, 'binary:abc')"</span>}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'c2'</span>
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, [<span class="hljs-string">'c1'</span>, <span class="hljs-string">'c2'</span>]
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>, ATTRIBUTES =&gt; {<span class="hljs-string">'mykey'</span>=&gt;<span class="hljs-string">'myvalue'</span>}}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>, AUTHORIZATIONS =&gt; [<span class="hljs-string">'PRIVATE'</span>,<span class="hljs-string">'SECRET'</span>]}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {CONSISTENCY =&gt; <span class="hljs-string">'TIMELINE'</span>}
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span>, {CONSISTENCY =&gt; <span class="hljs-string">'TIMELINE'</span>, REGION_REPLICA_ID =&gt; <span class="hljs-number">1</span>}

Besides <span class="hljs-operator">the</span> default <span class="hljs-string">'toStringBinary'</span> <span class="hljs-built_in">format</span>, <span class="hljs-string">'get'</span> also supports custom formatting <span class="hljs-keyword">by</span>
column.  A user can define <span class="hljs-operator">a</span> FORMATTER <span class="hljs-keyword">by</span> adding <span class="hljs-keyword">it</span> <span class="hljs-built_in">to</span> <span class="hljs-operator">the</span> column name <span class="hljs-operator">in</span> <span class="hljs-operator">the</span> <span class="hljs-built_in">get</span>
specification.  The FORMATTER can be stipulated: 

 <span class="hljs-number">1.</span> either <span class="hljs-keyword">as</span> <span class="hljs-operator">a</span> org.apache.hadoop.hbase.util.Bytes method name (e.g, toInt, toString)
 <span class="hljs-number">2.</span> <span class="hljs-operator">or</span> <span class="hljs-keyword">as</span> <span class="hljs-operator">a</span> custom class followed <span class="hljs-keyword">by</span> method name: e.g. <span class="hljs-string">'c(MyFormatterClass).format'</span>.

Example formatting cf:qualifier1 <span class="hljs-operator">and</span> cf:qualifier2 both <span class="hljs-keyword">as</span> Integers: 
  hbase&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'t1'</span>, <span class="hljs-string">'r1'</span> {COLUMN =&gt; [<span class="hljs-string">'cf:qualifier1:toInt'</span>,
    <span class="hljs-string">'cf:qualifier2:c(org.apache.hadoop.hbase.util.Bytes).toInt'</span>] } 

Note that you can specify <span class="hljs-operator">a</span> FORMATTER <span class="hljs-keyword">by</span> column only (cf:qualifier).  You cannot specify
<span class="hljs-operator">a</span> FORMATTER <span class="hljs-keyword">for</span> all columns <span class="hljs-operator">of</span> <span class="hljs-operator">a</span> column family.

The same commands also can be run <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">a</span> <span class="hljs-title">reference</span> <span class="hljs-title">to</span> <span class="hljs-title">a</span> <span class="hljs-title">table</span> (<span class="hljs-title">obtained</span> <span class="hljs-title">via</span> <span class="hljs-title">get_table</span> <span class="hljs-title">or</span></span>
create_table). Suppose you had <span class="hljs-operator">a</span> reference t <span class="hljs-built_in">to</span> table <span class="hljs-string">'t1'</span>, <span class="hljs-operator">the</span> corresponding commands
would be:

  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {TIMERANGE =&gt; [ts1, ts2]}
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>}
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {COLUMN =&gt; [<span class="hljs-string">'c1'</span>, <span class="hljs-string">'c2'</span>, <span class="hljs-string">'c3'</span>]}
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>, TIMESTAMP =&gt; ts1}
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>, TIMERANGE =&gt; [ts1, ts2], VERSIONS =&gt; <span class="hljs-number">4</span>}
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {COLUMN =&gt; <span class="hljs-string">'c1'</span>, TIMESTAMP =&gt; ts1, VERSIONS =&gt; <span class="hljs-number">4</span>}
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {FILTER =&gt; <span class="hljs-string">"ValueFilter(=, 'binary:abc')"</span>}
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, <span class="hljs-string">'c1'</span>, <span class="hljs-string">'c2'</span>
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, [<span class="hljs-string">'c1'</span>, <span class="hljs-string">'c2'</span>]
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {CONSISTENCY =&gt; <span class="hljs-string">'TIMELINE'</span>}
  hbase&gt; t.<span class="hljs-built_in">get</span> <span class="hljs-string">'r1'</span>, {CONSISTENCY =&gt; <span class="hljs-string">'TIMELINE'</span>, REGION_REPLICA_ID =&gt; <span class="hljs-number">1</span>}

</code></pre>

<p>hbase shell:</p>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">063</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'zhangshk:tb9'</span>,<span class="hljs-string">'10003'</span>,{COLUMN=&gt;<span class="hljs-string">'info:age'</span>}
COLUMN                     CELL                                                                        
 info:age                  timestamp=<span class="hljs-number">1513491759194</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">18</span>                                           
<span class="hljs-number">1</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0080</span> <span class="hljs-built_in">seconds</span>
</code></pre>

<p>java API:</p>



<pre class="prettyprint"><code class=" hljs cs"> <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) throws Exception {
        HBaseAdmin hBaseAdmin = <span class="hljs-keyword">new</span> HBaseAdmin(conf);
        <span class="hljs-comment">//createTable(hBaseAdmin,"zhangshk:tb11");</span>
        HTable hTable = <span class="hljs-keyword">new</span> HTable(conf,<span class="hljs-string">"zhangshk:tb9"</span>);
        <span class="hljs-comment">//putData(hTable);</span>
        <span class="hljs-comment">//scanTable(hTable);</span>
        getData(hTable);

    }
<span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getData</span>(HTable hTable) throws  Exception{
        Get <span class="hljs-keyword">get</span> = <span class="hljs-keyword">new</span> Get(Bytes.toBytes(<span class="hljs-string">"10003"</span>));
        <span class="hljs-keyword">get</span>.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>),Bytes.toBytes(<span class="hljs-string">"age"</span>));
        Result result = hTable.<span class="hljs-keyword">get</span>(<span class="hljs-keyword">get</span>);
        Cell[] cells = result.rawCells();
        <span class="hljs-keyword">for</span> (Cell cell:
             cells) {
            System.<span class="hljs-keyword">out</span>.println(Bytes.toString(CellUtil.cloneRow(cell))+<span class="hljs-string">"-&gt;"</span>+
                    Bytes.toString(CellUtil.cloneFamily(cell))+<span class="hljs-string">"-&gt;"</span>+Bytes.toString(CellUtil.cloneQualifier(cell))+<span class="hljs-string">"-&gt;"</span>+Bytes.toString(CellUtil.cloneValue(cell))
            );
        }
    }
查询结果为：
<span class="hljs-number">10003</span>-&gt;info-&gt;age-&gt;<span class="hljs-number">18</span>

结果和hbase shell一样
</code></pre>

<p>update ，在hbase中 可以使用put来实现，</p>

<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">064</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">put</span> <span class="hljs-string">'zhangshk:tb9'</span>,<span class="hljs-string">'10003'</span>,<span class="hljs-string">'info:name'</span>,<span class="hljs-string">'zhangshk_update'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0160</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">065</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">get</span> <span class="hljs-string">'zhangshk:tb9'</span>,<span class="hljs-string">'10003'</span>
COLUMN                     CELL                                                                        
 info:age                  timestamp=<span class="hljs-number">1513491759194</span>, <span class="hljs-built_in">value</span>=<span class="hljs-number">18</span>                                           
 info:name                 timestamp=<span class="hljs-number">1513493686439</span>, <span class="hljs-built_in">value</span>=zhangshk_update                              
<span class="hljs-number">2</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.0090</span> <span class="hljs-built_in">seconds</span>

</code></pre>

<p>DELETE ，最后讲讲删除操作。</p>

<p>delete有 两个命令 ，一个是delete ，一个是deleteall 我们先来看看delete：</p>



<pre class="prettyprint"><code class=" hljs oxygene">hbase(main):<span class="hljs-number">068</span>:<span class="hljs-number">0</span>&gt; delete

Here <span class="hljs-keyword">is</span> some help <span class="hljs-keyword">for</span> this command:
Put a delete cell value at specified table/row/column <span class="hljs-keyword">and</span> optionally
timestamp coordinates.  Deletes must match the deleted cell<span class="hljs-string">'s
coordinates exactly.  When scanning, a delete cell suppresses older
versions. To delete a cell from  '</span>t1<span class="hljs-string">' at row '</span>r1<span class="hljs-string">' under column '</span>c1<span class="hljs-string">'
marked with the time '</span>ts1<span class="hljs-string">', do:

  hbase&gt; delete '</span>ns1:t1<span class="hljs-string">', '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">', ts1
  hbase&gt; delete '</span>t1<span class="hljs-string">', '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">', ts1
  hbase&gt; delete '</span>t1<span class="hljs-string">', '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">', ts1, {VISIBILITY=&gt;'</span><span class="hljs-keyword">PRIVATE</span>|SECRET<span class="hljs-string">'}

The same command can also be run on a table reference. Suppose you had a reference
t to table '</span>t1<span class="hljs-string">', the corresponding command would be:

  hbase&gt; t.delete '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">',  ts1
  hbase&gt; t.delete '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">',  ts1, {VISIBILITY=&gt;'</span><span class="hljs-keyword">PRIVATE</span>|SECRET<span class="hljs-string">'}

我们可以看到delete 的时候必须要指定timestamp，但是一般来说我们都不知道timestamp
所以我们来看看deleteall
hbase(main):071:0&gt; deleteall

Here is some help for this command:
Delete all cells in a given row; pass a table name, row, and optionally
a column and timestamp. Examples:

  hbase&gt; deleteall '</span>ns1:t1<span class="hljs-string">', '</span>r1<span class="hljs-string">'
  hbase&gt; deleteall '</span>t1<span class="hljs-string">', '</span>r1<span class="hljs-string">'
  hbase&gt; deleteall '</span>t1<span class="hljs-string">', '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">'
  hbase&gt; deleteall '</span>t1<span class="hljs-string">', '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">', ts1
  hbase&gt; deleteall '</span>t1<span class="hljs-string">', '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">', ts1, {VISIBILITY=&gt;'</span><span class="hljs-keyword">PRIVATE</span>|SECRET<span class="hljs-string">'}

The same commands also can be run on a table reference. Suppose you had a reference
t to table '</span>t1<span class="hljs-string">', the corresponding command would be:

  hbase&gt; t.deleteall '</span>r1<span class="hljs-string">'
  hbase&gt; t.deleteall '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">'
  hbase&gt; t.deleteall '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">', ts1
  hbase&gt; t.deleteall '</span>r1<span class="hljs-string">', '</span>c1<span class="hljs-string">', ts1, {VISIBILITY=&gt;'</span><span class="hljs-keyword">PRIVATE</span>|SECRET<span class="hljs-string">'}

deleteall就可以多种方式删除了。

hbase(main):073:0&gt; deleteall '</span>zhangshk:tb9<span class="hljs-string">','</span><span class="hljs-number">10003</span><span class="hljs-string">'
0 row(s) in 0.0320 seconds

hbase(main):074:0&gt; scan '</span>zhangshk:tb9<span class="hljs-string">'
ROW                        COLUMN+CELL                                                                 
 10001                     column=info:age, timestamp=1513486347992, value=10                          
 10002                     column=info:age, timestamp=1513487143038, value=22                          
 10002                     column=info:name, timestamp=1513487176361, value=zhangshk                   
 10002                     column=info:sex, timestamp=1513487191856, value=male                        
2 row(s) in 0.0130 seconds
</span></code></pre>

<p>java API中就没有delete 和deleteall 的概念 ，只有一个delete ，应该是把他们统一起来了。</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteData</span>(HTable hTable) throws Exception{
        Delete delete = <span class="hljs-keyword">new</span> Delete(Bytes.toBytes(<span class="hljs-string">"10002"</span>));
        hTable.delete(delete);
    }

执行结果：
hbase(main):<span class="hljs-number">075</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'zhangshk:tb9'</span>
ROW                        COLUMN+CELL                                                                 
 <span class="hljs-number">10001</span>                     column=info:age, timestamp=<span class="hljs-number">1513486347992</span>, <span class="hljs-keyword">value</span>=<span class="hljs-number">10</span>                          
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0190</span> seconds
</code></pre>

<p>我们只讲了删除了 ，那么要做就做的不留痕迹，我们只是删除了数据，那怎么删除表呢，对于hbase中的表我们需要先disable表，之后再执行删除操作,删除我们使用的是drop关键字，用来删除整张表。</p>

<p>看一下disable的使用说明：</p>

<pre class="prettyprint"><code class=" hljs mel">hbase(main):<span class="hljs-number">076</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-keyword">disable</span>

Here is some <span class="hljs-keyword">help</span> <span class="hljs-keyword">for</span> this command:
Start <span class="hljs-keyword">disable</span> of named table:
  hbase&gt; <span class="hljs-keyword">disable</span> <span class="hljs-string">'t1'</span>
  hbase&gt; <span class="hljs-keyword">disable</span> <span class="hljs-string">'ns1:t1'</span>
</code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">hbase(main):<span class="hljs-number">077</span>:<span class="hljs-number">0</span>&gt; disable <span class="hljs-string">'zhangshk:tb9'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">1.2640</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">080</span>:<span class="hljs-number">0</span>&gt; drop <span class="hljs-string">'zhangshk:tb9'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-operator">in</span> <span class="hljs-number">0.3400</span> <span class="hljs-built_in">seconds</span>

hbase(main):<span class="hljs-number">081</span>:<span class="hljs-number">0</span>&gt; describe <span class="hljs-string">'zhangshk:tb9'</span>

ERROR: Unknown table zhangshk:tb9!

表已经不存在了。

</code></pre>

<p>java API 删除表：</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/**
     * 删除表
     *<span class="hljs-javadoctag"> @param</span> hBaseAdmin
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span>  <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteTable</span>(HBaseAdmin hBaseAdmin) <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-keyword">if</span>(hBaseAdmin.tableExists(<span class="hljs-string">"zhangshk:tb9"</span>)){
            hBaseAdmin.disableTable(<span class="hljs-string">"zhangshk:tb9"</span>);
            hBaseAdmin.deleteTable(<span class="hljs-string">"zhangshk:tb9"</span>);
        }

    }</code></pre>

<p>最后关闭shell </p>



<pre class="prettyprint"><code class=" hljs css"><span class="hljs-tag">hbase</span>(<span class="hljs-tag">main</span>)<span class="hljs-pseudo">:084</span><span class="hljs-pseudo">:0</span>* <span class="hljs-tag">exit</span>
<span class="hljs-attr_selector">[zhangshk@fonova-hadoop1 hbaseTest-1.0-SNAPSHOT]</span>$ </code></pre>

<p>停掉hbase 进程</p>



<pre class="prettyprint"><code class=" hljs ruby">[zhangshk<span class="hljs-variable">@fonova</span>-hadoop1 ~]<span class="hljs-variable">$ </span>sh stop-hbase.sh
stopping hbase........</code></pre>

<p>下面是完整的demo代码： <br>
需要说明一下，这里是在公司的完全分布式集群上做的，所以HBaseConfiguration可以直接拿到我的hbase-site.xml中的配置信息，如果你使用的是单机版，或者伪分布式的环境，那么你需要手动指定相关的zk地址等configuration信息。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">package</span> com.zhangshk;

<span class="hljs-keyword">import</span> org.apache.hadoop.conf.Configuration;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.*;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.client.*;
<span class="hljs-keyword">import</span> org.apache.hadoop.hbase.util.Bytes;

<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">TestConnectionToHbase</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> Configuration conf = HBaseConfiguration.create();

    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        HBaseAdmin hBaseAdmin = <span class="hljs-keyword">new</span> HBaseAdmin(conf);
        <span class="hljs-comment">//createTable(hBaseAdmin,"zhangshk:tb11");</span>
        HTable hTable = <span class="hljs-keyword">new</span> HTable(conf,<span class="hljs-string">"zhangshk:tb9"</span>);
        <span class="hljs-comment">//putData(hTable);</span>
        <span class="hljs-comment">//scanTable(hTable);</span>
        <span class="hljs-comment">//getData(hTable);</span>
        deleteData(hTable);

    }

    <span class="hljs-javadoc">/**
     * 创建一张hbase表，并返回表名
     *
     *<span class="hljs-javadoctag"> @param</span> tableName
     *<span class="hljs-javadoctag"> @return</span>
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> String <span class="hljs-title">createTable</span>(HBaseAdmin hBaseAdmin,String tableName)<span class="hljs-keyword">throws</span> Exception{

        <span class="hljs-keyword">if</span> (!hBaseAdmin.tableExists(tableName)){
            hBaseAdmin.createTable(<span class="hljs-keyword">new</span> HTableDescriptor(tableName).addFamily(<span class="hljs-keyword">new</span> HColumnDescriptor(<span class="hljs-string">"info"</span>)));

        }
        <span class="hljs-keyword">return</span> tableName;
    }

    <span class="hljs-javadoc">/**
     * 删除表
     *<span class="hljs-javadoctag"> @param</span> hBaseAdmin
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span>  <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteTable</span>(HBaseAdmin hBaseAdmin) <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-keyword">if</span>(hBaseAdmin.tableExists(<span class="hljs-string">"zhangshk:tb9"</span>)){
            hBaseAdmin.disableTable(<span class="hljs-string">"zhangshk:tb9"</span>);
            hBaseAdmin.deleteTable(<span class="hljs-string">"zhangshk:tb9"</span>);
        }

    }

    <span class="hljs-javadoc">/**
     * 插入数据到hbase表中
     *<span class="hljs-javadoctag"> @param</span> htable
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">putData</span>(HTable htable) <span class="hljs-keyword">throws</span> Exception{
        Put put = <span class="hljs-keyword">new</span> Put(Bytes.toBytes(<span class="hljs-string">"10003"</span>));
        put.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>),Bytes.toBytes(<span class="hljs-string">"age"</span>),Bytes.toBytes(<span class="hljs-string">"18"</span>));
        htable.put(put);
    }

    <span class="hljs-javadoc">/**
     * 通过scan ，全表扫描数据
     *<span class="hljs-javadoctag"> @param</span> hTable
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">scanTable</span>(HTable hTable)<span class="hljs-keyword">throws</span> Exception{

        Scan scan = <span class="hljs-keyword">new</span> Scan();
        scan.setStartRow(Bytes.toBytes(<span class="hljs-string">"10002"</span>)).setStopRow(Bytes.toBytes(<span class="hljs-string">"10003"</span>)).addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>),Bytes.toBytes(<span class="hljs-string">"age"</span>));
        ResultScanner scanner = hTable.getScanner(scan);
        Result results = scanner.next();
        <span class="hljs-keyword">for</span> (Cell cell:
             results.rawCells()) {

            System.out.println(
                    Bytes.toString(CellUtil.cloneFamily(cell)) + <span class="hljs-string">"-&gt;"</span> + Bytes.toString(CellUtil.cloneQualifier(cell))
                            + <span class="hljs-string">"-&gt;"</span> + Bytes.toString(CellUtil.cloneValue(cell)) + <span class="hljs-string">"-&gt;"</span> + cell.getTimestamp());

        }

    }

    <span class="hljs-javadoc">/**
     * get方式获取数据
     *<span class="hljs-javadoctag"> @param</span> hTable
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getData</span>(HTable hTable) <span class="hljs-keyword">throws</span>  Exception{
        Get get = <span class="hljs-keyword">new</span> Get(Bytes.toBytes(<span class="hljs-string">"10003"</span>));
        get.addColumn(Bytes.toBytes(<span class="hljs-string">"info"</span>),Bytes.toBytes(<span class="hljs-string">"age"</span>));
        Result result = hTable.get(get);
        Cell[] cells = result.rawCells();
        <span class="hljs-keyword">for</span> (Cell cell:
             cells) {
            System.out.println(Bytes.toString(CellUtil.cloneRow(cell))+<span class="hljs-string">"-&gt;"</span>+
                    Bytes.toString(CellUtil.cloneFamily(cell))+<span class="hljs-string">"-&gt;"</span>+Bytes.toString(CellUtil.cloneQualifier(cell))+<span class="hljs-string">"-&gt;"</span>+Bytes.toString(CellUtil.cloneValue(cell))
            );
        }
    }

    <span class="hljs-javadoc">/**
     * 删除数据
     *<span class="hljs-javadoctag"> @param</span> hTable
     *<span class="hljs-javadoctag"> @throws</span> Exception
     */</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteData</span>(HTable hTable) <span class="hljs-keyword">throws</span> Exception{
        Delete delete = <span class="hljs-keyword">new</span> Delete(Bytes.toBytes(<span class="hljs-string">"10002"</span>));
        hTable.delete(delete);
    }
}
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
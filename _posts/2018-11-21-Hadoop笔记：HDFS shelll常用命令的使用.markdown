---
layout:     post
title:      Hadoop笔记：HDFS shelll常用命令的使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/japson_iot/article/details/80467485				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="大数据笔记hdfs-shelll常用命令的使用">大数据笔记：HDFS shelll常用命令的使用</h1>

<p>标签： 大数据</p>

<p></p><div class="toc">
<ul>
<li><a href="#%E5%A4%A7%E6%95%B0%E6%8D%AE%E7%AC%94%E8%AE%B0hdfs-shelll%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E7%9A%84%E4%BD%BF%E7%94%A8" rel="nofollow">大数据笔记：HDFS shelll常用命令的使用</a><ul>
<li><a href="#hdfs-shelll%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E7%9A%84%E4%BD%BF%E7%94%A8" rel="nofollow">HDFS shelll常用命令的使用</a></li>
</ul>
</li>
</ul>
</div>


<hr>



<h2 id="hdfs-shelll常用命令的使用">HDFS shelll常用命令的使用</h2>

<p>如何使用hdfs</p>

<p>dfs <br>
不会用，按回车</p>



<pre class="prettyprint"><code class=" hljs applescript">[japson@localhost sbin]$ hdfs
Usage: hdfs [<span class="hljs-comment">--config confdir] COMMAND</span>
       <span class="hljs-keyword">where</span> COMMAND <span class="hljs-keyword">is</span> one <span class="hljs-keyword">of</span>:
  dfs                  <span class="hljs-command">run</span> a filesystem command <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> <span class="hljs-keyword">the</span> <span class="hljs-type">file</span> systems supported <span class="hljs-keyword">in</span> Hadoop.
  namenode -format     format <span class="hljs-keyword">the</span> DFS filesystem
  secondarynamenode    <span class="hljs-command">run</span> <span class="hljs-keyword">the</span> DFS secondary namenode
  namenode             <span class="hljs-command">run</span> <span class="hljs-keyword">the</span> DFS namenode
  journalnode          <span class="hljs-command">run</span> <span class="hljs-keyword">the</span> DFS journalnode
  zkfc                 <span class="hljs-command">run</span> <span class="hljs-keyword">the</span> ZK Failover Controller daemon
  datanode             <span class="hljs-command">run</span> a DFS datanode
  dfsadmin             <span class="hljs-command">run</span> a DFS admin client
  haadmin              <span class="hljs-command">run</span> a DFS HA admin client
  fsck                 <span class="hljs-command">run</span> a DFS filesystem checking utility
  balancer             <span class="hljs-command">run</span> a cluster balancing utility
  jmxget               <span class="hljs-keyword">get</span> JMX exported values <span class="hljs-keyword">from</span> NameNode <span class="hljs-keyword">or</span> DataNode.
  mover                <span class="hljs-command">run</span> a utility <span class="hljs-keyword">to</span> move block replicas across
                       storage types
  oiv                  apply <span class="hljs-keyword">the</span> offline fsimage viewer <span class="hljs-keyword">to</span> an fsimage
  oiv_legacy           apply <span class="hljs-keyword">the</span> offline fsimage viewer <span class="hljs-keyword">to</span> an legacy fsimage
  oev                  apply <span class="hljs-keyword">the</span> offline edits viewer <span class="hljs-keyword">to</span> an edits <span class="hljs-type">file</span>
  fetchdt              fetch a delegation token <span class="hljs-keyword">from</span> <span class="hljs-keyword">the</span> NameNode
  getconf              <span class="hljs-keyword">get</span> config values <span class="hljs-keyword">from</span> configuration
  groups               <span class="hljs-keyword">get</span> <span class="hljs-keyword">the</span> groups which users belong <span class="hljs-keyword">to</span>
  snapshotDiff         diff two snapshots <span class="hljs-keyword">of</span> a directory <span class="hljs-keyword">or</span> diff <span class="hljs-keyword">the</span>
                       current directory <span class="hljs-property">contents</span> <span class="hljs-keyword">with</span> a snapshot
  lsSnapshottableDir   <span class="hljs-type">list</span> all snapshottable dirs owned <span class="hljs-keyword">by</span> <span class="hljs-keyword">the</span> current user
                        Use -help <span class="hljs-keyword">to</span> see options
  portmap              <span class="hljs-command">run</span> a portmap service
  nfs3                 <span class="hljs-command">run</span> an NFS <span class="hljs-property">version</span> <span class="hljs-number">3</span> gateway
  cacheadmin           configure <span class="hljs-keyword">the</span> HDFS cache
  crypto               configure HDFS encryption zones
  storagepolicies      <span class="hljs-type">list</span>/<span class="hljs-keyword">get</span>/<span class="hljs-keyword">set</span> block storage policies
  <span class="hljs-property">version</span>              print <span class="hljs-keyword">the</span> <span class="hljs-property">version</span>

Most commands print help when invoked w/o parameters.</code></pre>

<p>选择dfs命令 <br>
hdfs dfs</p>



<pre class="prettyprint"><code class=" hljs r">[japson@localhost sbin]$ hdfs dfs
Usage: hadoop fs [generic options]
    [-appendToFile &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-cat [-ignoreCrc] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-checksum &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-chgrp [-R] GROUP PATH...]
    [-chmod [-R] &lt;MODE[,MODE]<span class="hljs-keyword">...</span> | OCTALMODE&gt; PATH...]
    [-chown [-R] [OWNER][:[GROUP]] PATH...]
    [-copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;localdst&gt;]
    [-count [-q] [-h] [-v] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-cp [-f] [-p | -p[topax]] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]
    [-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]
    [-df [-h] [&lt;path&gt; <span class="hljs-keyword">...</span>]]
    [-du [-s] [-h] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-expunge]
    [-find &lt;path&gt; <span class="hljs-keyword">...</span> &lt;expression&gt; <span class="hljs-keyword">...</span>]
    [-get [-p] [-ignoreCrc] [-crc] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;localdst&gt;]
    [-getfacl [-R] &lt;path&gt;]
    [-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]
    [-getmerge [-nl] &lt;src&gt; &lt;localdst&gt;]
    [-help [cmd <span class="hljs-keyword">...</span>]]
    [-ls [-d] [-h] [-R] [&lt;path&gt; <span class="hljs-keyword">...</span>]]
    [-mkdir [-p] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-moveFromLocal &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-moveToLocal &lt;src&gt; &lt;localdst&gt;]
    [-mv &lt;src&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-put [-f] [-p] [-l] &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
    [-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]
    [-rm [-f] [-r|-R] [-skipTrash] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; <span class="hljs-keyword">...</span>]
    [-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]
    [-setfattr {-n name [-v value] | -x name} &lt;path&gt;]
    [-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-stat [format] &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-tail [-f] &lt;file&gt;]
    [-test -[defsz] &lt;path&gt;]
    [-text [-ignoreCrc] &lt;src&gt; <span class="hljs-keyword">...</span>]
    [-touchz &lt;path&gt; <span class="hljs-keyword">...</span>]
    [-usage [cmd <span class="hljs-keyword">...</span>]]

Generic options supported are
-conf &lt;configuration file&gt;     specify an application configuration file
-D &lt;property=value&gt;            use value <span class="hljs-keyword">for</span> given property
-fs &lt;local|namenode:port&gt;      specify a namenode
-jt &lt;local|resourcemanager:port&gt;    specify a ResourceManager
-files &lt;comma separated list of files&gt;    specify comma separated files to be copied to the map reduce cluster
-libjars &lt;comma separated list of jars&gt;    specify comma separated jar files to include <span class="hljs-keyword">in</span> the classpath.
-archives &lt;comma separated list of archives&gt;    specify comma separated archives to be unarchived on the compute machines.

The general command line syntax is
bin/hadoop command [genericOptions] [commandOptions]
</code></pre>

<p>下面演示一些常用命令</p>

<p>在data目录下创建一个hello.txt：</p>



<pre class="prettyprint"><code class=" hljs ">hadoop welcome
hadop hdfs mapreduce
hadoop hdfs</code></pre>

<p>查看文件系统上面现在有的东西，使用命令<code>hdfs dfs -ls /</code>：</p>



<pre class="prettyprint"><code class=" hljs oxygene">[japson@localhost data]$ hdfs dfs -ls /
<span class="hljs-number">18</span>/<span class="hljs-number">05</span>/<span class="hljs-number">24</span> <span class="hljs-number">02</span>:<span class="hljs-number">50</span>:<span class="hljs-number">08</span> WARN util.NativeCodeLoader: Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your <span class="hljs-keyword">platform</span>... <span class="hljs-keyword">using</span> builtin-java classes <span class="hljs-keyword">where</span> applicable</code></pre>

<p>但是什么都没有。然后我们将hello.txt传入到文件系统的根目录上</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> data]<span class="hljs-variable">$ </span>hdfs dfs -put hello.txt /</code></pre>

<p>我们再次查看，发现hello.txt已经被上传上去了：</p>



<pre class="prettyprint"><code class=" hljs oxygene">[japson@localhost data]$ hdfs dfs -ls /
<span class="hljs-number">18</span>/<span class="hljs-number">05</span>/<span class="hljs-number">24</span> <span class="hljs-number">02</span>:<span class="hljs-number">54</span>:<span class="hljs-number">31</span> WARN util.NativeCodeLoader: Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your <span class="hljs-keyword">platform</span>... <span class="hljs-keyword">using</span> builtin-java classes <span class="hljs-keyword">where</span> applicable
Found <span class="hljs-number">1</span> items
-rw-r--r--   <span class="hljs-number">1</span> japson supergroup         <span class="hljs-number">48</span> <span class="hljs-number">2018</span>-<span class="hljs-number">05</span>-<span class="hljs-number">24</span> <span class="hljs-number">02</span>:<span class="hljs-number">52</span> /hello.txt</code></pre>

<p>我们也可以查看这个文件内容<code>hdfs dfs -cat /hello.txt</code>：</p>



<pre class="prettyprint"><code class=" hljs oxygene">[japson@localhost data]$ hdfs dfs -cat /hello.txt
<span class="hljs-number">18</span>/<span class="hljs-number">05</span>/<span class="hljs-number">24</span> <span class="hljs-number">03</span>:<span class="hljs-number">00</span>:<span class="hljs-number">00</span> WARN util.NativeCodeLoader: Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your <span class="hljs-keyword">platform</span>... <span class="hljs-keyword">using</span> builtin-java classes <span class="hljs-keyword">where</span> applicable
hadoop welcome
hadop hdfs mapreduce
hadoop hdfs</code></pre>

<p>也可以创建一个目录</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> data]<span class="hljs-variable">$ </span>hdfs dfs -mkdir /test</code></pre>

<p>递归地创建目录：</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> data]<span class="hljs-variable">$ </span>hdfs dfs -ls /test/a/b</code></pre>

<p>当然也可以递归地查看</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> data]<span class="hljs-variable">$ </span>hdfs dfs -ls -<span class="hljs-constant">R</span> /</code></pre>

<p>也可以使用copyFromLocal命令将文件进行复制</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> data]<span class="hljs-variable">$ </span>hdfs dfs -copyFromLoacl hello.txt /test/a/b/h.txt</code></pre>

<p>从文件系统中将内容复制到本地，使用get命令，然后就可以在本地看到了下载来的文件：</p>



<pre class="prettyprint"><code class=" hljs ruby">[japson<span class="hljs-variable">@localhost</span> data]<span class="hljs-variable">$ </span>hdfs dfs -get /test/a/b/h.txt</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
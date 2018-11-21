---
layout:     post
title:      HADOOP - QUICK GUIDE-[2]-HDFS OVERVIEW
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hadoop-quick-guide-2-hdfs-overview">HADOOP - QUICK GUIDE-[2]-HDFS OVERVIEW</h1>

<blockquote>
  <p>原文 <br>
  <a href="https://www.tutorialspoint.com/hadoop/hadoop_quick_guide.htm" rel="nofollow">https://www.tutorialspoint.com/hadoop/hadoop_quick_guide.htm</a></p>
</blockquote>



<h1 id="hdfs-overview">HDFS OVERVIEW</h1>



<h2 id="features-of-hdfs">Features of HDFS</h2>

<ul>
<li>适合分布式存储和处理</li>
<li>Hadoop提供和HDFS交互的命令接口</li>
<li>内嵌的namenode和datanode帮助很容易的检测集群状态</li>
<li>流式streaming获取文件系统数据</li>
<li>HDFS提供数据一致性和权限管理</li>
</ul>



<h2 id="hdfs-architecture">HDFS Architecture</h2>

<p>master-slave architecture</p>

<p><img src="https://img-blog.csdn.net/20171231202945310?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGp3MTk5MDg5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="namenode">Namenode</h3>

<p>The namenode is the commodity hardware that contains the GNU/Linux operating system and the namenode software. It is a software that can be run on commodity hardware. The system having the namenode acts as Master server，does the following tasks:</p>

<ul>
<li>管理文件系统的namespace</li>
<li>管理clients对文件的访问</li>
<li>执行文件操作，如renaming、closing、opening file and directories</li>
</ul>



<h3 id="datanode">Datanode</h3>

<p>The datanode is a commodity hardware having the GNU/Linux operating system and datanode software. <br>
主要管理数据存储:</p>

<ul>
<li>Datanode 在文件系统执行读写操作</li>
<li>其他操作如block create、deletion、根据namenode的指令执行replication</li>
</ul>



<h3 id="block">Block</h3>

<p>HDFS可以读写的最小数据单元称为一个<font color="red"><strong><em>Block</em></strong></font>。默认的Block大小为64MB，每个节点可具体配置改变其大小。</p>



<h3 id="goals-of-hdfs">Goals of HDFS</h3>

<ul>
<li><strong>Fault detection and recovery</strong>:由于HDFS包含大规模的商业硬件，failure的因素很多，HDFS负责及时发现错误并对其恢复。</li>
<li><strong>Huge datasets</strong>:一个集群有上百个nodes，需要数据复制备份，因此是巨大的datasets。</li>
<li><strong>Hardware at data</strong>: 当计算发生在数据附近时，请求任务可以高效地完成。特别是在涉及大量数据集时，它减少了网络流量并增加了吞吐量。</li>
</ul>

<p>-</p>



<h1 id="hdfs-operations">HDFS OPERATIONS</h1>



<h2 id="starting-hdfs">Starting HDFS</h2>

<p>最初我们需要格式化配置的HDFS文件系统，启动namenode HDFS server，执行命令如下：</p>

<blockquote>
  <p>$ hadoop namenode -format</p>
</blockquote>

<p>然后启动分布式文件系统，按下面命令作为集群启动namenode和data nodes</p>

<blockquote>
  <p>$ start-dfs.sh </p>
</blockquote>



<h2 id="listing-files-in-hdfs">Listing Files in HDFS</h2>

<p>查看目录中的文件，以目录或filename作为参数</p>

<blockquote>
  <p>$HADOOP_HOME/bin/hadoop fs -ls </p>
</blockquote>



<h2 id="inserting-data-into-hdfs">Inserting Data into HDFS</h2>

<p>假设本地文件系统中有文件 <strong><em>file.txt</em></strong> ,以下操作将其insert到HDFS文件系统中：</p>



<h3 id="step-1">Step 1</h3>

<p>建立input文件夹</p>

<blockquote>
  <p><span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-1-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML" /&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-1" style="width: 0em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(3.812em, 1000em, 4.17em, -999.997em); top: -3.985em; left: 0em;"><span class="mrow" id="MathJax-Span-2"></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.068em; border-left: 0px solid; width: 0px; height: 0.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"></math></span></span><script type="math/tex" id="MathJax-Element-1"> </script>HADOOP_HOME/bin/hadoop fs -mkdir /user/input</p>
</blockquote>



<h3 id="step-2">Step 2</h3>

<p>以put 命令将本地文件存储到HDFS文件系统中：</p>

<blockquote>
  <p><span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-2-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML" /&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-3" style="width: 0em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(3.812em, 1000em, 4.17em, -999.997em); top: -3.985em; left: 0em;"><span class="mrow" id="MathJax-Span-4"></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.068em; border-left: 0px solid; width: 0px; height: 0.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"></math></span></span><script type="math/tex" id="MathJax-Element-2"> </script>HADOOP_HOME/bin/hadoop fs -put /home/file.txt /us</p>
</blockquote>



<h3 id="step-3">Step 3</h3>

<p>ls命令校验:</p>

<blockquote>
  <p><span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-3-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML" /&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-5" style="width: 0em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(3.812em, 1000em, 4.17em, -999.997em); top: -3.985em; left: 0em;"><span class="mrow" id="MathJax-Span-6"></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.068em; border-left: 0px solid; width: 0px; height: 0.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"></math></span></span><script type="math/tex" id="MathJax-Element-3"> </script>HADOOP_HOME/bin/hadoop fs -ls /user/input</p>
</blockquote>



<h2 id="retrieving-data-from-hdfs">Retrieving Data from HDFS</h2>

<p>假设HDFS中有文件 <strong><em>outfile</em></strong>，下面示例如何在HDFS中获取需要的数据到本地:</p>



<h3 id="step-1-1">Step 1</h3>

<p>使用cat view data:</p>

<blockquote>
  <p><span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-4-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML" /&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-7" style="width: 0em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(3.812em, 1000em, 4.17em, -999.997em); top: -3.985em; left: 0em;"><span class="mrow" id="MathJax-Span-8"></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.068em; border-left: 0px solid; width: 0px; height: 0.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"></math></span></span><script type="math/tex" id="MathJax-Element-4"> </script>HADOOP_HOME/bin/hadoop fs -cat /user/output/outfile </p>
</blockquote>



<h3 id="step-2-1">Step 2</h3>

<p>使用get命令将数据获取到本地:</p>

<blockquote>
  <p><span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-5-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML" /&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-9" style="width: 0em; display: inline-block;"><span style="display: inline-block; position: relative; width: 0em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(3.812em, 1000em, 4.17em, -999.997em); top: -3.985em; left: 0em;"><span class="mrow" id="MathJax-Span-10"></span><span style="display: inline-block; width: 0px; height: 3.991em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.068em; border-left: 0px solid; width: 0px; height: 0.146em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"></math></span></span><script type="math/tex" id="MathJax-Element-5"> </script>HADOOP_HOME/bin/hadoop fs -get /user/output/ /home/hadoop_tp/</p>
</blockquote>



<h2 id="shutting-down-the-hdfs">Shutting Down the HDFS</h2>

<blockquote>
  <p>$ stop-dfs.sh </p>
</blockquote>



<h1 id="command-reference">COMMAND REFERENCE</h1>



<h2 id="hdfs-command-reference">HDFS Command Reference</h2>

<p>不添加任何参数运行<font color="green"><strong><em>./bin/hadoop dfs</em></strong></font> 将列举所有其他命名，<font color="green"><strong>$HADOOP_HOME/bin/hadoop fs ­help commandName</strong></font> 将给出简要使用说明。 <br>
下面给出一些命令说明</p>



<pre class="prettyprint"><code class=" hljs livecodeserver"><span class="hljs-string">"&lt;path&gt;"</span> means <span class="hljs-keyword">any</span> <span class="hljs-built_in">file</span> <span class="hljs-operator">or</span> <span class="hljs-built_in">directory</span> name.  
<span class="hljs-string">"&lt;path&gt;..."</span> means <span class="hljs-constant">one</span> <span class="hljs-operator">or</span> more <span class="hljs-built_in">file</span> <span class="hljs-operator">or</span> <span class="hljs-built_in">directory</span> names.  
<span class="hljs-string">"&lt;file&gt;"</span> means <span class="hljs-keyword">any</span> filename.  
<span class="hljs-string">"&lt;src&gt;"</span> <span class="hljs-operator">and</span> <span class="hljs-string">"&lt;dest&gt;"</span> are path names <span class="hljs-operator">in</span> <span class="hljs-operator">a</span> directed operation.  
<span class="hljs-string">"&lt;localSrc&gt;"</span> <span class="hljs-operator">and</span> <span class="hljs-string">"&lt;localDest&gt;"</span> are paths <span class="hljs-keyword">as</span> above, but <span class="hljs-command"><span class="hljs-keyword">on</span> <span class="hljs-title">the</span> <span class="hljs-title">local</span> <span class="hljs-title">file</span> <span class="hljs-title">system</span>.</span></code></pre>

<table>
<thead>
<tr>
  <th>Command</th>
  <th>Description</th>
</tr>
</thead>
<tbody><tr>
  <td>-ls &lt;path&gt;</td>
  <td>列举path下的文件内容，<br>包括names、permissions、owners、size、modification date</td>
</tr>
<tr>
  <td>-lsr &lt;path&gt;</td>
  <td>像ls，迭代path下所有文件</td>
</tr>
<tr>
  <td>-du &lt;path&gt;</td>
  <td>磁盘大小（bytes）</td>
</tr>
<tr>
  <td>-dus &lt;path&gt;</td>
  <td>像-du，显示summary</td>
</tr>
<tr>
  <td>-mv &lt;src&gt; <br>&lt;dest&gt;</td>
  <td>移动文件</td>
</tr>
<tr>
  <td>-cp &lt;src&gt; <br>&lt;dest&gt;</td>
  <td>复制文件</td>
</tr>
<tr>
  <td>-rm &lt;path&gt;</td>
  <td>removes文件或空目录，-r迭代删除，如删除文件夹 hdfs dfs -rm  -r 目录</td>
</tr>
<tr>
  <td>-rmr &lt;path&gt;</td>
  <td>迭代removes文件或目录</td>
</tr>
<tr>
  <td>-put &lt;localSrc&gt; <br>&lt;dest&gt;</td>
  <td>将本地文件放入DFS</td>
</tr>
<tr>
  <td>-copyFromLocal &lt;localSrc&gt; <br>&lt;dest&gt;</td>
  <td>同put</td>
</tr>
<tr>
  <td>-moveFromLocal &lt;localSrc&gt; <br>&lt;dest&gt;</td>
  <td>将本地文件放入DFS，成功后删除本地文件</td>
</tr>
<tr>
  <td>-get [-crc] &lt;src&gt;  &lt;localDest&gt;</td>
  <td>将DFS文件拷贝到本地</td>
</tr>
<tr>
  <td>-getmerge &lt;src&gt; &lt;localDest&gt;</td>
  <td>将DFS文件拷贝到本地并合并为一个文件</td>
</tr>
<tr>
  <td>-cat &lt;filename&gt;</td>
  <td>展示文件内容</td>
</tr>
<tr>
  <td>-copyToLocal &lt;src&gt; &lt;localDest&gt;</td>
  <td>同get</td>
</tr>
<tr>
  <td>-moveToLocal &lt;src&gt; &lt;localDest&gt;</td>
  <td>同get,成功后删除HDFS中文件</td>
</tr>
<tr>
  <td>-mkdir &lt;path&gt;</td>
  <td>建立目录（迭代）</td>
</tr>
<tr>
  <td>-setrep [-R] [-w] rep &lt;path&gt;</td>
  <td>Sets the target replication factor for files identified by path to rep.</td>
</tr>
<tr>
  <td>-touchz &lt;path&gt;</td>
  <td>path目录下以当前时间戳建立文件，如果文件存在失败（除非文件size=0），比如新建一个_SUCCESS文件,hadoop dfs -touchz /hdp/ddd/xxx.db/dddd/dt=20180222/_SUCCESS</td>
</tr>
<tr>
  <td>-test -[ezd] &lt;path&gt;</td>
  <td>Returns 1 if path exists; has zero length; or is a directory or 0 otherwise.</td>
</tr>
<tr>
  <td>-stat [format] &lt;path&gt;</td>
  <td>Prints information about path. Format is a string which accepts file size in blocks , filename , block size , replication , and modification date .</td>
</tr>
<tr>
  <td>-tail [-f] &lt;file2name&gt;</td>
  <td>Shows the last 1KB of file on stdout.</td>
</tr>
<tr>
  <td>-chmod [-R] mode,mode,… &lt;path&gt;…</td>
  <td>Changes the file permissions associated with one or more objects identified by path…. Performs changes recursively with R. mode is a 3-digit octal mode, or {augo}+/-{rwxX}. Assumes if no scope is specified and does not apply an umask.</td>
</tr>
<tr>
  <td>-chown [-R] [owner][: [group]] &lt;path&gt;…</td>
  <td>Sets the owning user and/or group for files or directories identified by path…. Sets owner recursively if -R is specified.</td>
</tr>
<tr>
  <td>-chgrp [-R] group &lt;path&gt;…</td>
  <td>Sets the owning group for files or directories identified by path…. Sets group recursively if -R is specified.</td>
</tr>
<tr>
  <td>-help &lt;cmdname&gt;</td>
  <td>Returns usage information for one of the commands listed above. You must omit the leading ‘-’ character in cmd.</td>
</tr>
<tr>
  <td>-grep &lt;cmdname&gt;</td>
  <td>搜索查询，如 hadoop dfs -cat 文件  / grep &lt;要搜索的字符&gt;.</td>
</tr>
</tbody></table>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
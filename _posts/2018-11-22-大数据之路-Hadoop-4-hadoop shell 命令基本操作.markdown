---
layout:     post
title:      大数据之路-Hadoop-4-hadoop shell 命令基本操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明出处：http://blog.csdn.net/liudongdong0909。					https://blog.csdn.net/liudongdong0909/article/details/79156348				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc">
<ul>
<li><ul>
<li><ul>
<li><a href="#%E4%B8%80hdfs-%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5" rel="nofollow">一HDFS 基本概念</a><ul>
<li><a href="#11-hdfs-%E5%89%8D%E8%A8%80" rel="nofollow">1 HDFS 前言</a></li>
<li><a href="#12-hdfs%E7%9A%84%E6%A6%82%E5%BF%B5%E5%92%8C%E7%89%B9%E6%80%A7" rel="nofollow">2 HDFS的概念和特性</a><ul>
<li><a href="#1-%E5%AE%83%E6%98%AF%E4%B8%80%E4%B8%AA%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F%E7%94%A8%E4%BA%8E%E5%AD%98%E5%82%A8%E6%96%87%E4%BB%B6%E9%80%9A%E8%BF%87%E7%BB%9F%E4%B8%80%E7%9A%84%E5%91%BD%E5%90%8D%E7%A9%BA%E9%97%B4%E7%9B%AE%E5%BD%95%E6%A0%91%E6%9D%A5%E5%AE%9A%E4%BD%8D%E6%96%87%E4%BB%B6" rel="nofollow">它是一个文件系统用于存储文件通过统一的命名空间目录树来定位文件</a></li>
<li><a href="#2-%E5%AE%83%E6%98%AF%E5%88%86%E5%B8%83%E5%BC%8F%E7%9A%84%E7%94%B1%E5%BE%88%E5%A4%9A%E6%9C%8D%E5%8A%A1%E5%99%A8%E8%81%94%E5%90%88%E8%B5%B7%E6%9D%A5%E5%AE%9E%E7%8E%B0%E5%85%B6%E5%8A%9F%E8%83%BD%E9%9B%86%E7%BE%A4%E4%B8%AD%E7%9A%84%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%9C%89%E5%90%84%E8%87%AA%E7%9A%84%E8%A7%92%E8%89%B2" rel="nofollow">它是分布式的由很多服务器联合起来实现其功能集群中的服务器有各自的角色</a></li>
<li><a href="#3-%E9%87%8D%E8%A6%81%E7%89%B9%E6%80%A7%E5%A6%82%E4%B8%8B" rel="nofollow">重要特性如下</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E4%BA%8Chdfs%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C" rel="nofollow">二HDFS基本操作</a><ul>
<li><a href="#21-%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%94%AF%E6%8C%81%E7%9A%84%E5%91%BD%E4%BB%A4%E5%8F%82%E6%95%B0" rel="nofollow">1 命令行客户端支持的命令参数</a></li>
<li><a href="#22-%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E5%8F%82%E6%95%B0" rel="nofollow">2 常用命令参数</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</li>
</ul>
</div>


<h3 id="一hdfs-基本概念">一、HDFS 基本概念</h3>



<h4 id="11-hdfs-前言">1.1 HDFS 前言</h4>

<ol>
<li>设计思想 <br>
分而治之：将大文件、大批量文件，分布式存放在大量服务器上，以便于采取分而治之的方式对海量数据进行运算分析。</li>
<li>在大数据系统中的应用 <br>
为各类分布式运算框架（mapreduce,spark…）提供数据存储服务</li>
<li>重点概念 <br>
文件切块、副本存放、元数据</li>
</ol>



<h4 id="12-hdfs的概念和特性">1.2 HDFS的概念和特性</h4>



<h5 id="1-它是一个文件系统用于存储文件通过统一的命名空间目录树来定位文件">1. 它是一个文件系统，用于存储文件，通过统一的命名空间——目录树来定位文件</h5>



<h5 id="2-它是分布式的由很多服务器联合起来实现其功能集群中的服务器有各自的角色">2. 它是分布式的，由很多服务器联合起来实现其功能，集群中的服务器有各自的角色</h5>



<h5 id="3-重要特性如下">3. 重要特性如下：</h5>

<p>（1） HDFS中的文件在物理上是分块存储（block），块的大小可以通过配置参数( dfs.blocksize)来规定，默认大小在hadoop2.x版本中是128M，老版本中是64M</p>

<p>（2） HDFS文件系统会给客户端提供一个统一的抽象目录树，客户端通过路径来访问文件，形如：</p>



<pre class="prettyprint"><code class=" hljs dos">hdfs://namenode:port/<span class="hljs-keyword">dir</span>-a/<span class="hljs-keyword">dir</span>-b/<span class="hljs-keyword">dir</span>-c/file.data</code></pre>

<p>（3） 目录结构及文件分块信息(元数据)的管理由namenode节点承担 —— namenode是HDFS集群主节点，负责维护整个hdfs文件系统的目录树，以及每一个路径（文件）所对应的block块信息（block的id，及所在的datanode服务器）</p>

<p>（4） 文件的各个block的存储管理由datanode节点承担 <br>
—- datanode是HDFS集群从节点，每一个block都可以在多个datanode上存储多个副本（副本数量也可以通过参数设置dfs.replication）</p>

<p>（5） HDFS是设计成适应一次写入，多次读出的场景，且不支持文件的修改 <br>
(注：适合用来做数据分析，并不适合用来做网盘应用，因为，不便修改，延迟大，网络开销大，成本太高)</p>



<h3 id="二hdfs基本操作">二、HDFS基本操作</h3>



<h4 id="21-命令行客户端支持的命令参数">2.1 命令行客户端支持的命令参数</h4>



<pre class="prettyprint"><code class=" hljs r">   [-appendToFile &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
   [-cat [-ignoreCrc] &lt;src&gt; <span class="hljs-keyword">...</span>]
   [-checksum &lt;src&gt; <span class="hljs-keyword">...</span>]
   [-chgrp [-R] GROUP PATH...]
   [-chmod [-R] &lt;MODE[,MODE]<span class="hljs-keyword">...</span> | OCTALMODE&gt; PATH...]
   [-chown [-R] [OWNER][:[GROUP]] PATH...]
   [-copyFromLocal [-f] [-p] &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
   [-copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;localdst&gt;]
   [-count [-q] &lt;path&gt; <span class="hljs-keyword">...</span>]
   [-cp [-f] [-p] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
   [-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]
   [-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]
   [-df [-h] [&lt;path&gt; <span class="hljs-keyword">...</span>]]
   [-du [-s] [-h] &lt;path&gt; <span class="hljs-keyword">...</span>]
   [-expunge]
   [-get [-p] [-ignoreCrc] [-crc] &lt;src&gt; <span class="hljs-keyword">...</span> &lt;localdst&gt;]
   [-getfacl [-R] &lt;path&gt;]
   [-getmerge [-nl] &lt;src&gt; &lt;localdst&gt;]
   [-help [cmd <span class="hljs-keyword">...</span>]]
   [-ls [-d] [-h] [-R] [&lt;path&gt; <span class="hljs-keyword">...</span>]]
   [-mkdir [-p] &lt;path&gt; <span class="hljs-keyword">...</span>]
   [-moveFromLocal &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
   [-moveToLocal &lt;src&gt; &lt;localdst&gt;]
   [-mv &lt;src&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
   [-put [-f] [-p] &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
   [-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]
   [-rm [-f] [-r|-R] [-skipTrash] &lt;src&gt; <span class="hljs-keyword">...</span>]
   [-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; <span class="hljs-keyword">...</span>]
   [-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]
   [-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; <span class="hljs-keyword">...</span>]
   [-stat [format] &lt;path&gt; <span class="hljs-keyword">...</span>]
   [-tail [-f] &lt;file&gt;]
   [-test -[defsz] &lt;path&gt;]
   [-text [-ignoreCrc] &lt;src&gt; <span class="hljs-keyword">...</span>]
   [-touchz &lt;path&gt; <span class="hljs-keyword">...</span>]
   [-usage [cmd <span class="hljs-keyword">...</span>]]</code></pre>



<h4 id="22-常用命令参数">2.2 常用命令参数</h4>

<table>
<thead>
<tr>
  <th>命令</th>
  <th>功能</th>
  <th>示例</th>
</tr>
</thead>
<tbody><tr>
  <td>-help</td>
  <td>输出这个命令参数手册</td>
  <td>hadoop dfs -help</td>
</tr>
<tr>
  <td>-ls</td>
  <td>显示目录信息</td>
  <td>hdfs dfs -ls / &lt;等价&gt; hdfs dfs -ls hdfs://hadoop01:9000/</td>
</tr>
<tr>
  <td>-mkdir</td>
  <td>在hdfs上创建目录</td>
  <td>hdfs dfs -mkdir -p /aaa/bbb</td>
</tr>
<tr>
  <td>-moveFromLocal</td>
  <td>从本地剪切粘贴到hdfs</td>
  <td>hdfs dfs -moveFromLocal /home/hadoop/test.txt  /aaa/bbb/</td>
</tr>
<tr>
  <td>-moveToLocal</td>
  <td>从hdfs剪切粘贴到本地</td>
  <td>hdfs dfs  -moveToLocal /aaa/bbb/test.txt /home/hadoop/test.txt</td>
</tr>
<tr>
  <td>-appendToFile</td>
  <td>追加一个文件到已经存在的文件末尾</td>
  <td>hdfs dfs -appendToFile a.txt  /b.txt</td>
</tr>
<tr>
  <td>-cat</td>
  <td>显示文件内容</td>
  <td>hdfs dfs -cat /b.txt</td>
</tr>
<tr>
  <td>-tail</td>
  <td>显示一个文件的末尾</td>
  <td>hdfs dfs -tail /b.txt</td>
</tr>
<tr>
  <td>-text</td>
  <td>以字符形式打印一个文件的内容</td>
  <td>hdfs dfs -text /input/test.txt</td>
</tr>
<tr>
  <td>-chgrp  -chmod -chown</td>
  <td>linux文件系统中的用法一样，对文件所属权限</td>
  <td>hdfs dfs -chmod 666 /b.txt</td>
</tr>
<tr>
  <td>-copyFromLocal</td>
  <td>从本地文件系统中拷贝文件到hdfs路径去</td>
  <td>hdfs dfs -copyFromLocal a.txt  /</td>
</tr>
<tr>
  <td>-copyToLocal</td>
  <td>从hdfs拷贝到本地</td>
  <td>hdfs dfs -copyToLocal /a.txt cc.txt</td>
</tr>
<tr>
  <td>-cp</td>
  <td>从hdfs的一个路径拷贝hdfs的另一个路径</td>
  <td>hdfs dfs -cp /a.txt /aaa/</td>
</tr>
<tr>
  <td>-mv</td>
  <td>在hdfs目录中移动文件</td>
  <td>hdfs dfs -mv /b.txt /aaa/</td>
</tr>
<tr>
  <td>-get</td>
  <td>等同于copyToLocal，就是从hdfs下载文件到本地</td>
  <td>hdfs dfs -get /aaa/b.txt</td>
</tr>
<tr>
  <td>-getmerge</td>
  <td>合并下载多个文件</td>
  <td>hdfs dfs -getmerge /aaa/*.txt c.txt</td>
</tr>
<tr>
  <td>-put</td>
  <td>等同于 copyFromLocal</td>
  <td>hdfs dfs -put c.txt /aaa/</td>
</tr>
<tr>
  <td>-rm</td>
  <td>删除文件或文件夹</td>
  <td>hdfs dfs -rm /a.txt</td>
</tr>
<tr>
  <td>-rmdir</td>
  <td>删除空目录</td>
  <td>hdfs dfs -rmdir /cc</td>
</tr>
<tr>
  <td>-df</td>
  <td>统计文件系统的可用空间信息</td>
  <td>hdfs dfs -df -h /</td>
</tr>
<tr>
  <td>-du</td>
  <td>统计文件夹的大小信息</td>
  <td>hdfs dfs -du -s -h /aaa/*</td>
</tr>
<tr>
  <td>-count</td>
  <td>统计一个指定目录下的文件节点数量</td>
  <td>hdfs dfs -count /aaa/</td>
</tr>
<tr>
  <td>-setrep</td>
  <td>设置hdfs中文件的副本数量</td>
  <td>hdfs dfs -setrep 3 /aaa/a.txt  [这里设置的副本数只是记录在namenode的元数据中，是否真的会有这么多副本，还得看datanode的数量]</td>
</tr>
</tbody></table>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
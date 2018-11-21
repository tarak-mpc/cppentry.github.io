---
layout:     post
title:      Hadoop（2）HDFS文件系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#%E4%B8%80-hdfs%E6%A6%82%E5%BF%B5" rel="nofollow">一 HDFS概念</a><ul>
<li><a href="#1-%E6%A6%82%E5%BF%B5" rel="nofollow">1 概念</a></li>
<li><a href="#2-%E7%BB%84%E6%88%90" rel="nofollow">2 组成</a></li>
<li><a href="#3-hdfs%E6%96%87%E4%BB%B6%E5%9D%97%E5%A4%A7%E5%B0%8F" rel="nofollow">3 HDFS文件块大小</a></li>
</ul>
</li>
<li><a href="#%E4%BA%8C-hfds%E5%91%BD%E4%BB%A4%E8%A1%8C%E6%93%8D%E4%BD%9C" rel="nofollow">二  HFDS命令行操作</a><ul>
<li><a href="#1-%E5%9F%BA%E6%9C%AC%E8%AF%AD%E6%B3%95" rel="nofollow">1 基本语法</a></li>
<li><a href="#2-%E5%8F%82%E6%95%B0%E5%A4%A7%E5%85%A8" rel="nofollow">2 参数大全</a></li>
<li><a href="#3%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4%E5%AE%9E%E6%93%8D" rel="nofollow">3常用命令实操</a></li>
</ul>
</li>
<li><a href="#%E4%B8%89-hdfs%E5%AE%A2%E6%88%B7%E7%AB%AF%E6%93%8D%E4%BD%9C" rel="nofollow">三 HDFS客户端操作</a><ul>
<li><a href="#1-eclipse%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87" rel="nofollow">1 eclipse环境准备</a><ul>
<li><a href="#1-jar%E5%8C%85%E5%87%86%E5%A4%87" rel="nofollow">jar包准备</a></li>
<li><a href="#2-eclipse%E5%87%86%E5%A4%87" rel="nofollow">eclipse准备</a></li>
</ul>
</li>
<li><a href="#2-%E9%80%9A%E8%BF%87api%E6%93%8D%E4%BD%9Chdfs" rel="nofollow">2 通过API操作HDFS</a><ul>
<li><a href="#1-hdfs%E8%8E%B7%E5%8F%96%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F" rel="nofollow">HDFS获取文件系统</a></li>
<li><a href="#2-hdfs%E6%96%87%E4%BB%B6%E4%B8%8A%E4%BC%A0" rel="nofollow">HDFS文件上传</a></li>
<li><a href="#3-hdfs%E6%96%87%E4%BB%B6%E4%B8%8B%E8%BD%BD" rel="nofollow">HDFS文件下载</a></li>
<li><a href="#4-hdfs%E7%9B%AE%E5%BD%95%E5%88%9B%E5%BB%BA" rel="nofollow">HDFS目录创建</a></li>
<li><a href="#5-hdfs%E6%96%87%E4%BB%B6%E5%A4%B9%E5%88%A0%E9%99%A4" rel="nofollow">HDFS文件夹删除</a></li>
<li><a href="#6-hdfs%E6%96%87%E4%BB%B6%E5%90%8D%E6%9B%B4%E6%94%B9" rel="nofollow">HDFS文件名更改</a></li>
<li><a href="#7-hdfs%E6%96%87%E4%BB%B6%E8%AF%A6%E6%83%85%E6%9F%A5%E7%9C%8B" rel="nofollow">HDFS文件详情查看</a></li>
<li><a href="#8-hdfs%E6%96%87%E4%BB%B6%E5%A4%B9%E6%9F%A5%E7%9C%8B" rel="nofollow">HDFS文件夹查看</a></li>
</ul>
</li>
<li><a href="#3-%E9%80%9A%E8%BF%87io%E6%B5%81%E6%93%8D%E4%BD%9Chdfs" rel="nofollow">3 通过IO流操作HDFS</a><ul>
<li><a href="#1-hdfs%E6%96%87%E4%BB%B6%E4%B8%8A%E4%BC%A0" rel="nofollow">HDFS文件上传</a></li>
<li><a href="#2-hdfs%E6%96%87%E4%BB%B6%E4%B8%8B%E8%BD%BD" rel="nofollow">HDFS文件下载</a></li>
<li><a href="#3-%E5%AE%9A%E4%BD%8D%E6%96%87%E4%BB%B6%E8%AF%BB%E5%8F%96" rel="nofollow">定位文件读取</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#%E5%9B%9B-hdfs%E7%9A%84%E6%95%B0%E6%8D%AE%E6%B5%81" rel="nofollow">四 HDFS的数据流</a><ul>
<li><a href="#1-hdfs%E5%86%99%E6%95%B0%E6%8D%AE%E6%B5%81%E7%A8%8B" rel="nofollow">1 HDFS写数据流程</a><ul>
<li><a href="#1-%E5%89%96%E6%9E%90%E6%96%87%E4%BB%B6%E5%86%99%E5%85%A5" rel="nofollow">剖析文件写入</a></li>
<li><a href="#2-%E7%BD%91%E7%BB%9C%E6%8B%93%E6%89%91%E6%A6%82%E5%BF%B5" rel="nofollow">网络拓扑概念</a></li>
<li><a href="#3-%E6%9C%BA%E6%9E%B6%E6%84%9F%E7%9F%A5%E5%89%AF%E6%9C%AC%E8%8A%82%E7%82%B9%E9%80%89%E6%8B%A9" rel="nofollow">机架感知副本节点选择</a></li>
</ul>
</li>
<li><a href="#2-hdfs%E8%AF%BB%E6%95%B0%E6%8D%AE%E6%B5%81%E7%A8%8B" rel="nofollow">2 HDFS读数据流程</a></li>
<li><a href="#3-%E4%B8%80%E8%87%B4%E6%80%A7%E6%A8%A1%E5%9E%8B" rel="nofollow">3 一致性模型</a></li>
</ul>
</li>
<li><a href="#%E4%BA%94-namenode%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6" rel="nofollow">五 NameNode工作机制</a><ul>
<li><a href="#1-51-namenodesecondary-namenode%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6" rel="nofollow">1 51 NameNodeSecondary NameNode工作机制</a></li>
<li><a href="#2-%E9%95%9C%E5%83%8F%E6%96%87%E4%BB%B6%E5%92%8C%E7%BC%96%E8%BE%91%E6%97%A5%E5%BF%97%E6%96%87%E4%BB%B6" rel="nofollow">2 镜像文件和编辑日志文件</a></li>
<li><a href="#3-%E6%BB%9A%E5%8A%A8%E7%BC%96%E8%BE%91%E6%97%A5%E5%BF%97" rel="nofollow">3 滚动编辑日志</a></li>
<li><a href="#4-namenode%E7%89%88%E6%9C%AC%E5%8F%B7" rel="nofollow">4 namenode版本号</a></li>
<li><a href="#5-secondarynamenode%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84" rel="nofollow">5 SecondaryNameNode目录结构</a></li>
<li><a href="#6-%E9%9B%86%E7%BE%A4%E5%AE%89%E5%85%A8%E6%A8%A1%E5%BC%8F%E6%93%8D%E4%BD%9C" rel="nofollow">6 集群安全模式操作</a></li>
<li><a href="#7-namenode%E5%A4%9A%E7%9B%AE%E5%BD%95%E9%85%8D%E7%BD%AE" rel="nofollow">7 Namenode多目录配置</a></li>
</ul>
</li>
<li><a href="#%E5%85%AD-datanode%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6" rel="nofollow">六 DataNode工作机制</a><ul>
<li><a href="#1-datanode%E5%B7%A5%E4%BD%9C%E6%9C%BA%E5%88%B6" rel="nofollow">1 DataNode工作机制</a></li>
<li><a href="#2-%E6%95%B0%E6%8D%AE%E5%AE%8C%E6%95%B4%E6%80%A7" rel="nofollow">2 数据完整性</a></li>
<li><a href="#3-%E6%8E%89%E7%BA%BF%E6%97%B6%E9%99%90%E5%8F%82%E6%95%B0%E8%AE%BE%E7%BD%AE" rel="nofollow">3 掉线时限参数设置</a></li>
<li><a href="#4-datanode%E7%9A%84%E7%9B%AE%E5%BD%95%E7%BB%93%E6%9E%84" rel="nofollow">4 DataNode的目录结构</a></li>
<li><a href="#5-%E6%9C%8D%E5%BD%B9%E6%96%B0%E6%95%B0%E6%8D%AE%E8%8A%82%E7%82%B9" rel="nofollow">5 服役新数据节点</a></li>
<li><a href="#6-%E9%80%80%E5%BD%B9%E6%97%A7%E6%95%B0%E6%8D%AE%E8%8A%82%E7%82%B9" rel="nofollow">6 退役旧数据节点</a></li>
<li><a href="#7-datanode%E5%A4%9A%E7%9B%AE%E5%BD%95%E9%85%8D%E7%BD%AE" rel="nofollow">7 Datanode多目录配置</a></li>
</ul>
</li>
</ul>
</div>
</div>




<h1 id="一-hdfs概念">一、 HDFS概念</h1>



<h2 id="1-概念">1、 概念</h2>

<p><strong>HDFS，它是一个文件系统，</strong>用于存储文件，通过目录树来定位文件；<strong>其次，它是分布式的，</strong>由很多服务器联合起来实现其功能，集群中的服务器有各自的角色。</p>

<p>HDFS的设计适合一次写入，多次读出的场景，且不支持文件的修改。适合用来做数据分析，并不适合用来做网盘应用。</p>



<h2 id="2-组成">2、 组成</h2>

<p>1）HDFS集群包括，NameNode和DataNode以及Secondary Namenode。</p>

<p>2）NameNode负责管理整个文件系统的元数据，以及每一个路径（文件）所对应的数据块信息。</p>

<p>3）DataNode 负责管理用户的文件数据块，每一个数据块都可以在多个datanode上存储多个副本。</p>

<p>4）Secondary NameNode用来监控HDFS状态的辅助后台程序，每隔一段时间获取HDFS元数据的快照。</p>



<h2 id="3-hdfs文件块大小">3、 HDFS文件块大小</h2>

<p>HDFS中的文件在物理上是分块存储（block），块的大小可以通过配置参数( dfs.blocksize)来规定，默认大小在hadoop2.x版本中是128M，老版本中是64M</p>

<p>HDFS的块比磁盘的块大，其目的是为了最小化寻址开销。如果块设置得足够大，从磁盘传输数据的时间会明显大于定位这个块开始位置所需的时间。因而，传输一个由多个块组成的文件的时间取决于磁盘传输速率。</p>

<p>如果寻址时间约为10ms，而传输速率为100MB/s，为了使寻址时间仅占传输时间的1%，我们要将块大小设置约为100MB。默认的块大小128MB。 <br>
块的大小：10ms*100*100M/s = 100M <br>
<img src="https://img-blog.csdn.net/20171128200332126" alt="这里写图片描述" title=""></p>



<h1 id="二-hfds命令行操作">二、  HFDS命令行操作</h1>



<h2 id="1-基本语法">1、 基本语法</h2>

<p><code>bin/hadoop fs 具体命令</code></p>



<h2 id="2-参数大全">2、 参数大全</h2>

<pre><code>`bin/hadoop fs`
</code></pre>



<pre class="prettyprint"><code class=" hljs r">        [-appendToFile &lt;localsrc&gt; <span class="hljs-keyword">...</span> &lt;dst&gt;]
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



<h2 id="3常用命令实操">3、常用命令实操</h2>

<p>（1）-help：输出这个命令参数 <br>
<code>bin/hdfs dfs -help rm</code> <br>
（2）-ls: 显示目录信息 <br>
<code>hadoop fs -ls /</code> <br>
（3）-mkdir：在hdfs上创建目录 <br>
<code>hadoop fs  -mkdir  -p  /aaa/bbb/cc/dd</code> <br>
（4）-moveFromLocal从本地剪切粘贴到hdfs <br>
<code>hadoop  fs  - moveFromLocal  /home/hadoop/a.txt  /aaa/bbb/cc/dd</code> <br>
（5）-moveToLocal：从hdfs剪切粘贴到本地（尚未实现）</p>



<pre class="prettyprint"><code class=" hljs ruby">[atguigu<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>hadoop fs -help moveToLocal
-moveToLocal &lt;src&gt; &lt;localdst&gt; <span class="hljs-symbol">:</span>
  <span class="hljs-constant">Not</span> implemented yet</code></pre>

<p>（6）–appendToFile  ：追加一个文件到已经存在的文件末尾 <br>
<code>hadoop  fs  -appendToFile  ./hello.txt  /hello.txt</code> <br>
（7）-cat ：显示文件内容 <br>
（8）-tail：显示一个文件的末尾 <br>
<code>hadoop  fs  -tail  /weblog/access_log.1</code> <br>
（9）-chgrp 、-chmod、-chown：linux文件系统中的用法一样，修改文件所属权限 <br>
<code>hadoop  fs  -chmod  666  /hello.txt</code> <br>
<code>hadoop  fs  -chown  someuser:somegrp   /hello.txt</code> <br>
（10）-copyFromLocal：从本地文件系统中拷贝文件到hdfs路径去 <br>
<code>hadoop  fs  -copyFromLocal  ./jdk.tar.gz  /aaa/</code> <br>
（11）-copyToLocal：从hdfs拷贝到本地 <br>
<code>hadoop fs -copyToLocal /user/hello.txt ./hello.txt</code> <br>
（12）-cp ：从hdfs的一个路径拷贝到hdfs的另一个路径 <br>
<code>hadoop  fs  -cp  /aaa/jdk.tar.gz  /bbb/jdk.tar.gz.2</code> <br>
（13）-mv：在hdfs目录中移动文件 <br>
<code>hadoop  fs  -mv  /aaa/jdk.tar.gz  /</code> <br>
（14）-get：等同于copyToLocal，就是从hdfs下载文件到本地 <br>
<code>hadoop fs -get /user/hello.txt ./</code> <br>
（15）-getmerge  ：合并下载多个文件，比如hdfs的目录 /aaa/下有多个文件:log.1, log.2,log.3,… <br>
<code>hadoop fs -getmerge /aaa/log.* ./log.sum</code> <br>
（16）-put：等同于copyFromLocal <br>
<code>hadoop  fs  -put  /aaa/jdk.tar.gz  /bbb/jdk.tar.gz.2</code> <br>
（17）-rm：删除文件或文件夹 <br>
<code>hadoop fs -rm -r /aaa/bbb/</code> <br>
（18）-rmdir：删除空目录 <br>
<code>hadoop  fs  -rmdir   /aaa/bbb/ccc</code> <br>
（19）-df ：统计文件系统的可用空间信息 <br>
<code>hadoop  fs  -df  -h  /</code> <br>
（20）-du统计文件夹的大小信息</p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>hadoop fs -du -s -h /user/atguigu/wcinput
<span class="hljs-number">188.5</span> <span class="hljs-constant">M</span>  /user/atguigu/wcinput</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">[admin@linux01 hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.2</span>]$ hadoop fs -du  -h /user/atguigu/wcinput
<span class="hljs-number">188.5</span> M  /user/atguigu/wcinput/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
<span class="hljs-number">97</span>       /user/atguigu/wcinput/wc<span class="hljs-preprocessor">.input</span></code></pre>

<p>（21）-count：统计一个指定目录下的文件节点数量 <br>
<code>hadoop fs -count /aaa/</code></p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>hadoop fs -count /user/atguigu/wcinput
       <span class="hljs-number">1</span>                <span class="hljs-number">2</span>          <span class="hljs-number">197657784</span> /user/atguigu/wcinput
嵌套文件层级；  包含文件的总数</code></pre>

<p>（22）-setrep：设置hdfs中文件的副本数量 <br>
<code>hadoop fs -setrep 3 /aaa/jdk.tar.gz</code> <br>
 <img src="https://img-blog.csdn.net/20171128201153000" alt="这里写图片描述" title=""> <br>
这里设置的副本数只是记录在namenode的元数据中，是否真的会有这么多副本，还得看datanode的数量。因为目前只有3台设备，最多也就3个副本，只有节点数的增加到10台时，副本数才能达到10。</p>



<h1 id="三-hdfs客户端操作">三、 HDFS客户端操作</h1>



<h2 id="1-eclipse环境准备">1、 eclipse环境准备</h2>



<h3 id="1-jar包准备">1 jar包准备</h3>

<p>1）解压hadoop-2.7.2.tar.gz到非中文目录 <br>
2）进入share文件夹，查找所有jar包，并把jar包拷贝到_lib文件夹下 <br>
3）在全部jar包中查找sources.jar，并剪切到_source文件夹。 <br>
4）在全部jar包中查找tests.jar，并剪切到_test文件夹。</p>



<h3 id="2-eclipse准备">2 eclipse准备</h3>

<p>1）配置HADOOP_HOME环境变量 <br>
2）采用hadoop编译后的bin 、lib两个文件夹（如果不生效，重新启动eclipse） <br>
3）创建第一个java工程</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HdfsClientDemo1</span> {</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">static</span> <span class="hljs-keyword">void</span> <span class="hljs-title">main</span>(String[] args) <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 1 获取文件系统</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();
        <span class="hljs-comment">// 配置在集群上运行</span>
        configuration.set(<span class="hljs-string">"fs.defaultFS"</span>, <span class="hljs-string">"hdfs://linux01:9000"</span>);
        FileSystem fileSystem = FileSystem.get(configuration);

        <span class="hljs-comment">// 直接配置访问集群的路径和访问集群的用户名称</span>
        <span class="hljs-comment">// FileSystem fileSystem = FileSystem.get(new URI("hdfs://linux01:9000"),configuration, "atguigu");</span>

        <span class="hljs-comment">// 2 把本地文件上传到文件系统中</span>
        fileSystem.copyFromLocalFile(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"f:/hello.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/hello1.copy.txt"</span>));

        <span class="hljs-comment">// 3 关闭资源</span>
        fileSystem.close();
        System.out.println(<span class="hljs-string">"over"</span>);
    }
}
</code></pre>

<p>4）执行程序 <br>
运行时需要配置用户名称 <br>
<img src="https://img-blog.csdn.net/20171128201539741" alt="这里写图片描述" title=""> <br>
客户端去操作hdfs时，是有一个用户身份的。默认情况下，hdfs客户端api会从jvm中获取一个参数来作为自己的用户身份：-DHADOOP_USER_NAME=admin，admin为用户名称。</p>



<h2 id="2-通过api操作hdfs">2、 通过API操作HDFS</h2>



<h3 id="1-hdfs获取文件系统">1  HDFS获取文件系统</h3>

<p>1）详细代码</p>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">initHDFS</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        <span class="hljs-comment">// new Configuration();的时候，它就会去加载jar包中的hdfs-default.xml</span>
        <span class="hljs-comment">// 然后再加载classpath下的hdfs-site.xml</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        <span class="hljs-comment">// 2 设置参数 </span>
        <span class="hljs-comment">// 参数优先级： 1、客户端代码中设置的值  2、classpath下的用户自定义配置文件 3、然后是服务器的默认配置</span>
<span class="hljs-comment">//      configuration.set("fs.defaultFS", "hdfs://linux01:9000");</span>
        configuration.set(<span class="hljs-string">"dfs.replication"</span>, <span class="hljs-string">"3"</span>);

        <span class="hljs-comment">// 3 获取文件系统</span>
        FileSystem fs = FileSystem.get(configuration);

        <span class="hljs-comment">// 4 打印文件系统</span>
        System.out.println(fs.toString());
    }</code></pre>

<p>2）将core-site.xml拷贝到项目的根目录下</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-pi">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
<span class="hljs-comment">&lt;!-- 指定HDFS中NameNode的地址 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://linux01:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

    <span class="hljs-comment">&lt;!-- 指定hadoop运行时产生文件的存储目录 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/module/hadoop-2.7.2/data/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>



<h3 id="2-hdfs文件上传">2 HDFS文件上传</h3>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">putFileToHDFS</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);

        <span class="hljs-comment">// 2 创建要上传文件所在的本地路径</span>
        Path src = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"e:/hello.txt"</span>);

        <span class="hljs-comment">// 3 创建要上传到hdfs的目标路径</span>
        Path dst = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://linux01:9000/user/admin/hello.txt"</span>);

        <span class="hljs-comment">// 4 拷贝文件</span>
        fs.copyFromLocalFile(src, dst);
        fs.close(); 
    }</code></pre>



<h3 id="3-hdfs文件下载">3 HDFS文件下载</h3>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getFileFromHDFS</span>() <span class="hljs-keyword">throws</span> Exception{

        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);  

    <span class="hljs-comment">//  fs.copyToLocalFile(new Path("hdfs://linux01:9000/user/admin/hello.txt"), new Path("d:/hello.txt"));</span>
        <span class="hljs-comment">// boolean delSrc 指是否将原文件删除</span>
        <span class="hljs-comment">// Path src 指要下载的文件路径</span>
        <span class="hljs-comment">// Path dst 指将文件下载到的路径</span>
        <span class="hljs-comment">// boolean useRawLocalFileSystem 是否开启文件效验</span>
        <span class="hljs-comment">// 2 下载文件</span>
        fs.copyToLocalFile(<span class="hljs-keyword">false</span>, <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://linux01:9000/user/admin/hello.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"e:/hellocopy.txt"</span>), <span class="hljs-keyword">true</span>);
        fs.close();
    }</code></pre>



<h3 id="4-hdfs目录创建">4 HDFS目录创建</h3>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mkdirAtHDFS</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);  

        <span class="hljs-comment">//2 创建目录</span>
        fs.mkdirs(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://linux01:9000/user/admin/output"</span>));
    }</code></pre>



<h3 id="5-hdfs文件夹删除">5 HDFS文件夹删除</h3>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">deleteAtHDFS</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);  

        <span class="hljs-comment">//2 删除文件夹 ，如果是非空文件夹，参数2必须给值true</span>
        fs.delete(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://linux01:9000/user/admin/output"</span>), <span class="hljs-keyword">true</span>);
    }</code></pre>



<h3 id="6-hdfs文件名更改">6 HDFS文件名更改</h3>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">renameAtHDFS</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);

        <span class="hljs-comment">//2 重命名文件或文件夹</span>
        fs.rename(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://linux01:9000/user/admin/hello.txt"</span>), <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://linux01:9000/user/admin/hellonihao.txt"</span>));
    }</code></pre>



<h3 id="7-hdfs文件详情查看">7 HDFS文件详情查看</h3>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readListFiles</span>() <span class="hljs-keyword">throws</span> Exception {
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);

        <span class="hljs-comment">// 思考：为什么返回迭代器，而不是List之类的容器</span>
        RemoteIterator&lt;LocatedFileStatus&gt; listFiles = fs.listFiles(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>), <span class="hljs-keyword">true</span>);

        <span class="hljs-keyword">while</span> (listFiles.hasNext()) {
            LocatedFileStatus fileStatus = listFiles.next();

            System.out.println(fileStatus.getPath().getName());
            System.out.println(fileStatus.getBlockSize());
            System.out.println(fileStatus.getPermission());
            System.out.println(fileStatus.getLen());

            BlockLocation[] blockLocations = fileStatus.getBlockLocations();

            <span class="hljs-keyword">for</span> (BlockLocation bl : blockLocations) {

                System.out.println(<span class="hljs-string">"block-offset:"</span> + bl.getOffset());

                String[] hosts = bl.getHosts();

                <span class="hljs-keyword">for</span> (String host : hosts) {
                    System.out.println(host);
                }
            }

            System.out.println(<span class="hljs-string">"--------------分割线--------------"</span>);
        }
    }</code></pre>



<h3 id="8-hdfs文件夹查看">8 HDFS文件夹查看</h3>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-annotation">@Test</span>
<span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">findAtHDFS</span>() <span class="hljs-keyword">throws</span> Exception, IllegalArgumentException, IOException{

    <span class="hljs-comment">// 1 创建配置信息对象</span>
    Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

    FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);

    <span class="hljs-comment">// 2 获取查询路径下的文件状态信息</span>
    FileStatus[] listStatus = fs.listStatus(<span class="hljs-keyword">new</span> Path(<span class="hljs-string">"/"</span>));

    <span class="hljs-comment">// 3 遍历所有文件状态</span>
    <span class="hljs-keyword">for</span> (FileStatus status : listStatus) {
        <span class="hljs-keyword">if</span> (status.isFile()) {
            System.out.println(<span class="hljs-string">"f--"</span> + status.getPath().getName());
        } <span class="hljs-keyword">else</span> {
            System.out.println(<span class="hljs-string">"d--"</span> + status.getPath().getName());
        }
    }
}</code></pre>



<h2 id="3-通过io流操作hdfs">3、 通过IO流操作HDFS</h2>



<h3 id="1-hdfs文件上传">1 HDFS文件上传</h3>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">putFileToHDFS</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);

        <span class="hljs-comment">// 2 创建输入流</span>
        FileInputStream inStream = <span class="hljs-keyword">new</span> FileInputStream(<span class="hljs-keyword">new</span> File(<span class="hljs-string">"e:/hello.txt"</span>));

        <span class="hljs-comment">// 3 获取输出路径</span>
        String putFileName = <span class="hljs-string">"hdfs://linux01:9000/user/admin/hello1.txt"</span>;
        Path writePath = <span class="hljs-keyword">new</span> Path(putFileName);

        <span class="hljs-comment">// 4 创建输出流</span>
        FSDataOutputStream outStream = fs.create(writePath);

        <span class="hljs-comment">// 5 流对接</span>
        <span class="hljs-keyword">try</span>{
            IOUtils.copyBytes(inStream, outStream, <span class="hljs-number">4096</span>, <span class="hljs-keyword">false</span>);
        }<span class="hljs-keyword">catch</span>(Exception e){
            e.printStackTrace();
        }<span class="hljs-keyword">finally</span>{
            IOUtils.closeStream(inStream);
            IOUtils.closeStream(outStream);
        }
    }</code></pre>



<h3 id="2-hdfs文件下载">2  HDFS文件下载</h3>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">getFileToHDFS</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>),configuration, <span class="hljs-string">"admin"</span>);

        <span class="hljs-comment">// 2 获取读取文件路径</span>
        String filename = <span class="hljs-string">"hdfs://linux01:9000/user/admin/hello1.txt"</span>;

        <span class="hljs-comment">// 3 创建读取path</span>
        Path readPath = <span class="hljs-keyword">new</span> Path(filename);

        <span class="hljs-comment">// 4 创建输入流</span>
        FSDataInputStream inStream = fs.open(readPath);

        <span class="hljs-comment">// 5 流对接输出到控制台</span>
        <span class="hljs-keyword">try</span>{
            IOUtils.copyBytes(inStream, System.out, <span class="hljs-number">4096</span>, <span class="hljs-keyword">false</span>);
        }<span class="hljs-keyword">catch</span>(Exception e){
            e.printStackTrace();
        }<span class="hljs-keyword">finally</span>{
            IOUtils.closeStream(inStream);
        }
    }</code></pre>



<h3 id="3-定位文件读取">3 定位文件读取</h3>

<p>1）下载第一块</p>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-comment">// 定位下载第一块内容</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readFileSeek1</span>() <span class="hljs-keyword">throws</span> Exception {

        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>), configuration, <span class="hljs-string">"admin"</span>);

        <span class="hljs-comment">// 2 获取输入流路径</span>
        Path path = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://linux01:9000/user/admin/tmp/hadoop-2.7.2.tar.gz"</span>);

        <span class="hljs-comment">// 3 打开输入流</span>
        FSDataInputStream fis = fs.open(path);

        <span class="hljs-comment">// 4 创建输出流</span>
        FileOutputStream fos = <span class="hljs-keyword">new</span> FileOutputStream(<span class="hljs-string">"e:/hadoop-2.7.2.tar.gz.part1"</span>);

        <span class="hljs-comment">// 5 流对接</span>
        <span class="hljs-keyword">byte</span>[] buf = <span class="hljs-keyword">new</span> <span class="hljs-keyword">byte</span>[<span class="hljs-number">1024</span>];
        <span class="hljs-keyword">for</span> (<span class="hljs-keyword">int</span> i = <span class="hljs-number">0</span>; i &lt; <span class="hljs-number">128</span> * <span class="hljs-number">1024</span>; i++) {
            fis.read(buf);
            fos.write(buf);
        }

        <span class="hljs-comment">// 6 关闭流</span>
        IOUtils.closeStream(fis);
        IOUtils.closeStream(fos);
    }</code></pre>

<p>2）下载第二块</p>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-comment">// 定位下载第二块内容</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">readFileSeek2</span>() <span class="hljs-keyword">throws</span> Exception{

        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();

        FileSystem fs = FileSystem.get(<span class="hljs-keyword">new</span> URI(<span class="hljs-string">"hdfs://linux01:9000"</span>), configuration, <span class="hljs-string">"admin"</span>);

        <span class="hljs-comment">// 2 获取输入流路径</span>
        Path path = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://linux01:9000/user/admin/tmp/hadoop-2.7.2.tar.gz"</span>);

        <span class="hljs-comment">// 3 打开输入流</span>
        FSDataInputStream fis = fs.open(path);

        <span class="hljs-comment">// 4 创建输出流</span>
        FileOutputStream fos = <span class="hljs-keyword">new</span> FileOutputStream(<span class="hljs-string">"e:/hadoop-2.7.2.tar.gz.part2"</span>);

        <span class="hljs-comment">// 5 定位偏移量（第二块的首位）</span>
        fis.seek(<span class="hljs-number">1024</span> * <span class="hljs-number">1024</span> * <span class="hljs-number">128</span>);

        <span class="hljs-comment">// 6 流对接</span>
        IOUtils.copyBytes(fis, fos, <span class="hljs-number">1024</span>);

        <span class="hljs-comment">// 7 关闭流</span>
        IOUtils.closeStream(fis);
        IOUtils.closeStream(fos);
    }</code></pre>

<p>3）合并文件 <br>
在window命令窗口中执行 <br>
<code>type hadoop-2.7.2.tar.gz.part2 &gt;&gt; hadoop-2.7.2.tar.gz.part1</code></p>



<h1 id="四-hdfs的数据流">四、 HDFS的数据流</h1>



<h2 id="1-hdfs写数据流程">1、 HDFS写数据流程</h2>



<h3 id="1-剖析文件写入">1 剖析文件写入</h3>

<p><img src="https://img-blog.csdn.net/20171130144010803" alt="这里写图片描述" title=""></p>

<p>1）客户端向namenode请求上传文件，namenode检查目标文件是否已存在，父目录是否存在。 <br>
2）namenode返回是否可以上传。 <br>
3）客户端请求第一个 block上传到哪几个datanode服务器上。 <br>
4）namenode返回3个datanode节点，分别为dn1、dn2、dn3。 <br>
5）客户端请求dn1上传数据，dn1收到请求会继续调用dn2，然后dn2调用dn3，将这个通信管道建立完成 <br>
6）dn1、dn2、dn3逐级应答客户端 <br>
7）客户端开始往dn1上传第一个block（先从磁盘读取数据放到一个本地内存缓存），以packet为单位，dn1收到一个packet就会传给dn2，dn2传给dn3；dn1每传一个packet会放入一个应答队列等待应答 <br>
8）当一个block传输完成之后，客户端再次请求namenode上传第二个block的服务器。（重复执行3-7步）</p>



<h3 id="2-网络拓扑概念">2 网络拓扑概念</h3>

<p>在本地网络中，两个节点被称为“彼此近邻”是什么意思？在海量数据处理中，其主要限制因素是节点之间数据的传输速率——带宽很稀缺。这里的想法是将两个节点间的带宽作为距离的衡量标准。</p>

<p>节点距离：两个节点到达最近的共同祖先的距离总和。</p>

<p>例如，假设有数据中心d1机架r1中的节点n1。该节点可以表示为/d1/r1/n1。利用这种标记，这里给出四种距离描述。 <br>
Distance(/d1/r1/n1, /d1/r1/n1)=0（同一节点上的进程） <br>
Distance(/d1/r1/n1, /d1/r1/n2)=2（同一机架上的不同节点） <br>
Distance(/d1/r1/n1, /d1/r3/n2)=4（同一数据中心不同机架上的节点） <br>
Distance(/d1/r1/n1, /d2/r4/n2)=6（不同数据中心的节点）</p>

<p><img src="https://img-blog.csdn.net/20171130145747451" alt="这里写图片描述" title=""></p>



<h3 id="3-机架感知副本节点选择">3 机架感知（副本节点选择）</h3>

<p>1）官方ip地址： <br>
<a href="http://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/RackAwareness.html" rel="nofollow" target="_blank">http://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-common/RackAwareness.html</a> <br>
<a href="http://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Data_Replication" rel="nofollow" target="_blank">http://hadoop.apache.org/docs/r2.7.2/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Data_Replication</a> <br>
2）低版本Hadoop副本节点选择 <br>
第一个副本在client所处的节点上。如果客户端在集群外，随机选一个。 <br>
第二个副本和第一个副本位于不相同机架的随机节点上。 <br>
第三个副本和第二个副本位于相同机架，节点随机。</p>

<p><img src="https://img-blog.csdn.net/20171130145923233" alt="这里写图片描述" title=""></p>

<p>3）Hadoop2.7.2副本节点选择 <br>
    第一个副本在client所处的节点上。如果客户端在集群外，随机选一个。 <br>
    第二个副本和第一个副本位于相同机架，随机节点。 <br>
    第三个副本位于不同机架，随机节点。 <br>
<img src="https://img-blog.csdn.net/20171130150006450" alt="这里写图片描述" title=""></p>



<h2 id="2-hdfs读数据流程">2、 HDFS读数据流程</h2>

<p><img src="https://img-blog.csdn.net/20171130151528598" alt="这里写图片描述" title=""></p>

<p>1）客户端向namenode请求下载文件，namenode通过查询元数据，找到文件块所在的datanode地址。 <br>
2）挑选一台datanode（就近原则，然后随机）服务器，请求读取数据。 <br>
3）datanode开始传输数据给客户端（从磁盘里面读取数据放入流，以packet为单位来做校验）。 <br>
4）客户端以packet为单位接收，先在本地缓存，然后写入目标文件。</p>



<h2 id="3-一致性模型">3、 一致性模型</h2>

<p>1）debug调试如下代码</p>



<pre class="prettyprint"><code class="language-java hljs ">    <span class="hljs-annotation">@Test</span>
    <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">writeFile</span>() <span class="hljs-keyword">throws</span> Exception{
        <span class="hljs-comment">// 1 创建配置信息对象</span>
        Configuration configuration = <span class="hljs-keyword">new</span> Configuration();
        fs = FileSystem.get(configuration);

        <span class="hljs-comment">// 2 创建文件输出流</span>
        Path path = <span class="hljs-keyword">new</span> Path(<span class="hljs-string">"hdfs://hadoop102:9000/user/atguigu/hello.txt"</span>);
        FSDataOutputStream fos = fs.create(path);

        <span class="hljs-comment">// 3 写数据</span>
        fos.write(<span class="hljs-string">"hello"</span>.getBytes());
        <span class="hljs-comment">// 4 一致性刷新</span>
        fos.hflush();

        fos.close();
    }
</code></pre>

<p>2）总结 <br>
写入数据时，如果希望数据被其他client立即可见，调用如下方法</p>



<pre class="prettyprint"><code class="language-java hljs ">FsDataOutputStream. hflush ();      <span class="hljs-comment">//清理客户端缓冲区数据，被其他client立即可见</span></code></pre>



<h1 id="五-namenode工作机制">五、 NameNode工作机制</h1>



<h2 id="1-51-namenodesecondary-namenode工作机制">1、 5.1 NameNode&amp;Secondary NameNode工作机制</h2>

<p><img src="https://img-blog.csdn.net/20171130162526739" alt="这里写图片描述" title=""></p>

<p>1）第一阶段：namenode启动 <br>
（1）第一次启动namenode格式化后，创建fsimage和edits文件。如果不是第一次启动，直接加载编辑日志和镜像文件到内存。 <br>
（2）客户端对元数据进行增删改的请求 <br>
（3）namenode记录操作日志，更新滚动日志。 <br>
（4）namenode在内存中对数据进行增删改查 <br>
2）第二阶段：Secondary NameNode工作 <br>
    （1）Secondary NameNode询问namenode是否需要checkpoint。直接带回namenode是否检查结果。 <br>
    （2）Secondary NameNode请求执行checkpoint。 <br>
    （3）namenode滚动正在写的edits日志 <br>
    （4）将滚动前的编辑日志和镜像文件拷贝到Secondary NameNode <br>
    （5）Secondary NameNode加载编辑日志和镜像文件到内存，并合并。 <br>
    （6）生成新的镜像文件fsimage.chkpoint <br>
    （7）拷贝fsimage.chkpoint到namenode <br>
    （8）namenode将fsimage.chkpoint重新命名成fsimage <br>
3）web端访问SecondaryNameNode <br>
    （1）启动集群 <br>
    （2）浏览器中输入：<a href="http://linux01:50090/status.html" rel="nofollow" target="_blank">http://linux01:50090/status.html</a> <br>
    （3）查看SecondaryNameNode信息</p>

<p><img src="https://img-blog.csdn.net/20171130163050970" alt="这里写图片描述" title=""></p>

<p>4）chkpoint检查时间参数设置 <br>
（1）通常情况下，SecondaryNameNode每隔一小时执行一次。 <br>
    [hdfs-default.xml]</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.checkpoint.period<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>3600<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
</code></pre>

<p>（2）一分钟检查一次操作次数，当操作次数达到1百万时，SecondaryNameNode执行一次。</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.checkpoint.txns<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1000000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>操作动作次数<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.checkpoint.check.period<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>60<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span> 1分钟检查一次操作次数<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
</code></pre>



<h2 id="2-镜像文件和编辑日志文件">2、 镜像文件和编辑日志文件</h2>

<p>1）概念 <br>
…   namenode被格式化之后，将在/opt/module/hadoop-2.7.2/data/tmp/dfs/name/current目录中产生如下文件</p>



<pre class="prettyprint"><code class=" hljs avrasm">edits_0000000000000000000
fsimage_0000000000000000000<span class="hljs-preprocessor">.md</span>5
seen_txid
VERSION</code></pre>

<p>（1）Fsimage文件：HDFS文件系统元数据的一个永久性的检查点，其中包含HDFS文件系统的所有目录和文件idnode的序列化信息。  <br>
（2）Edits文件：存放HDFS文件系统的所有更新操作的路径，文件系统客户端执行的所有写操作首先会被记录到edits文件中。  <br>
（3）seen_txid文件保存的是一个数字，就是最后一个edits_的数字 <br>
（4）每次Namenode启动的时候都会将fsimage文件读入内存，并从00001开始到seen_txid中记录的数字依次执行每个edits里面的更新操作，保证内存中的元数据信息是最新的、同步的，可以看成Namenode启动的时候就将fsimage和edits文件进行了合并。 <br>
2）oiv查看fsimage文件 <br>
（1）查看oiv和oev命令</p>



<pre class="prettyprint"><code class=" hljs applescript">[atguigu@hadoop102 current]$ hdfs
oiv                  apply <span class="hljs-keyword">the</span> offline fsimage viewer <span class="hljs-keyword">to</span> an fsimage
oev                  apply <span class="hljs-keyword">the</span> offline edits viewer <span class="hljs-keyword">to</span> an edits <span class="hljs-type">file</span></code></pre>

<p>（2）基本语法 <br>
<code>hdfs oiv -p 文件类型 -i镜像文件 -o 转换后文件输出路径</code> <br>
（3）案例实操</p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> current]<span class="hljs-variable">$ </span>pwd
/opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">data</span>/<span class="hljs-title">tmp</span>/<span class="hljs-title">dfs</span>/<span class="hljs-title">name</span>/<span class="hljs-title">current</span></span>

[admin<span class="hljs-variable">@linux01</span> current]<span class="hljs-variable">$ </span>hdfs oiv -p <span class="hljs-constant">XML</span> -i fsimage_0000000000000000025 -o /opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">fsimage</span>.<span class="hljs-title">xml</span></span>

[admin<span class="hljs-variable">@linux01</span> current]<span class="hljs-variable">$ </span>cat /opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">fsimage</span>.<span class="hljs-title">xml</span></span></code></pre>

<p>将显示的xml文件内容拷贝到eclipse中创建的xml文件中，并格式化。 <br>
3）oev查看edits文件 <br>
（1）基本语法 <br>
<code>hdfs oev -p 文件类型 -i编辑日志 -o 转换后文件输出路径</code> <br>
（2）案例实操</p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> current]<span class="hljs-variable">$ </span>hdfs oev -p <span class="hljs-constant">XML</span> -i edits_0000000000000000012-<span class="hljs-number">0000000000000000013</span> -o /opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">edits</span>.<span class="hljs-title">xml</span></span>
[admin<span class="hljs-variable">@linux01</span> current]<span class="hljs-variable">$ </span>cat /opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">edits</span>.<span class="hljs-title">xml</span></span></code></pre>

<p>将显示的xml文件内容拷贝到eclipse中创建的xml文件中，并格式化。</p>



<h2 id="3-滚动编辑日志">3、 滚动编辑日志</h2>

<p>正常情况HDFS文件系统有更新操作时，就会滚动编辑日志。也可以用命令强制滚动编辑日志。 <br>
1）滚动编辑日志（前提必须启动集群）</p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> current]<span class="hljs-variable">$ </span>hdfs dfsadmin -rollEdits</code></pre>

<p>2）镜像文件什么时候产生 <br>
Namenode启动时加载镜像文件和编辑日志</p>

<p><img src="https://img-blog.csdn.net/20171130170437627" alt="这里写图片描述" title=""></p>



<h2 id="4-namenode版本号">4、 namenode版本号</h2>

<p>1）查看namenode版本号 <br>
在/opt/module/hadoop-2.7.2/data/tmp/dfs/name/current这个目录下查看VERSION</p>



<pre class="prettyprint"><code class=" hljs ini"><span class="hljs-setting">namespaceID=<span class="hljs-value"><span class="hljs-number">1933630176</span></span></span>
<span class="hljs-setting">clusterID=<span class="hljs-value">CID-<span class="hljs-number">1</span>f2bf8d1-<span class="hljs-number">5</span>ad2-<span class="hljs-number">4202</span>-af1c-<span class="hljs-number">6713</span>ab381175</span></span>
<span class="hljs-setting">cTime=<span class="hljs-value"><span class="hljs-number">0</span></span></span>
<span class="hljs-setting">storageType=<span class="hljs-value">NAME_NODE</span></span>
<span class="hljs-setting">blockpoolID=<span class="hljs-value">BP-<span class="hljs-number">97847618</span>-<span class="hljs-number">192.168</span>.<span class="hljs-number">10.102</span>-<span class="hljs-number">1493726072779</span></span></span>
<span class="hljs-setting">layoutVersion=<span class="hljs-value">-<span class="hljs-number">63</span></span></span></code></pre>

<p>2）namenode版本号具体解释 <br>
（1） namespaceID在HDFS上，会有多个Namenode，所以不同Namenode的namespaceID是不同的，分别管理一组blockpoolID。 <br>
（2）clusterID集群id，全局唯一 <br>
（3）cTime属性标记了namenode存储系统的创建时间，对于刚刚格式化的存储系统，这个属性为0；但是在文件系统升级之后，该值会更新到新的时间戳。 <br>
（4）storageType属性说明该存储目录包含的是namenode的数据结构。 <br>
（5）blockpoolID：一个block pool id标识一个block pool，并且是跨集群的全局唯一。当一个新的Namespace被创建的时候(format过程的一部分)会创建并持久化一个唯一ID。在创建过程构建全局唯一的BlockPoolID比人为的配置更可靠一些。NN将BlockPoolID持久化到磁盘中，在后续的启动过程中，会再次load并使用。 <br>
（6）layoutVersion是一个负整数。通常只有HDFS增加新特性时才会更新这个版本号。</p>



<h2 id="5-secondarynamenode目录结构">5、 SecondaryNameNode目录结构</h2>

<p>Secondary NameNode用来监控HDFS状态的辅助后台程序，每隔一段时间获取HDFS元数据的快照。 <br>
在/opt/modules/hadoop-2.7.2/data/tmp/dfs/namesecondary/current这个目录中查看SecondaryNameNode目录结构。</p>



<pre class="prettyprint"><code class=" hljs avrasm">edits_0000000000000000001-<span class="hljs-number">0000000000000000002</span>
fsimage_0000000000000000002
fsimage_0000000000000000002<span class="hljs-preprocessor">.md</span>5
VERSION</code></pre>

<p>SecondaryNameNode的namesecondary/current目录和主namenode的current目录的布局相同。</p>

<p>好处：在主namenode发生故障时（假设没有及时备份数据），可以从SecondaryNameNode恢复数据。</p>

<p>方法一：将SecondaryNameNode中数据拷贝到namenode存储数据的目录；</p>

<p>方法二：使用-importCheckpoint选项启动namenode守护进程，从而将SecondaryNameNode用作新的主namenode。</p>

<p>1）案例实操（一）： <br>
模拟namenode故障，并采用方法一，恢复namenode数据 <br>
（1）kill -9 namenode进程 <br>
（2）删除namenode存储的数据（/opt/module/hadoop-2.7.2/data/tmp/dfs/name） <br>
<code>rm -rf /opt/module/hadoop-2.7.2/data/tmp/dfs/name/*</code> <br>
（3）拷贝SecondaryNameNode中数据到原namenode存储数据目录 <br>
        <code>cp -R /opt/module/hadoop-2.7.2/data/tmp/dfs/namesecondary/* /opt/module/hadoop-2.7.2/data/tmp/dfs/name/</code> <br>
（4）重新启动namenode <br>
<code>sbin/hadoop-daemon.sh start namenode</code> <br>
2）案例实操（二）： <br>
模拟namenode故障，并采用方法二，恢复namenode数据 <br>
（0）修改hdfs-site.xml中的</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.checkpoint.period<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>120<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/module/hadoop-2.7.2/data/tmp/dfs/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
</code></pre>

<p>（1）kill -9 namenode进程 <br>
（2）删除namenode存储的数据（/opt/module/hadoop-2.7.2/data/tmp/dfs/name） <br>
<code>rm -rf /opt/module/hadoop-2.7.2/data/tmp/dfs/name/*</code> <br>
（3）如果SecondaryNameNode不和Namenode在一个主机节点上，需要将SecondaryNameNode存储数据的目录拷贝到Namenode存储数据的平级目录。</p>



<pre class="prettyprint"><code class=" hljs ruby">[atguigu<span class="hljs-variable">@hadoop102</span> dfs]<span class="hljs-variable">$ </span>pwd
/opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">data</span>/<span class="hljs-title">tmp</span>/<span class="hljs-title">dfs</span></span>
[atguigu<span class="hljs-variable">@hadoop102</span> dfs]<span class="hljs-variable">$ </span>ls
data  name  namesecondary</code></pre>

<p>（4）导入检查点数据（等待一会ctrl+c结束掉） <br>
<code>bin/hdfs namenode -importCheckpoint</code> <br>
（5）启动namenode <br>
<code>sbin/hadoop-daemon.sh start namenode</code> <br>
（6）如果提示文件锁了，可以删除in_use.lock  <br>
<code>rm -rf /opt/module/hadoop-2.7.2/data/tmp/dfs/namesecondary/in_use.lock</code></p>



<h2 id="6-集群安全模式操作">6、 集群安全模式操作</h2>

<p>1）概述 <br>
Namenode启动时，首先将映像文件（fsimage）载入内存，并执行编辑日志（edits）中的各项操作。一旦在内存中成功建立文件系统元数据的映像，则创建一个新的fsimage文件和一个空的编辑日志。此时，namenode开始监听datanode请求。但是此刻，namenode运行在安全模式，即namenode的文件系统对于客户端来说是只读的。</p>

<p>系统中的数据块的位置并不是由namenode维护的，而是以块列表的形式存储在datanode中。在系统的正常操作期间，namenode会在内存中保留所有块位置的映射信息。在安全模式下，各个datanode会向namenode发送最新的块列表信息，namenode了解到足够多的块位置信息之后，即可高效运行文件系统。</p>

<p>如果满足“最小副本条件”，namenode会在30秒钟之后就退出安全模式。所谓的最小副本条件指的是在整个文件系统中99.9%的块满足最小副本级别（默认值：dfs.replication.min=1）。在启动一个刚刚格式化的HDFS集群时，因为系统中还没有任何块，所以namenode不会进入安全模式。</p>

<p>2）基本语法 <br>
集群处于安全模式，不能执行重要操作（写操作）。集群启动完成后，自动退出安全模式。</p>

<p>（1）<code>bin/hdfs dfsadmin -safemode get</code>        （功能描述：查看安全模式状态） <br>
（2）<code>bin/hdfs dfsadmin -safemode enter</code>  （功能描述：进入安全模式状态） <br>
（3）<code>bin/hdfs dfsadmin -safemode leave</code>  （功能描述：离开安全模式状态） <br>
（4）<code>bin/hdfs dfsadmin -safemode wait</code>   （功能描述：等待安全模式状态） <br>
3）案例 <br>
    模拟等待安全模式 <br>
    1）先进入安全模式 <br>
<code>bin/hdfs dfsadmin -safemode enter</code> <br>
    2）执行下面的脚本 <br>
编辑一个脚本</p>



<pre class="prettyprint"><code class="language-shell hljs bash"><span class="hljs-shebang">#!/bin/bash</span>
bin/hdfs dfsadmin -safemode wait
bin/hdfs dfs -put ~/hello.txt /root/hello.txt</code></pre>

<pre><code>3）再打开一个窗口，执行
</code></pre>

<p><code>bin/hdfs dfsadmin -safemode leave</code></p>



<h2 id="7-namenode多目录配置">7、 Namenode多目录配置</h2>

<p>1）namenode的本地目录可以配置成多个，且每个目录存放内容相同，增加了可靠性。 <br>
2）具体配置如下： <br>
    <strong>hdfs-site.xml</strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:///${hadoop.tmp.dir}/dfs/name1,file:///${hadoop.tmp.dir}/dfs/name2<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>



<h1 id="六-datanode工作机制">六、 DataNode工作机制</h1>

<h2 id="1-datanode工作机制">1、 DataNode工作机制</h2>

<p><img src="https://img-blog.csdn.net/20171205195000736" alt="这里写图片描述" title=""></p>

<p>1）一个数据块在datanode上以文件形式存储在磁盘上，包括两个文件，一个是数据本身，一个是元数据包括数据块的长度，块数据的校验和，以及时间戳。 <br>
2）DataNode启动后向namenode注册，通过后，周期性（1小时）的向namenode上报所有的块信息。 <br>
3）心跳是每3秒一次，心跳返回结果带有namenode给该datanode的命令如复制块数据到另一台机器，或删除某个数据块。如果超过10分钟没有收到某个datanode的心跳，则认为该节点不可用。 <br>
4）集群运行中可以安全加入和退出一些机器</p>

<h2 id="2-数据完整性">2、 数据完整性</h2>

<p>1）当DataNode读取block的时候，它会计算checksum <br>
2）如果计算后的checksum，与block创建时值不一样，说明block已经损坏。 <br>
3）client读取其他DataNode上的block. <br>
4）datanode在其文件创建后周期验证checksum</p>

<h2 id="3-掉线时限参数设置">3、 掉线时限参数设置</h2>

<p>datanode进程死亡或者网络故障造成datanode无法与namenode通信，namenode不会立即把该节点判定为死亡，要经过一段时间，这段时间暂称作超时时长。HDFS默认的超时时长为10分钟+30秒。如果定义超时时间为timeout，则超时时长的计算公式为：</p>

<blockquote>
  <p>timeout  = 2 * dfs.namenode.heartbeat.recheck-interval + 10 * dfs.heartbeat.interval</p>
</blockquote>

<p>而默认的dfs.namenode.heartbeat.recheck-interval 大小为5分钟，dfs.heartbeat.interval默认为3秒。</p>

<p>需要注意的是hdfs-site.xml 配置文件中的heartbeat.recheck.interval的单位为毫秒，dfs.heartbeat.interval的单位为秒。</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.heartbeat.recheck-interval<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>300000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span> dfs.heartbeat.interval <span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>3<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
</code></pre>

<h2 id="4-datanode的目录结构">4、 DataNode的目录结构</h2>

<p>和namenode不同的是，datanode的存储目录是初始阶段自动创建的，不需要额外格式化。</p>

<p>1）在/opt/modules/hadoop-2.7.2/data/tmp/dfs/data/current这个目录下查看版本号</p>

<pre class="prettyprint"><code class=" hljs makefile">[admin@linux01 current]$ cat VERSION 
<span class="hljs-constant">storageID</span>=DS-1b998a1d-71a3-43d5-82dc-c0ff3294921b
<span class="hljs-constant">clusterID</span>=CID-1f2bf8d1-5ad2-4202-af1c-6713ab381175
<span class="hljs-constant">cTime</span>=0
<span class="hljs-constant">datanodeUuid</span>=970b2daf-63b8-4e17-a514-d81741392165
<span class="hljs-constant">storageType</span>=DATA_NODE
<span class="hljs-constant">layoutVersion</span>=-56</code></pre>

<p>2）具体解释 <br>
    （1）storageID：存储id号 <br>
    （2）clusterID集群id，全局唯一 <br>
    （3）cTime属性标记了datanode存储系统的创建时间，对于刚刚格式化的存储系统，这个属性为0；但是在文件系统升级之后，该值会更新到新的时间戳。 <br>
    （4）datanodeUuid：datanode的唯一识别码 <br>
    （5）storageType：存储类型 <br>
    （6）layoutVersion是一个负整数。通常只有HDFS增加新特性时才会更新这个版本号。</p>

<p>3）在/opt/modules/hadoop-2.7.2/data/tmp/dfs/data/current/BP-97847618-192.168.10.102-1493726072779/current这个目录下查看该数据块的版本号</p>

<pre class="prettyprint"><code class=" hljs makefile">[admin@linux01 current]$ cat VERSION 
<span class="hljs-comment">#Mon May 08 16:30:19 CST 2017</span>
<span class="hljs-constant">namespaceID</span>=1933630176
<span class="hljs-constant">cTime</span>=0
<span class="hljs-constant">blockpoolID</span>=BP-97847618-192.168.10.102-1493726072779
<span class="hljs-constant">layoutVersion</span>=-56</code></pre>

<p>4）具体解释 <br>
（1）namespaceID：是datanode首次访问namenode的时候从namenode处获取的storageID对每个datanode来说是唯一的（但对于单个datanode中所有存储目录来说则是相同的），namenode可用这个属性来区分不同datanode。 <br>
（2）cTime属性标记了datanode存储系统的创建时间，对于刚刚格式化的存储系统，这个属性为0；但是在文件系统升级之后，该值会更新到新的时间戳。 <br>
（3）blockpoolID：一个block pool id标识一个block pool，并且是跨集群的全局唯一。当一个新的Namespace被创建的时候(format过程的一部分)会创建并持久化一个唯一ID。在创建过程构建全局唯一的BlockPoolID比人为的配置更可靠一些。NN将BlockPoolID持久化到磁盘中，在后续的启动过程中，会再次load并使用。 <br>
（4）layoutVersion是一个负整数。通常只有HDFS增加新特性时才会更新这个版本号。</p>

<h2 id="5-服役新数据节点">5、 服役新数据节点</h2>

<p>0）需求： <br>
随着公司业务的增长，数据量越来越大，原有的数据节点的容量已经不能满足存储数据的需求，需要在原有集群基础上动态添加新的数据节点。</p>

<p>1）环境准备 <br>
    （1）克隆一台虚拟机 <br>
    （2）修改ip地址和主机名称 <br>
    （3）修改xcall和xsync文件，增加新增节点的同步 <br>
    （4）删除原来HDFS文件系统留存的文件 <br>
        <code>/opt/modules/hadoop-2.7.2/data</code> <br>
2）服役新节点具体步骤</p>

<ul>
<li>（1）在namenode的/opt/module/hadoop-2.7.2/etc/hadoop目录下创建dfs.hosts文件</li>
</ul>

<pre class="prettyprint"><code class=" hljs ruby">    [admin<span class="hljs-variable">@linux04</span> hadoop]<span class="hljs-variable">$ </span>pwd
    /opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">etc</span>/<span class="hljs-title">hadoop</span></span>
    [admin<span class="hljs-variable">@linux04</span> hadoop]<span class="hljs-variable">$ </span>touch dfs.hosts
    [admin<span class="hljs-variable">@linux04</span> hadoop]<span class="hljs-variable">$ </span>vi dfs.hosts
    添加如下主机名称（包含新服役的节点）
    linux01
    linux02
    linux03
    linux04</code></pre>

<ul>
<li>（2）在namenode的hdfs-site.xml配置文件中增加dfs.hosts属性</li>
</ul>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.hosts<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/module/hadoop-2.7.2/etc/hadoop/dfs.hosts<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<ul>
<li>（3）刷新namenode </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>hdfs dfsadmin -refreshNodes
<span class="hljs-constant">Refresh</span> nodes successful</code></pre>

<ul>
<li>（4）更新resourcemanager节点</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>yarn rmadmin -refreshNodes
<span class="hljs-number">17</span>/<span class="hljs-number">06</span>/<span class="hljs-number">24</span> <span class="hljs-number">14</span><span class="hljs-symbol">:</span><span class="hljs-number">17</span><span class="hljs-symbol">:</span><span class="hljs-number">11</span> <span class="hljs-constant">INFO</span> client.<span class="hljs-constant">RMProxy</span><span class="hljs-symbol">:</span> <span class="hljs-constant">Connecting</span> to <span class="hljs-constant">ResourceManager</span> at linux02/<span class="hljs-number">192.168</span>.<span class="hljs-number">1.103</span><span class="hljs-symbol">:</span><span class="hljs-number">8033</span></code></pre>

<ul>
<li>（5）在namenode的slaves文件中增加新主机名称 <br>
    增加105  不需要分发 <br>
linux01 <br>
linux02 <br>
linux03 <br>
linux04</li>
<li>（6）单独命令启动新的数据节点和节点管理器</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux04</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>sbin/hadoop-daemon.sh start datanode
starting datanode, logging to /opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">logs</span>/<span class="hljs-title">hadoop</span>-<span class="hljs-title">atguigu</span>-<span class="hljs-title">datanode</span>-<span class="hljs-title">linux04</span>.<span class="hljs-title">out</span></span>
[admin<span class="hljs-variable">@linux04</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>sbin/yarn-daemon.sh start nodemanager
starting nodemanager, logging to /opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">logs</span>/<span class="hljs-title">yarn</span>-<span class="hljs-title">atguigu</span>-<span class="hljs-title">nodemanager</span>-<span class="hljs-title">linux04</span>.<span class="hljs-title">out</span></span></code></pre>

<ul>
<li>（7）在web浏览器上检查是否ok</li>
</ul>

<p>3）如果数据不均衡，可以用命令实现集群的再平衡</p>



<pre class="prettyprint"><code class=" hljs sql">[admin@linux01 sbin]$ ./<span class="hljs-operator"><span class="hljs-keyword">start</span>-balancer.sh
starting balancer, logging <span class="hljs-keyword">to</span> /opt/<span class="hljs-keyword">module</span>/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.2</span>/logs/hadoop-atguigu-balancer-linux01.out
<span class="hljs-keyword">Time</span> Stamp               Iteration#  Bytes Already Moved  Bytes <span class="hljs-keyword">Left</span> <span class="hljs-keyword">To</span> Move  Bytes Being Moved</span></code></pre>

<h2 id="6-退役旧数据节点">6、 退役旧数据节点</h2>

<p>1）在namenode的/opt/modules/hadoop-2.7.2/etc/hadoop目录下创建dfs.hosts.exclude文件</p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> hadoop]<span class="hljs-variable">$ </span>pwd
/opt/<span class="hljs-class"><span class="hljs-keyword">module</span>/<span class="hljs-title">hadoop</span>-2.7.2/<span class="hljs-title">etc</span>/<span class="hljs-title">hadoop</span></span>
[admin<span class="hljs-variable">@linux01</span> hadoop]<span class="hljs-variable">$ </span>touch dfs.hosts.exclude
[admin<span class="hljs-variable">@linux01</span> hadoop]<span class="hljs-variable">$ </span>vi dfs.hosts.exclude</code></pre>

<p>添加如下主机名称（要退役的节点） <br>
linux04 <br>
2）在namenode的hdfs-site.xml配置文件中增加dfs.hosts.exclude属性</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.hosts.exclude<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/module/hadoop-2.7.2/etc/hadoop/dfs.hosts.exclude<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>3）刷新namenode、刷新resourcemanager</p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>hdfs dfsadmin -refreshNodes
<span class="hljs-constant">Refresh</span> nodes successful
[admin<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>yarn rmadmin -refreshNodes
<span class="hljs-number">17</span>/<span class="hljs-number">06</span>/<span class="hljs-number">24</span> <span class="hljs-number">14</span><span class="hljs-symbol">:</span><span class="hljs-number">55</span><span class="hljs-symbol">:</span><span class="hljs-number">56</span> <span class="hljs-constant">INFO</span> client.<span class="hljs-constant">RMProxy</span><span class="hljs-symbol">:</span> <span class="hljs-constant">Connecting</span> to <span class="hljs-constant">ResourceManager</span> at hadoop103/<span class="hljs-number">192.168</span>.<span class="hljs-number">1.103</span><span class="hljs-symbol">:</span><span class="hljs-number">8033</span></code></pre>

<p>4）检查web浏览器，退役节点的状态为decommission in progress（退役中），说明数据节点正在复制块到其他节点。</p>

<p><img src="https://img-blog.csdn.net/20171205200602377" alt="这里写图片描述" title=""></p>

<p>5）等待退役节点状态为decommissioned（所有块已经复制完成），停止该节点及节点资源管理器。注意：如果副本数是3，服役的节点小于等于3，是不能退役成功的，需要修改副本数后才能退役。</p>

<p><img src="https://img-blog.csdn.net/20171205200628461" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux04</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>sbin/hadoop-daemon.sh stop datanode
stopping datanode
[admin<span class="hljs-variable">@linux04</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>sbin/yarn-daemon.sh stop nodemanager
stopping nodemanager</code></pre>

<p>6）从include文件中删除退役节点，再运行刷新节点的命令 <br>
（1）从namenode的dfs.hosts文件中删除退役节点linux04 <br>
    linux01 <br>
    linux02 <br>
    linux03 <br>
（2）刷新namenode，刷新resourcemanager</p>



<pre class="prettyprint"><code class=" hljs ruby">[admin<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>hdfs dfsadmin -refreshNodes
<span class="hljs-constant">Refresh</span> nodes successful
[admin<span class="hljs-variable">@linux01</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">2</span>]<span class="hljs-variable">$ </span>yarn rmadmin -refreshNodes
<span class="hljs-number">17</span>/<span class="hljs-number">06</span>/<span class="hljs-number">24</span> <span class="hljs-number">14</span><span class="hljs-symbol">:</span><span class="hljs-number">55</span><span class="hljs-symbol">:</span><span class="hljs-number">56</span> <span class="hljs-constant">INFO</span> client.<span class="hljs-constant">RMProxy</span><span class="hljs-symbol">:</span> <span class="hljs-constant">Connecting</span> to <span class="hljs-constant">ResourceManager</span> at hadoop103/<span class="hljs-number">192.168</span>.<span class="hljs-number">1.103</span><span class="hljs-symbol">:</span><span class="hljs-number">8033</span></code></pre>

<p>7）从namenode的slave文件中删除退役节点linux04 <br>
linux01 <br>
linux02 <br>
linux03 <br>
8）如果数据不均衡，可以用命令实现集群的再平衡</p>



<pre class="prettyprint"><code class=" hljs sql">[admin@linux01 hadoop-2.7.2]$ sbin/<span class="hljs-operator"><span class="hljs-keyword">start</span>-balancer.sh 
starting balancer, logging <span class="hljs-keyword">to</span> /opt/<span class="hljs-keyword">module</span>/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.2</span>/logs/hadoop-atguigu-balancer-linux01.out
<span class="hljs-keyword">Time</span> Stamp               Iteration#  Bytes Already Moved  Bytes <span class="hljs-keyword">Left</span> <span class="hljs-keyword">To</span> Move  Bytes Being Moved</span></code></pre>

<h2 id="7-datanode多目录配置">7、 Datanode多目录配置</h2>

<p>1）datanode也可以配置成多个目录，每个目录存储的数据不一样。即：数据不是副本。</p>

<p>2）具体配置如下： <br>
    <strong>hdfs-site.xml</strong></p>



<pre class="prettyprint"><code class=" hljs avrasm">&lt;property&gt;
        &lt;name&gt;dfs<span class="hljs-preprocessor">.datanode</span><span class="hljs-preprocessor">.data</span><span class="hljs-preprocessor">.dir</span>&lt;/name&gt;
        &lt;value&gt;file:///${hadoop<span class="hljs-preprocessor">.tmp</span><span class="hljs-preprocessor">.dir</span>}/dfs/data1,file:///${hadoop<span class="hljs-preprocessor">.tmp</span><span class="hljs-preprocessor">.dir</span>}/dfs/data2&lt;/value&gt;
&lt;/property&gt;</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
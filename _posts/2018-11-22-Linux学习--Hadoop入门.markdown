---
layout:     post
title:      Linux学习--Hadoop入门
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Uponz/article/details/79039032				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hadoop">Hadoop</h2>

<h3 id="获取jar包">获取jar包</h3>



<pre class="prettyprint"><code class=" hljs haml">[root@localhost Desktop]# lftp 172.25.254.250
lftp 172.25.254.250:~&gt; cd /pub/docs/hadoop
cd ok, cwd=/pub/docs/hadoop
lftp 172.25.254.250:/pub/docs/hadoop&gt; ls
-<span class="ruby">rw-rw-r--    <span class="hljs-number">1</span> <span class="hljs-number">1000</span>     <span class="hljs-number">1000</span>       <span class="hljs-number">545573</span> <span class="hljs-constant">Jun</span> <span class="hljs-number">26</span>  <span class="hljs-number">2016</span> <span class="hljs-constant">SparkBWA</span>-master.zip
</span>-<span class="ruby">rw-r--r--    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>          <span class="hljs-number">910811</span> <span class="hljs-constant">May</span> <span class="hljs-number">07</span>  <span class="hljs-number">2017</span> hadoop+<span class="hljs-constant">Zookeeper</span>.pdf
</span>-<span class="ruby">rwxr-xr-x    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>        <span class="hljs-number">63851630</span> <span class="hljs-constant">Jun</span> <span class="hljs-number">23</span>  <span class="hljs-number">2016</span> hadoop-<span class="hljs-number">1.2</span>.<span class="hljs-number">1</span>.tar.gz
</span>-<span class="ruby">rwxr-xr-x    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>        <span class="hljs-number">195257604</span> <span class="hljs-constant">Jun</span> <span class="hljs-number">23</span>  <span class="hljs-number">2016</span> hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">0</span>.tar.gz
</span>-<span class="ruby">rw-rw-r--    <span class="hljs-number">1</span> <span class="hljs-number">1000</span>     <span class="hljs-number">1000</span>     <span class="hljs-number">196015975</span> <span class="hljs-constant">Apr</span> <span class="hljs-number">29</span>  <span class="hljs-number">2016</span> hadoop-<span class="hljs-number">2.6</span>.<span class="hljs-number">4</span>.tar.gz
</span>-<span class="ruby">rw-r--r--    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>        <span class="hljs-number">214092195</span> <span class="hljs-constant">Mar</span> <span class="hljs-number">02</span>  <span class="hljs-number">2017</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.tar.gz
</span>-<span class="ruby">rw-rw-r--    <span class="hljs-number">1</span> <span class="hljs-number">1000</span>     <span class="hljs-number">1000</span>      <span class="hljs-number">4454912</span> <span class="hljs-constant">Apr</span> <span class="hljs-number">29</span>  <span class="hljs-number">2016</span> hadoop-native-<span class="hljs-number">64</span>-<span class="hljs-number">2.6</span>.<span class="hljs-number">0</span>.tar
</span>-<span class="ruby">rw-rw-r--    <span class="hljs-number">1</span> <span class="hljs-number">1000</span>     <span class="hljs-number">1000</span>     <span class="hljs-number">104497899</span> <span class="hljs-constant">Mar</span> <span class="hljs-number">05</span>  <span class="hljs-number">2017</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>-bin.tar.gz
</span>-<span class="ruby">rwxr-xr-x    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>        <span class="hljs-number">153512879</span> <span class="hljs-constant">Jun</span> <span class="hljs-number">23</span>  <span class="hljs-number">2016</span> jdk-<span class="hljs-number">7</span>u79-linux-x64.tar.gz
</span>-<span class="ruby">rw-rw-r--    <span class="hljs-number">1</span> <span class="hljs-number">1000</span>     <span class="hljs-number">1000</span>     <span class="hljs-number">29937534</span> <span class="hljs-constant">May</span> <span class="hljs-number">26</span>  <span class="hljs-number">2016</span> scala-<span class="hljs-number">2.10</span>.<span class="hljs-number">4</span>.tgz
</span>-<span class="ruby">rw-rw-r--    <span class="hljs-number">1</span> <span class="hljs-number">1000</span>     <span class="hljs-number">1000</span>     <span class="hljs-number">29928531</span> <span class="hljs-constant">Jul</span> <span class="hljs-number">07</span>  <span class="hljs-number">2016</span> scala-<span class="hljs-number">2.10</span>.<span class="hljs-number">6</span>.tgz
</span>-<span class="ruby">rwxr-xr-x    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>        <span class="hljs-number">243673720</span> <span class="hljs-constant">Jun</span> <span class="hljs-number">23</span>  <span class="hljs-number">2016</span> spark-<span class="hljs-number">1.6</span>.<span class="hljs-number">1</span>-bin-hadoop1.tgz
</span>-<span class="ruby">rw-rw-r--    <span class="hljs-number">1</span> <span class="hljs-number">1000</span>     <span class="hljs-number">1000</span>     <span class="hljs-number">289405702</span> <span class="hljs-constant">May</span> <span class="hljs-number">25</span>  <span class="hljs-number">2016</span> spark-<span class="hljs-number">1.6</span>.<span class="hljs-number">1</span>-bin-hadoop2.<span class="hljs-number">6</span>.tgz
</span>-<span class="ruby">rwxr-xr-x    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>        <span class="hljs-number">201791394</span> <span class="hljs-constant">Jun</span> <span class="hljs-number">23</span>  <span class="hljs-number">2016</span> spark-<span class="hljs-number">1.6</span>.<span class="hljs-number">1</span>-bin-without-hadoop.tgz
</span>-<span class="ruby">rwxr-xr-x    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>         <span class="hljs-number">5527120</span> <span class="hljs-constant">Jun</span> <span class="hljs-number">23</span>  <span class="hljs-number">2016</span> v2.<span class="hljs-number">10.6</span>.tar.gz
</span>-<span class="ruby">rw-r--r--    <span class="hljs-number">1</span> <span class="hljs-number">0</span>        <span class="hljs-number">0</span>        <span class="hljs-number">22724574</span> <span class="hljs-constant">Mar</span> <span class="hljs-number">07</span>  <span class="hljs-number">2017</span> zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">9</span>.tar.gz</span></code></pre>

<h3 id="安装hadoop-273targz">安装hadoop-2.7.3.tar.gz</h3>



<pre class="prettyprint"><code class=" hljs ruby">lftp <span class="hljs-number">172.25</span>.<span class="hljs-number">254.250</span><span class="hljs-symbol">:/pub/docs/hadoop&gt;</span> get hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.tar.gz
<span class="hljs-number">214092195</span> bytes transferred <span class="hljs-keyword">in</span> <span class="hljs-number">2</span> seconds (<span class="hljs-number">101.58</span>M/s) </code></pre>

<h3 id="创建普通用户lq">创建普通用户lq</h3>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># useradd -u 1001 lq</span></code></pre>

<h3 id="查看信息">查看信息</h3>



<pre class="prettyprint"><code class=" hljs ruby">[lq<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>id
uid=<span class="hljs-number">1001</span>(lq) gid=<span class="hljs-number">1001</span>(lq) groups=<span class="hljs-number">1001</span>(lq)</code></pre>

<h3 id="移动lq目录">移动lq目录</h3>



<pre class="prettyprint"><code class=" hljs coffeescript">[root<span class="hljs-property">@localhost</span> ~]<span class="hljs-comment"># mv /mnt/lq/ /home/</span>
[root<span class="hljs-property">@localhost</span> ~]<span class="hljs-comment"># df</span>
Filesystem     <span class="hljs-number">1</span>K-blocks    Used Available Use% Mounted <span class="hljs-literal">on</span>
<span class="hljs-regexp">/dev/vda1       10473900 4354732   6119168  42% /</span>
devtmpfs          <span class="hljs-number">469344</span>       <span class="hljs-number">0</span>    <span class="hljs-number">469344</span>   <span class="hljs-number">0</span>% /dev
tmpfs             <span class="hljs-number">484932</span>     <span class="hljs-number">140</span>    <span class="hljs-number">484792</span>   <span class="hljs-number">1</span>% /dev/shm
tmpfs             <span class="hljs-number">484932</span>     <span class="hljs-number">624</span>    <span class="hljs-number">484308</span>   <span class="hljs-number">1</span>% /run
tmpfs             <span class="hljs-number">484932</span>       <span class="hljs-number">0</span>    <span class="hljs-number">484932</span>   <span class="hljs-number">0</span>% /sys/fs/cgroup</code></pre>

<h3 id="切换到用户lq">切换到用户lq</h3>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># su - lq</span>
<span class="hljs-constant">Last</span> <span class="hljs-symbol">login:</span> <span class="hljs-constant">Thu</span> <span class="hljs-constant">Jan</span> <span class="hljs-number">11</span> <span class="hljs-number">15</span><span class="hljs-symbol">:</span><span class="hljs-number">23</span><span class="hljs-symbol">:</span><span class="hljs-number">00</span> <span class="hljs-constant">CST</span> <span class="hljs-number">2018</span> on pts/<span class="hljs-number">0</span>
[lq<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>ls
hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.tar.gz</code></pre>

<h3 id="解压hadoop">解压hadoop</h3>



<pre class="prettyprint"><code class=" hljs ruby">[lq<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>tar zxf hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.tar.gz 
[lq<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>ls
hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>  hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.tar.gz</code></pre>

<p>查看文件大小</p>



<pre class="prettyprint"><code class=" hljs ruby">[lq<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>du -sh hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>
<span class="hljs-number">334</span>M    hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span></code></pre>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-title">[</span><span class="hljs-comment">lq@localhost</span> <span class="hljs-comment">~</span><span class="hljs-title">]</span><span class="hljs-comment">$</span> <span class="hljs-comment">ll</span>
<span class="hljs-comment">total</span> <span class="hljs-comment">209076</span>
<span class="hljs-comment">drwxr</span><span class="hljs-literal">-</span><span class="hljs-comment">xr</span><span class="hljs-literal">-</span><span class="hljs-comment">x</span> <span class="hljs-comment">9</span> <span class="hljs-comment">lq</span>   <span class="hljs-comment">lq</span>         <span class="hljs-comment">139</span> <span class="hljs-comment">Aug</span> <span class="hljs-comment">18</span>  <span class="hljs-comment">2016</span> <span class="hljs-comment">hadoop</span><span class="hljs-literal">-</span><span class="hljs-comment">2</span><span class="hljs-string">.</span><span class="hljs-comment">7</span><span class="hljs-string">.</span><span class="hljs-comment">3</span>
<span class="hljs-literal">-</span><span class="hljs-comment">rw</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">r</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span> <span class="hljs-comment">1</span> <span class="hljs-comment">root</span> <span class="hljs-comment">root</span> <span class="hljs-comment">214092195</span> <span class="hljs-comment">Mar</span>  <span class="hljs-comment">2</span>  <span class="hljs-comment">2017</span> <span class="hljs-comment">hadoop</span><span class="hljs-literal">-</span><span class="hljs-comment">2</span><span class="hljs-string">.</span><span class="hljs-comment">7</span><span class="hljs-string">.</span><span class="hljs-comment">3</span><span class="hljs-string">.</span><span class="hljs-comment">tar</span><span class="hljs-string">.</span><span class="hljs-comment">gz</span></code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[lq<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>ls
hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>  hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.tar.gz
[lq<span class="hljs-variable">@localhost</span> ~]<span class="hljs-variable">$ </span>cd hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/
[lq<span class="hljs-variable">@localhost</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>ls
bin  <span class="hljs-keyword">include</span>  libexec      <span class="hljs-constant">NOTICE</span>.txt  sbin
etc  lib      <span class="hljs-constant">LICENSE</span>.txt  <span class="hljs-constant">README</span>.txt  share</code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[lq<span class="hljs-variable">@localhost</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>cd etc
[lq<span class="hljs-variable">@localhost</span> etc]<span class="hljs-variable">$ </span>ls
hadoop</code></pre>



<pre class="prettyprint"><code class=" hljs avrasm">[lq@localhost etc]$ cd hadoop
[lq@localhost hadoop]$ ls
capacity-scheduler<span class="hljs-preprocessor">.xml</span>      httpfs-env<span class="hljs-preprocessor">.sh</span>            mapred-env<span class="hljs-preprocessor">.sh</span>
configuration<span class="hljs-preprocessor">.xsl</span>           httpfs-log4j<span class="hljs-preprocessor">.properties</span>  mapred-queues<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.template</span>
container-executor<span class="hljs-preprocessor">.cfg</span>      httpfs-signature<span class="hljs-preprocessor">.secret</span>  mapred-site<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.template</span>
core-site<span class="hljs-preprocessor">.xml</span>               httpfs-site<span class="hljs-preprocessor">.xml</span>          slaves
hadoop-env<span class="hljs-preprocessor">.cmd</span>              kms-acls<span class="hljs-preprocessor">.xml</span>             ssl-client<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.example</span>
hadoop-env<span class="hljs-preprocessor">.sh</span>               kms-env<span class="hljs-preprocessor">.sh</span>               ssl-server<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.example</span>
hadoop-metrics2<span class="hljs-preprocessor">.properties</span>  kms-log4j<span class="hljs-preprocessor">.properties</span>     yarn-env<span class="hljs-preprocessor">.cmd</span>
hadoop-metrics<span class="hljs-preprocessor">.properties</span>   kms-site<span class="hljs-preprocessor">.xml</span>             yarn-env<span class="hljs-preprocessor">.sh</span>
hadoop-policy<span class="hljs-preprocessor">.xml</span>           log4j<span class="hljs-preprocessor">.properties</span>         yarn-site<span class="hljs-preprocessor">.xml</span>
hdfs-site<span class="hljs-preprocessor">.xml</span>               mapred-env<span class="hljs-preprocessor">.cmd</span></code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[lq<span class="hljs-variable">@localhost</span> hadoop]<span class="hljs-variable">$ </span>vim hadoop-env.sh</code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[lq<span class="hljs-variable">@localhost</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>mkdir input
[lq<span class="hljs-variable">@localhost</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>cp etc/hadoop/*.xml input
[lq<span class="hljs-variable">@localhost</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>ls input/
capacity-scheduler.xml  hadoop-policy.xml  httpfs-site.xml  kms-site.xml
core-site.xml           hdfs-site.xml      kms-acls.xml     yarn-site.xml</code></pre>



<pre class="prettyprint"><code class=" hljs ruby">[lq<span class="hljs-variable">@localhost</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.jar grep input output <span class="hljs-string">'dfs[a-z.]+'</span>

[lq<span class="hljs-variable">@localhost</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>ls
bin  <span class="hljs-keyword">include</span>  lib      <span class="hljs-constant">LICENSE</span>.txt  output      sbin
etc  input    libexec  <span class="hljs-constant">NOTICE</span>.txt   <span class="hljs-constant">README</span>.txt  share
[lq<span class="hljs-variable">@localhost</span> hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>cd output/
[lq<span class="hljs-variable">@localhost</span> output]<span class="hljs-variable">$ </span>ls
part-r-<span class="hljs-number">00000</span>  _SUCCESS
[lq<span class="hljs-variable">@localhost</span> output]<span class="hljs-variable">$ </span>cat *
<span class="hljs-number">1</span>   dfsadmin</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Linux运维：Hadoop集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="一安装配置hadoop">一、安装配置hadoop</h3>

<ul>
<li><strong>注意：hadoop的配置对内存有要求！！！</strong></li>
<li>配置<strong>hadoop、jdk（jdk辅助）</strong></li>
<li>以hadoop用户操作 </li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">[root@server1 ~]<span class="hljs-preprocessor"># ls</span>
hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>  jdk-<span class="hljs-number">7</span>u79-linux-x64<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
[root@server1 ~]<span class="hljs-preprocessor"># useradd -u 800 hadoop</span>
[root@server1 ~]<span class="hljs-preprocessor"># mv * /home/hadoop/</span>
[root@server1 ~]<span class="hljs-preprocessor"># su - hadoop</span>
[hadoop@server1 ~]$ ls
hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>  jdk-<span class="hljs-number">7</span>u79-linux-x64<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span></code></pre>

<ul>
<li>解压，配置软链接 </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>tar zxf hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.tar.gz 
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>tar zxf jdk-<span class="hljs-number">7</span>u79-linux-x64.tar.gz 
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>ln -s jdk1.<span class="hljs-number">7.0_79</span>/ java
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>ln -s hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span> hadoop</code></pre>

<ul>
<li>配置java，当jdk更新时，更改软链接即可 </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>cd hadoop
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>cd etc/hadoop/
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>vim hadoop-env.sh 
<span class="hljs-comment"># The java implementation to use.</span>
export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/home/hadoop</span><span class="hljs-regexp">/java</span></code></pre>

<ul>
<li>测试hadoop</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>pwd
/home/hadoop/hadoop
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>mkdir input
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>cp etc/hadoop/* input/
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.jar 

[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.jar grep input output <span class="hljs-string">'dfs[a-z.]+'</span>

[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>cat output/*
<span class="hljs-number">6</span>   dfs.audit.logger
<span class="hljs-number">4</span>   dfs.<span class="hljs-keyword">class</span>
<span class="hljs-number">3</span>   dfs.server.namenode.
<span class="hljs-number">2</span>   dfs.period
<span class="hljs-number">2</span>   dfs.audit.log.maxfilesize
<span class="hljs-number">2</span>   dfs.audit.log.maxbackupindex
<span class="hljs-number">1</span>   dfsmetrics.log
<span class="hljs-number">1</span>   dfsadmin
<span class="hljs-number">1</span>   dfs.servers
<span class="hljs-number">1</span>   dfs.file</code></pre>



<h3 id="二数据操作">二、数据操作</h3>



<h5 id="1配置hadoop">1、配置hadoop</h5>



<pre class="prettyprint"><code class=" hljs xml">[hadoop@server1 hadoop]$ cd etc/hadoop/
[hadoop@server1 hadoop]$ vim core-site.xml 
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://172.25.120.1:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>

[hadoop@server1 hadoop]$ vim slaves 
172.25.120.1

[hadoop@server1 hadoop]$ vim hdfs-site.xml 
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>



<h3 id="2配置ssh">2、配置ssh</h3>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>ssh-keygen 
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>cd 
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>cd .ssh/
[hadoop<span class="hljs-variable">@server1</span> .ssh]<span class="hljs-variable">$ </span>cp id_rsa.pub authorized_keys
[hadoop<span class="hljs-variable">@server1</span> .ssh]<span class="hljs-variable">$ </span>ssh localhost
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>logout
[hadoop<span class="hljs-variable">@server1</span> .ssh]<span class="hljs-variable">$ </span>ssh <span class="hljs-number">172.25</span>.<span class="hljs-number">120.1</span>
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>logout
[hadoop<span class="hljs-variable">@server1</span> .ssh]<span class="hljs-variable">$ </span>ssh server1
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>logout
[hadoop<span class="hljs-variable">@server1</span> .ssh]<span class="hljs-variable">$ </span>ssh <span class="hljs-number">0</span>.<span class="hljs-number">0</span>.<span class="hljs-number">0</span>.<span class="hljs-number">0</span>
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>logout</code></pre>



<h5 id="3启动dfs">3、启动dfs</h5>

<ul>
<li>格式化（文件存于/tmp） </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>pwd
/home/hadoop
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>cd hadoop
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs namenode -format

[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>ls /tmp/
hadoop-hadoop  hsperfdata_hadoop</code></pre>

<ul>
<li>启动dfs </li>
</ul>



<pre class="prettyprint"><code class=" hljs vhdl">[hadoop@server1 hadoop]$ sbin/start-dfs.sh 
Starting namenodes <span class="hljs-keyword">on</span> [server1]
server1: starting namenode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-namenode-server1.<span class="hljs-keyword">out</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.1</span>: starting datanode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-datanode-server1.<span class="hljs-keyword">out</span>
Starting secondary namenodes [<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>]
<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>: starting secondarynamenode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-secondarynamenode-server1.<span class="hljs-keyword">out</span></code></pre>

<ul>
<li>配置环境变量  </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>vim .bash_profile 
<span class="hljs-constant">PATH</span>=<span class="hljs-variable">$PATH</span><span class="hljs-symbol">:</span><span class="hljs-variable">$HOME</span>/<span class="hljs-symbol">bin:</span>~<span class="hljs-regexp">/java/bin</span>

[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>logout
[root<span class="hljs-variable">@server1</span> ~]<span class="hljs-comment"># su - hadoop</span>
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1896</span> <span class="hljs-constant">SecondaryNameNode</span>
<span class="hljs-number">1713</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">1620</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">2031</span> <span class="hljs-constant">Jps</span></code></pre>

<ul>
<li>处理文件系统 </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>pwd
/home/hadoop/hadoop
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs dfs -usage   <span class="hljs-comment">##查看用法</span>

[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs dfs -mkdir /user
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs dfs -mkdir /user/hadoop
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs dfs -put input/ 
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreducexamples-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.jar wordcount input output</code></pre>



<h3 id="三分布式文件存储">三、分布式文件存储</h3>



<h5 id="1namenode172251201">1、namenode（172.25.120.1）</h5>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@server1</span> ~]<span class="hljs-comment"># yum install -y nfs-utils</span>
[root<span class="hljs-variable">@server1</span> ~]<span class="hljs-comment"># /etc/init.d/rpcbind start</span>
<span class="hljs-constant">Starting</span> <span class="hljs-symbol">rpcbind:</span>                                          [  <span class="hljs-constant">OK</span>  ]
[root<span class="hljs-variable">@server1</span> ~]<span class="hljs-comment"># vim /etc/exports </span>
/home/hadoop    *(rw,anonuid=<span class="hljs-number">800</span>,anongid=<span class="hljs-number">800</span>)

[root<span class="hljs-variable">@server1</span> ~]<span class="hljs-comment"># /etc/init.d/nfs start</span>
<span class="hljs-constant">Starting</span> <span class="hljs-constant">NFS</span> <span class="hljs-symbol">services:</span>                                     [  <span class="hljs-constant">OK</span>  ]
<span class="hljs-constant">Starting</span> <span class="hljs-constant">NFS</span> <span class="hljs-symbol">mountd:</span>                                       [  <span class="hljs-constant">OK</span>  ]
<span class="hljs-constant">Starting</span> <span class="hljs-constant">NFS</span> <span class="hljs-symbol">daemon:</span>                                       [  <span class="hljs-constant">OK</span>  ]
<span class="hljs-constant">Starting</span> <span class="hljs-constant">RPC</span> <span class="hljs-symbol">idmapd:</span>                                       [  <span class="hljs-constant">OK</span>  ]

[root<span class="hljs-variable">@server1</span> ~]<span class="hljs-comment"># exportfs -v</span>
/home/hadoop    &lt;world&gt;(rw,wdelay,root_squash,no_subtree_check,anonuid=<span class="hljs-number">800</span>,anongid=<span class="hljs-number">800</span>)
[root<span class="hljs-variable">@server1</span> ~]<span class="hljs-comment"># exportfs -rv</span>
exporting *<span class="hljs-symbol">:/home/hadoop</span></code></pre>



<h5 id="2datanode172251202和172251203操作一致">2、datanode（172.25.120.2和172.25.120.3：操作一致）</h5>



<pre class="prettyprint"><code class=" hljs coffeescript">[root<span class="hljs-property">@server2</span> ~]<span class="hljs-comment"># yum install -y nfs-utils</span>
[root<span class="hljs-property">@server2</span> ~]<span class="hljs-comment"># /etc/init.d/rpcbind start</span>
Starting <span class="hljs-attribute">rpcbind</span>:                                          [  OK  ]
[root<span class="hljs-property">@server2</span> ~]<span class="hljs-comment"># useradd -u 800 hadoop</span>
[root<span class="hljs-property">@server2</span> ~]<span class="hljs-comment"># showmount -e 172.25.120.1</span>
Export list <span class="hljs-keyword">for</span> <span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.1</span>:
/home/hadoop *
[root<span class="hljs-property">@server2</span> ~]<span class="hljs-comment"># mount 172.25.120.1:/home/hadoop/ /home/hadoop/</span>
[root<span class="hljs-property">@server2</span> ~]<span class="hljs-comment"># df</span>
Filesystem                   <span class="hljs-number">1</span>K-blocks    Used Available Use% Mounted <span class="hljs-literal">on</span>
<span class="hljs-regexp">/dev/mapper/VolGroup-lv_root  19134332  936680  17225672   6% /</span>
tmpfs                           <span class="hljs-number">510200</span>       <span class="hljs-number">0</span>    <span class="hljs-number">510200</span>   <span class="hljs-number">0</span>% /dev/shm
/dev/vda1                       <span class="hljs-number">495844</span>   <span class="hljs-number">33480</span>    <span class="hljs-number">436764</span>   <span class="hljs-number">8</span>% /boot
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.1</span>:<span class="hljs-regexp">/home/hadoop/</span>    <span class="hljs-number">19134336</span> <span class="hljs-number">1968384</span>  <span class="hljs-number">16193920</span>  <span class="hljs-number">11</span>% /home/hadoop</code></pre>



<h5 id="3配置">3、配置</h5>



<pre class="prettyprint"><code class=" hljs coffeescript">[root<span class="hljs-property">@server1</span> ~]<span class="hljs-comment"># su - hadoop</span>
[hadoop<span class="hljs-property">@server1</span> ~]$ cd hadoop<span class="hljs-regexp">/etc/</span>
[hadoop<span class="hljs-property">@server1</span> etc]$ vim hadoop/slaves 
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>

[hadoop<span class="hljs-property">@server1</span> etc]$ vim hadoop/hdfs-site.xml 
&lt;configuration&gt;
    &lt;property&gt;
            &lt;name&gt;dfs.replication&lt;/name&gt;
                    &lt;value&gt;<span class="hljs-number">2</span>&lt;/value&gt;
                        &lt;/property&gt;
&lt;/configuration&gt;

[hadoop<span class="hljs-property">@server1</span> etc]$ cd <span class="hljs-regexp">/tmp/</span>
[hadoop<span class="hljs-property">@server1</span> tmp]$ rm -fr *</code></pre>

<ul>
<li>测试ssh服务</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> tmp]<span class="hljs-variable">$ </span>ssh server2
[hadoop<span class="hljs-variable">@server2</span> ~]<span class="hljs-variable">$ </span>logout
[hadoop<span class="hljs-variable">@server1</span> tmp]<span class="hljs-variable">$ </span>ssh server3
[hadoop<span class="hljs-variable">@server3</span> ~]<span class="hljs-variable">$ </span>logout
[hadoop<span class="hljs-variable">@server1</span> tmp]<span class="hljs-variable">$ </span>ssh <span class="hljs-number">172.25</span>.<span class="hljs-number">120.2</span>
[hadoop<span class="hljs-variable">@server2</span> ~]<span class="hljs-variable">$ </span>logout
[hadoop<span class="hljs-variable">@server1</span> tmp]<span class="hljs-variable">$ </span>ssh <span class="hljs-number">172.25</span>.<span class="hljs-number">120.3</span>
[hadoop<span class="hljs-variable">@server2</span> ~]<span class="hljs-variable">$ </span>logout</code></pre>

<ul>
<li>重新格式化 </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs namenode -format
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>ls /tmp/
hadoop-hadoop  hsperfdata_hadoop</code></pre>

<ul>
<li>启动dfs：namenode和datanode节点分开 </li>
</ul>



<pre class="prettyprint"><code class=" hljs vhdl">[hadoop@server1 hadoop]$ sbin/start-dfs.sh
Starting namenodes <span class="hljs-keyword">on</span> [server1]
server1: starting namenode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-namenode-server1.<span class="hljs-keyword">out</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span>: starting datanode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-datanode-server2.<span class="hljs-keyword">out</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>: starting datanode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-datanode-server3.<span class="hljs-keyword">out</span>
Starting secondary namenodes [<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>]
<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>: starting secondarynamenode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-secondarynamenode-server1.<span class="hljs-keyword">out</span></code></pre>

<ul>
<li>namenode节点： </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">4303</span> <span class="hljs-constant">SecondaryNameNode</span>
<span class="hljs-number">4115</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">4412</span> <span class="hljs-constant">Jps</span></code></pre>

<ul>
<li>datanode节点： </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@server2</span> ~]<span class="hljs-comment"># su - hadoop</span>
[hadoop<span class="hljs-variable">@server2</span> ~]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1261</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1167</span> <span class="hljs-constant">DataNode</span></code></pre>



<h5 id="4处理文件datanode实时同步">4、处理文件（datanode实时同步）</h5>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs dfs -mkdir /user
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs dfs -mkdir /user/hadoop
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs dfs -put etc/hadoop/ input
</code></pre>

<ul>
<li>访问 <a href="http://172.25.120.1:50070/explorer.html#/user/hadoop" rel="nofollow">http://172.25.120.1:50070/explorer.html#/user/hadoop</a> 查看 <br>
<img src="https://img-blog.csdn.net/20180722101519695?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0Zvcl9teXNlbGYw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
</ul>



<h3 id="四节点的添加与删除">四、节点的添加与删除</h3>



<h5 id="1在线添加server4172251204">1、在线添加server4（172.25.120.4）</h5>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-number">1</span>、在线添加server4（<span class="hljs-number">172.25</span>.<span class="hljs-number">120.4</span>）
[root<span class="hljs-variable">@server4</span> ~]<span class="hljs-comment"># yum install -y nfs-utils</span>
[root<span class="hljs-variable">@server4</span> ~]<span class="hljs-comment"># useradd -u 800 hadoop</span>
[root<span class="hljs-variable">@server4</span> ~]<span class="hljs-comment"># mount 172.25.120.1:/home/hadoop/ /home/hadoop/</span>
[root<span class="hljs-variable">@server4</span> ~]<span class="hljs-comment"># su - hadoop</span>
[hadoop<span class="hljs-variable">@server4</span> ~]<span class="hljs-variable">$ </span>vim hadoop/etc/hadoop/slaves 
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.2</span>
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.3</span>
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.4</span></code></pre>

<ul>
<li>namenode端测试ssh：</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>ssh server4
[hadoop<span class="hljs-variable">@server4</span> ~]<span class="hljs-variable">$ </span>logout
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>ssh <span class="hljs-number">172.25</span>.<span class="hljs-number">120.4</span>
[hadoop<span class="hljs-variable">@server4</span> ~]<span class="hljs-variable">$ </span>logout

[hadoop<span class="hljs-variable">@server4</span> ~]<span class="hljs-variable">$ </span>cd hadoop
[hadoop<span class="hljs-variable">@server4</span> hadoop]<span class="hljs-variable">$ </span>sbin/hadoop-daemon.sh start datanode
starting datanode, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/logs/hadoop-hadoop-datanode-server4.out
[hadoop<span class="hljs-variable">@server4</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1250</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1177</span> <span class="hljs-constant">DataNode</span></code></pre>



<h5 id="2在线删除server2172251202">2、在线删除server2（172.25.120.2）</h5>



<pre class="prettyprint"><code class=" hljs coffeescript">[hadoop<span class="hljs-property">@server1</span> hadoop]$ pwd
/home/hadoop/hadoop/etc/hadoop
[hadoop<span class="hljs-property">@server1</span> hadoop]$ vim hdfs-site.xml 
    &lt;property&gt;
        &lt;name&gt;dfs.hosts.exclude&lt;/name&gt;
        &lt;value&gt;/home/hadoop/hadoop/etc/hadoop/hosts-exclude&lt;/value&gt;
    &lt;/property&gt;

[hadoop<span class="hljs-property">@server1</span> hadoop]$ vim hosts-exclude
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span>    <span class="hljs-comment">##删除的节点IP</span>

[hadoop<span class="hljs-property">@server1</span> hadoop]$ dd <span class="hljs-keyword">if</span>=/dev/zero <span class="hljs-keyword">of</span>=bigfile bs=<span class="hljs-number">1</span>M count=<span class="hljs-number">200</span>
[hadoop<span class="hljs-property">@server1</span> hadoop]$ ../../bin/hdfs dfs -put bigfile 
[hadoop<span class="hljs-property">@server1</span> hadoop]$ vim slaves
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>

[hadoop<span class="hljs-property">@server1</span> hadoop]$ ../../bin/hdfs dfsadmin -refreshNodes <span class="hljs-comment">##刷新节点</span>
Refresh nodes successful
[hadoop<span class="hljs-property">@server1</span> hadoop]$ ../../bin/hdfs dfsadmin -report
<span class="hljs-attribute">Name</span>: <span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span>:<span class="hljs-number">50010</span> (server2)
<span class="hljs-attribute">Hostname</span>: server2
Decommission Status : Decommission <span class="hljs-keyword">in</span> progress
Configured <span class="hljs-attribute">Capacity</span>: <span class="hljs-number">19593555968</span> (<span class="hljs-number">18.25</span> GB)
DFS <span class="hljs-attribute">Used</span>: <span class="hljs-number">135581696</span> (<span class="hljs-number">129.30</span> MB)
Non DFS <span class="hljs-attribute">Used</span>: <span class="hljs-number">1954566144</span> (<span class="hljs-number">1.82</span> GB)
DFS <span class="hljs-attribute">Remaining</span>: <span class="hljs-number">17503408128</span> (<span class="hljs-number">16.30</span> GB)
DFS Used%: <span class="hljs-number">0.69</span>%
DFS Remaining%: <span class="hljs-number">89.33</span>%
Configured Cache <span class="hljs-attribute">Capacity</span>: <span class="hljs-number">0</span> (<span class="hljs-number">0</span> B)
Cache <span class="hljs-attribute">Used</span>: <span class="hljs-number">0</span> (<span class="hljs-number">0</span> B)
Cache <span class="hljs-attribute">Remaining</span>: <span class="hljs-number">0</span> (<span class="hljs-number">0</span> B)
Cache Used%: <span class="hljs-number">100.00</span>%
Cache Remaining%: <span class="hljs-number">0.00</span>%
<span class="hljs-attribute">Xceivers</span>: <span class="hljs-number">1</span>
Last <span class="hljs-attribute">contact</span>: Sat Jul <span class="hljs-number">21</span> <span class="hljs-number">14</span>:<span class="hljs-number">23</span>:<span class="hljs-number">43</span> CST <span class="hljs-number">2018</span>

<span class="hljs-comment">##出现Decommissioned时，查看 http://172.25.120.1:50070/dfshealth.html#tab-datanode</span>
<span class="hljs-attribute">Name</span>: <span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span>:<span class="hljs-number">50010</span> (server2)
<span class="hljs-attribute">Hostname</span>: server2
Decommission Status : Decommissioned</code></pre>

<ul>
<li>server2关闭节点</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server2</span> hadoop]<span class="hljs-variable">$ </span>pwd
/home/hadoop/hadoop
[hadoop<span class="hljs-variable">@server2</span> hadoop]<span class="hljs-variable">$ </span>sbin/hadoop-daemon.sh stop datanode
stopping datanode
[hadoop<span class="hljs-variable">@server2</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1497</span> <span class="hljs-constant">Jps</span></code></pre>

<ul>
<li>hadoop页面查看 <br>
<img src="https://img-blog.csdn.net/20180722102123982?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0Zvcl9teXNlbGYw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
</ul>



<h5 id="3yarn模式">3、yarn模式</h5>



<pre class="prettyprint"><code class=" hljs avrasm">[hadoop@server1 hadoop]$ pwd
/home/hadoop/hadoop/etc/hadoop
[hadoop@server1 hadoop]$ <span class="hljs-keyword">cp</span> mapred-site<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.template</span> mapred-site<span class="hljs-preprocessor">.xml</span>
[hadoop@server1 hadoop]$ vim mapred-site<span class="hljs-preprocessor">.xml</span>
    &lt;property&gt;
        &lt;name&gt;mapreduce<span class="hljs-preprocessor">.framework</span><span class="hljs-preprocessor">.name</span>&lt;/name&gt;
        &lt;value&gt;yarn&lt;/value&gt;
    &lt;/property&gt;

[hadoop@server1 hadoop]$ vim yarn-site<span class="hljs-preprocessor">.xml</span> 
    &lt;property&gt;
        &lt;name&gt;yarn<span class="hljs-preprocessor">.nodemanager</span><span class="hljs-preprocessor">.aux</span>-services&lt;/name&gt;
        &lt;value&gt;mapreduce_shuffle&lt;/value&gt;
    &lt;/property&gt;

[hadoop@server1 hadoop]$ ../../sbin/start-yarn<span class="hljs-preprocessor">.sh</span>
starting yarn daemons
starting resourcemanager, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/yarn-hadoop-resourcemanager-server1<span class="hljs-preprocessor">.out</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>: starting nodemanager, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/yarn-hadoop-nodemanager-server3<span class="hljs-preprocessor">.out</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>: starting nodemanager, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/yarn-hadoop-nodemanager-server4<span class="hljs-preprocessor">.out</span></code></pre>

<ul>
<li>namenode</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">5434</span> <span class="hljs-constant">ResourceManager</span>
<span class="hljs-number">4303</span> <span class="hljs-constant">SecondaryNameNode</span>
<span class="hljs-number">4115</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">5691</span> <span class="hljs-constant">Jps</span></code></pre>

<ul>
<li>datanode</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server3</span> ~]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1596</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1165</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">1498</span> <span class="hljs-constant">NodeManager</span></code></pre>



<h3 id="五zookeeper集群搭建">五、Zookeeper集群搭建</h3>

<ul>
<li>注意：所有节点清空 /tmp</li>
</ul>



<h5 id="1server5主机">1、server5主机</h5>



<pre class="prettyprint"><code class=" hljs ruby">[root<span class="hljs-variable">@server5</span> ~]<span class="hljs-comment"># yum install- y  nfs-utils</span>
[root<span class="hljs-variable">@server5</span> ~]<span class="hljs-comment"># /etc/init.d/rpcbind start</span>
<span class="hljs-constant">Starting</span> <span class="hljs-symbol">rpcbind:</span>                                          [  <span class="hljs-constant">OK</span>  ]

[root<span class="hljs-variable">@server5</span> ~]<span class="hljs-comment"># useradd -u 800 hadoop</span>
[root<span class="hljs-variable">@server5</span> ~]<span class="hljs-comment"># mount 172.25.120.1:/home/hadoop/ /home/hadoop/</span>
[root<span class="hljs-variable">@server5</span> ~]<span class="hljs-comment"># su - hadoop</span>
[hadoop<span class="hljs-variable">@server5</span> ~]<span class="hljs-variable">$ </span>ls
hadoop               java                       zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">9</span>.tar.gz
hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>         jdk1.<span class="hljs-number">7.0_79</span>
hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.tar.gz  jdk-<span class="hljs-number">7</span>u79-linux-x64.tar.gz</code></pre>



<h5 id="2server1主机">2、server1主机</h5>

<ul>
<li>停止所有服务</li>
</ul>



<pre class="prettyprint"><code class=" hljs vbnet">[hadoop@server1 hadoop]$ ../../sbin/<span class="hljs-keyword">stop</span>-all.sh 
This script <span class="hljs-keyword">is</span> Deprecated. Instead use <span class="hljs-keyword">stop</span>-dfs.sh <span class="hljs-keyword">and</span> <span class="hljs-keyword">stop</span>-yarn.sh
Stopping namenodes <span class="hljs-keyword">on</span> [server1]
server1: stopping namenode
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>: stopping datanode
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>: stopping datanode
Stopping secondary namenodes [<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>]
<span class="hljs-number">0.0</span><span class="hljs-number">.0</span><span class="hljs-number">.0</span>: stopping secondarynamenode
stopping yarn daemons
stopping resourcemanager
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>: stopping nodemanager
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>: stopping nodemanager
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>: nodemanager did <span class="hljs-keyword">not</span> <span class="hljs-keyword">stop</span> gracefully after <span class="hljs-number">5</span> seconds: killing <span class="hljs-keyword">with</span> kill -<span class="hljs-number">9</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>: nodemanager did <span class="hljs-keyword">not</span> <span class="hljs-keyword">stop</span> gracefully after <span class="hljs-number">5</span> seconds: killing <span class="hljs-keyword">with</span> kill -<span class="hljs-number">9</span>
no proxyserver <span class="hljs-keyword">to</span> <span class="hljs-keyword">stop</span></code></pre>

<ul>
<li>测试server5 ssh</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>ssh server5
[hadoop<span class="hljs-variable">@server5</span> ~]<span class="hljs-variable">$ </span>logout
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>ssh <span class="hljs-number">172.25</span>.<span class="hljs-number">120.5</span>
[hadoop<span class="hljs-variable">@server5</span> ~]<span class="hljs-variable">$ </span>logout</code></pre>

<ul>
<li>配置zookeeper</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>tar zxf zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">9</span>.tar.gz 
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>cd zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">9</span>
[hadoop<span class="hljs-variable">@server1</span> zookeeper-<span class="hljs-number">3.4</span>.<span class="hljs-number">9</span>]<span class="hljs-variable">$ </span>cd conf/
[hadoop<span class="hljs-variable">@server1</span> conf]<span class="hljs-variable">$ </span>cp zoo_sample.cfg zoo.cfg
[hadoop<span class="hljs-variable">@server1</span> conf]<span class="hljs-variable">$ </span>vim zoo.cfg 
server.<span class="hljs-number">1</span>=<span class="hljs-number">172.25</span>.<span class="hljs-number">120.2</span><span class="hljs-symbol">:</span><span class="hljs-number">2888</span><span class="hljs-symbol">:</span><span class="hljs-number">3888</span>
server.<span class="hljs-number">2</span>=<span class="hljs-number">172.25</span>.<span class="hljs-number">120.3</span><span class="hljs-symbol">:</span><span class="hljs-number">2888</span><span class="hljs-symbol">:</span><span class="hljs-number">3888</span>
server.<span class="hljs-number">3</span>=<span class="hljs-number">172.25</span>.<span class="hljs-number">120.4</span><span class="hljs-symbol">:</span><span class="hljs-number">2888</span><span class="hljs-symbol">:</span><span class="hljs-number">3888</span></code></pre>



<h5 id="3server234配置">3、server2、3、4配置</h5>

<ul>
<li>注意myid的不同：分别是1、2、3</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server2</span> hadoop]<span class="hljs-variable">$ </span>cd /tmp/
[hadoop<span class="hljs-variable">@server2</span> tmp]<span class="hljs-variable">$ </span>mkdir zookeeper
[hadoop<span class="hljs-variable">@server2</span> tmp]<span class="hljs-variable">$ </span>echo <span class="hljs-number">1</span> &gt; zookeeper/myid
[hadoop<span class="hljs-variable">@server2</span> tmp]<span class="hljs-variable">$ </span>cat zookeeper/myid 
<span class="hljs-number">1</span>

[hadoop<span class="hljs-variable">@server3</span> tmp]<span class="hljs-variable">$ </span>cat zookeeper/myid 
<span class="hljs-number">2</span>

[hadoop<span class="hljs-variable">@server4</span> tmp]<span class="hljs-variable">$ </span>cat zookeeper/myid
<span class="hljs-number">3</span></code></pre>

<ul>
<li>3个DN主机启动zookeeper集群</li>
</ul>



<pre class="prettyprint"><code class=" hljs r">[hadoop@server4 tmp]$ cd ~/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.9</span>
[hadoop@server4 zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.9</span>]$ bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /home/hadoop/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.9</span>/bin/../conf/zoo.cfg
Starting zookeeper <span class="hljs-keyword">...</span> STARTED</code></pre>



<h5 id="4查看所有节点信息">4、查看所有节点信息</h5>



<pre class="prettyprint"><code class=" hljs coffeescript">[hadoop<span class="hljs-property">@server2</span> zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.9</span>]$ bin/zkServer.sh status
ZooKeeper JMX enabled <span class="hljs-keyword">by</span> <span class="hljs-reserved">default</span>
Using <span class="hljs-attribute">config</span>: <span class="hljs-regexp">/home/hadoop/zookeeper-3.4.9/bin/</span>../conf/zoo.cfg
<span class="hljs-attribute">Mode</span>: follower

[hadoop<span class="hljs-property">@server3</span> zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.9</span>]$ bin/zkServer.sh status
ZooKeeper JMX enabled <span class="hljs-keyword">by</span> <span class="hljs-reserved">default</span>
Using <span class="hljs-attribute">config</span>: <span class="hljs-regexp">/home/hadoop/zookeeper-3.4.9/bin/</span>../conf/zoo.cfg
<span class="hljs-attribute">Mode</span>: follower

[hadoop<span class="hljs-property">@server4</span> zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.9</span>]$ bin/zkServer.sh status
ZooKeeper JMX enabled <span class="hljs-keyword">by</span> <span class="hljs-reserved">default</span>
Using <span class="hljs-attribute">config</span>: <span class="hljs-regexp">/home/hadoop/zookeeper-3.4.9/bin/</span>../conf/zoo.cfg
<span class="hljs-attribute">Mode</span>: leader

<span class="hljs-number">5</span>、leader（server4）测试
[hadoop<span class="hljs-property">@server4</span> zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.9</span>]$ bin/zkCli.sh
Connecting to <span class="hljs-attribute">localhost</span>:<span class="hljs-number">2181</span>
<span class="hljs-attribute">WATCHER</span>::

WatchedEvent <span class="hljs-attribute">state</span>:SyncConnected <span class="hljs-attribute">type</span>:None <span class="hljs-attribute">path</span>:<span class="hljs-literal">null</span>

[<span class="hljs-attribute">zk</span>: <span class="hljs-attribute">localhost</span>:<span class="hljs-number">2181</span>(CONNECTED) <span class="hljs-number">0</span>] ls
[<span class="hljs-attribute">zk</span>: <span class="hljs-attribute">localhost</span>:<span class="hljs-number">2181</span>(CONNECTED) <span class="hljs-number">1</span>] ls /
[zookeeper]
[<span class="hljs-attribute">zk</span>: <span class="hljs-attribute">localhost</span>:<span class="hljs-number">2181</span>(CONNECTED) <span class="hljs-number">2</span>] ls /zookeeper
[quota]
[<span class="hljs-attribute">zk</span>: <span class="hljs-attribute">localhost</span>:<span class="hljs-number">2181</span>(CONNECTED) <span class="hljs-number">3</span>] ls /zookeeper/quota
[]
[<span class="hljs-attribute">zk</span>: <span class="hljs-attribute">localhost</span>:<span class="hljs-number">2181</span>(CONNECTED) <span class="hljs-number">5</span>] quit 
Quitting...</code></pre>



<h3 id="六zookeeper高可用">六、Zookeeper高可用</h3>



<h5 id="1配置hadoop-1">1、配置hadoop</h5>

<ul>
<li>配置slaves</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>cd hadoop/etc/
[hadoop<span class="hljs-variable">@server1</span> etc]<span class="hljs-variable">$ </span>vim hadoop/slaves 
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.2</span>
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.3</span>
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.4</span></code></pre>

<ul>
<li>配置core-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml">[hadoop@server1 etc]$ vim hadoop/core-site.xml 
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://masters<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>ha.zookeeper.quorum<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>172.25.120.2:2181,172.25.120.3:2181,172.25.120.4:2181<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<ul>
<li>配置hdfs-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml">[hadoop@server1 etc]$ vim hadoop/hdfs-site.xml 
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>3<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.nameservices<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>masters<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.ha.namenodes.masters<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>h1,h2<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.rpc-address.masters.h1<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>172.25.120.1:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.http-address.masters.h1<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>172.25.120.1:50070<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.rpc-address.masters.h2<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>172.25.120.5:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.http-address.masters.h2<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>172.25.120.5:50070<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.shared.edits.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>qjournal://172.25.120.2:8485;172.25.120.3:8485;172.25.120.4:8485/masters<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.journalnode.edits.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/tmp/journaldata<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.ha.automatic-failover.enabled<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.client.failover.proxy.provider.masters<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvid
er<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.ha.fencing.methods<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>
sshfence
shell(/bin/true)
<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.ha.fencing.ssh.private-key-files<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/home/hadoop/.ssh/id_rsa<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.ha.fencing.ssh.connect-timeout<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>30000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>

[hadoop@server1 etc]$ cd ~/hadoop</code></pre>

<ul>
<li>格式化hdfs集群</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs namenode -format
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>scp -r /tmp/hadoop-hadoop <span class="hljs-number">172.25</span>.<span class="hljs-number">120.5</span><span class="hljs-symbol">:/tmp/</span>

<span class="hljs-comment">##查看server5主机</span>
[root<span class="hljs-variable">@server5</span> ~]<span class="hljs-comment"># ls /tmp/</span>
hadoop-hadoop</code></pre>



<h5 id="23个dn主机启动journalnod">2、3个DN主机启动journalnod</h5>



<pre class="prettyprint"><code class=" hljs d">[hadoop<span class="hljs-keyword">@server3</span> zookeeper-<span class="hljs-number">3.4</span>.9]$ cd ~/hadoop
[hadoop<span class="hljs-keyword">@server3</span> hadoop]$ sbin/hadoop-daemon.sh start journalnode
starting journalnode, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span>.3/logs/hadoop-hadoop-journalnode-server3.<span class="hljs-keyword">out</span>

查看<span class="hljs-number">3</span>个DN主机zookeeper集群状态
[hadoop<span class="hljs-keyword">@server3</span> hadoop]$ jps
<span class="hljs-number">1881</span> DataNode
<span class="hljs-number">1698</span> QuorumPeerMain
<span class="hljs-number">1983</span> Jps
<span class="hljs-number">1790</span> JournalNode</code></pre>



<h5 id="3nn主机格式化zookeeper">3、NN主机格式化zookeeper</h5>

<ul>
<li>格式化后，启动zookeeper</li>
</ul>



<pre class="prettyprint"><code class=" hljs applescript">[hadoop@server1 hadoop]$ bin/hdfs zkfc -formatZK

[hadoop@server1 hadoop]$ sbin/start-dfs.sh
Starting namenodes <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> [server1 server5]
server5: starting namenode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-namenode-server5.out
server1: starting namenode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-namenode-server1.out
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>: starting datanode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-datanode-server4.out
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span>: starting datanode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-datanode-server2.out
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>: starting datanode, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-datanode-server3.out
Starting journal nodes [<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span> <span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span> <span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>]
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>: journalnode <span class="hljs-property">running</span> <span class="hljs-keyword">as</span> process <span class="hljs-number">1790.</span> Stop <span class="hljs-keyword">it</span> <span class="hljs-keyword">first</span>.
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>: journalnode <span class="hljs-property">running</span> <span class="hljs-keyword">as</span> process <span class="hljs-number">1706.</span> Stop <span class="hljs-keyword">it</span> <span class="hljs-keyword">first</span>.
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span>: journalnode <span class="hljs-property">running</span> <span class="hljs-keyword">as</span> process <span class="hljs-number">1626.</span> Stop <span class="hljs-keyword">it</span> <span class="hljs-keyword">first</span>.
Starting ZK Failover Controllers <span class="hljs-function_start"><span class="hljs-keyword">on</span></span> NN hosts [server1 server5]
server1: starting zkfc, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-zkfc-server1.out
server5: starting zkfc, logging <span class="hljs-keyword">to</span> /home/hadoop/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.3</span>/logs/hadoop-hadoop-zkfc-server5.out
</code></pre>

<ul>
<li>查看zookeeper集群</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">6694</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">6646</span> <span class="hljs-constant">DFSZKFailoverController</span>
<span class="hljs-number">6352</span> <span class="hljs-constant">NameNode</span>

[hadoop<span class="hljs-variable">@server5</span> ~]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1396</span> <span class="hljs-constant">DFSZKFailoverController</span>
<span class="hljs-number">1298</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">1484</span> <span class="hljs-constant">Jps</span></code></pre>



<h5 id="4测试高可用">4、测试高可用</h5>

<ul>
<li>访问 <a href="http://172.25.120.1:50070/dfshealth.html#tab-overview" rel="nofollow">http://172.25.120.1:50070/dfshealth.html#tab-overview</a> 查看master</li>
<li>此时，server5 为 master，server1 为 standby </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server5</span> ~]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1396</span> <span class="hljs-constant">DFSZKFailoverController</span>
<span class="hljs-number">1298</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">1484</span> <span class="hljs-constant">Jps</span>
[hadoop<span class="hljs-variable">@server5</span> ~]<span class="hljs-variable">$ </span>kill -<span class="hljs-number">9</span> <span class="hljs-number">1298</span>
[hadoop<span class="hljs-variable">@server5</span> ~]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1396</span> <span class="hljs-constant">DFSZKFailoverController</span>
<span class="hljs-number">1515</span> <span class="hljs-constant">Jps</span></code></pre>

<ul>
<li>server1切换为 master</li>
<li>server5再次启动，状态为 standby </li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server5</span> hadoop]<span class="hljs-variable">$ </span>sbin/hadoop-daemon.sh start namenode
starting namenode, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/logs/hadoop-hadoop-namenode-server5.out</code></pre>



<h5 id="5dn主机查看">5、DN主机查看</h5>



<pre class="prettyprint"><code class=" hljs makefile">[hadoop@server2 hadoop]$ cd ~/zookeeper-3.4.9
[hadoop@server2 zookeeper-3.4.9]$ bin/zkCli.sh
Connecting to localhost:2181
[zk: localhost:2181(CONNECTED) 3] ls /hadoop-ha/masters
[ActiveBreadCrumb, ActiveStandbyElectorLock]
[zk: localhost:2181(CONNECTED) 4] get /hadoop-ha/masters/Active

ActiveBreadCrumb           ActiveStandbyElectorLock
[zk: localhost:2181(CONNECTED) 4] get /hadoop-ha/masters/ActiveBreadCrumb

mastersh1server1 �F(�&gt;
<span class="hljs-constant">cZxid</span> = 0x10000000a
<span class="hljs-constant">ctime</span> = Sat Jul 21 16:32:30 CST 2018
<span class="hljs-constant">mZxid</span> = 0x10000000e
<span class="hljs-constant">mtime</span> = Sat Jul 21 16:34:02 CST 2018
<span class="hljs-constant">pZxid</span> = 0x10000000a
<span class="hljs-constant">cversion</span> = 0
<span class="hljs-constant">dataVersion</span> = 1
<span class="hljs-constant">aclVersion</span> = 0
<span class="hljs-constant">ephemeralOwner</span> = 0x0
<span class="hljs-constant">dataLength</span> = 28
<span class="hljs-constant">numChildren</span> = 0</code></pre>



<h5 id="七yarn的高可用">七、yarn的高可用</h5>



<h5 id="1server1主机">1、server1主机</h5>

<ul>
<li>配置mapred-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml">[hadoop@server1 hadoop]$ pwd
/home/hadoop/hadoop/etc/hadoop
[hadoop@server1 hadoop]$ vim mapred-site.xml
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<ul>
<li>配置yarn-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml">[hadoop@server1 hadoop]$ vim yarn-site.xml 
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>

<span class="hljs-comment">&lt;!-- Site specific YARN configuration properties --&gt;</span>

    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
                        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.ha.enabled<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.cluster-id<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>RM_CLUSTER<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.ha.rm-ids<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>rm1,rm2<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname.rm1<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>172.25.120.1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname.rm2<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>172.25.120.5<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.recovery.enabled<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.store.class<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.hadoop.yarn.server.resourcemanager.recovery.ZKRMStateStore<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.zk-address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>172.25.120.2:2181,172.25.120.3:2181,172.25.120.4:2181<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<ul>
<li>配置regionservers，启动hbase</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>cd hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>/conf/
[hadoop<span class="hljs-variable">@server1</span> conf]<span class="hljs-variable">$ </span>vim regionservers
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.2</span>
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.3</span>
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.4</span>

[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>../../sbin/start-yarn.sh 
starting yarn daemons
starting resourcemanager, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/logs/yarn-hadoop-resourcemanager-server1.out
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.3</span><span class="hljs-symbol">:</span> starting nodemanager, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/logs/yarn-hadoop-nodemanager-server3.out
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.4</span><span class="hljs-symbol">:</span> starting nodemanager, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/logs/yarn-hadoop-nodemanager-server4.out
<span class="hljs-number">172.25</span>.<span class="hljs-number">120.2</span><span class="hljs-symbol">:</span> starting nodemanager, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/logs/yarn-hadoop-nodemanager-server2.out
[hadoop<span class="hljs-variable">@server1</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1927</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1837</span> <span class="hljs-constant">ResourceManager</span>
<span class="hljs-number">1325</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">1602</span> <span class="hljs-constant">DFSZKFailoverController</span></code></pre>



<h5 id="2server5主机">2、server5主机</h5>

<ul>
<li>手动开启RM节点</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server5</span> ~]<span class="hljs-variable">$ </span>cd hadoop
[hadoop<span class="hljs-variable">@server5</span> hadoop]<span class="hljs-variable">$ </span>sbin/yarn-daemon.sh start resourcemanager
starting resourcemanager, logging to /home/hadoop/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>/logs/yarn-hadoop-resourcemanager-server5.out

[hadoop<span class="hljs-variable">@server5</span> logs]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">3250</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1694</span> <span class="hljs-constant">ResourceManager</span>
<span class="hljs-number">1209</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">3184</span> <span class="hljs-constant">DFSZKFailoverController</span></code></pre>

<ul>
<li>查看NM节点信息</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server3</span> sbin]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">1549</span> <span class="hljs-constant">NodeManager</span>
<span class="hljs-number">1186</span> <span class="hljs-constant">QuorumPeerMain</span>
<span class="hljs-number">1293</span> <span class="hljs-constant">JournalNode</span>
<span class="hljs-number">1869</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1381</span> <span class="hljs-constant">DataNode</span></code></pre>



<h5 id="3访问-http1722512018088clustercluster-查看状态">3、访问 <a href="http://172.25.120.1:8088/cluster/cluster" rel="nofollow">http://172.25.120.1:8088/cluster/cluster</a> 查看状态</h5>

<p><img src="https://img-blog.csdn.net/20180722130427920?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0Zvcl9teXNlbGYw/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h3 id="八hbase分布式部署">八、Hbase分布式部署</h3>



<h5 id="1配置hbase">1、配置Hbase</h5>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>tar zxf hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>-bin.tar.gz 
[hadoop<span class="hljs-variable">@server1</span> ~]<span class="hljs-variable">$ </span>cd hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>
[hadoop<span class="hljs-variable">@server1</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>]<span class="hljs-variable">$ </span>ls
bin          conf  hbase-webapps  lib          <span class="hljs-constant">NOTICE</span>.txt
<span class="hljs-constant">CHANGES</span>.txt  docs  <span class="hljs-constant">LEGAL</span>          <span class="hljs-constant">LICENSE</span>.txt  <span class="hljs-constant">README</span>.txt
[hadoop<span class="hljs-variable">@server1</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>]<span class="hljs-variable">$ </span>cd conf/
[hadoop<span class="hljs-variable">@server1</span> conf]<span class="hljs-variable">$ </span>vim hbase-env.sh 
[hadoop<span class="hljs-variable">@server1</span> conf]<span class="hljs-variable">$ </span>vim regionservers 
[hadoop<span class="hljs-variable">@server1</span> conf]<span class="hljs-variable">$ </span>vim hbase-site.xml </code></pre>



<h5 id="2启动hbase">2、启动Hbase</h5>



<pre class="prettyprint"><code class=" hljs vhdl">[hadoop@server1 hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.4</span>]$ bin/start-hbase.sh 
starting master, logging <span class="hljs-keyword">to</span> /home/hadoop/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.4</span>/bin/../logs/hbase-hadoop-master-server1.<span class="hljs-keyword">out</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.3</span>: starting regionserver, logging <span class="hljs-keyword">to</span> /home/hadoop/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.4</span>/bin/../logs/hbase-hadoop-regionserver-server3.<span class="hljs-keyword">out</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.4</span>: starting regionserver, logging <span class="hljs-keyword">to</span> /home/hadoop/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.4</span>/bin/../logs/hbase-hadoop-regionserver-server4.<span class="hljs-keyword">out</span>
<span class="hljs-number">172.25</span><span class="hljs-number">.120</span><span class="hljs-number">.2</span>: starting regionserver, logging <span class="hljs-keyword">to</span> /home/hadoop/hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.4</span>/bin/../logs/hbase-hadoop-regionserver-server2.<span class="hljs-keyword">out</span>

[hadoop@server1 hbase-<span class="hljs-number">1.2</span><span class="hljs-number">.4</span>]$ jps
<span class="hljs-number">1837</span> ResourceManager
<span class="hljs-number">2567</span> HMaster
<span class="hljs-number">1325</span> NameNode
<span class="hljs-number">1602</span> DFSZKFailoverController
<span class="hljs-number">2634</span> Jps</code></pre>

<ul>
<li>server需要手动启动Hbase</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server5</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>]<span class="hljs-variable">$ </span>bin/hbase-daemon.sh start master
starting master, logging to /home/hadoop/hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>/bin/../logs/hbase-hadoop-master-server1.out

[hadoop<span class="hljs-variable">@server5</span> hbase-<span class="hljs-number">1.2</span>.<span class="hljs-number">4</span>]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">2253</span> <span class="hljs-constant">HMaster</span>
<span class="hljs-number">1694</span> <span class="hljs-constant">ResourceManager</span>
<span class="hljs-number">1209</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">2317</span> <span class="hljs-constant">Jps</span></code></pre>



<h5 id="3测试">3、测试</h5>



<pre class="prettyprint"><code class=" hljs ocaml">[hadoop@server1 hbase]$ bin/hbase shell
hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; create <span class="hljs-string">'test'</span>, <span class="hljs-string">'cf'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">1.2200</span> seconds
hbase(main):<span class="hljs-number">003</span>:<span class="hljs-number">0</span>&gt; <span class="hljs-built_in">list</span> <span class="hljs-string">'test'</span>
TABLE
test
<span class="hljs-number">1</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.2150</span> seconds
=&gt; [<span class="hljs-string">"test"</span>]
hbase(main):<span class="hljs-number">004</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'test'</span>, <span class="hljs-string">'row1'</span>, <span class="hljs-string">'cf:a'</span>, <span class="hljs-string">'value1'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0560</span> seconds
hbase(main):<span class="hljs-number">005</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'test'</span>, <span class="hljs-string">'row2'</span>, <span class="hljs-string">'cf:b'</span>, <span class="hljs-string">'value2'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0370</span> seconds
hbase(main):<span class="hljs-number">006</span>:<span class="hljs-number">0</span>&gt; put <span class="hljs-string">'test'</span>, <span class="hljs-string">'row3'</span>, <span class="hljs-string">'cf:c'</span>, <span class="hljs-string">'value3'</span>
<span class="hljs-number">0</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.0450</span> seconds
hbase(main):<span class="hljs-number">007</span>:<span class="hljs-number">0</span>&gt; scan <span class="hljs-string">'test'</span>
ROW
COLUMN+CELL
row1
column=cf:a, timestamp=<span class="hljs-number">1488879391939</span>, <span class="hljs-keyword">value</span>=value1
row2
column=cf:b, timestamp=<span class="hljs-number">1488879402796</span>, <span class="hljs-keyword">value</span>=value2
row3
column=cf:c, timestamp=<span class="hljs-number">1488879410863</span>, <span class="hljs-keyword">value</span>=value3
<span class="hljs-number">3</span> row(s) <span class="hljs-keyword">in</span> <span class="hljs-number">0.2770</span> seconds</code></pre>

<ul>
<li>查看hdfs</li>
</ul>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@server5</span> hadoop]<span class="hljs-variable">$ </span>bin/hdfs dfs -ls /
<span class="hljs-constant">Found</span> <span class="hljs-number">3</span> items
drwxr-xr-x - hadoop supergroup <span class="hljs-number">0</span> <span class="hljs-number">2017</span>-<span class="hljs-number">03</span>-<span class="hljs-number">07</span> <span class="hljs-number">23</span><span class="hljs-symbol">:</span><span class="hljs-number">56</span> /hbase
drwx------ - hadoop supergroup <span class="hljs-number">0</span> <span class="hljs-number">2017</span>-<span class="hljs-number">03</span>-<span class="hljs-number">04</span> <span class="hljs-number">17</span><span class="hljs-symbol">:</span><span class="hljs-number">50</span> /tmp
drwxr-xr-x - hadoop supergroup <span class="hljs-number">0</span> <span class="hljs-number">2017</span>-<span class="hljs-number">03</span>-<span class="hljs-number">04</span> <span class="hljs-number">17</span><span class="hljs-symbol">:</span><span class="hljs-number">38</span> /user</code></pre>

<ul>
<li>kill  HMaster可以通过网页查看 </li>
<li><a href="http://172.25.120.1:16010" rel="nofollow">http://172.25.120.1:16010</a></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
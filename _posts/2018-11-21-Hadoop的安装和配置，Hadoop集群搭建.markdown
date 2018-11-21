---
layout:     post
title:      Hadoop的安装和配置，Hadoop集群搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sinat_36888624/article/details/79506450				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一，Hadoop的安装：</p>



<pre class="prettyprint"><code class=" hljs lasso">vim /etc/hosts
<span class="hljs-number">172.25</span><span class="hljs-number">.38</span><span class="hljs-number">.7</span>    server7
useradd hadoop
su <span class="hljs-subst">-</span> hadoop
pwd 
/home/hadoop
tar zxf hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.3</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz
 tar zxf jdk<span class="hljs-subst">-</span><span class="hljs-number">7</span>u79<span class="hljs-attribute">-linux</span><span class="hljs-attribute">-x64</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz 
 ln <span class="hljs-attribute">-s</span> jdk1<span class="hljs-number">.7</span><span class="hljs-number">.0</span>_79<span class="hljs-subst">/</span> jdk
ln <span class="hljs-attribute">-s</span> hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.3</span> hadoop
vim /home/hadoop<span class="hljs-subst">/</span><span class="hljs-built_in">.</span>bash_profile 
<span class="hljs-number">10</span> PATH<span class="hljs-subst">=</span><span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HOME</span>/bin:/home/hadoop/jdk/bin

 vim hadoop/etc/hadoop/hadoop<span class="hljs-attribute">-env</span><span class="hljs-built_in">.</span>sh   
 <span class="hljs-number">25</span> export JAVA_HOME<span class="hljs-subst">=</span>/home/hadoop/jdk

</code></pre>

<p>测试使用Hadoop自带的wordcount：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> /home/hadoop/hadoop
mkdir input
cp etc/hadoop/* input/

bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-<span class="hljs-number">2.7</span>.<span class="hljs-number">3</span>.jar wordcount input output
<span class="hljs-built_in">cd</span> output
hadoop@server7 output]$ ls
part-r-<span class="hljs-number">00000</span>  _SUCCESS              <span class="hljs-comment">#对input执行mapreduce的结果</span>
cat part-r-<span class="hljs-number">00000</span>        <span class="hljs-comment">#可以看见wordcount功能已经完成了mapreduce任务；</span>
!=      <span class="hljs-number">3</span>
<span class="hljs-string">""</span>      <span class="hljs-number">6</span>
<span class="hljs-string">""</span>.     <span class="hljs-number">4</span>
<span class="hljs-string">"<span class="hljs-variable">$HADOOP_CLASSPATH</span>"</span>     <span class="hljs-number">1</span>
<span class="hljs-string">"<span class="hljs-variable">$JAVA_HOME</span>"</span>    <span class="hljs-number">2</span>
<span class="hljs-string">"<span class="hljs-variable">$YARN_HEAPSIZE</span>"</span>        <span class="hljs-number">1</span>
<span class="hljs-string">"<span class="hljs-variable">$YARN_LOGFILE</span>"</span> <span class="hljs-number">1</span>
<span class="hljs-string">"<span class="hljs-variable">$YARN_LOG_DIR</span>"</span> <span class="hljs-number">1</span>
<span class="hljs-string">"<span class="hljs-variable">$YARN_POLICYFILE</span>"</span>      <span class="hljs-number">1</span>
<span class="hljs-string">"*"</span>     <span class="hljs-number">18</span>
<span class="hljs-string">"AS     24
"</span>Error: <span class="hljs-number">1</span>
<span class="hljs-string">"License"</span>);     <span class="hljs-number">24</span>
<span class="hljs-string">"alice,bob      18
"</span>console<span class="hljs-string">"       1
"</span>dfs<span class="hljs-string">"   3
"</span>hadoop.root.logger<span class="hljs-string">".   1
"</span>jks<span class="hljs-string">".  4
"</span>jvm<span class="hljs-string">"   3
"</span>mapred<span class="hljs-string">"        3
"</span>rpc<span class="hljs-string">"   3
"</span>run    <span class="hljs-number">1</span>
<span class="hljs-string">"ugi"</span>   <span class="hljs-number">3</span>
<span class="hljs-string">"x"</span>     <span class="hljs-number">1</span>


</code></pre>



<h1 id="单节点集群nn-dn-snn在一台主机上">单节点集群：NN DN SNN在一台主机上：</h1>



<pre class="prettyprint"><code class=" hljs xml">cd /home/hadoop/hadoop/etc/hadoop
vim core-site.xml19 <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
 20 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
 21         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
 22                 <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://172.25.38.7:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
 23                     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
 24 <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>


vim hdfs-site.xml
19 <span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
 20 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
 21         <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
 22                 <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>        #此时数据块仅仅保存一份，默认保存三份
 23                     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
 24 <span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>

vim /home/hadoop/hadoop/etc/hadoop/slaves 
172.25.38.7

ssh-keygen
ssh-copy-id 172.25.38.7
ssh-copy-id server7

bin/hdfs namenode -format
/home/hadoop/hadoop/sbin/start-dfs.sh 
jps</code></pre>



<h1 id="hadoop集群搭建">Hadoop集群搭建：</h1>

<p>server7:namenode，SecondaryNameNode <br>
server8,server9:datanode <br>
1,配置集群节点时间同步：设置集群节点同步主机的时间：</p>



<pre class="prettyprint"><code class=" hljs avrasm">主机上：vim /etc/chrony<span class="hljs-preprocessor">.conf</span>
<span class="hljs-number">7</span> server time1<span class="hljs-preprocessor">.aliyun</span><span class="hljs-preprocessor">.com</span>  iburst    <span class="hljs-preprocessor">#设置主机同步阿里云的时间</span>
<span class="hljs-number">23</span> allow <span class="hljs-number">172.25</span><span class="hljs-number">.38</span>/<span class="hljs-number">24</span>           <span class="hljs-preprocessor">#允许同步的主机网段</span></code></pre>

<p>server7，server8，server9上：</p>



<pre class="prettyprint"><code class=" hljs avrasm">yum install -<span class="hljs-built_in">y</span> ntp
 vim /etc/ntp<span class="hljs-preprocessor">.conf</span> <span class="hljs-number">22</span> server <span class="hljs-number">172.25</span><span class="hljs-number">.38</span><span class="hljs-number">.250</span> iburst      设置集群节点同步主机的时间
 /etc/init<span class="hljs-preprocessor">.d</span>/ntpd start</code></pre>

<p>2，配置nfs共享配置： <br>
server7上：</p>



<pre class="prettyprint"><code class=" hljs lasso">yum install <span class="hljs-attribute">-y</span> nfs<span class="hljs-attribute">-utils</span>
/etc/init<span class="hljs-built_in">.</span>d/rpcbind  start
/etc/init<span class="hljs-built_in">.</span>d/nfs start
/home/hadoop    <span class="hljs-subst">*</span>(rw,anonuid<span class="hljs-subst">=</span><span class="hljs-number">500</span>,anongid<span class="hljs-subst">=</span><span class="hljs-number">500</span>)
exportfs <span class="hljs-attribute">-rv</span></code></pre>

<p>server8,server9上：</p>



<pre class="prettyprint"><code class=" hljs ruby">useradd hadoop
yum install -y nfs-utils
/etc/init.d/rpcbind  start
/etc/init.d/nfs start
showmount -e <span class="hljs-number">172.25</span>.<span class="hljs-number">38.7</span>
mount <span class="hljs-number">172.25</span>.<span class="hljs-number">38.7</span><span class="hljs-symbol">:/home/hadoop/</span> /home/hadoop/
vim /home/hadoop/.bash_profile 

<span class="hljs-constant">PATH</span>=<span class="hljs-variable">$PATH</span><span class="hljs-symbol">:</span><span class="hljs-variable">$HOME</span>/<span class="hljs-symbol">bin:</span>/home/hadoop/jdk/bin</code></pre>



<h1 id="hadoop增加集群节点扩容">Hadoop增加集群节点，扩容</h1>



<h1 id="hadoop缩荣减少集群节点">Hadoop缩荣，减少集群节点</h1>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
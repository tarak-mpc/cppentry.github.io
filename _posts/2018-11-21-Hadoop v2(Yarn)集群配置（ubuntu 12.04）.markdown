---
layout:     post
title:      Hadoop v2(Yarn)集群配置（ubuntu 12.04）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/edwardvsnc/article/details/8555225				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Hadoop版本: 2.0.2(Yarn)</p>
<p>添加一个用户</p>
<p>  sudo addgroup hadoop</p>
<p>  sudo adduser -ingroup hadoop hadoop</p>
<p>  sudo vim /etc/sudoers</p>
<p>  添加 hadoop ALL=(ALL:ALL) ALL</p>
<p>设置环境变量</p>
<p>vim /etc/profile</p>
<p>export JAVA_HOME=/usr/lib/jvm/java-7-sun</p>
<p>export JRE_HOME=$JAVA_HOME/jre</p>
<p>export CLASSPATH=.:$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH</p>
<p>export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$PATH</p>
<p>export HADOOP_PREFIX=/home/hadoop/hadoop-2.0.2-alpha</p>
<p>export HADOOP_HOME=$HADOOP_PREFIX</p>
<p>修改hosts</p>
<p>vim /etc/hosts</p>
<p>192.168.0.1  mcw-cc-nachuang</p>
<p></p>
<p>设置SSH无密码登录:</p>
<p>  1.安装<span lang="en-us" xml:lang="en-us">openssh-server</span></p>
<p><span lang="en-us" xml:lang="en-us">    sudo apt-get install openssh-server</span></p>
<p><span lang="en-us" xml:lang="en-us">  2. ssh-keygen -t rsa -P "" </span></p>
<p>    进入<span lang="en-us" xml:lang="en-us">~/.ssh/</span>目录，将<span lang="en-us" xml:lang="en-us">id_rsa.pub</span>追加到<span lang="en-us" xml:lang="en-us">authorized_keys</span>授权文件中：</p>
<p>    cat id_rsa.pub &gt;&gt; authorized_keys</p>
<p>    然后，将id_rsa.pub 再导入其他的slave中</p>
<p>    slave也要做同样的操作，使slave 和 master之间可以相互无密码登录</p>
<p>  3.关闭防火墙 sudo ufw disable (可选，不过对于初学者建议关掉)</p>
<p>配置完后通过ssh验证各个slaves和master相互都可以无密码登录。</p>
<p><br></p>
<p>etc/hadoop/配置文件:</p>
<p>1.hadoop-env.sh</p>
<p>export JAVA_HOME=/usr/lib/jvm/java-7-sun</p>
<p>2.core-site.xml</p>
<pre><code class="language-html">&lt;configuration&gt;
&lt;property&gt;
&lt;name&gt;fs.default.name&lt;/name&gt;
&lt;value&gt;hdfs://mcw-cc-nachuang:9000&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
&lt;value&gt;/home/nachuang/Workspace/Hadoop/hadoop-2.0.2-alpha/tmp&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<p style="font-family:monospace;">3.hdfs-site.xml</p>
<pre><code class="language-html">&lt;configuration&gt;
&lt;property&gt;  
&lt;name&gt;dfs.name.dir&lt;/name&gt;  
&lt;value&gt;/home/nachuang/Workspace/Hadoop/hadoop-2.0.2-alpha/hdfs/name&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;dfs.data.dir&lt;/name&gt;  
&lt;value&gt;/home/nachuang/Workspace/Hadoop/hadoop-2.0.2-alpha/hdfs/data&lt;/value&gt;
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;dfs.replication&lt;/name&gt;  
&lt;value&gt;1&lt;/value&gt; 
&lt;/property&gt;
&lt;property&gt;  
&lt;name&gt;dfs.block.size&lt;/name&gt;  
&lt;value&gt;268435456&lt;/value&gt;  
&lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<pre></pre>
<p>4.<span style="font-family:monospace;">mapred-site.xml</span></p>
<pre><code class="language-html">&lt;configuration&gt;  
&lt;property&gt;  
&lt;name&gt;mapreduce.framework.name&lt;/name&gt;  
&lt;value&gt;yarn&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;mapreduce.job.tracker&lt;/name&gt;  
&lt;value&gt;hdfs://mcw-cc-nachuang:9001&lt;/value&gt;  
&lt;final&gt;true&lt;/final&gt;  
&lt;/property&gt;  
&lt;property&gt;
&lt;name&gt;mapred.job.map.memory.mb&lt;/name&gt;
&lt;value&gt;1700&lt;/value&gt;
&lt;/property&gt;

&lt;property&gt;
&lt;name&gt;mapred.job.reduce.memory.mb&lt;/name&gt;
&lt;value&gt;1700&lt;/value&gt;
&lt;/property&gt;

&lt;property&gt;
&lt;name&gt;mapred.child.java.opts&lt;/name&gt;
&lt;value&gt;-Xmx1400m&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;  
&lt;name&gt;mapreduce.task.io.sort.mb&lt;/name&gt;  
&lt;value&gt;400&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;mapreduce.task.io.sort.factor&lt;/name&gt;  
&lt;value&gt;10&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;mapred.system.dir&lt;/name&gt;  
&lt;value&gt;file:/home/nachuang/Workspace/Hadoop/hadoop-2.0.2-alpha/mapred/system&lt;/value&gt;  
&lt;final&gt;true&lt;/final&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;mapred.local.dir&lt;/name&gt;  
&lt;value&gt;file:/home/nachuang/Workspace/Hadoop/hadoop-2.0.2-alpha/mapred/local&lt;/value&gt;  
&lt;final&gt;true&lt;/final&gt;  
&lt;/property&gt;  
&lt;property&gt;
&lt;name&gt;mapred.reduce.slowstart.completed.maps&lt;/name&gt;
&lt;value&gt;1&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;  </code></pre>
<p></p>
<p>yarn-site.xml</p>
<p></p>
<pre><code class="language-html">&lt;configuration&gt;  
&lt;property&gt;  
&lt;name&gt;yarn.resourcemanager.address&lt;/name&gt;  
&lt;value&gt;mcw-cc-nachuang:9080&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;yarn.resourcemanager.scheduler.address&lt;/name&gt;  
&lt;value&gt;mcw-cc-nachuang:9081&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;yarn.resourcemanager.resource-tracker.address&lt;/name&gt;  
&lt;value&gt;mcw-cc-nachuang:9082&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;  
&lt;value&gt;mapreduce.shuffle&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;  
&lt;name&gt;yarn.nodemanager.aux-services.mapreduce.shuffle.class&lt;/name&gt;  
&lt;value&gt;org.apache.hadoop.mapred.ShuffleHandler&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;
&lt;name&gt;yarn.nodemanager.vmem-pmem-ratio&lt;/name&gt;
&lt;value&gt;6&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;yarn.nodemanager.resource.memory-mb&lt;/name&gt;
&lt;value&gt;10240&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;  </code></pre>
<p>验证：</p>
<p>在hadoop-2.0.2-alpha目录下执行：</p>
<p>bin/hadoop namenode -format </p>
<p>sbin/start-all.sh</p>
<p>jps 后显示出以下5个服务，说明启动成功：</p>
<p>19166 SecondaryNameNode<br></p>
<p>19566 NodeManager<br>
20254 Jps<br>
19321 ResourceManager<br>
18610 NameNode<br>
18850 DataNode</p>
<p>如果启动成功，可以通过浏览器访问HDFS:  http://mcw-cc-nachuang:50070</p>
<p>生成terasort的数据：</p>
<p>bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.0.2-alpha.jar teragen 1000000 teradata/input100m<br></p>
<p>执行terasort:</p>
<p>bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.0.2-alpha.jar terasort teradata/input100m teradata/output100m<br></p>
<p>注意，在hadoop2.0.3默认情况下执行terasort cases会报<span class="pln" style="border:0px;vertical-align:baseline;">java</span><span class="pun" style="border:0px;vertical-align:baseline;">.</span><span class="pln" style="border:0px;vertical-align:baseline;">lang</span><span class="pun" style="border:0px;vertical-align:baseline;">.</span><span class="typ" style="border:0px;vertical-align:baseline;">OutOfMemoryError</span><span class="pun" style="border:0px;vertical-align:baseline;">:</span><span class="pln" style="border:0px;vertical-align:baseline;"></span><span class="typ" style="border:0px;vertical-align:baseline;">Java</span><span class="pln" style="border:0px;vertical-align:baseline;"> heap
 space需要修改hadoop-env.sh 中的export HADOOP_CLIENT_OPTS="-Xmx128m $HADOOP_CLIENT_OPTS"(JVM默认配置的最大内存是128m)，修改为256m。</span></p>
            </div>
                </div>
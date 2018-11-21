---
layout:     post
title:      centos7安装hadoop3.0.3和jdk1.8的伪分布式模式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>centos7安装hadoop3.0.3和jdk1.8的伪分布式模式</p>



<h1 id="添加普通用户hadoop">添加普通用户hadoop</h1>

<p>useradd hadoop <br>
passwd hadoop <br>
1</p>



<h1 id="给hadoop用户sudo权限">给hadoop用户sudo权限</h1>

<p>chmod u+w /etc/sudoers <br>
vi /etc/sudoers <br>
添加 <br>
hadoop ALL=(ALL)    ALL <br>
或者 <br>
hadoop ALL=(root)   NOPASSWD:ALL</p>



<h1 id="切换到hadoop用户">切换到hadoop用户</h1>

<p>su  -  hadoop</p>



<h1 id="安装到homehadoophadoop303目录">安装到/home/hadoop/hadoop3.03目录</h1>

<p>sudo mkidr /home/hadoop/hadoop3.03 <br>
tar -zxvf hadoop-3.0.3.tar.gz <br>
mv hadoop-3.0.3 hadoop3.03</p>

<h1 id="安装到homehadoopjavajdk18">安装到/home/hadoop/java/jdk1.8</h1>

<p>tar -zxvf jdk-8u172-linux-x64.gz <br>
mv jdk_1.8.0.172  jdk1.8</p>



<h1 id="配置环境变量">配置环境变量</h1>

<p>vi  /etc/profile</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment">##java</span>
<span class="hljs-keyword">export</span> JAVA_HOME=/home/hadoop/java/jdk1.<span class="hljs-number">8</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$JAVA_HOME</span>/bin

<span class="hljs-comment">##hadoop</span>
<span class="hljs-keyword">export</span> HADOOP_HOME=/home/hadoop/hadoop3.<span class="hljs-number">03</span>
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$HADOOP_HOME</span>/bin:<span class="hljs-variable">$HADOOP_HOME</span>/sbin:<span class="hljs-variable">$PATH</span></code></pre>



<h1 id="验证">验证</h1>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">echo</span> <span class="hljs-variable">$JAVA_HOME</span>
<span class="hljs-built_in">echo</span> <span class="hljs-variable">$HADOOP_HOME</span></code></pre>



<h1 id="配置-hadoop-envshmapred-envshyarn-envsh文件的javahome参数">配置 hadoop-env.sh、mapred-env.sh、yarn-env.sh文件的JAVA_HOME参数</h1>

<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/home/hadoop</span><span class="hljs-regexp">/java/jdk</span>1.<span class="hljs-number">8</span></code></pre>



<h1 id="配置core-sitexml">配置core-site.xml</h1>

<p>hadoop-localhost为主机名称，/opt/data/tmp要先创建好目录</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-pi">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</span>
<span class="hljs-comment">&lt;!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
--&gt;</span>

<span class="hljs-comment">&lt;!-- Put site-specific property overrides in this file. --&gt;</span>

<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://hadoop-localhost:8020<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>HDFS的URI，文件系统://namenode标识:端口号<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span> 
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/data/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>namenode上本地的hadoop临时文件夹<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span> 
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>

<p>hadoop.tmp.dir配置的是Hadoop临时目录，比如HDFS的NameNode数据默认都存放这个目录下，查看*-default.xml等默认配置文件，就可以看到很多依赖${hadoop.tmp.dir}的配置。</p>

<p>默认的hadoop.tmp.dir是/tmp/hadoop-${user.name},此时有个问题就是NameNode会将HDFS的元数据存储在这个/tmp目录下，如果操作系统重启了，系统会清空/tmp目录下的东西，导致NameNode元数据丢失，是个非常严重的问题，所有我们应该修改这个路径。</p>

<p>sudo mkdir -p /opt/data/tmp</p>

<p>将临时目录的所有者修改为hadoop <br>
sudo chown –R hadoop:hadoop /opt/data/tmp</p>



<h1 id="配置hdfs-sitexml">配置hdfs-site.xml</h1>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0" encoding="UTF-8"?&gt;</span>
<span class="hljs-pi">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</span>
<span class="hljs-comment">&lt;!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at
    http://www.apache.org/licenses/LICENSE-2.0
  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
--&gt;</span>
<span class="hljs-comment">&lt;!-- Put site-specific property overrides in this file. --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span> 
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/data/tmp/dfs/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>namenode上存储hdfs名字空间元数据<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/opt/data/tmp/dfs/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>datanode上数据块的物理存储位置<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span> 
  <span class="hljs-comment">&lt;!--设置hdfs副本数量--&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
  <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>



<h1 id="格式化hdfs">格式化HDFS</h1>

<p>sudo chown -R hadoop:hadoop /opt/data <br>
hdfs namenode –format</p>

<p>查看NameNode格式化后的目录 <br>
$ ll /opt/data/tmp/dfs/name/current</p>

<p>启动NameNode <br>
sbin/hadoop-daemon.sh start namenode</p>

<p>启动DataNode <br>
sbin/hadoop-daemon.sh start datanode</p>

<p>启动SecondaryNameNode <br>
sbin/hadoop-daemon.sh start secondarynamenode</p>

<p>JPS命令查看是否已经启动成功，有结果就是启动成功了 <br>
$ jps</p>



<h1 id="hdfs上测试创建目录上传下载文件">HDFS上测试创建目录、上传、下载文件</h1>

<p>[hadoop@hadoop-localhost  hadoop3.03]# <br>
创建目录 <br>
bin/hdfs dfs -mkdir /demo1</p>

<p>上传 <br>
bin/hdfs dfs -put  etc/hadoop/core-site.xml /demo1</p>

<p>读取HDFS上的文件内容 <br>
bin/hdfs dfs -cat  /demo1/core-site.xml</p>

<p>从HDFS上下载文件到本地 <br>
bin/hdfs dfs -get /demo1/core-site.xml</p>



<h1 id="查看hdfs的web页面">查看hdfs的web页面</h1>

<p>hdfs 2.X版本的web页面端口号为50070 <br>
<a href="http://192.168.145.129:50070" rel="nofollow">http://192.168.145.129:50070</a></p>

<p>hdfs 3.X版本的web页面端口号为9870 <br>
<a href="http://192.168.145.129:9870/dfshealth.html#tab-overview" rel="nofollow">http://192.168.145.129:9870/dfshealth.html#tab-overview</a></p>

<h1 id="配置启动yarn">配置、启动YARN</h1>

<h1 id="配置mapred-sitexml">配置mapred-site.xml</h1>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>
<span class="hljs-pi">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</span>
<span class="hljs-comment">&lt;!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at
    http://www.apache.org/licenses/LICENSE-2.0
  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
--&gt;</span>
<span class="hljs-comment">&lt;!-- Put site-specific property overrides in this file. --&gt;</span>
<span class="hljs-comment">&lt;!-- 指定mr运行在yarn上 --&gt;</span>
<span class="hljs-comment">&lt;!-- ${full path of your hadoop distribution directory} --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
　  <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>  
　　    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>  
　　    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>  
　 <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>  
   <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.app.mapreduce.am.env<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>HADOOP_MAPRED_HOME=/home/hadoop/hadoop3.03<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.map.env<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>HADOOP_MAPRED_HOME=/home/hadoop/hadoop3.03<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.reduce.env<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>HADOOP_MAPRED_HOME=/home/hadoop/hadoop3.03<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>



<h1 id="配置yarn-sitexml">配置yarn-site.xml</h1>

<p>arn.nodemanager.aux-services配置了yarn的默认混洗方式，选择为mapreduce的默认混洗算法。</p>

<p>yarn.resourcemanager.hostname指定了Resourcemanager运行在哪个节点上。</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>
<span class="hljs-comment">&lt;!--
  Licensed under the Apache License, Version 2.0 (the "License");
  you may not use this file except in compliance with the License.
  You may obtain a copy of the License at
    http://www.apache.org/licenses/LICENSE-2.0
  Unless required by applicable law or agreed to in writing, software
  distributed under the License is distributed on an "AS IS" BASIS,
  WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
  See the License for the specific language governing permissions and
  limitations under the License. See accompanying LICENSE file.
--&gt;</span>
<span class="hljs-comment">&lt;!-- 指定YARN的老大（ResourceManager）的地址 --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
<span class="hljs-comment">&lt;!-- Site specific YARN configuration properties --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>  
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>  
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>  
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>  　
<span class="hljs-comment">&lt;!-- reducer获取数据的方式 --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hadoop-localhost<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>



<h1 id="启动resourcemanager">启动Resourcemanager</h1>

<p>sbin/yarn-daemon.sh start resourcemanager</p>



<h1 id="启动nodemanager">启动nodemanager</h1>

<p>sbin/yarn-daemon.sh start nodemanager</p>

<p>也可执行批处理文件启动服务 <br>
启动hdfs 和yarn <br>
sbin/start-dfs.sh <br>
sbin/start-yarn.sh</p>

<p>sbin/start-all.sh</p>

<h1 id="yarn的web页面">YARN的Web页面</h1>

<p>YARN的Web客户端端口号是8088，通过<a href="http://192.168.145.129:8088/" rel="nofollow">http://192.168.145.129:8088/</a>可以查看。</p>



<h1 id="运行mapreduce-job">运行MapReduce Job</h1>

<p>创建测试用的Input文件 <br>
 bin/hdfs dfs -mkdir -p /wordcountdemo/input</p>

<p>wc.input文件内容为：</p>



<pre class="prettyprint"><code class=" hljs ">hadoop mapreduce hive
hbase spark storm
sqoop hadoop hive
spark hadoop</code></pre>

<p>将wc.input文件上传到HDFS的/wordcountdemo/input目录中: <br>
bin/hdfs dfs -put /opt/data/wc.input /wordcountdemo/input</p>

<p>运行WordCount MapReduce Job</p>

<h1 id="hadoophadoop-localhost-hadoop303-binyarn-jar-sharehadoopmapreducehadoop-mapreduce-examples-303jar-wordcount-wordcountdemoinput-wordcountdemooutput">[hadoop@hadoop-localhost hadoop3.03]$ bin/yarn jar share/hadoop/mapreduce/hadoop-mapreduce-examples-3.0.3.jar wordcount /wordcountdemo/input  /wordcountdemo/output</h1>

<pre class="prettyprint"><code class=" hljs vhdl"><span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">23</span>,<span class="hljs-number">956</span> INFO client.RMProxy: Connecting <span class="hljs-keyword">to</span> ResourceManager at hadoop-localhost/<span class="hljs-number">192.168</span><span class="hljs-number">.145</span><span class="hljs-number">.129</span>:<span class="hljs-number">8032</span>
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">24</span>,<span class="hljs-number">565</span> INFO mapreduce.JobResourceUploader: Disabling Erasure Coding <span class="hljs-keyword">for</span> path: /tmp/hadoop-yarn/staging/hadoop/.staging/job_1530615244194_0002
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">24</span>,<span class="hljs-number">879</span> INFO input.FileInputFormat: Total input files <span class="hljs-keyword">to</span> <span class="hljs-keyword">process</span> : <span class="hljs-number">1</span>
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">25</span>,<span class="hljs-number">784</span> INFO mapreduce.JobSubmitter: number <span class="hljs-keyword">of</span> splits:<span class="hljs-number">1</span>
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">25</span>,<span class="hljs-number">841</span> INFO <span class="hljs-keyword">Configuration</span>.deprecation: yarn.resourcemanager.system-metrics-publisher.enabled <span class="hljs-keyword">is</span> deprecated. Instead, <span class="hljs-keyword">use</span> yarn.system-metrics-publisher.enabled
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">26</span>,<span class="hljs-number">314</span> INFO mapreduce.JobSubmitter: Submitting tokens <span class="hljs-keyword">for</span> job: job_1530615244194_0002
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">26</span>,<span class="hljs-number">315</span> INFO mapreduce.JobSubmitter: Executing <span class="hljs-keyword">with</span> tokens: []
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">26</span>,<span class="hljs-number">466</span> INFO conf.<span class="hljs-keyword">Configuration</span>: resource-types.xml <span class="hljs-keyword">not</span> found
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">26</span>,<span class="hljs-number">466</span> INFO resource.ResourceUtils: Unable <span class="hljs-keyword">to</span> find <span class="hljs-attribute">'resource</span>-types.xml'.
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">26</span>,<span class="hljs-number">547</span> INFO impl.YarnClientImpl: Submitted application application_1530615244194_0002
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">26</span>,<span class="hljs-number">590</span> INFO mapreduce.Job: The url <span class="hljs-keyword">to</span> track the job: http://hadoop-localhost:<span class="hljs-number">8088</span>/proxy/application_1530615244194_0002/
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">26</span>,<span class="hljs-number">590</span> INFO mapreduce.Job: Running job: job_1530615244194_0002
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">35</span>,<span class="hljs-number">985</span> INFO mapreduce.Job: Job job_1530615244194_0002 running <span class="hljs-keyword">in</span> uber mode : false
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">35</span>,<span class="hljs-number">988</span> INFO mapreduce.Job:  <span class="hljs-keyword">map</span> <span class="hljs-number">0</span>% reduce <span class="hljs-number">0</span>%
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">42</span>,<span class="hljs-number">310</span> INFO mapreduce.Job:  <span class="hljs-keyword">map</span> <span class="hljs-number">100</span>% reduce <span class="hljs-number">0</span>%
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">47</span>,<span class="hljs-number">402</span> INFO mapreduce.Job:  <span class="hljs-keyword">map</span> <span class="hljs-number">100</span>% reduce <span class="hljs-number">100</span>%
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">49</span>,<span class="hljs-number">469</span> INFO mapreduce.Job: Job job_1530615244194_0002 completed successfully
<span class="hljs-number">2018</span>-<span class="hljs-number">07</span>-<span class="hljs-number">03</span> <span class="hljs-number">19</span>:<span class="hljs-number">38</span>:<span class="hljs-number">49</span>,<span class="hljs-number">579</span> INFO mapreduce.Job: Counters: <span class="hljs-number">53</span>
    <span class="hljs-keyword">File</span> System Counters
        <span class="hljs-keyword">FILE</span>: Number <span class="hljs-keyword">of</span> bytes read=<span class="hljs-number">94</span>
        <span class="hljs-keyword">FILE</span>: Number <span class="hljs-keyword">of</span> bytes written=<span class="hljs-number">403931</span>
        <span class="hljs-keyword">FILE</span>: Number <span class="hljs-keyword">of</span> read operations=<span class="hljs-number">0</span>
        <span class="hljs-keyword">FILE</span>: Number <span class="hljs-keyword">of</span> large read operations=<span class="hljs-number">0</span>
        <span class="hljs-keyword">FILE</span>: Number <span class="hljs-keyword">of</span> write operations=<span class="hljs-number">0</span>
        HDFS: Number <span class="hljs-keyword">of</span> bytes read=<span class="hljs-number">195</span>
        HDFS: Number <span class="hljs-keyword">of</span> bytes written=<span class="hljs-number">60</span>
        HDFS: Number <span class="hljs-keyword">of</span> read operations=<span class="hljs-number">8</span>
        HDFS: Number <span class="hljs-keyword">of</span> large read operations=<span class="hljs-number">0</span>
        HDFS: Number <span class="hljs-keyword">of</span> write operations=<span class="hljs-number">2</span>
    Job Counters 
        Launched <span class="hljs-keyword">map</span> tasks=<span class="hljs-number">1</span>
        Launched reduce tasks=<span class="hljs-number">1</span>
        Data-local <span class="hljs-keyword">map</span> tasks=<span class="hljs-number">1</span>
        Total <span class="hljs-typename">time</span> spent by <span class="hljs-keyword">all</span> maps <span class="hljs-keyword">in</span> occupied slots (ms)=<span class="hljs-number">4573</span>
        Total <span class="hljs-typename">time</span> spent by <span class="hljs-keyword">all</span> reduces <span class="hljs-keyword">in</span> occupied slots (ms)=<span class="hljs-number">2981</span>
        Total <span class="hljs-typename">time</span> spent by <span class="hljs-keyword">all</span> <span class="hljs-keyword">map</span> tasks (ms)=<span class="hljs-number">4573</span>
        Total <span class="hljs-typename">time</span> spent by <span class="hljs-keyword">all</span> reduce tasks (ms)=<span class="hljs-number">2981</span>
        Total vcore-milliseconds taken by <span class="hljs-keyword">all</span> <span class="hljs-keyword">map</span> tasks=<span class="hljs-number">4573</span>
        Total vcore-milliseconds taken by <span class="hljs-keyword">all</span> reduce tasks=<span class="hljs-number">2981</span>
        Total megabyte-milliseconds taken by <span class="hljs-keyword">all</span> <span class="hljs-keyword">map</span> tasks=<span class="hljs-number">4682752</span>
        Total megabyte-milliseconds taken by <span class="hljs-keyword">all</span> reduce tasks=<span class="hljs-number">3052544</span>
    <span class="hljs-keyword">Map</span>-Reduce Framework
        <span class="hljs-keyword">Map</span> input records=<span class="hljs-number">4</span>
        <span class="hljs-keyword">Map</span> output records=<span class="hljs-number">11</span>
        <span class="hljs-keyword">Map</span> output bytes=<span class="hljs-number">115</span>
        <span class="hljs-keyword">Map</span> output materialized bytes=<span class="hljs-number">94</span>
        Input split bytes=<span class="hljs-number">122</span>
        Combine input records=<span class="hljs-number">11</span>
        Combine output records=<span class="hljs-number">7</span>
        Reduce input groups=<span class="hljs-number">7</span>
        Reduce shuffle bytes=<span class="hljs-number">94</span>
        Reduce input records=<span class="hljs-number">7</span>
        Reduce output records=<span class="hljs-number">7</span>
        Spilled Records=<span class="hljs-number">14</span>
        Shuffled Maps =<span class="hljs-number">1</span>
        Failed Shuffles=<span class="hljs-number">0</span>
        Merged <span class="hljs-keyword">Map</span> outputs=<span class="hljs-number">1</span>
        GC <span class="hljs-typename">time</span> elapsed (ms)=<span class="hljs-number">171</span>
        CPU <span class="hljs-typename">time</span> spent (ms)=<span class="hljs-number">1630</span>
        Physical memory (bytes) snapshot=<span class="hljs-number">332750848</span>
        Virtual memory (bytes) snapshot=<span class="hljs-number">5473169408</span>
        Total committed heap usage (bytes)=<span class="hljs-number">165810176</span>
        Peak <span class="hljs-keyword">Map</span> Physical memory (bytes)=<span class="hljs-number">214093824</span>
        Peak <span class="hljs-keyword">Map</span> Virtual memory (bytes)=<span class="hljs-number">2733207552</span>
        Peak Reduce Physical memory (bytes)=<span class="hljs-number">118657024</span>
        Peak Reduce Virtual memory (bytes)=<span class="hljs-number">2739961856</span>
    Shuffle Errors
        BAD_ID=<span class="hljs-number">0</span>
        CONNECTION=<span class="hljs-number">0</span>
        IO_ERROR=<span class="hljs-number">0</span>
        WRONG_LENGTH=<span class="hljs-number">0</span>
        WRONG_MAP=<span class="hljs-number">0</span>
        WRONG_REDUCE=<span class="hljs-number">0</span>
    <span class="hljs-keyword">File</span> Input Format Counters 
        Bytes Read=<span class="hljs-number">73</span>
    <span class="hljs-keyword">File</span> Output Format Counters 
        Bytes Written=<span class="hljs-number">60</span>
[hadoop@hadoop-localhost hadoop3<span class="hljs-number">.03</span>]$</code></pre>

<p>输出统计结果为:</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@hadoop</span>-localhost hadoop3.<span class="hljs-number">03</span>]<span class="hljs-variable">$ </span>bin/hdfs dfs -cat /wordcountdemo/output/part-r-<span class="hljs-number">00000</span>
hadoop  <span class="hljs-number">3</span>
hbase   <span class="hljs-number">1</span>
hive    <span class="hljs-number">2</span>
mapreduce   <span class="hljs-number">1</span>
spark   <span class="hljs-number">2</span>
sqoop   <span class="hljs-number">1</span>
storm   <span class="hljs-number">1</span>
[hadoop<span class="hljs-variable">@hadoop</span>-localhost hadoop3.<span class="hljs-number">03</span>]<span class="hljs-variable">$ </span>
</code></pre>

<p>结果是按照键值排好序的</p>



<h1 id="停止hadoop">停止Hadoop</h1>

<p>sbin/hadoop-daemon.sh stop namenode <br>
sbin/hadoop-daemon.sh stop datanode <br>
sbin/yarn-daemon.sh stop resourcemanager <br>
sbin/yarn-daemon.sh stop nodemanager</p>

<p>全部停止批处理文件 <br>
sbin/stop_yarn.sh <br>
sbin/stop_dfs.sh</p>

<p>sbin/stop_all.sh</p>



<h1 id="hdfs模块简介">HDFS模块简介</h1>

<p>HDFS负责大数据的存储，通过将大文件分块后进行分布式存储方式，突破了服务器硬盘大小的限制，解决了单台机器无法存储大文件的问题，HDFS是个相对独立的模块，可以为YARN提供服务，也可以为HBase等其他模块提供服务。</p>



<h1 id="yarn模块简介">YARN模块简介</h1>

<p>YARN是一个通用的资源协同和任务调度框架，是为了解决Hadoop1.x中MapReduce里NameNode负载太大和其他问题而创建的一个框架。</p>

<p>YARN是个通用框架，不止可以运行MapReduce，还可以运行Spark、Storm等其他计算框架。</p>



<h1 id="mapreduce模块简介">MapReduce模块简介</h1>

<p>MapReduce是一个计算框架，它给出了一种数据处理的方式，即通过Map阶段、Reduce阶段来分布式地流式处理数据。它只适用于大数据的离线处理，对实时性要求很高的应用不适用。</p>

<p>—-the—–end—-</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
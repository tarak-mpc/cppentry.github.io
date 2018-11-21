---
layout:     post
title:      hadoop yarn 配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/guotong1988/article/details/47130701				</div>
								            <div id="content_views" class="markdown_views prism-github-gist">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>步骤一：上传hadoop-2.2.0.tar.gz 并解压到/home/hadoop/yarn目录，此时在yarn目录中解压出hadoop-2.2.0目录 <br>
sudo chown -R hadoop:hadoop hadoop-2.2.0 <br>
创建Hadoop数据目录： <br>
mkdir -p /home/hadoop/yarn/yarn_data/hdfs/namenode <br>
mkdir -p /home/hadoop/yarn/yarn_data/hdfs/datanode</p>

<p>配置文件之前先大体介绍一下hadoop-2.2.0目录中的各个文件夹，注意区分与Hadoop1中的改变。 <br>
    外层的启动脚本在sbin目录 <br>
    内层的被调用脚本在bin目录 <br>
    Native的so文件都在lib/native目录 <br>
    配置程序文件都放置在libexec <br>
    配置文件都在etc目录，对应以前版本的conf目录 <br>
    所有的jar包都在share/hadoop目录下面</p>

<p>步骤二：配置环境变量</p>

<p>在这里我自己没有将环境全局化所以在hadoop-2.2.0中没有配置系统环境/etc/profile <br>
如果配置，执行执行source /etc/profile，使之生效。</p>

<p>步骤三：core-site.xml hdfs-site.xml mapred-site.xml yarn-site.xml hadoop-env.sh配置 <br>
下来我们的具体配置就是/home/hadoop/yarn/hadoop-2.2.0/etc/hadoop目录中进行。</p>

<p>hadoop-env.sh里的export JAVA_HOME使用绝对路径，比如</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/home/spark/java</code></pre>

<p>core-site.xml配置</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span> &gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.default.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>指定NameNode的IP地址和端口号<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>hdfs-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>2<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>备份数<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/home/hadoop/yarn/yarn_data/hdfs/namenode<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/home/hadoop/yarn/yarn_data/hdfs/datanode<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>mapred-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span> 
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span> 
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span> 
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.jobhistory.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:10020<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.jobhistory.webapp.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:19888<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>        </code></pre>

<p>yarn-site.xml</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
<span class="hljs-comment">&lt;!-- Site specific YARN configuration properties --&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:8032<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.scheduler.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:8030<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.resource-tracker.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:8031<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>   
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.admin.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:8033<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>   
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.webapp.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
　　<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>localhost:8088<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>  
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span> 
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span> 
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span> 
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>   
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span> 
<span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services.mapreduce.shuffle.class<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span> 
<span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.hadoop.mapred.ShuffleHandler<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span> 
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span> 
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>步骤四：slaves配置</p>

<p>因为是伪分布式，所以我们只有localhost</p>

<p>步骤五：将配置好的hadoop-2.2.0分发同步到各个数据节点</p>

<p>因为是伪分布式，这步跳过。</p>

<p>步骤六：格式化NameNode</p>

<p>执行命令：</p>

<p>bin/hdfs namenode –format</p>

<p>或者</p>

<p>bin/hadoop namenode –format</p>

<p>步骤七：启动hdfs和yarn</p>

<p>启动hdfs:</p>

<p>sbin/start-dfs.sh</p>

<p>启动yarn:</p>

<p>sbin/start-yarn.sh</p>

<p>或者可以执行</p>

<p>sbin/start-all.sh</p>

<p>一起启动hdfs和yarn。</p>

<p>另外还要启动history服务，不然在面板中不能打开history链接。</p>

<p>sbin/mr-jobhistory-daemon.sh start historyserver</p>

<p>下面使用jps命令查看启动进程：</p>

<p>4504 ResourceManager <br>
4066 DataNode <br>
4761 NodeManager <br>
5068 JobHistoryServer <br>
4357 SecondaryNameNode <br>
3833 NameNode <br>
5127 Jps</p>

<p>步骤八：测试</p>

<p>hdfs测试：</p>

<p>在hdfs中创建文件：bin/hadoop fs -mkdir /wordcount <br>
向hdfs中上传文件：hadoop fs –copyFromLocal localSrc dest  <br>
查看hdfs文件目录：hadoop fs –ls /wordcount</p>

<p>Yarn测试： 运行WordCount测试程序，</p>

<p>bin/hadoop jar hadoop-mapreduce-examples-2.2.0.jar的绝对路径 wordcount /wordcount /output2</p>

<p>具体查看结果：</p>

<p>bin/hadoop fs -cat /output2/*</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
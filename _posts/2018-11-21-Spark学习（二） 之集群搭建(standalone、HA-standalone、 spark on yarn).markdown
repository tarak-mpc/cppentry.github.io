---
layout:     post
title:      Spark学习（二） 之集群搭建(standalone、HA-standalone、 spark on yarn)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-dracula">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>文章目录</h3><ul><ul><ul><li><a href="#Spark_standalone_7" rel="nofollow">Spark standalone</a></li><ul><li><a href="#__8" rel="nofollow">(一) 安装过程</a></li><li><a href="#_62" rel="nofollow">（二）启动</a></li><li><a href="#_80" rel="nofollow">（三）验证</a></li></ul><li><a href="#Spark_standalone_HA_95" rel="nofollow">Spark standalone HA</a></li><ul><ul><li><a href="#_98" rel="nofollow">主备切换</a></li></ul><li><a href="#_111" rel="nofollow">集群配置</a></li></ul><li><a href="#Spark_on_YARN_171" rel="nofollow">Spark on YARN</a></li></ul></ul></ul></div><p></p>
<p>(一) Spark安装包下载<br>
（1）官网下载<br>
<a href="http://spark.apache.org/downloads.html" rel="nofollow">http://spark.apache.org/downloads.html</a><br>
<img alt="spark" src="https://img-blog.csdnimg.cn/20181115184800787.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3RoeXl5eXl5eQ==,size_16,color_FFFFFF,t_70"></p>
<h3><a id="Spark_standalone_7"></a>Spark standalone</h3>
<h4><a id="__8"></a>(一) 安装过程</h4>
<p>1、上传并解压缩</p>
<pre><code>tar -zxvf spark-2.3.0-bin-hadoop2.7.tgz -C apps/
</code></pre>
<p>2、进入spark/conf修改配置文件</p>
<pre><code>cp slaves.template slaves
 cp spark-env.sh.template spark-env.sh
</code></pre>
<p>3、修改 slaves文件，在其中添加从节点</p>
<pre><code>node02
node03
node04
</code></pre>
<p>4、修改 spark-env.sh文件</p>
<pre><code># export JAVA_HOME=/opt/software/jdk1.8.0_151 # centOS 7 需要引入JAVA_HOME
SPARK_MASTER_IP=node01 master所在节点
SPARK_MASTER_PORT=7077 master资源通信端口
SPARK_WORKER_CORES=2 worker管理的核数
SPARK_WORKER_MEMORY=800m worker管理的内存
SPARK_WORKER_INSTANCES=1 每个节点启动worker的个数
SPARK_WORKER_DIR=/var/zgl/spark worker的工作目录
# SPARK_MASTER_WEBUI_PORT=8888 WebUI的端口号，默认为8080，与tomcat冲突

</code></pre>
<p>5、进入 sbin 目录，做如下修改，防止这两个命令与 hadoop 命令冲突</p>
<pre><code>mv start-all.sh start-spark.sh 
mv stop-all.sh  stop-spark.sh  
</code></pre>
<p>6、将配置好的spark安装包发送到其他节点和客户端节点</p>
<pre><code># scp -r spark-2.3.0 node02:`pwd`
# scp -r spark-2.3.0 node03:`pwd`
# scp -r spark-2.3.0 node04:`pwd`
# scp -r spark-2.3.0 client:`pwd`
</code></pre>
<p>7、在 node01 和 client 节点上配置 spark 的环境变量，配置到 ~/.bashrc 中</p>
<pre><code>vim ~/.bashrc

export SPARK_HOME=/opt/zgl/spark-1.6.3
export PATH=$PATH:$SPARK_HOME/bin:$SPARK_HOME/sbin
</code></pre>
<h4><a id="_62"></a>（二）启动</h4>
<p>1、先启动zookeeper集群<br>
所有zookeeper节点启动</p>
<pre><code>zkServer.sh start
</code></pre>
<p>2、在node01启动HDFS集群</p>
<pre><code>start-dfs.sh
</code></pre>
<p>3、在 node01 上启动集群</p>
<pre><code>start-spark.sh
</code></pre>
<p>4、jps 检查集群是否启动</p>
<h4><a id="_80"></a>（三）验证</h4>
<p>1、在客户端提交 spark 提供的 SparkPi 程序（该程序通过概率计算 π 的值）</p>
<ul>
<li>注意提交命令一行写完，不要换行<br>
任务很可能会因为内存不足导致失败</li>
</ul>
<pre><code>spark-submit  master的url  执行器所使用的内存  运行的程序的全类名  程序jar包所在的位置  提供一个参数（参数越大，计算的π值越精准） 
spark-submit --master spark://node01:7077 --executor-memory 500m  
--class org.apache.spark.examples.SparkPi  
/opt/software/spark-2.3.0/lib/spark-examples-2.3.0-hadoop2.6.0.jar  
50 
</code></pre>
<p>2、可以在 node01:8080 WEBUI页面查看任务<br>
<img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181106201042514.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=,size_16,color_FFFFFF,t_70"></p>
<h3><a id="Spark_standalone_HA_95"></a>Spark standalone HA</h3>
<p>同 HDFS 的 NameNode，YARN 的 ResourceManager 一样，spark standalone 的 master 也存在单点故障问题，于是同样也就有了基于 zookeeper 监控的高可用模式（spark standalone 还有一种高可用模式是基于本地文件系统，主master挂掉后需要手动切换）</p>
<h5><a id="_98"></a>主备切换</h5>
<blockquote>
<ul>
<li>spark standalone HA 模式中，master(active) 会将元数据同步到 zookeeper 中，元数据中有 worker，driver 和 application 的信息</li>
<li>当 master(active) 挂掉时，zookeeper 会选举出一个 master(standby)，被选中的 master(standby) 进入恢复状态 master(recovering)</li>
<li>master(recovering) 从 zookeeper 读取元数据，得到元数据后，master(recovering) 会向 worker 节点发送消息，告知主master已经更换</li>
<li>正在正常运行的 worker 在收到通知后，会向 master(recovering) 节点发送响应信息</li>
<li>master(recovering) 节点收到响应信息后，会调用自身的completeRecovery()方法，此时未向 master(recovering) 节点发送响应信息的 worker 节点会被认为已经挂掉，从 workers 中删除（workers<br>
是存储 worker 信息的对象）</li>
<li>短暂的 completeRecovery 状态一闪而过，master(completeRecovery) 成为 active 状态，开始对外提供服务</li>
</ul>
</blockquote>
<p>–主备切换的过程大概需要1~2分钟，此期间集群不接受提交任务的请求，但是已经跑在集群上的任务不会受到影响，会正常执行，这得益于 spark 粗粒度的资源调度<br>
–workers 使用 HashSet 数据结构来存储 worker 信息，是为了防止同一台 worker 节点在 master 中注册两次（worker 节点挂掉但是迅速恢复可能会导致此问题）</p>
<h4><a id="_111"></a>集群配置</h4>
<p>1、 在上面 standalone 集群配置的基础上，先关闭之前开启的集群</p>
<pre><code>stop-spark.sh
</code></pre>
<p>2、在 node01 节点上对 spark 安装包中的 spark-env.sh文件添加如下配置，注意写在一行，不要手动换行，三个参数间使用空格隔开</p>
<pre><code>SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER
 -Dspark.deploy.zookeeper.url=node02:2181,node03:2181,node04:2181 
 -Dspark.deploy.zookeeper.dir=/spark"
</code></pre>
<blockquote>
<p>官网对三个参数的解释<img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181112191359862.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=,size_16,color_FFFFFF,t_70"></p>
</blockquote>
<p>3、将 spark-env.sh发送到其他节点，进入 spark 安装包的 conf 目录下执行</p>
<pre><code>scp spark-env.sh node02:`pwd`
scp spark-env.sh node03:`pwd`
scp spark-env.sh node04:`pwd`
scp spark-env.sh client:`pwd`
</code></pre>
<p>4、选用 node02 节点作为备用的 master 节点，在 node02 节点的 spark 安装包中的 spark-env.sh文件中修改 SPARK_MASTER_IP，令 SPARK_MASTER_IP=node02<br>
5、启动zookeeper，spark集群</p>
<pre><code>zkServer.sh start
start-spark.sh

</code></pre>
<p>6、node02启动master(standby状态)</p>
<pre><code>start-master.sh
</code></pre>
<p>7、node02:8888 WEBUI页面查看节点状态信息<br>
<img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181113083419838.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=,size_16,color_FFFFFF,t_70"></p>
<p>将 nodeo1 上的 master 进程 kill 掉，然后观察 master(standby) 的状态变化<br>
<img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181113091519451.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=,size_16,color_FFFFFF,t_70"></p>
<p><img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181113091419758.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=,size_16,color_FFFFFF,t_70"></p>
<p>在客户端提交 spark 提供的 SparkPi 程序</p>
<pre><code>spark-submit --master spark://node01:7077,node02:7077 
--class org.apache.spark.examples.SparkPi 
/opt/software/spark-1.6.3/lib/spark-examples-1.6.3-hadoop2.6.0.jar 
100
</code></pre>
<p>在 WEBUI 页面查看任务<br>
<img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181113092137287.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=,size_16,color_FFFFFF,t_70"></p>
<h3><a id="Spark_on_YARN_171"></a>Spark on YARN</h3>
<p>spark 应用程序跑在 yarn 集群上很简单，只需要在客户端有 spark 安装包就可以了(用来提交 spark 应用程序)</p>
<ol>
<li>在客户端 spark 安装包中配置 spark-env.sh文件，添加如下配置信息</li>
</ol>
<pre><code>HADOOP_CONF_DIR=/opt/zgl/hadoop-2.6.5/etc/hadoop
</code></pre>
<ol start="2">
<li>开启 HDFS集群和 YARN集群，客户端提交的应用程序 jar 包会托管在 hdfs 上</li>
<li>在客户端提交 spark 提供的 SparkPi 程序</li>
</ol>
<pre><code>spark-submit --master yarn
 --class org.apache.spark.examples.SparkPi 
 /opt/zgl/spark-1.6.3/lib/spark-examples-1.6.3-hadoop2.6.0.jar 
 10000
</code></pre>
<ol start="4">
<li>在 yarn 的 WebUI 页面查看任务<br>
<img alt="在这里插入图片描述" src="https://img-blog.csdnimg.cn/20181109154019968.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=,size_16,color_FFFFFF,t_70"></li>
</ol>
<p>提交任务可能会报下面这个异常，主要是因为虚拟内存超限， contrainer 被 kill，从而导致任务结束</p>
<pre><code>Yarn application has already ended! It might have been killed or unable to launch application master
</code></pre>
<p>可以通过在 hadoop 安装包的 yarn-site.xml 文件中配置如下信息来解决</p>
<pre><code>&lt;property&gt;
    &lt;name&gt;yarn.nodemanager.pmem-check-enabled&lt;/name&gt;
    &lt;value&gt;false&lt;/value&gt;
    &lt;description&gt;是否检查每个任务正使用的物理内存量，如果超过默认值则将其杀死，默认是true &lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
    &lt;name&gt;yarn.nodemanager.vmem-check-enabled&lt;/name&gt;
    &lt;value&gt;false&lt;/value&gt;
    &lt;description&gt;Whether virtual memory limits will be enforced for containers&lt;/description&gt;
&lt;/property&gt;
</code></pre>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
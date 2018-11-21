---
layout:     post
title:      Linux安装Spark集群(CentOS7+Spark2.1.1+Hadoop2.8.0)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1 安装Spark依赖的Scala</p>

<p>          1.1下载和解压缩Scala</p>

<p>          1.2 配置环境变量</p>

<p>          1.3 验证Scala</p>

<p>2下载和解压缩Spark</p>

<p>          2.1 下载Spark压缩包</p>

<p>          2.2 解压缩Spark</p>

<p>3 Spark相关的配置</p>

<p>          3.1 配置环境变量</p>

<p>          3.2 配置conf目录下的文件</p>

<p>                  3.2.1 新建spark-env.h文件</p>

<p>                  3.2.2 新建slaves文件</p>

<p>4 启动和测试Spark集群</p>

<p>         4.1 启动Spark</p>

<p>         4.2 测试和使用Spark集群</p>

<p>                 4.2.1访问Spark集群提供的URL</p>

<p>                 4.2.2运行Spark提供的计算圆周率的示例程序</p>

<p> </p>

<p>关键字：Linux   CentOS  Hadoop  Spark   Scala   Java</p>

<p>版本号：CentOS7  Hadoop2.8.0  Spark2.1.1   Scala2.12.2   JDK1.8</p>

<p><strong> </strong></p>

<p><strong>          说明：Spark可以在只安装了JDK、Scala的机器上直接单机安装，但是这样的话只能使用单机模式运行不涉及分布式运算和分布式存储的代码，例如可以单机安装Spark，单机运行计算圆周率的Spark程序。</strong><strong>但是我们要运行的是Spark集群，并且需要调用Hadoop的分布式文件系统，</strong><strong>所以请你先安装</strong><strong>Hadoop</strong></p>

<p> </p>

<p><strong>在这里相信有许多想要学习大数据的同学，大家可以+下大数据学习裙：716加上【五8一】最后014，即可免费领取一整套系统的大数据学习教程</strong></p>

<p>     </p>

<p><strong>        Spark集群的最小化安装只需要安装这些东西：JDK  、Scala  、Hadoop  、Spark</strong></p>

<h1 id="h1_1">1 安装Spark依赖的Scala</h1>

<p>          Hadoop的安装请参考上面提到的博文，因为Spark依赖scala，所以在安装Spark之前，这里要先安装scala。在每个节点上都进行安装。</p>

<h2 id="h2_2">1.1  下载和解压缩Scala</h2>

<p>       <strong> 如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516222042850?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p> </p>

<p><strong>如图：</strong></p>

<p><strong>      <img alt="" class="has" src="https://img-blog.csdn.net/20170516222112491?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p> </p>

<p> </p>

<p> </p>

<p>在Linux服务器的opt目录下新建一个名为scala的文件夹，并将下载的压缩包上载上去</p>

<p><strong>如图：</strong></p>

<p>   <img alt="" class="has" src="https://img-blog.csdn.net/20170516222155304?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p>   </p>

<p>执行命令，进入到该目录：</p>

<p><strong>cd    /opt/scala</strong></p>

<p>执行命令进行解压缩：</p>

<p><strong>tar   -xvf    scala-2.12.2</strong></p>

<h2 id="h2_3">1.2  配置环境变量</h2>

<p>       编辑/etc/profile这个文件，在文件中增加一行配置：</p>

<p> </p>

<pre class="has">
<code>export    SCALA_HOME=/opt/scala/scala-2.12.2</code></pre>

<p> </p>

<p>      在该文件的PATH变量中增加下面的内容：</p>

<p> </p>

<pre class="has">
<code>  ${SCALA_HOME}/bin</code></pre>

<p> </p>

<p> </p>

<p>      添加完成后，我的/etc/profile的配置如下：</p>

<p> </p>

<pre>

 </pre>

<ol><li>
	<p><code>export JAVA_HOME=/opt/java/jdk1.8.0_121</code></p>
	</li>
	<li>
	<p><code>export HADOOP_HOME=/opt/hadoop/hadoop-2.8.0</code></p>
	</li>
	<li>
	<p><code>export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop</code></p>
	</li>
	<li>
	<p><code>export HADOOP_COMMON_LIB_NATIVE_DIR=${HADOOP_HOME}/lib/native</code></p>
	</li>
	<li>
	<p><code>export HADOOP_OPTS="-Djava.library.path=${HADOOP_HOME}/lib"</code></p>
	</li>
	<li>
	<p><code>export HIVE_HOME=/opt/hive/apache-hive-2.1.1-bin</code></p>
	</li>
	<li>
	<p><code>export HIVE_CONF_DIR=${HIVE_HOME}/conf</code></p>
	</li>
	<li>
	<p><code>export SQOOP_HOME=/opt/sqoop/sqoop-1.4.6.bin__hadoop-2.0.4-alpha</code></p>
	</li>
	<li>
	<p><code>export HBASE_HOME=/opt/hbase/hbase-1.2.5</code></p>
	</li>
	<li>
	<p><code>export ZK_HOME=/opt/zookeeper/zookeeper-3.4.10</code></p>
	</li>
	<li>
	<p><code>export SCALA_HOME=/opt/scala/scala-2.12.2</code></p>
	</li>
	<li>
	<p><code>export CLASS_PATH=.:${JAVA_HOME}/lib:${HIVE_HOME}/lib:$CLASS_PATH</code></p>
	</li>
	<li>
	<p><code>export PATH=.:${JAVA_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${SPARK_HOME}/bin:${ZK_HOME}/bin:${HIVE_HOME}/bin:${SQOOP_HOME}/bin:${HBASE_HOME}/bin:${SCALA_HOME}/bin:$PATH</code></p>
	</li>
</ol><p>   <strong>      说明：你可以只关注开头说的JDK  SCALA   Hadoop  Spark的环境变量，其余的诸如Zookeeper、Hbase、Hive、Sqoop都不用管。</strong></p>

<p><strong>        如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516222218586?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p> </p>

<p>      环境变量配置完成后，执行下面的命令：</p>

<p> </p>

<pre class="has">
<code>source   /etc/profile</code></pre>

<p> </p>

<h2 id="h2_4">1.3 验证Scala</h2>

<p>    执行命令：</p>

<p> </p>

<pre class="has">
<code>scala     -version</code></pre>

<p> </p>

<p><strong>   如图：</strong></p>

<p>    <img alt="" class="has" src="https://img-blog.csdn.net/20170516222239196?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<h1 id="h1_5">2 下载和解压缩Spark</h1>

<p>      在每个节点上都安装Spark，也就是重复下面的步骤。</p>

<h2 id="h2_6"> </h2>

<p><strong>  如图：</strong></p>

<p>        <img alt="" class="has" src="https://img-blog.csdn.net/20170516222259055?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>

<p><strong> </strong></p>

<p><strong>     </strong></p>

<h2 id="h2_7">2.2  解压缩Spark</h2>

<p>      在Linux服务器的opt目录下新建一个名为spark的文件夹，把刚才下载的压缩包，上载上去。</p>

<p><strong>如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516222333680?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p><strong>进入到该目录内，也就是执行下面的命令：</strong></p>

<p><strong>cd    /opt/spark</strong></p>

<p><strong>执行解压缩命令：</strong></p>

<p><strong>tar   -zxvf   spark-2.1.1-bin-hadoop2.7.tgz</strong></p>

<h1 id="h1_8">3  Spark相关的配置</h1>

<p>         说明：因为我们搭建的是基于hadoop集群的Spark集群，所以每个hadoop节点上我都安装了Spark，都需要按照下面的步骤做配置，启动的话只需要在Spark集群的Master机器上启动即可，我这里是在hserver1上启动。</p>

<h2 id="h2_9">3.1  配置环境变量</h2>

<p>编辑/etc/profile文件，增加</p>

<p> </p>

<pre class="has">
<code>export  SPARK_HOME=/opt/spark/spark-2.1.1-bin-hadoop2.7</code></pre>

<p> </p>

<p>      上面的变量添加完成后编辑该文件中的PATH变量，添加</p>

<p> </p>

<pre class="has">
<code>${SPARK_HOME}/bin</code></pre>

<p> </p>

<p>      注意：因为$SPARK_HOME/sbin目录下有一些文件名称和$HADOOP_HOME/sbin目录下的文件同名，为了避免同名文件冲突，这里不在PATH变量里添加<strong>$SPARK_HOME/sbin只添加了<strong>$SPARK_HOME/bin</strong></strong>。</p>

<p>修改完成后，我的/etc/profile文件内容是：</p>

<p> </p>

<pre>

 </pre>

<ol><li>
	<p><code>export JAVA_HOME=/opt/java/jdk1.8.0_121</code></p>
	</li>
	<li>
	<p><code>export HADOOP_HOME=/opt/hadoop/hadoop-2.8.0</code></p>
	</li>
	<li>
	<p><code>export HADOOP_CONF_DIR=${HADOOP_HOME}/etc/hadoop</code></p>
	</li>
	<li>
	<p><code>export HADOOP_COMMON_LIB_NATIVE_DIR=${HADOOP_HOME}/lib/native</code></p>
	</li>
	<li>
	<p><code>export HADOOP_OPTS="-Djava.library.path=${HADOOP_HOME}/lib"</code></p>
	</li>
	<li>
	<p><code>export HIVE_HOME=/opt/hive/apache-hive-2.1.1-bin</code></p>
	</li>
	<li>
	<p><code>export HIVE_CONF_DIR=${HIVE_HOME}/conf</code></p>
	</li>
	<li>
	<p><code>export SQOOP_HOME=/opt/sqoop/sqoop-1.4.6.bin__hadoop-2.0.4-alpha</code></p>
	</li>
	<li>
	<p><code>export HBASE_HOME=/opt/hbase/hbase-1.2.5</code></p>
	</li>
	<li>
	<p><code>export ZK_HOME=/opt/zookeeper/zookeeper-3.4.10</code></p>
	</li>
	<li>
	<p><code>export SCALA_HOME=/opt/scala/scala-2.12.2</code></p>
	</li>
	<li>
	<p><code>export SPARK_HOME=/opt/spark/spark-2.1.1-bin-hadoop2.7</code></p>
	</li>
	<li>
	<p><code>export CLASS_PATH=.:${JAVA_HOME}/lib:${HIVE_HOME}/lib:$CLASS_PATH</code></p>
	</li>
	<li>
	<p><code>export PATH=.:${JAVA_HOME}/bin:${HADOOP_HOME}/bin:${HADOOP_HOME}/sbin:${SPARK_HOME}/bin:${ZK_HOME}/bin:${HIVE_HOME}/bin:${SQOOP_HOME}/bin:${HBASE_HOME}:${SCALA_HOME}/bin:$PATH</code></p>
	</li>
</ol><p> </p>

<p><strong>     <strong> 说明：你可以只关注开头说的JDK  SCALA   Hadoop  Spark的环境变量，其余的诸如Zookeeper、Hbase、Hive、Sqoop都不用管。</strong> </strong></p>

<p><strong>      如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516222354759?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p>编辑完成后，执行命令：</p>

<p><strong>source    /etc/profile</strong></p>

<h2 id="h2_10">3.2 配置conf目录下的文件</h2>

<p><strong> </strong>        对/opt/spark/spark-2.1.1-bin-hadoop2.7/conf目录下的文件进行配置。</p>

<h3 id="h3_11">3.2.1  新建spark-env.h文件</h3>

<p>        执行命令，进入到/opt/spark/spark-2.1.1-bin-hadoop2.7/conf目录内：</p>

<p><strong>cd    /opt/spark/spark-2.1.1-bin-hadoop2.7/conf</strong></p>

<p>       以spark为我们创建好的模板创建一个spark-env.h文件，命令是：</p>

<p><strong>cp    spark-env.sh.template   spark-env.sh</strong></p>

<p><strong>     如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516222415057?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p>    编辑spark-env.h文件，在里面加入配置(具体路径以自己的为准)：</p>

<p> </p>

<pre>

 </pre>

<ol><li>
	<p><code>export SCALA_HOME=/opt/scala/scala-2.12.2</code></p>
	</li>
	<li>
	<p><code>export JAVA_HOME=/opt/java/jdk1.8.0_121</code></p>
	</li>
	<li>
	<p><code>export HADOOP_HOME=/opt/hadoop/hadoop-2.8.0</code></p>
	</li>
	<li>
	<p><code>export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop</code></p>
	</li>
	<li>
	<p><code>export SPARK_HOME=/opt/spark/spark-2.1.1-bin-hadoop2.7</code></p>
	</li>
	<li>
	<p><code>export SPARK_MASTER_IP=hserver1</code></p>
	</li>
	<li>
	<p><code>export SPARK_EXECUTOR_MEMORY=1G</code></p>
	</li>
</ol><p> </p>

<h3 id="h3_12">3.2.2 新建slaves文件</h3>

<p>执行命令，进入到/opt/spark/spark-2.1.1-bin-hadoop2.7/conf目录内：</p>

<p><strong>cd   /opt/spark/spark-2.1.1-bin-hadoop2.7/conf</strong></p>

<p>以spark为我们创建好的模板创建一个slaves文件，命令是：</p>

<p><strong>cp    slaves.template   slaves</strong></p>

<p><strong>如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516222438900?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p>编辑slaves文件，里面的内容为：</p>

<p> </p>

<pre class="has">
<code>hserver2
hserver3</code></pre>

<p> </p>

<h1 id="h1_13">4 启动和测试Spark集群</h1>

<h2 id="h2_14">4.1 启动Spark</h2>

<p>          因为spark是依赖于hadoop提供的分布式文件系统的，所以在启动spark之前，先确保hadoop在正常运行。</p>

<p>        在hadoop正常运行的情况下，在hserver1（也就是hadoop的namenode，spark的marster节点）上执行命令：</p>

<p> <strong>  cd   /opt/spark/spark-2.1.1-bin-hadoop2.7/sbin</strong></p>

<p>    执行启动脚本：</p>

<p><strong>  ./start-all.sh</strong></p>

<p><strong>  如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516222513074?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p> </p>

<p>  完整内容是：</p>

<p> </p>

<pre>

 </pre>

<ol><li>
	<p><code>[root@hserver1 sbin]# cd/opt/spark/spark-2.1.1-bin-hadoop2.7/sbin</code></p>
	</li>
	<li>
	<p><code>[root@hserver1 sbin]# ./start-all.sh</code></p>
	</li>
	<li>
	<p><code>starting org.apache.spark.deploy.master.Master,logging to/opt/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-root-org.apache.spark.deploy.master.Master-1-hserver1.out</code></p>
	</li>
	<li>
	<p><code>hserver2: startingorg.apache.spark.deploy.worker.Worker, logging to/opt/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-hserver2.out</code></p>
	</li>
	<li>
	<p><code>hserver3: startingorg.apache.spark.deploy.worker.Worker, logging to/opt/spark/spark-2.1.1-bin-hadoop2.7/logs/spark-root-org.apache.spark.deploy.worker.Worker-1-hserver3.out</code></p>
	</li>
	<li>
	<p><code>[root@hserver1 sbin]#</code></p>
	</li>
</ol><p> </p>

<p><strong>         注意：上面的命令中有./这个不能少，./的意思是执行当前目录下的start-all.sh脚本。</strong></p>

<h2 id="h2_15">4.2  测试和使用Spark集群</h2>

<h3 id="h3_16">4.2.1  访问Spark集群提供的URL</h3>

<p>  </p>

<p>       <strong>如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516222530790?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<h3 id="h3_17">4.2.2  运行Spark提供的计算圆周率的示例程序</h3>

<p>      这里只是简单的用local模式运行一个计算圆周率的Demo。按照下面的步骤来操作。</p>

<p>      <strong>第一步，进入到Spark的根目录，也就是执行下面的脚本：</strong></p>

<p><strong>cd     /opt/spark/spark-2.1.1-bin-hadoop2.7</strong></p>

<p><strong>如图：</strong></p>

<p><strong>        <img alt="" class="has" src="https://img-blog.csdn.net/20170516223201021?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p><strong>第二步，调用Spark自带的计算圆周率的Demo，执行下面的命令：</strong></p>

<p> </p>

<pre class="has">
<code>./bin/spark-submit  --class  org.apache.spark.examples.SparkPi  --master local   examples/jars/spark-examples_2.11-2.1.1.jar</code></pre>

<p> </p>

<p> </p>

<p><strong>        命令执行后，spark示例程序已经开始执行</strong></p>

<p><strong>        如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516223222608?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p>        很快执行结果出来了，执行结果我用红框标出来了</p>

<p><strong>如图：</strong></p>

<p><strong><img alt="" class="has" src="https://img-blog.csdn.net/20170516223248038?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcHVjYW9fY3Vn/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></strong></p>

<p><strong>        完整的控制台输出是：</strong></p>

<p> </p>

<pre>

 </pre>

<ol><li>
	<p><code>[root@hserver1 bin]# cd /opt/spark/spark-2.1.1-bin-hadoop2.7</code></p>
	</li>
	<li>
	<p><code>[root@hserver1 spark-2.1.1-bin-hadoop2.7]# ./bin/spark-submit--class org.apache.spark.examples.SparkPi --master localexamples/jars/spark-examples_2.11-2.1.1.jar</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:23 INFO spark.SparkContext: Running Spark version2.1.1</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:24 WARN util.NativeCodeLoader: Unable to loadnative-hadoop library for your platform... using builtin-java classes whereapplicable</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:25 INFO spark.SecurityManager: Changing view acls to:root</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:25 INFO spark.SecurityManager: Changing modify aclsto: root</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:25 INFO spark.SecurityManager: Changing view aclsgroups to:</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:25 INFO spark.SecurityManager: Changing modify aclsgroups to:</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:25 INFO spark.SecurityManager: SecurityManager:authentication disabled; ui acls disabled; users with view permissions: Set(root); groups withview permissions: Set(); users withmodify permissions: Set(root); groups with modify permissions: Set()</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:25 INFO util.Utils: Successfully started service'sparkDriver' on port 40855.</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:26 INFO spark.SparkEnv: Registering MapOutputTracker</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:26 INFO spark.SparkEnv: RegisteringBlockManagerMaster</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:26 INFO storage.BlockManagerMasterEndpoint: Usingorg.apache.spark.storage.DefaultTopologyMapper for getting topology information</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:26 INFO storage.BlockManagerMasterEndpoint:BlockManagerMasterEndpoint up</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:26 INFO storage.DiskBlockManager: Created localdirectory at /tmp/blockmgr-cf8cbb42-95d2-4284-9a48-67592363976a</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:26 INFO memory.MemoryStore: MemoryStore started withcapacity 413.9 MB</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:26 INFO spark.SparkEnv: RegisteringOutputCommitCoordinator</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:26 INFO util.log: Logging initialized @5206ms</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO server.Server: jetty-9.2.z-SNAPSHOT</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@5118388b{/jobs,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Started o.s.j.s.ServletContextHandler@15a902e7{/jobs/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@7876d598{/jobs/job,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Started o.s.j.s.ServletContextHandler@4a3e3e8b{/jobs/job/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@5af28b27{/stages,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Started o.s.j.s.ServletContextHandler@71104a4{/stages/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@4985cbcb{/stages/stage,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@72f46e16{/stages/stage/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@3c9168dc{/stages/pool,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@332a7fce{/stages/pool/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@549621f3{/storage,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@54361a9{/storage/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@32232e55{/storage/rdd,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@5217f3d0{/storage/rdd/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@37ebc9d8{/environment,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@293bb8a5{/environment/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@2416a51{/executors,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@6fa590ba{/executors/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@6e9319f{/executors/threadDump,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@72e34f77{/executors/threadDump/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@7bf9b098{/static,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@389adf1d{/,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@77307458{/api,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@1fc0053e{/jobs/job/kill,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@290b1b2e{/stages/stage/kill,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO server.ServerConnector: StartedSpark@32fe9d0a{HTTP/1.1}{0.0.0.0:4040}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO server.Server: Started @5838ms</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO util.Utils: Successfully started service'SparkUI' on port 4040.</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO ui.SparkUI: Bound SparkUI to 0.0.0.0, andstarted at http://192.168.27.143:4040</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO spark.SparkContext: Added JARfile:/opt/spark/spark-2.1.1-bin-hadoop2.7/examples/jars/spark-examples_2.11-2.1.1.jarat spark://192.168.27.143:40855/jars/spark-examples_2.11-2.1.1.jar withtimestamp 1494915987472</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO executor.Executor: Starting executor IDdriver on host localhost</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO util.Utils: Successfully started service'org.apache.spark.network.netty.NettyBlockTransferService' on port 41104.</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO netty.NettyBlockTransferService: Servercreated on 192.168.27.143:41104</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO storage.BlockManager: Usingorg.apache.spark.storage.RandomBlockReplicationPolicy for block replicationpolicy</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO storage.BlockManagerMaster: RegisteringBlockManager BlockManagerId(driver, 192.168.27.143, 41104, None)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO storage.BlockManagerMasterEndpoint:Registering block manager 192.168.27.143:41104 with 413.9 MB RAM,BlockManagerId(driver, 192.168.27.143, 41104, None)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO storage.BlockManagerMaster: RegisteredBlockManager BlockManagerId(driver, 192.168.27.143, 41104, None)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:27 INFO storage.BlockManager: InitializedBlockManager: BlockManagerId(driver, 192.168.27.143, 41104, None)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:28 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@4e6d7365{/metrics/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:28 INFO internal.SharedState: Warehouse path is'file:/opt/spark/spark-2.1.1-bin-hadoop2.7/spark-warehouse'.</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:28 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@705202d1{/SQL,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:28 INFO handler.ContextHandler: Started o.s.j.s.ServletContextHandler@3e58d65e{/SQL/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:28 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@6f63c44f{/SQL/execution,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:28 INFO handler.ContextHandler: Started o.s.j.s.ServletContextHandler@62a8fd44{/SQL/execution/json,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:28 INFO handler.ContextHandler: Startedo.s.j.s.ServletContextHandler@1d035be3{/static/sql,null,AVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:30 INFO spark.SparkContext: Starting job: reduce atSparkPi.scala:38</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:30 INFO scheduler.DAGScheduler: Got job 0 (reduce atSparkPi.scala:38) with 2 output partitions</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:30 INFO scheduler.DAGScheduler: Final stage:ResultStage 0 (reduce at SparkPi.scala:38)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:30 INFO scheduler.DAGScheduler: Parents of finalstage: List()</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:30 INFO scheduler.DAGScheduler: Missing parents:List()</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:30 INFO scheduler.DAGScheduler: SubmittingResultStage 0 (MapPartitionsRDD[1] at map at SparkPi.scala:34), which has nomissing parents</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:30 INFO memory.MemoryStore: Block broadcast_0 storedas values in memory (estimated size 1832.0 B, free 413.9 MB)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:30 INFO memory.MemoryStore: Block broadcast_0_piece0stored as bytes in memory (estimated size 1167.0 B, free 413.9 MB)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO storage.BlockManagerInfo: Addedbroadcast_0_piece0 in memory on 192.168.27.143:41104 (size: 1167.0 B, free:413.9 MB)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO spark.SparkContext: Created broadcast 0 frombroadcast at DAGScheduler.scala:996</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO scheduler.DAGScheduler: Submitting 2 missingtasks from ResultStage 0 (MapPartitionsRDD[1] at map at SparkPi.scala:34)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO scheduler.TaskSchedulerImpl: Adding task set0.0 with 2 tasks</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO scheduler.TaskSetManager: Starting task 0.0in stage 0.0 (TID 0, localhost, executor driver, partition 0, PROCESS_LOCAL,6026 bytes)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO executor.Executor: Running task 0.0 in stage0.0 (TID 0)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO executor.Executor: Fetchingspark://192.168.27.143:40855/jars/spark-examples_2.11-2.1.1.jar with timestamp1494915987472</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO client.TransportClientFactory: Successfullycreated connection to /192.168.27.143:40855 after 145 ms (0 ms spent inbootstraps)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:31 INFO util.Utils: Fetchingspark://192.168.27.143:40855/jars/spark-examples_2.11-2.1.1.jar to/tmp/spark-702c8654-489f-47f2-85e0-8b658ebb2988/userFiles-0a07fa86-4d14-4939-ad2b-95ac8488e187/fetchFileTemp3302336691796081023.tmp</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:33 INFO executor.Executor: Addingfile:/tmp/spark-702c8654-489f-47f2-85e0-8b658ebb2988/userFiles-0a07fa86-4d14-4939-ad2b-95ac8488e187/spark-examples_2.11-2.1.1.jarto class loader</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO executor.Executor: Finished task 0.0 in stage0.0 (TID 0). 1114 bytes result sent to driver</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO scheduler.TaskSetManager: Starting task 1.0in stage 0.0 (TID 1, localhost, executor driver, partition 1, PROCESS_LOCAL,6026 bytes)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO executor.Executor: Running task 1.0 in stage0.0 (TID 1)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO scheduler.TaskSetManager: Finished task 0.0in stage 0.0 (TID 0) in 2815 ms on localhost (executor driver) (1/2)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO executor.Executor: Finished task 1.0 in stage0.0 (TID 1). 1114 bytes result sent to driver</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO scheduler.TaskSetManager: Finished task 1.0in stage 0.0 (TID 1) in 416 ms on localhost (executor driver) (2/2)</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO scheduler.TaskSchedulerImpl: Removed TaskSet0.0, whose tasks have all completed, from pool</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO scheduler.DAGScheduler: ResultStage 0 (reduceat SparkPi.scala:38) finished in 3.269 s</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO scheduler.DAGScheduler: Job 0 finished:reduce at SparkPi.scala:38, took 4.404894 s</code></p>
	</li>
	<li>
	<p><code>Pi is roughly 3.1434157170785855</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO server.ServerConnector: StoppedSpark@32fe9d0a{HTTP/1.1}{0.0.0.0:4040}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@290b1b2e{/stages/stage/kill,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@1fc0053e{/jobs/job/kill,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@77307458{/api,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@389adf1d{/,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@7bf9b098{/static,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@72e34f77{/executors/threadDump/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@6e9319f{/executors/threadDump,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@6fa590ba{/executors/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@2416a51{/executors,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@293bb8a5{/environment/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stopped o.s.j.s.ServletContextHandler@37ebc9d8{/environment,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@5217f3d0{/storage/rdd/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@32232e55{/storage/rdd,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@54361a9{/storage/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@549621f3{/storage,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@332a7fce{/stages/pool/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@3c9168dc{/stages/pool,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@72f46e16{/stages/stage/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@4985cbcb{/stages/stage,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@71104a4{/stages/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@5af28b27{/stages,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@4a3e3e8b{/jobs/job/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@7876d598{/jobs/job,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@15a902e7{/jobs/json,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO handler.ContextHandler: Stoppedo.s.j.s.ServletContextHandler@5118388b{/jobs,null,UNAVAILABLE,@Spark}</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO ui.SparkUI: Stopped Spark web UI athttp://192.168.27.143:4040</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO spark.MapOutputTrackerMasterEndpoint:MapOutputTrackerMasterEndpoint stopped!</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO memory.MemoryStore: MemoryStore cleared</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO storage.BlockManager: BlockManager stopped</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO storage.BlockManagerMaster: BlockManagerMasterstopped</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFOscheduler.OutputCommitCoordinator$OutputCommitCoordinatorEndpoint:OutputCommitCoordinator stopped!</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO spark.SparkContext: Successfully stoppedSparkContext</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO util.ShutdownHookManager: Shutdown hookcalled</code></p>
	</li>
	<li>
	<p><code>17/05/16 14:26:34 INFO util.ShutdownHookManager: Deleting directory/tmp/spark-702c8654-489f-47f2-85e0-8b658ebb2988</code></p>
	</li>
	<li>
	<p><code>[root@hserver1 spark-2.1.1-bin-hadoop2.7]#</code></p>
	</li>
</ol><p><strong>注意：上面只是使用了单机本地模式调用Demo，使用集群模式运行Demo</strong></p>            </div>
                </div>
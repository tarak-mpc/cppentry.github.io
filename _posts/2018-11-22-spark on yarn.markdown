---
layout:     post
title:      spark on yarn
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明来自http://blog.csdn.net/jediael_lu/					https://blog.csdn.net/jediael_lu/article/details/76794787				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="spark-on-yarn">spark on yarn</h1>

<p>@(SPARK)[spark, 大数据]</p>



<h2 id="一spark的运行模式">（一）spark的运行模式</h2>

<p>spark可以根据需要运行在local, standalone, mesos和yarn四种模式。</p>

<ul>
<li>local会在本地起一个进程，所有任务均在此进程内运行，一般只用于验证代码，无实际工作意义</li>
<li>standalone是yarn自带的一个资源管理器，可以方便的使用spark的集群功能。</li>
<li>mesos是apache另一个资源管理器。</li>
<li>yarn是hadoop2.0以后引入的资源管理器，可以很方便的集成hdfs，但需要先安装好hadoop。</li>
</ul>



<h2 id="二spark-on-yarn的安装">（二）spark on yarn的安装</h2>

<p>1、先安装好hadoop集群 <br>
2、指定HADOOP_CONF_DIR环境变量，指向yarn-site.xml所在的目录。 <br>
3、然后就可以运行spark了。</p>



<h2 id="三yarn-client与yarn-cluster">（三）yarn-client与yarn-cluster</h2>

<p>client会将drive运行在启动spark进程的机器，即运行spark-submit的机器，因此很容易在本机上看到计算结果。 <br>
cluster会将driver通过yarn来分配，即在nodemanager中的某一台启动一个appMaster。</p>

<p>client适合在本机查看结果的应用，尤其是通过命令行的方式返回结果，但注意不要同时启动很多个driver，否则这些driver会用完这台机器的内存。 <br>
cluster适合通过代码提交的任务，它的输出不是通过console返回的，而是写入hdfs等操作。</p>



<h2 id="四启动yarn-client的常用命令选项">（四）启动yarn-client的常用命令选项</h2>



<h3 id="1最简单的方式">1、最简单的方式</h3>

<pre><code>bin/spark-shell --master yarn-client
</code></pre>

<p>这时会使用spark-defaults.conf中的默认配置，如：</p>

<pre><code># For executor
spark.cores.max                  300
spark.driver.memory              2g
spark.executor.memory            6g
spark.executor.cores             6
spark.driver.extraJavaOptions -XX:PermSize=512M -XX:MaxPermSize=2048M
</code></pre>



<h3 id="2指定内存大小线程数量等">2、指定内存大小，线程数量等</h3>

<pre><code>bin/spark-shell --master yarn-client  --num-executors 5 --executor-memory 8g --driver-memory 4g
</code></pre>



<h3 id="3如果使用hive需要添加extraclasspath参数">3、如果使用hive，需要添加extraClassPath参数</h3>

<pre><code>/home/hadoop/spark/bin/spark-sql \
    --master yarn-client \
    --conf spark.driver.extraClassPath=***  \
    --principal **/scheduler@**.COM \
    --keytab /home/keytab/**.keytab
</code></pre>

<p>常用的选项包括：</p>

<pre><code>--num-executors
--executor-memory
--driver-memory
--name
--jars：将一些包加入任务依赖中
--class
--driver-library-path
--driver-class-path
--principal
--keytab
</code></pre>

<p>完整选项如下：</p>

<pre><code>$ bin/spark-shell --help
Usage: ./bin/spark-shell [options]

Options:
  --master MASTER_URL         spark://host:port, mesos://host:port, yarn, or local.
  --deploy-mode DEPLOY_MODE   Whether to launch the driver program locally ("client") or
                              on one of the worker machines inside the cluster ("cluster")
                              (Default: client).
  --class CLASS_NAME          Your application's main class (for Java / Scala apps).
  --name NAME                 A name of your application.
  --jars JARS                 Comma-separated list of local jars to include on the driver
                              and executor classpaths.
  --packages                  Comma-separated list of maven coordinates of jars to include
                              on the driver and executor classpaths. Will search the local
                              maven repo, then maven central and any additional remote
                              repositories given by --repositories. The format for the
                              coordinates should be groupId:artifactId:version.
  --exclude-packages          Comma-separated list of groupId:artifactId, to exclude while
                              resolving the dependencies provided in --packages to avoid
                              dependency conflicts.
  --repositories              Comma-separated list of additional remote repositories to
                              search for the maven coordinates given with --packages.
  --py-files PY_FILES         Comma-separated list of .zip, .egg, or .py files to place
                              on the PYTHONPATH for Python apps.
  --files FILES               Comma-separated list of files to be placed in the working
                              directory of each executor.

  --conf PROP=VALUE           Arbitrary Spark configuration property.
  --properties-file FILE      Path to a file from which to load extra properties. If not
                              specified, this will look for conf/spark-defaults.conf.

  --driver-memory MEM         Memory for driver (e.g. 1000M, 2G) (Default: 1024M).
  --driver-java-options       Extra Java options to pass to the driver.
  --driver-library-path       Extra library path entries to pass to the driver.
  --driver-class-path         Extra class path entries to pass to the driver. Note that
                              jars added with --jars are automatically included in the
                              classpath.

  --executor-memory MEM       Memory per executor (e.g. 1000M, 2G) (Default: 1G).

  --proxy-user NAME           User to impersonate when submitting the application.

  --help, -h                  Show this help message and exit
  --verbose, -v               Print additional debug output
  --version,                  Print the version of current Spark

 Spark standalone with cluster deploy mode only:
  --driver-cores NUM          Cores for driver (Default: 1).

 Spark standalone or Mesos with cluster deploy mode only:
  --supervise                 If given, restarts the driver on failure.
  --kill SUBMISSION_ID        If given, kills the driver specified.
  --status SUBMISSION_ID      If given, requests the status of the driver specified.

 Spark standalone and Mesos only:
  --total-executor-cores NUM  Total cores for all executors.

 Spark standalone and YARN only:
  --executor-cores NUM        Number of cores per executor. (Default: 1 in YARN mode,
                              or all available cores on the worker in standalone mode)

 YARN-only:
  --driver-cores NUM          Number of cores used by the driver, only in cluster mode
                              (Default: 1).
  --queue QUEUE_NAME          The YARN queue to submit to (Default: "default").
  --num-executors NUM         Number of executors to launch (Default: 2).
  --archives ARCHIVES         Comma separated list of archives to be extracted into the
                              working directory of each executor.
  --principal PRINCIPAL       Principal to be used to login to KDC, while running on
                              secure HDFS.
  --keytab KEYTAB             The full path to the file that contains the keytab for the
                              principal specified above. This keytab will be copied to
                              the node running the Application Master via the Secure
                              Distributed Cache, for renewing the login tickets and the
                              delegation tokens periodically.
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
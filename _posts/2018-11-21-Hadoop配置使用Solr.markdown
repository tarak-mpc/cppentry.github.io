---
layout:     post
title:      Hadoop配置使用Solr
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
Hadoop配置使用Solr<br><br><br>
Solr可以和Hadoop一起使用。 由于Hadoop是用于处理大量数据，Solr帮助我们从这么大数据源中找到所需的信息。在本节中，我们将了解如何在系统上安装Hadoop。<br><br><br>
下载Hadoop<br><br><br>
下面给出了如何将Hadoop下载到系统中的步骤。<br><br><br>
第1步 - 打开Hadoop主页 - www.hadoop.apache.org/。 单击链接版本，如下面的屏幕截图中突出显示。<br><br><br><br><br>
它将重定向到Apache Hadoop发布页面，其中包含有关各种版本的Hadoop源代码和二进制文件镜像的链接，如下所示：<br><br><br><br><br><br><br>
步骤2 - 选择最新稳定Hadoop版本(在本教程中，选择的是2.6.4)，并单击其二进制链接。 它将转到一个页面，其中提供了Hadoop二进制文件的镜像。单击其中一个镜像下载Hadoop。<br><br><br>
从命令提示符下载Hadoop<br><br><br>
打开Linux终端并以超级用户(root)身份登录。<br><br><br>
$ su <br>
password:<br>
Shell<br>
转到需要安装Hadoop的目录，并使用之前复制的链接将文件保存在此目录下，如下面的代码块所示。<br><br><br>
# cd /usr/local <br>
# wget http://redrockdigimark.com/apachemirror/hadoop/common/hadoop-<br>
2.6.4/hadoop-2.6.4.tar.gz<br>
Shell<br>
下载Hadoop后，使用以下命令解压缩。<br><br><br>
# tar zxvf hadoop-2.6.4.tar.gz  <br>
# mkdir hadoop <br>
# mv hadoop-2.6.4/* to hadoop/ <br>
# exit<br>
Shell<br>
安装Hadoop<br><br><br>
按照下面给出的步骤在伪分布式模式下安装Hadoop。<br><br><br>
第1步：设置Hadoop<br><br><br>
可以通过将以下命令附加到〜/ .bashrc文件来设置Hadoop环境变量。<br><br><br><br><br>
 <br>
export HADOOP_HOME = /usr/local/hadoop export<br>
HADOOP_MAPRED_HOME = $HADOOP_HOME export<br>
HADOOP_COMMON_HOME = $HADOOP_HOME export <br>
HADOOP_HDFS_HOME = $HADOOP_HOME export <br>
YARN_HOME = $HADOOP_HOME <br>
export HADOOP_COMMON_LIB_NATIVE_DIR = $HADOOP_HOME/lib/native <br>
export PATH = $PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin <br>
export HADOOP_INSTALL = $HADOOP_HOME<br>
Shell<br>
接下来，将所有更改应用到当前运行的系统，执行以下代码 -<br><br><br>
$ source ~/.bashrc<br>
Shell<br>
第2步：Hadoop配置<br><br><br>
可以在位置“$HADOOP_HOME/etc/hadoop”目录中找到所有Hadoop配置文件。需要根据您的Hadoop基础结构对这些配置文件进行更改。<br><br><br>
$ cd $HADOOP_HOME/etc/hadoop<br>
Shell<br>
为了在Java中开发Hadoop程序，必须通过将JAVA_HOME值替换为系统中Java的位置来重置hadoop-env.sh文件中的Java环境变量。<br><br><br>
export JAVA_HOME = /usr/local/jdk1.7.0_71<br>
Shell<br>
以下是必须编辑以配置Hadoop的文件的列表 -<br><br><br>
core-site.xml<br>
hdfs-site.xml<br>
yarn-site.xml<br>
mapred-site.xml<br>
core-site.xml<br><br><br>
core-site.xml文件包含诸如用于Hadoop实例的端口号，为文件系统分配的内存，用于存储数据的内存限制以及读/写缓冲区大小的信息。<br><br><br>
打开core-site.xml并在&lt;configuration&gt;，&lt;/ configuration&gt;标签中添加以下属性。<br><br><br>
&lt;configuration&gt; <br>
   &lt;property&gt;     <br>
      &lt;name&gt;fs.default.name&lt;/name&gt;     <br>
      &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;   <br>
   &lt;/property&gt; <br>
&lt;/configuration&gt;<br>
XML<br>
hdfs-site.xml<br><br><br>
hdfs-site.xml文件包含本地文件系统的复制数据值，namenode路径和datanode路径等信息。表示要存储Hadoop基础架构的位置。<br><br><br>
假设有以下数据。<br><br><br>
dfs.replication (data replication value) = 1  <br><br><br>
(In the below given path /hadoop/ is the user name. <br>
hadoopinfra/hdfs/namenode is the directory created by hdfs file system.) <br>
namenode path = //home/hadoop/hadoopinfra/hdfs/namenode  <br><br><br>
(hadoopinfra/hdfs/datanode is the directory created by hdfs file system.) <br>
datanode path = //home/hadoop/hadoopinfra/hdfs/datanode<br>
Shell<br>
打开此文件，并在&lt;configuration&gt;，&lt;/ configuration&gt;标记中添加以下属性。<br><br><br>
&lt;configuration&gt; <br>
   &lt;property&gt;     <br>
      &lt;name&gt;dfs.replication&lt;/name&gt;     <br>
      &lt;value&gt;1&lt;/value&gt;   <br>
   &lt;/property&gt;  <br><br><br>
   &lt;property&gt;     <br>
      &lt;name&gt;dfs.name.dir&lt;/name&gt;     <br>
      &lt;value&gt;file:///home/hadoop/hadoopinfra/hdfs/namenode&lt;/value&gt;   <br>
   &lt;/property&gt;  <br><br><br>
   &lt;property&gt;     <br>
      &lt;name&gt;dfs.data.dir&lt;/name&gt;     <br>
      &lt;value&gt;file:///home/hadoop/hadoopinfra/hdfs/datanode&lt;/value&gt;   <br>
   &lt;/property&gt; <br>
&lt;/configuration&gt;<br>
XML<br>
注意 - 在上述文件中，所有属性值都是用户定义的，您可以根据Hadoop基础结构进行更改。<br><br><br>
yarn-site.xml<br>
此文件用于在Hadoop中配置yarn 。 打开yarn-site.xml文件，并在此文件中的&lt;configuration&gt;，&lt;/ configuration&gt;标记之间添加以下属性。<br><br><br>
&lt;configuration&gt; <br>
   &lt;property&gt;     <br>
      &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;     <br>
      &lt;value&gt;mapreduce_shuffle&lt;/value&gt;   <br>
   &lt;/property&gt; <br>
&lt;/configuration&gt;<br>
Shell<br>
mapred-site.xml<br><br><br>
此文件用于指定使用的MapReduce框架。默认情况下，Hadoop包含yarn-site.xml模板。 首先，需要使用以下命令将文件从mapred-site，xml.template复制到mapred-site.xml文件。<br><br><br><br><br>
$ cp mapred-site.xml.template mapred-site.xml<br>
Shell<br>
打开mapred-site.xml文件，并在&lt;configuration&gt;，&lt;/ configuration&gt;标签中添加以下属性。<br><br><br>
&lt;configuration&gt; <br>
   &lt;property&gt;     <br>
      &lt;name&gt;mapreduce.framework.name&lt;/name&gt;     <br>
      &lt;value&gt;yarn&lt;/value&gt;   <br>
   &lt;/property&gt; <br>
&lt;/configuration&gt;<br>
Shell<br>
验证Hadoop安装<br><br><br>
以下步骤用于验证Hadoop安装。<br><br><br>
第1步：命名节点设置<br><br><br>
使用命令“hdfs namenode -format”设置namenode，如下所示。<br><br><br>
$ cd ~ <br>
$ hdfs namenode -format<br>
Shell<br>
预期输出结果如下 -<br><br><br>
10/24/14 21:30:55 INFO namenode.NameNode: STARTUP_MSG: <br>
/************************************************************ <br>
STARTUP_MSG: Starting NameNode <br>
STARTUP_MSG:   host = localhost/192.168.1.11 <br>
STARTUP_MSG:   args = [-format] STARTUP_MSG:   version = 2.6.4 <br>
... <br>
... <br>
10/24/14 21:30:56 INFO common.Storage: Storage directory <br>
/home/hadoop/hadoopinfra/hdfs/namenode has been successfully formatted. <br>
10/24/14 21:30:56 INFO namenode.NNStorageRetentionManager: Going to retain 1 <br>
images with txid &gt;= 0 <br>
10/24/14 21:30:56 INFO util.ExitUtil: Exiting with status 0 <br>
10/24/14 21:30:56 INFO namenode.NameNode: SHUTDOWN_MSG: <br>
/************************************************************ <br>
SHUTDOWN_MSG: Shutting down NameNode at localhost/192.168.1.11 <br>
************************************************************/<br>
Shell<br>
第2步：验证Hadoop dfs<br><br><br>
以下命令用于启动Hadoop dfs。执行此命令将启动Hadoop文件系统。<br><br><br>
$ start-dfs.sh<br>
Shell<br>
输出结果如下 -<br><br><br>
10/24/14 21:37:56 <br>
Starting namenodes on [localhost] <br>
localhost: starting namenode, logging to /home/hadoop/hadoop-2.6.4/logs/hadoop-<br>
hadoop-namenode-localhost.out <br>
localhost: starting datanode, logging to /home/hadoop/hadoop-2.6.4/logs/hadoop-<br>
hadoop-datanode-localhost.out <br>
Starting secondary namenodes [0.0.0.0]<br>
Shell<br>
第3步：验证Yarn脚本<br><br><br>
以下命令用于启动Yarn脚本。执行此命令将启动 Yarn 实例。<br><br><br>
$ start-yarn.sh<br>
Shell<br>
输出结果如下 -<br><br><br>
starting yarn daemons <br>
starting resourcemanager, logging to /home/hadoop/hadoop-2.6.4/logs/yarn-<br>
hadoop-resourcemanager-localhost.out <br>
localhost: starting nodemanager, logging to /home/hadoop/hadoop-<br>
2.6.4/logs/yarn-hadoop-nodemanager-localhost.out<br>
Shell<br>
第4步：在浏览器上访问Hadoop<br><br><br>
访问Hadoop的默认端口号为50070。使用以下URL在浏览器上获取Hadoop服务。<br><br><br>
http://localhost:50070/<br>
得到结果如下 -<br><br><br><br><br><br><br>
在Hadoop上安装Solr<br><br><br>
按照以下步骤下载并安装Solr。<br><br><br>
第1步<br><br><br>
通过单击以下链接打开Apache Solr的主页 - http://lucene.apache.org/solr/<br><br><br><br><br><br><br>
第2步<br><br><br>
点击下载按钮(在上面的截图中箭头突出显示)。点击后将被重定向到Apache Solr的各种镜像的页面。 选择一个镜像并单击它，这将重定向到一个页面，您可以下载Apache Solr的源文件和二进制文件，如下面的屏幕截图所示。<br><br><br><br><br><br><br>
第3步<br><br><br>
选择Solr-6.2.0.tqz的文件夹将它下载到系统的文件夹中。提取下载的文件夹的内容。<br><br><br>
第4步<br><br><br>
在Hadoop主目录中创建一个Solr的文件夹，并将解压缩文件夹的内容移动到其中，如下所示。<br><br><br><br><br>
$ mkdir Solr <br>
$ cd Downloads <br>
$ mv Solr-6.2.0 /home/Hadoop/<br>
Shell<br>
验证<br><br><br>
进入到solr主目录的bin文件夹，并使用version选项验证安装，如以下代码块所示。<br><br><br>
$ cd bin/ <br>
$ ./Solr version <br>
6.2.0<br>
Shell<br>
设置主目录和路径<br><br><br>
使用以下命令打开.bashrc文件 -<br><br><br>
[Hadoop@localhost ~]$ source ~/.bashrc<br>
Shell<br>
现在设置Apache Solr的home和path目录如下 -<br><br><br>
export SOLR_HOME = /home/Hadoop/Solr  <br>
export PATH = $PATH:/$SOLR_HOME/bin/<br>
Shell<br>
打开终端并执行以下命令 -<br><br><br>
[Hadoop@localhost Solr]$ source ~/.bashrc<br>
Shell<br>
现在，可以从任何目录执行Solr命令。
            </div>
                </div>
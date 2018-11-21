---
layout:     post
title:      hadoop概况与hdfs部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="hadoop_0"></a>一、hadoop概况</h2>
<p>广义的Hadoop是指以hadoop软件为主的生态圈<br>
狭义d的hadoop是指hadoop软件<br>
大数据软件官网：<br>
hadoop: <a href="http://hadoop.apache.org" rel="nofollow">hadoop.apache.org</a><br>
hive: <a href="http://hive.apache.org" rel="nofollow">hive.apache.org</a><br>
spark: <a href="http://spark.apache.org" rel="nofollow">spark.apache.org</a><br>
flink: <a href="http://flink.apache.org" rel="nofollow">flink.apache.org</a><br>
hadoop软件版本：<br>
1.x<br>
2.x 生产上多用2.x版本<br>
3.x 在更新迭代</p>
<ul>
<li>
</ul>
<p><strong>hadoop2.x组件:</strong><br>
（1）hdfs: 存储大数据量的数据|       属于分布式文件系统 ，底层生产必不可少，生产上高级数据存储使用hive/hbase等<br>
（2）mapreduce: 分布式计算系统  |  生产上不常用，因其开发难度高、计算慢(shuffle 磁盘)；生产上常用hive sql或者spark来进行分布式计算<br>
（3）yarn: 资源(内存+core)+作业(job)调度管理系统 ，生产上必不可少<br>
但是：<br>
一般不选择部署apache官网上的提供的hadoop，企业一般选择CDH、Ambari、hdp部署<br>
最常用的就是CDH，关于CDH的起源，以hadoop-2.6.0版本为例，是cloudera公司将Apache hadoop-2.6.0源代码，修复bug，增加了一些新功能新特性，编译为自己的版本hadoop-2.6.0-cdh5.7.0，成为企业版本获取收益！！<br>
因此接下来的hadoop hdfs的部署是以cloudera公司编译的hadoop-2.6.0-cdh5.7.0版本部署，也是在cloudera公司官网进行学习。</p>
<h2><a id="hdfs_24"></a>二、hdfs部署</h2>
<p>我们直击官网：<a href="http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0/" rel="nofollow">http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0/</a><br>
hadoop软件下载地址：<a href="http://archive-primary.cloudera.com/cdh5/cdh/5/" rel="nofollow">http://archive-primary.cloudera.com/cdh5/cdh/5/</a><br>
下载hadoop-2.6.0-cdh5.7.0 .tar.gz包</p>
<ul>
<li>
</ul>
<p><strong>（1）添加sudo权限的无密码访问的hadoop用户</strong><br>
[root@hadoop001 ~]# <strong>useradd hadoop</strong><br>
[root@hadoop001 ~]# vi /etc/sudoers  （修改hadoop用户权限）<br>
添加：hadoop     ALL=(ALL)     NOPASSWD:ALL<br>
<img src="https://img-blog.csdn.net/20181005131056699?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
[root@hadoop001 ~]# cat /etc/sudoers | grep hadoop  （查看hadoop 用户权限）<br>
[root@hadoop001 ~]# su - hadoop （切换hadoop用户）<br>
<img src="https://img-blog.csdn.net/20181005130742479?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ul>
<li>
</ul>
<p><strong>（2）在hadoop 用户的家目录下创建环境文件：source源代码文件夹，software软件文件夹,app安装文件夹</strong><br>
[hadoop@hadoop001 ~]$ mkdir source software app<br>
[hadoop@hadoop001 ~]$ cd app  （切换到app文件夹）<br>
[hadoop@hadoop001 app]$ wget <a href="http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0.tar.gz" rel="nofollow">http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0.tar.gz</a> （下载hadoop-2.6.0-cdh5.7.0 .tar.gz包）<br>
[hadoop@hadoop001 app]$ tar -xzvf hadoop-2.6.0-cdh5.7.0.tar.gz （解压）<br>
<img src="https://img-blog.csdn.net/20181005131304391?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdn.net/20181005131737817?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ul>
<li>
</ul>
<p><strong>（3）java环境部署：使用java jdk 1.7版本</strong><br>
我们直击官网：<a href="http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0/" rel="nofollow">http://archive-primary.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0/</a><br>
学习的话先用单节点安装：Single Node Setup</p>
<blockquote>
<p>官网要求：<br>
Required Software<br>
Required software for Linux include:<br>
Java™ must be installed. Recommended Java versions are described at HadoopJavaVersions.<br>
ssh must be installed and sshd must be running to use the Hadoop scripts that manage remote Hadoop daemons.</p>
</blockquote>
<p>Hadoop可以在Linux或 Windows中部署，但是一般不会用Windows部署，另外需要安装一定版本的Java环境并且ssh必须要与hadoop连通<br>
步骤之前已经在安装MySQL时候演示过了，这里不再详解：<br>
先上传jdk-7u80-linux-x64.tar.gz包（rz命令），然后解压，修改用户和用户组，配置全局环境变量，生效全局环境变量文件<br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ chown -R roor:root jdk1.7.0_80<br>
<img src="https://img-blog.csdn.net/20181005132427889?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ vi /etc/proflie<br>
添加以下内容：<br>
<img src="https://img-blog.csdn.net/20181005133019922?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ source /etc/proflie<br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ which java<br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ java -version<br>
<img src="https://img-blog.csdn.net/20181005132605304?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdn.net/2018100513262545?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdn.net/20181005132630692?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ul>
<li>
</ul>
<p>（4）<strong>准备工作：修改变量文件</strong></p>
<blockquote>
<p>官网要求：<br>
Prepare to Start the Hadoop Cluster<br>
Unpack the downloaded Hadoop distribution. In the distribution, edit the file etc/hadoop/hadoop-env.sh to define some parameters as follows:<br>
#set to the root of your Java installation<br>
export JAVA_HOME=/usr/java/latest<br>
Assuming your installation directory is /usr/local/hadoop<br>
export HADOOP_PREFIX=/usr/local/hadoop<br>
Try the following command:<br>
$ bin/hadoop<br>
This will display the usage documentation for the hadoop script.<br>
Now you are ready to start your Hadoop cluster in one of the three supported modes:<br>
Local (Standalone) Mode<br>
Pseudo-Distributed Mode<br>
Fully-Distributed Mode</p>
</blockquote>
<p>[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ cd etc/hadoop （按照要求进入etc/hadoop文件夹修改hadoop-env.sh文件）<br>
[hadoop@hadoop001 hadoop]$ vi <a href="http://hadoop-env.sh" rel="nofollow">hadoop-env.sh</a><br>
添加以下内容：<br>
export JAVA_HOME=/usr/java/jdk1.7.0_80<br>
export HADOOP_PREFIX=/home/hadoop/app/hadoop-2.6.0-cdh5.7.0<br>
<img src="https://img-blog.csdn.net/2018100513355123?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
检查：<br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ bin/hadoop<br>
<img src="https://img-blog.csdn.net/20181005133623882?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
启动三种模式<br>
Local (Standalone) Mode: 单机 没有进程  不用<br>
Pseudo-Distributed Mode: 伪分布式 1台机器 每个服务只有一个进程  学习用<br>
Fully-Distributed Mode: 分布式 有多个进程  生产用</p>
<ul>
<li>
</ul>
<p><strong>（5）配置文件</strong></p>
<p>官网要求更改core-site.xml和hdfs-site.xml文件：<br>
<img src="https://img-blog.csdn.net/20181005133945585?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ cd etc/hadoop<br>
[hadoop@hadoop001 hadoop]$ vi core-site.xml<br>
替换成以下内容，并将localhost改写成当前机器名称：<br>
<img src="https://img-blog.csdn.net/20181005134147956?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
[hadoop@hadoop001 hadoop]$ vi hdfs-site.xml<br>
替换成以下内容：<br>
<img src="https://img-blog.csdn.net/20181005134318909?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ul>
<li>
</ul>
<p><strong>（6）配置无密码ssh</strong></p>
<blockquote>
<p>官网要求：<br>
Setup passphraseless ssh<br>
Now check that you can ssh to the localhost without a passphrase:<br>
$ ssh localhost<br>
If you cannot ssh to localhost without a passphrase, execute the following commands:<br>
$ ssh-keygen -t dsa -P ‘’ -f ~/.ssh/id_dsa<br>
$ cat ~/.ssh/id_dsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
</blockquote>
<h2><a id="sshhadoop_133"></a>执行之前，先删除.ssh隐藏文件（在hadoop家目录下）</h2>
<p>[hadoop@hadoop001 ~]$ rm -rf .ssh （重要）<br>
[hadoop@hadoop001 ~]$ ssh-keygen -t dsa -P ‘’ -f ~/.ssh/id_dsa<br>
[hadoop@hadoop001 ~]$ cat ~/.ssh/id_dsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
<h2><a id="httpsimgblogcsdnnet2018100513460375watermark2textaHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3font5a6L5L2Tfontsize400fillI0JBQkFCMAdissolve70
authorized_keys_140"></a><img src="https://img-blog.csdn.net/2018100513460375?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<strong>修改authorized_keys文件的权限：</strong></h2>
<p>[hadoop@hadoop001 .ssh]$ chmod 600 authorized_keys （重要）<br>
<img src="https://img-blog.csdn.net/20181005134901161?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
检测是否能无密码远程连接：<br>
[hadoop@hadoop001 .ssh]$ ssh hadoop001<br>
<img src="https://img-blog.csdn.net/20181005135034234?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ul>
<li>
</ul>
<p><strong>（7）改个人环境变量：</strong><br>
[hadoop@hadoop001 ~]$ vi .bash_profile<br>
添加以下内容：<br>
export JAVA_HOME=/usr/java/jdk1.7.0_80<br>
export HADOOP_PREFIX=/home/hadoop/app/hadoop-2.6.0-cdh5.7.0</p>
<p>export PATH=$ HADOOP_PREFIX/bin:$ JAVA_HOME/bin:$PATH<br>
<img src="https://img-blog.csdn.net/20181005135321813?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<p>之后生效个人环境变量：<br>
[hadoop@hadoop001 ~]$ source .bash_profile<br>
检测是否能无密码ssh远程连接：<br>
[hadoop@hadoop001 ~]$ ssh hadoop001<br>
[hadoop@hadoop001 ~]$ which hdfs<br>
<img src="https://img-blog.csdn.net/20181005135549328?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"></p>
<ul>
<li>
</ul>
<p><strong>（8）配置slaves</strong><br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ cd etc/hadoop<br>
[hadoop@hadoop001 hadoop]$ vi slaves<br>
将localhost改成hadoop001</p>
<ul>
<li>
</ul>
<p><strong>（9）格式化</strong></p>
<blockquote>
<p>官网要求：<br>
Execution<br>
The following instructions are to run a MapReduce job locally. If you want to execute a job on YARN, see YARN on Single Node.<br>
1、Format the filesystem:<br>
$ bin/hdfs namenode -format<br>
2、Start NameNode daemon and DataNode daemon:<br>
$ sbin/start-dfs.sh<br>
The hadoop daemon log output is written to the $HADOOP_LOG_DIR directory (defaults to $HADOOP_HOME/logs).<br>
3、Browse the web interface for the NameNode; by default it is available at:<br>
NameNode - <a href="http://localhost:50070/" rel="nofollow">http://localhost:50070/</a></p>
</blockquote>
<p>[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ <strong>bin/hdfs namenode -format</strong></p>
<h2><a id="successfully_formatted__with_status_0_188"></a>看到successfully formatted 和 with status 0说明格式化成功，否则会抛错</h2>
<p><img src="https://img-blog.csdn.net/20181005140232299?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdn.net/20181005140127777?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ <strong>sbin/start-dfs.sh</strong><br>
18/10/04 11:39:08 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… using builtin-java classes where applicable<br>
Starting namenodes on [hadoop001]<br>
hadoop001: starting namenode, logging to /home/hadoop/app/hadoop-2.6.0-cdh5.7.0/logs/hadoop-hadoop-namenode-hadoop001.out<br>
hadoop001: starting datanode, logging to /home/hadoop/app/hadoop-2.6.0-cdh5.7.0/logs/hadoop-hadoop-datanode-hadoop001.out<br>
Starting secondary namenodes [0.0.0.0]<br>
The authenticity of host ‘0.0.0.0 (0.0.0.0)’ can’t be established.<br>
RSA key fingerprint is e2:06:2f:8d:c0:a9:34:18:29🆎d3:24:fd:81:7a:40.<br>
Are you sure you want to continue connecting (yes/no)? yes<br>
0.0.0.0: Warning: Permanently added ‘0.0.0.0’ (RSA) to the list of known hosts.<br>
0.0.0.0: starting secondarynamenode, logging to /home/hadoop/app/hadoop-2.6.0-cdh5.7.0/logs/hadoop-hadoop-secondarynamenode-hadoop001.out<br>
18/10/04 11:39:52 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform… using builtin-java classes where applicable</p>
<ul>
<li>
</ul>
<p><strong>（10）查看启动是否成功：jps命令</strong><br>
[hadoop@hadoop001 hadoop-2.6.0-cdh5.7.0]$ jps<br>
13490 DataNode<br>
13774 Jps<br>
13366 NameNode<br>
13651 SecondaryNameNode<br>
<img src="https://img-blog.csdn.net/20181005140414304?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
DateNode 和NameNode 和SecondaryNameNode都启动了！！！</p>
<p>官网要求使用地址和端口号50070来看看网址是否ping通，查看我们部署的hadoop的概况：<br>
来看看：47.75.243.240：50070<br>
<img src="https://img-blog.csdn.net/20181005140809853?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1N5bHZpYV9ENTA3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="在这里插入图片描述"><br>
是ok的~~~</p>
<p>------------------------------------------------------未完待续------------------------------------------------</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
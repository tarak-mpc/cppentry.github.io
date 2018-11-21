---
layout:     post
title:      【Hadoop】Hadoop 2.7.6安装_伪分布式集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Alen_Liu_SZ/article/details/80628596				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">本篇主要演示在Linux上安装单节点Hadoop模式，以及伪分布式Hadoop模式。</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(57,57,57);"><strong>一 安装环境</strong></span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><ul><li style="text-align:left;line-height:1.75;font-size:14px;list-style-position:inside;white-space:pre-wrap;font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);">操作系统：Oracle Linux Server release 6.5；</li><li style="text-align:left;line-height:1.75;font-size:14px;list-style-position:inside;white-space:pre-wrap;font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);">Java版本：java-1.7.0-openjdk-1.7.0.45；</li><li style="text-align:left;line-height:1.75;font-size:14px;list-style-position:inside;white-space:pre-wrap;font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);">Hadoop版本：hadoop-2.7.6；</li></ul><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-size:18px;color:rgb(57,57,57);"><strong>二 安装前准备</strong></span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="color:rgb(57,57,57);"><strong>1 创建hadoop用户</strong></span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[root@strong ~]# useradd hadoop
[root@strong ~]# usermod -a -G root hadoop
[root@strong ~]# passwd hadoop
Changing password for user hadoop.
New password: 
Retype new password: 
passwd: all authentication tokens updated successfully.</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">2 安装SSH，配置SSH免密码登录</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">1）检查是否安装SSH，若没，则安装；</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong ~]$ rpm -qa|grep ssh
openssh-server-5.3p1-94.el6.x86_64
openssh-5.3p1-94.el6.x86_64
libssh2-1.4.2-1.el6.x86_64
ksshaskpass-0.5.1-4.1.el6.x86_64
openssh-askpass-5.3p1-94.el6.x86_64
openssh-clients-5.3p1-94.el6.x86_64</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">2）配置SSH免密码登录</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong ~]$ cd .ssh/
[hadoop@strong .ssh]$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/home/hadoop/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/hadoop/.ssh/id_rsa.
Your public key has been saved in /home/hadoop/.ssh/id_rsa.pub.
The key fingerprint is:
13:df:06:f2:ea:21:31:b2:c1:f8:13:24:c6:bf:45:05 hadoop@strong.hadoop.com
The key's randomart image is:
+--[ RSA 2048]----+
|      E..        |
| .     .         |
|  + . . o .      |
| . * .   = o     |
|  . * + S o o    |
|   . B o o .     |
|    = . o        |
|     . o .       |
|        .        |
+-----------------+
[hadoop@strong .ssh]$ cat id_rsa.pub &gt;&gt; authorized_keys
[hadoop@strong .ssh]$ chmod 600 authorized_keys 
[hadoop@strong .ssh]$ ssh localhost
Last login: Fri Jun  8 19:55:11 2018 from localhost</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">3 安装JAVA</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">Java版本可参照：<a href="https://wiki.apache.org/hadoop/HadoopJavaVersions" rel="nofollow"><span style="color:rgb(0,56,132);"><u>https://wiki.apache.org/hadoop/HadoopJavaVersions</u></span></a><span style="color:rgb(57,57,57);">。</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[root@strong ~]# yum install java-1.7.0-openjdk*
在.bash_profile中添加以下内容：
[hadoop@strong ~]$ vim .bash_profile
export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.45.x86_64
[hadoop@strong ~]$ . .bash_profile 
验证JDK配置是否正确：
[hadoop@strong ~]$ java -version
java version "1.7.0_45"
OpenJDK Runtime Environment (rhel-2.4.3.3.0.1.el6-x86_64 u45-b15)
OpenJDK 64-Bit Server VM (build 24.45-b08, mixed mode)
[hadoop@strong ~]$ /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.45.x86_64/bin/java -version
java version "1.7.0_45"
OpenJDK Runtime Environment (rhel-2.4.3.3.0.1.el6-x86_64 u45-b15)
OpenJDK 64-Bit Server VM (build 24.45-b08, mixed mode)</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="color:rgb(223,64,42);">注：Oracle Linux 6.5默认安装的是Java JRE，而不是JDK，为开发方便，则需安装JDK。</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-size:18px;"><strong>三 安装配置Hadoop</strong></span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">1 下载Hadoop软件</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">下载地址为：<a href="http://mirror.bit.edu.cn/apache/hadoop/common" rel="nofollow"><span style="color:rgb(0,56,132);"><u>http://mirror.bit.edu.cn/apache/hadoop/common</u></span></a><span style="color:rgb(57,57,57);">，下载</span><a href="http://mirror.bit.edu.cn/apache/hadoop/common/hadoop-2.7.6/hadoop-2.7.6.tar.gz" rel="nofollow"><span style="color:rgb(0,56,132);"><u>hadoop-2.7.6.tar.gz</u></span></a>  版本。</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[root@strong local]# ll hadoop-2.7.6.tar.gz 
-rw-r--r--. 1 root root 216745683 Jun  8 20:20 hadoop-2.7.6.tar.gz</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">2 解压缩</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[root@strong local]# tar zxvf hadoop-2.7.6.tar.gz
[root@strong local]# chown -R hadoop:hadoop hadoop-2.7.6</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">3 检查Hadoop是否可用，成功则显示版本信息</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[root@strong local]# su - hadoop
[hadoop@strong ~]$ cd /usr/local/hadoop-2.7.6
[hadoop@strong hadoop-2.7.6]$ ./bin/hadoop version
Hadoop 2.7.6
Subversion https://shv@git-wip-us.apache.org/repos/asf/hadoop.git -r 085099c66cf28be31604560c376fa282e69282b8
Compiled by kshvachk on 2018-04-18T01:33Z
Compiled with protoc 2.5.0
From source with checksum 71e2695531cb3360ab74598755d036
This command was run using /usr/local/hadoop-2.7.6/share/hadoop/common/hadoop-common-2.7.6.jar</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">4 设置JAVA_HOME变量</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">在以下文件修改JAVA_HOME</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ vim etc/hadoop/hadoop-env.sh
export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.45.x86_64</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">5 单机配置（非分布式）</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">Hadoop默认模式是非分布式模式，无须进行其他配置即可运行，非分布式即Java进程，方便进行调试。</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">1）运行wordcount示例</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">经过上面四步的设置，已经完成了单机配置，现在演示单机配置下的示例，来体验下Hadoop的功能：</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ mkdir input
[hadoop@strong hadoop-2.7.6]$ vim ./input/test.txt
[hadoop@strong hadoop-2.7.6]$ cat ./input/test.txt 
Hello this is my first time to learn hadoop 
love hadoop 
Hello hadoop
[hadoop@strong hadoop-2.7.6]$ ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.6.jar wordcount input output
18/06/08 20:38:44 INFO Configuration.deprecation: session.id is deprecated. Instead, use dfs.metrics.session-id
18/06/08 20:38:44 INFO jvm.JvmMetrics: Initializing JVM Metrics with processName=JobTracker, sessionId=
18/06/08 20:38:44 INFO input.FileInputFormat: Total input paths to process : 1
18/06/08 20:38:44 INFO mapreduce.JobSubmitter: number of splits:1
18/06/08 20:38:46 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_local241325947_0001
18/06/08 20:38:47 INFO mapreduce.Job: The url to track the job: http://localhost:8080/
18/06/08 20:38:47 INFO mapreduce.Job: Running job: job_local241325947_0001
18/06/08 20:38:47 INFO mapred.LocalJobRunner: OutputCommitter set in config null
18/06/08 20:38:47 INFO output.FileOutputCommitter: File Output Committer Algorithm version is 1
18/06/08 20:38:47 INFO mapred.LocalJobRunner: OutputCommitter is org.apache.hadoop.mapreduce.lib.output.FileOutputCommitter
18/06/08 20:38:48 INFO mapred.LocalJobRunner: Waiting for map tasks
18/06/08 20:38:48 INFO mapred.LocalJobRunner: Starting task: attempt_local241325947_0001_m_000000_0
18/06/08 20:38:48 INFO output.FileOutputCommitter: File Output Committer Algorithm version is 1
----------------中间过程省略------------------
Shuffle Errors
		BAD_ID=0
		CONNECTION=0
		IO_ERROR=0
		WRONG_LENGTH=0
		WRONG_MAP=0
		WRONG_REDUCE=0
	File Input Format Counters 
		Bytes Read=71
	File Output Format Counters 
		Bytes Written=81</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">2）查看运行结果</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ cat output/*
Hello	2
first	1
hadoop	3
is	1
learn	1
love	1
my	1
this	1
time	1
to	1</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="color:#ff0000;">注：Hadoop默认不会覆盖结果文件，再次运行会出错，提示文件存在，需先将output删除。</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-size:18px;"><strong>四 Hadoop伪分布式配置</strong></span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">Hadoop可以在单节点以伪分布式模式运行，Hadoop进程以分离的Java进程运行，节点即作为NameNode，也作为DataNode，同时，读取的是HDFS文件。</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">Hadoop的配置文件位于 /usr/local/hadoop-2.7.6/etc/hadoop/中，伪分布式需要修改两个配置文件，分别为core-site.xml和hdfs-site.xml，配置文件xml格式，每个配置以property的name和value设置。</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">1 修改core-site.xml</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">&lt;configuration&gt;
        &lt;property&gt;
                &lt;name&gt;fs.defaultFS&lt;/name&gt;
                &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
        &lt;/property&gt;
&lt;/configuration&gt;</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">2 修改hdfs-site.xml</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">&lt;configuration&gt;
        &lt;property&gt;
                &lt;name&gt;dfs.replication&lt;/name&gt;
                &lt;value&gt;1&lt;/value&gt;
        &lt;/property&gt;
&lt;/configuration&gt;</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">3 执行NameNode格式化</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs namenode -format
18/06/08 20:57:45 INFO namenode.NameNode: STARTUP_MSG: 
/************************************************************
STARTUP_MSG: Starting NameNode
STARTUP_MSG:   host = strong.hadoop.com/192.168.56.102
STARTUP_MSG:   args = [-format]
STARTUP_MSG:   version = 2.7.6
--------------------中间过程省略------------------------
18/06/08 20:57:52 INFO util.GSet: Computing capacity for map NameNodeRetryCache
18/06/08 20:57:52 INFO util.GSet: VM type       = 64-bit
18/06/08 20:57:52 INFO util.GSet: 0.029999999329447746% max memory 966.7 MB = 297.0 KB
18/06/08 20:57:52 INFO util.GSet: capacity      = 2^15 = 32768 entries
18/06/08 20:57:52 INFO namenode.FSImage: Allocated new BlockPoolId: BP-1934541033-192.168.56.102-1528462672607
18/06/08 20:57:52 INFO common.Storage: Storage directory /tmp/hadoop-hadoop/dfs/name has been successfully formatted.------------表示格式化成功
18/06/08 20:57:53 INFO namenode.FSImageFormatProtobuf: Saving image file /tmp/hadoop-hadoop/dfs/name/current/fsimage.ckpt_0000000000000000000 using no compression
18/06/08 20:57:53 INFO namenode.FSImageFormatProtobuf: Image file /tmp/hadoop-hadoop/dfs/name/current/fsimage.ckpt_0000000000000000000 of size 323 bytes saved in 0 seconds.
18/06/08 20:57:53 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid &gt;= 0
18/06/08 20:57:53 INFO util.ExitUtil: Exiting with status 0  ------------表示格式化成功
18/06/08 20:57:53 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at strong.hadoop.com/192.168.56.102
************************************************************/</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">4 启动NameNode和DataNode进程</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ sbin/start-dfs.sh 
Starting namenodes on [localhost]
localhost: starting namenode, logging to /usr/local/hadoop-2.7.6/logs/hadoop-hadoop-namenode-strong.hadoop.com.out
localhost: starting datanode, logging to /usr/local/hadoop-2.7.6/logs/hadoop-hadoop-datanode-strong.hadoop.com.out
Starting secondary namenodes [0.0.0.0]
The authenticity of host '0.0.0.0 (0.0.0.0)' can't be established.
RSA key fingerprint is 68:da:7d:9f:e5:46:14:fc:30:15:9e:24:3d:6e:a9:1d.
Are you sure you want to continue connecting (yes/no)? yes
0.0.0.0: Warning: Permanently added '0.0.0.0' (RSA) to the list of known hosts.
0.0.0.0: starting secondarynamenode, logging to /usr/local/hadoop-2.7.6/logs/hadoop-hadoop-secondarynamenode-strong.hadoop.com.out</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">启动成功后，查看：</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ jps
4034 DataNode
4346 Jps
3939 NameNode
4230 SecondaryNameNode</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">5 NameNode节点通过Web访问</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">默认URL：<a href="http://localhost:50070/" rel="nofollow"><span style="color:rgb(0,56,132);"><u>http://localhost:50070/</u></span></a></div><div><img src="https://img-blog.csdn.net/20180608220754905" alt=""><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">6 运行伪分布式实例</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">在上面的本地模式，wordcount读取的是本地实例，伪分布式读取的这是HDFS上的数据，要使用HDFS，首先需要在HDFS中创建用户目录。</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">1）创建用户目录</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs dfs -mkdir -p /user/hadoop</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">2）拷贝input文件至分布式文件系统</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">将上面创建的文件etc/test.txt复制到分布式文件系统的/user/hadoop/input中，我们使用的是Hadoop用户，并且已创建相应的用户目录/user/hadoop，因此在命令中可以使用相对路径，其对应的绝对路径为/user/hadoop/input。</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs dfs -put input/test.txt input
[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs dfs -ls
Found 1 items
-rw-r--r--   1 hadoop supergroup         71 2018-06-08 21:21 input</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">3) 运行wordcount示例</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.6.jar wordcount input output</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">4）查看运行结果</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs dfs -ls output
Found 2 items
-rw-r--r--   1 hadoop supergroup          0 2018-06-08 21:24 output/_SUCCESS
-rw-r--r--   1 hadoop supergroup         69 2018-06-08 21:24 output/part-r-00000
[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs dfs -cat output/*
Hello	2
first	1
hadoop	3
is	1
learn	1
love	1
my	1
this	1
time	1
to	1</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">5）Hadoop运行时，输出目录不能存在，否则会出错，删除output</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs dfs -ls output
Found 2 items
-rw-r--r--   1 hadoop supergroup          0 2018-06-08 21:24 output/_SUCCESS
-rw-r--r--   1 hadoop supergroup         69 2018-06-08 21:24 output/part-r-00000
[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs dfs -rm -r output
18/06/08 21:43:01 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0 minutes, Emptier interval = 0 minutes.
Deleted output</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-size:18px;"><strong>五 启动YARN</strong></span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">YARN是从MapReduce分离出来的，负责资源管理与任务调度，YARN运行位于MapReduce之上，提供了高可用性、高扩展性。</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">1配置mapred-site.xml</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">&lt;configuration&gt;
        &lt;property&gt;
                &lt;name&gt;mapreduce.framework.name&lt;/name&gt;
                &lt;value&gt;yarn&lt;/value&gt;
        &lt;/property&gt;
&lt;/configuration&gt;</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">2 配置yarn-site.xml</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">&lt;configuration&gt;
        &lt;property&gt;
                &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
                &lt;value&gt;mapreduce_shuffle&lt;/value&gt;
        &lt;/property&gt;
&lt;/configuration&gt;</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><span style="font-weight:bold;">3 启动ResourceManager进程和NodeManager进程</span></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">1）启动YARN</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ sbin/start-yarn.sh 
starting yarn daemons
starting resourcemanager, logging to /usr/local/hadoop-2.7.6/logs/yarn-hadoop-resourcemanager-strong.hadoop.com.out
localhost: starting nodemanager, logging to /usr/local/hadoop-2.7.6/logs/yarn-hadoop-nodemanager-strong.hadoop.com.out</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">2）开启历史服务器，才能在web中查看任务运行情况</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ sbin/mr-jobhistory-daemon.sh start historyserver
starting historyserver, logging to /usr/local/hadoop-2.7.6/logs/mapred-hadoop-historyserver-strong.hadoop.com.out</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">3）查看YARN启动</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ jps
4034 DataNode
5007 ResourceManager
5422 JobHistoryServer
5494 Jps
3939 NameNode
5108 NodeManager
4230 SecondaryNameNode</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">4）再次运行wordcount示例</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><pre><code class="language-sql">[hadoop@strong hadoop-2.7.6]$ ./bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.6.jar wordcount input output
[hadoop@strong hadoop-2.7.6]$ ./bin/hdfs dfs -cat output/*
Hello	2
first	1
hadoop	3
is	1
learn	1
love	1
my	1
this	1
time	1
to	1</code></pre></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">5）在浏览器查看YARN</div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;"><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">ResourceManager 默认URL：<a href="http://localhost:8088" rel="nofollow"><span style="color:rgb(0,56,132);"><u>http://localhost:8088</u></span></a></div><div><img src="https://img-blog.csdn.net/2018060822104422" alt=""><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">点击上图History，出现如下界面：</div><div><img src="https://img-blog.csdn.net/20180608221109809" alt=""><br></div><div style="white-space:pre-wrap;text-align:left;line-height:1.75;font-size:14px;">至此，Hadoop伪分布式集群安装及配置完成，这个安装只有HDFS、YARN，MapReduce等基本组件。</div><p></p><p></p>            </div>
                </div>
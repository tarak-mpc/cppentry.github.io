---
layout:     post
title:      CentOS 7部署Hadoop（单机版）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。转载或引用本文部分内容时请注明本文地址：					https://blog.csdn.net/u010993514/article/details/82930351				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <hr><h3 id="main-toc"><strong>目录</strong></h3>

<p id="-toc" style="margin-left:80px;"><a href="#%E6%B5%8B%E8%AF%95%E7%8E%AF%E5%A2%83" rel="nofollow">测试环境</a></p>

<p id="Hadoop%E9%83%A8%E7%BD%B2%E6%96%B9%E5%BC%8F%E4%BB%8B%E7%BB%8D-toc" style="margin-left:80px;"><a href="#Hadoop%E9%83%A8%E7%BD%B2%E6%96%B9%E5%BC%8F%E4%BB%8B%E7%BB%8D" rel="nofollow">Hadoop部署方式介绍</a></p>

<p id="%E5%AE%89%E8%A3%85CentOS%207-toc" style="margin-left:80px;"><a href="#%E5%AE%89%E8%A3%85CentOS%207" rel="nofollow">安装CentOS 7</a></p>

<p id="%E9%85%8D%E7%BD%AEJava%E7%8E%AF%E5%A2%83-toc" style="margin-left:80px;"><a href="#%E9%85%8D%E7%BD%AEJava%E7%8E%AF%E5%A2%83" rel="nofollow">配置Java环境</a></p>

<p id="Hadoop%E5%8D%95%E6%9C%BA%E7%89%88%E5%AE%89%E8%A3%85-toc" style="margin-left:80px;"><a href="#Hadoop%E5%8D%95%E6%9C%BA%E7%89%88%E5%AE%89%E8%A3%85" rel="nofollow">Hadoop单机版安装</a></p>

<p id="Hadoop%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E9%85%8D%E7%BD%AE-toc" style="margin-left:80px;"><a href="#Hadoop%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E9%85%8D%E7%BD%AE" rel="nofollow">Hadoop环境变量配置</a></p>

<p id="%E6%B5%8B%E8%AF%95-toc" style="margin-left:80px;"><a href="#%E6%B5%8B%E8%AF%95" rel="nofollow">测试</a></p>

<hr id="hr-toc"><h3 id="%E6%B5%8B%E8%AF%95%E7%8E%AF%E5%A2%83">测试环境</h3>

<p>Linux系统版本：CentOS 7 64位</p>

<p>Hadoop版本：<a href="https://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/" rel="nofollow">hadoop-2.7.3</a></p>

<p>Java版本：<a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html" rel="nofollow" id="jdk-8u181-oth-JPRXXXjdk-8u181-linux-x64.tar.gz" name="jdk-8u181-oth-JPRXXXjdk-8u181-linux-x64.tar.gz">jdk-8u181-linux-x64</a></p>

<hr><h3 id="Hadoop%E9%83%A8%E7%BD%B2%E6%96%B9%E5%BC%8F%E4%BB%8B%E7%BB%8D">Hadoop部署方式介绍</h3>

<p>Hadoop部署方式分三种：Standalone Mode（单机模式）、Pseudo-Distributed Mode（伪分布式模式）、Fully Distributed Mode（全分布式模式）</p>

<p style="text-indent:0;"><strong>单机模式：</strong></p>

<p style="text-indent:50px;">单机模式是Hadoop的默认模式。这种模式在一台单机上运行，没有<a href="http://zhidao.baidu.com/search?word=%E5%88%86%E5%B8%83%E5%BC%8F%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F&amp;fr=qb_search_exp&amp;ie=utf8" rel="nofollow">分布式文件系统</a>，而是直接读写本地<a href="http://zhidao.baidu.com/search?word=%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F&amp;fr=qb_search_exp&amp;ie=utf8" rel="nofollow">操作系统</a>的文件系统。当首次解压Hadoop的源码包时，Hadoop无法了解硬件安装环境，便保守地选择了最小配置。在这种默认模式下所有3个XML文件均为空。当配置文件为空时，Hadoop会完全运行在本地。因为不需要与其他节点交互，单机模式就不使用HDFS，也不加载任何Hadoop的守护进程。该模式主要用于开发调试MapReduce程序的应用逻辑。</p>

<p style="text-indent:50px;"><br><strong>伪分布模式：</strong></p>

<p style="text-indent:50px;">这种模式也是在一台单机上运行，但用不同的Java进程模仿<a href="http://zhidao.baidu.com/search?word=%E5%88%86%E5%B8%83%E5%BC%8F&amp;fr=qb_search_exp&amp;ie=utf8" rel="nofollow">分布式</a>运行中的各类<a href="http://zhidao.baidu.com/search?word=%E7%BB%93%E7%82%B9&amp;fr=qb_search_exp&amp;ie=utf8" rel="nofollow">结点</a>伪分布模式在“单节点集群”上运行Hadoop，其中所有的守护进程都运行在同一台机器上。该模式在单机模式之上增加了代码调试功能，允许你检查内存使用情况，HDFS输入输出，以及其他的守护进程。</p>

<p> </p>

<p><strong>全分布模式：</strong><br>
     Hadoop守护进程运行在一个集群上。</p>

<p> </p>

<p>部分内容来自：<a href="https://blog.csdn.net/bloodlc/article/details/19973009?utm_source=copy" rel="nofollow">https://blog.csdn.net/bloodlc/article/details/19973009?utm_source=copy</a></p>

<hr><h3 id="%E5%AE%89%E8%A3%85CentOS%207">安装CentOS 7</h3>

<p><a href="https://blog.csdn.net/u010993514/article/details/82924871" rel="nofollow">VMware虚拟机安装Linux系统</a></p>

<hr><h3 id="%E9%85%8D%E7%BD%AEJava%E7%8E%AF%E5%A2%83">配置Java环境</h3>

<p><a href="https://blog.csdn.net/u010993514/article/details/82926514" rel="nofollow">Linux系统下安装Java环境</a></p>

<hr><h3 id="Hadoop%E5%8D%95%E6%9C%BA%E7%89%88%E5%AE%89%E8%A3%85">Hadoop单机版安装</h3>

<p>本文的代码框解释：#后面的为执行代码，[]内root为登录用户，@后master100表示主机名，~代表当前目录，按下面步骤执行时要注意自己这些信息和我的是不是一样的</p>

<p>1、下载Hadoop2.7.3</p>

<pre class="has">
<code class="language-html">hadoop-2.7.3.tar.gz </code></pre>

<pre class="has">
<code>https://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/</code></pre>

<p>2、用root账户登录，创建hadoop用户和hadoop用户组，创建用户的时候会自动创建相应的用户组</p>

<pre class="has">
<code>[root@master100 ~]# useradd hadoop
</code></pre>

<p>3、创建完用户之后设置一个用户密码，有些密码太简单会提示无效的密码，提示重新输入时再输一遍就可以强制保存了</p>

<pre class="has">
<code>[root@master100 ~]# passwd hadoop
更改用户 hadoop 的密码 。
新的 密码：
无效的密码： 密码少于 8 个字符
重新输入新的 密码：
passwd：所有的身份验证令牌已经成功更新。
</code></pre>

<p>4、为hadoop用户添加sudo权限</p>

<pre class="has">
<code>[root@master100 ~]# vi /etc/sudoers</code></pre>

<p>如下，在root用户下面一行加上hadoop  ALL=(ALL)       ALL，保存并退出（这里需要用wq!强制保存退出）</p>

<pre class="has">
<code>## Next comes the main part: which users can run what software on
## which machines (the sudoers file can be shared between multiple
## systems).
## Syntax:
##
##      user    MACHINE=COMMANDS
##
## The COMMANDS section may have other options added to it.
##
## Allow root to run any commands anywhere
root    ALL=(ALL)       ALL
hadoop  ALL=(ALL)       ALL
</code></pre>

<p>5、上传第1步下载好的<a href="https://archive.apache.org/dist/hadoop/common/hadoop-2.7.3/hadoop-2.7.3.tar.gz" rel="nofollow">hadoop-2.7.3.tar.gz</a> 包到Linux系统中的/usr/local目录下</p>

<pre class="has">
<code>sftp:/root&gt; cd /usr/local
sftp:/usr/local&gt; 
Uploading hadoop-2.7.3.tar.gz to remote:/usr/local/hadoop-2.7.3.tar.gz
sftp: sent 204 MB in 9.38 seconds
</code></pre>

<p>6、进入/usr/local目录，解压上传的hadoop安装包</p>

<pre class="has">
<code>[root@master100 ~]# cd /usr/local
[root@master100 local]# tar -zxvf /usr/local/hadoop-2.7.3.tar.gz
</code></pre>

<p>7、解压完成后可以看到文件夹hadoop-2.7.3，将文件夹名改为hadoop，如果不在/usr/local目录下，命令使用时请加上文件夹的绝对路径</p>

<pre class="has">
<code>[root@master100 local]# ll
总用量 556412
drwxr-xr-x. 2 root root         6 4月  11 12:59 bin
drwxr-xr-x. 2 root root         6 4月  11 12:59 etc
drwxr-xr-x. 2 root root         6 4月  11 12:59 games
drwxr-xr-x. 9 root root       149 8月  18 2016 hadoop-2.7.3
-rw-r--r--. 1 root root 214092195 10月  3 11:43 hadoop-2.7.3.tar.gz
drwxr-xr-x. 2 root root         6 4月  11 12:59 include
-rwxr-xr-x. 1 root root 170023183 10月  2 17:28 jdk-8u181-linux-x64.rpm
-rw-r--r--. 1 root root 185646832 10月  2 16:31 jdk-8u181-linux-x64.tar.gz
drwxr-xr-x. 2 root root         6 4月  11 12:59 lib
drwxr-xr-x. 2 root root         6 4月  11 12:59 lib64
drwxr-xr-x. 2 root root         6 4月  11 12:59 libexec
drwxr-xr-x. 2 root root         6 4月  11 12:59 sbin
drwxr-xr-x. 5 root root        49 10月  2 13:00 share
drwxr-xr-x. 2 root root         6 4月  11 12:59 src
</code></pre>

<pre class="has">
<code>[root@master100 local]# mv hadoop-2.7.3 hadoop
[root@master100 local]# ll
总用量 556412
drwxr-xr-x. 2 root root         6 4月  11 12:59 bin
drwxr-xr-x. 2 root root         6 4月  11 12:59 etc
drwxr-xr-x. 2 root root         6 4月  11 12:59 games
drwxr-xr-x. 9 root root       149 8月  18 2016 hadoop
-rw-r--r--. 1 root root 214092195 10月  3 11:43 hadoop-2.7.3.tar.gz
drwxr-xr-x. 2 root root         6 4月  11 12:59 include
-rwxr-xr-x. 1 root root 170023183 10月  2 17:28 jdk-8u181-linux-x64.rpm
-rw-r--r--. 1 root root 185646832 10月  2 16:31 jdk-8u181-linux-x64.tar.gz
drwxr-xr-x. 2 root root         6 4月  11 12:59 lib
drwxr-xr-x. 2 root root         6 4月  11 12:59 lib64
drwxr-xr-x. 2 root root         6 4月  11 12:59 libexec
drwxr-xr-x. 2 root root         6 4月  11 12:59 sbin
drwxr-xr-x. 5 root root        49 10月  2 13:00 share
drwxr-xr-x. 2 root root         6 4月  11 12:59 src
</code></pre>

<p>8、将hadoop文件夹的主：组设置成hadoop</p>

<pre class="has">
<code>[root@master100 local]# chown -R hadoop:hadoop hadoop
[root@master100 local]# ll
总用量 556412
drwxr-xr-x. 2 root   root           6 4月  11 12:59 bin
drwxr-xr-x. 2 root   root           6 4月  11 12:59 etc
drwxr-xr-x. 2 root   root           6 4月  11 12:59 games
drwxr-xr-x. 9 hadoop hadoop       149 8月  18 2016 hadoop
-rw-r--r--. 1 root   root   214092195 10月  3 11:43 hadoop-2.7.3.tar.gz
drwxr-xr-x. 2 root   root           6 4月  11 12:59 include
-rwxr-xr-x. 1 root   root   170023183 10月  2 17:28 jdk-8u181-linux-x64.rpm
-rw-r--r--. 1 root   root   185646832 10月  2 16:31 jdk-8u181-linux-x64.tar.gz
drwxr-xr-x. 2 root   root           6 4月  11 12:59 lib
drwxr-xr-x. 2 root   root           6 4月  11 12:59 lib64
drwxr-xr-x. 2 root   root           6 4月  11 12:59 libexec
drwxr-xr-x. 2 root   root           6 4月  11 12:59 sbin
drwxr-xr-x. 5 root   root          49 10月  2 13:00 share
drwxr-xr-x. 2 root   root           6 4月  11 12:59 src
</code></pre>

<p>9、/usr目录与/usr/local目录所属主：组均为root，默认权限为755，也就是说其他用户（hadoop）没有写入（w）权限，<s>在这里我们需要将这两个目录其他用户的权限设置为7。</s>在这里将hadoop用户加入root组，然后将文件夹权限设置为775</p>

<pre class="has">
<code>###############################
#chmod 757 /usr
#chmod 757 /usr/local
###############################
 
使用下面代码：
 
gpasswd -a hadoop root
chmod 771 /usr
chmod 771 /usr/local
</code></pre>

<p>可以看到执行情况如下： </p>

<pre class="has">
<code>[root@master100 local]# cd /
[root@master100 /]# chmod 757 /usr
[root@master100 /]# ll
总用量 16
lrwxrwxrwx.   1 root root    7 10月  2 13:00 bin -&gt; usr/bin
dr-xr-xr-x.   5 root root 4096 10月  2 13:06 boot
drwxr-xr-x.  20 root root 3240 10月  3 11:47 dev
drwxr-xr-x.  76 root root 8192 10月  3 11:47 etc
drwxr-xr-x.   3 root root   20 10月  3 10:58 home
lrwxrwxrwx.   1 root root    7 10月  2 13:00 lib -&gt; usr/lib
lrwxrwxrwx.   1 root root    9 10月  2 13:00 lib64 -&gt; usr/lib64
drwxr-xr-x.   2 root root    6 4月  11 12:59 media
drwxr-xr-x.   2 root root    6 4月  11 12:59 mnt
drwxr-xr-x.   2 root root    6 4月  11 12:59 opt
dr-xr-xr-x. 133 root root    0 10月  3 11:46 proc
dr-xr-x---.   3 root root  160 10月  3 11:50 root
drwxr-xr-x.  24 root root  720 10月  3 11:47 run
lrwxrwxrwx.   1 root root    8 10月  2 13:00 sbin -&gt; usr/sbin
drwxr-xr-x.   2 root root    6 4月  11 12:59 srv
dr-xr-xr-x.  13 root root    0 10月  3 11:47 sys
drwxrwxrwt.  10 root root  253 10月  3 11:47 tmp
drwxr-xrwx.  14 root root  167 10月  2 17:57 usr
drwxr-xr-x.  19 root root  267 10月  2 13:08 var
[root@master100 /]# chmod 757 /usr/local
[root@master100 /]# cd /usr
[root@master100 usr]# ll
总用量 104
dr-xr-xr-x.  2 root root 24576 10月  2 17:57 bin
drwxr-xr-x.  2 root root     6 4月  11 12:59 etc
drwxr-xr-x.  2 root root     6 4月  11 12:59 games
drwxr-xr-x.  3 root root    23 10月  2 13:01 include
drwxr-xr-x.  3 root root    61 10月  2 17:57 java
dr-xr-xr-x. 27 root root  4096 10月  2 13:02 lib
dr-xr-xr-x. 37 root root 20480 10月  2 13:02 lib64
drwxr-xr-x. 20 root root  4096 10月  2 13:02 libexec
drwxr-xrwx. 13 root root   237 10月  3 11:55 local
dr-xr-xr-x.  2 root root 12288 10月  2 13:02 sbin
drwxr-xr-x. 75 root root  4096 10月  2 13:02 share
drwxr-xr-x.  4 root root    34 10月  2 13:00 src
lrwxrwxrwx.  1 root root    10 10月  2 13:00 tmp -&gt; ../var/tmp
</code></pre>

<hr><h3 id="Hadoop%E7%8E%AF%E5%A2%83%E5%8F%98%E9%87%8F%E9%85%8D%E7%BD%AE">Hadoop环境变量配置</h3>

<p>1、编辑/etc/profile文件</p>

<pre class="has">
<code>vi /etc/profile</code></pre>

<p>2、在末尾加上如下几行</p>

<pre class="has">
<code>export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
</code></pre>

<p>这里注意HADOOP_HOME与PATH的顺序，我这里最后几行的配置如下：</p>

<pre class="has">
<code>export JAVA_HOME=/usr/java/jdk1.8.0_181-amd64
export HADOOP_HOME=/usr/local/hadoop
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
export JRE_HOME=$JAVA_HOME/jre
</code></pre>

<p>3、配置完环境变量之后保存退出，让环境变量立即生效</p>

<pre class="has">
<code>source /etc/profile
</code></pre>

<hr><h3 id="%E6%B5%8B%E8%AF%95">测试</h3>

<p><strong>环境变量测试</strong></p>

<p>输入如下命令：</p>

<pre class="has">
<code>hadoop
hadoop version</code></pre>

<p>效果如下： </p>

<pre class="has">
<code>[root@master100 usr]# hadoop
Usage: hadoop [--config confdir] [COMMAND | CLASSNAME]
  CLASSNAME            run the class named CLASSNAME
 or
  where COMMAND is one of:
  fs                   run a generic filesystem user client
  version              print the version
  jar &lt;jar&gt;            run a jar file
                       note: please use "yarn jar" to launch
                             YARN applications, not this command.
  checknative [-a|-h]  check native hadoop and compression libraries availability
  distcp &lt;srcurl&gt; &lt;desturl&gt; copy file or directories recursively
  archive -archiveName NAME -p &lt;parent path&gt; &lt;src&gt;* &lt;dest&gt; create a hadoop archive
  classpath            prints the class path needed to get the
  credential           interact with credential providers
                       Hadoop jar and the required libraries
  daemonlog            get/set the log level for each daemon
  trace                view and modify Hadoop tracing settings

Most commands print help when invoked w/o parameters.
[root@master100 usr]# hadoop version
Hadoop 2.7.3
Subversion https://git-wip-us.apache.org/repos/asf/hadoop.git -r baa91f7c6bc9cb92be5982de4719c1c8af91ccff
Compiled by root on 2016-08-18T01:41Z
Compiled with protoc 2.5.0
From source with checksum 2e4ce5f957ea4db193bce3734ff29ff4
This command was run using /usr/local/hadoop/share/hadoop/common/hadoop-common-2.7.3.jar
</code></pre>

<p><strong>演示Hadoop自带的MapReduce例子</strong></p>

<p>在这里用hadoop账户登录系统进行测试</p>

<pre class="has">
<code>Xshell 6 (Build 0095)
Copyright (c) 2002 NetSarang Computer, Inc. All rights reserved.

Type `help' to learn how to use Xshell prompt.
[C:\~]$ 

Connecting to 192.168.33.100:22...
Connection established.
To escape to local shell, press 'Ctrl+Alt+]'.

WARNING! The remote SSH server rejected X11 forwarding request.
Last login: Wed Oct  3 11:25:59 2018
[hadoop@master100 ~]$ </code></pre>

<p>Hadoop 默认模式为非分布式模式，无需进行其他配置即可运行。非分布式即单 Java 进程，方便进行调试。<br>
 <br>
Hadoop 附带了丰富的例子（运行 ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0.jar 可以看到所有例子），包括 wordcount、terasort、join、grep 等。在此我们选择运行 grep 例子，我们将 input 文件夹中的所有文件作为输入，筛选当中符合正则表达式 dfs[a-z.]+ 的单词并统计出现的次数，最后输出结果到 output 文件夹中。</p>

<pre class="has">
<code>[hadoop@master100 ~]$ cd /usr/local/hadoop

[hadoop@master100 hadoop]$ ll
总用量 108
drwxr-xr-x. 2 hadoop hadoop   194 8月  18 2016 bin
drwxr-xr-x. 3 hadoop hadoop    20 8月  18 2016 etc
drwxr-xr-x. 2 hadoop hadoop   106 8月  18 2016 include
drwxr-xr-x. 3 hadoop hadoop    20 8月  18 2016 lib
drwxr-xr-x. 2 hadoop hadoop   239 8月  18 2016 libexec
-rw-r--r--. 1 hadoop hadoop 84854 8月  18 2016 LICENSE.txt
-rw-r--r--. 1 hadoop hadoop 14978 8月  18 2016 NOTICE.txt
-rw-r--r--. 1 hadoop hadoop  1366 8月  18 2016 README.txt
drwxr-xr-x. 2 hadoop hadoop  4096 8月  18 2016 sbin
drwxr-xr-x. 4 hadoop hadoop    31 8月  18 2016 share

[hadoop@master100 hadoop]$ mkdir ./input

# 将配置文件作为输入文件
[hadoop@master100 hadoop]$ cp ./etc/hadoop/* ./input

[hadoop@master100 hadoop]$ hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar grep ./input ./output 'dfs[a-z.]+'

...........

#查看运行结果
[hadoop@master100 hadoop]$ cat ./output/*
6	dfs.audit.logger
4	dfs.class
3	dfs.server.namenode.
2	dfs.period
2	dfs.audit.log.maxfilesize
2	dfs.audit.log.maxbackupindex
1	dfsmetrics.log
1	dfsadmin
1	dfs.servers
1	dfs.file
</code></pre>

<p>更多的MapReduce例子在这里就不一一测试了，有兴趣的可以自己去测试</p>

<hr><h3>相关推荐</h3>

<p><a href="https://blog.csdn.net/u010993514/article/details/82924871" rel="nofollow">VMware虚拟机安装Linux系统</a></p>

<p><a href="https://blog.csdn.net/u010993514/article/details/82926514" rel="nofollow">Linux系统下安装Java环境</a></p>

<p><a href="https://blog.csdn.net/u010993514/article/details/82933346" rel="nofollow">CentOS 7部署Hadoop（伪分布式）</a></p>

<p><a href="https://blog.csdn.net/u010993514/article/details/82935834" rel="nofollow">CentOS 7部署Hadoop集群（完全分布式）</a></p>

<p><a href="https://blog.csdn.net/u010993514/article/details/83009822" rel="nofollow">CentOS 7部署Hadoop集群（HA高可用集群）</a></p>            </div>
                </div>
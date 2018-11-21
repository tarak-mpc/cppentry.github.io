---
layout:     post
title:      Hadoop中的HDFS学习
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/heshangkung/article/details/47449445				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="实验环境">实验环境</h1>



<h2 id="服务器列表">服务器列表</h2>

<table>
<thead>
<tr>
  <th>服务器用途</th>
  <th>IP地址</th>
  <th>主机名</th>
</tr>
</thead>
<tbody><tr>
  <td>NameNode</td>
  <td>192.168.3.69</td>
  <td>namenode.abc.local</td>
</tr>
<tr>
  <td>DataNode 1</td>
  <td>192.168.3.70</td>
  <td>datanode1.abc.local</td>
</tr>
<tr>
  <td>DataNode 2</td>
  <td>192.168.3.71</td>
  <td>datanode2.abc.local</td>
</tr>
</tbody></table>




<h2 id="环境准备">环境准备</h2>

<p>三台服务器，都最小化安装CentOS 6.6，设置主机名，静态IP地址。</p>

<p>CentOS 6.6 最小化安装，默认是没有Java环境的，需要安装Java环境。</p>

<p>下载Java运行环境的安装介质：jre-7u80-linux-x64.tar.gz</p>

<pre><code># tar xvfz jre-7u80-linux-x64.tar.gz
# mv jre1.7.0_80/ /opt
</code></pre>

<p>在/etc/profile中设置Java环境变量</p>

<pre><code>export JAVA_HOME=/opt/jre1.7.0_80
PATH=$JAVA_HOME/bin:$PATH
export PATH
</code></pre>

<p>退出控制台，重新登录服务器，查看Java运行环境</p>

<pre><code>[root@namenode ~]# java -version
java version "1.7.0_80"
Java(TM) SE Runtime Environment (build 1.7.0_80-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.80-b11, mixed mode)
</code></pre>

<p>在其他两台服务器，也安装Java运行环境。</p>

<p>设置三台服务器之间SSH无密码登录</p>

<p>CentOS最小化安装没有安装scp,以及ssh客户端程序。通过rpm包安装如下：</p>

<pre><code># rpm -ivh libedit-2.11-4.20080712cvs.1.el6.x86_64.rpm
# rpm -ivh openssh-clients-5.3p1-104.el6.x86_64.rpm
libedit是openssh的依赖包
</code></pre>

<p>注： 通过SSH服务远程访问Linux服务器，连接非常慢，这时需要关闭SSH的DNS反解析,添加下面一行:</p>

<pre><code>UseDNS no
</code></pre>

<p>虽然配置文件中[UseDNS yes]被注释点，但默认开关就是yes。(SSH服务默认启用了DNS反向解析的功能)</p>

<p>同时在SSH客户端上，设置本地的DNS解析，编辑/etc/hosts文件，增加如下配置：</p>

<pre><code>192.168.3.69    namenode namenode.abc.local
192.168.3.70    datanode1 datanode1.abc.local
192.168.3.71    datanode2 datanode2.abc.local
</code></pre>

<p>注：在本机上安装好openssh-clients后，利用scp想把本地文件传到远程，这个时候，报错 <br>
-bash: scp: command not found。这是因为需要在远程也要安装openssh-client，要有scp程序。</p>

<p>在namenode上操作，生成本机的公钥，密码文件。</p>

<pre><code>[root@namenode ~]# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): #采用默认的文件存放密钥
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): #直接Enter，不输入密码
Enter same passphrase again: #直接Enter，不输入密码
Your identification has been saved in /root/.ssh/id_rsa. #生成密钥文件
Your public key has been saved in /root/.ssh/id_rsa.pub. #生成公钥文件
The key fingerprint is:
02:e0:5b:d0:53:19:25:48:e2:61:5a:a3:14:9e:d0:a6 root@namenode.abc.local
The key's randomart image is:
+--[ RSA 2048]----+
|.+Xo.o++.        |
|+B+*+ ..         |
|o=o o.           |
|E  o .           |
|  .   . S        |
|       .         |
|                 |
|                 |
|                 |
+-----------------+
</code></pre>

<p>在本机实现ssh登录本机无需密码</p>

<pre><code># cat /root/.ssh/id_rsa.pub &gt;&gt; /root/.ssh/authorized_keys
authorized_keys文件是新增文件，在ssh配置文件中使用
</code></pre>

<p>同时修改SSH服务的配置文件/etc/ssh/sshd_config。</p>

<pre><code>RSAAuthentication yes   # 去掉注释，开启RAS认证
PubkeyAuthentication yes    # 去掉注释
AuthorizedKeysFile      .ssh/authorized_keys    # 去掉注释
</code></pre>

<p>重启SSH服务。</p>

<pre><code>/etc/init.d/sshd restart
</code></pre>

<p>实现远程登录无需密码，需要将公钥文件上传到datanode1，将namenode上的/root/.ssh/id_rsa.pub上传到datanode1的/tmp目录。</p>

<pre><code># scp /root/.ssh/id_rsa.pub root@192.168.3.70:/tmp
此时，因为还未配置ssh无密码登录，还是需要输入密码，才能把文件上传过去。
</code></pre>

<p>在datanode1上，将namenode的公钥导入到SSH认证文件中。</p>

<pre><code>[root@datanode1 ~]# cat /tmp/id_rsa.pub &gt;&gt; /root/.ssh/authorized_keys
authorized_keys文件是新增文件，在ssh配置文件中使用
</code></pre>

<p>修改datanode1的SSH服务的配置文件/etc/ssh/sshd_config。</p>

<pre><code>RSAAuthentication yes   # 去掉注释，开启RAS认证
PubkeyAuthentication yes    # 去掉注释
AuthorizedKeysFile      .ssh/authorized_keys    # 去掉注释
</code></pre>

<p>重启SSH服务。</p>

<pre><code>/etc/init.d/sshd restart
</code></pre>

<p>在namenode上验证ssh无密码登录</p>

<pre><code>[root@namenode ~]# ssh root@192.168.3.70
The authenticity of host '192.168.3.70 (192.168.3.70)' can't be established.
RSA key fingerprint is c4:1f:56:68:f8:44:c7:d9:cc:97:b9:47:1c:37:bb:a7.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.168.3.70' (RSA) to the list of known hosts.
Last login: Mon Aug 10 18:47:02 2015 from 192.168.3.64
[root@datanode1 ~]# 
</code></pre>

<p>把namenode上的公钥文件上传到datanode2后，进行同样的操作。</p>

<p>同样，datanode1的公钥也要放到namenode，datanode2上，datanode2的公钥也要放到namenode，datanode1上。三台服务器之间，都要能够ssh无密码登录。</p>



<h1 id="安装hadoop">安装Hadoop</h1>

<p>下载Hadoop的安装介质：hadoop-2.7.1.tar.gz。 <br>
上传到namenode上，解压到/opt目录</p>

<pre><code># tar xvfz hadoop-2.7.1.tar.gz -C /opt/
</code></pre>

<p>在/opt/hadoop-2.7.1目录下创建数据存放的文件夹：tmp、hdfs、hdfs/data、hdfs/name。</p>

<pre><code>[root@namenode hadoop-2.7.1]# mkdir tmp
[root@namenode hadoop-2.7.1]# mkdir hdfs
[root@namenode hadoop-2.7.1]# cd hdfs/
[root@namenode hdfs]# mkdir data
[root@namenode hdfs]# mkdir name
</code></pre>



<h2 id="配置hadoop的运行环境opthadoop-271etchadoophadoop-envsh">配置hadoop的运行环境/opt/hadoop-2.7.1/etc/hadoop/hadoop-env.sh</h2>

<pre><code># The java implementation to use.
export JAVA_HOME=/opt/jre1.7.0_80
</code></pre>



<h2 id="配置namenode的运行参数opthadoop-271etchadoopcore-sitexml">配置namenode的运行参数/opt/hadoop-2.7.1/etc/hadoop/core-site.xml</h2>

<pre><code>&lt;configuration&gt;
    &lt;property&gt;
        &lt;name&gt;fs.defaultFS&lt;/name&gt;
        &lt;value&gt;hdfs://namenode.abc.local:9000&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
        &lt;value&gt;file:/opt/hadoop-2.7.1/tmp&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;io.file.buffer.size&lt;/name&gt;
        &lt;value&gt;131702&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>

<p>fs.defaultFS设置为NameNode的URI，io.file.buffer.size设置为在顺序文件中读写的缓存大小。</p>



<h2 id="配置hdfs的运行参数opthadoop-271etchadoophdfs-sitexml">配置hdfs的运行参数/opt/hadoop-2.7.1/etc/hadoop/hdfs-site.xml</h2>

<pre><code>&lt;configuration&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;
        &lt;value&gt;file:/opt/hadoop-2.7.1/hdfs/name&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;
        &lt;value&gt;file:/opt/hadoop-2.7.1/hdfs/data&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.replication&lt;/name&gt;
        &lt;value&gt;2&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;
        &lt;value&gt;true&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.namenode.secondary.http-address&lt;/name&gt;
        &lt;value&gt;datanode1.abc.local:9000&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>

<p>namenode的hdfs-site.xml是必须将dfs.webhdfs.enabled属性设置为true，否则就不能使用webhdfs的LISTSTATUS、LISTFILESTATUS等需要列出文件、文件夹状态的命令，因为这些信息都是由namenode来保存的。 <br>
hadoop 2.7.1 解决了namenode单点故障的问题，必须设置第二个namenode，通过dfs.namenode.secondary.http-address进行设置。</p>



<h2 id="配置mapred的运行参数opthadoop-271etchadoopmapred-sitexml">配置mapred的运行参数/opt/hadoop-2.7.1/etc/hadoop/mapred-site.xml</h2>

<pre><code>&lt;configuration&gt;
    &lt;property&gt;
        &lt;name&gt;mapreduce.framework.name&lt;/name&gt;
        &lt;value&gt;yarn&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>



<h2 id="配置yarn的运行参数opthadoop-271etchadoopyarn-sitexml">配置yarn的运行参数/opt/hadoop-2.7.1/etc/hadoop/yarn-site.xml</h2>

<pre><code>&lt;configuration&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
        &lt;value&gt;mapreduce_shuffle&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;yarn.resourcemanager.hostname&lt;/name&gt;
        &lt;value&gt;namenode.abc.local&lt;/value&gt;
    &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>



<h2 id="在datanode上安装hadoop">在datanode上安装hadoop</h2>

<p>通过scp工具，将namenode上的hadoop上传到datanode上。</p>

<pre><code># cd /opt/
# scp -r hadoop-2.7.1 root@192.168.3.70:/opt/
# scp -r hadoop-2.7.1 root@192.168.3.71:/opt/
</code></pre>



<h2 id="启动hadoop的hdfs环境">启动hadoop的hdfs环境</h2>

<p>在namenode上，执行hadoop的命令</p>

<pre><code># cd /opt/hadoop-2.7.1/sbin
# ./start-dfs.sh 
15/08/12 03:15:16 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Starting namenodes on [namenode.abc.local]
namenode.abc.local: starting namenode, logging to /opt/hadoop-2.7.1/logs/hadoop-root-namenode-namenode.abc.local.out
datanode2.abc.local: starting datanode, logging to /opt/hadoop-2.7.1/logs/hadoop-root-datanode-datanode2.abc.local.out
datanode1.abc.local: starting datanode, logging to /opt/hadoop-2.7.1/logs/hadoop-root-datanode-datanode1.abc.local.out
Starting secondary namenodes [datanode1.abc.local]
datanode1.abc.local: starting secondarynamenode, logging to /opt/hadoop-2.7.1/logs/hadoop-root-secondarynamenode-datanode1.abc.local.out
15/08/12 03:15:36 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
</code></pre>



<h1 id="hdfs的操作">HDFS的操作</h1>



<h2 id="格式化hdfs">格式化HDFS</h2>

<p>执行命令</p>

<pre><code># cd /opt/hadoop-2.7.1/bin
# ./hdfs namenode -format
..........................
15/08/12 03:48:58 INFO namenode.FSImage: Allocated new BlockPoolId: BP-486254444-192.168.3.69-1439322538827
15/08/12 03:48:59 INFO common.Storage: Storage directory **/opt/hadoop-2.7.1/hdfs/name** has been successfully formatted.
15/08/12 03:48:59 INFO namenode.NNStorageRetentionManager: Going to retain 1 images with txid &gt;= 0
15/08/12 03:48:59 INFO util.ExitUtil: Exiting with status 0
15/08/12 03:48:59 INFO namenode.NameNode: SHUTDOWN_MSG: 
/************************************************************
SHUTDOWN_MSG: Shutting down NameNode at namenode.abc.local/192.168.3.69
************************************************************/
该命令在namenode上关闭了hadoop的进程，但是在datanode1，datanode2上并没有关闭hadoop的进程。可以通过stop-dfs.sh来关闭datanode上的进程。
</code></pre>

<p>执行完命令后，在namenode的/opt/hadoop-2.7.1/hdfs/name目录下，生成文件。</p>

<pre><code>[root@namenode name]# tree
.
└── current
    ├── fsimage_0000000000000000000
    ├── fsimage_0000000000000000000.md5
    ├── seen_txid
    └── VERSION
</code></pre>

<p>重启hdfs系统。</p>

<p>在namenode的/opt/hadoop-2.7.1/hdfs/name目录下，查看文件</p>

<pre><code>[root@namenode name]# tree
.
├── current
│   ├── edits_0000000000000000001-0000000000000000002
│   ├── edits_0000000000000000003-0000000000000000004
│   ├── edits_0000000000000000005-0000000000000000006
│   ├── edits_0000000000000000007-0000000000000000008
│   ├── edits_0000000000000000009-0000000000000000010
│   ├── edits_0000000000000000011-0000000000000000012
│   ├── edits_0000000000000000013-0000000000000000014
│   ├── edits_0000000000000000015-0000000000000000016
│   ├── edits_0000000000000000017-0000000000000000018
│   ├── edits_0000000000000000019-0000000000000000020
│   ├── edits_0000000000000000021-0000000000000000022
│   ├── edits_0000000000000000023-0000000000000000024
│   ├── edits_0000000000000000025-0000000000000000026
│   ├── edits_0000000000000000027-0000000000000000028
│   ├── edits_0000000000000000029-0000000000000000030
│   ├── edits_0000000000000000031-0000000000000000032
│   ├── edits_0000000000000000033-0000000000000000034
│   ├── edits_0000000000000000035-0000000000000000036
│   ├── edits_0000000000000000037-0000000000000000038
│   ├── edits_0000000000000000039-0000000000000000040
│   ├── edits_0000000000000000041-0000000000000000042
│   ├── edits_0000000000000000043-0000000000000000044
│   ├── edits_0000000000000000045-0000000000000000046
│   ├── edits_0000000000000000047-0000000000000000047
│   ├── edits_inprogress_0000000000000000048
│   ├── fsimage_0000000000000000046
│   ├── fsimage_0000000000000000046.md5
│   ├── fsimage_0000000000000000047
│   ├── fsimage_0000000000000000047.md5
│   ├── seen_txid
│   └── VERSION
└── in_use.lock #该文件，说明NameNode已经启动
</code></pre>

<p>在datanode1，datanode2上的/opt/hadoop-2.7.1/hdfs/data目录下，查看文件。</p>

<pre><code>[root@datanode1 data]# tree
.
├── current
│   ├── BP-486254444-192.168.3.69-1439322538827
│   │   ├── current
│   │   │   ├── dfsUsed
│   │   │   ├── finalized
│   │   │   ├── rbw
│   │   │   └── VERSION
│   │   ├── scanner.cursor
│   │   └── tmp
│   └── VERSION
└── in_use.lock #该文件，说明DataNode已经启动
</code></pre>

<p>由于datanode1设置为第二个namenode，所以在/opt/hadoop-2.7.1/tmp目录下，生成了文件。</p>

<pre><code>[root@datanode1 tmp]# tree
.
└── dfs
    └── namesecondary
        ├── current
        │   ├── edits_0000000000000000001-0000000000000000002
        │   ├── edits_0000000000000000003-0000000000000000004
        │   ├── edits_0000000000000000005-0000000000000000006
        │   ├── edits_0000000000000000007-0000000000000000008
        │   ├── edits_0000000000000000009-0000000000000000010
        │   ├── edits_0000000000000000011-0000000000000000012
        │   ├── edits_0000000000000000013-0000000000000000014
        │   ├── edits_0000000000000000015-0000000000000000016
        │   ├── edits_0000000000000000017-0000000000000000018
        │   ├── edits_0000000000000000019-0000000000000000020
        │   ├── edits_0000000000000000021-0000000000000000022
        │   ├── edits_0000000000000000023-0000000000000000024
        │   ├── edits_0000000000000000025-0000000000000000026
        │   ├── edits_0000000000000000027-0000000000000000028
        │   ├── edits_0000000000000000029-0000000000000000030
        │   ├── edits_0000000000000000031-0000000000000000032
        │   ├── edits_0000000000000000033-0000000000000000034
        │   ├── edits_0000000000000000035-0000000000000000036
        │   ├── edits_0000000000000000037-0000000000000000038
        │   ├── edits_0000000000000000039-0000000000000000040
        │   ├── edits_0000000000000000041-0000000000000000042
        │   ├── edits_0000000000000000043-0000000000000000044
        │   ├── edits_0000000000000000045-0000000000000000046
        │   ├── edits_0000000000000000048-0000000000000000049
        │   ├── fsimage_0000000000000000047
        │   ├── fsimage_0000000000000000047.md5
        │   ├── fsimage_0000000000000000049
        │   ├── fsimage_0000000000000000049.md5
        │   └── VERSION
        └── in_use.lock
</code></pre>



<h2 id="向hdfs中放入文件">向HDFS中放入文件</h2>

<p>新建测试文件</p>

<pre><code># mkdir -p /root/input_data
# cd /root/input_data/
# echo "This is a test." &gt;&gt; test_data.txt
</code></pre>

<p>执行hadoop命令,放入文件</p>

<pre><code># cd /opt/hadoop-2.7.1/bin/
# ./hadoop fs -put /root/input_data/ /input_data
15/08/13 03:16:20 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
</code></pre>

<p>把/root/input_data目录下的文件，拷贝进HDFS的/input_data目录下</p>

<p>执行hadoop命令,查看文件</p>

<pre><code># ./hadoop fs -ls /input_data
15/08/13 03:20:42 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Found 1 items
-rw-r--r--   2 root supergroup         16 2015-08-13 03:20 /input_data/test_data.txt
</code></pre>

<p>针对HDFS的操作命令</p>

<pre><code># ./hadoop fs 
Usage: hadoop fs [generic options]
[-appendToFile &lt;localsrc&gt; ... &lt;dst&gt;]
[-cat [-ignoreCrc] &lt;src&gt; ...]
[-checksum &lt;src&gt; ...]
[-chgrp [-R] GROUP PATH...]
[-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]
[-chown [-R] [OWNER][:[GROUP]] PATH...]
[-copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]
[-copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]
[-count [-q] [-h] &lt;path&gt; ...]
[-cp [-f] [-p | -p[topax]] &lt;src&gt; ... &lt;dst&gt;]
[-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]
[-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]
[-df [-h] [&lt;path&gt; ...]]
[-du [-s] [-h] &lt;path&gt; ...]
[-expunge]
[-find &lt;path&gt; ... &lt;expression&gt; ...]
[-get [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]
[-getfacl [-R] &lt;path&gt;]
[-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]
[-getmerge [-nl] &lt;src&gt; &lt;localdst&gt;]
[-help [cmd ...]]
**[-ls [-d] [-h] [-R] [&lt;path&gt; ...]]**
[-mkdir [-p] &lt;path&gt; ...]
[-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]
[-moveToLocal &lt;src&gt; &lt;localdst&gt;]
[-mv &lt;src&gt; ... &lt;dst&gt;]
**[-put [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]**
[-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]
**[-rm [-f] [-r|-R] [-skipTrash] &lt;src&gt; ...]**
[-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; ...]
[-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]
[-setfattr {-n name [-v value] | -x name} &lt;path&gt;]
[-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; ...]
[-stat [format] &lt;path&gt; ...]
[-tail [-f] &lt;file&gt;]
[-test -[defsz] &lt;path&gt;]
[-text [-ignoreCrc] &lt;src&gt; ...]
[-touchz &lt;path&gt; ...]
[-truncate [-w] &lt;length&gt; &lt;path&gt; ...]
[-usage [cmd ...]]

Generic options supported are
-conf &lt;configuration file&gt;     specify an application configuration file
-D &lt;property=value&gt;            use value for given property
-fs &lt;local|namenode:port&gt;      specify a namenode
-jt &lt;local|resourcemanager:port&gt;    specify a ResourceManager
-files &lt;comma separated list of files&gt;    specify comma separated files to be copied to the map reduce cluster
-libjars &lt;comma separated list of jars&gt;    specify comma separated jar files to include in the classpath.
-archives &lt;comma separated list of archives&gt;    specify comma separated archives to be unarchived on the compute machines.

The general command line syntax is
bin/hadoop command [genericOptions] [commandOptions]
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
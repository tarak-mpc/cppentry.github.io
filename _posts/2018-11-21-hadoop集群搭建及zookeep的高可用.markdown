---
layout:     post
title:      hadoop集群搭建及zookeep的高可用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atelier-sulphurpool-light">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="hadoop_0"></a>一、安装配置hadoop</h2>
<ul>
<li>添加hadoop用户，设置密码</li>
</ul>
<pre><code>[root@server1 ~]# useradd -u 800 hadoop
[root@server1 ~]# ls
hadoop-2.7.3.tar.gz  jdk-7u79-linux-x64.tar.gz
[root@server1 ~]# passwd hadoop
Changing password for user hadoop.
New password: 
BAD PASSWORD: it is based on a dictionary word
BAD PASSWORD: is too simple
Retype new password: 
passwd: all authentication tokens updated successfully.
</code></pre>
<ul>
<li>解压jdk安装包，并且做软连接</li>
</ul>
<pre><code>[root@server1 ~]# mv * /home/hadoop/
[root@server1 ~]# su - hadoop
[hadoop@server1 ~]$ ls
hadoop-2.7.3.tar.gz  jdk-7u79-linux-x64.tar.gz
[hadoop@server1 ~]$ tar zxf jdk-7u79-linux-x64.tar.gz 
[hadoop@server1 ~]$ ls
hadoop-2.7.3.tar.gz  jdk1.7.0_79  jdk-7u79-linux-x64.tar.gz
[hadoop@server1 ~]$ ln -s jdk1.7.0_79/ java
[hadoop@server1 ~]$ ls
hadoop-2.7.3.tar.gz  java  jdk1.7.0_79  jdk-7u79-linux-x64.tar.gz
</code></pre>
<ul>
<li>配置java的环境变量。方便jdk更新</li>
</ul>
<pre><code>[hadoop@server1 ~]$ vim ~/.bash_profile 
 10 PATH=$PATH:$HOME/bin:/home/hadoop/java/bin
[hadoop@server1 ~]$ source ~/.bash_profile 
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120181728471.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<ul>
<li>在hadoop的脚本中配置java</li>
</ul>
<pre><code>[hadoop@server1 ~]$ tar zxf hadoop-2.7.3.tar.gz 
[hadoop@server1 ~]$ cd hadoop-2.7.3/etc/hadoop/
[hadoop@server1 hadoop]$ vim hadoop-env.sh
 25 export JAVA_HOME=/home/hadoop/java
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/2018112018192952.png" alt="在这里插入图片描述"></p>
<ul>
<li>测试hadoop</li>
</ul>
<pre><code>[hadoop@server1 hadoop-2.7.3]$ pwd
/home/hadoop/hadoop-2.7.3
[hadoop@server1 hadoop-2.7.3]$ bin/hadoop
[hadoop@server1 hadoop-2.7.3]$ mkdir input
[hadoop@server1 hadoop-2.7.3]$ cp etc/hadoop/*.xml input/
[hadoop@server1 hadoop-2.7.3]$ pwd
/home/hadoop/hadoop-2.7.3
[hadoop@server1 hadoop-2.7.3]$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar grep input output 'dfs[a-z.]+'
</code></pre>
<p><font color="0000dd">这里在测试的时候要给server1加本地解析</font></p>
<pre><code>[hadoop@server1 hadoop-2.7.3]$ su		
Password: 
[root@server1 hadoop-2.7.3]# vim /etc/hosts		#用超级用户执行
172.25.66.1	server1
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120185619225.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/2018112019002137.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="_62"></a>二、数据操作</h2>
<h3><a id="1hadoop_63"></a>1、配置hadoop</h3>
<pre><code>[hadoop@server1 hadoop-2.7.3]$ cd  etc/hadoop/
[hadoop@server1 hadoop]$ vim core-site.xml
 19 &lt;configuration&gt;
 20     &lt;property&gt;
 21             &lt;name&gt;fs.defaultFS&lt;/name&gt;
 22                     &lt;value&gt;hdfs://172.25.66.1:9000&lt;/value&gt;
 23                         &lt;/property&gt;
 24 &lt;/configuration&gt;
[hadoop@server1 hadoop]$ vim hdfs-site.xml
 19 &lt;configuration&gt;
 20     &lt;property&gt;
 21             &lt;name&gt;dfs.replication&lt;/name&gt;
 22                     &lt;value&gt;1&lt;/value&gt;
 23                         &lt;/property&gt;
 24 &lt;/configuration&gt;
</code></pre>
<h3><a id="2ssh_81"></a>2、添加ssh</h3>
<pre><code>[hadoop@server1 hadoop]$ ssh-keygen 
[hadoop@server1 hadoop]$ ssh-copy-id 172.25.66.1
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120190924341.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181120191208583.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<strong>连接各个主机，确保在启动dfs时，正常，不用输入yes</strong></p>
<pre><code>[hadoop@server1 hadoop]$ ssh 172.25.66.1
[hadoop@server1 ~]$ logout
Connection to 172.25.66.1 closed.
[hadoop@server1 hadoop]$ ssh localhost
[hadoop@server1 ~]$ logout
Connection to localhost closed.
[hadoop@server1 hadoop]$ ssh 0.0.0.0
[hadoop@server1 ~]$ logout
Connection to 0.0.0.0 closed.
[hadoop@server1 hadoop]$ ssh server1
Last login: Tue Nov 20 19:07:02 2018 from server1
[hadoop@server1 ~]$ logout
Connection to server1 closed.
</code></pre>
<h3><a id="3dfs_104"></a>3、启动dfs</h3>
<ul>
<li>格式化</li>
</ul>
<pre><code>[hadoop@server1 ~]$ cd hadoop-2.7.3/etc/hadoop/
[hadoop@server1 hadoop]$ cat slaves 
172.25.66.1
[hadoop@server1 hadoop]$ cd ..
[hadoop@server1 etc]$ cd ..
[hadoop@server1 hadoop-2.7.3]$ bin/hdfs namenode -format
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120191920788.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181120192251104.png" alt="在这里插入图片描述"></p>
<ul>
<li>启动dfs</li>
</ul>
<pre><code>[hadoop@server1 hadoop-2.7.3]$ sbin/start-dfs.sh
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120192551508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<ul>
<li>处理文件系统</li>
</ul>
<pre><code>[hadoop@server1 hadoop-2.7.3]$ bin/hdfs dfs -mkdir /user
[hadoop@server1 hadoop-2.7.3]$ bin/hdfs dfs -mkdir /user/hadoop
[hadoop@server1 hadoop-2.7.3]$ bin/hdfs dfs -ls
[hadoop@server1 hadoop-2.7.3]$ bin/hdfs dfs -put input/
[hadoop@server1 hadoop-2.7.3]$ bin/hdfs dfs -ls
Found 1 items
drwxr-xr-x   - hadoop supergroup          0 2018-11-20 19:28 input
[hadoop@server1 hadoop-2.7.3]$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar grep input output 'dfs[a-z.]+'
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120192947754.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p>在浏览器中访问172.25.66.1：50070，可以看到由一个server1端口是活着的状态<br>
<img src="https://img-blog.csdnimg.cn/20181120182239779.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
当给hadoop中上传文件，也可以在浏览器中看到，点击Utilities—Browse the file system<br>
<img src="https://img-blog.csdnimg.cn/20181120193602432.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181120193440844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="_139"></a>三、分布式文件存储</h2>
<h3><a id="1namenode_140"></a>1、namenode</h3>
<pre><code>[hadoop@server1 ~]$ cd hadoop-2.7.3
[hadoop@server1 hadoop-2.7.3]$ sbin/stop-dfs.sh
[hadoop@server1 hadoop-2.7.3]$ cd etc/hadoop/
[hadoop@server1 hadoop]$ vim hdfs-site.xml 
 19 &lt;configuration&gt;
 20     &lt;property&gt;
 21             &lt;name&gt;dfs.replication&lt;/name&gt;
 22                     &lt;value&gt;2&lt;/value&gt;
 23                         &lt;/property&gt;
 24 &lt;/configuration&gt;
[hadoop@server1 hadoop]$ vim slaves
172.25.66.2
172.25.66.3
[hadoop@server1 hadoop]$ jps 
3727 Jps
[hadoop@server1 hadoop]$ cd /tmp/
[hadoop@server1 tmp]$ ls
hadoop-hadoop      Jetty_0_0_0_0_50070_hdfs____w2cu08         yum.log
hsperfdata_hadoop  Jetty_0_0_0_0_50090_secondary____y6aanv
hsperfdata_root    Jetty_localhost_55086_datanode____.vo0c5n
[hadoop@server1 tmp]$ rm -fr *
[root@server1 ~]# yum install -y nfs-utils
[root@server1 ~]# /etc/init.d/rpcbind start
Starting rpcbind:                                          [  OK  ]
[root@server1 ~]# vim /etc/exports 
/home/hadoop    *(rw,anonuid=800,anongid=800)
[root@server1 ~]# /etc/init.d/nfs start
Starting NFS services:                                     [  OK  ]
Starting NFS mountd:                                       [  OK  ]
Starting NFS daemon:                                       [  OK  ]
Starting RPC idmapd:                                       [  OK  ]
[root@server1 ~]# exportfs -v
/home/hadoop    &lt;world&gt;(rw,wdelay,root_squash,no_subtree_check,anonuid=800,anongid=800)
[root@server1 ~]# exportfs -rv
exporting *:/home/hadoop
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120212601533.png" alt="在这里插入图片描述"></p>
<h3><a id="2datanode1722566217225663_179"></a>2、datanode（172.25.66.2和172.25.66.3一样的）</h3>
<pre><code>[root@server2 ~]# yum install -y nfs-utils
[root@server2 ~]# /etc/init.d/rpcbind start
Starting rpcbind:                                          [  OK  ]
[root@server2 ~]# useradd -u 800 hadoop
[root@server2 hadoop]# mount 172.25.66.1:/home/hadoop/ /home/hadoop/
[root@server2 hadoop]# showmount -e 172.25.66.1
Export list for 172.25.66.1:
/home/hadoop *
[root@server2 hadoop]# df
Filesystem                   1K-blocks    Used Available Use% Mounted on
/dev/mapper/VolGroup-lv_root  19134332 1660020  16502332  10% /
tmpfs                           510188       0    510188   0% /dev/shm
/dev/vda1                       495844   33490    436754   8% /boot
172.25.66.1:/home/hadoop/     19134336 1949248  16213120  11% /home/hadoop
</code></pre>
<h3><a id="3ssh_196"></a>3、测试ssh配置</h3>
<pre><code>[hadoop@server1 tmp]$ ssh server2
[hadoop@server2 ~]$ logout
[hadoop@server1 tmp]$ ssh server3
[hadoop@server3 ~]$ logout
[hadoop@server1 tmp]$ ssh 172.25.120.2
[hadoop@server2 ~]$ logout
[hadoop@server1 tmp]$ ssh 172.25.120.3
[hadoop@server2 ~]$ logout
</code></pre>
<h3><a id="4_207"></a>4、重新格式化</h3>
<pre><code>[hadoop@server1 hadoop]$ bin/hdfs namenode -format
[hadoop@server1 hadoop]$ ls /tmp/
hadoop-hadoop  hsperfdata_hadoop
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120213319666.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h3><a id="5dfsnamenodedatanode_214"></a>5、启动dfs：namenode和datanode分开</h3>
<pre><code>[hadoop@server1 hadoop-2.7.3]$ sbin/start-dfs.sh 
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120213741393.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<strong>在datanode节点测试</strong></p>
<pre><code>[hadoop@server2 ~]$ jps
1425 DataNode
1498 Jps
</code></pre>
<h3><a id="6datanode_225"></a>6、处理文件（datanode端实时同步）</h3>
<pre><code>[hadoop@server1 hadoop]$ bin/hdfs dfs -mkdir /user
[hadoop@server1 hadoop]$ bin/hdfs dfs -mkdir /user/hadoop
[hadoop@server1 hadoop]$ bin/hdfs dfs -put etc/hadoop/ input
</code></pre>
<h3><a id="71722566150070_231"></a>7、在浏览器中访问测试（172.25.66.1：50070）</h3>
<p><img src="https://img-blog.csdnimg.cn/20181120215049673.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<pre><code>[hadoop@server1 hadoop-2.7.3]$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar
[hadoop@server1 hadoop-2.7.3]$ bin/hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.3.jar wordcount input output
[hadoop@server1 hadoop-2.7.3]$ rm -fr output/
[hadoop@server1 hadoop-2.7.3]$ bin/hdfs dfs -get output
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/201811202204487.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p><img src="https://img-blog.csdnimg.cn/20181120220453560.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h2><a id="_242"></a>四、节点的添加与删除</h2>
<h3><a id="1server417225664_243"></a>1、在线添加server4（172.25.66.4）</h3>
<pre><code>[root@server4 ~]# yum install -y nfs-utils
[root@server4 ~]# useradd -u 800 hadoop
[root@server4 ~]# mount 172.25.120.1:/home/hadoop/ /home/hadoop/
[root@server4 ~]# su - hadoop
[hadoop@server4 ~]$ vim hadoop/etc/hadoop/slaves 
172.25.120.2
172.25.120.3
172.25.120.4
</code></pre>
<p>测试：</p>
<pre><code>[hadoop@server1 ~]$ ssh server4
[hadoop@server4 ~]$ logout
[hadoop@server1 ~]$ ssh 172.25.120.4
[hadoop@server4 ~]$ logout

[hadoop@server4 ~]$ cd hadoop
[hadoop@server4 hadoop]$ sbin/hadoop-daemon.sh start datanode
starting datanode, logging to /home/hadoop/hadoop-2.7.3/logs/hadoop-hadoop-datanode-server4.out
[hadoop@server4 hadoop]$ jps
1250 Jps
1177 DataNode
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120222829206.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181120222837513.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<strong>往hadoop中写入大文件</strong></p>
<pre><code>[hadoop@server4 hadoop-2.7.3]$ dd if=/dev/zero of=bigfile bs=1M count=500
103+0 records in
103+0 records out
108003328 bytes (108 MB) copied, 61.2318 s, 1.8 MB/s
[hadoop@server4 hadoop-2.7.3]$ bin/hdfs dfs -put bigfile
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/2018112022281638.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h3><a id="2server217225662_279"></a>2、在线删除server2（172.25.66.2）</h3>
<pre><code>[hadoop@server1 hadoop]$ pwd
/home/hadoop/hadoop/etc/hadoop
[hadoop@server1 hadoop]$ vim hdfs-site.xml 
    &lt;property&gt;
        &lt;name&gt;dfs.hosts.exclude&lt;/name&gt;
        &lt;value&gt;/home/hadoop/hadoop/etc/hadoop/exclude-hosts&lt;/value&gt;
    &lt;/property&gt;

[hadoop@server1 hadoop]$ vim hosts-exclude
172.25.66.4    ##删除的节点IP
[hadoop@server1 hadoop]$ vim slaves
172.25.120.2
172.25.120.3
[hadoop@server1 hadoop]$ bin/hdfs dfsadmin -refreshNodes
Refresh nodes successful
[hadoop@server1 hadoop]$ bin/hdfs dfsadmin -report
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120225448773.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181120225309795.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181120225335734.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181121083439660.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h3><a id="3yarn_302"></a>3、yarn模式</h3>
<pre><code>[hadoop@server1 hadoop]$ pwd
/home/hadoop/hadoop/etc/hadoop
[hadoop@server1 hadoop]$ cp mapred-site.xml.template mapred-site.xml
[hadoop@server1 hadoop]$ vim mapred-site.xml
 19 &lt;configuration&gt;
 20     &lt;property&gt;
 21             &lt;name&gt;mapreduce.framework.name&lt;/name&gt;
 22                     &lt;value&gt;yarn&lt;/value&gt;
 23                         &lt;/property&gt;
 24 
 25 &lt;/configuration&gt;
[hadoop@server1 hadoop]$ sbin/start-yarn.sh
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120230158874.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
在datanode中测试</p>
<pre><code>[hadoop@server2 ~]$ jps
1761 Jps
1425 DataNode
1654 NodeManager
</code></pre>
<h2><a id="zookeeper_325"></a>五、zookeeper集群搭建</h2>
<p>清空所有节点的/tmp</p>
<h3><a id="1server5_327"></a>1、server5主机</h3>
<pre><code>[root@server5 ~]# yum install- y  nfs-utils
[root@server5 ~]# /etc/init.d/rpcbind start
Starting rpcbind:                                          [  OK  ]

[root@server5 ~]# useradd -u 800 hadoop
[root@server5 ~]# mount 172.25.120.1:/home/hadoop/ /home/hadoop/
[root@server5 ~]# su - hadoop
[hadoop@server5 ~]$ ls
hadoop               java                       zookeeper-3.4.9.tar.gz
hadoop-2.7.3         jdk1.7.0_79
hadoop-2.7.3.tar.gz  jdk-7u79-linux-x64.tar.gz
</code></pre>
<h3><a id="2server2_341"></a>2、server2主机</h3>
<ul>
<li>停止掉所有服务<br>
<img src="https://img-blog.csdnimg.cn/20181120232529658.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></li>
<li>配置zookeeper</li>
</ul>
<pre><code>[hadoop@server2 ~]$ tar zxf zookeeper-3.4.9.tar.gz 
[hadoop@server2 ~]$ cd zookeeper-3.4.9
[hadoop@server2 zookeeper-3.4.9]$ cd conf/
[hadoop@server2 conf]$ cp zoo_sample.cfg zoo.cfg
[hadoop@server2 conf]$ vim zoo.cfg 
 30 server.1=172.25.66.2:2888:3888
 31 server.2=172.25.66.3:2888:3888
 32 server.3=172.25.66.4:2888:3888
</code></pre>
<h3><a id="3server234_355"></a>3、配置server2，3，4</h3>
<pre><code>[root@server2 zookeeper-3.4.9]# cd /tmp/zookeeper/
[root@server2 zookeeper]# echo 1 &gt;myid
[root@server2 zookeeper-3.4.9]# bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /root/zookeeper-3.4.9/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[root@server3 zookeeper-3.4.9]# cd /tmp/zookeeper/
[root@server3 zookeeper]# echo 2 &gt;myid
[root@server3 zookeeper-3.4.9]#  bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /root/zookeeper-3.4.9/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
[root@server4 zookeeper-3.4.9]# cd /tmp/zookeeper/
[root@server4 zookeeper]# echo 3 &gt;myid
[root@server4 zookeeper-3.4.9]#  bin/zkServer.sh start
ZooKeeper JMX enabled by default
Using config: /root/zookeeper-3.4.9/bin/../conf/zoo.cfg
Starting zookeeper ... STARTED
</code></pre>
<h3><a id="4_376"></a>4、查看所有节点信息</h3>
<pre><code>[root@server2 zookeeper-3.4.9]# bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /root/zookeeper-3.4.9/bin/../conf/zoo.cfg
Mode: follower

[root@server3 zookeeper-3.4.9]#  bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /root/zookeeper-3.4.9/bin/../conf/zoo.cfg
Mode: leader

[root@server4 zookeeper-3.4.9]#  bin/zkServer.sh status
ZooKeeper JMX enabled by default
Using config: /root/zookeeper-3.4.9/bin/../conf/zoo.cfg
Mode: follower
</code></pre>
<h3><a id="5leaderserver3_393"></a>5、在leader（server3）中测试</h3>
<pre><code>[root@server3 zookeeper-3.4.9]# bin/zkCli.sh
Connecting to localhost:2181
WATCHER::

WatchedEvent state:SyncConnected type:None path:null
[zk: localhost:2181(CONNECTED) 0] ls
[zk: localhost:2181(CONNECTED) 1] ls /
[zookeeper]
[zk: localhost:2181(CONNECTED) 2] quit
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181120233956183.png" alt="在这里插入图片描述"></p>
<h2><a id="zookeeper_406"></a>六、zookeeper的高可用</h2>
<h3><a id="1hadoop_407"></a>1、配置hadoop</h3>
<ul>
<li>配置slaves</li>
</ul>
<pre><code>[hadoop@server1 ~]$ cd hadoop/etc/
[hadoop@server1 etc]$ vim hadoop/slaves 
172.25.120.2
172.25.120.3
172.25.120.4
</code></pre>
<ul>
<li>配置core-site.xml</li>
</ul>
<pre><code>[hadoop@server1 etc]$ vim hadoop/core-site.xml 
 19 &lt;configuration&gt;
 20     &lt;property&gt;  
 21             &lt;name&gt;fs.defaultFS&lt;/name&gt;
 22                     &lt;value&gt;hdfs://master&lt;/value&gt;
 23                         &lt;/property&gt;
 24 &lt;property&gt;
 25 &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt;
 26 &lt;value&gt;172.25.66.2:2181,172.25.66.3:2181,172.25.66.4:2181&lt;/value&gt;
 27 &lt;/property&gt;
 28 &lt;/configuration&gt;
</code></pre>
<ul>
<li>配置hdfs-site.xml</li>
</ul>
<pre><code>[hadoop@server1 hadoop]$ vim hdfs-site.xml
&lt;configuration&gt;
    &lt;property&gt;
            &lt;name&gt;fs.defaultFS&lt;/name&gt;
                    &lt;value&gt;hdfs://masters&lt;/value&gt;
                        &lt;/property&gt;
&lt;property&gt;
&lt;name&gt;ha.zookeeper.quorum&lt;/name&gt;
&lt;value&gt;172.25.66.2:2181,172.25.66.3:2181,172.25.66.4:2181&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.namenode.rpc-address.masters.h1&lt;/name&gt;
&lt;value&gt;172.25.66.1:9000&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.namenode.http-address.masters.h1&lt;/name&gt;
&lt;value&gt;172.25.66.1:50070&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.namenode.rpc-address.masters.h2&lt;/name&gt;
&lt;value&gt;172.25.66.5:9000&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.namenode.http-address.masters.h2&lt;/name&gt;
&lt;value&gt;172.25.66.5:50070&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;
&lt;value&gt;qjournal://172.25.66.2:8485;172.25.66.3:8485;172.25.66.4:8485/masters&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.journalnode.edits.dir&lt;/name&gt;
&lt;value&gt;/tmp/journaldata&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt;
&lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.client.failover.proxy.provider.masters&lt;/name&gt;
&lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvid
er&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;
&lt;value&gt;
sshfence
shell(/bin/true)
&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.ha.fencing.ssh.private-key-files&lt;/name&gt;
&lt;value&gt;/home/hadoop/.ssh/id_rsa&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.ha.fencing.ssh.connect-timeout&lt;/name&gt;
&lt;value&gt;30000&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<ul>
<li>格式化hdfs集群</li>
</ul>
<pre><code>[hadoop@server1 hadoop]$ bin/hdfs namenode -format
[hadoop@server1 hadoop]$ scp -r /tmp/hadoop-hadoop 172.25.120.5:/tmp/

##查看server5主机
[root@server5 ~]# ls /tmp/
hadoop-hadoop
</code></pre>
<h3><a id="23DNjournalnod_501"></a>2、3个DN主机启动journalnod</h3>
<pre><code>[hadoop@server3 zookeeper-3.4.9]$ cd ~/hadoop
[hadoop@server3 hadoop]$ sbin/hadoop-daemon.sh start journalnode
starting journalnode, logging to /home/hadoop/hadoop-2.7.3/logs/hadoop-hadoop-journalnode-server3.out

查看3个DN主机zookeeper集群状态
[hadoop@server3 hadoop]$ jps
1881 DataNode
1698 QuorumPeerMain
1983 Jps
1790 JournalNode
</code></pre>
<h3><a id="3NNzookeeper_514"></a>3、NN主机格式化zookeeper</h3>
<ul>
<li>格式化后，启动zookeeper</li>
</ul>
<pre><code>[hadoop@server1 hadoop]$ bin/hdfs zkfc -formatZK
[hadoop@server1 hadoop]$ sbin/start-dfs.sh
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181121013644745.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<ul>
<li>查看zookeeper集群</li>
</ul>
<pre><code>[hadoop@server1 hadoop]$ jps
6694 Jps
6646 DFSZKFailoverController
6352 NameNode

[hadoop@server5 ~]$ jps
1396 DFSZKFailoverController
1298 NameNode
1484 Jps
</code></pre>
<h3><a id="4_533"></a>4、测试高可用</h3>
<p>访问http://172.25.66.1:50070<br>
<img src="https://img-blog.csdnimg.cn/2018112101381391.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181121013830711.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"><br>
上图可以看出server1为active，server5为standby</p>
<pre><code>[hadoop@server1 ~]$ jps
1396 DFSZKFailoverController
1298 NameNode
1484 Jps
[hadoop@server1 ~]$ kill -9 1298
[hadoop@server1 ~]$ jps
1396 DFSZKFailoverController
1515 Jps
</code></pre>
<p>执行上步操作，刷新浏览器，可以看出server5切换为master<br>
当server1再次启动时，状态为standby</p>
<pre><code>[hadoop@server5 hadoop]$ sbin/hadoop-daemon.sh start namenode
</code></pre>
<h3><a id="5DN_553"></a>5、DN主机查看</h3>
<pre><code>[hadoop@server2 hadoop]$ cd ~/zookeeper-3.4.9
[hadoop@server2 zookeeper-3.4.9]$ bin/zkCli.sh
Connecting to localhost:2181
[zk: localhost:2181(CONNECTED) 4] ls /hadoop-ha/masters
[ActiveBreadCrumb, ActiveStandbyElectorLock]
[zk: localhost:2181(CONNECTED) 5] get /hadoop-ha/masters/Active

ActiveBreadCrumb           ActiveStandbyElectorLock
[zk: localhost:2181(CONNECTED) 5] get /hadoop-ha/masters/ActiveBreadCrumb

</code></pre>
<p><img src="https://img-blog.csdnimg.cn/20181121014305902.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzQyMjQxOTMy,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
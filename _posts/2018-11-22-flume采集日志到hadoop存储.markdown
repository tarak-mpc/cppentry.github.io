---
layout:     post
title:      flume采集日志到hadoop存储
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="_0"></a>概览</h2>
<p>准备<br>
1.将hadoop的hdfs-site.xml和core-site.xml 放到flume/conf下<br>
2.将hadoop的jar包拷贝到flume的lib目录下<br>
3.配置flume2.conf<br>
4.启动flume(保证首先启动hdfs)<br>
5.测试</p>
<h2><a id="_9"></a>准备</h2>
<p>首先将flume配置完毕,参考<a href="https://blog.csdn.net/leanaoo/article/details/84260420" rel="nofollow">flume的单机版配置及测试</a></p>
<p>hadoop集群搭建完毕,参考<a href="https://blog.csdn.net/leanaoo/article/details/83098354" rel="nofollow">hadoop单机版搭建</a>,<a href="https://blog.csdn.net/leanaoo/article/details/83278384" rel="nofollow">hadoop集群搭建</a></p>
<p>工具:Xshell 5,Xftp 5</p>
<h2><a id="1hadoophdfssitexmlcoresitexml_flumeconf_17"></a>1.将hadoop的hdfs-site.xml和core-site.xml 放到flume/conf下</h2>
<pre><code>[root@localhost ~]# cp /usr/hadoop/hadoop-2.7.3/etc/hadoop/core-site.xml /usr/flume/apache-flume-1.8.0-bin/conf/
[root@localhost ~]# cp /usr/hadoop/hadoop-2.7.3/etc/hadoop/hdfs-site.xml /usr/flume/apache-flume-1.8.0-bin/conf/
</code></pre>
<h2><a id="2hadoopjarflumelib_24"></a>2.将hadoop的jar包拷贝到flume的lib目录下</h2>
<pre><code>[root@localhost ~]# cp $HADOOP_HOME/share/hadoop/common/hadoop-common-2.7.3.jar  /usr/flume/apache-flume-1.8.0-bin/lib/
[root@localhost ~]# cp $HADOOP_HOME/share/hadoop/common/lib/hadoop-auth-2.7.3.jar   /usr/flume/apache-flume-1.8.0-bin/lib/
[root@localhost ~]# cp $HADOOP_HOME/share/hadoop/common/lib/commons-configuration-1.6.jar   /usr/flume/apache-flume-1.8.0-bin/lib/
</code></pre>
<h2><a id="3flume2conf_32"></a>3.配置flume2.conf</h2>
<p>在flume安装目录下的conf下创建一个flume2.conf文件,并写入配置</p>
<pre><code>#定义agent名， source、channel、sink的名称  
a4.sources = r1  
a4.channels = c1  
a4.sinks = k1  
  
#具体定义source  
a4.sources.r1.type = spooldir 
 #先创建此目录，保证里面空的 
a4.sources.r1.spoolDir = /logs  
  
#具体定义channel  
a4.channels.c1.type = memory  
a4.channels.c1.capacity = 10000  
a4.channels.c1.transactionCapacity = 100  
  
#定义拦截器，为消息添加时间戳  
a4.sources.r1.interceptors = i1  
a4.sources.r1.interceptors.i1.type = org.apache.flume.interceptor.TimestampInterceptor$Builder  
  
  
#具体定义sink  
a4.sinks.k1.type = hdfs  
#集群的nameservers名字            
#单节点的直接写：hdfs://localhost:9000/flume/%Y%m%d  
#集群版的ns为集群名称
a4.sinks.k1.hdfs.path = hdfs://ns/flume/%Y%m%d  
a4.sinks.k1.hdfs.filePrefix = events-  
a4.sinks.k1.hdfs.fileType = DataStream  
#不按照条数生成文件  
a4.sinks.k1.hdfs.rollCount = 0  
#HDFS上的文件达到128M时生成一个文件  
a4.sinks.k1.hdfs.rollSize = 134217728  
#HDFS上的文件达到60秒生成一个文件  
a4.sinks.k1.hdfs.rollInterval = 60  
  
#组装source、channel、sink  
a4.sources.r1.channels = c1  
a4.sinks.k1.channel = c1  
</code></pre>
<h2><a id="4flumehdfs_76"></a>4.启动flume(保证首先启动hdfs)</h2>
<p>启动hdfs参考<a href="https://blog.csdn.net/leanaoo/article/details/83098354" rel="nofollow">hadoop单机版搭建</a>,<a href="https://blog.csdn.net/leanaoo/article/details/83278384" rel="nofollow">hadoop集群搭建</a>中的hadoop启动</p>
<pre><code>[root@localhost apache-flume-1.8.0-bin]# flume-ng agent -n a4 -c conf -f conf/flume2.conf -Dflume.root.logger=INFO,console 
</code></pre>
<h2><a id="5_85"></a>5.测试</h2>
<p>重新打开一个窗口</p>
<p>在/usr/tmp下创建一个a文件</p>
<pre><code>[root@localhost ~]# cd /usr/tmp/
[root@localhost tmp]# ls
hive_add.jar  hive_name.jar  hive_xx.jar  student
[root@localhost tmp]# vim a

</code></pre>
<p>随便输入一些内容</p>
<pre><code>dada
dada
aaa
aa

aaa
</code></pre>
<p>保存退出,将其copy到logs文件夹下</p>
<pre><code>[root@localhost tmp]# cp a /logs/
</code></pre>
<p>查看原来的窗口<br>
<img src="https://img-blog.csdnimg.cn/20181119220157130.png" alt="在这里插入图片描述"></p>
<p>然后登录http://你的主机ip:50070查看<br>
<img src="https://img-blog.csdnimg.cn/20181119220332397.png" alt="在这里插入图片描述"><br>
<img src="https://img-blog.csdnimg.cn/20181119220410546.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xlYW5hb28=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p><img src="https://img-blog.csdnimg.cn/20181119220421811.png" alt="在这里插入图片描述"></p>
<p>如此便是采集日志到hadoop成功了</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
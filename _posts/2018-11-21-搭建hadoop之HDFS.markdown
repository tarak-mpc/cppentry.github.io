---
layout:     post
title:      搭建hadoop之HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">一，HDFS的设计</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">     HDFS是什么：HDFS即Hadoop分布式文件系统（Hadoop分布式文件系统）;</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">     HDFS干什么：以</font></font><strong><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">流式数据访问模式</font></font></strong><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">来存储超大文件，运行于商用硬件集群上，是管理网络中跨多台计算机存储的文件系统</font><font style="vertical-align:inherit;">;</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">     HDFS缺点：要求低时间延迟数据访问的应用，存储大量的小文件，多用户写入，任意修改文件;</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">二，HDFS的特点</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">      主从模式：采用去中心化管理模式，高容错;</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">       低成本：使用廉价机器就可以提供存储;</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">        就近原则：在数据附近执行程序要比将数据转移到程序所在的位置之后再执行效率要高，大大降低系统IO，“移动计算要比移动数据便宜”;</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">         可移植性：HDFS可以实现不同平台之间的移植;</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">三，HDFS的组成</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">        HDFS既然是主从架构，就需要有主节点进行文件系统元数据管理，也就是</font></font><strong><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">NameNode</font></font></strong><font style="vertical-align:inherit;"><font style="vertical-align:inherit;"> ;从节点也就是负责实际数据存储，也就是</font></font><strong><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">DataNode</font></font></strong><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">。  </font></font><br><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">　　一个HDFS集群有且只有一个NameNode，但允许有许多DataNode ，主节点NameNode会负责管理，其他从节点数据节点负责存储。</font></font></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805212215381?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">四，使用linux快速入门</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">        1.访问</font></font><a href="http://hadoop.apache.org/releases.html" rel="nofollow"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">hadoop官网</font></font></a><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">，下载已发布的版本包，这里选择2.7.6的版本</font></font></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805213347425?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">        2.使用WinSCP工具，将本地下载的tar上传至linux环境</font></font></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805214137838?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">        3.登录账户，解压tar包，命令如下：</font></font><span style="color:#86ca5e;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">tar -zxvf hadoop-2.7.6.tar.gz / home / hadoop / app /</font></font></span><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">，完成后如下图。</font></font></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805214808482?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">        4.配置环境变量</font></font><span style="color:#f33b45;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">$ HADOOP_HOME</font></font></span><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">，</font></font><span style="color:#f33b45;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">$ HADOOP_CONF_DIR</font></font></span><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">，并生效</font></font></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805215500218?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805220124930?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">        5.导出至</font></font><span style="color:#f33b45;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">$ PATH</font></font></span><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">，命令如下：</font></font></p>

<pre class="has">
<code>$ echo export PATH=\$HADOOP_HOME/bin:\$HADOOP_HOME/sbin:\$PATH
$ echo export PATH=\$HADOOP_HOME/bin:\$HADOOP_HOME/sbin:\$PATH &gt;&gt; ~/.bashrc
$ whereis hadoop
$ source ~/.bashrc</code></pre>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">        6.配置HDFS文件</font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">           6.1。 </font></font><span style="color:#f33b45;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">核心的site.xml</font></font></span></p>

<p><span style="color:#f33b45;">        <img alt="" class="has" src="https://img-blog.csdn.net/20180805220919188?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></span></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">点击[i]，进入编辑，添加配置，这里可参考官方提供文件</font></font><a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/core-default.xml" rel="nofollow"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">core-default.xml</font></font></a><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">，配置默认的URL</font></font></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805221417904?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">           6.2 </font></font><span style="color:#f33b45;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;"> hdfs-site.xml</font></font></span></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805221900147?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">点击[i]，进入编辑，添加配置，这里可参考官方提供文件</font></font><a href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/hdfs-default.xml" rel="nofollow"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">hdfs-default.xml</font></font></a><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">，配置1.默认块，2.namenode，3.datanode</font></font></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/2018080522211184?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">           6.3配置  </font></font><span style="color:#f33b45;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">奴隶 </font></font></span></p>

<p><span style="color:#f33b45;">      </span><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">点击[i]，进入编辑，添加配置，这里直接配置主机映射名hadoop001</font></font></p>

<p><img alt="" class="has" src="https://img-blog.csdn.net/20180805222719984?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NtaWx5X3Rr/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">           6.4关闭防火墙 </font></font></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">           6.5第一次启动须将namenode格式化    </font></font><span style="color:#86ca5e;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">hdfs namenode -format</font></font></span></p>

<p><span style="color:#86ca5e;">           </span><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">6.6启动HDFS   </font></font><span style="color:#86ca5e;"><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">start-dfs.sh</font></font></span></p>

<p><font style="vertical-align:inherit;"><font style="vertical-align:inherit;">未完，待续</font></font></p>            </div>
                </div>
---
layout:     post
title:      日志采集框架Flume 与 数据迁移sqoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <strong>1, Flume介绍</strong><br><span>	</span>Flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。<br><span>	</span>Flume可以采集文件，socket数据包等各种形式源数据，又可以将采集到的数据输出到HDFS、hbase、hive、kafka等众多外部存储系统中<br><span>	</span>一般的采集需求，通过对flume的简单配置即可实现<br><p><span>	</span>Flume针对特殊场景也具备良好的自定义扩展能力，因此，flume可以适用于大部分的日常数据采集场景</p><p><strong>2, 运行机制</strong><br><span>	</span>Flume分布式系统中最核心的角色是agent，flume采集系统就是由一个个agent所连接起来形成<br><span>	</span>每一个agent相当于一个数据传递员，内部有三个组件：<br><span>	</span>Source：采集源，用于跟数据源对接，以获取数据<br><span>	</span>Sink：下沉地，采集数据的传送目的，用于往下一级agent传递数据或者往最终存储系统传递数据<br></p><p><span>	</span>Channel：angent内部的数据传输通道，用于从source将数据传递到sink</p><p>        <img src="https://img-blog.csdn.net/20180709211312762?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1ODM4Nzc3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p></p><p>        多级agent之间串联</p><p>        <img src="https://img-blog.csdn.net/20180709211338380?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1ODM4Nzc3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><strong>3, Flume的安装部署</strong><p></p><p>Flume的安装非常简单，只需要解压即可，当然，前提是已有hadoop环境<br>上传安装包到数据源所在节点上<br>然后解压  tar -zxvfapache-flume-1.6.0-bin.tar.gz<br></p><p>然后进入flume的目录，修改conf下的flume-env.sh，在里面配置</p><p></p><pre><code class="language-html">export JAVA_HOME=/usr/java/jdk1.7.0_67（输入你自己的jdk路径）</code></pre><pre><code class="language-html"><span style="color:#ff0000;">#输入你本地 flume-kafka-source-xx.jar包路径，注意flume1.6之前的版本好像没有这个jar, 只是要1.6及以上版本</span>
FLUME_CLASSPATH="/usr/app/apache-flume-1.6.0-bin/lib/flume-kafka-source-1.6.0.jar" </code></pre>根据数据采集的需求配置采集方案，描述在配置文件中(文件名可任意自定义)<br>指定采集方案配置文件，在相应的节点上启动flume agent<p></p><p>先用一个最简单的例子来测试一下程序环境是否正常</p><p>1、先在flume的conf目录下新建一个文件</p><br><pre><code class="language-html">vi   netcat-logger.conf
</code></pre><pre><code class="language-html"># 定义这个agent中各组件的名字
a1.sources = r1
a1.sinks = k1
a1.channels = c1

# 描述和配置source组件：r1
a1.sources.r1.type = netcat
a1.sources.r1.bind = localhost
a1.sources.r1.port = 44444

# 描述和配置sink组件：k1
a1.sinks.k1.type = logger

# 描述和配置channel组件，此处使用是内存缓存的方式
a1.channels.c1.type = memory
a1.channels.c1.capacity = 1000
a1.channels.c1.transactionCapacity = 100

# 描述和配置source  channel   sink之间的连接关系
a1.sources.r1.channels = c1
a1.sinks.k1.channel = c1
</code></pre><p>2、启动agent去采集数据</p><p>bin/flume-ng agent -c conf -f conf/netcat-logger.conf-n a1  -Dflume.root.logger=INFO,console<br></p><p></p><p>-c conf  指定flume自身的配置文件所在目录</p><p>-f conf/netcat-logger.con  指定我们所描述的采集方案</p><p>-n a1 指定我们这个agent的名字</p><p></p><p>3、测试</p><p>先要往agent采集监听的端口上发送数据，让agent有数据可采</p><p>随便在一个能跟agent节点联网的机器上</p><p>telnet anget-hostname  port   （telnet localhost 44444） </p><p><img src="https://img-blog.csdn.net/20180709211943593?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1ODM4Nzc3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""></p><p><strong>上面只是介绍了一种flume案例，实际上flume用处太多，具体用到的话大家可以去官网等等查看。</strong></p><pre class="html"></pre><p></p><p></p><h1><span style="font-size:16px;"> sqoop数据迁移</span></h1><p>sqoop是apache旗下一款“Hadoop和关系数据库服务器之间传送数据”的工具。<br>导入数据：MySQL，Oracle导入数据到Hadoop的HDFS、HIVE、HBASE等数据存储系统；<br>导出数据：从Hadoop的文件系统中导出数据到关系数据库<br></p><p><img src="https://img-blog.csdn.net/20180709212237650?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI1ODM4Nzc3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt=""><br></p><p></p><h2><span style="font-size:14px;">工作机制</span></h2><p>将导入或导出命令翻译成mapreduce程序来实现</p><p>在翻译出的mapreduce中主要是对inputformat和outputformat进行定制</p><p> </p><h3><span style="font-size:14px;">sqoop安装</span></h3><p>安装sqoop的前提是已经具备java和hadoop的环境</p><h4><span style="font-size:14px;">下载并解压</span></h4><p>最新版下载地址http://ftp.wayne.edu/apache/sqoop 自行下载安装不解释</p><h4><span style="font-size:14px;">修改配置文件</span></h4><p>进入sqoop的conf目录</p><p>$ mv sqoop-env-template.sh sqoop-env.sh</p><p>打开sqoop-env.sh并编辑下面几行：</p><p>export HADOOP_COMMON_HOME=<span style="color:#ffcc00;">/home/hadoop/apps/hadoop-2.6.1/</span> （输入你自己的hadoop安装目录）</p><p>export HADOOP_MAPRED_HOME=/home/hadoop/apps/hadoop-2.6.1/</p><p>exportHIVE_HOME=/home/hadoop/apps/hive-1.2.1</p><h4><span style="font-size:14px;">加入mysql的jdbc驱动包，放到sqoop的lib目录下， 没有的自己下载不解释</span></h4><p>cp ~/app/hive/lib/mysql-connector-java-5.1.28.jar   $SQOOP_HOME/lib/</p><h4><span style="font-size:14px;">验证启动</span></h4><p>$ cd $SQOOP_HOME/bin</p><p>$ sqoop-version</p><p>预期的输出：</p><p>15/12/17 14:52:32 INFO sqoop.Sqoop: RunningSqoop version: 1.4.6</p><p>Sqoop 1.4.6 git commit id5b34accaca7de251fc91161733f906af2eddbe83</p><p>Compiled by abe on Fri Aug 1 11:19:26 PDT2015</p><p>到这里，整个Sqoop安装工作完成。</p><h3><span style="font-size:14px;">语法（这里不解释太多了，语法很多，需要的自行百度）</span></h3><p>下面的语法用于将数据导入HDFS。</p><table border="1" cellspacing="0" cellpadding="0" width="568"><tbody><tr><td valign="top"><p>$ sqoop import (generic-args)  (import-args) </p></td> </tr></tbody></table><p>下面的命令用于从MySQL数据库服务器中的emp表导入HDFS。</p><table border="1" cellspacing="0" cellpadding="0" width="568"><tbody><tr><td valign="top"><p>$bin/sqoop import   \</p>  <p>--connect  jdbc:mysql://sqooptest:3306/test   \</p>  <p>--username root  \</p>  <p>--password root   \</p>  <p>--table emp   \</p>  <p>--m 1 </p></td></tr></tbody></table><p></p><p>为了验证在HDFS导入的数据，请使用以下命令查看导入的数据</p><table border="1" cellspacing="0" cellpadding="0" width="568"><tbody><tr><td valign="top"><p>$  $HADOOP_HOME/bin/hadoop fs -cat /user/hadoop/emp/part-m-00000</p></td> </tr></tbody></table><br>            </div>
                </div>
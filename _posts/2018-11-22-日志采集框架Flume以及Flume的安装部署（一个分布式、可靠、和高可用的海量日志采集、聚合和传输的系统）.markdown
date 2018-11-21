---
layout:     post
title:      日志采集框架Flume以及Flume的安装部署（一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/Biexiansheng/article/details/78813105				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p> <span style="font-size:18px;">Flume支持众多的source和sink类型，详细手册可参考官方文档，更多source和sink组件</span></p>
<p><span style="font-size:18px;"><a href="http://flume.apache.org/FlumeUserGuide.html" rel="nofollow"><span style="text-decoration:underline;">http://flume.apache.org/FlumeUserGuide.html</span></a></span><br></p>
<p><span style="font-size:18px;">Flume官网入门指南：</span></p>
<p><span style="font-size:18px;"><img src="http://images2017.cnblogs.com/blog/1002211/201712/1002211-20171213181115222-1386785333.png" alt=""></span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201712/1002211-20171213181301316-657344694.png" alt=""></p>
<hr><p> <span style="font-size:18px;">1：Flume的概述和介绍：</span></p>
<blockquote>
<p><span style="font-size:18px;">（1）：Flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统。<br>
（2）：Flume可以采集文件，socket数据包等各种形式源数据，又可以将采集到的数据输出到HDFS、hbase、hive、kafka等众多外部存储系统中<br>
（3）：一般的采集需求，通过对flume的简单配置即可实现<br>
（4）：Flume针对特殊场景也具备良好的自定义扩展能力，因此，flume可以适用于大部分的日常数据采集场景</span></p>
</blockquote>
<p><span style="font-size:18px;">2：Flume的运行机制：</span></p>
<blockquote>
<p><span style="font-size:18px;">（1）：Flume分布式系统中最核心的角色是agent，flume采集系统就是由一个个agent所连接起来形成。</span></p>
<p><span style="font-size:18px;">（2）：每一个agent相当于一个数据传递员，内部有三个组件：<br>
　　　　a)：Source：采集源，用于跟数据源对接，以获取数据。<br>
　　　　b)：Sink：下沉地，采集数据的传送目的，用于往下一级agent传递数据或者往最终存储系统传递数据。<br>
　　　　c)：Channel：angent内部的数据传输通道，用于从source将数据传递到sink。</span></p>
<p><span style="font-size:18px;">注意：Source 到 Channel 到 Sink之间传递数据的形式是Event事件；Event事件是一个数据流单元。</span></p>
</blockquote>
<p><span style="font-size:18px;"> 下面介绍单个Agent的fulme数据采集示意图：</span></p>
<p><span style="font-size:18px;"><img src="http://images2017.cnblogs.com/blog/1002211/201712/1002211-20171213102822722-1740492036.png" alt=""></span></p>
<p> <span style="font-size:18px;"><span style="font-family:'宋体';">多级</span>agent<span style="font-family:'宋体';">之间串联</span>示意图：</span></p>
<p><span style="font-size:18px;"><img src="http://images2017.cnblogs.com/blog/1002211/201712/1002211-20171213105049051-230973295.png" alt=""></span></p>
<p> <span style="font-size:18px;">3：Flume的安装部署：</span></p>
<blockquote>
<p><span style="font-size:18px;">（1）、Flume的安装非常简单，只需要解压即可，当然，前提是已有hadoop环境：<br>
　　a）：上传安装包到数据源所在节点上，上传过程省略。<br>
　　b）：然后解压  tar -zxvf apache-flume-1.6.0-bin.tar.gz；</span></p>
<p><span style="font-size:18px;">　　　　[root@master package]# tar -zxvf apache-flume-1.6.0-bin.tar.gz -C /home/hadoop/<br>
　　c）：然后进入flume的目录，修改conf下的flume-env.sh，在里面配置JAVA_HOME；（由于<span style="font-size:18px;">conf</span>目录下面是<span style="font-size:18px;"> flume-env.sh.template</span>，所以我复制一个<span style="font-size:18px;">flume-env.sh</span>，然后进行修改<span style="font-size:18px;">JAVA_HOME</span>）</span></p>
<p><span style="font-size:18px;">　　　　[root@master conf]# cp flume-env.sh.template flume-env.sh</span></p>
<p><span style="font-size:18px;">　　　　[root@master conf]# vim flume-env.sh</span></p>
<p><span style="font-size:18px;">　　　　<span style="color:#ff0000;">然后将#注释去掉，加上自己的<span style="font-size:18px;">JAVA_HOME</span>：export JAVA_HOME=/home/hadoop/jdk1.7.0_65</span><br>
（2）、根据数据采集的需求配置采集方案，描述在配置文件中(文件名可任意自定义)；<br>
（3）、指定采集方案配置文件，在相应的节点上启动flume agent；<br></span></p>
<p><span style="font-size:18px;">（4）、可以先用一个最简单的例子来测试一下程序环境是否正常（</span><span style="font-size:18px;">在flume的<span style="font-family:Calibri;">conf</span>目录下新建一个文件</span><span style="font-size:18px;">）；</span></p>
</blockquote>
<p><span style="font-size:18px;">4：部署安装好，可以开始配置采集方案（这里是一个简单的采集方案配置的使用，从网络端口接收数据，然后下沉到logger）， 然后需要配置一个文件，这个采集配置文件名称，netcat-logger.conf，</span><span style="font-size:18px;">采集配置文件netcat-logger.conf的内容如下所示：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;"> 1</span> # example.conf: A single-<span style="color:#000000;">node Flume configuration
</span><span style="color:#008080;"> 2</span> 
<span style="color:#008080;"> 3</span> # Name the components on <span style="color:#0000ff;">this</span><span style="color:#000000;"> agent
</span><span style="color:#008080;"> 4</span> <span style="color:#000000;">#定义这个agent中各组件的名字，给那三个组件sources，sinks，channels取个名字,是一个逻辑代号:
</span><span style="color:#008080;"> 5</span> <span style="color:#000000;">#a1是agent的代表。
</span><span style="color:#008080;"> 6</span> a1.sources =<span style="color:#000000;"> r1
</span><span style="color:#008080;"> 7</span> a1.sinks =<span style="color:#000000;"> k1
</span><span style="color:#008080;"> 8</span> a1.channels =<span style="color:#000000;"> c1
</span><span style="color:#008080;"> 9</span> 
<span style="color:#008080;">10</span> # Describe/<span style="color:#000000;">configure the source 描述和配置source组件：<span style="font-family:Calibri;">r1</span>
</span><span style="color:#008080;">11</span> #类型, 从网络端口接收数据,在本机启动, 所以localhost, type=<span style="color:#000000;">spoolDir采集目录源,目录里有就采
</span><span style="color:#008080;">12</span> <span style="color:#000000;">#type是类型，是采集源的具体实现，这里是接受网络端口的，netcat可以从一个网络端口接受数据的。netcat在linux里的程序就是nc，可以学习一下。
</span><span style="color:#008080;">13</span> <span style="color:#000000;">#bind绑定本机localhost。port端口号为44444。
</span><span style="color:#008080;">14</span> 
<span style="color:#008080;">15</span> a1.sources.r1.type =<span style="color:#000000;"> netcat
</span><span style="color:#008080;">16</span> a1.sources.r1.bind =<span style="color:#000000;"> localhost
</span><span style="color:#008080;">17</span> a1.sources.r1.port = <span style="color:#800080;">44444</span>
<span style="color:#008080;">18</span> 
<span style="color:#008080;">19</span> <span style="color:#000000;"># Describe the sink<span> 描述和配置sink组件：k1
</span></span><span style="color:#008080;">20</span> <span style="color:#000000;">#type，下沉类型，使用logger，将数据打印到屏幕上面。
</span><span style="color:#008080;">21</span> a1.sinks.k1.type =<span style="color:#000000;"> logger
</span><span style="color:#008080;">22</span> 
<span style="color:#008080;">23</span> # Use a channel which buffers events <span style="color:#0000ff;">in</span><span style="color:#000000;"> memory 描述和配置channel组件，此处使用是内存缓存的方式
</span><span style="color:#008080;">24</span> <span style="color:#000000;">#type类型是内存memory。
</span><span style="color:#008080;">25</span> <span style="color:#000000;">#下沉的时候是一批一批的, 下沉的时候是一个个eventChannel参数解释：
</span><span style="color:#008080;">26</span> <span style="color:#000000;">#capacity：默认该通道中最大的可以存储的event数量，1000是代表1000条数据。
</span><span style="color:#008080;">27</span> <span style="color:#000000;">#trasactionCapacity：每次最大可以从source中拿到或者送到sink中的event数量。
</span><span style="color:#008080;">28</span> a1.channels.c1.type =<span style="color:#000000;"> memory
</span><span style="color:#008080;">29</span> a1.channels.c1.capacity = <span style="color:#800080;">1000</span>
<span style="color:#008080;">30</span> a1.channels.c1.transactionCapacity = <span style="color:#800080;">100</span>
<span style="color:#008080;">31</span> 
<span style="color:#008080;">32</span> <span style="color:#000000;"># Bind the source and sink to the channel 描述和配置source  channel   sink之间的连接关系
</span><span style="color:#008080;">33</span> <span style="color:#000000;">#将sources和sinks绑定到channel上面。
</span><span style="color:#008080;">34</span> a1.sources.r1.channels =<span style="color:#000000;"> c1
</span><span style="color:#008080;">35</span> a1.sinks.k1.channel = c1</pre>
</div>
<p><span style="font-size:18px;">下面在flume的conf目录下面编辑这个文件</span><span style="font-size:18px;">netcat-logger.conf：</span></p>
<p><span style="font-size:18px;">[root@master conf]# vim netcat-logger.conf</span></p>
<p><span style="font-size:18px;"><img src="http://images2017.cnblogs.com/blog/1002211/201712/1002211-20171213115719332-1139489500.png" alt=""></span></p>
<p> <span style="font-size:18px;">启动agent去采集数据，然后可以进行启动了，启动命令如下所示：</span></p>
<p><span style="font-size:18px;">bin/flume-ng agent -c conf -f conf/netcat-logger.conf -n a1  -Dflume.root.logger=INFO,console</span></p>
<p><span style="font-size:18px;">　　-c conf   指定flume自身的配置文件所在目录</span></p>
<p><span style="font-size:18px;">　　-f conf/netcat-logger.con  指定我们所描述的采集方案</span></p>
<p><span style="font-size:18px;">　　-n a1  指定我们这个agent的名字</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;">1</span> <span style="color:#000000;">启动命令：
</span><span style="color:#008080;">2</span> <span style="color:#000000;">#告诉flum启动一个agent。
</span><span style="color:#008080;">3</span> #--<span style="color:#000000;">conf conf指定配置参数,。
</span><span style="color:#008080;">4</span> #conf/netcat-<span style="color:#000000;">logger.conf指定采集方案的那个文件(自命名)。
</span><span style="color:#008080;">5</span> #--<span style="color:#000000;">name a1:agent的名字,即agent的名字为a1。
</span><span style="color:#008080;">6</span> #-Dflume.root.logger=<span style="color:#000000;">INFO,console给log4j传递的参数。
</span><span style="color:#008080;">7</span> $ bin/flume-ng agent --conf conf --conf-file conf/netcat-logger.conf --name a1 -Dflume.root.logger=INFO,console</pre>
</div>
<p><span style="font-size:18px;"> 演示如下所示：</span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201712/1002211-20171213120019176-304026498.png" alt=""></p>
<p><span style="font-size:18px;"> 启动的信息如下所示，可以启动到前台，也可以启动到后台：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;"> 1</span> [root@master apache-flume-<span style="color:#800080;">1.6</span>.<span style="color:#800080;">0</span>-bin]# bin/flume-ng agent --conf conf --conf-file conf/netcat-logger.conf --name a1 -Dflume.root.logger=<span style="color:#000000;">INFO,console
</span><span style="color:#008080;"> 2</span> Info: Sourcing environment configuration script /home/hadoop/apache-flume-<span style="color:#800080;">1.6</span>.<span style="color:#800080;">0</span>-bin/conf/flume-<span style="color:#000000;">env.sh
</span><span style="color:#008080;"> 3</span> Info: Including Hadoop libraries found via (/home/hadoop/hadoop-<span style="color:#800080;">2.4</span>.<span style="color:#800080;">1</span>/bin/hadoop) <span style="color:#0000ff;">for</span><span style="color:#000000;"> HDFS access
</span><span style="color:#008080;"> 4</span> Info: Excluding /home/hadoop/hadoop-<span style="color:#800080;">2.4</span>.<span style="color:#800080;">1</span>/share/hadoop/common/lib/slf4j-api-<span style="color:#800080;">1.7</span>.<span style="color:#800080;">5</span>.jar <span style="color:#0000ff;">from</span><span style="color:#000000;"> classpath
</span><span style="color:#008080;"> 5</span> Info: Excluding /home/hadoop/hadoop-<span style="color:#800080;">2.4</span>.<span style="color:#800080;">1</span>/share/hadoop/common/lib/slf4j-log4j12-<span style="color:#800080;">1.7</span>.<span style="color:#800080;">5</span>.jar <span style="color:#0000ff;">from</span><span style="color:#000000;"> classpath
</span><span style="color:#008080;"> 6</span> Info: Including Hive libraries found via () <span style="color:#0000ff;">for</span><span style="color:#000000;"> Hive access
</span><span style="color:#008080;"> 7</span> + exec /home/hadoop/jdk1.<span style="color:#800080;">7</span>.0_65/bin/java -Xmx20m -Dflume.root.logger=INFO,console -cp <span style="color:#800000;">'</span><span style="color:#800000;">/home/hadoop/apache-flume-1.6.0-bin/conf:/home/hadoop/apache-flume-1.6.0-bin/lib/*:/home/hadoop/hadoop-2.4.1/etc/hadoop:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/activation-1.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/asm-3.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/avro-1.7.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-beanutils-1.7.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-beanutils-core-1.8.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-cli-1.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-codec-1.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-collections-3.2.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-compress-1.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-configuration-1.6.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-digester-1.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-el-1.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-httpclient-3.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-io-2.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-lang-2.6.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-logging-1.1.3.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-math3-3.1.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/commons-net-3.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/guava-11.0.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/hadoop-annotations-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/hadoop-auth-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/httpclient-4.2.5.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/httpcore-4.2.5.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jackson-core-asl-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jackson-jaxrs-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jackson-mapper-asl-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jackson-xc-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jasper-compiler-5.5.23.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jasper-runtime-5.5.23.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/java-xmlbuilder-0.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jaxb-api-2.2.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jaxb-impl-2.2.3-1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jersey-core-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jersey-json-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jersey-server-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jets3t-0.9.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jettison-1.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jetty-6.1.26.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jetty-util-6.1.26.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jsch-0.1.42.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jsp-api-2.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/jsr305-1.3.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/junit-4.8.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/log4j-1.2.17.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/mockito-all-1.8.5.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/netty-3.6.2.Final.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/paranamer-2.3.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/protobuf-java-2.5.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/servlet-api-2.5.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/snappy-java-1.0.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/stax-api-1.0-2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/xmlenc-0.52.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/xz-1.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib/zookeeper-3.4.5.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/hadoop-common-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/hadoop-common-2.4.1-tests.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/hadoop-nfs-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/common/jdiff:/home/hadoop/hadoop-2.4.1/share/hadoop/common/lib:/home/hadoop/hadoop-2.4.1/share/hadoop/common/sources:/home/hadoop/hadoop-2.4.1/share/hadoop/common/templates:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/asm-3.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/commons-cli-1.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/commons-codec-1.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/commons-daemon-1.0.13.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/commons-el-1.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/commons-io-2.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/commons-lang-2.6.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/commons-logging-1.1.3.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/guava-11.0.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jackson-core-asl-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jackson-mapper-asl-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jasper-runtime-5.5.23.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jersey-core-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jersey-server-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jetty-6.1.26.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jetty-util-6.1.26.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jsp-api-2.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/jsr305-1.3.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/log4j-1.2.17.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/netty-3.6.2.Final.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/protobuf-java-2.5.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/servlet-api-2.5.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib/xmlenc-0.52.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/hadoop-hdfs-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/hadoop-hdfs-2.4.1-tests.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/hadoop-hdfs-nfs-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/jdiff:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/lib:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/sources:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/templates:/home/hadoop/hadoop-2.4.1/share/hadoop/hdfs/webapps:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/activation-1.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/aopalliance-1.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/asm-3.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/commons-cli-1.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/commons-codec-1.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/commons-collections-3.2.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/commons-compress-1.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/commons-httpclient-3.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/commons-io-2.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/commons-lang-2.6.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/commons-logging-1.1.3.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/guava-11.0.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/guice-3.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/guice-servlet-3.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jackson-core-asl-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jackson-jaxrs-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jackson-mapper-asl-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jackson-xc-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/javax.inject-1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jaxb-api-2.2.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jaxb-impl-2.2.3-1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jersey-client-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jersey-core-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jersey-guice-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jersey-json-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jersey-server-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jettison-1.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jetty-6.1.26.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jetty-util-6.1.26.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jline-0.9.94.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/jsr305-1.3.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/leveldbjni-all-1.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/log4j-1.2.17.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/protobuf-java-2.5.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/servlet-api-2.5.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/stax-api-1.0-2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/xz-1.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib/zookeeper-3.4.5.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-api-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-applications-distributedshell-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-applications-unmanaged-am-launcher-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-client-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-common-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-server-applicationhistoryservice-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-server-common-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-server-nodemanager-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-server-resourcemanager-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-server-tests-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/hadoop-yarn-server-web-proxy-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/lib:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/sources:/home/hadoop/hadoop-2.4.1/share/hadoop/yarn/test:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/aopalliance-1.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/asm-3.2.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/avro-1.7.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/commons-compress-1.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/commons-io-2.4.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/guice-3.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/guice-servlet-3.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/hadoop-annotations-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/hamcrest-core-1.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/jackson-core-asl-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/jackson-mapper-asl-1.8.8.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/javax.inject-1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/jersey-core-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/jersey-guice-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/jersey-server-1.9.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/junit-4.10.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/log4j-1.2.17.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/netty-3.6.2.Final.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/paranamer-2.3.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/protobuf-java-2.5.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/snappy-java-1.0.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib/xz-1.0.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-app-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-common-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-core-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-hs-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-hs-plugins-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-jobclient-2.4.1-tests.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-client-shuffle-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.4.1.jar:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/lib-examples:/home/hadoop/hadoop-2.4.1/share/hadoop/mapreduce/sources:/home/hadoop/hadoop-2.4.1/contrib/capacity-scheduler/*.jar:/lib/*</span><span style="color:#800000;">'</span> -Djava.library.path=:/home/hadoop/hadoop-<span style="color:#800080;">2.4</span>.<span style="color:#800080;">1</span>/lib/native org.apache.flume.node.Application --conf-file conf/netcat-logger.conf --<span style="color:#000000;">name a1
</span><span style="color:#008080;"> 8</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">108</span> (lifecycleSupervisor-<span style="color:#800080;">1</span>-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.PollingPropertiesFileConfigurationProvider.start(PollingPropertiesFileConfigurationProvider.java:<span style="color:#800080;">61</span><span style="color:#000000;">)] Configuration provider starting
</span><span style="color:#008080;"> 9</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">130</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.PollingPropertiesFileConfigurationProvider$FileWatcherRunnable.run(PollingPropertiesFileConfigurationProvider.java:<span style="color:#800080;">133</span>)] Reloading configuration file:conf/netcat-<span style="color:#000000;">logger.conf
</span><span style="color:#008080;">10</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">142</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:<span style="color:#800080;">931</span><span style="color:#000000;">)] Added sinks: k1 Agent: a1
</span><span style="color:#008080;">11</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">143</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:<span style="color:#800080;">1017</span><span style="color:#000000;">)] Processing:k1
</span><span style="color:#008080;">12</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">143</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.conf.FlumeConfiguration$AgentConfiguration.addProperty(FlumeConfiguration.java:<span style="color:#800080;">1017</span><span style="color:#000000;">)] Processing:k1
</span><span style="color:#008080;">13</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">157</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.conf.FlumeConfiguration.validateConfiguration(FlumeConfiguration.java:<span style="color:#800080;">141</span>)] Post-validation flume configuration contains configuration <span style="color:#0000ff;">for</span><span style="color:#000000;"> agents: [a1]
</span><span style="color:#008080;">14</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">158</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:<span style="color:#800080;">145</span><span style="color:#000000;">)] Creating channels
</span><span style="color:#008080;">15</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">166</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.channel.DefaultChannelFactory.create(DefaultChannelFactory.java:<span style="color:#800080;">42</span><span style="color:#000000;">)] Creating instance of channel c1 type memory
</span><span style="color:#008080;">16</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">172</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.AbstractConfigurationProvider.loadChannels(AbstractConfigurationProvider.java:<span style="color:#800080;">200</span><span style="color:#000000;">)] Created channel c1
</span><span style="color:#008080;">17</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">174</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.source.DefaultSourceFactory.create(DefaultSourceFactory.java:<span style="color:#800080;">41</span><span style="color:#000000;">)] Creating instance of source r1, type netcat
</span><span style="color:#008080;">18</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">189</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.sink.DefaultSinkFactory.create(DefaultSinkFactory.java:<span style="color:#800080;">42</span><span style="color:#000000;">)] Creating instance of sink: k1, type: logger
</span><span style="color:#008080;">19</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">192</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.AbstractConfigurationProvider.getConfiguration(AbstractConfigurationProvider.java:<span style="color:#800080;">114</span><span style="color:#000000;">)] Channel c1 connected to [r1, k1]
</span><span style="color:#008080;">20</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">200</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:<span style="color:#800080;">138</span>)] Starting <span style="color:#0000ff;">new</span> configuration:{ sourceRunners:{r1=EventDrivenSourceRunner: { source:org.apache.flume.source.NetcatSource{name:r1,state:IDLE} }} sinkRunners:{k1=SinkRunner: { policy:org.apache.flume.sink.DefaultSinkProcessor@1ce79b8 counterGroup:{ name:<span style="color:#0000ff;">null</span> counters:{} } }} channels:{c1=<span style="color:#000000;">org.apache.flume.channel.MemoryChannel{name: c1}} }
</span><span style="color:#008080;">21</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">210</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:<span style="color:#800080;">145</span><span style="color:#000000;">)] Starting Channel c1
</span><span style="color:#008080;">22</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">371</span> (lifecycleSupervisor-<span style="color:#800080;">1</span>-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.register(MonitoredCounterGroup.java:<span style="color:#800080;">120</span>)] Monitored counter group <span style="color:#0000ff;">for</span> type: CHANNEL, name: c1: Successfully registered <span style="color:#0000ff;">new</span><span style="color:#000000;"> MBean.
</span><span style="color:#008080;">23</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">372</span> (lifecycleSupervisor-<span style="color:#800080;">1</span>-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.instrumentation.MonitoredCounterGroup.start(MonitoredCounterGroup.java:<span style="color:#800080;">96</span><span style="color:#000000;">)] Component type: CHANNEL, name: c1 started
</span><span style="color:#008080;">24</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">376</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:<span style="color:#800080;">173</span><span style="color:#000000;">)] Starting Sink k1
</span><span style="color:#008080;">25</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">376</span> (conf-file-poller-<span style="color:#800080;">0</span>) [INFO - org.apache.flume.node.Application.startAllComponents(Application.java:<span style="color:#800080;">184</span><span style="color:#000000;">)] Starting Source r1
</span><span style="color:#008080;">26</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">377</span> (lifecycleSupervisor-<span style="color:#800080;">1</span>-<span style="color:#800080;">3</span>) [INFO - org.apache.flume.source.NetcatSource.start(NetcatSource.java:<span style="color:#800080;">150</span><span style="color:#000000;">)] Source starting
</span><span style="color:#008080;">27</span> <span style="color:#800080;">2017</span>-<span style="color:#800080;">12</span>-<span style="color:#800080;">12</span> <span style="color:#800080;">19</span>:<span style="color:#800080;">59</span>:<span style="color:#800080;">37</span>,<span style="color:#800080;">513</span> (lifecycleSupervisor-<span style="color:#800080;">1</span>-<span style="color:#800080;">3</span>) [INFO - org.apache.flume.source.NetcatSource.start(NetcatSource.java:<span style="color:#800080;">164</span>)] Created serverSocket:sun.nio.ch.ServerSocketChannelImpl[/<span style="color:#800080;">127.0</span>.<span style="color:#800080;">0.1</span>:<span style="color:#800080;">44444</span>]</pre>
</div>
<p><span style="font-size:18px;"> 然后可以向这个端口发送数据，就打印出来了，因为这里输出是在console的：</span></p>
<p><span style="font-size:18px;"> 相当于产生数据的源：[root@master hadoop]# telnetlocalhost 44444<br></span></p>
<blockquote>
<p><span style="font-size:18px;">[root@master hadoop]# telnet localhost 44444<br>
bash: telnet: command not found</span></p>
<p><span style="font-size:18px;">我的机器没有安装<span style="font-size:18px;">telnet </span>
，所以先安装一下<span style="font-size:18px;">telnet ，如下所示</span>：</span></p>
<p><span style="font-size:18px;">第一步：检测telnet-server的rpm包是否安装 ？？？<br>
　　[root@localhost ~]# rpm -qa telnet-server <br>
　　若无输入内容，则表示没有安装。出于安全考虑telnet-server.rpm是默认没有安装的，而telnet的客户端是标配。即下面的软件是默认安装的。</span></p>
<p><span style="font-size:18px;">第二步：若未安装，则安装telnet-server：</span></p>
<p><span style="font-size:18px;">　　 [root@localhost ~]#yum install telnet-server</span></p>
<p><span style="font-size:18px;">第三步：3、检测telnet的rpm包是否安装 ？？？<br>
　　[root@localhost ~]# rpm -qa telnet <br>
　　telnet-0.17-47.el6_3.1.x86_64</span></p>
<p><span style="font-size:18px;">第四步：若未安装，则安装telnet：</span></p>
<p><span style="font-size:18px;">　　[root@localhost ~]# yum install telnet</span></p>
<p id="二重新启动xinetd守护进程"><span style="font-size:18px;">第五步：重新启动xinetd守护进程？？？</span></p>
<p><span style="font-size:18px;">　　由于telnet服务也是由xinetd守护的，所以安装完telnet-server，要启动telnet服务就必须重新启动xinetd
</span><br><span style="font-size:18px;">　　[root@locahost ~]#service xinetd restart </span></p>
<p><span style="font-size:18px;">完成以上步骤以后可以开始你的命令，如我的：</span></p>
<p><span style="font-size:18px;">　　[root@master hadoop]# telnet localhost 44444<br>
　　Trying ::1...<br>
　　telnet: connect to address ::1: Connection refused<br>
　　Trying 127.0.0.1...<br>
　　Connected to localhost.<br>
　　Escape character is '^]'.<br></span></p>
</blockquote>
<p><span style="font-size:18px;">解决完上面的错误以后就可以开始测试telnet数据源发送和<span style="font-size:18px;">flume的接受</span>：</span></p>
<p><span style="font-size:18px;">测试，先要往agent采集监听的端口上发送数据，让agent有数据可采集</span><span style="font-size:18px;">，</span><span style="font-size:18px;">随便在一个能跟agent节点联网的机器上：telnet localhost 44444</span></p>
<p> <img src="http://images2017.cnblogs.com/blog/1002211/201712/1002211-20171213130818754-101750151.png" alt=""></p>
<p><span style="font-size:18px;">然后可以看到flume已经接受到了数据：</span></p>
<p><img src="http://images2017.cnblogs.com/blog/1002211/201712/1002211-20171213130846754-308434955.png" alt=""></p>
<p><span style="font-size:18px;">如何退出telnet呢？？？</span></p>
<p><span style="font-size:18px;">　　首先按ctrl+]退出到<span style="font-size:18px;">telnet
</span>&gt; ，然后输入telnet&gt; quit即可退出，记住，quit后面不要加；</span></p>
<p><span style="font-size:18px;"> 5：flume监视文件夹案例：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;"> 1</span> <span style="color:#000000;">监视文件夹
</span><span style="color:#008080;"> 2</span> 
<span style="color:#008080;"> 3</span> 
<span style="color:#008080;"> 4</span> <span style="color:#ff0000;">第一步：
</span><span style="color:#008080;"> 5</span> 首先 在flume的conf的目录下创建文件名称为：vim spool-<span style="color:#000000;">logger.conf的文件。
</span><span style="color:#008080;"> 6</span> <span style="color:#000000;">将下面的内容复制到这个文件里面。
</span><span style="color:#008080;"> 7</span> 
<span style="color:#008080;"> 8</span> # Name the components on <span style="color:#0000ff;">this</span><span style="color:#000000;"> agent
</span><span style="color:#008080;"> 9</span> a1.sources =<span style="color:#000000;"> r1
</span><span style="color:#008080;">10</span> a1.sinks =<span style="color:#000000;"> k1
</span><span style="color:#008080;">11</span> a1.channels =<span style="color:#000000;"> c1
</span><span style="color:#008080;">12</span> 
<span style="color:#008080;">13</span> # Describe/<span style="color:#000000;">configure the source
</span><span style="color:#008080;">14</span> <span style="color:#000000;">#监听目录,spoolDir指定目录, fileHeader要不要给文件夹前坠名
</span><span style="color:#008080;">15</span> a1.sources.r1.type =<span style="color:#000000;"> spooldir
</span><span style="color:#008080;">16</span> a1.sources.r1.spoolDir = /home/hadoop/<span style="color:#000000;">flumespool
</span><span style="color:#008080;">17</span> a1.sources.r1.fileHeader = <span style="color:#0000ff;">true</span>
<span style="color:#008080;">18</span> 
<span style="color:#008080;">19</span> <span style="color:#000000;"># Describe the sink
</span><span style="color:#008080;">20</span> a1.sinks.k1.type =<span style="color:#000000;"> logger
</span><span style="color:#008080;">21</span> 
<span style="color:#008080;">22</span> # Use a channel which buffers events <span style="color:#0000ff;">in</span><span style="color:#000000;"> memory
</span><span style="color:#008080;">23</span> a1.channels.c1.type =<span style="color:#000000;"> memory
</span><span style="color:#008080;">24</span> a1.channels.c1.capacity = <span style="color:#800080;">1000</span>
<span style="color:#008080;">25</span> a1.channels.c1.transactionCapacity = <span style="color:#800080;">100</span>
<span style="color:#008080;">26</span> 
<span style="color:#008080;">27</span> <span style="color:#000000;"># Bind the source and sink to the channel
</span><span style="color:#008080;">28</span> a1.sources.r1.channels =<span style="color:#000000;"> c1
</span><span style="color:#008080;">29</span> a1.sinks.k1.channel =<span style="color:#000000;"> c1
</span><span style="color:#008080;">30</span> 
<span style="color:#008080;">31</span> <span style="color:#ff0000;">第二步：</span>根据a1.sources.r1.spoolDir = /home/hadoop/<span style="color:#000000;">flumespool配置的文件路径，创建相应的目录。<span style="color:#ff0000;">必须先创建对应的目录，不然报错。java.lang.IllegalStateException: Directory does not exist: /home/hadoop/flumespool
</span></span><span style="color:#008080;">32</span> [root@master conf]# mkdir  /home/hadoop/<span style="color:#000000;">flumespool
</span><span style="color:#008080;">33</span> 
<span style="color:#008080;">34</span> <span style="color:#000000;"><span style="color:#ff0000;">第三步：</span>启动命令：  
</span><span style="color:#008080;">35</span> bin/flume-ng agent -c ./conf -f ./conf/spool-logger.conf -n a1 -Dflume.root.logger=<span style="color:#000000;">INFO,console
</span><span style="color:#008080;">36</span> 
<span style="color:#008080;">37</span> <span style="color:#000000;"><span style="color:#ff0000;">第四步:</span>测试： 
</span><span style="color:#008080;">38</span> 往/home/hadoop/flumeSpool放文件（mv ././xxxFile /home/hadoop/flumeSpool），但是不要在里面生成文件</pre>
</div>
<p><span style="font-size:18px;">6：采集目录到HDFS案例：</span></p>
<blockquote>
<p><span style="font-size:18px;">（1）采集需求：某服务器的某特定目录下，会不断产生新的文件，每当有新文件出现，就需要把文件采集到HDFS中去<br>
（2）根据需求，首先定义以下3大要素<br>
　　a）：采集源，即source——监控文件目录 :  spooldir<br>
　　b）：下沉目标，即sink——HDFS文件系统  :  hdfs sink<br>
　　c）：source和sink之间的传递通道——channel，可用file channel 也可以用内存channel<br>
（3）：</span><span style="font-size:18px;">Channel参数解释：</span></p>
<p><span style="font-size:18px;">　　capacity：默认该通道中最大的可以存储的event数量；</span></p>
<p><span style="font-size:18px;">　　trasactionCapacity：每次最大可以从source中拿到或者送到sink中的event数量；</span></p>
<p><span style="font-size:18px;">　　keep-alive：event添加到通道中或者移出的允许时间；</span></p>
</blockquote>
<p><span style="font-size:18px;">配置文件编写：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;"> 1</span> <span style="color:#000000;">#定义三大组件的名称
</span><span style="color:#008080;"> 2</span> agent1.sources =<span style="color:#000000;"> source1
</span><span style="color:#008080;"> 3</span> agent1.sinks =<span style="color:#000000;"> sink1
</span><span style="color:#008080;"> 4</span> agent1.channels =<span style="color:#000000;"> channel1
</span><span style="color:#008080;"> 5</span> 
<span style="color:#008080;"> 6</span> <span style="color:#000000;"># 配置source组件
</span><span style="color:#008080;"> 7</span> agent1.sources.source1.type =<span style="color:#000000;"> spooldir
</span><span style="color:#008080;"> 8</span> agent1.sources.source1.spoolDir = /home/hadoop/logs/
<span style="color:#008080;"> 9</span> agent1.sources.source1.fileHeader = <span style="color:#0000ff;">false</span>
<span style="color:#008080;">10</span> 
<span style="color:#008080;">11</span> <span style="color:#000000;">#配置拦截器
</span><span style="color:#008080;">12</span> agent1.sources.source1.interceptors =<span style="color:#000000;"> i1
</span><span style="color:#008080;">13</span> agent1.sources.source1.interceptors.i1.type =<span style="color:#000000;"> host
</span><span style="color:#008080;">14</span> agent1.sources.source1.interceptors.i1.hostHeader =<span style="color:#000000;"> hostname
</span><span style="color:#008080;">15</span> 
<span style="color:#008080;">16</span> <span style="color:#000000;"># 配置sink组件
</span><span style="color:#008080;">17</span> agent1.sinks.sink1.type =<span style="color:#000000;"> hdfs
</span><span style="color:#008080;">18</span> agent1.sinks.sink1.hdfs.path =hdfs:<span style="color:#008000;">//</span><span style="color:#008000;">master:9000/weblog/flume-collection/%y-%m-%d/%H-%M</span>
<span style="color:#008080;">19</span> agent1.sinks.sink1.hdfs.filePrefix =<span style="color:#000000;"> access_log
</span><span style="color:#008080;">20</span> agent1.sinks.sink1.hdfs.maxOpenFiles = <span style="color:#800080;">5000</span>
<span style="color:#008080;">21</span> agent1.sinks.sink1.hdfs.batchSize= <span style="color:#800080;">100</span>
<span style="color:#008080;">22</span> agent1.sinks.sink1.hdfs.fileType =<span style="color:#000000;"> DataStream
</span><span style="color:#008080;">23</span> agent1.sinks.sink1.hdfs.writeFormat =<span style="color:#000000;">Text
</span><span style="color:#008080;">24</span> agent1.sinks.sink1.hdfs.rollSize = <span style="color:#800080;">102400</span>
<span style="color:#008080;">25</span> agent1.sinks.sink1.hdfs.rollCount = <span style="color:#800080;">1000000</span>
<span style="color:#008080;">26</span> agent1.sinks.sink1.hdfs.rollInterval = <span style="color:#800080;">60</span>
<span style="color:#008080;">27</span> #agent1.sinks.sink1.hdfs.round = <span style="color:#0000ff;">true</span>
<span style="color:#008080;">28</span> #agent1.sinks.sink1.hdfs.roundValue = <span style="color:#800080;">10</span>
<span style="color:#008080;">29</span> #agent1.sinks.sink1.hdfs.roundUnit =<span style="color:#000000;"> minute
</span><span style="color:#008080;">30</span> agent1.sinks.sink1.hdfs.useLocalTimeStamp = <span style="color:#0000ff;">true</span>
<span style="color:#008080;">31</span> # Use a channel which buffers events <span style="color:#0000ff;">in</span><span style="color:#000000;"> memory
</span><span style="color:#008080;">32</span> agent1.channels.channel1.type =<span style="color:#000000;"> memory
</span><span style="color:#008080;">33</span> agent1.channels.channel1.keep-alive = <span style="color:#800080;">120</span>
<span style="color:#008080;">34</span> agent1.channels.channel1.capacity = <span style="color:#800080;">500000</span>
<span style="color:#008080;">35</span> agent1.channels.channel1.transactionCapacity = <span style="color:#800080;">600</span>
<span style="color:#008080;">36</span> 
<span style="color:#008080;">37</span> <span style="color:#000000;"># Bind the source and sink to the channel
</span><span style="color:#008080;">38</span> agent1.sources.source1.channels =<span style="color:#000000;"> channel1
</span><span style="color:#008080;">39</span> agent1.sinks.sink1.channel = channel1</pre>
</div>
<p> <span style="font-size:18px;">7：采集文件到HDFS案例：</span></p>
<blockquote>
<p><span style="font-size:18px;">（1）：采集需求：比如业务系统使用log4j生成的日志，日志内容不断增加，需要把追加到日志文件中的数据实时采集到<span style="font-family:Calibri;">hdfs</span></span></p>
<p><span style="font-size:18px;">（2）：根据需求，首先定义以下3大要素</span></p>
<p class="15"><span style="font-size:18px;">　　采集源，即source——监控文件内容更新 <span style="font-family:Calibri;">
:  exec  </span>‘tail -F file’</span></p>
<p class="15"><span style="font-size:18px;">　　下沉目标，即sink——<span style="font-family:Calibri;">HDFS</span>文件系统  <span style="font-family:Calibri;">:  hdfs sink</span></span></p>
<p class="15"><span style="font-size:18px;">　　Source和<span style="font-family:Calibri;">sink</span>之间的传递通道——<span style="font-family:Calibri;">channel</span>，可用<span style="font-family:Calibri;">file channel
</span>也可以用 内存<span style="font-family:Calibri;">channel</span></span></p>
</blockquote>
<p><span style="font-size:18px;">配置文件编写：</span></p>
<div class="cnblogs_code">
<pre><span style="color:#008080;"> 1</span> agent1.sources =<span style="color:#000000;"> source1
</span><span style="color:#008080;"> 2</span> agent1.sinks =<span style="color:#000000;"> sink1
</span><span style="color:#008080;"> 3</span> agent1.channels =<span style="color:#000000;"> channel1
</span><span style="color:#008080;"> 4</span> 
<span style="color:#008080;"> 5</span> # Describe/configure tail -<span style="color:#000000;">F source1
</span><span style="color:#008080;"> 6</span> agent1.sources.source1.type =<span style="color:#000000;"> exec
</span><span style="color:#008080;"> 7</span> agent1.sources.source1.command = tail -F /home/hadoop/logs/<span style="color:#000000;">access_log
</span><span style="color:#008080;"> 8</span> agent1.sources.source1.channels =<span style="color:#000000;"> channel1
</span><span style="color:#008080;"> 9</span> 
<span style="color:#008080;">10</span> #configure host <span style="color:#0000ff;">for</span><span style="color:#000000;"> source
</span><span style="color:#008080;">11</span> agent1.sources.source1.interceptors =<span style="color:#000000;"> i1
</span><span style="color:#008080;">12</span> agent1.sources.source1.interceptors.i1.type =<span style="color:#000000;"> host
</span><span style="color:#008080;">13</span> agent1.sources.source1.interceptors.i1.hostHeader =<span style="color:#000000;"> hostname
</span><span style="color:#008080;">14</span> 
<span style="color:#008080;">15</span> <span style="color:#000000;"># Describe sink1
</span><span style="color:#008080;">16</span> agent1.sinks.sink1.type =<span style="color:#000000;"> hdfs
</span><span style="color:#008080;">17</span> #a1.sinks.k1.channel =<span style="color:#000000;"> c1
</span><span style="color:#008080;">18</span> agent1.sinks.sink1.hdfs.path =hdfs:<span style="color:#008000;">//master</span><span style="color:#008000;">:9000/weblog/flume-collection/%y-%m-%d/%H-%M</span>
<span style="color:#008080;">19</span> agent1.sinks.sink1.hdfs.filePrefix =<span style="color:#000000;"> access_log
</span><span style="color:#008080;">20</span> agent1.sinks.sink1.hdfs.maxOpenFiles = <span style="color:#800080;">5000</span>
<span style="color:#008080;">21</span> agent1.sinks.sink1.hdfs.batchSize= <span style="color:#800080;">100</span>
<span style="color:#008080;">22</span> agent1.sinks.sink1.hdfs.fileType =<span style="color:#000000;"> DataStream
</span><span style="color:#008080;">23</span> agent1.sinks.sink1.hdfs.writeFormat =<span style="color:#000000;">Text
</span><span style="color:#008080;">24</span> agent1.sinks.sink1.hdfs.rollSize = <span style="color:#800080;">102400</span>
<span style="color:#008080;">25</span> agent1.sinks.sink1.hdfs.rollCount = <span style="color:#800080;">1000000</span>
<span style="color:#008080;">26</span> agent1.sinks.sink1.hdfs.rollInterval = <span style="color:#800080;">60</span>
<span style="color:#008080;">27</span> agent1.sinks.sink1.hdfs.round = <span style="color:#0000ff;">true</span>
<span style="color:#008080;">28</span> agent1.sinks.sink1.hdfs.roundValue = <span style="color:#800080;">10</span>
<span style="color:#008080;">29</span> agent1.sinks.sink1.hdfs.roundUnit =<span style="color:#000000;"> minute
</span><span style="color:#008080;">30</span> agent1.sinks.sink1.hdfs.useLocalTimeStamp = <span style="color:#0000ff;">true</span>
<span style="color:#008080;">31</span> 
<span style="color:#008080;">32</span> # Use a channel which buffers events <span style="color:#0000ff;">in</span><span style="color:#000000;"> memory
</span><span style="color:#008080;">33</span> agent1.channels.channel1.type =<span style="color:#000000;"> memory
</span><span style="color:#008080;">34</span> agent1.channels.channel1.keep-alive = <span style="color:#800080;">120</span>
<span style="color:#008080;">35</span> agent1.channels.channel1.capacity = <span style="color:#800080;">500000</span>
<span style="color:#008080;">36</span> agent1.channels.channel1.transactionCapacity = <span style="color:#800080;">600</span>
<span style="color:#008080;">37</span> 
<span style="color:#008080;">38</span> <span style="color:#000000;"># Bind the source and sink to the channel
</span><span style="color:#008080;">39</span> agent1.sources.source1.channels =<span style="color:#000000;"> channel1
</span><span style="color:#008080;">40</span> agent1.sinks.sink1.channel = channel1</pre>
</div>
<p> 待续......</p>
            </div>
                </div>
---
layout:     post
title:      整体认识flume：Flume介绍、分布式安装、常见问题及解决方案
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<table cellspacing="0" cellpadding="0" style="border-collapse:collapse;table-layout:fixed;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:12px;"><tbody><tr><td class="t_f" id="postmessage_42409" style="font-size:14px;">
<span style="font-weight:700;"><br><span style="font-size:12px;">Flume是一个分布式、可靠、和高可用的海量日志采集、聚合和传输的系统，支持在系统中定制各类数据发送方，用于收集数据；同时，Flume提供对数据进行简单处理，并写到各种数据接受方（可定制）的能力。 </span><br><br><span style="font-size:12px;">Flume的逻辑架构：</span><br><br></span>
<p style="line-height:30px;text-indent:2em;">
<img id="aimg_6885" src="http://www.aboutyun.com/data/attachment/forum/201409/01/142905ohii5hdiur5pf495.png" class="zoom" width="293" alt=""></p>
<p>
</p>
<br><div align="left"><span style="font-weight:700;">Flume逻辑上分三层架构：</span></div>
<div align="left">agent，collector，storage</div>
<div align="left"><br></div>
<div align="left"><span style="font-weight:700;">agent</span></div>
<div align="left">用于采集数据，agent是flume中产生数据流的地方，同时，agent会将产生的数据流传输到collector。</div>
<div align="left"><span style="font-weight:700;">collector</span></div>
<div align="left">collector的作用是将多个agent的数据汇总后，加载到storage中。</div>
<div align="left"><span style="font-weight:700;">storage</span></div>
<div align="left">storage是存储系统，可以是一个普通file，也可以是HDFS，HIVE，HBase等。</div>
<br><div align="left"><span style="font-weight:700;">Master</span></div>
<div align="left">Master是管理协调agent和collector的配置等信息，是flume集群的控制器。</div>
<div align="left">在Flume中，最重要的抽象是data flow（数据流），data flow描述了数据从产生，传输、处理并最终写入目标的一条路径。</div>
<br><div align="left"><img id="aimg_6886" src="http://www.aboutyun.com/data/attachment/forum/201409/01/142943mea2r5fcqwzg7f97.png" class="zoom" width="475" alt=""> <br></div>
<br><div align="left">对于agent数据流配置就是从哪得到数据，把数据发送到哪个collector。</div>
<div align="left">对于collector是接收agent发过来的数据，把数据发送到指定的目标机器上。</div>
<div align="left"></div>
<div align="left"><span style="font-weight:700;">Flume的特性</span></div>
<div align="left">•         Reliability：Flume提供3中数据可靠性选项，包括End-to-end、Store on failure和Best effort。其中End-to-end使用了磁盘日志和接受端Ack的方式，保证Flume接受到的数据会最终到达目的，但是效率是最差的。Store on failure在目的不可用的时候，数据会保持在本地硬盘，效率会比end-to-end高，但是会出现日志丢失的情况。Best
 effort不做任何QoS保证，效率最高，日志记录没有保证。</div>
<br><div align="left">•         Scalability：Flume的3大组件：collector、master和storage tier都是可伸缩的。需要注意的是，Flume中对事件的处理不需要带状态，它的Scalability可以很容易实现。</div>
<br><div align="left">•         Manageability：master能够动态管理flume集群节点，多master情况，Flume利用ZooKeeper和gossip，保证配置数据的一致性。</div>
<br><div align="left">•         Extensibility：基于Java，用户可以为Flume添加各种新的功能，如通过继承Source，用户可以实现自己的数据接入方式，实现Sink的子类，用户可以将数据写往特定目标，同时，通过SinkDecorator，用户可以对数据进行一定的预处理。</div>
<br><div align="left">注：Flume框架对hadoop和zookeeper的依赖只是在jar包上，并不要求flume启动时必须将hadoop和zookeeper服务也启动。</div>
<br><div align="left"><br><img id="aimg_g28hv" class="zoom" border="0" alt="" src="http://www.aboutyun.com/static/image/hrline/2.gif" width="485" height="21"><br><br><br></div>
<span style="font-weight:700;">Flume的分布式安装</span>
<div align="left"><br></div>
<div align="left">                                        ————此为目前集群的flume安装过程</div>
<div align="left">部署flume在集群上，按照如下步骤：</div>
<div align="left">    在集群上的每台机器上安装flume</div>
<div align="left">    选择一个或多个节点当做master</div>
<div align="left">    修改静态配置文件</div>
<div align="left">    在至少一台机器上启动一个master ，所有节点启动flume node</div>
<div align="left">接下来的一章描述如何手动修改配置文件为集群上的节点指定master，如何为参数设置默认值，本章的后一部分描述对于大系统的数据流配置，如何通过增加collector来扩充系统容量，如何提高可靠性通过增加更多的master。注意：flume集群整个集群的网络环境要保证稳定，可靠，否则会出现一些莫名错误（比如：agent端发送不了数据到collector）。</div>
<br><span style="font-weight:700;">集群每台机器上安装flume</span><br><div align="left">场景：</div>
<div align="left">操作系统版本：CentOS5.6</div>
<div align="left">Hadoop版本：0.20.2</div>
<div align="left">Jdk版本：jdk1.6.0_26</div>
<div align="left">安装flume版本：flume-0.9.4</div>
<br><div align="left">步骤1：</div>
<div align="left">下载flume最新版本，现在服务器上安装的是flume-distribution-0.9.4的版本，下载地址是<a href="https://github.com/cloudera/flume/downloads" rel="nofollow" style="color:rgb(51,102,153);">https://github.com/cloudera/flume/downloads</a> 目前flume的安装包是放在/data/sysdir/install_tar文件件的下面。</div>
<br><div align="left">步骤2：</div>
<div align="left">解压flume安装包到/data/sysdir文件夹下面，</div>
<div align="left">在命令行中输入 tar zxvf/data/sysdir/install_tar/flume-distributin-0.9.4.tar.gz -C /data/sysdir</div>
<br><div align="left">步骤3：</div>
<div align="left">修改etc/profile文件，加入：</div>
<div align="left">export FLUME_HOME=/data/sysdir/flume-distribution-0.9.4</div>
<div align="left">export PATH=.:$PATH::$FLUME_HOME/bin</div>
<div align="left"><br></div>
<div align="left">步骤4：</div>
<div align="left">验证安装及其他</div>
<div align="left">安装完毕后，运行flume命令，会看到以下输出：</div>
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;"><tbody><tr><td width="533" style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
  
<div align="left">usage:  flume command [args...]</div>
  
<div align="left">commands  include:</div>
  
<div align="left">  dump            Takes a specified source and  dumps to console</div>
  
<div align="left">  node            Start a Flume node/agent (with  watchdog)</div>
  
<div align="left">  master          Start a Flume Master server (with  watchdog)</div>
  
<div align="left">  version         Dump flume build version information</div>
  
<div align="left">  node_nowatch    Start a flume node/agent (no watchdog)</div>
  
<div align="left">  master_nowatch  Start a Flume Master server (no watchdog)</div>
  
<div align="left">  class &lt;class&gt;   Run specified fully qualified class using  Flume environment (no watchdog)</div>
  
<div align="left">                   for example: flume  com.cloudera.flume.agent.FlumeNode</div>
  
<div align="left">  shell           Start the flume shell</div>
  
<div align="left">  killmaster       Kill a running master</div>
  </td>
</tr></tbody></table><div align="left"></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<div align="left"><br></div>
<br><div align="left"><br></div>
<div align="left">flume配置文件位置：$FLUME_HOME/conf 下</div>
<br><span style="font-weight:700;">选择一个或多个节点当做master</span>
<div align="left">对于master的选择情况，可以在集群上定义一个master，也可以为了提高可用性选择多个节点做为master,</div>
<div align="left">单点master模式：容易管理，但在系统的容错和扩展性有缺陷</div>
<div align="left">多点master模式：通常是运行3/5个master，能很好的容错</div>
<div align="left">原文如下：（<a href="http://archive.cloudera.com/cdh/3/flume/UserGuide/" rel="nofollow" style="color:rgb(51,102,153);">http://archive.cloudera.com/cdh/3/flume/UserGuide/</a>）</div>
<div align="left">Standalone mode - this is where the Master runs ona single machine. This is easy to administer, and simple to set-up, but hasdisadvantages when it comes to scalability and fault-tolerance.</div>
<div align="left">Distributed mode - this is where the Master isconfigured to run on several machines - usually three or five. This optionscales to serve many Flows, and also has good fault-toleranceproperties.</div>
<div align="left">Flume master数量的选择原则</div>
<br><div align="left">原文如下：</div>
<div align="left">The distributed Flume Master will continue to workcorrectly as long as more than half the physical machines running it are stillworking and haven’t crashed. Therefore if you want to survive one fault, youneed three
 machines (because 3-1 = 2 &gt; 3/2). For every extra fault you wantto tolerate, add another two machines, so for two faults you need fivemachines. Note that having an even number of machines doesn’t make the FlumeMaster any more fault-tolerant - four machines
 only tolerate one failure,because if two were to fail only two would be left functioning, which is notmore than half of four. Common deployments should be well served by three orfive machines.</div>
<div align="left"><br></div>
<div align="left">分布式的master能够继续正常工作不会崩溃，的前提是正常工作的master数量超过总master数量的一半。</div>
<div align="left">Flume master的作用主要有两个：</div>
<br><div align="left">原文如下：</div>
<div align="left">The Master has two main jobs to perform. The firstis to keep track of all the nodes in a Flume deployment and to keep theminformed of any changes to their configuration. The second is to trackacknowledgements from
 the end of a Flume flow that is operating in reliablemode so that the source at the top of that flow knows when to stop transmittingan event.</div>
<div align="left"><br></div>
<div align="left">Master主要有两个工作，第一是跟踪各节点的配置情况，通知节点配置的改变，第二是跟踪来自flow的结尾操控在可靠的模式下（E2E）的信息，以至于让flow的源头知道什么时候停止传输event。</div>
<div align="left">目前集群flume master的选择情况</div>
<div align="left">10.168.0.174、10.168.0.181、10.168.0.188为flume master</div>
<div align="left"></div>
<div align="left"></div>
<span style="font-weight:700;">修改静态配置文件</span>
<div align="left">Site-specific设置对于flume节点和master通过在每一个集群节点的conf/flume-site.xml是可配置的，如果这个文件不存在，设置的属性默认的在conf/flume--conf.xml中，在接下来的例子中，在flume的节点上设置master名，让节点自己去寻找叫“master”的flume Master</div>
<div align="left">conf/flume-conf.xml.  </div>
<div align="left">
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_sbb">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;?xml version="1.0"?&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;?xml-stylesheet type="text/xsl"  href="configuration.xsl"?&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;configuration&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;property&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;name&gt;flume.master.servers&lt;/name&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;value&gt;master&lt;/value&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;/property&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;/configuration&gt;</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br><br></div>
<div align="left">在多master的情况下需要如下配置：</div>
<div align="left">
<div class="blockcode" style="overflow:hidden;color:rgb(102,102,102);border:1px solid rgb(204,204,204);">
<div id="code_ZXV">
<ol><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
&lt;property&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;name&gt;flume.master.servers&lt;/name&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;value&gt;hadoopmaster.com,hadoopedge.com,datanode4.com&lt;/value&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;description&gt;A comma-separated list of hostnames, one for each<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      machine in the Flume Master.<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;/description&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  &lt;/property&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  &lt;property&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;name&gt;flume.master.store&lt;/name&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;value&gt;zookeeper&lt;/value&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;description&gt;How the Flume Master stores node configurations. Must<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      be either 'zookeeper' or 'memory'.&lt;/description&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  &lt;/property&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  &lt;property&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;name&gt;flume.master.serverid&lt;/name&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;value&gt;2&lt;/value&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
    &lt;description&gt;The unique identifier for a machine in a<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      Flume Master ensemble. Must be different on every<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
      master instance.&lt;/description&gt;<br></li><li style="font-family:Monaco, Consolas, 'Lucida Console', 'Courier New', serif;font-size:12px;line-height:1.8em;">
  &lt;/property&gt;</li></ol></div>
<span style="margin-left:43px;font-size:12px;color:rgb(51,102,153) !important;">复制代码</span></div>
<br><br></div>
<div align="left">注意：flume.master.serverid属性的配置主要是针对master，如在集群上目前是181,174,188为master，这三台机器的$FLUME_HOME/conf/flume-conf.xml文件中flume.master.serverid必须是不能相同的，该属性的值以0开始。</div>
<div align="left">当使用agent角色时，你可以通过添加下面的配置文件在flume-conf.xml中，来设置默认的collector主机：</div>
<div align="left">...</div>
<div align="left">&lt;property&gt;</div>
<div align="left">&lt;name&gt;flume.collector.event.host&lt;/name&gt;</div>
<div align="left">&lt;value&gt;collector&lt;/value&gt;</div>
<div align="left">&lt;description&gt;This is the host name of thedefault "remote"  collector.</div>
<div align="left">&lt;/description&gt;</div>
<div align="left">&lt;/property&gt;</div>
<div align="left">&lt;property&gt;</div>
<div align="left">&lt;name&gt;flume.collector.port&lt;/name&gt;</div>
<div align="left">&lt;value&gt;35853&lt;/value&gt;</div>
<div align="left">&lt;description&gt;This default tcp port that thecollector listens to in order to receive events it is collecting.</div>
<div align="left">&lt;/description&gt;</div>
<div align="left">&lt;/property&gt;</div>
<div align="left">...</div>
<div align="left">修改配置文件时，根据物理主机在不同的flume逻辑层，修改相应的属性的值即可。</div>
<div align="left">在agent上，需要修改flume.collector.event.host属性，来指定此agent默认发送到的collector</div>
<div align="left">在cllector上，不需要修改特定的属性</div>
<div align="left">在master上，flume.master.serverid属性的配置主要是针对master，如在集群上目前是181,174,188为master，这三台机器的$FLUME_HOME/conf/flume-conf.xml文件中flume.master.serverid必须是不能相同的，该属性的值以0开始。</div>
<div align="left">为简便起见，其他属性保持一致。</div>
<br><div align="left">关于配置可参见：<a href="http://www.cnblogs.com/zhangmiao-chp/archive/2011/05/18/2050443.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.cnblogs.com/zhangmiao-chp/archive/2011/05/18/2050443.html</a>。</div>
<br><div align="left">其中目前集群调整的配置文件属性：</div>
<div align="left">flume.agent.logdir 该属性是配置agent临时存放文件的路径,尽可能不要放到/tmp下</div>
<div align="left">flume.agent.logdir.maxage该属性是agent日志文件收集信息的时长，根据集群情况可适当调整。</div>
<div align="left">flume.collector.roll.millis该属性hdfs文件切换(关闭后新建)的时长，控制在hdfs上生成的文件大小，如果调整该属性需要同时调整flume.agent.logdir.retransmit的属性，一般情况下最多是flume.agent.logdir.retransmit的一半。</div>
<div align="left">flume.collector.output.format该属性是collector发送数据格式avro, avrojson(默认), avrodata…，目前集群是raw</div>
<div align="left"><br></div>
<span style="font-weight:700;">启动Flume集群节点</span>
<div align="left">集群上节点启动：</div>
<div align="left">1        在命令行输入：flume master 启动master节点</div>
<div align="left">2        在命令行输入：flume node –n nodeName 启动其他节点，nodeName最好根据集群逻辑的划分来取名子，这样在master进行配置的时候比较清晰。</div>
<div align="left">如目前集群上的181 和188 是collector 所以nodeName可以选择collector1 collector2</div>
<div align="left">名字规则自己定义，方便记忆和动态配置即可（后续会有介绍动态配置）</div>
<br><br><span style="font-weight:700;">Flume集群动态配置</span>
<div align="left">    Flume的动态配置指的是在flume shell下进行配置agent和collector，具体步骤如下：</div>
<div align="left">    在已经启动的master节点新开窗口输入 依次输入”flume shell”è”connect localhost ”此时可以进行动态的配置。</div>
<br><div align="left">如执行 exec config a1 ‘tailDir(“/data/logfile”)’ ‘agentSink’</div>
<div align="left">表示nodeName 是a1的机器监听/data/logfile文件夹下的日志，发送信息到flume-conf.xml配置的flume.collector.event.host 的flume.collector.port端口。</div>
<div align="left">如执行配置collector的命令： exec configc1 ‘collecotrSource’ ‘collectorSink(“hdfs://hadoopmaster.com:9000/flume/webdata/%Y-%m-%d/%H”,”syslog”)</div>
<div align="left"><br></div>
<div align="left">表示nodeName是c1的机器监听配置文件中flume.collector.port的端口，发送信息到hdfs上</div>
<div align="left">其中/%Y-%m-%d/%H表示/年-月-日/时 建立发送文件到达的文件夹，这个时间以agent读取日志时产生的文件时间为准，syslog是文件名，创建文件的时间是flume.collector.roll.millis属性决定。</div>
<br><div align="left">其他command shell 参见<a href="http://www.cnblogs.com/zhangmiao-chp/archive/2011/05/18/2050461.html" rel="nofollow" style="color:rgb(51,102,153);">http://www.cnblogs.com/zhangmiao-chp/archive/2011/05/18/2050461.html</a></div>
<div align="left">接下来的例子是 六个agent 一个collector</div>
<div align="left"><br></div>
<div align="left"><img id="aimg_6887" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144408hn6w11qhy6yquvzy.png" class="zoom" width="435" alt=""> <br></div>
<br><div align="left"><span style="font-weight:700;">显式的配置：</span></div>
<div align="left">agentA : src |agentSink("collector",35853);</div>
<div align="left">agentB : src |agentSink("collector",35853);</div>
<div align="left">agentC : src |agentSink("collector",35853);</div>
<div align="left">agentD : src |agentSink("collector",35853);</div>
<div align="left">agentE : src |agentSink("collector",35853);</div>
<div align="left">agentF : src |agentSink("collector",35853);</div>
<div align="left">collector : collectorSource(35853) |collectorSink("hdfs://namenode/flume/","srcdata");</div>
<div align="left"><br></div>
<div align="left"><span style="font-weight:700;">可靠性模式</span></div>
<div align="left">你可以运行agent的可靠性级别，简单的配置不同的agentSink就可以，下面有三个级别的配置</div>
<div align="left">agentE2ESink[("machine"[,port])]</div>
<div align="left">    end to end，这个级别是WAL，relies on an acknowledgement, and willretry if no acknowledgement is received.</div>
<div align="left">agentDFOSink[("machine"[,port])]</div>
<div align="left">    DFO，当agent发现在collector操作失败的时候，agent写入到本地硬盘上，如果出现存储在硬盘上的数据，agent重新进行网络连接，并试图重新发送数据。</div>
<div align="left">agentBESink[("machine"[,port])]</div>
<div align="left">    效率最好，agent不写入到本地任何数据，如果在collector 发现处理失败，直接删除消息。</div>
<div align="left">AgentSink 是agentE2ESink 的别名</div>
<div align="left"></div>
<span style="font-weight:700;">补充说明</span>
<div align="left">多个collector能够增加日志收集的吞吐量，提高collector的有效性能够提高数据的传输速度，数据的收集是可并行的，此外，来自多个agent的数据能够分配到多个collector上加载。</div>
<br><div align="left"><span style="font-weight:700;">多collector下agent的划分</span></div>
<div align="left">前面的图展示flume节点典型的拓扑结构和数据流，为了可靠的传输，当collector停止运行或是失去与agents的联系的时候，agents将会存储他们的events在各自的本地硬盘上，这些agents试图重新连接collector，因为collector的宕机，任何处理和分析的数据流都被阻塞。</div>
<br><div align="left"><img id="aimg_6888" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144458e67d6rwb594ryf2r.png" class="zoom" width="519" alt=""> <br></div>
<br><div align="left">当你有多个collector如上图所示，即使在collector宕机的情况下，数据处理仍然能够进行下去，如果collector b 宕机了，agent a，agent b，ageng e，和agentf会分别继续传送events通过collector a 和collector c，agent c 和agent d 的不得不排在其他agent的后面等待日志的处理直到collector b重新上线。</div>
<div align="left">接下来的配置划分agents在多collector，这个例子是每一个collector由同一个输出的dfs路径和文件的前缀名，聚合所有的日志到同一个目录下</div>
<div align="left">agentA : src |agentE2ESink("collectorA",35853);</div>
<div align="left">agentB : src |agentE2ESink("collectorA",35853);</div>
<div align="left">agentC : src |agentE2ESink("collectorB",35853);</div>
<div align="left">agentD : src |agentE2ESink("collectorB",35853);</div>
<div align="left">agentE : src |agentE2ESink("collectorC",35853);</div>
<div align="left">agentF : src |agentE2ESink("collectorC",35853);</div>
<div align="left">collectorA : collectorSource(35853) |collectorSink("hdfs://...","src");</div>
<div align="left">collectorB : collectorSource(35853) |collectorSink("hdfs://...","src");</div>
<div align="left">collectorC : collectorSource(35853) |collectorSink("hdfs://...","src");</div>
<div align="left"><br></div>
<div align="left"><img id="aimg_6889" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144541myoilyybi6ayyait.png" class="zoom" width="550" alt=""> <br></div>
<br><div align="left">当多个collector写入到相同的存储位置时，你可以设置agent c  agent d 当他们的collector出现错误时转向其他的collector，可以分别设置agent c 和agent d 转移到collectora 和collector c 上。</div>
<br><div align="left">用agents的failover chains。和指向单独的collector（agentSink）类似，failover chains也有三个可靠性的级别agentE2EChain,agentDFOChain, and agentBEChain.</div>
<div align="left">下面的例子，手动设置失败转移链表用agentE2EChain，有多个失败转移collector的agent的end-to-end的可靠性级别，agentA默认的将数据发送到collectorAd端口35853，第二个参数在agentA 's sink是指定备用的collector。你可以定义任意数量的collector，（至少一个以上）。</div>
<br><div align="left">agentA : src |agentE2EChain("collectorA:35853","collectorB:35853");</div>
<div align="left">agentB : src |agentE2EChain("collectorA:35853","collectorC:35853");</div>
<div align="left">agentC : src |agentE2EChain("collectorB:35853","collectorA:35853");</div>
<div align="left">agentD : src | agentE2EChain("collectorB:35853","collectorC:35853");</div>
<div align="left">agentE : src |agentE2EChain("collectorC:35853","collectorA:35853");</div>
<div align="left">agentF : src |agentE2EChain("collectorC:35853","collectorB:35853");</div>
<div align="left">collectorA : collectorSource(35853) |collectorSink("hdfs://...","src");</div>
<div align="left">collectorB : collectorSource(35853) |collectorSink("hdfs://...","src");</div>
<div align="left">collectorC : collectorSource(35853) |collectorSink("hdfs://...","src");</div>
<div align="left">        注意：这章中，agent[A-F] 和 collector[A-B] 是物理节点的名字</div>
<div align="left">        注意：自动失败转移链表功能还不能用在多master的集群。</div>
<br><div align="left"><br><img id="aimg_R5Ie6" class="zoom" border="0" alt="" src="http://www.aboutyun.com/static/image/hrline/2.gif" width="485" height="21"><br><br><br></div>
<div align="left"></div>
<span style="font-weight:700;">Flume集群测试以及节点失败后的处理</span>
<div align="left">Flume集群出现错误的节点按照逻辑划分，分别属于agent，collector，master，这三层的逻辑架构的节点出错可以使单独一个节点出错，也可以是组合出错。</div>
<span style="font-weight:700;">目前集群测试情况</span>
<div align="left">目前集群测试环境：</div>
<div align="left">说明：目前集群的测试主要通过上传文件到hdfs上然后通过hive查询文件的记录条数。</div>
<div align="left">场景一：三台master（174/176,181,188），两台collector(181，188)，一台agent(235)，传送的文件有据说有3000000条记录，agent配置188-181。</div>
<div align="left">Session1：不做任何异常测试，hive建立表是logflumeSession1,结果是：数据条数：300万</div>
<div align="left">Session2：两个collector都宕机，数据条数：3427971</div>
<div align="left"><br></div>
<div align="left"><img id="aimg_6890" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144648luwgpowzcocgip7j.png" class="zoom" width="99" alt=""></div>
<div align="left">Session3：宕机一个cllector，和一个master，然后重新启动collector，master，数据量：</div>
<br><div align="left"><img id="aimg_6891" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144658lyz3b8l9iy80zbz9.png" class="zoom" width="258" alt=""></div>
<div align="left">Session4:宕机agent，数据会重新发送</div>
<div align="left">场景n：三台master（174/176,181,188），一台collector(188)，一台agent(235)，传送的文件有6000000条记录，colletcor和agent不在174节点的master上</div>
<div align="left">出现的状况：174宕机</div>
<div align="left">错误情况：不能正常传送文件到hdfs上，collector报错信息如图</div>
<div align="left"><img id="aimg_6892" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144708t0wwzzopx0c06ul0.png" class="zoom" width="600" alt=""> <br></div>
<br><div align="left">Hdfs无法写入数据</div>
<br><div align="left"><img id="aimg_6893" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144733nvuhvzub2hnztnbc.png" class="zoom" width="600" alt=""> <br></div>
<div align="left"><br></div>
<div align="left">解决办法：重新启动collector</div>
<div align="left">结果：数据正常传输</div>
<div align="left"><img id="aimg_6894" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144747sf611uu221utzidg.png" class="zoom" width="600" alt=""> <br></div>
<div align="left">数据的总行数：</div>
<br><div align="left">
<div style="width:259px;"></div>
<img id="aimg_6895" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144754d37me33mfe3om3cd.png" class="zoom" width="259" alt="" style="width:1px;"></div>
<div align="left">数据会增加。</div>
<div align="left"></div>
<span style="font-weight:700;">单一逻辑层的节点失败及处理</span>
<div align="left">单一逻辑层的节点出错，情况有四种agent，collector，master，storage，storage。 storage层的失败和collector层的失败是一样的，只要数据放不到最终的位置，就认为节点是失败的。</div>
<span style="font-weight:700;">agent失败</span>
<div align="left">Flume数据安全级别的配置主要Agent的配置上，Agent提供三种级别发送数据到collector：E2E、DFO、BF。</div>
<div align="left">E2E：   agent会先将收集到的数据写到agent本地硬盘上，然后发送  collector，collector会将数据发送到最后数据的节点。确定数据已经成功保存到目标机器之后，collector通知agent删除硬盘上文件。</div>
<div align="left">DFO：   当数据无法正常保存到目标主机时，agent会将收集到的数    据保到agent安装时指定的目录下，错误恢复之后，将记录的数据发送到collector，进入目标机器。</div>
<div align="left">BF：    agent不做任何的日志记录，如果目标地址不可达则数据丢失。</div>
<div align="left"></div>
<div align="left">agent节点监控日志文件夹下的所有文件，每一个agent最多监听1024个文件，每一个文件在agent的都会有一个类似游标的东西，记录监听文件读取的位置，这样每次文件有新的记录产生，那么游标就会读取增量记录，根据agent配置发送到collector的安全层级属性有E2E,DFO。如果是E2E的情况那么agent节点会首先把文件写入到agent节点的文件夹下，然后发送给collector，如果最终数据最终成功存储到storage层，那么agent删除之前写入的文件，如果没有收到成功的信息，那么就保留信息。</div>
<div align="left">如果agent节点出现问题，那么相当于所有的记录信息都消失了，如果直接重新启动，agent会认为日志文件夹下的所有文件都是没有监听过的，没有文件记录的标示，所以会重新读取文件，这样，日志就会有重复，具体恢复办法如下</div>
<div align="left">      将agent节点上监听的日志文件夹下已经发送的日志文件移出，处理完</div>
<div align="left">      故障重新启动agent即可。</div>
<div align="left">注：在agent节点失败的情况下，按照失败的时间点，将时间点之前的数据文件移出，将flume.agent.logdir配置的文件夹清空，重新启动agent。</div>
<span style="font-weight:700;">collector失败  </span>
<div align="left">     collector端做数据的合并，并且将数据发送到目标机器上去，如果collector端失败那么不用担心，因为agent的E2E模式会将没有发送成功的数据保存到agent的本地，等到collector恢复之后，数据会重新发送到collector上，数据不会丢失。</div>
<div align="left">除了恢复故障节点之外不用做其他的额外处理，collector的性能影响</div>
<div align="left">      的是整个flume集群的数据吞吐量，所以collector最好单独部署。</div>
<span style="font-weight:700;">master失败</span>
<div align="left">      master宕机，整个集群将不能工作，在重新启动集群，将agent监听的日志文件夹下的所有文件</div>
<div align="left">       移出，然后重新启动master即可。</div>
<div align="left">      在多master节点情况下，只要集群上正常工作的master大于总master数量的一半，集群就能正常工作，那么只要恢复其中宕机的master即可。</div>
<div align="left">      </div>
<span style="font-weight:700;">多逻辑层节点组合失败及处理</span>
<div align="left">两个逻辑层组合出错，情况有agent-collector，collector-master，agent-master，实际上agent</div>
<span style="font-weight:700;">agent-collector组合</span>
<div align="left">    agent和collector组合出现错误，collector和stroage出错一样，姑且将collector和storage放在一起考虑。</div>
<span style="font-weight:700;">collector-master组合</span><span style="font-weight:700;">agent-master组合</span><br><br><br><div align="left"><br><img id="aimg_u3a6z" class="zoom" border="0" alt="" src="http://www.aboutyun.com/static/image/hrline/2.gif" width="485" height="21"><br><br><br></div>
<span style="font-weight:700;">Flume集群遇到的问题</span>
<div align="left">1，  Flume在agent端采集数据的时候默认会在/tmp/flume-{user}下生成临时的目录用于存放agent自己截取的日志文件，如果文件过大导致磁盘写满那么agent端会报出</div>
<div align="left">Error closing logicalNode a2-18 sink: No space lefton device，所以在配置agent端的时候需要注意</div>
<div align="left">&lt;property&gt;</div>
<div align="left">   &lt;name&gt;flume.agent.logdir&lt;/name&gt;</div>
<div align="left">   &lt;value&gt;/data/tmp/flume-${user.name}/agent&lt;/value&gt;</div>
<div align="left">  &lt;/property&gt;</div>
<div align="left">属性，只要保证flume在7*24小时运行过程agent端不会使该路径flume.agent.logdir磁盘写满即可。</div>
<br><div align="left">2，  Flume在启动时候会去寻找hadoop-core-*.jar的文件，需要修改标准版的hadoop核心jar包的名字 将hadoop-*-core.jar改成hadoop-core-*.jar。</div>
<br><div align="left">3，  Flume集群中的flume必须版本一致。否则会出现莫名其妙的错误。</div>
<br><div align="left">4，  Flume集群收集的日志发送到hdfs上建立文件夹的时间依据是根据event的时间，在源代码上是Clock.unixTime()，所以如果想要根据日志生成的时间来生成文件的话，需要对</div>
<div align="left">com.cloudera.flume.core.EventImpl类的构造函数</div>
<div align="left">publicEventImpl(byte[] s, long timestamp, Priority pri, long nanoTime,</div>
<div align="left">      String host, Map&lt;String,byte[]&gt; fields)重新写，解析数组s的内容取出时间，赋给timestamp。</div>
<div align="left">注意：flume的框架会构造s内容是空的数组，用来发送类似简单验证的event，所以需要注意s内容为空的时候timestamp的问题。</div>
<br><div align="left">5，  Flume在读取utf-8格式的文件时会出现解析不了时间戳，因为utf-8的文件格式在文件头会加上utf-8的文件标识，</div>
<div align="left">解决办法：</div>
<div align="left">flume-core-0.9.4-modify.jar包修改了 EventImpl类</div>
<div align="left">使文件创建的时间是根据日志中的时间来确定的,修改的代码部分在FlumeSource中</div>
<div align="left">修改的部分是在构造函数上，支持utf-8的文件格式，也支持其他普通的文件格式的文件</div>
<div align="left">主要代码如下：</div>
<div align="left">System.arraycopy(s, 3, tmpByte, 0, 13);</div>
<div align="left">     tmpStr= new String(tmpByte);</div>
<div align="left">     m =p.matcher(tmpStr);</div>
<div align="left">     if(m.matches())//表示符合utf-8的文件格式</div>
<div align="left">     this.timestamp= Long.parseLong(tmpStr);</div>
<div align="left">     else</div>
<div align="left">     this.timestamp=Long.parseLong(new String(s).split("\t")[0].toString());</div>
<div align="left">     </div>
<div align="left">性能没有测试。</div>
<br><div align="left">6，   与5不同的是时间戳不是long的，而是字符串如：“2011-10-21 10:12:65.125”，修改办法如下： System.arraycopy(s, 3, tmpByte, 0, 25);</div>
<div align="left">     tmpStr = new String(tmpByte);</div>
<div align="left">     try {</div>
<div align="left">            //this.timestamp=sf.parse(tmpStr.split("\t")[0].toString()).getTime();</div>
<div align="left">            this.timestamp=sf.parse(tmpStr.substring(0,tmpStr.indexOf("\t"))).getTime();</div>
<div align="left">        }catch (ParseException e) {</div>
<div align="left">            e.printStackTrace();</div>
<div align="left">        }</div>
<div align="left"></div>
<div align="left">7，  如果collector和agent不在一个网段的话会发生闪断的现象，这样的话，就会造成agent端不能传送数据个collector所以，在部署agent和collector最好在一个网段。</div>
<br><div align="left">8，  如果在启动master时出现：“试着启动hostname，但是hostname不在master列表里的错误“，这是需要检查是否主机地址和hostname配置的正确与否。</div>
<br><br><br><br></td>
</tr></tbody></table><div class="pattl" style="overflow:hidden;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;">
<dl class="tattl attm" style="overflow:visible;"><dt style="width:0px;font-weight:700;"></dt><dd style="color:rgb(153,153,153);">
<p class="mbn">
<a href="http://www.aboutyun.com/forum.php?mod=attachment&amp;aid=Njg5Nnw1Zjc4YjNlNnwxNDgwNTczNzczfDB8ODk4NA%3D%3D&amp;nothumb=yes" rel="nofollow" id="aid6896" class="xw1" style="color:rgb(51,102,153);font-weight:700;">12.png</a> <span class="xg1" style="color:rgb(102,102,102);">(65.5
 KB, 下载次数: 31)</span></p>
<p class="mbn">
</p>
<div class="mbn savephotop">
<img id="aimg_6896" src="http://www.aboutyun.com/data/attachment/forum/201409/01/144811wb99wzi6gb2wiz8g.png" class="zoom" width="600" alt="12.png" title="12.png"></div>
</dd><dd style="color:rgb(153,153,153);">
<div class="mbn savephotop">
<br></div>
</dd><dd style="color:rgb(153,153,153);">
<div class="mbn savephotop">
<br></div>
</dd><dd style="color:rgb(153,153,153);">
<div class="mbn savephotop">
出处：http://www.aboutyun.com/thread-8984-1-1.html<br></div>
</dd></dl></div>
            </div>
                </div>
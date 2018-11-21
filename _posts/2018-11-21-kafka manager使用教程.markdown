---
layout:     post
title:      kafka manager使用教程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 id="toc_0" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
1. 前言</h1>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
市面上主流的kafka监控工具有：</p>
<ol style="overflow:auto;color:rgb(68,68,68);font-family:sans-serif;font-size:15px;"><li style="text-indent:.5em;">Kafka Web Conslole</li><li style="text-indent:.5em;">Kafka Manager</li><li style="text-indent:.5em;">KafkaOffsetMonitor、</li></ol><p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
这些工具都是开源的。三者的对比可以参看<a href="http://top.jobbole.com/31084/#comment-4690" rel="nofollow" style="color:rgb(110,113,115);border-bottom:1px solid rgb(170,170,170);">kafka三种工具比较</a></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
我查看了github上三者工具的更新时间，发现kafka manager的作者维护的比较情况。虽然WIKI上说kafka manager没说支持0.10，但是通过调研后发现，直接使用于kafka 0.10版本也是没有问题的。本文就介绍下如何使用kafka manager</p>
<h1 id="toc_1" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
2. kafka manager功能简介</h1>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
kafka manager支持以下功能：</p>
<ol style="overflow:auto;color:rgb(68,68,68);font-family:sans-serif;font-size:15px;"><li style="text-indent:.5em;">管理多个kafka集群</li><li style="text-indent:.5em;">方便的集群状态监控 (包括topics, consumers, offsets, brokers, replica distribution, partition distribution)</li><li style="text-indent:.5em;">方便选择你想要的分区副本</li><li style="text-indent:.5em;">配置分区任务，包括选择使用哪些brokers</li><li style="text-indent:.5em;">可以对分区任务重分配</li><li style="text-indent:.5em;">提供不同的选项来创建topic (0.8.1.1 has different configs than 0.8.2+，不同版本配置上有所不同)</li><li style="text-indent:.5em;">删除topic(仅仅支持 0.8.2以上版本并且注意在broker config中设置delete.topic.enable=true in broker config)</li><li style="text-indent:.5em;">Topic list会指明哪些topic被删除 （在0.8.2以上版本适用）</li><li style="text-indent:.5em;">批量产生分区任务并且和多个topic和brokers关联</li><li style="text-indent:.5em;">批量运行多个主题对应的多个分区</li><li style="text-indent:.5em;">向已经存在的主题中添加分区</li><li style="text-indent:.5em;">对已经存在的topic修改配置</li><li style="text-indent:.5em;">可选地在broker level和topic level的度量中启用JMX polling功能</li><li style="text-indent:.5em;">可选地过滤在ZK上没有 ids/ owners/ &amp; offsets/ directories的consumer</li></ol><h1 id="toc_2" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
3. 下载安装</h1>
<h2 id="toc_3" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
3.1 环境要求</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
Kafka 0.8.1.1 or 0.8.2.+ or 0.9.0.+<br>
Java 8+<br>
sbt 0.13.x</p>
<h2 id="toc_4" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
3.2 安装scala插件</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
这里我们使用idea和sbt插件来完成kafka-manager的安装</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
直接在设置里面搜索scala安装即可，科学上网建议采用<a href="https://github.com/XX-net/XX-Net" rel="nofollow" style="color:rgb(110,113,115);border-bottom:1px solid rgb(170,170,170);">xxnet</a></p>
<h2 id="toc_5" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
3.3 使用repox加载SBT下载依赖</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
一开始用公司的nexus私服尝试了下，发现不行。不代理ivy格式的文件。还好后来找到了这个开源工具，提供给了依赖的私服下载。使用办法可以查看<a href="https://github.com/Centaur/repox/wiki/%E5%85%A5%E9%97%A8%E6%8C%87%E5%8D%97" rel="nofollow" style="color:rgb(110,113,115);border-bottom:1px solid rgb(170,170,170);">入门指南</a></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
在使用repox工作前，可以做以下准备工作<br>
1.下载sbt并且安装。<br>
下载安装好后，修改如下两个文件：</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/14-36-57.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
sbtconfig.txt增加如下内容：</p>
<div class="codehilite" style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<pre style="border:1px solid rgb(227,237,243);font-family:monospace, serif;font-size:1em;overflow:auto;background:rgb(63,63,63);color:rgb(253,206,147);"># Set the java args to high

-Xmx512M

-XX:MaxPermSize=256m

-XX:ReservedCodeCacheSize=128m

# Set the extra SBT options

-Dsbt.ivy.home=D:/sbt/.ivy2
-Dsbt.global.base=D:/sbt/.sbt
-Dsbt.repository.config=D:/sbt/conf/repo.properties
-Dsbt.log.format=true
</pre>
</div>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
repo.properties可以增加如下内容</p>
<div class="codehilite" style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<pre style="border:1px solid rgb(227,237,243);font-family:monospace, serif;font-size:1em;overflow:auto;background:rgb(63,63,63);color:rgb(253,206,147);">[repositories]
local
#  这里可以添加下自己的NEXUS库或者其他可用的公有仓库
typesafe: http://repo.typesafe.com/typesafe/ivy-releases/, [organization]/[module]/(scala_[scalaVersion]/)(sbt_[sbtVersion]/)[revision]/[type]s/[artifact](-[classifier]).[ext], bootOnly
sonatype-oss-releases
maven-central
sonatype-oss-snapshots
</pre>
</div>
<ol style="overflow:auto;color:rgb(68,68,68);font-family:sans-serif;font-size:15px;"><li style="text-indent:.5em;">下载nodejs(包括了npm工具)</li><li style="text-indent:.5em;">下载bower，npm install -g bower</li><li style="text-indent:.5em;">按照上面的入门指南操作</li></ol><p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
上述中的bower依赖于node.js和npm，因此需要先安装node.js。 sbt将会在target/scala-2.11/目录下生成 repox-assembly-$VERSION.jar</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
运行使用以下命令</p>
<div class="codehilite" style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<pre style="border:1px solid rgb(227,237,243);font-family:monospace, serif;font-size:1em;overflow:auto;background:rgb(63,63,63);color:rgb(253,206,147);">$ java -Xmx512m -jar repox-assembly-$VERSION.jar
</pre>
</div>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
注意repox也需要先编译，这个过程很慢。有时候发现一直在下载的包很慢，可以手动去maven仓库下载，然后扔到本地的sbt仓库。</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/14-34-13.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
Repox web admin 默认密码为zhimakaimen.</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
通过访问127.0.0.1:8087访问repox</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/11-27-43.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
这里的仓库信息可以使用repox提供的，直接点击右上角的进行导入操作</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/11-37-41.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
仓库信息可以从<a href="http://centaur.github.io/repox/" rel="nofollow" style="color:rgb(110,113,115);border-bottom:1px solid rgb(170,170,170);">repox社区公服</a>导入：</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/11-38-04.jpg" alt="" style="border:0px;"><br>
这个即为需要导入的文件<br><img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/11-39-23.jpg" alt="" style="border:0px;"></p>
<h2 id="toc_6" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
3.4 从github下载工程并且创建sbt工程</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
设置下自定义的sbt还有VM参数，然后倒入SBT工程就可以了。慢慢等待吧，用了repox确实快了不少。</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/15-12-07.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/14-59-28.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
PS：如果sbt出现了java.lang.NoClassDefFoundError: sdt/testing/Framework 可以考虑删除sbt安装目录下的.sbt目录就可以了。这个错误貌似是多个项目同时使用sbt时发生了一些冲突。</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
导入工程后，在SBT主目录下执行以下命令编译：</p>
<div class="codehilite" style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<pre style="border:1px solid rgb(227,237,243);font-family:monospace, serif;font-size:1em;overflow:auto;background:rgb(63,63,63);color:rgb(253,206,147);">./sbt clean dist
</pre>
</div>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
执行完毕后，生成的包会在kafka-manager/target/universal 下。该压缩包可以直接部署后使用</p>
<h1 id="toc_7" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
4.部署kafka-manaer</h1>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
将编译打包后的kafka-manager在需要部署的机器上解压出来。</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/14-02-40.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
进入conf中配置下application.conf中的kafka-manager.zkhosts来指定下自己的zk-server<br><img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/14-13-57.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
启动kafka-manager，这里采用nohup配合&amp;来后台运行（使用nohup，如果账户退出的话，该进程不会被关掉）。通过-Dhttp.port可以指定端口，默认为9000</p>
<pre style="border:1px solid rgb(227,237,243);font-family:monospace, serif;font-size:15px;overflow:auto;background:rgb(247,250,251);color:rgb(68,68,68);"><code style="font-family:monospace, serif;border:none;background:transparent;color:rgb(51,51,51);">nohup bin/kafka-manager -Dconfig.file=conf/application.conf -Dhttp.port=9001 &amp;</code></pre>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
通过浏览器访问该机器的9001端口可以访问kafka-manager的web管理界面</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/14-26-42.jpg" alt="" style="border:0px;"></p>
<h1 id="toc_8" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
5. 配置kafka-manager</h1>
<h2 id="toc_9" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
5.1 配置JMX（JAVA管理扩展）</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
为了监控Kafka，我们建议配置下JMX，使得监控能够提供更加详细的内容。kafka是用scala写的，而scala依赖JVM，所以用JMX来监控是理所当然的。</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
修改bin/kafka-server-start.sh，可以在堆信息配置那里添加JMX_PORT参数。我们这里使JMX端口为9999。</p>
<div class="codehilite" style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<pre style="border:1px solid rgb(227,237,243);font-family:monospace, serif;font-size:1em;overflow:auto;background:rgb(63,63,63);color:rgb(253,206,147);"><span class="k" style="color:rgb(240,223,175);">if</span> <span class="o" style="color:rgb(240,239,208);">[</span> <span class="s2" style="color:rgb(204,147,147);">"x</span>$<span class="s2" style="color:rgb(204,147,147);">KAFKA_HEAP_OPTS"</span> <span class="o" style="color:rgb(240,239,208);">=</span> <span class="s2" style="color:rgb(204,147,147);">"x"</span> <span class="o" style="color:rgb(240,239,208);">]</span><span class="p" style="color:rgb(65,112,111);">;</span> <span class="k" style="color:rgb(240,223,175);">then</span>
    <span class="nb" style="color:rgb(239,239,143);">export</span> <span class="nv" style="color:rgb(220,220,204);">KAFKA_HEAP_OPTS</span><span class="o" style="color:rgb(240,239,208);">=</span><span class="s2" style="color:rgb(204,147,147);">"-Xmx1G -Xms1G"</span>
    <span class="nb" style="color:rgb(239,239,143);">export</span> <span class="nv" style="color:rgb(220,220,204);">JMX_PORT</span><span class="o" style="color:rgb(240,239,208);">=</span><span class="s2" style="color:rgb(204,147,147);">"9999"</span>
<span class="k" style="color:rgb(240,223,175);">fi</span>
</pre>
</div>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
然后再启动kafka</p>
<pre style="border:1px solid rgb(227,237,243);font-family:monospace, serif;font-size:15px;overflow:auto;background:rgb(247,250,251);color:rgb(68,68,68);"><code style="font-family:monospace, serif;border:none;background:transparent;color:rgb(51,51,51);">nohup sh kafka-server-start.sh ../config/server.properties &amp;</code></pre>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
开启JMX之后除了可以使得jconsole监控更加详细的kafka信息。JMX监控kafka的使用可以参考我其他的<a href="http://kaimingwan.com/category/kafka" rel="nofollow" style="color:rgb(110,113,115);border-bottom:1px solid rgb(170,170,170);">kafka系列文章</a></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
用visualVM可以来监控kafka manager的JVM</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/15-09-41.jpg" alt="" style="border:0px;"></p>
<h2 id="toc_10" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
5.2 新建集群</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
刚进去只是个空空的管理界面，首先需要新建一个集群。<br><img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/14-37-01.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
建完集群之后可以查看集群的状态信息</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/15-43-22.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
还可以进行一些日志查看以及增删主题等操作。</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
虽说kafka-manager官方没说支持最新的0.10半本的kafka，但是现在用起来没什么问题。</p>
<h2 id="toc_11" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
5.3 使用体验</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
我们拿otter来获取实时信息给kafka，然后来体验下kafka manager提供的功能。在我们的实验中，我们采用2个broker（分别在不同的NODE上）和3个consumer(在一台机器上)</p>
<h3 id="toc_12" style="line-height:1;color:rgb(68,68,68);font-family:sans-serif;">
5.3.1 使用logkafka做日志收集</h3>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
logkafka貌似是国人开发的一个开源工具，被集成在kakfa-manager。详情见<a href="https://github.com/Qihoo360/logkafka/wiki" rel="nofollow" style="color:rgb(110,113,115);border-bottom:1px solid rgb(170,170,170);">logkafka说明</a></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
具体的安装教程参见<a href="http://kaimingwan.com/post/kafka/logkafkaan-zhuang" rel="nofollow" style="color:rgb(110,113,115);border-bottom:1px solid rgb(170,170,170);">logkafka安装</a></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
把玩了一下，觉得如果要做日志收集还是上logstash或者flume这种成熟点的系统比较好。</p>
<h3 id="toc_13" style="line-height:1;color:rgb(68,68,68);font-family:sans-serif;">
5.3.2 往kafka写消息</h3>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
这时候尝试往kafka写入1万条数据库记录看看效果。数据表结构为：</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/16-13-42.jpg" alt="" style="border:0px;"></p>
<h2 id="toc_14" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
5.4 broker信息</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
broker提供了一些准实时的监控（监控会有个采样间隔，表示的是某个时间段内的吞吐信息）。红圈部分可以查看broker fetch消息失败的情况。其他地方主要是查看一个吞吐量信息。</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/17-47-46.jpg" alt="" style="border:0px;"></p>
<h2 id="toc_15" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
5.5 topic信息</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/16-34-00.jpg" alt="" style="border:0px;"></p>
<h2 id="toc_16" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
5.6 分区信息</h2>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
可以查看分区上offset的信息</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
可以修改分区和broker的绑定关系</p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/17-49-16.jpg" alt="" style="border:0px;"></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/17-49-36.jpg" alt="" style="border:0px;"></p>
<h1 id="toc_17" style="line-height:1.2em;color:rgb(68,68,68);font-family:sans-serif;">
6. kafka manager with 0.10.x supported</h1>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
官方现在提供的kafka manager过过用在0.10.x的kafka集群上会出问题。例如在复制的时候，由于kafka manager使用的仍然是0.9的client来实现的，可能会有如下报错(在kafka manager日志当中)，造成kafka manager不断的产生日志，然后导致磁盘资源耗尽。日志报错内容如下：<br>
可见是由于协议的缘故导致找不到指定的域。<br><img src="http://kaimingwan.com/kafka/_image/kafka%20manager%E4%BD%BF%E7%94%A8%E6%95%99%E7%A8%8B/11-06-40.jpg" alt="" style="border:0px;"></p>
<pre style="border:1px solid rgb(227,237,243);font-family:monospace, serif;font-size:15px;overflow:auto;background:rgb(247,250,251);color:rgb(68,68,68);"><code style="font-family:monospace, serif;border:none;background:transparent;color:rgb(51,51,51);">2016-09-01 21:32:31,015 - [WARN] - from kafka.manager.actor.cluster.KafkaManagedOffsetCache in KafkaManagedOffsetCache 
Failed to process a message from offset topic!
org.apache.kafka.common.protocol.types.SchemaException: Error reading field 'responses': Error reading array of size 680815, only 448 bytes available
    at org.apache.kafka.common.protocol.types.Schema.read(Schema.java:71) ~[org.apache.kafka.kafka-clients-0.9.0.1.jar:na]
    at org.apache.kafka.clients.NetworkClient.handleCompletedReceives(NetworkClient.java:439) ~[org.apache.kafka.kafka-clients-0.9.0.1.jar:na]
    at org.apache.kafka.clients.NetworkClient.poll(NetworkClient.java:265) ~[org.apache.kafka.kafka-clients-0.9.0.1.jar:na]
    at org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient.clientPoll(ConsumerNetworkClient.java:320) ~[org.apache.kafka.kafka-clients-0.9.0.1.jar:na]
    at org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient.poll(ConsumerNetworkClient.java:213) ~[org.apache.kafka.kafka-clients-0.9.0.1.jar:na]
    at org.apache.kafka.clients.consumer.internals.ConsumerNetworkClient.poll(ConsumerNetworkClient.java:193) ~[org.apache.kafka.kafka-clients-0.9.0.1.jar:na]
    at org.apache.kafka.clients.consumer.KafkaConsumer.pollOnce(KafkaConsumer.java:908) ~[org.apache.kafka.kafka-clients-0.9.0.1.jar:na]
    at org.apache.kafka.clients.consumer.KafkaConsumer.poll(KafkaConsumer.java:853) ~[org.apache.kafka.kafka-clients-0.9.0.1.jar:na]
    at kafka.manager.actor.cluster.KafkaManagedOffsetCache$$anonfun$run$3.apply(KafkaStateActor.scala:253) [kafka-manager.kafka-manager-1.3.1.6-sans-externalized.jar:na]
    at kafka.manager.actor.cluster.KafkaManagedOffsetCache$$anonfun$run$3.apply(KafkaStateActor.scala:235) [kafka-manager.kafka-manager-1.3.1.6-sans-externalized.jar:na]
    at scala.util.Success.foreach(Try.scala:236) [org.scala-lang.scala-library-2.11.8.jar:na]
    at kafka.manager.actor.cluster.KafkaManagedOffsetCache.run(KafkaStateActor.scala:235) [kafka-manager.kafka-manager-1.3.1.6-sans-externalized.jar:na]
    at java.lang.Thread.run(Thread.java:745) [na:1.8.0_45]</code></pre>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
不过值得高兴的是，也有细心的网友提了pull request。大家可以按照前面所说的编译方法直接clone<a href="https://github.com/Innometrics/kafka-manager" rel="nofollow" style="color:rgb(110,113,115);border-bottom:1px solid rgb(170,170,170);">kafka manager for
 0.10.x</a></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
<br></p>
<p style="color:rgb(68,68,68);font-family:sans-serif;font-size:15px;">
转载于：http://kaimingwan.com/post/kafka/kafka-managershi-yong-jiao-cheng</p>
            </div>
                </div>
---
layout:     post
title:      spark history server部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>spark history server是spark提供的spark应用历史数据查询服务，可以通过history server页面查看已经运行结束的spark应用的所有job执行情况，用于分析，因为spark集群保存的spark应用数量和每个应用的job数量有一定的限制，对于时间比较久远的应用或者应用的job数据无法在spark的web ui查看到并进行分析。所以spark提供了history server服务用于对spark应用的历史数据进行管理、查看、分析。</p>

<h3 id="1配置">1、配置</h3>



<h4 id="history-server相关配置介绍">history server相关配置介绍</h4>

<p>history server相关的配置可以写在spark-default.conf文件，也可以 <br>
相关配置参数如下： <br>
spark.eventLog.enabled  是否启动spark事件日志，默认false，既关闭 <br>
spark.eventLog.dir  spark事件日志存储目录路径，默认/tmp/spark-events，强烈建议配置为hdfs路径 <br>
spark.eventLog.compress 是否对事件日志进行压缩，默认false；如果开启压缩，使用配置参数spark.io.compression.codec指定的压缩算法，该参数默认压缩算法为lz4。 <br>
spark.history.provider  一个ApplicationHistoryProvider抽象类的实现类，用于从从存储在文件系统中的事件日志获取应用的历史信息。目前默认实现类为FsHistoryProvider。 <br>
spark.history.fs.logDirectory job日志存储路径，目录必须已经创建，默认值为/tmp/spark-events，建议将目录配置为hdfs路径，这样当app和history server不再同一台机器运行时，同样可以访问history数据。该配置项必须要和spark.eventLog.dir配置项相同，history server需要从 <br>
spark.history.fs.cleaner.maxAge job日志清理时间，默认值为7d，既7天 <br>
spark.history.store.path 用于缓存app history信息的本地目录路径，默认没有配置，所有所有的app history信息都是保存在内存中 <br>
spark.history.store.maxDiskUsage 本地磁盘最大使用量，默认是10g <br>
spark.history.ui.port   history server服务ui绑定端口，默认是18080</p>



<h4 id="修改配置文件spark-defaultconf文件">修改配置文件spark-default.conf文件</h4>

<p>修改在spark-default.conf配置文件，添加history server相关的配置项 <br>
添加必须以下配置项： <br>
<img src="https://note.youdao.com/yws/api/personal/file/WEBfe3613cafa5021d1c05717a1e61145b6?method=download&amp;shareKey=4c4d0df6e3c0d578cf6546dca0f38ec3" alt="image" title=""></p>



<h3 id="2启动history-server">2、启动history server</h3>



<h4 id="创建history-server存储事件日志目录">创建history server存储事件日志目录</h4>

<p>此处配置的事件日志存储目录是hdfs的spark_history目录，需要手动创建该目录，否则启动app和history server会报错 <br>
使用hdfs命令创建事件日志目录： <br>
<img src="https://note.youdao.com/yws/api/personal/file/WEBad25e98e7b8de8d6b142fad2f151d71a?method=download&amp;shareKey=a0827163feb14319992b694ed68070ee" alt="image" title=""></p>



<h4 id="启动history-server">启动history server</h4>

<p>使用spark的sbin目录下的start-history-server.sh脚本启动history server: <br>
<img src="https://note.youdao.com/yws/api/personal/file/WEBb05e6d6209039d8f308d8bc23e998259?method=download&amp;shareKey=0d53aa16330c1f2e98dd999a4beec3df" alt="image" title=""> <br>
浏览器访问history server服务： <br>
<img src="https://note.youdao.com/yws/api/personal/file/WEB3e025d1a49ad2aadb6900030b7af4210?method=download&amp;shareKey=82b82ee3c3fb366e2f373f3f6aa135c2" alt="image" title=""> <br>
因为没有提交的app，所有没有任何提交的app记录。 <br>
接下来，启动一个spark-shell app，然后退出 <br>
<img src="https://note.youdao.com/yws/api/personal/file/WEB472f9782c825d9e83ac44c956c416cfa?method=download&amp;shareKey=e26febaf431c53e90601126ebce5c2b2" alt="image" title=""> <br>
在重新刷新一下history server页面 <br>
<img src="https://note.youdao.com/yws/api/personal/file/WEBf95f88b440768a1ec792115aca1f58be?method=download&amp;shareKey=fea78f87ebc3bec4395d1fb0a78b005f" alt="image" title=""> <br>
发现多了一个app的提交历史记录。 <br>
到此，spark的history server启动就算成功了。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
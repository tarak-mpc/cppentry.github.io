---
layout:     post
title:      Apache Flume - HDFSSink
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<blockquote>
<div>        这篇文章是关于Flume HDFSSink的，以Flume-1.3.1版为依据。Flume设计目标是：高效收集聚合大量的日志数据。Fluem是一个可靠的、可用的、分布式日志收集聚合系统。Flume体系结构基于流式数据流设计。其中Flume Sink主要承担将数据传输到下一跳或最终目的的任务。</div>
<div>       <img src="http://img.my.csdn.net/uploads/201306/02/1370148377_2717.png" alt=""></div>
        HDFSSink用来将数据写入Hadoop分布式文件系统（HDFS）中。支持创建text和sequence文件。支持这2种文件类型的压缩。支持文件周期性滚动（就是关闭当前文件在建立一个新的），滚动可以基于时间、数据大小、事件数量。也支持通过event hearder属性timestamp或host分割数据。HDFS目录路径或文件名支持格式化封装，相应的封装串在HDFSSink生成目录或文件时被恰当的替换。使用HDFSSink需要首先安装hadoop，HDFSSink是通过hadoop jar和HDFS集群通信的。注意Hadoop版本需要支持sync().</blockquote>
                 以下是HDFS支持的封装串：
<div>                  <img src="http://img.my.csdn.net/uploads/201306/02/1370148392_2886.png" alt=""></div>
<blockquote>
<div>      使用中的文件以".tmp"结尾，一旦文件关闭，这个扩展名将被移除。这样我们就可以排除没有完成的文件。时间相关的封装串都依赖于event的header中的timestamp属性。HDFSSink的属性如下：</div>
<div> <img src="http://img.my.csdn.net/uploads/201306/02/1370148425_8904.png" alt=""></div>
</blockquote>
<div>
<blockquote>
<p>配置示例：</p>
<p>log-collector.sources=screen</p>
<p>log-collector.sinks=screen</p>
<p>log-collector.channels=screen</p>
</blockquote>
 <br><blockquote>
<p># Describe the source</p>
<p>略</p>
<p> </p>
<p><strong># Describe the sink</strong></p>
<p><strong>log-collector.sinks.screen.type = hdfs</strong></p>
<p><strong>#日志保存路径，这里按天存放</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.path=hdfs://nn.uusee.com:9000/user/flume/original/screen/%Y/%m/%d</strong></p>
<p><strong>#文件前缀，也可以使用封装串</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.filePrefix=screen-%Y%m%d-%H0000000</strong></p>
<p><strong>#使用LZO压缩算法，注意这里有一个bug，使用LzopCodec时文档没有说明</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.codeC=LzopCodec</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.fileType=CompressedStream</strong></p>
<p><strong>#每10分钟滚动</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.rollInterval=600</strong></p>
<p><strong>#不根据文件大小滚动</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.rollSize=0</strong></p>
<p><strong>#不根据事件条数滚动</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.rollCount=0</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.writeFormat=Text</strong></p>
<p><strong>#批量保存大小，需要和channel的匹配</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.batchSize=1000</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.threadsPoolSize=15</strong></p>
<p><strong>#hadoop集群响应时间较长时需要配置</strong></p>
<p><strong>log-collector.sinks.screen.hdfs.callTimeout=30000</strong></p>
<p> </p>
<p> </p>
<p># Describe the channel</p>
<p>log-collector.channels.screen.type = file</p>
<p>log-collector.channels.screen.checkpointDir = /data0/flume-ng-channel/log/screen/checkpoint</p>
<p>log-collector.channels.screen.dataDirs = /data0/flume-ng-channel/log/screen/data</p>
<p>log-collector.channels.screen.capacity = 2000000</p>
<p><span style="color:#FF0000;"><strong>log-collector.channels.screen.transactionCapacity = 1000</strong></span></p>
<p> </p>
<p># Bind the source and sink to the channel</p>
<p>log-collector.sources.screen.channels = screen</p>
<p>log-collector.sinks.screen.channel = screen</p>
</blockquote>
</div>
            </div>
                </div>
---
layout:     post
title:      Spark学习笔记-Streaming-Flume
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';"><span style="line-height:24px;"><span style="font-size:24px;">http://blog.csdn.net/fighting_one_piece/article/details/40667035<br></span></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';"><span style="line-height:24px;"><span style="font-size:24px;">Spark Streaming 与 Flume集成有两种模式：</span></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';"><span style="line-height:24px;"><span style="font-size:24px;"><br></span></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">1、基于推模式</span></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"><br></span></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;">Flume 配置文件如下：</span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></span></p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/503927" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/503927/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">a1.sources = r1  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.channels = c1  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks = k1  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.type = netcat  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.bind = centos.host1  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.port = 22222  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.channels = c1  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.channels.c1.type = memory  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.channels.c1.capacity = 1000  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.channels.c1.transactionCapacity = 100  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks.k1.type = avro  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks.k1.channel = c1  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks.k1.hostname = localhost  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks.k1.port = 11111  </span></li></ol></div>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">当然也可以配置其他source，比如exec、thrift、avro等</span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
</p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/503927" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/503927/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">a1.sources.r1.type = avro  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.bind = localhost  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.port = 22222  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.channels = c1   </span></li></ol></div>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">Spark 代码如下：</span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"></span></p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/503927" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/503927/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">import org.apache.spark.streaming.flume._  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.streaming.StreamingContext  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.SparkContext  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.streaming.Seconds  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.storage.StorageLevel  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">object SparkStreamingFlume1 {  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  def main(args: Array[String]) {  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    if (args.length &lt; 2) {    </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      print("please enter host and port")  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      System.exit(1)  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    }    </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    val sc = new SparkContext("spark://centos.host1:7077", "Spark Streaming Flume Integration")  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    //创建StreamingContext，20秒一个批次  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    val ssc = new StreamingContext(sc, Seconds(20))  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    val hostname = args(0)  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    val port = args(1).toInt  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    val storageLevel = StorageLevel.MEMORY_ONLY  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    val flumeStream = FlumeUtils.createStream(ssc, hostname, port, storageLevel)  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    flumeStream.count().map(cnt =&gt; "Received " + cnt + " flume events." ).print()  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    //开始运行  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    ssc.start()  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    //计算完毕退出  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    ssc.awaitTermination()  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    sc.stop()  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">}  </span></li></ol></div>
<br style="font-family:Arial;font-size:14px;line-height:26px;"><span style="line-height:26px;font-family:'Microsoft YaHei';font-size:18px;">提交Spark，这里需要注意的添加必要的jar包，可以在提交的时候加上--jars来指定相关的jar包，也可以在sc中调用addJar()添加</span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;">[hadoop@centos spark-1.1.0-bin-hadoop2.4]$bin/spark-submit --class org.project.modules.streaming.SparkStreamingFlume1 --jars lib/spark-streaming-flume-sink_2.10-1.1.0.jar,lib/spark-examples-1.1.0-hadoop2.4.0.jar
 --master spark://centos.host1:7077 /home/hadoop/temp/flume.jar centos.host1 11111<br></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">启动flume</span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><span style="line-height:24px;">[hadoop@centos flume-1.5.0.1]$bin/flume-ng agent --conf conf --conf-file conf/example5.properties --name a1 -Dflume.root.logger=INFO,console</span><br></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">source类型是netcat的话，可以通过telnet centos.host1 22222测试，如果是avro类型的话，可以通过<span style="line-height:24px;">flume内部提供了一个avro client测试，方式如下</span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;">[hadoop@centos flume-1.5.0.1]$bin/flume-ng avro-client --conf conf -H localhost -p 44444 -F a.xml -Dflume.root.logger=DEBUG,console <br></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">2、基于拉模式</span></span><br></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;">Flume 配置文件如下：<br></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></span></p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/503927" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/503927/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">a1.sources = r1  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.channels = c1  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks = k1  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.type = netcat  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.bind = centos.host1  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.port = 22222  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sources.r1.channels = c1  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.channels.c1.type = memory  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.channels.c1.capacity = 1000  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.channels.c1.transactionCapacity = 100  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks.k1.type = org.apache.spark.streaming.flume.sink.SparkSink  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks.k1.hostname = centos.host1  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks.k1.port = 11111  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">a1.sinks.k1.channel = c1  </span></li></ol></div>
<br style="font-family:Arial;font-size:14px;line-height:26px;"><span style="line-height:26px;font-family:'Microsoft YaHei';font-size:18px;">Spark代码如下：<br></span><span style="font-family:Arial;font-size:14px;line-height:26px;"></span>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></span></p>
<div class="dp-highlighter bg_plain" style="font-family:Consolas, 'Courier New', Courier, mono, serif;overflow:auto;line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[plain]</strong> <a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><a href="http://blog.csdn.net/fighting_one_piece/article/details/40667035#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a><a href="https://code.csdn.net/snippets/503927" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a><a href="https://code.csdn.net/snippets/503927/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a>
<div style="width:18px;z-index:99;">
</div>
</div>
</div>
<ol start="1" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">import org.apache.spark.streaming.flume._  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.streaming.StreamingContext  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.SparkContext  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.streaming.Seconds  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">import org.apache.spark.storage.StorageLevel  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">object SparkStreamingFlume2 {  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  def main(args: Array[String]) {  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    if (args.length &lt; 2) {    </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      print("please enter host and port")  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      System.exit(1)  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    }    </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    val sc = new SparkContext("spark://centos.host1:7077", "Spark Streaming Flume Integration")  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    //创建StreamingContext，20秒一个批次  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    val ssc = new StreamingContext(sc, Seconds(20))  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    val hostname = args(0)  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    val port = args(1).toInt  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    val storageLevel = StorageLevel.MEMORY_ONLY  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    val flumeStream = FlumeUtils.createPollingStream(ssc, hostname, port, storageLevel)  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    flumeStream.count().map(cnt =&gt; "Received " + cnt + " flume events." ).print()  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    //开始运行  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    ssc.start()  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    //计算完毕退出  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    ssc.awaitTermination()  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    sc.stop()  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">  }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">}  </span></li></ol></div>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><br></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;"><span style="line-height:24px;">启动Flume，这里主要需要添加 </span>scala-library.jar 、<span style="line-height:24px;">spark-streaming-flume-sink_2.10-1.1.0.jar 到$FLUME_HOME/lib目录下</span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;">[hadoop@centos flume-1.5.0.1]$bin/flume-ng agent --conf conf --conf-file conf/example6.properties --name a1 -Dflume.root.logger=INFO,console<br></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></span></p>
<p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="font-family:'Microsoft YaHei';font-size:18px;">可以通过telnet centos.host1 22222测试</span></p>
<span style="line-height:26px;font-family:'Microsoft YaHei';font-size:18px;"><br><span style="line-height:24px;">提交Spark，这里需要注意的和上面一样添加必要的jar包</span></span><br style="font-family:Arial;font-size:14px;line-height:26px;"><p style="font-family:Arial;font-size:14px;line-height:26px;">
<span style="line-height:24px;"><span style="font-family:'Microsoft YaHei';font-size:18px;">[hadoop@centos spark-1.1.0-bin-hadoop2.4]$bin/spark-submit --class org.project.modules.streaming.SparkStreamingFlume2 --jars lib/spark-streaming-flume-sink_2.10-1.1.0.jar,lib/spark-examples-1.1.0-hadoop2.4.0.jar
 --master spark://centos.host1:7077 /home/hadoop/temp/flume.jar centos.host1 11111</span></span></p>
            </div>
                </div>
---
layout:     post
title:      apache kafka技术分享系列(目录索引) .
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 id="kafkareplication设计机制-概览:" style="border-bottom-color:rgb(46,61,84);line-height:1.5;font-family:Arial, sans-serif;color:rgb(51,51,51);font-weight:normal;">
<span style="color:rgb(51,51,255);font-size:24px;">apache kafka中国社区QQ群:162272557</span></h2>
<p></p>
<p><span style="font-family:verdana, Arial, Helvetica, sans-serif;"><span style="line-height:25.2px;"><span style="color:#009900;font-size:14px;">每当有新blog发布时，会在此处更新，欢迎大家拍砖共同成长</span></span></span></p>
<p><span style="line-height:25.2px;font-family:verdana, Arial, Helvetica, sans-serif;"><span style="color:#009900;font-size:14px;">目录索引：</span></span></p>
<p><span style="line-height:25.2px;font-family:verdana, Arial, Helvetica, sans-serif;color:rgb(51,51,51);"><span style="font-size:14px;">1）</span><span style="font-family:'Microsoft YaHei';"><span style="line-height:30px;"><a href="http://blog.csdn.net/lizhitao/article/details/23743821" rel="nofollow"><span style="font-size:14px;">apache
 kafka消息服务</span></a></span></span></span></p>
<p><span style="line-height:25.2px;font-family:verdana, Arial, Helvetica, sans-serif;color:rgb(51,51,51);"><span style="font-family:'Microsoft YaHei';"><span style="font-size:14px;">2）</span><span style="line-height:30px;"><a href="http://blog.csdn.net/lizhitao/article/details/23744675" rel="nofollow"><span style="font-size:14px;">kafka在zookeeper中存储结构</span></a></span></span></span></p>
<p><span style="line-height:25.2px;font-family:verdana, Arial, Helvetica, sans-serif;color:rgb(51,51,51);font-size:14px;">3）</span><a href="http://blog.csdn.net/lizhitao/article/details/24490817" rel="nofollow"><span style="font-size:14px;"><span style="color:#ff9900;">kafka
 log4j配置</span></span></a></p>
<p><span style="font-size:14px;">4）</span><a href="http://blog.csdn.net/lizhitao/article/details/24491551" rel="nofollow"><span style="font-size:14px;"><span style="color:#ff9900;">kafka replication设计机制</span></span></a></p>
<p><span style="font-size:14px;">5）<a href="http://blog.csdn.net/lizhitao/article/details/24581907" rel="nofollow"><span style="color:#ff9900;">apache kafka监控系列-监控指标</span></a></span></p>
<p><span style="font-size:14px;">6）<a href="http://blog.csdn.net/lizhitao/article/details/25301387" rel="nofollow"><span style="color:#ff9900;">kafka.common.ConsumerRebalanceFailedException异常解决办法</span></a></span></p>
<p><span style="font-size:14px;">7）<a href="http://blog.csdn.net/lizhitao/article/details/24991799" rel="nofollow">kafak安装与使用</a></span></p>
<p><span style="font-size:14px;">8）<a href="http://blog.csdn.net/lizhitao/article/details/25667831" rel="nofollow">apache kafka中server.properties配置文件参数说明</a></span></p>
<p><span style="font-size:14px;">9）<a href="http://blog.csdn.net/lizhitao/article/details/25906957" rel="nofollow">apache kafka的consumer初始化时获取不到消息</a></span></p>
<p><span style="font-size:14px;">10）<a href="http://blog.csdn.net/lizhitao/article/details/26687109" rel="nofollow">Kafka Producer处理逻辑</a></span></p>
<p><span style="font-size:14px;">11）<a href="http://blog.csdn.net/lizhitao/article/details/26874961" rel="nofollow">apache kafka源代码工程环境搭建(IDEA)</a></span></p>
<p><span style="font-size:14px;">12）<a href="http://blog.csdn.net/lizhitao/article/details/27199863" rel="nofollow">apache kafka监控系列-KafkaOffsetMonitor</a></span></p>
<p><span style="font-size:14px;">13）<a href="http://blog.csdn.net/lizhitao/article/details/28108919" rel="nofollow">Kafka Controller设计机制</a></span></p>
<p><span style="font-size:14px;">14）<a href="http://blog.csdn.net/lizhitao/article/details/33396975" rel="nofollow">Kafka性能测试报告(虚拟机版)</a></span></p>
<p><span style="font-size:14px;">15）<a href="http://blog.csdn.net/lizhitao/article/details/35595723" rel="nofollow">apache kafka监控系列-kafka-web-console</a></span></p>
<p><span style="font-size:14px;">16）<a href="http://blog.csdn.net/lizhitao/article/details/33344597" rel="nofollow">apache kafka迁移与扩容工具用法</a></span></p>
<p><span style="font-size:14px;">17）<a href="http://blog.csdn.net/lizhitao/article/details/35641523" rel="nofollow">kafka LeaderNotAvailableException</a></span></p>
<p><span style="font-size:14px;">18）<a href="http://blog.csdn.net/lizhitao/article/details/35986849" rel="nofollow">apache kafka jmx监控指标参数</a></span></p>
<p><span style="font-size:14px;">19）<a href="http://blog.csdn.net/lizhitao/article/details/35847927" rel="nofollow">apache kafka性能测试命令使用和构建kafka-perf</a></span></p>
<p><span style="font-size:14px;">20）<a href="http://blog.csdn.net/lizhitao/article/details/37566765" rel="nofollow">apache kafka源码构建打包</a></span></p>
<p><span style="font-size:14px;">21）<a href="http://blog.csdn.net/lizhitao/article/details/37811291" rel="nofollow">Apache kafka客户端开发-java</a></span></p>
<p><span style="font-size:14px;">22）<a href="http://blog.csdn.net/lizhitao/article/details/38352947" rel="nofollow"> kafka broker内部架构</a></span></p>
<p><span style="font-size:14px;">23）<a href="http://blog.csdn.net/lizhitao/article/details/37911993" rel="nofollow">apache kafka源码分析走读-kafka整体结构分析</a></span></p>
<p><span style="font-size:14px;">24）<a href="http://blog.csdn.net/lizhitao/article/details/38438123" rel="nofollow">apache kafka源码分析走读-Producer分析</a></span></p>
<p><span style="font-size:14px;">25）<a href="http://blog.csdn.net/lizhitao/article/details/38439769" rel="nofollow">apache kafka性能优化架构分析</a></span></p>
<p><span style="font-size:14px;">26）<a href="http://blog.csdn.net/lizhitao/article/details/38442733" rel="nofollow">apache kafka源码分析走读-server端网络架构分析</a></span></p>
<p><span style="font-size:14px;">27）<a href="http://blog.csdn.net/lizhitao/article/details/38458631" rel="nofollow">apache kafka源码分析走读-ZookeeperConsumerConnector分析</a></span></p>
<p><span style="font-size:14px;">28）<a href="http://blog.csdn.net/lizhitao/article/details/38518527" rel="nofollow">kafka的ZkUtils类的java版本部分代码</a></span></p>
<p><span style="font-size:14px;">29）<a href="http://download.csdn.net/detail/lizhitao/7730029" rel="nofollow">kafka &amp; mafka client开发与实践</a></span></p>
<p>30)   <span style="font-family:'Microsoft YaHei';color:#333333;font-size:14px;"><span style="line-height:30px;"><a href="http://blog.csdn.net/lizhitao/article/details/40543631" rel="nofollow">kafka文件系统设计那些事</a></span></span></p>
<p><span style="font-family:'Microsoft YaHei';color:#333333;font-size:14px;"><strong>31</strong>）</span><span style="font-family:'Microsoft YaHei';color:rgb(51,51,51);font-size:14px;"><strong><a href="http://blog.csdn.net/lizhitao/article/details/40650989" rel="nofollow">kafka的ZookeeperConsumer实现</a></strong></span><span style="font-family:'Microsoft YaHei';color:rgb(51,51,51);font-size:14px;"><br><br></span></p>
<p><br></p>
<p><span style="color:#006600;font-size:18px;">请注明转载自:http://blog.csdn.net/lizhitao/article/details/39499283</span></p>
            </div>
                </div>
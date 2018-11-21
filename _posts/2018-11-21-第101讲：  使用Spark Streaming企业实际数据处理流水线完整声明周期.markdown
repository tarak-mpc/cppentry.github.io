---
layout:     post
title:      第101讲：  使用Spark Streaming企业实际数据处理流水线完整声明周期
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">有兴趣想学习国内整套Spark+Spark Streaming+Machine learning</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">顶级</span><span style="color:rgb(51,51,51);font-family:Arial, 'Microsoft YaHei';font-size:14px;line-height:14px;background-color:rgb(238,238,238);">课程的，可加我qq
  471186150。共享视频，性价比超高！</span><br></p>
<p>1：kafka直接也可以监控一个文件夹，但是为什么我们采用flume的方式，通过flume把文件传给kafka，而不直接通过kafka去监控一个文件夹呢？</p>
<p>1）flume可以搜集分布式的日志</p>
<p>2）kafka直接读取文件，默认情况下，kafka要求文件格式是json格式。而数据很多情况下都不是json格式。这样就会崩溃。当然自定义一个读取器。所以kafka生产环境下很少去读文件，它就是直接收放消息的。</p>
<p>2：企业实际生产环境下初步架构图如下：</p>
<p>1）下图是描述数据来源，如前端有高并发，每秒访问一百万，一千万之类的，用Nginx接收请求后，在后端用Tomcat或者apache之类的做负载均衡。</p>
<p><img src="https://img-blog.csdn.net/20160522071232359?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>2）Server中接收到请求路由后一般都会对每个请求在文件中写一条Log：<br>
可以自动配置Server写日志；<br>
也可以通过执行业务逻辑的过程来写日志<br></p>
<p><img src="https://img-blog.csdn.net/20160522071445486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>3）用户生成的日志或者不同服务器生成的日志一般是比较零散的，企业中一般都会有Crontab等定时工具来通过日志整理工具来把当天的日志采集、合并和初步的处理成为一份日志文件，当然可以多份，分布式建议1份，然后发送到Flume监控目录中！！！当Flume发现有新的日志文件进来的时候，会按照配置把数据通过Channel来Sink到目的地，这里是Sink到Kafka集群中</p>
<p><img src="https://img-blog.csdn.net/20160522070605387?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20160522071734193?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>6）上面kafka的方式是在线数据分析，下面是离线数据分析。日志上的数据当然可以如下图，直接放在hdfs上，但是实际生产环境下基本没有人直接把server上传的日志放在hdfs上。假设放在hdfs上，因为hdfs不断的收到数据，可以对数据进行如下初步的清洗：下面的分区表：每隔一小时或者一天一个目录。下面第3步，在已有表的基础上产生很多表，然后公司的业务人员分析人员基于这些目标表去写sql。执行他相应的分析需要</p>
<p><img src="https://img-blog.csdn.net/20160522071900914?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>上面数据仓库进行了多维度的ETL分析后，下面交给spark集群去处理。在企业生产环境下，大多数如果是离线数据分析，Spark处理数据都是基于Hive的，分析后的结果用于提升营业额，利润和市场占有率。而kafka则是在线数据分析</p>
<p><img src="https://img-blog.csdn.net/20160522072021347?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
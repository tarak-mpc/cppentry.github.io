---
layout:     post
title:      spark history server学习
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>原文：<a href="http://bit1129.iteye.com/blog/2174673" rel="nofollow">http://bit1129.iteye.com/blog/2174673</a> <br>
本文包括如下内容：</p>

<p>Spark History Server的作用 <br>
配置Spark History Server <br>
运行Spark History Server <br>
查看Spark程序的运行信息 <br>
Spark History Server的作用 <br>
在 运行Spark应用程序的时候，driver会提供一个webUI用于展现应用程序的运行信息，但是该webUI随着应用程序的完成而关闭端口。也就是说，这个服务是伴随Spark应用程序的运行周期的，也就是当应用程序运行完成后，将无法查看应用程序的历史记录。Spark history server就是为了应对这种情况而产生的，通过配置，Spark应用程序在运行完应用程序之后，将应用程序的运行信息写入指定目录，而Spark history server可以将这些运行信息装载并以web的方式供用户浏览</p>

<p>伴随Spark应用程序而启动的web查看服务的默认端口号是4040，或者4041(如果当前端口被占用，比如4040被占用，那么Spark会抛一个异常，但是不影响应用继续运行，driver会使用这个已用的端口加1，以此类推)</p>

<p>配置Spark History Server <br>
1. 在Spark的conf目录下，将spark-defaults.conf.template改名为spark-defaults.conf <br>
Java代码  收藏代码 <br>
mv spark-defaults.conf.template spark-defaults.conf  </p>

<ol>
<li>对spark-defaults.conf作如下配置</li>
</ol>

<p>Java代码  收藏代码 <br>
///Spark Master的IP/Port <br>
spark.master                     spark://hadoop.master:7077 <br>
///是否记录作业产生的事件或者运行状态(job，stage等使用内存等信息) <br>
spark.eventLog.enabled           true <br>
///如果记录作业产生的事件或者运行状态，则将事件写入什么位置 <br>
spark.eventLog.dir               hdfs://hadoop.master:9000/user/hadoop/sparkevtlog <br>
///http history的监听端口号，通过<a href="http://hadoop.master:18080" rel="nofollow">http://hadoop.master:18080</a>访问 <br>
spark.history.ui.port            18080  </p>

<p>启动Spark History Server <br>
1， 使用如下命令启动History Server</p>

<p>Java代码  收藏代码 <br>
[hadoop@hadoop sbin]$sbin/start-history-server.sh  </p>

<p>启动完成后，发现18080并没有监听，到Spark的logs目录下查看history server的启动日志，发现报如下错误：</p>

<p>Java代码  收藏代码 <br>
Caused by: java.lang.IllegalArgumentException: Log directory specified does not exist: file:/tmp/spark-events. Did you configure the correct one through spark.fs.history.logDirectory?  </p>

<ol>
<li>将spark.fs.history.logDirectory配置到conf/spark-defaults.conf目录里，发现并不起作用，</li>
</ol>

<p>3.查看start-history-server.sh脚本内容，发现这个参数可以作为启动脚本的第一个参数传递：</p>

<p>Java代码  收藏代码 <br>
if [ <span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-5-Frame" role="textbox" aria-readonly="true"><span class="math" id="MathJax-Span-9" style="vertical-align: -1.732em;"><span class="noError" id="MathJax-Span-10" style="display: inline-block;"># != 0 ]; then  <br>  echo “Using command line arguments for setting the log directory is deprecated. Please ”  <br>  echo “set the spark.history.fs.logDirectory configuration option instead.”  <br>  export SPARK_HISTORY_OPTS=”</span></span></span><script type="math/tex" id="MathJax-Element-5"># != 0 ]; then  
  echo “Using command line arguments for setting the log directory is deprecated. Please ”  
  echo “set the spark.history.fs.logDirectory configuration option instead.”  
  export SPARK_HISTORY_OPTS=”</script>SPARK_HISTORY_OPTS -Dspark.history.fs.logDirectory=$1” <br>
fi <br>
 所以使用如下命令启动History Server</p>

<p>Java代码  收藏代码 <br>
./start-history-server.sh hdfs://hadoop.master:9000/user/hadoop/sparkhistorylog  </p>

<p>查看作业的执行情况 <br>
1. 当History第一次启动，从没有作业执行，那么History Server上不显示内容(只提示没有Job信息可供查看) <br>
2. 使用spark-submit提交一个作业，保证SparkContext调用了stop方法，否则History Server不会显示历史信息 <br>
3.访问<a href="http://hadoop.master:18080" rel="nofollow">http://hadoop.master:18080</a>得到如下结果</p>

<p>查看作业信息 <br>
点击App ID访问<a href="http://192.168.26.136:18080/history/app-20150110055201-0002/" rel="nofollow">http://192.168.26.136:18080/history/app-20150110055201-0002/</a>，可以打开这个Spark程序的执行情况。 <br>
可以查看如下信息： <br>
Spark程序启动几个作业 <br>
这个Spark程序一个Job包含多少Stages <br>
这个Spark程序一共包含多少个Stages <br>
Stage的详细信息 <br>
这个Spark程序是几个Executor执行完成的</p>

<ol>
<li><p>在这个Spark程序启动几个Job</p>

<ol><li>Spark程序一个Job包含多少Stages</li></ol></li>
<li><p>Spark程序一共包含多少个Stages</p></li>
<li><p>Stage的详细信息</p></li>
<li><p>Spark程序是几个Executor执行完成的</p></li>
</ol>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
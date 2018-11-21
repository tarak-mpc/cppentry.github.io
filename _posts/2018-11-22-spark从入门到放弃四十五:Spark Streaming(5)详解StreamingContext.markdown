---
layout:     post
title:      spark从入门到放弃四十五:Spark Streaming(5)详解StreamingContext
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>文章地址：<a href="http://www.haha174.top/article/details/251607" rel="nofollow">http://www.haha174.top/article/details/251607</a> <br>
有两种创建StreamingContext的方式 <br>
1就是之前用的</p>

<pre class="prettyprint"><code class=" hljs fsharp"> <span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"WorldCountStreaming"</span>).setMaster(<span class="hljs-string">"local[2]"</span>)

    <span class="hljs-keyword">val</span> jssc = <span class="hljs-keyword">new</span> StreamingContext(conf, Durations.seconds(<span class="hljs-number">5</span>))
</code></pre>

<p>2.</p>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">val</span> sc=<span class="hljs-keyword">new</span> SparkContext(conf)
<span class="hljs-keyword">val</span> ssc=<span class="hljs-keyword">new</span> StreamingContext(sc,Durations.seconds(<span class="hljs-number">5</span>))</code></pre>

<p>appName  应用名称，master  是spark mesos  或者yarn  的url </p>

<p>batch interval  可以根据你的应用程序的延迟要求以及可用的集群资源情况来设置。</p>

<p>JavaStreamingcontext  还可以使用已有的JavaSparkContext来创建</p>

<p>一个StreamingContext  定义之后必须做以下几件事情 <br>
1.通过创建输入DStream  来创建输入数据源。 <br>
2.通过对Dstream  定义transformation  和output  算子操作，来定义实时计算逻辑。 <br>
3.调用Streamcontext  的start  方法来开始实时处理数据。 <br>
4.调用StreamingContext  的awaitTermination   方法，来等待应用程序的终止。开始使用CTRL+C   手动停止，或者就是让他持续不点的运行计算逻辑 <br>
5.也可以通过调用StreamingContext  的stop  方法，来停止应用程序。</p>

<p>需要注意的点 <br>
1.只要一个Streamingcontext  启动之后，就不能在往其中添加任何的计算逻辑了，比如执行了某个DStream   执行一个算子。 <br>
2.一个Streamcontext  停止之后，是肯定不能重启的，调用stop  之后，不能在调用start  方法。 <br>
3.一个jvm  同时只能有一个StreamingContext   启动，在你的应用程序中，不能创建两个StreamingContext， <br>
4.调用stop  方法时，会停止内部的SparkContext  如果不希望如此，还希望后面继续使用SparkContext 创建的其他类型的Contrext  比如Sql  context  那么就是用stop(false) <br>
5.  一个Spark  Context   可以同时创建多个StreamingContext只要上一个先用stop(false)  停止那么在创建下一个即可</p>

<h2 id="欢迎关注更多福利">欢迎关注，更多福利</h2>

<p><img src="http://upload-images.jianshu.io/upload_images/7822142-23641a8df1ead152.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
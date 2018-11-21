---
layout:     post
title:      Spark Streaming 数据清理机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-weight:normal;line-height:1.5em;color:rgb(51,51,51);font-size:24px;">
Spark Streaming 数据清理机制</h1>
<div class="article_meta" style="color:rgb(153,153,153);border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(211,211,211);font-size:14px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;line-height:21px;">
<div><span class="timestamp">时间 2016-05-11 10:29:54 </span><span class="from"><span class="icon-globe"></span> <a class="cut cut28 from" href="http://www.tuicool.com/sites/rMjaeyJ" rel="nofollow" style="color:rgb(51,51,51);text-decoration:none;overflow:hidden;display:inline;">51CTO</a></span></div>
<div class="source"><span>原文</span>  <a class="cut cut70" href="http://developer.51cto.com/art/201605/511024.htm?utm_source=tuicool&amp;utm_medium=referral" rel="nofollow" style="color:rgb(51,51,51);text-decoration:none;overflow:hidden;display:inline-block;">http://developer.51cto.com/art/201605/511024.htm</a></div>
<div><span>主题</span> <a href="http://www.tuicool.com/topics/11090153" rel="nofollow" style="color:rgb(51,51,51);text-decoration:none;"><span class="new-label" style="display:inline-block;font-size:.9em;line-height:16px;vertical-align:baseline;color:rgb(120,120,120);background-color:rgb(242,242,242);">Spark
 Streaming</span></a></div>
</div>
<div class="article_body" id="nei" style="min-height:340px;font-size:16px;line-height:1.7em;color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;">
<div style="line-height:1.7em;">
<div style="line-height:1.7em;">
<h2 style="font-family:inherit;line-height:1.6em;color:inherit;font-size:18px;text-indent:1em;">
Spark Streaming 数据清理机制</h2>
<p style="line-height:1.7em;text-indent:1em;">
大家刚开始用Spark Streaming时，心里肯定嘀咕，对于一个7*24小时运行的数据，cache住的RDD,broadcast 系统会帮忙自己清理掉么？还a是说必须自己做清理？如果系统帮忙清理的话，机制是啥？</p>
<dl><dt style="line-height:1.5em;font-weight:bold;">作者：祝威廉 来源：简书 | <em>2016-05-11 10:29</em></dt></dl></div>
<div style="line-height:1.7em;">
<h2 style="font-family:inherit;line-height:1.6em;color:inherit;font-size:18px;text-indent:1em;">
<a href="http://mdsa.51cto.com/act/2015/applications/" rel="nofollow" style="color:rgb(148,148,148);text-decoration:none;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(148,148,148);font-style:italic;">51CTO网+
 首届中国APP创新评选大赛&gt;&gt;</a></h2>
<h4 style="font-family:inherit;line-height:1.7em;color:inherit;text-indent:1em;">
前言</h4>
<p style="line-height:1.7em;text-indent:1em;">
为啥要了解机制呢?这就好比JVM的垃圾回收，虽然JVM的垃圾回收已经巨牛了，但是依然会遇到很多和它相关的case导致系统运行不正常。</p>
<p style="line-height:1.7em;text-indent:1em;">
这个内容我记得自己刚接触Spark Streaming的时候，老板也问过我，运行期间会保留多少个RDD? 当时没回答出来。后面在群里也有人问到了，所以就整理了下。文中如有谬误之处，还望指出。</p>
<h4 style="font-family:inherit;line-height:1.7em;color:inherit;text-indent:1em;">
DStream 和 RDD</h4>
<p style="line-height:1.7em;text-indent:1em;">
我们知道Spark Streaming 计算还是基于Spark Core的，Spark Core 的核心又是RDD. 所以Spark Streaming 肯定也要和RDD扯上关系。然而Spark Streaming 并没有直接让用户使用RDD而是自己抽象了一套DStream的概念。 DStream 和 RDD 是包含的关系，你可以理解为Java里的装饰模式，也就是DStream 是对RDD的增强，但是行为表现和RDD是基本上差不多的。都具备几个条件：</p>
<p style="line-height:1.7em;text-indent:1em;">
具有类似的tranformation动作，比如map,reduceByKey等，也有一些自己独有的，比如Window，mapWithStated等</p>
<p style="line-height:1.7em;text-indent:1em;">
都具有Action动作，比如foreachRDD，count等</p>
<p style="line-height:1.7em;text-indent:1em;">
从编程模型上看是一致的。</p>
<p style="line-height:1.7em;text-indent:1em;">
所以很可能你写的那堆Spark Streaming代码看起来好像和Spark 一致的,然而并不能直接复用，因为一个是DStream的变换，一个是RDD的变化。</p>
<h4 style="font-family:inherit;line-height:1.7em;color:inherit;text-indent:1em;">
Spark Streaming中 DStream 介绍</h4>
<p style="line-height:1.7em;text-indent:1em;">
DStream 下面包含几个类：</p>
<ul style="list-style-type:none;line-height:1.7em;"><li style="line-height:1.7em;list-style-type:disc;">数据源类，比如InputDStream,具体如DirectKafkaInputStream等</li><li style="line-height:1.7em;list-style-type:disc;">转换类，典型比如MappedDStream,ShuffledDStream</li><li style="line-height:1.7em;list-style-type:disc;">输出类，典型比如ForEachDStream</li></ul><p style="line-height:1.7em;text-indent:1em;">
从上面来看，数据从开始(输入)到结束(输出)都是DStream体系来完成的，也就意味着用户正常情况是无法直接去产生和操作RDD的，这也就是说，DStream有机会和义务去负责RDD的生命周期。</p>
<p style="line-height:1.7em;text-indent:1em;">
这就回答了前言中的问题了。Spark Streaming具备自动清理功能。</p>
<h4 style="font-family:inherit;line-height:1.7em;color:inherit;text-indent:1em;">
RDD 在Spark Stream中产生的流程</h4>
<p style="line-height:1.7em;text-indent:1em;">
在Spark Streaming中RDD的生命流程大体如下：</p>
<ul style="list-style-type:none;line-height:1.7em;"><li style="line-height:1.7em;list-style-type:disc;">在InputDStream会将接受到的数据转化成RDD,比如DirectKafkaInputStream 产生的就是 KafkaRDD</li><li style="line-height:1.7em;list-style-type:disc;">接着通过MappedDStream等进行数据转换，这个时候是直接调用RDD对应的map方法进行转换的</li><li style="line-height:1.7em;list-style-type:disc;">在进行输出类操作时，才暴露出RDD,可以让用户执行相应的存储，其他计算等操作。</li></ul><p style="line-height:1.7em;text-indent:1em;">
我们这里就以下面的代码来进行更详细的解释：</p>
<pre class="prettyprint hljs vim" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;border:none;background-color:rgb(246,246,246);">val <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">source</span>  =   KafkaUtils.createDirectInputStream(....) 
<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">source</span>.<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">map</span>(....).foreachRDD{rdd=&gt; 
    rdd.saveTextFile(....) 
} 
</pre>
<p style="line-height:1.7em;text-indent:1em;">
foreachRDD 产生ForEachDStream，因为foreachRDD是个Action,所以会触发任务的执行，会被调用generateJob方法。</p>
<pre><code class="language-python">override <span class="hljs-function"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">def</span> <span class="hljs-title" style="color:rgb(136,0,0);font-weight:bold;">generateJob</span><span class="hljs-params">(time: Time)</span>:</span> Option[Job] = { 
   parent.getOrCompute(time) match { 
     case Some(rdd) =&gt; 
       val jobFunc = () =&gt; createRDDWithLocalProperties(time, displayInnerRDDOps) { 
         foreachFunc(rdd, time) 
       } 
       Some(new Job(time, jobFunc)) 
     case <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">None</span> =&gt; <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">None</span> 
   } 
 } 
</code></pre>
<p style="line-height:1.7em;text-indent:1em;">
对应的parent是MappedDStream,也就是说调用MappedDStream.getOrCompute.该方法在DStream中，首先会在MappedDStream对象中的generatedRDDs 变量中查找是否已经有RDD,如果没有则触发计算，并且将产生的RDD放到generatedRDDs</p>
<pre><code class="language-python"><span class="hljs-meta" style="color:rgb(31,113,153);">@transientprivate[streaming] var generatedRDDs = new HashMap[Time, RDD[T]] () </span>
 
private[streaming] final <span class="hljs-function"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">def</span> <span class="hljs-title" style="color:rgb(136,0,0);font-weight:bold;">getOrCompute</span><span class="hljs-params">(time: Time)</span>:</span> Option[RDD[T]] = { 
    // If RDD was already generated, then retrieve it <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">from</span> HashMap, 
    // <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">or</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">else</span> compute the RDD 
    generatedRDDs.get(time).orElse { 
.... 
generatedRDDs.put(time, newRDD) 
.... 
</code></pre>
<p style="line-height:1.7em;text-indent:1em;">
计算RDD是调用的compute方法，MappedDStream 的compute方法很简单，直接调用的父类也就是DirectKafkaInputStream的getOrCompute方法：</p>
<pre><code class="language-python">override <span class="hljs-function"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">def</span> <span class="hljs-title" style="color:rgb(136,0,0);font-weight:bold;">compute</span><span class="hljs-params">(validTime: Time)</span>:</span> Option[RDD[U]] = { 
    parent.getOrCompute(validTime).map(_.map[U](mapFunc)) 
  } 
</code></pre>
<p style="line-height:1.7em;text-indent:1em;">
在上面的例子中，MappedDStream 的parent是DirectKafkaInputStream中,这是个数据源，所以他的compute方法会直接new出一个RDD.</p>
<p style="line-height:1.7em;text-indent:1em;">
从上面可以得出几个结论：</p>
<ul style="list-style-type:none;line-height:1.7em;"><li style="line-height:1.7em;list-style-type:disc;">数据源以及转换类DStream都会维护一个generatedRDDs，可以按batchTime 进行获取</li><li style="line-height:1.7em;list-style-type:disc;">内部本质还是进行的RDD的转换</li><li style="line-height:1.7em;list-style-type:disc;">如果我们调用了cache会发生什么</li></ul><p style="line-height:1.7em;text-indent:1em;">
这里又会有两种情况，一种是调用DStream.cache,第二种是RDD.cache。事实上他们是完全一样的。</p>
<ol style="line-height:1.7em;"><li style="line-height:1.7em;">DStream的cache 动作只是将DStream的变量storageLevel 设置为MEMORY_ONLY_SER，然后在产生(或者获取)RDD的时候，调用RDD的persit方法进行设置。所以DStream.cache 产生的效果等价于RDD.cache(也就是你自己调用foreachRDD 将RDD 都设置一遍)</li><li style="line-height:1.7em;">进入正题，我们是怎么释放Cache住的RDD的</li></ol><p style="line-height:1.7em;text-indent:1em;">
其实无所谓Cache不Cache住，RDD最终都是要释放的，否则运行久了，光RDD对象也能承包了你的内存。我们知道，在Spark Streaming中，周期性产生事件驱动Spark Streaming 的类其实是:</p>
<pre><code class="language-css"><span class="hljs-selector-tag" style="color:rgb(51,51,51);font-weight:700;">org</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.apache</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.spark</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.streaming</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.scheduler</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.JobGenerator</span> 
</code></pre>
<p style="line-height:1.7em;text-indent:1em;">
他内部有个永动机(定时器),定时发布一个产生任务的事件:</p>
<pre class="prettyprint hljs lisp" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;border:none;background-color:rgb(246,246,246);">private val timer = new RecurringTimer(<span class="hljs-name" style="color:rgb(51,51,51);font-weight:700;">clock</span>, ssc.graph.batchDuration.milliseconds, longTime =&gt; eventLoop.post(<span class="hljs-name" style="color:rgb(51,51,51);font-weight:700;">GenerateJobs</span>(<span class="hljs-name" style="color:rgb(51,51,51);font-weight:700;">new</span> Time(<span class="hljs-name" style="color:rgb(51,51,51);font-weight:700;">longTime</span>))), <span class="hljs-string" style="color:rgb(136,0,0);">"JobGenerator"</span>) 
</pre>
<p style="line-height:1.7em;text-indent:1em;">
然后通过processEvent进行事件处理：</p>
<pre class="prettyprint hljs verilog" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;border:none;background-color:rgb(246,246,246);"><span class="hljs-comment" style="color:rgb(136,136,136);">/** Processes all events */</span> 
 private def processEvent(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">event</span>: JobGeneratorEvent) { 
   logDebug(<span class="hljs-string" style="color:rgb(136,0,0);">"Got event "</span> + <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">event</span>) 
   <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">event</span> match { 
     <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">case</span> GenerateJobs(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">time</span>) =&gt; generateJobs(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">time</span>) 
     <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">case</span> ClearMetadata(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">time</span>) =&gt; clearMetadata(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">time</span>) 
     <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">case</span> DoCheckpoint(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">time</span>, clearCheckpointDataLater) =&gt; 
       doCheckpoint(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">time</span>, clearCheckpointDataLater) 
     <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">case</span> ClearCheckpointData(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">time</span>) =&gt; clearCheckpointData(<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">time</span>) 
   } 
 } 
</pre>
<p style="line-height:1.7em;text-indent:1em;">
目前我们只关注ClearMetadata 事件。对应的方法为：</p>
<pre class="prettyprint hljs coffeescript" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;border:none;background-color:rgb(246,246,246);">private def clearMetadata(time: Time) { 
    ssc.graph.clearMetadata(time) 
 
    <span class="hljs-regexp" style="color:rgb(188,96,96);">//</span> If checkpointing <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">is</span> enabled, <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">then</span> checkpoint, 
    <span class="hljs-regexp" style="color:rgb(188,96,96);">//</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">else</span> mark batch to be fully processed 
    <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">if</span> (shouldCheckpoint) { 
      eventLoop.post(DoCheckpoint(time, clearCheckpointDataLater = <span class="hljs-literal" style="color:rgb(120,169,96);">true</span>)) 
    } <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">else</span> { 
      <span class="hljs-regexp" style="color:rgb(188,96,96);">//</span> If checkpointing <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">is</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">not</span> enabled, <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">then</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">delete</span> metadata information about 
      <span class="hljs-regexp" style="color:rgb(188,96,96);">//</span> received blocks (block data <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">not</span> saved <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">in</span> any case). Otherwise, wait <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">for</span> 
      <span class="hljs-regexp" style="color:rgb(188,96,96);">//</span> checkpointing <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">of</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">this</span> batch to complete. 
      val maxRememberDuration = graph.getMaxInputStreamRememberDuration() 
      jobScheduler.receiverTracker.cleanupOldBlocksAndBatches(time - maxRememberDuration) 
      jobScheduler.inputInfoTracker.cleanup(time - maxRememberDuration) 
      markBatchFullyProcessed(time) 
    } 
  } 
</pre>
<p style="line-height:1.7em;text-indent:1em;">
首先是清理输出DStream(比如ForeachDStream),接着是清理输入类(基于Receiver模式)的数据。</p>
<p style="line-height:1.7em;text-indent:1em;">
ForeachDStream 其实调用的也是DStream的方法。该方法大体如下：</p>
<pre class="prettyprint hljs scala" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;border:none;background-color:rgb(246,246,246);"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">private</span>[streaming] <span class="hljs-function"><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">def</span><span class="hljs-title" style="color:rgb(136,0,0);font-weight:bold;"> clearMetadata</span></span>(time: <span class="hljs-type" style="color:rgb(136,0,0);">Time</span>) { 
    <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">val</span> unpersistData = ssc.conf.getBoolean(<span class="hljs-string" style="color:rgb(136,0,0);">"spark.streaming.unpersist"</span>, <span class="hljs-literal" style="color:rgb(120,169,96);">true</span>) 
    <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">val</span> oldRDDs = generatedRDDs.filter(_._1 &lt;= (time - rememberDuration)) 
    logDebug(<span class="hljs-string" style="color:rgb(136,0,0);">"Clearing references to old RDDs: ["</span> + 
      oldRDDs.map(x =&gt; <span class="hljs-string" style="color:rgb(136,0,0);">s"<span class="hljs-subst" style="color:rgb(68,68,68);">${x._1}</span> -&gt; <span class="hljs-subst" style="color:rgb(68,68,68);">${x._2.id}</span>"</span>).mkString(<span class="hljs-string" style="color:rgb(136,0,0);">", "</span>) + <span class="hljs-string" style="color:rgb(136,0,0);">"]"</span>) 
    generatedRDDs --= oldRDDs.keys 
    <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">if</span> (unpersistData) { 
      logDebug(<span class="hljs-string" style="color:rgb(136,0,0);">"Unpersisting old RDDs: "</span> + oldRDDs.values.map(_.id).mkString(<span class="hljs-string" style="color:rgb(136,0,0);">", "</span>)) 
      oldRDDs.values.foreach { rdd =&gt; 
        rdd.unpersist(<span class="hljs-literal" style="color:rgb(120,169,96);">false</span>) 
        <span class="hljs-comment" style="color:rgb(136,136,136);">// Explicitly remove blocks of BlockRDD </span>
        rdd <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">match</span> { 
          <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">case</span> b: <span class="hljs-type" style="color:rgb(136,0,0);">BlockRDD</span>[_] =&gt; 
            logInfo(<span class="hljs-string" style="color:rgb(136,0,0);">"Removing blocks of RDD "</span> + b + <span class="hljs-string" style="color:rgb(136,0,0);">" of time "</span> + time) 
            b.removeBlocks() 
          <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">case</span> _ =&gt; 
        } 
      } 
    } 
    logDebug(<span class="hljs-string" style="color:rgb(136,0,0);">"Cleared "</span> + oldRDDs.size + <span class="hljs-string" style="color:rgb(136,0,0);">" RDDs that were older than "</span> + 
      (time - rememberDuration) + <span class="hljs-string" style="color:rgb(136,0,0);">": "</span> + oldRDDs.keys.mkString(<span class="hljs-string" style="color:rgb(136,0,0);">", "</span>)) 
    dependencies.foreach(_.clearMetadata(time)) 
  } 
</pre>
<p style="line-height:1.7em;text-indent:1em;">
大体执行动作如下描述：</p>
<ol style="line-height:1.7em;"><li style="line-height:1.7em;">根据记忆周期得到应该剔除的RDD</li><li style="line-height:1.7em;">根据是否要清理cache数据，进行unpersit 操作，并且显示的移除block</li><li style="line-height:1.7em;">根据依赖调用其他的DStream进行动作清理</li></ol><p style="line-height:1.7em;text-indent:1em;">
这里我们还可以看到，通过参数spark.streaming.unpersist 你是可以决定是否手工控制是否需要对cache住的数据进行清理。</p>
<p style="line-height:1.7em;text-indent:1em;">
这里你会有两个疑问：</p>
<ol style="line-height:1.7em;"><li style="line-height:1.7em;">dependencies 是什么?</li><li style="line-height:1.7em;">rememberDuration 是怎么来的?</li></ol><p style="line-height:1.7em;text-indent:1em;">
dependencies 你可以简单理解为父DStream,通过dependencies 我们可以获得已完整DStream链。</p>
<p style="line-height:1.7em;text-indent:1em;">
rememberDuration 的设置略微复杂些,大体是 slideDuration,如果设置了checkpointDuration 则是2*checkpointDuration 或者通过DStreamGraph.rememberDuration(如果设置了的话，譬如通过StreamingContext.remember方法,不过通过该方法设置的值要大于计算得到的值会生效)</p>
<p style="line-height:1.7em;text-indent:1em;">
另外值得一提的就是后面的DStream 会调整前面的DStream的rememberDuration，譬如如果你用了window* 相关的操作，则在此之前的DStream 的rememberDuration 都需要加上windowDuration。</p>
<p style="line-height:1.7em;text-indent:1em;">
然后根据Spark Streaming的定时性，每个周期只要完成了，都会触发清理动作,这个就是清理动作发生的时机。代码如下：</p>
<pre class="prettyprint hljs less" style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;color:rgb(68,68,68);font-size:14px;line-height:1.5em;border:none;background-color:rgb(246,246,246);"><span class="hljs-selector-tag" style="color:rgb(51,51,51);font-weight:700;">def</span> <span class="hljs-selector-tag" style="color:rgb(51,51,51);font-weight:700;">onBatchCompletion</span>(<span class="hljs-attribute" style="color:rgb(51,51,51);font-weight:700;">time</span>: Time) {      
    <span class="hljs-selector-tag" style="color:rgb(51,51,51);font-weight:700;">eventLoop</span><span class="hljs-selector-class" style="color:rgb(136,0,0);">.post</span>(ClearMetadata(time)) 
} 
</pre>
<h4 style="font-family:inherit;line-height:1.7em;color:inherit;text-indent:1em;">
总结下</h4>
<p style="line-height:1.7em;text-indent:1em;">
Spark Streaming 会在每个Batch任务结束时进行一次清理动作。每个DStream 都会被扫描，不同的DStream根据情况不同，保留的RDD数量也是不一致的，但都是根据rememberDuration变量决定,而该变量会被下游的DStream所影响，所以不同的DStream的rememberDuration取值是不一样的。</p>
<p style="line-height:1.7em;text-indent:1em;">
【编辑推荐】</p>
<ol style="line-height:1.7em;"><li style="line-height:1.7em;"><a href="http://developer.51cto.com/art/201601/504887.htm" rel="nofollow" style="color:rgb(148,148,148);text-decoration:none;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(148,148,148);font-style:italic;font-weight:bold;">Spark
 Streaming 的玫瑰与刺</a></li><li style="line-height:1.7em;"><a href="http://developer.51cto.com/art/201603/506796.htm" rel="nofollow" style="color:rgb(148,148,148);text-decoration:none;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(148,148,148);font-style:italic;font-weight:bold;">Spark
 Streaming 妙用之实现工作流调度器</a></li><li style="line-height:1.7em;"><a href="http://developer.51cto.com/art/201603/507663.htm" rel="nofollow" style="color:rgb(148,148,148);text-decoration:none;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(148,148,148);font-style:italic;font-weight:bold;">借助
 Redis ，让 Spark 提速 45 倍！</a></li><li style="line-height:1.7em;"><a href="http://developer.51cto.com/art/201604/509314.htm" rel="nofollow" style="color:rgb(148,148,148);text-decoration:none;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(148,148,148);font-style:italic;font-weight:bold;">LinkedIn开源Dr.
 Elephant:Hadoop和Spark性能监控工具</a></li><li style="line-height:1.7em;"><a href="http://developer.51cto.com/art/201604/510227.htm" rel="nofollow" style="color:rgb(148,148,148);text-decoration:none;border-bottom-width:1px;border-bottom-style:dashed;border-bottom-color:rgb(148,148,148);font-style:italic;font-weight:bold;">Spark与Storm注意：Apache
 Apex已然横空出世</a></li></ol><p style="line-height:1.7em;text-indent:1em;">
 </p>
</div>
</div>
</div>
            </div>
                </div>
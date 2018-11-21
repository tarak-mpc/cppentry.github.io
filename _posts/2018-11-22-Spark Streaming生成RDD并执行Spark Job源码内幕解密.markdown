---
layout:     post
title:      Spark Streaming生成RDD并执行Spark Job源码内幕解密
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>在spark streaming程序执行之前会先执行一个spark core job，然后才是执行spark streaming job，spark core job执行的作用是：让spark调度系统启动相关的类，预分配资源，启动相关的executor。     </p>

<p>Spark Streaming可以动态的资源分配，  同时可以写多线程并发提交执行job</p>

<p>源码解析过程：</p>

<p>1、首先看的是streamingcontext 中的socketTextStream这个DStream是如何生成RDD的 <br>
2、FileInputDStream  如何生成RDD <br>
3、MappedDStream是如何依赖父DStream的RDD <br>
4、forachRDD <br>
JobGenerator类：</p>



<pre class="prettyprint"><code class=" hljs sql"> <span class="hljs-comment">/** Generate jobs and perform checkpoint for the given `time`.  */</span>
  private def generateJobs(time: Time) {
    // <span class="hljs-operator"><span class="hljs-keyword">Set</span> the SparkEnv <span class="hljs-keyword">in</span> this thread, so that job generation code can access the environment
    // Example: BlockRDDs <span class="hljs-keyword">are</span> created <span class="hljs-keyword">in</span> this thread, <span class="hljs-keyword">and</span> it needs <span class="hljs-keyword">to</span> access BlockManager
    // <span class="hljs-keyword">Update</span>: This <span class="hljs-keyword">is</span> probably redundant <span class="hljs-keyword">after</span> threadlocal stuff <span class="hljs-keyword">in</span> SparkEnv has been removed.
    SparkEnv.<span class="hljs-keyword">set</span>(ssc.env)
    Try {
      jobScheduler.receiverTracker.allocateBlocksToBatch(<span class="hljs-keyword">time</span>) // <span class="hljs-keyword">allocate</span> received blocks <span class="hljs-keyword">to</span> batch
      graph.generateJobs(<span class="hljs-keyword">time</span>) // generate jobs <span class="hljs-keyword">using</span> allocated block
    } <span class="hljs-keyword">match</span> {
      <span class="hljs-keyword">case</span> Success(jobs) =&gt;
        val streamIdToInputInfos = jobScheduler.inputInfoTracker.getInfo(<span class="hljs-keyword">time</span>)
        jobScheduler.submitJobSet(JobSet(<span class="hljs-keyword">time</span>, jobs, streamIdToInputInfos))
      <span class="hljs-keyword">case</span> Failure(e) =&gt;
        jobScheduler.reportError(<span class="hljs-string">"Error generating jobs for time "</span> + <span class="hljs-keyword">time</span>, e)
    }
    eventLoop.post(DoCheckpoint(<span class="hljs-keyword">time</span>, clearCheckpointDataLater = <span class="hljs-keyword">false</span>))
  }</span></code></pre>

<p>DStreamGraph类：</p>



<pre class="prettyprint"><code class=" hljs python"> <span class="hljs-function"><span class="hljs-keyword">def</span> <span class="hljs-title">generateJobs</span><span class="hljs-params">(time: Time)</span>:</span> Seq[Job] = {
    logDebug(<span class="hljs-string">"Generating jobs for time "</span> + time)
    val jobs = this.synchronized {
      outputStreams.flatMap { outputStream =&gt;
        val jobOption = outputStream.generateJob(time)
        jobOption.foreach(_.setCallSite(outputStream.creationSite))
        jobOption
      }
    }
    logDebug(<span class="hljs-string">"Generated "</span> + jobs.length + <span class="hljs-string">" jobs for time "</span> + time)
    jobs
  }</code></pre>

<p>DStream类：</p>



<pre class="prettyprint"><code class=" hljs coffeescript">  /**
   * Generate a SparkStreaming job <span class="hljs-keyword">for</span> the given time. This <span class="hljs-keyword">is</span> an internal method that
   * should <span class="hljs-keyword">not</span> be called directly. This <span class="hljs-reserved">default</span> implementation creates a job
   * that materializes the corresponding RDD. Subclasses <span class="hljs-keyword">of</span> DStream may override <span class="hljs-keyword">this</span>
   * to generate their own jobs.
   */
  private[streaming] def generateJob(<span class="hljs-attribute">time</span>: Time): Option[Job] = {
    getOrCompute(time) match {
      <span class="hljs-reserved">case</span> Some<span class="hljs-function"><span class="hljs-params">(rdd)</span> =&gt;</span> {
        val <span class="hljs-function"><span class="hljs-title">jobFunc</span> = <span class="hljs-params">()</span> =&gt;</span> {
          val emptyFunc = { <span class="hljs-function"><span class="hljs-params">(iterator: Iterator[T])</span> =&gt;</span> {} }
          context.sparkContext.runJob(rdd, emptyFunc)
        }
        Some(<span class="hljs-keyword">new</span> Job(time, jobFunc))
      }
      <span class="hljs-reserved">case</span> None<span class="hljs-function"> =&gt;</span> None
    }
  }</code></pre>

<p>在getOrCompute调用DStream的compute方法</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
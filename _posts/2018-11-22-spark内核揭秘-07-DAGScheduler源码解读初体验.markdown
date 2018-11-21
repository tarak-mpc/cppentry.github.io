---
layout:     post
title:      spark内核揭秘-07-DAGScheduler源码解读初体验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/stark_summer/article/details/42876537				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>当构建完TaskScheduler之后，我们需要构建DAGScheduler这个<span style="color:#ff0000;">核心</span>对象：</p>
<p><img src="https://img-blog.csdn.net/20150119182542265?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>进入其构造函数中：</p>
<p><img src="https://img-blog.csdn.net/20150119182757315?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150119183920112?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150119183914156?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20150119184022887?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>可以看出构建DAGScheduler实例的时候需要把TaskScheduler实例对象作为参数传入。<br></p>
<p><span style="color:#ff0000;">LiveListenerBus：</span></p>
<p><img src="https://img-blog.csdn.net/20150119184220230?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">MapOutputTrackerMaster：</span></p>
<p><img src="https://img-blog.csdn.net/20150119184345750?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><span style="color:#ff0000;">BlockManagerMaster：</span></p>
<p><img src="https://img-blog.csdn.net/20150119184452468?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Rhcmtfc3VtbWVy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>通过阅读代码，我们可以发现DAGScheduler实例化的时候，调用了initializeEventProcessActor()方法</p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'宋体';font-size:22pt;"><span style="color:#cc7832;"><strong>private def </strong></span><span style="color:#ffc66d;">initializeEventProcessActor</span>() {
  <span style="color:#808080;">// blocking the thread until supervisor is started, which ensures eventProcessActor is
</span><span style="color:#808080;">  // not null before any job is submitted
</span><span style="color:#808080;">  // 阻塞当前线程，等待supervisor启动，这样可以确保Job提交时，eventProcessActor not null
</span><span style="color:#808080;">  </span><span style="color:#cc7832;"><strong>implicit val </strong></span>timeout = <span style="font-style:italic;">Timeout</span>(<span style="color:#6897bb;">30 </span>seconds)
  <span style="color:#cc7832;"><strong>val </strong></span>initEventActorReply =
    <span style="color:#9876aa;"><em>dagSchedulerActorSupervisor </em></span>? <span style="font-style:italic;">Props</span>(<span style="color:#cc7832;"><strong>new </strong></span>DAGSchedulerEventProcessActor(<span style="color:#cc7832;"><strong>this</strong></span>))
  <span style="color:#9876aa;"><em>eventProcessActor </em></span>= Await.<span style="font-style:italic;">result</span>(initEventActorReply<span style="color:#cc7832;">, </span>timeout.duration).
    asInstanceOf[ActorRef]
}

initializeEventProcessActor()</pre>
<span style="color:#ff0000;">DAGSchedulerEventProcessActor：</span>
<p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'宋体';font-size:22pt;"><span style="color:#cc7832;"><strong>private</strong></span>[scheduler] <span style="color:#cc7832;"><strong>class </strong></span>DAGSchedulerEventProcessActor(dagScheduler: DAGScheduler)
  <span style="color:#cc7832;"><strong>extends </strong></span>Actor <span style="color:#cc7832;"><strong>with </strong></span>Logging {

  <span style="color:#cc7832;"><strong>override def </strong></span><span style="color:#ffc66d;">preStart</span>() {
    <span style="color:#808080;">// set DAGScheduler for taskScheduler to ensure eventProcessActor is always
</span><span style="color:#808080;">    // valid when the messages arrive
</span><span style="color:#808080;">    // 设置taskScheduler对DAGScheduler的引用句柄。在此处设置保证了Job提交时候
</span><span style="color:#808080;">    // eventProcessActor已经准备就绪
</span><span style="color:#808080;">    </span>dagScheduler.taskScheduler.setDAGScheduler(dagScheduler)
  }

  <span style="color:#629755;"><em>/**
</em></span><span style="color:#629755;"><em>   * The main event loop of the DAG scheduler.
</em></span><span style="color:#629755;"><em>   */
</em></span><span style="color:#629755;"><em>  </em></span><span style="color:#cc7832;"><strong>def </strong></span><span style="color:#ffc66d;">receive </span>= {
    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">JobSubmitted</span>(jobId<span style="color:#cc7832;">, </span>rdd<span style="color:#cc7832;">, </span>func<span style="color:#cc7832;">, </span>partitions<span style="color:#cc7832;">, </span>allowLocal<span style="color:#cc7832;">, </span>callSite<span style="color:#cc7832;">, </span>listener<span style="color:#cc7832;">, </span>properties) =&gt;
      dagScheduler.handleJobSubmitted(jobId<span style="color:#cc7832;">, </span>rdd<span style="color:#cc7832;">, </span>func<span style="color:#cc7832;">, </span>partitions<span style="color:#cc7832;">, </span>allowLocal<span style="color:#cc7832;">, </span>callSite<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">        </span>listener<span style="color:#cc7832;">, </span>properties)

    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">StageCancelled</span>(stageId) =&gt;
      dagScheduler.handleStageCancellation(stageId)

    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">JobCancelled</span>(jobId) =&gt;
      dagScheduler.handleJobCancellation(jobId)

    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">JobGroupCancelled</span>(groupId) =&gt;
      dagScheduler.handleJobGroupCancelled(groupId)

    <span style="color:#cc7832;"><strong>case </strong></span>AllJobsCancelled =&gt;
      dagScheduler.doCancelAllJobs()

    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">ExecutorAdded</span>(execId<span style="color:#cc7832;">, </span>host) =&gt;
      dagScheduler.handleExecutorAdded(execId<span style="color:#cc7832;">, </span>host)

    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">ExecutorLost</span>(execId) =&gt;
      dagScheduler.handleExecutorLost(execId<span style="color:#cc7832;">, </span>fetchFailed = <span style="color:#cc7832;"><strong>false</strong></span>)

    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">BeginEvent</span>(task<span style="color:#cc7832;">, </span>taskInfo) =&gt;
      dagScheduler.handleBeginEvent(task<span style="color:#cc7832;">, </span>taskInfo)

    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">GettingResultEvent</span>(taskInfo) =&gt;
      dagScheduler.handleGetTaskResult(taskInfo)

    <span style="color:#cc7832;"><strong>case </strong></span>completion @ <span style="font-style:italic;">CompletionEvent</span>(task<span style="color:#cc7832;">, </span>reason<span style="color:#cc7832;">, </span>_<span style="color:#cc7832;">, </span>_<span style="color:#cc7832;">, </span>taskInfo<span style="color:#cc7832;">, </span>taskMetrics) =&gt;
      dagScheduler.handleTaskCompletion(completion)

    <span style="color:#cc7832;"><strong>case </strong></span><span style="font-style:italic;">TaskSetFailed</span>(taskSet<span style="color:#cc7832;">, </span>reason) =&gt;
      dagScheduler.handleTaskSetFailed(taskSet<span style="color:#cc7832;">, </span>reason)

    <span style="color:#cc7832;"><strong>case </strong></span>ResubmitFailedStages =&gt;
      dagScheduler.resubmitFailedStages()
  }

  <span style="color:#cc7832;"><strong>override def </strong></span><span style="color:#ffc66d;">postStop</span>() {
    <span style="color:#808080;">// Cancel any active jobs in postStop hook
</span><span style="color:#808080;">    </span>dagScheduler.cleanUpAfterSchedulerStop()
  }
}</pre>
<br><p></p>
<p>可以看出核心在于实例化eventProcessActor对象，eventProcessActor会负责接收和发送DAGScheduler的消息，是DAGScheduler的通信载体。<br></p>
<p><br></p>
            </div>
                </div>
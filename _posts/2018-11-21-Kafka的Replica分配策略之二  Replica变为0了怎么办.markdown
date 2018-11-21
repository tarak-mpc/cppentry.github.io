---
layout:     post
title:      Kafka的Replica分配策略之二  Replica变为0了怎么办
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_21519863/article/details/78575220				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><code class="language-plain">这一篇文章准备讨论当kafka集群的broker发生变化,诸如broker崩溃,退出时,kafka集群会如何分配该broker上的Replica和Partition.
</code></pre>
<p>在讨论这个问题之前,需要先搞清kafka集群中,leader与follower的分工.可以看我写的这篇文章<a href="http://blog.csdn.net/qq_21519863/article/details/78574614" rel="nofollow"> Kafka的leader选举过程</a></p>
<p>在之前介绍kafka的选举过程时,提到成功选举出的leader会向zookeeper注册各种监视其中</p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#9876aa;font-style:italic;">replicaStateMachine</span>.registerListeners()    <span style="color:#808080;">//"/brokers/ids" </span><span style="font-family:'AR PL UKai CN';color:#808080;">重点</span><span style="color:#808080;">,</span><span style="font-family:'AR PL UKai CN';color:#808080;">监视所有的</span><span style="color:#808080;">follower</span><span style="font-family:'AR PL UKai CN';color:#808080;">的加入</span><span style="color:#808080;">,</span><span style="font-family:'AR PL UKai CN';color:#808080;">离开集群的行为</span></pre>
这一句注册了对/brokers/ids的监视,跟进这句命令
<p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">private def </span><span style="background-color:#344134;">registerBrokerChangeListener</span>() = {
  <span style="color:#9876aa;font-style:italic;">zkUtils</span>.zkClient.subscribeChildChanges(ZkUtils.<span style="color:#9876aa;font-style:italic;">BrokerIdsPath</span><span style="color:#cc7832;">, </span><span style="color:#9876aa;font-style:italic;">brokerChangeListener</span>)
}</pre>
来到了这里,ZkUtils.BrokerIdsPath就是/brokers/ids这个路径,那由此可以得知,重点就在brokerChangeListener上.这个listener被定义在kafka.controller包的ReplicaStateMachine.scala下.
<p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">class </span>BrokerChangeListener() <span style="color:#cc7832;font-weight:bold;">extends </span>IZkChildListener <span style="color:#cc7832;font-weight:bold;">with </span>Logging {
  <span style="color:#cc7832;font-weight:bold;">this</span>.<span style="color:#9876aa;font-style:italic;">logIdent </span>= <span style="color:#6a8759;">"[BrokerChangeListener on Controller " </span>+ controller.config.<span style="color:#9876aa;font-style:italic;">brokerId </span>+ <span style="color:#6a8759;">"]: "
</span><span style="color:#6a8759;">  </span><span style="color:#cc7832;font-weight:bold;">def </span><span style="color:#ffc66d;">handleChildChange</span>(parentPath : <span style="color:#4e807d;">String</span><span style="color:#cc7832;">, </span>currentBrokerList : java.util.List[<span style="color:#4e807d;">String</span>]) {
    info(<span style="color:#6a8759;">"Broker change listener fired for path %s with children %s"</span>.format(parentPath<span style="color:#cc7832;">, </span>currentBrokerList.sorted.mkString(<span style="color:#6a8759;">","</span>)))
    <span style="font-style:italic;">inLock</span>(<span style="color:#9876aa;font-style:italic;">controllerContext</span>.<span style="color:#9876aa;font-style:italic;">controllerLock</span>) {
      <span style="color:#cc7832;font-weight:bold;">if </span>(<span style="color:#9876aa;font-style:italic;">hasStarted</span>.get) {
        ControllerStats.<span style="font-style:italic;">leaderElectionTimer</span>.time {
          <span style="color:#cc7832;font-weight:bold;">try </span>{
            <span style="color:#cc7832;font-weight:bold;">val </span>curBrokers = currentBrokerList.map(_.toInt).toSet.flatMap(<span style="color:#9876aa;font-style:italic;">zkUtils</span>.getBrokerInfo)
            <span style="color:#cc7832;font-weight:bold;">val </span>curBrokerIds = curBrokers.map(_.id)
            <span style="color:#cc7832;font-weight:bold;">val </span>liveOrShuttingDownBrokerIds = <span style="color:#9876aa;font-style:italic;">controllerContext</span>.liveOrShuttingDownBrokerIds
            <span style="color:#cc7832;font-weight:bold;">val </span>newBrokerIds = curBrokerIds -- liveOrShuttingDownBrokerIds
            <span style="color:#cc7832;font-weight:bold;">val </span>deadBrokerIds = liveOrShuttingDownBrokerIds -- curBrokerIds
            <span style="color:#cc7832;font-weight:bold;">val </span>newBrokers = curBrokers.filter(broker =&gt; newBrokerIds(broker.id))
            <span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">上面几句很好理解</span><span style="color:#808080;">,</span><span style="font-family:'AR PL UKai CN';color:#808080;">筛选出新加入的</span><span style="color:#808080;">broker,</span><span style="font-family:'AR PL UKai CN';color:#808080;">与退出的</span><span style="color:#808080;">broker
</span><span style="color:#808080;">            </span><span style="color:#9876aa;font-style:italic;">controllerContext</span>.liveBrokers = curBrokers
            <span style="color:#cc7832;font-weight:bold;">val </span>newBrokerIdsSorted = newBrokerIds.toSeq.sorted
            <span style="color:#cc7832;font-weight:bold;">val </span>deadBrokerIdsSorted = deadBrokerIds.toSeq.sorted
            <span style="color:#cc7832;font-weight:bold;">val </span>liveBrokerIdsSorted = curBrokerIds.toSeq.sorted
            info(<span style="color:#6a8759;">"Newly added brokers: %s, deleted brokers: %s, all live brokers: %s"
</span><span style="color:#6a8759;">              </span>.format(newBrokerIdsSorted.mkString(<span style="color:#6a8759;">","</span>)<span style="color:#cc7832;">, </span>deadBrokerIdsSorted.mkString(<span style="color:#6a8759;">","</span>)<span style="color:#cc7832;">, </span>liveBrokerIdsSorted.mkString(<span style="color:#6a8759;">","</span>)))
            newBrokers.foreach(<span style="color:#9876aa;font-style:italic;">controllerContext</span>.<span style="color:#9876aa;font-style:italic;">controllerChannelManager</span>.addBroker)
            deadBrokerIds.foreach(<span style="color:#9876aa;font-style:italic;">controllerContext</span>.<span style="color:#9876aa;font-style:italic;">controllerChannelManager</span>.removeBroker)
            <span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">上面两句维护存有</span><span style="color:#808080;">broker</span><span style="font-family:'AR PL UKai CN';color:#808080;">信息的</span><span style="color:#808080;">map
</span><span style="color:#808080;">            </span><span style="color:#cc7832;font-weight:bold;">if</span>(newBrokerIds.nonEmpty)
              controller.onBrokerStartup(newBrokerIdsSorted)
            <span style="color:#cc7832;font-weight:bold;">if</span>(deadBrokerIds.nonEmpty)
              <span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">这一句是重点</span><span style="color:#808080;">,</span><span style="font-family:'AR PL UKai CN';color:#808080;">如何处理</span><span style="color:#808080;">fail</span><span style="font-family:'AR PL UKai CN';color:#808080;">的</span><span style="color:#808080;">broker
</span><span style="color:#808080;">              </span>controller.onBrokerFailure(deadBrokerIdsSorted)
          } <span style="color:#cc7832;font-weight:bold;">catch </span>{
            <span style="color:#cc7832;font-weight:bold;">case </span>e: <span style="color:#4e807d;">Throwable </span>=&gt; error(<span style="color:#6a8759;">"Error while handling broker changes"</span><span style="color:#cc7832;">, </span>e)
          }
        }
      }
    }
  }
}</pre>
<p>可以看到,针对broker变化这一情况.Kafka controller从znode节点的变化,推测出了新加入与新离开的节点.对于离开的节点调用了onBrokerFailure函数.</p>
<p>继续跟进这里只截取了部分onBrokerFailure的源码,一段一段来分析.</p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">def onBrokerFailure(deadBrokers: Seq[Int]){
.....
....
</span><span style="color:#cc7832;font-weight:bold;"></span><span style="color:#cc7832;font-weight:bold;">val </span>deadBrokersSet = deadBrokers.toSet
<span style="color:#808080;">// trigger OfflinePartition state for all partitions whose current leader is one amongst the dead brokers
</span><span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">筛选出</span><span style="color:#808080;">dead</span><span style="font-family:'AR PL UKai CN';color:#808080;">的</span><span style="color:#808080;">broker</span><span style="font-family:'AR PL UKai CN';color:#808080;">中所有担任</span><span style="color:#808080;">partition leader</span><span style="font-family:'AR PL UKai CN';color:#808080;">的</span><span style="color:#808080;">broker
</span><span style="color:#cc7832;font-weight:bold;">val </span>partitionsWithoutLeader = <span style="color:#9876aa;font-style:italic;">controllerContext</span>.<span style="color:#9876aa;font-style:italic;">partitionLeadershipInfo</span>.filter(partitionAndLeader =&gt;
  deadBrokersSet.contains(partitionAndLeader._2.leaderAndIsr.leader) &amp;&amp;
    !<span style="color:#9876aa;font-style:italic;">deleteTopicManager</span>.isTopicQueuedUpForDeletion(partitionAndLeader._1.topic)).keySet
<span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">对于这些</span><span style="color:#808080;">leader</span><span style="font-family:'AR PL UKai CN';color:#808080;">挂掉的</span><span style="color:#808080;">partition,</span><span style="font-family:'AR PL UKai CN';color:#808080;">进行处理
</span><span style="color:#9876aa;font-style:italic;">partitionStateMachine</span>.handleStateChanges(partitionsWithoutLeader<span style="color:#cc7832;">, </span>OfflinePartition)</pre>
我们看到这一段中,提取出了所有担任partition leader的broker,这些broker下线了,是要做特殊处理的.也就是handleStateChanges函数,跟进这个函数
<p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;"></span></pre><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">def </span><span style="background-color:#344134;">handleStateChanges</span>(partitions: Set[TopicAndPartition]<span style="color:#cc7832;">, </span>targetState: PartitionState<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">                       </span>leaderSelector: PartitionLeaderSelector = <span style="color:#9876aa;font-style:italic;">noOpPartitionLeaderSelector</span><span style="color:#cc7832;">,
</span><span style="color:#cc7832;">                       </span>callbacks: Callbacks = (<span style="color:#cc7832;font-weight:bold;">new </span>CallbackBuilder).build) {
....
....
</pre>case OfflinePartition =&gt;  <span style="color:#808080;">// pre: partition should be in New or Online state</span><span style="color:#808080;">  </span><span style="background-color:#344134;">assertValidPreviousStates</span>(topicAndPartition<span style="color:#cc7832;">, </span><span style="color:#9876aa;font-style:italic;">List</span>(NewPartition<span style="color:#cc7832;">, </span>OnlinePartition<span style="color:#cc7832;">, </span>OfflinePartition)<span style="color:#cc7832;">, </span>OfflinePartition)  <span style="color:#808080;">// should be called when the leader for a partition is no longer alive</span><span style="color:#808080;">  </span><span style="color:#9876aa;font-style:italic;">stateChangeLogger</span>.trace(<span style="color:#6a8759;">"Controller %d epoch %d changed partition %s state from %s to %s"</span><span style="color:#6a8759;">                            </span>.format(<span style="color:#9876aa;font-style:italic;">controllerId</span><span style="color:#cc7832;">, </span>controller.epoch<span style="color:#cc7832;">, </span>topicAndPartition<span style="color:#cc7832;">, </span>currState<span style="color:#cc7832;">, </span>targetState))  <span style="color:#9876aa;font-style:italic;">partitionState</span>.put(topicAndPartition<span style="color:#cc7832;">, </span>OfflinePartition)
这个函数中,针对offline的情况仅仅是在partitionState这个HashMap中,将这个partion的状态值为offline了.其他没有任何改变,如果你也看了源码,会发现之后会有sendRequestToBrokers的调用.但是在之前的处理中,既没有改变metaInfo,也没有改变leaderShip所以没有什么可以通知follower broker的,这个sendRequestToBrokers()可以说什么都没有做.
<p></p>
<p>接下来回到OnBrokerFailure()上,看看接下来做了什么?</p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">def </span><span style="color:#ffc66d;">onBrokerFailure</span>(deadBrokers: Seq[<span style="color:#cc7832;">Int</span>]) {
...
...
</pre><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#9876aa;font-style:italic;">    partitionStateMachine</span>.triggerOnlinePartitionStateChange()</pre><br>
调用了triggerOnlinePartitionStateChange()函数
<p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">def </span><span style="color:#ffc66d;">triggerOnlinePartitionStateChange</span>() {
  <span style="color:#cc7832;font-weight:bold;">try </span>{
    <span style="color:#9876aa;font-style:italic;">brokerRequestBatch</span>.newBatch()
    <span style="color:#808080;">// try to move all partitions in NewPartition or OfflinePartition state to OnlinePartition state except partitions
</span><span style="color:#808080;">    // that belong to topics to be deleted
</span><span style="color:#808080;">    </span><span style="color:#cc7832;font-weight:bold;">for</span>((topicAndPartition<span style="color:#cc7832;">, </span>partitionState) &lt;- <span style="color:#9876aa;font-style:italic;">partitionState
</span><span style="color:#9876aa;font-style:italic;">        </span><span style="color:#cc7832;font-weight:bold;">if </span>!controller.<span style="color:#9876aa;font-style:italic;">deleteTopicManager</span>.isTopicQueuedUpForDeletion(topicAndPartition.topic)) {
      <span style="color:#cc7832;font-weight:bold;">if</span>(partitionState.equals(OfflinePartition) || partitionState.equals(NewPartition))
        <span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">这里把</span><span style="color:#808080;">offline</span><span style="font-family:'AR PL UKai CN';color:#808080;">和</span><span style="color:#808080;">new</span><span style="font-family:'AR PL UKai CN';color:#808080;">的情况筛选出来了</span><span style="color:#808080;">,</span><span style="font-family:'AR PL UKai CN';color:#808080;">为什么不直接作为参数传入呢</span><span style="color:#808080;">?</span><span style="font-family:'AR PL UKai CN';color:#808080;">感觉怪怪的</span><span style="color:#808080;">.
</span><span style="color:#808080;">        //</span><span style="font-family:'AR PL UKai CN';color:#808080;">针对</span><span style="color:#808080;">broker down</span><span style="font-family:'AR PL UKai CN';color:#808080;">的情况</span><span style="color:#808080;">,</span><span style="font-family:'AR PL UKai CN';color:#808080;">里边的核心其实就是这一句</span><span style="color:#808080;">partitionState.put(topicAndPartition, OfflinePartition)
</span><span style="color:#808080;">        //</span><span style="font-family:'AR PL UKai CN';color:#808080;">但是这里的这个</span><span style="color:#808080;">target Position</span><span style="font-family:'AR PL UKai CN';color:#808080;">变成了</span><span style="color:#808080;">OnlinePartition</span><span style="font-family:'AR PL UKai CN';color:#808080;">了</span><span style="color:#808080;">!!!!!!
</span><span style="color:#808080;">        </span>handleStateChange(topicAndPartition.topic<span style="color:#cc7832;">, </span>topicAndPartition.partition<span style="color:#cc7832;">, </span>OnlinePartition<span style="color:#cc7832;">, </span>controller.<span style="color:#9876aa;font-style:italic;">offlinePartitionSelector</span><span style="color:#cc7832;">,
</span><span style="color:#cc7832;">                          </span>(<span style="color:#cc7832;font-weight:bold;">new </span>CallbackBuilder).<span style="background-color:#344134;">build</span>)
    }
    <span style="color:#9876aa;font-style:italic;">brokerRequestBatch</span>.sendRequestsToBrokers(controller.epoch)</pre>
有没有看到和之前的handleStateChanges很相似,没错,这两个handleStateChange调用的是同一个函数.但是,参数有变化了,看到那个OnlinePatition了嘛?之前在hashmap中,将该topic标记为下线,现在重新处理将这个topic上线.
<p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;"></span></pre><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;"></span></pre><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">def </span><span style="background-color:#344134;">handleStateChanges</span>(partitions: Set[TopicAndPartition]<span style="color:#cc7832;">, </span>targetState: PartitionState<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">                       </span>leaderSelector: PartitionLeaderSelector = <span style="color:#9876aa;font-style:italic;">noOpPartitionLeaderSelector</span><span style="color:#cc7832;">,
</span><span style="color:#cc7832;">                       </span>callbacks: Callbacks = (<span style="color:#cc7832;font-weight:bold;">new </span>CallbackBuilder).build) {
....
....</pre>case OnlinePartition =&gt;  assertValidPreviousStates(topicAndPartition<span style="color:#cc7832;">, </span><span style="color:#9876aa;font-style:italic;">List</span>(NewPartition<span style="color:#cc7832;">, </span>OnlinePartition<span style="color:#cc7832;">, </span>OfflinePartition)<span style="color:#cc7832;">, </span>OnlinePartition)  <span style="color:#9876aa;font-style:italic;">partitionState</span>(topicAndPartition) <span style="color:#cc7832;font-weight:bold;">match </span>{    <span style="color:#cc7832;font-weight:bold;">case </span>NewPartition =&gt;      <span style="color:#808080;">// initialize leader and isr path for new partition</span><span style="color:#808080;">      </span>initializeLeaderAndIsrForPartition(topicAndPartition)    <span style="color:#cc7832;font-weight:bold;">case </span>OfflinePartition =&gt;      electLeaderForPartition(topic<span style="color:#cc7832;">, </span>partition<span style="color:#cc7832;">, </span>leaderSelector)    <span style="color:#cc7832;font-weight:bold;">case </span>OnlinePartition =&gt; <span style="color:#808080;">// invoked when the leader needs to be re-elected</span><span style="color:#808080;">      </span>electLeaderForPartition(topic<span style="color:#cc7832;">, </span>partition<span style="color:#cc7832;">, </span>leaderSelector)    <span style="color:#cc7832;font-weight:bold;">case </span>_ =&gt; <span style="color:#808080;">// should never come here since illegal previous states are checked above</span><span style="color:#808080;">  </span>}
由于之前的改变,现在我们进入了OfflinePartion分支,看到了令人激动的函数有没有?就是electLeaderForPartion函数,重新选则partition的leader.如果觉得这个选举函数太长就可以只关注两句,就是我在上边写注释,重点的那两句.<br><p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">class </span>OfflinePartitionLeaderSelector(controllerContext: ControllerContext<span style="color:#cc7832;">, </span>config: KafkaConfig)
  <span style="color:#cc7832;font-weight:bold;">extends </span>PartitionLeaderSelector <span style="color:#cc7832;font-weight:bold;">with </span>Logging {
  <span style="color:#cc7832;font-weight:bold;">this</span>.<span style="color:#9876aa;font-style:italic;">logIdent </span>= <span style="color:#6a8759;">"[OfflinePartitionLeaderSelector]: "
</span><span style="color:#6a8759;">
</span><span style="color:#6a8759;">  </span><span style="color:#cc7832;font-weight:bold;">def </span><span style="color:#ffc66d;">selectLeader</span>(topicAndPartition: TopicAndPartition<span style="color:#cc7832;">, </span>currentLeaderAndIsr: LeaderAndIsr): (LeaderAndIsr<span style="color:#cc7832;">, </span><span style="color:#4e807d;">Seq</span>[<span style="color:#cc7832;">Int</span>]) = {
    controllerContext.<span style="color:#9876aa;font-style:italic;">partitionReplicaAssignment</span>.get(topicAndPartition) <span style="color:#cc7832;font-weight:bold;">match </span>{
      <span style="color:#cc7832;font-weight:bold;">case </span><span style="font-style:italic;">Some</span>(assignedReplicas) =&gt;
        <span style="color:#cc7832;font-weight:bold;">val </span>liveAssignedReplicas = assignedReplicas.filter(r =&gt; controllerContext.liveBrokerIds.contains(r))
        <span style="color:#cc7832;font-weight:bold;">val </span>liveBrokersInIsr = currentLeaderAndIsr.isr.filter(r =&gt; controllerContext.liveBrokerIds.contains(r))
        <span style="color:#cc7832;font-weight:bold;">val </span>currentLeaderEpoch = currentLeaderAndIsr.leaderEpoch
        <span style="color:#cc7832;font-weight:bold;">val </span>currentLeaderIsrZkPathVersion = currentLeaderAndIsr.zkVersion
        <span style="color:#cc7832;font-weight:bold;">val </span>newLeaderAndIsr =
          <span style="color:#cc7832;font-weight:bold;">if </span>(liveBrokersInIsr.isEmpty) {
            <span style="color:#808080;">// Prior to electing an unclean (i.e. non-ISR) leader, ensure that doing so is not disallowed by the configuration
</span><span style="color:#808080;">            // for unclean leader election.
</span><span style="color:#808080;">            </span><span style="color:#cc7832;font-weight:bold;">if </span>(!LogConfig.<span style="font-style:italic;">fromProps</span>(config.originals<span style="color:#cc7832;">, </span>AdminUtils.<span style="font-style:italic;">fetchEntityConfig</span>(controllerContext.zkUtils<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">              </span>ConfigType.<span style="color:#9876aa;font-style:italic;">Topic</span><span style="color:#cc7832;">, </span>topicAndPartition.topic)).<span style="color:#9876aa;font-style:italic;">uncleanLeaderElectionEnable</span>) {
              <span style="color:#cc7832;font-weight:bold;">throw new </span>NoReplicaOnlineException((<span style="color:#6a8759;">"No broker in ISR for partition " </span>+
                <span style="color:#6a8759;">"%s is alive. Live brokers are: [%s],"</span>.format(topicAndPartition<span style="color:#cc7832;">, </span>controllerContext.liveBrokerIds)) +
                <span style="color:#6a8759;">" ISR brokers are: [%s]"</span>.format(currentLeaderAndIsr.isr.mkString(<span style="color:#6a8759;">","</span>)))
            }
            debug(<span style="color:#6a8759;">"No broker in ISR is alive for %s. Pick the leader from the alive assigned replicas: %s"
</span><span style="color:#6a8759;">              </span>.format(topicAndPartition<span style="color:#cc7832;">, </span>liveAssignedReplicas.mkString(<span style="color:#6a8759;">","</span>)))
            <span style="color:#cc7832;font-weight:bold;">if </span>(liveAssignedReplicas.isEmpty) {
              <span style="color:#cc7832;font-weight:bold;">throw new </span>NoReplicaOnlineException((<span style="color:#6a8759;">"No replica for partition " </span>+
                <span style="color:#6a8759;">"%s is alive. Live brokers are: [%s],"</span>.format(topicAndPartition<span style="color:#cc7832;">, </span>controllerContext.liveBrokerIds)) +
                <span style="color:#6a8759;">" Assigned replicas are: [%s]"</span>.format(assignedReplicas))
            } <span style="color:#cc7832;font-weight:bold;">else </span>{
              ControllerStats.<span style="font-style:italic;">uncleanLeaderElectionRate</span>.mark()
              <span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">重点就是这么简单</span><span style="color:#808080;">,liveAssignedReplicas.head
</span><span style="color:#808080;">              </span><span style="color:#cc7832;font-weight:bold;">val </span>newLeader = liveAssignedReplicas.head
              warn(<span style="color:#6a8759;">"No broker in ISR is alive for %s. Elect leader %d from live brokers %s. There's potential data loss."
</span><span style="color:#6a8759;">                </span>.format(topicAndPartition<span style="color:#cc7832;">, </span>newLeader<span style="color:#cc7832;">, </span>liveAssignedReplicas.mkString(<span style="color:#6a8759;">","</span>)))
              <span style="color:#cc7832;font-weight:bold;">new </span>LeaderAndIsr(newLeader<span style="color:#cc7832;">, </span>currentLeaderEpoch + <span style="color:#6897bb;">1</span><span style="color:#cc7832;">, </span><span style="color:#9876aa;font-style:italic;">List</span>(newLeader)<span style="color:#cc7832;">, </span>currentLeaderIsrZkPathVersion + <span style="color:#6897bb;">1</span>)
            }
          } <span style="color:#cc7832;font-weight:bold;">else </span>{
            <span style="color:#cc7832;font-weight:bold;">val </span>liveReplicasInIsr = liveAssignedReplicas.filter(r =&gt; liveBrokersInIsr.contains(r))
            <span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">重点就是这么简单</span><span style="color:#808080;">,liveReplicasInIsr.head
</span><span style="color:#808080;">            </span><span style="color:#cc7832;font-weight:bold;">val </span>newLeader = liveReplicasInIsr.head
            debug(<span style="color:#6a8759;">"Some broker in ISR is alive for %s. Select %d from ISR %s to be the leader."
</span><span style="color:#6a8759;">              </span>.format(topicAndPartition<span style="color:#cc7832;">, </span>newLeader<span style="color:#cc7832;">, </span>liveBrokersInIsr.mkString(<span style="color:#6a8759;">","</span>)))
            <span style="color:#cc7832;font-weight:bold;">new </span>LeaderAndIsr(newLeader<span style="color:#cc7832;">, </span>currentLeaderEpoch + <span style="color:#6897bb;">1</span><span style="color:#cc7832;">, </span>liveBrokersInIsr.toList<span style="color:#cc7832;">, </span>currentLeaderIsrZkPathVersion + <span style="color:#6897bb;">1</span>)
          }
        info(<span style="color:#6a8759;">"Selected new leader and ISR %s for offline partition %s"</span>.format(newLeaderAndIsr.toString()<span style="color:#cc7832;">, </span>topicAndPartition))
        (newLeaderAndIsr<span style="color:#cc7832;">, </span>liveAssignedReplicas)
      <span style="color:#cc7832;font-weight:bold;">case </span>None =&gt;
        <span style="color:#cc7832;font-weight:bold;">throw new </span>NoReplicaOnlineException(<span style="color:#6a8759;">"Partition %s doesn't have replicas assigned to it"</span>.format(topicAndPartition))
    }
  }
}</pre>
重新选举的逻辑很简单,有没有同步的ISR?有的话,从ISR列表里拿一个出来.如果没有同步的ISR,那有没有不同步但是仍然运行的replica,有的话从存活repilica列表里拿一个出来.同时发出警告:"there is potential data loss"-&gt;"有数据丢失的可能".连活的replica都没有?那么只能throw exception了.
<p></p>
<p>OK,那么现在partition的leader选举完了,该处理replica的关系了.看一下kafka源码的结构,可以知道每个leader controller维护了partitionStateMachine以及ReplicaStateMachine两种状态机.这可以看作是面向对象的抽象,把每个partition与replica都抽象成一个类.上面这些分析都是在partitionStateMachine里进行的.</p>
<p>回到一开始的OnBrokerFailure()</p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;"></span></pre><pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">def </span><span style="color:#ffc66d;">onBrokerFailure</span>(deadBrokers: Seq[<span style="color:#cc7832;">Int</span>]) {
....
....
</pre>var allReplicasOnDeadBrokers = <span style="color:#9876aa;font-style:italic;">controllerContext</span>.replicasOnBrokers(deadBrokersSet)<span style="color:#cc7832;font-weight:bold;"><br>val </span>activeReplicasOnDeadBrokers = allReplicasOnDeadBrokers.filterNot(p =&gt; <span style="color:#9876aa;font-style:italic;">deleteTopicManager</span>.isTopicQueuedUpForDeletion(p.topic))<span style="color:#808080;">// handle dead replicas</span><span style="color:#9876aa;font-style:italic;"><br>replicaStateMachine</span>.handleStateChanges(activeReplicasOnDeadBrokers<span style="color:#cc7832;">, </span>OfflineReplica)
如我刚才所讲,现在开始处理受影响的replica的状态机,target state是OfflineReplica,来到相应的case分支
<p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">case </span>OfflineReplica =&gt;
  assertValidPreviousStates(partitionAndReplica<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">    </span><span style="color:#9876aa;font-style:italic;">List</span>(NewReplica<span style="color:#cc7832;">, </span>OnlineReplica<span style="color:#cc7832;">, </span>OfflineReplica<span style="color:#cc7832;">, </span>ReplicaDeletionIneligible)<span style="color:#cc7832;">, </span>targetState)
  <span style="color:#808080;">// send stop replica command to the replica so that it stops fetching from the leader
</span><span style="color:#808080;">  </span><span style="color:#9876aa;font-style:italic;">brokerRequestBatch</span>.addStopReplicaRequestForBrokers(<span style="color:#9876aa;font-style:italic;">List</span>(replicaId)<span style="color:#cc7832;">, </span>topic<span style="color:#cc7832;">, </span>partition<span style="color:#cc7832;">, </span>deletePartition = <span style="color:#cc7832;font-weight:bold;">false</span>)
  <span style="color:#808080;">// As an optimization, the controller removes dead replicas from the ISR
</span><span style="color:#808080;">  </span><span style="color:#cc7832;font-weight:bold;">val </span>leaderAndIsrIsEmpty: <span style="color:#cc7832;">Boolean </span>=
    <span style="color:#9876aa;font-style:italic;">controllerContext</span>.<span style="color:#9876aa;font-style:italic;">partitionLeadershipInfo</span>.get(topicAndPartition) <span style="color:#cc7832;font-weight:bold;">match </span>{
      <span style="color:#cc7832;font-weight:bold;">case </span><span style="font-style:italic;">Some</span>(currLeaderIsrAndControllerEpoch) =&gt;
        <span style="color:#808080;">//</span><span style="font-family:'AR PL UKai CN';color:#808080;">重点</span><span style="color:#808080;">,rempveReplicaFromIsr
</span><span style="color:#808080;">        </span>controller.removeReplicaFromIsr(topic<span style="color:#cc7832;">, </span>partition<span style="color:#cc7832;">, </span>replicaId) <span style="color:#cc7832;font-weight:bold;">match </span>{
          <span style="color:#cc7832;font-weight:bold;">case </span><span style="font-style:italic;">Some</span>(updatedLeaderIsrAndControllerEpoch) =&gt;
            <span style="color:#808080;">// send the shrunk ISR state change request to all the remaining alive replicas of the partition.
</span><span style="color:#808080;">            </span><span style="color:#cc7832;font-weight:bold;">val </span>currentAssignedReplicas = <span style="color:#9876aa;font-style:italic;">controllerContext</span>.<span style="color:#9876aa;font-style:italic;">partitionReplicaAssignment</span>(topicAndPartition)
            <span style="color:#cc7832;font-weight:bold;">if </span>(!controller.<span style="color:#9876aa;font-style:italic;">deleteTopicManager</span>.isPartitionToBeDeleted(topicAndPartition)) {
              <span style="color:#9876aa;font-style:italic;">brokerRequestBatch</span>.addLeaderAndIsrRequestForBrokers(currentAssignedReplicas.filterNot(_ == replicaId)<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">                </span>topic<span style="color:#cc7832;">, </span>partition<span style="color:#cc7832;">, </span>updatedLeaderIsrAndControllerEpoch<span style="color:#cc7832;">, </span>replicaAssignment)
            }
            <span style="color:#9876aa;font-style:italic;">replicaState</span>.put(partitionAndReplica<span style="color:#cc7832;">, </span>OfflineReplica)
            <span style="color:#9876aa;font-style:italic;">stateChangeLogger</span>.trace(<span style="color:#6a8759;">"Controller %d epoch %d changed state of replica %d for partition %s from %s to %s"
</span><span style="color:#6a8759;">              </span>.format(<span style="color:#9876aa;font-style:italic;">controllerId</span><span style="color:#cc7832;">, </span>controller.epoch<span style="color:#cc7832;">, </span>replicaId<span style="color:#cc7832;">, </span>topicAndPartition<span style="color:#cc7832;">, </span>currState<span style="color:#cc7832;">, </span>targetState))
            <span style="color:#cc7832;font-weight:bold;">false
</span><span style="color:#cc7832;font-weight:bold;">          case </span>None =&gt;
            <span style="color:#cc7832;font-weight:bold;">true
</span><span style="color:#cc7832;font-weight:bold;">        </span>}
      <span style="color:#cc7832;font-weight:bold;">case </span>None =&gt;
        <span style="color:#cc7832;font-weight:bold;">true
</span><span style="color:#cc7832;font-weight:bold;">    </span>}</pre>
一开始会先通知,受影响的follower broker,现在你们的leader变了,不要再向这台broker拉数据以维持ISR了.也就是下面这句
<p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#808080;">  </span><span style="color:#9876aa;font-style:italic;">brokerRequestBatch</span>.addStopReplicaRequestForBrokers(<span style="color:#9876aa;font-style:italic;">List</span>(replicaId)<span style="color:#cc7832;">, </span>topic<span style="color:#cc7832;">, </span>partition<span style="color:#cc7832;">, </span>deletePartition = <span style="color:#cc7832;font-weight:bold;">false</span>)</pre>
然后主要的处理逻辑来到了removeReplicaFromISR这边,<br><p></p>
<p></p>
<pre style="background-color:#2b2b2b;color:#a9b7c6;font-family:'Ubuntu Mono';font-size:13.5pt;"><span style="color:#cc7832;font-weight:bold;">def </span><span style="color:#ffc66d;">removeReplicaFromIsr</span>(topic: <span style="color:#4e807d;">String</span><span style="color:#cc7832;">, </span>partition: <span style="color:#cc7832;">Int, </span>replicaId: <span style="color:#cc7832;">Int</span>): Option[LeaderIsrAndControllerEpoch] = {
......
......
<span style="color:#cc7832;font-weight:bold;">if </span>(leaderAndIsr.isr.contains(replicaId)) {
          <span style="color:#808080;">// if the replica to be removed from the ISR is also the leader, set the new leader value to -1
</span><span style="color:#808080;">          </span><span style="color:#cc7832;font-weight:bold;">val </span>newLeader = <span style="color:#cc7832;font-weight:bold;">if </span>(replicaId == leaderAndIsr.leader) LeaderAndIsr.<span style="color:#9876aa;font-style:italic;">NoLeader </span><span style="color:#cc7832;font-weight:bold;">else </span>leaderAndIsr.leader
          <span style="color:#cc7832;font-weight:bold;">var </span>newIsr = leaderAndIsr.isr.filter(b =&gt; b != replicaId)

          <span style="color:#808080;">// if the replica to be removed from the ISR is the last surviving member of the ISR and unclean leader election
</span><span style="color:#808080;">          // is disallowed for the corresponding topic, then we must preserve the ISR membership so that the replica can
</span><span style="color:#808080;">          // eventually be restored as the leader.
</span><span style="color:#808080;">          </span><span style="color:#cc7832;font-weight:bold;">if </span>(newIsr.isEmpty &amp;&amp; !LogConfig.<span style="font-style:italic;">fromProps</span>(config.originals<span style="color:#cc7832;">, </span>AdminUtils.<span style="font-style:italic;">fetchEntityConfig</span>(zkUtils<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">            </span>ConfigType.<span style="color:#9876aa;font-style:italic;">Topic</span><span style="color:#cc7832;">, </span>topicAndPartition.topic)).<span style="color:#9876aa;font-style:italic;">uncleanLeaderElectionEnable</span>) {
            info(<span style="color:#6a8759;">"Retaining last ISR %d of partition %s since unclean leader election is disabled"</span>.format(replicaId<span style="color:#cc7832;">, </span>topicAndPartition))
            newIsr = leaderAndIsr.isr
          }

          <span style="color:#cc7832;font-weight:bold;">val </span>newLeaderAndIsr = <span style="color:#cc7832;font-weight:bold;">new </span>LeaderAndIsr(newLeader<span style="color:#cc7832;">, </span>leaderAndIsr.leaderEpoch + <span style="color:#6897bb;">1</span><span style="color:#cc7832;">,
</span><span style="color:#cc7832;">            </span>newIsr<span style="color:#cc7832;">, </span>leaderAndIsr.zkVersion + <span style="color:#6897bb;">1</span>)
          <span style="color:#808080;">// update the new leadership decision in zookeeper or retry
</span><span style="color:#808080;">          </span><span style="color:#cc7832;font-weight:bold;">val </span>(updateSucceeded<span style="color:#cc7832;">, </span>newVersion) = ReplicationUtils.<span style="font-style:italic;">updateLeaderAndIsr</span>(zkUtils<span style="color:#cc7832;">, </span>topic<span style="color:#cc7832;">, </span>partition<span style="color:#cc7832;">,
</span><span style="color:#cc7832;">            </span>newLeaderAndIsr<span style="color:#cc7832;">, </span>epoch<span style="color:#cc7832;">, </span>leaderAndIsr.zkVersion)

          newLeaderAndIsr.zkVersion = newVersion
          finalLeaderIsrAndControllerEpoch = <span style="font-style:italic;">Some</span>(<span style="font-style:italic;">LeaderIsrAndControllerEpoch</span>(newLeaderAndIsr<span style="color:#cc7832;">, </span>epoch))
          <span style="color:#9876aa;font-style:italic;">controllerContext</span>.<span style="color:#9876aa;font-style:italic;">partitionLeadershipInfo</span>.put(topicAndPartition<span style="color:#cc7832;">, </span>finalLeaderIsrAndControllerEpoch.get)
          <span style="color:#cc7832;font-weight:bold;">if </span>(updateSucceeded)
            info(<span style="color:#6a8759;">"New leader and ISR for partition %s is %s"</span>.format(topicAndPartition<span style="color:#cc7832;">, </span>newLeaderAndIsr.toString()))
          updateSucceeded
        }
......
......
}</pre>
删除了一些非重点的代码,看到重点了吗?这个Repica会记录下自己的leader,要是leader为空了就标记为noleader=-1.其他的就是向zookeeper同步数据,更新版本号(这个partition leader的epoch)
<p></p>
<p>好了到这里,一台broker down了的基本主要逻辑就讲完了.<br></p>
<p></p>
<p>那么现在问题来了,如果一个ISR中的Replica变为0了那么kafka好像并不会有什么额外的作为.<br></p>
<p>看到这里,我其实是很震惊的.于是整个上午我都在源码中寻找,当replica变为0时,kafka的该怎么处理.但是并没有找到kafka会新分配一台机器当作partition leader的代码.</p>
<p>既然实践是检验真理的唯一标准,那么不如实际测试一下,看看kafka在这种情况下会怎么表现?实验环境是一个有5台broker的kafka集群</p>
<p>建立一个名为testSource的topic,partition为2,replica为1.之所以用replica为1,是为了更快地模拟出replica为0的情况.<br></p>
<p></p>
<pre><code class="language-plain">u928@master:~/yf/deploying/kafka-configs$ kafka-topics.sh --zookeeper 10.255.0.12:2181 --describe  --topic testSource
Topic:testSource	PartitionCount:2	ReplicationFactor:1	Configs:
	Topic: testSource	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
	Topic: testSource	Partition: 1	Leader: 2	Replicas: 2	Isr: 2
</code></pre>那么现在kill 掉 broker 2上的进程.
<p></p>
<pre><code class="language-plain">u928@master:~/yf/deploying/kafka-configs$ kafka-topics.sh --zookeeper 10.255.0.12:2181 --describe  --topic testSource
Topic:testSource	PartitionCount:2	ReplicationFactor:1	Configs:
	Topic: testSource	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
	Topic: testSource	Partition: 1	Leader: -1	Replicas: 2	Isr: </code></pre>
<p>看到broker2,挂了之后leader如预期地变为了-1,就像刚才提到的,leader为空就标记为noleader=-1,Isr副本数变为了0.</p>
<p>这个时候能否提供服务?在控制台上分别开一个producer与一个consumer</p>
<p></p>
<pre><code class="language-plain">u928@master:~/yf/deploying/kafka-configs$ kafka-console-producer.sh --broker-list 10.255.0.12:9092 --topic testSource
sdsd
sdsd
sdsd
aaa
</code></pre><pre><code class="language-plain">u928@slaver2:~$ /opt/kafka0.10/bin/kafka-console-consumer.sh --bootstrap-server master:9092  --topic testSource
sdsd
sdsd
sdsd
aaa
</code></pre>
<p>OK,我们看到即使有一个partition挂了,kafka还是可以正常运转.</p>
<p>现在我们kill掉 broker1,也就是剩下的唯一broker</p>
<p></p>
<pre><code class="language-plain">u928@master:~/yf/deploying/kafka-configs$ kafka-topics.sh --zookeeper 10.255.0.12:2181 --describe  --topic testSource
Topic:testSource	PartitionCount:2	ReplicationFactor:1	Configs:
	Topic: testSource	Partition: 0	Leader: -1	Replicas: 1	Isr: 
	Topic: testSource	Partition: 1	Leader: -1	Replicas: 2	Isr: 
</code></pre>那么两个partition的leader都变为了-1,这时候kafka的这个topic还会正常工作吗?<br><p></p>
<p></p>
<pre><code class="language-plain">u928@master:~/yf/deploying/kafka-configs$ kafka-console-producer.sh --broker-list 10.255.0.12:9092 --topic testSource
dsd
fdfd
gfgfg
sdsd[2017-11-20 14:41:40,532] ERROR Error when sending message to topic testSource with key: null, value: 3 bytes with error: (org.apache.kafka.clients.producer.internals.ErrorLoggingCallback)
org.apache.kafka.common.errors.TimeoutException: Expiring 2 record(s) for testSource-0 due to 1504 ms has passed since batch creation plus linger time
[2017-11-20 14:41:40,536] ERROR Error when sending message to topic testSource with key: null, value: 5 bytes with error: (org.apache.kafka.clients.producer.internals.ErrorLoggingCallback)
org.apache.kafka.common.errors.TimeoutException: Expiring 2 record(s) for testSource-0 due to 1504 ms has passed since batch creation plus linger time
</code></pre><br><pre><code class="language-plain">/opt/kafka0.10/bin/kafka-console-consumer.sh --bootstrap-server master:9092  --topic testSource
[2017-11-20 14:45:52,852] WARN Auto offset commit failed for group console-consumer-33901: Commit cannot be completed since the group has already rebalanced and assigned the partitions to another member. This means that the time between subsequent calls to poll() was longer than the configured max.poll.interval.ms, which typically implies that the poll loop is spending too much time message processing. You can address this either by increasing the session timeout or by reducing the maximum size of batches returned in poll() with max.poll.records. (org.apache.kafka.clients.consumer.internals.ConsumerCoordinator)
^CProcessed a total of 0 messages
</code></pre>rua, 我们现在看到这个topic, 真的摸了!
<p></p>
<p>也就是这个topic不再提供服务,现在我们再把两台broker打开</p>
<p></p>
<pre><code class="language-html">u928@master:~kafka-topics.sh --zookeeper 10.255.0.12:2181 --describe  --topic testSource
Topic:testSource	PartitionCount:2	ReplicationFactor:1	Configs:
	Topic: testSource	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
	Topic: testSource	Partition: 1	Leader: 2	Replicas: 2	Isr: 2
</code></pre>恢复的broker自动加入了kafka集群,基于改topic的kafka服务也恢复了.
<p></p>
<p><br></p>
<p>现在进入总结环节:</p>
<p>虽然难以置信,不过kafka的可靠性并没有我想象中的那么高.当一个topic创建完毕后,那么为这个topic服务的broker也确定下来了.(不包括人为地通过合法的命令增加partition和replica的情况,这里只考虑在server down/crash的情况).如果一个topic的某个partition的ISR全部挂了,那么这个partition就会停止服务,但是客户端仍然可以通过这个topic接收或者发送数据.如果某个topic的全部partition都挂了,那么不好意思,整个topic就不再提供服务了.Producer和Consumer要么阻塞要么报错,直到指定的broker恢复为止.这里其实有一个trick,kafka集群识别broker仅仅通过brokerId,只要配置了相同的brokerId那么即便不是同一台物理机或Ip,也可以顶替之前broker的服务.</p>
<p>还记得一开始想要解决的问题吗?</p>
<p></p>
<p>2.要是一个broker down了,那它的replica该怎么重新分配.</p>
<p>3.如果一个broker因为2成为了一个topic的新replica,那么他没有之前的那些message该怎么办?需要从其他broker拉过来吗,如果要拉,那么数据量太大的会不会对网络造成负载?</p>
现在应该也有了答案,在kafka的默认配置下:
<p>2.从这个partition的replica里依次取broker作为新ISR的leader,直到ISR为空,该partition停止服务.</p>
<p>3.不会有broker会因为其他服务器的崩溃而成为新的broker</p>
            </div>
                </div>
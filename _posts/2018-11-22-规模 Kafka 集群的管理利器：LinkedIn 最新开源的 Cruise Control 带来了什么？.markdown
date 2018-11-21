---
layout:     post
title:      规模 Kafka 集群的管理利器：LinkedIn 最新开源的 Cruise Control 带来了什么？
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为乐投网www.99lt.com特邀专栏作者原创或翻译文章，未经博主允许不得转载。					https://blog.csdn.net/qq_36852006/article/details/77833500				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="text-align:left;text-indent:0em;line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="color:#ff0000;"><strong>转载请注明出处：<a href="http://www.99lt.com" rel="nofollow">乐投网 - 规模 Kafka 集群的管理利器：LinkedIn 最新开源的 Cruise Control 带来了什么？</a></strong></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;text-align:center;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><img alt="" src="http://www.99lt.com/uploads/allimg/170831/8-1FS11A640.png" style="visibility:visible;"></samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>最近几年kafka受到越来越多的关注，LinkedIn的Kafka规模已经非常庞大。如何运维如此大规模的Kafka集群是一个很有挑战的事情。针对这一问题，LinkedIn的团队交出了自己的答卷。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>在过去几年中 Apache Kafka[1] 受欢迎程度日益高涨。
 LinkedIn 部署的 Kafka 集群每天处理超过 2 万亿条消息。 虽然 Kafka 已被证明是非常稳定的，但运行如此大规模的 Kafka 仍然存在运维上的挑战。 Broker 每天都会失败，导致我们集群工作负载不均衡。 因此， SRE 花费大量时间和精力重新分配分区，以保证 Kafka 群集均衡。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>智能自动化在这种情况下至关重要，这就是为什么我们开发了 Cruise
 Control[2] 。 Cruise Control 是一个用于持续监视 Kafka 集群并根据配置自动调整分配资源的系统 。用户指定目标， Cruise Control 负责监控是否违反这些目标，分析群集现有工作负载，并自动执行管理操作以满足这些目标。 在去年秋天的流处理会议上，您可以看到有关 Cruise Control 的视频 [3]。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>今天我们很高兴地宣布，我们开源了 Cruise Control，现在可以在
 Github 上获得 [3] 该软件 。 在本文中，我们将描述 Cruise Control 在 LinkedIn 的使用，其架构以及创建时遇到的一些独特挑战。 如果对本文中使用的 Kafka 术语不理解，这份参考资料 [4] 将对您很有帮助。</samp></span></span><br>
 </p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:1.6em;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span style="color:rgb(0,128,0);"><span>设计目标</span></span></samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>当我们设计Cruise Control时，我们有一些重要的要求：</samp></span></span></p>
<ul style="color:rgb(51,51,51);list-style-type:none;font-size:16px;line-height:27.2px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="list-style:disc;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>可靠的自动化：Cruise Control应该能够准确地分析集群工作负载并生成可信赖的执行计划，而无需任何人为干预。</samp></span></span></p>
</li><li style="list-style:disc;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>资源效率：Cruise Control应该足够聪明，在执行操作时会避免对集群可用性产生不利影响。</samp></span></span></p>
</li><li style="list-style:disc;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>可扩展性：Kafka用户对其Kafka部署的可用性和性能有不同的要求，而且会使用各种部署工具，管理节点和数据收集机制。
 Cruise Control必须能够满足任意用户定义的目标并执行用户定义的操作。</samp></span></span></p>
</li><li style="list-style:disc;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>通用性：我们在早期就意识到，其他分布式系统也可以受益于类似的运维自动化（需要完成应用感知监控分析
 - 动作周期）。 虽然现有的类似产品有助于均衡集群中的资源利用率，但大多数与应用无关，并通过迁移整个应用程序达到目的。 虽然这对于无状态系统来说工作的很好，但当涉及到有状态系统（例如Kafka）时，这些产品通常就无计可施了。 因此，我们希望Cruise Control成为在任何分布式系统中都可以使用的通用框架。</samp></span></span><br>
 </p>
</li></ul><p style="text-indent:0em;color:rgb(51,51,51);line-height:1.6em;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span><span style="color:rgb(0,128,0);">LinkedIn的Cruise
 Control</span></span></samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>Cruise Control旨在解决以下关键的运维目标：</samp></span></span></p>
<ol style="color:rgb(51,51,51);font-size:16px;line-height:27.2px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>Kafka集群必须在磁盘，网络和CPU利用率方面持续均衡节点。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>当broker失败时，我们需要自动将该broker上的副本重新分配给群集中的其他broker，并恢复原始的复制因子。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>我们需要能够识别消耗群集最多资源的分区。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>我们需要支持低接触集群扩展和broker停用。 从集群添加或删除代理程序后需要手动重新分配分区，这些操作非常繁琐。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>能够使用异构硬件运行群集（例如，在缺少相同的硬件时快速修复硬件故障）。
 然而，异构硬件增加运维开销，因为在均衡集群时，SRE需要精确地识别硬件差异。 Cruise Control应该能够支持异构机器的Kafka群集和单机运行多个broker。<br><br><span style="font-size:18px;line-height:1.6em;text-indent:1em;"><span style="color:rgb(0,128,0);">它是如何工作的？</span></span></samp></span></span></p>
</li></ol><p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>Cruise Control遵循监控/分析/操作 这样的工作周期。
 下图描述了Cruise Control的架构。 许多组件是可插拔的，如图所示（例如，度量取样器，分析器等）。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;text-align:center;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><img alt="规模 Kafka 集群的管理利器：LinkedIn 最新开源的 Cruise Control 带来了什么？" src="http://www.99lt.com/uploads/allimg/170831/1AA21193-0.jpg" style="width:500px;visibility:visible;"></samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span>REST
 API</span></samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>Cruise Control提供了REST API，允许用户与之进行交互。
 REST API支持查询Kafka群集的工作负载和优化建议，并支持触发运维操作。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span>负载监视器</span></samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>负载监视器从集群收集标准Kafka指标，并且可以获取每个分区资源指标（例如，每个分区的估计CPU利用率）。
 然后生成一个集群工作负载模型，以准确捕获集群资源利用率，其中包括磁盘利用率，CPU利用率，字节输入和输出速率。 然后将模型反馈到检测器和分析器。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span>分析器</span></samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>分析器是Cruise Control的“大脑”。 它使用启发式方法基于用户提供的优化目标和来自负载监视器的集群工作负载模型生成优化方案。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>优化目标基于用户配置的优先级。 优先级较高的目标首先被保证。低优先级目标不能导致违反高优先级目标。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>Cruise Control还允许指定硬性目标和软性目标。 硬性目标是必须满足的目标（例如，副本放置必须做到机架可识别）。
 另一方面，如果可以满足所有硬性目标，那么软性目标可以不必达成。 如果优化结果违反了一个硬性目标，优化将会失败。 硬性目标将比软性目标具有更高的优先级。 到目前为止，我们实现了以下硬性/软性目标：</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span>硬目标</span></samp></span></span></p>
<ol style="color:rgb(51,51,51);font-size:16px;line-height:27.2px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>副本配置必须是机架可识别的。 相同分区副本放在不同的机架上，这样当某个机架停机时，任何分区最多会有一个副本丢失。
 这有助于控制故障边界，并使Kafka更加健壮。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>broker资源利用率必须在预定义的阈值内。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>即使broker上的所有副本都成为leader，也不能允许网络利用超出预先定义的容量。在这种情况下，所有的消费者流量将被重定向到该broker，这将导致比平常更高的网络利用率。</samp></span></span></p>
</li></ol><p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>软目标</samp></span></span></p>
<ol style="color:rgb(51,51,51);font-size:16px;line-height:27.2px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>试图在所有broker之间实现一致的资源利用率。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>试图在broker之间实现leader分区的均匀字节输入速率（来自客户端而非来自复制的字节输入速率）。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>试图将特定主题的分区均匀分配到所有broker上。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>试图将副本均匀分配到所有broker上。</samp></span></span></p>
</li></ol><p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>在高层次上，目标优化逻辑如下：</samp></span></span></p>
<p class="prettyprint hljs vbscript" style="text-indent:0em;color:rgb(68,68,68);line-height:1.5em;font-size:16px;font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;border:none;background-color:rgb(246,246,246);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">For</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">each</span> goal
 g <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">in</span> the goal list ordered by priority* {<br>
  <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">For</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">each</span> broker
 b {<br>
    <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">while</span> b does <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">not</span> meet
 g’s requirement {<br>
      <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">For</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">each</span> replica
 r <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">on</span> b sorted by the resource utilization density** {<br>
        Move r (<span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">or</span> the leadership of r) <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">to</span> another
 eligible broker b’ so b’ still satisfies g <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">and</span> all the satisfied goals<br>
        Finish the optimization <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">for</span> b once g <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">is</span> satisfied.<br>
      }<br>
      Fail the optimization <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">if</span> g <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">is</span> a
 hard goal <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">and</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">is</span> <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">not</span> satisfied <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">for</span> b<br>
    }<br>
  }<br>
  Add g <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">to</span> the satisfied goals<br>
}<br>
* The high priority goals are optimized first<br>
**The utilization density <span class="hljs-keyword" style="color:rgb(51,51,51);font-weight:700;">is</span> Resource_Utilization / Partition_Size</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>异常检测器</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>异常检测器识别两种异常：</samp></span></span></p>
<ol style="color:rgb(51,51,51);font-size:16px;line-height:27.2px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);"><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>broker故障：即非空代理离开集群，导致分区副本数不足。 因为这可能会在正常的群集恢复期间发生，所以异常检测器在触发通知并修复群集之前提供可配置的宽限期。</samp></span></span></p>
</li><li style="list-style:none;line-height:1.7em;">
<p class="no-text-indent" style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>目标违反：即 <span>违反</span> 优化目标。
 如果启用自愈功能，Cruise Control将主动尝试通过自动分析工作负载并执行优化方案来解决目标违反。</samp></span></span></p>
</li></ol><p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>执行器</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>执行器负责执行来自分析器的优化方案。 重新均衡Kafka集群通常涉及重新分配分区。
 执行程序确保执行是资源可感知的，并不会压跨任何代理。 重新分配分区可能是一个长期运行的过程（在一个大的Kafka集群中可能需要几天的时间才能最终完成）。 有时，用户可能希望停止正在进行的分区重新分配。 执行器的设计就考虑到了在执行方案时安全中断。<br><br><span style="font-size:18px;line-height:1.6em;text-indent:1em;"><span style="color:rgb(0,128,0);">有趣的挑战</span></span></samp></span></span></p>
<div style="color:rgb(51,51,51);font-size:16px;line-height:27.2px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<p style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>我们在开发和使用Cruise Control时遇到许多有趣的挑战。
 下面列出其中几个。</samp></span></span></p>
<p style="text-indent:0em;line-height:1.7;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span style="color:rgb(0,128,0);"><span>为Kafka构建可靠的集群工作负载模型</span></span></samp></span></span></p>
<p style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>这并非像听起来那样简单。 有很多细微之处要注意。 例如，从broker收集CPU利用率指标非常简单，但是我们如何量化每个分区对CPU利用率的影响？
 这个wiki页面[5]解释了我们是回答这个问题的。</samp></span></span></p>
<p style="text-indent:0em;line-height:1.7em;">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>您愿意为优化方案等待多久？</samp></span></span></p>
</div>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>分析器组件已经取得了长足进步。 我们最初使用具有复杂参数化损耗函数的通用优化器。
 在中型Kafka群集上获取优化方案需要几周时间。 后来我们切换到当前的启发式优化器，这可以在几分钟之内得到相当不错的优化方案。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>内存还是速度？</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>因为我们必须保存一段时间（例如一个星期）的度量指标，以便分析Kafka群集中分区的流量模式，并且由于生成优化方案需要进行大量计算，所以Cruise
 Control同时是内存密集型和CPU密集型的程序。 但是这是相互矛盾的。 为了加快优化方案生成，我们希望使用更多的缓存和并行计算，但这样做会增加内存和CPU的使用。 我们最终做出了一些设计决策，以在两者之间取得平衡。 例如，我们预先计算优化方案并缓存起来，以避免用户查询到时的长时间等待。 另一方面，我们还错开了内存密集型任务的执行（例如，方案预计算和异常检测等），以避免高内存消耗操作同时执行。</samp></span></span><br>
 </p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:1.6em;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp><span><span style="color:rgb(0,128,0);">未来的工作</span></span></samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>更多Kafka集群优化目标！</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>由于Cruise Control的优化目标是可插拔的，因此用户可能会根据需要提出精密的目标来优化自己的Kafka群集。
 例如，我们使用LinkedIn的Kafka Monitor[6]来监控我们的集群可用性。 由于Kafka Monitor根据其向“监控”主题发送消息的能力来报告每个broker的可用性，因此我们需要确保该主题分区的leader能够覆盖所有broker。 作为一个开源项目，我们也鼓励用户创造自己的目标，并为社区做贡献。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>与云管理系统（CMS）集成</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>目前，Cruise Control系统通过迁移坏掉的分区代理来治愈集群。
 我们设想，Cruise Control可以与其他CMS集成，以便在利用率达到一定阈值时自动扩展群集，必要时也可以从备用池用一个新代理替换故障代理。 如上所述，我们欢迎社区的加入和对未来功能的贡献。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>通过深入了解操作</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>Cruise Control有助于深入分析从Kafka收集的指标。
 我们相信，它将使SRE能够量化各种资源使用指标的影响，并获得有助于容量规划和性能调优。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>概括我们开发Cruise Control时认识到动态负载平衡器对任何分布式系统来说都是实用工具。
 Cruise Control的度量指标聚合，资源利用率分析和优化方案的生成同样适用于其他分布式系统。我们想抽象出这些核心组件，并提供给其他项目。 我们对Cruise Control的愿景是以允许与任何分布式系统直接集成的方式构建，以促进特定于应用程序的性能分析，优化和执行。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:1.6em;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>致谢</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>Cruise Control开始于Efe Gencer[7]的实习项目。
 Kafka开发团队的许多成员都参加了头脑风暴，设计和评论。 Cruise Control还收到了来自LinkedIn的Kafka SRE团队的许多宝贵贡献和见解。</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[1]https://kafka.apache.org</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[2]https://github.com/linkedin/cruise-control</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[3]https://www.youtube.com/watch?v=lf31udm9cYY</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[4]https://github.com/jet/kafunk/wiki/Kafka-Crash-Cource</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[5]https://github.com/linkedin/cruise-control/wiki/Build-the-cluster-workload-model</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[6]https://engineering.linkedin.com/blog/2016/05/open-sourcing-kafka-monitor</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[7]https://www.linkedin.com/in/efegencer/</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[8]https://www.meetup.com/Stream-Processing-Meetup-LinkedIn/</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>[9]http://samza.apache.org</samp></span></span></p>
<p style="text-indent:0em;color:rgb(51,51,51);line-height:27.2px;font-size:16px;font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;background-color:rgb(254,254,254);">
<span style="font-family:'trebuchet ms', helvetica, sans-serif;"><span><samp>英文原文：https://engineering.linkedin.com/blog/2017/08/open-sourcing-kafka-cruise-control</samp></span></span></p>
<div id="KSFIND_MASK" style="border:0px none !important;z-index:100000;width:0px;background:rgb(0,0,0);">
</div>
<div id="0KSFindDIVKSFindDIV" class="KSFIND_CLASS_POP" style="font-family:'Helvetica Neue', Helvetica, Tahoma, Arial, STXihei, 'Microsoft YaHei', '微软雅黑', sans-serif;font-size:16px;text-decoration:none;text-align:center;width:10.8906px;">
s</div>
<div class="KSFIND_CLASS_POP" style="font-family:monospace;font-size:16px;text-decoration:none;text-align:center;width:18px;">
sh</div>
<div class="KSFIND_CLASS_POP" style="font-family:monospace;font-size:16px;text-decoration:none;text-align:center;width:18px;">
sh</div>
            </div>
                </div>
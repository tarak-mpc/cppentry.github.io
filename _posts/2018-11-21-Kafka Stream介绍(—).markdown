---
layout:     post
title:      Kafka Stream介绍(—)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka-streams介绍">Kafka Streams介绍(—)</h1>

<hr>

<p><strong><em>说明</em></strong> <br>
本文是Confluent Platform 3.0版本中对于Kafka Streams的翻译。 <br>
原文地址：<a href="https://docs.confluent.io/3.0.0/streams/index.html" rel="nofollow">https://docs.confluent.io/3.0.0/streams/index.html</a> <br>
看了很多其他人翻译的文档， 还是第一次翻译，有什么翻译的不好的地方还请指出。</p>



<h2 id="介绍">介绍</h2>



<h3 id="kafka-streams-举重若轻的强大">Kafka Streams – 举重若轻的强大</h3>

<p>Kafka Streams。Apache Kafka开源项目的一个组成部分。是一个功能强大，易于使用的库。用于在Kafka上构建高可分布式、拓展性，容错的应用程序。它建立在流处理的一系列重要功能基础之上，比如正确区分事件事件和处理时间，处理迟到数据以及高效的应用程序状态管理。 <br>
下面的列表强调了Kafka Streams的几个关键功能，使得Kafka Streams成为构建流处理应用程序、持续查询、转换和微服务等场景的新选择。</p>

<blockquote>
  <ul>
  <li>功能强大 <br>
      高拓展性，弹性，容错 <br>
      有状态和无状态处理 <br>
      基于事件时间的Window，Join，Aggergations</li>
  <li><strong>轻量级</strong> <br>
     无需专门的集群 <br>
     没有外部以来 <br>
      一个库，而不是框架</li>
  <li><strong>完全集成</strong> <br>
     100%的Kafka 0.10.0版本兼容 <br>
     易于集成到现有的应用程序 <br>
     程序部署无需手工处理(这个指的应该是Kafka多分区机制对Kafka Streams多实例的自动匹配)</li>
  <li><strong>实时性</strong> <br>
      毫秒级延迟 <br>
      并非微批处理 <br>
      窗口允许乱序数据 <br>
      允许迟到数据</li>
  </ul>
</blockquote>



<h3 id="近看kafka-streams">近看Kafka Streams</h3>

<p>在我们深入Kafka Streams的概念和架构细节以及按部就班认识Kafka Streams之前，我们先来对上面提出的列表做更多的介绍。</p>

<blockquote>
  <ul>
  <li>更简单的流处理：Kafka Streams的设计目标为一个轻量级的库，就像Kafka的Producer和Consumer似得。可以轻松将Kafka Streams整合到自己的应用程序中。对应用程序的额外要求仅仅是打包和部署到应用程序所在集群罢了。</li>
  <li>除了Apache Kafka之外没有任何其它外部依赖， 并且可以在任何Java应用程序中使用。不需要为流处理需求额外部署一个其它集群。操作和维护团队肯定会很高兴这一点。</li>
  <li>使用Kafka作为内部消息通讯存储介质，而不是像其它流处理框架似得，重新加入其它外部组件来做消息通讯。Kafka Streams使用Kafka的分区水平拓展来对数据做有序高效的处理。这样同时兼顾了高性能，高扩展性，并使操作简便。这种决策的好处是，你不必了解和调整两个不同的消息传输层（数据在不同伸缩介质中间移动和流处理的独立消息处理层），同样，Kafka的性能和高可靠性方面的改进，都会使得Kafka Streams直接受益。也可以同时借助Kafka社区强大的开发能力。</li>
  <li>允许和其他资源管理和配置共聚焦集成。因此，Kafka Streams能够更加无缝的集成到现有的开发、打包、部署和业务实践当中去。你可以自由地使用自己喜欢的工具，比如java 应用服务器，Puppet， Ansible，Mesos，Yarn，Docket， 甚至在一台手工运行你自己应用程序进行验证的机器上。</li>
  <li>支持本地状态容错。这样就可以进行非常高效快速的包含状态的Join和Window 聚合操作。本地状态被保存在Kafka中，在机器故障的时候，其他机器可以自动恢复这些状态继续处理。</li>
  <li>每次处理一条数据以实现低延时，这对于欺诈监测等场景是至关重要的。这也是Kafka Streams和其他基于微批处理的流处理框架的不同。</li>
  </ul>
</blockquote>

<p>此外，Kafka Streams在设计上基于丰富的开发经验，具有很强的实用性。它提供了流处理所有的必要的原语，允许应用程序从Kafka中读取流数据，处理数据并且将结果写回Kafka或者发送到其他外部系统中取。提供了高层次的比如Filter，Map，Join等DSL操作以及低级别API供开发者选择使用。 <br>
    最后，Kafka Streams为拓展开发者提供帮助，它入门门槛低，开发路径平滑，你可以快速编写和运行一个小规模的应用程序进行验证，因为你完全不需要安装或者了解其他分布式流处理平台。并且只需要将应用程序部署在多个实例上就可以在大批量的生产工作中实现负载均衡。Kafka Streams透明地使用Kafka并行操作模型处理同一应用程序的多个实例来实现负载均衡。 <br>
    综上所述，Kafka Streams是构建流处理应用中的一个引人注目的选择，请给它一个试用的机会，并运行你的第一个Hello World流处理程序。文档的下一章将带你开始由浅入深编写Kafka Streams应用程序。</p>



<h3 id="需求">需求</h3>

<p>Kafka 0.10及更新版本 <br>
[可选]额外的avro schema支持: Confluent Schema Registry 3.0.0</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
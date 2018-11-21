---
layout:     post
title:      kafka介绍(详细讲解kafka的前世今生和功能特色，优缺点)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：欢迎分享交流，转载请注明出处，谢谢！					https://blog.csdn.net/myhes/article/details/83246504				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><h3>kafka介绍,又作卡夫卡，kafuka</h3><ul><li><a href="#kafka_2" rel="nofollow">kafka介绍</a></li><ul><li><a href="#kafka_6" rel="nofollow">kafka研发背景</a></li><li><a href="#kafka_10" rel="nofollow">kafka名字的由来</a></li><li><a href="#kafka_13" rel="nofollow">kafka的设计目标：</a></li><li><a href="#kafka_20" rel="nofollow">kafka历史</a></li><li><a href="#kafka_27" rel="nofollow">kafka的优点</a></li><li><a href="#kafka_43" rel="nofollow">kafka的缺点</a></li></ul></ul></div><p></p>
<h1><a id="kafka_2"></a>kafka介绍</h1>
<p>kafka是一个比较经典的消息发布和订阅系统，也是大数据种用作数据交换的核心组件之一，以并发量大，社区活跃备受广大组织的喜爱，国内也有人称之为卡夫卡，kafuka。kafka有几个不同的衍生版本，这里所说的kafka是指 <strong>apache kafka</strong> 。下面我来来介绍kafka的前世今生</p>
<h2><a id="kafka_6"></a>kafka研发背景</h2>
<p>kafka的诞生，是为了解决linkedin的数据管道问题，期初linkedin采用了ActiveMQ来进行数据交换，大约是在2010年前后，那时的ActiveMQ还远远无法满足linkedin对数据传递系统的要求，经常由于各种缺陷而导致消息阻塞或者服务无法正常访问，为了能够解决这个问题，linkedin决定研发自己的消息传递系统，当时linkedin的首席架构师jay kreps便开始组织团队进行消息传递系统的研发；</p>
<h2><a id="kafka_10"></a>kafka名字的由来</h2>
<p>kafka的架构师jay kreps对于kafka的名称由来是这样讲的，由于jay kreps非常喜欢franz kafka,并且觉得kafka这个名字很酷，因此取了个和消息传递系统完全不相干的名称kafka，取名字是并没有特别的含义。</p>
<h2><a id="kafka_13"></a>kafka的设计目标：</h2>
<ol>
<li><strong>使用推送和拉取模式</strong> 实现生产者和消费者的解耦；</li>
<li><strong>微消息系统中的消息提供数据持久化</strong>，以便支持多个消费者；</li>
<li>系统可以<strong>随着数据流的增长进行横向扩展</strong>；</li>
<li>通过系统优化<strong>实现高吞吐量</strong>；</li>
</ol>
<h2><a id="kafka_20"></a>kafka历史</h2>
<ol>
<li>2010年底，开源道github，初始版本为0.7.0；</li>
<li>2011年7月因为备受关注，被纳入apache孵化器项目；</li>
<li>2012年10月，kafka从apache孵化器项目毕业，成为apache顶级项目；</li>
<li>2014年，jay kreps,neha narkhede,jun rao离开linkedin,成立confluent,此后linkedin和confluent成为kafka的核心贡组织，致力于将kafka推广应用；</li>
</ol>
<h2><a id="kafka_27"></a>kafka的优点</h2>
<ol>
<li>kafka支持多个生产者；</li>
<li>kafka支持多个消费者；</li>
<li>kafka支持broker的横向扩展；</li>
<li>kafka副本集机制，实现数据冗余，保障数据尽量不丢失；</li>
<li>通过topic将不同的消息进行分类(几乎所有的MQ都有)；</li>
<li>通过分批发送压缩的方式，减少网络数据传输开销，提升数据吞吐量；</li>
<li>支持多种模式的消息(schema)；</li>
<li>可基于磁盘实现数据持久化；</li>
<li>高性能处理消息，在处理大量数据的情况下，能保证亚秒级的消息延迟；</li>
<li>一个消费者支持多个主题的消费；</li>
<li>对cpu和内存消耗较小；</li>
<li>对网络开销相对较小；</li>
<li>支持跨数据中心的消息复制；</li>
<li>支持镜像集群；</li>
</ol>
<h2><a id="kafka_43"></a>kafka的缺点</h2>
<ol>
<li>由于是批量发送，数据并非真正的实时；</li>
<li>对于mqtt协议不支持；</li>
<li>不支持物联网传感数据直接接入；</li>
<li>仅支持统一分区内消息有序，无法实现全局消息有序；</li>
<li>监控不完善，需要安装插件；</li>
<li>依赖zookeeper进行元数据管理；</li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Kafka实战－Kafka到Storm
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>1.概述</h1>
<p>　　在《<a id="homepage1_HomePageDays_ctl00_DayList_TitleUrl_0" class="postTitle2" href="http://www.cnblogs.com/smartloli/p/4615908.html" rel="nofollow">Kafka实战－Flume到Kafka</a>》一文中给大家分享了Kafka的数据源生产，今天为大家介绍如何去实时消费Kafka中的数据。这里使用实时计算的模型——Storm。下面是今天分享的主要内容，如下所示：</p>
<ul><li>数据消费</li><li>Storm计算</li><li>预览截图</li></ul><p>　　接下来，我们开始分享今天的内容。</p>
<h1>2.数据消费</h1>
<p>　　Kafka的数据消费，是由Storm去消费，通过KafkaSpout将数据输送到Storm，然后让Storm安装业务需求对接受的数据做实时处理，下面给大家介绍数据消费的流程图，如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201507/091043394713164.png" alt=""></p>
<p>　　从图可以看出，Storm通过KafkaSpout获取Kafka集群中的数据，在经过Storm处理后，结果会被持久化到DB库中。</p>
<h1>3.Storm计算</h1>
<p>　　接着，我们使用Storm去计算，这里需要体检搭建部署好Storm集群，若是未搭建部署集群，大家可以参考我写的《<a id="CategoryEntryList1_EntryStoryList_ctl00_Entries_TitleUrl_0" class="entrylistItemTitle" href="http://www.cnblogs.com/smartloli/p/4585268.html" rel="nofollow">Kafka实战－Storm Cluster</a>》。这里就不多做赘述搭建的过程了，下面给大家介绍实现这部分的代码，关于KafkaSpout的代码如下所示：</p>
<ul><li>KafkaSpout类：</li></ul><div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#0000ff;">package</span><span style="color:#000000;"> cn.hadoop.hdfs.storm;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.util.HashMap;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.util.List;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.util.Map;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> java.util.Properties;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.slf4j.Logger;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> org.slf4j.LoggerFactory;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> cn.hadoop.hdfs.conf.ConfigureAPI.KafkaProperties;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> kafka.consumer.Consumer;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> kafka.consumer.ConsumerConfig;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> kafka.consumer.ConsumerIterator;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> kafka.consumer.KafkaStream;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> kafka.javaapi.consumer.ConsumerConnector;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.spout.SpoutOutputCollector;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.task.TopologyContext;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.topology.IRichSpout;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.topology.OutputFieldsDeclarer;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.tuple.Fields;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.tuple.Values;

</span><span style="color:#008000;">/**</span><span style="color:#008000;">
 * @Date Jun 10, 2015
 *
 * @Author dengjie
 *
 * @Note Data sources using KafkaSpout to consume Kafka
 </span><span style="color:#008000;">*/</span>
<span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span> KafkaSpout <span style="color:#0000ff;">implements</span><span style="color:#000000;"> IRichSpout {

    </span><span style="color:#008000;">/**</span><span style="color:#008000;">
     * 
     </span><span style="color:#008000;">*/</span>
    <span style="color:#0000ff;">private</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">final</span> <span style="color:#0000ff;">long</span> serialVersionUID = -7107773519958260350L<span style="color:#000000;">;
    </span><span style="color:#0000ff;">private</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">final</span> Logger LOGGER = LoggerFactory.getLogger(KafkaSpout.<span style="color:#0000ff;">class</span><span style="color:#000000;">);

    SpoutOutputCollector collector;
    </span><span style="color:#0000ff;">private</span><span style="color:#000000;"> ConsumerConnector consumer;
    </span><span style="color:#0000ff;">private</span><span style="color:#000000;"> String topic;

    </span><span style="color:#0000ff;">private</span> <span style="color:#0000ff;">static</span><span style="color:#000000;"> ConsumerConfig createConsumerConfig() {
        Properties props </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> Properties();
        props.put(</span>"zookeeper.connect"<span style="color:#000000;">, KafkaProperties.ZK);
        props.put(</span>"group.id"<span style="color:#000000;">, KafkaProperties.GROUP_ID);
        props.put(</span>"zookeeper.session.timeout.ms", "40000"<span style="color:#000000;">);
        props.put(</span>"zookeeper.sync.time.ms", "200"<span style="color:#000000;">);
        props.put(</span>"auto.commit.interval.ms", "1000"<span style="color:#000000;">);
        </span><span style="color:#0000ff;">return</span> <span style="color:#0000ff;">new</span><span style="color:#000000;"> ConsumerConfig(props);
    }

    </span><span style="color:#0000ff;">public</span><span style="color:#000000;"> KafkaSpout(String topic) {
        </span><span style="color:#0000ff;">this</span>.topic =<span style="color:#000000;"> topic;
    }

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
        </span><span style="color:#0000ff;">this</span>.collector =<span style="color:#000000;"> collector;
    }

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> close() {
        </span><span style="color:#008000;">//</span><span style="color:#008000;"> TODO Auto-generated method stub</span>
<span style="color:#000000;">
    }

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> activate() {
        </span><span style="color:#0000ff;">this</span>.consumer =<span style="color:#000000;"> Consumer.createJavaConsumerConnector(createConsumerConfig());
        Map</span>&lt;String, Integer&gt; topickMap = <span style="color:#0000ff;">new</span> HashMap&lt;String, Integer&gt;<span style="color:#000000;">();
        topickMap.put(topic, </span><span style="color:#0000ff;">new</span> Integer(1<span style="color:#000000;">));
        Map</span>&lt;String, List&lt;KafkaStream&lt;<span style="color:#0000ff;">byte</span>[], <span style="color:#0000ff;">byte</span>[]&gt;&gt;&gt; streamMap =<span style="color:#000000;"> consumer.createMessageStreams(topickMap);
        KafkaStream</span>&lt;<span style="color:#0000ff;">byte</span>[], <span style="color:#0000ff;">byte</span>[]&gt; stream = streamMap.get(topic).get(0<span style="color:#000000;">);
        ConsumerIterator</span>&lt;<span style="color:#0000ff;">byte</span>[], <span style="color:#0000ff;">byte</span>[]&gt; it =<span style="color:#000000;"> stream.iterator();
        </span><span style="color:#0000ff;">while</span><span style="color:#000000;"> (it.hasNext()) {
            String value </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> String(it.next().message());
            LOGGER.info(</span>"(consumer)==&gt;" +<span style="color:#000000;"> value);
            collector.emit(</span><span style="color:#0000ff;">new</span><span style="color:#000000;"> Values(value), value);
        }
    }

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> deactivate() {
        </span><span style="color:#008000;">//</span><span style="color:#008000;"> TODO Auto-generated method stub</span>
<span style="color:#000000;">
    }

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> nextTuple() {
        </span><span style="color:#008000;">//</span><span style="color:#008000;"> TODO Auto-generated method stub</span>
<span style="color:#000000;">
    }

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> ack(Object msgId) {
        </span><span style="color:#008000;">//</span><span style="color:#008000;"> TODO Auto-generated method stub</span>
<span style="color:#000000;">
    }

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> fail(Object msgId) {
        </span><span style="color:#008000;">//</span><span style="color:#008000;"> TODO Auto-generated method stub</span>
<span style="color:#000000;">
    }

    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(</span><span style="color:#0000ff;">new</span> Fields("KafkaSpout"<span style="color:#000000;">));
    }

    </span><span style="color:#0000ff;">public</span> Map&lt;String, Object&gt;<span style="color:#000000;"> getComponentConfiguration() {
        </span><span style="color:#008000;">//</span><span style="color:#008000;"> TODO Auto-generated method stub</span>
        <span style="color:#0000ff;">return</span> <span style="color:#0000ff;">null</span><span style="color:#000000;">;
    }

}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<ul><li class="p1">KafkaTopology类：</li></ul><div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
<pre><span style="color:#0000ff;">package</span><span style="color:#000000;"> cn.hadoop.hdfs.storm.client;

</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> cn.hadoop.hdfs.storm.FileBlots;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> cn.hadoop.hdfs.storm.KafkaSpout;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> cn.hadoop.hdfs.storm.WordsCounterBlots;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.Config;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.LocalCluster;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.StormSubmitter;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.topology.TopologyBuilder;
</span><span style="color:#0000ff;">import</span><span style="color:#000000;"> backtype.storm.tuple.Fields;

</span><span style="color:#008000;">/**</span><span style="color:#008000;">
 * @Date Jun 10, 2015
 *
 * @Author dengjie
 *
 * @Note KafkaTopology Task
 </span><span style="color:#008000;">*/</span>
<span style="color:#0000ff;">public</span> <span style="color:#0000ff;">class</span><span style="color:#000000;"> KafkaTopology {
    </span><span style="color:#0000ff;">public</span> <span style="color:#0000ff;">static</span> <span style="color:#0000ff;">void</span><span style="color:#000000;"> main(String[] args) {
        TopologyBuilder builder </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> TopologyBuilder();
        builder.setSpout(</span>"testGroup", <span style="color:#0000ff;">new</span> KafkaSpout("test"<span style="color:#000000;">));
        builder.setBolt(</span>"file-blots", <span style="color:#0000ff;">new</span> FileBlots()).shuffleGrouping("testGroup"<span style="color:#000000;">);
        builder.setBolt(</span>"words-counter", <span style="color:#0000ff;">new</span> WordsCounterBlots(), 2).fieldsGrouping("file-blots", <span style="color:#0000ff;">new</span> Fields("words"<span style="color:#000000;">));
        Config config </span>= <span style="color:#0000ff;">new</span><span style="color:#000000;"> Config();
        config.setDebug(</span><span style="color:#0000ff;">true</span><span style="color:#000000;">);
        </span><span style="color:#0000ff;">if</span> (args != <span style="color:#0000ff;">null</span> &amp;&amp; args.length &gt; 0<span style="color:#000000;">) {
            </span><span style="color:#008000;">//</span><span style="color:#008000;"> online commit Topology</span>
            config.put(Config.NIMBUS_HOST, args[0<span style="color:#000000;">]);
            config.setNumWorkers(</span>3<span style="color:#000000;">);
            </span><span style="color:#0000ff;">try</span><span style="color:#000000;"> {
                StormSubmitter.submitTopologyWithProgressBar(KafkaTopology.</span><span style="color:#0000ff;">class</span><span style="color:#000000;">.getSimpleName(), config,
                        builder.createTopology());
            } </span><span style="color:#0000ff;">catch</span><span style="color:#000000;"> (Exception e) {
                e.printStackTrace();
            }
        } </span><span style="color:#0000ff;">else</span><span style="color:#000000;"> {
            </span><span style="color:#008000;">//</span><span style="color:#008000;"> Local commit jar</span>
            LocalCluster local = <span style="color:#0000ff;">new</span><span style="color:#000000;"> LocalCluster();
            local.submitTopology(</span>"counter"<span style="color:#000000;">, config, builder.createTopology());
            </span><span style="color:#0000ff;">try</span><span style="color:#000000;"> {
                Thread.sleep(</span>60000<span style="color:#000000;">);
            } </span><span style="color:#0000ff;">catch</span><span style="color:#000000;"> (InterruptedException e) {
                e.printStackTrace();
            }
            local.shutdown();
        }
    }
}</span></pre>
<div class="cnblogs_code_toolbar"><span class="cnblogs_code_copy"><a title="复制代码"><img src="http://common.cnblogs.com/images/copycode.gif" alt="复制代码"></a></span></div>
</div>
<h1>4.预览截图</h1>
<p>　　首先，我们启动Kafka集群，目前未生产任何消息，如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201507/091120429244353.png" alt=""></p>
<p>　　接下来，我们启动Flume集群，开始收集日志信息，将数据输送到Kafka集群，如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201507/091122176276553.png" alt=""></p>
<p>　　接下来，我们启动Storm UI来查看Storm提交的任务运行状况，如下图所示：<br><img src="http://images0.cnblogs.com/blog2015/666745/201507/091123177524952.png" alt=""></p>
<p>　　最后，将统计的结果持久化到Redis或者MySQL等DB中，结果如下图所示：</p>
<p><img src="http://images0.cnblogs.com/blog2015/666745/201507/091124342054795.png" alt=""></p>
<h1>5.总结</h1>
<p>　　这里给大家分享了数据的消费流程，并且给出了持久化的结果预览图，关于持久化的细节，后面有单独有一篇博客会详细的讲述，给大家分享其中的过程，这里大家熟悉下流程，预览结果即可。</p>
            </div>
                </div>
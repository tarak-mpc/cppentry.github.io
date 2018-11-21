---
layout:     post
title:      kafka安装配置及与logstash集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="font-family:'Helvetica Neue';font-size:14px;">1、kafka安装</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">下载   wget http://mirror.bit.edu.cn/apache/kafka/0.8.2.2/kafka_2.9.1-0.8.2.2.tgz</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">配置zookeeper     vim bin/zookeeper-server-start.sh   根据机器状况更改jvm 内存设置</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">配置kafka             vim bin/kafka-server-start.sh            根据机器状况更改jvm 内存设置</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">启动zookeeper       nohup bin/zookeeper-server-start.sh config/zookeeper.properties &amp;</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">启动kafka               nohup bin/kafka-server-start.sh config/server.properties &amp;</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">创建topic               bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic  pay</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">查看topic               bin/kafka-topics.sh --list --zookeeper localhost:2181</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">启动producer         bin/kafka-console-producer.sh --broker-list localhost:9092 --topic pay</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">启动consumer       bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic pay --from-beginning</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">多broker集群配置：</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">cp config/server.properties config/server-1.properties</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">cp config/server.properties config/server-2.properties</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">config/server-1.properties:</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">    broker.id=1</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">    listeners=PLAINTEXT://:9093</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">    log.dir=/tmp/kafka-logs-1</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">config/server-2.properties:</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">    broker.id=2</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">    listeners=PLAINTEXT://:9094</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">    log.dir=/tmp/kafka-logs-2</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">启动kafka       nohup bin/kafka-server-start.sh config/server-1.properties &amp;</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">                       nohup bin/kafka-server-start.sh config/server-2.properties &amp;</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">创建--replication-factor 3的topic</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"> bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic pay-replicated</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">查看topic描述：</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">bin/kafka-topics.sh --describe --zookeeper localhost:2181 --topic pay-replicated</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><img src="https://img-blog.csdn.net/20160719113737243?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">leader：负责所有的读和写的节点id（broker.id）</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">replicas：复制log的节点列表，不管他们是否是leader或是否alive</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">isr：in-sync的replicas子集合，是目前存活且被leader捕获的节点集。</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">停止当前leader(broker.id=1)后：</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><img src="https://img-blog.csdn.net/20160719113757884?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">启动producer         bin/kafka-console-producer.sh --broker-list localhost:9092 --topic pay-replicated</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">启动consumer       bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic pay-replicated --from-beginning</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">注意，multi-broker模式下，当只剩下一个broker时会有问题</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">2、logstash安装</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">下载  wget <a href="https://download.elastic.co/logstash/logstash/packages/centos/logstash-2.2.2-1.noarch.rpm" rel="nofollow">
https://download.elastic.co/logstash/logstash/packages/centos/logstash-2.2.2-1.noarch.rpm</a></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">安装  yum localinstall logstash-2.2.2-1.noarch.rpm</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">测试 /opt/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">配置文件目录 /etc/logstash/conf.d/xxx.conf</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">3、kafka+logstash整合</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">logstash1.5以后已经集成了对kafka的支持扩展，可以在conf配置中直接使用</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">vim /etc/logstash/conf.d/pay.conf</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">input {</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">        kafka{</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">                zk_connect =&gt; "your zookeeper address:2181"</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">                group_id =&gt; "logstash"</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">                topic_id =&gt; "pay-replicated"</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">                reset_beginning =&gt; false</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">                consumer_threads =&gt; 5</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">                decorate_events =&gt; true</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">        }</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">}</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">output {</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">#    stdout{ codec=&gt; rubydebug }</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">    redis {</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">        host =&gt; ["your redis address:6380"]</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">        batch =&gt; true</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">        key =&gt; "logstash-nginx-pay-replicated"</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">        data_type =&gt; "list"</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">    }</div>
<div style="font-family:'Helvetica Neue';font-size:14px;">}</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;">重启logstash</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"> service logstash restart</div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
<div style="font-family:'Helvetica Neue';font-size:14px;"><br></div>
            </div>
                </div>
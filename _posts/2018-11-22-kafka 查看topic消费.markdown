---
layout:     post
title:      kafka 查看topic消费
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p> </p>

<p>sudo su</p>

<p>su kafka</p>

<p><strong>加证书前：</strong></p>

<p>cd home/kafka/software/kafka/bin</p>

<p>./kafka-console-consumer.sh --zookeeper localhost:2181 --topic  monitor --from-beginning</p>

<p><strong>加证书后：</strong></p>

<p>cd home/kafka/software/kafka/</p>

<p>./bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --new-consumer --consumer.config config/consumer.properties --topic  monitor</p>

<p></p>            </div>
                </div>
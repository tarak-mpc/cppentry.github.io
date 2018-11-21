---
layout:     post
title:      kafka如何彻底删除topic及数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1>前言：</h1>

<p>删除kafka topic及其数据，严格来说并不是很难的操作。但是，往往给kafka 使用者带来诸多问题。项目组之前接触过多个开发者，发现都会偶然出现无法彻底删除kafka的情况。本文总结多个删除kafka topic的应用场景，总结一套删除kafka topic的标准操作方法。</p>

<p><strong>step1：</strong></p>

<p>如果需要被删除topic 此时正在被程序 produce和consume，则这些生产和消费程序需要停止。</p>

<p>因为如果有程序正在生产或者消费该topic，则该topic的offset信息一致会在broker更新。调用kafka delete命令则无法删除该topic。</p>

<p>同时，需要设置 auto.create.topics.enable = false，默认设置为true。如果设置为true，则produce或者fetch 不存在的topic也会自动创建这个topic。这样会给删除topic带来很多意向不到的问题。</p>

<p>所以，这一步很重要，必须设置auto.create.topics.enable = false，并认真把生产和消费程序彻底全部停止。</p>

<p><strong>step2：</strong></p>

<p>server.properties <strong>设置 delete.topic.enable=true</strong></p>

<p>如果没有设置 delete.topic.enable=true，则调用kafka 的delete命令无法真正将topic删除，而是显示（marked for deletion）</p>

<p><strong>step3：</strong></p>

<p><strong>调用命令删除topic：</strong></p>

<p>./bin/kafka-topics --delete --zookeeper 【zookeeper server:port】 --topic 【topic name】</p>

<p><strong>step4：</strong></p>

<p>删除kafka存储目录（server.properties文件log.dirs配置，默认为"/data/kafka-logs"）相关topic的数据目录。</p>

<p>注意：如果kafka 有多个 broker，且每个broker 配置了多个数据盘（比如 /data/kafka-logs,/data1/kafka-logs ...），且topic也有多个分区和replica，则需要对所有broker的所有数据盘进行扫描，删除该topic的所有分区数据。</p>

<p> </p>

<p>一般而言，经过上面4步就可以正常删除掉topic和topic的数据。但是，如果经过上面四步，还是无法正常删除topic，则需要对kafka在zookeeer的存储信息进行删除。具体操作如下：</p>

<p>（注意：以下步骤里面，kafka在zk里面的节点信息是采用默认值，如果你的系统修改过kafka在zk里面的节点信息，则需要根据系统的实际情况找到准确位置进行操作）</p>

<p><strong>step5：</strong></p>

<p>找一台部署了zk的服务器，使用命令：</p>

<p>bin/zkCli.sh -server 【zookeeper server:port】</p>

<p>登录到zk shell，然后找到topic所在的目录：ls /brokers/topics，找到要删除的topic，然后执行命令：</p>

<p>rmr /brokers/topics/【topic name】</p>

<p>即可，此时topic被彻底删除。</p>

<p>如果topic 是被标记为 marked for deletion，则通过命令 ls /admin/delete_topics，找到要删除的topic，然后执行命令：</p>

<p>rmr /admin/delete_topics/【topic name】</p>

<p><strong>备注：</strong></p>

<p>网络上很多其它文章还说明，需要删除topic在zk上面的消费节点记录、配置节点记录，比如：</p>

<p>rmr /consumers/【consumer-group】</p>

<p>rmr /config/topics/【topic name】</p>

<p>其实正常情况是不需要进行这两个操作的，如果需要，那都是由于操作不当导致的。比如step1停止生产和消费程序没有做，step2没有正确配置。也就是说，正常情况下严格按照step1 -- step5 的步骤，是一定能够正常删除topic的。</p>

<p><strong>step6：</strong></p>

<p>完成之后，调用命令：</p>

<p>./bin/kafka-topics.sh --list --zookeeper 【zookeeper server:port】</p>

<p>查看现在kafka的topic信息。正常情况下删除的topic就不会再显示。</p>

<p>但是，如果还能够查询到删除的topic，则重启zk和kafka即可。</p>

<p style="text-align:center;"><img alt="" class="has" src="https://img-blog.csdn.net/20180728101608523?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2JlbGFsZHM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p> </p>            </div>
                </div>
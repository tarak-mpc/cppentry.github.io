---
layout:     post
title:      Linux 安装kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhuyouyong/article/details/78921676				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:10px;"> </p>

<p>下载地址：<a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.1.1/kafka_2.10-0.8.1.1.tgz" rel="nofollow"><u><span style="color:#333333;">https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.1.1/kafka_2.10-0.8.1.1.tgz</span></u></a></p>

<h1><strong>安装以及启动kafka</strong></h1>

<h2><strong>步骤1：安装kafka</strong></h2>

<p> $<span style="color:#0000ff;">tar</span> -xzf kafka_2.<span style="color:#800080;">10</span>-<span style="color:#800080;">0.8</span>.<span style="color:#800080;">1.1</span>.tgz</p>

<p>$ cd kafka_2.<span style="color:#800080;">10</span>-<span style="color:#800080;">0.8</span>.<span style="color:#800080;">1.1</span>.tgz</p>

<p> </p>

<h2><strong>步骤2：配置server.properties</strong></h2>

<p> 配置zookeeper(假设您已经安装了zookeeper,如果没有安装，请再网上搜索安装方法)</p>

<p>进入kafka安装工程根目录编辑 </p>

<p>vim config/server.properties  </p>

<p>修改属性 zookeeper.connect=ip:2181,ip2: 2181</p>

<p> </p>

<h2><strong>步骤3：server.properties配置说明</strong></h2>

<p>kafka最为重要三个配置依次为：broker.id、log.dir、zookeeper.connect</p>

<p>kafka server端config/server.properties参数说明和解释如下:</p>

<p>（参考配置说明地址：http://blog.csdn.net/lizhitao/article/details/25667831）</p>

<p>#实际使用案例 这里211上面的kafka配置文件</p>

<p>broker.id=1</p>

<p>port=<span style="color:#800080;">9092</span></p>

<p>host.name=<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.211</span></p>

<p>num.network.threads=<span style="color:#800080;">2</span></p>

<p>num.io.threads=<span style="color:#800080;">8</span></p>

<p>socket.send.buffer.bytes=<span style="color:#800080;">1048576</span></p>

<p>socket.receive.buffer.bytes=<span style="color:#800080;">1048576</span></p>

<p>socket.request.max.bytes=<span style="color:#800080;">104857600</span></p>

<p>log.dirs=/tmp/kafka-logs</p>

<p>num.partitions=<span style="color:#800080;">2</span></p>

<p>log.retention.hours=<span style="color:#800080;">168</span></p>

<p>log.segment.bytes=<span style="color:#800080;">536870912</span></p>

<p>log.retention.check.interval.ms=<span style="color:#800080;">60000</span></p>

<p>log.cleaner.enable=<span style="color:#0000ff;">false</span></p>

<p>zookeeper.connect=<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.213</span>:<span style="color:#800080;">2181</span>,<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.216</span>:<span style="color:#800080;">2181</span>,<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.217</span>:<span style="color:#800080;">2181</span></p>

<p>zookeeper.connection.timeout.ms=<span style="color:#800080;">1000000</span></p>

<p>#kafka实际使用案例 210服务器kafka配置</p>

<p>broker.<span style="color:#0000ff;">id</span>=<span style="color:#800080;">2</span></p>

<p>port=<span style="color:#800080;">9092</span></p>

<p>host.name=<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.210</span></p>

<p>num.network.threads=<span style="color:#800080;">2</span></p>

<p>num.io.threads=<span style="color:#800080;">8</span></p>

<p>socket.send.buffer.bytes=<span style="color:#800080;">1048576</span></p>

<p>socket.receive.buffer.bytes=<span style="color:#800080;">1048576</span></p>

<p>socket.request.max.bytes=<span style="color:#800080;">104857600</span></p>

<p>log.dirs=/tmp/kafka-logs</p>

<p>num.partitions=<span style="color:#800080;">2</span></p>

<p>log.retention.hours=<span style="color:#800080;">168</span></p>

<p>log.segment.bytes=<span style="color:#800080;">536870912</span></p>

<p>log.retention.check.interval.ms=<span style="color:#800080;">60000</span></p>

<p>log.cleaner.enable=<span style="color:#0000ff;">false</span></p>

<p>zookeeper.connect=<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.213</span>:<span style="color:#800080;">2181</span>,<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.216</span>:<span style="color:#800080;">2181</span>,<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.217</span>:<span style="color:#800080;">2181</span></p>

<p>zookeeper.connection.timeout.ms=<span style="color:#800080;">1000000</span> </p>

<h2><strong>步骤4： 启动kafka</strong></h2>

<p>(先启动zookeeper $:  bin/zkServer.sh start config/zookeeper.properties &amp;)</p>

<p>cd kafka-0.8.1</p>

<p>$ bin/kafka-server-start.<span style="color:#0000ff;">sh</span> -daemon config/server.properties &amp;</p>

<p> (实验时,需要启动至少两个broker   bin/kafka-server-start.sh -daemon config/server-1.properties &amp;) </p>

<h2><strong>步骤5：创建topic</strong></h2>

<p>$ bin/kafka-topics.<span style="color:#0000ff;">sh</span> --create --zookeeper localhost:<span style="color:#800080;">2181</span> --replication-factor<span style="color:#800080;">1</span> --partitions<span style="color:#800080;">1</span> --topic test</p>

<p><strong>步骤6：验证topic是否创建成功</strong></p>

<p> </p>

<p>$ bin/kafka-topics.<span style="color:#0000ff;">sh</span> --list --zookeeper localhost:<span style="color:#800080;">2181</span></p>

<p>localhost为zookeeper地址 </p>

<p>topic描述：</p>

<p>bin/kafka-topics.<span style="color:#0000ff;">sh</span> --describe --zookeeper<span style="color:#800080;">192.168</span>.<span style="color:#800080;">1.8</span>:<span style="color:#800080;">2181</span> --topic test</p>

<h2><strong>步骤7：查看topic</strong></h2>

<p> </p>

<p>bin/kafka-topics.<span style="color:#0000ff;">sh</span>--create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test</p>

<h2><strong>步骤8：发送消息</strong></h2>

<p> </p>

<p>bin/kafka-console-producer.sh  --zookeeper localhost:2181 --topic test</p>

<h2><strong>步骤9：接收消息</strong></h2>

<p> </p>

<p>bin/kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning</p>

<p> </p>            </div>
                </div>
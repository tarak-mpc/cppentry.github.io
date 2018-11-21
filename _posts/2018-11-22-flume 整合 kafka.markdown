---
layout:     post
title:      flume 整合 kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012935445/article/details/79045613				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
flume 整合 kafka：</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
flume 采集业务日志，发送到kafka</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
安装部署Kafka</div>
<h1 style="font-size:28px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
Download</h1>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
1.0.0 is the latest release. The current stable version is 1.0.0.</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
You can verify your download by following these <a href="http://www.apache.org/info/verification.html" rel="nofollow" style="color:rgb(0,0,0);">procedures</a> and using these <a href="https://kafka.apache.org/KEYS" rel="nofollow" style="color:rgb(0,0,0);">KEYS</a>.</div>
<h3 style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
1.0.0</h3>
<ul style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);"><li><ul><li style="list-style-type:disc;">Released November 1, 2017</li><li style="list-style-type:disc;">Source download: <a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/1.0.0/kafka-1.0.0-src.tgz" rel="nofollow" style="color:rgb(0,0,0);">kafka-1.0.0-src.tgz</a> (<a href="https://www.apache.org/dist/kafka/1.0.0/kafka-1.0.0-src.tgz.asc" rel="nofollow" style="color:rgb(0,0,0);">asc</a>, <a href="https://www.apache.org/dist/kafka/1.0.0/kafka-1.0.0-src.tgz.sha512" rel="nofollow" style="color:rgb(0,0,0);">sha512</a>)</li><li style="list-style-type:disc;">Binary downloads:
<ul><li style="list-style-type:disc;">Scala 2.11  - <a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/1.0.0/kafka_2.11-1.0.0.tgz" rel="nofollow" style="color:rgb(0,0,0);">kafka_2.11-1.0.0.tgz</a> (<a href="https://www.apache.org/dist/kafka/1.0.0/kafka_2.11-1.0.0.tgz.asc" rel="nofollow" style="color:rgb(0,0,0);">asc</a>, <a href="https://www.apache.org/dist/kafka/1.0.0/kafka_2.11-1.0.0.tgz.sha512" rel="nofollow" style="color:rgb(0,0,0);">sha512</a>)</li><li style="list-style-type:disc;">Scala 2.12  - <a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/1.0.0/kafka_2.12-1.0.0.tgz" rel="nofollow" style="color:rgb(0,0,0);">kafka_2.12-1.0.0.tgz</a> (<a href="https://www.apache.org/dist/kafka/1.0.0/kafka_2.12-1.0.0.tgz.asc" rel="nofollow" style="color:rgb(0,0,0);">asc</a>, <a href="https://www.apache.org/dist/kafka/1.0.0/kafka_2.12-1.0.0.tgz.sha512" rel="nofollow" style="color:rgb(0,0,0);">sha512</a>)</li></ul></li></ul></li></ul><div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
We build for multiple versions of Scala. This only matters if you are using Scala and you want a version built for the same Scala version you use. Otherwise any version should work (2.11 is recommended).</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
1.解压：tar zxvf <a href="https://www.apache.org/dyn/closer.cgi?path=/kafka/1.0.0/kafka_2.11-1.0.0.tgz" rel="nofollow" style="color:rgb(0,0,0);">kafka_2.11-1.0.0.tgz</a></div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
2.部署目录：mv kafka_2.12-1.0.0 /usr/local/kafka2.12</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
3.启动zookeeper ....</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
4.启动kafka：</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
#nohup bin/kafka-server-start.sh config/server.properties &amp;</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<h3 style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
5.创建topic：</h3>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
#bin/kafka-topics.sh --create --zookeeper localhost:2181 --partitions 1 --replication-factor 1 --topic test</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
Created topic "test".</div>
<h3 style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
6.查看topic：</h3>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
# bin/kafka-topics.sh --list --zookeeper localhost:2181</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
__consumer_offsets</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
test</div>
<h3 style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
7.测试发送数据</h3>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
#bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
输入：my test</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
8.测试消费消息：</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
#bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
安装部署flume</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
flume下载：</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<h1 style="font-size:28px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
Download</h1>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
Apache Flume is distributed under the <a href="http://www.apache.org/licenses/LICENSE-2.0.html" rel="nofollow" style="color:rgb(0,0,0);">Apache License, version 2.0</a></div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
The link in the Mirrors column should display a list of available mirrors with a default selection based on your inferred location. If you do not see that page, try a different browser. The checksum and signature are links to the originals on the main distribution
 server.</div>
<table border="1" style="margin-left:0px;border-color:#C0C0C0;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;"><colgroup><col><col><col><col><col></colgroup><tbody valign="top"><tr><td style="border-color:#C0C0C0;border-collapse:collapse;">
Apache Flume binary (tar.gz)</td>
<td style="border-color:#C0C0C0;border-collapse:collapse;">
<a href="http://www.apache.org/dyn/closer.lua/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-bin.tar.gz</a></td>
<td style="border-color:#C0C0C0;border-collapse:collapse;">
<a href="http://www.apache.org/dist/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz.md5" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-bin.tar.gz.md5</a></td>
<td style="border-color:#C0C0C0;border-collapse:collapse;">
<a href="http://www.apache.org/dist/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz.sha1" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-bin.tar.gz.sha1</a></td>
<td style="border-color:#C0C0C0;border-collapse:collapse;">
<a href="http://www.apache.org/dist/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz.asc" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-bin.tar.gz.asc</a></td>
</tr><tr><td style="border-color:#C0C0C0;border-collapse:collapse;">
Apache Flume source (tar.gz)</td>
<td style="border-color:#C0C0C0;border-collapse:collapse;">
<a href="http://www.apache.org/dyn/closer.lua/flume/1.8.0/apache-flume-1.8.0-src.tar.gz" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-src.tar.gz</a></td>
<td style="border-color:#C0C0C0;border-collapse:collapse;">
<a href="http://www.apache.org/dist/flume/1.8.0/apache-flume-1.8.0-src.tar.gz.md5" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-src.tar.gz.md5</a></td>
<td style="border-color:#C0C0C0;border-collapse:collapse;">
<a href="http://www.apache.org/dist/flume/1.8.0/apache-flume-1.8.0-src.tar.gz.sha1" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-src.tar.gz.sha1</a></td>
<td style="border-color:#C0C0C0;border-collapse:collapse;">
<a href="http://www.apache.org/dist/flume/1.8.0/apache-flume-1.8.0-src.tar.gz.asc" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-src.tar.gz.asc</a></td>
</tr></tbody></table><div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
It is essential that you verify the integrity of the downloaded files using the PGP or MD5 signatures. Please read <a href="http://httpd.apache.org/dev/verification.html" rel="nofollow" style="color:rgb(0,0,0);">Verifying Apache HTTP
 Server Releases</a> for more information on why you should verify our releases.</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
1.下载：wget <a href="http://www.apache.org/dyn/closer.lua/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz" rel="nofollow" style="color:rgb(0,0,0);">apache-flume-1.8.0-bin.tar.gz</a></div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
2.解压：tar zxvf apache-flume-1.8.0-bin.tar.gz</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
3.设置目录：mv apache-flume-1.8.0-bin /usr/local/flume1.8</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<h2 style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
4.准备工作：</h2>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
<div>
<div>安装java并设置java环境变量,flume环境变量，在`/etc/profile`中加入</div>
<div> </div>
<div>export JAVA_HOME=/usr/java/jdk1.8.0_65</div>
<div>export FLUME_HOME=/usr/local/flume1.8</div>
<div>export PATH=$PATH:$JAVA_HOME/bin:$FLUME_HOME</div>
<div> </div>
</div>
</div>
<h2 style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
执行：source /etc/profile 生效变量</h2>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
5.建立log采集目录：</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
/tmp/logs/kafka.log</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
6.配置</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
拷贝配置模板：</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
# cp conf/flume-conf.properties.template conf/flume-conf.properties</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
# cp conf/flume-env.properties.template conf/flume-env.properties</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
编辑配置如下：</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
<div>
<div>agent.sources = s1                                                                                                                  </div>
<div>agent.channels = c1                                                                                                                 </div>
<div>agent.sinks = k1                                                                                                                    </div>
<div>                                                                                                                                      </div>
<div>agent.sources.s1.type=exec                                     </div>
<div>#日志采集位置                                                                     </div>
<div>agent.sources.s1.command=tail -F /tmp/logs/kafka.log                                                                                </div>
<div>agent.sources.s1.channels=c1                                                                                                        </div>
<div>agent.channels.c1.type=memory                                                                                                       </div>
<div>agent.channels.c1.capacity=10000                                                                                                    </div>
<div>agent.channels.c1.transactionCapacity=100                                                                                           </div>
<div>                                                                                                                                      </div>
<div>agent.sinks.k1.type= org.apache.flume.sink.kafka.KafkaSink    </div>
<div>#kafka 地址                                                                      </div>
<div>agent.sinks.k1.brokerList=localhost:9092   </div>
<div>#kafka topic                                                                                         </div>
<div>agent.sinks.k1.topic=test                                                                                                      </div>
<div>agent.sinks.k1.serializer.class=kafka.serializer.StringEncoder                                                                      </div>
<div>                                                                                                                                      </div>
<div>agent.sinks.k1.channel=c1</div>
</div>
</div>
<h2 style="font-size:21px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
功能验证</h2>
<h3 style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
7.启动服务</h3>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
# bin/flume-ng agent --conf ./conf/ -f conf/kafka.properties -Dflume.root.logger=DEBUG,console -n agent</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
运行日志位于logs目录，或者启动时添加-Dflume.root.logger=INFO,console 选项前台启动，输出打印日志，查看具体运行日志，服务异常时查原因。</div>
<h3 style="font-size:16px;line-height:1.5;color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;background-color:rgb(255,255,255);">
8.创建测试日志生成：log_producer_test.sh</h3>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
<div>for((i=0;i&lt;=1000;i++));</div>
<div>do echo "kafka_flume_test-"+$i&gt;&gt;/tmp/logs/kafka.log;</div>
<div>do</div>
</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
9.生成日志：</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
./log_producer_test.sh</div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
 </div>
<div style="color:rgb(51,51,51);font-family:Verdana, Arial, Helvetica, sans-serif;font-size:14px;background-color:rgb(255,255,255);">
观察kafka日志消费情况。。。</div>
            </div>
                </div>
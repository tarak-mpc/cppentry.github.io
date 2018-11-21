---
layout:     post
title:      log4j+flume+kafka+strom整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div style="background-color:inherit;"><span style="color:#ff0000;background-color:inherit;font-size:22px;"> log4j+flume+kafka+strom整合  </span></div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">整合流程图。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<br style="background-color:inherit;"></td>
</tr></tbody></table></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;">第一步  整合log4j和flume（主机名为hadoop0）</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(1) 安装flume在<span style="background-color:inherit;line-height:1.5;">/usr/softinstall/下</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(2)在<span style="background-color:inherit;line-height:1.5;">/usr/softinstall/flume/conf 目录下建立文件</span><span style="background-color:inherit;line-height:1.5;">lfks.conf</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;line-height:1.5;"><br style="background-color:inherit;"></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">a1.channels = c1                                </div>
<div style="min-width:2px;background-color:inherit;">a1.sources =s1                        </div>
<div style="min-width:2px;background-color:inherit;">a1.sinks = k1                             </div>
<div style="min-width:2px;background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"># 定义channel                                        </div>
<div style="min-width:2px;background-color:inherit;">a1.channels.c1.type = memory                    </div>
<div style="min-width:2px;background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"># 定义source                                         </div>
<div style="min-width:2px;background-color:inherit;">a1.sources.s1.channels = c1           </div>
<div style="min-width:2px;background-color:inherit;">a1.sources.s1.type = avro              </div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'微软雅黑';font-size:14px;"> #  接受任何IP主机的日志。</span><br style="background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;">a1.sources.s1.bind = 0.0.0.0   </div>
<div style="min-width:2px;background-color:inherit;"><span style="font-family:'微软雅黑';font-size:14px;">#  监听主机端口号。</span><br style="background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;">a1.sources.s1.port = 41414            </div>
<div style="min-width:2px;background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"># 定义sink              </div>
<div style="min-width:2px;background-color:inherit;">a1.sinks.k1.channel = c1</div>
<div style="min-width:2px;background-color:inherit;"><span style="background-color:inherit;line-height:1.5;">a1.sinks.k1.type = logger</span>                             </div>
</td>
</tr></tbody></table>
(3)建立maven项目往pom.xml导入jar依赖</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">&lt;dependencies&gt;</div>
<div style="background-color:inherit;">        &lt;dependency&gt;</div>
<div style="background-color:inherit;">            &lt;groupId&gt;junit&lt;/groupId&gt;</div>
<div style="background-color:inherit;">            &lt;artifactId&gt;junit&lt;/artifactId&gt;</div>
<div style="background-color:inherit;">            &lt;version&gt;3.8.1&lt;/version&gt;</div>
<div style="background-color:inherit;">            &lt;scope&gt;test&lt;/scope&gt;</div>
<div style="background-color:inherit;">        &lt;/dependency&gt;</div>
<div style="background-color:inherit;">        &lt;dependency&gt;</div>
<div style="background-color:inherit;">            &lt;groupId&gt;log4j&lt;/groupId&gt;</div>
<div style="background-color:inherit;">            &lt;artifactId&gt;log4j&lt;/artifactId&gt;</div>
<div style="background-color:inherit;">            &lt;version&gt;1.2.17&lt;/version&gt;</div>
<div style="background-color:inherit;">        &lt;/dependency&gt;</div>
<div style="background-color:inherit;">        &lt;dependency&gt;</div>
<div style="background-color:inherit;">            &lt;groupId&gt;org.apache.flume.flume-ng-clients&lt;/groupId&gt;</div>
<div style="background-color:inherit;">            &lt;artifactId&gt;flume-ng-log4jappender&lt;/artifactId&gt;</div>
<div style="background-color:inherit;">            &lt;version&gt;1.5.2&lt;/version&gt;</div>
<div style="background-color:inherit;">        &lt;/dependency&gt;</div>
<div style="background-color:inherit;">        &lt;dependency&gt;</div>
<div style="background-color:inherit;">            &lt;groupId&gt;org.apache.flume&lt;/groupId&gt;</div>
<div style="background-color:inherit;">            &lt;artifactId&gt;flume-ng-core&lt;/artifactId&gt;</div>
<div style="background-color:inherit;">            &lt;version&gt;1.5.2&lt;/version&gt;</div>
<div style="background-color:inherit;">        &lt;/dependency&gt;</div>
<div style="background-color:inherit;">        &lt;dependency&gt;</div>
<div style="background-color:inherit;">            &lt;groupId&gt;org.apache.kafka&lt;/groupId&gt;</div>
<div style="background-color:inherit;">            &lt;artifactId&gt;kafka_2.10&lt;/artifactId&gt;</div>
<div style="background-color:inherit;">            &lt;version&gt;0.8.2.1&lt;/version&gt;</div>
<div style="background-color:inherit;">        &lt;/dependency&gt;</div>
<div style="background-color:inherit;">        &lt;dependency&gt;</div>
<div style="background-color:inherit;">         &lt;groupId&gt;org.apache.storm&lt;/groupId&gt;</div>
<div style="background-color:inherit;">         &lt;artifactId&gt;storm-core&lt;/artifactId&gt;</div>
<div style="background-color:inherit;">         &lt;version&gt;0.9.3&lt;/version&gt;</div>
<div style="background-color:inherit;">       &lt;/dependency&gt;</div>
<div style="background-color:inherit;">       &lt;dependency&gt;</div>
<div style="background-color:inherit;">        &lt;groupId&gt;org.apache.storm&lt;/groupId&gt;</div>
<div style="background-color:inherit;">        &lt;artifactId&gt;storm-kafka&lt;/artifactId&gt;</div>
<div style="background-color:inherit;">        &lt;version&gt;0.9.3&lt;/version&gt;</div>
<div style="background-color:inherit;">       &lt;/dependency&gt;</div>
<div style="background-color:inherit;">    &lt;/dependencies&gt;</div>
</div>
</td>
</tr></tbody></table>
(3) 建立<span style="background-color:inherit;line-height:1.5;">LogsFlume类产生日志</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">package com.east.lfks;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">import org.apache.log4j.Logger;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">public class LogsFlume {</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">    private static Logger logger = Logger.getLogger(LogsFlume.class);</div>
<div style="background-color:inherit;">    public static void main(String[] args) {</div>
<div style="background-color:inherit;">        // System.out.println("This is println message.");</div>
<div style="background-color:inherit;">        // 记录debug级别的信息</div>
<div style="background-color:inherit;">        while(true){</div>
<div style="background-color:inherit;">            logger.debug("This is debug message.");</div>
<div style="background-color:inherit;">            // 记录info级别的信息</div>
<div style="background-color:inherit;">            logger.info("my name is liu xiang ke");</div>
<div style="background-color:inherit;">            // 记录error级别的信息</div>
<div style="background-color:inherit;">            logger.error("my work is it  i love my work.");</div>
<div style="background-color:inherit;">            try {</div>
<div style="background-color:inherit;">                Thread.sleep(6000);</div>
<div style="background-color:inherit;">            } catch (InterruptedException e) {</div>
<div style="background-color:inherit;">                // TODO Auto-generated catch block</div>
<div style="background-color:inherit;">                e.printStackTrace();</div>
<div style="background-color:inherit;">            }</div>
<div style="background-color:inherit;">        }</div>
<div style="background-color:inherit;">    }</div>
<div style="background-color:inherit;">}</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
</td>
</tr></tbody></table>
(4)建立log4j.properties</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">log4j.rootLogger=INFO,flume</div>
<div style="background-color:inherit;">log4j.appender.flume = org.apache.flume.clients.log4jappender.Log4jAppender</div>
<div style="background-color:inherit;">log4j.appender.flume.Hostname = hadoop0</div>
<div style="background-color:inherit;">log4j.appender.flume.Port = 41414</div>
<div style="background-color:inherit;">log4j.appender.flume.UnsafeMode = true</div>
</div>
</td>
</tr></tbody></table>
(5)在flume目录下运行命令</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;"><span style="color:#00b050;background-color:inherit;">bin/flume-ng agent -c conf -f conf/testconf/lfks.conf -n a1 -Dflume.root.logger=INFO,console</span></div>
</td>
</tr></tbody></table>
(6)运行LogsFlume 类，参考结果</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;"><span style="background-color:inherit;line-height:1.5;">15/12/17 22:19:29 INFO sink.LoggerSink: Event: { headers:{flume.client.log4j.log.level=40000, flume.client.log4j.message.encoding=UTF8, flume.client.log4j.logger.name=com.east.testlfks.TestLogsFlume, flume.client.log4j.timestamp=1450419569909} body: 6D 79
 20 77 6F 72 6B 20 69 73 20 69 74 20 20 69 my work is it  i }</span></div>
<div style="min-width:2px;background-color:inherit;">15/12/17 22:19:35 INFO sink.LoggerSink: Event: { headers:{flume.client.log4j.log.level=20000, flume.client.log4j.message.encoding=UTF8, flume.client.log4j.logger.name=com.east.testlfks.TestLogsFlume, flume.client.log4j.timestamp=1450419575911} body: 6D 79
 20 6E 61 6D 65 20 69 73 20 6C 69 75 20 78 my name is liu x }</div>
<div style="min-width:2px;background-color:inherit;">15/12/17 22:19:35 INFO sink.LoggerSink: Event: { headers:{flume.client.log4j.log.level=40000, flume.client.log4j.message.encoding=UTF8, flume.client.log4j.logger.name=com.east.testlfks.TestLogsFlume, flume.client.log4j.timestamp=1450419575925} body: 6D 79
 20 77 6F 72 6B 20 69 73 20 69 74 20 20 69 my work is it  i }</div>
<div style="min-width:2px;background-color:inherit;">15/12/17 22:19:41 INFO sink.LoggerSink: Event: { headers:{flume.client.log4j.log.level=20000, flume.client.log4j.message.encoding=UTF8, flume.client.log4j.logger.name=com.east.testlfks.TestLogsFlume, flume.client.log4j.timestamp=1450419581929} body: 6D 79
 20 6E 61 6D 65 20 69 73 20 6C 69 75 20 78 my name is liu x }</div>
<div style="min-width:2px;background-color:inherit;">15/12/17 22:19:41 INFO sink.LoggerSink: Event: { headers:{flume.client.log4j.log.level=40000, flume.client.log4j.message.encoding=UTF8, flume.client.log4j.logger.name=com.east.testlfks.TestLogsFlume, flume.client.log4j.timestamp=1450419581934} body: 6D 79
 20 77 6F 72 6B 20 69 73 20 69 74 20 20 69 my work is it  i }</div>
<div style="min-width:2px;background-color:inherit;">15/12/17 22:19:47 INFO sink.LoggerSink: Event: { headers:{flume.client.log4j.log.level=20000, flume.client.log4j.message.encoding=UTF8, flume.client.log4j.logger.name=com.east.testlfks.TestLogsFlume, flume.client.log4j.timestamp=1450419587940} body: 6D 79
 20 6E 61 6D 65 20 69 73 20 6C 69 75 20 78 my name is liu x }</div>
<div style="min-width:2px;background-color:inherit;">15/12/17 22:19:47 INFO sink.LoggerSink: Event: { headers:{flume.client.log4j.log.level=40000, flume.client.log4j.message.encoding=UTF8, flume.client.log4j.logger.name=com.east.testlfks.TestLogsFlume, flume.client.log4j.timestamp=1450419587943} body: 6D 79
 20 77 6F 72 6B 20 69 73 20 69 74 20 20 69 my work is it  i }</div>
</td>
</tr></tbody></table>
log4j和flume整合成功。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;">第二步  整合flume和kafka</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(1) 我使用的flume版本 是Flume 1.6.0，在1.6版本开始可以直接配置文件来实现flume的sink给kafka传送消息,不需要再自定义kafkasink。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">[<a href="mailto:root@hadoop0" rel="nofollow" style="background-color:inherit;">root@hadoop0</a> bin]# flume-ng version</div>
<div style="background-color:inherit;">Flume 1.6.0</div>
<div style="background-color:inherit;">Source code repository: <a href="https://git-wip-us.apache.org/repos/asf/flume.git" rel="nofollow" style="background-color:inherit;">https://git-wip-us.apache.org/repos/asf/flume.git</a></div>
<div style="background-color:inherit;">Revision: 2561a23240a71ba20bf288c7c2cda88f443c2080</div>
<div style="background-color:inherit;">Compiled by hshreedharan on Mon May 11 11:15:44 PDT 2015</div>
<div style="background-color:inherit;">From source with checksum b29e416802ce9ece3269d34233baf43f</div>
</div>
</td>
</tr></tbody></table>
(2)修改配置/usr/softinstall/flume/conf /lfks.conf</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">a1.channels = c1                                </div>
<div style="background-color:inherit;">a1.sources =s1                        </div>
<div style="background-color:inherit;">a1.sinks = k1                             </div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># 定义channel                                        </div>
<div style="background-color:inherit;">a1.channels.c1.type = memory                    </div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># 定义source                                         </div>
<div style="background-color:inherit;">a1.sources.s1.channels = c1           </div>
<div style="background-color:inherit;">a1.sources.s1.type = avro              </div>
<div style="background-color:inherit;">a1.sources.s1.bind = 0.0.0.0     </div>
<div style="background-color:inherit;">a1.sources.s1.port = 41414           </div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"># 定义sink           </div>
<div style="background-color:inherit;">a1.sinks.k1.type = org.apache.flume.sink.kafka.KafkaSink</div>
<div style="background-color:inherit;">a1.sinks.k1.topic = mytest</div>
<div style="background-color:inherit;">a1.sinks.k1.brokerList = hadoop0:9092,hadoop1:9092,hadoop2:9092</div>
<div style="background-color:inherit;">a1.sinks.k1.requiredAcks = 1</div>
<div style="background-color:inherit;">a1.sinks.k1.batchSize = 20</div>
<div style="background-color:inherit;">a1.sinks.k1.channel = c1 </div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
</td>
</tr></tbody></table></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(3)安装kafka集群，先启动zookeeper集群，然后在启动kafka集群，保证kafka集群是能正常运行的。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;"><span style="color:rgb(0,176,80);font-family:'微软雅黑';font-size:14px;">在各个节点（hadoop0，hadoop1，hadoop2）启动kafka集群</span></div>
<div style="min-width:2px;background-color:inherit;">
<hr style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;"><span style="color:rgb(0,176,80);font-family:'微软雅黑';font-size:14px;">bin/kafka-server-start.sh config/server.properties &amp;</span><br style="background-color:inherit;"></div>
</td>
</tr></tbody></table>
(4)创建topic</div>
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;font-family:'微软雅黑';"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="font-family:'微软雅黑';font-size:14px;">
<div style="background-color:inherit;"><span style="color:#00b050;background-color:inherit;"> bin/kafka-topics.sh --create  --zookeeper hadoop0:2181,hadoop1:2181,hadoop2:2181 --replication-factor 3 --partitions 6 --topic mytest</span></div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;"><span style="color:#00b050;background-color:inherit;"><br style="background-color:inherit;"></span></div>
<div style="font-family:'微软雅黑';font-size:14px;"><span style="color:#00b050;background-color:inherit;"><span style="color:rgb(55,55,55);font-family:'Open Sans', sans-serif;font-size:13.6364px;background-color:rgb(245,248,253);">factor大小不能超过broker数</span></span></div>
<br style="background-color:inherit;"></div>
</td>
</tr></tbody></table><div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;line-height:1.5;">(5)</span><span style="background-color:rgb(245,248,253);line-height:1.5;color:rgb(55,55,55);font-family:'Open Sans', sans-serif;">通过以下命令查看主题topic</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">[<a href="mailto:root@hadoop1" rel="nofollow" style="background-color:inherit;">root@hadoop1</a> kafka]# bin/kafka-topics.sh --list --zookeeper hadoop0:2181,hadoop1:2181,hadoop2:2181</div>
<div style="background-color:inherit;">mytest</div>
</div>
</td>
</tr></tbody></table></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(6)<span style="background-color:rgb(245,248,253);color:rgb(55,55,55);font-family:'Open Sans', sans-serif;line-height:1.5;">通过下述命令可以看到该主题详情</span></div>
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;font-family:'微软雅黑';"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;"><span style="color:#00b050;background-color:inherit;">bin/kafka-topics.sh --describe --zookeeper hadoop0:2181,hadoop1:2181,hadoop2:2181 --topic mytest</span></div>
<div style="min-width:2px;background-color:inherit;">Topic:mytest    PartitionCount:6        ReplicationFactor:3     Configs:</div>
<div style="min-width:2px;background-color:inherit;">        Topic: mytest   Partition: 0    Leader: 0       Replicas: 0,1,2 Isr: 0,1,2</div>
<div style="min-width:2px;background-color:inherit;">        Topic: mytest   Partition: 1    Leader: 1       Replicas: 1,2,0 Isr: 1,2,0</div>
<div style="min-width:2px;background-color:inherit;">        Topic: mytest   Partition: 2    Leader: 2       Replicas: 2,0,1 Isr: 2,0,1</div>
<div style="min-width:2px;background-color:inherit;">        Topic: mytest   Partition: 3    Leader: 0       Replicas: 0,2,1 Isr: 0,2,1</div>
<div style="min-width:2px;background-color:inherit;">        Topic: mytest   Partition: 4    Leader: 1       Replicas: 1,0,2 Isr: 1,0,2</div>
<div style="min-width:2px;background-color:inherit;">        Topic: mytest   Partition: 5    Leader: 2       Replicas: 2,1,0 Isr: 2,1,0</div>
<div style="min-width:2px;background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;">
<pre style="font-family:Monaco, Consolas, Courier, 'Lucida Console', monospace;background-color:rgb(255,255,255);">下面解释一下这些输出。第一行是对所有分区的一个描述，然后每个分区都会对应一行，因为我们只有一个分区所以下面就只加了一行。
</pre>
<div style="font-family:Tahoma;">
<ul style="background-color:inherit;"><li style="background-color:inherit;">leader：负责处理消息的读和写，leader是从所有节点中随机选择的.</li><li style="background-color:inherit;">replicas：列出了所有的副本节点，不管节点是否在服务中.</li><li style="background-color:inherit;">isr：是正在服务中的节点.</li></ul></div>
</div>
</td>
</tr></tbody></table><div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(7)<span style="background-color:rgb(245,248,253);color:rgb(55,55,55);font-family:'Open Sans', sans-serif;line-height:1.5;">生产者生成消息和消费者消费消息</span></div>
<p style="font-size:14px;line-height:21px;color:rgb(55,55,55);font-family:'Open Sans', sans-serif;background-color:rgb(245,248,253);">
在hadoop0上建立生产者角色，并发送消息（其实可以是三台机子中的任何一台）</p>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;"> <span style="color:#00b050;background-color:inherit;">bin/kafka-console-producer.sh --broker-list hadoop1:9092 --topic mytest   </span>  </div>
<div style="background-color:inherit;">this is a message</div>
</div>
<div style="min-width:2px;background-color:inherit;">this is the second message</div>
</td>
</tr></tbody></table></div>
<p style="font-size:14px;line-height:21px;color:rgb(55,55,55);font-family:'Open Sans', sans-serif;background-color:rgb(245,248,253);">
在hadoop2上建立消费者角色（在该终端窗口内可以看到生产者发布这消息）</p>
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;font-family:'微软雅黑';"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">[<a href="mailto:root@hadoop2" rel="nofollow" style="background-color:inherit;">root@hadoop2</a> kafka]# <span style="color:#00b050;background-color:inherit;">bin/kafka-console-consumer.sh --zookeeper hadoop1:2181 --topic mytest --from-beginning</span></div>
<div style="background-color:inherit;">this is a message</div>
<div style="background-color:inherit;">this is the second message</div>
</div>
<div style="min-width:2px;background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;">
<hr style="background-color:inherit;">
kafka 集群安装成功，能正常运行。</div>
</td>
</tr></tbody></table><div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(8)在flume,kafka均启动的情况下，运行LogsFlume类产生日志，再再hadoop2消费者窗口下是否接收到了日志产生的消息。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;"></div>
<div style="min-width:2px;background-color:inherit;">
<hr style="background-color:inherit;">
接受到消息，成功！</div>
</td>
</tr></tbody></table></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="background-color:inherit;">第三步 kafka与storm的整合</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">实现要把storm的jar依赖包写在pom.xml文件中（上面的pom.xml文件中已经把所有的依赖包写在里面了）</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(1)建立<span style="background-color:inherit;line-height:1.5;">Logprocess类</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">package com.east.testlfks;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">import java.util.UUID;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">import backtype.storm.Config;</div>
<div style="background-color:inherit;">import backtype.storm.LocalCluster;</div>
<div style="background-color:inherit;">import backtype.storm.StormSubmitter;</div>
<div style="background-color:inherit;">import backtype.storm.generated.AlreadyAliveException;</div>
<div style="background-color:inherit;">import backtype.storm.generated.InvalidTopologyException;</div>
<div style="background-color:inherit;">import backtype.storm.topology.TopologyBuilder;</div>
<div style="background-color:inherit;">import storm.kafka.BrokerHosts;</div>
<div style="background-color:inherit;">import storm.kafka.KafkaSpout;</div>
<div style="background-color:inherit;">import storm.kafka.SpoutConfig;</div>
<div style="background-color:inherit;">import storm.kafka.ZkHosts;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">public class Logprocess {</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">    public static void main(String[] args) throws AlreadyAliveException, InvalidTopologyException {</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">        TopologyBuilder topologyBuilder = new TopologyBuilder();</div>
<div style="background-color:inherit;">        // 表示kafka使用的zookeeper的地址</div>
<div style="background-color:inherit;">        BrokerHosts hosts = new ZkHosts("hadoop0:2181,hadoop1:2181,hadoop2:2181");</div>
<div style="background-color:inherit;">        // 表示的是kafak中存储数据的主题名称</div>
<div style="background-color:inherit;">        String topic = "mytest";</div>
<div style="background-color:inherit;">        // 指定zookeeper中的一个根目录，里面存储kafkaspout读取数据的位置等信息</div>
<div style="background-color:inherit;">        String zkRoot = "/kafkaspout";</div>
<div style="background-color:inherit;">        String id = UUID.randomUUID().toString();</div>
<div style="background-color:inherit;">        SpoutConfig spoutConf = new SpoutConfig(hosts, topic, zkRoot, id);</div>
<div style="background-color:inherit;">        String SPOUT_ID = KafkaSpout.class.getSimpleName();</div>
<div style="background-color:inherit;">        String BOLT_ID = LogFilterBolt.class.getSimpleName();</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">        topologyBuilder.setSpout(SPOUT_ID, new KafkaSpout(spoutConf));</div>
<div style="background-color:inherit;">        topologyBuilder.setBolt(BOLT_ID, new LogFilterBolt()).shuffleGrouping(SPOUT_ID);</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">        if (args != null &amp;&amp; args.length &gt; 0) {</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">            StormSubmitter.submitTopology(Logprocess.class.getSimpleName(), new Config(),</div>
<div style="background-color:inherit;">                    topologyBuilder.createTopology());</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">        } else {</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">            LocalCluster localCluster = new LocalCluster();</div>
<div style="background-color:inherit;">            localCluster.submitTopology(Logprocess.class.getSimpleName(), new Config(),</div>
<div style="background-color:inherit;">                    topologyBuilder.createTopology());</div>
<div style="background-color:inherit;">        }</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">    }</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">}</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
</td>
</tr></tbody></table></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">(2)建立<span style="background-color:inherit;line-height:1.5;">LogFilterBolt类</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">package com.east.testlfks;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">import java.util.Map;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">import backtype.storm.task.OutputCollector;</div>
<div style="background-color:inherit;">import backtype.storm.task.TopologyContext;</div>
<div style="background-color:inherit;">import backtype.storm.topology.OutputFieldsDeclarer;</div>
<div style="background-color:inherit;">import backtype.storm.topology.base.BaseRichBolt;</div>
<div style="background-color:inherit;">import backtype.storm.tuple.Tuple;</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">public class LogFilterBolt extends BaseRichBolt {</div>
<div style="background-color:inherit;">    private OutputCollector collector;</div>
<div style="background-color:inherit;">    public void prepare(Map stormConf, TopologyContext context,</div>
<div style="background-color:inherit;">            OutputCollector collector) {</div>
<div style="background-color:inherit;">        this.collector = collector;</div>
<div style="background-color:inherit;">    }</div>
<div style="background-color:inherit;">    public void execute(Tuple input) {</div>
<div style="background-color:inherit;">        try{</div>
<div style="background-color:inherit;">            byte[] binaryByField = input.getBinaryByField("bytes");</div>
<div style="background-color:inherit;">            String value = new String(binaryByField);</div>
<div style="background-color:inherit;">            System.out.println(value);</div>
<div style="background-color:inherit;">            this.collector.ack(input);</div>
<div style="background-color:inherit;">        }catch(Exception e){</div>
<div style="background-color:inherit;">            this.collector.fail(input);</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">        }</div>
<div style="background-color:inherit;">    }</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">    public void declareOutputFields(OutputFieldsDeclarer declarer) {</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">    }</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">}</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
</td>
</tr></tbody></table></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">先运行日志生成<span style="background-color:inherit;line-height:1.5;">TestLogsFlume类，然后再运行</span><span style="background-color:inherit;line-height:1.5;">Logprocess类，然后在控制台台上能看到日志消息输出</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<table border="1" cellpadding="2" cellspacing="0" style="border-collapse:collapse;background-color:inherit;"><tbody style="background-color:inherit;"><tr style="background-color:inherit;"><td valign="top" style="border:1px solid rgb(153,153,153);min-height:25px;min-width:25px;background-color:inherit;width:579px;">
<div style="min-width:2px;background-color:inherit;">
<div style="background-color:inherit;">Task [1/1] Refreshing partition manager connections</div>
<div style="background-color:inherit;">Read partition info from zookeeper: GlobalPartitionInformation{partitionMap={0=hadoop1:9092, 1=hadoop2:9092, 2=hadoop0:9092, 3=hadoop1:9092, 4=hadoop2:9092, 5=hadoop0:9092}}</div>
<div style="background-color:inherit;">Task [1/1] assigned [Partition{host=hadoop1:9092, partition=0}, Partition{host=hadoop2:9092, partition=1}, Partition{host=hadoop0:9092, partition=2}, Partition{host=hadoop1:9092, partition=3}, Partition{host=hadoop2:9092, partition=4}, Partition{host=hadoop0:9092, partition=5}]</div>
<div style="background-color:inherit;">Task [1/1] Deleted partition managers: []</div>
<div style="background-color:inherit;">Task [1/1] New partition managers: []</div>
<div style="background-color:inherit;">Task [1/1] Finished refreshing</div>
<div style="background-color:inherit;">my name is liu xiang ke</div>
<div style="background-color:inherit;">my work is it  i love my work.</div>
<div style="background-color:inherit;">my name is liu xiang ke</div>
<div style="background-color:inherit;">my work is it  i love my work.</div>
<div style="background-color:inherit;">my name is liu xiang ke</div>
<div style="background-color:inherit;">my work is it  i love my work.</div>
<div style="background-color:inherit;">my name is liu xiang ke</div>
<div style="background-color:inherit;">my work is it  i love my work.</div>
<div style="background-color:inherit;">my name is liu xiang ke</div>
<div style="background-color:inherit;">my work is it  i love my work.</div>
<div style="background-color:inherit;">my name is liu xiang ke</div>
<div style="background-color:inherit;">my work is it  i love my work.</div>
</div>
</td>
</tr></tbody></table></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">最后log4j+flume+kafka+storm整合成功。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">参考链接：<a href="http://blog.csdn.net/jianghuxiaojin/article/details/51725347" rel="nofollow" style="color:rgb(102,102,102);text-decoration:none;font-family:'Microsoft YaHei';font-size:20px;">http://blog.csdn.net/jianghuxiaojin/article/details/51725347</a></div>
            </div>
                </div>
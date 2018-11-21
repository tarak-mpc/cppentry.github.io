---
layout:     post
title:      使用flume搜集服务器log到hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>将多个logs服务器上的log搜集到hdfs服务器上，多个logs服务上的flume-sink和hdfs服务器上的flume-source都是avro类型，hdfs服务器上的flume-sink是hdfs类型</p>
<p><br></p>
<p>Flume部署文档</p>
<p>系统要求：</p>
<p>Java 运行时环境 <br></p>
<p> </p>
<p>部署方式：</p>
<p>在logs和hdfs服务器上下载并解压flume包</p>
<p> </p>
<p>下载flume包并解压：</p>
<p><a href="http://mirror.bit.edu.cn/apache/flume/1.4.0/apache-flume-1.4.0-bin.tar.gz" rel="nofollow">http://mirror.bit.edu.cn/apache/flume/1.4.0/apache-flume-1.4.0-bin.tar.gz</a></p>
<p> </p>
<h2><a name="t0"></a>logs服务器flume配置</h2>
<p>进入解压后的flume目录，修改配置文件：</p>
<p>1.      Cp  conf/flume-env.sh.template conf/flume-env.sh</p>
<p>在conf/flume-env.sh中添加</p>
<p><strong><em>JAVA_HOME=” JAVA HOME DIR”</em></strong></p>
<p> </p>
<p>2.      在conf目录下创建flume.conf配置文件,添加以下内容</p>
<p>修改agent.sources.loggerSource.command 值</p>
<p> </p>
<p># Licensed to the Apache SoftwareFoundation (ASF) under one</p>
<p># or more contributor licenseagreements.  See the NOTICE file</p>
<p># distributed with this work for additionalinformation</p>
<p># regarding copyright ownership.  The ASF licenses this file</p>
<p># to you under the Apache License, Version2.0 (the</p>
<p># "License"); you may not usethis file except in compliance</p>
<p># with the License.  You may obtain a copy of the License at</p>
<p>#</p>
<p># http://www.apache.org/licenses/LICENSE-2.0</p>
<p>#</p>
<p># Unless required by applicable law oragreed to in writing,</p>
<p># software distributed under the License isdistributed on an</p>
<p># "AS IS" BASIS, WITHOUTWARRANTIES OR CONDITIONS OF ANY</p>
<p># KIND, either express or implied.  See the License for the</p>
<p># specific language governing permissionsand limitations</p>
<p># under the License.</p>
<p> </p>
<p> </p>
<p># The configuration file needs to definethe sources, </p>
<p># the channels and the sinks.</p>
<p># Sources, channels and sinks are definedper agent, </p>
<p># in this case called 'agent'</p>
<p> </p>
<p>agenta.sources = loggerSource</p>
<p>agenta.channels = memoryChannel</p>
<p>agenta.sinks = loggerSink</p>
<p> </p>
<p># For each one of the sources, the type isdefined</p>
<p>agenta.sources.loggerSource.type = exec</p>
<p><strong><em>agenta.sources.loggerSource.command = tail -F &lt;logpath&gt;</em></strong></p>
<p> </p>
<p># The channel can be defined as follows.</p>
<p>agenta.sources.loggerSource.channels =memoryChannel</p>
<p> </p>
<p># Each sink's type must be defined</p>
<p>agenta.sinks.loggerSink.type = avro</p>
<p><strong><em>agenta.sinks.loggerSink.hostname = &lt;hdfs serverip&gt;</em></strong></p>
<p>agenta.sinks.loggerSink.port = 4141</p>
<p> </p>
<p>#Specify the channel the sink should use</p>
<p>agenta.sinks.loggerSink.channel =memoryChannel</p>
<p> </p>
<p># Each channel's type is defined.</p>
<p>agenta.channels.memoryChannel.type = memory</p>
<p> </p>
<p># Other config values specific to each typeof channel(sink or source)</p>
<p># can be defined as well</p>
<p># In this case, it specifies the capacityof the memory channel</p>
<p>agenta.channels.memoryChannel.capacity =1000</p>
<p> </p>
<p>启动命令：</p>
<p>./bin/flume-ng agent --conf conf/  --conf-file conf/flume.conf --name agent</p>
<p> </p>
<p> </p>
<h2><a name="t1"></a>Hdfs 服务器flume配置</h2>
<p>进入解压后的flume目录，修改配置文件：</p>
<p>1.      Cpconf/flume-env.sh.template conf/flume-env.sh</p>
<p>在conf/flume-env.sh中添加</p>
<p><strong><em>JAVA_HOME=” JAVA HOME DIR”</em></strong></p>
<p><strong><em>HADOOP_HOME= “HADOOP HOME”</em></strong></p>
<p> </p>
<p>2.      在conf目录下创建flume.conf配置文件,添加以下内容</p>
<p> </p>
<p> </p>
<p># Licensed to the Apache SoftwareFoundation (ASF) under one</p>
<p># or more contributor license agreements.  See the NOTICE file</p>
<p># distributed with this work for additionalinformation</p>
<p># regarding copyright ownership.  The ASF licenses this file</p>
<p># to you under the Apache License, Version2.0 (the</p>
<p># "License"); you may not usethis file except in compliance</p>
<p># with the License.  You may obtain a copy of the License at</p>
<p>#</p>
<p># http://www.apache.org/licenses/LICENSE-2.0</p>
<p>#</p>
<p># Unless required by applicable law oragreed to in writing,</p>
<p># software distributed under the License isdistributed on an</p>
<p># "AS IS" BASIS, WITHOUTWARRANTIES OR CONDITIONS OF ANY</p>
<p># KIND, either express or implied.  See the License for the</p>
<p># specific language governing permissionsand limitations</p>
<p># under the License.</p>
<p> </p>
<p> </p>
<p># The configuration file needs to definethe sources, </p>
<p># the channels and the sinks.</p>
<p># Sources, channels and sinks are definedper agent, </p>
<p># in this case called 'agent'</p>
<p> </p>
<p>agent.sources = loggerSource</p>
<p>agent.channels = memoryChannel</p>
<p>agent.sinks = loggerSink</p>
<p> </p>
<p># For each one of the sources, the type isdefined</p>
<p>agent.sources.loggerSource.type = avro</p>
<p>agent.sources.loggerSource.bind = 0.0.0.0</p>
<p>agent.sources.loggerSource.port = 4141</p>
<p> </p>
<p># The channel can be defined as follows.</p>
<p>agent.sources.loggerSource.channels =memoryChannel</p>
<p> </p>
<p># Each sink's type must be defined</p>
<p>agent.sinks.loggerSink.type = hdfs</p>
<p><strong><em>agent.sinks.loggerSink.hdfs.path = &lt;hdfs sink path&gt;</em></strong></p>
<p>agent.sinks.loggerSink.hdfs.filePrefix =csplog-</p>
<p>agent.sinks.loggerSink.hdfs.rollInterval=86400</p>
<p>agent.sinks.loggerSink.hdfs.rollSize = 0</p>
<p>agent.sinks.loggerSink.hdfs.rollCount = 0</p>
<p>agent.sinks.loggerSink.hdfs.fileType =DataStream</p>
<p> </p>
<p>#Specify the channel the sink should use</p>
<p>agent.sinks.loggerSink.channel =memoryChannel</p>
<p> </p>
<p># Each channel's type is defined.</p>
<p>agent.channels.memoryChannel.type = memory</p>
<p> </p>
<p># Other config values specific to each typeof channel(sink or source)</p>
<p># can be defined as well</p>
<p># In this case, it specifies the capacityof the memory channel</p>
<p>agent.channels.memoryChannel.capacity =1000</p>
<p> </p>
<p> </p>
<p>启动命令：</p>
<p>bin/flume-ng agent --conf conf/ --conf-fileflume.conf --name agent</p>
            </div>
                </div>
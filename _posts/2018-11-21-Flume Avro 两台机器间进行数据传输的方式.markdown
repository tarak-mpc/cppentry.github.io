---
layout:     post
title:      Flume Avro 两台机器间进行数据传输的方式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>flume 通过avro方式在两台机器之间进行数据传输</p>
<p><br></p>
<p>比如要在192.168.17.18 上把数据传到 192.168.17.17</p>
<p>1 首先要再两台机器上都部署 flume</p>
<p>2 在 17 flume下这样配置 avro-flume.conf</p>
<p></p><pre><code class="language-python">agent3.channels = ch1
agent3.sources = avro-source1
agent3.sinks = log-sink1

agent3.sources.avro-source1.channels = ch1
agent3.sources.avro-source1.type = avro
agent3.sources.avro-source1.bind = 0.0.0.0
agent3.sources.avro-source1.port = 12343

agent3.channels.ch1.type = memory

agent3.sinks.log-sink1.channel = ch1
agent3.sinks.log-sink1.type = logger</code></pre>
<p>3 在17上运行</p>
<pre><code class="language-python">bin/flume-ng agent --conf ./conf/ -f conf/avro-flume.conf -Dflume.root.logger=DEBUG,console -n agent3</code></pre>
<p><br></p>
<p>在18上运行</p>
<pre><code class="language-python">bin/flume-ng avro-client -c ./conf -H 192.168.17.17 -p 12343 -F logs/test_data.log</code></pre>
<p><br></p>
<p>这样会把 18 上的logs/test_data.log 的数据直接在 17上打印出来</p>
<p><br></p>
<p>注： 18上不用配置任何conf文件，只要在 conf目录下留有 flume-env.sh 和 log4j.properties 就行了，18上用的只是flume的avro-client客户端，也就是相当于模拟了一个avro客户端，把18上的数据发到17上</p>
<p><br></p>
<p><br></p>
<div>﻿﻿</div>
            </div>
                </div>
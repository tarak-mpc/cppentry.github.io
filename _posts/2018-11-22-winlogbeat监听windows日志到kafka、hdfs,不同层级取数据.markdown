---
layout:     post
title:      winlogbeat监听windows日志到kafka、hdfs,不同层级取数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u011192458/article/details/74930960				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="font-size:24px;">只截取关键部分:</span></strong></p>
<p><span><strong><span style="font-size:24px;"># https://go.es.io/WinlogbeatConfig<br>
winlogbeat.event_logs:<br>
  - name: Application<br>
    ignore_older: 72h<br>
  - name: Security<br>
  - name: System</span></strong></span></p>
<p><strong><span style="font-size:24px;"><br></span></strong></p>
<p><strong><span style="font-size:24px;">接入kafka</span></strong></p>
<p><strong><span style="font-size:24px;">#--------------------------kafka-----------------------------------<br>
output.kafka:<br>
  # initial brokers for reading cluster metadata<br>
  hosts: ["192.1.1.216:9092"]<br>
  topic: 'chao-beat0710'<br></span></strong></p>
<p><strong><span style="font-size:24px;"><br></span></strong></p>
<p><strong><span style="font-size:24px;">kafka查询数据命令</span></strong></p>
<p><strong><span style="font-size:24px;">bin/kafka-console-consumer.sh --zookeeper 192.1.1.216:2181 --topic chao-beat0710 --from-beginning</span></strong><br></p>
<p><strong><span style="font-size:24px;"><br></span></strong></p>
<p><span style="font-size:24px;"><strong>接入到hdfs</strong></span></p>
<p><span style="font-size:24px;"><strong><br>
input{<br>
        kafka{<br>
                zk_connect=&gt;"192.1.1.216:2181"<br>
                topic_id=&gt;"chao-beat0710"<br>
        }<br>
}<br>
filter{<br><br><br>
        mutate{</strong></span></p>
<p><span style="font-size:24px;"><strong><span></span>#替换windows中获取message数据中的\n以及\t为空<br>
                gsub =&gt; ["message","\n",""]<br>
                gsub =&gt; ["message","\t",""]<br>
                #获取key值的测试,可以通过map的值,比如..beat是map,则beat下面的字段是key，可以通过beat[name]去到字段值<br>
                update =&gt; {"message" =&gt; "%{activity_id}--%{beat}--%{beat[hostname]}"}<br>
        }<br>
}<br>
output{<br>
        webhdfs{<br>
                host =&gt; "192.1.1.151"<br>
                port =&gt; 50070<br>
                path =&gt; "/chao/hdfs/test/kafka3/data.txt"<br>
                user =&gt; "lee"<br>
        }<br>
        stdout{<br>
                codec =&gt; rubydebug<br>
        }<br>
}<br></strong></span></p>
<p><span style="font-size:24px;"><strong><br></strong></span></p>
<p><span style="font-size:24px;"><strong>数据格式例子</strong></span></p>
<p><span style="font-size:24px;"><strong><span><img src="https://img-blog.csdn.net/20170710165625737?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTE5MjQ1OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></strong></span></p>
<p><span style="font-size:24px;"><strong><span>取出</span>数据在hdfs展示</strong></span></p>
<p><span style="font-size:24px;"><strong><img src="https://img-blog.csdn.net/20170710165802071?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTE5MjQ1OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></strong></span></p>
<p><span style="font-size:24px;"><strong>附带winlogbeat接入es，以及logstash方式</strong></span></p>
<p><span style="font-size:24px;"><strong>#-------------------------- Elasticsearch output ------------------------------<br>
#output.elasticsearch:<br>
  # Array of hosts to connect to.<br>
  #hosts: ["localhost:9200"]<br><br><br>
  # Optional protocol and basic auth credentials.<br>
  #protocol: "https"<br>
  #username: "elastic"<br>
  #password: "changeme"<br><br><br>
#----------------------------- Logstash output --------------------------------<br>
#output.logstash:<br>
  # The Logstash hosts<br>
  #hosts: ["localhost:5044"]<br></strong></span></p>
<p><span style="font-size:24px;"><strong><br></strong></span></p>
<p><span style="font-size:24px;"><strong><br></strong></span></p>
<p><strong><span style="font-size:24px;">最后附带官方文档API(</span></strong></p>
<ul style="list-style:none;color:rgb(68,68,68);font-family:'微软雅黑';font-size:14px;line-height:25.2px;"><li style="list-style:disc inside;">packetbeat(用于监控网络流量)、</li><li style="list-style:disc inside;">filebeat(用于监听日志数据，可以替代logstash-input-file)、</li><li style="list-style:disc inside;">topbeat(用于搜集进程的信息、负载、内存、磁盘等数据)、</li><li style="list-style:disc inside;">winlogbeat(用于搜集windows事件日志)</li></ul><span style="font-size:24px;">)</span>
<p></p>
<p><span style="font-size:24px;"><strong>https://www.elastic.co/guide/en/beats/winlogbeat/current/winlogbeat-configuration-details.html<br></strong></span></p>
            </div>
                </div>
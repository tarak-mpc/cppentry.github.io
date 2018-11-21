---
layout:     post
title:      python向influxdb写入数据
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>python版本：3.6 <br>
influxdb版本：1.5.2 <br>
kafka版本：2.11-1.1.0</p>

<p>1、基本环境 <br>
    使用python消费kafka中的数据，然后插入influxdb</p>

<pre><code>python安装 requests模块
python安装 kafka模块
kafka服务器：192.168.1.100:9092
influxdb服务器：192.168.1.100：8086
</code></pre>

<p>2、关键代码</p>

<pre><code># coding: utf-8
from kafka import KafkaConsumer
import requests     

topic = 'host-resource-detector'
broker_list = ['192.168.1.100:9092'] ##kafka ip，也可以是集群
kafka = KafkaConsumer(topic, bootstrap_servers=broker_list, group_id='host-monitor', enable_auto_commit=True, auto_offset_reset='latest')

##带验证的写法，u 表示用户名，p 表示密码
influxdb_url = "http://10.25.172.88:8086/write?db=hostdb&amp;u=monitor;&amp;p=tiger1234"

for msg in kafka:
    record = eval(str(msg.value,encoding = "utf-8"))
    ##从kafka消费数据并组装成influxdb sql语句
    influxdb_data = "hostdb," + "hostname=" + record['hostname'] + " cpu_used=" + str(round(100-float(record['cpu_idle']),2)) + ",memory_used=" + str(record['memory_used']) + ",num_process=" + str(record['num_process']) + " " + str(record['timestamp']) + "000000000"
    ##通过http api插入数据到influxdb
    response = requests.post(influxdb_url, data=influxdb_data)
    ##获取请求返回信息，特别是报错信息
    print(response.content) 
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
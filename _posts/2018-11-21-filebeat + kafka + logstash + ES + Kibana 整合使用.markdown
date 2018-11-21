---
layout:     post
title:      filebeat + kafka + logstash + ES + Kibana 整合使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3><a id="filebeat__kafka__logstash__ES__Kibana__0"></a>filebeat + kafka + logstash + ES + Kibana 整合使用</h3>
<h4><a id="_2"></a>环境准备</h4>
<ul>
<li>JDK 1.8</li>
<li>filebeat 6.4.1</li>
<li>kafka 0.10.2.0</li>
<li>logstash 6.4.1</li>
<li>elasticsearch 6.4.1</li>
<li>Kibana 6.4.1</li>
</ul>
<h4><a id="kafka_12"></a>kafka</h4>
<ul>
<li>创建topic</li>
</ul>
<pre class=" language-sh"><code class="prism  language-sh">kafka-topics --zookeeper 192.168.23.121,192.168.23.122,192.168.23.123 --create --partitions 3 --replication-factor 3 --topic nginx-data001
</code></pre>
<h4><a id="filebeat_20"></a>filebeat</h4>
<ul>
<li>修改配置文件</li>
</ul>
<pre class=" language-sh"><code class="prism  language-sh">cd /etc/filebeat/
vi filebeat.yml
</code></pre>
<ul>
<li>filebeat.yml 添加如下配置，输入为Nginx日志目录，输出为kafka</li>
</ul>
<pre><code>filebeat.inputs:

# Each - is an input. Most options can be set at the input level, so
# you can use different inputs for various configurations.
# Below are the input specific configurations.

- type: log

  # Change to true to enable this input configuration.
  enabled: true

  # Paths that should be crawled and fetched. Glob based paths.
  paths:
   - /data01/datadir_test/nginx_logs/dataLKOne/access.log
output.kafka:
   enable: true
   hosts: ["dsgcd4121:9092","dsgcd4122:9092","dsgcd4123:9092"]
   topic: 'nginx-data001'
   version: '0.10.2.0'
</code></pre>
<ul>
<li>启动 filebeat</li>
</ul>
<pre><code>service filebeat start
</code></pre>
<h4><a id="logstash_58"></a>logstash</h4>
<ul>
<li>自定义 logstash patterns</li>
</ul>
<pre class=" language-sh"><code class="prism  language-sh">mkdir -p /usr/local/logstash/patterns
vi /usr/local/logstash/patterns/nginx
</code></pre>
<ul>
<li>nginx文件内容如下：</li>
</ul>
<pre><code>QS1 (.*?)
NGINXACCESS %{IPORHOST:clientip} - %{USERNAME:remote_user} \[%{HTTPDATE:timestamp}\] \"(?:%{WORD:http_verb} %{NOTSPACE:http_request}(?: HTTP/%{NUMBER:http_version})?|%{DATA:raw_http_request})\" (%{IPORHOST:http_host}.%{WORD:http_port}) %{NUMBER:response_status} %{NUMBER:response_length} (?:%{NUMBER:bytes_read}|-) %{QS1:referrer} %{QS1:agent}  %{NUMBER:request_time:float} %{NUMBER:upstream_response_time:float}
</code></pre>
<ul>
<li>创建配置文件</li>
</ul>
<pre class=" language-sh"><code class="prism  language-sh">cd /etc/logstash/conf.d/
vi nginx_datalkone.conf
</code></pre>
<ul>
<li>nginx_datalkone.conf 内容如下：</li>
</ul>
<pre class=" language-conf"><code class="prism  language-conf">input {
  kafka {
        enable_auto_commit =&gt; true
        auto_commit_interval_ms =&gt; "1000"
        codec =&gt; "json"
        bootstrap_servers =&gt; "192.168.23.121:9092,192.168.23.122:9092,192.168.23.123:9092"
        topics =&gt; ["nginx-data001"]
  }
}
filter {
     grok {
        patterns_dir =&gt; "/usr/local/logstash/patterns"
        match =&gt; { "message" =&gt; "%{NGINXACCESS}" }
        remove_field =&gt; ["message"]
     }
     urldecode {
          all_fields =&gt; true
     }
     geoip {
        source =&gt; "clientip"
     }
}
output {
    elasticsearch {
        hosts =&gt; ["localhost:9200"]
        index =&gt; "nginx-data-%{+YYYY.MM.dd}"
    }
}
</code></pre>
<ul>
<li>启动logstash<code>initctl start logstash</code></li>
</ul>
<h4><a id="ElasticSearch_113"></a>ElasticSearch</h4>
<ul>
<li>通过<code>curl 'localhost:9200/_cat/indices?v'</code>可以查看是否写入了数据</li>
</ul>
<pre><code>176&gt;# curl 'localhost:9200/_cat/indices?v'
health status index                 uuid                   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   .kibana               iLXOPq3wTc2PECVeXtXzOw   1   1          2            0      7.7kb          7.7kb
yellow open   nginx-data-2018.09.28 ORbbgojRTEGJVSl_jN2MXg   5   1          0            0       401b           401b
</code></pre>
<h4><a id="kibanaindex_125"></a>在kibana中配置index</h4>
<p>····</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Spark学习笔记（18）—— Elasticsearch 安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/u012292754/article/details/83387167				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="1__0"></a>1 解压</h1>
<pre><code>[hadoop@node1 ~]$ ll
total 42708
drwxrwxr-x. 9 hadoop hadoop      180 Oct 24 21:33 apps
-rw-r--r--. 1 hadoop hadoop 27540442 Jun 10  2017 elasticsearch-2.3.1.tar.gz
drwxrwxr-x. 4 hadoop hadoop       28 Sep 14 19:02 hbase
drwxrwxr-x. 4 hadoop hadoop       32 Sep 14 14:44 hdfsdir
-rw-r--r--. 1 hadoop hadoop 16162559 Jun 24 12:55 kafka_2.10-0.8.2.1.tgz
drwxrwxrwx. 3 hadoop hadoop       18 Oct 24 21:45 kafkaData
-rw-rw-r--. 1 hadoop hadoop        0 Oct 24 21:58 log-cleaner.log
drwxrwxr-x. 5 hadoop hadoop      133 Oct 23 14:40 metastore_db
drwxrwxr-x. 3 hadoop hadoop       63 Oct 24 21:21 zookeeperData
-rw-rw-r--. 1 hadoop hadoop    26108 Oct 25 17:54 zookeeper.out
[hadoop@node1 ~]$ tar -zxvf elasticsearch-2.3.1.tar.gz -C /home/hadoop/apps/

</code></pre>
<h1><a id="2__elasticsearchyml_18"></a>2 修改配置文件 <code>elasticsearch.yml</code></h1>
<p><code>/home/hadoop/apps/elasticsearch-2.3.1/config</code></p>
<pre><code>#集群名称，通过组播的方式通信，通过名称判断属于哪个集群
cluster.name: TzbBigData
#节点名称，要唯一
node.name: node-1
#数据存放位置
path.data: /home/hadoop/elasticsearchData/data
#日志存放位置
path.logs: /home/hadoop/elasticsearchData/logs
#es绑定的ip地址
network.host: 192.168.30.131
#初始化时可进行选举的节点
discovery.zen.ping.unicast.hosts: ["node1", "node2","node3"]


</code></pre>
<h1><a id="3__37"></a>3 拷贝到其他节点</h1>
<pre><code>scp -r /home/hadoop/apps/elasticsearch-2.3.1/ node2:/home/hadoop/apps
scp -r /home/hadoop/apps/elasticsearch-2.3.1/ node3:/home/hadoop/apps

</code></pre>
<p>修改node2,node3 的配置文件的 node.name和network.host</p>
<h1><a id="4__45"></a>4 启动</h1>
<p>es 启动时要求非 root 用户<br>
三个节点同时操作</p>
<pre><code>/home/hadoop/apps/elasticsearch-2.3.1/bin/elasticsearch -d
</code></pre>
<p>浏览器访问 <code>http://node1:9200/</code></p>
<p><img src="https://img-blog.csdnimg.cn/20181025222625177.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h1><a id="5__55"></a>5 测试</h1>
<pre><code>RESTful接口URL的格式：
http://localhost:9200/&lt;index&gt;/&lt;type&gt;/[&lt;id&gt;]
其中index、type是必须提供的。
id是可选的，不提供es会自动生成。
index、type将信息进行分层，利于管理。
index可以理解为数据库；type理解为数据表；id相当于数据库表中记录的主键，是唯一的。
</code></pre>
<p>在命令行用 curl ,添加数据</p>
<pre><code>curl -XPUT 'http://node1:9200/store/books/1' -d '{
  "title": "Elasticsearch: The Definitive Guide",
  "name" : {
    "first" : "Zachary",
    "last" : "Tong"
  },
  "publish_date":"2018-10-25",
  "price":"49.99"
}'
</code></pre>
<h2><a id="51__78"></a>5.1 获取数据</h2>
<p>浏览器访问 <code>http://node1:9200/store/books/1</code><br>
<img src="https://img-blog.csdnimg.cn/20181025224102203.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<pre><code>curl -XGET 'http://node1:9200/store/books/1'
</code></pre>
<p><img src="https://img-blog.csdnimg.cn/201810252301031.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h1><a id="6_es__87"></a>6 es 离线安装插件</h1>
<pre><code>[hadoop@node1 ~]$ ll
total 908
drwxrwxr-x. 10 hadoop hadoop    207 Oct 25 21:49 apps
drwxrwxr-x.  4 hadoop hadoop     30 Oct 25 21:59 elasticsearchData
-rw-r--r--.  1 hadoop hadoop 899399 Jun 10  2017 elasticsearch-head-master.zip
drwxrwxr-x.  4 hadoop hadoop     28 Sep 14 19:02 hbase
drwxrwxr-x.  4 hadoop hadoop     32 Sep 14 14:44 hdfsdir
drwxrwxrwx.  3 hadoop hadoop     18 Oct 24 21:45 kafkaData
-rw-rw-r--.  1 hadoop hadoop      0 Oct 24 21:58 log-cleaner.log
drwxrwxr-x.  5 hadoop hadoop    133 Oct 23 14:40 metastore_db
drwxrwxr-x.  3 hadoop hadoop     63 Oct 24 21:21 zookeeperData
-rw-rw-r--.  1 hadoop hadoop  26108 Oct 25 17:54 zookeeper.out

</code></pre>
<pre><code>[hadoop@node1 ~]$ cd /home/hadoop/apps/elasticsearch-2.3.1/bin
[hadoop@node1 bin]$ ./plugin install file:///home/hadoop/elasticsearch-head-master.zip 
-&gt; Installing from file:/home/hadoop/elasticsearch-head-master.zip...
Trying file:/home/hadoop/elasticsearch-head-master.zip ...
Downloading .........DONE
Verifying file:/home/hadoop/elasticsearch-head-master.zip checksums if available ...
NOTE: Unable to verify checksum for downloaded plugin (unable to find .sha1 or .md5 file to verify)
Installed head into /home/hadoop/apps/elasticsearch-2.3.1/plugins/head
[hadoop@node1 bin]$ 

</code></pre>
<p>浏览器访问 <code>http://node1:9200/_plugin/head/</code><br>
<img src="https://img-blog.csdnimg.cn/2018102522501778.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTIyOTI3NTQ=,size_27,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
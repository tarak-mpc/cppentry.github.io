---
layout:     post
title:      Kafka Manager - 一个管理 Apache Kafka 的工具
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>Kafka Manager - 一个管理 Apache Kafka 的工具</h1>
<div class="article_meta">
<div><span class="timestamp">时间 2015-02-05 08:23:49<span> </span></span><span class="from"><em class="icon-globe"></em><span> </span><a class="cut cut28 from" href="http://www.tuicool.com/sites/6z2yQr" rel="nofollow" style="display:inline;color:rgb(51,51,51);overflow:hidden;text-decoration:none;">佚名</a></span></div>
<div class="source"><em style="font-style:normal;">原文</em> <span> </span><a class="cut cut70" href="http://www.tuicool.com/articles/goto?id=Nbye2i2" rel="nofollow" style="display:inline-block;color:rgb(51,51,51);overflow:hidden;text-decoration:none;">https://github.com/yahoo/kafka-manager</a></div>
<div><span>主题</span><span> </span><a href="http://www.tuicool.com/topics/11090075" rel="nofollow" style="color:rgb(51,51,51);text-decoration:none;"><span class="new-label" style="color:#787878;line-height:16px;background-color:rgb(242,242,242);display:inline-block;font-size:.9em;vertical-align:baseline;text-decoration:none;">Kafka</span></a></div>
</div>
<div id="nei" class="article_body article_body_en">
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
A tool for managing<span> </span><a href="http://kafka.apache.org/" rel="nofollow" style="border-bottom:rgb(148,148,148) 1px dashed;font-style:italic;color:rgb(148,148,148);font-weight:bold;text-decoration:none;">Apache Kafka</a><span> </span>.</p>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
It supports the following :</p>
<ul style="line-height:1.7em;list-style-type:none;font-size:16px;"><li style="line-height:1.7em;list-style-type:disc;font-size:16px;">Manage multiple clusters</li><li style="line-height:1.7em;list-style-type:disc;font-size:16px;">Easy inspection of cluster state (topics, brokers, replica distribution, partition distribution)</li><li style="line-height:1.7em;list-style-type:disc;font-size:16px;">Run preferred replica election</li><li style="line-height:1.7em;list-style-type:disc;font-size:16px;">Generate partition assignments (based on current state of cluster)</li><li style="line-height:1.7em;list-style-type:disc;font-size:16px;">Run reassignment of partition (based on generated assignments)</li></ul><p style="line-height:1.7em;text-indent:1em;font-size:16px;">
Cluster Management</p>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
<img src="https://github.com/yahoo/kafka-manager/raw/master/img/cluster.png" alt="" style="border-bottom:0px;text-align:center;border-left:0px;width:550px;display:block;vertical-align:middle;border-top:0px;border-right:0px;"></p>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
Topic View</p>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
<img src="https://github.com/yahoo/kafka-manager/raw/master/img/topic.png" alt="" style="border-bottom:0px;text-align:center;border-left:0px;width:550px;display:block;vertical-align:middle;border-top:0px;border-right:0px;"></p>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
Broker View</p>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
<img src="https://github.com/yahoo/kafka-manager/raw/master/img/broker.png" alt="" style="border-bottom:0px;text-align:center;border-left:0px;width:550px;display:block;vertical-align:middle;border-top:0px;border-right:0px;"></p>
<h2 style="line-height:1.6em;text-indent:1em;font-family:inherit;font-size:18px;font-weight:bold;">
Requirements</h2>
<ol style="line-height:1.7em;font-size:16px;"><li style="line-height:1.7em;font-size:16px;">Kafka 0.8.1.1 or 0.8.2-beta</li><li style="line-height:1.7em;font-size:16px;">sbt 0.13.x</li><li style="line-height:1.7em;font-size:16px;">Java 7+</li></ol><h2 style="line-height:1.6em;text-indent:1em;font-family:inherit;font-size:18px;font-weight:bold;">
Configuration</h2>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
The minimum configuration is the zookeeper hosts which are to be used for kafka manager state. This can be found in the application.conf file in conf directory. The same file will be packaged in the distribution zip file; you may modify settings after unzipping
 the file on the desired server.</p>
<pre class="prettyprint undefined" style="border-bottom:1px solid;border-left:1px solid;line-height:1.5em;background-color:rgb(246,246,246);display:block;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:12px;border-top:1px solid;border-right:1px solid;"><code style="border-bottom:0px;border-left:0px;background-color:rgb(246,246,246);display:block;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:12px;border-top:0px;border-right:0px;">kafka-manager.zkhosts="my.zookeeper.host.com:2181"
</code></pre>
<h2 style="line-height:1.6em;text-indent:1em;font-family:inherit;font-size:18px;font-weight:bold;">
Deployment</h2>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
The command below will create a zip file which can be used to deploy the application.</p>
<pre class="undefined" style="border-bottom:1px solid;border-left:1px solid;line-height:1.5em;background-color:rgb(246,246,246);display:block;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:12px;border-top:1px solid;border-right:1px solid;"><code style="border-bottom:0px;border-left:0px;background-color:rgb(246,246,246);display:block;font-family:Monaco, Menlo, Consolas, 'Courier New', monospace;color:rgb(51,51,51);font-size:12px;border-top:0px;border-right:0px;">sbt clean dist
</code></pre>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
Please refer to play framework documentation on production deployment.</p>
<h2 style="line-height:1.6em;text-indent:1em;font-family:inherit;font-size:18px;font-weight:bold;">
Credits</h2>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
Logo/favicon used is from<span> </span><a href="http://kafka.apache.org/" rel="nofollow" style="border-bottom:rgb(148,148,148) 1px dashed;font-style:italic;color:rgb(148,148,148);font-weight:bold;text-decoration:none;">Apache Kafka</a><span> </span>.</p>
<p style="line-height:1.7em;text-indent:1em;font-size:16px;">
Most of the utils code has been adapted to work with<span> </span><a href="http://curator.apache.org/" rel="nofollow" style="border-bottom:rgb(148,148,148) 1px dashed;font-style:italic;color:rgb(148,148,148);font-weight:bold;text-decoration:none;">Apache
 Curator</a><span> </span>from<span> </span><a href="http://kafka.apache.org/" rel="nofollow" style="border-bottom:rgb(148,148,148) 1px dashed;font-style:italic;color:rgb(148,148,148);font-weight:bold;text-decoration:none;">Apache
 Kafka</a><span> </span>.</p>
</div>
            </div>
                </div>
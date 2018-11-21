---
layout:     post
title:      Apache kafka 中zookeeper 的应用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载务必注明出处，否则追求法律责任					https://blog.csdn.net/yanshu2012/article/details/54614519				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;">正如ZooKeeper用于分布式系统的协调和促进，Kafka使用ZooKeeper也是基于相同的原因。ZooKeeper用于管理、协调Kafka代理。每个Kafka代理都通过ZooKeeper协调其它Kafka代理。当Kafka系统中新增了代理或者某个代理故障失效时，ZooKeeper服务将通知生产者和消费者。生产者和消费者据此开始与其它代理协调工作。Kafka整体系统架构如图所示。</span></p>
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;"><img src="https://img-blog.csdn.net/20170119121726456?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveWFuc2h1MjAxMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;">下面zookeepeer的结构和流程用来协调producer,broker,consumer.</span></span></p>
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;">broker 节点注册目录</span></span></p>
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;"></span></span></p><pre><code class="language-plain">/brokers/ids/[0...N] --&gt; {"jmx_port":...,"timestamp":...,"endpoints":[...],"host":...,"version":...,"port":...} (ephemeral node)</code></pre><br>
broker topic注册目录
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;"></span></span></p><pre><code class="language-plain">/brokers/topics/[topic]/partitions/[0...N]/state --&gt; {"controller_epoch":...,"leader":...,"version":...,"leader_epoch":...,"isr":[...]} (ephemeral node)</code></pre>
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;"><br></span></span></p>
默认配置时consumer group的offset信息存储在zookeeper中，所以zookeeper中有提供给<span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;font-size:14px;">consumer  记录topic partion offset的目录。</span>
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;">consumer id注册目录</span></span></p>
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;"></span></span></p><pre><code class="language-plain">/consumers/[group_id]/ids/[consumer_id] --&gt; {"version":...,"subscription":{...:...},"pattern":...,"timestamp":...} (ephemeral node)</code></pre><br>
consumer offsets目录
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;"></span></span></p><pre><code class="language-plain">/consumers/[group_id]/offsets/[topic]/[partition_id] --&gt; offset_counter_value (persistent node)</code></pre><br>
partition owener 注册目录
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;"></span></span></p><pre><code class="language-plain">/consumers/[group_id]/owners/[topic]/[partition_id] --&gt; consumer_node_id (ephemeral node)</code></pre>
<p>cluster id目录</p>
<p><span style="font-family:Helvetica, 'Open Sans', Arial, 'Hiragino Sans GB', 'Microsoft YaHei', '微软雅黑', STHeiti, 'WenQuanYi Micro Hei', SimSun, sans-serif;"><span style="font-size:14px;"></span></span></p><pre><code class="language-plain">/cluster/id</code></pre>
<p>consumer 注册流程</p>
<p>1. 在相应的consumer组下注册<span style="font-family:Roboto, sans-serif;font-size:14px;">consumer id 。</span></p>
<p><span style="font-family:Roboto, sans-serif;font-size:14px;">2.注册一个watcher关注<span style="font-family:Roboto, sans-serif;font-size:14px;">consumer id 的改变情况。</span></span></p>
<p><span style="font-family:Roboto, sans-serif;font-size:14px;"><span style="font-family:Roboto, sans-serif;font-size:14px;">3.<span style="font-family:Roboto, sans-serif;font-size:14px;">注册一个watcher关注broker </span><span style="font-family:Roboto, sans-serif;font-size:14px;">id
 的改变情况。</span></span></span></p>
<p>4. 如果用户用topic 过滤器创建一个消息流，需要注册一个watcher 关注topic的变化。</p>
<p>5.强制自己在consumer group中做负载均衡。</p>
<p><br></p>
<br><br>            </div>
                </div>
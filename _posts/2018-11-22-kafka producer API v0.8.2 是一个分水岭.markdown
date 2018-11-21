---
layout:     post
title:      kafka producer API v0.8.2 是一个分水岭
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/bigtree_3721/article/details/82622636				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>kafka v0.8.2是一个分水岭，对于kafka v0.8.x以前的kafka producer API，用如下的import头文件</p>

<pre class="has">
<code class="hljs language-java">//for kafka client before v0.8.2
import kafka.javaapi.producer.ProducerData;
import kafka.producer.ProducerConfig;
import kafka.javaapi.producer.Producer;
</code></pre>

<p>kafka producer （v0.8.2 以前）例子代码见：</p>

<p> 【<a href="https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+Producer+Example" rel="nofollow">0.8.0 Producer Example】https://cwiki.apache.org/confluence/display/KAFKA/0.8.0+Producer+Example</a></p>

<p>对于kafka v0.8.x以后的kafka producer API，用如下的import头文件</p>

<pre class="has">
<code class="hljs language-java">import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;</code></pre>

<p>kafka producer 例子代码见</p>

<p>【<a href="https://www.cnblogs.com/xiaojf/p/6602732.html" rel="nofollow" id="cb_post_title_url">kafka 0.8.2 消息生产者 KafkaProducer</a>】 <a href="https://www.cnblogs.com/xiaojf/p/6602732.html" rel="nofollow">https://www.cnblogs.com/xiaojf/p/6602732.html</a></p>            </div>
                </div>
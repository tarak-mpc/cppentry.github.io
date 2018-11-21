---
layout:     post
title:      Vertica的这些事（十七）——Vertica消费kafka答疑
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/WindyQCF/article/details/84312834				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>关于如何消费kafka数据，请参看<a href="https://www.jianshu.com/p/3ee7e4405f2b" rel="nofollow">Vertica的这些事（十四）——Vertica实时消费kafka实现</a><br>
最近有小伙伴提出了消费kafka的一些疑问，答疑如下：</p>
<p>#####1、	Vertica消费kafka机制是什么？</p>
<blockquote>
<p>Vertica通过scheduler来按定义的频度调度执行copy table from kafkasource(…)来从kafka的主题加载数据。<br>
#####2、	Vertica消费kafka的偏移量是怎么维护的？<br>
Vertica的scheduler会把每个主题消费到的偏移量存储到表stream_microbatch_history中（schema由scheduler的参数指定和创建），没加载一批就会scheduler就会给它加上，下一次加载的时候会作为参数传递给kafkasource。<br>
#####3、	Vertica消费kafka是多个节点并发执行？资源怎么控制？<br>
Vertica是多个节点并行读取kafka主题多个patition的消息的。资源池的EXECUTIONPARALLELISM参数会决定节点内解析消息的并行线程数，内存等资源也由资源池控制。<br>
#####4、	Vertica消费kafka应该怎么监控？<br>
MC本身具备kafka消息加载的监控界面，表stream_microbatch_history中有详细的加载信息。<br>
#####5、	出现节点宕机对kafka消息读取有什么影响？<br>
Kafka读过的消息偏移量和业务数据都存放到Vertica中，就算Vertica集群数据不完全恢复，他们也会保证是一致的，恢复后会自动从数据库中记录的偏移量开始继续加载，不会出现数据对视。</p>
</blockquote>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
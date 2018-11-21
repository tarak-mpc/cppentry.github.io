---
layout:     post
title:      HBase HA 集群启动 报错 java.net.UnknownHostException: HadoopCluster
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请附上原出处。					https://blog.csdn.net/Veechange/article/details/52912875				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">       HBase HA集群是基于Hadoop HA集群的，在搭建HBase HA集群之前需要先有1个运行正常的Hadoop HA集群环境。在做完相关HBase配置后，启动HBase时，只有主HBase启动正常，备用HBase及Region全部没有启动，排查发现是启动时报错了：</span></p>
<p><span style="font-size:14px;"></span></p><pre><code class="language-java">java.net.UnknownHostException: HadoopCluster</code></pre>
<p><span style="font-size:14px;">       这是因为，HBase没有识别HadoopCluster这个集群，解决这个问题的方法是把Hadoop的2个配置文件（core-site.xml和hdfs-site.xml），放到HBase的conf目录下，让HBase能找到Hadoop的配置。</span></p>
            </div>
                </div>
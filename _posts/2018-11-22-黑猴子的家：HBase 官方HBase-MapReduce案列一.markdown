---
layout:     post
title:      黑猴子的家：HBase 官方HBase-MapReduce案列一
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_28652401/article/details/83507889				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
    <div class="show-content-free">
            <h4>1、查看HBase的MapReduce任务的执行</h4>
<pre><code>[victor@node1 hbase]$ bin/hbase mapredcp
</code></pre>
<h4>2、执行环境变量的导入</h4>
<pre><code>[victor@node1 ~]$ export HBASE_HOME=/opt/module/hbase-1.3.1
[victor@node1 ~]$ export HADOOP_HOME=/opt/module/hadoop-2.8.2
[victor@node1 ~]$ export HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase mapredcp` 
</code></pre>
<h4>3、统计Student表中有多少行数据</h4>
<pre><code>[victor@node1 hbase-1.3.1]$ /opt/module/hadoop-2.8.2/bin/yarn jar \
lib/hbase-server-1.3.1.jar rowcounter student
</code></pre>

          </div>
              </div>
                </div>
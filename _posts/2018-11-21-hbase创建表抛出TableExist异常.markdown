---
layout:     post
title:      hbase创建表抛出TableExist异常
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xx7330842/article/details/52932698				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hbase异常启动不起来后，从hdfs中删除hbase的文件，重新建表，抛出TableExist异常。</p>
<p>原因是zookeeper上还残留hbase表的信息，需要删除即可。</p>
<p>即通过zkCli.sh进行zookeeper后，delete /hbase/table/its007-meta</p>
            </div>
                </div>
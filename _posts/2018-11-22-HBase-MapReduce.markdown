---
layout:     post
title:      HBase-MapReduce
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liangzelei/article/details/80298514				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>通过HBase的相关JavaAPI，我们可以实现伴随HBase操作的MapReduce过程，比如使用MapReduce将数据从本地文件系统导入到HBase的表中，比如我们从HBase中读取一些原始数据后使用MapReduce做数据分析。</strong></p><p><strong>1) 查看HBase的MapReduce任务的所需的依赖</strong><br></p><p><strong>$ bin/hbase mapredcp<br></strong></p><p><strong><strong>2) 执行环境变量的导入</strong><br></strong></p><p></p><p><strong>$ export HBASE_HOME=/home/admin/modules/hbase-1.3.1</strong></p><p><strong>$ export HADOOP_CLASSPATH=`${HBASE_HOME}/bin/hbase mapredcp`</strong></p><p><strong>（如果失败，请先运行<strong>/bin/hbase mapredcp，再将结果赋值给<strong>HADOOP_CLASSPATH</strong></strong>）</strong></p><p><strong><strong>3) 运行官方的MapReduce任务</strong><br></strong></p><p><strong><strong>$ /home/lzl/modules/hadoop/bin/yarn jar<br></strong>/home/<strong>lzl</strong>/modules/hbase/lib/hbase-server-1.3.1.jarrowcounter tableName（hbase内的表）</strong></p>            </div>
                </div>
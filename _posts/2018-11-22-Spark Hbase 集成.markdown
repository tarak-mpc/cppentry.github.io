---
layout:     post
title:      Spark Hbase 集成
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sunrising_hill/article/details/79108471				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>安装Spark和Hbase，将Hbase的lib目录下的jar包复制到Spark目录的jars目录下：</p>
<p><br></p>
<p>cp /apps/hbase/lib/metrics-core-2.2.0.jar /apps/spark/jars<br>
cp /apps/hbase/lib/hbase*.jar /apps/spark/jars<br></p>
<p><br></p>
<p>撰写代码，从hbase中读取数据，再通过RDD转换成DataFrame，注册成SparkSql的数据表，即可通过SQL查询任意数据。</p>
<p><br></p>
<p>性能估计比直接读取文件快，借助HBase 的scan可以实现只读取特定范围的数据，不必扫描全部文件。</p>
<p><br></p>
            </div>
                </div>
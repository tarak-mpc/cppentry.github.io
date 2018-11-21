---
layout:     post
title:      hive启动报错，找不到spark的jar包们
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主成吉思潇原创文章，未经博主允许不得转载。					https://blog.csdn.net/xx1710/article/details/70908051				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;">类型下面的报错</span></p>
<p><span style="font-size:24px;">cannot access /usr/local/spark/lib/spark-assembly-*.jar: </span></p>
<p><span style="font-size:24px;">No such file or directory</span></p>
<p><span style="font-size:24px;"><br></span></p>
<p><span style="font-size:24px;">编辑你的hive启动脚本</span></p>
<p></p><pre><code class="language-plain"><span style="font-size:24px;">vim /usr/local/hive/bin/hive</span></code></pre><span style="font-size:24px;">更改116行的内容，我的版本是1.2.2的hive</span>
<p></p><pre><code class="language-plain"><span style="font-size:24px;">if [[ -n "$SPARK_HOME" ]]
then
  sparkAssemblyPath=`ls ${SPARK_HOME}/lib/spark-assembly-*.jar`
  CLASSPATH="${CLASSPATH}:${sparkAssemblyPath}"
fi
</span></code></pre><span style="font-size:24px;"><br>
把上述的 /lib/spark什么什么的那个路径改为</span>
<p></p><pre><code class="language-plain"><span style="font-size:24px;">/jars/*.jar</span></code></pre><span style="font-size:24px;"><br>
问题的主要原因是因为spark新版本的jar包文件结构改变</span>
            </div>
                </div>
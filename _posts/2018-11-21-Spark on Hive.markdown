---
layout:     post
title:      Spark on Hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_41455420/article/details/79549450				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark-on-hive">Spark on Hive</h2>

<h3 id="1spark-on-hive介绍">1、spark on hive介绍</h3>

<ul>
<li>（1）就是通过sparksql，加载hive的配置文件，获取到hive的元数据信息</li>
<li>（2）spark sql获取到hive的元数据信息之后就可以拿到hive的所有表的数据</li>
<li>（3）接下来就可以通过spark sql来操作hive表中的数据</li>
</ul>

<h3 id="2spark-on-hive-配置">2、spark on hive 配置</h3>

<ul>
<li>（1）将hive安装目录下conf/hive-site.xml拷贝到spark的conf目录下</li>
<li>（2）同时把hive安装目录下lib目录中的mysql驱动包，拷贝到spark的jars目录</li>
<li>（3）分发所有配置和拷贝mysql驱动到spark的其他节点</li>
<li>（4） 启动spark-sql 就可以操作hive的sql语句了。</li>
<li>注意需要添加配置 <br>
<ul><li>–conf <code>spark.sql.warehouse.dir=hdfs://node1:9000/user/hive/warehouse</code></li></ul></li>
</ul>

<h3 id="3操作实例">3、操作实例</h3>

<ul>
<li><p>执行命令：<code>spark-sql --master spark://node1:7077 --executor-memory 1g --total--executor-cores 2 --conf spark.sql.warehouse.dir=hdfs://node1:9000/user/hive/warehouse</code></p></li>
<li><p>操作： <br>
<code>show databases;</code> </p></li>
</ul>

<p>喜欢就点赞评论+关注吧</p>

<p><img src="https://img-blog.csdn.net/20180314084932329?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfNDE0NTU0MjA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>感谢阅读，希望能帮助到大家，谢谢大家的支持！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
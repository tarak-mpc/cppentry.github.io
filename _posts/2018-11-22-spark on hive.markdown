---
layout:     post
title:      spark on hive
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>spark on hive</p>

<p>1、spark on hive介绍</p>

<ul>
<li>（1）就是通过sparksql，加载hive的配置文件，获取到hive的元数据信息</li>
<li>（2）spark sql获取到hive的元数据信息之后就可以拿到hive的所有表的数据</li>
<li>（3）接下来就可以通过spark sql来操作hive表中的数据</li>
</ul>

<p>2、spark on hive 配置</p>

<ul>
<li>（1）将hive安装目录下conf/hive-site.xml拷贝到spark的conf目录下 <br>
（2）同时把hive安装目录下lib目录中的mysql驱动包，拷贝到spark的jars目录 <br>
  （3）分发所有配置和拷贝mysql驱动到spark的其他节点 <br>
  （4） 启动spark-sql 就可以操作hive的sql语句了。 <br>
  注意需要添加配置 <br>
<ul><li>–conf spark.sql.warehouse.dir=hdfs://bigdata-1:9000/user/hive/warehouse</li></ul></li>
</ul>

<p>3、操作实例</p>

<ul>
<li>执行命令：spark-sql –master spark://bigdata-1bigdata-1:7077 –executor-memory 1g –total–executor-cores 2 –conf spark.sql.warehouse.dir=hdfs://bigdata-1:9000/user/hive/warehouse</li>
<li>操作： <br>
<ul><li>show databases; </li></ul></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
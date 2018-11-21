---
layout:     post
title:      Unable to instantiate org.apache.hadoop.hive.ql.metadata.SessionHiveMetaStoreClient
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_24309787/article/details/83859485				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>hive初始化（mysql为元数据库）完成后，执行SQL语句报错</p>

<p><img alt="" class="has" height="48" src="https://img-blog.csdnimg.cn/20181108125716266.png" width="1200"></p>

<p>经过各种查询资料，找到了一种解决办法，大家可以参考。</p>

<p>首先进入hive/bin目录下</p>

<p>执行命令  <strong> ./hive --service metastore &amp;</strong></p>

<p>如图：</p>

<p><img alt="" class="has" height="440" src="https://img-blog.csdnimg.cn/20181108171302867.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0MzA5Nzg3,size_16,color_FFFFFF,t_70" width="964"></p>

<p>然后执行命令<strong>  ./hive</strong></p>

<p>成功进入hive数据库</p>

<p><img alt="" class="has" height="369" src="https://img-blog.csdnimg.cn/20181108130421508.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzI0MzA5Nzg3,size_16,color_FFFFFF,t_70" width="999"></p>

<p>在这里简单介绍一下hive的metastore</p>

<p>1. 概述<br>
通过hive metastore能访问hive的元数据，hive的元数据使用Data Nucleus持久化，因此mysql等数据库可以和hive结合，存储元数据。</p>

<p>2. metastore的两种方式<br>
配置metastore服务端以及metastore数据库主要有内嵌、远程两种方式。<br>
2.1 内嵌方式<br>
hive使用derby作为内嵌的metastore数据库，主要用于测试，但是在同一时间，它只允许一个进程连接metastore数据库。<br>
注意确认数据库能被执行查询的机器访问到，jdbc库放到hive客户端的classpath下。<br>
2.2 远程方式（我使用的方式）<br>
远程metastore服务端和客户端之间使用Thrift协议通信。执行<span style="color:#f33b45;"><strong>hive --service metastore </strong></span>启动Thrift服务端。</p>

<p>（所以我在初始化之后没有启动Thrift服务端，直接执行SQL语句就会报错）<br>
如果使用mysql，需要在启动hive客户端和HiveMetastore服务端之前把jar包放到HIVE_HOME/lib目录。<br>
远程metastore服务端和客户端都需要设置hive.metastore.warehouse.dir参数为HDFS路径，此外服务端需要设置ConnectionURL等参数，客户端需要设置hive.metastore.uris参数。</p>

<p>相关文章：</p>

<h1><a href="https://www.cnblogs.com/netuml/p/7841387.html" rel="nofollow" id="cb_post_title_url">Hive 学习笔记（启动方式，内置服务）</a></h1>            </div>
                </div>
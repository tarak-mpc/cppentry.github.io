---
layout:     post
title:      elasticsearch2.1 elasticsearch-hadoop安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="background-color:rgb(199,237,204);"><span style="font-size:10.5pt;">1、下载elasticsearch-hadoop-<span style="font-size:14px;line-height:21px;"><span style="font-family:'Microsoft YaHei UI';font-size:10.5pt;line-height:1.5;">2.2.0</span><span style="font-size:10.5pt;line-height:1.5;">beta1</span></span>.jar</span>，拷贝到hive的lib目录中，然后以如下方式打开hive命令窗口：<br></div>
<blockquote style="border:none;">
<div style="background-color:rgb(199,237,204);">bin/hive -hiveconf hive.aux.jars.path=/root/hive/lib/elasticsearch-hadoop-<span style="font-family:'Microsoft YaHei UI';font-size:10.5pt;line-height:1.5;">2.2.0</span><span style="font-size:10.5pt;line-height:1.5;">beta1</span>.jar</div>
<div style="background-color:rgb(199,237,204);"><span style="font-size:10.5pt;">这个也可以写在hive的配置文件中，</span></div>
<div style="background-color:rgb(199,237,204);">&lt;property&gt;</div>
<div style="background-color:rgb(199,237,204);">        &lt;name&gt;hive.aux.jars.path&lt;/name&gt;</div>
<div style="background-color:rgb(199,237,204);">        &lt;value&gt;hdfs://ns/path/elasticsearch-hadoop.jar&lt;/value&gt;</div>
<div style="background-color:rgb(199,237,204);">       &lt;description&gt;A comma separated list (with no spaces) of the jar files&lt;/description&gt;</div>
<div style="background-color:rgb(199,237,204);">&lt;/property&gt;</div>
</blockquote>
<div style="background-color:rgb(199,237,204);">2、创建表<br></div>
<blockquote style="border:none;">
<div style="background-color:rgb(199,237,204);"><span style="font-family:'Microsoft Yahei';color:#272a30;"><span style="line-height:22px;">CREATE TABLE es(location STRING, url STRING) </span></span></div>
<div style="background-color:rgb(199,237,204);"><span style="font-family:'Microsoft Yahei';color:#272a30;"><span style="line-height:22px;">STORED BY 'org.elasticsearch.hadoop.hive.EsStorageHandler' </span></span></div>
<div style="background-color:rgb(199,237,204);"><span style="font-family:'Microsoft Yahei';color:#272a30;"><span style="line-height:22px;">TBLPROPERTIES('es.resource' = 'test/share',</span></span></div>
<div style="background-color:rgb(199,237,204);"><span style="font-family:'Microsoft Yahei';color:#272a30;"><span style="line-height:22px;">'es.index.auto.create' = 'true','es.nodes'='customercenter1,customercenter2,customercenter3,customercenter4','es.port'='9200');</span></span></div>
</blockquote>
<div style="background-color:rgb(199,237,204);">
<p style="font-family:'Microsoft Yahei';line-height:22px;color:rgb(39,42,48);background-color:rgb(255,255,255);">
<span style="font-size:10.5pt;"></span></p>
</div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
<br></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
由于在<span style="font-size:10.5pt;line-height:1.5;">Elasticsearch for hive搭建时，</span><span style="font-size:10.5pt;line-height:1.5;">Elasticsearch使用的是官方最新版本，而</span><span style="font-size:10.5pt;line-height:1.5;">Elasticsearch
 for</span><span style="font-size:10.5pt;line-height:1.5;"> hadoop的</span><span style="font-size:10.5pt;line-height:1.5;">插件包版本更新不及时,导致在环境搭建的时候出现了问题</span></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
<div> Elasticsearch for Apache Hadoop 2.1.0版本及前版本不支持最新的<span style="font-size:10.5pt;line-height:1.5;">Elasticsearch2.1.0导致遇到兼容性问题如下：</span></div>
</div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
而<span style="font-size:10.5pt;line-height:1.5;">Elasticsearch for Apache Hadoop</span><span style="font-size:10.5pt;line-height:1.5;">2.1.1版本开始做了版本限制,详情如下：</span></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
</div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
<span style="font-size:10.5pt;line-height:1.5;">Elasticsearch for Apache Hadoop</span><span style="font-size:10.5pt;line-height:1.5;">2.2.0</span><span style="font-size:10.5pt;line-height:1.5;">beta1开始对</span><span style="font-size:10.5pt;line-height:1.5;">Elasticsearch2.x版本做了兼容</span></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
</div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
<span style="font-size:10.5pt;line-height:1.5;">在使用</span><span style="font-size:10.5pt;line-height:1.5;">Elasticsearch for Apache Hadoop</span><span style="font-size:10.5pt;line-height:1.5;">2.2.0</span><span style="font-size:10.5pt;line-height:1.5;">beta1过程中一直遇到以下问题：</span></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
</div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
经过修改源码打印日志发现是rest API返回结果变了</div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
Elasticsearch1.7的<span style="font-size:10.5pt;line-height:1.5;">/_nodes/transport </span><span style="font-size:10.5pt;line-height:1.5;">rest API返回结果</span></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
<span style="font-size:10.5pt;line-height:1.5;"><img src="" alt=""><br></span></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
</div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
Elasticsearch2.1<span style="font-size:10.5pt;line-height:1.5;">的</span><span style="font-size:10.5pt;line-height:1.5;">/_nodes/transport </span><span style="font-size:10.5pt;line-height:1.5;">rest
 API返回结果</span></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
<span style="font-size:10.5pt;line-height:1.5;"><img src="" alt=""><br></span></div>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
</div>
<span style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">现已修改解析http_address部分源代码StringUtils.java：</span>
<div style="font-family:'Microsoft YaHei UI';background-color:rgb(199,237,204);">
<br style="font-size:14px;line-height:21px;"></div>
<div><img src="" alt=""><br></div>
            </div>
                </div>
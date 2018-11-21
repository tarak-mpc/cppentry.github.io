---
layout:     post
title:      启动Hbase后Hmaster节点挂掉的几个原因
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;">学习HBase过程中，在安装Hbase后，会出现Hmaster节点在几秒种后会突然挂掉，有可能是以下原因:<br>
   1.时钟不同步<br>
   2.zookeeper出问题了<br>
   3.hdfs和hbase配置的端口不匹配<br>
    hdfs<br>
     core-site.xml hdfs://master<br>
     hbase-rootdir hdfs://master:9000/hbase<br>
   4.zookeeper /hbase 节点的历史数据不匹配 <br>
    zkCli.sh -server master:2181<br>
    ls /<br>
    [/hbase , /zookeeper]<br>
    rmr /hbase</span></p>
<p><span style="font-size:24px;">    重启zookeeper和Hbase即可</span></p>
            </div>
                </div>
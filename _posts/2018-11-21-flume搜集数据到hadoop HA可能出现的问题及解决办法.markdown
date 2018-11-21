---
layout:     post
title:      flume搜集数据到hadoop HA可能出现的问题及解决办法
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/haoxiaoyan/article/details/84304736				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;">把Hadoop集群的hdfs-site.xml、core-site.xml两个配置文件复制到 flume安装目录的conf目录去，把hadoop-hdfs-2.7.1.jar复制到 Flume  lib目录。</p>

<p style="margin-left:0cm;"><span style="color:#000000;">ERROR - org.apache.flume.sink.hdfs.HDFSEventSink.process(HDFSEventSink.java:459)] process failed</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;">java.lang.IllegalArgumentException: java.net.UnknownHostException: ha1</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;">解决方法</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;">ha1公司Hadoop集群NameService的名字，这个错误是由于找不到Hadoop集群NameService造成的，所以需要把hdfs-site.xml复制到flume/conf目录。</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;">root@:~# cp /etc/hadoop/conf/hdfs-site.xml /home//flume/conf/</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">java.io.IOException: Mkdirs failed to create /test/flume/16-09-19 (exists=false, cwd=file:/data/apache-flume-1.6.0-bin)</p>

<p style="margin-left:0cm;">解决方法</p>

<p style="margin-left:0cm;"><span style="color:#000000;">root@:~# cp/etc/hadoop/conf/core-site.xml /home/flume/conf/</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">java.io.IOException: No FileSystem for scheme: hdfs</p>

<p style="margin-left:0cm;"><span style="color:#444444;">解决方法把hadoop-hdfs-*.jar复制到flume/lib目录下</span></p>

<p style="margin-left:0cm;"><span style="color:#000000;">root@:~# cp /opt/cloudera/parcels/CDH-5.13.3-1.cdh5.13.3.p0.2/lib/hadoop/client/hadoop-hdfs.jar /home/worker/flume/lib/</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">java.lang.NullPointerException: Expected timestamp in the Flume event headers, but it was null</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;"><span style="color:#444444;">原因是Event对象headers没有设置timestamp造成的，解决办法：设置a1.sinks.k1.hdfs.useLocalTimeStamp=true，使用本地时间戳。</span></p>            </div>
                </div>
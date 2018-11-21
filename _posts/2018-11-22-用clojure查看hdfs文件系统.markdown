---
layout:     post
title:      用clojure查看hdfs文件系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><br></p>
(add-classpath "file:///usr/hadoop/hadoop/hadoop-core-0.20.2-cdh3u1.jar")<br>
(add-classpath "file:///usr/hadoop/hadoop/lib/commons-logging-1.0.4.jar")<br><br><br>
(import '(java.io IOException))<br><br>
(import '(org.apache.hadoop.conf Configuration))<br>
(import '(org.apache.hadoop.fs FileSystem))<br>
(import '(org.apache.hadoop.fs FileStatus))<br><p>(import '(org.apache.hadoop.fs Path))</p>
<p><br></p>
(def conf (new Configuration))<br>
(. conf addResource (new Path "/usr/hadoop/hadoop/conf/core-site.xml"))<br>
(println (. conf get "fs.default.name"))<br>
(def hdfs (. FileSystem get conf))<br>
(println (. hdfs getName))<br>
(def rootf (new Path "/"))<br>
(. hdfs exists rootf)<br>
(. hdfs listStatus rootf)
<p><br></p>
<p><br>
(for [file (. hdfs listStatus rootf)] (. file getPath)) <br><br></p>
            </div>
                </div>
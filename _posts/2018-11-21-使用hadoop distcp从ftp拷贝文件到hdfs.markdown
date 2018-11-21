---
layout:     post
title:      使用hadoop distcp从ftp拷贝文件到hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>DistCp（Distributed Copy）是用于大规模集群内部或者集群之间的高性能拷贝工具。 它使用Map/Reduce实现文件分发，错误处理和恢复，以及报告生成。 它把文件和目录的列表作为map任务的输入，每个任务会完成源列表中部分文件的拷贝。</p>
<p><a href="http://hadoop.apache.org/docs/r2.7.1/hadoop-distcp/DistCp.html" rel="nofollow">官网链接：http://hadoop.apache.org/docs/r2.7.1/hadoop-distcp/DistCp.html</a></p>
<p>最常用的就是从一个集群拷贝文件到第二个集群：<br></p>
<p>这将从第一个集群中复制/foo目录（和它的内容）到第二个集群中的/bar目录下，所以第二个集群会有/bar/foo目录结构。如果/bar不存在，则会新建一个。可以指定多个源路径，并且所有的都会被复制到目标路径。源路径必须是绝对路径：</p>
<p></p>
<pre>bash$ hadoop distcp hdfs://namenode1/foo hdfs://namenode2/bar </pre>
<p>==============================================================================</p>
<p><br></p>
<p></p>
<p><span style="font-size:18px;"><strong>distcp从ftp拷贝文件到hdfs：</strong></span></p>
<p>hadoop distcp ftp://fptuser:ftppassword@host/ftp-path/ hdfs://node:port1/hdfs-path/</p>
<p><span style="font-size:18px;"><strong>============= example================</strong></span><br></p>
<p>hdfs目录：hdfs dfs -ls /user/wanghc</p>
<p>ftp文件目录：/root/README.txt</p>
<p>默认端口：21<br></p>
<p>hadoop distcp ftp://root:uway123@192.168.8.192/root/README.txt hdfs://192.168.8.109:8020/user/wanghc</p>
<p><br></p>
<p>如果ftp端口修改为 9022</p>
<p>hadoop distcp ftp://root:uway123@192.168.8.192:9022/root/README.txt hdfs://192.168.8.109:8020/user/wanghc<br></p>
            </div>
                </div>
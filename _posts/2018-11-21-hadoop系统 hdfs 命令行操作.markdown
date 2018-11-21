---
layout:     post
title:      hadoop系统 hdfs 命令行操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Hadoop文件系统shell命令列表: https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html</p><p>命令查看:  hadoop fs</p><pre><code class="language-html">[hadoop@mini1 mapreduce]$ hadoop fs
Usage: hadoop fs [generic options]
	[-appendToFile &lt;localsrc&gt; ... &lt;dst&gt;]
	[-cat [-ignoreCrc] &lt;src&gt; ...]
	[-checksum &lt;src&gt; ...]
	[-chgrp [-R] GROUP PATH...]
	[-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]
	[-chown [-R] [OWNER][:[GROUP]] PATH...]
	[-copyFromLocal [-f] [-p] [-l] [-d] &lt;localsrc&gt; ... &lt;dst&gt;]
	[-copyToLocal [-f] [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]
	[-count [-q] [-h] [-v] [-t [&lt;storage type&gt;]] [-u] [-x] &lt;path&gt; ...]
	[-cp [-f] [-p | -p[topax]] [-d] &lt;src&gt; ... &lt;dst&gt;]
	[-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]
	[-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]
	[-df [-h] [&lt;path&gt; ...]]
	[-du [-s] [-h] [-x] &lt;path&gt; ...]
	[-expunge]
	[-find &lt;path&gt; ... &lt;expression&gt; ...]
	[-get [-f] [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]
	[-getfacl [-R] &lt;path&gt;]
	[-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]
	[-getmerge [-nl] [-skip-empty-file] &lt;src&gt; &lt;localdst&gt;]
	[-help [cmd ...]]
	[-ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [&lt;path&gt; ...]]
	[-mkdir [-p] &lt;path&gt; ...]
	[-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]
	[-moveToLocal &lt;src&gt; &lt;localdst&gt;]
</code></pre><br><h4>1. hdfs文件上传</h4><pre><code class="language-html"># 上传 XXX.zip 文件到 / 目录
hadoop fs -put XXX.zip /</code></pre><h4>2. 查看文件目录列表</h4><pre><code class="language-html">[hadoop@mini1 ~]$ hadoop fs -ls /
Found 2 items
-rw-r--r--   2 hadoop supergroup  354635831 2018-04-02 10:27 /jdk-9.0.4_linux-x64_bin.tar.gz
-rw-r--r--   2 hadoop supergroup        279 2018-04-02 10:30 /test.txt</code></pre><p><img src="https://img-blog.csdn.net/20180402223152452" alt=""></p><h4>3. 获取文件 hadoop fs -get /XXX</h4><pre><code class="language-html">[hadoop@mini2 ~]$ hadoop fs -get /jdk-9.0.4_linux-x64_bin.tar.gz
[hadoop@mini2 ~]$ ls
hadoop-2.9.0  hdfsdata  jdk-9.0.4_linux-x64_bin.tar.gz
[hadoop@mini2 ~]$ 
</code></pre><h4>4. 创建目录 hadoop fs -mkdir /XXX</h4><pre><code class="language-html">[hadoop@mini1 ~]$ hadoop fs -mkdir /wordCount
[hadoop@mini1 ~]$ hadoop fs -ls /
Found 3 items
-rw-r--r--   2 hadoop supergroup  354635831 2018-04-02 10:27 /jdk-9.0.4_linux-x64_bin.tar.gz
-rw-r--r--   2 hadoop supergroup        279 2018-04-02 10:30 /test.txt
drwxr-xr-x   - hadoop supergroup          0 2018-04-02 10:44 /wordCount
[hadoop@mini1 ~]$ </code></pre><h4>5. 指定jar命令  hadoop jar XXX.jar YYY /wordCount/ /wordCount/output</h4><pre><code class="language-html">[hadoop@mini1 mapreduce]$ hadoop jar hadoop-mapreduce-examples-2.9.0.jar wordcount /wordCount/ /wordCount/output
18/04/02 10:48:26 INFO client.RMProxy: Connecting to ResourceManager at mini1/192.168.241.100:8032
18/04/02 10:48:27 INFO input.FileInputFormat: Total input files to process : 2
18/04/02 10:48:28 INFO mapreduce.JobSubmitter: number of splits:2
18/04/02 10:48:28 INFO Configuration.deprecation: yarn.resourcemanager.system-metrics-publisher.enabled is deprecated. Instead, use yarn.system-metrics-publisher.enabled
18/04/02 10:48:28 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1522678018314_0001
18/04/02 10:48:29 INFO impl.YarnClientImpl: Submitted application application_1522678018314_0001
18/04/02 10:48:29 INFO mapreduce.Job: The url to track the job: http://mini1:8088/proxy/application_1522678018314_0001/
18/04/02 10:48:29 INFO mapreduce.Job: Running job: job_1522678018314_0001
18/04/02 10:48:36 INFO mapreduce.Job: Job job_1522678018314_0001 running in uber mode : false
18/04/02 10:48:36 INFO mapreduce.Job:  map 0% reduce 0%
18/04/02 10:48:48 INFO mapreduce.Job:  map 100% reduce 0%
18/04/02 10:48:53 INFO mapreduce.Job:  map 100% reduce 100%
18/04/02 10:48:54 INFO mapreduce.Job: Job job_1522678018314_0001 completed successfully


[hadoop@mini1 mapreduce]$ hadoop fs -cat /wordCount/output/part-r-00000
Apr	2
Don't	1
Jan	1
Mar	1
a	1
and	1
for	2
hadoop	9

[hadoop@mini1 mapreduce]$ 
</code></pre><p>6. 设置文件副本数 setrep</p><p>hadoop fs -setrep 10 XXX.yyy</p><p>这里设置的文件副本数是指记录在namenode中的元数据，具体的副本数量还需要看集群中国是否有足够数量的机器存放，如果有则真实的副本数量为元数据中的数量，否则只能达到机器允许的最大数量。</p>            </div>
                </div>
---
layout:     post
title:      hadoop自带的wordcount小案例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1.建立文件目录并生成文件</p>

<pre><code>[root@centos hadoop]# ls
hadoop-1.2.1  hadoop-1.2.1.tar.gz
[root@centos hadoop]# mkdir  work
[root@centos hadoop]# ls
hadoop-1.2.1  hadoop-1.2.1.tar.gz  work
[root@centos hadoop]# cd work/
[root@centos work]# mkdir wordcount
[root@centos work]# ls
wordcount
[root@centos work]# cd wordcount/
</code></pre>

<ol>
<li><p>向文件里写入文本</p>

<p>[root@centos wordcount]# echo “hello world”&gt;test1.txt <br>
[root@centos wordcount]# echo “hello hadoop”&gt;test2.txt <br>
[root@centos wordcount]# echo “hello hadoop centos”&gt;test3.txt <br>
[root@centos wordcount]# ls <br>
test1.txt  test2.txt  test3.txt <br>
[root@centos wordcount]#  <br>
[root@centos wordcount]# cd .. <br>
[root@centos work]# ls -l wordcount <br>
总用量 12 <br>
-rw-r–r–. 1 root root 12 8月  18 01:52 test1.txt <br>
-rw-r–r–. 1 root root 13 8月  18 01:52 test2.txt <br>
-rw-r–r–. 1 root root 20 8月  18 01:53 test3.txt <br>
[root@centos work]#</p></li>
</ol>

<p>3.上传至hdfs</p>

<pre><code>[root@centos work]# hadoop dfs -put wordcount 
Warning: $HADOOP_HOME is deprecated.

Usage: java FsShell [-put &lt;localsrc&gt; ... &lt;dst&gt;]
[root@centos work]# hadoop dfs -put wordcount wordcount
Warning: $HADOOP_HOME is deprecated.

put: Cannot copy wordcount to its subdirectory wordcount/wordcount
[root@centos work]# hadoop dfs -ls
Warning: $HADOOP_HOME is deprecated.

Found 1 items
drwxr-xr-x   - root root       4096 2016-08-18 01:53 /root/xuekan/hadoop/work/wordcount

[root@centos work]# hadoop dfs -mkdir  work
Warning: $HADOOP_HOME is deprecated.


[root@centos work]# hadoop dfs -put /root/xuekan/hadoop/work/wordcount   work
Warning: $HADOOP_HOME is deprecated.

16/08/18 02:05:47 INFO util.NativeCodeLoader: Loaded the native-hadoop library
[root@centos work]# hadoop dfs -ls
Warning: $HADOOP_HOME is deprecated.

Found 1 items
drwxr-xr-x   - root root       4096 2016-08-18 01:53 /root/xuekan/hadoop/work/wordcount
[root@centos work]# hadoop dfs -ls  /root/xuekan/hadoop/work/work
Warning: $HADOOP_HOME is deprecated.

Found 1 items
drwxr-xr-x   - root root       4096 2016-08-18 02:05 /root/xuekan/hadoop/work/work/wordcount
[root@centos work]# hadoop dfs -ls  /root/xuekan/hadoop/work/work/wordcount/
Warning: $HADOOP_HOME is deprecated.

Found 3 items
-rwxrwxrwx   1 root root         20 2016-08-18 02:05 /root/xuekan/hadoop/work/work/wordcount/test3.txt
-rwxrwxrwx   1 root root         12 2016-08-18 02:05 /root/xuekan/hadoop/work/work/wordcount/test1.txt
-rwxrwxrwx   1 root root         13 2016-08-18 02:05 /root/xuekan/hadoop/work/work/wordcount/te
</code></pre>

<p>4.使用hadoop自带的wordcount.jar执行文件</p>

<pre><code>[root@centos work]# hadoop jar /root/xuekan/hadoop/hadoop-1.2.1/hadoop-examples-1.2.1.jar wordcount /root/xuekan/hadoop/work/work/wordcount  /root/xuekan/hadoop/work/work/wordcount/out
Warning: $HADOOP_HOME is deprecated.

16/08/18 02:10:07 INFO util.NativeCodeLoader: Loaded the native-hadoop library
16/08/18 02:10:07 INFO input.FileInputFormat: Total input paths to process : 3
16/08/18 02:10:07 WARN snappy.LoadSnappy: Snappy native library not loaded
16/08/18 02:10:08 INFO mapred.JobClient: Running job: job_201608180129_0001
16/08/18 02:10:09 INFO mapred.JobClient:  map 0% reduce 0%
16/08/18 02:10:30 INFO mapred.JobClient:  map 66% reduce 0%
16/08/18 02:10:36 INFO mapred.JobClient:  map 100% reduce 0%
16/08/18 02:10:43 INFO mapred.JobClient:  map 100% reduce 100%
16/08/18 02:10:45 INFO mapred.JobClient: Job complete: job_201608180129_0001
16/08/18 02:10:45 INFO mapred.JobClient: Counters: 27
16/08/18 02:10:45 INFO mapred.JobClient:   Job Counters 
16/08/18 02:10:45 INFO mapred.JobClient:     Launched reduce tasks=1
16/08/18 02:10:45 INFO mapred.JobClient:     SLOTS_MILLIS_MAPS=22693
16/08/18 02:10:45 INFO mapred.JobClient:     Total time spent by all reduces waiting after reserving slots (ms)=0
16/08/18 02:10:45 INFO mapred.JobClient:     Total time spent by all maps waiting after reserving slots (ms)=0
16/08/18 02:10:45 INFO mapred.JobClient:     Rack-local map tasks=3
16/08/18 02:10:45 INFO mapred.JobClient:     Launched map tasks=3
16/08/18 02:10:45 INFO mapred.JobClient:     SLOTS_MILLIS_REDUCES=12284
16/08/18 02:10:45 INFO mapred.JobClient:   File Output Format Counters 
16/08/18 02:10:45 INFO mapred.JobClient:     Bytes Written=46
16/08/18 02:10:45 INFO mapred.JobClient:   FileSystemCounters
16/08/18 02:10:45 INFO mapred.JobClient:     FILE_BYTES_READ=1302
16/08/18 02:10:45 INFO mapred.JobClient:     FILE_BYTES_WRITTEN=184818
16/08/18 02:10:45 INFO mapred.JobClient:   File Input Format Counters 
16/08/18 02:10:45 INFO mapred.JobClient:     Bytes Read=81
16/08/18 02:10:45 INFO mapred.JobClient:   Map-Reduce Framework
16/08/18 02:10:45 INFO mapred.JobClient:     Map output materialized bytes=105
16/08/18 02:10:45 INFO mapred.JobClient:     Map input records=3
16/08/18 02:10:45 INFO mapred.JobClient:     Reduce shuffle bytes=105
16/08/18 02:10:45 INFO mapred.JobClient:     Spilled Records=14
16/08/18 02:10:45 INFO mapred.JobClient:     Map output bytes=73
16/08/18 02:10:45 INFO mapred.JobClient:     Total committed heap usage (bytes)=417280000
16/08/18 02:10:45 INFO mapred.JobClient:     CPU time spent (ms)=1980
16/08/18 02:10:45 INFO mapred.JobClient:     Combine input records=7
16/08/18 02:10:45 INFO mapred.JobClient:     SPLIT_RAW_BYTES=357
16/08/18 02:10:45 INFO mapred.JobClient:     Reduce input records=7
16/08/18 02:10:45 INFO mapred.JobClient:     Reduce input groups=4
16/08/18 02:10:45 INFO mapred.JobClient:     Combine output records=7
16/08/18 02:10:45 INFO mapred.JobClient:     Physical memory (bytes) snapshot=591405056
16/08/18 02:10:45 INFO mapred.JobClient:     Reduce output records=4
16/08/18 02:10:45 INFO mapred.JobClient:     Virtual memory (bytes) snapshot=2890244096
16/08/18 02:10:45 INFO mapred.JobClient:     Map output records=7
</code></pre>

<p>5.查看输出的结果</p>

<pre><code>[root@centos work]# hadoop dfs -cat /root/xuekan/hadoop/work/work/wordcount/out
Warning: $HADOOP_HOME is deprecated.

cat: /root/xuekan/hadoop/work/work/wordcount/out (是一个目录)
[root@centos work]# hadoop dfs -cat /root/xuekan/hadoop/work/work/wordcount/out/part-r-00000
Warning: $HADOOP_HOME is deprecated.

centos  1
hadoop  2
hello   3
world   1
[root@centos work]# 
</code></pre>

<p>6.实验完成</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
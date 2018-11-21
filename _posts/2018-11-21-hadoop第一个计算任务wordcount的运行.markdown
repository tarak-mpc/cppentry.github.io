---
layout:     post
title:      hadoop第一个计算任务wordcount的运行
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/jjshouji/article/details/77980460				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.上传文件HDFS</p>
<p></p><pre><code class="language-plain">[hadoop@master example]$ cat mytest.txt #文件内容
Hello world ,you are My lunky! 
Hello you are my friend!
hello ,are you OKay!
hello ,GGG?
no GG</code></pre>上传文件到HDFS
<p></p><pre><code class="language-plain">[hadoop@master example]$ hadoop fs -put mytest.txt /example/data/  #上传到目录/example/data/
[hadoop@master example]$ hadoop fs -cat  /example/data/mytest.txt #查看上传的内容
Hello world ,you are My lunky! 
Hello you are my friend!
hello ,are you OKay!
hello ,GGG?
no GG
[hadoop@master example]$ </code></pre><br>
2.执行计算
<p></p><pre><code class="language-plain">[hadoop@master mapreduce]$ pwd
/home/hadoop/hadoop-2.8.1/share/hadoop/mapreduce
[hadoop@master mapreduce]$ hadoop jar hadoop-mapreduce-examples-2.8.1.jar wordcount /example/data/mytest.txt /output
17/09/14 10:51:57 INFO client.RMProxy: Connecting to ResourceManager at master/10.0.1.118:18040
17/09/14 10:51:58 INFO input.FileInputFormat: Total input files to process : 1
17/09/14 10:51:58 INFO mapreduce.JobSubmitter: number of splits:1
17/09/14 10:51:58 INFO mapreduce.JobSubmitter: Submitting tokens for job: job_1505390401611_0002
17/09/14 10:51:59 INFO impl.YarnClientImpl: Submitted application application_1505390401611_0002
17/09/14 10:51:59 INFO mapreduce.Job: The url to track the job: http://master:18088/proxy/application_1505390401611_0002/
17/09/14 10:51:59 INFO mapreduce.Job: Running job: job_1505390401611_0002
17/09/14 10:52:13 INFO mapreduce.Job: Job job_1505390401611_0002 running in uber mode : false
17/09/14 10:52:13 INFO mapreduce.Job:  map 0% reduce 0%
17/09/14 10:52:21 INFO mapreduce.Job:  map 100% reduce 0%
17/09/14 10:52:28 INFO mapreduce.Job:  map 100% reduce 100%
17/09/14 10:52:29 INFO mapreduce.Job: Job job_1505390401611_0002 completed successfully
17/09/14 10:52:30 INFO mapreduce.Job: Counters: 49
     File System Counters
        FILE: Number of bytes read=171
        FILE: Number of bytes written=272737
        FILE: Number of read operations=0
        FILE: Number of large read operations=0
        FILE: Number of write operations=0
        HDFS: Number of bytes read=203
        HDFS: Number of bytes written=105
        HDFS: Number of read operations=6
        HDFS: Number of large read operations=0
        HDFS: Number of write operations=2
     Job Counters 
        Launched map tasks=1
        Launched reduce tasks=1
        Data-local map tasks=1
        Total time spent by all maps in occupied slots (ms)=6215
        Total time spent by all reduces in occupied slots (ms)=4850
        Total time spent by all map tasks (ms)=6215
        Total time spent by all reduce tasks (ms)=4850
        Total vcore-milliseconds taken by all map tasks=6215
        Total vcore-milliseconds taken by all reduce tasks=4850
        Total megabyte-milliseconds taken by all map tasks=6364160
        Total megabyte-milliseconds taken by all reduce tasks=4966400
     Map-Reduce Framework
        Map input records=5
        Map output records=19
        Map output bytes=171
        Map output materialized bytes=171
        Input split bytes=107
        Combine input records=19
        Combine output records=15
        Reduce input groups=15
        Reduce shuffle bytes=171
        Reduce input records=15
        Reduce output records=15
        Spilled Records=30
        Shuffled Maps =1
        Failed Shuffles=0
        Merged Map outputs=1
        GC time elapsed (ms)=180
        CPU time spent (ms)=1620
        Physical memory (bytes) snapshot=294346752
        Virtual memory (bytes) snapshot=4173053952
        Total committed heap usage (bytes)=139882496
     Shuffle Errors
        BAD_ID=0
        CONNECTION=0
        IO_ERROR=0
        WRONG_LENGTH=0
        WRONG_MAP=0
        WRONG_REDUCE=0
     File Input Format Counters 
        Bytes Read=96
     File Output Format Counters 
        Bytes Written=105</code></pre>
<p><br></p>
<p>3.查看计算结果</p>
<p><br></p>
<p></p><pre><code class="language-plain">[hadoop@master mapreduce]$ hadoop fs -cat /output/part-r-00000
,GGG?   1
,are 1
,you 1
GG   1
Hello   2
My   1
OKay!   1
are  2
friend! 1
hello   2
lunky!  1
my   1
no   1
world   1
you  2
[hadoop@master mapreduce]$ </code></pre><br><br>
运行hadoop自带的wordcount 计算，让自己对hadoop 有个初步了解.<br><br><br><br>            </div>
                </div>
---
layout:     post
title:      WordCount运行笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">前期工作：</span></p>
<p><span style="font-size:14px;">Hadoop正常开启，将WordCount.Java文件放在Hadoop安装目录下，并在目录下创建输入目录input，目录下有输入文件file1、file2。其中：</span></p>
<p><span style="font-size:14px;">file1的内容为：</span></p>
<p><span style="font-size:14px;">hello world</span></p>
<p><span style="font-size:14px;">file2的内容为：</span></p>
<p><span style="font-size:14px;">hello Hadoop</span></p>
<p><span style="font-size:14px;">hello mapreduce</span></p>
<p><span style="font-size:14px;">准备好之后在命令行输入命令运行。下面对执行的命令进行介绍：</span></p>
<p><span style="font-size:14px;">1）在集群上创建输入文件夹：</span></p>
<p><span style="font-size:14px;">xiaoqian@ubuntu:~/opt/hadoop$ sudo bin/hadoop fs -mkdir input3</span></p>
<p><span style="font-size:14px;">2) 上传本地目录input下的几个file文件到集群上的input3目录下：</span></p>
<p><span style="font-size:14px;">xiaoqian@ubuntu:~/opt/hadoop$ sudo bin/hadoop fs -put input/file* input3</span></p>
<p><span style="font-size:14px;">3）编译WordCount.Java程序，将结果放入当前目录的wordcount_classes目录下：</span></p>
<p><span style="font-size:14px;">xiaoqian@ubuntu:~/opt/hadoop$ javac -classpath hadoop-0.20.1-core.jar:lib/commons-cli-1.2.jar -d wordcount_classes WordCount.java<br></span></p>
<p><span style="font-size:14px;">4）将编译结果打成jar包：</span></p>
<p><span style="font-size:14px;"><span style="font-size:14px;">xiaoqian@ubuntu:~/opt/hadoop$ jar -cvf wordcount.jar -C WordCount</span><br></span></p>
<p><span style="font-size:14px;"><span style="font-size:14px;">5)  在集群上运行WordCount程序，以input3目录作为输入目录，output3目录作为输出目录：</span></span></p>
<span style="font-size:14px;">xiaoqian@ubuntu:~/opt/hadoop$ sudo bin/hadoop jar wordcount_classes.jar org.apache.hadoop.examples.WordCount input3 output3<br>
14/04/21 17:56:52 INFO input.FileInputFormat: Total input paths to process : 2<br>
14/04/21 17:56:52 INFO mapred.JobClient: Running job: job_201404211455_0013<br>
14/04/21 17:56:53 INFO mapred.JobClient:  map 0% reduce 0%<br>
14/04/21 17:57:02 INFO mapred.JobClient:  map 100% reduce 0%<br>
14/04/21 17:57:14 INFO mapred.JobClient:  map 100% reduce 100%<br>
14/04/21 17:57:16 INFO mapred.JobClient: Job complete: job_201404211455_0013<br>
14/04/21 17:57:16 INFO mapred.JobClient: Counters: 17<br>
14/04/21 17:57:16 INFO mapred.JobClient:   Job Counters <br>
14/04/21 17:57:16 INFO mapred.JobClient:     Launched reduce tasks=1<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Launched map tasks=2<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Data-local map tasks=2<br>
14/04/21 17:57:16 INFO mapred.JobClient:   FileSystemCounters<br>
14/04/21 17:57:16 INFO mapred.JobClient:     FILE_BYTES_READ=71<br>
14/04/21 17:57:16 INFO mapred.JobClient:     HDFS_BYTES_READ=41<br>
14/04/21 17:57:16 INFO mapred.JobClient:     FILE_BYTES_WRITTEN=212<br>
14/04/21 17:57:16 INFO mapred.JobClient:     HDFS_BYTES_WRITTEN=37<br>
14/04/21 17:57:16 INFO mapred.JobClient:   Map-Reduce Framework<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Reduce input groups=0<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Combine output records=5<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Map input records=3<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Reduce shuffle bytes=47<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Reduce output records=0<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Spilled Records=10<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Map output bytes=65<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Combine input records=6<br>
14/04/21 17:57:16 INFO mapred.JobClient:     Map output records=6<br></span>
<p><span style="font-size:14px;">14/04/21 17:57:16 INFO mapred.JobClient:     Reduce input records=5</span></p>
<p><span style="font-size:14px;">6）查看输出结果：</span></p>
<p><span style="font-size:14px;">xiaoqian@ubuntu:~/opt/hadoop$ sudo bin/hadoop fs -cat output3/part-r-00000</span></p>
<p><span style="font-size:14px;">hadoop<span> </span>1</span></p>
<span style="font-size:14px;">hello<span> </span>3<br>
mapreduce<span> </span>1<br></span>
<p><span style="font-size:14px;">world<span> </span>1</span></p>
<p><span style="font-size:14px;"><br></span></p>
            </div>
                </div>
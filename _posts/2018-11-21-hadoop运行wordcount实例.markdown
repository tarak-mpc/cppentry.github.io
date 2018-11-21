---
layout:     post
title:      hadoop运行wordcount实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);">1.查看hadoop版本</span></p>
<p style="background:rgb(245,245,245);"> </p>
<p><span style="background:rgb(245,245,245);">[hadoop@ltt1 sbin]$ hadoop version</span></p>
<p><span style="background:rgb(245,245,245);">Hadoop </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2.6</span><span style="background:rgb(245,245,245);">.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span><span style="background:rgb(245,245,245);">-cdh5.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">12.0</span></p>
<p><span style="background:rgb(245,245,245);">Subversion http:</span><span style="color:rgb(0,128,0);background:rgb(245,245,245);">//github.com/cloudera/hadoop -r dba647c5a8bc5e09b572d76a8d29481c78d1a0dd</span></p>
<p><span style="background:rgb(245,245,245);">Compiled by jenkins on </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2017</span><span style="background:rgb(245,245,245);">-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">06</span><span style="background:rgb(245,245,245);">-29T11:33Z</span></p>
<p><span style="background:rgb(245,245,245);">Compiled with protoc </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2.5</span><span style="background:rgb(245,245,245);">.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">From source with checksum 7c45ae7a4592ce5af86bc4598c5b4</span></p>
<p><span style="background:rgb(245,245,245);">This command was run </span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">using</span><span style="background:rgb(245,245,245);"> /home/hadoop/hadoop260/share/hadoop/common/hadoop-common-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2.6</span><span style="background:rgb(245,245,245);">.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span><span style="background:rgb(245,245,245);">-cdh5.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">12.0</span><span style="background:rgb(245,245,245);">.jar</span></p>
<p style="background:rgb(245,245,245);"> </p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);">2.通过hadoop自带的jar文件，可以简单测试一些功能。</span></p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);"><span style="font-family:'black verdana';">查看</span>hadoop-mapreduce-examples-2.6.0-cdh5.12.0.jar文件所支持的MapReduce功能列表</span></p>
<p style="background:rgb(245,245,245);"> </p>
<p><span style="background:rgb(245,245,245);">[hadoop@ltt1 sbin]$ hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2.6</span><span style="background:rgb(245,245,245);">.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span><span style="background:rgb(245,245,245);">-cdh5.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">12.0</span><span style="background:rgb(245,245,245);">.jar</span></p>
<p><span style="background:rgb(245,245,245);">An example program must be given </span>
<span style="color:rgb(0,0,255);background:rgb(245,245,245);">as</span><span style="background:rgb(245,245,245);"> the first argument.</span></p>
<p><span style="background:rgb(245,245,245);">Valid program names are:</span></p>
<p><span style="background:rgb(245,245,245);">  aggregatewordcount: An Aggregate based map/reduce program that counts the words
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> the input files.</span></p>
<p><span style="background:rgb(245,245,245);">  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> the input files.</span></p>
<p><span style="background:rgb(245,245,245);">  bbp: A map/reduce program that uses Bailey-Borwein-Plouffe to compute exact digits of Pi.</span></p>
<p><span style="background:rgb(245,245,245);">  dbcount: An example job that count the pageview counts
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">from</span><span style="background:rgb(245,245,245);"> a database.</span></p>
<p><span style="background:rgb(245,245,245);">  distbbp: A map/reduce program that uses a BBP-type formula to compute exact bits of Pi.</span></p>
<p><span style="background:rgb(245,245,245);">  grep: A map/reduce program that counts the matches of a regex
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> the input.</span></p>
<p><span style="background:rgb(245,245,245);">  join: A job that effects a join over sorted, equally partitioned datasets</span></p>
<p><span style="background:rgb(245,245,245);">  multifilewc: A job that counts words
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">from</span><span style="background:rgb(245,245,245);"> several files.</span></p>
<p><span style="background:rgb(245,245,245);">  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.</span></p>
<p><span style="background:rgb(245,245,245);">  pi: A map/reduce program that estimates Pi
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">using</span><span style="background:rgb(245,245,245);"> a quasi-Monte Carlo method.</span></p>
<p><span style="background:rgb(245,245,245);">  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.</span></p>
<p><span style="background:rgb(245,245,245);">  randomwriter: A map/reduce program that writes 10GB of random data per node.</span></p>
<p><span style="background:rgb(245,245,245);">  secondarysort: An example defining a secondary sort to the reduce.</span></p>
<p><span style="background:rgb(245,245,245);">  sort: A map/reduce program that sorts the data written by the random writer.</span></p>
<p><span style="background:rgb(245,245,245);">  sudoku: A sudoku solver.</span></p>
<p><span style="background:rgb(245,245,245);">  teragen: Generate data </span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">for</span><span style="background:rgb(245,245,245);"> the terasort</span></p>
<p><span style="background:rgb(245,245,245);">  terasort: Run the terasort</span></p>
<p><span style="background:rgb(245,245,245);">  teravalidate: Checking results of terasort</span></p>
<p><span style="background:rgb(245,245,245);">  wordcount: A map/reduce program that counts the words
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> the input files.</span></p>
<p><span style="background:rgb(245,245,245);">  wordmean: A map/reduce program that counts the average length of the words
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> the input files.</span></p>
<p><span style="background:rgb(245,245,245);">  wordmedian: A map/reduce program that counts the median length of the words
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> the input files.</span></p>
<p><span style="background:rgb(245,245,245);">  wordstandarddeviation: A map/reduce program that counts the standard deviation of the length of the words
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> the input files.</span></p>
<p style="background:rgb(245,245,245);"> </p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);">3.在hdfs上创建文件夹</span></p>
<p><span style="background:rgb(245,245,245);">hadoop fs -mkdir /input</span></p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);">4.查看hdfs的更目录列表</span></p>
<p><span style="background:rgb(245,245,245);">[hadoop@ltt1 ~]$ hadoop fs -ls /</span><span style="background:rgb(245,245,245);"><br></span><span style="background:rgb(245,245,245);">Found 2 items</span><span style="background:rgb(245,245,245);"><br></span><span style="background:rgb(245,245,245);">drwxr-xr-x - hadoop supergroup 0 2017-09-17 08:11 /input</span><span style="background:rgb(245,245,245);"><br></span><span style="background:rgb(245,245,245);">drwx------ - hadoop supergroup 0 2017-09-17 08:07 /tmp</span></p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);">5.上传本地文件到hdfs</span></p>
<p><span style="background:rgb(245,245,245);">hadoop fs -put $HADOOP_HOME</span><span style="color:rgb(0,128,0);background:rgb(245,245,245);">/*.txt /input</span></p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);">6.查看hdfs上input目录下文件</span></p>
<p><span style="background:rgb(245,245,245);">[hadoop@ltt1 ~]$ hadoop fs -ls /input</span></p>
<p><span style="background:rgb(245,245,245);">Found </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">3</span><span style="background:rgb(245,245,245);"> items-rw-r--r--   </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2</span><span style="background:rgb(245,245,245);"> hadoop
 supergroup      </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">85063</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2017</span><span style="background:rgb(245,245,245);">-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">15</span><span style="background:rgb(245,245,245);"> /input/LICENSE.txt-rw-r--r--
   </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2</span><span style="background:rgb(245,245,245);"> hadoop supergroup      </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">14978</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2017</span><span style="background:rgb(245,245,245);">-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">15</span><span style="background:rgb(245,245,245);"> /input/NOTICE.txt-rw-r--r--
   </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2</span><span style="background:rgb(245,245,245);"> hadoop supergroup       </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">1366</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2017</span><span style="background:rgb(245,245,245);">-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">15</span><span style="background:rgb(245,245,245);"> /input/README.txt</span></p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);">7.wordcount简单测试。</span></p>
<p style="background:rgb(245,245,245);"> </p>
<p><span style="background:rgb(245,245,245);">[hadoop@ltt1 ~]$ hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2.6</span><span style="background:rgb(245,245,245);">.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span><span style="background:rgb(245,245,245);">-cdh5.</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">12.0</span><span style="background:rgb(245,245,245);">.jar
 wordcount /input /output</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">12</span><span style="background:rgb(245,245,245);"> INFO
 input.FileInputFormat: Total input paths to process : </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">317</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">13</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.JobSubmitter: number of splits:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">317</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">13</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.JobSubmitter: Submitting tokens </span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">for</span><span style="background:rgb(245,245,245);"> job: job_1505605169997_0002</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">14</span><span style="background:rgb(245,245,245);"> INFO
 impl.YarnClientImpl: Submitted application application_1505605169997_0002</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">14</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job: The url to track the job: http:</span><span style="color:rgb(0,128,0);background:rgb(245,245,245);">//ltt1.bg.cn:9180/proxy/application_1505605169997_0002/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">14</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job: Running job: job_1505605169997_0002</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">27</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job: Job job_1505605169997_0002 running </span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> uber mode :
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">false</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">27</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job:  map </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span><span style="background:rgb(245,245,245);">% reduce
</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span><span style="background:rgb(245,245,245);">%</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">39</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job:  map </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">33</span><span style="background:rgb(245,245,245);">% reduce
</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span><span style="background:rgb(245,245,245);">%</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">48</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job:  map </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">100</span><span style="background:rgb(245,245,245);">% reduce
</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span><span style="background:rgb(245,245,245);">%</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">50</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job:  map </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">100</span><span style="background:rgb(245,245,245);">% reduce
</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">100</span><span style="background:rgb(245,245,245);">%</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">50</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job: Job job_1505605169997_0002 completed successfully</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">09</span><span style="background:rgb(245,245,245);">/</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">17</span><span style="background:rgb(245,245,245);"> </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">08</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">19</span><span style="background:rgb(245,245,245);">:</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">50</span><span style="background:rgb(245,245,245);"> INFO
 mapreduce.Job: Counters: </span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">50</span></p>
<p><span style="background:rgb(245,245,245);">    File System Counters</span></p>
<p><span style="background:rgb(245,245,245);">        FILE: Number of bytes read=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">42705</span></p>
<p><span style="background:rgb(245,245,245);">        FILE: Number of bytes written=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">588235</span></p>
<p><span style="background:rgb(245,245,245);">        FILE: Number of read operations=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        FILE: Number of large read operations=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        FILE: Number of write operations=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        HDFS: Number of bytes read=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">101699</span></p>
<p><span style="background:rgb(245,245,245);">        HDFS: Number of bytes written=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">30167</span></p>
<p><span style="background:rgb(245,245,245);">        HDFS: Number of read operations=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">12</span></p>
<p><span style="background:rgb(245,245,245);">        HDFS: Number of large read operations=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        HDFS: Number of write operations=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2</span></p>
<p><span style="background:rgb(245,245,245);">    Job Counters </span></p>
<p><span style="background:rgb(245,245,245);">        Launched map tasks=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">3</span></p>
<p><span style="background:rgb(245,245,245);">        Launched reduce tasks=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">1</span></p>
<p><span style="background:rgb(245,245,245);">        Data-local map tasks=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2</span></p>
<p><span style="background:rgb(245,245,245);">        Rack-local map tasks=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">1</span></p>
<p><span style="background:rgb(245,245,245);">        Total time spent by all maps
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> occupied slots (ms)=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">47617</span></p>
<p><span style="background:rgb(245,245,245);">        Total time spent by all reduces
</span><span style="color:rgb(0,0,255);background:rgb(245,245,245);">in</span><span style="background:rgb(245,245,245);"> occupied slots (ms)=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">8244</span></p>
<p><span style="background:rgb(245,245,245);">        Total time spent by all map tasks (ms)=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">47617</span></p>
<p><span style="background:rgb(245,245,245);">        Total time spent by all reduce tasks (ms)=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">8244</span></p>
<p><span style="background:rgb(245,245,245);">        Total vcore-milliseconds taken by all map tasks=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">47617</span></p>
<p><span style="background:rgb(245,245,245);">        Total vcore-milliseconds taken by all reduce tasks=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">8244</span></p>
<p><span style="background:rgb(245,245,245);">        Total megabyte-milliseconds taken by all map tasks=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">48759808</span></p>
<p><span style="background:rgb(245,245,245);">        Total megabyte-milliseconds taken by all reduce tasks=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">8441856</span></p>
<p><span style="background:rgb(245,245,245);">    Map-Reduce Framework</span></p>
<p><span style="background:rgb(245,245,245);">        Map input records=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2035</span></p>
<p><span style="background:rgb(245,245,245);">        Map output records=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">14239</span></p>
<p><span style="background:rgb(245,245,245);">        Map output bytes=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">155828</span></p>
<p><span style="background:rgb(245,245,245);">        Map output materialized bytes=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">42717</span></p>
<p><span style="background:rgb(245,245,245);">        Input split bytes=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">292</span></p>
<p><span style="background:rgb(245,245,245);">        Combine input records=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">14239</span></p>
<p><span style="background:rgb(245,245,245);">        Combine output records=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2653</span></p>
<p><span style="background:rgb(245,245,245);">        Reduce input groups=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2402</span></p>
<p><span style="background:rgb(245,245,245);">        Reduce shuffle bytes=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">42717</span></p>
<p><span style="background:rgb(245,245,245);">        Reduce input records=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2653</span></p>
<p><span style="background:rgb(245,245,245);">        Reduce output records=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">2402</span></p>
<p><span style="background:rgb(245,245,245);">        Spilled Records=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">5306</span></p>
<p><span style="background:rgb(245,245,245);">        Shuffled Maps =</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">3</span></p>
<p><span style="background:rgb(245,245,245);">        Failed Shuffles=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        Merged Map outputs=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">3</span></p>
<p><span style="background:rgb(245,245,245);">        GC time elapsed (ms)=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">881</span></p>
<p><span style="background:rgb(245,245,245);">        CPU time spent (ms)=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">22320</span></p>
<p><span style="background:rgb(245,245,245);">        Physical memory (bytes) snapshot=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">690192384</span></p>
<p><span style="background:rgb(245,245,245);">        Virtual memory (bytes) snapshot=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">10862809088</span></p>
<p><span style="background:rgb(245,245,245);">        Total committed heap usage (bytes)=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">380243968</span></p>
<p><span style="background:rgb(245,245,245);">    Shuffle Errors</span></p>
<p><span style="background:rgb(245,245,245);">        BAD_ID=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        CONNECTION=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        IO_ERROR=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        WRONG_LENGTH=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        WRONG_MAP=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">        WRONG_REDUCE=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">0</span></p>
<p><span style="background:rgb(245,245,245);">    File Input Format Counters </span>
</p>
<p><span style="background:rgb(245,245,245);">        Bytes Read=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">101407</span></p>
<p><span style="background:rgb(245,245,245);">    File Output Format Counters </span>
</p>
<p><span style="background:rgb(245,245,245);">        Bytes Written=</span><span style="color:rgb(128,0,128);background:rgb(245,245,245);">30167</span></p>
<p style="background:rgb(245,245,245);"> </p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);">8.查看wordcount运行结果（由于结果太长，只举出了部分结果）</span></p>
<p style="background:rgb(245,245,245);"> </p>
<p><span style="background:rgb(245,245,245);">[hadoop@ltt1 ~]$ hadoop fs -cat /output</span><span style="color:rgb(0,128,0);background:rgb(245,245,245);">/*</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">worldwide,    4</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">would    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">writing    2</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">writing,    4</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">written    19</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">xmlenc    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">year    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">you    12</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">your    5</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">zlib    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);"> 252.227-7014(a)(1))    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">§    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“AS    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Contributor    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Contributor”    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Covered    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Executable”    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Initial    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Larger    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Licensable”    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“License”    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Modifications”    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Original    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Participant”)    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Patent    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Source    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“Your”)    1</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“You”    2</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“commercial    3</span></p>
<p><span style="color:rgb(0,128,0);background:rgb(245,245,245);">“control”    1</span></p>
<p style="background:rgb(245,245,245);"> </p>
<p style="background:rgb(0,0,0);"><span style="color:rgb(255,255,255);"><span style="font-family:'black verdana';">至此，通过一个</span>wordcount的一个小栗子，简介实践了一下hdfs的创建文件夹，上传文件，查看目录，运行wordcount实例。</span></p>
<p> </p>
            </div>
                </div>
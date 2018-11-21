---
layout:     post
title:      云计算学习笔记006---运行hadoop的例子程序：统计字符--wordcount例子程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主credreamer 允许不得转载 违者追究法律责任。					https://blog.csdn.net/lidew521/article/details/8830568				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'Times New Roman';font-size:14px;">04-</span>运行<span style="font-family:'Times New Roman';">wordcount</span>例子程序</p>
<div>
<p><span style="font-size:14px;">下面可以看下<span style="font-family:'Times New Roman';">hadoop</span>的例子程序：</span></p>
</div>
<p><span style="font-family:'Times New Roman';font-size:14px;">hadoop-0.20.2-examples.jar</span></p>
<p></p>
<p><span style="font-size:14px;">注意这里用到的<span style="font-family:'Times New Roman';">wordcount.txt</span>中的内容为：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">hello hadoop credream  hello hadoop credream  hello hadoop credream hello hadoop credream  hello hadoop credream</span></p>
<div>
<p><span style="font-size:14px;">重复了<span style="font-family:'Times New Roman';">15</span>行</span></p>
</div>
<p><span style="font-size:14px;">然后上传到<span style="font-family:'Times New Roman';">hdfs</span>中的<span style="font-family:'Times New Roman';">test</span>文件夹下</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs –put wordcount.txt /test/</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs –ls /test</span></p>
<div>
<p><span style="font-size:14px;">可以看到已经上传成功了。</span></p>
</div>
<p><span style="font-family:'Times New Roman';font-size:14px;">xiaofeng@xiaofeng-PC /opt/hadoop</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">$ cd /opt/hadoop//</span>进入<span style="font-family:'Times New Roman';">hadoop</span>目录</p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">xiaofeng@xiaofeng-PC /opt/hadoop</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">$ ls//</span>查看<span style="font-family:'Times New Roman';">hadoop</span>中的文件</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">CHANGES.txt conf                  hadoop-0.20.2-core.jar     librecordio</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">LICENSE.txt conf-local            hadoop-0.20.2-examples.jar  logs</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">NOTICE.txt  conf-pseudo            hadoop-0.20.2-test.jar      src</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">README.txt  conf.lnk              hadoop-0.20.2-tools.jar    webapps</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">bin         contrib                ivy</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">build.xml   docs                   ivy.xml</span></p>
<div>
<p><span style="font-family:'Times New Roman';font-size:14px;">c++          hadoop-0.20.2-ant.jar  lib</span></p>
</div>
<p><span style="font-family:'Times New Roman';font-size:14px;">hadoop-0.20.2-examples.jar//</span>这个就是<span style="font-family:'Times New Roman';">hadoop</span>提供的一个<span style="font-family:'Times New Roman';">wordcount</span>例子程序</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">xiaofeng@xiaofeng-PC /opt/hadoop</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">$ hadoop jar hadoop-0.20.2-examples.jar//</span>运行这条命令可以看到</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">An example program must be given as thefirst argument.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Valid program names are:</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> aggregatewordcount: An Aggregate based map/reduce program that countsthe word</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">s in the input files.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> aggregatewordhist: An Aggregate based map/reduce program that computesthe his</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">togram of the words in the input files.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> dbcount: An example job that count the pageview counts from a database.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> grep: A map/reduce program that counts the matches of a regex in theinput.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> join: A job that effects a join over sorted, equally partitioneddatasets</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> multifilewc: A job that counts words from several files.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> pentomino: A map/reduce tile laying program to find solutions topentomino pro</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">blems.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">  pi:A map/reduce program that estimates Pi using monte-carlo method.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> randomtextwriter: A map/reduce program that writes 10GB of randomtextual data</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> pernode.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> randomwriter: A map/reduce program that writes 10GB of random data pernode.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">  secondarysort:An example defining a secondary sort to the reduce.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> sleep: A job that sleeps at each map and reduce task.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> sort: A map/reduce program that sorts the data written by the randomwriter.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> sudoku: A sudoku solver.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> teragen: Generate data for the terasort</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> terasort: Run the terasort</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> teravalidate: Checking results of terasort</span></p>
<div>
<p><span style="font-family:'Times New Roman';font-size:14px;">//</span>这个程序可以统计文件中的每个单词的出现次数</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">  wordcount: A map/reduce program that countsthe words in the input files.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">xiaofeng@xiaofeng-PC/opt/hadoop</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">$ hadoop jarhadoop-0.20.2-examples.jar wordcount</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Usage:wordcount &lt;in&gt; &lt;out&gt;</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">//</span>这里给出了这个程序的使用方法</p>
</div>
<p><span style="font-family:'Times New Roman';font-size:14px;">xiaofeng@xiaofeng-PC /opt/hadoop</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">$ hadoop jar hadoop-0.20.2-examples.jarwordcount /test/wordcount.txt /test/result</span></p>
<div>
<p><span style="font-family:'Times New Roman';font-size:14px;">//</span>这里是运行<span style="font-family:'Times New Roman';">hadoop-0.20.2-examples.jar</span>这个<span style="font-family:'Times New Roman';">jar</span>文件来统计<span style="font-family:'Times New Roman';">hdfs</span>系统中<span style="font-family:'Times New Roman';">test/</span>下的<span style="font-family:'Times New Roman';">wordcount.txt</span>文件然后把统计结果放到<span style="font-family:'Times New Roman';">/test/result</span>文件中</p>
</div>
<p><span style="font-size:14px;">运行的时候可以在<a href="http://localhost:50030/jobtracker.jsp" rel="nofollow"><span style="font-family:'Times New Roman';color:#0000ff;">http://localhost:50030/jobtracker.jsp</span></a>这里查看进程的运行状态</span></p>
<p><span style="font-size:14px;">RunningJobs</span></p>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td>
<p><span style="font-size:14px;"><strong>Jobid</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Priority</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>User</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Name</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Map  % Complete</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Map  Total</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Maps  Completed</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Reduce  % Complete</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Reduce  Total</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Reduces  Completed</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Job  Scheduling Information</strong></span></p>
</td>
</tr><tr><td id="job_0">
<p><a href="http://localhost:50030/jobdetails.jsp?jobid=job_201304211117_0001&amp;refresh=30" rel="nofollow"><span style="font-size:14px;color:#0000ff;">job_201304211117_0001</span></a></p>
</td>
<td id="priority_0">
<p><span style="font-size:14px;">NORMAL</span></p>
</td>
<td id="user_0">
<p><span style="font-size:14px;">xiaofeng-pc\xiaofeng</span></p>
</td>
<td id="name_0">
<p><span style="font-size:14px;">word  count</span></p>
</td>
<td>
<p><span style="font-size:14px;">0.00%  </span></p>
</td>
<td>
<p><span style="font-size:14px;">1</span></p>
</td>
<td>
<p><span style="font-size:14px;">0</span></p>
</td>
<td>
<p><span style="font-size:14px;">0.00%  </span></p>
</td>
<td>
<p><span style="font-size:14px;">1</span></p>
</td>
<td>
<p><span style="font-size:14px;">0</span></p>
</td>
<td>
<p><span style="font-size:14px;">NA</span></p>
</td>
</tr></tbody></table><h2><span style="font-family:Arial;font-size:24px;">Completed Jobs</span></h2>
<table border="1" cellspacing="0" cellpadding="0"><tbody><tr><td>
<p><span style="font-size:14px;"><strong>Jobid</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Priority</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>User</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Name</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Map  % Complete</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Map  Total</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Maps  Completed</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Reduce  % Complete</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Reduce  Total</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Reduces  Completed</strong></span></p>
</td>
<td>
<p><span style="font-size:14px;"><strong>Job  Scheduling Information</strong></span></p>
</td>
</tr><tr><td>
<p><a href="http://localhost:50030/jobdetails.jsp?jobid=job_201304211117_0001&amp;refresh=0" rel="nofollow"><span style="font-size:14px;color:#0000ff;">job_201304211117_0001</span></a></p>
</td>
<td>
<p><span style="font-size:14px;">NORMAL</span></p>
</td>
<td>
<p><span style="font-size:14px;">xiaofeng-pc\xiaofeng</span></p>
</td>
<td>
<p><span style="font-size:14px;">word  count</span></p>
</td>
<td>
<p><span style="font-size:14px;">100.00%  </span></p>
</td>
<td>
<p><span style="font-size:14px;">1</span></p>
</td>
<td>
<p><span style="font-size:14px;">1</span></p>
</td>
<td>
<p><span style="font-size:14px;">100.00%  </span></p>
</td>
<td>
<p><span style="font-size:14px;">1</span></p>
</td>
<td>
<p><span style="font-size:14px;">1</span></p>
</td>
<td>
<p><span style="font-size:14px;">NA</span></p>
</td>
</tr></tbody></table><div>
<p><span style="font-size:14px;">可以看到运行这个程序的时候先执行了<span style="font-family:'Times New Roman';">Map</span>，然后执行了<span style="font-family:'Times New Roman';">Reduce</span>，等完成了之后就不在RunningJobs中显示了，就会在Completed Jobs中显示100%</span></p>
</div>
<p><span style="font-size:14px;">下面查看统计结果<a href="http://127.0.0.1:50070/" rel="nofollow"><span style="color:#0000ff;">http://127.0.0.1:50070/</span></a>点击进入hdfs系统，然后进入test下的result文件，查看</span></p>
<p><a href="http://127.0.0.1:50075/browseDirectory.jsp?dir=%5Ctest&amp;namenodeInfoPort=50070" rel="nofollow"><span style="font-family:'Times New Roman';font-size:14px;color:#0000ff;">Goto parent directory</span></a></p>
<table border="1" cellpadding="0"><tbody><tr><td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Name</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Type</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Size</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Replication</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Block Size</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Modification Time</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Permission</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Owner</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>Group</strong></span></p>
</td>
</tr><tr><td valign="top">
<p><strong><a href="http://127.0.0.1:50075/browseDirectory.jsp?dir=%2Ftest%2Fresult%2F_logs&amp;namenodeInfoPort=50070" rel="nofollow"><span style="font-family:'Times New Roman';font-size:14px;color:#0000ff;">_logs</span></a></strong></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>dir</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>2013-04-21 11:52</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>rwxr-xr-x</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>xiaofeng-pc\xiaofeng</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>supergroup</strong></span></p>
</td>
</tr><tr><td valign="top">
<p><strong><a href="http://127.0.0.1:50075/browseDirectory.jsp?dir=%2Ftest%2Fresult%2Fpart-r-00000&amp;namenodeInfoPort=50070" rel="nofollow"><span style="font-family:'Times New Roman';font-size:14px;color:#0000ff;">part-r-00000</span></a></strong></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>file</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>0.03 KB</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>3</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>64 MB</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>2013-04-21 11:52</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>rw-r--r--</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>xiaofeng-pc\xiaofeng</strong></span></p>
</td>
<td valign="top">
<p><span style="font-family:'Times New Roman';font-size:14px;"><strong>supergroup</strong></span></p>
</td>
</tr></tbody></table><p><span style="font-size:14px;"> </span></p>
<p><span style="font-size:14px;">这里的<strong><a href="http://127.0.0.1:50075/browseDirectory.jsp?dir=%2Ftest%2Fresult%2Fpart-r-00000&amp;namenodeInfoPort=50070" rel="nofollow"><span style="font-family:'Times New Roman';color:#0000ff;">part-r-00000</span></a>点击这个就可以查看了。</strong></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">credream 65</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">hadoop    65</span></p>
<div>
<p><span style="font-family:'Times New Roman';font-size:14px;">hello       65</span></p>
<p><span style="font-size:14px;">可以看到已经统计出了结果</span></p>
</div>
<p><span style="font-size:14px;">也可以通过命令查看：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">xiaofeng@xiaofeng-PC /opt/hadoop</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">$ hadoop fs -ls /test/result//</span>列出文件</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Found 2 items</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">drwxr-xr-x  - xiaofeng-pc\xiaofeng supergroup          0 2013-04-21 11:52 /test</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">/result/_logs</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">-rw-r--r--  3 xiaofeng-pc\xiaofeng supergroup        31 2013-04-21 11:52 /test</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">/result/part-r-00000</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">xiaofeng@xiaofeng-PC /opt/hadoop</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">$ hadoop fs -cat /test/result/part*//</span>注意这里使用了通配符，也就是开头是<span style="font-family:'Times New Roman';">part</span>的文件</p>
<p><span style="font-family:'Times New Roman';font-size:14px;">credream        65</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">hadoop 65</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">hello  65</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<div>
<p><span style="font-family:'Times New Roman';font-size:14px;">xiaofeng@xiaofeng-PC/opt/hadoop</span></p>
</div>
<p><span style="font-size:14px;">另外在<span style="font-family:'Times New Roman';">D:\hadoop4win\opt\hadoop</span>也可以看到<span style="font-family:'Times New Roman';">hadoop-0.20.2-examples.jar</span>这个例子程序</span></p>
<h3><span style="font-family:'Times New Roman';">2</span>、<span style="font-family:'Times New Roman';">MapReduce</span>范例操作－测试字符统计</h3>
<p><span style="font-size:14px;">先向服务器上传一文件：</span></p>
<p></p>
<p><span style="font-size:14px;">执行统计：</span></p>
<div style="background:rgb(224,224,224);">
<p style="background:rgb(224,224,224);"><span style="font-family:'Times New Roman';font-size:14px;">root@linux:/home/wangjian#hadoop fs -copyFromLocal a.txt /wj/a.txt</span></p>
<p style="background:rgb(224,224,224);"><span style="font-family:'Times New Roman';font-size:14px;">root@linux:/home/wangjian#cd /opt/hadoop-0.20.2/</span></p>
<p style="background:rgb(224,224,224);"><span style="color:#FF0000;"><span style="font-family:'Times New Roman';font-size:14px;">root@linux:/opt/hadoop-0.20.2#hadoop jar hadoop-0.20.2-examples.jar wordcount /wj/a.txt /wj/result2</span></span></p>
</div>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">4</span>、查看结果：</p>
<p><span style="font-size:14px;">默认情况下结果文件名为：<span style="font-family:'Times New Roman';">/wj/result2/part-0000</span></span></p>
<div style="background:rgb(224,224,224);">
<p style="background:rgb(224,224,224);"><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p style="background:rgb(224,224,224);"><span style="font-family:'Times New Roman';font-size:14px;">root@linux:/opt/hadoop-0.20.2#hadoop fs -cat /wj/result2/part*</span></p>
</div>
<p></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"> </span></p>
            </div>
                </div>
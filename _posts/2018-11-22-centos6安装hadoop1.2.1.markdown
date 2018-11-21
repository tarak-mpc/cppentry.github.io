---
layout:     post
title:      centos6安装hadoop1.2.1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>1-&gt;下载hadoop-1.2.1.tar.gz</strong></p>
<p>tar -zxvf <span style="font-size:13.3333339691162px;">hadoop-1.2.1.tar.gz  解压 这里假设解压的文件在 /root/soft</span></p>
<p><strong>2-&gt;创建 hadoop 账户</strong></p>
<p>groupadd hadoop</p>
<p>useradd -g haddop -d /home/hadoop</p>
<p>chown -R hadoop:hadoop /home/hadoop</p>
<p>mv /root/soft/<span style="font-size:13.3333339691162px;">hadoop-1.2.1 /home/hadoop/hadoop-1.2.1</span></p>
<p><strong>3-&gt;hadoop账户免登陆设置</strong></p>
<p><span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.0799999237061px;line-height:15.3599996566772px;">su - hadoop</span></p>
<p><span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.0799999237061px;line-height:15.3599996566772px;">ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa</span><span style="font-family:Verdana, Helvetica, sans-serif;font-size:12.8000001907349px;line-height:15.3599996566772px;"> </span><br style="font-family:Verdana, Helvetica, sans-serif;font-size:12.8000001907349px;line-height:15.3599996566772px;"><span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.0799999237061px;line-height:15.3599996566772px;">cat ~/.ssh/id_dsa.pub &gt;&gt; ~/.ssh/authorized_keys</span><br></p>
<p><span style="font-family:'Courier New', Courier, monospace;"><span style="font-size:14.0799999237061px;line-height:15.3599996566772px;">cd .ssh &amp;&amp; </span></span><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">chmod 710 authorized_keys</span></p>
<p><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><strong>4-&gt;安装jdk </strong></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">下载  jdk-8u40-linux-i586.rpm  </span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">linux下安装  rpm -ivh <span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">jdk-8u40-linux-i586.rpm  </span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">安装完成后 被安装在/usr/java/jdk1.8.0_40</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">设置环境变量 </span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">/etc/profile添加</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">JAVA_HOME=<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">/usr/java/jdk1.8.0_40</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">export PATH=$JAVA_HOME/bin:$PATH</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">进入hadoop根目录/conf/hadoop-env.sh 添加javahome</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">export JAVA_HOME=/usr/java/jdk1.8.0_40<br></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><strong>5-&gt;设置hadoop环境变量</strong></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">/etc/profile添加</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">HADOOP_HOME=/home/hadoop/hadoop-1.2.1<br>
PATH=$HADOOP_HOME/bin:$PATH<br></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">进入hadoop根目录/conf</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">修改三个配置文件</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"></span></span></p>
<p style="line-height:15.3599996566772px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8000001907349px;">
<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.0799999237061px;">conf/core-site.xml</span>:</p>
<pre class="code" style="border-color:rgb(165,182,198);margin-left:0em;font-size:12.8000001907349px;background-color:rgb(241,239,231);">&lt;configuration&gt;
     &lt;property&gt;
         &lt;name&gt;fs.default.name&lt;/name&gt;
         &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
     &lt;/property&gt;
&lt;/configuration&gt;
</pre>
<p style="line-height:15.3599996566772px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8000001907349px;">
<br><span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.0799999237061px;">conf/hdfs-site.xml</span>:</p>
<pre class="code" style="border-color:rgb(165,182,198);margin-left:0em;font-size:12.8000001907349px;background-color:rgb(241,239,231);">&lt;configuration&gt;
     &lt;property&gt;
         &lt;name&gt;dfs.replication&lt;/name&gt;
         &lt;value&gt;1&lt;/value&gt;
     &lt;/property&gt;
&lt;/configuration&gt;
</pre>
<p style="line-height:15.3599996566772px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8000001907349px;">
<br><span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.0799999237061px;">conf/mapred-site.xml</span>:</p>
<pre class="code" style="border-color:rgb(165,182,198);margin-left:0em;font-size:12.8000001907349px;background-color:rgb(241,239,231);">&lt;configuration&gt;
     &lt;property&gt;
         &lt;name&gt;mapred.job.tracker&lt;/name&gt;
         &lt;value&gt;localhost:9001&lt;/value&gt;
     &lt;/property&gt;
&lt;/configuration&gt;</pre>
<br><p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><strong>6-&gt;启动hadoop</strong></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">su - hadoop</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">start-all.sh</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">或者 依次运行start-dfs.sh   start-mapred.sh </span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">启动完成后  查看 hdfs文件系统的目录里的文件 可以理解为是个ftp服务器</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">hadoop fs -lsa /<br></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">这里 运行 hadoop 根目录下的 hadoop-examples-1.2.1.jar 测试程序</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">hadoop jar <span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop-examples-1.2.1.jar 可以看到 这个测试程序 有多个程序</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">[root@localhost hadoop-1.2.1]# hadoop jar hadoop-examples-1.2.1.jar<br></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">An example program must be given as the first argument.<br>
Valid program names are:<br>
  aggregatewordcount: An Aggregate based map/reduce program that counts the words in the input files.<br>
  aggregatewordhist: An Aggregate based map/reduce program that computes the histogram of the words in the input files.<br>
  dbcount: An example job that count the pageview counts from a database.<br>
  grep: A map/reduce program that counts the matches of a regex in the input.<br>
  join: A job that effects a join over sorted, equally partitioned datasets<br>
  multifilewc: A job that counts words from several files.<br>
  pentomino: A map/reduce tile laying program to find solutions to pentomino problems.<br>
  pi: A map/reduce program that estimates Pi using monte-carlo method.<br>
  randomtextwriter: A map/reduce program that writes 10GB of random textual data per node.<br>
  randomwriter: A map/reduce program that writes 10GB of random data per node.<br>
  secondarysort: An example defining a secondary sort to the reduce.<br>
  sleep: A job that sleeps at each map and reduce task.<br>
  sort: A map/reduce program that sorts the data written by the random writer.<br>
  sudoku: A sudoku solver.<br>
  teragen: Generate data for the terasort<br>
  terasort: Run the terasort<br>
  teravalidate: Checking results of terasort<br>
  wordcount: A map/reduce program that counts the words in the input files.<br></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">这里测试个<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">wordcount  这个程序 是通过mapreduce程序 将hdfs某个目录下的文本文件 统计他里面出现的所有单词 以及单词出现的次数</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">首先 在hdfs文件系统上创建一个文件夹 这里的/就是我们定义的</span></span></span></p>
<p style="color:rgb(51,51,51);line-height:15.3599996566772px;font-family:Verdana, Helvetica, sans-serif;font-size:12.8000001907349px;">
<span class="codefrag" style="font-family:'Courier New', Courier, monospace;font-size:14.0799999237061px;">conf/core-site.xml</span>:</p>
<pre class="code" style="margin-left:0em;color:rgb(51,51,51);line-height:26px;border-color:rgb(165,182,198);font-size:12.8000001907349px;background-color:rgb(241,239,231);">&lt;configuration&gt;
     &lt;property&gt;
         &lt;name&gt;fs.default.name&lt;/name&gt;
         &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
     &lt;/property&gt;
&lt;/configuration&gt;</pre>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">当然 一般情况下要写成 <span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hadoop
 fs -mkdir hdfs://localhost:9000</span></span></span></span><span style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;">/test 下面简便写如下</span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">hadoop fs -mkdir /test<br></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"> hadoop fs -mkdir /test/input<br></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">下面也可以将 <span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">/test/input
 改成 </span><span style="font-family:Arial;color:#333333;font-size:13.3333339691162px;"><span style="font-size:14px;line-height:26px;">hdfs://localhost:9000</span></span><span style="font-family:Arial;font-size:14px;color:rgb(51,51,51);line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">/test/input</span></span></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">[hadoop@localhost hadoop-1.2.1]$ hadoop fs -put /home/hadoop/hadoop-1.2.1/conf/*.xml
 /test/input<br>
[hadoop@localhost hadoop-1.2.1]$ hadoop fs -lsr /<br>
drwxr-xr-x   - hadoop supergroup          0 2015-03-31 14:36 /test<br>
drwxr-xr-x   - hadoop supergroup          0 2015-03-31 14:38 /test/input<br>
-rw-r--r--   1 hadoop supergroup       7457 2015-03-31 14:38 /test/input/capacity-scheduler.xml<br>
-rw-r--r--   1 hadoop supergroup        294 2015-03-31 14:38 /test/input/core-site.xml<br>
-rw-r--r--   1 hadoop supergroup        327 2015-03-31 14:38 /test/input/fair-scheduler.xml<br>
-rw-r--r--   1 hadoop supergroup       4644 2015-03-31 14:38 /test/input/hadoop-policy.xml<br>
-rw-r--r--   1 hadoop supergroup        274 2015-03-31 14:38 /test/input/hdfs-site.xml<br>
-rw-r--r--   1 hadoop supergroup       2033 2015-03-31 14:38 /test/input/mapred-queue-acls.xml<br>
-rw-r--r--   1 hadoop supergroup        285 2015-03-31 14:38 /test/input/mapred-site.xml<br>
drwxr-xr-x   - hadoop supergroup          0 2015-03-31 13:21 /tmp<br>
drwxr-xr-x   - hadoop supergroup          0 2015-03-31 13:21 /tmp/hadoop-hadoop<br>
drwxr-xr-x   - hadoop supergroup          0 2015-03-31 13:59 /tmp/hadoop-hadoop/mapred<br>
drwx------   - hadoop supergroup          0 2015-03-31 13:59 /tmp/hadoop-hadoop/mapred/system<br>
-rw-------   1 hadoop supergroup          4 2015-03-31 13:59 /tmp/hadoop-hadoop/mapred/system/jobtracker.info</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">运行程序 </span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">hadoop jar hadoop-examples-1.2.1.jar wordcount /test/input /test/output<br></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">可以通过http://192.168.2.88:50070/ 查看hdfs文件情况</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">这里看 input为7个文件</span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"></span></span></p>
<form action="http://192.168.2.88:50075/browseDirectory.jsp" method="get" name="goto" style="font-family:Simsun;font-size:14px;">
Goto : <input name="dir" type="text" value="/test/input"><input name="go" type="submit" value="go"></form>
<p></p>
<hr style="font-family:Simsun;font-size:14px;"><a href="http://192.168.2.88:50075/browseDirectory.jsp?dir=%2Ftest&amp;namenodeInfoPort=50070" rel="nofollow" style="font-family:Simsun;font-size:14px;">Go to parent directory</a><br style="font-family:Simsun;font-size:14px;"><table border="1" cellpadding="2" cellspacing="2" style="color:rgb(0,0,0);font-family:Simsun;"><tbody><tr><td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Name</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Type</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Size</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Replication</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Block Size</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Modification Time</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Permission</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Owner</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>Group</strong></td>
</tr><tr><td style="vertical-align:top;background-color:rgb(173,216,230);"><strong><a href="http://192.168.2.88:50075/browseDirectory.jsp?dir=%2Ftest%2Finput%2Fcapacity-scheduler.xml&amp;namenodeInfoPort=50070" rel="nofollow">capacity-scheduler.xml</a></strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>file</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>7.28 KB</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>1</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>64 MB</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>2015-03-31 14:38</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>rw-r--r--</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>hadoop</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>supergroup</strong></td>
</tr><tr><td style="vertical-align:top;background-color:rgb(211,211,211);"><strong><a href="http://192.168.2.88:50075/browseDirectory.jsp?dir=%2Ftest%2Finput%2Fcore-site.xml&amp;namenodeInfoPort=50070" rel="nofollow">core-site.xml</a></strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>file</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>0.29 KB</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>1</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>64 MB</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>2015-03-31 14:38</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>rw-r--r--</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>hadoop</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>supergroup</strong></td>
</tr><tr><td style="vertical-align:top;background-color:rgb(173,216,230);"><strong><a href="http://192.168.2.88:50075/browseDirectory.jsp?dir=%2Ftest%2Finput%2Ffair-scheduler.xml&amp;namenodeInfoPort=50070" rel="nofollow">fair-scheduler.xml</a></strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>file</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>0.32 KB</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>1</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>64 MB</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>2015-03-31 14:38</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>rw-r--r--</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>hadoop</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>supergroup</strong></td>
</tr><tr><td style="vertical-align:top;background-color:rgb(211,211,211);"><strong><a href="http://192.168.2.88:50075/browseDirectory.jsp?dir=%2Ftest%2Finput%2Fhadoop-policy.xml&amp;namenodeInfoPort=50070" rel="nofollow">hadoop-policy.xml</a></strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>file</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>4.54 KB</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>1</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>64 MB</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>2015-03-31 14:38</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>rw-r--r--</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>hadoop</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>supergroup</strong></td>
</tr><tr><td style="vertical-align:top;background-color:rgb(173,216,230);"><strong><a href="http://192.168.2.88:50075/browseDirectory.jsp?dir=%2Ftest%2Finput%2Fhdfs-site.xml&amp;namenodeInfoPort=50070" rel="nofollow">hdfs-site.xml</a></strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>file</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>0.27 KB</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>1</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>64 MB</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>2015-03-31 14:38</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>rw-r--r--</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>hadoop</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>supergroup</strong></td>
</tr><tr><td style="vertical-align:top;background-color:rgb(211,211,211);"><strong><a href="http://192.168.2.88:50075/browseDirectory.jsp?dir=%2Ftest%2Finput%2Fmapred-queue-acls.xml&amp;namenodeInfoPort=50070" rel="nofollow">mapred-queue-acls.xml</a></strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>file</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>1.99 KB</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>1</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>64 MB</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>2015-03-31 14:38</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>rw-r--r--</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>hadoop</strong></td>
<td style="vertical-align:top;background-color:rgb(211,211,211);"><strong>supergroup</strong></td>
</tr><tr><td style="vertical-align:top;background-color:rgb(173,216,230);"><strong><a href="http://192.168.2.88:50075/browseDirectory.jsp?dir=%2Ftest%2Finput%2Fmapred-site.xml&amp;namenodeInfoPort=50070" rel="nofollow">mapred-site.xml</a></strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>file</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>0.28 KB</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>1</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>64 MB</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>2015-03-31 14:38</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>rw-r--r--</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>hadoop</strong></td>
<td style="vertical-align:top;background-color:rgb(173,216,230);"><strong>supergroup<br></strong></td>
</tr></tbody></table><br><p></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;">可以通过<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">http://192.168.2.88:50030/  查看mapreduce运行情况</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="font-family:sans-serif;font-size:14px;">User:</span><span style="font-family:sans-serif;font-size:14px;"> hadoop</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Job Name:</span><span style="font-family:sans-serif;font-size:14px;"> word count</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Job File:</span><span style="font-family:sans-serif;font-size:14px;"> </span><a href="http://192.168.2.88:50030/jobconf.jsp?jobid=job_201503311441_0001" rel="nofollow" style="text-decoration:none;font-family:sans-serif;font-size:14px;">hdfs://localhost:9000/tmp/hadoop-hadoop/mapred/staging/hadoop/.staging/job_201503311441_0001/job.xml</a><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Submit Host:</span><span style="font-family:sans-serif;font-size:14px;"> localhost.localdomain</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Submit Host Address:</span><span style="font-family:sans-serif;font-size:14px;"> 127.0.0.1</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Job-ACLs: All users are allowed</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Job Setup:</span><a href="http://192.168.2.88:50030/jobtasks.jsp?jobid=job_201503311441_0001&amp;type=setup&amp;pagenum=1&amp;state=completed" rel="nofollow" style="text-decoration:none;font-family:sans-serif;font-size:14px;"> Successful</a><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Status:</span><span style="font-family:sans-serif;font-size:14px;"> Succeeded</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Started at:</span><span style="font-family:sans-serif;font-size:14px;"> Tue Mar 31 14:45:01 PDT 2015</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Finished at:</span><span style="font-family:sans-serif;font-size:14px;"> Tue Mar 31 14:45:40 PDT 2015</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Finished in:</span><span style="font-family:sans-serif;font-size:14px;"> 38sec</span><br style="font-family:sans-serif;font-size:14px;"><span style="font-family:sans-serif;font-size:14px;">Job Cleanup:</span><a href="http://192.168.2.88:50030/jobtasks.jsp?jobid=job_201503311441_0001&amp;type=cleanup&amp;pagenum=1&amp;state=completed" rel="nofollow" style="text-decoration:none;font-family:sans-serif;font-size:14px;"> Successful</a><br style="font-family:sans-serif;font-size:14px;"></span></span></span></p>
<hr style="font-family:sans-serif;font-size:14px;"><table border="2" cellpadding="5" cellspacing="2" style="color:rgb(0,0,0);font-family:sans-serif;"><tbody><tr><th style="border-bottom-style:none;">Kind</th>
<th style="border-bottom-style:none;">% Complete</th>
<th style="border-bottom-style:none;">Num Tasks</th>
<th style="border-bottom-style:none;">Pending</th>
<th style="border-bottom-style:none;">Running</th>
<th style="border-bottom-style:none;">Complete</th>
<th style="border-bottom-style:none;">Killed</th>
<th style="border-bottom-style:none;"><a href="http://192.168.2.88:50030/jobfailures.jsp?jobid=job_201503311441_0001" rel="nofollow" style="text-decoration:none;">Failed/Killed<br>
Task Attempts</a></th>
</tr><tr><th style="border-bottom-style:none;"><a href="http://192.168.2.88:50030/jobtasks.jsp?jobid=job_201503311441_0001&amp;type=map&amp;pagenum=1" rel="nofollow" style="text-decoration:none;">map</a></th>
<td align="right">100.00%
<table border="1" width="80"><tbody><tr><td class="perc_filled" width="100%" style="background-color:rgb(170,170,255);">
</td>
</tr></tbody></table></td>
<td align="right">7</td>
<td align="right">0</td>
<td align="right">0</td>
<td align="right"><a href="http://192.168.2.88:50030/jobtasks.jsp?jobid=job_201503311441_0001&amp;type=map&amp;pagenum=1&amp;state=completed" rel="nofollow" style="text-decoration:none;">7</a></td>
<td align="right">0</td>
<td align="right">0 / 0</td>
</tr><tr><th style="border-bottom-style:none;"><a href="http://192.168.2.88:50030/jobtasks.jsp?jobid=job_201503311441_0001&amp;type=reduce&amp;pagenum=1" rel="nofollow" style="text-decoration:none;">reduce</a></th>
<td align="right">100.00%
<table border="1" width="80"><tbody><tr><td class="perc_filled" width="100%" style="background-color:rgb(170,170,255);">
</td>
</tr></tbody></table></td>
<td align="right">1</td>
<td align="right">0</td>
<td align="right">0</td>
<td align="right"><a href="http://192.168.2.88:50030/jobtasks.jsp?jobid=job_201503311441_0001&amp;type=reduce&amp;pagenum=1&amp;state=completed" rel="nofollow" style="text-decoration:none;">1</a></td>
<td align="right">0</td>
<td align="right">0 / 0</td>
</tr></tbody></table><br><p></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">这里通过 这个结果 可以理解 什么是map 什么是reduce  这里 input目录下 可以看到有 7个文件 就启动了 7个 task 也就是7个map </span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">就是7个线程单独统计 各自文件中的单词及其单词出现的次数 </span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">最后 再来一次 reduce 这里可以理解为统计  7个线程 统计出来的7个结果 合并到一起 排序后 最后所有相同单词 统计到一起 就是reduce的过程</span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">这里 map 运行的就是  tasktracker 而<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">tasktracker
 处理的文件处于 datanode中 所以一般 运行中<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">tasktracker 和<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">datanode处于同一台机器</span></span></span></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">而
 jobtracker 可以理解为 <span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">tasktracker 的启动者 创建多少个<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">tasktracker 都由<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">jobtracker
 调度</span></span></span></span></span></span></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><br></span></span></span></span></span></span></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">集群安装
 参考</span></span></span></span></span></span></span></span></span></p>
<p><span style="font-family:Arial;color:#333333;"><span style="font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;"><span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">http://www.cnblogs.com/xia520pi/archive/2012/05/16/2503949.html<br></span></span></span></span></span></span></span></span></span></p>
            </div>
                </div>
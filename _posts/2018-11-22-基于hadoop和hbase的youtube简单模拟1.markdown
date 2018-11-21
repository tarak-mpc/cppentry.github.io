---
layout:     post
title:      基于hadoop和hbase的youtube简单模拟1
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">Hadoop的版本:hadoop-0.20.203.0</span></p>
<p><span style="font-size:18px;">Hbase的版本:hbase-0.90.5</span></p>
<p><span style="font-size:18px;">这里Hadoop与Hbase的版本可能不兼容</span></p>
<p><span style="font-size:18px;">将Hadoop lib中的 commons-collections-3.2.1.jar commons-configuration-1.6.jar 拷贝到Hbase的lib中,删除hbase中的hadoop-core-0.20-append-r1056497.jar</span></p>
<p><span style="font-size:18px;">安装jdk:</span></p>
<p><span style="font-size:18px;">环境变量如:</span></p>
<span style="font-size:18px;">#jdk<br>
#export JAVA_HOME=/usr/lib/jvm/java-7-sun<br>
export JAVA_HOME=/usr/lib/jvm/java-6-sun<br>
export JRE_HOME=${JAVA_HOME}/jre<br>
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib<br></span>
<p><span style="font-size:18px;">export PATH=${JAVA_HOME}/bin:$PATH</span></p>
<p><span style="font-size:18px;"><br></span></p>
<span style="font-size:18px;">#Hadoop<br></span>
<p><span style="font-size:18px;">export HADOOP_HOME=/home/wangchen/work/Hadoop</span></p>
<p><span style="font-size:18px;">export PATH=$HADOOP_HOME/bin:$PATH#Hbase</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><span style="font-size:18px;">#Hbase</span><br></span></p>
<span style="font-size:18px;">export HBASE_HOME=/home/wangchen/work/Hbase<br></span>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">大家最好用jdk6,因为后面安装fuse2.9的时候jdk7编译会有问题.</span></p>
<p><br></p>
<p><span style="font-size:18px;">&lt;伪分布式&gt;<br></span></p>
<p><span style="font-size:18px;">hadoop配置文件的设置:</span></p>
<p><span style="font-size:18px;">hadoop-env.sh:</span></p>
<p><span style="font-size:18px;">export JAVA_HOME=/usr/lib/jvm/java-6-sun</span></p>
<p><span style="font-size:18px;">core-site.xml:</span></p>
<p><span style="font-size:18px;">注:master代表本机,hadoop.tmp.dir 是hadoop存放临时文件的地方,需要自己设置,不然电脑重启后启动hadoop出问题.<br></span></p>
<p><span style="font-size:18px;">&lt;configuration&gt;<br>
    &lt;property&gt;<br>
    &lt;name&gt;fs.default.name&lt;/name&gt;<br>
    &lt;value&gt;hdfs://master:9000&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
    &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;<br>
    &lt;value&gt;/home/wangchen/HadoopInstall/Hadoop/tmp&lt;/value&gt;<br>
    &lt;/property&gt;<br>
&lt;/configuration&gt;</span></p>
<p><br></p>
<p><span style="font-size:18px;">hdfs-site.xml:</span></p>
<span style="font-size:18px;">&lt;configuration&gt;<br>
    &lt;property&gt;<br>
    &lt;name&gt;dfs.replication&lt;/name&gt;<br>
    &lt;value&gt;1&lt;/value&gt;<br>
    &lt;/property&gt;<br>
&lt;/configuration&gt;</span>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">mapred-site.xml:</span></p>
<p><span style="font-size:18px;">&lt;configuration&gt;<br>
    &lt;property&gt;<br>
    &lt;name&gt;mapred.job.tracker&lt;/name&gt;<br>
    &lt;value&gt;master:9001&lt;/value&gt;<br>
    &lt;/property&gt;<br>
&lt;/configuration&gt;</span></p>
<p><br></p>
<p><span style="font-size:18px;">Hbase配置文件的设置:</span></p>
<p><span style="font-size:18px;">hbase-env.xml:<br></span></p>
<p><span style="font-size:18px;">export JAVA_HOME=/usr/lib/jvm/java-6-sun<br></span></p>
<p><span style="font-size:18px;">export HBASE_MANAGES_ZK=true</span></p>
<p><br></p>
<p><span style="font-size:18px;">hbase-site.xml</span></p>
<p><span style="font-size:18px;">&lt;configuration&gt;<br>
    &lt;property&gt;<br>
    &lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
    &lt;value&gt;hdfs://master:9000/hbase&lt;/value&gt;<br>
    &lt;/property&gt;<br>
    &lt;property&gt;<br>
    &lt;name&gt;dfs.replication&lt;/name&gt;<br>
    &lt;value&gt;1&lt;/value&gt;<br>
    &lt;/property&gt;<br>
&lt;/configuration&gt;<br></span></p>
<p><span style="font-size:18px;">现在你就可以启动Hadoop和Hbase了,如果不能成功启动,注意:</span></p>
<p><span style="font-size:18px;">1./etc/hosts 是否配置正确,以下是我的配置,这里如果配置的不正确,或者配置多个127.0.0.1可能产生拒绝连接的问题.<br></span></p>
<p><span style="font-size:18px;">127.0.0.1    master wangchen-Rev-1-0 localhost</span></p>
<p><span style="font-size:18px;">2.如果hadoop启动成功,但是hbase启动不成功,查看hbase的log,可能是hadoop安全模式的问题:</span></p>
<p><span style="font-size:18px;">bin/hadoop dfsadmin -safemode leave</span></p>
<p><span style="font-size:18px;">3.查看你的环境变量是否配置正确,hbase需要HADOOP_HOME</span><br></p>
<br>            </div>
                </div>
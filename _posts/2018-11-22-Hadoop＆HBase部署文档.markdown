---
layout:     post
title:      Hadoop＆HBase部署文档
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:#FF0000;">安装是需要特别注意HBase与Hadoop兼容性问题</span></p>
<p>安装jdk</p>
<pre class="wiki">aptitude install openjdk-7-jdk
</pre>
<p>创建hbase用户</p>
<pre class="wiki">for i in AP-HB1 AP-HB2 AP-HB3; do echo =====$i=====; ssh $i “groupadd hbase; useradd -m hbase -g hbase -s /bin/bash -d /home/hbase; passwd hbase“; done
</pre>
<p>设置ssh免密登录</p>
<pre class="wiki">for i in AP-HB1 AP-HB2 AP-HB3;do ssh $i “mkdir /home/hbase/.ssh; ssh-keygen -t rsa -P ” -f /home/hbase/.ssh/id_rsa; chown hbase.hbase /home/hbase/.ssh -R”; done
for i in AP-HB1 AP-HB2 AP-HB3;do ssh $i “cat /home/hbase/.ssh/id_rsa.pub”;done &gt;&gt; /home/hbase/.ssh/authorized_keys
for i in AP-HB2 AP-HB3;do scp /home/hbase/.ssh/authorized_keys $i:/home/hbase/.ssh/;done
</pre>
<p>创建HDFS存储目录</p>
<pre class="wiki">for i in AP-HB1 AP-HB2 AP-HB3; do ssh $i “mkdir -p /vobiledata/HadoopData/; chown -R hbase:hbase/vobiledata/HadoopData”; done
</pre>
<hr><p>安装HDFS<br>
解压hadoop</p>
<pre class="wiki"> tar zxf ~/hadoop-1.2.1-bin.tar.gz
</pre>
<p>配置conf/hadoop-env.sh（指定JAVA_HOME）</p>
<pre class="wiki">export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64/
</pre>
<p>配置conf/core-site.xml<br></p>
<pre class="wiki">&lt;property&gt;
	&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
	&lt;value&gt;/vobiledata/HadoopData/tmp&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
	&lt;name&gt;fs.default.name&lt;/name&gt;
	&lt;value&gt;hdfs://AP-HB1:9000  &lt;/value&gt;
&lt;/property&gt;
</pre>
<p>配置conf/hdfs-site.xml</p>
<pre class="wiki">&lt;property&gt;
        &lt;name&gt;dfs.name.dir&lt;/name&gt;
        &lt;value&gt;/vobiledata/HadoopData/dfs/name/,/vobiledata2/HadoopData/dfs/name/&lt;/value&gt; #文件系统索引信息，存入不同磁盘
&lt;/property&gt;
&lt;property&gt;
        &lt;name&gt;dfs.data.dir&lt;/name&gt;
        &lt;value&gt;/vobiledata/HadoopData/dfs/data/&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
        &lt;name&gt;dfs.replication&lt;/name&gt;
        &lt;value&gt;3&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
        &lt;name&gt;dfs.permissions&lt;/name&gt;
        &lt;value&gt;false&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
	&lt;name&gt;dfs.http.address&lt;/name&gt;  #如果secondarynamenode与namenode分别部署在不同机器，必须添加这两个参数，否则会报告连接失败
	&lt;value&gt;AP-HB1:50070&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
	&lt;name&gt;dfs.secondary.http.address&lt;/name&gt;
	&lt;value&gt;AP-HB2:50090&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
	&lt;name&gt;fs.checkpoint.dir&lt;/name&gt;
	&lt;value&gt;/vobiledata/hadoopdata1/dfs/namesecondary,/vobiledata/hadoopdata3/dfs/namesecondary&lt;/value&gt;  #secondnamenode检查点存放目录，可以设置多个提高可用性
&lt;/property&gt;
</pre>
<p>配置conf/masters（指定SecondaryNameNode机器）</p>
<pre class="wiki">AP-HB2
</pre>
<p>配置conf/slaves（指定DataNode机器）</p>
<pre class="wiki">AP-HB1
AP-HB2
AP-HB3
</pre>
<p>安装HBase<br>
解压HBase</p>
<pre class="wiki"> tar zxf ~/hbase-0.98.12-hadoop1-bin.tar.gz
</pre>
<p>配置conf/hbase-env.sh（指定JAVA_HOME）</p>
<pre class="wiki">export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64/
</pre>
<p>配置conf/hbase-site.xml</p>
<pre class="wiki">&lt;property&gt;
        &lt;name&gt;hbase.rootdir&lt;/name&gt;
	&lt;value&gt;hdfs://AP-HB1:9000/hbase&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
        &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;
        &lt;value&gt;true&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
        &lt;name&gt;hbase.master&lt;/name&gt;
        &lt;value&gt;AP-HB1:60000&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
        &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;
        &lt;value&gt;/vobiledata/HadoopData/zookeeper&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
        &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;
        &lt;value&gt;AP-HB1,AP-HB2,AP-HB3&lt;/value&gt;
&lt;/property&gt;

</pre>
<p>配置conf/regionservers<br></p>
<pre class="wiki">AP-HB1
AP-HB2
AP-HB3
</pre>
<p>复制程序到所有机器</p>
<pre class="wiki">cd /opt; chown hbase.hbase hadoop-1.2.1 hbase-0.98.12-hadoop1 -R
for i in AP-HB2 AP-HB3;do rsync -av /opt/hadoop-1.2.1 /opt/hbase-0.98.12-hadoop1 $i:/opt/; done
</pre>
<hr><p>格式化namenode</p>
<pre class="wiki">bin/hadoop namenode -format
</pre>
<hr><p>开启HDFS服务</p>
<pre class="wiki">cd /opt/hadoop-1.2.1; bin/start-dfs.sh（关闭bin/stop-dfs.sh）
</pre>
<p>开启HBase服务</p>
<pre class="wiki">cd /opt/hbase-0.98.12-hadoop1; bin/start-hbase.sh（关闭bin/stop-hbase.sh）
</pre>
<hr><p>Troubleshooting</p>
<p>2015-05-19 16:48:34,815 ERROR org.apache.hadoop.metrics2.impl.MetricsSystemImpl: Error getting localhost name. Using ‘loca<br>
lhost’…<br>
hadoop要使用hostname，需要修改hostname与配置文件一致，修改hostname</p>
<pre class="wiki">hostname $hostname
cat $hostname &gt; /etc/hostname
</pre>
<p>2015-05-19 17:40:00,771 FATAL [AP-HB1:16020.activeMasterManager] master.HMaster: Unhandled exception. Starting shutdown.<br>
java.io.IOException: Failed on local exception: java.io.IOException: Broken pipe; Host Details : localhost is: “AP-HB1/10.162.50.249″; destination host is: “AP-HB1″:9000;
<br>
注意HBase与Hadoop版本兼容性问题</p>
<p>*重要，dfs.name.dir 中数据非常重要，写入多个目录进行备份，最好可以备份到NFS。</p>
            </div>
                </div>
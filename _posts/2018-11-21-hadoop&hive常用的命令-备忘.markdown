---
layout:     post
title:      hadoop&hive常用的命令-备忘
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://10.200.200.156:50070/dfshealth.jsp" rel="nofollow">http://10.200.200.156:50070/dfshealth.jsp</a>   --dfs容量状态<br><a href="http://10.200.200.156:50030/jobtracker.jsp" rel="nofollow">http://10.200.200.156:50030/jobtracker.jsp</a>    --hadoop任务状态<br><a href="http://10.200.200.156:50060/logLevel" rel="nofollow">http://10.200.200.156:50060/logLevel</a>  ---hadoop 日志级别</p>
<p>bin/hadoop fs -cat /<br>
hadoop fs -mkdir /user/xttest<br>
bin/hadoop fs -put /home/searchdata <br>
bin/hadoop fs -ls /user/</p>
<p><br>
mapred.job.map.capacity<br>
mapred.map.tasks</p>
<p>describe regtest;<br>
show partitions wdlog_tmpmap_p_dt;<br>
//行转列<br>
select ads, ip from iploction LATERAL VIEW explode(split(ips,',')) myview as ip;<br>
//导出数据到本地文件<br>
INSERT OVERWRITE LOCAL DIRECTORY '/home/spvdata' select * from wdlog_ready_spv;<br>
--查看hive为某个查询使用多少个MapReduce作业<br>
Explain SELECT sales.*, things.* FROM sales JOIN things ON (sales.id = things.id);<br>
停止某个hive查询<br>
hadoop job -list<br>
hadoop job -kill job_201310162019_0039<br>
hadoop namenode -format<br>
order by 保证整个输出是有序的</p>
<p>sort by 只保证在一个reducer里面是有序的，有可能最终的结果是半序的<br>
Distributing By 所有的数据集，保证相同的key被分配到同一个reducer上，但是他们不保证被聚集在临近的位置</p>
<p>Cluster By Distributing By的基础上保证单个reducer上的数据是有序的<br>
hive -e "SELECT * FROM mwdlog.regex_init" &gt;&gt; /home/hadoop/hadoop/hive-0.10.0/regdata.txt 导出数据</p>
<p>/etc/init.d/iptables stop   关闭防火墙<br>
ln  软连接<br>
shutdown – h now  关机<br>
tar zxvf      --解压缩<br>
unzip wdlog0614.zip ---zip解压缩<br>
unrar e all.rar  <br>
netstat -anp|grep 9002  --查看端口信息<br>
service mysqld  status   --服务状态<br>
ps -ef |grep mysqld   --进程号<br>
mv  hive-log4j.properties hive-log4j.properties.template --重名文件<br>
date   查看系统时间<br>
mkdir auxlib --创建文件夹<br>
---rar软件安装<br>
wget <a href="http://www.rarlab.com/rar/rarlinux-x64-5.0.0.tar.gz" rel="nofollow">
http://www.rarlab.com/rar/rarlinux-x64-5.0.0.tar.gz</a><br>
tar -zxvf rarlinux-x64-5.0.0.tar.gz<br>
cd rar<br>
make install</p>
<p> </p>
            </div>
                </div>
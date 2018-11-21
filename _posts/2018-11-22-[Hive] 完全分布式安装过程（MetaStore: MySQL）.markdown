---
layout:     post
title:      [Hive] 完全分布式安装过程（MetaStore: MySQL）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <div style="font-family:'微软雅黑';"><span style="font-family:'微软雅黑', '宋体';line-height:18px;">Hadoop版本：0.20.2</span></div>
<div style="font-family:'微软雅黑';"><span style="font-family:'微软雅黑', '宋体';line-height:18px;">Hive版本：0.9.0</span></div>
<div style="font-family:'微软雅黑';">
<span style="font-family:'微软雅黑', '宋体';line-height:18px;">mysql版本:</span><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">5.6.11</span></span>
</div>
<div style="font-family:'微软雅黑';"><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"><br></span></span></div>
<div style="font-family:'微软雅黑';"><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">1) 在mysql里创建hive用户，并赋予其足够权限</span></span></div>
<div style="font-family:'微软雅黑';">
<span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"></span></span>
<div>[root@node01 mysql]# mysql -u root -p</div>
<div>Enter password:</div>
<div><br></div>
</div>
<div style="font-family:'微软雅黑';">
<span style="font-family:'微软雅黑', '宋体';"></span>
<div><span style="line-height:18px;">mysql&gt; create user 'hive' identified by 'hive';</span></div>
<div><span style="line-height:18px;">Query OK, 0 rows affected (0.00 sec)</span></div>
<div><span style="line-height:18px;"><br></span></div>
<div><span style="line-height:18px;">mysql&gt; grant all privileges on *.* to 'hive' with grant option;</span></div>
<div><span style="line-height:18px;">Query OK, 0 rows affected (0.00 sec)</span></div>
<div><span style="line-height:18px;"><br></span></div>
<div><span style="line-height:18px;">mysql&gt; flush privileges;</span></div>
<div><span style="line-height:18px;">Query OK, 0 rows affected (0.01 sec)</span></div>
<div style="line-height:18px;"><br></div>
</div>
<div style="font-family:'微软雅黑';"><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">2）测试hive用户是否能正常连接mysql，并创建hive数据库</span></span></div>
<div style="font-family:'微软雅黑';">
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">[root@node01 mysql]# mysql -u hive -p</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">Enter password:</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">mysql&gt; create database hive;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">Query OK, 1 row affected (0.00 sec)</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"><br></span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">mysql&gt; use hive;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">Database changed</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">mysql&gt; show tables;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">Empty set (0.00 sec)</span></span></div>
</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">3）解压缩hive安装包</div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">tar -xzvf hive-0.9.0.tar.gz</span></span></div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 ~]$ cd hive-0.9.0</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 hive-0.9.0]$ ls</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">bin conf docs examples lib LICENSE NOTICE README.txt RELEASE_NOTES.txt scripts src</span></span></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
</div>
</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">4）下载mysql连接java的驱动 并拷入hive home的lib下</div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 ~]$ mv mysql-connector-java-5.1.24-bin.jar ./hive-0.9.0/lib</span></span></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">5）修改环境变量，把Hive加到PATH</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">/etc/profile</div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">export HIVE_HOME=/home/hadoop/hive-0.9.0</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">export PATH=$PATH:$HIVE_HOME/bin</span></span></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">6）修改hive-env.sh</div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 conf]$ cp hive-env.sh.template hive-env.sh</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 conf]$ vi hive-env.sh</span></span></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">7）拷贝hive-default.xml 并命名为 hive-site.xml</div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">修改四个关键配置 为上面mysql的配置</span></span></div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 conf]$ cp hive-default.xml.template hive-site.xml</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 conf]$ vi hive-site.xml</span></span></div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">&lt;property&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;name&gt;javax.jdo.option.ConnectionURL&lt;/name&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;value&gt;jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&lt;/value&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;description&gt;JDBC connect string for a JDBC metastore&lt;/description&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">&lt;/property&gt;</span></span></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
</div>
</div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">&lt;property&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;name&gt;javax.jdo.option.ConnectionDriverName&lt;/name&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;value&gt;com.mysql.jdbc.Driver&lt;/value&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;description&gt;Driver class name for a JDBC metastore&lt;/description&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">&lt;/property&gt;</span></span></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
</div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">&lt;property&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;name&gt;javax.jdo.option.ConnectionUserName&lt;/name&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;value&gt;hive&lt;/value&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;description&gt;username to use against metastore database&lt;/description&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">&lt;/property&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"><br></span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">&lt;property&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;name&gt;javax.jdo.option.ConnectionPassword&lt;/name&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;value&gt;hive&lt;/value&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"> &lt;description&gt;password to use against metastore database&lt;/description&gt;</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">&lt;/property&gt;</span></span></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
</div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">8）启动Hadoop，打开hive shell 测试</div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 conf]$ start-all.sh</span></span></div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;"><br></div>
</div>
<div>
<div>
<span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;"></span></span>
<div><strong>hive&gt; load data inpath 'hdfs://node01:9000/user/hadoop/access_log.txt'</strong></div>
<div><strong>  &gt; overwrite into table records;</strong></div>
<div>Loading data to table default.records</div>
<div>Moved to trash: hdfs://node01:9000/user/hive/warehouse/records</div>
<div>OK</div>
<div>Time taken: 0.526 seconds</div>
<div><strong>hive&gt; select ip, count(*) from records</strong></div>
<div><strong>  &gt; group by ip;</strong></div>
<div>
<div>Total MapReduce jobs = 1</div>
<div>Launching Job 1 out of 1</div>
<div>Number of reduce tasks not specified. Estimated from input data size: 1</div>
<div>In order to change the average load for a reducer (in bytes):</div>
<div> set hive.exec.reducers.bytes.per.reducer=&lt;number&gt;</div>
<div>In order to limit the maximum number of reducers:</div>
<div> set hive.exec.reducers.max=&lt;number&gt;</div>
<div>In order to set a constant number of reducers:</div>
<div> set mapred.reduce.tasks=&lt;number&gt;</div>
<div>Starting Job = job_201304242001_0001, Tracking URL = http://node01:50030/jobdetails.jsp?jobid=job_201304242001_0001</div>
<div>Kill Command = /home/hadoop/hadoop-0.20.2/bin/../bin/hadoop job -Dmapred.job.tracker=192.168.231.131:9001 -kill job_201304242001_0001</div>
<div>Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1</div>
<div>2013-04-24 20:11:03,127 Stage-1 map = 0%, reduce = 0%</div>
<div>2013-04-24 20:11:11,196 Stage-1 map = 100%, reduce = 0%</div>
<div>2013-04-24 20:11:23,331 Stage-1 map = 100%, reduce = 100%</div>
<div>Ended Job = job_201304242001_0001</div>
<div>MapReduce Jobs Launched:</div>
<div>Job 0: Map: 1 Reduce: 1  HDFS Read: 7118627 HDFS Write: 9 SUCCESS</div>
<div>Total MapReduce CPU Time Spent: 0 msec</div>
<div>OK</div>
<div>NULL  28134</div>
<div>Time taken: 33.273 seconds</div>
</div>
<div><br></div>
</div>
<div>
<div style="font-family:'微软雅黑', '宋体';line-height:18px;">records在HDFS中就是一个文件：</div>
</div>
</div>
<div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">[hadoop@node01 home]$ hadoop fs -ls /user/hive/warehouse/records</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">Found 1 items</span></span></div>
<div><span style="font-family:'微软雅黑', '宋体';"><span style="line-height:18px;">-rw-r--r--  2 hadoop supergroup  7118627 2013-04-15 20:06 /user/hive/warehouse/records/access_log.txt</span></span></div>
</div>
</div>            </div>
                </div>
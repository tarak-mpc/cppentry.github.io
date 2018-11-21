---
layout:     post
title:      hadoop单机部署伪分布式系列3:YARN
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>﻿﻿</div>
<div><span style="background-color:inherit;">安装服务</span></div>
<div>单机版Yarn：ResourceManager、NodeManager</div>
<div><span style="background-color:inherit;"><br style="background-color:inherit;"></span></div>
<div><span style="background-color:inherit;">环境准备：</span></div>
<div>节点：10.1.253.178(hostname:cdh1)</div>
<div>节点已经安装完成HDFS服务，参见：<a href="http://blog.csdn.net/liuluxyz/article/details/31393535" rel="nofollow">hadoop单机部署伪分布式系列1:HDFS</a></div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;"><span style="background-color:inherit;">安装包：</span></div>
<div style="background-color:inherit;">hadoop-2.0.0-cdh4.2.1.tar.gz（使用已经安装完成后的HDFS对应的包，Yarn与HDFS集成在一个包内）</div>
</div>
<div><br style="background-color:inherit;"></div>
<div>
<div style="background-color:inherit;">
<div style="background-color:inherit;"><span style="background-color:inherit;">部署步骤：</span></div>
<div style="background-color:inherit;"><span style="background-color:inherit;">1. 修改以下文件</span></div>
<div style="background-color:inherit;"><span style="background-color:inherit;">yarn文件1：/home/liulu/app/hadoop-2.0.0-cdh4.2.1/etc/hadoop/mapred-site.xml</span></div>
<div style="background-color:inherit;">mv ~/app/hadoop-2.0.0-cdh4.2.1/etc/hadoop/mapred-site.xml.templat ~/app/hadoop-2.0.0-cdh4.2.1/etc/hadoop/mapred-site.xml<br style="background-color:inherit;">
&lt;property&gt;<br style="background-color:inherit;">
    &lt;name&gt;mapreduce.framework.name&lt;/name&gt;<br style="background-color:inherit;">
    &lt;value&gt;yarn&lt;/value&gt;<br style="background-color:inherit;">
&lt;/property&gt;<br style="background-color:inherit;"></div>
<div style="background-color:inherit;"><span style="background-color:inherit;">yarn文件2：/home/liulu/app/hadoop-2.0.0-cdh4.2.1/etc/hadoop/yarn-site.xml：</span><br style="background-color:inherit;">
&lt;property&gt;<br style="background-color:inherit;">
    &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;<br style="background-color:inherit;">
    &lt;value&gt;mapreduce.shuffle&lt;/value&gt;<br style="background-color:inherit;">
&lt;/property&gt;<br style="background-color:inherit;">
&lt;property&gt;<br style="background-color:inherit;">
    &lt;name&gt;yarn.nodemanager.aux-services.mapreduce.shuffle.class&lt;/name&gt;<br style="background-color:inherit;">
    &lt;value&gt;org.apache.hadoop.mapred.ShuffleHandler&lt;/value&gt;<br style="background-color:inherit;">
&lt;/property&gt;</div>
<div style="background-color:inherit;"><span style="background-color:inherit;">yarn文件3：/home/liulu/app/hadoop-2.0.0-cdh4.2.1/etc/hadoop/conf/slaves:</span><br style="background-color:inherit;">
localhost</div>
</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">
<div style="background-color:inherit;">
<p style="background-color:inherit;"><span style="background-color:inherit;">2. 启动yarn集群</span></p>
<div style="background-color:inherit;">
<div style="background-color:inherit;">cd /home/liulu/app/hadoop-2.0.0-cdh4.2.1/sbin</div>
</div>
<div style="background-color:inherit;">
<div style="background-color:inherit;">./start-yarn.sh</div>
</div>
</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">
<div style="background-color:inherit;"><span style="background-color:inherit;">3. 检查yarn集群</span></div>
<div style="background-color:inherit;">看进程（ResourceManager、NodeManager）：</div>
<div style="background-color:inherit;">[liulu@cdh1 sbin]$ jps -m</div>
<div style="background-color:inherit;">25751 DataNode</div>
<div style="background-color:inherit;">25610 NameNode</div>
<div style="background-color:inherit;">9304 ResourceManager</div>
<div style="background-color:inherit;">9423 NodeManager</div>
<div style="background-color:inherit;">25936 SecondaryNameNode</div>
<div style="background-color:inherit;">看yarn监控页面：</div>
<div style="background-color:inherit;">http://10.1.253.178:8088/cluster</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
<div style="background-color:inherit;">
<p style="background-color:inherit;"><span style="background-color:inherit;">4. yarn操作</span></p>
<div style="background-color:inherit;">
<p style="background-color:inherit;">执行wordcount例子</p>
<p style="background-color:inherit;">cd /home/liulu/app/hadoop-2.0.0-cdh4.2.1/bin</p>
<p style="background-color:inherit;">./hdfs dfs -mkdir /in</p>
<p style="background-color:inherit;">./hdfs dfs -put ~/testfile /in/（准备源数据）</p>
<p style="background-color:inherit;"> ./yarn jar ../share/hadoop/mapreduce/hadoop-mapreduce-examples-2.0.0-cdh4.2.1.jar wordcount /in /out（执行wordcount）</p>
<p style="background-color:inherit;">./hdfs dfs -ls /out（查看输出结果）</p>
</div>
</div>
<div style="background-color:inherit;">
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
</div>
</div>
<div style="background-color:inherit;">
<p style="background-color:inherit;"><span style="background-color:inherit;">5. 关闭yarn</span></p>
<div style="background-color:inherit;">
<p style="background-color:inherit;">cd /home/liulu/app/hadoop-2.0.0-cdh4.2.1/sbin</p>
<div style="background-color:inherit;">
<div style="background-color:inherit;">./stop-yarn.sh</div>
</div>
</div>
</div>
<div style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="background-color:inherit;">参考文档：</div>
<div style="background-color:inherit;"><a href="http://dongxicheng.org/mapreduce-nextgen/hadoop-yarn-install/" rel="nofollow" style="background-color:inherit;">http://dongxicheng.org/mapreduce-nextgen/hadoop-yarn-install/</a></div>
</div>
            </div>
                </div>
---
layout:     post
title:      hadoop快速自我学习--hadoop平台管理与维护
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>1，HDFS常用命令</p>

<p>hdfs dfs -mkdir /data</p>

<p>hdfs dfs -put  localfile  hdfspath</p>

<p>-rm  </p>

<p>-du -h</p>

<p>-chmod</p>

<p>-chown  user:group  hdfspath</p>

<p>-cat   </p>

<p>-zcat</p>

<p>-version</p>

<p>hdfs dfsadmin -report  集群运行状态</p>

<p>-help   获取更多命令</p>

<p> </p>

<p>2,作业命令</p>

<p>mapred job -list   列出所有正在运行的作业</p>

<p>mapred job -kill job_xx  停止某个指定的job</p>

<p> </p>

<p>3，资源命令</p>

<p>yarn application -list   列出所有在YARN中正在运行的任务</p>

<p>yarn application -kill application_id   停止指定的application任务</p>

<p> </p>

<p>4，计算空间命令</p>

<p>hdfs fsck  /file  -files -blocks -locations -racks  查看文件健康状况</p>

<p>hdfs dfs -du -h /hdfsfile/  打印目录信息</p>

<p>hdfs dfs -count -q /hdfspath 打印目录信息和qutoa(文件配额)占用情况</p>

<p>hdfs dfs -count -g /hdfspath 打印目录信息和qutoa(文件配额)占用情况</p>

<p> </p>

<p>5，namenode</p>

<p>查看NN状态</p>

<p>hdfs haadmin -getServiceState nna（节点名）</p>

<p>强制将nns节点有NameNode Standby切换成Active</p>

<p>hdfs haadmin -transitionToActive/transitionToStandby -forcemanual nna</p>

<p>强制将nna节点有NameNode Active切换成Standby</p>

<p>hdfs haadmin -failover --forcefence --forceactive nna nns</p>

<p><span style="color:#ffbb66;">以上切换最简单的办法是直接kill某个节点上的NameNode进程</span></p>

<p> </p>

<p>6，安全模式</p>

<p>进入安全模式</p>

<p>hdfs dfsadmin -safemode enter</p>

<p>关闭安全模式</p>

<p>hdfs dfsadmin -safemode leave</p>

<p>查看安全模式</p>

<p>hdfs dfsadmin -safemode get</p>

<p> </p>

<p>7,节点管理</p>

<p>(1), 移除DN</p>

<p>hdfs-site.xml文件配置移除属性</p>

<p>&lt;property&gt;<br>
        &lt;name&gt;dfs.hosts.exclude&lt;/name&gt;<br>
        &lt;value&gt;/home/hadoop/aaa/includes&lt;/value&gt;<br>
    &lt;/property&gt;</p>

<p>[$]vi /home/hadoop/aaa/includes   填入要移除的节点名，如dn3， 保存退出</p>

<p>强制重新加载  hdfs dfsadmin - refreshNodes</p>

<p>dn3节点状态为Decommission in progress 表示块正在移动  Decommissioned 移动完毕，节点可以物理下架</p>

<p>(2),增加DN</p>

<p>hdfs-site.xml文件配置增加属性</p>

<p>&lt;property&gt;<br>
        &lt;name&gt;dfs.hosts&lt;/name&gt;<br>
        &lt;value&gt;/home/hadoop/bbb/includes&lt;/value&gt;<br>
    &lt;/property&gt;</p>

<p>[$]vi /home/hadoop/bbb/includes   填入要移除的节点名，如dn3， 保存退出</p>

<p>启动DataNode进程  hadoop-daemon.sh start datanode</p>

<p>启动NodeManager进程  yarn-daemon.sh start nodemanager</p>

<p>强制重新加载  hdfs dfsadmin - refreshNodes</p>

<p> </p>

<p>8, 快照</p>

<p>快照可以用来备份hdfs上的目录或者整个文件系统</p>

<p>新建快照</p>

<p>1）开启快照权限   hdfs dfsadmin -allowSnapshot  /tmp/snapshot</p>

<p>2) 指定目录中创建快照   hdfs dfs -createSnapshot /tmp/snapshot backup</p>

<p>查看快照</p>

<p>打印出快照在HDFS上的路径  hdfs  lsSnapshottablesDir  </p>

<p>根据上条命令打印出来的路径查看  hdfs -dfs -ls  /xxxxx/xxxxx</p>

<p>快照对比</p>

<p>hdfs dfs snapshotDiff /xxxx/xxx  backup1  backup2</p>

<p>删除快照</p>

<p>hdfs dfs -deleteSnapshot /xxxx/xxxx backup1</p>

<p>禁用快照</p>

<p>hdfs dfsadmin -disallowSnapshot  /xxx/xxx</p>

<p> </p>            </div>
                </div>
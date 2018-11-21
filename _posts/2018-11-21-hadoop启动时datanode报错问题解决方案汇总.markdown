---
layout:     post
title:      hadoop启动时datanode报错问题解决方案汇总
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wangwei249/article/details/70808670				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<br><br>
20170426搜集解决方案：<br><br><br>
1.解决方案（关闭安全模式）：<br>
  bin/hadoop dfsadmin -safemode leave<br>
  bin/hadoop dfs -chmod -R 777 /tmp<br>
 <br>
hadoop dfsadmin -report看看datanode是否运行<br>
 <br>
2.hdfs-site.xml 配置name路径 <br>
  &lt;configuration&gt;<br><span></span>&lt;property&gt;<br><span></span>  &lt;name&gt;dfs.name.dir&lt;/name&gt;<br><span></span>  &lt;value&gt;file:/home/hadoop/dfs/name&lt;/value&gt;<br><span></span>&lt;/property&gt;<br><span></span>&lt;property&gt;<br><span></span>  &lt;name&gt;dfs.data.dir&lt;/name&gt;<br><span></span>  &lt;value&gt;file:/home/hadoop/dfs/data&lt;/value&gt;<br><span></span>&lt;/property&gt;<br>
  &lt;/configuration&gt;<br><br><br>
3.hadoop各种xml配置文件中ip地址都用hosts文件中对应的别名设置。<br>
4.删除hosts文件中localhost等配置，自己明确定义每个ip对应的别名。<br><br><br><br><br><br><br>
20170427新搜集解决方案：<br><br><br>
一。保持master节点上namespaceid和子节点上的一致：<br><span></span>0.20.2版本解决方式:<br><span></span>1、查看名称节点上(即在配置文件中配置的hadoop.tmp.dir参数路径)/usr/hadoop0.20.2/hadoop-huanghz/dfs/name/current/文件夹中VERSION文件中的namespaceid；<br><span></span>2、在两个数据节点修改上dfs.data.dir配置的路径下current文件夹中的VERSION文件namespaceid与名称节点保持一致<br><span></span> <br><span></span>2.20版本解决<br><span></span>/data/hadoop/dfs/name/current/VERSION<br><span></span>用name下面的clusterID，修改datanode的/data/hadoop/dfs/data/current/VERSION 里面的clusterID<br><span></span>每次格式化，name下面的VERSION的clusterID会产生一个新的ID，要去修改各个节点的VERSION的clusterID<br><br><br><br><br><br><br>
二。hdfs-site.xml文件的配置问题，置的路径*/dfs/name注意权限的问题，前面需要加file://（不是hdfs？），否则格式化失败<br><span></span>&lt;property&gt;<br><span></span>&lt;name&gt;dfs.name.dir&lt;/name&gt; <br><span></span>&lt;value&gt;file:///home/grid/hadoop-2.5.2/dfs/name&lt;/value&gt;<br><span></span>&lt;/property&gt;<br><span></span>&lt;property&gt;<br><span></span>&lt;name&gt;dfs.data.dir&lt;/name&gt;<br><span></span>&lt;value&gt;file:///home/grid/hadoop-2.5.2/dfs/data&lt;/value&gt;<br><span></span>&lt;/property&gt;<br><br><br>
三。关闭防火墙：***********非常重要*********** <br><span></span># systemctl status firewalld.service  --查看防火墙状态<br><span></span># systemctl stop firewalld.service    --关闭防火墙<br><span></span># systemctl disable firewalld.service --永久关闭防火墙<br>
 <br>
    关闭防火墙：<br><span></span>systemctl status firewalld  <br><span></span>systemctl disable firewalld  <br>
    systemctl stop firewalld<br>
 <br>
    关闭SELinux：<br><span></span>sestatus<br><span></span>vi /etc/sysconfig/selinu<br><span></span>#下面为selinux文件中需要修改的元素  <br>
    SELINUX=disabled  <br>
    <span></span><br><span></span>chkconfig  iptables  off<br>
    /etc/init.d/iptables  stop<span> </span><br><br><br>
 <br>
 http://www.xdowns.com/soft/softdown.asp?softid=121508
            </div>
                </div>
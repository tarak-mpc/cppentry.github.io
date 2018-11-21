---
layout:     post
title:      搭建多个节点的hadoop集群环境（CDH）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="article_content" class="article_content clearfix csdn-tracking-statistics">
                                            
                        <div class="htmledit_views">

<p>
<span>提示：如果还不了解Hadoop的，可以下查看这篇文章<a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51493191" rel="nofollow">Hadoop生态系统</a>，通过这篇文章，我们可以首先大致了解Hadoop及Hadoop的生态系统中的工具的使用场景。</span></p>
<p>
<span>搭建一个分布式的hadoop集群环境，下面是详细步骤，使用cdh5 </span>。<br>
</p>
<span><span><span>一、硬件准备<br>
<br>
</span></span></span><span></span><span>基本配置:</span><span></span>
<table>
<tbody>
<tr>
<td>操作系统</td>
<td>64位</td>
</tr>
<tr>
<td>CPU</td>
<td>(英特尔)Intel(R) I3处理器</td>
</tr>
<tr>
<td>内存</td>
<td>8.00 GB ( 1600 MHz)</td>
</tr>
<tr>
<td>硬盘剩余空间</td>
<td>50G</td>
</tr>
</tbody>
</table>
<br>
<span>流畅配置:</span><span></span>
<table>
<tbody>
<tr>
<td>操作系统</td>
<td>64位</td>
</tr>
<tr>
<td>CPU</td>
<td>(英特尔)Intel(R) I5处理器或以上配置</td>
</tr>
<tr>
<td>内存</td>
<td>16.00 GB ( 1600 MHz)</td>
</tr>
<tr>
<td>硬盘剩余空间</td>
<td>100G</td>
</tr>
</tbody>
</table>
<p>
<br>
</p>
<p>
<span>注意：上面是在单个pc机上搭建集群，所以对内存要求较高。若是在多台pc机上搭建集群环境，则只需要内存足够即可。</span></p>
<p>
<br>
<span></span></p>
<h1 id="二软件环境准备"><a></a>
<a target="_blank"></a><span>二、软件环境准备</span></h1>
<span></span><span></span>
<table border="1" cellpadding="1" cellspacing="1" height="194" width="342">
<tbody>
<tr>
<td><span>虚拟机</span></td>
<td>VMWare</td>
</tr>
<tr>
<td>操作系统</td>
<td>CentOS6.5</td>
</tr>
<tr>
<td valign="top">JDK</td>
<td valign="top"><a target="_blank" href="http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u79-oth-JPR" rel="nofollow">jdk-7u79-linux-x64.tar.gz </a></td>
</tr>
<tr>
<td valign="top">远程连接</td>
<td valign="top">XShell</td>
</tr>
<tr>
<td align="center" valign="top"><br>
<br>
<br>
<br>
<br>
hadoop生态系统</td>
<td valign="top"><a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.4.5.tar.gz" rel="nofollow"><br>
hadoop-2.6.0-cdh5.4.5.tar.gz</a><br>
<br>
<a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/hbase-1.0.0-cdh5.4.4.tar.gz" rel="nofollow">hbase-1.0.0-cdh5.4.4.tar.gz</a><br>
<br>
<a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/hive-1.1.0-cdh5.4.5.tar.gz" rel="nofollow">hive-1.1.0-cdh5.4.5.tar.gz</a><br>
<br>
<a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/flume-ng-1.5.0-cdh5.4.5.tar.gz" rel="nofollow">flume-ng-1.5.0-cdh5.4.5.tar.gz</a><br>
<br>
<a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/sqoop-1.4.5-cdh5.4.5.tar.gz" rel="nofollow">sqoop-1.4.5-cdh5.4.5.tar.gz</a><br>
<br>
<a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/zookeeper-3.4.5-cdh5.4.5.tar.gz" rel="nofollow">zookeeper-3.4.5-cdh5.4.5.tar.gz</a></td>
</tr>
</tbody>
</table>
<p>
<br>
<span><span></span></span></p>
<p>
<span><span>这篇文章是搭建CDH5集群环境，以上软件可以在此<a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/" rel="nofollow">网址下载</a></span></span></p>
<p>
<span><a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/" rel="nofollow"></a></span><br>
<span></span></p>
<h1 id="三主机规划"><a></a>
<a target="_blank"></a>三、主机规划</h1>
<p>
<span></span></p>
<p>
<span><span>     <span><span>由于我们要安装5个节点的集群环境，所以我们分配好ip地址和主机功能</span></span><br>
</span></span></p>
<p>
</p>
<table height="235" width="792">
<tbody>
<tr>
<th> </th>
<th>
<p>
CDHNode1</p>
<p>
/192.168.3.188</p>
</th>
<th>
<p>
CDHNode2</p>
<p>
/192.168.3.189</p>
</th>
<th>
<p>
CDHNode3</p>
<p>
/192.168.3.190</p>
</th>
<th>
<p>
CDHNode4</p>
<p>
/192.168.3.191</p>
</th>
<th>
<p>
CDHNode5</p>
<p>
/192.168.3.192</p>
</th>
</tr>
<tr>
<td>
<p>
namenode</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
否</p>
</td>
<td>
<p>
否</p>
</td>
<td>
<p>
否</p>
</td>
</tr>
<tr>
<td>
<p>
datanode</p>
</td>
<td>
<p>
否</p>
</td>
<td>
<p>
否</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
</tr>
<tr>
<td>
<p>
resourcemanager</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
否</p>
</td>
<td>
<p>
否</p>
</td>
<td>
<p>
否</p>
</td>
</tr>
<tr>
<td>
<p>
journalnode</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
</tr>
<tr>
<td>
<p>
zookeeper</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
是</p>
</td>
<td>
<p>
否<br>
</p>
</td>
<td>
<p>
否<br>
</p>
</td>
</tr>
</tbody>
</table>
<p>
<span><span><span>注意：<span>Journalnode和ZooKeeper保持奇数个，最少不少于 3 个节点。具体原因，以后详叙。</span></span></span></span></p>
<p>
<br>
<span><span><span><span></span></span></span></span></p>
<p>
<span><span><span><span></span></span>        我的主机分配情况是在两台pc的虚拟机上安装centos系统，具体分配情况如下:</span></span></p>
<p>
<span><span></span></span></p>
<table border="1" cellpadding="1" cellspacing="1" height="101" width="528">
<tbody>
<tr>
<td> </td>
<td>CDHNode1</td>
<td>CDHNode2</td>
<td>CDHNode3</td>
<td>CDHNode4</td>
<td>CDHNode4</td>
</tr>
<tr>
<td> PC1</td>
<td>是</td>
<td> </td>
<td>是</td>
<td> </td>
<td> </td>
</tr>
<tr>
<td> PC2</td>
<td> </td>
<td>是</td>
<td> </td>
<td>是</td>
<td>是</td>
</tr>
</tbody>
</table>
<span>这样分配的原因是为了采用HA时，两台namenode在不同pc上，若有一台pc出现异常，导致一个namenode无法运作，而standy namenode(备用namenode)可以active(激活)，而不会影响整个集群的运作。</span><br>
<p>
</p>
<p>
</p>
<p>
<br>
</p>
<h1 id="三详细安装步骤"><a></a>
<a target="_blank"></a><span>三、详细安装步骤</span></h1>
<p>
              <span> 我们首先在1个主机（CHDNode1/192.168.3.188）上安装centos6.5操作系统,使用<span>root用户</span>配置网络，创建hadoop用户，关闭防火墙，安装一些必备软件。为记下来的集群软件安装做准备。</span><br>
</p>
<h2 id="centos65安装"><a></a>
<a target="_blank"></a><span>CentOS6.5安装</span></h2>
<p>
 <span>                    在主机<span>CHDNode1/192.168.3.188</span>，安装CentOS6.5操作系统。详细安装步骤可以查看<a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51444677" rel="nofollow">CentOS安装</a>这篇文章。此处就不再赘叙。</span></p>
<h2 id="网络配置"><a></a>
<a target="_blank"></a><span>网络配置</span></h2>
<p>
 <span>1.打开安装好的CentOS虚拟机CDHNode1</span><br>
</p>
<p>
  <img src="https://img-blog.csdn.net/20160518213301443?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<br>
</p>
<p>
<span> 2、登录CentOS系统</span></p>
<p>
<span>         <img src="https://img-blog.csdn.net/20160518213646927?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span></span><span>3.输入ifconfig命令，先查看ip地址</span></p>
<p>
<img src="https://img-blog.csdn.net/20160518214041478?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><span><br>
</span></p>
<p>
<span><span>4、这个时候我们发现除了回环地址以外，我们并不能和外界通信，比如我们可以使用ping命令进行测试。</span></span></p>
<p>
<span><span><img src="https://img-blog.csdn.net/20160518215515342?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></span></p>
<p>
<span><span><span>注意：ping 127.0.0.1时，结束icmp报文，使用Ctrl+C命令</span><br>
</span></span></p>
<p>
<span><span><span></span></span></span><span>第一次ping 百度,ping不通，说明虚拟机无法连接外网</span></p>
<p>
<span>第二次ping 虚拟机NAT网关，ping不通</span></p>
<p>
 注：虚拟机网关查看方法</p>
<p>
<br>
</p>
<p>
<img src="https://img-blog.csdn.net/20160518215925457?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
点击虚拟机网络编辑器，点击VMnet8</p>
<p>
<img src="https://img-blog.csdn.net/20160518220005361?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
点击Nat设置</p>
<p>
<img src="https://img-blog.csdn.net/20160518220115518?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</p>
<p>
<span>第三次ping物理机ip地址，ping不通</span></p>
<p>
<span>注：查看物理机IP地址，开启cmd.exe ,输入ipconfig</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518221608306?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span><br>
</span></p>
<p>
<span>第四次ping虚拟机的回环地址，ping成功，说明虚拟机的网络协议是正确的</span></p>
<p>
<span>5、修改网卡的配置文件</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518220814568?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518220845937?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>可以看到虚拟机网卡没有开启，因此修改ONBOOT=yes,然后保存退出（按Esc键，然后输入：wq）</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518221222802?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>6、重启网络服务</span></p>
<p>
<img src="https://img-blog.csdn.net/20160518223147281?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</p>
<p>
<br>
</p>
<p>
<span>7、再次输入ifconfig命令，查看ip地址。</span></p>
<p>
<span><span>注意：我的虚拟机设置的是桥接模式，所以ip地址是192.168.2.X网段，或192.168.3.X网段；因为桥接模式是直接使用物理网卡，而我的物理主机的网关是192.168.0.1，子网掩码是255.255.252.0，所以我的虚拟机ip地址可以在192.168.0.2-192.168.3.255之间任意选择(除了物理主机的ip)。若你的虚拟机是使用nat模式，可能就是，如：以我的虚拟机为例，nat网关是192.168.117.2,子网掩码为255.255.255.0，所以虚拟机的ip地址可以在192.168.117.3-192.168.117.255之间任意选择。</span><br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518222448434?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span>此时网卡已经成功开启。</span></p>
<p>
<span>8.再次ping步骤4的ip或域名,查看具体情况</span></p>
<p>
<span>检查本机网络协议</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518223830612?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span>检查网卡链路</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518223854555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span>检查Nat网关</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518223919643?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span>检查外网</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160518223941534?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>此时虚拟机连接互联网成功，但使用dhcp（动态主机配置协议）配置ip地址，此时的IP地址时动态生成的，不方便以后hadoop集群环境的搭建。所以我们还需要配置静态Ip地址，配置详情，下面细说。</span></p>
<p>
<span>9、使用ifconfig命令可以查看动态ip地址为192.168.3.188，所以接下来我们把此ip作为CDHNode1的静态ip地址<span>。注：你可以使用你的动态ip作为你当前主机的静态ip。然后后面几台IP地址可以紧跟着设置成，如<span>192.168.3.189。DHCP生成ip地址是随机的，你可具体问题具体分析。</span></span></span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519152657593?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>10、修改网卡配置信息，把BOOTPROTO=dhcp修改为<span>BOOTPROTO=static,并且添加上设置的ip地址，子网掩码，和网关。</span></span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519152846342?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>注意：由于我是在两台pc上配置集群环境，所以我使用的是桥接模式。若你是在一台主机上建议你使用Nat（网络地址转换）模式。因为nat模式的网关在不同的电脑上虚拟机VMWare虚拟出来的网段是不同的。不方便使用Xshell连接。</span></p>
<p>
<span>下面是<span>桥接模式</span>的配置，IPADDR是设置ip地址，NETMASK(子网掩码)与GATEWAY（网关）可以设置成与物理主机一样的<span>NETMASK(子网掩码)与GATEWAY（网关）</span>。注：物理主机ip配置具体查看，看上面的步骤4。</span><br>
</p>
<p>
<span><img src="https://img-blog.csdn.net/20160519152853048?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>下面是<span>Nat模式</span>的配置，IPADDR是设置ip地址，NETMASK(子网掩码)与GATEWAY（网关）可以设置成与物理主机一样的<span>NETMASK(子网掩码)与GATEWAY（网关）</span>。注：Nat模式ip配置具体查看，看上面的步骤4。</span></p>
<p>
<span>上面步骤中我们可以看到Nat模式的网关是192.168.117.2，子网掩码为255.255.255.0</span></p>
<p>
<span>所以具体可配置成</span></p>
<p>
<span><span><span>BOOTPROTO=static</span></span><br>
</span></p>
<p>
<span>IPADDR=192.168.117.40</span></p>
<p>
<span>NETMASK=255.255.255.0</span></p>
<p>
<span>GATEWAY=192.168.117.2</span></p>
<p>
<span>最后按Esc,然后：wq保存退出。（注意编辑按i或a即可进入编辑模式，具体操作查看vi命令的使用说明）<br>
</span></p>
<p>
<span>11、重启网络服务</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519160304060?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span>至此网络配置完毕。</span></p>
<h2 id="title">
<a target="_blank"></a></h2>
<h3 id="下载必备软件"><a></a>
<a target="_blank"></a><span>下载必备软件</span></h3>
<p>
<span>注：1、在CDHNode1节点上安装，使用yum命令 ,参数-y表示，下载过程中的自动回答yes,有兴趣的话，可以试试不加的情况；install表示从网上下载安装。</span></p>
<p>
<span>       <span>  2、使用yum命令安装软件必须是root用户。</span></span><br>
</p>
<p>
<span>1、安装lrzsz，可以方便在Xshell上，上传和下载文件，输入rz命令，可以上传文件，sz命令可以从远程主机上下载文件到本地。</span><br>
</p>
<p>
 <img src="https://img-blog.csdn.net/20160519151233365?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>2、安装ssh服务器。</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519151713603?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</p>
<p>
<span>3、安装ssh客户端。</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519151722424?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<br>
<span></span><span></span>
<h2 id="用户创建户"><a></a>
<a target="_blank"></a><span>用户创建户</span></h2>
<p>
<span>1、使用useradd命令添加用户hadoop，并同时创建用户的home目录，关于useradd的参数使用可以使用 useradd -h查看参数<br>
</span></p>
<blockquote>
<p>
<span><img src="https://img-blog.csdn.net/20160519145253623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
</blockquote>
<p>
<span>2、可以切换到/home目录下查看，是否创建成功</span></p>
<blockquote>
<p>
<span><img src="https://img-blog.csdn.net/20160519145749141?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span><br>
</p>
</blockquote>
<p>
<span>3、为hadoop用户创建密码，这是为了接下来使用XShell软件远程连接CDHNode1节点做准备，出现successfully表示创建密码成功，<span>注意：密码创建必须是root用户</span>。</span></p>
<blockquote>
<p>
<img src="https://img-blog.csdn.net/20160519145807047?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
</blockquote>
<p>
<span>4、可以切换到hadoop用户，使用 su命令，可以看到，此时root@CDHNode1已经改成hadoop@CDHNode1。</span></p>
<blockquote>
<p>
<img src="https://img-blog.csdn.net/20160519150103486?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
</blockquote>
<p>
<span>5、从hadoop用户退出，使用exit命令</span></p>
<blockquote>
<p>
<span><img src="https://img-blog.csdn.net/20160519150341878?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
</blockquote>
<h2 id="克隆虚拟机"><a></a>
<a target="_blank"></a><span>克隆虚拟机</span></h2>
<p>
由于我们使用VMware创建的Centos虚拟机，所以我们可以直接克隆虚拟机，就减少了安装的时间，提高效率。</p>
<p>
若你是在一台pc机上配置集群环境，就可以按照以下步骤连续克隆出四个虚拟机分别是CDHNode2、CDHNode3、CDHNode4、CDHNode5；我是在两个pc机上配置的所以，我就需要在另一台pc上重新按照第一台pc机上安装CDHNode1一样，再安装CDHNode2，然后从CDHNode2克隆CDHNode4、CDHNode5。</p>
<p>
下面我以在CDHNode2上克隆出CDHNode5虚拟机为例，演示以下克隆的步骤。</p>
<p>
<span>1、右键CDHNode2虚拟机–》快照–》拍摄快照</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519195147052?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="530" width="742"></span></p>
<p>
<span>2、点击拍摄快照，快照拍摄成功</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519195202787?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>3、再右键<span>CDHNode2虚拟机</span>–》管理–》克隆</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519195218325?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>4、下一步</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519195438404?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>5、选择现有快照–》下一步</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519195454888?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>6、选择创建完整克隆–》下一步</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519195508091?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>7、输入虚拟机名称，点击完成，等待克隆完成。</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519195527276?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>8、至此我们完成了克隆虚拟机的任务</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519203241951?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="464" width="728"><br>
</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519203127810?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>9、接下来是修改配置虚拟机的网卡信息，下面我们在CDHNode5为例，其他节点自己按照下面的自行配置。</span></p>
<p>
<span>首先打开CDHNode5,此时显示的主机名称为CDHNode2，因为<span>CDHNode5</span>是从<span>CDHNode2克隆来的，所以主机名称还是<span>CDHNode2。</span></span></span><br>
</p>
<p>
<span><img src="https://img-blog.csdn.net/20160519203552048?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>10、暂时不该主机名，我们先查看一下，此时显示没有网卡</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519203922391?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>11、克隆后的网卡变成了eth1,如果想改回eth0，则需要修改配置文件70-persistent-net.rules配置文件</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519204825252?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519204808988?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span><br>
<span></span></p>
<p>
<span>11、我们先设置行号输入：set number,我们需要修改第8行和第11行，然后输入i或a进入编辑模式，使用#注释第8行，并把第10行的eth1改为eth0，可以记一下第二个网卡的mac硬件地址</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519205151974?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span>12、我们先移除网卡e1000，使用modprobe -r e1000命令<br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519205305148?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>13、重新安装网卡e000</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519205816039?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>14、修改网卡配置信息</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519205920805?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519205834180?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>15、把设备号修改为DEVICE=eth0,先注释掉mac地址（硬件地址），在修改ip地址。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519210000868?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>16、重启网络服务</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519210614714?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>注意：如果不正确，ip已经被使用，可以重新设置成其他的ip地址，按照以上方式配置。</span></p>
<p>
<span><span>17、接下来是修改主机名，把<span>CDHNode2改成<span>CDHNode5</span></span></span><br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519210955389?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519211226158?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<img src="https://img-blog.csdn.net/20160519211242623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>18、重启主机后，就可以看到主机名的变成<span>CDHNode5</span>。</span><br>
</p>
<p>
<img src="https://img-blog.csdn.net/20160519211316050?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<img src="https://img-blog.csdn.net/20160519211506062?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>19、由于我们注释了mac地址，所以我们开改成新的mac地址，首先使用ifconfig查看新的mac地址，记住下面地址，</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519211719220?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>20、进入ifcfg-eth0文件，修改HWaddr，改为刚才查看的mac地址</span></p>
<p>
<span><span><img src="https://img-blog.csdn.net/20160519205920805?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span><br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519211943793?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span>再使用service network restart命令重启网络服务。至此配置完毕，<span><span>最后按Esc,然后：wq保存退出</span></span>。</span></p>
<p>
<span>接下来在其他节点上进行相应的配置。</span></p>
<p>
<span></span><br>
</p>
<h2 id="配置host文件"><a></a>
<a target="_blank"></a>配置host文件</h2>
<p>
<span>在<span>5个节点</span>上分别配置hosts文件，注意使用root用户配置</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519214427919?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<img src="https://img-blog.csdn.net/20160520205606559?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</p>
<p>
<span><span>最后按Esc,然后：wq保存退出</span>。</span><br>
</p>
<p>
<br>
</p>
<h2 id="关闭防火墙"><a></a>
<a target="_blank"></a><span>关闭防火墙</span></h2>
<p>
<span>在所有节点上使用root用户，关闭防火墙。由于要使用ssh协议来进行主机间的无密码访问，所以需要关闭防火墙。</span></p>
<p>
<span>1、首先查看防火墙的状态，显示防火墙正在运行</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519164128623?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span><br>
</p>
<p>
<span>2、然后<span>永久关闭防火墙</span>，使用chkconfig iptables off命令，此时当前虚拟机的防火墙还没有关闭。只有在关机重启后才能生效。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519164414218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>3、关闭ipv6的防火墙</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519192740689?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<br>
</p>
<p>
<span>4、也可以使用service iptables stop命令，<span>暂时关闭当前主机的防火墙</span>。<br>
</span></p>
<p>
<img src="https://img-blog.csdn.net/20160519164611915?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>5、关闭selinux </span></p>
<p>
<img src="https://img-blog.csdn.net/20160519192748189?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<img src="https://img-blog.csdn.net/20160519192753692?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>将其SELINUX=enforcing设置为SELINUX=disabled</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160519192758502?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span><span>最后按Esc,然后：wq保存退出</span>。</span><br>
</p>
<h2 id="时间同步"><a></a>
<a target="_blank"></a>时间同步</h2>
<p>
当我们每一次启动集群时，时间基本上是不同步的，所以需要时间同步。要求所以节点保持一致的时间。</p>
<p>
<span>注意：使用root用户修改，5个节点同时修改</span><br>
</p>
<p>
<span>1、我们先使用date命令查看当前系统时间</span></p>
<p>
<img src="https://img-blog.csdn.net/20160520210129565?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>如果系统时间与当前时间不一致，可以按照如下方式修改</span></p>
<p>
<span>2、查看时区设置是否正确。我们设置的统一时区为<span><span>Asia</span></span>/Shanghai，如果时区设置不正确，可以按照如下步骤把当前时区修改为上海。</span></p>
<p>
<img src="https://img-blog.csdn.net/20160520211506426?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>3、下面我们使用ntp(网络时间协议)同步时间。如果ntp命令不存在，则需要在线安装ntp</span><br>
</p>
<p>
<img src="https://img-blog.csdn.net/20160520211748689?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>4、安装ntp后，我们可以使用ntpdate命令进行联网时间同步。</span></p>
<p>
<img src="https://img-blog.csdn.net/20160520211922002?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<span>5、最后我们在使用date命令查看，时间是否同步成功。</span></p>
<p>
<span>注意：<span><span>在<span>桥接模式</span>下，上述同步时钟的方法行不通。换一下方法，我们使用手动配置时间，在xshell中，全部xshell会话的方式的方式同时更改所有节点。</span></span></span></p>
<p>
<span>a、使用date查看时间</span></p>
<p>
<span>b、设置日期，比如设置成2016年5月20日</span></p>
<p>
<img src="https://img-blog.csdn.net/20160520212210606?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</p>
<p>
<span>c、设置时间，比如设置成下午1点48分45秒</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160520212602677?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
<span>d、最后将当前时间和日期写入BIOS,避免重启后失效</span></p>
<p>
<img src="https://img-blog.csdn.net/20160520212531739?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<h2 id="使用xshell远程连接centos系统"><a></a>
<a target="_blank"></a>使用Xshell远程连接centos系统</h2>
<p>
由于在centos中复制修改等操作方便，我们使用windows上的一款远程连接工具Xshell，下面简单讲一下连接步骤。你需要先从网上下载安装Xshell和Xftp（可以用来可视化的文件传输）这两款工具。</p>
<p>
连接步骤如下，以连接CDHNode1为例。</p>
<p>
<span>1、首先点击新建按钮，如下；在新建会话属性对话框中输入名称和需要连接的主机ip地址。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160520213713730?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>2、接下来点击左侧的用户身份验证，输入要登录主机的用户名和密码，点击确定，此时创建成功。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160520213721308?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>3、在打开会话对话框中选中刚创建的CDHNode1，然后点击连接</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160520213731206?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>4、此时连接成功，即可进行远程操作</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160520213743144?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>5、为了以后方便打开远程主机，我们可以把当前连接的主机添加到链接栏中，只需点击添加到链接栏按钮即可添加</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160520213749800?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>6、为了防止一个命令在多个主机中输入，我们也可以使用撰写栏，快速的把一个命令传送到所以打开的会话框。提示：撰写栏可以在查看菜单栏中打开。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160520213812113?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="586" width="703"></span><br>
</p>
<p>
<br>
</p>
<h2 id="配置免密码登录ssh"><a></a>
<a target="_blank"></a>配置免密码登录ssh</h2>
<p>
接上面的配置，我们已经使用Xshell远程登录上五个节点。下面我们就配置免密码登录hadoop用户，如果你使用root用户登录的，需要<span>先切换到hadoop用户</span>，使用 su hadoop命令切换。步骤如下：</p>
<p>
<span>1、首先切换到hadoop的家目录下，使用cd /home/hadoop命令来切换。然后在hadoop家目录下创建 <span> .ssh<span>目录。</span></span><br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160521215459175?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>2、然后生成hadoop用户的rsa(<span>非对称加密算法</span>)，运行如下命令后，一直回车，即可生成hadoop的公钥和私钥</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160521215516154?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<img src="https://img-blog.csdn.net/20160521220500980?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
<span></span></p>
<p>
<span>3、切换到 .ssh目录下，即可看到已经生成的<span>公钥和私钥。</span><br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160521215521060?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<br>
<span></span></p>
<p>
<span>4、按照上面的步骤，在所有节点上生成公钥和私钥，接下来需要把所有节点的公钥发到CDHNode1节点的授权文件。如下图，我们使用Xshell最下方的撰写栏向所有节点发送ssh-copy-id CDHNode1命令。<br>
</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160521221553059?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="443" width="698"></span></p>
<p>
<span>5、执行结果如下，每个节点包括CDHNode1节点，都把自己刚才生成的公钥 id_rsa.pub文件发送到CDHNode1节点的授权文件authorized_keys中。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160521221601432?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>注意：也可以在所有节点使用</span></p>
<p>
cat ~/.ssh/id_rsa.pub | ssh hadoop@CDHNode1 ‘cat &gt;&gt; ~/.ssh/authorized_keys’ 命令把自己的公钥追加到<span>CDHNode1节点的授权文件authorized_keys中。</span></p>
<p>
<span>6、可以查看<span><span>CDHNode1节点的授权文件authorized_keys</span></span>中是否把所有节点的公钥都添加到此文件中，若有节点没有加入，则可以使用上一步命令重新添加。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160521222455475?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br>
</span></p>
<p>
7、然后我们就把这个文件拷贝到其他节点的.ssh目录下。</p>
<p>
<img src="https://img-blog.csdn.net/20160521222500985?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>
<img src="https://img-blog.csdn.net/20160521222506194?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<span>CDHNode4、CDHNode5按照上述命令自己执行。注意，这个命令是在CDHNode1节点上执行的。</span>
<p>
<span>8、根据下图，可以看到<span>CDHNode5</span>下已经复制了一份<span><span><span><span>authorized_keys</span></span></span></span>文件。下面以<span><span>CDHNode5</span></span>为例，修改.ssh目录以及.ssh目录下的文件的权限。其他节点按照如下步骤
 一 一 修改。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160521223238566?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>9、修改好权限后，至此ssh配置成功，可以使用ssh测试是否配置成功，第一次使用ssh连接其他节点时需要输入yes,退出使用exit命令，在第二次登陆时，就不需要在输入，如下图直接登陆并显示最后登录时间。</span></p>
<p>
<span><img src="https://img-blog.csdn.net/20160521223729553?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></span></p>
<p>
<span>提示：rsa非对称加密算法是把公钥发送个对方，对方使用公钥对数据进行加密后，自己在使用私钥对数据进行解密。</span></p>
<p>
<span><span>免密码登录的原理：</span></span></p>
<ol>
<li><span>需要依靠密钥，也就是自己创建的一对密钥，并把公钥放到需要访问的服务器上。</span></li><li><span>如果你需要连接SSH服务器，客户端软件就会向服务器发出请求，请求用你的密钥进行安全验证。</span></li><li><span>服务器收到请求后，现在该服务器上的主目录下寻找你的公钥，然后把它和你发送过来的公钥进行比较。如果两个密钥一致，服务端就用公钥加密“质询”（challenge）,并把它发送给客户端软件。</span></li><li><span>客户端收到“质询”后，就用你的私钥进行解密。再把它发送个服务器。</span></li><li><span>服务器比较发送来的“质询”和原先的是否一致，如果一致则进行授权，完成建立会话的操作。</span></li></ol>
<h2 id="脚本工具的使用"><a></a>
<a target="_blank"></a>脚本工具的使用</h2>
<p>
此处使用脚本文件的目的是为了简化安装步骤，毕竟有五个节点，如果全部使用命令一个一个操作，太费时费力了。为了简化操作，我们使用脚本文件来帮助我们执行多个重复的命令。就就相当于windows的批处理，把所有的命令集中起来，一个命令完成多个操作。</p>
<p>
<span>下面我们在<span>CDHNode1节点</span>上新建三个文件，deploy.conf（配置文件），deploy.sh（实现文件复制的shell脚本文件），runRemoteCdm.sh(在远程节点上执行命令的shell脚本文件)。</span></p>
<p>
<span>1、我们把三个文件放到/home/hadoop/tools目录下，先创建此目录</span></p>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 ~]$ mkdir /home/hadoop/tools  </span></span></li></ol>

<p></p></div> <br>
<span>2、然后切换到tools目录下</span><span></span>

<p>
</p>

<p>
[hadoop@CDHNode1 ~]$cd tools</p>
<span>3、首先创建deploy.conf文件</span><span></span>
<p>
</p>

<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 tools]$ vi deploy.conf  </span></span></li></ol>
</div>
<p>
</p>

<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>CDHNode1,all,zookeeper,journalnode,namenode,resourcemanager,  </span></span></li><li>
<span>CDHNode2,all,slave,zookeeper,journalnode,namenode,datanode,resourcemanager,  </span></li><li class="alt">
<span>CDHNode3,all,slave,zookeeper,journalnode,datanode,nodemanager,  </span></li><li>
<span>CDHNode4,all,slave,journalnode,datanode,nodemanager,  </span></li><li class="alt">
<span>CDHNode5,all,slave,journalnode,datanode,nodemanager,  </span></li></ol>
</div>
<span>先解释一下这个文件，这个文件就是配置在每个几点上的功能，就是上面所讲的</span><span>主机规划</span><span>。比如zookeeper安装在CDHnode1、CDHnode2、CDHnode3这三个主机上。其他的自己对比查看。</span><br>
<span>4、创建deploy.sh文件<br>
<span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 tools]$ vi deploy.sh  </span></span></li></ol>
</div>
<br>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>#!/bin/bash  </span></span></li><li>
<span>#set -x  </span></li><li class="alt">
<span>  </span></li><li>
<span>#判断参数是否小于3个，因为运行deploy.sh需要有源文件（或源目录）和目标文件（或目标目录），  </span></li><li class="alt">
<span>#以及在MachineTag（哪些主机）上执行，这个标记就是上面deploy.conf中的标记 ，如 zookeeper、all等  </span></li><li>
<span>#使用实例如：我们把app目录下的所有文件复制到远程标记为zookeeper的主机上的/home/hadoop/app目录下  </span></li><li class="alt">
<span># ./deploy.sh  /home/hadoop/app /home/hadoop/app zookeeper  </span></li><li>
<span>#执行完上述命令后，shell脚本文件就自动把CDHNode1下的app目录中的文件复制到三个zookeeper节点的app目录下  </span></li><li class="alt">
<span>if [ $# -lt 3 ]    </span></li><li>
<span>then   </span></li><li class="alt">
<span>  echo “Usage: ./deply.sh srcFile(or Dir) descFile(or Dir) MachineTag”  </span></li><li>
<span>  echo “Usage: ./deply.sh srcFile(or Dir) descFile(or Dir) MachineTag confFile”  </span></li><li class="alt">
<span>  exit   </span></li><li>
<span>fi  </span></li><li class="alt">
<span>#源文件或源目录  </span></li><li>
<span>src=$1  </span></li><li class="alt">
<span>#目标文件或目标目录  </span></li><li>
<span>dest=$2cat   </span></li><li class="alt">
<span>#标记  </span></li><li>
<span>tag=$3  </span></li><li class="alt">
<span>  </span></li><li>
<span>#判断是否使用deploy.conf配置文件，或者自己指定配置文件  </span></li><li class="alt">
<span>  </span></li><li>
<span>if [ ‘a’$4’a’ == ‘aa’ ]  </span></li><li class="alt">
<span>then  </span></li><li>
<span>  confFile=/home/hadoop/tools/deploy.conf  </span></li><li class="alt">
<span>else   </span></li><li>
<span>  confFile=$4  </span></li><li class="alt">
<span>fi  </span></li><li>
<span>  </span></li><li class="alt">
<span>#判断配置文件是否是普通文本文件  </span></li><li>
<span>if [ -f $confFile ]  </span></li><li class="alt">
<span>then  </span></li><li>
<span>#判断原件是普通文件还是目录  </span></li><li class="alt">
<span> if [ -f $src ]  </span></li><li>
<span>  then  </span></li><li class="alt">
<span>#如果是普通文件就把解析出标记对应的主机名的ip  </span></li><li>
<span> for server in `cat <span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-12-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;merror&gt;&lt;mtext&gt;confFile|grep&amp;#xA0;-v&amp;#xA0;&amp;#x2019;^#&amp;#x2019;|grep&amp;#xA0;&amp;#x2019;,&amp;#x2019;&lt;/mtext&gt;&lt;/merror&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><span class="math" id="MathJax-Span-1" style="" aria-hidden="true"><span class="noError" id="MathJax-Span-2" style="display: inline-block;">confFile|grep -v ’^#’|grep ’,’</span></span><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><merror><mtext>confFile|grep -v ’^#’|grep ’,’</mtext></merror></math></span></span><script type="math/tex" id="MathJax-Element-12">confFile|grep -v ’^#’|grep ’,’</script>tag’,’|awk -F’,’ ’{print $1}’`   </span></li><li class="alt">
<span>    do  </span></li><li>
<span>       scp <span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-13-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;merror&gt;&lt;mtext&gt;src&amp;amp;nbsp;&lt;/mtext&gt;&lt;/merror&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><span class="math" id="MathJax-Span-3" style="" aria-hidden="true"><span class="noError" id="MathJax-Span-4" style="display: inline-block;">src&amp;nbsp;</span></span><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><merror><mtext>src&amp;nbsp;</mtext></merror></math></span></span><script type="math/tex" id="MathJax-Element-13">src&nbsp;</script>server”:”${dest}  #使用循环把文件复制到目标ip上的相应目录下  </span></li><li class="alt">
<span>    done   </span></li><li>
<span>  elif [ -d $src ]  </span></li><li class="alt">
<span>  then  </span></li><li>
<span>    for server in `cat <span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-14-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;merror&gt;&lt;mtext&gt;confFile|grep&amp;#xA0;-v&amp;#xA0;&amp;#x2019;^#&amp;#x2019;|grep&amp;#xA0;&amp;#x2019;,&amp;#x2019;&lt;/mtext&gt;&lt;/merror&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><span class="math" id="MathJax-Span-5" style="" aria-hidden="true"><span class="noError" id="MathJax-Span-6" style="display: inline-block;">confFile|grep -v ’^#’|grep ’,’</span></span><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><merror><mtext>confFile|grep -v ’^#’|grep ’,’</mtext></merror></math></span></span><script type="math/tex" id="MathJax-Element-14">confFile|grep -v ’^#’|grep ’,’</script>tag’,’|awk -F’,’ ’{print $1}’`   </span></li><li class="alt">
<span>    do  </span></li><li>
<span>       scp -r <span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-15-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;merror&gt;&lt;mtext&gt;src&amp;amp;nbsp;&lt;/mtext&gt;&lt;/merror&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><span class="math" id="MathJax-Span-7" style="" aria-hidden="true"><span class="noError" id="MathJax-Span-8" style="display: inline-block;">src&amp;nbsp;</span></span><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><merror><mtext>src&amp;nbsp;</mtext></merror></math></span></span><script type="math/tex" id="MathJax-Element-15">src&nbsp;</script>server”:”${dest}  </span></li><li class="alt">
<span>    done   </span></li><li>
<span>  else  </span></li><li class="alt">
<span>      echo “Error: No source file exist”  </span></li><li>
<span>  fi  </span></li><li class="alt">
<span>  </span></li><li>
<span>else  </span></li><li class="alt">
<span>  echo “Error: Please assign config file or run deploy.sh command with deploy.conf in same directory”  </span></li><li>
<span>fi  </span></li></ol>
</div>
<p>
<span>5、创建 <span><span>runRemoteCmd.sh </span></span>脚本文件</span></p>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 tools]$ vi  runRemoteCmd.sh  </span></span></li></ol>
</div>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>#!/bin/bash  </span></span></li><li>
<span>#set -x  </span></li><li class="alt">
<span>#判断参数个数  </span></li><li>
<span>#实例如：显示所有节点的java进程，中间用引号的就是命令，这个命令将在所以节点上执行  </span></li><li class="alt">
<span>#./runRemoteCmd.sh  “jps” all  </span></li><li>
<span>if [ $# -lt 2 ]  </span></li><li class="alt">
<span>then   </span></li><li>
<span>  echo “Usage: ./runRemoteCmd.sh Command MachineTag”  </span></li><li class="alt">
<span>  echo “Usage: ./runRemoteCmd.sh Command MachineTag confFile”  </span></li><li>
<span>  exit   </span></li><li class="alt">
<span>fi  </span></li><li>
<span>  </span></li><li class="alt">
<span>cmd=$1  </span></li><li>
<span>tag=$2  </span></li><li class="alt">
<span>if [ ‘a’$3’a’ == ‘aa’ ]  </span></li><li>
<span>then  </span></li><li class="alt">
<span>   </span></li><li>
<span>  confFile=/home/hadoop/tools/deploy.conf  </span></li><li class="alt">
<span>else   </span></li><li>
<span>  confFile=$3  </span></li><li class="alt">
<span>fi  </span></li><li>
<span>  </span></li><li class="alt">
<span>if [ -f $confFile ]  </span></li><li>
<span>then  </span></li><li class="alt">
<span>    for server in `cat <span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-16-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;merror&gt;&lt;mtext&gt;confFile|grep&amp;#xA0;-v&amp;#xA0;&amp;#x2019;^#&amp;#x2019;|grep&amp;#xA0;&amp;#x2019;,&amp;#x2019;&lt;/mtext&gt;&lt;/merror&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><span class="math" id="MathJax-Span-9" style="" aria-hidden="true"><span class="noError" id="MathJax-Span-10" style="display: inline-block;">confFile|grep -v ’^#’|grep ’,’</span></span><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><merror><mtext>confFile|grep -v ’^#’|grep ’,’</mtext></merror></math></span></span><script type="math/tex" id="MathJax-Element-16">confFile|grep -v ’^#’|grep ’,’</script>tag’,’|awk -F’,’ ’{print $1}’`   </span></li><li>
<span>    do  </span></li><li class="alt">
<span>       echo ”*******************$server***************************”  </span></li><li>
<span>       ssh <span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-17-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;merror&gt;&lt;mtext&gt;server&amp;amp;nbsp;&amp;quot;source&amp;amp;nbsp;/etc/profile;&amp;amp;nbsp;&lt;/mtext&gt;&lt;/merror&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><span class="math" id="MathJax-Span-11" style="" aria-hidden="true"><span class="noError" id="MathJax-Span-12" style="display: inline-block;">server&amp;nbsp;"source&amp;nbsp;/etc/profile;&amp;nbsp;</span></span><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><merror><mtext>server&amp;nbsp;"source&amp;nbsp;/etc/profile;&amp;nbsp;</mtext></merror></math></span></span><script type="math/tex" id="MathJax-Element-17">server&nbsp;"source&nbsp;/etc/profile;&nbsp;</script>cmd”    </span></li><li class="alt">
<span># 注意在使用的时候要根据自己设置的环境变量的配置位置，给定相应的source源 ，  </span></li><li>
<span># 如 我把环境变量设/home/hadoop/.bash_profile文件下，就需要上面这条命令改为  </span></li><li class="alt">
<span># ssh <span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-18-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;merror&gt;&lt;mtext&gt;server&amp;amp;nbsp;&amp;quot;source&amp;amp;nbsp;/home/hadoop/.bash_profile;&lt;/mtext&gt;&lt;/merror&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><span class="math" id="MathJax-Span-13" style="" aria-hidden="true"><span class="noError" id="MathJax-Span-14" style="display: inline-block;">server&amp;nbsp;"source&amp;nbsp;/home/hadoop/.bash_profile;</span></span><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><merror><mtext>server&amp;nbsp;"source&amp;nbsp;/home/hadoop/.bash_profile;</mtext></merror></math></span></span><script type="math/tex" id="MathJax-Element-18">server&nbsp;"source&nbsp;/home/hadoop/.bash_profile;</script>cmd”  </span></li><li>
<span>#上面的例子：这条命令就是在远程标记为tag的主机下执行这个命令jps。  </span></li><li class="alt">
<span> done   </span></li><li>
<span>else  </span></li><li class="alt">
<span>  echo “Error: Please assign config file or run deploy.sh command with deploy.conf in same directory”  </span></li><li>
<span>fi  </span></li></ol>
</div>
<br>
<span>6、给脚本文件添加执行权限。</span>
<p>
</p>

<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 tools]$chmod u+x deploy.sh  </span></span></li><li>
<span>[hadoop@CDHNode1 tools]$chmod u+x runRemoteCmd.sh  </span></li></ol>
</div>
<p>
</p>

<pre></pre>
<span>7、把tools目录添加到环境变量PATH中。</span>
<p>
</p>
<p>
<span><span></span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 tools]$vi /home/hadoop/.bash_profile  </span></span></li></ol>
</div>
<span>添加下面内容</span><br>
<p>
</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>#tools  </span></span></li><li>
<span>export PATH=$PATH:/home/hadoop/tools  </span></li></ol>
</div>
<p>
</p>
<pre></pre>
<span>8、是环境变量及时生效</span><span></span>
<p>
</p>
<p>
</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 tools]$source /home/hadoop/.bash_profile  </span></span></li></ol>
</div>
<span>9、<span><span>在CDHNode1节点上，通过runRemoteCmd.sh脚本，一键创建所有节点的软件安装目录/home/hadoop/app。</span></span></span><span></span>
<p>
</p>
<p>
<span><span></span></span><span></span></p>
<span><span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 tools]$ runRemoteCmd.sh “mkdir /home/hadoop/app” all  </span></span></li></ol>
</div>
<span><span>我们可以在所有节点查看到/home/hadoop/app目录已经创建成功。</span></span><span> </span><br>
<p>
</p>
<h2 id="title-1">
<a target="_blank"></a><br>
</h2>
<p>
<span>我们先来说一下软件的安装步骤：</span></p>
<p>
<span>对于解压安装的软件，安装步骤为：</span></p>
<ol>
<li><span>使用rz命令上传要安装的文件，此命令只能在远程连接工具xshell上执行，不能再centos虚拟机上执行</span><br>
</li><li><span>使用tar -zxvf  softwarename.tar.gz</span></li><li><span><span>修改配置文件（根据需要而定，有时可省略）</span><br>
</span></li><li><span>在环境变量文件中配置环境变量</span></li><li><span>使用source 是环境变量文件即时生效。</span></li></ol>
<h2 id="安装jdk"><a></a>
<a target="_blank"></a>安装JDK</h2>
<p>
<span>首先在CDHNode1上安装jdk,然后复制到其他节点。</span><br>
</p>
<p>
<span>1、上传使用rz后，找到下载的jdk文件（<span><span>jdk-7u79-linux-x64.tar.gz  </span></span>）即可，选择后就可以上传，上传需要时间。</span></p>
<span><span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 ~]$cd /home/hadoop/app  </span></span></li><li>
<span>  </span></li><li class="alt">
<span>[hadoop@CDHNode1 app]$ rz              //选择本地的下载好的jdk-7u79-linux-x64.tar.gz  </span></li></ol>
</div>
<br>
<br>
<p>
<span>2、解压<span><span>jdk-7u79-linux-x64.tar.gz  </span></span></span></p>
<span><span><br>
</span></span><span></span><span><span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ tar zxvf jdk-7u79-linux-x64.tar.gz               //解压  </span></span></li></ol>
</div>
<br>
<br>
<p>
<span><span>3、修改jdk的名字，删除上传的压缩文件<span><span>jdk-7u79-linux-x64.tar.gz</span></span></span></span></p>
<span><span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ mv jdk1.7.0_79 jdk  </span></span></li><li>
<span>[hadoop@CDHNode1 app]$ rm -rf jdk-7u79-linux-x64.tar.gz              //删除安装包  </span></li></ol>
</div>
<br>
<br>
<p>
<span>4、配置环境变量</span></p>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$vi /home/hadoop/.bash_profile  </span></span></li></ol>
</div>
<br>
<br>
<p>
</p>
<p>
<span><span></span></span></p>
<p>
</p>
<p>
<span>添加</span></p>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>#java  </span></span></li><li>
<span>export JAVA_HOME=/home/hadoop/app/jdk  </span></li><li class="alt">
<span>export CLASSPATH=.:<span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-19-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;mi&gt;J&lt;/mi&gt;&lt;mi&gt;A&lt;/mi&gt;&lt;mi&gt;V&lt;/mi&gt;&lt;msub&gt;&lt;mi&gt;A&lt;/mi&gt;&lt;mi&gt;H&lt;/mi&gt;&lt;/msub&gt;&lt;mi&gt;O&lt;/mi&gt;&lt;mi&gt;M&lt;/mi&gt;&lt;mi&gt;E&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;/&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;l&lt;/mi&gt;&lt;mi&gt;i&lt;/mi&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mo&gt;:&lt;/mo&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-15" style="width: 9.892em; display: inline-block;"><span style="display: inline-block; position: relative; width: 8.225em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.225em, 1008.17em, 2.558em, -999.997em); top: -2.164em; left: 0em;"><span class="mrow" id="MathJax-Span-16"><span class="mi" id="MathJax-Span-17" style="font-family: MathJax_Math-italic;">J<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mi" id="MathJax-Span-18" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-19" style="font-family: MathJax_Math-italic;">V<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.169em;"></span></span><span class="msubsup" id="MathJax-Span-20"><span style="display: inline-block; position: relative; width: 1.447em; height: 0px;"><span style="position: absolute; clip: rect(3.114em, 1000.73em, 4.169em, -999.997em); top: -3.997em; left: 0em;"><span class="mi" id="MathJax-Span-21" style="font-family: MathJax_Math-italic;">A</span><span style="display: inline-block; width: 0px; height: 4.003em;"></span></span><span style="position: absolute; top: -3.831em; left: 0.781em;"><span class="mi" id="MathJax-Span-22" style="font-size: 70.7%; font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span style="display: inline-block; width: 0px; height: 4.003em;"></span></span></span></span><span class="mi" id="MathJax-Span-23" style="font-family: MathJax_Math-italic;">O</span><span class="mi" id="MathJax-Span-24" style="font-family: MathJax_Math-italic;">M<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mi" id="MathJax-Span-25" style="font-family: MathJax_Math-italic;">E<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="texatom" id="MathJax-Span-26"><span class="mrow" id="MathJax-Span-27"><span class="mo" id="MathJax-Span-28" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-29" style="font-family: MathJax_Math-italic;">l</span><span class="mi" id="MathJax-Span-30" style="font-family: MathJax_Math-italic;">i</span><span class="mi" id="MathJax-Span-31" style="font-family: MathJax_Math-italic;">b</span><span class="mo" id="MathJax-Span-32" style="font-family: MathJax_Main; padding-left: 0.281em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.169em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.33em; border-left: 0px solid; width: 0px; height: 1.337em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>J</mi><mi>A</mi><mi>V</mi><msub><mi>A</mi><mi>H</mi></msub><mi>O</mi><mi>M</mi><mi>E</mi><mrow class="MJX-TeXAtom-ORD"><mo>/</mo></mrow><mi>l</mi><mi>i</mi><mi>b</mi><mo>:</mo></math></span></span><script type="math/tex" id="MathJax-Element-19">JAVA_HOME/lib:</script>CLASSPATH  </span></li><li>
<span>export PATH=<span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-20-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;mi&gt;P&lt;/mi&gt;&lt;mi&gt;A&lt;/mi&gt;&lt;mi&gt;T&lt;/mi&gt;&lt;mi&gt;H&lt;/mi&gt;&lt;mo&gt;:&lt;/mo&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-33" style="width: 4.447em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.669em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.281em, 1003.61em, 2.336em, -999.997em); top: -2.164em; left: 0em;"><span class="mrow" id="MathJax-Span-34"><span class="mi" id="MathJax-Span-35" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.114em;"></span></span><span class="mi" id="MathJax-Span-36" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-37" style="font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.114em;"></span></span><span class="mi" id="MathJax-Span-38" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mo" id="MathJax-Span-39" style="font-family: MathJax_Main; padding-left: 0.281em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.169em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.063em; border-left: 0px solid; width: 0px; height: 1.003em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>P</mi><mi>A</mi><mi>T</mi><mi>H</mi><mo>:</mo></math></span></span><script type="math/tex" id="MathJax-Element-20">PATH:</script>JAVA_HOME/bin:$JAVA_HOME/jre/bin  </span></li></ol>
</div>
<br>
<br>
<p>
</p>
<p>
</p>
<p>
</p>
<p>
<span>5、<span>使环境变量文件即时生效</span></span><br>
</p>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$source  /home/hadoop/.bash_profile  </span></span></li></ol>
</div>
<p>
</p>
<br>
<pre></pre>
<pre></pre>
<span>6、查看是否安装成功，查看Java版本</span><span></span>
<p>
</p>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]# java -version  </span></span></li><li>
<span>java version “1.7.0_79”  </span></li><li class="alt">
<span>Java(TM) SE Runtime Environment (build 1.7.0_79-b15)  </span></li><li>
<span>Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)  </span></li></ol>
</div>
<span></span><span></span>
<p>
</p>
<span>出现以上结果就说明CDHNode1节点上的jdk安装成功。</span><span> </span><span>然后将<span><span>CDHNode1</span></span>下的jdk安装包复制到其他节点上。<br>
</span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ deploy.sh jdk /home/hadoop/app/ slave  </span></span></li></ol>
</div>
<span><span><span><span>CDHNode2、<span><span><span><span>CDHNode3、<span><span><span><span>CDHNode4、<span><span><span><span>CDHNode5加点重复<span><span><span><span>CDHNode1</span></span></span></span>上的jdk配置即可。就是在其他节点上从第4步开始配置。<br>
<br>
</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span><span></span>
<h2 id="安装zookeeper"><a></a>
<a target="_blank"></a>安装Zookeeper</h2>
<p>
<span>首先在CDHNode1上安装Zookeeper,然后复制到其他节点。</span></p>
<p>
<span><span>1、<span><span>将本地下载好的zookeeper-3.4.6.tar.gz安装包，上传至<span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span>CDHNode1</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span>节点下的/home/hadoop/app目录下。</span></span></span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ rz              //选择本地下载好的zookeeper-3.4.6.tar.gz  </span></span></li></ol>
</div>
<p>
</p>
<p>
<span><span>2、解压<span><span>zookeeper-3.4.6.tar.gz</span></span><span><span> <br>
</span></span></span></span></p>
<p>
<span><span></span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ tar zxvf zookeeper-3.4.6.tar.gz              //解压  </span></span></li></ol>
</div>
<p>
</p>
<span><span>3、修改<span><span><span><span>zookeeper</span></span></span></span>的名字，删除上传的压缩文件<span><span>zookeeper-3.4.6.tar.gz<br>
</span></span></span></span><span><span><span><span></span></span></span></span><span><span><span></span></span></span><span></span>
<p>
</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ rm -rf zookeeper-3.4.6.tar.gz              //删除zookeeper-3.4.6.tar.gz安装包  </span></span></li><li>
<span>[hadoop@CDHNode1 app]$ mv zookeeper-3.4.6 zookeeper              //重命名  </span></li></ol>
</div>
<p>
</p>
<span>4、<span><span>修改Zookeeper中的配置文件。</span></span></span><span></span>
<p align="left">
<span>[hadoop@CDHNode1 app]$ cd /home/hadoop/app/zookeeper/conf/</span></p>
<span><span><span><span></span></span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 conf]$ cp zoo_sample.cfg zoo.cfg              //复制一个zoo.cfg文件  </span></span></li><li>
<span>[hadoop@CDHNode1 conf]$ vi zoo.cfg  </span></li></ol>
</div>
<br>
<br>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>#添加下面的  </span></span></li><li>
<span># The number of milliseconds of each tick  </span></li><li class="alt">
<span>tickTime=2000  </span></li><li>
<span># The number of ticks that the initial  </span></li><li class="alt">
<span># synchronization phase can take  </span></li><li>
<span>initLimit=10  </span></li><li class="alt">
<span># The number of ticks that can pass between  </span></li><li>
<span># sending a request and getting an acknowledgement  </span></li><li class="alt">
<span>syncLimit=5  </span></li><li>
<span># the directory where the snapshot is stored.  </span></li><li class="alt">
<span># do not use /tmp for storage, /tmp here is just  </span></li><li>
<span># example sakes.  </span></li><li class="alt">
<span>#数据文件目录与日志目录  </span></li><li>
<span>dataDir=/home/hadoop/data/zookeeper/zkdata  </span></li><li class="alt">
<span>dataLogDir=/home/hadoop/data/zookeeper/zkdatalog  </span></li><li>
<span># the port at which the clients will connect  </span></li><li class="alt">
<span>clientPort=2181  </span></li><li>
<span>#server.服务编号=主机名称：Zookeeper不同节点之间同步和通信的端口：选举端口（选举leader）  </span></li><li class="alt">
<span>server.1=CDHNode1:2888:3888  </span></li><li>
<span>server.2=CDHNode2:2888:3888  </span></li><li class="alt">
<span>server.3=CDHNode3:2888:3888  </span></li><li>
<span>  </span></li><li class="alt">
<span># administrator guide before turning on autopurge.  </span></li><li>
<span>#  </span></li><li class="alt">
<span># http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance  </span></li><li>
<span>#  </span></li><li class="alt">
<span># The number of snapshots to retain in dataDir  </span></li><li>
<span>#autopurge.snapRetainCount=3  </span></li><li class="alt">
<span># Purge task interval in hours  </span></li><li>
<span># Set to “0” to disable auto purge feature  </span></li><li class="alt">
<span>#autopurge.purgeInterval=1  </span></li><li>
<span>~                                                                                                                                     </span></li><li class="alt">
<span>~                                                                                                                                     </span></li><li>
<span>~                                 </span></li></ol>
</div>
<br>
<br>
<p>
</p>
<p>
<span>5、配置环境变量</span></p>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$vi /home/hadoop/.bash_profile  </span></span></li></ol>
</div>
<p>
</p>
<span>添加</span><br>
<p>
</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>export ZOOKEEPER_HOME=/home/hadoop/app/zookeeper  </span></span></li><li>
<span>export PATH=<span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-21-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;mi&gt;P&lt;/mi&gt;&lt;mi&gt;A&lt;/mi&gt;&lt;mi&gt;T&lt;/mi&gt;&lt;mi&gt;H&lt;/mi&gt;&lt;mo&gt;:&lt;/mo&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-40" style="width: 4.447em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.669em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.281em, 1003.61em, 2.336em, -999.997em); top: -2.164em; left: 0em;"><span class="mrow" id="MathJax-Span-41"><span class="mi" id="MathJax-Span-42" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.114em;"></span></span><span class="mi" id="MathJax-Span-43" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-44" style="font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.114em;"></span></span><span class="mi" id="MathJax-Span-45" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mo" id="MathJax-Span-46" style="font-family: MathJax_Main; padding-left: 0.281em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.169em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.063em; border-left: 0px solid; width: 0px; height: 1.003em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>P</mi><mi>A</mi><mi>T</mi><mi>H</mi><mo>:</mo></math></span></span><script type="math/tex" id="MathJax-Element-21">PATH:</script>ZOOKEEPER_HOME/bin  </span></li></ol>
</div>
<p>
</p>
<p>
<span>6、<span>使环境变量文件即时生效</span></span></p>
<p>
<span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$source  /home/hadoop/.bash_profile  </span></span></li></ol>
</div>
<p>
</p>
<p>
<span></span></p>
<span><span>7、<span><span>通过远程命令deploy.sh将Zookeeper安装目录拷贝到其他节点(<span><span><span><span><span><span>CDHNode2、<span><span><span>CDHNode3</span></span></span></span></span></span></span></span></span>)上面<br>
</span></span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$deploy.sh zookeeper /home/hadoop/app/ zookeeper  </span></span></li></ol>
</div>
<pre></pre>
<pre></pre>
<span>8、<span><span>通过远程命令runRemoteCmd.sh在所有的zookeeper节点(<span><span><span><span><span><span><span><span><span><span><span>CDHNode1、</span></span></span></span></span>CDHNode2、<span><span><span>CDHNode3</span></span></span></span></span></span></span></span></span>)上面创建目录：<br>
</span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ runRemoteCmd.sh “mkdir -p /home/hadoop/data/zookeeper/zkdata” zookeeper   //创建数据目录  </span></span></li><li>
<span>[hadoop@CDHNode1 app]$ runRemoteCmd.sh “mkdir -p /home/hadoop/data/zookeeper/zkdatalog” zookeeper   //创建日志目录  </span></li></ol>
</div>
<span> </span><span><span></span></span><span>9、</span><span><span>然后分别在<span><span><span><span><span><span><span><span><span><span><span><span><span><span><span>CDHNode1、</span></span></span></span></span>CDHNode2、<span><span><span>CDHNode3</span></span></span></span></span></span></span></span></span></span></span></span></span>上面，进入zkdata目录下，创建文件myid，里面的内容分别填充为：1、2、3，
 这里我们以<span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span>CDHNode</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span>1为例。<br>
<span></span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ cd /home/hadoop/data/zookeeper/zkdata  </span></span></li><li>
<span>[hadoop@CDHNode1 zkdata]$ vi myid  </span></li></ol>
</div>
<span><span><span>//输入数字1<br>
<span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span></span></span></span></span></span>CDHNode2<span><span><span><span>输入数字2</span></span></span></span>、<span><span><span><span>CDHNode3<span><span><span><span>输入数字3</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span>。<br>
<br>
</span></span></span></span></span></span></span><span>10、在<span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span></span></span></span></span></span>CDHNode2、<span><span><span>CDHNode3</span></span></span></span></span></span></span></span></span></span></span></span></span>上面<span><span>配置Zookeeper环境变量。</span></span>按照第5、6步配置。</span></span></span></span><span><span><span><span></span></span></span></span><br>
<br>
11、<span><span>使用runRemoteCmd.sh 脚本，启动所有节点（<span><span><span><span><span><span><span><span><span><span><span><span><span><span><span>CDHNode1、</span></span></span></span></span>CDHNode2、<span><span><span>CDHNode3</span></span></span></span></span></span></span></span></span></span></span></span></span>）上面的Zookeeper。<br>
<span><span><span><span></span></span></span></span></span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 zkdata]$ runRemoteCmd.sh ”/home/hadoop/app/zookeeper/bin/zkServer.sh start” zookeeper  </span></span></li></ol>
</div>
<span><span></span></span><span></span><span>12、</span><span><span><span>查看所有节点上面的QuorumPeerMain进程是否启动。</span><br>
<br>
</span></span><span><span><span><span></span></span></span></span><span><span><span><span></span></span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 zkdata]$ runRemoteCmd.sh “jps” zookeeper  </span></span></li></ol>
</div>
<span><span><br>
</span></span><span>13、</span><span><span><span>查看所有Zookeeper节点状态。</span><br>
</span></span><span><span><span><span></span></span></span></span><span><span><span><span></span></span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 zkdata]$ runRemoteCmd.sh ”/home/hadoop/app/zookeeper/bin/zkServer.sh status” zookeeper  </span></span></li></ol>
</div>
<span>如果一个节点为leader，另2个节点为follower，则说明Zookeeper安装成功。</span><br>
<span><span>注意：QuorumPeerMain可能不显示在jps进程中，可以使用bin/zkServer.sh status 查看状态,无法启动的原因可以查看zookeeper.out文件，查看错误原因<br>
</span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>&lt;span style=”color:#000000;”&gt;[hadoop@CDHNode2 ~]$ cd app/zookeeper/  </span></span></li><li>
<span>[hadoop@CDHNode2 zookeeper]$ ls  </span></li><li class="alt">
<span>bin               cloudera-pom.xml  ivysettings.xml  NOTICE.txt            share                              zookeeper.out  </span></li><li>
<span>build.properties  conf              ivy.xml          README_packaging.txt  src  </span></li><li class="alt">
<span>build.xml         contrib           lib              README.txt            zookeeper-3.4.5-cdh5.4.5.jar  </span></li><li>
<span>CHANGES.txt       dist-maven        libexec          recipes               zookeeper-3.4.5-cdh5.4.5.jar.md5  </span></li><li class="alt">
<span>cloudera          docs              LICENSE.txt      sbin                  zookeeper-3.4.5-cdh5.4.5.jar.sha1  </span></li><li>
<span>[hadoop@CDHNode2 zookeeper]$ vi zookeeper.out  </span></li><li class="alt">
<span>  </span></li><li>
<span>2016-05-20 16:00:30,095 [myid:] - INFO  [main:QuorumPeerConfig@101] - Reading configuration from: /home/hadoop/app/zookeeper/bin/../conf/zoo.cfg  </span></li><li class="alt">
<span>2016-05-20 16:00:30,099 [myid:] - INFO  [main:QuorumPeerConfig@334] - Defaulting to majority quorums  </span></li><li>
<span>2016-05-20 16:00:30,100 [myid:] - ERROR [main:QuorumPeerMain@86] - Invalid config, exiting abnormally  </span></li><li class="alt">
<span>org.apache.zookeeper.server.quorum.QuorumPeerConfig$ConfigException: Error processing /home/hadoop/app/zookeeper/bin/../conf/zoo.cfg  </span></li><li>
<span>        at org.apache.zookeeper.server.quorum.QuorumPeerConfig.parse(QuorumPeerConfig.java:121)  </span></li><li class="alt">
<span>        at org.apache.zookeeper.server.quorum.QuorumPeerMain.initializeAndRun(QuorumPeerMain.java:106)  </span></li><li>
<span>        at org.apache.zookeeper.server.quorum.QuorumPeerMain.main(QuorumPeerMain.java:79)  </span></li><li class="alt">
<span>Caused by: java.lang.IllegalArgumentException: /home/hadoop/data/zookeeper/zkdata/myid file is missing  </span></li><li>
<span>        at org.apache.zookeeper.server.quorum.QuorumPeerConfig.parseProperties(QuorumPeerConfig.java:344)  </span></li><li class="alt">
<span>        at org.apache.zookeeper.server.quorum.QuorumPeerConfig.parse(QuorumPeerConfig.java:117)  </span></li><li>
<span>        … 2 more  </span></li><li class="alt">
<span>Invalid config, exiting abnormally  </span></li><li>
<span>&lt;/span&gt;  </span></li></ol>
</div>
<span><br>
</span><br>
<span></span><span></span>
<h2 id="安装hadoop"><a></a>
<a target="_blank"></a>安装hadoop</h2>
<p>
<span>首先在CDHNode1上安装hadoop,然后复制到其他节点。</span></p>
<p>
<span>1、<span><span><span>将本地下载</span>好的<a target="_blank" href="http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.4.5.tar.gz" rel="nofollow">hadoop-2.6.0-cdh5.4.5.tar.gz</a>安装包，上传至<span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span><span>CDHNode1</span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span></span>节点下的/home/hadoop/app目录下。</span></span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ rz              //选择本地下载好的hadoop-2.6.0-cdh5.4.5.tar.gz  </span></span></li></ol>
</div>
<p>
<span>2、解压</span><span>hadoop-2.6.0-cdh5.4.5.tar.gz</span></p>
<p>
<span><span></span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ tar zxvf hadoop-2.6.0-cdh5.4.5.tar.gz             //解压  </span></span></li></ol>
</div>
<span><span>3、修改<span><span><span><span><span>hadoop</span></span></span></span></span>的名字，删除上传的压缩文件<span><span><span></span><span>hadoop-2.6.0-cdh5.4.5.tar.gz</span><br>
</span></span></span></span><span><span><span><span></span></span></span></span><span><span><span></span></span></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ rm -rf hadoop-2.6.0-cdh5.4.5.tar.gz              //删除hadoop-2.6.0-cdh5.4.5.tar.gz  安装包  </span></span></li><li>
<span>[hadoop@CDHNode1 app]$ mv hadoop-2.6.0 hadoop              //重命名  </span></li></ol>
</div>
<p>
</p>
<p>
<span>4、配置环境变量</span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$vi /home/hadoop/.bash_profile  </span></span></li></ol>
</div>
<span>添加</span><br>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span># hadoop_home 2.6.0 path  </span></span></li><li>
<span>HADOOP_HOME=/home/hadoop/app/hadoop  </span></li><li class="alt">
<span>PATH=<span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-22-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;mi&gt;H&lt;/mi&gt;&lt;mi&gt;A&lt;/mi&gt;&lt;mi&gt;D&lt;/mi&gt;&lt;mi&gt;O&lt;/mi&gt;&lt;mi&gt;O&lt;/mi&gt;&lt;msub&gt;&lt;mi&gt;P&lt;/mi&gt;&lt;mi&gt;H&lt;/mi&gt;&lt;/msub&gt;&lt;mi&gt;O&lt;/mi&gt;&lt;mi&gt;M&lt;/mi&gt;&lt;mi&gt;E&lt;/mi&gt;&lt;mrow class="MJX-TeXAtom-ORD"&gt;&lt;mo&gt;/&lt;/mo&gt;&lt;/mrow&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mi&gt;i&lt;/mi&gt;&lt;mi&gt;n&lt;/mi&gt;&lt;mo&gt;:&lt;/mo&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-47" style="width: 12.336em; display: inline-block;"><span style="display: inline-block; position: relative; width: 10.281em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.225em, 1010.23em, 2.558em, -999.997em); top: -2.164em; left: 0em;"><span class="mrow" id="MathJax-Span-48"><span class="mi" id="MathJax-Span-49" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mi" id="MathJax-Span-50" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-51" style="font-family: MathJax_Math-italic;">D</span><span class="mi" id="MathJax-Span-52" style="font-family: MathJax_Math-italic;">O</span><span class="mi" id="MathJax-Span-53" style="font-family: MathJax_Math-italic;">O</span><span class="msubsup" id="MathJax-Span-54"><span style="display: inline-block; position: relative; width: 1.336em; height: 0px;"><span style="position: absolute; clip: rect(3.169em, 1000.78em, 4.169em, -999.997em); top: -3.997em; left: 0em;"><span class="mi" id="MathJax-Span-55" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.114em;"></span></span><span style="display: inline-block; width: 0px; height: 4.003em;"></span></span><span style="position: absolute; top: -3.831em; left: 0.669em;"><span class="mi" id="MathJax-Span-56" style="font-size: 70.7%; font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span style="display: inline-block; width: 0px; height: 4.003em;"></span></span></span></span><span class="mi" id="MathJax-Span-57" style="font-family: MathJax_Math-italic;">O</span><span class="mi" id="MathJax-Span-58" style="font-family: MathJax_Math-italic;">M<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.058em;"></span></span><span class="mi" id="MathJax-Span-59" style="font-family: MathJax_Math-italic;">E<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.003em;"></span></span><span class="texatom" id="MathJax-Span-60"><span class="mrow" id="MathJax-Span-61"><span class="mo" id="MathJax-Span-62" style="font-family: MathJax_Main;">/</span></span></span><span class="mi" id="MathJax-Span-63" style="font-family: MathJax_Math-italic;">b</span><span class="mi" id="MathJax-Span-64" style="font-family: MathJax_Math-italic;">i</span><span class="mi" id="MathJax-Span-65" style="font-family: MathJax_Math-italic;">n</span><span class="mo" id="MathJax-Span-66" style="font-family: MathJax_Main; padding-left: 0.281em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.169em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.33em; border-left: 0px solid; width: 0px; height: 1.337em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>H</mi><mi>A</mi><mi>D</mi><mi>O</mi><mi>O</mi><msub><mi>P</mi><mi>H</mi></msub><mi>O</mi><mi>M</mi><mi>E</mi><mrow class="MJX-TeXAtom-ORD"><mo>/</mo></mrow><mi>b</mi><mi>i</mi><mi>n</mi><mo>:</mo></math></span></span><script type="math/tex" id="MathJax-Element-22">HADOOP_HOME/bin:</script>PATH  </span></li><li>
<span>export   HADOOP_HOME PATH  </span></li></ol>
</div>
<p>
<span>5、<span>使环境变量文件即时生效</span></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$source  /home/hadoop/.bash_profile  </span></span></li></ol>
</div>
<p>
<span><span></span></span></p>
<p>
<span>6、切换到/home/hadoop/app/hadoop/etc/hadoop/目录下，修改配置文件。</span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 app]$ cd /home/hadoop/app/hadoop/etc/hadoop/  </span></span></li></ol>
</div>
<br>
<span></span><span></span>
<h3 id="配置hdfs"><a></a>
<a target="_blank"></a><span>配置HDFS</span></h3>
<p>
<span>配置hadoop-env.sh</span></p>
<pre><span>[hadoop@CDHNode1 hadoop]$ vi hadoop-env.sh
export JAVA_HOME=/home/hadoop/app/jdk1.7.0_79

</span></pre>
<p>
<span>配置core-site.xml</span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ vi core-site.xml  </span></span></li><li>
<span>&lt;configuration&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;fs.defaultFS&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;hdfs://cluster1&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– 这里的值指的是默认的HDFS路径 ，取名为cluster1 –&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;/home/hadoop/data/tmp&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>        &lt;– hadoop的临时目录，如果需要配置多个目录，需要逗号隔开，data目录需要我们自己创建 –&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;ha.zookeeper.quorum&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;CDHNode1:2181,CDHNode2:2181,CDHNode3:2181,CDHNode4:2181,CDHNode5:2181&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– 配置Zookeeper 管理HDFS –&gt;  </span></li><li>
<span>&lt;/configuration&gt;  </span></li></ol>
</div>
<span>               <br>
</span><span></span>
<p>
<span>配置hdfs-site.xml</span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ vi hdfs-site.xml  </span></span></li><li>
<span>&lt;configuration&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;dfs.replication&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;3&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– 数据块副本数为3 –&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;dfs.permissions&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;false&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;dfs.permissions.enabled&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;false&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>        &lt;– 权限默认配置为false –&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;dfs.nameservices&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;cluster1&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– 命名空间，它的值与fs.defaultFS的值要对应，namenode高可用之后有两个namenode，cluster1是对外提供的统一入口 –&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;dfs.ha.namenodes.cluster1&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;CDHNode1,CDHNode2&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>        &lt;– 指定 nameService 是 cluster1 时的nameNode有哪些，这里的值也是逻辑名称，名字随便起，相互不重复即可 –&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;dfs.namenode.rpc-address.cluster1.CDHNode1&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;CDHNode1:9000&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– CDHNode1 rpc地址 –&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;dfs.namenode.http-address.cluster1.CDHNode1&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;CDHNode1:50070&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>        &lt;– CDHNode1 http地址 –&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;dfs.namenode.rpc-address.cluster1.CDHNode2&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;CDHNode2:9000&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– CDHNode2 rpc地址 –&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;dfs.namenode.http-address.cluster1.CDHNode2&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;CDHNode2:50070&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>        &lt;– CDHNode2 http地址 –&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;dfs.ha.automatic-failover.enabled&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;true&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– 启动故障自动恢复 –&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;qjournal://CDHNode1:8485;CDHNode2:8485;CDHNode3:8485;CDHNode4:8485;CDHNode5:8485/cluster1&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>        &lt;– 指定journal –&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;dfs.client.failover.proxy.provider.cluster1&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;org.apache.hadoop.hdfs.server.namenode.ha.ConfiguredFailoverProxyProvider&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– 指定 cluster1 出故障时，哪个实现类负责执行故障切换 –&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;dfs.journalnode.edits.dir&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;/home/hadoop/data/journaldata/jn&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>        &lt;– 指定JournalNode集群在对nameNode的目录进行共享时，自己存储数据的磁盘路径 –&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;dfs.ha.fencing.methods&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;shell(/bin/true)&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>        &lt;name&gt;dfs.ha.fencing.ssh.private-key-files&lt;/name&gt;  </span></li><li class="alt">
<span>        &lt;value&gt;/home/hadoop/.ssh/id_rsa&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>        &lt;name&gt;dfs.ha.fencing.ssh.connect-timeout&lt;/name&gt;  </span></li><li class="alt">
<span>        &lt;value&gt;10000&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– 脑裂默认配置 –&gt;  </span></li><li>
<span>        &lt;property&gt;  </span></li><li class="alt">
<span>                &lt;name&gt;dfs.namenode.handler.count&lt;/name&gt;  </span></li><li>
<span>                &lt;value&gt;100&lt;/value&gt;  </span></li><li class="alt">
<span>        &lt;/property&gt;  </span></li><li>
<span>&lt;/configuration&gt;  </span></li></ol>
</div>
<br>
<br>
<br>
<p>
<span>配置 slave</span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ vi slaves  </span></span></li><li>
<span>CDHNode3  </span></li><li class="alt">
<span>  </span></li><li>
<span>CDHNode4  </span></li><li class="alt">
<span>  </span></li><li>
<span>CDHNode5  </span></li></ol>
</div>
<br>
<br>
<h3 id="yarn安装配置"><a></a>
<a target="_blank"></a>YARN安装配置</h3>
<p>
配置mapred-site.xml</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ vi mapred-site.xml  </span></span></li><li>
<span>&lt;configuration&gt;  </span></li><li class="alt">
<span>        &lt;property&gt;  </span></li><li>
<span>                &lt;name&gt;mapreduce.framework.name&lt;/name&gt;  </span></li><li class="alt">
<span>                &lt;value&gt;yarn&lt;/value&gt;  </span></li><li>
<span>        &lt;/property&gt;  </span></li><li class="alt">
<span>        &lt;– 指定运行mapreduce的环境是Yarn，与hadoop1不同的地方 –&gt;  </span></li><li>
<span>&lt;/configuration&gt;  </span></li><li class="alt">
<span>                  </span></li></ol>
</div>
<br>
<br>
<span>配置yarn-site.xml</span><br>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ vi yarn-site.xml  </span></span></li><li>
<span>&lt;configuration&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>          &lt;name&gt;yarn.resourcemanager.connect.retry-interval.ms&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;2000&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;– 超时的周期 –&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>          &lt;name&gt;yarn.resourcemanager.ha.enabled&lt;/name&gt;  </span></li><li>
<span>          &lt;value&gt;true&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;– 打开高可用 –&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>          &lt;name&gt;yarn.resourcemanager.ha.automatic-failover.enabled&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;true&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;– 启动故障自动恢复 –&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>          &lt;name&gt;yarn.resourcemanager.ha.automatic-failover.embedded&lt;/name&gt;  </span></li><li>
<span>          &lt;value&gt;true&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>          &lt;name&gt;yarn.resourcemanager.cluster-id&lt;/name&gt;  </span></li><li>
<span>           &lt;value&gt;yarn-rm-cluster&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;– 给yarn cluster 取个名字yarn-rm-cluster –&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>          &lt;name&gt;yarn.resourcemanager.ha.rm-ids&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;rm1,rm2&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;– 给ResourceManager 取个名字 rm1,rm2 –&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>          &lt;name&gt;yarn.resourcemanager.hostname.rm1&lt;/name&gt;  </span></li><li>
<span>          &lt;value&gt;CDHNode1&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;– 配置ResourceManager rm1 hostname –&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>          &lt;name&gt;yarn.resourcemanager.hostname.rm2&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;CDHNode2&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;– 配置ResourceManager rm2 hostname –&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>           &lt;name&gt;yarn.resourcemanager.recovery.enabled&lt;/name&gt;  </span></li><li>
<span>          &lt;value&gt;true&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;– 启用resourcemanager 自动恢复 –&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>          &lt;name&gt;yarn.resourcemanager.zk.state-store.address&lt;/name&gt;  </span></li><li class="alt">
<span>           &lt;value&gt;CDHNode1:2181,CDHNode2:2181,CDHNode3:2181,CDHNode4:2181,CDHNode5:2181&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;– 配置Zookeeper地址 –&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>           &lt;name&gt;yarn.resourcemanager.zk-address&lt;/name&gt;  </span></li><li>
<span>          &lt;value&gt;CDHNode1:2181,CDHNode2:2181,CDHNode3:2181,CDHNode4:2181,CDHNode5:2181&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;– 配置Zookeeper地址 –&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>           &lt;name&gt;yarn.resourcemanager.address.rm1&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;CDHNode1:8032&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;–  rm1端口号 –&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>          &lt;name&gt;yarn.resourcemanager.scheduler.address.rm1&lt;/name&gt;  </span></li><li>
<span>          &lt;value&gt;CDHNode1:8034&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;– rm1调度器的端口号 –&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>          &lt;name&gt;yarn.resourcemanager.webapp.address.rm1&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;CDHNode1:8088&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;– rm1 webapp端口号 –&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>          &lt;name&gt;yarn.resourcemanager.address.rm2&lt;/name&gt;  </span></li><li>
<span>     &lt;value&gt;CDHNode2:8032&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;– rm2端口号 –&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>          &lt;name&gt;yarn.resourcemanager.scheduler.address.rm2&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;CDHNode2:8034&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;– rm2调度器的端口号 –&gt;  </span></li><li>
<span>     &lt;property&gt;  </span></li><li class="alt">
<span>          &lt;name&gt;yarn.resourcemanager.webapp.address.rm2&lt;/name&gt;  </span></li><li>
<span>          &lt;value&gt;CDHNode2:8088&lt;/value&gt;  </span></li><li class="alt">
<span>     &lt;/property&gt;  </span></li><li>
<span>     &lt;– rm2 webapp端口号 –&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>           &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;mapreduce_shuffle&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;property&gt;  </span></li><li>
<span>           &lt;name&gt;yarn.nodemanager.aux-services.mapreduce_shuffle.class&lt;/name&gt;  </span></li><li class="alt">
<span>          &lt;value&gt;org.apache.hadoop.mapred.ShuffleHandler&lt;/value&gt;  </span></li><li>
<span>     &lt;/property&gt;  </span></li><li class="alt">
<span>     &lt;– 执行MapReduce需要配置的shuffle过程 –&gt;  </span></li><li>
<span>&lt;/configuration&gt;  </span></li></ol>
</div>
<br>
<span></span><span></span>
<p>
<span>向所有节点分发hadoop安装包。</span></p>
<pre><span>[hadoop@CDHNode1 app]$ deploy.sh hadoop /home/hadoop/app/ slave</span></pre>
<br>
<br>
<p>
按照目录的规划建立好目录（用于存放数据的目录）：</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>runRemoteCmd.sh “mkdir -p /home/hadoop/data/name” all  </span></span></li><li>
<span>  </span></li><li class="alt">
<span>runRemoteCmd.sh “mkdir -p /home/hadoop/data/hdfs/edits” all  </span></li><li>
<span>  </span></li><li class="alt">
<span>runRemoteCmd.sh “mkdir -p /home/hadoop/data/datanode” all  </span></li><li>
<span>  </span></li><li class="alt">
<span>runRemoteCmd.sh “mkdir -p /home/hadoop/data/journaldata/jn” all  </span></li><li>
<span>  </span></li><li class="alt">
<span>runRemoteCmd.sh “mkdir -p /home/hadoop/data/tmp” all  </span></li><li>
<span>  </span></li><li class="alt">
<span>runRemoteCmd.sh “touch /home/hadoop/app/hadoop/etc/hadoop/excludes” all  </span></li><li>
<span>  </span></li><li class="alt">
<span>runRemoteCmd.sh “mkdir -p /home/hadoop/data/pid” all  </span></li></ol>
</div>
<br>
<p>
当你的在初始化工程中出错，要把相关目录的文件删除，然后再重新初始化</p>
<p>
</p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>rm -rf /home/hadoop/data/name/*  </span></span></li><li>
<span>  </span></li><li class="alt">
<span>rm -rf /home/hadoop/data/hdfs/edits/*  </span></li><li>
<span>  </span></li><li class="alt">
<span>rm -rf /home/hadoop/data/datanode/*  </span></li><li>
<span>  </span></li><li class="alt">
<span>rm -rf /home/hadoop/data/journaldata/jn/*  </span></li><li>
<span>  </span></li><li class="alt">
<span>rm -rf /home/hadoop/data/tmp/*  </span></li></ol>
</div>
<p>
</p>
<h3 id="title-2">
<a target="_blank"></a></h3>
<h3 id="集群初始化"><a></a>
<a target="_blank"></a>集群初始化<br>
</h3>
<span></span><span></span>
<p>
<span>1、启动所有节点上面的Zookeeper进程</span></p>
<pre><span>[hadoop@CDHNode1 hadoop]$ runRemoteCmd.sh "/home/hadoop/app/zookeeper/bin/zkServer.sh start" zookeeper
</span></pre>
<p>
<span>2、启动所有节点上面的journalnode进程</span></p>
<pre><span>[hadoop@CDHNode1 hadoop]$ runRemoteCmd.sh "/home/hadoop/app/hadoop/sbin/hadoop-daemon.sh start journalnode" all
</span></pre>
<p>
<span>3、首先在主节点上(比如,CDHNode1)执行格式化</span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ bin/hdfs namenode -format              / /namenode 格式化  </span></span></li><li>
<span>[hadoop@CDHNode1 hadoop]$ bin/hdfs zkfc -formatZK                 //格式化高可用  </span></li><li class="alt">
<span>[hadoop@CDHNode1 hadoop]$bin/hdfs namenode           //启动namenode  </span></li></ol>
</div>
<br>
<span>注意：执行完上述命令后，程序就会在等待状态，只有执行完下一步时，利用按下ctrl+c来结束namenode进程。</span><span>  
              </span><br>
<br>
<span>4、与此同时，需要在备节点（比如 CDHNode2）上执行数据同步 </span><span>注意：同时是在执行完上一步后，上一步程序停止的情况下执行此步骤的</span><span></span>
<pre>[hadoop@CDHNode2 hadoop]$ bin/hdfs namenode -bootstrapStandby     //同步主节点和备节点之间的元数据

</pre>

<p>
5、CDHNode2同步完数据后，紧接着在CDHNode1节点上，按下ctrl+c来结束namenode进程。 然后关闭所有节点上面的journalnode进程</p>
<pre>[hadoop@CDHNode1 hadoop]$ runRemoteCmd.sh "/home/hadoop/app/hadoop/sbin/hadoop-daemon.sh stop journalnode" all        //然后停掉各节点的journalnode
</pre>
<br>
<span>6、如果上面操作没有问题，我们可以一键启动hdfs所有相关进程</span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ sbin/start-dfs.sh  </span></span></li></ol>
</div>
<br>
<span>启动成功之后，查看关闭其中一个namenode ，然后在启动namenode 观察切换的状况</span>
<p>
7、验证是否启动成功</p>
<p>
通过web界面查看namenode启动情况。</p>
<pre>http://CDHNode1:50070
</pre>
<span>注意：在浏览器输入以上网址时，需要先在本机的hosts目录下添加如下映射：<br>
192.168.3.188  CDHNode1<br>
<span>192.168.3.189  CDHNode2</span><br>
<span>192.168.3.190  CDHNode3</span><br>
<span>192.168.3.191  CDHNode4</span><br>
<span>192.168.3.192  CDHNode5</span><br>
</span><br>
<span></span><span></span><span>启动成功之后，查看关闭其中一个namenode
 ，然后在启动namenode 观察切换的状况</span><span> </span><span>使用命令 kill </span><span> </span><br>
<p>
上传文件至hdfs</p>
<pre>[hadoop@CDHNode1 hadoop]$ vi a.txt  //本地创建一个a.txt文件
hadoop CDH
hello world
CDH hadoop
[hadoop@CDHNode1 hadoop]$ hdfs dfs -mkdir /test   //在hdfs上创建一个文件目录
[hadoop@CDHNode1 hadoop]$ hdfs dfs -put djt.txt /test      //向hdfs上传一个文件
[hadoop@CDHNode1 hadoop]$ hdfs dfs -ls /test       //查看a.txt是否上传成功

</pre>
<p>
如果上面操作没有问题说明hdfs配置成功。</p>
<br>
<p>
启动YARN</p>
<p>
1、在CDHNode1节点上执行。</p>
<pre>[hadoop@CDHNode1 hadoop]$ sbin/start-yarn.sh                
</pre>
<p>
2、在CDHNode2节点上面执行。</p>
<pre>[hadoop@CDHNode2 hadoop]$ sbin/yarn-daemon.sh start resourcemanager               
</pre>
<br>
<span></span><span></span>
<p>
<span>同时打开一下web界面。</span></p>
<pre><span>http://CDHNode1:8088
http://CDHNode2:8088

</span></pre>
<p>
<span>关闭其中一个resourcemanager，然后再启动，看看这个过程的web界面变化。</span></p>
<p>
<span>3、检查一下ResourceManager状态</span></p>
<pre><span>[hadoop@CDHNode1 hadoop]$ bin/yarn rmadmin -getServiceState rm1
[hadoop@CDHNode1 hadoop]$ bin/yarn rmadmin -getServiceState rm2

</span></pre>
<p>
<span>4、Wordcount示例测试</span></p>
<pre><span>[hadoop@djt11 hadoop]$ hadoop jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0.jar wordcount /test/a.txt /test/out/
</span></pre>
<br>
<span><span>如果上面执行没有异常，说明YARN安装成功。</span></span><br>
<br>
<h3 id="集群关启顺序"><a></a>
<a target="_blank"></a><span>集群关启顺序</span></h3>
<p>
关闭YARN</p>
<p>
a、在<span><span>CDHNode2</span></span><span>节点上面执行</span></p>
<p>
</p>

<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode2 hadoop]$ sbin/yarn-daemon.sh stop resourcemanager  </span></span></li></ol>

<p></p></div>

<p>
</p>

<p>
b、在<span>CDHNode1</span><span>节点上执行</span></p>
<p>
</p>

<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1  hadoop]$ sbin/stop-yarn.sh   </span></span></li></ol>

<p></p></div>

<p>
</p>

<p>
c、关闭<span>HDFS</span></p>
<p>
</p>

<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ sbin/stop-dfs.sh  </span></span></li></ol>

<p></p></div>

<p>
</p>

<p>
d、关闭<span>zookeeper</span></p>
<p>
</p>

<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ runRemoteCmd.sh ”/home/hadoop/app/zookeeper/bin/zkServer.sh stop” zookeeper  </span></span></li></ol>

<p></p></div>

<p>
</p>

<p><br></p>

<h3 id="再次启动集群"><a></a>
<a target="_blank"></a>再次启动集群</h3>

<p>
</p>

<p>
a、启动<span>zookeeper</span></p>
<p>
</p>

<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ runRemoteCmd.sh ”/home/hadoop/app/zookeeper/bin/zkServer.sh start” zookeeper  </span></span></li></ol>

<p></p></div>

<p>
</p>

<p>
b、启动<span>HDFS</span></p>
<p>
</p>

<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ sbin/start-dfs.sh  </span></span></li></ol>

<p></p></div>

<p>
</p>

<p>
c、在<span>CDHNode1</span><span>节点上执行</span></p>
<p align="justify">
</p>

<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode1 hadoop]$ sbin/start-yarn.sh     </span></span></li></ol>

<p></p></div>

<p>
</p>

<p align="justify">
d、在<span>CDHNode2</span><span></span><span>节点上面执行</span></p>
<p align="justify">
</p>

<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools">
<span>[plain]</span> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="ViewSource" title="view plain">view
 plain</a><span class="tracking-ad"> <a target="_blank" href="http://blog.csdn.net/u010270403/article/details/51446674#" rel="nofollow" class="CopyToClipboard" title="copy">copy</a></span><div></div><div></div>
<div>
</div>

<p><span class="tracking-ad"></span></p></div> <br>
</div>

<ol start="1">
<li class="alt">
<span><span>[hadoop@CDHNode2 hadoop]$ sbin/yarn-daemon.sh start resourcemanager  </span></span></li></ol>

<p></p></div>

<p>
</p>

<p><span>至此，hadoop 分布式集群搭建完毕。 </span> <br>
            </p></div> <br>
                </div>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
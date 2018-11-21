---
layout:     post
title:      Hadoop集群（第5期）_Hadoop安装配置         1、集群部署介绍       1.1 Hadoop简介       　　Hadoop是Apache软件基金会旗下的一个开源分布式计算平
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="postTitle"><a id="cb_post_title_url" class="postTitle2" href="http://www.cnblogs.com/xia520pi/archive/2012/05/16/2503949.html" rel="nofollow">Hadoop集群（第5期）_Hadoop安装配置</a></h1>
<div class="clear"></div>
<div class="postBody">
<div id="cnblogs_post_body">
<p style="text-align:justify;"></p>
<h2>1、集群部署介绍</h2>
<p style="text-align:justify;"></p>
<h3>1.1 Hadoop简介</h3>
<p></p>
<p><img alt="" align="left" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427003307.png">　　Hadoop是Apache软件基金会旗下的一个开源分布式计算平台。以Hadoop分布式文件系统（HDFS，Hadoop Distributed Filesystem）和MapReduce（Google MapReduce的开源实现）为<span style="color:#FF0000;"><strong>核心</strong></span>的Hadoop为用户提供了系统底层细节透明的分布式基础架构。</p>
<p>　　对于Hadoop的集群来讲，可以分成两大类角色：Master和Salve。一个<span style="color:#0000FF;"><strong>HDFS</strong></span>集群是由一个NameNode和若干个DataNode组成的。其中NameNode作为主服务器，管理文件系统的命名空间和客户端对文件系统的访问操作；集群中的DataNode管理存储的数据。<span style="color:#0000FF;"><strong>MapReduce</strong></span>框架是由一个单独运行在主节点上的JobTracker和运行在每个集群从节点的TaskTracker共同组成的。主节点负责调度构成一个作业的所有任务，这些任务分布在不同的从节点上。主节点监控它们的执行情况，并且重新执行之前的失败任务；从节点仅负责由主节点指派的任务。当一个Job被提交时，JobTracker接收到提交作业和配置信息之后，就会将配置信息等分发给从节点，同时调度任务并监控TaskTracker的执行。</p>
<p>　　从上面的介绍可以看出，HDFS和MapReduce共同组成了Hadoop分布式系统体系结构的核心。<span style="color:#0000FF;"><strong>HDFS</strong></span>在集群上实现<span style="color:#0000FF;">分布式文件系统</span>，<span style="color:#0000FF;"><strong>MapReduce</strong></span>在集群上实现了<span style="color:#0000FF;">分布式计算</span>和<span style="color:#0000FF;">任务处理</span>。HDFS在MapReduce任务处理过程中提供了文件操作和存储等支持，MapReduce在HDFS的基础上实现了任务的分发、跟踪、执行等工作，并收集结果，二者相互作用，完成了Hadoop分布式集群的主要任务。</p>
<p style="text-align:justify;"></p>
<h3>1.2 环境说明</h3>
<p>　　集群中包括4个节点：1个Master，3个Salve，节点之间局域网连接，可以相互ping通，具体集群信息可以查看"<span style="color:#FF0000;"><strong>Hadoop集群（第2期）</strong></span>"。节点IP地址分布如下：</p>
<p></p>
<div style="text-align:center;">
<table border="1" cellspacing="1" cellpadding="1" style="border-collapse:collapse;"><colgroup><col style="width:187px;"><col style="width:228px;"></colgroup><tbody valign="top"><tr style="background:rgb(224,224,224);"><td style="border:.5pt solid;">
<p style="text-align:center;"><span style="font-family:'宋体';font-size:10pt;"><strong>机器名称</strong></span></p>
</td>
<td style="border-style:solid solid solid none;border-top-width:.5pt;border-right-width:.5pt;border-bottom-width:.5pt;">
<p style="text-align:center;"><span style="font-size:10pt;"><strong><span style="font-family:'times new roman';">IP</span><span style="font-family:'宋体';">地址</span></strong></span></p>
</td>
</tr><tr><td style="border-style:none solid solid;border-right-width:.5pt;border-bottom-width:.5pt;border-left-width:.5pt;">
<p style="text-align:justify;"><span style="font-family:'times new roman';font-size:10pt;">Master.Hadoop</span></p>
</td>
<td style="border-style:none solid solid none;border-right-width:.5pt;border-bottom-width:.5pt;">
<p style="text-align:justify;"><span style="font-family:'times new roman';font-size:10pt;">192.168.1.2</span></p>
</td>
</tr><tr><td style="border-style:none solid solid;border-right-width:.5pt;border-bottom-width:.5pt;border-left-width:.5pt;">
<p style="text-align:justify;"><span style="font-family:'times new roman';font-size:10pt;">Salve1.Hadoop</span>
</p>
</td>
<td style="border-style:none solid solid none;border-right-width:.5pt;border-bottom-width:.5pt;">
<p style="text-align:justify;"><span style="font-family:'times new roman';font-size:10pt;">192.168.1.3</span>
</p>
</td>
</tr><tr><td style="border-style:none solid solid;border-right-width:.5pt;border-bottom-width:.5pt;border-left-width:.5pt;">
<p style="text-align:justify;"><span style="font-family:'times new roman';font-size:10pt;">Salve2.Hadoop</span>
</p>
</td>
<td style="border-style:none solid solid none;border-right-width:.5pt;border-bottom-width:.5pt;">
<p style="text-align:justify;"><span style="font-family:'times new roman';font-size:10pt;">192.168.1.4</span>
</p>
</td>
</tr><tr><td style="border-style:none solid solid;border-right-width:.5pt;border-bottom-width:.5pt;border-left-width:.5pt;">
<p style="text-align:justify;"><span style="font-family:'times new roman';font-size:10pt;">Salve3.Hadoop</span>
</p>
</td>
<td style="border-style:none solid solid none;border-right-width:.5pt;border-bottom-width:.5pt;">
<p style="text-align:justify;"><span style="font-family:'times new roman';font-size:10pt;">192.168.1.5</span>
</p>
</td>
</tr></tbody></table></div>
<p></p>
<p>　　四个节点上均是CentOS6.0系统，并且有一个相同的用户<span style="color:#FF0000;"><strong>hadoop</strong></span>。Master机器主要配置NameNode和JobTracker的角色，负责总管分布式数据和分解任务的执行；3个Salve机器配置DataNode和TaskTracker的角色，负责分布式数据存储以及任务的执行。其实应该还应该有1个Master机器，用来作为<span style="color:#FF0000;"><strong>备用</strong></span>，以防止Master服务器<span style="color:#FF0000;"><strong>宕机</strong></span>，还有一个备用马上启用。后续经验积累一定阶段后<span style="color:#FF0000;"><strong>补上</strong></span>一台备用Master机器。</p>
<p style="text-align:justify;"></p>
<h3>1.3 网络配置</h3>
<p>　　Hadoop集群要按照<span style="color:#FF0000;"><strong>1.2小节</strong></span>表格所示进行配置，我们在"<span style="color:#FF0000;"><strong>Hadoop集群（第1期）</strong></span>"的CentOS6.0安装过程就按照提前规划好的主机名进行安装和配置。如果实验室后来人在安装系统时，没有配置好，不要紧，没有必要重新安装，在安装完系统之后仍然可以根据后来的规划对机器的主机名进行修改。</p>
<p>　　下面的例子我们将以Master机器为例，即主机名为"Master.Hadoop"，IP为"192.168.1.2"进行一些主机名配置的相关操作。其他的Slave机器以此为依据进行修改。</p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）查看当前机器名称 </strong></span></p>
<p>　　用下面命令进行显示机器名称，如果跟规划的不一致，要按照下面进行修改。</p>
<p></p>
<blockquote>
<p>hostname</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427007734.png"></p>
<p></p>
<p>　　上图中，用"hostname"查"Master"机器的名字为"Master.Hadoop"，与我们预先规划的一致。</p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）修改当前机器名称 </strong></span></p>
<p><span style="color:#FF0000;"><strong>　　假定</strong></span>我们发现我们的机器的主机名不是我们想要的，通过对"<span style="color:#FF0000;"><strong>/etc/sysconfig/network</strong></span>"文件修改其中"<span style="color:#0000FF;"><strong>HOSTNAME</strong></span>"后面的值，改成我们规划的名称。</p>
<p>　　这个"<span style="color:#FF0000;"><strong>/etc/sysconfig/network</strong></span>"文件是定义hostname和是否利用网络的不接触网络设备的对系统全体定义的文件。</p>
<p><span style="color:#0000FF;"><strong>　　设定形式</strong></span>：设定值=值</p>
<p>　　"/etc/sysconfig/network"的<span style="color:#FF0000;"><strong>设定项目</strong></span>如下：</p>
<p></p>
<blockquote>
<p>NETWORKING 是否利用网络</p>
<p>GATEWAY 默认网关</p>
<p>IPGATEWAYDEV 默认网关的接口名</p>
<p>HOSTNAME 主机名</p>
<p>DOMAIN 域名</p>
</blockquote>
<p></p>
<p>　　用下面命令进行修改当前机器的主机名（<span style="color:#FF0000;"><strong>备注：</strong></span>修改系统文件一般用<span style="color:#FF0000;"><strong>root</strong></span>用户）</p>
<p></p>
<blockquote>
<p>vim /etc/sysconfig/network</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427002717.png"></p>
<p></p>
<p>　　通过上面的命令我们从"/etc/sysconfig/network"中找到"HOSTNAME"进行修改，查看内容如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427008780.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>3）修改当前机器IP </strong></span></p>
<p><span style="color:#FF0000;"><strong>假定</strong></span>我们的机器连IP在当时安装机器时都没有配置好，那此时我们需要对"<span style="color:#FF0000;"><strong>ifcfg-eth0</strong></span>"文件进行配置，该文件位于"<span style="color:#0000FF;"><strong>/etc/sysconfig/network-scripts</strong></span>"文件夹下。</p>
<p>　　在这个目录下面，存放的是网络接口（网卡）的制御脚本文件（控制文件），ifcfg- eth0是默认的第一个网络接口，如果机器中有多个网络接口，那么名字就将依此类推ifcfg-eth1，ifcfg-eth2，ifcfg- eth3，……。</p>
<p>　　这里面的文件是相当重要的，涉及到网络能否正常工作。</p>
<p>　　设定形式：设定值=值</p>
<p>　　设定项目项目如下：</p>
<p></p>
<blockquote>
<p>DEVICE 接口名（设备,网卡）</p>
<p>BOOTPROTO IP的配置方法（static:固定IP， dhcpHCP， none:手动）</p>
<p>HWADDR MAC地址</p>
<p>ONBOOT 系统启动的时候网络接口是否有效（yes/no）</p>
<p>TYPE 网络类型（通常是Ethemet）</p>
<p>NETMASK 网络掩码</p>
<p><span style="color:#FF0000;"><strong>IPADDR</strong></span> IP地址</p>
<p>IPV6INIT IPV6是否有效（yes/no）</p>
<p>GATEWAY 默认网关IP地址</p>
</blockquote>
<p></p>
<p>　　查看"/etc/sysconfig/network-scripts/ifcfg-eth0"内容，如果IP不复核，就行修改。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427016763.png"></p>
<p></p>
<p>　　如果上图中IP与规划不相符，用下面命令进行修改：</p>
<p></p>
<blockquote>
<p>vim /etc/sysconfig/network-scripts/ifcgf-eth0</p>
</blockquote>
<p></p>
<p>　　修改完之后可以用"ifconfig"进行查看。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427013141.png"></p>
<p></p>
<p>　　<span style="color:rgb(51,102,255);"><strong>4）配置hosts文件（<span style="color:#FF0000;">必须<span style="color:rgb(51,102,255);">）
</span></span></strong></span></p>
<p>　　"<span style="color:#FF0000;"><strong>/etc/hosts</strong></span>"这个文件是用来配置主机将用的<span style="color:#FF0000;"><strong>DNS</strong></span>服务器信息，是记载LAN内接续的各主机的对应[HostName和IP]用的。当用户在进行网络连接时，首先查找该文件，寻找对应主机名（或域名）对应的IP地址。</p>
<p>　　我们要测试两台机器之间知否连通，一般用"ping 机器的IP"，如果想用"ping 机器的主机名"发现找不见该名称的机器，解决的办法就是修改"<span style="color:#FF0000;"><strong>/etc/hosts</strong></span>"这个文件，通过把LAN内的各主机的IP地址和HostName的<span style="color:#FF0000;"><strong>一一对应</strong></span>写入这个文件的时候，就可以解决问题。</p>
<p>　　例如：机器为"Master.Hadoop:192.168.1.2"对机器为"Salve1.Hadoop:192.168.1.3"用命令"ping"记性连接测试。测试结果如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427024122.png"></p>
<p></p>
<p>　　 从上图中的值，直接对IP地址进行测试，能够ping通，但是对主机名进行测试，发现没有ping通，提示"unknown host——未知主机"，这时查看"Master.Hadoop"的"/etc/hosts"文件内容。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427026597.png"></p>
<p></p>
<p>　　发现里面没有"192.168.1.3 Slave1.Hadoop"内容，故而本机器是无法对机器的主机名为"Slave1.Hadoop" 解析。</p>
<p>　　在进行<span style="color:#FF0000;"><strong>Hadoop集群</strong></span>配置中，需要在"/etc/hosts"文件中添加集群中所有机器的IP与主机名，这样Master与所有的Slave机器之间不仅可以通过IP进行通信，而且还可以通过主机名进行通信。所以在所有的机器上的"/etc/hosts"文件<span style="color:#FF0000;"><strong>末尾</strong></span>中都要添加如下内容：</p>
<p></p>
<blockquote>
<p>192.168.1.2 Master.Hadoop</p>
<p>192.168.1.3 Slave1.Hadoop</p>
<p>192.168.1.4 Slave2.Hadoop</p>
<p>192.168.1.5 Slave3.Hadoop</p>
</blockquote>
<p></p>
<p>　　用以下命令进行添加：</p>
<p></p>
<blockquote>
<p>vim /etc/hosts</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427037403.png"></p>
<p></p>
<p>　　添加结果如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427053236.png"></p>
<p></p>
<p>　　现在我们在进行对机器为"Slave1.Hadoop"的主机名进行ping通测试，看是否能测试成功。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427062298.png"></p>
<p></p>
<p>　　从上图中我们已经能用主机名进行ping通了，说明我们刚才添加的内容，在局域网内能进行DNS解析了，那么现在剩下的事儿就是在其余的Slave机器上进行相同的配置。然后进行测试。（<span style="color:#FF0000;"><strong>备注：</strong></span>当设置SSH无密码验证后，可以"scp"进行复制，然后把原来的"hosts"文件执行覆盖即可。）</p>
<p style="text-align:justify;"></p>
<h3>1.4 所需软件</h3>
<p><span style="color:rgb(51,102,255);"><strong>1）JDK软件 </strong></span></p>
<p>下载地址：<a href="http://www.oracle.com/technetwork/java/javase/index.html" rel="nofollow">http://www.oracle.com/technetwork/java/javase/index.html</a></p>
<p>JDK版本：<span style="color:#FF00FF;">jdk-6u31-linux-i586.bin</span></p>
<p><span style="color:rgb(51,102,255);"><strong>2）Hadoop软件 </strong></span></p>
<p>下载地址：<a href="http://hadoop.apache.org/common/releases.html" rel="nofollow">http://hadoop.apache.org/common/releases.html</a></p>
<p>Hadoop版本：<span style="color:#FF00FF;">hadoop-1.0.0.tar.gz</span></p>
<p style="text-align:justify;"></p>
<h3>1.5 VSFTP上传</h3>
<p>　　在"<span style="color:#FF0000;"><strong>Hadoop集群（第3期）</strong></span>"讲了VSFTP的安装及配置，如果没有安装VSFTP可以按照该文档进行安装。如果安装好了，就可以通过<span style="color:#0000FF;"><strong>FlashFXP.exe</strong></span>软件把我们下载的JDK6.0和Hadoop1.0软件上传到"<span style="color:#FF0000;"><strong>Master.Hadoop:192.168.1.2</strong></span>"服务器上。</p>
<p></p>
<p style="text-align:center;"><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/20120516142707562.png"></p>
<p style="text-align:center;"></p>
<p>　　刚才我们用一般用户（<span style="color:#FF00FF;">hadoop</span>）通过FlashFXP软件把所需的两个软件上传了跟目下，我们通过命令查看下一下是否已经上传了。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427074989.png"></p>
<p></p>
<p>从图中，我们的所需软件已经准备好了。</p>
<p></p>
<h2>2、SSH无密码验证配置</h2>
<p>　　Hadoop运行过程中需要管理远端Hadoop守护进程，在Hadoop启动以后，NameNode是通过SSH（Secure Shell）来启动和停止各个DataNode上的各种守护进程的。这就必须在节点之间执行指令的时候是不需要输入密码的形式，故我们需要配置SSH运用无密码公钥认证的形式，这样NameNode使用SSH无密码登录并启动DataName进程，同样原理，DataNode上也能使用SSH无密码登录到NameNode。</p>
<p style="text-align:justify;"></p>
<h3>2.1 安装和启动SSH协议</h3>
<p>　　在"Hadoop集群（第1期）"安装CentOS6.0时，我们选择了一些基本安装包，所以我们需要两个服务：ssh和rsync已经安装了。可以通过下面命令查看结果显示如下：</p>
<p></p>
<blockquote>
<p>rpm –qa | grep openssh</p>
<p>rpm –qa | grep rsync</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427083462.png"></p>
<p></p>
<p><span style="color:#FF0000;"><strong>　　假设</strong></span>没有安装ssh和rsync，可以通过下面命令进行安装。</p>
<p></p>
<blockquote>
<p>yum install ssh 安装SSH协议</p>
<p>yum install rsync （rsync是一个远程数据同步工具，可通过LAN/WAN快速同步多台主机间的文件）</p>
<p>service sshd restart 启动服务</p>
</blockquote>
<p></p>
<p>　　确保所有的服务器都安装，上面命令执行完毕，各台机器之间可以通过密码验证相互登。</p>
<p style="text-align:justify;"></p>
<h3>2.2 配置Master无密码登录所有Salve</h3>
<p><span style="color:rgb(51,102,255);"><strong>　　1）SSH无密码原理 </strong></span></p>
<p>　　Master（NameNode | JobTracker）作为客户端，要实现无密码公钥认证，连接到服务器Salve（DataNode | Tasktracker）上时，需要在Master上生成一个密钥对，包括一个公钥和一个私钥，而后将公钥复制到所有的Slave上。当Master通过SSH连接Salve时，Salve就会生成一个随机数并用Master的公钥对随机数进行加密，并发送给Master。Master收到加密数之后再用私钥解密，并将解密数回传给Slave，Slave确认解密数无误之后就允许Master进行连接了。这就是一个公钥认证过程，其间不需要用户手工输入密码。重要过程是将客户端Master复制到Slave上。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）Master机器上生成密码对 </strong></span></p>
<p>　　 在Master节点上执行以下命令：</p>
<p></p>
<blockquote>
<p>ssh-keygen –t rsa –P ''</p>
</blockquote>
<p></p>
<p>　　这条命是生成其<span style="color:#FF0000;"><strong>无密码密钥对</strong></span>，询问其保存路径时<span style="color:#FF0000;"><strong>直接回车</strong></span>采用默认路径。生成的密钥对：id_rsa和id_rsa.pub，默认存储在"<span style="color:#FF0000;"><strong>/home/hadoop/.ssh</strong></span>"目录下。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427096984.png"></p>
<p></p>
<p>　　查看"/home/hadoop/"下是否有".ssh"文件夹，且".ssh"文件下是否有两个刚生产的无密码密钥对。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427098903.png"></p>
<p></p>
<p>　　接着在Master节点上做如下配置，把id_rsa.pub追加到授权的key里面去。</p>
<p></p>
<blockquote>
<p>cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427107233.png"></p>
<p></p>
<p>　　在验证前，需要做两件事儿。第一件事儿是修改文件"<span style="color:#0000FF;"><strong>authorized_keys</strong></span>"权限（<span style="color:#FF0000;"><strong>权限的设置非常重要，因为不安全的设置安全设置，会让你不能使用RSA功能</strong></span>），另一件事儿是用<span style="color:#FF00FF;">root</span>用户设置"<span style="color:#FF0000;"><strong>/etc/ssh/sshd_config</strong></span>"的内容。使其无密码登录有效。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）修改文件"<span style="color:#0000FF;">authorized_keys<span style="color:rgb(51,102,255);">"
</span></span></strong></span></p>
<p></p>
<blockquote>
<p>chmod 600 ~/.ssh/authorized_keys</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427111246.png"></p>
<p></p>
<p>　　 <span style="color:#FF0000;"><strong>备注：</strong></span>如果不进行设置，在验证时，扔提示你输入密码，在这里花费了将近半天时间来查找原因。在网上查到了几篇不错的文章，把作为"<span style="color:#FF0000;"><strong>Hadoop集群_第5期副刊_JDK和SSH无密码配置</strong></span>"来帮助额外学习之用。</p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>2）设置SSH配置 </strong></span></p>
<p>　　用<span style="color:#FF0000;"><strong>root</strong></span>用户登录服务器修改SSH配置文件"<span style="color:#FF00FF;">/etc/ssh/sshd_config</span>"的下列内容。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427114593.png"></p>
<p></p>
<blockquote>
<p>RSAAuthentication yes # 启用 RSA 认证</p>
<p>PubkeyAuthentication yes # 启用公钥私钥配对认证方式</p>
<p>AuthorizedKeysFile .ssh/authorized_keys # 公钥文件路径（和上面生成的文件同）</p>
</blockquote>
<p></p>
<p>　　设置完之后记得<span style="color:#FF0000;"><strong>重启SSH服务</strong></span>，才能使刚才设置有效。</p>
<p></p>
<blockquote>
<p>service sshd restart</p>
</blockquote>
<p></p>
<p><span style="color:#FF0000;"><strong>　　退出root登录</strong></span>，使用<span style="color:#0000FF;"><strong>hadoop</strong></span>普通用户验证是否成功。</p>
<p></p>
<blockquote>
<p>ssh localhost</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427125574.png"></p>
<p></p>
<p>　　从上图中得知无密码登录本级已经设置完毕，接下来的事儿是把<span style="color:#FF0000;"><strong>公钥</strong></span>复制<span style="color:#0000FF;"><strong>所有</strong></span>的<span style="color:#FF00FF;">Slave</span>机器上。使用下面的命令格式进行复制公钥：</p>
<p></p>
<blockquote>
<p>scp ~/.ssh/id_rsa.pub 远程用户名@远程服务器IP:~/</p>
</blockquote>
<p></p>
<p>　　例如：</p>
<p></p>
<blockquote>
<p>scp ~/.ssh/id_rsa.pub hadoop@192.168.1.3:~/</p>
</blockquote>
<p></p>
<p>　　上面的命令是<span style="color:#FF0000;"><strong>复制</strong></span>文件"<span style="color:#FF0000;"><strong>id_rsa.pub</strong></span>"到服务器IP为"<span style="color:#FF0000;"><strong>192.168.1.3</strong></span>"的用户为"<span style="color:#FF0000;"><strong>hadoop</strong></span>"的"<span style="color:#FF0000;"><strong>/home/hadoop/</strong></span>"下面。</p>
<p></p>
<p>　　下面就针对IP为"192.168.1.3"的Slave1.Hadoop的节点进行配置。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）把Master.Hadoop上的公钥复制到Slave1.Hadoop上</strong></span></p>
<p><span style="color:rgb(51,102,255);"><strong></strong></span></p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427131048.png"></p>
<p></p>
<p>　　从上图中我们得知，已经把文件"id_rsa.pub"传过去了，因为并没有建立起无密码连接，所以在连接时，仍然要提示输入输入Slave1.Hadoop服务器用户hadoop的密码。为了确保确实已经把文件传过去了，用SecureCRT登录Slave1.Hadoop:192.168.1.3服务器，查看"/home/hadoop/"下是否存在这个文件。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427135475.png"></p>
<p></p>
<p>　　从上面得知我们已经成功把公钥复制过去了。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）在"/home/hadoop/"下创建".ssh"文件夹 </strong>
</span></p>
<p>　　这一步<span style="color:#FF0000;"><strong>并不是必须</strong></span>的，如果在Slave1.Hadoop的"/home/hadoop"<span style="color:#FF0000;"><strong>已经存在</strong></span>就不需要创建了，因为我们之前并没有对Slave机器做过无密码登录配置，所以该文件是不存在的。用下面命令进行创建。（<span style="color:#FF0000;"><strong>备注：</strong></span>用hadoop登录系统，如果不涉及系统文件修改，一般情况下都是用我们之前建立的普通用户hadoop进行执行命令。）</p>
<p></p>
<blockquote>
<p>mkdir ~/.ssh</p>
</blockquote>
<p></p>
<p>　　然后是修改文件夹"<span style="color:#FF0000;"><strong>.ssh</strong></span>"的用户权限，把他的权限修改为"<span style="color:#FF0000;"><strong>700</strong></span>"，用下面命令执行：</p>
<p></p>
<blockquote>
<p>chmod 700 ~/.ssh</p>
</blockquote>
<p></p>
<p><span style="color:#FF0000;"><strong>　　备注：</strong></span>如果不进行，即使你按照前面的操作设置了"authorized_keys"权限，并配置了"/etc/ssh/sshd_config"，还重启了sshd服务，在Master能用"ssh localhost"进行无密码登录，但是对Slave1.Hadoop进行登录仍然需要输入密码，就是因为".ssh"文件夹的权限设置不对。这个文件夹".ssh"在配置SSH无密码登录时系统自动生成时，权限自动为"700"，如果是自己手动创建，它的组权限和其他权限都有，这样就会导致RSA无密码远程登录失败。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427151656.png"></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427169114.png"></p>
<p></p>
<p>　　对比上面两张图，发现文件夹".ssh"权限已经变了。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　3）追加到授权文件"<span style="color:#0000FF;">authorized_keys<span style="color:rgb(51,102,255);">"
</span></span></strong></span></p>
<p>　　到目前为止Master.Hadoop的公钥也有了，文件夹".ssh"也有了，且权限也修改了。这一步就是把Master.Hadoop的公钥<span style="color:#FF0000;"><strong>追加</strong></span>到Slave1.Hadoop的授权文件"authorized_keys"中去。使用下面命令进行追加并修改"authorized_keys"文件权限：</p>
<p></p>
<blockquote>
<p>cat ~/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
<p>chmod 600 ~/.ssh/authorized_keys</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427169605.png"></p>
<p><span style="color:rgb(51,102,255);"><strong>　　4）用root用户修改"/etc/ssh/sshd_config"</strong></span></p>
<p><span style="color:rgb(51,102,255);"><strong>　　</strong></span>具体步骤参考前面Master.Hadoop的"<span style="color:#FF0000;"><strong>设置SSH配置</strong></span>"，具体分为两步：第1是修改配置文件；第2是重启SSH服务。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　5）用Master.Hadoop使用SSH无密码登录Slave1.Hadoop
</strong></span></p>
<p>　　当前面的步骤设置完毕，就可以使用下面命令格式进行SSH无密码登录了。</p>
<p></p>
<blockquote>
<p>ssh 远程服务器IP</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427163160.png"></p>
<p></p>
<p>　　从上图我们主要3个地方，第1个就是SSH无密码登录命令，第2、3个就是登录前后"<span style="color:#FF0000;"><strong>@</strong></span>"后面的<span style="color:#FF0000;"><strong>机器名</strong></span>变了，由"<span style="color:#FF0000;"><strong>Master</strong></span>"变为了"<span style="color:#0000FF;"><strong>Slave1</strong></span>"，这就说明我们已经成功实现了SSH无密码登录了。</p>
<p>　　最后记得把"/home/hadoop/"目录下的"id_rsa.pub"文件删除掉。</p>
<p></p>
<blockquote>
<p>rm –r ~/id_rsa.pub</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427175079.png"></p>
<p></p>
<p>　　到此为止，我们经过前5步已经实现了从"<span style="color:#0000FF;">Master</span>.Hadoop"到"<span style="color:#FF00FF;">Slave1</span>.Hadoop"SSH无密码登录，下面就是重复上面的步骤把剩余的两台（<span style="color:#FF00FF;">Slave2</span>.Hadoop和<span style="color:#FF00FF;">Slave3</span>.Hadoop）Slave服务器进行配置。<span style="color:#FF0000;"><strong>这样</strong></span>，我们就完成了"配置<span style="color:#0000FF;">Master</span>无密码登录所有的<span style="color:#FF00FF;">Slave</span>服务器"。</p>
<p style="text-align:justify;"></p>
<h3>2.3 配置所有Slave无密码登录Master</h3>
<p>　　和Master无密码登录所有Slave原理一样，就是把Slave的公钥<span style="color:#FF0000;"><strong>追加</strong></span>到Master的".ssh"文件夹下的"authorized_keys"中，记得是<span style="color:#FF0000;"><strong>追加（&gt;&gt;）</strong></span>。</p>
<p>　　为了说明情况，我们现在就以"Slave1.Hadoop"无密码登录"Master.Hadoop"为例，进行一遍操作，也算是<span style="color:#0000FF;"><strong>巩固</strong></span>一下前面所学知识，剩余的"Slave2.Hadoop"和"Slave3.Hadoop"就按照这个示例进行就可以了。</p>
<p>　　首先创建"Slave1.Hadoop"自己的公钥和私钥，并把自己的公钥追加到"authorized_keys"文件中。用到的命令如下：</p>
<p></p>
<blockquote>
<p>ssh-keygen –t rsa –P ''</p>
<p>cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427225107.png"></p>
<p></p>
<p>　　接着是用命令"<span style="color:#FF0000;"><strong>scp</strong></span>"复制"Slave1.Hadoop"的公钥"id_rsa.pub"到"Master.Hadoop"的"/home/hadoop/"目录下，并<span style="color:#FF0000;"><strong>追加</strong></span>到"Master.Hadoop"的"authorized_keys"中。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）在"Slave1.Hadoop"服务器的操作 </strong>
</span></p>
<p>　　用到的命令如下：</p>
<p></p>
<blockquote>
<p>scp ~/.ssh/id_rsa.pub hadoop@192.168.1.2:~/</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/20120516142723547.png"></p>
<p><span style="color:rgb(51,102,255);"><strong>　　</strong></span></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）在"Master.Hadoop"服务器的操作 </strong>
</span></p>
<p>　　用到的命令如下：</p>
<p></p>
<blockquote>
<p>cat ~/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427239402.png"></p>
<p></p>
<p>　　然后删除掉刚才复制过来的"id_rsa.pub"文件。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427241320.png"></p>
<p></p>
<p>　　最后是测试从"Slave1.Hadoop"到"Master.Hadoop"无密码登录。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427242924.png"></p>
<p></p>
<p>　　从上面结果中可以看到已经成功实现了，再试下从"Master.Hadoop"到"Slave1.Hadoop"无密码登录。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427249303.png"></p>
<p></p>
<p>　　至此"Master.Hadoop"与"Slave1.Hadoop"之间可以互相无密码登录了，剩下的就是按照上面的步骤把剩余的"Slave2.Hadoop"和"Slave3.Hadoop"与"Master.Hadoop"之间建立起无密码登录。这样，Master能无密码验证登录每个Slave，每个Slave也能无密码验证登录到Master。</p>
<p style="text-align:justify;"></p>
<h2>3、Java环境安装</h2>
<p>　　所有的机器上都要安装JDK，现在就先在Master服务器安装，然后其他服务器按照步骤重复进行即可。安装JDK以及配置环境变量，需要以"<span style="color:#FF0000;"><strong>root</strong></span>"的身份进行。</p>
<p style="text-align:justify;"></p>
<h3>3.1 安装JDK</h3>
<p>　　首先用<span style="color:#FF0000;"><strong>root</strong></span>身份登录"Master.Hadoop"后在"/usr"下创建"java"文件夹，再把用FTP上传到"/home/hadoop/"下的"jdk-6u31-linux-i586.bin"复制到"/usr/java"文件夹中。</p>
<p></p>
<blockquote>
<p>mkdir /usr/java</p>
<p>cp /home/hadoop/ jdk-6u31-linux-i586.bin /usr/java</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427261646.png"></p>
<p></p>
<p>　　接着<span style="color:#FF0000;"><strong>进入</strong></span>"<span style="color:#0000FF;"><strong>/usr/java</strong></span>"目录<span style="color:#FF0000;"><strong>下</strong></span>通过下面命令使其JDK获得可执行权限，并安装JDK。</p>
<p></p>
<blockquote>
<p>chmod +x jdk-6u31-linux-i586.bin</p>
<p>./jdk-6u31-linux-i586.bin</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427267992.png"></p>
<p></p>
<p>　　按照上面几步进行操作，最后点击"<span style="color:#FF0000;"><strong>Enter</strong></span>"键开始安装，安装完会提示你按"<span style="color:#FF0000;"><strong>Enter</strong></span>"键退出，然后查看"<span style="color:#0000FF;"><strong>/usr/java</strong></span>"下面会发现多了一个名为"<span style="color:#FF0000;"><strong>jdk1.6.0_31</strong></span>"文件夹，说明我们的JDK安装结束，删除"jdk-6u31-linux-i586.bin"文件，进入下一个"配置环境变量"环节。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427272070.png"></p>
<p></p>
<h3>3.2 配置环境变量</h3>
<p>　　编辑"/etc/profile"文件，在后面添加Java的"JAVA_HOME"、"CLASSPATH"以及"PATH"内容。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）编辑"/etc/profile"文件 </strong></span></p>
<p></p>
<blockquote>
<p>vim /etc/profile</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427277054.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）添加Java环境变量 </strong></span></p>
<p>　　在"<span style="color:#FF0000;"><strong>/etc/profile</strong></span>"文件的<span style="color:#FF0000;"><strong>尾部</strong></span>添加以下内容：</p>
<p></p>
<blockquote>
<p># set java environment</p>
<p>export JAVA_HOME=/usr/java/jdk1.6.0_31/</p>
<p>export JRE_HOME=/usr/java/jdk1.6.0_31/jre</p>
<p>export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JRE_HOME/lib</p>
<p>export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin</p>
</blockquote>
<p></p>
<p>　　或者</p>
<p></p>
<blockquote>
<p># set java environment</p>
<p>export JAVA_HOME=/usr/java/jdk1.6.0_31</p>
<p>export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib</p>
<p>export PATH=$PATH:$JAVA_HOME/bin:$JAVA_HOME/jre/bin</p>
</blockquote>
<p></p>
<p>　　 以上两种意思一样，那么我们就选择<span style="color:#FF0000;"><strong>第2种</strong></span>来进行设置。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427295776.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　3）使配置生效 </strong></span></p>
<p>　　保存并退出，执行下面命令使其配置立即生效。</p>
<p></p>
<blockquote>
<p>source /etc/profile</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427296822.png"></p>
<p style="text-align:justify;"></p>
<h3>3.3 验证安装成功</h3>
<p>　　配置完毕并生效后，用下面命令判断是否成功。</p>
<p></p>
<blockquote>
<p>java -version</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427309821.png"></p>
<p></p>
<p>　　从上图中得知，我们以确定JDK已经安装成功。</p>
<p style="text-align:justify;"></p>
<h3>3.4 安装剩余机器</h3>
<p>　　这时用<span style="color:#FF0000;"><strong>普通用户hadoop</strong></span>通过下面命令格式把"Master.Hadoop"文件夹"/home/hadoop/"的JDK复制到其他Slave的"/home/hadoop/"下面，剩下的事儿就是在其余的Slave服务器上按照上图的步骤安装JDK。</p>
<p></p>
<blockquote>
<p>scp /home/hadoop/jdk-6u31-linux-i586.bin 远程用户名@远程服务器IP:~/</p>
</blockquote>
<p></p>
<p>　　或者</p>
<p></p>
<blockquote>
<p>scp ~/jdk-6u31-linux-i586.bin 远程用户名@远程服务器IP:~/</p>
</blockquote>
<p></p>
<p><span style="color:#FF0000;"><strong>　　备注：</strong></span>"<span style="color:#FF0000;"><strong>~</strong></span>"代表<span style="color:#FF0000;"><strong>当前</strong></span>用户的主目录，当<span style="color:#FF0000;"><strong>前用户为hadoop</strong></span>，所以"<span style="color:#FF0000;"><strong>~</strong></span>"代表"<span style="color:#0000FF;"><strong>/home/hadoop</strong></span>"。</p>
<p><span style="color:#0000FF;"><strong>　　例如：</strong></span>把JDK从"Master.Hadoop"复制到"Slave1.Hadoop"的命令如下。</p>
<p></p>
<blockquote>
<p>scp ~/jdk-6u31-linux-i586 hadoop@192.168.1.3:~/</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427305087.png"></p>
<p></p>
<p>　　然后查看"Slave1.Hadoop"的"/home/hadoop"查看是否已经复制成功了。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427303102.png"></p>
<p></p>
<p>　　从上图中得知，我们已经成功复制了，现在我们就用<span style="color:#0000FF;"><strong>最高权限用户<span style="color:#FF0000;">root</span></strong>进行安</span>装了。其他的与这个一样。</p>
<p style="text-align:justify;"></p>
<h2>4、Hadoop集群安装</h2>
<p>　　所有的机器上都要安装hadoop，现在就先在Master服务器安装，然后其他服务器按照步骤重复进行即可。安装和配置hadoop需要以"<span style="color:#FF0000;"><strong>root</strong></span>"的身份进行。</p>
<p style="text-align:justify;"></p>
<h3>4.1 安装hadoop</h3>
<p>　　首先用<span style="color:#FF0000;"><strong>root</strong></span>用户登录"Master.Hadoop"机器，查看我们之前用FTP上传至"/home/Hadoop"上传的"<span style="color:#FF0000;"><strong>hadoop-1.0.0.tar.gz</strong></span>"。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427316101.png"></p>
<p></p>
<p>　　接着把"<span style="color:#FF0000;"><strong>hadoop-1.0.0.tar.gz</strong></span>"复制到"/usr"目录下面。</p>
<p></p>
<blockquote>
<p>cp /home/hadoop/hadoop-1.0.0.tar.gz /usr</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427319100.png"></p>
<p></p>
<p>　　下一步进入"/usr"目录下，用下面命令把"<span style="color:#FF0000;"><strong>hadoop-1.0.0.tar.gz</strong></span>"进行解压，并将其命名为"hadoop"，把该文件夹的<span style="color:#FF0000;"><strong>读权限</strong></span>分配给普通用户<span style="color:#0000FF;"><strong>hadoop</strong></span>，然后删除"<span style="color:#FF0000;"><strong>hadoop-1.0.0.tar.gz</strong></span>"安装包。</p>
<p></p>
<blockquote>
<p>cd /usr #进入"/usr"目录</p>
<p>tar –zxvf hadoop-1.0.0.tar.gz #解压"<span style="color:#FF0000;"><strong>hadoop-1.0.0.tar.gz</strong></span>"安装包</p>
<p>mv hadoop-1.0.0 hadoop #将"hadoop-1.0.0"文件夹<span style="color:#FF0000;"><strong>重命名</strong></span>"hadoop"</p>
<p>chown <span style="color:#FF0000;"><strong>–R</strong></span> hadoop:hadoop hadoop #<span style="color:#FF0000;"><strong>将文件夹"hadoop"读权限分配给hadoop用户</strong></span></p>
<p>rm –rf hadoop-1.0.0.tar.gz #删除"<span style="color:#FF0000;"><strong>hadoop-1.0.0.tar.gz</strong></span>"安装包</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427325445.png"></p>
<p></p>
<p>　　解压后，并重命名。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427353753.png"></p>
<p></p>
<p>　　把"/usr/hadoop"<span style="color:#FF0000;"><strong>读权</strong></span>限分配给<span style="color:#FF0000;"><strong>hadoop</strong></span>用户（<span style="color:#0000FF;"><strong>非常<span style="color:#FF0000;">重要</span></strong>）</span></p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427366652.png"></p>
<p></p>
<p>　　删除"<span style="color:#FF0000;"><strong>hadoop-1.0.0.tar.gz</strong></span>"安装包</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427360.png"></p>
<p></p>
<p>　　最后在"<span style="color:#FF0000;"><strong>/usr/hadoop</strong></span>"下面创建<span style="color:#FF0000;"><strong>tmp</strong></span>文件夹，把Hadoop的安装路径添加到"<span style="color:#0000FF;"><strong>/etc/profile</strong></span>"中，修改"/etc/profile"文件（配置java环境变量的文件），将以下语句添加到<span style="color:#FF0000;"><strong>末尾</strong></span>，并使其有效：</p>
<p></p>
<blockquote>
<p># set hadoop path</p>
<p>export HADOOP_HOME=/usr/hadoop</p>
<p>export PATH=$PATH :$HADOOP_HOME/bin</p>
</blockquote>
<p></p>
<p><span style="color:rgb(51,102,255);"><span style="font-weight:bold;">1）在"<span style="color:#FF0000;">/usr/hadoop<span style="color:rgb(51,102,255);">"创建"<span style="color:#FF0000;">tmp<span style="color:rgb(51,102,255);">"文件夹
</span></span></span></span></span></span></p>
<p></p>
<blockquote>
<p>mkdir /usr/hadoop/tmp</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427369967.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>2）配置"/etc/profile" </strong></span></p>
<p></p>
<blockquote>
<p>vim /etc/profile</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427372126.png"></p>
<p></p>
<p>　　配置后的文件如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427385341.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>3）重启"/etc/profile" </strong></span></p>
<p></p>
<blockquote>
<p>source /etc/profile</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427387260.png"></p>
<p style="text-align:justify;"></p>
<h3>4.2 配置hadoop</h3>
<p><span style="color:rgb(51,102,255);"><strong>　　1）配置hadoop-env.sh </strong></span></p>
<p>　　该"<span style="color:#FF0000;"><strong>hadoop-env.sh</strong></span>"文件位于"<span style="color:#0000FF;"><strong>/usr/hadoop/conf</strong></span>"目录下。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427394403.png"></p>
<p></p>
<p>　　在文件的末尾添加下面内容。</p>
<p></p>
<blockquote>
<p># set java environment</p>
<p>export JAVA_HOME=/usr/java/jdk1.6.0_31</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427398274.png"></p>
<p></p>
<p>　　Hadoop配置文件在conf目录下，之前的版本的配置文件主要是Hadoop-default.xml和Hadoop-site.xml。由于Hadoop发展迅速，代码量急剧增加，代码开发分为了core，hdfs和map/reduce三部分，配置文件也被分成了三个core-site.xml、hdfs-site.xml、mapred-site.xml。core-site.xml和hdfs-site.xml是站在HDFS角度上配置文件；core-site.xml和mapred-site.xml是站在MapReduce角度上配置文件。</p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）配置<span style="color:#FF0000;">core-site.xml<span style="color:rgb(51,102,255);">文件
</span></span></strong></span></p>
<p>　　修改Hadoop核心配置文件core-site.xml，这里配置的是HDFS的地址和端口号。</p>
<p></p>
<blockquote>
<p>&lt;configuration&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;</p>
<p>&lt;value&gt;/usr/hadoop/tmp&lt;/value&gt;</p>
<p>（<span style="color:#FF0000;"><strong>备注：</strong></span>请先在 /usr/hadoop 目录下建立 tmp 文件夹）</p>
<p>&lt;description&gt;A base for other temporary directories.&lt;/description&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;!-- file system properties --&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;fs.default.name&lt;/name&gt;</p>
<p>&lt;value&gt;hdfs://<span style="color:#FF0000;"><strong>192.168.1.2</strong></span>:<span style="color:#0000FF;"><strong>9000</strong></span>&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;/configuration&gt;</p>
</blockquote>
<p></p>
<p><span style="color:#FF0000;"><strong>　　备注：</strong></span>如没有配置hadoop.tmp.dir参数，此时系统默认的临时目录为：/tmp/hadoo-hadoop。而这个目录在每次重启后都会被干掉，必须重新执行format才行，否则会出错。</p>
<p>用下面命令进行编辑：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427393258.png"></p>
<p></p>
<p>编辑结果显示如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427407095.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　3）配置<span style="color:#FF0000;">hdfs-site.xml<span style="color:rgb(51,102,255);">文件
</span></span></strong></span></p>
<p>　　修改Hadoop中HDFS的配置，配置的备份方式默认为3。</p>
<p></p>
<blockquote>
<p>&lt;configuration&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;dfs.replication&lt;/name&gt;</p>
<p>&lt;value&gt;<span style="color:#FF0000;"><strong>1</strong></span>&lt;/value&gt;</p>
<p>(<span style="color:#FF0000;"><strong>备注：</strong></span>replication 是数据副本数量，默认为3，salve少于3台就会报错)</p>
<p>&lt;/property&gt;</p>
<p>&lt;configuration&gt;</p>
</blockquote>
<p></p>
<p>　　用下面命令进行编辑：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427403158.png"></p>
<p></p>
<p>　　编辑结果显示如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427413092.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　4）配置<span style="color:#FF0000;">mapred-site.xml<span style="color:rgb(51,102,255);">文件
</span></span></strong></span></p>
<p>　　修改Hadoop中MapReduce的配置文件，配置的是JobTracker的地址和端口。</p>
<p></p>
<blockquote>
<p>&lt;configuration&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;mapred.job.tracker&lt;/name&gt;</p>
<p>&lt;value&gt;http://<span style="color:#FF0000;"><strong>192.168.1.2</strong></span>:<span style="color:#0000FF;"><strong>9001</strong></span>&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;/configuration&gt;</p>
</blockquote>
<p></p>
<p>　　用下面命令进行编辑：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427414488.png"></p>
<p></p>
<p>　　编辑结果显示如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427411075.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　5）配置masters文件 </strong></span></p>
<p>　　有两种方案：</p>
<p>（1）第一种</p>
<p>修改localhost为Master.Hadoop</p>
<p>（2）第二种</p>
<p>去掉"localhost"，加入Master机器的IP：192.168.1.2</p>
<p>　　为保险起见，启用第二种，因为万一忘记配置"/etc/hosts"局域网的DNS失效，这样就会出现意想不到的错误，但是一旦IP配对，网络畅通，就能通过IP找到相应主机。</p>
<p>　　用下面命令进行修改：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427412470.png"></p>
<p></p>
<p>　　编辑结果显示如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427423484.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>6）配置slaves文件（<span style="color:#FF0000;">Master主机特有</span></strong>）</span></p>
<p>有两种方案：</p>
<p>（1）第一种</p>
<p style="margin-left:21pt;">去掉"localhost"，每行只添加一个主机名，把剩余的Slave主机名都填上。</p>
<p>例如：添加形式如下</p>
<p></p>
<blockquote>
<p>Slave1.Hadoop</p>
<p>Slave2.Hadoop</p>
<p>Slave3.Hadoop</p>
</blockquote>
<p></p>
<p>（2）第二种</p>
<p>去掉"localhost"，加入集群中所有Slave机器的IP，也是每行一个。</p>
<p>例如：添加形式如下</p>
<p></p>
<blockquote>
<p>192.168.1.3</p>
<p>192.168.1.4</p>
<p>192.168.1.5</p>
</blockquote>
<p></p>
<p>　　原因和添加"masters"文件一样，选择第二种方式。</p>
<p>　　用下面命令进行修改：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427424879.png"></p>
<p></p>
<p>　　编辑结果如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/20120516142743942.png"></p>
<p></p>
<p>　　现在在Master机器上的Hadoop配置就结束了，剩下的就是配置Slave机器上的Hadoop。</p>
<p></p>
<p><span style="color:#FF0000;"><strong>　　一种方式</strong></span>是按照上面的步骤，把Hadoop的安装包在用<span style="color:#0000FF;"><strong>普通用户<span style="color:#FF0000;">hadoop</span></strong>通过"<span style="color:#FF0000;"><strong>scp</strong></span>"复制到其他机器的"</span>/home/hadoop"目录下，然后根据实际情况进行安装配置，<span style="color:#FF00FF;"><strong>除了第6步，那是Master特有的</strong></span>。用下面命令格式进行。（<span style="color:#FF0000;"><strong>备注：</strong></span>此时切换到普通用户hadoop）</p>
<p></p>
<blockquote>
<p>scp ~/hadoop-1.0.0.tar.gz hadoop@服务器IP:~/</p>
</blockquote>
<p></p>
<p>例如：从"Master.Hadoop"到"Slave1.Hadoop"复制Hadoop的安装包。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427436449.png"></p>
<p></p>
<p><span style="color:#FF0000;"><strong>另一种方式</strong></span>是将 Master上配置好的hadoop所在文件夹"<span style="color:#0000FF;"><strong>/usr/<span style="color:#FF0000;">hadoop</span></strong>"复制到所有的</span>Slave的"/usr"目录下（实际上Slave机器上的slavers文件是不必要的， 复制了也没问题）。用下面命令格式进行。（<span style="color:#FF0000;"><strong>备注：</strong></span>此时用户可以为hadoop也可以为root）</p>
<p></p>
<blockquote>
<p>scp <span style="color:#FF0000;"><strong>-r</strong></span> /usr/hadoop <span style="color:#FF0000;">
<strong>root</strong></span>@服务器IP:/usr/</p>
</blockquote>
<p></p>
<p>例如：从"Master.Hadoop"到"Slave1.Hadoop"复制配置Hadoop的文件。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427437812.png"></p>
<p></p>
<p>上图中以root用户进行复制，当然不管是用户root还是hadoop，虽然Master机器上的"/usr/hadoop"文件夹用户hadoop有权限，但是Slave1上的hadoop用户却没有"/usr"权限，所以没有创建文件夹的权限。所以无论是哪个用户进行拷贝，右面都是"root@机器IP"格式。因为我们只是建立起了hadoop用户的SSH无密码连接，所以用root进行"scp"时，扔提示让你输入"Slave1.Hadoop"服务器用户root的密码。</p>
<p>查看"Slave1.Hadoop"服务器的"/usr"目录下是否已经存在"hadoop"文件夹，确认已经复制成功。查看结果如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427447746.png"></p>
<p></p>
<p>　　从上图中知道，hadoop文件夹确实已经复制了，但是我们发现hadoop权限是root，所以我们现在要给"Slave1.Hadoop"服务器上的用户hadoop添加对"/usr/hadoop"读权限。</p>
<p>　　以<span style="color:#FF0000;"><strong>root</strong></span>用户登录"Slave1.Hadoop"，执行下面命令。</p>
<p></p>
<blockquote>
<p>chown <span style="color:#FF0000;"><strong>-R</strong></span> hadoop:hadoop（<span style="color:#FF00FF;"><strong>用户名：用户组</strong></span>） hadoop（<span style="color:#0000FF;"><strong>文件夹</strong></span>）</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427452630.png"></p>
<p>　　</p>
<p>　　接着在"Slave1 .Hadoop"上修改"/etc/profile"文件（配置 java 环境变量的文件），将以下语句添加到末尾，并使其有效（source /etc/profile）：</p>
<p></p>
<blockquote>
<p># set hadoop environment</p>
<p>export HADOOP_HOME=/usr/hadoop</p>
<p>export PATH=$PATH :$HADOOP_HOME/bin</p>
</blockquote>
<p></p>
<p>　　如果不知道怎么设置，可以查看前面"Master.Hadoop"机器的"/etc/profile"文件的配置，到此为此在一台Slave机器上的Hadoop配置就结束了。剩下的事儿就是照葫芦画瓢把剩余的几台Slave机器按照《<span style="color:#FF00FF;"><strong>从"Master.Hadoop"到"Slave1.Hadoop"复制Hadoop的安装包。</strong></span>》这个例子进行部署Hadoop。</p>
<p style="text-align:justify;"></p>
<h3>4.3 启动及验证</h3>
<p><span style="color:rgb(51,102,255);"><strong>　　1）格式化<span style="color:#FF00FF;">HDFS<span style="color:rgb(51,102,255);">文件系统
</span></span></strong></span></p>
<p>　　在"Master.Hadoop"上使用<span style="color:#FF0000;"><strong>普通</strong></span>用户<span style="color:#FF0000;"><strong>hadoop</strong></span>进行操作。（<span style="color:#FF0000;"><strong>备注：</strong></span>只需一次，下次启动不再需要格式化，只需 start-all.sh）</p>
<p></p>
<blockquote>
<p>hadoop namenode -format</p>
</blockquote>
<p></p>
<p>　　某些书上和网上的某些资料中用下面命令执行。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427451517.png"></p>
<p></p>
<p>　　我们在看好多文档包括有些书上，按照他们的hadoop环境变量进行配置后，并立即使其生效，但是执行发现没有找见"bin/hadoop"这个命令。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427466991.png"></p>
<p></p>
<p>　　其实我们会发现我们的环境变量配置的是"<span style="color:#0000FF;"><strong>$HADOOP_HOME/<span style="color:#FF0000;">bin</span></strong>"，我们已经把</span>bin包含进入了，所以执行时，加上"bin"反而找不到该命令，除非我们的hadoop坏境变量如下设置。</p>
<p></p>
<blockquote>
<p># set hadoop path</p>
<p>export HADOOP_HOME=/usr/hadoop</p>
<p>export PATH=$PATH :<span style="color:#FF00FF;"><strong> $HADOOP_HOME </strong>
</span>:<span style="color:#0000FF;"><strong>$HADOOP_HOME/bin </strong></span></p>
</blockquote>
<p></p>
<p>　　这样就能直接使用"bin/hadoop"也可以直接使用"hadoop"，现在不管哪种情况，hadoop命令都能找见了。我们也没有必要重新在设置hadoop环境变量了，只需要记住执行Hadoop命令时不需要在前面加"bin"就可以了。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427467515.png"></p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427479367.png"></p>
<p></p>
<p>　　从上图中知道我们已经成功格式话了，但是美中不足就是出现了一个<span style="color:#FF0000;"><strong>警告</strong></span>，从网上的得知这个警告并不影响hadoop执行，但是也有办法解决，详情看后面的"常见问题FAQ"。</p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）启动hadoop </strong></span></p>
<p>　　在启动前关闭集群中所有机器的防火墙，不然会出现datanode开后又自动关闭。</p>
<p></p>
<blockquote>
<p>service iptables stop</p>
</blockquote>
<p></p>
<p>　　使用下面命令启动。</p>
<p></p>
<blockquote>
<p>start-all.sh</p>
</blockquote>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427479891.png"></p>
<p></p>
<p>　　执行结果如下：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427483761.png"></p>
<p></p>
<p>　　可以通过以下启动日志看出，首先启动namenode 接着启动datanode1，datanode2，…，然后启动secondarynamenode。再启动jobtracker，然后启动tasktracker1，tasktracker2，…。</p>
<p>　　启动 hadoop成功后，在 Master 中的 tmp 文件夹中生成了 dfs 文件夹，在Slave 中的 tmp 文件夹中均生成了 dfs 文件夹和 mapred 文件夹。</p>
<p>　　查看Master中"/usr/hadoop/tmp"文件夹内容</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427495789.png"></p>
<p></p>
<p>　　查看Slave1中"/usr/hadoop/tmp"文件夹内容。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/20120516142750183.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　3）验证hadoop </strong></span></p>
<p>　　（1）验证方法一：用"jps"命令</p>
<p>　　在Master上用 java自带的小工具<span style="color:#FF0000;"><strong>jps</strong></span>查看进程。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427502658.png"></p>
<p></p>
<p>　　在Slave1上用jps查看进程。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427509594.png"></p>
<p></p>
<p>　　如果在查看Slave机器中发现"DataNode"和"TaskTracker"没有起来时，先查看一下日志的，如果是"namespaceID"不一致问题，采用"常见问题FAQ6.2"进行解决，如果是"No route to host"问题，采用"常见问题FAQ6.3"进行解决。</p>
<p></p>
<p>　　（2）验证方式二：用"hadoop dfsadmin -report"</p>
<p>　　用这个命令可以查看Hadoop集群的状态。</p>
<p>　　Master服务器的状态：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427515873.png"></p>
<p></p>
<p>　　Slave服务器的状态</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427533723.png"></p>
<p></p>
<h3>4.4 网页查看集群</h3>
<p><span style="color:rgb(51,102,255);"><strong>　　1）访问"http:192.168.1.2:50030" </strong>
</span></p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427548674.png"></p>
<p></p>
<p>　　2）访问"<span style="color:rgb(51,102,255);"><strong>http:192.168.1.2:50070</strong></span>"</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/20120516142754593.png"></p>
<p style="text-align:justify;"></p>
<h2>5、常见问题FAQ</h2>
<p style="text-align:justify;"></p>
<h3>5.1 关于 Warning: $HADOOP_HOME is deprecated.</h3>
<p>　　hadoop 1.0.0版本，安装完之后敲入hadoop命令时，<span style="color:#FF0000;"><strong>老</strong></span>是提示这个警告：</p>
<p></p>
<blockquote>
<p>Warning: $HADOOP_HOME is deprecated.</p>
</blockquote>
<p></p>
<p>　　经查hadoop-1.0.0/bin/hadoop脚本和"hadoop-config.sh"脚本，发现脚本中对HADOOP_HOME的环境变量设置做了判断，笔者的环境根本不需要设置HADOOP_HOME环境变量。</p>
<p>　　解决方案一：编辑"/etc/profile"文件，去掉HADOOP_HOME的变量设定，重新输入hadoop fs命令，警告消失。</p>
<p>　　解决方案二：编辑"/etc/profile"文件，添加一个环境变量，之后警告消失：</p>
<p></p>
<blockquote>
<p>export HADOOP_HOME_WARN_SUPPRESS=1</p>
</blockquote>
<p></p>
<p>　　解决方案三：编辑"hadoop-config.sh"文件，把下面的"if - fi"功能注释掉。</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427545576.png"></p>
<p></p>
<p>　　我们这里本着不动Hadoop原配置文件的前提下，采用"<span style="color:#FF0000;"><strong>方案二</strong></span>"，在"/etc/profile"文件添加上面内容，并用命令"source /etc/profile"使之有效。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）切换至root用户 </strong></span></p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/20120516142754244.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）添加内容 </strong></span></p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427558542.png"></p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　3）重新生效 </strong></span></p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427559589.png"></p>
<p style="text-align:justify;"></p>
<h3>5.2 解决"no datanode to stop"问题</h3>
<p>　　当我停止Hadoop时发现如下信息：</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427562903.png"></p>
<p></p>
<p>　　原因：每次namenode format会重新创建一个namenodeId，而tmp/dfs/data下包含了上次format下的id，namenode format清空了namenode下的数据，但是没有清空datanode下的数据，导致启动时失败，所要做的就是每次fotmat前，清空tmp一下的所有目录。</p>
<p><span style="color:#FF00FF;"><strong>　　第一种解决方案如下： </strong></span></p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）先删除"/usr/hadoop/tmp" </strong>
</span></p>
<p></p>
<blockquote>
<p>rm -rf /usr/hadoop/tmp</p>
</blockquote>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）创建"/usr/hadoop/tmp"文件夹 </strong>
</span></p>
<p></p>
<blockquote>
<p>mkdir /usr/hadoop/tmp</p>
</blockquote>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　3）删除"/tmp"下以"hadoop"开头文件 </strong>
</span></p>
<p></p>
<blockquote>
<p>rm -rf /tmp/hadoop*</p>
</blockquote>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　4）重新格式化hadoop </strong></span></p>
<p></p>
<blockquote>
<p>hadoop namenode -format</p>
</blockquote>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　5）启动hadoop </strong></span></p>
<p></p>
<blockquote>
<p>start-all.sh</p>
</blockquote>
<p></p>
<p>　　使用第一种方案，有种不好处就是原来集群上的重要数据全没有了。假如说Hadoop集群已经运行了一段时间。建议采用第二种。</p>
<p><span style="color:#FF00FF;"><strong>　　第二种方案如下： </strong></span></p>
<p>　　1）修改每个Slave的namespaceID使其与Master的namespaceID一致。</p>
<p>　　或者</p>
<p>　　2）修改Master的namespaceID使其与Slave的namespaceID一致。</p>
<p></p>
<p>　　该"namespaceID"位于"<span style="color:#0000FF;"><strong>/usr/hadoop/tmp<span style="color:#FF0000;">/dfs/data/current/VERSION</span></strong>"文件中，前面<strong>蓝色</strong></span>的可能根据实际情况变化，但后面<span style="color:#0000FF;"><strong>红色</strong></span>是不变的。</p>
<p>　　例如：查看"Master"下的"<span style="color:#FF0000;"><strong>VERSION</strong></span>"文件</p>
<p></p>
<p><img alt="" src="http://images.cnblogs.com/cnblogs_com/xia520pi/201205/201205161427562554.png"></p>
<p></p>
<p>　　本人建议采用<span style="color:#FF0000;"><strong>第二种</strong></span>，这样方便快捷，而且还能防止误删。</p>
<p></p>
<h3>5.3 Slave服务器中datanode启动后又自动关闭</h3>
<p>　　查看日志发下如下错误。</p>
<p><span style="color:#FF0000;"><strong>　　ERROR</strong></span> org.apache.hadoop.hdfs.server.datanode.DataNode: java.io.IOException: Call to ... failed on local exception: java.net.NoRouteToHostException:
<span style="color:#FF0000;"><strong>No route to host</strong></span></p>
<p>　　解决方案是：关闭防火墙</p>
<p></p>
<blockquote>
<p>service iptables stop</p>
</blockquote>
<p></p>
<h3>5.4 从本地往hdfs文件系统上传文件</h3>
<p>　　出现如下错误：</p>
<p>　　INFO hdfs.DFSClient: Exception in createBlockOutputStream java.io.IOException:
<span style="color:#FF0000;"><strong>Bad connect ack with firstBadLink </strong></span></p>
<p>　　INFO hdfs.DFSClient: Abandoning block blk_-1300529705803292651_37023</p>
<p>　　WARN hdfs.DFSClient: DataStreamer Exception: java.io.IOException: <span style="color:#FF0000;">
<strong>Unable to create new block.</strong></span></p>
<p>　　解决方案是：</p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）关闭防火墙 </strong></span></p>
<p></p>
<blockquote>
<p>service iptables stop</p>
</blockquote>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）禁用selinux </strong></span></p>
<p>编辑 "<span style="color:#0000FF;"><strong>/etc/selinux/config</strong></span>"文件，设置"<span style="color:#FF0000;"><strong>SELINUX</strong></span>=<span style="color:#FF00FF;"><strong>disabled</strong></span>"</p>
<p style="text-align:justify;"></p>
<h3>5.5 安全模式导致的错误</h3>
<p>　　出现如下错误：</p>
<p>　　org.apache.hadoop.dfs.SafeModeException: <span style="color:#FF0000;"><strong>Cannot delete ..., Name node is in safe mode</strong></span></p>
<p>　　在分布式文件系统启动的时候，开始的时候会有安全模式，当分布式文件系统处于安全模式的情况下，文件系统中的内容不允许修改也不允许删除，直到安全模式结束。安全模式主要是为了系统启动的时候检查各个DataNode上数据块的有效性，同时根据策略必要的复制或者删除部分数据块。运行期通过命令也可以进入安全模式。在实践过程中，系统启动的时候去修改和删除文件也会有安全模式不允许修改的出错提示，只需要等待一会儿即可。</p>
<p>　　解决方案是：关闭安全模式</p>
<p></p>
<blockquote>
<p>hadoop dfsadmin -safemode leave</p>
</blockquote>
<p></p>
<h3>5.6 解决Exceeded MAX_FAILED_UNIQUE_FETCHES</h3>
<p>　　出现错误如下：</p>
<p>　　Shuffle Error: Exceeded MAX_FAILED_UNIQUE_FETCHES; bailing-out</p>
<p>　　程序里面需要打开多个文件，进行分析，系统一般默认数量是1024，（用ulimit -a可以看到）对于正常使用是够了，但是对于程序来讲，就太少了。</p>
<p>　　解决方案是：修改2个文件。</p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）"/etc/security/limits.conf" </strong>
</span></p>
<p></p>
<blockquote>
<p>vim /etc/security/limits.conf</p>
</blockquote>
<p></p>
<p>　　加上：</p>
<p></p>
<blockquote>
<p>soft nofile 102400</p>
<p>hard nofile 409600</p>
</blockquote>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）"/etc/pam.d/login" </strong></span></p>
<p></p>
<blockquote>
<p>vim /etc/pam.d/login</p>
</blockquote>
<p></p>
<p>　　添加：</p>
<p></p>
<blockquote>
<p>session required /lib/security/pam_limits.so</p>
</blockquote>
<p></p>
<p>　　针对第一个问题我纠正下答案：</p>
<p>　　这是reduce预处理阶段shuffle时获取已完成的map的输出失败次数超过上限造成的，上限默认为5。引起此问题的方式可能会有很多种，比如网络连接不正常，连接超时，带宽较差以及端口阻塞等。通常框架内网络情况较好是不会出现此错误的。</p>
<p style="text-align:justify;"></p>
<h3>5.7 解决"Too many fetch-failures"</h3>
<p>　　出现这个问题主要是结点间的连通不够全面。</p>
<p>　　解决方案是：</p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）检查"/etc/hosts" </strong></span></p>
<p>　　要求本机ip 对应 服务器名</p>
<p>　　要求要包含所有的服务器ip +服务器名</p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）检查".ssh/authorized_keys" </strong>
</span></p>
<p>　　要求包含所有服务器（包括其自身）的public key</p>
<p style="text-align:justify;"></p>
<h3>5.8 处理速度特别的慢</h3>
<p>　　出现<span style="color:#FF0000;"><strong>map</strong></span>很<span style="color:#0000FF;"><strong>快</strong></span>，但是<span style="color:#FF0000;"><strong>reduce</strong></span>很<span style="color:#0000FF;"><strong>慢</strong></span>，而且反复出现"<span style="color:#FF0000;"><strong>reduce=0%</strong></span>"。</p>
<p>　　解决方案如下：</p>
<p>　　结合解决方案5.7，然后修改"conf/hadoop-env.sh"中的"export HADOOP_HEAPSIZE=4000"</p>
<p style="text-align:justify;"></p>
<h3>5.9解决hadoop OutOfMemoryError问题</h3>
<p>　　出现这种异常，明显是jvm内存不够得原因。</p>
<p>　　解决方案如下：要修改所有的datanode的jvm内存大小。</p>
<p></p>
<blockquote>
<p>Java –Xms 1024m -Xmx 4096m</p>
</blockquote>
<p></p>
<p>　　一般jvm的最大内存使用应该为总内存大小的一半，我们使用的8G内存，所以设置为4096m，这一值可能依旧不是最优的值。</p>
<p style="text-align:justify;"></p>
<h3>5.10 Namenode in safe mode</h3>
<p>　　解决方案如下：</p>
<p></p>
<blockquote>
<p>bin/hadoop dfsadmin -safemode leave</p>
</blockquote>
<p></p>
<h3>5.11 IO写操作出现问题</h3>
<p><span style="font-size:9pt;">　　0-1246359584298, infoPort=50075, ipcPort=50020):Got exception while serving blk_-5911099437886836280_1292 to /172.16.100.165:
</span></p>
<p><span style="font-size:9pt;">　　java.net.SocketTimeoutException: 480000 millis timeout while waiting for channel to be ready for write. ch : java.nio.channels.SocketChannel[connected local=/
</span></p>
<p><span style="font-size:9pt;">　　172.16.100.165:50010 remote=/172.16.100.165:50930]
</span></p>
<p><span style="font-size:9pt;">　　at org.apache.hadoop.net.SocketIOWithTimeout.waitForIO(SocketIOWithTimeout.java:185)
</span></p>
<p><span style="font-size:9pt;">　　at org.apache.hadoop.net.SocketOutputStream.waitForWritable(SocketOutputStream.java:159)
</span></p>
<p><span style="font-size:9pt;">　　…… </span></p>
<p><span style="font-size:9pt;">　　It seems there are many reasons that it can timeout, the example given in HADOOP-3831 is a slow reading client.
</span></p>
<p>解决方案如下：</p>
<p>　　在hadoop-site.xml中设置dfs.datanode.socket.write.timeout=0</p>
<p style="text-align:justify;"></p>
<h3>5.12 status of 255 error</h3>
<p>　　错误类型：</p>
<p>　　java.io.IOException: Task process exit with nonzero status of 255.</p>
<p>　　at org.apache.hadoop.mapred.TaskRunner.run(TaskRunner.java:424)</p>
<p></p>
<p>　　错误原因：</p>
<p>　　Set mapred.jobtracker.retirejob.interval and mapred.userlog.retain.hours to higher value. By default, their values are 24 hours. These might be the reason for failure, though I'm not sure restart.</p>
<p>　　解决方案如下：单个datanode</p>
<p>　　如果一个datanode 出现问题，解决之后需要重新加入cluster而不重启cluster，方法如下：</p>
<p></p>
<blockquote>
<p>bin/hadoop-daemon.sh start datanode</p>
<p>bin/hadoop-daemon.sh start jobtracker</p>
</blockquote>
<p></p>
<p style="text-align:justify;"></p>
<h2>6、用到的Linux命令</h2>
<p style="text-align:justify;"></p>
<h3>6.1 chmod命令详解</h3>
<p><span style="color:#0000FF;"><strong>　　使用权限：</strong></span>所有使用者</p>
<p><span style="color:#0000FF;"><strong>　　使用方式：</strong></span>chmod [-cfvR] [--help] [--version] mode file...</p>
<p><span style="color:#0000FF;"><strong>　　说明： </strong></span></p>
<p>　　Linux/Unix 的档案存取权限分为三级 : 档案拥有者、群组、其他。利用 chmod 可以藉以控制档案如何被他人所存取。</p>
<p>　　mode ：权限设定字串，格式如下 ：[ugoa...][[+-=][rwxX]...][,...]，其中u 表示该档案的拥有者，g 表示与该档案的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。</p>
<p></p>
<blockquote>
<p>+ 表示增加权限、- 表示取消权限、= 表示唯一设定权限。</p>
<p>r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该档案是个子目录或者该档案已经被设定过为可执行。</p>
</blockquote>
<p></p>
<blockquote>
<p>-c : 若该档案权限确实已经更改，才显示其更改动作</p>
<p>-f : 若该档案权限无法被更改也不要显示错误讯息</p>
<p>-v : 显示权限变更的详细资料</p>
<p>-R : 对目前目录下的所有档案与子目录进行相同的权限变更(即以递回的方式逐个变更)</p>
<p>--help : 显示辅助说明</p>
<p>--version : 显示版本</p>
</blockquote>
<p></p>
<p><span style="color:#0000FF;"><strong>　　范例： </strong></span></p>
<p>　　将档案 file1.txt 设为所有人皆可读取</p>
<p></p>
<blockquote>
<p>chmod ugo+r file1.txt</p>
</blockquote>
<p></p>
<p>　　将档案 file1.txt 设为所有人皆可读取</p>
<p></p>
<blockquote>
<p>chmod a+r file1.txt</p>
</blockquote>
<p></p>
<p>　　将档案 file1.txt 与 file2.txt 设为该档案拥有者，与其所属同一个群体者可写入，但其他以外的人则不可写入</p>
<p></p>
<blockquote>
<p>chmod ug+w,o-w file1.txt file2.txt</p>
</blockquote>
<p></p>
<p>　　将 ex1.py 设定为只有该档案拥有者可以执行</p>
<p></p>
<blockquote>
<p>chmod u+x ex1.py</p>
</blockquote>
<p></p>
<p>　　将目前目录下的所有档案与子目录皆设为任何人可读取</p>
<p></p>
<blockquote>
<p>chmod -R a+r *</p>
</blockquote>
<p></p>
<p>　　此外chmod也可以用数字来表示权限如 chmod 777 file</p>
<p><span style="color:#0000FF;"><strong>　　语法为：</strong></span>chmod abc file</p>
<p>　　其中a,b,c各为一个数字，分别表示User、Group、及Other的权限。</p>
<p>　　r=4，w=2，x=1</p>
<p>　　若要rwx属性则4+2+1=7；</p>
<p>　　若要rw-属性则4+2=6；</p>
<p>　　若要r-x属性则4+1=7。</p>
<p><span style="color:#0000FF;"><strong>　　范例： </strong></span></p>
<p>　　chmod a=rwx file 和 chmod 777 file 效果相同</p>
<p>　　chmod ug=rwx,o=x file 和 chmod 771 file 效果相同</p>
<p>　　若用chmod 4755 filename可使此程式具有<span style="color:#FF00FF;"><strong>root</strong></span>的权限</p>
<p style="text-align:justify;"></p>
<h3>6.2 chown命令详解</h3>
<p><span style="color:#0000FF;"><strong>　　使用权限：</strong></span>root</p>
<p><span style="color:#0000FF;"><strong>　　使用方式：</strong></span>chown [-cfhvR] [--help] [--version] user[:group] file...</p>
<p><span style="color:#0000FF;"><strong>　　说明： </strong></span></p>
<p>　　Linux/Unix 是多人多工作业系统，所有的档案皆有拥有者。利用 chown 可以将档案的拥有者加以改变。一般来说，这个指令只有是由系统管理者(root)所使用，一般使用者没有权限可以改变别人的档案拥有者，也没有权限可以自己的档案拥有者改设为别人。只有系统管理者(root)才有这样的权限。</p>
<p></p>
<blockquote>
<p>user : 新的档案拥有者的使用者</p>
<p>IDgroup : 新的档案拥有者的使用者群体(group)</p>
<p>-c : 若该档案拥有者确实已经更改，才显示其更改动作</p>
<p>-f : 若该档案拥有者无法被更改也不要显示错误讯息</p>
<p>-h : 只对于连结(link)进行变更，而非该 link 真正指向的档案</p>
<p>-v : 显示拥有者变更的详细资料</p>
<p>-R : 对目前目录下的所有档案与子目录进行相同的拥有者变更(即以递回的方式逐个变更)</p>
<p>--help : 显示辅助说明</p>
<p>--version : 显示版本</p>
</blockquote>
<p></p>
<p><span style="color:#0000FF;"><strong>　　范例： </strong></span></p>
<p>　　将档案 file1.txt 的拥有者设为 users 群体的使用者 jessie</p>
<p></p>
<blockquote>
<p>chown jessie:users file1.txt</p>
</blockquote>
<p></p>
<p>　　将目前目录下的所有档案与子目录的拥有者皆设为 users 群体的使用者 lamport</p>
<p></p>
<blockquote>
<p>chown -R lamport:users *</p>
</blockquote>
<p></p>
<blockquote>
<p>-rw------- (600) -- 只有属主有读写权限。</p>
<p>-rw-r--r-- (644) -- 只有属主有读写权限；而属组用户和其他用户只有读权限。</p>
<p>-rwx------ (700) -- 只有属主有读、写、执行权限。</p>
<p>-rwxr-xr-x (755) -- 属主有读、写、执行权限；而属组用户和其他用户只有读、执行权限。</p>
<p>-rwx--x--x (711) -- 属主有读、写、执行权限；而属组用户和其他用户只有执行权限。</p>
<p>-rw-rw-rw- (666) -- 所有用户都有文件读、写权限。这种做法不可取。</p>
<p>-rwxrwxrwx (777) -- 所有用户都有读、写、执行权限。更不可取的做法。</p>
</blockquote>
<p></p>
<p>　　以下是对目录的两个普通设定：</p>
<p></p>
<blockquote>
<p>drwx------ (700) - 只有属主可在目录中读、写。</p>
<p>drwxr-xr-x (755) - 所有用户可读该目录，但只有属主才能改变目录中的内容</p>
</blockquote>
<p></p>
<p>　　suid的代表数字是4，比如4755的结果是-rwsr-xr-x</p>
<p>　　sgid的代表数字是2，比如6755的结果是-rwsr-sr-x</p>
<p>　　sticky位代表数字是1，比如7755的结果是-rwsr-sr-t</p>
<p style="text-align:justify;"></p>
<h3>6.3 scp命令详解</h3>
<p>　　scp是 secure copy的缩写，scp是linux系统下基于ssh登陆进行安全的远程文件拷贝命令。linux的scp命令可以在linux服务器之间复制文件和目录。</p>
<p><span style="color:#0000FF;"><strong>　　scp命令的用处： </strong></span></p>
<p>　　scp在网络上不同的主机之间复制文件，它使用ssh安全协议传输数据，具有和ssh一样的验证机制，从而安全的远程拷贝文件。</p>
<p><span style="color:#0000FF;"><strong>　　scp命令基本格式： </strong></span></p>
<p></p>
<blockquote>
<p>scp [-1246BCpqrv] [-c cipher] [-F ssh_config] [-i identity_file]</p>
<p>[-l limit] [-o ssh_option] [-P port] [-S program]</p>
<p>[[user@]host1:]file1 [...] [[user@]host2:]file2</p>
</blockquote>
<p style="margin-left:11pt;"></p>
<p>　　scp命令的参数说明：</p>
<p></p>
<blockquote>
<p>-1 强制scp命令使用协议ssh1</p>
<p>-2 强制scp命令使用协议ssh2</p>
<p>-4 强制scp命令只使用IPv4寻址</p>
<p>-6 强制scp命令只使用IPv6寻址</p>
<p>-B 使用批处理模式（传输过程中不询问传输口令或短语）</p>
<p>-C 允许压缩。（将-C标志传递给ssh，从而打开压缩功能）</p>
<p>-p 保留原文件的修改时间，访问时间和访问权限。</p>
<p>-q 不显示传输进度条。</p>
<p>-r 递归复制整个目录。</p>
<p>-v 详细方式显示输出。scp和ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。</p>
<p>-c cipher 以cipher将数据传输进行加密，这个选项将直接传递给ssh。</p>
<p>-F ssh_config 指定一个替代的ssh配置文件，此参数直接传递给ssh。</p>
<p>-i identity_file 从指定文件中读取传输时使用的密钥文件，此参数直接传递给ssh。</p>
<p>-l limit 限定用户所能使用的带宽，以Kbit/s为单位。</p>
<p>-o ssh_option 如果习惯于使用ssh_config(5)中的参数传递方式，</p>
<p>-P port 注意是大写的P, port是指定数据传输用到的端口号</p>
<p>-S program 指定加密传输时所使用的程序。此程序必须能够理解ssh(1)的选项。</p>
</blockquote>
<p></p>
<p><span style="color:#FF0000;"><strong>　　scp命令的实际应用 </strong></span></p>
<p><span style="color:rgb(51,102,255);"><strong>　　1）从本地服务器复制到远程服务器 </strong></span></p>
<p><span style="color:#0000FF;"><strong>　　(1) 复制文件： </strong></span></p>
<p>　　命令格式：</p>
<p></p>
<blockquote>
<p>scp local_file remote_username@remote_ip:remote_folder</p>
</blockquote>
<p>　　或者</p>
<blockquote>
<p>scp local_file remote_username@remote_ip:remote_file</p>
</blockquote>
<p>　　或者</p>
<blockquote>
<p>scp local_file remote_ip:remote_folder</p>
</blockquote>
<p>　　或者</p>
<blockquote>
<p>scp local_file remote_ip:remote_file</p>
</blockquote>
<p></p>
<p>　　第1,2个指定了用户名，命令执行后需要输入用户密码，第1个仅指定了远程的目录，文件名字不变，第2个指定了文件名</p>
<p>　　第3,4个没有指定用户名，命令执行后需要输入用户名和密码，第3个仅指定了远程的目录，文件名字不变，第4个指定了文件名</p>
<p><span style="color:#FF0000;"><strong>　　实例： </strong></span></p>
<p></p>
<blockquote>
<p>scp /home/linux/soft/scp.zip root@www.mydomain.com:/home/linux/others/soft</p>
<p>scp /home/linux/soft/scp.zip root@www.mydomain.com:/home/linux/others/soft/scp2.zip</p>
<p>scp /home/linux/soft/scp.zip www.mydomain.com:/home/linux/others/soft</p>
<p>scp /home/linux/soft/scp.zip www.mydomain.com:/home/linux/others/soft/scp2.zip</p>
</blockquote>
<p></p>
<p><span style="color:#0000FF;"><strong>　　(2) 复制目录： </strong></span></p>
<p>　　命令格式：</p>
<p></p>
<blockquote>
<p>scp -r local_folder remote_username@remote_ip:remote_folder</p>
</blockquote>
<p>　　或者</p>
<blockquote>
<p>scp -r local_folder remote_ip:remote_folder</p>
</blockquote>
<p></p>
<p>　　第1个指定了用户名，命令执行后需要输入用户密码；</p>
<p>　　第2个没有指定用户名，命令执行后需要输入用户名和密码；</p>
<p></p>
<p><span style="color:#FF0000;"><strong>　　例子： </strong></span></p>
<p></p>
<blockquote>
<p>scp -r /home/linux/soft/ root@www.mydomain.com:/home/linux/others/</p>
<p>scp -r /home/linux/soft/ www.mydomain.com:/home/linux/others/</p>
</blockquote>
<p></p>
<p>　　上面 命令 将 本地 soft 目录 复制 到 远程 others 目录下，即复制后远程服务器上会有/home/linux/others/soft/ 目录。</p>
<p></p>
<p><span style="color:rgb(51,102,255);"><strong>　　2）从远程服务器复制到本地服务器 </strong></span></p>
<p>　　从远程复制到本地的scp命令与上面的命令雷同，只要将从本地复制到远程的命令后面2个参数互换顺序就行了。</p>
<p><span style="color:#FF0000;"><strong>　　例如： </strong></span></p>
<p></p>
<blockquote>
<p>scp root@www.mydomain.com:/home/linux/soft/scp.zip /home/linux/others/scp.zip</p>
<p>scp www.mydomain.com:/home/linux/soft/ -r /home/linux/others/</p>
</blockquote>
<p></p>
<p>　　linux系统下scp命令中很多参数都和ssh1有关，还需要看到更原汁原味的参数信息，可以运行man scp 看到更细致的英文说明。</p>
<p></p>
</div>
</div>
            </div>
                </div>
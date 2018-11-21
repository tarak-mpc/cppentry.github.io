---
layout:     post
title:      Hadoop学习之二《Hadoop环境伪分布式环境搭建》
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">问题？Hadoop学习之二《Hadoop环境伪分布式环境搭建》</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">一、Hadoop定义</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">二、环境搭建</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">(1)资源下载</span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">JDK1.7-Linux-64:<a href="http://download.csdn.net/detail/mr_li13/9623970" rel="nofollow">点击下载链接</a></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;">Hadoop2.2版本:<a href="http://download.csdn.net/detail/mr_li13/9619309" rel="nofollow">点击下载链接</a></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">(2)配置过程</span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;">首先是，虚拟机要先安装好，而且网路要配置好。详情请看<a href="http://blog.csdn.net/mr_li13/article/details/52389966" rel="nofollow">环境配置</a></span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span></span></p>
<p><span style="color:#FF0000;">1.</span><span style="color:#FF0000;">设置网络，并且网络</span><span style="color:#FF0000;">service networkrestart</span></p>
<p><span style="color:#FF0000;">2.</span><span style="color:#FF0000;">创建组，并且创建</span><span style="color:#FF0000;">hadoop</span><span style="color:#FF0000;">用户设置密码（放到这个组中）</span></p>
<p><span style="color:#FF0000;">3.</span><span style="color:#FF0000;">下载</span><span style="color:#FF0000;">jdk1.7</span><span style="color:#FF0000;">及</span><span style="color:#FF0000;">hadoop2.2</span><span style="color:#FF0000;">压缩包，并且使用</span><span style="color:#FF0000;">WinSCP</span><span style="color:#FF0000;">软件移动这两个文件到</span><span style="color:#FF0000;">/usr/local</span><span style="color:#FF0000;">下</span></p>
<p><span style="color:#FF0000;"> </span></p>
<p><span style="color:#FF0000;">解压</span><span style="color:#FF0000;">jdk</span><span style="color:#FF0000;">：</span><span style="color:#FF0000;">tar –zxvf …… 
</span><span style="color:#FF0000;">并且重命名为</span><span style="color:#FF0000;">jdk1.7</span></p>
<p><span style="color:#FF0000;">解压</span><span style="color:#FF0000;">hadoop</span><span style="color:#FF0000;">，并且移动到用户主目录下</span><span style="color:#FF0000;">(</span><span style="color:#FF0000;">这个时候这个文件只有</span><span style="color:#FF0000;">root</span><span style="color:#FF0000;">有权限，</span><span style="color:#FF0000;">hadoop</span><span style="color:#FF0000;">还没有，后面会给予权限</span><span style="color:#FF0000;">)</span></p>
<p><span style="color:#FF0000;"><br></span></p>
<img src="https://img-blog.csdn.net/20160907170534566" alt=""><br><p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"><br></span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span></span></p>
<p>在hadoop程序目录下创建文件夹:都是以后放一些数据文件的</p>
<br><p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20160907170611221" alt=""><br></span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"><br></span></span></p>
<p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"></span></span></p>
<p>给hadoopGroup组分配对此hadopp2.2文件的操作权限</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907170734487" alt=""></p>
<p><br></p>
<p>给组分配读写执行权限</p>
<p><img src="https://img-blog.csdn.net/20160907170752690" alt=""><br></p>
<p><br></p>
<p></p>
<p>配置jdk的环境变量</p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160907171318434" alt=""> </p>
<p><img src="https://img-blog.csdn.net/20160907171343622" alt=""><br></p>
<p>查看是否配置成功</p>
<p><img src="https://img-blog.csdn.net/20160907171405919" alt=""></p>
<p> </p>
<p>修改hadoop环境变量</p>
<p><img src="https://img-blog.csdn.net/20160907171421748" alt=""><br></p>
<p></p>
<p>查看是否生效</p>
<p><img src="https://img-blog.csdn.net/20160907171443717" alt=""><br></p>
<p><br></p>
<p></p>
<p>修改hadoop2.2里面etc下的配置文件，这个非常重要。配置不成功，后面的文件系统就会出错。</p>
<p> </p>
<p>修改环境变量：export JAVA_HOME=/usr/local/jdk1.7/   (就只改这个，有些有#号的别去掉了。)</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907171508600" alt=""></p>
<p> </p>
<p>修改slaves文件</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907171527522" alt=""></p>
<p><br></p>
<p>修改core-site.xml</p>
<p></p><pre><code class="language-html">&lt;configuration&gt;
 &lt;property&gt;
       &lt;name&gt;fs.defaultFS&lt;/name&gt;
       &lt;value&gt;hdfs://node:9000&lt;/value&gt;
       &lt;description&gt;configerate hostnameand port&lt;/description&gt;
 &lt;/property&gt;
 &lt;property&gt;
       &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
       &lt;value&gt;/home/hadoop/hadoop2.2/tmp/hadoop-${user.name}&lt;/value&gt;
        &lt;description&gt;storethe temp dir&lt;/description&gt;
 &lt;/property&gt;
 &lt;property&gt;
       &lt;name&gt;hadoop.proxyuser.hadoop.hosts&lt;/name&gt;
       &lt;value&gt;*&lt;/value&gt;
 &lt;/property&gt;
&lt;property&gt;
       &lt;name&gt;hadoop.proxyuser.hadoop.groups&lt;/name&gt;
       &lt;value&gt;*&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;</code></pre><br><p>修改mapred-site.xml.temp</p>
<p></p><pre><code class="language-html">&lt;configuration&gt;
       &lt;property&gt;
              &lt;name&gt;mapreduce.framework.name&lt;/name&gt;
              &lt;value&gt;yarn&lt;/value&gt;
       &lt;/property&gt;
       &lt;property&gt;
              &lt;name&gt;mapreduce.jobhistory.address&lt;/name&gt;
              &lt;value&gt;node:10020&lt;/value&gt;
       &lt;/property&gt;
       &lt;property&gt;
              &lt;name&gt;mapreduce.jobhistory.wabapp.address&lt;/name&gt;
              &lt;value&gt;node:19888&lt;/value&gt;
       &lt;/property&gt;
&lt;/configuration&gt;</code></pre><br><br><p> </p>
<p> </p>
<p>修改yarn-site.xml</p>
<p></p><pre><code class="language-html">&lt;configuration&gt;
 
&lt;!--Site specific YARN configuration properties --&gt;
       &lt;property&gt;
              &lt;name&gt;yarn.resourcemanager.hostname&lt;/name&gt;
              &lt;value&gt;node&lt;/value&gt;
       &lt;/property&gt;
 
       &lt;property&gt;
              &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
              &lt;value&gt;mapreduce_shuffle&lt;/value&gt;
       &lt;/property&gt;
      
 
       &lt;property&gt;
              &lt;name&gt;yarn.nodemanager.aux-services.mapreduce.shuffle.class&lt;/name&gt;
              &lt;value&gt;org.apache.hadoop.mapred.ShuffleHandler&lt;/value&gt;
       &lt;/property&gt;
      
       &lt;property&gt;
              &lt;name&gt;yarn.resourcemanager.address&lt;/name&gt;
              &lt;value&gt;node:8032&lt;/value&gt;
       &lt;/property&gt;
      
       &lt;property&gt;
              &lt;name&gt;yarn.resourcemanager.scheduler.address&lt;/name&gt;
              &lt;value&gt;node:8030&lt;/value&gt;
       &lt;/property&gt;
      
       &lt;property&gt;
              &lt;name&gt;yarn.resourcemanager.resource-tracker.address&lt;/name&gt;
              &lt;value&gt;node:8031&lt;/value&gt;
       &lt;/property&gt;
      
       &lt;property&gt;
              &lt;name&gt;yarn.resourcemanager.admin.address&lt;/name&gt;
              &lt;value&gt;node:8033&lt;/value&gt;
       &lt;/property&gt;
 
       &lt;property&gt;
              &lt;name&gt;yarn.resourcemanager.webapp.address&lt;/name&gt;
              &lt;value&gt;node:8088&lt;/value&gt;
       &lt;/property&gt;
&lt;/configuration&gt;
 </code></pre><br><br><p> </p>
<p>修改hdfs-site.xml</p>
<p> </p><pre><code class="language-html">&lt;configuration&gt;
       &lt;property&gt;
              &lt;name&gt;dfs.namenode.http-address&lt;/name&gt;
              &lt;value&gt;node:50070&lt;/value&gt;
              &lt;description&gt;namenodeaddress and port&lt;/description&gt;
       &lt;/property&gt;
 
       &lt;property&gt;
              &lt;name&gt;dfs.namenode.secondary.http-address&lt;/name&gt;
              &lt;value&gt;node1:50090&lt;/value&gt;
              &lt;description&gt;seconde addressand port&lt;/description&gt;
       &lt;/property&gt;
 
       &lt;property&gt;
              &lt;name&gt;dfs.replication&lt;/name&gt;
              &lt;value&gt;3&lt;/value&gt;
              &lt;description&gt;configeratehdfs store dir number,default :3&lt;/description&gt;
       &lt;/property&gt;
      
       &lt;property&gt;
              &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;
              &lt;value&gt;file:///home/hadoop/hadoop2.2/hdfs/name&lt;/value&gt;
              &lt;description&gt;namnode is usedto keep storing namespace and exchange the path of local log file&lt;/description&gt;
       &lt;/property&gt;
      
       &lt;property&gt;
              &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;
              &lt;value&gt;file:///home/hadoop/hadoop2.2/hdfs/data&lt;/value&gt;
              &lt;description&gt;datanode isstore local dir&lt;/description&gt;
       &lt;/property&gt;
 
       &lt;property&gt;
              &lt;name&gt;dfs.namenode.checkpiont.dir&lt;/name&gt;
              &lt;value&gt;file:///home/hadoop/hadoop2.2/hdfs/namesecondary&lt;/value&gt;
              &lt;description&gt;secondarynamenode&lt;/description&gt;
       &lt;/property&gt;
      
       &lt;property&gt;
              &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;
              &lt;value&gt;true&lt;/value&gt;       
              &lt;description&gt;is it alowedto  view web hdfsfile&lt;/description&gt;
       &lt;/property&gt;
      
       &lt;property&gt;
              &lt;name&gt;dfs.stream-buffer-size&lt;/name&gt;
              &lt;value&gt;131072&lt;/value&gt;
              &lt;description&gt;buffer:4kb&lt;/description&gt;
       &lt;/property&gt;
      
&lt;/configuration&gt;</code></pre><br><p><br></p>
<p>修改主机名：这个只要在hdfs开启前改就行了，可以一开始就把主机名改了</p>
<p><img src="https://img-blog.csdn.net/20160907171927552" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20160907171939384" alt=""><br></p>
<p><br></p>
<p>绑定hostname与IP 增加四个节点(另外三个后面会加虚拟机) 检验是否成功。</p>
<p></p>
<p><img src="https://img-blog.csdn.net/20160907171953678" alt=""><img src="https://img-blog.csdn.net/20160907172008006" alt=""><br></p>
<p><br></p>
<p><span style="color:#FF0000;">关闭防火墙</span></p>
<p></p>
<p><span style="color:#FF0000;"> <img src="https://img-blog.csdn.net/20160907172024866" alt=""></span></p>
<p><span style="color:#FF0000;"> </span></p>
<p><span style="color:#FF0000;">克隆虚拟机，克隆三个，分别是</span><span style="color:#FF0000;">Node1</span><span style="color:#FF0000;">、</span><span style="color:#FF0000;">Node2</span><span style="color:#FF0000;">、</span><span style="color:#FF0000;">Node3</span></p>
<p><span style="color:#FF0000;">开启四个节点，分别设置网络</span><span style="color:#FF0000;">192.168.10.101</span><span style="color:#FF0000;">、</span><span style="color:#FF0000;">192.168.10.102</span><span style="color:#FF0000;">、</span><span style="color:#FF0000;">192.168.10.103</span></p>
<p><span style="color:#FF0000;">分别改主机名</span><span style="color:#FF0000;">:vi /etc/sysconfig/network  
</span><span style="color:#FF0000;">查看</span><span style="color:#FF0000;">more /etc/hosts</span><span style="color:#FF0000;">是否四个都是一样的</span></p>
<p><span style="color:#FF0000;"> </span></p>
<p><span style="color:#FF0000;">最后全部重启网络：</span><span style="color:#FF0000;">service network restart</span></p>
<p><span style="color:#FF0000;">查看防火墙是否全部关闭</span><span style="color:#FF0000;">:chkconfig –list | grepiptables</span></p>
<p><span style="color:#FF0000;"> </span></p>
<p><span style="color:#FF0000;"> </span></p>
<p><span style="color:#FF0000;">ssh</span><span style="color:#FF0000;">免登陆设置</span><span style="color:#FF0000;">(</span><span style="color:#FF0000;">产生秘钥这个，最好用自己新建的用户进行产生</span><span style="color:#FF0000;">,hadoop2.2</span><span style="color:#FF0000;">最终权限是谁就用那个设置</span><span style="color:#FF0000;">)</span></p>
<p><span style="color:#FF0000;"> </span></p>
<p> </p>
<p>.ssh下产生公钥:  详情：31页</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907172311963" alt=""></p>
<p><img src="https://img-blog.csdn.net/20160907172330026" alt=""><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160907172347667" alt=""><br></p>
<p><br></p>
<p></p>
<p> </p>
<p></p>
<p> </p>
<p>实现远程无密码登录</p>
<p> </p>
<p>最后只要将每一个的公钥都加到其中一个authorized_keys中，再使用复制命令就可以达到每一个节点当中都有各自节点的公钥，即可实现远程无密码登录。是不是很方便</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907172413136" alt=""></p>
<p>全部节点的authorized_keys文件内容:</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907172544294" alt=""></p>
<p> </p>
<p><img src="https://img-blog.csdn.net/20160907172604169" alt=""><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160907172622295" alt=""><br></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160907172639686" alt=""><br></p>
<p><br></p>
<p><br></p>
<p>完成，这样无论在那个节点，都可以实现远程无密码登录了</p>
<p> </p>
<p>接下来实现Hadoop的测试了</p>
<p>格式化文件系统:（要把四个节点的虚拟机开着）:hdfs namenode –format</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907172704185" alt=""></p>
<p><br></p>
<p><br></p>
<p>启动hadoop:  start-dfs.sh    如下图，全部都起来了。</p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20160907172720000" alt=""><br></p>
<p></p>
<p>jps查看进程，看看namenode出现没。</p>
<p><img src="https://img-blog.csdn.net/20160907172831639" alt=""><br></p>
<p><br></p>
<p></p>
<p>去其他节点测试，如果只有master节点的namenode才开启的话，而datanote没开启的话</p>
<p>问题就是前面配置错误了。可能性在hadoop配置上面。</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907172855643" alt=""></p>
<p> </p>
<p>测试：在虚拟机中输入网址<a href="http://node:50070/" rel="nofollow">http://node:50070</a></p>
<p><img src="https://img-blog.csdn.net/20160907172914393" alt=""></p>
<p><br></p>
<p>出现这个网页，说明成功了。当然你也可以使用其他节点试一下。(换到其它节点去)</p>
<p>同样的，其他节点的hadoop也得启动才能成功</p>
<p> </p>
<p> </p>
<p>用windows也可以访问，前提是需要配置hosts文件：</p>
<p>C:\Windows\System32\drivers\etc\hosts</p>
<p><img src="https://img-blog.csdn.net/20160907172959593" alt=""><br></p>
<p><br></p>
<p></p>
<p>最后windows浏览器访问：同样成功的</p>
<p><img src="https://img-blog.csdn.net/20160907173026265" alt=""><br></p>
<p></p>
<p>可以关掉服务:stop-dfs.sh，这里就不关闭了，还要继续做实验。</p>
<p> <img src="https://img-blog.csdn.net/20160907173047312" alt=""></p>
<p></p>
<p> </p>
<p>启动Yarn：start-yarn.sh</p>
<p><img src="https://img-blog.csdn.net/20160907173104625" alt=""><br></p>
<p><br></p>
<p></p>
<p>发现比启动文件系统HDFS的时候多了几个进程(ResourceManager and NodeManager)</p>
<p>其它节点也是一样的。是这样的，node节点成为了ResourceManager，而其他三个节点都成为了NodeManager。</p>
<p> </p>
<p>访问网址:</p>
<p></p>
<p><img src="https://img-blog.csdn.net/20160907173123584" alt=""><br></p>
<p><br></p>
<p>查看集群计算的任务信息</p>
<p></p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907173202554" alt=""></p>
<p>种植查看看集群计算任务信息，这个只是平时拿来查看看任务的。</p>
<p></p>
<p> <img src="https://img-blog.csdn.net/20160907173248414" alt=""></p>
<p> </p>
<p>集群验证：(详情请看36页)</p>
<p>使用hadoop自带的WordCount例子进行集群验证</p>
<p></p>
<p><img src="https://img-blog.csdn.net/20160907173308524" alt=""><br></p>
<p><br></p>
<p>可以查看到节点的运行情况了。这是一个案例，hadoop自带的有一个例子，拿来测试集群是否成功。hadoop的集群环境已经搭建成功，显而易见，成功了。</p>
<p><img src="https://img-blog.csdn.net/20160907173324603" alt=""><br></p>
<br><p><span style="font-family:'Microsoft YaHei';"><span style="font-size:18px;"><br></span></span></p>
<p><span style="font-family:'Microsoft YaHei';font-size:18px;"><br></span></p>
            </div>
                </div>
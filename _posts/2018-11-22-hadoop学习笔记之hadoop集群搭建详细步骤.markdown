---
layout:     post
title:      hadoop学习笔记之hadoop集群搭建详细步骤
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/YF_Li123/article/details/79161502				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">本次集群的搭建是在之前的伪分布集群的基础上实现的，如果没有搭建好伪分布集群，可参看之前的详细教程：</span></p>
<p><span style="font-size:18px;"><a href="http://blog.csdn.net/YF_Li123/article/details/79100701" rel="nofollow">hadoop学习笔记之hadoop伪分布集群搭建（一）</a><br></span></p>
<p><span style="font-size:18px;"><a href="http://blog.csdn.net/YF_Li123/article/details/79101032" rel="nofollow">hadoop学习笔记之hadoop伪分布集群搭建（二）</a><br></span></p>
<p><span style="font-size:18px;"><a href="http://blog.csdn.net/YF_Li123/article/details/79105395" rel="nofollow">hadoop学习笔记之hadoop伪分布集群搭建（三）</a><br></span></p>
<p><span style="font-size:18px;"><a href="http://blog.csdn.net/YF_Li123/article/details/79106194" rel="nofollow">hadoop学习笔记之hadoop伪分布集群搭建（四）</a><br></span></p>
<p><span style="font-size:18px;"><a href="http://blog.csdn.net/YF_Li123/article/details/79108321" rel="nofollow">hadoop学习笔记之hadoop伪分布集群搭建（五）</a><br></span></p>
<p><span style="font-size:18px;">如果按照以上的步骤成功搭建了hadoop伪分布集群，那么实现真正的集群搭建就很简单了。本次是在虚拟机下搭建了1个NameNode节点和3个DataNode节点的集群（NameNode节点也作为一个DataNode节点）。</span></p>
<p><span style="font-size:24px;">----------------------------------------------------------------------------------------------------</span></p>
<p><span style="font-size:24px;"><strong>hadoop集群搭建</strong></span></p>
<p><span style="font-size:18px;">1、克隆之前搭建好的伪分布集群系统，创建另外两个节点作为DataNode节点work02、work03（或者按照同样的步骤单独创建也可以）。</span></p>
<p><span style="font-size:18px;">2、配置IP地址</span></p>
<p><span style="font-size:18px;">按照之前的配置方式配置即可（注意，要使三台机器在同一个网段里）。</span></p>
<p><span style="font-size:18px;">将work01机器的IP地址配置为192.168.26.101</span></p>
<p><span style="font-size:18px;">将work02机器的IP地址配置为192.168.26.102<br></span></p>
<p><span style="font-size:18px;">将work03机器的IP地址配置为192.168.26.103</span><br></p>
<p><span style="font-size:18px;">3、修改机器名</span></p>
<p><span style="font-size:18px;">利用命令sudo vi /etc/sysconfig/network</span></p>
<p><span style="font-size:18px;">work02机器修改为work02</span></p>
<p><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20180125144336233" alt=""><br></span></p>
<p><span style="font-size:18px;">将work03机器修改为work03</span><br></p>
<p><span style="font-size:24px;"><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20180125144408786" alt=""><br></span></span></p>
<p><span style="font-size:18px;">4、配置相应的IP地址映射</span></p>
<p><span style="font-size:18px;">使用命令sudo vi /etc/hosts</span></p>
<p><span style="font-size:18px;">在work01、work02、work03上均配置如下内容</span></p>
<p><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20180125144541614" alt=""><br></span></p>
<p><span style="font-size:18px;">4、配置ssh免密登录（关键步骤）</span></p>
<p><span style="font-size:18px;">（1）首先利用命令ssh-keygen -t rsa 在每台机器上生成密钥对（work01已经有了，就不要再生成了），如果是克隆的系统，这时候生成会提示已经存在，直接覆盖即可</span></p>
<p><span style="font-size:18px;">（2）然后利用命令 cat id_rsa.pub &gt;&gt; authorized_keys 将各自对应的密钥公钥添加到自己的authorized_keys中（详细步骤可参看之间的教程）</span></p>
<p><span style="font-size:18px;">（3）最后将其他机器的id_rsa.pub也添加到当前的authorized_keys中</span></p>
<p><span style="font-size:18px;">完成以上步骤后，每台机器的authorized_keys的内容大致如下：</span></p>
<p><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20180125144954409" alt=""><br></span></p>
<p><span style="font-family:SimSun;font-size:18px;">配置完成后，可以work01机器上使用命令 ssh work02 远程登录work02机器，使用exit退出登录。如果不需要密码登录成功，这说明ssh免密登录成功</span></p>
<p><span style="font-size:18px;">5、修改相关配置文件</span></p>
<p><span style="font-size:18px;">（1）hdfs-site.xml文件</span></p>
<p><span style="font-size:18px;">将其内容修改为3，我们之前伪分布集群配置的是1。现在我们有三台DataNode节点，这时候就可以设置副本数为3了。</span></p>
<p><span style="font-size:18px;"></span></p>
<pre><code class="language-html">&lt;configuration&gt;
  &lt;property&gt;
    &lt;value&gt;dfs.replication&lt;/value&gt;
    &lt;name&gt;3&lt;/name&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<br><p><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20180125145314419" alt=""><br></span></p>
<p><span style="font-size:18px;">（2）slaves文件</span></p>
<p><span style="font-size:18px;">在该文件中添加另外两个DataNode机器名，之前伪分布只添加了work01（为防止出错，每台机器都修改吧）</span></p>
<p><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20180125145433255" alt=""><br></span></p>
<p><span style="font-size:18px;">（3）其他文件保持和work01的配置相同即可。</span></p>
<p><span style="font-size:18px;">6、找到每天机器上配置的hadoop.tmp.dir路径下的data文件夹，将其删除。我这里是/home/hadoop/app/hadoop-2.4.1/data</span></p>
<p><span style="font-size:18px;">然后使用命令hadoop namenode -format初始化集群</span></p>
<p><span style="font-size:24px;"><span style="font-size:16pt;">---------------------------------------------------------------------------------------------------------------------</span></span></p>
<p><span style="font-size:18px;">至此，hadoop集群已经配置成功，可以使用命令start-dfs.sh和start-yarn.sh启动集群，或者start-all.sh启动也可以（不推荐）。</span></p>
<p><span style="font-size:18px;">启动完毕，可以通过以下方式查看集群是否启动成功</span></p>
<p><strong><span style="font-size:18px;">1、查看启动的进程</span></strong></p>
<p><span style="font-size:18px;">在NameNode节点机器上（work01）,可以看到如下进程</span></p>
<p><span style="font-size:21.3333px;"><img src="https://img-blog.csdn.net/20180125145859525" alt=""><br></span></p>
<p><span style="font-size:18px;">而DataNode节点机器上（work02、work03），可以看到如下进程</span></p>
<p><span style="font-size:21.3333px;"><img src="https://img-blog.csdn.net/20180125145939941" alt=""><br></span></p>
<p><span style="font-size:21.3333px;"><img src="https://img-blog.csdn.net/20180125145954725" alt=""><br></span></p>
<p><strong><span style="font-size:18px;">2、通过web服务查看</span></strong></p>
<p><span style="font-size:18px;">浏览器输入http://work01:50070</span></p>
<p><span style="font-size:18px;">查看DataNode节点个数</span></p>
<p><span style="font-size:21.3333px;"><img src="https://img-blog.csdn.net/20180125150104765" alt=""><br></span></p>
<p><span style="font-size:18px;">还可以查看详细的DataNode节点信息</span></p>
<p><span style="font-size:24px;"><img src="https://img-blog.csdn.net/20180125150132156" alt=""><br></span></p>
<p><span style="font-size:24px;">===========================================================</span></p>
<p><span style="color:#ff0000;"><strong><span style="font-size:18px;">可能会出现DataNode启动不了的问题</span></strong></span></p>
<p><span style="font-size:18px;">原因可能是NameNode和DataNode的clusterID不一致，造成不一致的原因可能是多次使用hadoop namenode -format 。因为每次初始化的时候，NameNode都会重新生成clusterID，而DataNode却只是在最初的时候生成，之后便不再更新</span></p>
<p></p>
<div><span style="font-size:18px;"><strong><span style="color:#ff0000;">解决办法1</span></strong>：找到NameNode和DataNode的dfs.tmp.dir配置的data路径，里面有一个VERSION文件，将DataNode的ClusterID手动修改为NameNode的VERSION中的ClusterID即可</span></div>
<img src="https://img-blog.csdn.net/20180125150335723" alt=""><br><p></p>
<p><span style="font-size:24px;"></span></p>
<div><span style="font-size:18px;"><strong><span style="color:rgb(255,0,0);">解决办法2：</span></strong>如果上面的办法还是无法启动DataNode，就直接将每台机器的 /home/hadoop/app/hadopp-2.4.1/data目录的data删除，重新使用命令hadoop namenode -format初始化，然后再次启动集群即可。</span></div>
<br><p><br></p>
            </div>
                </div>
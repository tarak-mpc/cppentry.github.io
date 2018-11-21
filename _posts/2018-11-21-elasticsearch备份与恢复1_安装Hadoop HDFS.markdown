---
layout:     post
title:      elasticsearch备份与恢复1_安装Hadoop HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p style="margin-left:0cm;">先介绍一下背景：</p>

<p style="margin-left:0cm;"><img alt="" class="has" src="https://img-blog.csdn.net/20180810094847166?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM5MDU3NDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p style="margin-left:0cm;">我自己的两台虚拟机，一台部署了es，另一台安装hadoop HDFS作为索引备份的存储。</p>

<p style="margin-left:0cm;">这里es版本是6.2.3，而Hadoop HDFS版本是2.7.1</p>

<p style="margin-left:0cm;">这是第一篇：为虚拟机安装Hadoop HDFS</p>

<p style="margin-left:0cm;">hadoop hdfs，提供的是分布式的文件存储，数据存储</p>

<p style="margin-left:0cm;">这里我们在192.168.211.104的docker02上安装hadoop HDFS，由于只有一台机器，采用Hadoop伪分布式模式</p>

<p style="margin-left:0cm;">安装过程（前提已经配置好jdk）：</p>

<p style="margin-left:0cm;">1、使用hadoop-2.7.1.tar.gz，使用XFTP上传到CentOS的/usr/local目录下。</p>

<p style="margin-left:0cm;">2、将hadoop包进行解压缩：tar -zxvf hadoop-2.7.1.tar.gz</p>

<p style="margin-left:0cm;">3、对hadoop目录进行重命名：mv hadoop-2.7.1 hadoop</p>

<p style="margin-left:0cm;">4、配置hadoop相关环境变量，这样就不需要每次都切换到Hadoop的安装目录执行hadoop的相关命令了</p>

<p style="margin-left:0cm;">vi ~/.bashrc</p>

<p style="margin-left:0cm;">export JAVA_HOME=/usr/local/jdk1.8.0_171</p>

<p style="margin-left:0cm;">export HADOOP_HOME=/usr/local/hadoop</p>

<p style="margin-left:0cm;">export PATH=$PATH:JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin</p>

<p style="margin-left:0cm;">source .bashrc</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">以上过程也可以</p>

<p style="margin-left:0cm;">vi /etc/profile</p>

<p style="margin-left:0cm;">         编辑相同内容之后</p>

<p style="margin-left:0cm;">    source /etc/profile</p>

<p style="margin-left:0cm;">5. 设置ssh免密登录</p>

<p style="margin-left:0cm;">Hadoop通过SSH实现对各节点的管理，centos已经默认安装了ssh</p>

<p style="margin-left:0cm;">可以用以下语句rpm –qa|grep ssh</p>

<p style="margin-left:0cm;"><img alt="" class="has" src="https://img-blog.csdn.net/2018081018003236?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM5MDU3NDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p style="margin-left:0cm;">接下来通过以下语句设置SSH免密码通信</p>

<p style="margin-left:0cm;"><img alt="" class="has" src="https://img-blog.csdn.net/20180810180053466?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM5MDU3NDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p style="margin-left:0cm;">测试ssh登录，再次登录就无须密码验证了</p>

<p style="margin-left:0cm;"><img alt="" class="has" src="https://img-blog.csdn.net/20180810180123751?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM5MDU3NDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p style="margin-left:0cm;">具体过程可参考：<a href="https://hyjk2000.github.io/2012/03/16/how-to-set-up-ssh-keys/" rel="nofollow">设置 SSH 通过密钥登录</a></p>

<p style="margin-left:0cm;">6、在/usr/local目录下创建data目录</p>

<p style="margin-left:0cm;">mkdir data</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">etc/hosts文件中加入</p>

<p style="margin-left:0cm;">192.168.211.104 docker02</p>

<p style="margin-left:0cm;">7、修改配置文件</p>

<p style="margin-left:0cm;">cd /usr/local/hadoop/etc/Hadoop</p>

<p style="margin-left:0cm;">1） 编辑hadoop-env.sh，找到HADOOP_OPTS修改为</p>

<pre class="has">
<code>export HADOOP_OPTS="$HADOOP_OPTS -Djava.net.preferIPv4Stack=true -Djava.security.krb5.realm= -Djava.security.krb5.kdc="</code></pre>

<p style="margin-left:0cm;">2） 编辑core-site.xml</p>

<pre class="has">
<code>&lt;property&gt;
  &lt;name&gt;fs.defaultFS&lt;/name&gt;
  &lt;value&gt;hdfs://192.168.211.104:9000&lt;/value&gt;
&lt;/property&gt;
</code></pre>

<p style="margin-left:0cm;">3）编辑hdfs-site.xml</p>

<pre class="has">
<code>&lt;property&gt;
  &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;
  &lt;value&gt;/usr/local/data/namenode&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;
  &lt;value&gt;/usr/local/data/datanode&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;dfs.replication&lt;/name&gt;
  &lt;value&gt;1&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
  &lt;name&gt;dfs.permissions&lt;/name&gt;
  &lt;value&gt;false&lt;/value&gt;
&lt;/property&gt;
</code></pre>

<p style="margin-left:0cm;">其中变量dfs.replication指定了每个HDFS数据库的复制次数。通常为3，由于我们只有一台主机和一个伪分布式的DataNode，因此将此值修改为1</p>

<p>其中dfs.permissions的配置，其作用是禁止权限检查，这样方便我们在es上把snapshot传输到docker02上</p>

<p style="margin-left:0cm;">4）编辑vi slaves</p>

<p style="margin-left:0cm;">docker02</p>

<p style="margin-left:0cm;"><span style="color:#ff0000;">注意：不要忘记打开防火墙</span><span style="color:#ff0000;">50070</span><span style="color:#ff0000;">端口</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">通过上面的配置，Hadoop伪分布式配置就已经完成了，接下来启动hadoop</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">1）首先格式化namenode</p>

<p style="margin-left:0cm;">hdfs namenode -format</p>

<p style="margin-left:0cm;">2）执行完成之后，启动hdfs集群：</p>

<p style="margin-left:0cm;">start-dfs.sh</p>

<p style="margin-left:0cm;">验证启动是否成功：jps</p>

<p style="margin-left:0cm;">看到</p>

<p style="margin-left:0cm;">DataNode NameNode以及SecondaryNameNode都启动了，说明HDFS启动成功了</p>

<p style="margin-left:0cm;">3）最后访问HDFS的50070端口，看datanodes部分</p>

<p style="margin-left:0cm;"><img alt="" class="has" src="https://img-blog.csdn.net/20180810095348272?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTM5MDU3NDQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">由于后面es与docker02的datanode要进行交互，所以不要忘记<span style="color:#ff0000;">打开防火墙</span><span style="color:#ff0000;">50010</span><span style="color:#ff0000;">端口</span></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">4）停止hdfs集群的方法</p>

<p style="margin-left:0cm;">stop-dfs.sh</p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">接下来的第二部分索引备份和恢复参考：<a href="https://blog.csdn.net/u013905744/article/details/81558438" rel="nofollow">elasticsearch备份与恢复2_es使用snapshot和restore API</a></p>

<p style="margin-left:0cm;"> </p>

<p style="margin-left:0cm;">遇到的问题：</p>

<p style="margin-left:0cm;">1）如果Hadoop中Datanode为空，那么要清空/usr/local/data的内容，再重新format</p>

<p style="margin-left:0cm;">参考：<a href="https://blog.csdn.net/u013815546/article/details/42000295" rel="nofollow">hadoop中Datanode进程无法启动解决方法</a></p>

<p style="margin-left:0cm;">2）如果防火墙开启了50070端口之后，端口仍无法打开，解决参考：</p>

<p style="margin-left:0cm;"><a href="http://blog.51cto.com/13543214/2096697" rel="nofollow">Hadoop 50070端口打不开，该如何排查？</a></p>            </div>
                </div>
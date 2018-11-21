---
layout:     post
title:      hadoop学习【2】——hadoop安装配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 align="center">hadoop学习【2】——hadoop安装配置</h1>
<p><span style="font-size:18px;color:#666666;">【<u><strong>前言</strong></u>】<em>这是hadoop学习的第二步了，这次是首次正式的摸索安装和配置hadoop，不过还好，这次安装配置还是比较幸运的，只花了半天的时间（也就3个来小时）。最终包含三个节点的hadoop平台算是跑起来了。</em></span></p>
<p><span style="font-size:18px;">这里我没有截图，所以只能写下一些步骤和记录一些期中安装出现了错误了。</span></p>
<h2>一、环境准备：</h2>
<blockquote>
<h3>（1）硬件准备<br></h3>
<blockquote>
<p><span style="font-size:18px;">       这里因为只是搭一个学习的<strong><u><span style="color:#FF0000;">实验平台</span></u></strong>，所以机器的选择没有配置很高，这里的三台机子都是我们学校机房的机子，配置：2G内存、500G硬盘其他的都是很普通的配置。在上面安装的就是CentOS操作系统，前面一篇文章介绍了我安装CentOS和win7双系统的苦逼过程，这里就不再说了，假设大家已经有了linux操作系统了。</span></p>
<p><span style="font-size:18px;">       【附】<span style="color:#000099;"><strong>企业hadoop环境配置</strong>：</span></span></p>
<p><span style="font-size:18px;"><u><strong><span style="color:#000099;">选型理念</span></strong></u>：</span></p>
<p><span style="font-size:18px;">&gt;&gt;普通的，廉价的，标准的（容易替换的），工业化大规模生产的，Hadoop可以自劢支持多核CPU，比如选择2个四核CPU，主流主频，16-32G内存，更大的内存可以使Linux将常用数据缓存在内存，减少IO量，提高速度，存储集群应该使用大量廉价硬盘，例如4个主流大容量（当前为2T）普通SATA硬盘。出于成本考虑一般无需使用SCSI、SAS接口类型硬盘或SSD硬盘<br>
&gt;&gt;网络非常重要，建议使用千兆网和高带宽的交换机，但也无需使用infinband这类与用设备RAID是丌需要的，可以考虑使用JBOD<br></span></p>
<p><span style="font-size:18px;"><span style="color:#FF0000;"><strong>**</strong></span>*<span style="color:#FF0000;">安装Linux后一定要确认iptables,selinux等防火墙戒访问控制机制已经关闭，否则实验很可能受影响</span></span></p>
<p><span style="font-size:18px;">我这里三台机子分别是：</span></p>
<blockquote>
<p><span style="font-size:18px;">10.106.0.24  h1    namenode</span></p>
<p><span style="font-size:18px;">10.106.0.2    h2    datanode</span></p>
<p><span style="font-size:18px;">10.106.0.42  h3    datanode</span></p>
</blockquote>
<p><span style="color:#FF0000;"><u><span style="font-size:18px;">用grid用户进行安装</span></u></span><br></p>
</blockquote>
<h3>（2）所需文件：</h3>
<blockquote>
<p><span style="font-size:18px;">包括 jdk 和 hadoop</span></p>
<p><span style="font-size:18px;">             jdk下载地址：<a href="http://www.oracle.com/technetwork/java/javase/downloads/jdk6u37-downloads-1859587.html" rel="nofollow">http://www.oracle.com/technetwork/java/javase/downloads/jdk6u37-downloads-1859587.html</a>，下载如jdk-6u13-linux-i586.bin</span></p>
<p><span style="font-size:18px;">            hadoop下载地址：<a href="http://archive.apache.org/dist/hadoop/core/hadoop-1.0.3/" rel="nofollow">http://archive.apache.org/dist/hadoop/core/hadoop-1.0.3/</a>或者当前更高版本<br></span></p>
<p><span style="font-size:18px;">             这里jdk使用：jdk1.6.0_13</span></p>
<span style="font-size:18px;">             hadoop使用：hadoop-1.0.3</span></blockquote>
</blockquote>
<h2>二、安装java jdk</h2>
<blockquote>
<p><span style="font-size:18px;">以root登录三个机器，执行jdk-6u13-linux-i586.bin，会得到一个jdk文件夹，将其放入预定位置(如/usr/program/jdk/)下</span></p>
<p><span style="font-size:18px;">将JAVA_HOME添加到/etc/profile文件下</span></p>
<pre><code class="language-plain">[root:]#vi /etc/profile</code></pre><span style="font-size:18px;">在最后添加如下内容：</span><br></blockquote>
<blockquote>
<p></p>
<pre><code class="language-plain">#add by jonsen for jdk
#set java envrionment
export JAVA_HOME=/usr/program/jdk/jdk1.6.0_13
export JRE_HOME=/usr/program/jdk/jdk1.6.0_13/jre
export CLASSPATH=.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH</code></pre>
<p></p>
</blockquote>
<blockquote>
<p><br></p>
</blockquote>
<h2>三、配置CentOS：</h2>
<blockquote>
<p><span style="font-size:18px;">在/etc/hosts文件中为三台机器添加名称映射</span></p>
<pre><code class="language-plain">10.106.0.24 h1
10.106.0.2 h2
10.106.0.42 h3</code></pre></blockquote>
<h2>四、hadoop配置</h2>
<blockquote>
<h3>0、（重点）免密码访问配置（两台服务器之间访问免密码）</h3>
<blockquote>
<p><span style="font-size:18px;">ssh-keygen -t rsa -f ~/.ssh/id_rsa</span></p>
<p><span style="font-size:18px;">后面接着两个回车（使用缺省目录即可）</span></p>
<p><span style="font-size:18px;">ls -a可以看到隐藏目录：.ssh</span></p>
<p><span style="font-size:18px;">进入会发现两个文件: id_rsa   id_rsa.pub</span></p>
<p><span style="font-size:18px;">注意要以grid用户登录，在grid用户的主目录下进行操作！<br>
每个节点作相同操作<br></span></p>
<p><span style="font-size:18px;">将id_rsa.pub 拷贝到当前目录下的authoried_keys文件中</span></p>
<p><span style="font-size:18px;">把各个节点的authorized_keys的内容互相拷贝加入到对方的此文件中，然后就可以免<br>
密码彼此ssh连入</span><br></p>
</blockquote>
<h3>1、将下载的hadoop-1.0.3.tar.gz解压到/home/grid/目录下, 并修改权限</h3>
<blockquote>
<p></p>
<pre><code class="language-plain">[root]# chown -R grid:grid /home/grid/hadoop-1.0.3</code></pre>
<p></p>
</blockquote>
<h2>2、配置hadoop, 配置文件在/home/grid/hadoop-1.0.3/conf/目录下</h2>
<blockquote>
<h4>2.1、在hadoop-env.sh文件中添加JAVA_HOME</h4>
<blockquote>
<p></p>
<pre><code class="language-plain">#added by Jonsen for JDK_HOME
export JAVA_HOME=/usr/program/jdk/jdk1.6.0_13</code></pre>
<p></p>
</blockquote>
<h4>2.2、在core-site.xml文件中添加配置信息</h4>
<blockquote><pre><code class="language-plain">&lt;configuration&gt;
   &lt;property&gt;
     &lt;name&gt;fs.default.name&lt;/name&gt;
     &lt;value&gt;hdfs://h1:9000/&lt;/value&gt;
   &lt;/property&gt;
   &lt;property&gt;
     &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
     &lt;value&gt;/home/grid/hadoop-1.0.3/tmp&lt;/value&gt;
   &lt;/property&gt;
&lt;/configuration&gt;</code></pre><span style="font-size:18px;">其中fs.default.name为namenode的交互端口，注意不要使用已经占用的端口就好</span>
<p><span style="font-size:18px;">hadoop.tmp.dir是用来存储其他临时目录的根目录，这个<u><span style="color:#FF0000;"><strong>一定要有</strong></span></u>，否则重启后可能会启动不了hadoop了</span></p>
</blockquote>
<h4>2.3、在hdfs-site.xml文件中添加配置信息</h4>
<p></p>
<pre><code class="language-plain">&lt;configuration&gt;
   &lt;property&gt;
     &lt;name&gt;dfs.replication&lt;/name&gt;
     &lt;value&gt;2&lt;/value&gt;
   &lt;/property&gt;
&lt;/configuration&gt;</code></pre><span style="font-size:18px;">dfs.replication:冗余数量，备份的数据量</span><br><br><p></p>
<h4>2.4、在mapred-site.xml文件中添加配置信息</h4>
<blockquote><pre><code class="language-plain">&lt;configuration&gt;
  &lt;property&gt;
     &lt;name&gt;mapred.job.tracker&lt;/name&gt;
     &lt;value&gt;h1:9001&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;</code></pre><span style="font-size:18px;">mapred.job.tracker:jobTracker的交互端口<br></span></blockquote>
<h4>2.5、在master文件中写入hadoop  namenode节点</h4>
<blockquote><pre><code class="language-plain">h1</code></pre></blockquote>
<h4>2.6、在slaves文件中写入hadoop datanode数据节点</h4>
<blockquote><pre><code class="language-plain">h2
h3
</code></pre></blockquote>
<h4>2.7、配置完毕，将/home/hadoop/hadoop-1.0.3拷贝到其他站点</h4>
<blockquote><pre><code class="language-plain">scp -r /home/grid/hadoop-1.0.3/ grid@h2:/home/grid/
scp -r /home/grid/hadoop-1.0.3/ gred@h3:/home/grid/</code></pre></blockquote>
</blockquote>
<h3>3、格式化hadoop，在hadoop.master站点上执行</h3>
<blockquote>
<p></p>
<pre><code class="language-plain">cd /home/grid/hadoop-1.0.3
bin/hadoop namenode -format</code></pre>
<p></p>
</blockquote>
<h3>4、启动集群</h3>
<blockquote><pre><code class="language-plain">cd /home/grid/hadoop-1.0.3
bin/start-all.sh</code></pre></blockquote>
</blockquote>
<h2>五、配置中常见的错误总结：</h2>
<p><span style="font-size:18px;">参见：<a href="http://blog.csdn.net/yonghutwo/article/details/9206059" rel="nofollow">http://blog.csdn.net/yonghutwo/article/details/9206059</a>写的很详细！</span><br></p>
            </div>
                </div>
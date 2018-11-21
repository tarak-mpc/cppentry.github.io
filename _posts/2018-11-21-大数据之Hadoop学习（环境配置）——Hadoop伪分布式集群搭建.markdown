---
layout:     post
title:      大数据之Hadoop学习（环境配置）——Hadoop伪分布式集群搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lzw2016/article/details/84197986				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<hr>
<p>title: Hadoop伪分布式集群搭建<br>
date: 2018-11-14 15:17:20<br>
tags: Hadoop<br>
categories: 大数据<br>
点击查看我的博客: <a href="https://josonle.github.io/blog/2018-11-14-Hadoop%E4%BC%AA%E5%88%86%E5%B8%83%E5%BC%8F%E9%9B%86%E7%BE%A4%E6%90%AD%E5%BB%BA.html/" rel="nofollow">Josonlee’s Blog</a></p>
<hr>
<p></p><div class="toc"><h3>文章目录</h3><ul><ul><li><a href="#_10" rel="nofollow">前言准备</a></li><li><a href="#_17" rel="nofollow">伪分布式特点</a></li><li><a href="#JDK__24" rel="nofollow">JDK 下载和环境配置</a></li><li><a href="#_48" rel="nofollow">配置虚拟机网络环境</a></li><li><a href="#Hadoop_107" rel="nofollow">配置Hadoop的环境</a></li><ul><li><a href="#Hadoop_108" rel="nofollow">下载Hadoop</a></li><li><a href="#_115" rel="nofollow">上传并解压安装</a></li><li><a href="#Hadoop_126" rel="nofollow">配置Hadoop环境变量</a></li><li><a href="#hadoop_138" rel="nofollow">修改hadoop配置文件</a></li></ul><li><a href="#hadoop_234" rel="nofollow">配置hadoop用户免密登录</a></li><li><a href="#Hadoop_259" rel="nofollow">启动Hadoop</a></li><ul><li><a href="#hdfs_260" rel="nofollow">格式化hdfs文件系统</a></li><li><a href="#hdfs_265" rel="nofollow">启动hdfs</a></li><li><a href="#yarn_277" rel="nofollow">启动yarn</a></li><li><a href="#JobHistory_ServerMR_286" rel="nofollow">启动JobHistory Server（MR）</a></li></ul></ul></ul></div><p></p>
<h2><a id="_10"></a>前言准备</h2>
<p>Win10上通过VMware12 + Centos7准备好了基本环境，配置虚拟机的子网IP地址（我这里是192.168.17.0），如图通过管理员可配置子网IP，掩码，网关（我这里是192.168.17.2）<br>
<img src="https://img-blog.csdnimg.cn/20181118140918185.JPG?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x6dzIwMTY=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<p><strong>下文提到的文件上传到虚拟机中，建议使用WinSCP工具</strong></p>
<h2><a id="_17"></a>伪分布式特点</h2>
<p>具备Hadoop的所有功能，在单机上模拟一个分布式的环境，需要配置hdfs和yarn框架</p>
<ul>
<li>HDFS：主节点：master，从节点：slave 【伪分布式这里也是master】</li>
<li>Yarn：容器，运行MapReduce程序
<ul>
<li>主节点：ResourceManager</li>
<li>从节点：NodeManager</li>
</ul>
</li>
</ul>
<!--more-->
<h2><a id="JDK__24"></a>JDK 下载和环境配置</h2>
<ul>
<li>下载安装</li>
</ul>
<p>这里下载Linux版本：<a href="http://www.oracle.com/technetwork/java/javase/downloads/index.html" rel="nofollow">http://www.oracle.com/technetwork/java/javase/downloads/index.html</a> ，我这里使用的是 jdk-8u131-linux-x64 版本</p>
<p>首先要把下载的文件上传到centos系统下的随便那个目录下（最好root下），把文件解压缩到<code>/root</code> 目录下</p>
<p><strong>以下操作都是在root管理员权限下操作</strong></p>
<pre><code class="prism language-linux">cd /root
rpm -ivh [你下载的文件名]
</code></pre>
<ul>
<li>环境配置</li>
</ul>
<p>编辑环境变量，在<code>/etc/profile</code>文件中添加如下变量<br>
<img src="https://img-blog.csdnimg.cn/20181118140850245.JPG" alt="在这里插入图片描述"></p>
<p>注意，我这里jdk1.8.0_131换成你下载的jdk版本号即可</p>
<ul>
<li>测试</li>
</ul>
<p>命令行下输入 <code>java -version</code>，能正确查看到java的版本就表示上面步骤配置成功</p>
<h2><a id="_48"></a>配置虚拟机网络环境</h2>
<p>首先要知道完全分布式和伪分布式的原理是一样的，都得有master和slave节点，节点之间通信时不可能有DHCP临时随机配置IP，所以要配置虚拟机的固定IP</p>
<ul>
<li>设置静态IP</li>
</ul>
<p>同样是在<code>/etc</code>下（这是配置文件所在地）</p>
<pre><code class="prism language-linux">ls sysconfig/network-scripts/ifcfg-ens*
# 输出结果就是要配置的文件

vi sysconfig/network-scripts/ifcfg-en*
# 进入编辑
</code></pre>
<p>改动以下设置</p>
<pre><code class="prism language-linux">BOOTPROTO=static //改成static
ONBOOT=yes //改成yes
IPADDR=192.168.17.10 //随便设，不过要在子网192.168.17.0下
NETMASK=255.255.255.0 //掩码
GATEWAY=192.168.17.2 //第一步配置时设好的
DNS1=192.168.17.2 //随便写
</code></pre>
<ul>
<li>设置主机名</li>
</ul>
<p>命令行下输入<code>hostname</code>可查看主机名，一般是localhost，但为了方便重新设置主机名</p>
<pre><code class="prism language-linux">vi /etc/hostname 
</code></pre>
<p>删掉原先的，配置主机名（如master，namenode等）</p>
<ul>
<li>绑定IP地址到主机名的映射</li>
</ul>
<pre><code class="prism language-linux">vi /etc/hosts

# 追加如下内容
127.168.17.10 master //就是你配置的固定IP和主机名
</code></pre>
<ul>
<li>关闭防火墙</li>
</ul>
<p>我这里是centos，防火墙是firewall，而不是iptables【其他系统有其他关闭方法】。防火墙务必要关闭，否则完全分布式搭建的话无法和其他主机相连</p>
<pre><code class="prism language-linux">systemctl stop firewalld    //停止firewall服务
systemctl disable firewalld   //禁止firewall开机启动
</code></pre>
<p>然后可以通过<code>systemctl status firewalld</code>查看防火墙状态</p>
<pre><code class="prism language-linux">Active: inactive (dead) //这就是关闭了

</code></pre>
<ul>
<li>重启网络服务</li>
</ul>
<p><code>systemctl restart network</code>，然后你可以ping以下主机名<code>ping master</code>，看可以ping通吗，可以的话就OK</p>
<h2><a id="Hadoop_107"></a>配置Hadoop的环境</h2>
<h3><a id="Hadoop_108"></a>下载Hadoop</h3>
<p>(1) apache hadoop：<a href="http://www-us.apache.org/dist/hadoop/common/" rel="nofollow">http://www-us.apache.org/dist/hadoop/common/</a>  【这个是开源的】</p>
<p>(2) cloudera hadoop（CDH）：<a href="http://archive-primary.cloudera.com/cdh5/cdh/5/" rel="nofollow">http://archive-primary.cloudera.com/cdh5/cdh/5/</a> 【推荐使用】</p>
<p>CDH是hadoop的一个版本，我们老师推荐的，原因没记住是啥</p>
<h3><a id="_115"></a>上传并解压安装</h3>
<p>我这里是下的<code>hadoop-2.6.0-cdh5.12.1</code>，一样是上传放在了<code>/root</code>目录下</p>
<pre><code class="prism language-linux">tar -zxvf hadoop-2.6.0-cdh5.12.1.tar.gz -C /home/hadoop //把hadoop-2.6.0-cdh5.12.1解压到/home/hadoop目录下

cd /home/hadoop/hadoop-2.6.0-cdh5.12.1 //切换到该目录下
</code></pre>
<p>hdfs在运行时需要name，data，logs，tmp文件夹用来存放原数据，实际数据，日志，临时文件</p>
<pre><code class="prism language-linux">mkdir hdfs hdfs/name hdfs/data logs tmp         //在hadoop目录中创建
</code></pre>
<h3><a id="Hadoop_126"></a>配置Hadoop环境变量</h3>
<pre><code class="prism language-linux">vi /home/hadoop/.bash_profile 
# 修改添加下列内容

export HADOOP_HOME=/home/hadoop/hadoop-2.6.0-cdh5.12.1    //hadoop主目录
export HADOOP_LOG_DIR=$HADOOP_HOME/logs         //hadoop日志目录
export YARN_LOG_DIR=$HADOOP_LOG_DIR             //YARN日志目录
export PATH=$HADOOP_HOME/bin:$HADOOP_HOME/sbin:$PATH    //添加hadoop命令路径
</code></pre>
<p>中文去掉</p>
<h3><a id="hadoop_138"></a>修改hadoop配置文件</h3>
<p>我在文末会放一个链接，你可以直接下载，修改其中的主机名上传到<code>/home/hadoop/hadoop-2.6.0-cdh5.12.1/etc/hadoop</code>目录下，覆盖原文件即可</p>
<p><code>cd /home/hadoop/hadoop-2.6.0-cdh5.12.1/etc/hadoop</code></p>
<p><strong>中文统统都去掉</strong></p>
<ul>
<li>设置java所在环境变量</li>
</ul>
<p><a href="http://xn--hadoop-env-eh3r.sh" rel="nofollow">在hadoop-env.sh</a>、<a href="http://mapred-env.sh" rel="nofollow">mapred-env.sh</a>、yarn-env.sh中分别设置JAVA_HOME</p>
<pre><code class="prism language-linux">export JAVA_HOME=/usr/java/jdk1.8.0_131 //之前设置的
</code></pre>
<ul>
<li>配置slaves文件 设置slave节点名</li>
</ul>
<pre><code class="prism language-linux">vi slaves
#添加slave节点名

master
</code></pre>
<p>因为是伪分布式，一台虚拟机充当master也充当slave</p>
<ul>
<li>修改log4j.properties文件</li>
</ul>
<p>追加一行内容：<code>log4j.logger.org.apache.hadoop.util.NativeCodeLoader=ERROR</code>，避免启动时报警</p>
<ul>
<li>配置core-site.xml</li>
</ul>
<pre><code class="prism language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>configuration</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>fs.defaultFS<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>hdfs://master:9000<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span> //指定对外访问接口
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>hadoop.tmp.dir<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>/home/hadoop/hadoop-2.6.0-cdh5.12.1/tmp<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span> //指定临时文件放哪
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>configuration</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<ul>
<li>配置hdfs-site.xml</li>
</ul>
<pre><code class="prism language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>configuration</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>dfs.namenode.name.dir<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>/home/hadoop/hadoop-2.6.0-cdh5.12.1/hdfs/name<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span> //元数据放哪
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>dfs.datanode.data.dir<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>/home/hadoop/hadoop-2.6.0-cdh5.12.1/hdfs/data<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span> //实际数据放哪
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>dfs.replication<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>1<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>          // 这个1是指定数据备份几份 【默认3份，伪分布式1是因为只有一个slave】
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>dfs.permissions<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>false<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>    // 跳过身份验证
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>configuration</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<p>以上是hdfs的配置，下面是yarn的配置</p>
<ul>
<li>配置mapred-site.xml文件</li>
</ul>
<p><code>cp mapred-site.xml.template mapred-site.xml</code></p>
<p>该文件默认不存在，需要用模板文件来创建</p>
<pre><code class="prism language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>configuration</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>mapreduce.framework.name<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span> //配置MapReduce运行时用的框架为yarn
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>yarn<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
  <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>configuration</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<ul>
<li>配置yarn-site.xml文件</li>
</ul>
<pre><code class="prism language-xml"><span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>configuration</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>yarn.resourcemanager.hostname<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span> //配置ResourceManager的地址
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>master:8031<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>property</span><span class="token punctuation">&gt;</span></span>
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>name</span><span class="token punctuation">&gt;</span></span>yarn.nodemanager.aux-services<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>name</span><span class="token punctuation">&gt;</span></span> //配置NodeManager执行任务的方式
      <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;</span>value</span><span class="token punctuation">&gt;</span></span>mapreduce_shuffle<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>value</span><span class="token punctuation">&gt;</span></span>
    <span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>property</span><span class="token punctuation">&gt;</span></span> 
<span class="token tag"><span class="token tag"><span class="token punctuation">&lt;/</span>configuration</span><span class="token punctuation">&gt;</span></span>
</code></pre>
<ul>
<li>设置hadoop目录的owner</li>
</ul>
<pre><code class="prism language-linux">chown -R hadoop:hadoop /home/hadoop/hadoop-2.6.0-cdh5.12.1 //修改hadoop目录的owner
</code></pre>
<p><strong>以上就是Hadoop基本配置</strong></p>
<h2><a id="hadoop_234"></a>配置hadoop用户免密登录</h2>
<p>从上文可以看出，我的centos的普通用户是hadoop(/home/hadoop)，设置hadoop用户免密登录可以便于集群间后台数据传输时省去密码的输入过程</p>
<p>这里使用ssh免密登录，rsa加密算法</p>
<ul>
<li><strong>切换到hadoop用户</strong></li>
<li>命令行执行 <code>ssh -keygen -t rsa</code>
<ul>
<li>遇到提示时，回车即可；三次回车</li>
<li>默认会在<code>/home/hadoop/.ssh/</code>下生成私钥（id_rsa）、公钥（id_rsa.pub）</li>
</ul>
</li>
</ul>
<p>进入<code>.ssh</code>目录，把公钥汇总到授权文件（authorized_keys），并授权</p>
<pre><code class="prism language-linux">cd ~/.ssh
cat id_rsa.pub &gt;&gt; authorized_keys       //在master执行，合并授权文件
chmod 600 authorized_keys         //设置授权文件的访问权限为读写
</code></pre>
<p>如果是完全分布式搭建，这里略有不同。要在每台主机上都生成公钥、私钥，并把公钥汇总到master节点的授权文件中，然后master再把授权文件分发给每个节点</p>
<ul>
<li>测试ssh免密登录</li>
</ul>
<p>如果配置成功，输入<code>ssh master[主机名]</code>即可登录，登录会显示<code>Last login:XXX...</code>。第一次登录会有提示，输入yes就OK</p>
<p><strong>如果以上配置都成功了，那恭喜你，配置成功了</strong></p>
<h2><a id="Hadoop_259"></a>启动Hadoop</h2>
<h3><a id="hdfs_260"></a>格式化hdfs文件系统</h3>
<p>第一次启动时干的</p>
<pre><code class="prism language-linux">hdfs namenode -format
</code></pre>
<h3><a id="hdfs_265"></a>启动hdfs</h3>
<pre><code class="prism language-linux">start-dfs.sh
</code></pre>
<p>结果如下图<br>
<img src="https://img-blog.csdnimg.cn/20181118135905555.jpg" alt="在这里插入图片描述"></p>
<p>你可以使用jps命令查看当前运行的进程<br>
<img src="https://img-blog.csdnimg.cn/20181118140030114.jpg" alt="在这里插入图片描述"></p>
<p>你也可以本地通过浏览器查看，打开浏览器输入 <a href="http://192.168.17.10:50070" rel="nofollow">http://192.168.17.10:50070</a> 【这个IP是前面配的固定IP地址】<br>
<img src="https://img-blog.csdnimg.cn/20181118140057314.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x6dzIwMTY=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h3><a id="yarn_277"></a>启动yarn</h3>
<pre><code class="prism language-linux">start-yarn.sh
</code></pre>
<p>master启动ResourceManager进程，slave启动NodeManager进程<br>
<img src="https://img-blog.csdnimg.cn/20181118140137649.jpg" alt="在这里插入图片描述"></p>
<p>你也可以本地通过浏览器查看，打开浏览器输入 <a href="http://192.168.17.10:8088" rel="nofollow">http://192.168.17.10:8088</a><br>
<img src="https://img-blog.csdnimg.cn/20181118140200116.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2x6dzIwMTY=,size_16,color_FFFFFF,t_70" alt="在这里插入图片描述"></p>
<h3><a id="JobHistory_ServerMR_286"></a>启动JobHistory Server（MR）</h3>
<pre><code class="prism language-linux">mr-jobhistory-daemon.sh start historyserver
</code></pre>
<p>记录分布式环境下提交的作业记录，namenode启动JobHistoryServer进程</p>
<p>你也可以本地通过浏览器查看，打开浏览器输入 <a href="http://192.168.17.10:19888" rel="nofollow">http://192.168.17.10:19888</a></p>
<hr>
<p>最近开的大数据课，包含Hadoop和Spark俩门，会不定时记录学习心得，优先会记录在github博客上，有空就迁移过来<br>
点击查看我的博客: <a href="https://josonle.github.io/blog/2018-11-14-Hadoop%E4%BC%AA%E5%88%86%E5%B8%83%E5%BC%8F%E9%9B%86%E7%BE%A4%E6%90%AD%E5%BB%BA.html/" rel="nofollow">Josonlee’s Blog</a></p>
<p><a href="https://download.csdn.net/download/lzw2016/10791857" rel="nofollow">这是我文中提到的我配置好的配置文件，点击下载即可</a>，要修改处可参考我文中的注释<br>
下一篇会谈如何编写MapReduce程序和如何本地配置eclipse连接虚拟机hdfs服务器</p>
<hr>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
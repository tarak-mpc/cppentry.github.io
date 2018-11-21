---
layout:     post
title:      ubuntu 11配置hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>   最近没事，研究下ubuntu 配置hadoop！</p>
<p>   ubuntu版本:64 bit   11.04<br></p>
<p>   hadoop版本: hadoop1.2.1</p>
<p>一、<strong>在Ubuntu下创建hadoop用户组和用户;</strong></p>
<p><strong>1.</strong> 创建hadoop用户组;</p>
<p>sudo addgroup hadoop<br></p>
<p><strong>2. </strong>创建hadoop用户;</p>
<p>sudo adduser -ingroup hadoop hadoop<br></p>
<p><strong>3. </strong>给hadoop用户添加权限，打开/etc/sudoers文件;</p>
<p>sudo gedit /etc/sudoers<br></p>
<p>按回车键后就会打开/etc/sudoers文件了，给hadoop用户赋予root用户同样的权限。</p>
<p>在root   ALL=(ALL:ALL)   ALL下添加</p>
<p>hadoop   ALL=(ALL:ALL)  ALL</p>
<p><br></p>
<p><strong>二、在Ubuntu下安装JDK</strong></p>
<p>下载适合自己系统的jdk并配置JAVA环境变量，</p>
<p>http://blog.csdn.net/wei_ge163/article/details/8060534</p>
<p><br></p>
<p><strong>三、安装ssh服务 </strong></p>
<p></p>
<div>
<div class="line number1 index0 alt2"><code class="bash functions">sudo</code> <code class="bash plain">
apt-get </code><code class="bash functions">install</code> <code class="bash functions">
ssh</code> <code class="bash plain">openssh-server<br><br></code></div>
</div>
<p></p>
<strong>四、建立ssh无密码登录本机 <br></strong>1.换成hadoop用户<br>
su - hadoop<br><br>
2.创建ssh-key<br>
ssh-keygen -t rsa<br>
cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys  //将id_rsa.pub追加到authorized_keys授权文件<br>
chmod 777 ~/.ssh/authorized_keys    <br>
scp ~/.ssh/id_rsa.pub hadoop@localhost:~/<br><br>
3.登录localhost;<br>
ssh localhost<br>
( 注：当ssh远程登录到其它机器后，现在你控制的是远程的机器，需要执行退出命令才能重新控制本地主机。)<br><br>
4.执行退出命令;<br>
exit<br><p>五、安装hadoop</p>
我们采用的hadoop版本是：hadoop-1.2.1（http://www.apache.org/dyn/closer.cgi/hadoop/common/<br>
1.假设hadoop-1.2.1.tar.gz在/work，将它复制到安装目录 /usr/local/下；    <br>
sudo cp hadoop-1.2.1.tar.gz /usr/local/<br>
2.解压hadoop-0.20.203.tar.gz；<br>
cd /usr/local<br>
sudo tar -zxf hadoop-1.2.1.tar.gz<br>
3. 将解压出的文件夹改名为hadoop;<br>
sudo mv hadoop-1.2.1 hadoop<br>
4.将该hadoop文件夹的属主用户设为hadoop，<br>
sudo chown -R hadoop:hadoop hadoop<br>
5.打开hadoop/conf/hadoop-env.sh文件;    <br>
sudo gedit hadoop/conf/hadoop-env.sh<br><br>
6.配置conf/hadoop-env.sh（找到#export JAVA_HOME=...,去掉#，然后加上本机jdk的路径）;<br>
export JAVA_HOME=/usr/lib/jvm/java-6-openjdk<br><br>
7.打开conf/core-site.xml文件;<br>
sudo gedit hadoop/conf/core-site.xml<br>
编辑如下：property后面需要手工敲    <br>
&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;                                                                                          
<br>
&lt;!-- Put site-specific property overrides in this file. --&gt;                                                                                                          
<br>
&lt;configuration&gt;<br>
  &lt;property&gt;  <br>
    &lt;name&gt;fs.default.name&lt;/name&gt;  <br>
    &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;   <br>
  &lt;/property&gt;  <br>
&lt;/configuration&gt;<br><br>
8. 打开conf/mapred-site.xml文件;<br>
sudo gedit hadoop/conf/mapred-site.xml<br>
编辑如下property后面需要手工敲：    <br>
&lt;?xml version="1.0"?&gt;<br>
&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;<br>
 &lt;!-- Put site-specific property overrides in this file. --&gt;<br>
    &lt;configuration&gt;  <br>
     &lt;property&gt;   <br>
      &lt;name&gt;mapred.job.tracker&lt;/name&gt;  <br>
      &lt;value&gt;localhost:9001&lt;/value&gt;   <br>
     &lt;/property&gt;  <br><p>    &lt;/configuration&gt;</p>
<p>9. 打开conf/hdfs-site.xml文件;    </p>
sudo gedit hadoop/conf/hdfs-site.xml<br>
编辑如下：    <br>
&lt;configuration&gt;<br>
  &lt;property&gt;<br>
    &lt;name&gt;dfs.name.dir&lt;/name&gt;<br>
    &lt;value&gt;/usr/local/hadoop/datalog1,/usr/local/hadoop/datalog2&lt;/value&gt;<br>
  &lt;/property&gt;<br><br>
  &lt;property&gt;<br>
    &lt;name&gt;dfs.data.dir&lt;/name&gt;<br>
    &lt;value&gt;/usr/local/hadoop/data1,/usr/local/hadoop/data2&lt;/value&gt;<br>
  &lt;/property&gt;<br><br>
  &lt;property&gt;<br>
    &lt;name&gt;dfs.replication&lt;/name&gt;<br>
    &lt;value&gt;2&lt;/value&gt;<br>
  &lt;/property&gt;<br>
&lt;/configuration&gt;<br><br>
10.打开conf/masters文件，添加作为secondarynamenode的主机名，作为单机版环境，这里只需填写 localhost<br>
sudo gedit hadoop/conf/masters<br><br>
11.打开conf/slaves文件，添加作为slave的主机名，一行一个。作为单机版，这里也只需填写 localhost<br>
sudo gedit hadoop/conf/slaves<br>
 <br>
六、在单机上运行hadoop<br>
1.进入hadoop目录下，格式化hdfs文件系统，初次运行hadoop时一定要有该操作，    <br>
cd /usr/local/hadoop/<br>
bin/hadoop namenode -format<br>
注：可以在/etc/profile中导入如下:<br>
$ export HADOOP_INSTALL=/usr/local/hadoop<br>
$ export PATH=$PATH:$HADOOP_INSTALL/bin<br><br>
2.启动bin/start-all.sh    <br>
bin/start-all.sh<br><br>
3.检测hadoop是否启动成功<br>
jps<br>
如果有Namenode，SecondaryNameNode，TaskTracker，DataNode，JobTracker五个进程，就说明你的hadoop单机版环境配置好了，然后可以通过firefox浏览器查看，使用http://localhost:50030/<br><br>
注意：Hadoop的各组件都可以用xml文件进行配置。<br>
一般配置的话，主要是三个配置文件(全在$HADOOP_INSTALL的conf目录)：<br>
    core-site.xml：用于配置Common组件的属。<br>
    hdfs-site.xml：用于配置HDFS的属性，<br>
    HDFS顾名思义Hadoop Distributed File System，Hadoop分布式文件系统。<br>
    mapred-site.xml：用于配置MapReduce属性。<br><br>
在Hadoop的早期版本中，只有一个配置文件来配置这三项，叫hadoop_site.xml的配置文件，后期开始分为三个，一一对应个组件。属性内容不变，仅仅是分开。<br>
另外，在docs目录中还有对应的三个html文件，分别保存着各组件的默认设置。<br><br>
Hadoop的三种运行模式：<br>
第一：独立模式（standalone）或者本地模式（local model）<br>
此模式无需守护进程，所有的程序都在单个的虚拟机上运行。<br>
由于在本地模式下测试和调试MapReduce程序很方便，所以一般来说比较适用于开发阶段。<br>
第二：伪分布模式（pseudo-distributed model）<br>
顾名思义，一个模拟的小规模的集群，Hadoop守护进程运行在本地环境中。一般用作测试环境<br>
第三：全分布模式（fully distributed model）<br>
这才是真实的，Hadoop守护进程运行在真正的集群环境。一般作为产品环境。<br><br>
注：在无论哪个模式下去运行Hadoop都需要关注两个重要因素：<br>
1.是否启动Hadoop守护进程，2.是否已经正确配置各属性。<br><br><p class="p4"><span class="s3">下面</span>这个表里简单列举了三种模式下的所需的最小属性配置集合：</p>
<table class="t1" cellpadding="0" cellspacing="0"><tbody><tr><td class="td1" valign="top">
<p class="p4">组件名称</p>
</td>
<td class="td2" valign="top">
<p class="p1">属性名称</p>
</td>
<td class="td3" valign="top">
<p class="p1">独立模式</p>
</td>
<td class="td4" valign="top">
<p class="p4">伪分布模式</p>
</td>
<td class="td5" valign="top">
<p class="p1">全分布模式</p>
</td>
</tr><tr><td class="td1" valign="top">
<p class="p5">Common</p>
</td>
<td class="td2" valign="top">
<p class="p5">fs.default.name</p>
</td>
<td class="td3" valign="top">
<p class="p5">file:///</p>
<p class="p1">（默<span class="s1">认）</span></p>
</td>
<td class="td4" valign="top">
<p class="p5">hdfs://localhost/</p>
</td>
<td class="td5" valign="top">
<p class="p5">hdfs://namenode/</p>
</td>
</tr><tr><td class="td1" valign="top">
<p class="p5">HDFS</p>
</td>
<td class="td2" valign="top">
<p class="p5">dfs.replication</p>
</td>
<td class="td3" valign="top">
<p class="p5">N/A</p>
</td>
<td class="td4" valign="top">
<p class="p5">1</p>
</td>
<td class="td5" valign="top">
<p class="p5">3</p>
<p class="p1">（默<span class="s1">认）</span></p>
</td>
</tr><tr><td class="td1" valign="top">
<p class="p5">MapReduce</p>
</td>
<td class="td2" valign="top">
<p class="p5">mapred.job.tracker</p>
</td>
<td class="td3" valign="top">
<p class="p5">local</p>
<p class="p1">（默<span class="s1">认）</span></p>
</td>
<td class="td4" valign="top">
<p class="p5">localhost:8021</p>
</td>
<td class="td5" valign="top">
<p class="p5">jobtracker:8021</p>
</td>
</tr></tbody></table><p class="p3"> </p>
<p class="p4">三种模式下的配置：</p>
<p class="p1"><strong>本机模式：</strong></p>
<p class="p4"><span class="s3">一般</span><span class="s2">Hadoop</span><span class="s3">安装后默</span>认属性就是本地模式（独立模式），所以一般来说，不用进行更多的配置。</p>
<p class="p1">三个配置文件内容如下：<span class="s1"></span></p>
<p class="p5"><span class="s3">－－－－－</span>core-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">   &lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">   &lt;configuration&gt;</p>
<p class="p9">   &lt;/configuration&gt;</p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p5"><span class="s3">－－－－－</span>mapred-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">&lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">&lt;configuration&gt;</p>
<p class="p9">&lt;/configuration&gt;</p>
<p class="p5"><span class="s3">－－－－－</span>mapred-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">&lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">&lt;configuration&gt;</p>
<p class="p9">&lt;/configuration&gt;</p>
<p class="p9"><br></p>
<p class="p4"><strong>伪分布模式：</strong></p>
<p class="p1">三个配置配置文件内容如下：</p>
<p class="p5"><span class="s3">－－－－－</span>core-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">&lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">&lt;configuration&gt;</p>
<p class="p9">   &lt;property&gt;</p>
<p class="p9">     &lt;name&gt;<span class="s9">fs.default.name</span>&lt;/name&gt;</p>
<p class="p9">     &lt;value&gt;<span class="s9">hdfs://localhost/</span>&lt;/value&gt;</p>
<p class="p9">   &lt;/property&gt;</p>
<p class="p9">&lt;/configuration&gt;</p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p5"><span class="s3">－－－－－</span>mapred-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">&lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">&lt;configuration&gt;</p>
<p class="p9">   &lt;property&gt;</p>
<p class="p9">     &lt;name&gt;<span class="s9">dfs.replication</span>&lt;/name&gt;</p>
<p class="p5">     <span class="s10">&lt;value&gt;</span>1<span class="s10">&lt;/value&gt;</span></p>
<p class="p9">   &lt;/property&gt;</p>
<p class="p9">&lt;/configuration&gt;</p>
<p class="p5"><span class="s3">－－－－－</span>mapred-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">&lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">&lt;configuration&gt;</p>
<p class="p9">   &lt;property&gt;</p>
<p class="p9">     &lt;name&gt;<span class="s9">mapred.job.tracker </span>&lt;/name&gt;</p>
<p class="p9">     &lt;value&gt;<span class="s9">localhost:8021</span>&lt;/value&gt;</p>
<p class="p9">   &lt;/property&gt;</p>
<p class="p9">&lt;/configuration&gt;</p>
<p class="p9"><br></p>
<p class="p1"><strong>全分布模式：</strong></p>
<p class="p1">三个配置配置文件内容如下：</p>
<p class="p5"><span class="s3">－－－－－</span>core-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">&lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">&lt;configuration&gt;</p>
<p class="p9">   &lt;property&gt;</p>
<p class="p9">      &lt;name&gt;<span class="s9">fs.default.name</span>&lt;/name&gt;</p>
<p class="p9">     &lt;value&gt;<span class="s9">hdfs://namenode/</span>&lt;/value&gt;</p>
<p class="p9">   &lt;/property&gt;</p>
<p class="p9">&lt;/configuration&gt;</p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p5"><span class="s3">－－－－－</span>mapred-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">&lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">&lt;configuration&gt;</p>
<p class="p9">   &lt;property&gt;</p>
<p class="p9">     &lt;name&gt;<span class="s9">dfs.replication</span>&lt;/name&gt;</p>
<p class="p5">     <span class="s10">&lt;value&gt;</span>3<span class="s10">&lt;/value&gt;</span></p>
<p class="p9">   &lt;/property&gt;</p>
<p class="p9">&lt;/configuration&gt;</p>
<p class="p5"><span class="s3">－－－－－</span>mapred-site.xml<span class="s3">－－－－－</span></p>
<p class="p9">&lt;?xml version="1.0"?&gt;</p>
<p class="p9">&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p class="p9">&lt;!-- Put site-specific property overrides in this file. --&gt;</p>
<p class="p9">&lt;configuration&gt;</p>
<p class="p9">   &lt;property&gt;</p>
<p class="p9">     &lt;name&gt;<span class="s9">mapred.job.tracker </span>&lt;/name&gt;</p>
<p class="p9">     &lt;value&gt;<span class="s9">jobtracker:8021 </span>&lt;/value&gt;</p>
<p class="p9">   &lt;/property&gt;</p>
<p class="p9">&lt;/configuration&gt;</p>
<p class="p9">参考：http://www.cnblogs.com/tippoint/archive/2012/10/23/2735532.html</p>
<p class="p9">http://www.cnblogs.com/shannyn/archive/2013/08/28/3287443.html<br></p>
            </div>
                </div>
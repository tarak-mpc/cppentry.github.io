---
layout:     post
title:      hadoop伪分布式的安装搭建(Hadoop-2.7.3+jdk1.8+centos6.5)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div id="article_content" class="article_content">
<p><span style="font-size:18px;"><strong>安装4台主机模拟<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:#df3434;font-weight:bold;">Hadoop</a>工作环境:</strong></span></p>
<p><span style="font-size:18px;"><strong>主机                                    ip                        账号密码</strong></span></p>
<p><span style="font-size:18px;"><strong>master                      192.168.40.100 -------hadoop/hadoop</strong></span></p>
<p><span style="font-size:18px;"><strong>weekend001             192.168.40.101 -------hadoop/hadoop</strong></span></p>
<p><span style="font-size:18px;"><strong><span style="font-size:18px;">weekend002             192.168.40.102 -------hadoop/hadoop</span><br></strong></span></p>
<p><span style="font-size:18px;"><strong><span style="font-size:18px;"><span style="font-size:18px;">weekend003             192.168.40.103 -------hadoop/hadoop</span><br></span></strong></span></p>
<p><span style="font-size:18px;"><strong>安装步骤:</strong></span></p>
<p><br></p>
<p><strong><span style="font-size:18px;">1.将网卡切换至nat模式并设置好网关</span></strong></p>
<p>设置网段：192.168.40.0 子网掩码：255.255.255.0 -&gt; apply -&gt; ok</p>
<p>                                               <img src="https://img-blog.csdn.net/20161102102800438?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="248" width="407">  <br></p>
<p><strong><span style="font-size:18px;">2.安装centos6.5</span></strong><br></p>
<p><span style="font-size:14px;"><strong>a.关闭防火墙(在root权限下)----------centos6.5</strong></span></p>
<p><strong><span style="font-size:18px;"> </span></strong><span style="font-size:18px;">#查看防火墙状态<br><span></span>service iptables status<br><span></span>#关闭防火墙<br><span></span>service iptables stop<br><span></span>#查看防火墙开机启动状态<br><span></span>chkconfig iptables --list<br><span></span>#关闭防火墙开机启动<br><span></span><span style="color:#ff0000;">chkconfig iptables off</span></span><strong><span style="font-size:18px;"><br></span></strong></p>
<p><strong><span style="font-size:18px;"><span style="color:#ff0000;"><img src="https://img-blog.csdn.net/20161102102219476?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" align="middle"><br></span></span></strong></p>
<p><span style="color:#FFCC00;"><span style="font-size:18px;">/////////////////////////////////补充一点centos7的防火墙关闭方式与centos6.5不同//////////////////////////////////</span></span></p>
<p><span style="color:#FFCC00;"><span style="font-weight:bold;"><span style="font-size:18px;">关闭方法为</span></span></span></p>
<div style="line-height:1.875;font-size:14px;"><span style="color:#FFCC00;">systemctl stop firewalld.service #停止firewall</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="color:#FFCC00;">systemctl disable firewalld.service #禁止firewall开机启动</span></div>
<div style="line-height:1.5;font-size:14px;"><strong>b.修改ip (建议在图形用户界面下修改)</strong></div>
<div style="line-height:1.5;font-size:14px;"><br></div>
<div style="line-height:1.5;font-size:14px;"><img src="https://img-blog.csdn.net/20161102101954097?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="334" width="261"><br></div>
<div style="line-height:1.5;font-size:14px;">终端下修改的方法</div>
<div style="line-height:1.5;font-size:14px;">vim /etc/sysconfig/network-scripts/ifcfg-eth0<br><span></span><br><span></span>DEVICE="eth0"<br><span></span>BOOTPROTO="static"               ###<br><span></span>HWADDR="00:0C:29:3C:BF:E7"<br><span></span>IPV6INIT="yes"<br><span></span>NM_CONTROLLED="yes"<br><span></span>ONBOOT="yes"<br><span></span>TYPE="Ethernet"<br><span></span>UUID="ce22eeca-ecde-4536-8cc2-ef0dc36d4a8c"<br><span></span><span style="color:#ff0000;">IPADDR="192.168.40.100"           ###<br><span></span>NETMASK="255.255.255.0"          ###<br><span></span>GATEWAY="192.168.40.2"            ###--------------------------------请与vmware配置时的输入一一对应上</span></div>
<div style="line-height:1.5;font-size:14px;"><br></div>
<div style="line-height:1.5;font-size:14px;"><strong>c.将创建的用户添加到sudoers的列表下</strong></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-family:Arial;font-size:14px;color:#333333;">切换到root账户，修改/etc/sudoers文件，增加：hadoop  ALL=(ALL)  ALL</span><br></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-family:Arial;font-size:14px;color:#333333;"><img src="https://img-blog.csdn.net/20161102102338555?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" align="middle" height="105" width="629"><br></span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-family:Arial;font-size:14px;color:#333333;"><br><strong>d.</strong></span><strong><span style="font-family:Arial;color:#333333;">修改/etc/hosts 文件，增加4台机器的ip和hostname的映射关系</span></strong></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-family:Arial;color:#333333;"><img src="https://img-blog.csdn.net/20161102102433899?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" align="middle" height="133" width="590"><br></span></div>
<div style="line-height:1.5;font-size:14px;">
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;"><strong>e.修改主机名</strong></p>
<p style="font-family:Arial;font-size:14px;">sudo vi /etc/sysconfig/network--------------------------------每个节点都要修改</p>
<div style="color:rgb(51,51,51);line-height:1.875;font-size:14px;">
<img src="https://img-blog.csdn.net/20161102102549325?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="70" width="229"><br></div>
<div style="line-height:1.875;font-size:14px;">
<div style="line-height:1.875;font-size:14px;"><span style="color:#ff0000;">sudo hostname master</span></div>
<div style="color:rgb(51,51,51);line-height:1.875;font-size:14px;">
//即可将主机名改为hadoop<br></div>
<div style="color:rgb(51,51,51);line-height:1.875;font-size:14px;">
重启生效</div>
<div style="color:rgb(51,51,51);line-height:1.875;font-size:14px;">
<span style="font-size:18px;"><strong>3.安装jdk</strong><span style="font-size:14px;"><br>
在hadoop文件夹下创建APP文件夹<br>
mkdir /home/hadoop/app<br><span style="font-size:18px;"><span style="font-size:14px;">通过ftp服务器将jdk文件,hadoop文件,eclipse等文件上传至虚拟机.</span></span><br><strong>a. 解压jdk</strong></span><strong><br></strong>tar -zxvf <span style="font-size:18px;">jdk-8u111-linux-x64.tar.gz</span> -C /home/hadoop/app<br><img src="https://img-blog.csdn.net/20161102105131687?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" align="middle" height="51" width="463"><br><br><strong>b.sudo vim /etc/profile</strong><br>
在文件末尾添加<br><span style="color:#FF0000;">export JAVA_HOME=/home/hadoop/app/jdk1.8.0_111<br>
export PATH=$JAVA_HOME/bin:$PATH<br>
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar</span><br><img src="https://img-blog.csdn.net/20161102105747101?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="88" width="693"><br><strong>c.刷新</strong><br>
source /etc/profile<br><strong>d.验证配置</strong><br><img src="https://img-blog.csdn.net/20161102110040793?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" align="middle" height="98" width="502"><br><strong>4.安装配置hadoop<br><span style="font-size:14px;">a.tar -zvxf hadoop-2.7.3.tar.gz -C /home/hadoop/app<br>
b.sudo vim /etc/profile</span></strong><br>
在文件末尾添加<br>
export HADOOP_HOME=/home/hadoop/app/hadoop-2.7.3<br>
export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin(修改)<br><img src="" alt="" height="45" width="475"><br><strong><span style="font-size:14px;">c.刷新配置文件</span></strong><br>
source /etc/profile<br><strong><span style="font-size:14px;">d.验证配置文件</span></strong><br>
hadoop version<br><img src="https://img-blog.csdn.net/20161102111927864?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" align="middle" height="142" width="508"><br><strong>5.修改配置文件(7个)<br></strong><br>
注意：hadoop2.x的配置文件$HADOOP_HOME/etc/hadoop<br></span>
<p><span style="font-size:14px;">配置之前，需要在app本地文件系统创建以下文件夹：</span></p>
<p><span style="font-size:14px;"><span></span></span><span style="font-size:18px;"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20161102112821397?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="166" width="405"><br></span></span></p>
<p><br><span style="font-size:18px;"><span style="font-size:14px;"></span></span></p>
<span style="font-size:14px;"><strong>a.hadoop-env.sh</strong><br></span></div>
<span style="color:#333333;"></span>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> <a href="#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><span> <a href="#" rel="nofollow" class="CopyToClipboard" title="copy">
copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span><a href="#" rel="nofollow" class="PrintSource" title="print">print</a></span><a href="#" rel="nofollow" class="About" title="?">?</a></div>
</div>
<ol start="1"><li class="alt"><span><span>[hadoop@master hadoop]$ echo $JAVA_HOME  </span></span></li><li><span>/home/hadoop/app/jdk1.8.0_111  </span></li><li class="alt"><span>[hadoop@master hadoop]$ vi hadoop-env.sh  </span></li></ol><div class="save_code tracking-ad"><a href="" rel="nofollow"><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div>
</div>
<pre><code class="language-plain">[hadoop@master hadoop]$ echo $JAVA_HOME
/home/hadoop/app/jdk1.8.0_111
[hadoop@master hadoop]$ vi hadoop-env.sh</code></pre><img src="https://img-blog.csdn.net/20161102145136287?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="">-----在第25/26行<br></div>
<span style="color:#333333;"> </span>
<p>b.core-site.xml</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;display:block;font-size:14px;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.0470588);border:1px solid rgba(128,128,128,.0745098);font-style:normal;font-weight:normal;letter-spacing:normal;text-align:left;text-indent:0px;text-transform:none;word-spacing:0px;">&lt;code class="hljs xml has-numbering" style="display: block; padding: 0px; background: transparent; color: inherit; box-sizing: border-box; font-family: "Source Code Pro", monospace;font-size:undefined; white-space: pre; border-radius: 0px; word-wrap: normal;"&gt;&lt;configuration&gt;

 &lt;property&gt;
&lt;!-- 指定hadoop运行时产生文件的存储目录 --&gt;
 &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
 &lt;value&gt;/home/hadoop/tmp&lt;/value&gt;
 &lt;/property&gt;
&lt;!-- 指定HADOOP所使用的文件系统schema（URI），HDFS的老大（NameNode）的地址 --&gt;
 &lt;property&gt;
 &lt;name&gt;fs.defaultFS&lt;/name&gt;
&lt;value&gt;hdfs://master:9000&lt;/value&gt;
&lt;/property&gt;
 &lt;property&gt;  

 &lt;name&gt;io.file.buffer.size&lt;/name&gt;
 &lt;value&gt;131072&lt;/value&gt;
 &lt;/property&gt;
&lt;/configuration&gt;
&lt;/code&gt;</pre><div class="save_code tracking-ad" style="display:none;"><a><img src="http://static.blog.csdn.net/images/save_snippets_01.png" alt="save_snippets_01.png"></a></div><strong>c.hdfs-site.xml</strong>
<p></p>
<p></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> <a href="#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><span> <a href="#" rel="nofollow" class="CopyToClipboard" title="copy">
copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span><a href="#" rel="nofollow" class="PrintSource" title="print">print</a></span><a href="#" rel="nofollow" class="About" title="?">?</a></div>
</div>
<ol start="1"><li class="alt"><span><span>&lt;configuration&gt;  </span></span></li><li><span>&lt;!-- 指定HDFS副本的数量 --&gt;  </span></li><li class="alt"><span>                &lt;property&gt;  </span></li><li><span>                &lt;name&gt;dfs.replication&lt;/name&gt;  </span></li><li class="alt"><span>                &lt;value&gt;1&lt;/value&gt;  </span></li><li><span>                &lt;/property&gt;  </span></li><li class="alt"><span>&lt;/configuration&gt;  </span></li><li><span>&lt;pre name="code" class="plain"&gt;&lt;property&gt;  </span></li><li class="alt"><span> &lt;name&gt;dfs.namenode.secondary.http-address&lt;/name&gt;  </span></li><li><span>&lt;value&gt;master:9001&lt;/value&gt;  </span></li><li class="alt"><span>&lt;/property&gt;  </span></li><li><span>  </span></li><li class="alt"><span>         &lt;property&gt;  </span></li><li><span> &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;  </span></li><li class="alt"><span>&lt;value&gt;file:/home/hadoop/hdfs/name&lt;/value&gt;  </span></li><li><span> &lt;/property&gt;  </span></li><li class="alt"><span>  </span></li><li><span>           &lt;property&gt;  </span></li><li class="alt"><span>&lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;  </span></li><li><span> &lt;value&gt;file:/home/hadoop/hdfs/data&lt;/value&gt;  </span></li><li class="alt"><span> &lt;/property&gt;  </span></li></ol><div class="save_code tracking-ad"><a href="" rel="nofollow"><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div>
</div>
<pre><code class="language-plain">&lt;configuration&gt;
&lt;!-- 指定HDFS副本的数量 --&gt;
                &lt;property&gt;
                &lt;name&gt;dfs.replication&lt;/name&gt;
                &lt;value&gt;1&lt;/value&gt;
                &lt;/property&gt;
&lt;/configuration&gt;
&lt;pre name="code" class="plain"&gt;&lt;property&gt;
 &lt;name&gt;dfs.namenode.secondary.http-address&lt;/name&gt;
&lt;value&gt;master:9001&lt;/value&gt;
&lt;/property&gt;

         &lt;property&gt;
 &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;
&lt;value&gt;file:/home/hadoop/hdfs/name&lt;/value&gt;
 &lt;/property&gt;

           &lt;property&gt;
&lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;
 &lt;value&gt;file:/home/hadoop/hdfs/data&lt;/value&gt;
 &lt;/property&gt;</code></pre><br><div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> <a href="#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><span> <a href="#" rel="nofollow" class="CopyToClipboard" title="copy">
copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span><a href="#" rel="nofollow" class="PrintSource" title="print">print</a></span><a href="#" rel="nofollow" class="About" title="?">?</a></div>
</div>
<ol start="1"><li class="alt"><span><span>&lt;property&gt;  </span></span></li><li><span> &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;  </span></li><li class="alt"><span>    &lt;value&gt;true&lt;/value&gt;  </span></li><li><span> &lt;/property&gt;  </span></li></ol><div class="save_code tracking-ad"><a href="" rel="nofollow"><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div>
</div>
<pre><code class="language-plain">&lt;property&gt;
 &lt;name&gt;dfs.webhdfs.enabled&lt;/name&gt;
    &lt;value&gt;true&lt;/value&gt;
 &lt;/property&gt;</code></pre><br><p></p>
<pre></pre>
<strong>d.mapred-site.xml</strong>
<p></p>
<p><span>需要重命名： mv mapred-site.xml.template mapred-site.xml</span></p>
<p></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> <a href="#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><span> <a href="#" rel="nofollow" class="CopyToClipboard" title="copy">
copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span><a href="#" rel="nofollow" class="PrintSource" title="print">print</a></span><a href="#" rel="nofollow" class="About" title="?">?</a></div>
</div>
<ol start="1"><li class="alt"><span><span>&lt;!-- 指定mr运行在yarn上 --&gt;  </span></span></li><li><span>        &lt;property&gt;  </span></li><li class="alt"><span>            &lt;name&gt;mapreduce.framework.name&lt;/name&gt;  </span></li><li><span>            &lt;value&gt;yarn&lt;/value&gt;  </span></li><li class="alt"><span>    &lt;/property&gt;  </span></li><li><span>&lt;pre name="code" class="plain"&gt;&lt;property&gt;  </span></li><li class="alt"><span> &lt;name&gt;mapreduce.jobhistory.address&lt;/name&gt;  </span></li><li><span>&lt;value&gt;master:10020&lt;/value&gt;  </span></li><li class="alt"><span> &lt;/property&gt;  </span></li><li><span>    </span></li><li class="alt"><span>  &lt;property&gt;  </span></li><li><span>  &lt;name&gt;mapreduce.jobhistory.webapp.address&lt;/name&gt;                </span></li><li class="alt"><span>  &lt;value&gt;master:19888&lt;/value&gt;  </span></li><li><span>  &lt;/property&gt;  </span></li></ol><div class="save_code tracking-ad"><a href="" rel="nofollow"><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div>
</div>
<pre><code class="language-plain">&lt;!-- 指定mr运行在yarn上 --&gt;
		&lt;property&gt;
			&lt;name&gt;mapreduce.framework.name&lt;/name&gt;
			&lt;value&gt;yarn&lt;/value&gt;
    &lt;/property&gt;
&lt;pre name="code" class="plain"&gt;&lt;property&gt;
 &lt;name&gt;mapreduce.jobhistory.address&lt;/name&gt;
&lt;value&gt;master:10020&lt;/value&gt;
 &lt;/property&gt;
  
  &lt;property&gt;
  &lt;name&gt;mapreduce.jobhistory.webapp.address&lt;/name&gt;              
  &lt;value&gt;master:19888&lt;/value&gt;
  &lt;/property&gt;</code></pre><br><br><p></p>
<pre></pre>
<strong>e.yarn-site.xml</strong>
<p></p>
<p></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> <a href="#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><span> <a href="#" rel="nofollow" class="CopyToClipboard" title="copy">
copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span><a href="#" rel="nofollow" class="PrintSource" title="print">print</a></span><a href="#" rel="nofollow" class="About" title="?">?</a></div>
</div>
<ol start="1"><li class="alt"><span><span>&lt;!-- 指定YARN的老大（ResourceManager）的地址 --&gt;  </span></span></li><li><span>        &lt;property&gt;  </span></li><li class="alt"><span>            &lt;name&gt;yarn.resourcemanager.hostname&lt;/name&gt;  </span></li><li><span>            &lt;value&gt;master&lt;/value&gt;  </span></li><li class="alt"><span>    &lt;/property&gt;  </span></li><li><span>        &lt;!-- reducer获取数据的方式 --&gt;  </span></li><li class="alt"><span>    &lt;property&gt;  </span></li><li><span>            &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;  </span></li><li class="alt"><span>            &lt;value&gt;mapreduce_shuffle&lt;/value&gt;  </span></li><li><span>     &lt;/property&gt;  </span></li></ol><div class="save_code tracking-ad"><a href="" rel="nofollow"><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div>
</div>
<pre><code class="language-plain">&lt;!-- 指定YARN的老大（ResourceManager）的地址 --&gt;
		&lt;property&gt;
			&lt;name&gt;yarn.resourcemanager.hostname&lt;/name&gt;
			&lt;value&gt;master&lt;/value&gt;
    &lt;/property&gt;
		&lt;!-- reducer获取数据的方式 --&gt;
    &lt;property&gt;
			&lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
			&lt;value&gt;mapreduce_shuffle&lt;/value&gt;
     &lt;/property&gt;</code></pre><br><strong>f.yarn-env.sh</strong>
<p></p>
<p><img src="" alt="" height="62" width="403"></p>
<p><strong>g.vi slaves/master(需新建)</strong></p>
<p><img src="" alt="" height="66" width="361"></p>
<p>vi master</p>
<p>master<br></p>
<p><span style="font-size:24px;"><strong>6.配置三台datanode节点</strong></span></p>
<p><span style="font-size:18px;"><strong>a.复制虚拟机</strong></span></p>
<p><span style="font-size:18px;"><strong>b.修改ip地址</strong></span></p>
<p><span style="font-size:18px;"><strong>c.修改主机名及配置文件</strong></span></p>
<p><span style="font-size:18px;"><strong><img src="https://img-blog.csdn.net/20161102192344792?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" align="middle" height="318" width="462">----------退出重新登录生效<br></strong></span></p>
<p><span style="font-size:24px;"><strong><span style="font-size:18px;">d.互通验证</span></strong></span></p>
<p><span style="font-size:24px;"><strong><span style="font-size:18px;">e.为节约资源关闭图形用户界面</span></strong></span></p>
<p><span style="font-size:24px;"><span style="font-size:14px;">sudo vi /etc/inittab--------------由5改为3,重启生效.</span></span></p>
<p><span style="font-size:24px;"><span style="font-size:14px;"></span><strong><img src="https://img-blog.csdn.net/20161102193527859?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="98" width="341"><br></strong></span></p>
<p><span style="font-size:24px;"><strong>7.ssh免秘钥登录</strong></span></p>
<p><span style="font-size:24px;"><strong></strong></span></p>
<p>a.查看SSH是否安装。<br></p>
<p>输入命令：rpm -qa | grep ssh<span style="font-size:14px;"><strong></strong></span></p>
<div class="content-list-text">
<p><em>注：若没安装SSH则可输入：yum install openssh-server安装。</em></p>
</div>
<span style="font-size:24px;"><strong><span style="font-size:14px;">sodu apt-get install ssh(ubuntu)</span></strong></span><br><p></p>
<p><span style="font-size:14px;">b.生成公钥</span><span style="font-size:14px;"></span></p>
<p><span style="font-size:14px;">安装完成后会在~目录（当前用户主目录，即这里的/home/h<strong>adoop</strong>）下产生一个隐藏文件夹.ssh（ls  -a 可以查看隐藏文件）。如果没有这个文件，自己新建即可（mkdir .ssh）。</span></p>
<span style="font-size:14px;"></span>
<p><span style="font-size:14px;"><span></span>具体步骤如下：</span></p>
<p><span style="font-size:14px;"><span></span>1、 进入.ssh文件夹</span></p>
<p><span style="font-size:14px;"><span></span>2、 ssh-keygen -t  rsa 之后一路回 车（产生秘钥）</span></p>
<img src="https://img-blog.csdn.net/20161102195227031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p>3.cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys  <br><img src="https://img-blog.csdn.net/20161102195855664?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" height="75" width="545"></p>
<p><strong>c. chmod 600 ~/.ssh/authorized_keys<br></strong></p>
<p><strong>d.scp ~/.ssh/id_rsa.pub  hadoop@192.168.40.102:~/.ssh(以此类推三台都要复制一遍)</strong></p>
<p><strong></strong></p>
<div style="line-height:1.5;font-size:14px;">生成authorized_keys.</div>
<div style="line-height:1.5;font-size:14px;"><span style="color:#3333ff;">cat id_rsa.pub &gt;&gt; authorized_keys</span></div>
<span style="color:#3333ff;">chmod 700 ~/.ssh</span>
<div style="line-height:1.5;font-size:14px;"><span style="color:#3333ff;">chmod 600 ~/.ssh/authorized_keys</span></div>
<strong>e.ssh互通测试</strong><br><p></p>
<p><span style="font-size:24px;"><strong>8.启动</strong></span></p>
<p></p>
<p>第一次启动得格式化</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;display:block;font-size:14px;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.0470588);border:1px solid rgba(128,128,128,.0745098);font-style:normal;font-weight:normal;letter-spacing:normal;text-align:left;text-indent:0px;text-transform:none;word-spacing:0px;">hdfs namenode -format或
hadoop namenode -format
</pre><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div>
<p>启动dfs</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;display:block;font-size:14px;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.0470588);border:1px solid rgba(128,128,128,.0745098);font-style:normal;font-weight:normal;letter-spacing:normal;text-align:left;text-indent:0px;text-transform:none;word-spacing:0px;">start-dfs.sh
</pre><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div>
<p>启动yarn</p>
<pre class="prettyprint" style="font-family:'Source Code Pro', monospace;display:block;font-size:14px;line-height:1.45;color:rgb(51,51,51);background-color:rgba(128,128,128,.0470588);border:1px solid rgba(128,128,128,.0745098);font-style:normal;font-weight:normal;letter-spacing:normal;text-align:left;text-indent:0px;text-transform:none;word-spacing:0px;">start-yarn.sh
</pre><div class="save_code tracking-ad"><a><img src="http://static.blog.csdn.net/images/save_snippets.png" alt="save_snippets.png"></a></div><span style="font-size:24px;"><strong><span style="font-size:24px;"><strong>9.测试</strong></span></strong></span>
<p>jps</p>
<p>master</p>
<p><img src="https://img-blog.csdn.net/20161103162216086?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>datanode</p>
<p><img src="" alt=""><br></p>
<p>http://192.168.40.100:50070/dfshealth.html#tab-overview</p>
<p><img src="https://img-blog.csdn.net/20161103162329383?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>http://192.168.40.100:8088/cluster</p>
<p><img src="" alt=""><br></p>
<p></p>
<p>可以看到一切正常。<strong>3</strong>个节点。</p>
<p>至此我们的<strong>4</strong>台hadoop运行</p>
<br><span style="font-size:14px;"></span></div>
<p></p>
</div>
            </div>
                </div>
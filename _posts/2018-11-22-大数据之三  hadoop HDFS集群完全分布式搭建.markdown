---
layout:     post
title:      大数据之三  hadoop HDFS集群完全分布式搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2><a id="_0"></a>完全分布式</h2>
<p>多台服务器，每台服务器启动表示不同角色的不同进程，组成HDFS集群<br>
现在以四台虚拟机为例，主机名分别为node01,node02,node03,node04.<br>
node01–&gt;NameNode<br>
node02–&gt;SecondaryNameNode  DateNode<br>
node03–&gt;DateNode<br>
node04–&gt;DataNode</p>
<h2><a id="_9"></a>完全分布式搭建步骤</h2>
<ol>
<li>首先确保各节点都配置好<a href="https://blog.csdn.net/xingyao231/article/details/83006595" rel="nofollow">静态IP</a></li>
<li>配置各节点的hosts，修改/etc/hosts文件</li>
</ol>
<pre><code>[root@node01 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.23.130 node01
192.168.23.131 node02
192.168.23.133 node03
192.168.23.134 node04

</code></pre>
<ol start="3">
<li>
<p>关闭各节点的防火墙<br>
使用 <code>service iptables status</code>命令查看当前防火墙状态<br>
使用 <code>service iptables stop</code>暂时关闭防火墙（防火墙会在下次开机时启动）<br>
使用 <code>chkconfig iptables off</code>永久关闭防火墙（该命令在下次重启时生效）</p>
</li>
<li>
<p>时间同步<br>
首先在各个节点上使用<code>yum install ntp</code>安装ntp命令<br>
执行<code>ntpdate ntp1.aliyun.com</code>来同步时间（这里使用阿里云服务器的时间）</p>
</li>
<li>
<p>配置SSH免密登录<br>
在所有节点上执行<code>ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa</code><br>
在node01节点上执行以下命令（将node01的公钥加入到其他节点的白名单中）</p>
</li>
</ol>
<pre><code>ssh-copy-id -i ~/.ssh/id_rsa.pub root@node01
ssh-copy-id -i ~/.ssh/id_rsa.pub root@node02
ssh-copy-id -i ~/.ssh/id_rsa.pub root@node03
ssh-copy-id -i ~/.ssh/id_rsa.pub root@node04
</code></pre>
<ol start="6">
<li>所有节点配置JDK<br>
所有节点在 /etc/profile 中配置JDK环境变量</li>
</ol>
<pre><code>export JAVA_HOME=/opt/zgl/jdk1.8.0_151		&lt;!-- 你的JDK的实际位置 --&gt;
export PATH=$PATH:$JAVA_HOME/bin
</code></pre>
<ol start="7">
<li>在node01节点（即NameNode节点）配置hadoop<br>
修改hdfs-site.xml配置文件</li>
</ol>
<pre><code>    &lt;property&gt;
            &lt;name&gt;dfs.replication&lt;/name&gt;
            &lt;value&gt;3&lt;/value&gt;	&lt;!-- 总存储分数（备份数+1）--&gt;
    &lt;/property&gt;
    
    &lt;property&gt;     
     &lt;name&gt;dfs.namenode.secondary.http-address&lt;/name&gt;
         &lt;value&gt;node02:50090&lt;/value&gt;	   &lt;!-- SecondaryNameNode的位置 --&gt;
    &lt;/property&gt;
</code></pre>
<p>修改core-site.xml配置文件</p>
<pre><code>&lt;property&gt;
        &lt;name&gt;fs.defaultFS&lt;/name&gt;		&lt;!--  HDFS文件默认地址前缀（简化实际访问时路径书写） --&gt;
        &lt;value&gt;hdfs://node01:9000&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
        &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;			&lt;!-- HDFS文件存储位置 --&gt;
        &lt;value&gt;/var/abc/hadoop/cluster&lt;/value&gt;
&lt;/property&gt;
</code></pre>
<p>修改slaves配置文件<br>
配置DataNode节点，注意每行写一个</p>
<pre><code>node02
node03
node04
</code></pre>
<p>修改hadoop-env.sh配置文件</p>
<pre><code>    # The java implementation to use.
    export JAVA_HOME=/opt/zgl/jdk1.8.0_151
    # 在hadoop-env.sh 配置一条hadoop配置文件所在目录
    export HADOOP_CONF_DIR=/opt/zgl/hadoop-2.6.5/etc/hadoop
</code></pre>
<p>刷新配置 <code>source hadoop-env.sh</code></p>
<ol start="8">
<li>将配置好的hadoop安装包分发到其他节点上<br>
使用  scp 命令将hadoop安装包分发到其他节点,注意其他节点提前建好目录且与node01目录（名称，层级）保持一致<br>
eg: node01 /opt/zgl/hadoop-2.6.5      其他节点  /opt/zgl</li>
</ol>
<pre><code>scp -r hadoop-2.6.5 root@node02:/opt/zgl/
scp -r hadoop-2.6.5 root@node03:/opt/zgl
scp -r hadoop-2.6.5 root@node04:/opt/zgl/
</code></pre>
<ol start="9">
<li>关闭所有节点上的HDFS相关进程</li>
</ol>
<pre><code>stop-dfs.sh
</code></pre>
<ol start="10">
<li>在node01节点执行格式化NameNode</li>
</ol>
<pre><code>hdfs namenode -format
</code></pre>
<ol start="11">
<li>在node01节点上启动HDFS</li>
</ol>
<pre><code>start-dfs.sh
</code></pre>
<ol start="12">
<li>在各节点上运行jps命令查看节点是否启动</li>
</ol>
<pre><code>[root@node01 ~]# jps
2408 NameNode
3545 Jps

[root@node02 ~]# jps
2225 DataNode
2290 SecondaryNameNode
4283 Jps

[root@node03 ~]# jps
3252 Jps
2110 DataNode

[root@node04 ~]# jps
4662 Jps
2111 DataNode

</code></pre>
<p>这里我们也可以通过hadoop提供的 web UI 来进行查看<br>
打开浏览器，输入<code>http://192.168.23.130:50070</code>（因为主机 hosts 文件中并未配置node01，所以这里不能使用 <code>http://node01:50070</code>来访问），然后我们就来到如下页面<br>
<img src="https://img-blog.csdn.net/20181010214426485?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="node01 WEB UI"><br>
在 Summary 中可以看到 Live Nodes（存活节点数），点击 Live Nodes可以进入节点详情页<br>
<img src="https://img-blog.csdn.net/20181010214710503?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Live Nodes"><br>
<img src="https://img-blog.csdn.net/20181010214821646?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="Live Nodes详情页"></p>
<h2><a id="_160"></a>思维导图</h2>
<p><img src="https://img-blog.csdn.net/20181010215529696?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpbmd5YW8yMzE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="完全分布式搭建思维导图"></p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
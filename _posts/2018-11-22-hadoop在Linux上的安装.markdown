---
layout:     post
title:      hadoop在Linux上的安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>注意：hadoop2.x的配置文件$HADOOP_HOME/etc/hadoop</p>
<p><br></p>
2.1上传hadoop包<br><span></span>
<p>下载地址: http://archive.apache.org/dist/hadoop/core/current/</p>
<span></span>2.2解压hadoop包<br><span></span>一般解压到/usr/local<br><span></span>tar -zxvf hadoop-3.0.0-beta1.tar.gz <br><span></span>2.3配置hadoop伪分布式（要修改4个文件）<br><span></span>cd /usr/local/hadoop-3.0.0-beta1/etc/hadoop<br><span></span>伪分布式需要修改5个配置文件<br><span></span>3.1配置hadoop<br><span></span>第一个：hadoop-env.sh<br><span></span>vim hadoop-env.sh<br><span></span> <br><span></span>export JAVA_HOME=/usr/java/jdk1.7.0_65<br><span></span><br><span></span>第二个：core-site.xml<br><span></span>vim core-site.xml<br><span></span><br><span></span>&lt;configuration&gt;<br><span></span>&lt;!-- 指定HDFS的namenode的通信地址 --&gt;<br><span></span>&lt;property&gt;<br><span></span>&lt;name&gt;fs.default&lt;/name&gt;<br><span></span>&lt;value&gt;hdfs://192.168.174.131:9000&lt;/value&gt;<br><span></span>&lt;/property&gt;<br><span></span>&lt;!-- 指定hadoop运行时产生文件的存放目录 --&gt;<br><span></span>&lt;property&gt;<br><span></span>&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;<br><span></span>&lt;value&gt;/usr/local/hadoop-3.0.0-beta1/tmp&lt;/value&gt;<br><span></span>&lt;/property&gt;<br><span></span>&lt;/configuration&gt;<br><span></span><br><span></span>第三个：hdfs-site.xml<br><span></span>&lt;!-- 指定HDFS副本的数量 --&gt;<br><span></span>&lt;property&gt;<br><span></span>&lt;name&gt;dfs.replication&lt;/name&gt;<br><span></span>&lt;value&gt;1&lt;/value&gt;<br>
        &lt;/property&gt;<br><span></span><br><span></span>第四个：mapred-site.xml (mv mapred-site.xml.template mapred-site.xml)<br><span></span>mv mapred-site.xml.template mapred-site.xml<br><span></span>vim mapred-site.xml<br><span></span>&lt;!-- 指定mr运行在yarn上 --&gt;<br><span></span>&lt;property&gt;<br><span></span>&lt;name&gt;mapreduce.framework.name&lt;/name&gt;<br><span></span>&lt;value&gt;yarn&lt;/value&gt;<br>
        &lt;/property&gt;<br><span></span><br><span></span>第五个：yarn-site.xml<br><span></span>&lt;!-- 指定YARN的老大（ResourceManager）的地址 --&gt;<br><span></span>&lt;property&gt;<br><span></span>&lt;name&gt;yarn.resourcemanager.hostname&lt;/name&gt;<br><span></span>&lt;value&gt;192.168.174.131&lt;/value&gt;<br>
        &lt;/property&gt;<br><span></span>&lt;!-- reducer获取数据的方式 --&gt;<br>
        &lt;property&gt;<br><span></span>&lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;<br><span></span>&lt;value&gt;mapreduce_shuffle&lt;/value&gt;<br><p>        &lt;/property&gt;</p>
<span></span><br><span></span>3.2将hadoop添加到环境变量<br><span></span><br><span></span>vim /etc/profile<br><span></span>export JAVA_HOME=/usr/local/jdk1.7.0_65<br><span></span>export HADOOP_HOME= /hadoop-2.4.1<br><span></span>export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin<br><br><br><span></span>source /etc/profile<br><span></span><br><span></span>3.3格式化namenode（是对namenode进行初始化）<br><span></span>hdfs namenode -format (hadoop namenode -format)<br><span></span><br><span></span>3.4启动hadoop<br><span></span>先启动HDFS<br><span></span>sbin/start-dfs.sh<br><span></span><br><span></span>再启动YARN<br><span></span>sbin/start-yarn.sh<br><span></span><br><span></span>3.5验证是否启动成功<br><span></span>使用jps命令验证<br><span></span>27408 NameNode<br><span></span>28218 Jps<br><span></span>27643 SecondaryNameNode<br><span></span>28066 NodeManager<br><span></span>27803 ResourceManager<br><span></span>
<p>27512 DataNode</p>
<p>我的启动之后只有3个,参见一下链接才修改好:</p>
<p>http://erhuo.org/a_606.html<br></p>
<span></span><br><span></span>http://192.168.174.131:50070 （HDFS管理界面）<br><span></span>http://192.168.174.131:8088 （MR管理界面）<br><span></span><br>
4.配置ssh免登陆<br><span></span>#生成ssh免登陆密钥<br><span></span>#进入到我的home目录<br><span></span>cd ~/.ssh<br><br><span></span>ssh-keygen -t rsa （四个回车）<br><span></span>执行完这个命令后，会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）<br><span></span>
<p>将公钥拷贝到要免登陆的机器上</p>
<p>cp /root/.ssh/id_rsa.pub /root/.ssh/authorized_keys<br>
#测试成功效果<br>
ssh 127.0.0.1<br></p>
<span></span>ssh-copy-id localhost<br><span></span>
            </div>
                </div>
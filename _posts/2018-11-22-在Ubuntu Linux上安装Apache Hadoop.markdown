---
layout:     post
title:      在Ubuntu Linux上安装Apache Hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Hadoop是Apache软件基金会所研发的分布式系统框架。其中，MapReduce和HDFS是Hadoop的两大核心模块。MapReduce是用于大规模数据集并行计算的编程模型。HDFS是面向大规模数据集的分布式文件系统。本文的目的是采用<a href="http://f.dataguru.cn/thread-68146-1-1.html" rel="nofollow">伪分布模式</a>，在Ubuntu Linux上安装Apache Hadoop。</p>
<p><br></p>
<p><strong>0.准备</strong></p>
<p>如果安装Hadoop是出于学习目的，建议首先安装<a href="http://www.vmware.com/cn" rel="nofollow">VMware Workstation</a>。VMware Workstation是一款功能强大的桌面虚拟计算机软件，通常称为“虚拟机”。在虚拟机中，需要安装以下软件：</p>
<p><a href="http://www.ubuntu.org.cn/download/desktop" rel="nofollow">Ubuntu Linux</a> 12.04 LTS（根据需求选择32位或者64位，本文选用64位）</p>
<p><a href="http://mirror.tcpdiag.net/apache/hadoop/common/hadoop-1.2.1/" rel="nofollow">hadoop -1.2.1.tar.gz</a></p>
<p><a href="http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html#jdk-7u40-oth-JPR" rel="nofollow">jdk-7u40-linux-x64.tar.gz</a></p>
<p><a href="http://www.eclipse.org/downloads/?osType=linux&amp;release=undefined" rel="nofollow">Eclipse IDE for JAVA EE Developers</a></p>
<p><a href="http://pan.baidu.com/s/1mVmQE" rel="nofollow">hadoop-eclipse-plugin-1.2.1.jar</a></p>
<p><br></p>
<p><strong>1.安装JDK</strong></p>
<p>Hadoop是由Java语言编写，因此安装Hadoop之前需要安装JDK。本文将JDK的安装包下载到了/home/hduser/Downloads目录，并计划安装到/usr/lib目录，该安装目录可以任意指定。然后，用tar命令解压。具体操作如下，打开Ubuntu的终端，输入以下命令：</p>
<p></p>
<pre><code class="language-plain">sudo cp /home/hduser/Downloads/jdk-7u40-linux-x64.tar.gz /usr/lib 
sudo tar xzf jdk-7u40-linux-x64.tar.gz /usr/lib 
sudo gedit /etc/profile
</code></pre>
<p>在/etc/profile文件的末尾添加以下代码：</p>
<p></p>
<pre><code class="language-plain">export JAVA_HOME=/usr/lib/jdk1.7.0_40
export JRE_HOME=/usr/lib/jdk1.7.0_40/jre
export PATH=$JAVA_HOME/bin:$JAVA_HOME/jre/bin:$PATH
export CLASSPATH=$CLASSPATH:.:$JAVA_HOME/lib:$JAVA_HOME/jre/lib</code></pre>
<p>保存退出/etc/profile后，使用source命令更新/etc/profile。使用java -version测试JDK是否安装成功。</p>
<p></p>
<pre><code class="language-plain">source /etc/profile
java -version</code></pre>如果返回下图所示的结果，那么表明JDK安装成功！<br><p></p>
<p><img src="https://img-blog.csdn.net/20131015213941046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p><br></p>
<p><strong>2.安装及配置SSH</strong></p>
<p>Hadoop采用<a href="http://baike.baidu.com/link?url=mB_Wo8FFBxodlb8mXounc-ml20gWG7dlYOVv9sp6u-tnAUeLmBTaykVWm6kKcoY3PIAJ5WAotO0Mo9ZZ2D6AGESIk8jUVlwNvlIY2bZU0ULGW3DCj2UvU6PTMgOtnQEr" rel="nofollow">SSH</a>协议。为了用户可以无密码登录，需要安装SSH并进行相应配置。如果使用CentOS，那么系统自带SSH，不需要另外安装。通过ssh
 localhost命令测试本机是否已安装了SSH。</p>
<p><img src="https://img-blog.csdn.net/20131015215915593?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>如果返回上图所示的结果，那么表明本机没有安装SSH。通过在终端输入下面的命令安装SSH：</p>
<p></p>
<pre><code class="language-plain">sudo apt-get install openssh-server
sudo /etc/init.d/ssh start
ps -e|grep ssh</code></pre>
<p>如果返回下图所示的结果，那么表明SSH安装成功。</p>
<p><img src="https://img-blog.csdn.net/20131016140327375?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p>接下来对SSH进行配置。</p>
<p></p>
<pre><code class="language-plain">ssh-keygen -t rsa -P ""</code></pre>
<p>返回结果：</p>
<p><img src="https://img-blog.csdn.net/20131016142621203?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p></p>
<p>系统将生成rsa密钥对，分别是私钥id_rsa和公钥id_rsa.pub。</p>
<p><img src="https://img-blog.csdn.net/20131016143344093?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""></p>
<p>接下来，将公钥添加到authorized_keys中：</p>
<p></p>
<pre><code class="language-plain">cd /home/hduser/.ssh
ls
cat id_rsa.pub &gt;&gt; authorized_keys</code></pre>再次使用ssh localhost命令，输入yes，与上次相比，返回了不一样的输出结果：<br><p><img src="https://img-blog.csdn.net/20131016143602000?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p><br></p>
<p><strong>3.安装Hadoop</strong></p>
<p>在安装Hadoop之前，先创建一个名为hadoop的group。</p>
<pre><code class="language-plain">sudo addgroup hadoop
sudo adduser --ingroup hadoop hduser</code></pre>
<p>正如上述安装JDK那样，Hadoop的安装位置也可以任意指定。本文选择/usr/local/hadoop作为安装目录。将Hadoop安装包移动到该目录后解压出来，并修改文件夹的所有者。注意，这一步很重要，<strong>如果文件夹的所有者搞错了，将导致后续无法正常启动Hadoop</strong>。</p>
<p></p>
<pre><code class="language-plain">sudo cp /home/hduser/Downloads/hadoop-1.2.1.tar.gz /usr/local/hadoop
cd /usr/local/hadoop
sudo tar xzf hadoop-1.2.1.tar.gz
sudo chown -R hduser:hadoop hadoop-1.2.1
sudo gedit $HOME/.bashrc</code></pre>
<p></p>
将下面的代码添加到$HOME/.bashrc文件的末尾。<br><p></p>
<pre><code class="language-plain"># Set Hadoop-related environment variables
export HADOOP_HOME=/usr/local/hadoop/hadoop-1.2.1

# Set JAVA_HOME (we will also configure JAVA_HOME directly for Hadoop later on)
export JAVA_HOME=/usr/lib/jdk1.7.0_40

# Some convenient aliases and functions for running Hadoop-related commands
unalias fs &amp;&gt; /dev/null
alias fs="hadoop fs"
unalias hls &amp;&gt; /dev/null
alias hls="fs -ls"

# If you have LZO compression enabled in your Hadoop cluster and
# compress job outputs with LZOP (not covered in this tutorial):
# Conveniently inspect an LZOP compressed file from the command
# line; run via:
#
# $ lzohead /hdfs/path/to/lzop/compressed/file.lzo
#
# Requires installed 'lzop' command.
#
lzohead () {
    hadoop fs -cat $1 | lzop -dc | head -1000 | less
}

# Add Hadoop bin/ directory to PATH
export PATH=$PATH:$HADOOP_HOME/bin</code></pre>接下来，还需要配置hadoop-env.sh，con/*-site.xml等文件。打开conf/hadoop-env.sh，然后将<pre><code class="language-plain"># The java implementation to use.  Required.
# export JAVA_HOME=/usr/lib/j2sdk1.5-sun</code></pre>修改为：<pre><code class="language-plain"># The java implementation to use.  Required.
export JAVA_HOME=/usr/lib/jdk1.7.0_40</code></pre>正如这样：<br><p><img src="https://img-blog.csdn.net/20131016210501609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>Hadoop默认使用hadoop.tmp.dir来存放临时文件。建议自己建立一个目录来存放临时文件。</p>
<pre><code class="language-plain">$ sudo mkdir -p /app/hadoop/tmp
$ sudo chown hduser:hadoop /app/hadoop/tmp
$ sudo chmod 750 /app/hadoop/tmp</code></pre>
<p>如果想获得更高的安全性，请为目录设置750的权限。如果忘记了设置所有者以及权限，可能在格式化名称节点时遇到java.io.IOException错误。对于conf/core-site.xml，conf/mapred-site.xml，conf/hdfs-site.xml配置非常简单，只要在&lt;configuration&gt;...&lt;/configuration&gt;键值对之间，将下面的代码粘贴进去就可以了。</p>
<p>conf/core-site.xml：</p>
<pre><code class="language-plain">&lt;property&gt;
  &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
  &lt;value&gt;/app/hadoop/tmp&lt;/value&gt;
  &lt;description&gt;A base for other temporary directories.&lt;/description&gt;
&lt;/property&gt;

&lt;property&gt;
  &lt;name&gt;fs.default.name&lt;/name&gt;
  &lt;value&gt;hdfs://localhost:54310&lt;/value&gt;
  &lt;description&gt;The name of the default file system.  A URI whose
  scheme and authority determine the FileSystem implementation.  The
  uri's scheme determines the config property (fs.SCHEME.impl) naming
  the FileSystem implementation class.  The uri's authority is used to
  determine the host, port, etc. for a filesystem.&lt;/description&gt;
&lt;/property&gt;</code></pre>
<p>conf/mapred-site.xml：</p>
<pre><code class="language-plain">&lt;property&gt;
  &lt;name&gt;mapred.job.tracker&lt;/name&gt;
  &lt;value&gt;localhost:54311&lt;/value&gt;
  &lt;description&gt;The host and port that the MapReduce job tracker runs
  at.  If "local", then jobs are run in-process as a single map
  and reduce task.
  &lt;/description&gt;
&lt;/property&gt;</code></pre>con/hdfs-site.xml：<pre><code class="language-plain">&lt;property&gt;
  &lt;name&gt;dfs.replication&lt;/name&gt;
  &lt;value&gt;1&lt;/value&gt;
  &lt;description&gt;Default block replication.
  The actual number of replications can be specified when the file is created.
  The default is used if replication is not specified in create time.
  &lt;/description&gt;
&lt;/property&gt;</code></pre><br><strong>4.启动Hadoop</strong>
<p></p>
<p>在首次启动Hadoop之前，需要对HDFS进行格式化。</p>
<pre><code class="language-plain">/usr/local/hadoop/hadoop-1.2.1/bin/hadoop namenode -format</code></pre>
<p>执行完毕之后，将返回下图所示的结果：</p>
<img src="https://img-blog.csdn.net/20131016211620718?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" width="800" height="387"><br><p>接着，让我们启动Hadoop吧！</p>
<pre><code class="language-plain">/usr/local/hadoop/hadoop-1.2.1/bin/start-all.sh</code></pre>
<p>执行完毕之后，将返回下图所示结果：</p>
<img src="https://img-blog.csdn.net/20131016211956968?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br><p>启动完毕，之后通过jps指令来查看Hadoop的状态：</p>
<p></p>
<pre><code class="language-plain">cd /usr/local/hadoop/hadoop-1.2.1
jps</code></pre>执行完毕之后，将返回下图所示结果：<br><p><img src="https://img-blog.csdn.net/20131016212350109?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>通过netstat指令来查看Hadoop的端口：</p>
<pre><code class="language-plain">sudo netstat -plten | grep java</code></pre><img src="https://img-blog.csdn.net/20131016212505453?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" width="800" height="520"><br><p>关闭集群：</p>
<pre><code class="language-plain">/usr/local/hadoop/hadoop-1.2.1/bin/stop-all.sh</code></pre>
<p><br></p>
<p><strong>5.安装与配置Eclipse</strong></p>
<p>Eclipse的安装非常简单，将下载好的压缩包用tar命令解压出来就可以使用Eclipse，这里不再赘述。为了能够在Eclipse中查看HDFS和开发MapReduce程序，需要先安装一个插件hadoop-eclipse-plugin-1.2.1.jar。插件的安装方法也很简单，将jar包放到eclipse/plugin目录下即可。现在，让我们启动Eclipse看看吧。</p>
<p><img src="https://img-blog.csdn.net/20131023144920031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGluY2hlbjIwMTM=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="" width="800" height="510"><br></p>
<p>点击Window菜单下的Preference后弹出一个对话框。在对话框的左侧点击Hadoop Map/Reduce选项，输入Hadoop的安装目录，本文的Hadoop安装目录是/usr/local/hadoop/hadoop-1.2.1。注意，在下方Console控制台选项卡的左侧，多了一个Map/Reduce Locations选项卡。在该选项卡的空白处右击，点击New Hadoop location。在弹出的对话框中，填入Map/Reduce master以及DFS master的host和port。这两个端口号在安装Hadoop时已经进行了配置，分别在hadoop-1.2.1/conf目录下的mapred-site.xml和core-site.xml两个文件中，注意别填反了。然后，点击Advanced
 parameters，从中找到hadoop.tmp.dir，填入临时目录，本文选择/app/hadoop/tmp，这个属性之前已经在core-site.xml进行了配置。再从Advanced parameters中找到fs.default.name，填入Hadoop集群的地址，本文的Hadoop集群地址是hdfs://localhost:54310，这个属性之前也已经在“core-site.xml”进行了配置。点击完成之后，就配置好了Eclipse。接下来，可以在Eclipse中新建一个Map/Reduce
 Project，尝试写一些简单的MapReduce程序。</p>
<p><br></p>
<p>至此，搭建开发环境的所有工作都已经完成。</p>
<p>Hope you enjoy it! </p>
<p><br></p>
<p><strong>6.参考资料</strong></p>
<p></p>
<ul><li><a href="http://www.michael-noll.com/tutorials/running-hadoop-on-ubuntu-linux-single-node-cluster/" rel="nofollow">Running Hadoop on Ubuntu Linux (Single-Node Cluster)，Michael G. Noll</a><br></li><li><a href="http://ishare.iask.sina.com.cn/f/23043517.html" rel="nofollow">Hadoop权威指南</a></li><li><a href="http://www.cnblogs.com/linjiqin/archive/2013/06/21/3147902.html" rel="nofollow">在ubuntu下使用Eclipse搭建Hadoop开发环境</a><br></li></ul><p></p>
<p><br></p>
            </div>
                </div>
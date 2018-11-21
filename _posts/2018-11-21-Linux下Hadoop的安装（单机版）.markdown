---
layout:     post
title:      Linux下Hadoop的安装（单机版）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/huhui_bj/article/details/8909415				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>1.安装前准备</h1>
<h2>1.1创建hadoop用户</h2>
<div>首先创建hadoop用户组和用户，如果对linux命令不熟悉，可以参考<a href="http://blog.csdn.net/huhui_bj/article/details/8878701" rel="nofollow">http://blog.csdn.net/huhui_bj/article/details/8878701</a>中的命令。</div>
<div><pre><code class="language-plain">addgroup hadoop //创建hadoop组
useradd hadoop -g hadoop -m //创建hadoop用户并加入hadoop组
passwd hadoop //设置hadoop用户密码</code></pre></div>
<h2>1.2下载hadoop安装程序</h2>
<div>我所用的是hadoop1.0.4，也是目前比较稳定的版本，下载地址：<a href="http://download.csdn.net/detail/huhui_bj/6028891" rel="nofollow">http://download.csdn.net/detail/huhui_bj/6028891</a></div>
<div>下载完之后，把文件放在hadoop用户目录的software目录下，然后用tar命令解压缩（请确保是用hadoop用户解压缩，否则需要改变文件夹的用户和用户组）：</div>
<div><pre><code class="language-plain">tar -zxvf hadoop-1.0.4.tar.gz</code></pre></div>
<div><img src="https://img-blog.csdn.net/20130510101926386" alt=""><br></div>
<h2>1.3安装ssh</h2>
<div>请确保你的Linux系统中已经安装了ssh软件，一般的Linux发行版都已经在安装完操作系统之后自带ssh软件。如果没有，执行以下命令安装：</div>
<div><pre><code class="language-plain">sudo apt-get install ssh</code></pre>
<h2>1.4安装rsync</h2>
</div>
<div>请确保你的Linux中已经安装了rsync，不然，请执行以下命令安装：</div>
<div><pre><code class="language-plain">sudo apt-get install rsync</code></pre>
<h2>1.5配置ssh免密码登录</h2>
</div>
<div><pre><code class="language-plain">ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
cat ~/.ssh/id_dsa.pub &gt;&gt; ~/.ssh/authorized_keys</code></pre>如果在配置ssh免密码登录的时候出现了ssh: connect to host localhost port 22: Connection refused错误，解决的办法如下：</div>
<div>
<p>第一步，在<a href="https://launchpad.net/ubuntu/lucid/i386/openssh-client/1:5.3p1-3ubuntu3" rel="nofollow">https://launchpad.net/ubuntu/lucid/i386/openssh-client/1:5.3p1-3ubuntu3</a></p>
<p>下载文件并安装：<span lang="en-us" xml:lang="en-us">$ sudo dpkg -i openssh-client_5.3p1-3ubuntu3_i386.deb</span><span><span lang="en-us" xml:lang="en-us"> </span></span></p>
<p>第二步，在<a href="https://launchpad.net/ubuntu/lucid/i386/openssh-server/1:5.3p1-3ubuntu3" rel="nofollow">https://launchpad.net/ubuntu/lucid/i386/openssh-server/1:5.3p1-3ubuntu3</a></p>
<p>下载文件并安装：<span lang="en-us" xml:lang="en-us">$ sudo dpkg -i openssh-server_5.3p1-3ubuntu3_i386.deb</span><span><span lang="en-us" xml:lang="en-us"> </span></span></p>
<p>第三步，在<a href="https://launchpad.net/ubuntu/lucid/i386/ssh/1:5.3p1-3ubuntu3" rel="nofollow">https://launchpad.net/ubuntu/lucid/i386/ssh/1:5.3p1-3ubuntu3</a></p>
<p>下载文件并安装：<span lang="en-us" xml:lang="en-us">$ sudo dpkg -i ssh_5.3p1-3ubuntu3_all.deb</span><span><span lang="en-us" xml:lang="en-us"> </span></span></p>
<p><br></p>
<p>验证ssh免登录是否配置成功：</p>
<p></p>
<pre><code class="language-plain">ssh localhost</code></pre>
<p></p>
<p>方法二（CentOS可使用）：</p>
<p></p>
<pre><code class="language-plain">#生成公钥和私钥
ssh-keygen -q -t rsa -N "" -f ~/.ssh/id_rsa
cd .ssh
cat id_rsa.pub &gt; authorized_keys
chmod go-wx  authorized_keys
</code></pre><br><p></p>
<p>出现如下信息，则说明ssh免登录配置成功：</p>
<img src="https://img-blog.csdn.net/20130510171615834" alt=""><br><h2>1.6安装并配置JDK</h2>
<div>JDK的安装和配置，请参考博文<a href="http://blog.csdn.net/huhui_cs/article/details/10473789" rel="nofollow">http://blog.csdn.net/huhui_cs/article/details/10473789</a>中关于JDK的安装说明。</div>
<div><br></div>
<h1>2.安装并配置单机版Hadoop</h1>
</div>
<h2>2.1修改hadoop-env.sh</h2>
<div>在hadoop目录下的conf目录下，找到hadoop-env.sh文件，将JAVA_HOME配置进去：</div>
<div><pre><code class="language-plain">vi conf/hadoop-env.sh</code></pre>添加JAVA_HOME：</div>
<div><pre><code class="language-plain">JAVA_HOME=/home/hadoop/software/jdk1.6.0_21</code></pre>
<h2>2.2修改hadoop的核心配置文件core-site.xml，配置HDFS的地址和端口号</h2>
</div>
<div><pre><code class="language-plain">vi conf/core-site.xml</code></pre>添加如下内容：</div>
<div><pre><code class="language-html">&lt;configuration&gt;
     &lt;property&gt;
         &lt;name&gt;fs.default.name&lt;/name&gt;
         &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
     &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<h2>2.3修改hadoop中HDFS的配置</h2>
</div>
<div><pre><code class="language-plain">vi conf/hdfs-site.xml</code></pre>添加如下内容：</div>
<div><pre><code class="language-html">&lt;configuration&gt;
     &lt;property&gt;
         &lt;name&gt;dfs.replication&lt;/name&gt;
         &lt;value&gt;1&lt;/value&gt;
     &lt;/property&gt;
&lt;/configuration&gt;</code></pre>
<h2>2.4修改hadoop中MapReduce的配置文件，配置的是JobTracker的地址和端口</h2>
</div>
<div><pre><code class="language-plain">vi conf/mapred-site.xml</code></pre>添加如下内容：</div>
<div><pre><code class="language-html">&lt;configuration&gt;
     &lt;property&gt;
         &lt;name&gt;mapred.job.tracker&lt;/name&gt;
         &lt;value&gt;localhost:9001&lt;/value&gt;
     &lt;/property&gt;
&lt;/configuration&gt;</code></pre><br></div>
<div>以上就是单机版Hadoop的最小化配置</div>
<h2>2.5格式化一个新的分布式文件系统</h2>
<div><pre><code class="language-plain">bin/hadoop namenode -format</code></pre>
<h2>2.6启动hadoop</h2>
</div>
<div><pre><code class="language-plain">bin/start-all.sh</code></pre></div>
<h1>3.验证Hadoop是否安装成功</h1>
<div>
<ul><li><span class="codefrag">HDFS的web页面：</span> - <a href="http://localhost:50070/" rel="nofollow">http://localhost:50070/</a></li><li>MapReduce的web页面： - <a href="http://localhost:50030/" rel="nofollow">http://localhost:50030/</a></li></ul><div>如果这两个页面能打开，说明你的hadoop已经安装成功了。</div>
<h2>3.1运行WordCount例子</h2>
<div>在HDFS的根目录下创建input文件夹(bin目录下执行)：</div>
<div><pre><code class="language-plain">./hadoop fs -mkdir /input</code></pre></div>
<div>将start-all.sh文件放入hadoop的文件系统input目录下：</div>
<div><pre><code class="language-plain">./hadoop fs -put start-all.sh /input</code></pre>运行Wordount例子：</div>
</div>
<div><pre><code class="language-plain">bin/hadoop jar hadoop-examples-1.0.4.jar  wordcount /input /output</code></pre><br><br></div>
<div><span style="font-size:14px;color:#ff0000;">常见问题1</span></div>
<div>ERROR org.apache.hadoop.hdfs.server.datanode.DataNode: java.io.IOException: Incompatible namespaceIDs in /tmp/hadoop-hadoop/dfs/data: namenode namespaceID = 1091972464; datanode namespaceID = 640175512</div>
<div>原因：<br>
Your Hadoop namespaceID became corrupted. Unfortunately the easiest thing to do reformat the HDFS.<br>
解决方案：</div>
<div><pre><code class="language-plain">bin/stop-all.sh
rm -Rf /tmp/hadoop-your-username/*
bin/hadoop namenode -format
bin/start-all.sh</code></pre>
<div><br></div>
<span style="font-size:14px;color:#ff6666;">常见问题2</span><br>
每次重启后启动hadoop都要格式化文件系统，否则namenode不能启动<br></div>
<div>原因：</div>
<div>hadoop默认配置是把一些tmp文件放在/tmp目录下，重启系统后，tmp目录下的东西被清除，所以要重新格式化DFS，再生成tmp目录<br></div>
<div>解决方案：</div>
<div>在conf/core-site.xml 中增加以下内容<br><pre><code class="language-plain">&lt;property&gt;
       &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
       &lt;value&gt;/var/log/hadoop/tmp&lt;/value&gt;
       &lt;description&gt;A base for other temporary directories&lt;/description&gt;
&lt;/property&gt;</code></pre></div>
<div>重启hadoop后，格式化namenode即可<br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
<div><br></div>
            </div>
                </div>
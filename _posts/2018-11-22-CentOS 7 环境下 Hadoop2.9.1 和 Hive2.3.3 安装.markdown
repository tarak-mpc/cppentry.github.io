---
layout:     post
title:      CentOS 7 环境下 Hadoop2.9.1 和 Hive2.3.3 安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主贾磊GarayM原创文章，转载请注明出处					https://blog.csdn.net/Garay_/article/details/82286166				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><div class="toc">
<ul>
<li><ul>
<li><a href="#hadoop-%E5%AE%89%E8%A3%85%E5%87%86%E5%A4%87" rel="nofollow">Hadoop - 安装准备</a><ul>
<li><a href="#%E4%B8%80%E6%B7%BB%E5%8A%A0hadoop%E7%94%A8%E6%88%B7%E5%88%9B%E5%BB%BA%E7%9B%B8%E5%85%B3%E7%9B%AE%E5%BD%95%E5%B9%B6%E5%88%86%E9%85%8D%E6%9D%83%E9%99%90" rel="nofollow">一、添加Hadoop用户、创建相关目录并分配权限</a></li>
<li><a href="#%E4%BA%8C%E7%8E%AF%E5%A2%83%E9%85%8D%E7%BD%AE" rel="nofollow">二、环境配置</a><ul>
<li><a href="#%E7%AC%AC%E4%B8%80%E6%AD%A5-%E5%8D%B8%E8%BD%BD%E7%B3%BB%E7%BB%9F%E8%87%AA%E5%B8%A6%E7%9A%84openjdk%E4%BB%A5%E5%8F%8A%E7%9B%B8%E5%85%B3%E7%9A%84java%E6%96%87%E4%BB%B6" rel="nofollow">第一步 卸载系统自带的OpenJDK以及相关的java文件</a></li>
<li><a href="#%E7%AC%AC%E4%BA%8C%E6%AD%A5-%E5%AE%89%E8%A3%85jdk" rel="nofollow">第二步 安装JDK</a></li>
<li><a href="#%E7%AC%AC%E4%B8%89%E6%AD%A5-%E5%85%B3%E9%97%AD%E9%98%B2%E7%81%AB%E5%A2%99%E5%B9%B6%E9%85%8D%E7%BD%AE%E4%B8%BB%E6%9C%BA%E5%88%AB%E5%90%8D" rel="nofollow">第三步 关闭防火墙并配置主机别名</a></li>
<li><a href="#%E7%AC%AC%E5%9B%9B%E6%AD%A5-%E9%85%8D%E7%BD%AE%E9%9B%86%E7%BE%A4%E8%8A%82%E7%82%B9%E9%97%B4%E7%9A%84%E5%85%8D%E5%AF%86%E7%99%BB%E5%BD%95" rel="nofollow">第四步 配置集群节点间的免密登录</a></li>
</ul>
</li>
</ul>
</li>
<li><a href="#hadoop-%E5%AE%89%E8%A3%85%E5%92%8C%E9%85%8D%E7%BD%AE" rel="nofollow">Hadoop - 安装和配置</a><ul>
<li><a href="#%E4%B8%8B%E8%BD%BD-hadoop" rel="nofollow">下载 Hadoop</a></li>
<li><a href="#%E5%AE%89%E8%A3%85-hadoop" rel="nofollow">安装 Hadoop</a></li>
<li><a href="#%E9%85%8D%E7%BD%AE-hadoop" rel="nofollow">配置 Hadoop</a></li>
<li><a href="#%E5%90%AF%E5%8A%A8-hadoop" rel="nofollow">启动 Hadoop</a></li>
</ul>
</li>
<li><a href="#hive-%E5%AE%89%E8%A3%85%E5%87%86%E5%A4%87" rel="nofollow">Hive - 安装准备</a><ul>
<li><a href="#%E5%AE%89%E8%A3%85mysql" rel="nofollow">安装MySQL</a></li>
</ul>
</li>
<li><a href="#hive-%E5%AE%89%E8%A3%85%E5%92%8C%E9%85%8D%E7%BD%AE" rel="nofollow">Hive - 安装和配置</a><ul>
<li><a href="#%E4%B8%8B%E8%BD%BD-hive" rel="nofollow">下载 Hive</a></li>
<li><a href="#%E5%AE%89%E8%A3%85-hive" rel="nofollow">安装 Hive</a></li>
<li><a href="#%E9%85%8D%E7%BD%AE-hive" rel="nofollow">配置 Hive</a></li>
<li><a href="#%E5%88%9D%E5%A7%8B%E5%8C%96%E5%90%AF%E5%8A%A8%E5%92%8C%E6%B5%8B%E8%AF%95-hive" rel="nofollow">初始化启动和测试 Hive</a></li>
</ul>
</li>
</ul>
</li>
</ul>
</div>
</div>


<p>本文记录的是部署的3节点的分布式Hadoop集群的过程，环境是CentOS 7.4，1个NameNode，2个DataNode。并在此Hadoop环境基础上完成Hive的安装。</p>

<blockquote>
  <p><strong>Hadoop</strong>，Apache软件基金会旗下的一个开源分布式计算平台，是一个运行和处理海量数据的软件平台。以Hadoop分布式文件系统 HDFS 和 MapReduce 为核心的Hadoop为用户提供了系统底层细节透明的分布式基础架构。</p>
  
  <p><strong>Hive</strong>，基于Hadoop的数据仓库管理工具，它能够提供数据的精炼，查询和分析。Hive并不存储数据，而是依赖于Hadoop平台的HDFS，将结构化的数据文件映射为一张数据库表，并提供类SQL（HQL）查询功能，可以将HQL语句转换为MapReduce任务执行。其设计目标是使Hadoop上的数据操作与传统SQL结合。</p>
</blockquote>



<h2 id="hadoop-安装准备"><strong>Hadoop - 安装准备</strong></h2>



<h3 id="一添加hadoop用户创建相关目录并分配权限">一、添加Hadoop用户、创建相关目录并分配权限</h3>

<p>在CentOS 7下新建hadoop用户，官方推荐的是hadoop、mapreduce、yarn分别用不同的用户安装，本文相关环境全部在hadoop用户下安装。首先需要添加Hadoop用户，为了方便部署，并为其分配管理员权限：</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># groupadd hadoop</span>
[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># useradd -m hadoop -G hadoop -s /bin/bash</span>
[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># passwd hadoop</span>
[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># visudo</span></code></pre>

<p>使用 visudo 命令后找到 <code>root ALL=(ALL) ALL</code> 这一行（应该在90到100行之间，vi命令模式下输入 <code>:set nu</code> 就会显示行号，可以输入例如 <code>:92</code> 回车跳转到92行。），然后在这行下面增加一行内容：<code>hadoop ALL=(ALL) ALL</code> （当中的间隔为tab），然后保存退出。</p>

<p>在 <code>/usr/local/</code> 目录下创建目录：java、hadoop、hive三个目录并分配权限给hadoop用户：</p>



<pre class="prettyprint"><code class="language-shell hljs perl">[root<span class="hljs-variable">@localhost</span> <span class="hljs-keyword">local</span>]<span class="hljs-comment"># chown -R hadoop:hadoop /usr/local/java</span>
[root<span class="hljs-variable">@localhost</span> <span class="hljs-keyword">local</span>]<span class="hljs-comment"># chown -R hadoop:hadoop /usr/local/hadoop</span>
[root<span class="hljs-variable">@localhost</span> <span class="hljs-keyword">local</span>]<span class="hljs-comment"># chown -R hadoop:hadoop /usr/local/hive</span></code></pre>

<p> </p>



<h3 id="二环境配置">二、环境配置</h3>



<h4 id="第一步-卸载系统自带的openjdk以及相关的java文件">第一步 卸载系统自带的OpenJDK以及相关的java文件</h4>

<blockquote>
  <p>一些开发版的CentOS会自带JDK，我们一般用自己的JDK，把自带的删除。 <br>
  关于OpenJDK和JDK的区别可以查看：<a href="http://www.cnblogs.com/sxdcgaq8080/p/7487369.html" rel="nofollow">http://www.cnblogs.com/sxdcgaq8080/p/7487369.html</a></p>
</blockquote>

<p>使用命令 <code>java -version</code> 看是否已安装OpenJDK。</p>



<pre class="prettyprint"><code class="language-shell hljs d">[root<span class="hljs-keyword">@localhost</span> ~]# java -<span class="hljs-keyword">version</span>
openjdk <span class="hljs-keyword">version</span> <span class="hljs-string">"1.8.0_101"</span>
OpenJDK Runtime Environment (build <span class="hljs-number">1.8</span>.0_101-b13)
OpenJDK <span class="hljs-number">64</span>-Bit Server VM (build <span class="hljs-number">25.101</span>-b13, mixed mode)</code></pre>

<p><strong>如果没有可以略过此第一步</strong>，如果有（如上图），则找到它们的安装位置：</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">[root@localhost ~]<span class="hljs-preprocessor"># rpm -qa | grep java</span>
java-<span class="hljs-number">1.8</span><span class="hljs-number">.0</span>-openjdk-headless-<span class="hljs-number">1.8</span><span class="hljs-number">.0</span><span class="hljs-number">.101</span>-<span class="hljs-number">3.</span>b13<span class="hljs-preprocessor">.el</span>7_2<span class="hljs-preprocessor">.x</span>86_64
tzdata-java-<span class="hljs-number">2016</span>f-<span class="hljs-number">1.</span>el7<span class="hljs-preprocessor">.noarch</span>
java-<span class="hljs-number">1.8</span><span class="hljs-number">.0</span>-openjdk-<span class="hljs-number">1.8</span><span class="hljs-number">.0</span><span class="hljs-number">.101</span>-<span class="hljs-number">3.</span>b13<span class="hljs-preprocessor">.el</span>7_2<span class="hljs-preprocessor">.x</span>86_64
javapackages-tools-<span class="hljs-number">3.4</span><span class="hljs-number">.1</span>-<span class="hljs-number">11.</span>el7<span class="hljs-preprocessor">.noarch</span>
java-<span class="hljs-number">1.7</span><span class="hljs-number">.0</span>-openjdk-headless-<span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-number">.111</span>-<span class="hljs-number">2.6</span><span class="hljs-number">.7</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.el</span>7_2<span class="hljs-preprocessor">.x</span>86_64
java-<span class="hljs-number">1.7</span><span class="hljs-number">.0</span>-openjdk-<span class="hljs-number">1.7</span><span class="hljs-number">.0</span><span class="hljs-number">.111</span>-<span class="hljs-number">2.6</span><span class="hljs-number">.7</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.el</span>7_2<span class="hljs-preprocessor">.x</span>86_64
python-javapackages-<span class="hljs-number">3.4</span><span class="hljs-number">.1</span>-<span class="hljs-number">11.</span>el7<span class="hljs-preprocessor">.noarch</span></code></pre>

<p>删除全部，noarch文件可以不用删除</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># rpm -e --nodeps java-1.8.0-openjdk-headless-1.8.0.101-3.b13.el7_2.x86_64</span>
[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># rpm -e --nodeps java-1.8.0-openjdk-1.8.0.101-3.b13.el7_2.x86_64</span>
[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># rpm -e --nodeps java-1.7.0-openjdk-headless-1.7.0.111-2.6.7.2.el7_2.x86_64</span>
[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># rpm -e --nodeps java-1.7.0-openjdk-1.7.0.111-2.6.7.2.el7_2.x86_64</span></code></pre>

<p>检查有没有删除</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># java -version</span>
-<span class="hljs-symbol">bash:</span> /usr/bin/<span class="hljs-symbol">java:</span> 没有那个文件或目录</code></pre>

<p>如果还没有删除，则用yum -y remove去删除他们。 <br>
 </p>



<h4 id="第二步-安装jdk">第二步 安装JDK</h4>

<p><strong>1. 下载JDK安装包</strong></p>

<p>如果系统没有wget命令则先使用 <code>yum install wget</code> 安装wget命令。 <br>
然后下载JDK安装包：<code>cd /usr/local/java</code>，然后下载JDK：</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[root<span class="hljs-variable">@localhost</span> java]<span class="hljs-comment"># wget http://download.oracle.com/otn-pub/java/jdk/8u181-b13/96a7b8442fe848ef90c96a2fad6ed6d1/jdk-8u181-linux-x64.tar.gz</span></code></pre>

<p>下载完成之后执行解压命令：<code>tar -zxvf jdk-8u181-linux-x64.tar.gz</code></p>

<p><strong>2. 配置Java环境变量</strong></p>

<p>执行 <code>vim /etc/profile</code>，打开文件添加如下内容:</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-comment"># set java environment</span>
<span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/java/jdk</span>1.<span class="hljs-number">8.0_181</span>
<span class="hljs-constant">JRE_HOME</span>=<span class="hljs-variable">$JAVA_HOME</span>/jre
<span class="hljs-constant">PATH</span>=<span class="hljs-variable">$PATH</span><span class="hljs-symbol">:</span><span class="hljs-variable">$JAVA_HOME</span>/<span class="hljs-symbol">bin:</span><span class="hljs-variable">$JRE_HOME</span>/bin
<span class="hljs-constant">CLASSPATH</span>=.<span class="hljs-symbol">:</span><span class="hljs-variable">$JAVA_HOME</span>/lib/dt.<span class="hljs-symbol">jar:</span><span class="hljs-variable">$JAVA_HOME</span>/lib/tools.<span class="hljs-symbol">jar:</span><span class="hljs-variable">$JRE_HOME</span>/lib
export <span class="hljs-constant">JAVA_HOME</span> <span class="hljs-constant">JRE_HOME</span> <span class="hljs-constant">PATH</span> <span class="hljs-constant">CLASSPATH</span></code></pre>

<p>保存并退出，然后使用命令 <code>source /etc/profile</code> 使环境变量生效，并验证Java环境是否成功配置：</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[root<span class="hljs-variable">@localhost</span> java]<span class="hljs-comment"># java -version</span>
java version <span class="hljs-string">"1.8.0_181"</span>
<span class="hljs-constant">Java</span>(<span class="hljs-constant">TM</span>) <span class="hljs-constant">SE</span> <span class="hljs-constant">Runtime</span> <span class="hljs-constant">Environment</span> (build <span class="hljs-number">1.8</span>.<span class="hljs-number">0_1</span>81-b13)
<span class="hljs-constant">Java</span> <span class="hljs-constant">HotSpot</span>(<span class="hljs-constant">TM</span>) <span class="hljs-number">64</span>-<span class="hljs-constant">Bit</span> <span class="hljs-constant">Server</span> <span class="hljs-constant">VM</span> (build <span class="hljs-number">25.181</span>-b13, mixed mode)
[root<span class="hljs-variable">@localhost</span> java]<span class="hljs-comment">#</span></code></pre>

<p><strong><em>到此，需要在每一台 Hadoop 节点上依次配置如上安装准备步骤内容</em></strong> <br>
 </p>



<h4 id="第三步-关闭防火墙并配置主机别名">第三步 关闭防火墙并配置主机别名</h4>

<p><strong>1. 执行如下命令关闭防火墙，并禁用开机启动:</strong></p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># systemctl stop firewalld.service</span>
[root<span class="hljs-variable">@localhost</span> ~]<span class="hljs-comment"># systemctl disable firewalld.service</span></code></pre>

<p><strong>2. 修改主机名</strong></p>

<p>在Master主机上执行： <code>hostnamectl set-hostname master</code> <br>
在Slave01主机上执行：<code>hostnamectl set-hostname slave01</code> <br>
在Slave02主机上执行：<code>hostnamectl set-hostname slave02</code></p>

<blockquote>
  <p><strong>注意：</strong> 尽量不要在主机别名中含有任何特殊字符，这可能导致后面的Hadoop和Hive的配置出现问题！</p>
</blockquote>

<p><strong>3. 配置相关网络</strong></p>

<p>以master主机为例，演示如何配置静态网络及host文件。 <br>
首先使用 <code>ifconfig</code> 命令查看本地网卡及IP： <br>
<img src="https://code.aliyun.com/jialei/MarkDownIMG/raw/master/1535276827068.png" alt="IP内容" title=""></p>

<p>然后打开配置文件：</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[root<span class="hljs-variable">@master</span> ~]<span class="hljs-comment"># vim /etc/sysconfig/network-scripts/ifcfg-ens33</span></code></pre>

<p>根据节点网络相关信息，修改或添加如下内容：</p>



<pre class="prettyprint"><code class="language-shell hljs ini"><span class="hljs-setting">ONBOOT=<span class="hljs-value"><span class="hljs-keyword">yes</span></span></span>
<span class="hljs-setting">BOOTPROTO=<span class="hljs-value">static</span></span>
<span class="hljs-setting">IPADDR=<span class="hljs-value"><span class="hljs-number">192.168</span>.<span class="hljs-number">188.8</span></span></span>
<span class="hljs-setting">NETMASK=<span class="hljs-value"><span class="hljs-number">255.255</span>.<span class="hljs-number">255.0</span></span></span>
<span class="hljs-setting">GATEWAY=<span class="hljs-value"><span class="hljs-number">182.168</span>.<span class="hljs-number">188.2</span></span></span>
<span class="hljs-setting">DNS1=<span class="hljs-value"><span class="hljs-number">114.114</span>.<span class="hljs-number">114.114</span></span></span>
<span class="hljs-setting">DEFROUTE=<span class="hljs-value"><span class="hljs-keyword">yes</span></span></span>
<span class="hljs-setting">IPV6INIT=<span class="hljs-value"><span class="hljs-keyword">no</span></span></span>
<span class="hljs-setting">IPV4_FAILURE_FATAL=<span class="hljs-value"><span class="hljs-keyword">yes</span></span></span></code></pre>

<p><strong><em>依次在各个节点中配置上述网络信息</em></strong>  </p>

<p><strong>4. 修改hosts文件</strong></p>



<pre class="prettyprint"><code class="language-shell hljs ">vim /etc/hosts</code></pre>

<p>在各个节点主机上添加所有节点其静态IP和主机别名：</p>



<pre class="prettyprint"><code class="language-shell hljs ">192.168.188.8   master
192.168.188.9   slave01
192.168.188.10  slave02</code></pre>

<p>配置完成后使用ping命令检查这3个机器是否相互ping得通，以master为例：</p>



<pre class="prettyprint"><code class="language-shell hljs avrasm"><span class="hljs-built_in">ping</span> -c <span class="hljs-number">3</span> slave01</code></pre>

<p> </p>



<h4 id="第四步-配置集群节点间的免密登录">第四步 配置集群节点间的免密登录</h4>

<p><strong>1. 对于每一台机器，<code>su - hadoop</code> 切换到hadoop用户，在hadoop用户下执行以下指令，以master节点为例，在hadoop用户home目录下生成密钥文件</strong></p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[hadoop<span class="hljs-variable">@master</span> ~]<span class="hljs-variable">$ </span>ssh-keygen -t rsa -<span class="hljs-constant">P</span> <span class="hljs-string">''</span></code></pre>

<p>一直Enter到底。</p>

<p><strong>2. 对于每台机器，首先将自己的公钥加到authorized_keys中，保证ssh localhost无密码登录：</strong></p>



<pre class="prettyprint"><code class="language-shell hljs rust">cat id_rsa.<span class="hljs-keyword">pub</span> &gt;&gt; authorized_keys</code></pre>

<p><strong>3. 然后将自己的公钥添加至其他每台机器的authorized_keys中，在此过程中需要输入其他机器的密码：</strong></p>

<p>master:</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">scp /home/hadoop/.ssh/id_rsa.pub hadoop<span class="hljs-variable">@slave01</span><span class="hljs-symbol">:/home/hadoop/</span>.ssh/id_rsa_master.pub
scp /home/hadoop/.ssh/id_rsa.pub hadoop<span class="hljs-variable">@slave02</span><span class="hljs-symbol">:/home/hadoop/</span>.ssh/id_rsa_master.pub</code></pre>

<p>slave01:</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">scp /home/hadoop/.ssh/id_rsa.pub hadoop<span class="hljs-variable">@master</span><span class="hljs-symbol">:/home/hadoop/</span>.ssh/id_rsa_slave01.pub
scp /home/hadoop/.ssh/id_rsa.pub hadoop<span class="hljs-variable">@slave02</span><span class="hljs-symbol">:/home/hadoop/</span>.ssh/id_rsa_slave01.pub</code></pre>

<p>slave02:</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">scp /home/hadoop/.ssh/id_rsa.pub hadoop<span class="hljs-variable">@master</span><span class="hljs-symbol">:/home/hadoop/</span>.ssh/id_rsa_slave02.pub
scp /home/hadoop/.ssh/id_rsa.pub hadoop<span class="hljs-variable">@slave01</span><span class="hljs-symbol">:/home/hadoop/</span>.ssh/id_rsa_slave02.pub</code></pre>

<p><strong>4. 分别在每一台主机的 /home/hadoop/.ssh/ 目录下，将除本机产生的公钥（id_rsa.pub）之外的其他公钥使用cat命令添加至 authorized_keys 中。添加完毕之后使用 chmod 命令给 authorized_keys 文件设置权限，然后使用 rm 命令删除所有的公钥：</strong></p>

<p>master:</p>



<pre class="prettyprint"><code class="language-shell hljs rust">cat id_rsa_slave01.<span class="hljs-keyword">pub</span> &gt;&gt; authorized_keys
cat id_rsa_slave02.<span class="hljs-keyword">pub</span> &gt;&gt; authorized_keys
chmod <span class="hljs-number">600</span> authorized_keys
rm id_rsa*.<span class="hljs-keyword">pub</span></code></pre>

<p>slave01:</p>



<pre class="prettyprint"><code class="language-shell hljs rust">cat id_rsa_master.<span class="hljs-keyword">pub</span> &gt;&gt; authorized_keys
cat id_rsa_slave02.<span class="hljs-keyword">pub</span> &gt;&gt; authorized_keys
chmod <span class="hljs-number">600</span> authorized_keys
rm id_rsa*.<span class="hljs-keyword">pub</span></code></pre>

<p>slave02:</p>



<pre class="prettyprint"><code class="language-shell hljs rust">cat id_rsa_master.<span class="hljs-keyword">pub</span> &gt;&gt; authorized_keys
cat id_rsa_slave01.<span class="hljs-keyword">pub</span> &gt;&gt; authorized_keys
chmod <span class="hljs-number">600</span> authorized_keys
rm id_rsa*.<span class="hljs-keyword">pub</span></code></pre>

<p>完成上述步骤，就可以实现从任意一台机器通过ssh命令免密码登录任意一台其他机器了。</p>

<p> </p>



<h2 id="hadoop-安装和配置"><strong>Hadoop - 安装和配置</strong></h2>

<p>下述步骤切换到创建的 hadoop 用户下执行</p>



<h3 id="下载-hadoop">下载 Hadoop</h3>

<p>本文采用的是Hadoop 2.9.1版本，下载Hadoop：</p>

<p><img src="https://code.aliyun.com/jialei/MarkDownIMG/raw/master/1535280774002.png" alt="下载Hadoop" title=""></p>



<h3 id="安装-hadoop">安装 Hadoop</h3>

<p><strong>1. 进入 <code>/usr/local/hadoop/</code> 目录，下载hadoop-2.9.1.tar.gz文件并解压：</strong></p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">wget http://mirrors<span class="hljs-preprocessor">.tuna</span><span class="hljs-preprocessor">.tsinghua</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span>/apache/hadoop/common/stable/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>

tar -zxvf hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span></code></pre>

<p><strong>2. 生成Hadoop相关数据目录</strong></p>

<p>在master上，首先/home/hadoop/目录下创建以下目录：</p>



<pre class="prettyprint"><code class="language-shell hljs lasso">mkdir <span class="hljs-attribute">-p</span> /usr/<span class="hljs-built_in">local</span>/hadoop/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/hadoopdir/name
mkdir <span class="hljs-attribute">-p</span> /usr/<span class="hljs-built_in">local</span>/hadoop/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/hadoopdir/<span class="hljs-built_in">data</span>
mkdir <span class="hljs-attribute">-p</span> /usr/<span class="hljs-built_in">local</span>/hadoop/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/hadoopdir/temp
mkdir <span class="hljs-attribute">-p</span> /usr/<span class="hljs-built_in">local</span>/hadoop/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/hadoopdir/logs
mkdir <span class="hljs-attribute">-p</span> /usr/<span class="hljs-built_in">local</span>/hadoop/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/hadoopdir/pids</code></pre>

<p><strong>3. 设置Hadoop脚本文件</strong></p>

<p><strong>hadoop-env.sh</strong></p>



<pre class="prettyprint"><code class="language-shell hljs ruby">export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/java/jdk</span>1.<span class="hljs-number">8.0_181</span>
export <span class="hljs-constant">HADOOP_LOG_DIR</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/hadoop/hadoop</span>-<span class="hljs-number">2.9</span>.<span class="hljs-number">1</span>/hadoopdir/logs
export <span class="hljs-constant">HADOOP_PID_DIR</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/hadoop/hadoop</span>-<span class="hljs-number">2.9</span>.<span class="hljs-number">1</span>/hadoopdir/pids</code></pre>

<p><strong>mapred-env.sh</strong></p>



<pre class="prettyprint"><code class="language-shell hljs ruby">export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/java/jdk</span>1.<span class="hljs-number">8.0_181</span>
export <span class="hljs-constant">HADOOP_MAPRED_LOG_DIR</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/hadoop/hadoop</span>-<span class="hljs-number">2.9</span>.<span class="hljs-number">1</span>/hadoopdir/logs
export <span class="hljs-constant">HADOOP_MAPRED_PID_DIR</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/hadoop/hadoop</span>-<span class="hljs-number">2.9</span>.<span class="hljs-number">1</span>/hadoopdir/pids</code></pre>

<p><strong>yarn-env.sh</strong></p>



<pre class="prettyprint"><code class="language-shell hljs javascript">export JAVA_HOME=<span class="hljs-regexp">/usr/</span>java/jdk1<span class="hljs-number">.8</span><span class="hljs-number">.0</span>_112
YARN_LOG_DIR=<span class="hljs-regexp">/usr/</span>local/hadoop/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/hadoopdir/logs</code></pre>

<p><strong>Slaves文件</strong></p>



<pre class="prettyprint"><code class="language-shell hljs vala"><span class="hljs-preprocessor">#localhost</span>
slave1
slave2</code></pre>

<blockquote>
  <p><strong>注意：</strong> 如果slaves文件里面不注释localhost，意思是把本机也作为一个DataNode节点</p>
</blockquote>



<h3 id="配置-hadoop">配置 Hadoop</h3>

<p><strong>1. 配置相关XML文件</strong></p>

<p><strong>core-site.xml</strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://master:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>io.file.buffer.size<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>131072<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:///usr/local/hadoop/hadoop-2.9.1/hadoopdir/temp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.proxyuser.hadoop.hosts<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>*<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.proxyuser.hadoop.groups<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hadoop<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>hdfs-site.xml</strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:///usr/local/hadoop/hadoop-2.9.1/hadoopdir/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:///usr/local/hadoop/hadoop-2.9.1/hadoopdir/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>2<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.blocksize<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>64m<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.secondary.http-address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:9001<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.webhdfs.enabled<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>mapred-site.xml</strong></p>



<pre class="prettyprint"><code class="language-shell hljs lasso">cp mapred<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span><span class="hljs-built_in">.</span>template mapred<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span>
vi mapred<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span></code></pre>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">final</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">final</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.jobhistory.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:10020<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.jobtracker.http.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:50030<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapred.job.tracker<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>http://master:9001<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.jobhistory.webapp.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:19888<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>yarn-site.xml</strong></p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services.mapreduce_shuffle.class<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.hadoop.mapred.ShuffleHandler<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.scheduler.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8030<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.resource-tracker.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8031<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8032<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.admin.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8033<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.webapp.address<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>master:8088<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p><strong>2. master节点下，将/usr/local/hadoop/hadoop-2.9.1目录里面所有内容拷贝至其他节点</strong></p>



<pre class="prettyprint"><code class="language-shell hljs ruby">scp -r /usr/local/hadoop/ hadoop<span class="hljs-variable">@slave01</span><span class="hljs-symbol">:/usr/local/hadoop/</span>
scp -r /usr/local/hadoop/ hadoop<span class="hljs-variable">@slave02</span><span class="hljs-symbol">:/usr/local/hadoop/</span></code></pre>

<p><strong>3. 进入/usr/local/hadoop/hadoop-2.9.1/bin目录，格式化文件系统：</strong></p>



<pre class="prettyprint"><code class="language-shell hljs rsl">./hdfs namenode -<span class="hljs-built_in">format</span></code></pre>

<p>格式化文件系统会产生一系列的终端输出，在输出最后几行看到STATUS=0表示格式化成功，如果格式化失败请详细查看日志确定错误原因。</p>

<p><strong>4. 进入/usr/local/hadoop/hadoop-2.9.1/sbin目录：</strong></p>



<pre class="prettyprint"><code class="language-shell hljs sql">./<span class="hljs-operator"><span class="hljs-keyword">start</span>-dfs.sh
./<span class="hljs-keyword">start</span>-yarn.sh</span></code></pre>

<p>上述命令就启动了hdfs和yarn。hadoop集群就跑起来了，如果要关闭，在sbin目录下执行以下命令：</p>



<pre class="prettyprint"><code class="language-shell hljs vbnet">./<span class="hljs-keyword">stop</span>-yarn.sh
./<span class="hljs-keyword">stop</span>-dfs.sh</code></pre>



<h3 id="启动-hadoop">启动 Hadoop</h3>

<p>可以在master:50070网页上看到如下结果，可以看到集群信息和DataNode相关信息：</p>

<p><img src="https://code.aliyun.com/jialei/MarkDownIMG/raw/master/hadoop-started.png" alt="Hadoop启动" title=""></p>

<p><img src="https://code.aliyun.com/jialei/MarkDownIMG/raw/master/hadoop-datanode-info.png" alt="Hadoop-datanode-info" title=""></p>

<p> </p>



<h2 id="hive-安装准备"><strong>Hive - 安装准备</strong></h2>

<blockquote>
  <p>Hive 内置了Derby数据库，Hive默认使用内嵌的Derby数据库来存储它的元数据，但由于Derby数据库只支持单会话，所以，通常会使用MySQL作为它的外置存储引擎，方便多用户同时访问，这里以MySQL 5.7为例。</p>
</blockquote>



<h3 id="安装mysql">安装MySQL</h3>

<p><strong>1. 下载MySQL Yum 包</strong></p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">wget http://repo<span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.com</span>/mysql57-community-release-el7-<span class="hljs-number">10.</span>noarch<span class="hljs-preprocessor">.rpm</span></code></pre>

<p><strong>2. 安转软件源</strong></p>



<pre class="prettyprint"><code class="language-shell hljs lasso">rpm <span class="hljs-attribute">-Uvh</span> mysql57<span class="hljs-attribute">-community</span><span class="hljs-attribute">-release</span><span class="hljs-attribute">-el7</span><span class="hljs-subst">-</span><span class="hljs-number">10.</span>noarch<span class="hljs-built_in">.</span>rpm</code></pre>

<p><strong>3. 安装mysql服务端</strong></p>



<pre class="prettyprint"><code class="language-shell hljs lasso">yum install  <span class="hljs-attribute">-y</span>  mysql<span class="hljs-attribute">-community</span><span class="hljs-attribute">-server</span></code></pre>

<p><strong>4. 启动MySQL，并设置开机启动</strong></p>



<pre class="prettyprint"><code class="language-shell hljs sql">service mysqld <span class="hljs-operator"><span class="hljs-keyword">start</span>
systemctl <span class="hljs-keyword">start</span> mysqld.service</span></code></pre>

<p><strong>5. 检查mysql 的运行状态</strong></p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">service mysqld status
systemctl status mysqld<span class="hljs-preprocessor">.service</span></code></pre>

<p><strong>6. 修改MySQL密码</strong></p>

<blockquote>
  <p>为了加强安全性，MySQL5.7为 MySQL的 root 用户随机生成了一个临时密码，如果安装的是RPM包，则默认是/var/log/mysqld.log。</p>
</blockquote>

<p>（1）通过以下命令可以看到初始密码：</p>



<pre class="prettyprint"><code class="language-shell hljs perl"><span class="hljs-keyword">grep</span> <span class="hljs-string">'temporary password'</span> /var/<span class="hljs-keyword">log</span>/mysqld.<span class="hljs-keyword">log</span></code></pre>

<p>（2）使用默认密码登陆并修改密码</p>



<pre class="prettyprint"><code class="language-shell hljs lasso">mysql <span class="hljs-attribute">-uroot</span> <span class="hljs-attribute">-p</span></code></pre>

<p>用默认密码登录到服务端后，必须马上修改密码，不然会报如下错误：</p>



<pre class="prettyprint"><code class="language-shell hljs cs">mysql&gt; <span class="hljs-keyword">select</span> @@log_error;
ERROR <span class="hljs-number">1820</span> (HY000): You must reset your password <span class="hljs-keyword">using</span> ALTER USER statement before executing <span class="hljs-keyword">this</span> statement.
mysql&gt;</code></pre>

<p>修改密码:</p>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">ALTER</span> <span class="hljs-keyword">USER</span> <span class="hljs-string">'root'</span>@<span class="hljs-string">'localhost'</span> IDENTIFIED <span class="hljs-keyword">BY</span> <span class="hljs-string">'root123456'</span>;</span></code></pre>

<p>授权其他机器登陆:</p>



<pre class="prettyprint"><code class="language-sql hljs "><span class="hljs-operator"><span class="hljs-keyword">GRANT</span> <span class="hljs-keyword">ALL</span> <span class="hljs-keyword">PRIVILEGES</span> <span class="hljs-keyword">ON</span> *.* <span class="hljs-keyword">TO</span> <span class="hljs-string">'root'</span>@<span class="hljs-string">'%'</span> IDENTIFIED <span class="hljs-keyword">BY</span> <span class="hljs-string">'root123456'</span> <span class="hljs-keyword">WITH</span> <span class="hljs-keyword">GRANT</span> <span class="hljs-keyword">OPTION</span>;</span>
FLUSH  PRIVILEGES;</code></pre>

<p> </p>



<h2 id="hive-安装和配置"><strong>Hive - 安装和配置</strong></h2>



<h3 id="下载-hive">下载 Hive</h3>

<p>Hive官网地址：<a href="http://hive.apache.org/index.html" rel="nofollow">http://hive.apache.org/index.html</a></p>

<p>Hive下载地址：<a href="https://mirrors.tuna.tsinghua.edu.cn/apache/hive/" rel="nofollow">https://mirrors.tuna.tsinghua.edu.cn/apache/hive/</a></p>

<blockquote>
  <p><strong>注意：</strong> 在安装Hive之前，需要保证你的Hadoop集群已经正常启动，Hive只需在Hadoop集群的NameNode节点上安装即可，无需在DataNode节点上安装。</p>
</blockquote>

<p>本文安装的是 apache-hive-2.3.3-bin.tar.gz  其下载地址为： <br>
<a href="https://mirrors.tuna.tsinghua.edu.cn/apache/hive/hive-2.3.3/apache-hive-2.3.3-bin.tar.gz" rel="nofollow">https://mirrors.tuna.tsinghua.edu.cn/apache/hive/hive-2.3.3/apache-hive-2.3.3-bin.tar.gz</a> <br>
 </p>



<h3 id="安装-hive">安装 Hive</h3>



<pre class="prettyprint"><code class="language-shell hljs avrasm"><span class="hljs-preprocessor"># 切换到hive压缩包的下载目录</span>
cd /usr/local/hive/
<span class="hljs-preprocessor"># 据自己的实际需要下载相应版本的 hive 压缩包</span>
wget https://mirrors<span class="hljs-preprocessor">.tuna</span><span class="hljs-preprocessor">.tsinghua</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span>/apache/hive/hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>/apache-hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>-bin<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
--<span class="hljs-number">2018</span>-<span class="hljs-number">08</span>-<span class="hljs-number">12</span> <span class="hljs-number">13</span>:<span class="hljs-number">53</span>:<span class="hljs-number">31</span>--  https://mirrors<span class="hljs-preprocessor">.tuna</span><span class="hljs-preprocessor">.tsinghua</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span>/apache/hive/hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>/apache-hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>-bin<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
Resolving mirrors<span class="hljs-preprocessor">.tuna</span><span class="hljs-preprocessor">.tsinghua</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span> (mirrors<span class="hljs-preprocessor">.tuna</span><span class="hljs-preprocessor">.tsinghua</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span>)... <span class="hljs-number">101.6</span><span class="hljs-number">.8</span><span class="hljs-number">.193</span>, <span class="hljs-number">2402</span>:f000:<span class="hljs-number">1</span>:<span class="hljs-number">408</span>:<span class="hljs-number">8100</span>::<span class="hljs-number">1</span>
Connecting to mirrors<span class="hljs-preprocessor">.tuna</span><span class="hljs-preprocessor">.tsinghua</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span> (mirrors<span class="hljs-preprocessor">.tuna</span><span class="hljs-preprocessor">.tsinghua</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span>)|<span class="hljs-number">101.6</span><span class="hljs-number">.8</span><span class="hljs-number">.193</span>|:<span class="hljs-number">443.</span>.. connected.
HTTP request sent, awaiting response... <span class="hljs-number">200</span> OK
<span class="hljs-label">Length:</span> <span class="hljs-number">232229830</span> (<span class="hljs-number">221</span>M) [application/octet-stream]
Saving to: ‘apache-hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>-bin<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>’

<span class="hljs-number">100</span>%[==============================================================================&gt;] <span class="hljs-number">232</span>,<span class="hljs-number">229</span>,<span class="hljs-number">830</span> <span class="hljs-number">1.54</span>MB/s   <span class="hljs-keyword">in</span> <span class="hljs-number">1</span>m <span class="hljs-number">52</span>s

<span class="hljs-number">2018</span>-<span class="hljs-number">08</span>-<span class="hljs-number">12</span> <span class="hljs-number">13</span>:<span class="hljs-number">55</span>:<span class="hljs-number">24</span> (<span class="hljs-number">1.97</span> MB/s) - ‘apache-hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>-bin<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>’ saved [<span class="hljs-number">232229830</span>/<span class="hljs-number">232229830</span>]

<span class="hljs-preprocessor"># 将下载好的 hive 压缩包解压到</span>
tar zxvf apache-hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>-bin<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
<span class="hljs-preprocessor"># 之前已经将 /usr/local/hive/ 目录权限分配给hadoop用户</span></code></pre>

<p> </p>



<h3 id="配置-hive">配置 Hive</h3>

<p><strong>1. 执行 <code>sudo vim /etc/profile</code> 命令配置环境变量，在 /etc/profile 配置文件中添加 Hive 环境变量，内容如下：</strong></p>



<pre class="prettyprint"><code class="language-shell hljs bash"><span class="hljs-keyword">export</span> HIVE_HOME=/usr/local/hive/apache-hive-<span class="hljs-number">2.3</span>.<span class="hljs-number">3</span>
<span class="hljs-keyword">export</span> HIVE_CONF_DIR=<span class="hljs-variable">$HIVE_HOME</span>/conf

PATH=<span class="hljs-variable">$HIVE_HOME</span>/bin:<span class="hljs-variable">$PATH</span></code></pre>

<p>然后使用命令 <code>source /etc/profile</code> 使环境变量生效。</p>

<p><strong>2. 创建hive-site.xml</strong></p>



<pre class="prettyprint"><code class="language-shell hljs avrasm"><span class="hljs-preprocessor"># 在开始配置Hive之前，先执行如下命令，切换到Hive的操作账户，我的是 hadoop </span>
su - hadoop
cd $HIVE_CONF_DIR
<span class="hljs-preprocessor"># 以 hive-default.xml.template 为模板，创建 hive-site.xml</span>
<span class="hljs-keyword">cp</span> hive-default<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.template</span>  hive-site<span class="hljs-preprocessor">.xml</span></code></pre>

<p><strong>3. 在HDFS中创建Hive所需目录</strong></p>

<p>因为在hive-site.xml中有以下配置：</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.metastore.warehouse.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/user/hive/warehouse<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>location of default database for the warehouse<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hive.exec.scratchdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/tmp/hive<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>HDFS root scratch dir for Hive jobs which gets created with write all (733) permission. <span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>所以需要在HDFS中创建好相应的目录，操作命令如下：</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>hdfs dfs -mkdir -p /user/hive/warehouse
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>hdfs dfs -chmod -<span class="hljs-constant">R</span> <span class="hljs-number">777</span> /user/hive/warehouse
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>hdfs dfs -mkdir -p /tmp/hive
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>hdfs dfs -chmod -<span class="hljs-constant">R</span> <span class="hljs-number">777</span> /tmp/hive
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>hdfs dfs -ls /
<span class="hljs-constant">Found</span> <span class="hljs-number">2</span> items
drwx------   - hadoop supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2018</span>-08-<span class="hljs-number">12</span> <span class="hljs-number">11</span><span class="hljs-symbol">:</span><span class="hljs-number">53</span> /tmp
drwxr-xr-x   - hadoop supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2018</span>-08-<span class="hljs-number">12</span> <span class="hljs-number">14</span><span class="hljs-symbol">:</span><span class="hljs-number">31</span> /user
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>hdfs dfs -ls /tmp/
<span class="hljs-constant">Found</span> <span class="hljs-number">1</span> items
drwxrwxrwx   - hadoop supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2018</span>-08-<span class="hljs-number">12</span> <span class="hljs-number">11</span><span class="hljs-symbol">:</span><span class="hljs-number">53</span> /tmp/hive
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>hdfs dfs -ls /user/hive
<span class="hljs-constant">Found</span> <span class="hljs-number">1</span> items
drwxrwxrwx   - hadoop supergroup          <span class="hljs-number">0</span> <span class="hljs-number">2018</span>-08-<span class="hljs-number">12</span> <span class="hljs-number">14</span><span class="hljs-symbol">:</span><span class="hljs-number">31</span> /user/hive/warehouse
</code></pre>

<p><strong>4. 配置hive-site.xml</strong></p>

<p>(1) 配置hive本地临时目录</p>

<p>将hive-site.xml文件中的${system:java.io.tmpdir}替换为hive的本地临时目录，本文举例使用 /usr/local/hive-2.3.3/tmp 这个目录，如果该目录不存在，需要先进行创建，并且赋予读写权限：</p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>cd <span class="hljs-variable">$HIVE_HOME</span>
[hadoop<span class="hljs-variable">@master</span> hive-<span class="hljs-number">2.3</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>mkdir tmp/
[hadoop<span class="hljs-variable">@master</span> hive-<span class="hljs-number">2.3</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>chmod -<span class="hljs-constant">R</span> <span class="hljs-number">777</span> tmp/
[hadoop<span class="hljs-variable">@master</span> hive-<span class="hljs-number">2.3</span>.<span class="hljs-number">3</span>]<span class="hljs-variable">$ </span>cd <span class="hljs-variable">$HIVE_CONF_DIR</span>
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>vim hive-site.xml</code></pre>

<p>在hive-site.xml文件的 <code>configuration</code> 标签开始添加以下内容：</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>system:java.io.tmpdir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hive-2.3.3/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>或者在hive-site.xml文件的vim命令模式下执行如下命令完成内容替换：</p>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-symbol">:%s</span><span class="hljs-comment">#${system:java.io.tmpdir}#/usr/local/hive-2.3.3/tmp#g</span></code></pre>

<p>(2) 配置Hive用户名</p>

<p>将hive-site.xml文件中的 ${system:user.name} 替换为操作Hive的账户的用户名，本文用户是 hadoop 。</p>

<p>在改文件的 <code>configuration</code> 标签开始添加以下内容：</p>



<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>system:user.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>${user.name}<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>或者在hive-site.xml文件的vim命令模式下执行如下命令完成替换：</p>



<pre class="prettyprint"><code class="language-shell hljs css">:%<span class="hljs-tag">s</span>#$<span class="hljs-rules">{<span class="hljs-rule"><span class="hljs-attribute">system</span>:<span class="hljs-value">user.name</span></span></span>}<span class="hljs-id">#hadoop</span><span class="hljs-id">#g</span></code></pre>

<p>(3) 修改Hive数据库配置</p>

<table>
<thead>
<tr>
  <th>属性名称</th>
  <th>描述</th>
</tr>
</thead>
<tbody><tr>
  <td>javax.jdo.option.ConnectionDriverName</td>
  <td>数据库的驱动类名称</td>
</tr>
<tr>
  <td>javax.jdo.option.ConnectionURL</td>
  <td>数据库的JDBC连接地址</td>
</tr>
<tr>
  <td>javax.jdo.option.ConnectionUserName</td>
  <td>连接数据库所使用的用户名</td>
</tr>
<tr>
  <td>javax.jdo.option.ConnectionPassword</td>
  <td>连接数据库所使用的密码</td>
</tr>
</tbody></table>


<p>Hive默认的配置使用的是Derby数据库来存储Hive的元数据信息，其配置信息如下：</p>

<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.derby.jdbc.EmbeddedDriver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Driver class name for a JDBC metastore<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:derby:;databaseName=metastore_db;create=true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>
      JDBC connect string for a JDBC metastore.
      To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.
      For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.
    <span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>APP<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Username to use against metastore database<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mine<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>password to use against metastore database<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<p>需要将Derby数据库切换为MySQL数据库的话，只需要修改以上4项配置，这里可以使用vim命令模式下输入 <code>/搜索内容</code> 即可快速定位到指定修改位置，以本文举例修改：</p>

<pre class="prettyprint"><code class="language-xml hljs "><span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionDriverName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>com.mysql.cj.jdbc.Driver<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Driver class name for a JDBC metastore<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionURL<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>jdbc:mysql://localhost:3306/hive?createDatabaseIfNotExist=true&amp;amp;useSSL=false<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>
      JDBC connect string for a JDBC metastore.
      To use SSL to encrypt/authenticate the connection, provide database-specific SSL flag in the connection URL.
      For example, jdbc:postgresql://myhost/db?ssl=true for postgres database.
    <span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionUserName<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>root<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>Username to use against metastore database<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>javax.jdo.option.ConnectionPassword<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>root123456<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>password to use against metastore database<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
</code></pre>

<blockquote>
  <p>在配置 <code>javax.jdo.option.ConnectionURL</code> 的时候，默认要求使用 SSL 连接，URL中使用 useSSL=false ，禁用MySQL连接警告，而且消除了可能会导致Hive初始化MySQL元数据失败的异常。</p>
</blockquote>

<p>此外，还需要将MySQL的驱动包拷贝到Hive的lib目录下：</p>

<blockquote>
  <p>因为MySQL官方强烈建议使用MySQL Connector/J 8.0与MySQL Server 8.0、5.7、5.6和5.5一起使用。所以使用mysql-connector-java-8.0.12.jar，上面配置文件中的驱动名称是 <code>com.mysql.cj.jdbc.Driver</code></p>
</blockquote>



<pre class="prettyprint"><code class="language-shell hljs lasso">cp /home/hadoop/mysql<span class="hljs-attribute">-connector</span><span class="hljs-attribute">-java</span><span class="hljs-subst">-</span><span class="hljs-number">8.0</span><span class="hljs-number">.12</span><span class="hljs-built_in">.</span>jar <span class="hljs-variable">$HIVE_HOME</span>/lib<span class="hljs-subst">/</span></code></pre>

<p><strong>5. 配置 hive-env.sh</strong></p>



<pre class="prettyprint"><code class="language-shell hljs ruby">[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>cd <span class="hljs-variable">$HIVE_CONF_DIR</span>
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>cp hive-env.sh.template hive-env.sh
[hadoop<span class="hljs-variable">@master</span> conf]<span class="hljs-variable">$ </span>vim hive-env.sh
<span class="hljs-comment"># 编辑 hive-env.sh 增加下面3行内容</span>
export <span class="hljs-constant">HADOOP_HOME</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/hadoop/hadoop</span>-<span class="hljs-number">2.9</span>.<span class="hljs-number">1</span>
export <span class="hljs-constant">HIVE_CONF_DIR</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/hive/hive</span>-<span class="hljs-number">2.3</span>.<span class="hljs-number">3</span>/conf
export <span class="hljs-constant">HIVE_AUX_JARS_PATH</span>=<span class="hljs-regexp">/usr/local</span><span class="hljs-regexp">/hive/hive</span>-<span class="hljs-number">2.3</span>.<span class="hljs-number">3</span>/lib</code></pre>

<p> </p>



<h3 id="初始化启动和测试-hive">初始化启动和测试 Hive</h3>

<p><strong>1. Hive 数据库初始化</strong></p>



<pre class="prettyprint"><code class="language-shell hljs avrasm">[hadoop@master conf]$ cd $HIVE_HOME/bin
<span class="hljs-preprocessor"># 初始化mysql数据库</span>
[hadoop@master bin]$ schematool -initSchema -dbType mysql
<span class="hljs-label">SLF4J:</span> Class path contains multiple SLF4J bindings.
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>/lib/log4j-slf4j-impl-<span class="hljs-number">2.6</span><span class="hljs-number">.2</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/local/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/share/hadoop/common/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.25</span><span class="hljs-preprocessor">.jar</span>!/org/slf4j/impl/StaticLoggerBinder<span class="hljs-preprocessor">.class</span>]
<span class="hljs-label">SLF4J:</span> See http://www<span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.org</span>/codes<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#multiple_bindings for an explanation.</span>
<span class="hljs-label">SLF4J:</span> Actual binding is of type [org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.logging</span><span class="hljs-preprocessor">.slf</span>4j<span class="hljs-preprocessor">.Log</span>4jLoggerFactory]
Metastore connection URL:     jdbc:mysql://localhost:<span class="hljs-number">3306</span>/hive?createDatabaseIfNotExist=true&amp;amp<span class="hljs-comment">;useSSL=false</span>
Metastore Connection Driver :     <span class="hljs-keyword">com</span><span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.cj</span><span class="hljs-preprocessor">.jdbc</span><span class="hljs-preprocessor">.Driver</span>
Metastore connection User:     root
Starting metastore schema initialization to <span class="hljs-number">2.3</span><span class="hljs-number">.0</span>
Initialization script hive-schema-<span class="hljs-number">2.3</span><span class="hljs-number">.0</span><span class="hljs-preprocessor">.mysql</span><span class="hljs-preprocessor">.sql</span>
Initialization script completed
schemaTool completed</code></pre>

<p>数据库初始化完成之后，会在MySQL数据库里生成如下metadata表用于存储Hive的元数据信息：</p>

<p><img src="https://code.aliyun.com/jialei/MarkDownIMG/raw/master/hive-metadata-database.png" alt="Hive-Metadata-Database" title=""></p>

<p><strong>2. 启动 Hive 并测试</strong></p>



<pre class="prettyprint"><code class="language-shell hljs lua">[hadoop@master bin]$ cd $HIVE_HOME/bin
# 使用 hive 命令启动Hive
[hadoop@master bin]$ ./hive
which: no hbase <span class="hljs-keyword">in</span> (/usr/<span class="hljs-keyword">local</span>/hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>/bin:/usr/<span class="hljs-keyword">local</span>/jdk1<span class="hljs-number">.8</span><span class="hljs-number">.0</span>_144/bin:/usr/<span class="hljs-keyword">local</span>/jdk1<span class="hljs-number">.8</span><span class="hljs-number">.0</span>_144/bin:/usr/lib64/qt-<span class="hljs-number">3.3</span>/bin:/usr/<span class="hljs-keyword">local</span>/hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>/bin:/usr/<span class="hljs-keyword">local</span>/bin:/usr/bin:/usr/<span class="hljs-keyword">local</span>/sbin:/usr/sbin:/usr/<span class="hljs-keyword">local</span>/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.12</span>/bin:/home/hadoop/.<span class="hljs-keyword">local</span>/bin:/home/hadoop/bin:/usr/<span class="hljs-keyword">local</span>/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/bin:/usr/<span class="hljs-keyword">local</span>/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/sbin:/home/hadoop/.<span class="hljs-keyword">local</span>/bin:/home/hadoop/bin:/home/hadoop/.<span class="hljs-keyword">local</span>/bin:/home/hadoop/bin:/usr/<span class="hljs-keyword">local</span>/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/bin:/usr/<span class="hljs-keyword">local</span>/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/sbin:/usr/<span class="hljs-keyword">local</span>/zookeeper-<span class="hljs-number">3.4</span><span class="hljs-number">.12</span>/bin:/home/hadoop/.<span class="hljs-keyword">local</span>/bin:/home/hadoop/bin:/usr/<span class="hljs-keyword">local</span>/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/bin:/usr/<span class="hljs-keyword">local</span>/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/sbin)
SLF4J: Class path contains multiple SLF4J bindings.
SLF4J: Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/<span class="hljs-keyword">local</span>/hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>/lib/log4j-slf4j-impl-<span class="hljs-number">2.6</span><span class="hljs-number">.2</span>.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: Found binding <span class="hljs-keyword">in</span> [jar:file:/usr/<span class="hljs-keyword">local</span>/hadoop-<span class="hljs-number">2.9</span><span class="hljs-number">.1</span>/share/hadoop/common/lib/slf4j-log4j12-<span class="hljs-number">1.7</span><span class="hljs-number">.25</span>.jar!/org/slf4j/impl/StaticLoggerBinder.class]
SLF4J: See http://www.slf4j.org/codes.html#multiple_bindings <span class="hljs-keyword">for</span> an explanation.
SLF4J: Actual binding is of <span class="hljs-built_in">type</span> [org.apache.logging.slf4j.Log4jLoggerFactory]

Logging initialized using configuration <span class="hljs-keyword">in</span> jar:file:/usr/<span class="hljs-keyword">local</span>/hive-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>/lib/hive-common-<span class="hljs-number">2.3</span><span class="hljs-number">.3</span>.jar!/hive-log4j2.properties Async: <span class="hljs-keyword">true</span>
Hive-on-MR is deprecated <span class="hljs-keyword">in</span> Hive <span class="hljs-number">2</span> <span class="hljs-keyword">and</span> may <span class="hljs-keyword">not</span> be available <span class="hljs-keyword">in</span> the future versions. Consider using a different execution engine (i.e. spark, tez) <span class="hljs-keyword">or</span> using Hive <span class="hljs-number">1.</span>X releases.
hive&gt; show databases;
OK
Time taken: <span class="hljs-number">5.682</span> seconds, Fetched: <span class="hljs-number">0</span> row(s)
hive&gt; desc <span class="hljs-function"><span class="hljs-keyword">function</span> <span class="hljs-title">sum</span>;
<span class="hljs-title">OK</span>
<span class="hljs-title">sum</span><span class="hljs-params">(x)</span></span> - Returns the sum of a set of numbers
Time taken: <span class="hljs-number">0.008</span> seconds, Fetched: <span class="hljs-number">1</span> row(s)
hive&gt;</code></pre>

<p> </p>

<p><strong>怎么退出Hive命令行？</strong></p>

<blockquote>
  <p>两种方式：1.输入 <code>exit;</code> 退出。 2.直接使用 <code>Ctrl + C</code> 中断操作并退出。</p>
</blockquote>

<p>我们目前使用的都是在安装Hive的本机操作Hive，如何在另外的及其上操作远程Hive？接下来再说一下 <strong>启动hiveserver2服务和使用Beeline远程连接Hive</strong></p>

<p>（1）首先在安装了Hive服务的节点上（也就是刚才的master节点，事实上，一般情况下就是在Hadoop的主节点安装Hive）进入Hive的Home目录在 <code>/bin</code> 目录下直接使用 <code>ls</code> 命令查看就会看到 <strong>beeline</strong> 和 <strong>hiveserver2</strong>，在master节点中直接使用命令 <code>./hiveserver2</code> 就可以启动hiveserver2服务。</p>

<p>（2）在另外的一台安装了Hive的机器上进入Hive的Home目录在 <code>/bin</code> 目录下直接使用命令 <code>./beeline</code> 命令启动Beeline命令行客户端，完成启动之后输入如下命令进行连接：</p>

<pre class="prettyprint"><code class="language-shell hljs ruleslanguage"><span class="hljs-array"># </span>输入 !connect jdbc:hive2:<span class="hljs-comment">//主机别名或者IP:端口（默认10000） 用户名 密码</span>
!connect jdbc:hive2:<span class="hljs-comment">//master:10000 hadoop hadoop</span></code></pre>

<p>成功连接之后就会出现：<code>0: jdbc:hive2://master:10000&gt;</code> 这样的形式，这样可以输入Hive命令使用远程的Hive了。</p>

<p> </p>

<p>如果出现问题，一般情况下是Hadoop或者Hive的配置出现了一些小问题，结合节点配置信息，Google或者Baidu都可以解决。</p>

<hr>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Hadoop_Pseudo_Distributed
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/liberty1997/article/details/68973833				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="hadoop-273-pseudo-distributed-on-archlinux">Hadoop-2.7.3 Pseudo Distributed on Archlinux</h1>

<hr>

<blockquote>
  <h2 id="环境">环境</h2>
</blockquote>

<ul>
<li><a href="https://www.archlinux.org" rel="nofollow">Archlinux 4.x</a></li>
<li><a href="http://hadoop.apache.org/releases.html" rel="nofollow">Hadoop-2.7.3</a></li>
<li>JDK7-openjdk</li>
</ul>

<blockquote>
  <h2 id="start">Start</h2>
</blockquote>



<h3 id="1-创建用户名为hadoop的用户">1. 创建用户名为hadoop的用户</h3>

<p><code># useradd -m hadoop -s /bin/bash</code> <em>以root用户登陆创建</em> <br>
<code># passwd hadoop</code> <em>设置hadoop用户的密码</em> <br>
 <strong>为hadoop用户增加sudo权限:</strong> <br>
 <em>sudoers文件如果为440，需要修改权限为640，否则不能对文件进行写入操作</em> <br>
<code># vim /etc/sudoers</code> <em>找到root ALL=(ALL) ALL这行，在它下一行添加hadoop ALL=(ALL) ALL</em></p>



<h3 id="2-安装环境">2. 安装环境</h3>

<ol>
<li>登录hadoop用户</li>
<li>启用网络并安装SSH和JDK</li>
<li><p>配置SSH免密码登录</p>

<p><code>$ ssh yourhostname</code> <em>‘yourhostname’请换成自己的主机名</em> <br>
此时会有ssh首次登录提示，输入yes，然后按提示输入hadoop用户密码，然后退出配置免密码登录</p></li>
</ol>



<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>exitcd  <span class="hljs-comment"># 退出刚才的ssh localhost</span>
<span class="hljs-variable">$ </span>cd ~<span class="hljs-regexp">/.ssh/</span> <span class="hljs-comment"># 没有该目录请先执行ssh yourhostname</span>
<span class="hljs-variable">$ </span>ssh-keygen -t rsa <span class="hljs-comment"># 出现的提示都按 Enter</span>
<span class="hljs-variable">$ </span>cat id_rsa.pub &gt;&gt; authorized_keys <span class="hljs-comment"># 加入授权</span>
<span class="hljs-variable">$ </span>chmod <span class="hljs-number">600</span> ./authorized_keys <span class="hljs-comment"># 更改文件权限</span></code></pre>

<p>再次执行<code>$ ssh yourhostname</code> 检查是否成功免密码登录ssh <br>
3. 配置Java环境变量</p>

<p><code>$ vim ~/.bashrc</code> 在文件最后添加一行<em>(指向JDK的安装位置,不同Linux发行版的安装目录可能不一样)</em> <br>
  <code>export JAVA_HOME=/usr/lib/jvm/java-1.7.0-openjdk</code>  <em>保存并退出</em></p>

<p><code>$ source ~/.bashrc</code> <em>使JAVA_HOME变量生效</em></p>

<p>执行以下两条命令,若两次的输出一样,说明环境变量配置成功 <br>
  <code>$ java -version</code> <br>
  <code>$ $JAVA_HOME/bin/java -version</code> <br>
4. 安装Hadoop-2.7.3</p>

<p>将Hadoop安装在/usr/local/中:</p>

<p><code>shell <br>
  $ sudo tar -zxvf /hadoop的.tar.gz包的位置 -C /usr/local # 解压到/usr/local/中 <br>
  $ cd /usr/local/ <br>
  $ sudo mv ./hadoop-2.7.3/ ./hadoop # 将文件夹名改为hadoop <br>
  $ sudo chown -R hadoop:hadoop ./hadoop # 修改文件权限 <br>
  $ cd /usr/local/hadoop <br>
  $ ./bin/hadoop version # 显示Hadoop版本信息则说明Hadoop安装成功 <br>
</code></p>

<ol>
<li><p>配置hadoop环境变量</p>

<p>在~/.bashrc中增加以下内容:</p>

<pre class="prettyprint"><code class="language-shell hljs bash">
<span class="hljs-comment"># Hadoop Environment Variables</span>

<span class="hljs-keyword">export</span> HADOOP_HOME=/usr/local/hadoop
<span class="hljs-keyword">export</span> HADOOP_INSTALL=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> HADOOP_MAPRED_HOME=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> HADOOP_COMMON_HOME=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> HADOOP_HDFS_HOME=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> YARN_HOME=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> HADOOP_COMMON_LIB_NATIVE_DIR=<span class="hljs-variable">$HADOOP_HOME</span>/lib/native
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HADOOP_HOME</span>/sbin:<span class="hljs-variable">$HADOOP_HOME</span>/bin</code></pre>

<p>保存并执行<code>$ source ~/.bashrc</code>使其生效</p></li>
<li><p>修改hadoop配置文件</p>

<p><code>$ cd /usr/local/hadoop/etc/hadoop/</code></p>

<p>将文件core-site.xml修改为以下内容:</p>

<pre class="prettyprint"><code class="language-XML hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hadoop/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>A base for other temporary directories.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
 <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://yourhostname:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
 <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>将文件hdfs-site.xml修改为以下内容:</p>

<pre class="prettyprint"><code class="language-XML hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
 <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file://${hadoop.tmp.dir}/dfs/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
 <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
   <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file://${hadoop.tmp.dir}/dfs/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
 <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre></li>
<li><p>启动Hadoop</p>

<p>完成上述配置后，执行Namenode的格式化:</p>

<p><code>$ ./bin/hdfs namenode -format</code></p>

<p>在输出信息中找 <em>“successfully formatted”</em> 和 <em>“Exitting with status 0”</em> ，若有则成功；若为 <em>“Exitting with status 1”</em> 则是出错。</p>

<p>启动守护进程:  <br>
<code>$ ./sbin/start-dfs.sh</code> <em>出现 “(yes/no)?” 输yes</em></p>

<p>执行： <br>
<code>$ jps</code> <em>若成功，则会有Jps NameNode DataNode SecondaryNameNode 这四个进程</em></p>

<p><strong>成功启动后可在浏览器输入 <a href="http://yourhostname:50070" rel="nofollow">http://yourhostname:50070</a> 查看相关信息</strong> <br>
<strong>请注意查看此页面中的live nodes 和 dead nodes 的数量,前者为0说明不成功</strong></p></li>
</ol>

<blockquote>
  <h2 id="运行伪分布式实例">运行伪分布式实例</h2>
</blockquote>

<ol>
<li><p>首先，在HDFS中创建用户目录:  </p>

<p><code>$ cd /usr/local/hadoop/</code> </p>

<p><code>$ ./bin/hdfs dfs -mkdir -p /user/hadoop</code></p></li>
<li><p>将./etc/hadoop 中的xml文件作为输入文件复制到分布式文件系统中：</p>

<p><code>$ ./bin/hdfs dfs -mkdir input</code></p>

<p><code>$ ./bin/hdfs dfs -put ./etc/hadoop/*.xml input</code></p>

<p>查看HDFS中的文件列表：</p>

<p><code>$ ./bin/hdfs dfs -ls input</code></p>

<p><code>$ ./bin/hadoop jar ./share/hadoop/mapreduce/hadoop-mapreduce-examples-*.jar grep input output 'dfs[a-z.]+'</code></p>

<p>查看运行结果：</p>

<p><code>$ ./bin/hdfs dfs -cat output/*</code></p>

<p>将运行结果取回本地：</p>

<pre class="prettyprint"><code class="language-shell hljs ruby"><span class="hljs-variable">$ </span>rm -r ./output <span class="hljs-comment"># 先删除本地的output文件夹</span>
<span class="hljs-variable">$ </span>./bin/hdfs dfs -get output ./output <span class="hljs-comment"># 将HDFS上的output文件夹拷贝到本机</span>

<span class="hljs-comment"># Hadoop运行程序时输出目录不能存在</span>


<span class="hljs-comment"># 执行 ./bin/hdfs dfs -rm -r output 可删除output文件夹</span>

<span class="hljs-variable">$ </span>cat ./output/*</code></pre></li>
<li><p>关闭Hadoop</p>

<p><code>$ ./sbin/stop-dfs.sh</code></p>

<p><strong>下次启动Hadoop时不需要格式化namenode，只需要启动start-dfs.sh即可</strong></p></li>
</ol>

<blockquote>
  <h2 id="错误原因分析及警示">错误原因分析及警示</h2>
</blockquote>

<ul>
<li>SSH <br>
<ul><li>/etc/hosts 和 /etc/hostname 中的主机名没有正确配置</li>
<li>SSH服务未启动</li></ul></li>
<li>JAVA_HOME变量 <br>
<ul><li>jdk安装路径没输对 <em>不同发行版的jdk安装路径可能不同,可用whereis java 查找安装路径</em></li>
<li>没有source使环境变量生效</li>
<li>PATH里的路径分隔符是英文冒号不是分号</li></ul></li>
<li>Hadoop <br>
<ul><li>检查/etc/hostname 中的主机名和下行提到的主机名是否一致</li>
<li>检查是否修改/usr/local/hadoop/etc/hadoop/core-site.xml文件中的 <strong>“yourhostname”</strong> 为自己的主机名</li>
<li>重新格式化Namenode前请先删除/usr/local/hadoop/下的tmp目录及其子目录</li></ul></li>
</ul>

<blockquote>
  <h2 id="参考">参考</h2>
</blockquote>

<ul>
<li><a href="http://dblab.xmu.edu.cn/blog/install-hadoop-in-centos/" rel="nofollow">厦大数据库实验室博客</a></li>
<li><a href="https://wiki.apache.org/hadoop" rel="nofollow">Hadoop Wiki</a></li>
<li><a href="https://wiki.archlinux.org/" rel="nofollow">Archlinux Wiki</a></li>
<li><a href="http://www.jianshu.com/p/1e402922ee32/" rel="nofollow">Markdown指南</a></li>
</ul>

<hr>

<p></p><center>©<a href="https://github.com/liberty1997" rel="nofollow">Liberty1997</a>,04/03/2017 </center><p></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
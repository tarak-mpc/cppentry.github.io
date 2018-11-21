---
layout:     post
title:      centos7 安装hadoop：伪分布式配置、全分布模式配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1-centos-新建hadoop用户">1. centos 新建hadoop用户</h2>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-comment">#切换到root用户</span>
[sunwei<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span>su 

<span class="hljs-comment">#增加一个名为 hadoop 的用户</span>
[root<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-comment"># useradd -m hadoop -G root -s /bin/bash</span>

<span class="hljs-comment">#给hadoop新建一个密码</span>
[root<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-comment"># passwd hadoop</span>

<span class="hljs-comment">#为hadoop用户增加管理员权限</span>
[root<span class="hljs-variable">@vdevops</span> sunwei]<span class="hljs-comment"># visudo</span>

在“<span class="hljs-comment">## Allow root to run any commands anywhere”这句下面增加：</span>
hadoop  <span class="hljs-constant">ALL</span>=(<span class="hljs-constant">ALL</span>)       <span class="hljs-constant">ALL</span>
</code></pre>



<h2 id="2配置java环境">2.配置Java环境</h2>

<p>登录hadoop用户。</p>

<p>首先安装JDK，可用yum安装</p>

<p>配置JAVA_HOME环境变量</p>



<pre class="prettyprint"><code class=" hljs avrasm">vim ~/<span class="hljs-preprocessor">.bashrc</span></code></pre>

<p>在文件最后面添加如下单独一行（指向 JDK 的安装位置），并保存</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=/usr/java/jdk1.<span class="hljs-number">8.0</span>_112</code></pre>

<p>接着还需要让该环境变量生效，执行如下代码：</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span>source ~<span class="hljs-regexp">/.bashrc</span></code></pre>

<p>设置好后我们来检验一下是否设置正确：</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span>echo <span class="hljs-variable">$JAVA_HOME</span> 
[hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span>java -version
[hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span><span class="hljs-variable">$JAVA_HOME</span>/bin/java -version</code></pre>

<p>如果设置正确的话，$JAVA_HOME/bin/java -version 会输出 java 的版本信息，且和 java -version 的输出结果一样。</p>



<h2 id="3-安装hadoop">3. 安装hadoop</h2>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-comment">#先创建一个文件夹hadoopDownload用于存放下载的文件（目录/home/hadoop/下）：</span>
[hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$mkdir</span> hadoopDownload

<span class="hljs-comment">#1.下载hadoop</span>
[hadoop<span class="hljs-variable">@vdevops</span> hadoopDownload]<span class="hljs-variable">$ </span>wget <span class="hljs-symbol">http:</span>/<span class="hljs-regexp">/mirrors.hust.edu.cn/apache</span><span class="hljs-regexp">/hadoop/common</span><span class="hljs-regexp">/hadoop-2.8.2/hadoop</span>-<span class="hljs-number">2.8</span>.<span class="hljs-number">2</span>.tar.gz

<span class="hljs-comment">#2.下载mds 该文件包含了检验值，可用于检查 hadoop-2.x.y.tar.gz 的完整性，否则若文件发生了损坏或下载不完整，Hadoop 将无法正常运行。</span>
[hadoop<span class="hljs-variable">@vdevops</span> hadoopDownload]<span class="hljs-variable">$ </span>wget <span class="hljs-symbol">https:</span>/<span class="hljs-regexp">/dist.apache.org/repos</span><span class="hljs-regexp">/dist/release</span><span class="hljs-regexp">/hadoop/common</span><span class="hljs-regexp">/hadoop-2.8.2/hadoop</span>-<span class="hljs-number">2.8</span>.<span class="hljs-number">2</span>.tar.gz.mds

<span class="hljs-comment">#3.检查MD5值是否相等，如果不相等 表示下载的文件不完整</span>

<span class="hljs-comment"># 3.1 列出hadoop-2.8.2.tar.gz.mds的md5检验值</span>
[hadoop<span class="hljs-variable">@vdevops</span> hadoopDownload]<span class="hljs-variable">$ </span>cat ./hadoop-<span class="hljs-number">2.8</span>.<span class="hljs-number">2</span>.tar.gz.mds | grep <span class="hljs-string">'MD5'</span>

<span class="hljs-comment"># 3.2 计算hadoop-2.8.2.tar.gz的MD5值，并转化为大写，方便比较</span>
[hadoop<span class="hljs-variable">@vdevops</span> hadoopDownload]<span class="hljs-variable">$ </span>md5sum ./hadoop-<span class="hljs-number">2.8</span>.<span class="hljs-number">2</span>.tar.gz | tr <span class="hljs-string">"a-z"</span>  <span class="hljs-string">"A-Z"</span>
</code></pre>

<p>比较3.1 3.2步列出的md5值，若相等则说明下载的tar.gz文件相等，若不相等，则必须重新下载。</p>

<p>解压hadoop包</p>



<pre class="prettyprint"><code class=" hljs perl">[hadoop<span class="hljs-variable">@vdevops</span> hadoopDownload]$ sudo tar -zxf /home/hadoop/hadoopDownload/hadoop-<span class="hljs-number">2.8</span>.<span class="hljs-number">2</span>.tar.gz -C /usr/<span class="hljs-keyword">local</span> <span class="hljs-comment"># 解压到/usr/local目录下</span>

[hadoop<span class="hljs-variable">@vdevops</span> hadoopDownload]$ cd /usr/<span class="hljs-keyword">local</span>/ 
[hadoop<span class="hljs-variable">@vdevops</span> <span class="hljs-keyword">local</span>]<span class="hljs-variable">$sudo</span> mv ./hadoop-<span class="hljs-number">2.8</span>.<span class="hljs-number">2</span>/ ./hadoop <span class="hljs-comment">#修改文件夹名</span>
[hadoop<span class="hljs-variable">@vdevops</span> <span class="hljs-keyword">local</span>]$ sudo <span class="hljs-keyword">chown</span> -R hadoop:hadoop ./hadoop <span class="hljs-comment"># 修改文件权限</span></code></pre>



<h2 id="4hadoop单机配置非分布式">4.Hadoop单机配置(非分布式)</h2>



<h2 id="5hadoop伪分布式配置">5.Hadoop伪分布式配置</h2>



<h3 id="51-设置-hadoop-环境变量">5.1 设置 HADOOP 环境变量</h3>

<p>在文件/home/hadoop/.bashrc 中的最后添加以下配置：</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-comment"># Hadoop Environment Variables</span>
<span class="hljs-keyword">export</span> HADOOP_HOME=/usr/local/hadoop
<span class="hljs-keyword">export</span> HADOOP_INSTALL=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> HADOOP_MAPRED_HOME=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> HADOOP_COMMON_HOME=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> HADOOP_HDFS_HOME=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> YARN_HOME=<span class="hljs-variable">$HADOOP_HOME</span>
<span class="hljs-keyword">export</span> HADOOP_COMMON_LIB_NATIVE_DIR=<span class="hljs-variable">$HADOOP_HOME</span>/lib/native
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$HADOOP_HOME</span>/sbin:<span class="hljs-variable">$HADOOP_HOME</span>/bin</code></pre>

<p>这些变量在启动 Hadoop 进程时需要用到，不设置的话可能会报错（这些变量也可以通过修改 ./etc/hadoop/hadoop-env.sh 实现）</p>



<h3 id="52-修改hadoop配置文件">5.2 修改Hadoop配置文件</h3>

<p>Hadoop 的配置文件位于 /usr/local/hadoop/etc/hadoop/ 中 <br>
伪分布式需要修改2个配置文件 core-site.xml 和 hdfs-site.xml</p>



<h4 id="521修改core-sitexml">5.2.1:修改core-site.xml：</h4>

<p>将其中的</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>修改成：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>A base for other temporary directories.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>              </code></pre>



<h4 id="522-修改hdfs-sitexml文件将">5.2.2: 修改hdfs-site.xml文件：将</h4>

<p><code>&lt;configuration&gt;&lt;/configuration&gt;</code> <br>
修改成</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp/dfs/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp/dfs/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>



<h3 id="53-namenode格式化">5.3  NameNode格式化</h3>

<p>退回到目录/usr/local/hadoop下，执行 NameNode 的格式化:</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> hadoop]<span class="hljs-variable">$ </span>./bin/hdfs namenode -format</code></pre>

<p>格式化成功标志：“successfully formatted.”，“Exiting with status 0”。如果失败的话：Exiting with status 1 <br>
<img src="https://img-blog.csdn.net/20171031140953217?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Vud2Vpam0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="54-开启-namenode-和-datanode-守护进程">5.4 开启 NameNode 和 DataNode 守护进程：</h3>

<p>在目录/usr/local/hadoop下：</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> hadoop]<span class="hljs-variable">$ </span>sudo ./sbin/start-dfs.sh <span class="hljs-comment">#开启 NameNode 和 DataNode 守护进程：</span></code></pre>

<p>在./sbin/start-dfs.sh时可能会报以下错误: <br>
Error:   JAVA_HOME is not set and could not be found. <br>
修改 usr/local/hadoop/etc/hadoop/hdoop-env.sh: <br>
将语句      export JAVA_HOME=${JAVA_HOME} <br>
修改为      export JAVA_HOME=/usr/local/java/jdk1.8.0_161</p>

<p>开启之后：</p>

<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">964</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">1556</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1158</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">1389</span> <span class="hljs-constant">SecondaryNameNode</span></code></pre>

<p>若出现如下 SSH 的提示 “Are you sure you want to continue connecting”，输入 yes 即可。 <br>
<img src="https://img-blog.csdn.net/20171220111924786?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Vud2Vpam0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>关闭守护进程：</p>



<pre class="prettyprint"><code class=" hljs vbnet">./sbin/<span class="hljs-keyword">stop</span>-dfs.sh</code></pre>

<p>有关启动与关闭的日志目录：/usr/local/hadoop/logs/</p>



<h2 id="6运行hadoop伪分布式实例">6.运行Hadoop伪分布式实例</h2>

<p>参考：<a href="http://blog.csdn.net/sunweijm/article/details/78465230" rel="nofollow">http://blog.csdn.net/sunweijm/article/details/78465230</a> <br>
的第三部分【3 上传jar包部署到hadoop服务器中】</p>



<h2 id="7-启动yarn">7. 启动YARN</h2>



<h3 id="71-首先重命名-mapred-sitexmltemplate-文件">7.1 首先重命名 mapred-site.xml.template 文件</h3>



<pre class="prettyprint"><code class=" hljs avrasm">[hadoop@vdevops hadoop]$ mv ./etc/hadoop/mapred-site<span class="hljs-preprocessor">.xml</span><span class="hljs-preprocessor">.template</span> ./etc/hadoop/mapred-site<span class="hljs-preprocessor">.xml</span></code></pre>



<h3 id="72-修改mapred-sitexml">7.2 修改mapred-site.xml</h3>

<p>修改为：</p>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>



<h3 id="73-修改文件yarn-sitexml">7.3 修改文件yarn-site.xml：</h3>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>

<span class="hljs-comment">&lt;!-- Site specific YARN configuration properties --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>



<h3 id="74-启动yarn">7.4 启动YARN</h3>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> hadoop]<span class="hljs-variable">$ </span>./sbin/start-dfs.sh <span class="hljs-comment">#首先启动守护进程</span>
[hadoop<span class="hljs-variable">@vdevops</span> hadoop]<span class="hljs-variable">$ </span>./sbin/start-yarn.sh <span class="hljs-comment">#启动YARN</span>
[hadoop<span class="hljs-variable">@vdevops</span> hadoop]<span class="hljs-variable">$ </span>./sbin/mr-jobhistory-daemon.sh start historyserver <span class="hljs-comment"># 开启历史服务器，才能在Web中查看任务运行情况</span></code></pre>

<p>开启后通过 jps 查看，可以看到多了 NodeManager 和ResourceManager 两个后台进程(比较只开启 NameNode 和 DataNode 守护进程)</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">964</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">2228</span> <span class="hljs-constant">ResourceManager</span>
<span class="hljs-number">2740</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">1158</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">2566</span> <span class="hljs-constant">NodeManager</span>
<span class="hljs-number">1389</span> <span class="hljs-constant">SecondaryNameNode</span></code></pre>



<h2 id="8-全分布模式">8. 全分布模式</h2>



<h3 id="81首先需要配置主从节点的免密登录">8.1首先需要配置主从节点的免密登录</h3>

<p>参考：<a href="http://blog.csdn.net/sunweijm/article/details/78852889" rel="nofollow">http://blog.csdn.net/sunweijm/article/details/78852889</a> <br>
这样从 主节点 登录 从节点就可以不用输用户密码。 <br>
<strong>注意主从节点的用户名需要一样</strong></p>



<h4 id="1配置core-sitexml">1.配置core-site.xml</h4>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>A base for other temporary directories.<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://vdevops:9900<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">description</span>&gt;</span>set namenode's hostname and ports<span class="hljs-tag">&lt;/<span class="hljs-title">description</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>解释： <br>
vdevops是主节点的hostname（也可以写成ip形式），所有丛节点上的该处都应该写上主节点的hostname或者ip，这里就是vdevops。 <br>
指定端口为9900，所有主从节点端口都为9900</p>



<h4 id="2配置hdfs-sitexml">2.配置hdfs-site.xml</h4>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>2<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.namenode.name.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp/dfs/name<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.datanode.data.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>file:/usr/local/hadoop/tmp/dfs/data<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.webhdfs.enabled<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>true<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>解释： <br>
dfs.replication：因为hadoop是具有可靠性的，它会备份多个副本，value的值就是HDFS的副本数，一般是等于从节点的个数（小于等于从节点的数量） <br>
如果想要snn（SecondaryNameNode）运行在另外一台单独节点上，需要在hdfs-site.xml上增加以下内容：</p>



<pre class="prettyprint"><code class=" hljs xml">            <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.nameservices<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hadoop-cluster<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
            <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>



<h4 id="3配置mapred-sitexml">3.配置mapred-site.xml</h4>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapred.job.tracker<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>http://vdevops:9901<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>



<h4 id="4配置mapred-sitexml">4.配置mapred-site.xml</h4>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
<span class="hljs-comment">&lt;!-- Site specific YARN configuration properties --&gt;</span>
    <span class="hljs-comment">&lt;!-- 指定ResourceManager的地址--&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>vdevops<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-comment">&lt;!-- 指定reducer获取数据的方式--&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>解释： <br>
vdevops是主节点的hostname，从节点的该值也都应该是主节点的hostname，这里就是vdevops。</p>

<p>所以所有节点（主从节点）的core-site.xml、hdfs-site.xml、mapred-site.xml、yarn-site.xml配置文件内容都应该相同</p>



<h3 id="82实现主节点控制从节点的集群控制测试">8.2实现主节点控制从节点的集群控制测试</h3>



<h4 id="1主节点中">1.主节点中</h4>

<p>修改hadoop/etc/hadoop/slaves文件：</p>

<p>删除原先的localhost配置 <br>
将从节点的hostname或者ip添加进去 <br>
<img src="https://img-blog.csdn.net/20171221102722257?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Vud2Vpam0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
这里我的从节点只有test2一个。 <br>
当你去执行start-dfs.sh时，它会去slaves文件中去找从节点（这就是配置免密登录的原因）然后去启动从节点。</p>



<h4 id="2主节点对自己的免密登录配置">2.主节点对自己的免密登录配置</h4>



<pre class="prettyprint"><code class=" hljs avrasm">[hadoop@vdevops <span class="hljs-preprocessor">.ssh</span>]$ <span class="hljs-keyword">cp</span> id_rsa<span class="hljs-preprocessor">.pub</span>  authorized_keys
<span class="hljs-preprocessor">#测试对本地的免密登录，如下显示</span>
[hadoop@vdevops <span class="hljs-preprocessor">.ssh</span>]$ ssh <span class="hljs-number">127.0</span><span class="hljs-number">.0</span><span class="hljs-number">.1</span>  
Last login: Wed <span class="hljs-keyword">Dec</span> <span class="hljs-number">20</span> <span class="hljs-number">15</span>:<span class="hljs-number">27</span>:<span class="hljs-number">45</span> <span class="hljs-number">2017</span> from <span class="hljs-number">192.168</span><span class="hljs-number">.1</span><span class="hljs-number">.247</span></code></pre>



<h4 id="3测试主节点控制从节点">3.测试主节点控制从节点</h4>

<p><strong>主节点上运行start-dfs.sh</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span>/usr/local/hadoop/sbin/start-dfs.sh</code></pre>

<p><strong>查看启动服务情况</strong></p>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-comment">#主节点上</span>
[hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">28192</span> <span class="hljs-constant">SecondaryNameNode</span>
<span class="hljs-number">28343</span> <span class="hljs-constant">Jps</span>
<span class="hljs-number">27935</span> <span class="hljs-constant">NameNode</span></code></pre>



<pre class="prettyprint"><code class=" hljs ruby"><span class="hljs-comment">#从节点上</span>
[hadoop<span class="hljs-variable">@test2</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">27702</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">27914</span> <span class="hljs-constant">Jps</span></code></pre>

<p><strong>主节点上运行start-yarn.sh</strong></p>



<pre class="prettyprint"><code class=" hljs ruby">    [hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span>/usr/local/hadoop/sbin/start-yarn.sh</code></pre>

<p>再次查看主节点：</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> ~]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">16372</span> <span class="hljs-constant">ResourceManager</span>
<span class="hljs-number">15031</span> <span class="hljs-constant">NameNode</span>
<span class="hljs-number">15288</span> <span class="hljs-constant">SecondaryNameNode</span>
<span class="hljs-number">16639</span> <span class="hljs-constant">Jps</span></code></pre>

<p>再次查看从几点：</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@test2</span> hadoop]<span class="hljs-variable">$ </span>jps
<span class="hljs-number">27972</span> <span class="hljs-constant">NodeManager</span>
<span class="hljs-number">27702</span> <span class="hljs-constant">DataNode</span>
<span class="hljs-number">28124</span> <span class="hljs-constant">Jps</span></code></pre>

<p>在web平台查看： <br>
<img src="https://img-blog.csdn.net/20171221111842168?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Vud2Vpam0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20171221112238339?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3Vud2Vpam0=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<h3 id="一些问题">一些问题：</h3>

<p><strong>1.</strong>一开始在启动运行start-dfs.sh时，发现子节点的Datanode启动不了 <br>
    参考：<a href="http://blog.csdn.net/mmdlyn/article/details/52966942" rel="nofollow">http://blog.csdn.net/mmdlyn/article/details/52966942</a> <br>
    <a href="http://blog.csdn.net/xiesai588/article/details/51222889" rel="nofollow">http://blog.csdn.net/xiesai588/article/details/51222889</a> <br>
    这种错误的原因是多次对namenode格式化使得VERSION不一致造成的。所以删除你在core-site.xml中配置的hadoop.tmp.dir所对应目录下的data目录。 <br>
    1.1 首先在坏死的节点（这里是test2）上单独启动datanode： <br>
    进入到sbin下</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@test2</span> sbin]<span class="hljs-variable">$ </span>hadoop-daemon.sh  start datanode</code></pre>

<p>1.2 然后用jps查看发现test2的datanode仍然没有起来， <br>
1.3 删除/usr/local/hadoop/tmp/dfs下的data目录 <br>
1.4 然后运行start-all.sh就可以看到从节点上datanode已经起来</p>

<p><strong>2.</strong>刚启动hadoop时，删除hdfs文件时报错 <br>
报错信息：</p>



<pre class="prettyprint"><code class=" hljs sql">[hadoop@vdevops hadoop]$ hdfs dfs -rm -r  /user/hadoop/README.md
17/12/20 18:38:10 WARN util.NativeCodeLoader: Unable to <span class="hljs-operator"><span class="hljs-keyword">load</span> native-hadoop library <span class="hljs-keyword">for</span> your platform... <span class="hljs-keyword">using</span> builtin-java classes <span class="hljs-keyword">where</span> applicable
rm: Cannot <span class="hljs-keyword">delete</span> /<span class="hljs-keyword">user</span>/hadoop/README.md. Name node <span class="hljs-keyword">is</span> <span class="hljs-keyword">in</span> safe mode.</span></code></pre>

<p>关闭safe mode模式就可以了：</p>



<pre class="prettyprint"><code class=" hljs ruby">[hadoop<span class="hljs-variable">@vdevops</span> hadoop]<span class="hljs-variable">$ </span>bin/hadoop dfsadmin -safemode leave</code></pre>

<hr>

<p>[1]: Hadoop安装教程 <a href="http://www.powerxing.com/install-hadoop-in-centos/" rel="nofollow">http://www.powerxing.com/install-hadoop-in-centos/</a> <br>
[2]:《Hadoop 实战 r3》 <br>
[3]:hadoop集群的配置 <a href="https://www.cnblogs.com/ejiyuan/p/5557061.html" rel="nofollow">https://www.cnblogs.com/ejiyuan/p/5557061.html</a> <br>
[4]:手把手教你搭建Hadoop全分布式集群        <a href="https://www.cnblogs.com/zhangyinhua/p/7652686.html#_label0" rel="nofollow">https://www.cnblogs.com/zhangyinhua/p/7652686.html#_label0</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Centos7搭建Hadoop 2.X伪分布式环境并运行wordcount MapReduce示例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-tomorrow-night-eighties">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="1-下载hadoop">1. 下载Hadoop</h2>

<p>在 Apache的 Hadoop项目界面找到hadoop的 <a href="http://hadoop.apache.org/releases.html" rel="nofollow">hadoop下载地址</a> <br>
<a href="http://hadoop.apache.org/releases.html" rel="nofollow">http://hadoop.apache.org/releases.html</a> <br>
<strong>因为直接从Apache官方下载文件速度很慢，所以在表格下面选择别的镜像站地址。</strong> <br>
在弹出的界面中会推荐一个下载速度很快的国内镜像站。 <br>
<img src="https://img-blog.csdn.net/20180514194513834?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20180514194522281?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
选择其中某个版本的hadoop下载，因为现在市面上绝大部分的学习资料都是以hadoop 2.x 为基础，所以推荐大家下载hadoop 2.x的版本。 <br>
没有安装图形界面的系统可以直接在命令行中使用下面的wget命令下载文件</p>



<pre class="prettyprint"><code class=" hljs avrasm">wget http://mirrors<span class="hljs-preprocessor">.shu</span><span class="hljs-preprocessor">.edu</span><span class="hljs-preprocessor">.cn</span>/apache/hadoop/common/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.6</span>/hadoop-<span class="hljs-number">2.7</span><span class="hljs-number">.6</span><span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
</code></pre>



<h2 id="2-配置ssh免密码登录">2. 配置ssh免密码登录</h2>

<p><strong>以下操作均在root用户中进行，centos7 可以在命令行中使用  su  命令，输入密码后进入root用户。并且默认centos系统中已经正确的配置好了java环境</strong></p>

<p><strong>- 进入当前用户目录，找到 .ssh 文件夹</strong></p>



<pre class="prettyprint"><code class=" hljs glsl">cd ~      <span class="hljs-comment">//切换到当前用户的用户目录下</span>
ls -<span class="hljs-built_in">all</span>   <span class="hljs-comment">//查看当前用户目录下的所有文件</span></code></pre>

<p>查看当前用户的目录下是否有  .ssh  文件夹，如果没有就自己创建一个 <br>
<img src="https://img-blog.csdn.net/20180514211306952?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><strong>- 生成私钥和公钥</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">cd <span class="hljs-built_in">.</span>ssh                 
ssh<span class="hljs-attribute">-keygen</span> <span class="hljs-attribute">-t</span> rsa           <span class="hljs-comment">//  生成公钥和私钥，期间系统会询问密钥的保存位置，直接一路回车确认即可</span>
cp id_rsa<span class="hljs-built_in">.</span>pub authorized_keys           <span class="hljs-comment">// 复制公钥</span></code></pre>

<p><img src="https://img-blog.csdn.net/20180514211737542?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li>验证ssh免密码登录是否成功</li>
</ul>



<pre class="prettyprint"><code class=" hljs php">ssh localhost           <span class="hljs-comment">//ssh登录本机，第一次登录可能需要输入一次密码，退出之后再登录就不需要了</span>
<span class="hljs-keyword">exit</span></code></pre>

<p><img src="https://img-blog.csdn.net/2018051421183098?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>如果在输入ssh localhost之后出现 报错：Permission denied (publickey,gssapi-keyex,gssapi-with-mic) <br>
这是因为ssh公钥验证失败导致的错误，这个错误的解决办法可以参照别人的博客：</p>

<p><a href="https://blog.csdn.net/u010853261/article/details/54811554" rel="nofollow">以root用户远程登录Linux报错：Permission denied</a></p>

<p><a href="https://blog.csdn.net/songhuiqiao/article/details/40427739" rel="nofollow">ssh 公钥认证报错：Permission denied解决</a></p>



<h2 id="3-安装配置hadoop">3. 安装配置Hadoop</h2>

<p><strong>以下操作均在root用户中进行，centos7 可以在命令行中使用  su  命令，输入密码后进入root用户。并且默认centos系统中已经正确的配置好了java环境</strong></p>

<p><strong>- 解压Hadoop源文件</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">tar <span class="hljs-attribute">-xzvf</span> hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.6</span><span class="hljs-built_in">.</span>tar<span class="hljs-built_in">.</span>gz           <span class="hljs-comment">//解压Hadoop安装包</span>
mv hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.6</span> /opt                    <span class="hljs-comment">// 将解压后的安装包移动到别的地方，方便管理。</span></code></pre>

<p><strong>- 配置hadoop环境变量</strong></p>



<pre class="prettyprint"><code class=" hljs cs">vim /etc/profile            <span class="hljs-comment">//配置系统变量，配置之后可在全局任意地方使用Hadoop命令</span></code></pre>

<p>打开文件之后，在文件的最后面添加hadoop的配置信息</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> HADOOP_HOME=/opt/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">6</span>            //这个地方记得修改成自己Hadoop安装目录的地址
<span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$HADOOP_HOME</span>/bin:<span class="hljs-variable">$PATH</span>               </code></pre>

<p>使配置文件生效</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">source</span> /etc/profile                             </code></pre>

<p>之后可以输入命令验证配置是否正确生效 <br>
<img src="https://img-blog.csdn.net/20180514213521495?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><strong>- 修改hadoop配置文件</strong></p>

<p>修改 /opt/hadoop-2.7.6/etc/hadoop/ 下面的hadoop配置文件 hadoop-env.sh  、hdfs-site.xml  、 core-site.xml 这三个文件</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> /opt/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">6</span>/etc/hadoop/                //切换到Hadoop配置文件所在的目录</code></pre>

<p>1 .  hadoop-env.sh</p>

<p>将文件中的</p>

<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-keyword">export</span> JAVA_HOME=<span class="hljs-variable">${JAVA_HOME}</span></code></pre>

<p>修改为</p>



<pre class="prettyprint"><code class=" hljs ruby">export <span class="hljs-constant">JAVA_HOME</span>=<span class="hljs-regexp">/opt/java</span><span class="hljs-regexp">/jdk1.8.0_171          /</span><span class="hljs-regexp">/把原来的相对地址更改为绝对地址</span></code></pre>

<p>2 . hdfs-site.xml</p>

<p>补充最后的属性：</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span>
</code></pre>

<p>3 . core-site.xml <br>
在文件的最后补充：</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://localhost:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>- 启动hadoop</strong></p>



<pre class="prettyprint"><code class=" hljs lasso">cd /opt/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.6</span>/bin                <span class="hljs-comment">//切换到Hadoop安装目录下的bin目录</span>
hdfs namenode <span class="hljs-attribute">-format</span>                   <span class="hljs-comment">//第一次启动Hadoop，要格式化namenode，之后再启动就不需要再格式化了</span>
cd /opt/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.6</span>/sbin               <span class="hljs-comment">//切换到Hadoop安装目录下的sbin目录</span>
<span class="hljs-built_in">.</span>/start<span class="hljs-attribute">-dfs</span><span class="hljs-built_in">.</span>sh                          <span class="hljs-comment">//启动Hadoop</span>
jps                                     <span class="hljs-comment">//查看已经开启的进程，看namenode，datanode是否开启</span></code></pre>

<p><img src="https://img-blog.csdn.net/20180514220752531?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
在centos系统的浏览器中输入 <a href="http://localhost:50070" rel="nofollow">http://localhost:50070</a>  查看运行在本机上的hadoop的运行状态 <br>
<img src="https://img-blog.csdn.net/20180514220708307?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h2 id="4-配置yarn">4. 配置YARN</h2>

<p>修改  /opt/hadoop-2.7.6/etc/hadoop  目录下的YARN配置文件</p>



<pre class="prettyprint"><code class=" hljs lasso">cd /opt/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.6</span>/etc/hadoop
mv mapred<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span><span class="hljs-built_in">.</span>template mapred<span class="hljs-attribute">-site</span><span class="hljs-built_in">.</span><span class="hljs-built_in">xml</span>             <span class="hljs-comment">//首先复制YARN配置文件</span></code></pre>

<p>1 . mapred-site.xml <br>
在最后修改：</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
             <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
             <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>2 . yarn-site.xml <br>
在最后修改：</p>

<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
 <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
 <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services.mapreduce.shuffle.class<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
  <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>org.apache.hadoop.mapred.ShuffleHandler<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
 <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>运行YARN，验证是否成功</p>



<pre class="prettyprint"><code class=" hljs lasso">cd /opt/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.6</span>/sbin
<span class="hljs-built_in">.</span>/start<span class="hljs-attribute">-yarn</span><span class="hljs-built_in">.</span>sh                 <span class="hljs-comment">//开启YARN</span>
jps</code></pre>

<p><img src="https://img-blog.csdn.net/20180514221718200?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
在浏览器中输入 <a href="http://localhost:8088" rel="nofollow">http://localhost:8088</a> 查看YARN管理的集群状态 <br>
<img src="https://img-blog.csdn.net/20180514221804596?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>



<h2 id="5-运行mapreduce示例wordcount">5. 运行MapReduce示例(Wordcount)</h2>

<p>Wordcount是MapReduce的入门示例程序，相当于我们在学某个编程语言时写的Hello World示例一样。这个程序可以统计某个文件中，各个单词出现的次数。Wordcount程序的jar包已经放置在hadoop安装目录下的   /share/hadoop/mapreduce 文件夹中。 <br>
 本次我们使用hadoop安装包下自带的几个文件，测试Wordcount程序的运行效果。</p>



<pre class="prettyprint"><code class=" hljs bash"><span class="hljs-built_in">cd</span> /opt/hadoop-<span class="hljs-number">2.7</span>.<span class="hljs-number">6</span>
ls -all </code></pre>

<p>可以看到在hadoop的安装目录下有几个txt文件，我们使用这几个文件中的某个文件作为Wordcount的测试文件。 <br>
<img src="https://img-blog.csdn.net/20180515112204259?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
 - 上传文件到HDFS</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop fs <span class="hljs-attribute">-mkdir</span> /input                         <span class="hljs-comment">//在HDFS的根目录下新建 input 目录</span>
hadoop fs <span class="hljs-attribute">-put</span> NOTICE<span class="hljs-built_in">.</span>txt /input                <span class="hljs-comment">//将本地的NOTICE.txt文件上传到HDFS的 input 目录下</span>
hadoop fs <span class="hljs-attribute">-ls</span> <span class="hljs-attribute">-R</span> <span class="hljs-subst">/</span>                              <span class="hljs-comment">//查看文件是否成功上传到HDFS上面</span>
</code></pre>

<p>可以看到，已经成功的在本机的HDFS的根目录下创建了 input 文件夹，并将NOTICE.txt文件上传到了HDFS上的 input 文件夹下面。 <br>
<img src="https://img-blog.csdn.net/20180515112316168?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<ul>
<li>运行Wordcount示例程序</li>
</ul>

<p>使用hadoop jar 命令， 后面先指定程序虽用jar包的路径，后面是要运行的程序的名称，最后是输入文件和输出路径，这个命令要根据自己本机Hadoop的配置做相应的修改。</p>



<pre class="prettyprint"><code class=" hljs lasso">hadoop jar /opt/hadoop<span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.6</span>/share/hadoop/mapreduce/hadoop<span class="hljs-attribute">-mapreduce</span><span class="hljs-attribute">-examples</span><span class="hljs-subst">-</span><span class="hljs-number">2.7</span><span class="hljs-number">.6</span><span class="hljs-built_in">.</span>jar wordcount /input /output                
hadoop fs <span class="hljs-attribute">-ls</span> <span class="hljs-attribute">-R</span> <span class="hljs-subst">/</span>                              <span class="hljs-comment">//查看程序运行后产生的文件</span>
hadoop fs <span class="hljs-attribute">-cat</span> /output/part<span class="hljs-attribute">-r</span><span class="hljs-subst">-</span><span class="hljs-number">00000</span>             <span class="hljs-comment">//查看程序最终的运行结果</span></code></pre>

<p><img src="https://img-blog.csdn.net/20180515112550332?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
可以看到，在运行Wordcount程序之后，HDFS上面产生了很多的文件，其中 /output/part-r-00000    文件就是程序最终的运行结果。查看该文件可以看到： <br>
<img src="https://img-blog.csdn.net/20180515112518521?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2toeHU2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
这说明已经成功的运行了Wordcount示例程序。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
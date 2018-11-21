---
layout:     post
title:      大数据Hadoop之HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/chao2016/article/details/79020011				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p></p><div class="toc"><div class="toc">
<ul>
<li><a href="#1hdfs%E6%A6%82%E8%BF%B0" rel="nofollow">1.HDFS概述</a></li>
<li><a href="#2hdfs%E6%9E%B6%E6%9E%84" rel="nofollow">2.HDFS架构</a></li>
<li><a href="#3%E4%BC%AA%E5%88%86%E5%B8%83%E5%BC%8F%E4%B8%8Bhdfs%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%E7%9A%84%E4%BF%AE%E6%94%B9" rel="nofollow">3.伪分布式下：HDFS配置文件的修改</a></li>
<li><a href="#4%E5%90%AF%E5%8A%A8hdfs" rel="nofollow">4.启动HDFS</a></li>
<li><a href="#5%E5%90%AF%E5%8A%A8hdfs%E7%9A%84%E8%BF%87%E7%A8%8B%E4%B8%AD%E5%8F%AF%E8%83%BD%E5%87%BA%E7%8E%B0%E7%9A%84%E9%97%AE%E9%A2%98" rel="nofollow">5.启动HDFS的过程中可能出现的问题</a></li>
<li><a href="#6%E5%81%9C%E6%AD%A2hdfs" rel="nofollow">6.停止HDFS</a></li>
<li><a href="#7hdfs-shell%E7%9A%84%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4" rel="nofollow">7.HDFS shell的常用命令</a></li>
<li><a href="#8java-api%E6%93%8D%E4%BD%9Chdfs%E6%96%87%E4%BB%B6" rel="nofollow">8.Java API操作HDFS文件</a></li>
</ul>
</div>
</div>




<h1 id="1hdfs概述">1.HDFS概述</h1>

<ul>
<li>全称：Hadoop Distributed File System（Hadoop分布式文件系统）</li>
<li><p>HDFS优点</p>

<blockquote>
  <ol><li>数据冗余、硬件容错</li>
  <li>处理流式的数据访问</li>
  <li>适合存储大文件</li>
  <li>可构建在廉价机器上</li></ol>
</blockquote></li>
<li><p>HDFS缺点</p>

<blockquote>
  <ol><li>不能实现低延迟的数据访问</li>
  <li>不适合小文件存储：小文件越多，源数据存放在NameNode上所占用的内存越多</li></ol>
</blockquote></li>
</ul>



<h1 id="2hdfs架构">2.HDFS架构</h1>

<ul>
<li>1个Master（NameNode）带N个Slaves（即DataNode）</li>
<li>1个文件会被拆分成多个Block</li>
</ul>

<blockquote>
  <p>blocksize：128M <br>
  130M ==&gt; 2个Block： 128M 和 2M <br>
  除了最后一个块，文件中所有的块都是128M</p>
</blockquote>

<ul>
<li><p>NameNode（NN）： <br>
1）负责客户端请求的响应 <br>
2）负责元数据（文件的名称、副本系数、Block存放的DN）的管理</p></li>
<li><p>DataNode（DN）： <br>
1）存储用户的文件对应的数据块（Block） <br>
2）要定期向NN发送心跳信息，汇报本身及其所有的block信息，即健康状况</p></li>
<li><p>replication factor：副本系数、副本因子</p></li>
</ul>



<h1 id="3伪分布式下hdfs配置文件的修改">3.伪分布式下：HDFS配置文件的修改</h1>

<ul>
<li>我的配置文件路径为：/home/hadoop/app/hadoop-2.6.0-cdh5.7.0/etc/hadoop/hdfs-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml">// 直接添加：
<span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span></code></pre>

<ul>
<li>注：真实集群下还要另外配置slaves文件。</li>
</ul>



<h1 id="4启动hdfs">4.启动HDFS</h1>

<ul>
<li>1）格式化文件系统（仅第一次执行即可，不要重复执行），格式化的文件系统会放在core-site.xml配置的tmp文件夹里：</li>
</ul>



<pre class="prettyprint"><code class=" hljs rsl">hdfs namenode -<span class="hljs-built_in">format</span>
<span class="hljs-comment">// 或者</span>
hadoop namenode -<span class="hljs-built_in">format</span></code></pre>

<blockquote>
  <p>注意：过程中如果出现Y/N的提示：一定要大写Y。</p>
</blockquote>

<ul>
<li>2）Hadoop的环境变量已设置（见<a href="http://blog.csdn.net/chao2016/article/details/78998352" rel="nofollow">《大数据Hadoop之环境搭建》</a>），直接打命令启动HDFS：</li>
</ul>



<pre class="prettyprint"><code class=" hljs sql"><span class="hljs-operator"><span class="hljs-keyword">start</span>-dfs.sh</span></code></pre>

<ul>
<li>3）验证是否启动成功：</li>
</ul>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/** 1.查看进程：应该有三个
      DataNode、NameNode、SecondaryNameNode */</span>
jps</code></pre>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-javadoc">/** 2.本地的浏览器访问：http://chao:50070 
      或 http://192.168.27.131:50070 */</span></code></pre>

<blockquote>
  <p>会看到如下的页面：</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20180110092544773?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhbzIwMTY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="HDFS浏览器视图" title=""></p>



<h1 id="5启动hdfs的过程中可能出现的问题">5.启动HDFS的过程中可能出现的问题</h1>

<ul>
<li><p>如果在start-dfs.sh后jps只看到两个进程，没有DataNode进程，则是DataNode和NameNode的Cluster ID不匹配，下列方法解决：</p>

<blockquote>
  <ol><li>复制：/home/hadoop/app/tmp/dfs/data/current/VERSION 里的Cluster ID到tmp/dfs/name/current/VERSION中；</li>
  <li>或者删除tmp中的整个dfs文件夹，重新格式化（tmp的位置在core-site.xml中有配置）</li></ol>
</blockquote></li>
<li><p>当jps能看到3个进程，但是本地浏览器不能访问50070端口，输入以下命令：</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso"><span class="hljs-comment">// 开放对应的防火墙端口（在普通账户下）：</span>
/sbin/iptables <span class="hljs-attribute">-I</span> INPUT <span class="hljs-attribute">-p</span> tcp <span class="hljs-subst">--</span>dport <span class="hljs-number">50070</span> <span class="hljs-attribute">-j</span> ACCEPT  <span class="hljs-comment">// 开放50070端口</span>
/etc/rc<span class="hljs-built_in">.</span>d/init<span class="hljs-built_in">.</span>d/iptables save  <span class="hljs-comment">// 存储防火墙规则</span>
/etc/init<span class="hljs-built_in">.</span>d/iptables restart  <span class="hljs-comment">// 重启防火墙</span>
/etc/init<span class="hljs-built_in">.</span>d/iptables status  <span class="hljs-comment">// 查看开放的端口=service iptables status</span></code></pre>



<pre class="prettyprint"><code class=" hljs livecodeserver">// 或者关闭防火墙（一定要在root权限下）：<span class="hljs-comment">
// 1） 禁止开机启动防火墙</span>
开启： chkconfig iptables <span class="hljs-command"><span class="hljs-keyword">on</span></span>
关闭： chkconfig iptables off<span class="hljs-comment">
// 2） 关闭防火墙，即时生效，重启后复原</span>
开启： service iptables start
关闭： service iptables stop</code></pre>

<h1 id="6停止hdfs">6.停止HDFS</h1>



<pre class="prettyprint"><code class=" hljs vbnet"><span class="hljs-keyword">stop</span>-dfs.sh </code></pre>



<h1 id="7hdfs-shell的常用命令">7.HDFS shell的常用命令</h1>

<ul>
<li>所有命令均用hadoop fs / hdfs dfs 开头</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso">  ● 查看文件系统根目录（<span class="hljs-subst">/</span>）：hadoop fs <span class="hljs-attribute">-ls</span> <span class="hljs-subst">/</span>
  ● 把一个文件上传到文件系统的根目录：hadoop fs <span class="hljs-attribute">-put</span> hello<span class="hljs-built_in">.</span>txt <span class="hljs-subst">/</span>
  ● 再查看就有了：hadoop fs <span class="hljs-attribute">-ls</span> <span class="hljs-subst">/</span>
  ● 和linux的cat一样的功能：hadoop fs <span class="hljs-attribute">-text</span> /hello<span class="hljs-built_in">.</span>txt
  ● 查看内容（<span class="hljs-subst">=-</span>text）：hadoop fs <span class="hljs-attribute">-cat</span> /test/a/b/h<span class="hljs-built_in">.</span>txt
  ● 创建文件夹：hadoop fs <span class="hljs-attribute">-mkdir</span> /test
  ● 递归地创建文件夹加<span class="hljs-attribute">-p</span>参数：hadoop fs <span class="hljs-attribute">-mkdir</span> <span class="hljs-attribute">-p</span> /test/a/b
  ● 递归地查看文件：hadoop fs <span class="hljs-attribute">-ls</span> <span class="hljs-attribute">-R</span> <span class="hljs-subst">/</span>
  ● 拷贝文件：hadoop fs <span class="hljs-attribute">-copyFromLocal</span> hello<span class="hljs-built_in">.</span>txt /test/a/b/h<span class="hljs-built_in">.</span>txt
  ● 从文件系统中获取文件：hadoop fs <span class="hljs-attribute">-get</span> /test/a/b/h<span class="hljs-built_in">.</span>txt
  ● 删除文件：hadoop fs <span class="hljs-attribute">-rm</span> /hello<span class="hljs-built_in">.</span>txt
  ● 删除文件夹：hadoop fs <span class="hljs-attribute">-rm</span> <span class="hljs-attribute">-R</span> /test
  ● 检查端口号：hadoop fs <span class="hljs-attribute">-ls</span> hdfs:<span class="hljs-comment">//chao:8020/</span></code></pre>



<h1 id="8java-api操作hdfs文件">8.Java API操作HDFS文件</h1>

<ul>
<li>开发工具：IDEA</li>
<li>查看方法提示：ctrl+j</li>
<li><p>进入方法：command+单击</p></li>
<li><p>log4j报错：</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm"><span class="hljs-label">log4j:</span>WARN No appenders could be found for logger (org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.hadoop</span><span class="hljs-preprocessor">.metrics</span>2<span class="hljs-preprocessor">.lib</span><span class="hljs-preprocessor">.MutableMetricsFactory</span>).
<span class="hljs-label">log4j:</span>WARN Please initialize the log4j system properly.
<span class="hljs-label">log4j:</span>WARN See http://logging<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.org</span>/log4j/<span class="hljs-number">1.2</span>/faq<span class="hljs-preprocessor">.html</span><span class="hljs-preprocessor">#noconfig for more info.</span></code></pre>

<ul>
<li>原因是没有对log4j这个jar进行文件配置。解决办法：在项目的/hadooptest/target/classes路径下创建log4j.properties文件</li>
<li>添加下面的代码：</li>
</ul>



<pre class="prettyprint"><code class=" hljs avrasm">log4j<span class="hljs-preprocessor">.rootLogger</span>=WARN, stdout
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.ConsoleAppender</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.layout</span>=org<span class="hljs-preprocessor">.apache</span><span class="hljs-preprocessor">.log</span>4j<span class="hljs-preprocessor">.PatternLayout</span>
log4j<span class="hljs-preprocessor">.appender</span><span class="hljs-preprocessor">.stdout</span><span class="hljs-preprocessor">.layout</span><span class="hljs-preprocessor">.ConversionPattern</span>=%d %p [%c] - %m%n</code></pre>

<ul>
<li><p>源码地址：<a href="git@github.com:chao2015/HadoopConfig.git" rel="nofollow">git@github.com:chao2015/HadoopConfig.git</a></p></li>
<li><p>测试运行：src/java/com/chenchao/hadoop/hdfs/HDFSApp的listFiles()方法，结果：</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs oxygene">HDFSApp.setUp
<span class="hljs-number">2018</span>-<span class="hljs-number">01</span>-<span class="hljs-number">10</span> <span class="hljs-number">15</span>:<span class="hljs-number">42</span>:<span class="hljs-number">41</span>,<span class="hljs-number">098</span> WARN [org.apache.hadoop.util.NativeCodeLoader] - Unable <span class="hljs-keyword">to</span> load native-hadoop <span class="hljs-keyword">library</span> <span class="hljs-keyword">for</span> your <span class="hljs-keyword">platform</span>... <span class="hljs-keyword">using</span> builtin-java classes <span class="hljs-keyword">where</span> applicable
文件夹 <span class="hljs-number">0</span>   <span class="hljs-number">0</span>   hdfs:<span class="hljs-comment">//192.168.27.131:8020/hdfsapi</span>
文件      <span class="hljs-number">1</span>   <span class="hljs-number">49</span>  hdfs:<span class="hljs-comment">//192.168.27.131:8020/hello.txt</span>
文件  <span class="hljs-number">3</span>   <span class="hljs-number">12</span>  hdfs:<span class="hljs-comment">//192.168.27.131:8020/hdfsapi/test/a.txt</span>
文件  <span class="hljs-number">3</span>   <span class="hljs-number">0</span>   hdfs:<span class="hljs-comment">//192.168.27.131:8020/hdfsapi/test/b.txt</span>
HDFSApp.tearDown</code></pre>

<p></p><ul> <br>
<li>问题：我们已经在hdfs-site.xml中设置了副本系数为1，为什么查询到有的文件看到的3呢？ <br></li></ul><p></p>

<blockquote>
  <ol>
  <li>如果你是通过hdfs shell的方式put的上去的那么，才采用默认的副本系数1； <br></li>
  <li>如果我们是java api上传上去的，在本地我们并没有手工设置副本系数，所以否则采用的是hadoop自己的副本系数。</li>
  
  </ol>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
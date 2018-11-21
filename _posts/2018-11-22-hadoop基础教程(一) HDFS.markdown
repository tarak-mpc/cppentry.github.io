---
layout:     post
title:      hadoop基础教程(一) HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/LuoZheng4698729/article/details/78894379				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p><strong>1.这是hadoop基础系列教程，适合入门者学习。</strong></p>

<p><strong>2.HDFS单机环境搭建</strong></p>

<ul>
<li><p>hadoop core下载：<a href="http://archive.apache.org/dist/hadoop/core/stable/" rel="nofollow" target="_blank">http://archive.apache.org/dist/hadoop/core/stable/</a></p></li>
<li><p>下载解压 到/usr/local/hadoop</p></li>
<li><p>将 hadoop/bin 添加到环境变量 /etc/profile</p></li>
<li><p>修改配置文件: hadoop-2.7.3/etc/hadoop/hadoop-env.sh <br>
export JAVA_HOME=/home/java8  ##改成你自己的java路径</p></li>
<li><p>修改配置文件: hadoop-2.7.3/etc/hadoop/core-site.xml</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-comment">&lt;!--指定 HDFS的 NameNode地址--&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://hadluo:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

        <span class="hljs-comment">&lt;!--指定hadoop运行时产生文件的存放目录 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/usr/local/hadhoop/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span> 

<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p>这个 hadluo为我的主机名，在hosts里面配置，下面是我的hosts文件内容:</p>

<p>127.0.0.1   <strong>hadluo</strong> localhost.localdomain localhost4 localhost4.localdomain4 <br>
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6</p>

<ul>
<li>修改配置文件: hadoop-2.7.3/etc/hadoop/hdfs-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-comment">&lt;!--指定hdfs DataNode 保存数据副本的数量 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<ul>
<li>修改配置文件: hadoop-2.7.3/etc/hadoop/mapred-site.xml <br>
先将mapred-site.xml.template 文件 改名 成mapred-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-comment">&lt;!--告诉hadoop 以后MR运行在 yarn上 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<ul>
<li>修改配置文件: hadoop-2.7.3/etc/hadoop/yarn-site.xml</li>
</ul>



<pre class="prettyprint"><code class=" hljs xml"><span class="hljs-tag">&lt;<span class="hljs-title">configuration</span>&gt;</span>
        <span class="hljs-comment">&lt;!--NodeManager获取数据的方式是 shuffle方式 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-comment">&lt;!-- 指定yarn的老大（ResourceManager）的地址 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
                <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hadluo<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
<span class="hljs-tag">&lt;/<span class="hljs-title">configuration</span>&gt;</span></code></pre>

<p><strong>3.hdfs的使用</strong></p>

<ul>
<li><p>先初始化HDFS(格式化hadoop文件系统) <br>
hdfs namenode -format  </p></li>
<li><p>启动hdfs , 会输4次root密码 <br>
hadoop-2.7.3/sbin/start-all.sh</p></li>
<li><p>jps检测是否启动成功</p></li>
</ul>



<pre class="prettyprint"><code class=" hljs ">5218 DataNode
5826 NodeManager
5540 ResourceManager
5864 Jps
5389 SecondaryNameNode
5087 NameNode</code></pre>

<p><strong>4.hdfs命令</strong></p>

<p><strong>1.查看 ls</strong> <br>
hadoop fs -ls hdfs://</p>

<p><strong>2.上传 put</strong> <br>
hadoop fs -put /home/scala_test/fun.scala hdfs://hadluo:9000/fun.scala</p>

<p><strong>3.下载 get</strong> <br>
hadoop fs -get hdfs://hadluo:9000/11.mp4 /home/11.mp4</p>

<p><strong>4.删除 rm</strong> <br>
hadoop fs -rm &lt; hdfs file &gt; … <br>
hadoop fs -rm -r &lt; hdfs dir&gt;…</p>

<p><strong>5.mkdir 命令</strong></p>

<p>hadoop fs -mkdir &lt; hdfs path&gt; <br>
只能一级一级的建目录，父目录不存在的话使用这个命令会报错</p>

<p>hadoop fs -mkdir -p &lt; hdfs path&gt;  <br>
所创建的目录如果父目录不存在就创建该父目录</p>

<p><strong>6.getmerge 命令</strong></p>

<p>hadoop fs -getmerge &lt; hdfs dir &gt;  &lt; local file &gt; <br>
将hdfs指定目录下所有文件排序后合并到local指定的文件中，文件不存在时会自动创建，文件存在时会覆盖里面的内容</p>

<p>hadoop fs -getmerge -nl  &lt; hdfs dir &gt;  &lt; local file &gt; <br>
加上nl后，合并到local file中的hdfs文件之间会空出一行</p>

<p><strong>7.cp 命令</strong></p>

<p>hadoop fs -cp  &lt; hdfs file &gt;  &lt; hdfs file &gt; <br>
目标文件不能存在，否则命令不能执行，相当于给文件重命名并保存，源文件还存在</p>

<p>hadoop fs -cp &lt; hdfs file or dir &gt;… &lt; hdfs dir &gt; <br>
目标文件夹要存在，否则命令不能执行</p>

<p><strong>8.mv 命令</strong></p>

<p>hadoop fs -mv &lt; hdfs file &gt;  &lt; hdfs file &gt; <br>
目标文件不能存在，否则命令不能执行，相当于给文件重命名并保存，源文件不存在</p>

<p>hadoop fs -mv  &lt; hdfs file or dir &gt;…  &lt; hdfs dir &gt; <br>
源路径有多个时，目标路径必须为目录，且必须存在。  <br>
注意：跨文件系统的移动（local到hdfs或者反过来）都是不允许的</p>

<p><strong>9.count 命令</strong></p>

<p>hadoop fs -count &lt; hdfs path &gt; <br>
统计hdfs对应路径下的目录个数，文件个数，文件总计大小  <br>
显示为目录个数，文件个数，文件总计大小，输入路径</p>

<p><strong>10.du 命令</strong></p>

<p>hadoop fs -du &lt; hdsf path&gt;  <br>
显示hdfs对应路径下每个文件夹和文件的大小</p>

<p>hadoop fs -du -s &lt; hdsf path&gt;  <br>
显示hdfs对应路径下所有文件和的大小</p>

<p>hadoop fs -du - h &lt; hdsf path&gt;  <br>
显示hdfs对应路径下每个文件夹和文件的大小,文件的大小用方便阅读的形式表示，例如用64M代替67108864</p>

<p><strong>11.text 命令</strong></p>

<p>hadoop fs -text &lt; hdsf file&gt; <br>
将文本文件或某些格式的非文本文件通过文本格式输出</p>

<p><strong>12.setrep 命令</strong></p>

<p>hadoop fs -setrep -R 3 &lt; hdfs path &gt; <br>
改变一个文件在hdfs中的副本个数，上述命令中数字3为所设置的副本个数，-R选项可以对一个人目录下的所有目录+文件递归执行改变副本个数的操作</p>

<p><strong>13.stat 命令</strong></p>

<p>hdoop fs -stat [format] &lt; hdfs path &gt; <br>
返回对应路径的状态信息</p>

<p>[format]可选参数有：%b（文件大小），%o（Block大小），%n（文件名），%r（副本个数），%y（最后一次修改日期和时间）</p>

<p>可以这样书写hadoop fs -stat %b%o%n &lt; hdfs path &gt;，不过不建议，这样每个字符输出的结果不是太容易分清楚</p>

<p><strong>14.tail 命令</strong></p>

<p>hadoop fs -tail &lt; hdfs file &gt; <br>
在标准输出中显示文件末尾的1KB数据</p>

<p><strong>15.archive 命令</strong></p>

<p>hadoop archive -archiveName name.har -p &lt; hdfs parent dir &gt; &lt; src &gt;* &lt; hdfs dst &gt; <br>
命令中参数name：压缩文件名，自己任意取；  <br>
&lt; hdfs parent dir &gt; ：压缩文件所在的父目录；  <br>
&lt; src &gt;：要压缩的文件名；  <br>
&lt; hdfs dst &gt;：压缩文件存放路径</p>

<p>*示例：hadoop archive -archiveName hadoop.har -p /user 1.txt 2.txt /des  <br>
示例中将hdfs中/user目录下的文件1.txt，2.txt压缩成一个名叫hadoop.har的文件存放在hdfs中/des目录下，如果1.txt，2.txt不写就是将/user目录下所有的目录和文件压缩成一个名叫hadoop.har的文件存放在hdfs中/des目录下 <br>
显示har的内容可以用如下命令：</p>

<p>hadoop fs -ls /des/hadoop.jar <br>
显示har压缩的是那些文件可以用如下命令</p>

<p>hadoop fs -ls -R har:///des/hadoop.har <br>
注意：har文件不能进行二次压缩。如果想给.har加文件，只能找到原来的文件，重新创建一个。har文件中原来文件的数据并没有变化，har文件真正的作用是减少NameNode和DataNode过多的空间浪费。</p>

<p><strong>16.balancer 命令</strong></p>

<p>hdfs balancer <br>
如果管理员发现某些DataNode保存数据过多，某些DataNode保存数据相对较少，可以使用上述命令手动启动内部的均衡过程</p>

<p>dfsadmin 命令</p>

<p>hdfs dfsadmin -help <br>
管理员可以通过dfsadmin管理HDFS，用法可以通过上述命令查看</p>

<p>hdfs dfsadmin -report <br>
显示文件系统的基本数据</p>

<p>hdfs dfsadmin -safemode &lt; enter | leave | get | wait &gt; <br>
enter：进入安全模式；leave：离开安全模式；get：获知是否开启安全模式；  <br>
wait：等待离开安全模式</p>

<p><strong>17.distcp 命令</strong> <br>
用来在两个HDFS之间拷贝数据</p>

<p>今天就到这里，谢谢大家观看。</p>

<p><strong>老生常谈：深圳有爱好音乐的会打鼓（吉他，键盘，贝斯等）的程序员和其它职业可以一起交流加入我们乐队一起嗨。我的QQ：657455400 表演视频实例：<a href="https://v.qq.com/x/page/f0517awx0x4.html" rel="nofollow">https://v.qq.com/x/page/f0517awx0x4.html</a></strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
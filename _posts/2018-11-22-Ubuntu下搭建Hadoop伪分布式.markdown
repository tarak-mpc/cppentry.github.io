---
layout:     post
title:      Ubuntu下搭建Hadoop伪分布式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<div id="article_content" class="article_content tracking-ad">

<h2 id="一在ubuntu下创建hadoop组和hadoop用户"><a target="_blank"></a>
<span>一、在</span>Ubuntu<span>下创建</span>hadoop<span>组和</span>hadoop<span>用户</span></h2>
<p>
    增加<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" target="_blank">Hadoop</a>用户组，同时在该组里增加<a href="http://lib.csdn.net/base/hadoop" rel="nofollow" class="replace_word" title="Hadoop知识库" target="_blank">hadoop</a>用户<span>，</span>后续在涉及到hadoop操作时，我们使用<span>该</span>用户<span>。</span></p>
<p>
 </p>
<h3 id="1创建hadoop用户组"><a target="_blank"></a>
<span>1、创建</span>hadoop<span>用户组</span></h3>
<p>
 </p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325537236804.png">        </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<h3 id="2创建hadoop用户"><a target="_blank"></a>
    2<span>、创建</span>hadoop<span>用户</span></h3>
<p>
    </p>
<p>
    sudo adduser -ingroup hadoop hadoop</p>
<p>
    回车后会提示输入新的UNIX密码，这是新建用户hadoop的密码<span>，</span>输入回车即可。</p>
<p>
    如果不输入密码，回车后会重新提示输入密码，即密码不能为空。</p>
<p>
    最后确认信息是否正确，如果没问题，输入 Y，回车即可。</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325547071504.png">    </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<h3 id="3为hadoop用户添加权限"><a target="_blank"></a>
    3<span>、为</span>hadoop<span>用户添加权限</span></h3>
<p>
 </p>
<p>
     输入：sudo gedit /etc/sudoers</p>
<p>
     回车，打开sudoers文件</p>
<p>
     给hadoop用户赋予和root用户同样的权限</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325553952348.png"></p>
<p>
 </p>
<p>
 </p>
<p>
    <img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325561927991.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<h2 id="二用新增加的hadoop用户登录ubuntu系统"><a target="_blank"></a>
二、用新增加的hadoop用户登录Ubuntu系统</h2>
<p>
 </p>
<h2 id="三安装ssh"><a target="_blank"></a>
三、安装ssh</h2>
<p>
sudo apt-get install openssh-server</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325569737863.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
安装完成后，启动服务</p>
<p>
sudo /etc/init.d/ssh start</p>
<p>
 </p>
<p>
查看服务是否正确启动：ps -e | grep ssh</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325580364821.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
设置免密码登录，生成私钥和公钥</p>
<p>
ssh-keygen -t rsa -P “”</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325587709679.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
此时会在／home／hadoop/.ssh下生成两个文件：id_rsa和id_rsa.pub，前者为私钥，后者为公钥。</p>
<p>
下面我们将公钥追加到authorized_keys中，它用户保存所有允许以当前用户身份登录到ssh客户端用户的公钥内容。</p>
<p>
cat ~/.ssh/id_rsa.pub &gt;&gt; ~/.ssh/authorized_keys</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325593481023.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
登录ssh</p>
<p>
ssh localhost</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181325598014095.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
      </p>
<p>
退出</p>
<p>
exit</p>
<p>
 </p>
<h2 id="四安装java环境"><a target="_blank"></a>
四、安装Java环境</h2>
<p>
提前注意：如果Ubuntu16版本可能提示找不到openjdk软件目录引用，因为软件仓库没有自带这个包，需要手动添加到仓库。如下：
Ubuntu16.04的安装源已经默认没有openjdk7了，所以要自己手动添加仓库，如下：

sudo add-apt-repository ppa:openjdk-r/ppa  
sudo apt-get update   
sudo apt-get install openjdk-7-jdk，如果是其他版本，则直接运行：
sudo apt-get install openjdk-7-jdk</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326008641052.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
查看安装结果，输入命令：<a href="http://lib.csdn.net/base/java" rel="nofollow" class="replace_word" title="Java 知识库" target="_blank">Java</a> -version，结果如下表示安装成功。</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326019736724.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<h2 id="五安装hadoop240"><a target="_blank"></a>
<span>五、安装</span>hadoop2.4.0</h2>
<h3 id="1官网下载httpmirrorbiteducnapachehadoopcommon"><a target="_blank"></a>
    1<span>、官网下载</span><span>http://mirror.bit.edu.cn/apache/hadoop/common/</span></h3>
<p>
 </p>
<h3 id="2安装"><a target="_blank"></a>
    2<span>、安装</span></h3>
<p>
 </p>
<p>
        解压</p>
<p>
        sudo tar xzf hadoop-2.4.0.tar.gz        </p>
<p>
        假如我们要把hadoop安装到/usr/local下</p>
<p>
        拷贝到/usr/local/下，文件夹为hadoop</p>
<p>
        sudo mv hadoop-2.4.0 /usr/local/hadoop        </p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326023644310.png"></p>
<p>
        </p>
<p>
 </p>
<p>
赋予用户对该文件夹的读写权限</p>
<p>
        sudo chmod 774 /usr/local/hadoop</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326027238353.png"></p>
<p>
    </p>
<p>
 </p>
<h3 id="3配置"><a target="_blank"></a>
3、配置</h3>
<p>
      </p>
<p>
        1）<span>配置</span>~/.bashrc</p>
<p>
        </p>
<p>
<span>配置</span>该文件前需要知道Java的安装路径，用来设置JAVA_HOME环境变量，可以使用下面命令行查看安装路径</p>
<p>
        update-alternatives - -config java</p>
<p>
        执行结果如下：</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326030041140.png">        </p>
<p>
    </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
完整的路径为</p>
<p>
    /usr/lib/jvm/java-7-openjdk-amd64/jre/bin/java</p>
<p>
    我们只取前面的部分 /usr/lib/jvm/java-7-openjdk-amd64</p>
<p>
    配置.bashrc文件</p>
<p>
    sudo gedit ~/.bashrc</p>
<p>
    </p>
<p>
    该命令会打开该文件的编辑窗口，在文件末尾追加下面内容，然后保存，关闭编辑窗口。</p>
<p>




</p></div>

#HADOOP VARIABLES START
<p>
export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64</p>
<p>
export HADOOP_INSTALL=/usr/local/hadoop</p>
<p>
export PATH=<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-1-Frame" role="textbox" aria-readonly="true" style=""><nobr><span class="math" id="MathJax-Span-1" style="width: 4.536em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.629em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.869em 1000em 2.883em -0.424em); top: -2.717em; left: 0.003em;"><span class="mrow" id="MathJax-Span-2"><span class="mi" id="MathJax-Span-3" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-4" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-5" style="font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-6" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.056em;"></span></span><span class="mo" id="MathJax-Span-7" style="font-family: MathJax_Main; padding-left: 0.269em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.723em;"></span></span></span><span style="border-left: 0.003em solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-1">PATH:</script>HADOOP_INSTALL/bin</p>
<p>
export PATH=<span class="MathJax_Preview"></span><span class="MathJax" id="MathJax-Element-2-Frame" role="textbox" aria-readonly="true" style=""><nobr><span class="math" id="MathJax-Span-8" style="width: 4.536em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.629em; height: 0px; font-size: 125%;"><span style="position: absolute; clip: rect(1.869em 1000em 2.883em -0.424em); top: -2.717em; left: 0.003em;"><span class="mrow" id="MathJax-Span-9"><span class="mi" id="MathJax-Span-10" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-11" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-12" style="font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.109em;"></span></span><span class="mi" id="MathJax-Span-13" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.056em;"></span></span><span class="mo" id="MathJax-Span-14" style="font-family: MathJax_Main; padding-left: 0.269em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.723em;"></span></span></span><span style="border-left: 0.003em solid; display: inline-block; overflow: hidden; width: 0px; height: 1.003em; vertical-align: -0.063em;"></span></span></nobr></span><script type="math/tex" id="MathJax-Element-2">PATH:</script>HADOOP_INSTALL/sbin</p>
<p>
export HADOOP_MAPRED_HOME=$HADOOP_INSTALL</p>
<p>
export HADOOP_COMMON_HOME=$HADOOP_INSTALL</p>
<p>
export HADOOP_HDFS_HOME=$HADOOP_INSTALL</p>
<p>
export YARN_HOME=$HADOOP_INSTALL</p>
<p>
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_INSTALL/lib/native</p>
<p>
export HADOOP_OPTS=”-Djava.library.path=$HADOOP_INSTALL/lib”</p>
<p>




</p>

#HADOOP VARIABLES END
<p>
 </p>
<p>
 </p>
<p>
最终结果如下图：</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326035825782.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
执行下面命，使添加的环境变量生效：</p>
<p>
        source ~/.bashrc</p>
<p>
2）编辑/usr/local/hadoop/etc/hadoop/hadoop-env.sh</p>
<p>
 </p>
<p>
        执行下面命令<span>，</span>打开该文件的编辑窗口</p>
<p>
        sudo gedit /usr/local/hadoop/etc/hadoop/hadoop-env.sh</p>
<p>
找到JAVA_HOME变量，修改此变量如下</p>
<p>
        export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64    </p>
<p>
        修改后的hadoop-env.sh文件如下所示<span>：</span></p>
<p>
    <img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326041767598.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
接下来就是hadoop2.x的几个核心配置文件，这里chidianwei-virtualbox是我的计算机名称，大家可以改为自己的即可。
具体如下：





</p>

<pre class="prettyprint"><code class=" hljs xml">  core-site.xml配置：       <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>fs.defaultFS<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>hdfs://chidianwei-VirtualBox:9000<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-comment">&lt;!-- 指定hadoop运行时产生文件的存储目录 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>hadoop.tmp.dir<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>/home/hadoop/hadoop/tmp<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
           <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

  hdfs-site.xml配置：

  <span class="hljs-comment">&lt;!-- 指定HDFS副本的数量 --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>dfs.replication<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>

  mapred-site.xml配置 (mv mapred-site.xml.template mapred-site.xml)
        mv mapred-site.xml.template mapred-site.xml
        vim mapred-site.xml
        <span class="hljs-comment">&lt;!-- 指定mr运行在yarn上 --&gt;</span>

 yarn-site.xml配置
        <span class="hljs-comment">&lt;!-- 指定YARN的老大（ResourceManager）的地址 --&gt;</span>
     <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.resourcemanager.hostname<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>chidianwei-VirtualBox<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
    <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
        <span class="hljs-comment">&lt;!-- reducer获取数据的方式 --&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>yarn.nodemanager.aux-services<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>mapreduce_shuffle<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span>
     <span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    <span class="hljs-tag">&lt;<span class="hljs-title">property</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">name</span>&gt;</span>mapreduce.framework.name<span class="hljs-tag">&lt;/<span class="hljs-title">name</span>&gt;</span><span class="hljs-tag">&lt;<span class="hljs-title">value</span>&gt;</span>yarn<span class="hljs-tag">&lt;/<span class="hljs-title">value</span>&gt;</span><span class="hljs-tag">&lt;/<span class="hljs-title">property</span>&gt;</span>
    slaves文件配置：
    这里需要注意，yarn的resourcemanager配置的服务器名称或者ip地址一定要和slaves对应。这里我配置的是hostname即本机名称，所以slaves需要加一行本机名称即可。如果rm是localhost则slaves文件也要是localhost，二者对应ip不一样。毕竟resourcemanager和slave机器（nodemanager）要通信。
</code></pre>



<h2 id="六wordcount测试"><a target="_blank"></a>
<span>六、</span>WordCount测试</h2>
<p>
 </p>
<p>
单机模式安装完成，下面通过执行hadoop自带实例WordCount验证是否安装成功</p>
<p>
    /usr/local/hadoop路径下创建input文件夹    </p>
<p>
mkdir input</p>
<p>
 </p>
<p>
    拷贝README.txt到input    </p>
<p>
cp README.txt input</p>
<p>
    执行WordCount</p>
<p>
    bin/hadoop jar share/hadoop/mapreduce/sources/hadoop-mapreduce-examples-2.4.0-sources.jar org.apache.hadoop.examples.WordCount input output</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326049269226.png"></p>
<p>
    </p>
<p>
 </p>
<p>
 </p>
<p>
执行结果：</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326055049570.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
执行 cat output/*，查看字符统计结果</p>
<p>
<img align="left" alt="" src="http://images.cnitblog.com/blog/12097/201406/181326062072484.png"></p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 </p>
<p>
 常见问题：</p>
<p>
问题： 50070 hadoop 打不开 50075可以打开</p>
<p>
解决: hadoop namenode -format</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
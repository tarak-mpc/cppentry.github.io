---
layout:     post
title:      Hadoop实战-初级部分 之 Hadoop安装部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span class="topictitle"><a class="blogtitle" title="Hadoop实战-初级部分 之 Hadoop安装部署" href="http://sishuok.com/forum/blogPost/list/5337.html" rel="nofollow">Hadoop实战-初级部分 之 Hadoop安装部署
</a></span></p>
<p><br></p>
<div class="div_content_text_all">
<div class="O">
<div>
<div>
<div class="O">
<div>
<div class="O">
<div><a href="http://sishuok.com/forum/blogPost/list/0/5336.html" rel="nofollow">第一部分：开始云计算之旅</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/0/5336.html" rel="nofollow">第二部分：初识Hadoop</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/5337.html" rel="nofollow">第三部分：Hadoop 环境安装部署</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/5457.html" rel="nofollow">第四部分：Hadoop Shell 基本操作介绍</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/5458.html" rel="nofollow">第五部分：Hadoop  分布式文件系统1</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/5459.html" rel="nofollow">第五部分：Hadoop  分布式文件系统2</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/5460.html" rel="nofollow">第五部分：Hadoop  分布式文件系统3</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/5456.html" rel="nofollow">第六部分：MapReduce  开发1</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/5461.html" rel="nofollow">第六部分：MapReduce  开发2</a>
</div>
<div><a href="http://sishuok.com/forum/blogPost/list/5461.html" rel="nofollow">第七部分：Hadoop集群的安装</a>
</div>
<div>  </div>
</div>
</div>
</div>
</div>
</div>
<div>
<div class="O">
<div>
<div class="O">
<div class="O">
<div class="O">
<div><span class="bold">本节课程概览</span> </div>
<div>
<div class="O">
<div>Hadoop 在windows 上伪分布式的安装过程  </div>
<div>Hadoop 在linux  上单节点伪分布式的安装过程  </div>
<div>集成Eclipse 开发环境  </div>
<div>Hadoop UI 介绍 </div>
<div>运行WordCounter  事例 </div>
<div>  </div>
<div> 
<div class="O">
<div><span class="bold">第一部分：Hadoop 在windows 上伪分布式的安装过程</span> </div>
<div>
<div class="O">安装JDK </div>
<div class="O">
<div class="O">
<div>1.下载JDK </div>
<div>       http://www.oracle.com/technetwork/java/javaee/downloads/java-ee-sdk-6u3-jdk-6u29-downloads-523388.html
</div>
<div>2.解压 </div>
<div>   或者直接运行.exe安装 </div>
<div>后面根据项目的实际需要来进行调整。 </div>
<div>
<div class="O"><span>安装</span> <span lang="en-us" xml:lang="en-us">Cygwin</span> </div>
<div class="O">
<div class="O">1.下载Cygwin </div>
<div class="O">    网址： <strong>http://www.cygwin.com/setup.exe </strong></div>
<div class="O"> 2.安装Cygwin </div>
<div class="O1">1. Net Category 下的OpenSSL </div>
<div class="O1">2.    Base Category 下的sed </div>
<div class="O1">3.Editors Category 下的vim </div>
<div class="O1">4.Devel Category  下subversion </div>
<div class="O">后面根据项目的实际需要来进行调整。 </div>
<div class="O">  </div>
</div>
<div class="O"><span>配置环境变量</span> </div>
<div class="O">
<div class="O">
<div>1.配置JAVA 环境变量 </div>
<div>    PATH 和JAVA_HOME： </div>
<div>        JAVA_HOME 指向JDK安装目录； </div>
<div>        PATH 指向JDK的bin </div>
<div> 
<div class="O"><span>启动</span> <span lang="en-us" xml:lang="en-us">SSHD</span> <span>服务</span> </div>
<div class="O">
<div class="O">
<div>1.安装SSHD </div>
<div>   ssh-host-config </div>
<div>2. 启动SSHD </div>
<div>    net start sshd  </div>
<div>
<div class="O"><span lang="en-us" xml:lang="en-us">ssh</span> <span>的安装和配置</span> </div>
<div class="O">
<div class="O">
<div>实现ssh无密码登陆 </div>
<div>$ssh-keygen -t rsa   </div>
<div>直接回车，完成后会在~/.ssh/生成两个文件：id_dsa 和id_dsa.pub。这两个是成对 </div>
<div>出现，类似钥匙和锁。再把id_dsa.pub 追加到授权key 里面(当前并没有authorized_keys <br>
$cat~/.ssh/id_rsa.pub&gt;&gt;~/.ssh/authorized_keys完成后可以实现无密码登录本机 </div>
<div>  </div>
<div>
<div class="O"><span>安装</span> <span lang="en-us" xml:lang="en-us">Hadoop</span> </div>
<div class="O">
<div class="O">1.下载Hadoop </div>
<div class="O1">   http://labs.mop.com/apache-mirror/hadoop/common/hadoop-1.0.0/ </div>
<div class="O">2.解压Hadoop </div>
<div class="O">         tar xzvf hadoop-0.21.0.tar.gz </div>
<div class="O">
<div class="O"><span>配置</span> <span lang="en-us" xml:lang="en-us">Hadoop</span> </div>
<div class="O">
<div class="O">
<div>1. 配置hadoop-env.sh </div>
<div>2 .配置conf/core-site.xml、conf/hdfs-site.xml、conf/mapred-site.xml文件 </div>
<div>
<div class="O"><span class="bold">概念</span> </div>
<div class="O">
<div class="O">
<div>HDFS: </div>
<div>      NameNode  ：管理节点 </div>
<div>      DataNode   ：数据节点 </div>
<div>      SecondaryNamenode : 数据源信息备份整理节点 </div>
<div>  </div>
<div>MapReduce </div>
<div>       JobTracker  ：任务管理节点 </div>
<div>       Tasktracker  ：任务运行节点 </div>
<div>  </div>
<div>配置文件 </div>
<div>       core-site.xml   common属性配置 </div>
<div>       hdfs-site.xml    HDFS属性配置 </div>
<div>       mapred-site.xml  MapReduce属性配置 </div>
<div>       hadoop-env.sh     hadooop 环境变量配置 </div>
<div>
<div class="O"><span>修改</span> <span lang="en-us" xml:lang="en-us">hadoop-env.sh</span> </div>
<div class="O">
<div class="O">
<div>配置JDK即可 </div>
<div>export JAVA_HOME=/cygdrive/d/java/jdk1.6.0_25 </div>
<div>
<div class="O"><span lang="en-us" xml:lang="en-us">core-site.xml</span> </div>
<div class="O">
<div class="O">
<div><br><div style="border-bottom:#aabbcc 1px solid;border-left:#aabbcc 1px solid;border-top:#aabbcc 1px solid;border-right:#aabbcc 1px solid;">
<span class="bold" style="font-family:'arial black', 'avant garde';font-size:14px;">java代码：</span>
<div class="dp-highlighter">
<div class="bar">
<div class="tools"><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">查看</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">复制到剪贴板</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">打印</a></div>
</div>
<ol class="dp-j"><li class="alt"><span><span>&lt;configuration&gt;        </span></span></li><li><span>&lt;property&gt;     </span></li><li class="alt"><span>       &lt;name&gt;fs.</span><span class="keyword">default</span><span>.name&lt;/name&gt;     </span></li><li><span>       &lt;value&gt;hdfs:</span><span class="comment">//localhost:9000&lt;/value&gt;  
</span><span>  </span></li><li class="alt"><span>&lt;/property&gt;     </span></li><li><span>&lt;property&gt;   </span></li><li class="alt"><span>       &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;   </span></li><li><span>        &lt;value&gt;/hadoop&lt;/value&gt;   </span></li><li class="alt"><span>     &lt;/property&gt;   </span></li><li><span>&lt;/configuration&gt;    </span></li></ol></div>
<pre><code class="language-java">&lt;configuration&gt;     
&lt;property&gt;  
       &lt;name&gt;fs.default.name&lt;/name&gt;  
       &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;  
&lt;/property&gt;  
&lt;property&gt;
       &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
        &lt;value&gt;/hadoop&lt;/value&gt;
     &lt;/property&gt;
&lt;/configuration&gt;  
</code></pre></div>
<br><div class="O"><span lang="en-us" xml:lang="en-us">hdfs-site.xml</span> </div>
  <br><div style="border-bottom:#aabbcc 1px solid;border-left:#aabbcc 1px solid;border-top:#aabbcc 1px solid;border-right:#aabbcc 1px solid;">
<span class="bold" style="font-family:'arial black', 'avant garde';font-size:14px;">java代码：</span>
<div class="dp-highlighter">
<div class="bar">
<div class="tools"><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">查看</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">复制到剪贴板</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">打印</a></div>
</div>
<ol class="dp-j"><li class="alt"><span><span>&lt;configuration&gt;        </span></span></li><li><span>   &lt;property&gt;     </span></li><li class="alt"><span>         &lt;name&gt;dfs.replication&lt;/name&gt;     </span></li><li><span>         &lt;value&gt;</span><span class="number"><span style="color:#c00000;">1</span></span><span>&lt;/value&gt;     </span></li><li class="alt"><span>    &lt;/property&gt;     </span></li><li><span>&lt;/configuration&gt;    </span></li></ol></div>
<pre><code class="language-java">&lt;configuration&gt;     
   &lt;property&gt;  
         &lt;name&gt;dfs.replication&lt;/name&gt;  
         &lt;value&gt;1&lt;/value&gt;  
    &lt;/property&gt;  
&lt;/configuration&gt;  </code></pre></div>
</div>
<div>
<div class="O"><span lang="en-us" xml:lang="en-us">mapred-site.xml</span> </div>
<div class="O">
<div style="border-bottom:#aabbcc 1px solid;border-left:#aabbcc 1px solid;border-top:#aabbcc 1px solid;border-right:#aabbcc 1px solid;">
<span class="bold" style="font-family:'arial black', 'avant garde';font-size:14px;">java代码：</span>
<div class="dp-highlighter">
<div class="bar">
<div class="tools"><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">查看</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">复制到剪贴板</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">打印</a></div>
</div>
<ol class="dp-j"><li class="alt"><span><span>&lt;configuration&gt;        </span></span></li><li><span>    &lt;property&gt;     </span></li><li class="alt"><span>         &lt;name&gt;mapred.job.tracker&lt;/name&gt;     </span></li><li><span>         &lt;value&gt;localhost:</span><span class="number"><span style="color:#c00000;">9001</span></span><span>&lt;/value&gt;     </span></li><li class="alt"><span>    &lt;/property&gt;     </span></li><li><span>&lt;/configuration&gt;    </span></li></ol></div>
<pre><code class="language-java">&lt;configuration&gt;     
    &lt;property&gt;  
         &lt;name&gt;mapred.job.tracker&lt;/name&gt;  
         &lt;value&gt;localhost:9001&lt;/value&gt;  
    &lt;/property&gt;  
&lt;/configuration&gt;  </code></pre></div>
<span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"><br></span></span>
<div class="O"><span>启动</span> <span lang="en-us" xml:lang="en-us">Hadoop</span> </div>
<div class="O">
<div class="O"> 1.格式化文件系统 </div>
<div class="O">      hadoop namenode –format </div>
<div class="O"> 2. 启动hadoop </div>
<div class="O1">      启动所有任务start-all.sh/stop-all.sh </div>
<div class="O1">      启动关闭HDFS: start-dfs.sh/stop-dfs.sh </div>
<div class="O1">      启动关闭MapReduce:  start-mapred.sh/stop-mapred.sh       </div>
<div class="O"> 3. 用jps命令查看进程,确保有   namenode,dataNode,JobTracker,TaskTracker </div>
<div class="O">       </div>
<div class="O">
<div class="O">
<div><span class="bold">第二部分：Hadoop 在linux  上单节点伪分布式的安装过程</span> </div>
<div>
<div class="O"><span>安装</span> <span lang="en-us" xml:lang="en-us">JDK</span> </div>
<div class="O">
<div class="O">
<div>$chmod +x jdk-6u27-linux-i586.bin </div>
<div>$./jdk-6u27-linux-i586.bin </div>
<div>安装完后设置java 环境变量如下 </div>
<div>命令 ：/home路径下 </div>
<div>$vi .bashrc </div>
<div>然后添加如下语句 </div>
<div>export JAVA_HOME=/home/test/ jdk1.6.0_27 </div>
<div>export PATH =/bin:$JAVA_HOME/bin:/usr/bin:$PATH:. </div>
<div>然后. .bashrc使其生效 </div>
<div>  </div>
<div>
<div class="O"><span lang="en-us" xml:lang="en-us">ssh</span> <span>的安装和配置</span> </div>
<div class="O">
<div class="O">
<div>$实现ssh无密码登陆 </div>
<div>$sudo apt-get install ssh </div>
<div>$ssh-keygen  </div>
<div>直接回车，完成后会在~/.ssh/生成两个文件：id_dsa 和id_dsa.pub。这两个是成对 </div>
<div>出现，类似钥匙和锁。再把id_dsa.pub 追加到授权key 里面(当前并没有authorized_keys <br>
$cat~/.ssh/id_rsa.pub&gt;&gt;~/.ssh/authorized_keys完成后可以实现无密码登录本机 </div>
<div>  </div>
<div> 
<div class="O"><span lang="en-us" xml:lang="en-us">Hadoop</span> <span>安装</span> </div>
</div>
<div>
<div class="O">1.下载Hadoop </div>
<div class="O1">   http://labs.mop.com/apache-mirror/hadoop/common/hadoop-1.0.0/ </div>
<div class="O">2.解压Hadoop </div>
<div class="O">         tar xzvf hadoop-0.21.0.tar.gz </div>
<div class="O">3. 添加Hadoop Bin到环境变量中 </div>
<div class="O">  </div>
<div class="O">
<div class="O"><span>修改</span> <span lang="en-us" xml:lang="en-us">hadoop-env.sh</span> </div>
<div class="O">
<div class="O">
<div>配置JDK即可 </div>
<div>export JAVA_HOME=/cygdrive/d/java/jdk1.6.0_25 </div>
<div>  </div>
<div> 
<div class="O"><span lang="en-us" xml:lang="en-us">conf/core-site.xml</span> </div>
<div class="O"><span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"><br></span></span>
<div style="border-bottom:#aabbcc 1px solid;border-left:#aabbcc 1px solid;border-top:#aabbcc 1px solid;border-right:#aabbcc 1px solid;">
<span class="bold" style="font-family:'arial black', 'avant garde';font-size:14px;">java代码：</span>
<div class="dp-highlighter">
<div class="bar">
<div class="tools"><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">查看</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">复制到剪贴板</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">打印</a></div>
</div>
<ol class="dp-j"><li class="alt"><span><span>&lt;configuration&gt;        </span></span></li><li><span>    &lt;property&gt;     </span></li><li class="alt"><span>          &lt;name&gt;fs.</span><span class="keyword">default</span><span>.name&lt;/name&gt;     </span></li><li><span>          &lt;value&gt;hdfs:</span><span class="comment">//localhost:9000&lt;/value&gt;  
</span><span>  </span></li><li class="alt"><span>      &lt;/property&gt;     </span></li><li><span>      &lt;property&gt;   </span></li><li class="alt"><span>           &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;   </span></li><li><span>           &lt;value&gt;/hadoop&lt;/value&gt;   </span></li><li class="alt"><span>       &lt;/property&gt;   </span></li><li><span>&lt;/configuration&gt;    </span></li></ol></div>
<pre><code class="language-java">&lt;configuration&gt;     
	&lt;property&gt;  
          &lt;name&gt;fs.default.name&lt;/name&gt;  
          &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;  
      &lt;/property&gt;  
      &lt;property&gt;
           &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
           &lt;value&gt;/hadoop&lt;/value&gt;
       &lt;/property&gt;
&lt;/configuration&gt;  </code></pre></div>
<span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"><br></span></span>
<div class="O"><span lang="en-us" xml:lang="en-us">conf/hdfs-site.xml</span> </div>
<div class="O"><span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"><br></span></span>
<div style="border-bottom:#aabbcc 1px solid;border-left:#aabbcc 1px solid;border-top:#aabbcc 1px solid;border-right:#aabbcc 1px solid;">
<span class="bold" style="font-family:'arial black', 'avant garde';font-size:14px;">java代码：</span>
<div class="dp-highlighter">
<div class="bar">
<div class="tools"><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">查看</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">复制到剪贴板</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">打印</a></div>
</div>
<ol class="dp-j"><li class="alt"><span><span>&lt;configuration&gt;        </span></span></li><li><span>   &lt;property&gt;     </span></li><li class="alt"><span>         &lt;name&gt;dfs.replication&lt;/name&gt;     </span></li><li><span>         &lt;value&gt;</span><span class="number"><span style="color:#c00000;">1</span></span><span>&lt;/value&gt;     </span></li><li class="alt"><span>    &lt;/property&gt;     </span></li><li><span>&lt;/configuration&gt;    </span></li></ol></div>
<pre><code class="language-java">&lt;configuration&gt;     
   &lt;property&gt;  
         &lt;name&gt;dfs.replication&lt;/name&gt;  
         &lt;value&gt;1&lt;/value&gt;  
    &lt;/property&gt;  
&lt;/configuration&gt;  
</code></pre></div>
<span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"><br></span></span>
<div class="O"><span lang="en-us" xml:lang="en-us">conf/mapred-site.xml</span> </div>
<div class="O"><span lang="en-us" xml:lang="en-us"><span lang="en-us" xml:lang="en-us"><br></span></span>
<div style="border-bottom:#aabbcc 1px solid;border-left:#aabbcc 1px solid;border-top:#aabbcc 1px solid;border-right:#aabbcc 1px solid;">
<span class="bold" style="font-family:'arial black', 'avant garde';font-size:14px;">java代码：</span>
<div class="dp-highlighter">
<div class="bar">
<div class="tools"><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">查看</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">复制到剪贴板</a><a href="http://sishuok.com/forum/blogPost/list/5337.html#" rel="nofollow">打印</a></div>
</div>
<ol class="dp-j"><li class="alt"><span><span>&lt;configuration&gt;        </span></span></li><li><span>     &lt;property&gt;     </span></li><li class="alt"><span>         &lt;name&gt;mapred.job.tracker&lt;/name&gt;     </span></li><li><span>         &lt;value&gt;localhost:</span><span class="number"><span style="color:#c00000;">9001</span></span><span>&lt;/value&gt;     </span></li><li class="alt"><span>    &lt;/property&gt;     </span></li><li><span>&lt;/configuration&gt;    </span></li></ol></div>
<pre><code class="language-java">&lt;configuration&gt;     
     &lt;property&gt;  
         &lt;name&gt;mapred.job.tracker&lt;/name&gt;  
         &lt;value&gt;localhost:9001&lt;/value&gt;  
    &lt;/property&gt;  
&lt;/configuration&gt;  </code></pre></div>
</div>
<div class="O">
<div class="O"><span>启动</span> <span lang="en-us" xml:lang="en-us">Hadoop</span> </div>
<div class="O">
<div class="O"> 1.格式化文件系统 </div>
<div class="O">      hadoop namenode –format </div>
<div class="O"> 2. 启动hadoop </div>
<div class="O1">     启动关闭所有服务 start-all.sh/stop-all.sh </div>
<div class="O1">     启动关闭HDFS: start-dfs.sh/stop-dfs.sh </div>
<div class="O1">     启动关闭MapReduce:  start-mapred.sh/stop-mapred.sh </div>
<div class="O"> 3. 用jps命令查看进程,确保有   namenode,dataNode,JobTracker,TaskTracker </div>
<div class="O">       </div>
<div class="O">
<div class="O">
<div><span class="bold">第三部分：Hadoop UI 介绍</span> </div>
<div>
<div class="O"><span lang="en-us" xml:lang="en-us">Hadoop</span> <span>重要的端口</span> </div>
<div class="O">
<div class="O">
<div>1.Job Tracker 管理界面：50030 </div>
<div>2.HDFS 管理界面 ：50070 </div>
<div>3.HDFS通信端口：9000 </div>
<div>4.MapReduce通信端口：9001 </div>
<div>
<div class="O"><span>常用访问页面</span> </div>
<div class="O">
<div class="O">
<div>1. HDFS 界面 </div>
<div>        http://hostname:50070 </div>
<div>2. MapReduce 管理界面 </div>
<div>        http://hostname:50030 </div>
<div>  </div>
<div>
<div class="O">
<div><span class="bold">第四部分：运行Word Count 示例</span> </div>
<div>
<div class="O"><span>相关步骤及命令</span> </div>
<div class="O">
<div class="O">
<div>1.先在本地磁盘上建立两个文件file1和file2 </div>
<div>      $ echo ”Hello world Bye world" &gt; &gt;~/input/file1 </div>
<div>      $ echo ”hello hadoop bye hadoop” &gt; &gt;~/input/file2 </div>
<div>2.在HDFS上建立一个input目录 </div>
<div>       $ hadoop fs -mkdir /tmp/input </div>
<div>3.将file1和file2拷贝到hdfs的input目录下 </div>
<div>       $ hadoop fs -put input/* /tmp/input </div>
<div>
<div class="O">
<div>4.检查hdfs上有没有file1,file2 </div>
<div>     $ hadoop fs -ls /tmp/input </div>
<div>5.执行wordcount </div>
<div>   hadoop jar hadoop-examples-1.0.0.jar wordcount  /tmp/input  /tmp/output </div>
<div>6.运行完成，查看结果 </div>
<div>   hadoop fs -cat /output/part-r-00000 </div>
<div>  </div>
<div> 
<div class="O">
<div><span class="bold">第五部分：集成 Eclipse 开发环境</span> </div>
<div>
<div class="O"><span>通过</span> <span lang="en-us" xml:lang="en-us">Eclipse</span> <span>编写</span> <span lang="en-us" xml:lang="en-us">
Hadoop</span> <span>程序</span> </div>
<div class="O">
<div class="O">
<div>1.导入hadoop-core.jar及hadoop/lib下所有Jar包 </div>
<div>2.加入Hadoop配置文件，包括conf/core-site.xml、conf/hdfs-site.xml、conf/mapred-site.xml,并修改相应参数。
</div>
<div>
<div class="O">
<div><span class="bold">本节课程小结</span> </div>
<div>掌握了在Windows ,Linux(ubuntu)上安装Hadoop 单节点伪分布式安装 
<div class="O">
<div>初次体验了Hadoop运行的WordCounter程序  </div>
<div>了解了Hadoop UI </div>
<div> 掌握了Hadoop 集成到 Eclipse的情况 </div>
</div>
</div>
<div>私塾在线学习网原创内容，转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/0/5337.html" rel="nofollow">
http://sishuok.com/forum/blogPost/list/0/5337.html</a>】 </div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>
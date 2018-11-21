---
layout:     post
title:      Hadoop的基本配置及HDFS的简单使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wangdatao_/article/details/74275813				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="软件存放目录">软件存放目录</h2>

<p>/home/hadoop/app    软件安装目录 <br>
            /sofWare    软件安装包</p>



<h2 id="hadoop环境搭建">Hadoop环境搭建</h2>



<h3 id="1下载hadoop">1）下载Hadoop</h3>

<pre><code>http://archive.cloudera.com/cdh5/cdh/5/
hadoop-2.6.0-cdh5.7.0.tar.gz

wget http://archive.cloudera.com/cdh5/cdh/5/hadoop-2.6.0-cdh5.7.0.tar.gz
</code></pre>



<h3 id="2安装jdk">2)安装JDK</h3>

<pre><code>下载
解压到APP目录： 
tar -zxvf jdk-7u51-linux-x64.tar.gz 
mv jdk1.7.0_51 java7

验证安装是否成功：
~/app/java7/bin    ./java -version

JDK 配置到环境变量中：
vi .bash_profile
在文件中添加： 
export: JAVA_HOME=/home/hadoop/app/java7
export: PATH=$JAVA_HOME/bin:$PATH
</code></pre>



<h3 id="3机器参数设置">3)机器参数设置</h3>

<p>1、主机名：</p>

<pre><code>hostname:hadoop01
修改机器名：
vi /etc/sysconfig/network
</code></pre>

<p><img src="https://img-blog.csdn.net/20170703140907935?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ2RhdGFvXw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>2、设置ip和hostname的映射关系</p>

<pre><code>    vi /etc/hosts
    192.168.199.200 hadoop001
    127.0.0.1 localhost
</code></pre>

<p>3、Hadoop配置文件修改：</p>

<pre><code>cd /home/hadoop/app/hadoop-2.6.0-cdh5.7.0/etc/hadoop

hadoop-env.sh
    export JAVA_HOME=/home/hadoop/app/java7

core-site.xml
     &lt;property&gt;
         &lt;name&gt;fs.defaultFS&lt;/name&gt;
         &lt;value&gt;hdfs://localhost:8020&lt;/value&gt;
     &lt;/property&gt;
     &lt;property&gt;
       &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
       &lt;value&gt;/home/hadoop/app/tmp&lt;/value&gt;
     &lt;/property&gt;
</code></pre>

<p><img src="https://img-blog.csdn.net/20170703153832528?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ2RhdGFvXw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<pre><code>hdfs-site.xml
    &lt;configuration&gt;
        &lt;property&gt;
            &lt;name&gt;dfs.replication&lt;/name&gt;
            &lt;value&gt;1&lt;/value&gt;
        &lt;/property&gt;
    &lt;/configuration&gt;
</code></pre>

<p><img src="https://img-blog.csdn.net/20170703154200001?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ2RhdGFvXw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>4、格式化HDFS <br>
    注意：这一步操作只在安装配置好后第一次执行，每次如果都格式化的话，那么HDFS上的数据就会被清空。</p>

<blockquote>
  <p>cd bin <br>
  ./hdfs namenode -format</p>
</blockquote>

<p>5、 启动HDFS</p>

<blockquote>
  <p>sbin/start-dfs.sh</p>
</blockquote>

<p><img src="https://img-blog.csdn.net/20170703192706654?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvd2FuZ2RhdGFvXw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>验证是否安装成功： <br>
① jps</p>

<blockquote>
  <p>DataNode <br>
  SecondaryNameNode <br>
  NameNode</p>
</blockquote>

<p>②浏览器 <br>
浏览器中输入：</p>

<blockquote>
  <p><a href="http://199.20.1.140:50070/dfshealth.html#tab-overview" rel="nofollow" target="_blank">http://199.20.1.140:50070/dfshealth.html#tab-overview</a></p>
</blockquote>

<p>6 停止HDFS</p>

<blockquote>
  <p>cd sbin <br>
  ./stop-dfs-sh</p>
</blockquote>



<h2 id="文件夹介绍">文件夹介绍</h2>

<ul>
<li><strong>bin</strong>：Hadoop最基本的管理脚本和使用脚本的目录，这些脚本是sbin目录下管理脚本的基础实现，用户可以直接使用这些脚本管理和使用Hadoop。</li>
<li><strong>etc</strong>：Hadoop配置文件所在的目录，包括core-site,xml、hdfs-site.xml、mapred-site.xml等从Hadoop1.0继承而来的配置文件和yarn-site.xml等Hadoop2.0新增的配置文件。</li>
<li><strong>include</strong>：对外提供的编程库头文件（具体动态库和静态库在lib目录中），这些头文件均是用C++定义的，通常用于C++程序访问HDFS或者编写MapReduce程序。</li>
<li><strong>lib</strong>：该目录包含了Hadoop对外提供的编程动态库和静态库，与include目录中的头文件结合使用。</li>
<li><strong>libexec</strong>：各个服务对用的shell配置文件所在的目录，可用于配置日志输出、启动参数（比如JVM参数）等基本信息。</li>
<li><strong>sbin</strong>：Hadoop管理脚本所在的目录，主要包含HDFS和YARN中各类服务的启动/关闭脚本。</li>
<li><strong>share</strong>：Hadoop各个模块编译后的jar包所在的目录。</li>
</ul>



<h3 id="hdfs-优点">HDFS 优点</h3>

<ol>
<li>高容错</li>
<li>审核批量处理</li>
<li>适合大数据处理</li>
<li>可构建在廉价的机器上</li>
</ol>



<h3 id="hdfs缺点">HDFS缺点</h3>

<ol>
<li>低延迟的数据访问</li>
<li>小文件储存 </li>
</ol>

<p>参考文档：<a href="http://hadoop.apache.org/docs/r1.0.4/cn/index.html" rel="nofollow" target="_blank">http://hadoop.apache.org/docs/r1.0.4/cn/index.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Hadoop - HDFS操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="hadoop-hdfs操作">Hadoop - HDFS操作</h2>

<p><img src="http://upload-images.jianshu.io/upload_images/6871092-c02f1453d98d9b9d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt="" title=""></p>

<p>原文地址：<a href="http://blogxinxiucan.sh1.newtouch.com/2017/07/17/Hadoop-HDFS%E6%93%8D%E4%BD%9C/" rel="nofollow" target="_blank">http://blogxinxiucan.sh1.newtouch.com/2017/07/17/Hadoop-HDFS操作/</a></p>



<h3 id="启动hdfs">启动HDFS</h3>

<p>最初，您必须格式化配置的<code>HDFS文件系统</code>，打开<code>namenode</code>（HDFS服务器），然后执行以下命令。</p>

<pre><code>$ hadoop namenode -format 
</code></pre>

<p>格式化HDFS后，启动分布式文件系统。以下命令将启动namenode以及数据节点作为集群。</p>

<pre><code>$ start-dfs.sh 
</code></pre>



<h3 id="列出hdfs中的文件">列出HDFS中的文件</h3>

<p>在服务器中加载信息后，我们可以使用’ls’找到目录中的文件列表，文件的状态。下面给出了可以传递给目录或文件名作为参数的ls的语法。</p>

<pre><code>$ $HADOOP_HOME/bin/hadoop fs -ls &lt;args&gt;
</code></pre>



<h3 id="将数据插入hdfs">将数据插入HDFS</h3>

<p>假设我们在本地系统中的文件名为file.txt中的数据，该文件应该保存在hdfs文件系统中。按照以下步骤在Hadoop文件系统中插入所需的文件。</p>

<p><strong>步骤1</strong> <br>
你必须创建一个输入目录。</p>

<pre><code>$ $HADOOP_HOME/bin/hadoop fs -mkdir /user/input 
</code></pre>

<p><strong>第2步</strong> <br>
使用put命令将数据文件从本地系统传输到Hadoop文件系统。</p>

<pre><code>$ $HADOOP_HOME/bin/hadoop fs -put /home/file.txt /user/input 
</code></pre>

<p><strong>步骤3</strong> <br>
您可以使用ls命令验证文件。</p>

<pre><code>$ $HADOOP_HOME/bin/hadoop fs -ls /user/input 
</code></pre>



<h3 id="从hdfs检索数据">从HDFS检索数据</h3>

<p>假设我们在HDFS中有一个名为outfile的文件。以下是从Hadoop文件系统中检索所需文件的简单演示。</p>

<p><strong>步骤1</strong> <br>
最初，使用cat命令查看HDFS中的数据。</p>

<pre><code>$ $HADOOP_HOME/bin/hadoop fs -cat /user/output/outfile 
</code></pre>

<p><strong>第2步</strong> <br>
使用get命令将文件从HDFS获取到本地文件系统。</p>

<pre><code>$ $HADOOP_HOME/bin/hadoop fs -get /user/output/ /home/hadoop_tp/ 
</code></pre>



<h3 id="关闭hdfs">关闭HDFS</h3>

<p>您可以使用以下命令关闭HDFS。</p>

<pre><code>$ stop-dfs.sh
</code></pre>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
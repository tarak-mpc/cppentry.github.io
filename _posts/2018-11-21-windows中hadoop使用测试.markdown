---
layout:     post
title:      windows中hadoop使用测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>基于</p>
<p><a href="http://blog.csdn.net/u010908524/article/details/38538893" rel="nofollow">windows编译安装hadoop</a><br></p>
<p><a href="http://blog.csdn.net/u010908524/article/details/38532777" rel="nofollow">windows 安装hadoop</a></p>
<p>两篇文章，已经在windows上安装了hadoop。</p>
<p>接下来，就试一下吧，测试的是wordcount MapReduce job（在\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.5.0.jar）。主要用到的命令是</p>
<p></p>
<pre><code class="language-plain">bin\hdfs dfs </code></pre>1.首先是运行（在cmd上运行，并且<span style="background-color:rgb(102,255,255);"><strong>以管理员身份运行</strong></span>）
<p></p>
<p></p>
<pre><code class="language-plain">sbin\start-all</code></pre>2.新建一个txt用于测试（D:\files.txt），内容自己喜欢吧，要求是都是英文单词。
<p></p>
<p>3.在HDFS上新建一个目录（'input'吧，方便记忆）。<br></p>
<pre><code class="language-plain">bin\hdfs dfs -mkdir /input</code></pre>4.把测试用的文件放进去吧（命令类似于linux的cp）
<p></p>
<p></p>
<pre><code class="language-plain">bin\hdfs dfs -copyFromLocal d:/files.txt /input</code></pre>5.检查一下吧
<p></p>
<p></p>
<pre><code class="language-plain">bin\hdfs dfs -ls /input
bin\hdfs dfs -cat /input/files.txt</code></pre>6.运行
<p></p>
<p></p>
<pre><code class="language-plain">bin\yarn jar share/hadoop/mapreduce/hadoop-mapreduce-examples-2.5.0.jar wordcount /input /output</code></pre>
<div>7.检查结果</div>
<div><pre><code class="language-plain">bin\hdfs dfs -cat /output/*</code></pre><a href="http://abhijitg:8088/cluster" rel="nofollow">http://abhijitg:8088/cluster</a><br>
8.停止hadoop</div>
<div><pre><code class="language-plain">sbin\stop-all</code></pre><br></div>
<em>reference：<a href="http://www.srccodes.com/p/article/45/run-hadoop-wordcount-mapreduce-example-windows" rel="nofollow">Run Hadoop wordcount MapReduce Example on Windows</a></em>
            </div>
                </div>
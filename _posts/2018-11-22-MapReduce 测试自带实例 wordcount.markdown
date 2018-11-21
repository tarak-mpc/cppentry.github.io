---
layout:     post
title:      MapReduce 测试自带实例 wordcount
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>Hadoop版本：2.7.6</p>

<p>Jar程序所在目录：是你解压后hadoop解压目录：</p>

<p>/usr/local/hadoop/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.7.6.jar</p>

<p> </p>

<h1>1.本地创建测试文件：</h1>

<p>mkdir /home/hadoop/data</p>

<p>touch a.txt  这里笔者创建了三个txt文件 </p>

<p>vim a.txt  修改文本添加内容</p>

<p>cat a.txtx</p>

<p><img alt="" class="has" height="298" src="https://img-blog.csdn.net/2018080310150887?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyOTQxODgx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="666"></p>

<h1>2.HDFS上：</h1>

<pre>
<code class="language-html hljs">hadoop fs -ls /  //查看hdfs上的目录
hadoop fs -mkdir /input  //创建input目录    这里是输出文件必须要有的  也就是我们源数据的存放地
hadoop fs -rm -r /output  //如果有output目录，删除     这个是把结果集输入到这里的  事先不能存在的
hadoop fs -put /home/hadoop/data/*.txt /input  //把测试文本上传到input目录上</code></pre>

<p><strong>//执行程序，WordCount</strong></p>

<p><img alt="" class="has" height="330" src="https://img-blog.csdn.net/20180803101933247?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyOTQxODgx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="953"></p>

<p>进入上图路径 输入如下命令启动：</p>

<p>hadoop jar hadoop-mapreduce-examples-2.7.6.jar wordcount /input /output</p>

<p>//     <span style="color:#f33b45;"> /input 输入目录 /output 输出目录（输出目录不能存在）</span></p>

<p> </p>

<p>命令执行过后我们可以去查看output里面的结构 </p>

<p>启动的过程中 可以去<a href="http://master:8088/cluster" rel="nofollow">http://IP:8088/cluster</a> 中查看进度  会生成一个任务的ID，上面会显示有一个MapReduce的程序正在运行</p>

<p><img alt="" class="has" height="103" src="https://img-blog.csdn.net/20180803102238762?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyOTQxODgx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="826"></p>

<p><span style="color:#f33b45;">查看我们输出的结果</span></p>

<p><img alt="" class="has" height="629" src="https://img-blog.csdn.net/20180803102531479?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzMyOTQxODgx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="919"></p>            </div>
                </div>
---
layout:     post
title:      learning-spark 学习笔记（1）：装个环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/sdoddyjm68/article/details/79478453				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>最近在学习 <br>
<img src="https://img-blog.csdn.net/20180307223957856?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2RvZGR5am02OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
Spark框架版本迭代很快，这本书上的Spark版本是1.1，对应的hadoop版本是2.4。遗憾的是Apache Spark已经不提供1.1版本的下载了，为了避免版本更迭导致的学习受阻，要尽量采用低版本的Spark和hadoop</p>

<p>Spark下载地址：<a href="http://spark.apache.org/downloads.html" rel="nofollow">Apach Spark</a>。 <br>
Spark提供java、scala及python的api接口 <br>
如果使用的是python且python的版本是3.6，那么就只能下载Spark 2.2.1对应hadoop2.6（python2.7到python3.5的可以下载Spark2.1 对应hadoop2.\4） <br>
<img src="https://img-blog.csdn.net/20180307224432926?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2RvZGR5am02OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
将压缩包解压，放在任意的文件夹，建议放在C:\Spark <br>
下载hadoop，这里我们Spark对应的hadoop版本是2.6，所以要在<a href="https://archive.apache.org/dist/hadoop/core/hadoop-2.6.0/" rel="nofollow">这里</a>选择2.6.0版本的hadoop下载 <br>
<img src="https://img-blog.csdn.net/20180307225144300?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2RvZGR5am02OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
将下载的文件解压到C:\Spark目录下（C:\Spark\hadoop-2.6.0） <br>
设置环境变量：</p>

<ol>
<li>添加SPARK_HOME，地址为C:\Spark</li>
<li>添加HADOOP_HOME，地址为%SPARK_HOME%\hadoop-2.6.0 <br>
打开命令行，输入pyspark显示以下图片即表示安装成功（无视WARNING） <br>
<img src="https://img-blog.csdn.net/20180307225432919?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2RvZGR5am02OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></li>
</ol>

<h2 id="遇到的报错">遇到的报错</h2>



<h4 id="javaioioexception-could-not-locate-executable-nullbinwinutilsexe-in-the-hadoop-binaries">java.io.IOException: Could not locate executable null\bin\winutils.exe in the Hadoop binaries.</h4>

<p>在hadoop文件夹中没有winutils.exe <br>
解决办法：</p>

<ul>
<li>在<a href="https://github.com/steveloughran/winutils" rel="nofollow">这里</a>，选择对应hadoop版本的winutils.exe，下载后解压到%HADOOP_HOME%\bin</li>
</ul>

<h4 id="the-root-scratch-dir-tmphive-on-hdfs-should-be-writable">The root scratch dir: /tmp/hive on HDFS should be writable.</h4>

<ul>
<li>在管理员权限下打开windows powershell，输入<code>%HADOOP_HOME%\bin\winutils.exe chmod 777 C:\tmp\hive</code>实际上是提升了权限</li>
</ul>

<h2 id="配置spark到jupyter-notebook">配置Spark到jupyter notebook</h2>

<p>打开%SPARK_HOME%\bin\spark <br>
在<code>PYSPARK_DRIVER_PYTHON</code>、<code>PYSPARK_DRIVER_PYTHON_OPTS</code>这两项后面修改一下 <br>
<img src="https://img-blog.csdn.net/20180307231115674?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2RvZGR5am02OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
在命令行输入pyspark，启动Spark的时候顺带会启动jupyter notebook</p>

<h4 id="如果没有启动">如果没有启动</h4>

<p>pip安装findspark<code>pip3 install findspark</code> <br>
以后每次在使用jupyter notebook的时候加上</p>



<pre class="prettyprint"><code class="language-python hljs "><span class="hljs-keyword">import</span> findspark
findspark.init()</code></pre>

<p><img src="https://img-blog.csdn.net/20180307231456408?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc2RvZGR5am02OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
即可正常使用</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      在windows上pycharm配置spark环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/j904538808/article/details/78773769				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>(1)分别从Hadoop官网和spark官网下载Hadoop和spark的安装包 <br>
Hadoop下载地址：<a href="http://www.apache.org/dyn/closer.cgi/hadoop/common" rel="nofollow" target="_blank">http://www.apache.org/dyn/closer.cgi/hadoop/common</a> <br>
spark下载地址： <br>
<a href="http://spark.apache.org/downloads.html" rel="nofollow" target="_blank">http://spark.apache.org/downloads.html</a> <br>
注意版本要兼容； <br>
（2）把Hadoop和spark的安装包解压，由于他们都是非安装软件，解压就可以了，然后设置环境变量，把路径添加到path中，D:\hadoop-2.6.0\bin；D:\spark-2.2.0-bin-hadoop2.7\bin注意用分号隔开 <br>
<img src="https://img-blog.csdn.net/20171211151959208?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvajkwNDUzODgwOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
（3）启动pyspark验证 <br>
理想情况下是这样的： <br>
<img src="https://img-blog.csdn.net/20171211152450700?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvajkwNDUzODgwOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
可是宝宝的却是这样的： <br>
<img src="https://img-blog.csdn.net/20171211152641640?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvajkwNDUzODgwOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
有个哥们说，原因是这样的：<a href="http://blog.csdn.net/helloxiaozhe/article/details/77802865" rel="nofollow" target="_blank">http://blog.csdn.net/helloxiaozhe/article/details/77802865</a> <br>
因为可以出来python的&gt;&gt;&gt;,应该就可以用，我不想关电脑，没有试。 <br>
（4）在pycharm中配置开发环境 <br>
a、打开pycharm，创建一个progect,设置run configuration <br>
<img src="https://img-blog.csdn.net/20171211155311987?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvajkwNDUzODgwOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
在环境变量中添加HADOOP_HOME,SPARK_HOME和PYTHONPATH <br>
b、安装pyspark 和py4j <br>
pyspark安装，在cmd终端中pip install pyspark或者在pycharm的setting中<img src="https://img-blog.csdn.net/20171211155940068?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvajkwNDUzODgwOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
安装的比较慢，勿骄勿躁。</p>

<p><strong>py4j</strong> <br>
Py4j可以使运行于python解释器的python程序动态的访问java虚拟机中的java对象。Java方法可以像java对象就在python解释器里一样被调用，Java collection也可以通过标准python collection方法调用。Py4j也可以使java程序回调python对象。 <br>
（5）测试程序</p>



<pre class="prettyprint"><code class=" hljs python"><span class="hljs-keyword">import</span> os
<span class="hljs-keyword">import</span> sys

<span class="hljs-comment"># Path for spark source folder</span>
os.environ[<span class="hljs-string">'SPARK_HOME'</span>]=<span class="hljs-string">"D:\spark-2.2.0-bin-hadoop2.7"</span>

<span class="hljs-comment"># Append pyspark to Python Path</span>
sys.path.append(<span class="hljs-string">"D:\spark-2.2.0-bin-hadoop2.7\python"</span>)

<span class="hljs-keyword">try</span>:
<span class="hljs-keyword">from</span> pyspark <span class="hljs-keyword">import</span> SparkContext
<span class="hljs-keyword">from</span> pyspark <span class="hljs-keyword">import</span> SparkConf

<span class="hljs-keyword">print</span> (<span class="hljs-string">"Successfully imported Spark Modules"</span>)

<span class="hljs-keyword">except</span> ImportError <span class="hljs-keyword">as</span> e:
<span class="hljs-keyword">print</span> (<span class="hljs-string">"Can not import Spark Modules"</span>, e)
sys.exit(<span class="hljs-number">1</span>)</code></pre>

<p><img src="https://img-blog.csdn.net/20171211161135103?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvajkwNDUzODgwOA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
这样，就说明环境配置好了啦~</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Spark Demo程序运行及相应问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_31422383/article/details/78901159				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h3 id="demo程序">Demo程序</h3>

<p>创建scala项目 <br>
<img src="https://img-blog.csdn.net/20171226125317601?/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
注意JDK与Scala SDK的选择 <br>
<img src="https://img-blog.csdn.net/20171226125426828?/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
从spark官网下载spark-2.2.1-bin-hadoop2.7.tgz，<a href="http://spark.apache.org/downloads.html" rel="nofollow">链接</a> <br>
解压之后，在Libraries中添加 <br>
<img src="https://img-blog.csdn.net/20171226130332388?/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<a href="http://spark.apache.org/examples.html" rel="nofollow">Spark的例子 Pi Estimation</a> <br>
完整版本代码如下</p>

<pre class="prettyprint"><code class=" hljs scala"><span class="hljs-keyword">import</span> org.apache.spark.{SparkConf, SparkContext}

<span class="hljs-class"><span class="hljs-keyword">object</span> <span class="hljs-title">SparkPi</span> {</span>
  <span class="hljs-keyword">def</span> main(args:Array[String]){
    <span class="hljs-keyword">val</span> conf = <span class="hljs-keyword">new</span> SparkConf().setAppName(<span class="hljs-string">"Spark Pi"</span>).setMaster(<span class="hljs-string">"local"</span>)
    <span class="hljs-keyword">val</span> sc = <span class="hljs-keyword">new</span> SparkContext(conf)
    <span class="hljs-keyword">val</span> slices = <span class="hljs-keyword">if</span> (args.length &gt; <span class="hljs-number">0</span>) args(<span class="hljs-number">0</span>).toInt <span class="hljs-keyword">else</span> <span class="hljs-number">2</span>
    <span class="hljs-keyword">val</span> n = <span class="hljs-number">100000</span> * slices
    <span class="hljs-keyword">val</span> count = sc.parallelize(<span class="hljs-number">1</span> to n).filter{ _ =&gt;
      <span class="hljs-keyword">val</span> x = math.random * <span class="hljs-number">2</span> - <span class="hljs-number">1</span>
      <span class="hljs-keyword">val</span> y = math.random * <span class="hljs-number">2</span> - <span class="hljs-number">1</span>
      x * x + y * y &lt; <span class="hljs-number">1</span>
    }.count()
    println(<span class="hljs-string">"Pi is roughly "</span> + <span class="hljs-number">4.0</span> * count / n)
    sc.stop()
  }
}</code></pre>

<p>然后，运行程序，可能出现的问题及方法总结如下：</p>

<h3 id="问题">问题</h3>

<p><strong>Could not locate executable null\bin\winutils.exe in the Hadoop binaries.</strong> <br>
<a href="http://blog.csdn.net/lanwenbing/article/details/40783335" rel="nofollow" target="_blank">解决方法参考1</a> <br>
<a href="https://www.cnblogs.com/wuxun1997/p/6847950.html" rel="nofollow" target="_blank">解决方法参考博客2</a> <br>
<a href="https://stackoverflow.com/questions/19620642/failed-to-locate-the-winutils-binary-in-the-hadoop-binary-path" rel="nofollow" target="_blank">解决方法参考博客3</a></p>

<h4 id="问题解决方法">问题解决方法</h4>

<p>先从Hadoop官网下载hadoop-2.7.5.tar.gz，<a href="http://mirrors.hust.edu.cn/apache/hadoop/common/" rel="nofollow" target="_blank">链接</a> <br>
解压后，从<strong>参考3</strong>中Prasad D回答的</p>

<blockquote>
  <p>Download it from <strong>here</strong> and add to $HADOOP_HOME/bin</p>
</blockquote>

<p>点击下载得到hadoop-common-2.2.0-bin-master.zip <br>
解压后，将所有文件复制到Hadoop-2.7.5/bin文件夹下 <br>
<img src="https://img-blog.csdn.net/20171226131357147?/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
再根据<strong>参考2</strong>中的说法，将hadoop.dll复制到system32中， <br>
修改hadoop-2.7.5\etc\hadoop中的xml文件 <br>
然后，根据<strong>参考1</strong>，修改计算机环境变量，添加 <br>
<img src="https://img-blog.csdn.net/20171226131727777?/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20171226131740291?/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
之后可能需要重启电脑或者IDEA软件</p>

<h3 id="结果图">结果图</h3>

<p><img src="https://img-blog.csdn.net/20171226132018125?/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
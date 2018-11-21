---
layout:     post
title:      window7下在pycharm上配置pyspark 搭建spark测试环境
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/summerxiachen/article/details/79199603				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>因为在虚拟机linux上已经部署好了spark，但是每次编写好spark的python脚本程序都得在虚拟机上测试，很麻烦，所以就在本地的win7系统下，结合pycharm开发工具，搭建可本地测试运行环境。 <br>
<font color="red"> <br>
本地运行spark的python脚本程序，当然需要spark的相关环境，所以前提也要在本地win7下搭建好spark环境 <br>
</font> <br>
【步骤思路如下】</p>



<pre class="prettyprint"><code class=" hljs "> 1. 搭建本地测试的spark环境
 2. 在pycharm 开发工具中配置spark环境指向，这样脚本运行时，就能成功调用spark的相关资源包。
 3. 编写好程序，运行</code></pre>

<hr>

<p><strong>【1搭建本地测试的spark环境】</strong> <br>
为了避免版本不一致导致的问题，所以本地win下安装的spark版本和虚拟机上的spark版本一致。 <br>
spark 依赖scala scala 依赖jdk ，所以 jdk 和scala也都得配置安装。 <br>
虚拟机上的 </p>



<pre class="prettyprint"><code class=" hljs ">    jdk1.7
    scala   2.11.8
    spark-2.0.1</code></pre>

<p><img src="https://img-blog.csdn.net/20180129171957395?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VtbWVyeGlhY2hlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>

<p>在本地window上安装版本最好也一致，如下图。</p>



<h2 id="title"><img src="https://img-blog.csdn.net/20180129172240074?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VtbWVyeGlhY2hlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></h2>

<p>下载对应的window系统版本（jdk1.7的linux和window是不一样的， scala和spark在inux和window是一样的，因为jdk是为了实现系统平台无关性，所以跟系统有关，scala，spark是运行在jdk之上，已经是平台无关了）。配置jdk、scala、spark的相关环境变量。</p>

<hr>

<p><strong>【2安装python环境】</strong> </p>

<p><font color="red"> <br>
不同的spark支持python版本有所不同，可以通过 spark目录/bin/pyspark.sh文件查看版本要求 。</font></p>



<pre class="prettyprint"><code class=" hljs fsharp"><span class="hljs-keyword">if</span> hash python2<span class="hljs-number">.7</span> <span class="hljs-number">2</span>&gt;/dev/<span class="hljs-keyword">null</span>; <span class="hljs-keyword">then</span>
  # Attempt <span class="hljs-keyword">to</span> <span class="hljs-keyword">use</span> Python <span class="hljs-number">2.7</span>, <span class="hljs-keyword">if</span> installed:
  DEFAULT_PYTHON=<span class="hljs-string">"python2.7"</span>
<span class="hljs-keyword">else</span>
  DEFAULT_PYTHON=<span class="hljs-string">"python"</span>
fi</code></pre>

<p>最新版本3.6 好像也不怎么良好，python3.6环境中会报如下错误 <br>
<code>TypeError: namedtuple() missing 3 required keyword-only arguments: 'verbose', 'rename', and 'module'。</code></p>

<p>所以安装2.7或则以上的，我选择的python 3.5版本 <br>
配置好python3.5 环境变量后，查看能否正常运行。 <br>
<img src="https://img-blog.csdn.net/20180129200339934?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VtbWVyeGlhY2hlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<hr>

<p><strong>【3安装配置pycharm】</strong> <br>
下载安装好pychram（破解方法自行百度，学生的话，如果有学校邮箱的话，可以免费注册） <br>
新建testspark的python项目，编写test.py脚本如下</p>

<pre class="prettyprint"><code class=" hljs python"><span class="hljs-keyword">import</span> os
<span class="hljs-keyword">import</span> sys
<span class="hljs-keyword">import</span> re
<span class="hljs-keyword">try</span>:
    <span class="hljs-keyword">from</span> pyspark <span class="hljs-keyword">import</span> SparkContext
    <span class="hljs-keyword">from</span> pyspark <span class="hljs-keyword">import</span> SparkConf
    print(<span class="hljs-string">"Successfully imported Spark Modules"</span>)
<span class="hljs-keyword">except</span> ImportError <span class="hljs-keyword">as</span> e:
    print(<span class="hljs-string">"Can not import Spark Modules"</span>, e)</code></pre>

<p><img src="https://img-blog.csdn.net/20180129201227218?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VtbWVyeGlhY2hlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
选中tesy.py 右键选中执行【run ‘test.py’】 操作，会输出</p>

<p><code>Can not import Spark Modules No module named 'pyspark'</code></p>

<p>这就是代码中try操作未成功。加载spark的库文件失败</p>



<pre class="prettyprint"><code class=" hljs python">   <span class="hljs-keyword">from</span> pyspark <span class="hljs-keyword">import</span> SparkContext
   <span class="hljs-keyword">from</span> pyspark <span class="hljs-keyword">import</span> SparkConf</code></pre>

<p>需要配置运行尝试——spark库文件位置，配置如下：Run菜单栏——&gt;Edit Configurations——&gt;选中脚本文件tesy.py-&gt;Environment variables ——&gt;添加spark环境变量——&gt;apply 然后保存再次运行</p>

<p><img src="https://img-blog.csdn.net/20180129201948139?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VtbWVyeGlhY2hlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20180129202232330?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VtbWVyeGlhY2hlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p><img src="https://img-blog.csdn.net/20180129202720046?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VtbWVyeGlhY2hlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>



<pre class="prettyprint"><code class=" hljs tex">//本地spark目录下的python位置及py4j的位置 

PYTHONPATH=C:<span class="hljs-command">\Java</span><span class="hljs-command">\spark</span>-2.0.1-bin-hadoop2.7<span class="hljs-command">\python</span>;C:<span class="hljs-command">\Java</span><span class="hljs-command">\spark</span>-2.0.1-bin-hadoop2.7<span class="hljs-command">\python</span><span class="hljs-command">\lib</span><span class="hljs-command">\py</span>4j-0.10.3-src.zip

//spark 目录位置
SPARK_HOME=C:<span class="hljs-command">\Java</span><span class="hljs-command">\spark</span>-2.0.1-bin-hadoop2.7</code></pre>

<p>再次执行test.py</p>

<p><img src="https://img-blog.csdn.net/20180129203123357?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvc3VtbWVyeGlhY2hlbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<p>成功加载。就可以在本地测试spark代码了。 <br>
<font color="red"> <br>
注意：因为配置的环境作用对象是当前py脚本，所以新建了py文件后，需要再次增加PYTHONPATH和SPARK_HOME环境变量。 <br>
</font></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
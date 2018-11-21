---
layout:     post
title:      Linux安装Spark
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>首先需要安装JDK7+ <br>
Spark是Scala写的，如果是使用Python API 还得要安装Python2.6+ 或者Python3.4+ <br>
Spark需和Scala版本对应  假如版本不一致 后期可能会很麻烦  <br>
Spark 1.6.2 - Scala 2.10 Spark 2.0.0 - Scala 2.11</p>

<p>在Spark官网上下载自己要的Spark版本 <a href="http://spark.apache.org/downloads.html" rel="nofollow">http://spark.apache.org/downloads.html</a>  <br>
下载之后解压缩: tar -xf    spark-<strong>*-bin-hadoop</strong>.tgz(对应着你下载的版本号)</p>

<p>增加环境变量 更新profile文件:  /etc/profile  在后面加入:　 <br>
SPARK_HOME=/home/vincent/Downloads/spark  <br>
PATH=<span class="MathJax_Preview" style="color: inherit; display: none;"></span><span class="MathJax" id="MathJax-Element-86-Frame" tabindex="0" data-mathml='&lt;math xmlns="http://www.w3.org/1998/Math/MathML"&gt;&lt;mi&gt;P&lt;/mi&gt;&lt;mi&gt;A&lt;/mi&gt;&lt;mi&gt;T&lt;/mi&gt;&lt;mi&gt;H&lt;/mi&gt;&lt;mo&gt;:&lt;/mo&gt;&lt;/math&gt;' role="presentation" style="position: relative;"><nobr aria-hidden="true"><span class="math" id="MathJax-Span-625" style="width: 4.326em; display: inline-block;"><span style="display: inline-block; position: relative; width: 3.596em; height: 0px; font-size: 120%;"><span style="position: absolute; clip: rect(1.253em 1003.49em 2.294em -999.997em); top: -2.133em; left: 0em;"><span class="mrow" id="MathJax-Span-626"><span class="mi" id="MathJax-Span-627" style="font-family: MathJax_Math-italic;">P<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.107em;"></span></span><span class="mi" id="MathJax-Span-628" style="font-family: MathJax_Math-italic;">A</span><span class="mi" id="MathJax-Span-629" style="font-family: MathJax_Math-italic;">T<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.107em;"></span></span><span class="mi" id="MathJax-Span-630" style="font-family: MathJax_Math-italic;">H<span style="display: inline-block; overflow: hidden; height: 1px; width: 0.055em;"></span></span><span class="mo" id="MathJax-Span-631" style="font-family: MathJax_Main; padding-left: 0.263em;">:</span></span><span style="display: inline-block; width: 0px; height: 2.138em;"></span></span></span><span style="display: inline-block; overflow: hidden; vertical-align: -0.059em; border-left: 0px solid; width: 0px; height: 1.003em;"></span></span></nobr><span class="MJX_Assistive_MathML" role="presentation"><math xmlns="http://www.w3.org/1998/Math/MathML"><mi>P</mi><mi>A</mi><mi>T</mi><mi>H</mi><mo>:</mo></math></span></span><script type="math/tex" id="MathJax-Element-86">PATH:</script>{SPARK_HOME}/bin</p>

<p>进入spark安装位置, 然后进入spark中的 bin 文件夹</p>

<p>./spark-shell   运行scala</p>

<p>./pyspark  运行python</p>

<p>运行命令之后 会出现图形Spark 表示启动成功</p>

<p>调整日志级别控制输出的信息量 <br>
在conf目录下将log4j.properties.template 复制为  log4j.properties, 然后找到 log4j.rootCategory = INFO, console <br>
将INFO改为WARN</p>

<p>之后再启动就没有那么多日志打印了</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
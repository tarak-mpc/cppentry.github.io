---
layout:     post
title:      Spark学习笔记 --- Spark中的Shuffle
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/60955915				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">这几天在理解Spark的时候找了大量的网络文章，这里还看到几篇，在这里我做一个关于Spark的总结</span></p>
<p><span style="font-size:18px;">首先与Hadoop的Shuffle进行对比，Spark的Shuffle是根据Spark的逻辑设计的。</span></p>
<p><span style="font-size:18px;">首先看一下Hadoop的Shuffle。</span></p>
<p><br></p>
<p><img src="https://img-blog.csdn.net/20170621170458427?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">可以理解为执行Map函数与执行Reduce函数中间的核心过程叫做Shuffle。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">再来看Spark的Shuffle，</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><img src="https://img-blog.csdn.net/20170309172253268?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NTM3Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;">可以理解为也是MapTask 和 Reduce Task的过程。</span></p>
<p><span style="font-size:18px;"><br></span></p>
<p><span style="font-size:18px;"><br></span></p>
<p></p>
<ul style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:20px;"><li><span style="font-size:18px;">首先每一个Mapper会根据Reducer的数量创建出相应的bucket，bucket的数量是<span class="MathJax_Preview" style="color:rgb(136,136,136);"></span><span class="MathJax" id="MathJax-Element-1-Frame" style="display:inline;line-height:normal;word-spacing:normal;border:0px;"><span class="mrow" id="MathJax-Span-2"><span class="mi" id="MathJax-Span-3">M</span><span class="mo" id="MathJax-Span-4">×</span><span class="mi" id="MathJax-Span-5">R</span></span><span style="color:rgb(255,255,255);"></span></span>，其中<span class="MathJax_Preview" style="color:rgb(136,136,136);"></span><span class="MathJax" id="MathJax-Element-2-Frame" style="display:inline;line-height:normal;word-spacing:normal;border:0px;"><span class="mrow" id="MathJax-Span-7"><span class="mi" id="MathJax-Span-8">M</span></span><span style="color:rgb(255,255,255);"></span></span>是Map的个数，<span class="MathJax_Preview" style="color:rgb(136,136,136);"></span><span class="MathJax" id="MathJax-Element-3-Frame" style="display:inline;line-height:normal;word-spacing:normal;border:0px;"><span class="mrow" id="MathJax-Span-10"><span class="mi" id="MathJax-Span-11">R</span></span><span style="color:rgb(255,255,255);"></span></span>是Reduce的个数。</span></li><li><span style="font-size:18px;">其次Mapper产生的结果会根据设置的partition算法填充到每个bucket中去。这里的partition算法是可以自定义的，当然默认的算法是根据key哈希到不同的bucket中去。</span></li><li><span style="font-size:18px;">当Reducer启动时，它会根据自己task的id和所依赖的Mapper的id从远端或是本地的block manager中取得相应的bucket作为Reducer的输入进行处理。</span></li></ul><p></p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:20px;">
<span style="font-size:18px;"><br></span></p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:20px;">
<span style="font-size:18px;">这里的bucket是一个抽象概念，在实现中每个bucket可以对应一个文件，可以对应文件的一部分或是其他等。</span></p>
<p style="color:rgb(51,51,51);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;line-height:20px;">
<span style="font-size:18px;">接下来我们分别从<strong>shuffle write</strong>和<strong>shuffle fetch</strong>这两块来讲述一下Spark的shuffle进化史。</span></p>
<p><br></p>
            </div>
                </div>
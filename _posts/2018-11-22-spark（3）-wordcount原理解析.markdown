---
layout:     post
title:      spark（3）-wordcount原理解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/answer100answer/article/details/78720026				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);font-size:30px;">
1. WordCount Examples详解</h1>
<h2 style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);font-size:24px;">
1.1 Word Count流程示意图</h2>
<div><br></div>
<div>
<pre style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;line-height:1.42857;color:rgb(51,51,51);background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);"><code class="language-java" style="font-family:Menlo, 'Lucida Console', Consolas, monospace;color:inherit;background:transparent;"><span class="n">JavaRDD</span><span class="o" style="color:rgb(102,102,102);">&lt;</span><span class="n">String</span><span class="o" style="color:rgb(102,102,102);">&gt;</span> <span class="n">textFile</span> <span class="o" style="color:rgb(102,102,102);">=</span> <span class="n">sc</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">textFile</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"hdfs://..."</span><span class="o" style="color:rgb(102,102,102);">);</span>
<span class="n">JavaPairRDD</span><span class="o" style="color:rgb(102,102,102);">&lt;</span><span class="n">String</span><span class="o" style="color:rgb(102,102,102);">,</span> <span class="n">Integer</span><span class="o" style="color:rgb(102,102,102);">&gt;</span> <span class="n">counts</span> <span class="o" style="color:rgb(102,102,102);">=</span> <span class="n">textFile</span>
    <span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">flatMap</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="n">s</span> <span class="o" style="color:rgb(102,102,102);">-&gt;</span> <span class="n">Arrays</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">asList</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="n">s</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">split</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">" "</span><span class="o" style="color:rgb(102,102,102);">)).</span><span class="na" style="color:rgb(64,112,160);">iterator</span><span class="o" style="color:rgb(102,102,102);">())</span>
    <span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">mapToPair</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="n">word</span> <span class="o" style="color:rgb(102,102,102);">-&gt;</span> <span class="k" style="color:rgb(0,112,32);font-weight:bold;">new</span> <span class="n">Tuple2</span><span class="o" style="color:rgb(102,102,102);">&lt;&gt;(</span><span class="n">word</span><span class="o" style="color:rgb(102,102,102);">,</span> <span class="mi" style="color:rgb(64,160,112);">1</span><span class="o" style="color:rgb(102,102,102);">))</span>
    <span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">reduceByKey</span><span class="o" style="color:rgb(102,102,102);">((</span><span class="n">a</span><span class="o" style="color:rgb(102,102,102);">,</span> <span class="n">b</span><span class="o" style="color:rgb(102,102,102);">)</span> <span class="o" style="color:rgb(102,102,102);">-&gt;</span> <span class="n">a</span> <span class="o" style="color:rgb(102,102,102);">+</span> <span class="n">b</span><span class="o" style="color:rgb(102,102,102);">);</span>
<span class="n">counts</span><span class="o" style="color:rgb(102,102,102);">.</span><span class="na" style="color:rgb(64,112,160);">saveAsTextFile</span><span class="o" style="color:rgb(102,102,102);">(</span><span class="s" style="color:rgb(64,112,160);">"hdfs://..."</span><span class="o" style="color:rgb(102,102,102);">);</span></code></pre>
<br></div>
<div><br></div>
<div><img src="https://img-blog.csdn.net/20171205192307223" alt=""><br></div>
<div><img src="https://img-blog.csdn.net/20171205201340116" alt=""><br></div>
<div><span style="color:rgb(85,85,85);font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">, </span></div>
<div></div>
<br><span></span>
            </div>
                </div>
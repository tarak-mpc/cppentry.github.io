---
layout:     post
title:      wordcount spark...
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre class="prettyprint"><code class=" hljs avrasm">sc<span class="hljs-preprocessor">.textFile</span>(<span class="hljs-string">"hdfs://..."</span>)<span class="hljs-preprocessor">.flatMap</span>(line =&gt; line<span class="hljs-preprocessor">.split</span>(<span class="hljs-string">" "</span>))<span class="hljs-preprocessor">.map</span>(word =&gt; (word, <span class="hljs-number">1</span>))<span class="hljs-preprocessor">.reduceByKey</span>(_ + _)<span class="hljs-preprocessor">.saveAsTextFile</span>(<span class="hljs-string">"hdfs://..."</span>) </code></pre>

<p>.</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
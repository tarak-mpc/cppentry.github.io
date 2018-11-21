---
layout:     post
title:      用 Python 编写 Hadoop MR
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Hadoop 版本：2.7.2</p>

<p>本地测试：</p>

<pre class="prettyprint"><code class=" hljs avrasm">cat input<span class="hljs-preprocessor">.txt</span> | ./mapper<span class="hljs-preprocessor">.py</span> </code></pre>

<p>提交Hadoop：</p>

<pre class="prettyprint"><code class=" hljs haml">hadoop jar {HADOOP_HOME}/share/hadoop/tools/lib/hadoop-streaming-2.7.2.jar \
                -<span class="ruby">file map.py 
</span>                -<span class="ruby">mapper <span class="hljs-string">'python map.py'</span> \
</span>                -<span class="ruby">reducer  cat \
</span>                -<span class="ruby">input  %s\
</span>                -<span class="ruby">output %s</span></code></pre>

<p>map.py 基本结构</p>

<pre class="prettyprint"><code class=" hljs r"><span class="hljs-comment">#!/usr/bin/env python</span>
<span class="hljs-comment">#coding:utf-8</span>

import sys


<span class="hljs-keyword">for</span> line <span class="hljs-keyword">in</span> sys.stdin:

    line = line.strip()

    <span class="hljs-keyword">...</span>

    print <span class="hljs-keyword">...</span></code></pre>

<p>说明：map.py 对hdfs输入的数据按行处理后直接输出到hdfs，无reduce 部分</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
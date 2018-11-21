---
layout:     post
title:      Flume TailDirSource问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
  <p>在使用Flume的tailDirSource时，发现每天凌晨log文件名切换时会丢失日志，查看源码发现，发现log文件名修改后不符合flume file group定义的正则表达式，导致改过名的日志文件不会再被flume读到；后来改了正则表达式，适应改名后的log文件，但发现改名后的log文件会被flume当成新文件，重新读一遍，再看源码，发现tailDirSource通过inode和filePath两个维度来判断是否为新文件，log改名后，filePath不一样，就被tailDirSource视为新的文件，重新读一遍，好了，看下源码怎么修改？</p>
</blockquote>

<p>flume-taildir-source工程ReliableTaildirEventReader.java 247行</p>

<pre><code>if (tf == null || !tf.getPath().equals(f.getAbsolutePath())) {
    ...
}
</code></pre>

<p>把 <strong>!tf.getPath().equals(f.getAbsolutePath())</strong> 这个比较filePath的代码去掉即可！</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
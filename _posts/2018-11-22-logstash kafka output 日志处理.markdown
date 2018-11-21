---
layout:     post
title:      logstash kafka output 日志处理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：作者：jiankunking 出处：http://blog.csdn.net/jiankunking  本文版权归作者和CSDN共有，欢迎转载，但未经作者同意必须保留此段声明，且在文章页面明显位置给出原文连接。					https://blog.csdn.net/xunzaosiyecao/article/details/70146022				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>今天在用logstash解析日志并传送给kafka的时候，发现kafka能收到数据但数据内容已经丢失，具体如下： <br>
<img src="https://img-blog.csdn.net/20170412184016877?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmt1bmtpbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""> <br>
在logstash output中增加输出样式之后，问题解决kafka再次接受到的内容如下图所示： <br>
<img src="https://img-blog.csdn.net/20170412184322210?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvamlhbmt1bmtpbmc=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>

<pre class="prettyprint"><code class=" hljs php">output{
    kafka{
        topic_id =&gt; <span class="hljs-string">"log_test"</span>
        <span class="hljs-comment"># kafka的地址</span>
        bootstrap_servers =&gt; <span class="hljs-string">"10.10.10.10:9092"</span> 
        <span class="hljs-comment"># 一定要注明输出格式</span>
        codec =&gt; <span class="hljs-string">"json"</span>
    }
}</code></pre>

<blockquote>
  <p>作者：jiankunking 出处：<a href="http://blog.csdn.net/jiankunking" rel="nofollow">http://blog.csdn.net/jiankunking</a></p>
</blockquote>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
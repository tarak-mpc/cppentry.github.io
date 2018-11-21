---
layout:     post
title:      spark shuffle 发展变迁及源码实现
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。-- Bruce Xu					https://blog.csdn.net/xwc35047/article/details/78634613				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文主要分享关于spark shuffle优秀文章汇总，通过这几篇文章，能由浅入深学习spark shuffle过程。</p>

<p>不同计算引擎shuffle过程区别 <br>
<a href="http://www.csdn.net/article/2014-05-19/2819831-TDW-Shuffle/2" rel="nofollow" target="_blank">MR与Spark shuffle区别</a></p>

<p>发展历程了解： <br>
<a href="http://www.jianshu.com/p/4c5c2e535da5" rel="nofollow" target="_blank">spark shuffle从0.X到2.X的发展历程</a></p>

<p>shuffle write和shuffle read的理论解读： <br>
<a href="https://github.com/JerryLead/SparkInternals/blob/master/markdown/4-shuffleDetails.md" rel="nofollow" target="_blank">spark shuffle 过程理论解读</a></p>

<p>根据shuffle过程理论，剖析代码实现： <br>
<a href="http://www.jianshu.com/p/ac41682c5d16" rel="nofollow" target="_blank">spark shuffle 1.6源码解读</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
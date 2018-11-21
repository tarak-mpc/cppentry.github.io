---
layout:     post
title:      SparkStreaming窗口滑动以及拉链实现转化率模型的高阶应用-Spark商业应用实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83721583				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark商业应用实战指导，请持续关注本套博客。版权声明：本套Spark商业应用实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://juejin.im/post/5bded9d9e51d45053b5c73b4" rel="nofollow">秦凯新的技术社区-大数据商业实战系列全集目录  </a></li>
<li><a href="https://juejin.im/post/5bded7bf518825171425a23d" rel="nofollow">Spark商业应用实战-Spark聚合开窗与自定义累加器的高级应用 </a></li>
<li><a href="https://juejin.im/post/5bdef3dd51882516df02fdc4" rel="nofollow">Spark商业应用实战-Spark自定义排序规则以及SQL高级应用 </a></li>
<li><a href="https://juejin.im/post/5bdefd4a518825170b1013b3" rel="nofollow">Spark商业应用实战-SparkStreaming窗口滑动以及拉链实现转化率的高阶应用 </a></li>
</ul>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df035d09fc117?w=683&amp;h=268&amp;f=png&amp;s=18898" alt=""></p>
<h2><a id="1_SparkStreaming_9"></a>1 SparkStreaming窗口滑动</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df10b07bc32a2?w=1090&amp;h=394&amp;f=png&amp;s=143975" alt=""></p>
<h2><a id="2_Streaming_rdd_DataSet_13"></a>2 Streaming rdd 转换成DataSet</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df11a1a3ed121?w=1003&amp;h=313&amp;f=png&amp;s=115302" alt=""></p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df1266b0f498e?w=946&amp;h=541&amp;f=png&amp;s=141737" alt=""></p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df12d13ec5561?w=1122&amp;h=507&amp;f=png&amp;s=87854" alt=""></p>
<h2><a id="3_sparkKafka_23"></a>3 spark连接Kafka时参数设置</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df20412ca96e0?w=899&amp;h=720&amp;f=png&amp;s=159922" alt=""><br>
<img src="https://user-gold-cdn.xitu.io/2018/11/4/166df13ee0b1e4ef?w=894&amp;h=479&amp;f=png&amp;s=335929" alt=""></p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df148d926c5d9?w=1236&amp;h=309&amp;f=png&amp;s=191919" alt=""></p>
<h2><a id="4__31"></a>4 拉链转化操作，定义转化率：</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df162d8f1f717?w=1246&amp;h=393&amp;f=png&amp;s=194896" alt=""></p>
<h2><a id="5__36"></a>5 最后</h2>
<p>本节内容主要探讨了Spark自定义排序规则以及SQL高级应用，可能部分截图来自github公开源码，部分是我的测试案例，如有雷同某位大神私有内容，请直接留言于我，我来重新修正案例。</p>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
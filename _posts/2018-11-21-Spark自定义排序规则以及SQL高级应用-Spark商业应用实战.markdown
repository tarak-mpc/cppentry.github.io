---
layout:     post
title:      Spark自定义排序规则以及SQL高级应用-Spark商业应用实战
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本套技术专栏是作者（秦凯新）平时工作的总结和升华，通过从真实商业环境抽取案例进行总结和分享，并给出商业应用的调优建议和集群环境容量规划等内容，请持续关注本套博客。版权声明：禁止转载，欢迎学习。QQ邮箱地址：1120746959@qq.com，如有任何商业交流，可随时联系。					https://blog.csdn.net/shenshouniu/article/details/83721124				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本套系列博客从真实商业环境抽取案例进行总结和分享，并给出Spark商业应用实战指导，请持续关注本套博客。版权声明：本套Spark商业应用实战归作者（秦凯新）所有，禁止转载，欢迎学习。</p>
<ul>
<li><a href="https://juejin.im/post/5bded9d9e51d45053b5c73b4" rel="nofollow">秦凯新的技术社区-大数据商业实战系列全集目录  </a></li>
<li><a href="https://juejin.im/post/5bded7bf518825171425a23d" rel="nofollow">Spark商业应用实战-Spark聚合开窗与自定义累加器的高级应用 </a></li>
<li><a href="https://juejin.im/post/5bdef3dd51882516df02fdc4" rel="nofollow">Spark商业应用实战-Spark自定义排序规则以及SQL高级应用 </a></li>
</ul>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166df035d09fc117?w=683&amp;h=268&amp;f=png&amp;s=18898" alt=""></p>
<h2><a id="1_Spark_8"></a>1 Spark自定义排序规则</h2>
<h3><a id="11__9"></a>1.1 如何自定义排序规则：</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166def43037e01b8?w=1079&amp;h=385&amp;f=png&amp;s=75544" alt=""></p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166def493a18a657?w=1069&amp;h=405&amp;f=png&amp;s=127203" alt=""></p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166def5710f4ae72?w=1073&amp;h=373&amp;f=png&amp;s=211895" alt=""></p>
<h3><a id="12__18"></a>1.2 如何自定义排序规则：</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166def739109d3ba?w=1072&amp;h=168&amp;f=png&amp;s=79490" alt=""></p>
<h2><a id="2_Spark_SQL_Mysql_24"></a>2 Spark SQL 操作Mysql</h2>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166def8cbf239741?w=1080&amp;h=299&amp;f=png&amp;s=135819" alt=""></p>
<h2><a id="3_Spark_SQL__28"></a>3 Spark SQL 使用高级技巧</h2>
<h3><a id="31_Case_when__row_number__30"></a>3.1 Case when 和 row number 的高级用法</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166defa5f7688cb9?w=885&amp;h=357&amp;f=png&amp;s=105427" alt=""></p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166defb81c4ed918?w=1022&amp;h=453&amp;f=png&amp;s=107193" alt=""></p>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166defc60c08e235?w=979&amp;h=469&amp;f=png&amp;s=85189" alt=""></p>
<h3><a id="32_if_41"></a>3.2 内置if的高级用法</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166defddc54d0e9d?w=1021&amp;h=492&amp;f=png&amp;s=113167" alt=""></p>
<h3><a id="33_make_rdd__DF_45"></a>3.3 make rdd 和 DF的转化</h3>
<p><img src="https://user-gold-cdn.xitu.io/2018/11/4/166deff3092cf5c9?w=1018&amp;h=136&amp;f=png&amp;s=71993" alt=""></p>
<h2><a id="4__50"></a>4 总结</h2>
<p>本节内容主要探讨了Spark自定义排序规则以及SQL高级应用，可能部分截图来自github公开源码，部分是我的测试案例，如有雷同某位大神私有内容，请直接留言于我，我来重新修正案例。</p>
<p>秦凯新 于深圳</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
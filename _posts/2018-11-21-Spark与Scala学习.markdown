---
layout:     post
title:      Spark与Scala学习
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/JasonDing1354/article/details/46899317				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h2 id="spark学习">Spark学习</h2>

<ul>
<li><a href="http://jasonding1354.github.io/2015/05/28/Spark/%E3%80%90Spark%E3%80%91%E9%85%8D%E7%BD%AESpark%E6%BA%90%E7%A0%81%E9%98%85%E8%AF%BB%E7%8E%AF%E5%A2%83/" rel="nofollow">配置Spark源码阅读环境</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/07/Spark/%E3%80%90Spark%E3%80%91Spark%E7%9A%84Standalone%E6%A8%A1%E5%BC%8F%E5%AE%89%E8%A3%85%E9%83%A8%E7%BD%B2/" rel="nofollow">Spark的Standalone模式安装部署</a></li>
<li><a href="http://jasonding1354.github.io/2015/06/07/Spark/%E3%80%90Spark%E3%80%91Spark%E7%94%9F%E6%80%81%E5%92%8CSpark%E6%9E%B6%E6%9E%84/" rel="nofollow">Spark生态和Spark架构</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/07/Spark/%E3%80%90Spark%E3%80%91Spark%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5/" rel="nofollow">Spark基本概念</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/08/Spark/%E3%80%90Spark%E3%80%91%E5%BC%B9%E6%80%A7%E5%88%86%E5%B8%83%E5%BC%8F%E6%95%B0%E6%8D%AE%E9%9B%86RDD/" rel="nofollow">弹性分布式数据集RDD概述</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/08/Spark/%E3%80%90Spark%E3%80%91Spark%E5%BA%94%E7%94%A8%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6/" rel="nofollow">Spark应用执行机制</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/08/Spark/%E3%80%90Spark%E3%80%91RDD%E6%93%8D%E4%BD%9C%E8%AF%A6%E8%A7%A31%E2%80%94%E2%80%94Transformation%E5%92%8CActions%E6%A6%82%E5%86%B5/" rel="nofollow">RDD操作详解1——Transformation和Actions概况</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/10/Spark/%E3%80%90Spark%E3%80%91RDD%E6%93%8D%E4%BD%9C%E8%AF%A6%E8%A7%A32%E2%80%94%E2%80%94%E5%80%BC%E5%9E%8BTransformation%E7%AE%97%E5%AD%90/" rel="nofollow">RDD操作详解2——值型Transformation算子</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/10/Spark/%E3%80%90Spark%E3%80%91RDD%E6%93%8D%E4%BD%9C%E8%AF%A6%E8%A7%A33%E2%80%94%E2%80%94%E9%94%AE%E5%80%BC%E5%9E%8BTransformation%E7%AE%97%E5%AD%90/" rel="nofollow">RDD操作详解3——键值型Transformation算子</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/12/Spark/%E3%80%90Spark%E3%80%91RDD%E6%93%8D%E4%BD%9C%E8%AF%A6%E8%A7%A34%E2%80%94%E2%80%94Action%E7%AE%97%E5%AD%90/" rel="nofollow">RDD操作详解4——Action算子</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/14/Spark/%E3%80%90Spark%E3%80%91RDD%E6%9C%BA%E5%88%B6%E5%AE%9E%E7%8E%B0%E6%A8%A1%E5%9E%8B/" rel="nofollow">RDD机制实现模型</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/14/Spark/%E3%80%90Spark%E3%80%91Spark%E5%AE%B9%E9%94%99%E6%9C%BA%E5%88%B6/" rel="nofollow">Spark容错机制</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/14/Spark/%E3%80%90Spark%E3%80%91Spark%E7%9A%84Shuffle%E6%9C%BA%E5%88%B6/" rel="nofollow">Spark的Shuffle机制</a> <br>
<a href="" rel="nofollow"></a></li>
</ul>



<h2 id="spark深入与源码走读">Spark深入与源码走读</h2>

<ul>
<li><a href="http://jasonding1354.github.io/2015/07/12/Spark/%E3%80%90Spark%20Core%E3%80%91SparkContext%E6%BA%90%E7%A0%81%E8%A7%A3%E8%AF%BB/" rel="nofollow">SparkContext源码解读</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/14/Spark/%E3%80%90Spark%20Core%E3%80%91DAGScheduler%E6%BA%90%E7%A0%81%E6%B5%85%E6%9E%90/" rel="nofollow">DAGScheduler源码浅析</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/15/Spark/%E3%80%90Spark%20Core%E3%80%91DAGScheduler%E6%BA%90%E7%A0%81%E6%B5%85%E6%9E%902/" rel="nofollow">DAGScheduler源码浅析2</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/15/Spark/%E3%80%90Spark%20Core%E3%80%91Stage%E7%94%9F%E6%88%90%E5%92%8CStage%E6%BA%90%E7%A0%81%E6%B5%85%E6%9E%90/" rel="nofollow">Stage生成和Stage源码浅析</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/15/Spark/%E3%80%90Spark%20Core%E3%80%91TaskScheduler%E6%BA%90%E7%A0%81%E4%B8%8E%E4%BB%BB%E5%8A%A1%E6%8F%90%E4%BA%A4%E5%8E%9F%E7%90%86%E6%B5%85%E6%9E%901/" rel="nofollow">TaskScheduler源码与任务提交原理浅析1</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/16/Spark/%E3%80%90Spark%20Core%E3%80%91TaskScheduler%E6%BA%90%E7%A0%81%E4%B8%8E%E4%BB%BB%E5%8A%A1%E6%8F%90%E4%BA%A4%E5%8E%9F%E7%90%86%E6%B5%85%E6%9E%902/" rel="nofollow">TaskScheduler源码与任务提交原理浅析2</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/19/Spark/%E3%80%90Spark%20Core%E3%80%91%E4%BB%BB%E5%8A%A1%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6%E5%92%8CTask%E6%BA%90%E7%A0%81%E6%B5%85%E6%9E%901/" rel="nofollow">任务执行机制和Task源码浅析1</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/20/Spark/%E3%80%90Spark%20Core%E3%80%91%E4%BB%BB%E5%8A%A1%E6%89%A7%E8%A1%8C%E6%9C%BA%E5%88%B6%E5%92%8CTask%E6%BA%90%E7%A0%81%E6%B5%85%E6%9E%902/" rel="nofollow">任务执行机制和Task源码浅析2</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/20/Spark/%E3%80%90Spark%20Core%E3%80%91%E4%BB%8E%E4%BD%9C%E4%B8%9A%E6%8F%90%E4%BA%A4%E5%88%B0%E4%BB%BB%E5%8A%A1%E8%B0%83%E5%BA%A6%E5%AE%8C%E6%95%B4%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E6%B5%85%E6%9E%90/" rel="nofollow">从作业提交到任务调度完整生命周期浅析</a></li>
</ul>



<h2 id="scala基础">Scala基础</h2>

<ul>
<li><a href="http://jasonding1354.github.io/2015/05/26/Scala/%E3%80%90Scala%E3%80%91Scala%E5%87%BD%E6%95%B0%E5%BC%8F%E7%BC%96%E7%A8%8B%E5%88%9D%E6%8E%A2/" rel="nofollow">Scala函数式编程初探</a></li>
<li><a href="http://jasonding1354.github.io/2015/05/26/Scala/%E3%80%90Scala%E3%80%91%E5%8D%95%E4%BE%8B%E5%AF%B9%E8%B1%A1%E4%B8%8E%E4%BC%B4%E7%94%9F%E5%AF%B9%E8%B1%A1/" rel="nofollow">单例对象与伴生对象</a></li>
<li><a href="http://jasonding1354.github.io/2015/06/03/Scala/%E3%80%90Scala%E3%80%91%E5%A4%B4%E7%AD%89%E5%87%BD%E6%95%B0%E4%B8%8E%E5%87%BD%E6%95%B0%E5%8D%B3%E5%AF%B9%E8%B1%A1/" rel="nofollow">头等函数与函数即对象</a></li>
<li><a href="http://jasonding1354.github.io/2015/06/04/Scala/%E3%80%90Scala%E3%80%91%E9%AB%98%E9%98%B6%E5%87%BD%E6%95%B0%E5%92%8C%E6%9F%AF%E9%87%8C%E5%8C%96/" rel="nofollow">高阶函数和柯里化</a></li>
<li><a href="http://jasonding1354.github.io/2015/06/09/Scala/%E3%80%90Scala%E3%80%91Scala%E7%9A%84%E7%B1%BB%E5%B1%82%E7%BA%A7/" rel="nofollow">Scala的类层级</a></li>
<li><a href="http://jasonding1354.github.io/2015/06/11/Scala/%E3%80%90Scala%E3%80%91%E7%89%B9%E8%B4%A8%E4%B8%8E%E7%89%B9%E8%B4%A8%E7%9A%84%E7%BA%BF%E6%80%A7%E5%8C%96/" rel="nofollow">特质与特质的线性化</a></li>
<li><a href="http://jasonding1354.github.io/2015/06/20/Scala/%E3%80%90Scala%E3%80%91%E6%A0%B7%E6%9C%AC%E7%B1%BB%E5%92%8C%E6%A8%A1%E5%BC%8F%E5%8C%B9%E9%85%8D/" rel="nofollow">模式匹配和样本类</a></li>
<li><a href="" rel="nofollow"></a> <br>
<a href="" rel="nofollow"></a></li>
</ul>



<h2 id="scala编程实例">Scala编程实例</h2>

<ul>
<li><a href="http://jasonding1354.github.io/2015/06/22/Scala/%E3%80%90Scala%E7%BC%96%E7%A8%8B%E3%80%91%E5%87%BD%E6%95%B0%E5%BC%8F%E9%A3%8E%E6%A0%BC%E7%BC%96%E5%86%99%E6%8E%92%E5%BA%8F%E7%AE%97%E6%B3%95/" rel="nofollow">函数式风格编写排序算法</a></li>
<li><a href="http://jasonding1354.github.io/2015/06/28/Scala/%E3%80%90Scala%E7%BC%96%E7%A8%8B%E3%80%91%E6%A0%BC%E5%BC%8F%E5%8C%96%E7%AE%97%E6%9C%AF%E8%A1%A8%E8%BE%BE%E5%BC%8F%E7%A8%8B%E5%BA%8F/" rel="nofollow">格式化算术表达式程序</a></li>
<li><a href="http://jasonding1354.github.io/2016/01/14/Scala/%E3%80%90Scala%E3%80%91%E5%B0%BE%E9%80%92%E5%BD%92%E4%BC%98%E5%8C%96/" rel="nofollow">尾递归优化</a></li>
</ul>

<h2 id="scala深入探索">Scala深入探索</h2>

<ul>
<li><a href="http://jasonding1354.github.io/2015/06/30/Scala/%E3%80%90Scala%E3%80%91Scala%E7%9A%84Predef%E5%AF%B9%E8%B1%A1/" rel="nofollow">Scala的Predef对象</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/01/Scala/%E3%80%90Scala%E3%80%91Scala%E4%B8%AD%E7%9A%84Nothing%EF%BC%8CNull%EF%BC%8CNone%EF%BC%8CNil/" rel="nofollow">Scala中的Nothing，Null，None，Nil</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/06/Scala/%E3%80%90Scala%E3%80%91%E4%BD%BF%E7%94%A8OptionSomeNone%EF%BC%8C%E9%81%BF%E5%85%8D%E4%BD%BF%E7%94%A8null/" rel="nofollow">使用Option、Some、None，避免使用null</a></li>
<li><a href="http://jasonding1354.github.io/2015/07/09/Scala/%E3%80%90Scala%E3%80%91%E4%BD%BF%E7%94%A8Option%E3%80%81Either%E5%92%8CTry%E5%A4%84%E7%90%86%E6%95%B0%E6%8D%AE%E4%BA%A4%E4%BA%92/" rel="nofollow">使用Option、Either和Try处理数据交互</a> <br>
<a href="" rel="nofollow"></a></li>
</ul>



<h2 id="akka编程-1">Akka编程</h2>

<ul>
<li><a href="http://jasonding1354.github.io/2015/06/26/Scala/%E3%80%90Akka%E3%80%91Actor%E6%A8%A1%E5%9E%8B%E6%8E%A2%E7%B4%A2/" rel="nofollow">Actor模型探索</a></li>
<li><a href="http://jasonding1354.github.io/2016/01/15/Scala/%E3%80%90Akka%E3%80%91Akka%E5%85%A5%E9%97%A8%E7%BC%96%E7%A8%8B%E5%AE%9E%E4%BE%8B/" rel="nofollow">Akka入门编程实例</a></li>
<li><a href="http://jasonding1354.github.io/2016/01/16/Scala/%E3%80%90Akka%E3%80%91Actor%E5%BC%95%E7%94%A8/" rel="nofollow">Actor引用</a></li>
<li><a href="http://jasonding1354.github.io/2016/01/17/Scala/%E3%80%90Akka%E3%80%91Akka%E4%B8%ADactor%E7%9A%84%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E4%B8%8EDeathWatch%E7%9B%91%E6%8E%A7/" rel="nofollow">Akka中actor的生命周期与DeathWatch监控</a></li>
<li><a href="http://jasonding1354.github.io/2016/01/21/Scala/%E3%80%90Akka%E3%80%91%E5%9C%A8%E5%B9%B6%E5%8F%91%E7%A8%8B%E5%BA%8F%E4%B8%AD%E4%BD%BF%E7%94%A8Future/" rel="nofollow">在并发程序中使用Future</a></li>
</ul>

<p><strong>转载请注明作者Jason Ding及其出处</strong> <br>
<a href="http://jasonding1354.gitcafe.io/" rel="nofollow">GitCafe博客主页(http://jasonding1354.gitcafe.io/)</a> <br>
<a href="http://jasonding1354.github.io/" rel="nofollow">Github博客主页(http://jasonding1354.github.io/)</a> <br>
<a href="http://blog.csdn.net/jasonding1354" rel="nofollow">CSDN博客(http://blog.csdn.net/jasonding1354)</a> <br>
<a href="http://www.jianshu.com/users/2bd9b48f6ea8/latest_articles" rel="nofollow">简书主页(http://www.jianshu.com/users/2bd9b48f6ea8/latest_articles)</a> <br>
<strong>百度搜索jasonding1354进入我的博客主页</strong></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
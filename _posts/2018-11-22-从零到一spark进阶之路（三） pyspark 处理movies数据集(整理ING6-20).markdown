---
layout:     post
title:      从零到一spark进阶之路（三） pyspark 处理movies数据集(整理ING6-20)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，欢迎交流分享，未经博主允许不得转载。					https://blog.csdn.net/HHTNAN/article/details/80745221				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h4 id="pyspark简介">PySpark简介</h4>

<p>官方对PySpark的释义为：“PySpark is the Python API for Spark”。 也就是说pyspark为Spark提供的Python编程接口。 <br>
Spark使用py4j来实现python与java的互操作，从而实现使用python编写Spark程序。Spark也同样提供了pyspark，一个Spark的python shell，可以以交互式的方式使用Python编写Spark程序。 </p>



<h4 id="处理movies数据集">处理movies数据集</h4>

<p>下面我通过PySpark对真实的数据集进行处理，并作图形来分析。首先我需要介绍下数据集以及数据处理的环境。</p>



<h5 id="数据概况">数据概况</h5>

<p>MovieLens数据集是由Minnesota大学的GroupLens Research Project对电影评分网站(movielens.umn.edu)收集的，数据集包含了1997年9月19日到1998年四月22日间共七个月的数据。这些数据已经被处理过了（清除了那些评分次数少于20次以及信息没有填写完整的数据）</p>

<p><strong>MovieLens数据集：</strong></p>

<p>MovieLens数据集，用户对自己看过的电影进行评分，分值为1～5。MovieLens包括两个不同大小的库，适用于不同规模的算法．小规模的库是943个独立用户对1682部电影作的10000次评分的数据（我是用这个小规模作数据处理和分析）；通过对数据集分析，为用户预测他对其他未观看的电影的打分，将预测分值高的电影推荐给用户，认为这些电影是用户下一步感兴趣的电影。</p>

<p><strong>数据集结构：</strong></p>

<p>1、943个用户对1682场电影评分，评判次数为100000次，评分标准：1~5分。 <br>
2、每位用户至少评判20场电影。 <br>
3、简单地统计了用户的一些信息 (age, gender, occupation, zip)</p>

<p><strong>数据用途：</strong></p>

<p>供科研单位和研发企业使用，可用于数据挖掘、推荐系统，人工智能等领域，复杂网络研究等领域。</p>



<h4 id="用户年龄统计分析py35">用户年龄统计分析(PY3.5)</h4>

<p>通过对用户数据处理，获得用户信息中的年龄。然后对年龄进行统计并使用Python中的图形框架Matplotlib生成柱状图，最后通过柱状图分析观看电影的观众年龄分布趋势。</p>

<p>参考文献： <br>
官网：<a href="http://spark.apache.org/docs/latest/api/python/index.html" rel="nofollow">http://spark.apache.org/docs/latest/api/python/index.html</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      网易王健宗：革命Hadoop，Spark带来百亿市场价值！
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
2014年4月19日“<strong><a href="http://spark.csdn.net/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">中国Spark技术峰会</a></strong>”（Spark Summit China 2014）将在北京召开，国内外Apache Spark社区成员和企业用户将首次齐聚北京。AMPLab、Databricks、Intel、淘宝、网易等公司的Spark贡献者及一线开发者将分享他们在生产环境中的Spark项目经验和最佳实践方案。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;text-align:center;color:rgb(51,51,51);">
<a href="http://cms.csdnimg.cn/article/201403/20/532aafd5b2f97.jpg" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;"><img src="http://cms.csdnimg.cn/article/201403/20/532aafd5b2f97.jpg?pt=5&amp;ek=1&amp;kp=1&amp;sce=0-12-12" border="0" alt="" style="border:0px;width:568px;vertical-align:middle;"> </a><br></p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
Spark作为一个通用的并行计算框架，已经成为继Hadoop之后又一大热门开源项目，逐渐获得很多企业的支持。在Spark技术峰会召开前夕，记者采访到了本次峰会的演讲者——网易高级研究员、中国计算机学会大数据专委会委员王健宗博士。他曾任惠普云计算高级解决方案专家和美国莱斯大学电子与计算工程系研究员，现负责网易游戏大数据框架的研究和部署工作，在其推广下成功将Spark稳定应用在生产环境中。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;text-align:center;color:rgb(51,51,51);">
<a href="http://cms.csdnimg.cn/article/201403/21/532b954eb3735.jpg" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;"><img src="http://ugc.qpic.cn/adapt/0/1b0a013d-6700-7b8e-6cc0-ebcfa6257982/800?pt=0&amp;ek=1&amp;kp=1&amp;sce=0-12-12" border="0" alt="" style="border:0px;vertical-align:middle;"> </a><br><strong>网易高级研究员、中国计算机学会大数据专委会委员王健宗博士 </strong><br></p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
<strong>CSDN的专访整理如下：</strong></p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
<strong> - 什么原因吸引你钻研Spark技术？</strong>   </p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
主要是Berkeley AMPLab出的东西吸引了我，我很信奉一句：“AMP出品，必属精品”，基本上每周我都会关注他们LAB的主页，阅读他们的技术报告和论文，紧跟他们的研究前沿，建议大家有兴趣多关注一下 <a href="https://amplab.cs.berkeley.edu/projects/" rel="nofollow" style="color:rgb(0,102,204);text-decoration:none;">https://amplab.cs.berkeley.edu/projects/ </a> ，目前他们所孵化的一些生物计算、多核、机器学习等项目可能未来也会影响整个计算机领域。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
记得五六年前，在云计算风生水起的时候，AMPLab所发表的“ABOVE THE CLOUDS: A BERKELEY VIEW OF CLOUD COMPUTING”，是目前云计算引用最高的文章，你现在回头看这篇文章会发现目前云计算的发展方向和战略依然逃不出这篇文章当时所定义和规划的内容。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
我再说个例子，我以前最早是从事存储研究工作的，而AMPLab所出的RISC 、RAID、NOW可以说改变了并深远影响了整个计算机工业，记得我在美国留学的时候，和AMPLab的创始人DAVID PATTERSON有过当面的交流，对于这个杰出科学家依然保持对技术的热情，每天依然和学生MEETING到深夜的敬业精神所深深感动，有这样的一群人，你说他们所出的东西还用怀疑吗？</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
Spark最早出现的时候，我就很关注，这是Berkeley AMPLab在大数据时代的杀手锏，也是将来能一统大数据领域的利器，套用大约三十年前他们发明RAID时候的话，我觉得“ Spark会带来百亿的市场价值”。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
<strong>- 对于解决哪些问题Spark独具优势？</strong></p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
Spark的优势得天独厚，其是一个完整的大数据处理生态系统，除了底层存储HDFS还需要使用Hadoop的生态系统外，其他方面完全可以替代Hadoop，Hadoop自己在可用性、可靠性、实时性方面的一些劣势我这里就不重复叙述了，而Spark正是可以革命Hadoop的目前唯一替代者。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
<strong>- 目前企业应用Spark最大的困难是什么？</strong></p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
目前最大的困难还是人的因素，了解Spark的人还是太少，我现在去和一些企业有大数据需求的CEO聊天的时候，一些单位使用Hadoop的人都很少，何谈Spark？</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
目前Spark处于企业应用的起步阶段，主要是一些大公司在应用，的确各方面还是不成熟，所以培养一批Spark技术专家来推动企业的应用迫不及待，也是目前企业应用的最大困难。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
<strong>- 根据您的了解，目前Spark发展的情况如何？</strong></p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
目前SPARK整个大数据处理生态系统，如流处理、图技术、机器学习、NoSQL查询等方面都有自己的技术，并且也刚刚成为Apache顶级Project，在Hadoop 2.0中也完美整合，此外AMPLab一批人成立了公司来全力推动，有一些教授放弃了公职，一些博士中断了学业，相信以这种壮士断腕的霸气肯定可以把Spark发展好，Hadoop从推动到目前大规模应用也走了五年多时间，而Spark还有比较长的路要走。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
<strong>- 请谈谈你在这次大会上即将分享的话题。</strong></p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
我主要介绍自己Spark的一些经验得失，因为Spark对于你我他都是新东西，都处于学习阶段，我只是走得前了一小步，所以我的一些经验总结期望能为大家应用落地Spark提供参考和帮助。</p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
<strong>- 哪些听众最应该了解这些话题，这个话题可以帮助听众解决哪些问题？</strong></p>
<p style="font-size:14px;line-height:24px;font-family:Helvetica, Tahoma, Arial, sans-serif;list-style:none;color:rgb(51,51,51);">
我认为所有对大数据处理有兴趣，或者出现大数据处理实时性困难的公司或者个人都可以来参加这个峰会。相信经过这次峰会之后，可以坚定大家回去之后应用Spark来提到Hadoop的决心和信心，更多的内容我们可以峰会的时候交流。</p>
            </div>
                </div>
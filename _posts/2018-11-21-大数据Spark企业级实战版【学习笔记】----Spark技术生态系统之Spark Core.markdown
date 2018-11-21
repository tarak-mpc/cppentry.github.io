---
layout:     post
title:      大数据Spark企业级实战版【学习笔记】----Spark技术生态系统之Spark Core
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/henni_719/article/details/77255508				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>1.1.3 Spark技术生态系统</h3>
<p>       Spark生态系统如果1-8所示：</p>
<p>      <img src="https://img-blog.csdn.net/20170816170344731?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p>        Spark Core包含Spark的基本功能，这些功能包括任务调度、内存管理、故障恢复以及存储系统的交互等，其中几个重要的概念：<strong>RDD、Stage、DAG</strong>。Spark Core的核心思想就是将数据集缓存在内存中，并用Lineage机制来进行容错。</p>
<h4>1. Spark Core</h4>
<p><strong>1)       RDD功能特性</strong></p>
<p>      RDD是弹性分布式数据集(ResilientDistributed Datasets)的简称，是分布式只读的且已分区的集合对象。这些集合是弹性的，如果数据集的一部分丢失，则可以对它们进行重建。具有自动容错、位置感知调度和可伸缩性，而容错性是最难实现的。大多数分布式数据集的容错性有两种方式：数据检查点和记录数据的更新。RDD支持记录数据的更新；RDD也支持粗粒度的转换，也就是记录如何从其他RDD转换而来(即Lineage)，以便恢复丢失的分区。</p>
<p><strong>2)       RDD编程接口</strong></p>
<p>Spark提供了丰富的API来操作这些数据集，RDD包含2类API:</p>
<p>Ø  Transformations---转换操作，返回值还是一个RDD，如map、filter、union；</p>
<p>Ø  Actions---行动操作，返回结果或是把RDD持久化起来，如count、collect、save。</p>
<p>transformaton采用的是懒策略，如果只是将transformation提交是不会提交任务来执行的，任务执行只有在action被提交时才被触发。</p>
<p>Spark支持的RDD转换和动作如表1-3所示：</p>
<p>      <img src="https://img-blog.csdn.net/20170816170403350?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><strong>3)       RDD依赖关系</strong></p>
<p>RDD之间的依赖有以下两种：</p>
<p>n  窄依赖(Narrow Dependency)-----一个父RDD最多被一个子RDD引用，如map、filter、union操作；</p>
<p>n  宽依赖(Wide Dependency)----一个父RDD被多个子RDD引用，如groupByKey操作。</p>
<p>RDD依赖图如图1-9所示：</p>
<p><img src="https://img-blog.csdn.net/20170816170653949?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p><strong>4)       Stage DAG</strong></p>
<p>       Spark提交Job之后会把Job生成多个Stage，多个Stage之间是有依赖的。对于窄依赖，Spark会尽量多地将RDD转换放在同一个Stage中；对于宽依赖，大多数时候是shuffle操作，因此Spark会将此Stage定义为ShuffleMapStage，以便于向MapOutputTracker注册shuffle操作。Spark通常将shuffle操作定义为stage的边界。Stage划分图如图1-10所示：</p>
<p><img src="https://img-blog.csdn.net/20170816170707842?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvaGVubmlfNzE5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="">     </p>
<p>    </p>
            </div>
                </div>
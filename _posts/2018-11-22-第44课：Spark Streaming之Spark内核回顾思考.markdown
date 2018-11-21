---
layout:     post
title:      第44课：Spark Streaming之Spark内核回顾思考
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：王家林大咖2018年新书《SPARK大数据商业实战三部曲》清华大学出版，清华大学出版社官方旗舰店（天猫）https://qhdx.tmall.com/?spm=a220o.1000855.1997427721.d4918089.4b2a2e5dT6bUsM					https://blog.csdn.net/duan_zhihua/article/details/51930396				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>第44课：Spark Streaming之Spark内核回顾思考<br>
1 spark streaming 与spark core<br>
2 spark core思考</p>
<p>一：spark core的数据结构三种：</p>
<p><br>
rdd</p>
<p> broadcast</p>
<p> accumlator</p>
<p>私有的，全局的理解</p>
<p> </p>
<p>二：spark 思考</p>
<p>rdd理解是一个数组，是分布式的数组；而hashset、array 就是一个单机版本的结构，<br>
rdd平铺在分布式的机器上，和在一台机器上的具体的区别，不同部分位于不同的机器上，<br>
因此必须获取数据的地址，如数组的下标，需要知道在什么机器上，加上了一个机器的维度而已，<br>
所以我们学习RDD，认识RDD，和认识一个数组没有区别。RDD有自己的定位机制，不比一个数组的学习更复杂。</p>
<p>基于rdd的编程和调度<br>
数组：循环遍历、增加、减少，在同一个进程中<br>
RDD：位于不同机器上，因此要到不同机器上去操作，数据不动，代码动的理念。我们清楚知道RDD的数据位置<br>
。http，ftp方式把代码传过去而已。有一个管理的过程，由driver来负责，而数组的操作没有管理，driver主要管理计算。</p>
<p><br>
管理延伸出来的：<br>
1、到不同的具体的机器，任务调度<br>
2、在不同的机器上，容错的方式，出错了，怎么恢复？就像数组出错了，怎么恢复？（缓存，保存）<br>
   业界经典方式：做快照，计算一部分，就备份一部分。但是消耗时间，牵涉到网络通信。<br>
   而RDD记住的是算子之间的依赖关系，从数据产生点来做。数据从哪里来，rdd 本身包含了数据在哪里，包含了对数据</p>
<p>的计算，因此可以部分的恢复数据。计算的时候搞了一堆规则，数据的传送搞了shuffle，   就象对数组计算，循环item进行计算，全局级别的计算，数组各个item的出现的次数，   RDD一开始计算各个部分的计算，group，reduce是全局级别的计算，就象对数组全局的操作，没有任何区别，<br>
只是rdd数据在不同机器上，加上了IP地址和端口，在不同机器之间传数据，这代价比较大。spark想把计算的结果<br>
保存在本地上，防止网络出现故障，要重新开始计算，这个是空间换时间的理念。曾经一段时间，spark没有保存在磁盘</p>
<p>上，而在内存上。如出现故障，就需重新计算。后来因此就放到了本地磁盘上。shuffle就是对全局数据进行分类的过程</p>
<p>，从前面的数据中抽取出需要的数据。<br>
   就象数组，一个部分，一个部分，也是保存在磁盘上，100万item，开辟很多线程，前面10万，后面10万，中间10万，</p>
<p>分布计算。   <br>
   这样，rdd就是一个大大的数组。</p>
<p> </p>
<p> </p>
<p>3、spark运行在jvm上，而jvm不知道spark的，JVM只有基本的数据结构和支撑，spark是jvm基础之上构造了rdd的数据结</p>
<p>构，rdd就像整数、字符串一样，编译成字节码，和jvm之上的helloword没有区别。数据在一台机器上，和在分布式机器</p>
<p>没有区别，这个是jvm的伟大之处，就是地址的定位而已。就象一个数组的数据定位而已。<br>
   </p>
<p> </p>
<p><img alt="" src="https://img-blog.csdn.net/20160717064618020?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center"></p>
<p> </p>
<p> </p>
<p>在spark基础之上，做spark特定领域框架。spark的强悍之处在于，可以使用spark其他的框架。</p>
<p> </p>
<p>推荐一本书：腾讯写的 《spark最佳实践》 ，作者很有经验！！写的确实不错！！</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
            </div>
                </div>
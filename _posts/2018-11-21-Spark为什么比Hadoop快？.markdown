---
layout:     post
title:      Spark为什么比Hadoop快？
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>最近在招聘面试的时候，往往听到应聘者在介绍Spark的时候，通常拿Spark官网案例Spark和Hadoop做比较。当我问到为什么Spark比Hadoop快时候，得到的答案往往是：Spark是基于内存的计算，而Hadoop是基于磁盘的计算；Spark是一种内存计算技术。</p>

<p>果真如此吗？事实上，不光Spark是内存计算，Hadoop其实也是内存计算。Spark和Hadoop的根本差异是多个任务之间的数据通信问题：Spark多个任务之间数据通信是基于内存，而Hadoop是基于磁盘。</p>

<h1><a name="t0"></a>什么是内存计算技术？</h1>

<p>我们说的内存计算技术是指将数据持久化至内存RAM中进行加工处理的技术。Spark并不支持将数据持久化至内存中，我们通常所说的是spark的数据缓存技术，如将RDD数据缓存至内存，但并没有实现持久化。缓存数据是可以擦除的，擦除后同样是需要我们重新计算的。Spark的数据处理是在内存中进行的，这话并没有错，其实当前的所有操作系统的数据处理都是在内存中进行的。所以，这内存计算并不是Spark的特性。</p>

<p>Spark是允许我们利用缓存技术和LRU算法缓存数据的。Spark的所有运算并不是全部都在内存中，当shuffle发生的时候，数据同样是需要写入磁盘的。所以，Spark并不是基于内存的技术，而是使用了缓存机制的技术。</p>

<h1><a name="t1"></a>Spark真的比Hadoop快一个或两个数量级吗？</h1>

<p>Spark最引以为豪的无非就是官网和相关书籍中描述的和Hadoop之间性能比较的案例。这个案例是逻辑回归机器学习算法，主要特征是对同一份数据的反复迭代运算。Spark是内存缓存，所以数据只加载一次，Hadoop则需要反复加载。实际情况下，Spark通常比Hadoop快十倍以内是合理的。主要快在哪里呢？</p>

<p>首先，Spark Task的启动时间快。Spark采用fork线程的方式，而Hadoop采用创建新的进程的方式。</p>

<p>其次，Spark只有在shuffle的时候将数据写入磁盘，而Hadoop中多个MR作业之间的数据交互都要依赖于磁盘交互。</p>

<p>第三，Spark的缓存机制比HDFS的缓存机制高效。</p>

<p>所以，整体而言，Spark比Hadoop的MR程序性能要高，正常在三到四倍左右，而并不是官网所说的高几百倍。</p>            </div>
                </div>
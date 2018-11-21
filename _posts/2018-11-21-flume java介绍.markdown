---
layout:     post
title:      flume java介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 id="1">背景</h1>

<p>近期在做shark flume开发框架的测试，该框架是一个简单高效的面向数据的pipeline框架，采用flume java的思想，实现了一套flume java on MaxCompute的library。为了更好的了解shark自己也去阅读了flume java的paper，这里做一些总结，主要针对flume java的一些基本概念和思想，由此可以了解shark相较于我们已有的MaxCompute开发框架有哪些不同。</p>

<h1 id="2">基本概念</h1>

<h2 id="3">MapReduce介绍</h2>

<p>flume java底层也是通过Map Reduce的方式执行任务，所以这里先简单介绍一下MapReduce框架。<br>
MapReduce是一个分布式计算框架，主要针对批量大数据处理的场景。大家很熟悉的hadoop和MaxCompute计算都是MapReduce思想的具体实现。MapReduce框架的计算过程主要分成map-combine-shuffle-reduce几个阶段，具体每个阶段的工作可以参见下图。（引用自<a href="http://blog.jobbole.com/84089/" rel="nofollow">http://blog.jobbole.com/84089/</a>）<br><img alt="hadoop1" class="has" src="http://img1.tbcdn.cn/L1/461/1/90a4bf0cc1bd5a56a601b1440a09936fd044bf1e"></p>

<h2 id="4">husky介绍</h2>

<p>Husky是一个基于MaxCompute Map-reduce计算框架的工具包，内部封装了一些常用的功能模块。比如最常用的Flow模块可以完成各种不同类型任务的依赖搭建，甚至是外部任务或者是天网任务。Flow可以尽量的并行任务，提高MaxCompute集群的资源利用率。Husky除了支持Map-reduce任务外还支持sql、exec、tunnel等多种类型的任务，目前在阿里妈妈使用非常广泛。</p>

<h1 id="5">flume java介绍</h1>

<p>flume java的本质是一个面向数据的计算框架，这里包含了两个主要概念，数据和操作。</p>

<h2 id="6">基本数据类型</h2>

<p>flume java定义了两种数据集合：PCollection表示一个不可变的数据集合，PTable是PCollection的一个子类，表示一个不可变的结构的数据集合。数据集合中的数据类型是用户可以随意指定，比如MaxCompute的record，基本数据类型，pb类型等，所以这两种数据集合基本可以涵盖我们日常见到的全部类型的数据。</p>

<h2 id="7">基本操作</h2>

<p>flume java定义了几种针对以上数据集合的基本的数据操作。<br><strong>ParallelDo(DoFn)</strong>--该操作可以类比MapReduce框架中的map或reduce阶段，通过执行DoFn，将输入数据集合PCollection转换成集合PCollection。<br>
用户在使用该算子的时候需要自己实现DoFn方法，实际上用户在使用flume java框架时就是在编写各种处理数据集合的Fn方法，并将这些数据处理转换的过程串联成一个完整的pipeline。<br><strong>GroupByKey</strong>--该操作可以类比MapReduce框架中的shuffle阶段，作用是将相同的key对应的value聚合起来，即将PCollection处理成PCollection&gt;的过程。<br><strong>combineValue</strong>--该操作可以类比MapReduce框架中的combine阶段，将PCollection&gt;处理成PCollection。<br><strong>flattens</strong>--针对所有的输入数据集合做一个合并操作，即将多个PCollection合并成PCollection。</p>

<h2 id="8">衍生操作</h2>

<p>基于以上几种基本操作，flume java又封装了一些常用的衍生操作。<br><strong>count</strong>--将PCollection经过parallelDo变换，groupByKey聚合和combineValue操作最终变成PTable。大数据计算里最常见的wordCount在flume java中可以用这样一个算子实现。<br><strong>join</strong>--将PTable和PTable经过parallelDo变换，flatten聚合和又一次的parallelDo变换转变成PTable, PCollection&gt;&gt;。<br><strong>top</strong>--计算一个PCollection中top n的元素，该算子是parallelDo，groupByKey和combineValue几种算子的封装。</p>

<p>以上就是flume java中定义的数据集合和操作，任何基于flume java思想实现的library都要实现这些基础的操作算子，然后再结合实际应用总结出更多的算子，并用基础算子加以实现封装，shark框架就结合实际应用封装了更多的衍生算子，让框架使用者更方便的使用。</p>

<h2 id="9">任务执行</h2>

<p>flume java的执行粒度是pipeline，用户通过前面介绍的一系列算子生成了一个pipeline后，flume java并不是马上开始执行这个pipeline。因为flume java的运行采用lazy模式，用户需要显示的调用一个执行命令，才会真正开始执行任务，前期会完成创建执行计划和执行计划的优化工作，所以最终执行的是已经优化完成的pipeline。</p>

<h2 id="10">任务优化</h2>

<p>flume java有多种复杂的优化机制，一种是针对执行计划进行优化，另外在调度上会根据数据量大小选择本地和远程执行的模式，这种支持local模式的机制在测试场景中非常实用，测试人员的小数据量的任务无需再提交到集群上跟大任务争资源，直接本地就可以执行，这将大大缩短测试的时间。<br>
pipeline优化的核心思想就是将可以合并在同一个map reduce job中的任务进行合并，减少总体job数量同时减少中间数据的落地输出。<br>
这里给出paper中一个优化的例子。<br>
原始任务pipeline：<br><img alt="flume5" class="has" src="http://img4.tbcdn.cn/L1/461/1/63af808d665434c16f3b3795f150501261a82f48"><br>
该图中每一个节点对应一个并行的数据操作，除去writes节点共有16个操作节点。<br>
优化后的pipeline：<br><img alt="flume6" class="has" src="http://img4.tbcdn.cn/L1/461/1/877603a318aed8bd1458b5d42760df5731d91611"><br>
最终经过优化实际只需要执行2个map reduce任务即可。<br>
具体的优化策略这里不再介绍，大家可以参考flume java的paper了解更详细的过程。</p>

<h1 id="11">shark的特性</h1>

<p>前面介绍过shark实际是实现了一套flume java on MaxCompute的library，包含了丰富的join算子，比如双流join、multi join、shard join、map join；多种聚合算子，sum、avg、top等；支持二次排序和一些集合运算。同时也采用了husky中的一些功能，比如采用xml配置描述pipeline，但配置过程已经大大简化。支持断点重跑，pipeline DAG可视化等功能。</p>

<h1 id="12">总结</h1>

<p>paper中给出了一系列的结论用于阐明该框架的优势，比如由于该框架的易用性，越来越多的开发人员开始使用这个框架编写任务；采用优化策略使得实际的mr任务数缩减到1/5，从而执行效率也得到提升；开发人员迁移到该框架后代码量也得到缩减。<br>
在测试shark的过程中自己也编写了一些基于该框架的任务，自己的感觉是配置文件得到了简化，无需再去了解MaxCompute或者husky的一些接口，只需要专注对数据的处理，实际执行任务时也没有了各种中间数据的输出，对于一些比较简单的任务测试起来是非常方便的。当然这种优化有时候也是一把双刃剑，一旦最终结果出现问题，往往需要追溯每个中间步骤的结果，而在这种框架下中间步骤可能已经被优化合并了，所以也曾跟开发讨论过如何支持debug模式的方案。但整体来说该框架还是能大大提高测试开发效率的。</p>            </div>
                </div>
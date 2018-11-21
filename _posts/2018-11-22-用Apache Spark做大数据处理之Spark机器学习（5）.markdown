---
layout:     post
title:      用Apache Spark做大数据处理之Spark机器学习（5）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<blockquote>
<h2><span style="font-family:SimSun;font-size:14px;">关键点：</span></h2>
<ul><li><span style="font-family:SimSun;font-size:14px;">了解机器学习数据流水线有关内容。 </span></li><li><span style="font-family:SimSun;font-size:14px;">怎么用Apache Spark机器学习包来实现机器学习数据流水线。</span></li><li><span style="font-family:SimSun;font-size:14px;">数据价值链处理的步骤。 </span></li><li><span style="font-family:SimSun;font-size:14px;">Spark机器学习流水线模块和API。 </span></li><li><span style="font-family:SimSun;font-size:14px;">文字分类和广告检测用例。 </span></li></ul></blockquote>
<p><span style="font-family:SimSun;font-size:14px;">在之前的“用Apache Spark做大数据处理”<a href="http://www.infoq.com/author/Srini-Penchikala" rel="nofollow">系列</a>文章中，我们学习了Apache Spark框架，介绍了Spark和它用作大数据处理的不同库（<a href="http://www.infoq.com/articles/apache-spark-introduction" rel="nofollow">第一部分</a>），Spark
 SQL库（<a href="http://www.infoq.com/articles/apache-spark-sql" rel="nofollow">第二部分</a>），Spark流（<a href="http://www.infoq.com/articles/apache-spark-streaming" rel="nofollow">第三部分</a>）和Spark MLlib机器学习库（<a href="https://www.infoq.com/articles/apache-spark-machine-learning" rel="nofollow">第四部分</a>）。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">在这篇文章中，我们Spark的其它机器学习API，名为<a href="http://spark.apache.org/docs/latest/ml-guide.html" rel="nofollow">Spark ML</a>，如果要用数据流水线来开发大数据应用程序的话，这个是推荐的解决方案。</span></p>
<span style="font-family:SimSun;font-size:14px;"></span>
<p><span style="font-family:SimSun;font-size:14px;">Spark ML（<a href="http://spark.apache.org/docs/latest/ml-guide.html" rel="nofollow">spark.ml</a>）包提供了构建在<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#dataframes" rel="nofollow">DataFrame</a>之上的机器学习API，它已经成了Spark
 SQL库的核心部分。这个包可以用于开发和管理机器学习流水线。它也可以提供特征抽取器、转换器、选择器，并支持分类、汇聚和分簇等机器学习技术。这些全都对开发机器学习解决方案至关重要。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">在这里我们看看如何使用Apache Spark来做探索式数据分析（Exploratory Data Analysis）、开发机器学习流水线，并使用Spark ML包中提供的API和算法。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">因为支持构建机器学习数据流水线，Apache Spark框架现在已经成了一个非常不错的选择，可以用于构建一个全面的用例，包括ETL、指量分析、实时流分析、机器学习、图处理和可视化等。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">机器学习数据流水线</span></h2>
<span style="font-family:SimSun;font-size:14px;"></span>
<p><span style="font-family:SimSun;font-size:14px;">机器学习流水线可以用于创建、调节和检验机器学习工作流程序等。机器学习流水线可以帮助我们更加专注于项目中的大数据需求和机器学习任务等，而不是把时间和精力花在基础设施和分布式计算领域上。它也可以在处理机器学习问题时帮助我们，在探索阶段我们要开发迭代式功能和组合模型。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">机器学习工作流通常需要包括一系列的处理和学习阶段。机器学习数据流水线常被描述为一种阶段的序列，每个阶段或者是一个转换器模块，或者是个估计器模块。这些阶段会按顺序执行，输入数据在流水线中流经每个阶段时会被处理和转换。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">机器学习开发框架要支持分布式计算，并作为组装流水线模块的工具。还有一些其它的构建数据流水线的需求，包括容错、资源管理、可扩展性和可维护性等。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">在真实项目中，机器学习工作流解决方案也包括模型导入导出工具、交叉验证来选择参数、为多个数据源积累数据等。它们也提供了一些像功能抽取、选择和统计等的数据工具。这些框架支持机器学习流水线持久化来保存和导入机器学习模型和流水线，以备将来使用。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">机器学习工作流的概念和工作流处理器的组合已经在多种不同系统中越来越受欢迎。象<a href="http://scikit-learn.org/stable/modules/generated/sklearn.pipeline.Pipeline.html" rel="nofollow">scikit-learn</a>和<a href="http://graphlab.com/learn/userguide/index.html#Deployment" rel="nofollow">GraphLab</a>等大数据处理框架也使用流水线的概念来构建系统。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">一个典型的<a href="http://conferences.oreilly.com/strata/big-data-conference-ny-2015/public/schedule/detail/43278" rel="nofollow">数据价值链</a>流程包括如下步骤：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">发现 </span></li><li><span style="font-family:SimSun;font-size:14px;">注入 </span></li><li><span style="font-family:SimSun;font-size:14px;">处理 </span></li><li><span style="font-family:SimSun;font-size:14px;">保存 </span></li><li><span style="font-family:SimSun;font-size:14px;">整合 </span></li><li><span style="font-family:SimSun;font-size:14px;">分析 </span></li><li><span style="font-family:SimSun;font-size:14px;">展示 </span></li></ul><p><span style="font-family:SimSun;font-size:14px;">机器学习数据流水线所用的方法都是类似的。下图展示了在机器学习流水线处理中涉及到的不同步骤。</span></p>
<table><tbody><tr><td width="83">
<p align="center"><span style="font-family:SimSun;font-size:14px;">步骤#</span></p>
</td>
<td width="99">
<p align="center"><span style="font-family:SimSun;font-size:14px;">名字</span></p>
</td>
<td width="410">
<p align="center"><span style="font-family:SimSun;font-size:14px;">描述</span></p>
</td>
</tr><tr><td width="83">
<p align="center"><span style="font-family:SimSun;font-size:14px;">ML1</span></p>
</td>
<td width="99">
<p align="center"><span style="font-family:SimSun;font-size:14px;">数据注入</span></p>
</td>
<td width="410">
<p align="center"><span style="font-family:SimSun;font-size:14px;">从不同的数据源中导入数据。</span></p>
</td>
</tr><tr><td width="83">
<p align="center"><span style="font-family:SimSun;font-size:14px;">ML2</span></p>
</td>
<td width="99">
<p align="center"><span style="font-family:SimSun;font-size:14px;">数据清洗</span></p>
</td>
<td width="410">
<p align="center"><span style="font-family:SimSun;font-size:14px;">对数据进行预处理，为接下来的机器学习数据分析做好准备。</span></p>
</td>
</tr><tr><td width="83">
<p align="center"><span style="font-family:SimSun;font-size:14px;">ML3</span></p>
</td>
<td width="99">
<p align="center"><span style="font-family:SimSun;font-size:14px;">功能抽取</span></p>
</td>
<td width="410">
<p align="center"><span style="font-family:SimSun;font-size:14px;">也叫特征工程，这一步是从数据集中抽取功能。</span></p>
</td>
</tr><tr><td width="83">
<p align="center"><span style="font-family:SimSun;font-size:14px;">ML4</span></p>
</td>
<td width="99">
<p align="center"><span style="font-family:SimSun;font-size:14px;">模型训练</span></p>
</td>
<td width="410">
<p align="center"><span style="font-family:SimSun;font-size:14px;">在接下来的几个步骤里用训练数据集来训练机器学习模型。</span></p>
</td>
</tr><tr><td width="83">
<p align="center"><span style="font-family:SimSun;font-size:14px;">ML5</span></p>
</td>
<td width="99">
<p align="center"><span style="font-family:SimSun;font-size:14px;">模型验证</span></p>
</td>
<td width="410">
<p align="center"><span style="font-family:SimSun;font-size:14px;">接下来要基于不同的预测参数来评估机器学习模型的效率。我们也会在验证步骤调节模型，这一步用于挑选出最佳模型。</span></p>
</td>
</tr><tr><td width="83">
<p align="center"><span style="font-family:SimSun;font-size:14px;">ML6</span></p>
</td>
<td width="99">
<p align="center"><span style="font-family:SimSun;font-size:14px;">模型测试</span></p>
</td>
<td width="410">
<p align="center"><span style="font-family:SimSun;font-size:14px;">这一步是在做模型部署之前进行测试。</span></p>
</td>
</tr><tr><td width="83">
<p align="center"><span style="font-family:SimSun;font-size:14px;">ML7</span></p>
</td>
<td width="99">
<p align="center"><span style="font-family:SimSun;font-size:14px;">模型部署</span></p>
</td>
<td width="410">
<p align="center"><span style="font-family:SimSun;font-size:14px;">最后一步是把选出来的模型部署到生产环境中运行。</span></p>
</td>
</tr></tbody></table><p><span style="font-family:SimSun;font-size:14px;">表一：机器学习流水线处理步骤</span></p>
<p><span style="font-family:SimSun;font-size:14px;">这些步骤也可以用下面的图一表示。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><img src="http://cdn4.infoqstatic.com/statics_s1_20170726-0537/resource/articles/apache-sparkml-data-pipelines/zh/resources/00.jpg" alt="" width="550"></span></p>
<p><span style="font-family:SimSun;font-size:14px;">图一：机器学习数据流水线处理流图</span></p>
<p><span style="font-family:SimSun;font-size:14px;">接下来让我们一起看看每个步骤的细节。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>数据注入</strong>：我们收集起来供给机器学习流水线应用程序的数据可以来自于多种数据源，数据规模也是从几百GB到几TB都可以。而且，大数据应用程序还有一个特征，就是注入不同格式的数据。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>数据清洗</strong>：数据清洗这一步在整个数据分析流水线中是第一步，也是至关重要的一步，也可以叫做数据清理或<a href="https://en.wikipedia.org/wiki/Data_wrangling" rel="nofollow">数据转换</a>，这一步主要是要把输入数据变成结构化的，以方便后续的数据处理和预测性分析。依进入到系统中的数据质量不同，<a href="http://blog.kaggle.com/2016/07/21/approaching-almost-any-machine-learning-problem-abhishek-thakur/" rel="nofollow">总处理时间的60%-70%会被花在数据清洗上</a>，把数据转成合适的格式，这样才能把机器学习模型应用到数据上。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">数据总会有各种各样的质量问题，比如数据不完整，或者数据项不正确或不合法等。数据清洗过程通常会使用各种不同的方法，<a href="https://developer.ibm.com/spark/blog/2016/02/22/predictive-model-for-online-advertising-using-spark-machine-learning-pipelines/" rel="nofollow">包括定制转换器</a>等，用流水线中的定制的转换器去执行数据清洗动作。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">稀疏或粗粒数据是数据分析中的另一个挑战。在这方面总会发生许多极端案例，所以我们要用上面讲到的数据清洗技术来保证输入到数据流水线中的数据必须是高质量的。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">伴随着我们对问题的深入理解，每一次的连续尝试和不断地更新模型，数据清洗也通常是个迭代的过程。象<a href="https://www.trifacta.com/" rel="nofollow">Trifacta</a>、<a href="http://openrefine.org/" rel="nofollow">OpenRefine</a>或<a href="https://activeclean.github.io/" rel="nofollow">ActiveClean</a>等数据转换工具都可以用来完成数据清洗任务。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>特征抽取</strong>：在特征抽取（有时候也叫特征工程）这一步，我们会用<a href="https://en.wikipedia.org/wiki/Feature_hashing" rel="nofollow">特征哈希</a>（Hashing Term Frequency）和<a href="https://en.wikipedia.org/wiki/Word2vec" rel="nofollow">Word2Vec</a>等技术来从原始数据中抽取具体的功能。这一步的输出结果常常也包括一个汇编模块，会一起传入下一个步骤进行处理。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>模型训练</strong>：机器学习<a href="http://docs.aws.amazon.com/machine-learning/latest/dg/training-ml-models.html" rel="nofollow">模型训练</a>包括提供一个算法，并提供一些训练数据让模型可以学习。学习算法会从训练数据中发现模式，并生成输出模型。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>模型验证</strong>：这一步包评估和调整机器学习模型，以衡量用它来做预测的有效性。如这篇<a href="http://blog.cloudera.com/blog/2016/02/how-to-predict-telco-churn-with-apache-spark-mllib/" rel="nofollow">文章</a>所说，对于二进制分类模型评估指标可以用接收者操作特征（Receiver
 Operating Characteristic，<a href="https://en.wikipedia.org/wiki/Receiver_operating_characteristic" rel="nofollow">ROC</a>）曲线。ROC曲线可以表现一个二进制分类器系统的性能。创建它的方法是在不同的阈值设置下描绘真阳性率（True Positive Rate，<a href="https://en.wikipedia.org/wiki/Sensitivity_and_specificity" rel="nofollow">TPR</a>）和假阳性率（False
 Positive Rate，<a href="https://en.wikipedia.org/wiki/False_positive_rate" rel="nofollow">FPR</a>）之间的对应关系。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>模型选择</strong>：模型选择指让转换器和估计器用数据去选择参数。这在机器学习流水线处理过程中也是关键的一步。ParamGridBuilder和CrossValidator等类都提供了API来选择机器学习模型。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>模型部署</strong>：一旦选好了正确的模型，我们就可以开始部署，输入新数据并得到预测性的分析结果。我们也可以把机器学习模型部署成<a href="https://azure.microsoft.com/en-us/documentation/videos/deploying-a-predictive-model-as-a-service-part-i-/" rel="nofollow">网页服务</a>。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">Spark机器学习</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">机器学习流水线API是在Apache Spark框架1.2版中引入的。它给开发者们提供了API来创建并执行复杂的机器学习工作流。流水线API的目标是通过为不同机器学习概念提供标准化的API，来让用户可以快速并轻松地组建并配置可行的分布式机器学习流水线。流水线API包含在org.apache.spark.ml包中。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">Spark ML也有助于把多种机器学习算法组合到一条流水线中。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">Spark机器学习API被分成了两个包，分别是spark.mllib和spark.ml。其中spark.ml包包括了基于RDD构建的原始API。而spark.ml包则提供了构建于DataFrame之上的高级API，用于构建机器学习流水线。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">基于RDD的MLlib库API现在处于<a href="https://www.infoq.com/news/2016/05/Apache-Spark-2.0-Tech-Preview" rel="nofollow">维护模式</a>。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">如下面图二所示，Spark ML是Apache Spark生态系统中的一个非常重要的大数据分析库。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><img src="http://cdn4.infoqstatic.com/statics_s1_20170726-0537/resource/articles/apache-sparkml-data-pipelines/zh/resources/01.jpg" alt="" width="550"></span></p>
<p><span style="font-family:SimSun;font-size:14px;">图二：包括了Spark ML的Spark生态系统</span></p>
<h4><span style="font-family:SimSun;font-size:14px;">机器学习流水线模块</span></h4>
<p><span style="font-family:SimSun;font-size:14px;">机器学习数据流水线包括了完成数据分析任务所需要的多个模块。数据流水线的关键模块被列在了下面：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">数据集 </span></li><li><span style="font-family:SimSun;font-size:14px;">流水线 </span></li><li><span style="font-family:SimSun;font-size:14px;">流水线的阶段 </span></li><li><span style="font-family:SimSun;font-size:14px;">转换器 </span></li><li><span style="font-family:SimSun;font-size:14px;">估计器 </span></li><li><span style="font-family:SimSun;font-size:14px;">评估器 </span></li><li><span style="font-family:SimSun;font-size:14px;">参数（和参数地图） </span></li></ul><p><span style="font-family:SimSun;font-size:14px;">接下来我们简单看看这些模块可以怎么对应到整体的步骤中。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>数据集</strong>：在机器学习流水线中是使用DataFrame来表现数据集的。它也允许按有名字的字段保存结构化数据。这些字段可以用于保存文字、功能向量、真实标签和预测。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>流水线</strong>：机器学习工作流被建模为流水线，这包括了一系列的阶段。每个阶段都对输入数据进行处理，为下一个阶段产生输出数据。一个流水线把多个转换器和估计器串连起来，描述一个机器学习工作流。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>流水线的阶段</strong>：我们定义两种阶段，转换器和估计器。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>转换器</strong>：算法可以把一个DataFrame转换成另一个DataFrame。比如，机器学习模型就是一个转换器，用于把一个有特征的DataFrame转换成一个有预测信息的DataFrame。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">转换器会把一个DataFrame转成另一个DataFrame，同时为它加入新的特征。比如在Spark ML包中，<a href="https://spark.apache.org/docs/latest/ml-features.html#onehotencoder" rel="nofollow">OneHotEncoder</a>就会把一个有标签索引的字段转换成一个有向量特征的字段。每个转换器都有一个transform()函数，被调用时就会把一个DataFrame转换成另一个。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>估计器</strong>：估计器就是一种机器学习算法，会从你提供的数据中进行学习。估计器的输入是一个DataFrame，输出就是一个转换器。估计器用于训练模型，它生成转换器。比如，逻辑回归估计器就会产生逻辑回归转换器。另一个例子是把K-Means做为估计器，它接受训练数据，生成K-Means模型，就是一个转换器。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>参数</strong>：机器学习模块会使用通用的API来描述参数。参数的例子之一就是模型要使用的最大迭代次数。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">下图展示的是一个用作文字分类的数据流水线的各个模块。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><img src="http://cdn4.infoqstatic.com/statics_s1_20170726-0537/resource/articles/apache-sparkml-data-pipelines/zh/resources/02.jpg" alt="" width="250"></span></p>
<p><span style="font-family:SimSun;font-size:14px;">图三：使用Spark ML的数据流水线</span></p>
<h3><span style="font-family:SimSun;font-size:14px;">用例</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">机器学习流水线的用例之一就是文字分类。这种用例通常包括如下步骤：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">清洗文字数据 </span></li><li><span style="font-family:SimSun;font-size:14px;">将数据转化成特征向量，并且 </span></li><li><span style="font-family:SimSun;font-size:14px;">训练分类模型 </span></li></ul><p><span style="font-family:SimSun;font-size:14px;">在文字分类中，在进行分类模型（类似<a href="https://en.wikipedia.org/wiki/Support_vector_machine" rel="nofollow">SVM</a>）的训练之前，会进行n-gram抽象和TF-IDF特征权重等数据预处理。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">另一个机器学习流水线用例就是在这篇<a href="https://amplab.cs.berkeley.edu/ml-pipelines/" rel="nofollow">文章</a>中描述的图像分类。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">还有很多种其它机器学习用例，包括欺诈检测（使用分类模型，这也是监督式学习的一部分），用户分区（聚簇模型，这也是非监督式学习的一部分）。</span></p>
<h3><span style="font-family:SimSun;font-size:14px;">TF-IDF</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">词频-逆向文档频率（Term Frequency - Inverse Document Frequency，<a href="https://en.wikipedia.org/wiki/Tf%E2%80%93idf" rel="nofollow">TF-IDF</a>）是一种在给定样本集合内评估一个词的重要程度的静态评估方法。这是一种信息获取算法，用于在一个文档集合内给一个词的重要性打分。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">TF：如果一个词在一份文档中反复出现，那这个词就比较重要。具体计算方法为：</span></p>
<pre><span style="font-family:SimSun;font-size:14px;">TF = (# of times word X appears in a document) / (Total # of
words in the document)
</span></pre>
<p><span style="font-family:SimSun;font-size:14px;">IDF：但如果一个词在多份文档中都频繁出现（比如the，and，of等），那就说明这个词没有什么实际意义，因此就要降低它的评分。</span></p>
<h3><span style="font-family:SimSun;font-size:14px;">示例程序</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">下面我们看个示例程序，了解一下Spark ML包可以怎样用在大数据处理系统中。我们会开发一个文档分类程序，用于区别程序输入数据中的广告内容。测试用的输入数据集包括文档、电子邮件或其它任何从外部系统中收到的可能包含广告的内容。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">我们将使用在<a href="http://conferences.oreilly.com/strata/strata-eu-2016" rel="nofollow">Strata Hadoop World Conference</a>研讨会上讨论的“用Spark构建机器学习应用”的<a href="http://conferences.oreilly.com/strata/hadoop-big-data-eu/public/schedule/detail/49475" rel="nofollow">广告检测</a>示例来构建我们的示例程序。</span></p>
<h3><span style="font-family:SimSun;font-size:14px;">用例</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">这个用例会对发送到我们的系统中的各种不同消息进行分析。有些消息里面是含有广告信息的，但有些消息里面没有。我们的目标就是要用Spark ML API找出那些包含了广告的消息。</span></p>
<h3><span style="font-family:SimSun;font-size:14px;">算法</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">我们将使用机器学习中的<a href="https://en.wikipedia.org/wiki/Logistic_regression" rel="nofollow">逻辑回归</a>算法。逻辑回归是一种回归分析模型，可以基于一个或多个独立变量来预测得到是或非的可能结果。</span></p>
<h3><span style="font-family:SimSun;font-size:14px;">详细的解决方案</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">接下来咱们看看这个Spark ML示例程序的细节，以及运行步骤。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>数据注入</strong>：我们会把包含广告的数据（文本文件）和不包含广告的数据都导入。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>数据清洗</strong>：在示例程序中，我们不做任何特别的数据清洗操作。我们只是把所有的数据都汇聚到一个DataFrame对象中。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">我们随机地从训练数据和测试数据中选择一些数据，创建一个数组对象。在这个例子中我们的选择是70%的训练数据，和30%的测试数据。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">在后续的流水线操作中我们分别用这两个数据对象来训练模型和做预测。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">我们的机器学习数据流水线包括四步：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">Tokenizer </span></li><li><span style="font-family:SimSun;font-size:14px;">HashingTF </span></li><li><span style="font-family:SimSun;font-size:14px;">IDF </span></li><li><span style="font-family:SimSun;font-size:14px;">LR </span></li></ul><p><span style="font-family:SimSun;font-size:14px;">创建一个流水线对象，并且在流水线中设置上面的各个阶段。然后我们就可以按照例子，基于训练数据来创建一个逻辑回归模型。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">现在，我们再使用测试数据（新数据集）来用模型做预测。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">下面图四中展示了例子程序的架构图。</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><img src="http://cdn4.infoqstatic.com/statics_s1_20170726-0537/resource/articles/apache-sparkml-data-pipelines/zh/resources/03.jpg" alt="" width="550"></span></p>
<p><span style="font-family:SimSun;font-size:14px;">图4：数据分类程序架构图</span></p>
<h3><span style="font-family:SimSun;font-size:14px;">技术</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">在实现机器学习流水线解决方案时我们用到了下面的技术。</span></p>
<table width="489"><tbody><tr><td width="218">
<p align="center"><span style="font-family:SimSun;font-size:14px;">技术</span></p>
</td>
<td width="269">
<p align="center"><span style="font-family:SimSun;font-size:14px;">版本</span></p>
</td>
</tr><tr><td width="218">
<p align="center"><span style="font-family:SimSun;font-size:14px;">Apache Spark</span></p>
</td>
<td width="269">
<p align="center"><span style="font-family:SimSun;font-size:14px;">2.0.0</span></p>
</td>
</tr><tr><td width="218">
<p align="center"><span style="font-family:SimSun;font-size:14px;">JDK</span></p>
</td>
<td width="269">
<p align="center"><span style="font-family:SimSun;font-size:14px;">1.8</span></p>
</td>
</tr><tr><td width="218">
<p align="center"><span style="font-family:SimSun;font-size:14px;">Maven</span></p>
</td>
<td width="269">
<p align="center"><span style="font-family:SimSun;font-size:14px;">3.3</span></p>
</td>
</tr></tbody></table><p><span style="font-family:SimSun;font-size:14px;">表二：在机器学习例子中用到的技术和工具</span></p>
<h3><span style="font-family:SimSun;font-size:14px;">Spark ML程序</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">根据研讨会上的例子而写成的机器学习<a href="https://github.com/jayantshekhar/strata-2016/blob/master/src/main/scala/com/cloudera/spark/spamdetection/Spam.scala" rel="nofollow">代码</a>是用Scala编程语言写的，我们可以直接使用Spark
 Shell控制台来运行这个程序。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">广告检测Scala代码片段：</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><strong>第一步</strong>：创建一个定制的类，用来存储广告内容的细节。</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>case class SpamDocument(file: String, text: String, label:
Double)
</code></span></pre>
<p><span style="font-family:SimSun;font-size:14px;"><strong>第二步</strong>：初始化SQLContext，并通过<a href="https://spark.apache.org/docs/latest/api/java/org/apache/spark/sql/SQLContext.implicits%24.html" rel="nofollow">隐式转换方法</a>来把Scala对象转换成DataFrame。然后从存放着输入文件的指定目录导入数据集，结果会返回RDD对象。然后由这两个数据集的RDD对象创建DataFrame对象。</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>val sqlContext = new SQLContext(sc)
import sqlContext.implicits._

//
// Load the data files with spam
//
val rddSData = sc.wholeTextFiles("SPAM_DATA_FILE_DIR", 1)
val dfSData = rddSData.map(d =&gt; SpamDocument(d._1, d._2,1)).toDF()
dfSData.show()

//
// Load the data files with no spam
//
val rddNSData = sc.wholeTextFiles("NO_SPAM_DATA_FILE_DIR",
1)
val dfNSData = rddNSData.map(d =&gt; SpamDocument(d._1,d._2, 0)).toDF()
dfNSData.show()
</code></span></pre>
<p><span style="font-family:SimSun;font-size:14px;"><strong>第三步</strong>：现在，把数据集汇聚起来，然后根据70%和30%的比例来把整份数据拆分成训练数据和测试数据。</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>//
// Aggregate both data frames
//
val dfAllData = dfSData.unionAll(dfNSData)
dfAllData.show()

//
// Split the data into 70% training data and 30% test data
//
val Array(trainingData, testData) =
dfAllData.randomSplit(Array(0.7, 0.3))
</code></span></pre>
<p><span style="font-family:SimSun;font-size:14px;"><strong>第四步</strong>：现在可以配置机器学习数据流水线了，要创建我们在文章前面部分讨论到的几个部分：Tokenizer、HashingTF和IDF。然后再用训练数据创建回归模型，在这个例子中是逻辑回归。</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>//
// Configure the ML data pipeline
//

//
// Create the Tokenizer step
//
val tokenizer = new Tokenizer()
  .setInputCol("text")
  .setOutputCol("words")

//
// Create the TF and IDF steps
//
val hashingTF = new HashingTF()
  .setInputCol(tokenizer.getOutputCol)
  .setOutputCol("rawFeatures")

val idf = new
IDF().setInputCol("rawFeatures").setOutputCol("features")

//
// Create the Logistic Regression step
//
val lr = new LogisticRegression()
  .setMaxIter(5)
lr.setLabelCol("label")
lr.setFeaturesCol("features")

//
// Create the pipeline
//
val pipeline = new Pipeline()
  .setStages(Array(tokenizer, hashingTF, idf, lr))

val lrModel = pipeline.fit(trainingData)
println(lrModel.toString())
</code></span></pre>
<p><span style="font-family:SimSun;font-size:14px;"><strong>第五步</strong>：最后，我们调用逻辑回归模型中的转换方法来用测试数据做预测。</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>//
// Make predictions.
//
val predictions = lrModel.transform(testData)

//
// Display prediction results
//
predictions.select("file", "text", "label", "features", "prediction").show(300)
</code></span></pre>
<h3><span style="font-family:SimSun;font-size:14px;">结论</span></h3>
<p><span style="font-family:SimSun;font-size:14px;">Spark机器学习库是Apache Spark框架中最重要的库之一。它用于实现数据流水线。在这篇文章中，我们了解了如何使用<a href="https://spark.apache.org/docs/latest/ml-guide.html" rel="nofollow">Spark ML</a>包的API以及用它来实现一个文本分类用例。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">接下来的内容</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">图数据模型是关于在数据模型中不同的实体之间的连接和关系的。图数据处理技术最近受到了很多关注，因为可以用它来解决许多问题，包括欺诈检测和开发推荐引擎等。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">Spark框架提供了一个库，专门用于图数据分析。我们在这个系列的文章中，接下来会了解这个名为<a href="https://spark.apache.org/docs/latest/graphx-programming-guide.html" rel="nofollow">Spark GraphX</a>的库。我们会用Spark GraphX来开发一个示例程序，用于图数据处理和分析。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">引用</span></h2>
<ul><li><span style="font-family:SimSun;font-size:14px;">用Apache Spark做大数据分析——第一部分：<a href="http://www.infoq.com/articles/apache-spark-introduction" rel="nofollow">介绍</a></span></li><li><span style="font-family:SimSun;font-size:14px;">用Apache Spark做大数据分析——第二部分：<a href="http://www.infoq.com/articles/apache-spark-sql" rel="nofollow">Spark SQL</a></span></li><li><span style="font-family:SimSun;font-size:14px;">用Apache Spark做大数据分析——第三部分：<a href="http://www.infoq.com/articles/apache-spark-streaming" rel="nofollow">Spark流</a></span></li><li><span style="font-family:SimSun;font-size:14px;">用Apache Spark做大数据分析——第四部分：<a href="https://www.infoq.com/articles/apache-spark-machine-learning" rel="nofollow">Spark机器学习</a></span></li><li><span style="font-family:SimSun;font-size:14px;">Apache Spark项目<a href="http://spark.apache.org/" rel="nofollow">官方网站</a></span></li><li><span style="font-family:SimSun;font-size:14px;">Spark<a href="http://spark.apache.org/mllib/" rel="nofollow">机器学习网站</a></span></li><li><span style="font-family:SimSun;font-size:14px;">Spark机器学习<a href="https://spark.apache.org/docs/latest/ml-guide.html" rel="nofollow">编程指导</a></span></li><li><span style="font-family:SimSun;font-size:14px;">Spark研讨会关于<a href="http://conferences.oreilly.com/strata/hadoop-big-data-eu/public/schedule/detail/49475" rel="nofollow">广告检测</a>的练习</span></li></ul>            </div>
                </div>
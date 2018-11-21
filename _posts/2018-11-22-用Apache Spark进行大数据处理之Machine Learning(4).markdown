---
layout:     post
title:      用Apache Spark进行大数据处理之Machine Learning(4)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:SimSun;font-size:14px;">本文是《Spark大数据处理》系列的第四篇，其他三篇：<a href="http://www.infoq.com/articles/apache-spark-introduction" rel="nofollow">Spark介绍</a>、<a href="http://www.infoq.com/articles/apache-spark-sql" rel="nofollow">Saprk
 SQL</a>和<a href="http://www.infoq.com/articles/apache-spark-streaming" rel="nofollow">Spark Streaming</a>。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">最近几年，机器学习、预测分析和数据科学主题得到了广泛的关注。Spark的机器学习库（Spark MLlib），包括各种机器学习算法：协同过滤算法、聚类算法、分类算法和其他算法。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">在前面的《Spark大数据处理》系列文章，介绍Apache Spark框架，介绍如何使用Spark SQL库的SQL接口去访问数据，使用Spark Streaming进行实时流式数据处理和分析。</span></p>
<span style="font-family:SimSun;font-size:14px;"></span>
<p><span style="font-family:SimSun;font-size:14px;">在本篇文章，作者将讨论机器学习概念以及如何使用Spark MLlib来进行预测分析。后面将会使用一个例子展示Spark MLlib在机器学习领域的强悍。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">Spark机器学习API包含两个package：spark.mllib 和spark.ml。<br><a href="http://spark.apache.org/docs/latest/mllib-guide.html" rel="nofollow">spark.mllib</a> 包含基于弹性数据集（RDD）的原始Spark机器学习API。它提供的机器学习技术有：相关性、分类和回归、协同过滤、聚类和数据降维。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">spark.ml提供建立在<a href="http://spark.apache.org/docs/latest/sql-programming-guide.html#dataframes" rel="nofollow">DataFrame</a>的机器学习API，DataFrame是Spark SQL的核心部分。这个包提供开发和管理机器学习管道的功能，可以用来进行特征提取、转换、选择器和机器学习算法，比如分类和回归和聚类。</span></p>
<span style="font-family:SimSun;font-size:14px;"></span>
<p><span style="font-family:SimSun;font-size:14px;">本篇文章聚焦在Spark MLlib上，并讨论各个机器学习算法。下篇文章将讲述Spark ML以及如何创建和管理数据管道。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">机器学习和数据科学</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">机器学习是从已经存在的数据进行学习来对将来进行数据预测，它是基于输入数据集创建模型做数据驱动决策。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">数据科学是从海里数据集（结构化和非结构化数据）中抽取知识，为商业团队提供数据洞察以及影响商业决策和路线图。数据科学家的地位比以前用传统数值方法解决问题的人要重要。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">以下是几类机器学习模型：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">监督学习模型</span></li><li><span style="font-family:SimSun;font-size:14px;">非监督学习模型</span></li><li><span style="font-family:SimSun;font-size:14px;">半监督学习模型</span></li><li><span style="font-family:SimSun;font-size:14px;">增强学习模型</span></li></ul><p><span style="font-family:SimSun;font-size:14px;">下面简单的了解下各机器学习模型，并进行比较：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">监督学习模型：监督学习模型对已标记的训练数据集训练出结果，然后对未标记的数据集进行预测；<br>
监督学习又包含两个子模型：回归模型和分类模型。</span></li><li><span style="font-family:SimSun;font-size:14px;">非监督学习模型：非监督学习模型是用来从原始数据（无训练数据）中找到隐藏的模式或者关系，因而非监督学习模型是基于未标记数据集的；</span></li><li><span style="font-family:SimSun;font-size:14px;">半监督学习模型：半监督学习模型用在监督和非监督机器学习中做预测分析，其既有标记数据又有未标记数据。典型的场景是混合少量标记数据和大量未标记数据。半监督学习一般使用分类和回归的机器学习方法；</span></li><li><span style="font-family:SimSun;font-size:14px;">增强学习模型：<a href="https://en.wikipedia.org/wiki/Reinforcement_learning" rel="nofollow">增强学习模型</a>通过不同的行为来寻找目标回报函数最大化。</span></li></ul><p><span style="font-family:SimSun;font-size:14px;">下面给各个机器学习模型举个列子：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">监督学习：异常监测；</span></li><li><span style="font-family:SimSun;font-size:14px;">非监督学习：社交网络，语言预测；</span></li><li><span style="font-family:SimSun;font-size:14px;">半监督学习：图像分类、语音识别；</span></li><li><span style="font-family:SimSun;font-size:14px;">增强学习：人工智能（AI）。</span></li></ul><h2><span style="font-family:SimSun;font-size:14px;">机器学习项目步骤</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">开发机器学习项目时，数据预处理、清洗和分析的工作是非常重要的，与解决业务问题的实际的学习模型和算法一样重要。<br>
典型的机器学习解决方案的一般步骤：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">特征工程</span></li><li><span style="font-family:SimSun;font-size:14px;">模型训练</span></li><li><span style="font-family:SimSun;font-size:14px;">模型评估</span></li></ul><p><span style="font-family:SimSun;font-size:14px;"><img src="http://cdn4.infoqstatic.com/statics_s1_20170726-0537/resource/articles/apache-spark-machine-learning/zh/resources/000.jpg" alt="" width="550"></span></p>
<p><span style="font-family:SimSun;font-size:14px;">图1</span></p>
<p><span style="font-family:SimSun;font-size:14px;">原始数据如果不能清洗或者预处理，则会造成最终的结果不准确或者不可用，甚至丢失重要的细节。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">训练数据的质量对最终的预测结果非常重要，如果训练数据不够随机，得出的结果模型不精确；如果数据量太小，机器学习出的模型也不准确。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">使用案例：<br>
业务使用案例分布于各个领域，包括个性化推荐引擎（<a href="https://chimpler.wordpress.com/2014/07/22/building-a-food-recommendation-engine-with-spark-mllib-and-play/" rel="nofollow">食品推荐引擎</a>），数据预测分析（<a href="http://eugenezhulenev.com/blog/2014/11/14/stock-price-prediction-with-big-data-and-machine-learning/" rel="nofollow">股价预测</a>或者<a href="http://eugenezhulenev.com/blog/2014/11/14/stock-price-prediction-with-big-data-and-machine-learning/" rel="nofollow">预测航班延迟</a>），广告，异常监测，图像和视频模型识别，以及其他各类人工智能。<br>
接着来看两个比较流行的机器学习应用：个性化推荐引擎和异常监测。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">推荐引擎</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">个性化推荐引擎使用商品属性和用户行为来进行预测。推荐引擎一般有两种算法实现：基于内容过滤和协同过滤。<br>
协调过滤的解决方案比其他算法要好，Spark MLlib实现了<a href="http://spark.apache.org/docs/latest/mllib-collaborative-filtering.html" rel="nofollow">ALS协同过滤算法</a>。Spark MLlib的协同过滤有两种形式：<a href="http://spark.apache.org/docs/latest/mllib-collaborative-filtering.html#explicit-vs-implicit-feedback" rel="nofollow">显式反馈和隐试反馈</a>。显式反馈是基于用户购买的商品（比如，电影），显式反馈虽好，但很多情况下会出现数据倾斜；隐试反馈是基于用户的行为数据，比如，浏览、点击、喜欢等行为。隐试反馈现在大规模应用在工业上进行数据预测分析，因为其很容易收集各类数据。<br>
另外有基于模型的方法实现推荐引擎，这里暂且略过。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">异常监测</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">异常监测是机器学习中另外一个应用非常广泛的技术，因为其可以快速和准确地解决金融行业的棘手问题。金融服务业需要在几百毫秒内判断出一笔在线交易是否非法。<br>
神经网络技术被用来进行销售点的异常监测。比如像PayPal等公司使用不同的机器学习算法（比如，线性回归，神经网络和深度学习）来进行风险管理。</span></p>
<p><span style="font-family:SimSun;font-size:14px;">Spark MLlib库提供给了几个实现的算法，比如，线性SVM、逻辑回归、决策树和贝叶斯算法。另外，一些集成模型，比如随机森林和gradient-boosting树。<br>
那么现在开始我们的使用Apache Spark框架进行机器学习之旅。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">Spark MLlib</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">Spark MLlib实现的机器学习库使得机器学习模型可扩展和易使用，包括分类算法、回归算法、聚类算法、协同过滤算法、降维算法，并提供了相应的API。除了这些算法外，Spark MLlib还提供了各种数据处理功能和数据分析工具为大家使用：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">通过FP-growth算法进行频繁项集挖掘和关联分析；</span></li><li><span style="font-family:SimSun;font-size:14px;">通过PrefixSpan算法进行序列模式挖掘；</span></li><li><span style="font-family:SimSun;font-size:14px;">提供概括性统计和假设检验；</span></li><li><span style="font-family:SimSun;font-size:14px;">提供特征转换；</span></li><li><span style="font-family:SimSun;font-size:14px;">机器学习模型评估和超参数调优。</span></li></ul><p><span style="font-family:SimSun;font-size:14px;"><img src="http://cdn4.infoqstatic.com/statics_s1_20170726-0537/resource/articles/apache-spark-machine-learning/zh/resources/001.jpg" alt="" width="550"></span></p>
<p><span style="font-family:SimSun;font-size:14px;">图2 展示Spark生态<br>
Spark MLlib API支持Scala，Java和Python编程。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">Spark MLlib应用实践</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">使用Spark MLlib实现推荐引擎。推荐引擎最佳实践是基于已知用户的商品行为而去预测用户可能感兴趣的未知商品。推荐引擎基于已知数据（也即，训练数据）训练出预测模型。然后利用训练好的预测模型来预测。<br>
最佳电影推荐引擎的实现有下面几步：</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">加载电影数据；</span></li><li><span style="font-family:SimSun;font-size:14px;">加载你指定的评价数据；</span></li><li><span style="font-family:SimSun;font-size:14px;">加载社区提供的评价数据；</span></li><li><span style="font-family:SimSun;font-size:14px;">将评价数据join成单个RDD；</span></li><li><span style="font-family:SimSun;font-size:14px;">使用ALS算法训练模型；</span></li><li><span style="font-family:SimSun;font-size:14px;">确认指定用户（userId ＝ 1）未评价的电影；</span></li><li><span style="font-family:SimSun;font-size:14px;">预测未被用户评价的电影的评价；</span></li><li><span style="font-family:SimSun;font-size:14px;">获取Top N的推荐（这里N＝ 5）；</span></li><li><span style="font-family:SimSun;font-size:14px;">在终端显示推荐结果。</span></li></ul><p><span style="font-family:SimSun;font-size:14px;">如果你想对输出的数据做进一步分析，你可以把预测的结果存储到Cassandra或者MongoDB等数据库。</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">使用到的技术</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">这里采用Java开发Spark MLlib程序，并在stand－alone模型下执行。使用到的MLlib Java类：org.apache.spark.mllib.recommendation。</span></p>
<ul><li><span style="font-family:SimSun;font-size:14px;">ALS</span></li><li><span style="font-family:SimSun;font-size:14px;">MatrixFactorizationModel</span></li><li><span style="font-family:SimSun;font-size:14px;">Rating</span></li></ul><p><span style="font-family:SimSun;font-size:14px;"><img src="http://cdn4.infoqstatic.com/statics_s1_20170726-0537/resource/articles/apache-spark-machine-learning/zh/resources/002.jpg" alt="" width="550"></span></p>
<p><span style="font-family:SimSun;font-size:14px;">图3 Spark机器学习的例子程序架构</span></p>
<p><span style="font-family:SimSun;font-size:14px;">程序执行：<br>
开发好的程序进行打包，设置环境变量：JDK (JAVA_HOME), Maven (MAVEN_HOME)和Spark (SPARK_HOME)。<br>
在Windows环境中：</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>set JAVA_HOME=[JDK_INSTALL_DIRECTORY]
set PATH=%PATH%;%JAVA_HOME%\bin

set MAVEN_HOME=[MAVEN_INSTALL_DIRECTORY]
set PATH=%PATH%;%MAVEN_HOME%\bin

set SPARK_HOME=[SPARK_INSTALL_DIRECTORY]
set PATH=%PATH%;%SPARK_HOME%\bin

cd c:\dev\projects\spark-mllib-sample-app

mvn clean install
mvn eclipse:clean eclipse:eclipse
</code></span></pre>
<p><span style="font-family:SimSun;font-size:14px;">在Linux或者MAC系统中；</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>export JAVA_HOME=[JDK_INSTALL_DIRECTORY]
export PATH=$PATH:$JAVA_HOME/bin

export MAVEN_HOME=[MAVEN_INSTALL_DIRECTORY]
export PATH=$PATH:$MAVEN_HOME/bin

export SPARK_HOME=[SPARK_INSTALL_DIRECTORY]
export PATH=$PATH:$SPARK_HOME/bin

cd /Users/USER_NAME/spark-mllib-sample-app

mvn clean install
mvn eclipse:clean eclipse:eclipse
</code></span></pre>
<p><span style="font-family:SimSun;font-size:14px;">运行Spark程序，命令如下：<br>
在Windows环境下：</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>%SPARK_HOME%\bin\spark-submit --class "org.apache.spark.examples.mllib.JavaRecommendationExample" --master local[*] target\spark-mllib-sample-1.0.jar
</code></span></pre>
<p><span style="font-family:SimSun;font-size:14px;">在Linux或者MAC环境下：</span></p>
<pre><span style="font-family:SimSun;font-size:14px;"><code>$SPARK_HOME/bin/spark-submit --class "org.apache.spark.examples.mllib.JavaRecommendationExample" --master local[*] target/spark-mllib-sample-1.0.jar
</code></span></pre>
<h2><span style="font-family:SimSun;font-size:14px;">Spark MLlib应用监控</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">使用Spark的web控制台可以进行监控程序运行状态。这里只给出程序运行的有向无环图（DAG）：</span></p>
<p><span style="font-family:SimSun;font-size:14px;"><img src="http://cdn4.infoqstatic.com/statics_s1_20170726-0537/resource/articles/apache-spark-machine-learning/zh/resources/003.jpg" alt="" width="550"></span></p>
<p><span style="font-family:SimSun;font-size:14px;">图4 DAG的可视化</span></p>
<h2><span style="font-family:SimSun;font-size:14px;">结论</span></h2>
<p><span style="font-family:SimSun;font-size:14px;">Spark MLlib是Spark实现的机器学习库中的一种，经常用来做业务数据的预测分析，比如个性化推荐引擎和异常监测系统。</span></p>
            </div>
                </div>
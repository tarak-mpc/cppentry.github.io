---
layout:     post
title:      Spark Python 快速体验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/github_39335046/article/details/73920443				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>Spark是2015年最受热捧大数据开源平台，我们花一点时间来快速体验一下Spark。</p>
<p><strong>Spark 技术栈</strong></p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/103717_d6qN_1450051.png" alt=""></p>
<p>如上图所示，Spark的技术栈包括了这些模块：</p>
<ul><li>
<p>核心模块 ：Spark Core</p>
</li><li>
<p>集群管理 </p>
<ul><li>
<p>Standalone Scheduler</p>
</li><li>
<p>YARN</p>
</li><li>
<p>Mesos</p>
</li></ul></li><li>
<p>Spark SQL</p>
</li><li>
<p>Spark 流 Streaming</p>
</li><li>
<p>Spark 机器学习 MLLib</p>
</li><li>
<p>GraphX 图处理模块</p>
</li></ul><p><br></p>
<p><strong>安装和启动Spark</strong></p>
<p>Spark Python Shell</p>
<div class="cke_widget_wrapper cke_widget_block cke_widget_selected">
<pre class="cke_widget_element"><code class="hljs">&gt; bin/pyspark</code></pre>
<img src="" class="cke_reset cke_widget_mask" alt=""><span class="cke_reset cke_widget_drag_handler_container"><img class="cke_reset cke_widget_drag_handler" src="" width="15" title="Click and drag to move" height="15" alt=""></span></div>
<p>Spark Ipython Shell</p>
<div class="cke_widget_wrapper cke_widget_block cke_widget_selected">
<pre class="cke_widget_element"><code class="hljs">&gt; IPYTHON=1 ./bin/pyspark
&gt; IPYTHON_OPTS="notebook" ./bin/pyspark</code></pre>
<img src="" class="cke_reset cke_widget_mask" alt=""><span class="cke_reset cke_widget_drag_handler_container"><img class="cke_reset cke_widget_drag_handler" src="" width="15" title="Click and drag to move" height="15" alt=""></span></div>
<p><strong>Spark 架构</strong></p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/133119_8CZR_1450051.png" alt=""></p>
<p><strong>初始化 Spark Context</strong></p>
<p>在使用Spark的功能之前首先要初始化Spark的context，Context包含了Spark的连接和配置信息。</p>
<p>Spark Context，Driver和Worker节点之间的关系如下图：</p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/104355_Wazv_1450051.png" alt=""></p>
<div class="cke_widget_wrapper cke_widget_block cke_widget_selected">
<pre class="cke_widget_element"><code class="hljs">from pyspark import SparkConf, SparkContext
conf = SparkConf().setMaster("local").setAppName("My App")
sc = SparkContext(conf = conf)</code></pre>
<img src="" class="cke_reset cke_widget_mask" alt=""><span class="cke_reset cke_widget_drag_handler_container"><img class="cke_reset cke_widget_drag_handler" src="" width="15" title="Click and drag to move" height="15" alt=""></span></div>
<p><strong>创建 RDD</strong></p>
<p>RDD是Spark的基本数据模型，所有的操作都是基于RDD。RDD是inmutable（不可改变）的。</p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/104758_XUvl_1450051.png" alt=""></p>
<div class="cke_widget_wrapper cke_widget_block cke_widget_selected">
<pre class="cke_widget_element"><code class="hljs">lines = sc.textFile("README.md")
pythonLines = lines.filter(lambda line: "Python" in line) 
pythonLines.first()
pythonLines.count()</code></pre>
<img src="" class="cke_reset cke_widget_mask" alt=""><span class="cke_reset cke_widget_drag_handler_container"><img class="cke_reset cke_widget_drag_handler" src="" width="15" title="Click and drag to move" height="15" alt=""></span></div>
<p><strong>RDD 操作:</strong></p>
<p>下面是一些对RDD的变形操作</p>
<p>RDD Transformation on {1,2,3,4}</p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/104932_fuNZ_1450051.png" alt=""></p>
<p>两个RDD之间的操作， Transformation on {1,2,3} and {3,4,5}</p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/105040_IRIP_1450051.png" alt=""></p>
<p>RDD actions on {1,2,3,3}</p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/110942_wuT1_1450051.png" alt=""></p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/111023_hPfv_1450051.png" alt=""></p>
<p>Transformation on pair RDD {(1,2),(3,4),(3,6)}</p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/111500_Avgl_1450051.png" alt=""></p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/111548_2uHT_1450051.png" alt=""></p>
<p>Transform on two pair RDDs {(1,2),(3,4),(3,6)}, {(3,9)}</p>
<p><img src="http://static.oschina.net/uploads/space/2016/0331/111705_hKRC_1450051.png" alt=""></p>
<p><br></p>
<p><br></p>
<p><strong>Spark 流 stream</strong></p>
<p>Spark流基于RDD，可以理解对小的时间片段上的RDD操作。</p>
<p><img src="http://static.oschina.net/uploads/space/2016/0401/091935_8CFC_1450051.png" alt=""></p>
<p><img src="http://static.oschina.net/uploads/space/2016/0401/092052_b6w8_1450051.png" alt=""></p>
<p><img src="http://static.oschina.net/uploads/space/2016/0401/092143_HsOa_1450051.png" alt=""></p>
<p><br></p>
<p><br></p>
<p><strong>SparkSQL</strong></p>
<p>Spark SQL可以用于操作和查询结构化和半结构化的数据。包括Hive，JSON， CSV等。</p>
<div class="cke_widget_wrapper cke_widget_block cke_widget_selected">
<pre class="cke_widget_element"><code class="hljs"># Import Spark SQL
from pyspark.sql import HiveContext, Row
# Or if you can't include the hive requirements
from pyspark.sql import SQLContext, Row 
					
input = hiveCtx.jsonFile(inputFile)
# Register the input schema RDDinput.registerTempTable("tweets")
# Select tweets based on the retweetCount
topTweets = hiveCtx.sql("SELECT text, retweetCount FROM tweets ORDER BY retweetCount LIMIT 10")</code></pre>
<img src="" class="cke_reset cke_widget_mask" alt=""><span class="cke_reset cke_widget_drag_handler_container"><img class="cke_reset cke_widget_drag_handler" src="" width="15" title="Click and drag to move" height="15" alt=""></span></div>
<p>Spark SQL支持JDBC</p>
<p><strong>SparkML </strong></p>
<p>机器学习的基本流程如下：</p>
<ol><li>
<ol><li>
<p>获得数据</p>
</li><li>
<p>从数据中提取特征</p>
</li><li>
<p>对数据进行有监督的或者无监督的学习，训练机器学习的模型</p>
</li><li>
<p>对模型进行评估，找出最佳模型</p>
</li></ol></li></ol><p><img src="http://static.oschina.net/uploads/space/2016/0401/095704_bxG8_1450051.png" alt=""></p>
<p>由于Spark的架构特点，Spark支持的机器学习算法是哪些可以并行的算法。</p>
<div class="cke_widget_wrapper cke_widget_block cke_widget_selected">
<pre class="cke_widget_element"><code class="hljs">from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.feature import HashingTF
from pyspark.mllib.classification import LogisticRegressionWithSGD
					
spam = sc.textFile("spam.txt")normal = sc.textFile("normal.txt")
				
# Create a HashingTF instance to map email text to vectors of 10,000 features.
				
tf = HashingTF(numFeatures = 10000)
# Each email is split into words, and each word is mapped to one feature.
spamFeatures = spam.map(lambda email: tf.transform(email.split(" ")))
normalFeatures = normal.map(lambda email: tf.transform(email.split(" ")))
				
# Create LabeledPoint datasets for positive (spam) and negative (normal) examples.
					
positiveExamples = spamFeatures.map(lambda features: LabeledPoint(1, features))
negativeExamples = normalFeatures.map(lambda features: LabeledPoint(0, features))
trainingData = positiveExamples.union(negativeExamples)
trainingData.cache() # Cache since Logistic Regression is an iterative algorithm.
			
# Run Logistic Regression using the SGD algorithm.
				
model = LogisticRegressionWithSGD.train(trainingData)
				
# Test on a positive example (spam) and a negative one (normal). We first apply
# the same HashingTF feature transformation to get vectors, then apply the model.
posTest = tf.transform("O M G GET cheap stuff by sending money to ...".split(" "))
negTest = tf.transform("Hi Dad, I started studying Spark the other ...".split(" "))
print "Prediction for positive test example: %g" % model.predict(posTest)
print "Prediction for negative test example: %g" % model.predict(negTest) </code></pre>
<img src="" class="cke_reset cke_widget_mask" alt=""><span class="cke_reset cke_widget_drag_handler_container"><img class="cke_reset cke_widget_drag_handler" src="" width="15" title="Click and drag to move" height="15" alt=""></span></div>
<br>            </div>
                </div>
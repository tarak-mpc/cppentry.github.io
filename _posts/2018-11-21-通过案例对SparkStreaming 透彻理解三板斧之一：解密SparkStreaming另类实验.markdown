---
layout:     post
title:      通过案例对SparkStreaming 透彻理解三板斧之一：解密SparkStreaming另类实验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p align="left"> 从spark框架的角度来说，spark包含了如下图所示的功能:</p>
<p align="left"><img src="" alt=""><img src="https://img-blog.csdn.net/20160502133816323?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>从最近spark发布的几个版本来看，GraphX一直没有大的变化，MLlib涉及大量的数学，spark sql则是涉及了大量的sql的优化，所以如果要从某一个子框架来彻底研究spark 那么，spark streaming子框架是最好的切入口。因为本身它就很像是spark core 的一个应用程序。2015年是流式处理的一年，一切数据不是流式处理的话，那么它将失去它应有的价值。</p>
<p>Sparkstreaming可以调用，sparksql ,MLlib,Graphx,这些都源于它的一体化，多元化的特性。</p>
<p> </p>
<p>如何去研究sparkstreaming ,将Batch interval放大，相当于看到了Streaming的慢放版本，可以更清楚它的各个环节工作流程以及状态。</p>
<p> </p>
<pre><code class="language-html">packagecom.dt.spark.sparksteaming
 
importorg.apache.spark.SparkConf
importorg.apache.spark.SparkContext
importorg.apache.spark.rdd.RDD
importorg.apache.spark.streaming.StreamingContext
importorg.apache.spark.streaming.Seconds
/**
 *使用Scala开发集群运行的Spark 在线黑名单过滤程序
 *@author DT大数据梦工厂
 *新浪微博：http://weibo.com/ilovepains/
 *
 *背景描述：在广告点击计费系统中，我们在线过滤掉黑名单的点击，进而保护广告商的利益，只进行有效的广告点击计费
 * 或者在防刷评分（或者流量）系统，过滤掉无效的投票或者评分或者流量；
 *实现技术：使用transform Api直接基于RDD编程，进行join操作
 *
 */
objectOnlineBlackListFilter {
    defmain(args: Array[String]){
      /**
       *第1步：创建Spark的配置对象SparkConf，设置Spark程序的运行时的配置信息
       */
      valconf = new SparkConf() //创建SparkConf对象
      conf.setAppName("OnlineBlackListFilter")//设置应用程序的名称，在程序运行的监控界面可以看到名称
      conf.setMaster("spark://Master:7077")//此时，程序在Spark集群
        
      valssc = new StreamingContext(conf, Seconds(30))//这里可以将Batch interval调整到更大，例如300秒，以便更好的了解Streaming内幕
       
      /**
       *黑名单数据准备，实际上黑名单一般都是动态的，例如在Redis或者数据库中，黑名单的生成往往有复杂的业务
       *逻辑，具体情况算法不同，但是在SparkStreaming进行处理的时候每次都能工访问完整的信息
       */
      valblackList = Array(("hadoop", true),("mahout", true))
      valblackListRDD = ssc.sparkContext.parallelize(blackList, 8)
       
      valadsClickStream = ssc.socketTextStream("Master", 9999)
       
      /**
       *此处模拟的广告点击的每条数据的格式为：time、name
       *此处map操作的结果是name、（time，name）的格式
       */
      valadsClickStreamFormatted = adsClickStream.map { ads =&gt; (ads.split("")(1), ads) }
      adsClickStreamFormatted.transform(userClickRDD=&gt; {
        //通过leftOuterJoin操作既保留了左侧用户广告点击内容的RDD的所有内容，又获得了相应点击内容是否在黑名单中
        valjoinedBlackListRDD = userClickRDD.leftOuterJoin(blackListRDD)
         
        /**
         *进行filter过滤的时候，其输入元素是一个Tuple：（name,((time,name), boolean)）
         *其中第一个元素是黑名单的名称，第二元素的第二个元素是进行leftOuterJoin的时候是否存在在值
         *如果存在的话，表面当前广告点击是黑名单，需要过滤掉，否则的话则是有效点击内容；
         */
        valvalidClicked = joinedBlackListRDD.filter(joinedItem =&gt; {
          if(joinedItem._2._2.getOrElse(false))
          {
            false
          }else {
            true
          }
        })
         
        validClicked.map(validClick=&gt; {validClick._2._1})
      }).print
       
      /**
       *计算后的有效数据一般都会写入Kafka中，下游的计费系统会从kafka中pull到有效数据进行计费
       */
      ssc.start()
      ssc.awaitTermination()
      
    }
}
 </code></pre><br><p></p>
<p>打包运行于spark 集群之中，具体方法如下：</p>
<p>1首先启动hdfs和spark集群，并且开启historyServer以便于查看执行完成的job</p>
<p>2将上述代码打成jar包</p>
<p>3.运行该命令： /usr/local/spark-1.6.1-bin-hadoop2.4/bin/spark-submit --classcom.pzw.spark.OnlineBlackListFilter --master spark://Master:7077/root/Documents/SparkApps/WordCount.jar</p>
<p> </p>
<p>Note:需要启动nc -lk 9999</p>
<p> </p>
<p>从spark ui 的角度分析：</p>
<p><img src="" alt=""><img src="https://img-blog.csdn.net/20160502133855939?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p><span style="color:rgb(51,51,51);">查看JOB0 </span>的UI：</p>
<p><img src="" alt=""><img src="https://img-blog.csdn.net/20160502133910058?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>从此处可以得出，spark在启动的时候会自动创建一些job. </p>
<p>这个job的作用是<span style="color:rgb(51,51,51);">为了最大化第利用集群资源</span>。</p>
<p><span style="color:rgb(51,51,51);">继续看jobid1</span></p>
<p><img src="https://img-blog.csdn.net/20160502133945472?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""></p>
<p><img src="https://img-blog.csdn.net/20160502133959715?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="" alt=""><img src="" alt=""></p>
<p> </p>
<p>此处任务接受者以任务的方式运行在worker中的executor中，并且只有一个task运行。从tasks提示来看，localitylevel为process_local ,我们之前提到过，sparkstreaming 保存数据的方式是memory_and_disk_ser_2的方式，所以当内存能够放下我们接受到的数据的时候，是不会保存到磁盘中的。<span style="color:#333333;"> </span><span style="color:#333333;">这里的数据要经过网络传输，每次数据收集的时候会将数据分片，并
    </span>且将数据分发到各个计算节点上。</p>
<p> </p>
<p>Jobid2</p>
<p><img src="" alt=""><img src="https://img-blog.csdn.net/20160502134016355?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>首先通过sockettext stream会产生一个blockRDD.具体如下图：</p>
<p><img src="" alt=""><img src="https://img-blog.csdn.net/20160502134047567?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><img src="" alt=""><img src="https://img-blog.csdn.net/20160502134201194?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p>当前为我们写的transfrom阶段，由此可以看出，我们虽然在一台机器上接受数据，但是我们却在4台机器上处理数据。由此说明，spark streaming会最大化的利用集群资源处理计算的业务逻辑。</p>
<p>我们接着看jobid3 4 </p>
<p><img src="" alt=""><img src="https://img-blog.csdn.net/20160502134126756?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p></p>
<p><span style="color:#333333;">最后说明：sparkStreaming</span><span style="color:#333333;">是以时间为单位来生成JOB</span>，本质上来讲是加上了时间维度的批处理任务</p>
<p> </p>
<p> 资料来源：<span style="color:rgb(51,51,51);font-family:'Hiragino Sans GB W3', 'Hiragino Sans GB', Arial, Helvetica, simsun, u5b8bu4f53;font-size:16px;line-height:28px;background-color:rgb(238,238,238);">DT_大数据梦工厂</span></p>
            </div>
                </div>
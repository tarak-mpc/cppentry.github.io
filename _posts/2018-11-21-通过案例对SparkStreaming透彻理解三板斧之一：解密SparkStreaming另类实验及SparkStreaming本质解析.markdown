---
layout:     post
title:      通过案例对SparkStreaming透彻理解三板斧之一：解密SparkStreaming另类实验及SparkStreaming本质解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/askvinson/article/details/51340645				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong>一、<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">从Spark Streaming入手</span></strong></p>
<p><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
为何从Spark Streaming切入Spark定制？Spark的子框架已有若干，为何选择Spark Streaming？让我们细细道来。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
1.  Spark最开始只有Spark Core，没有目前的这些子框架。这些子框架是构建于Spark Core之上的。没有哪个子框架能摆脱Spark Core。我们通过对一个框架的彻底研究，肯定可以领会Spark力量的源泉，并精通所有问题的解决之道。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
2.  我们再看看目前的这些子框架。Spark SQL有太多语法，研究这些会太浪费精力。SparkR还没完善。Spark GraphX已无太多可改进之处，图计算相关的数学知识也不是目前重点。Spark MLlib中的机器学习也有太多算法是与数学相关，也不是做改进的好的选择 。所以我们选择了Spark Streaming。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<strong>二 . 对Spark Streaming的理解</strong></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
1. Spark Streaming是流式计算，当今时代是一个流处理时代，一切数据如果不是流式处理， 或者说和流式处理不相关的话，都是无效的数据。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
2. 流式处理才是我们对大数据的初步印象，而不是批处理和数据挖掘，当然Spark强悍的地方在于，他的流式处理可以在线的直接使用机器学习、图计算、SparkSQL、Spark R的成果。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
3. 整个Spark的程序，基于Spark Streaming的最容易出问题，也是最受关注的地方，也是最需要人才的地方。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
4. Spark Streaming和其他子框架的不同之处，Spark Streaming很像基于Spark Core之上的应用程序。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
5. 寻龙点穴，Spark就是龙脉，Spark Streaming就是穴位</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
 </p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
2015年是流式处理的一年。大家考虑用Spark，主要也是因为Spark Streaming。这是一个流处理的时代，一切数据如果与流式处理不相关的话，都是无效的数据。Spark之所以强悍的一个重要原因在于，它的流式处理可以在线使用图计算、机器学习或者SparkR的成果，这得益于Spark一体化、多元化的基础架构设计。也就是在Spark Streaming中可以调用其它子框架，无需任何设置。这是Spark的无可匹敌之处，也是Spark Streaming必将一统天下的根源。但Spark的应用中，Spark Streaming也是最容易出问题的。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
Spark Streaming与其它子框架不同之处在于，它更像是Spark Core之上的一个应用程序。所以如果要做Spark的定制开发，Spark Streaming则提供了最好的参考。你想掌握Spark Streaming，但你不去精通Spark Core的话，那是不可能的。所以我们选择Spark Streaming来提升自己，是找到了关键点。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
三 .<strong>Spark Streaming另类在线实验</strong></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
       我们在研究Spark Streaming的过程中，会有困惑的事情：如何清晰的看到数据的流入、被处理的过程？</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
       使用一个小技巧，通过调节放大Batch Interval的方式，来降低批处理次数，以方便看清楚各个环节。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
       我们从已写过的广告点击的在线黑名单过滤的Spark Streaming应用程序入手。</p>
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">1. 案例代码 :</span><br><p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></p>
<pre style="line-height:19.2px;font-family:'Courier New' !important;"><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.spark.SparkConf  
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.spark.streaming.StreamingContext  
</span><span style="color:rgb(0,0,255);line-height:1.5 !important;">import</span><span style="line-height:1.5 !important;"> org.apache.spark.streaming.Seconds  

</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">
* Created by hadoop on 2016/4/18.
* 背景描述 在广告点击计费系统中 我们在线过滤掉 黑名单的点击 进而保护广告商的利益
* 只有效的广告点击计费
* 新浪微博：</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"><u>http://www.weibo.com/ilovepains
</u></span><span style="color:rgb(0,128,0);line-height:1.5 !important;">* 邮   箱<span style="line-height:1.5 !important;">: DT_Spark@126.com</span>
*/</span><span style="line-height:1.5 !important;">
object OnlineBlackListFilter {  
    def main(args: Array[String]){  
      </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">  
       * 第1步：创建Spark的配置对象SparkConf，设置Spark程序的运行时的配置信息，  
       * 例如说通过setMaster来设置程序要链接的Spark集群的Master的URL，如果设置  
       * 为local，则代表Spark程序在本地运行，特别适合于机器配置条件非常差（例如  
       * 只有1G的内存）的初学者。  
       </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span>  
      <span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 创建SparkConf对象  </span>
      val conf = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span><span style="line-height:1.5 !important;"> SparkConf()  
      </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 设置应用程序的名称，在程序运行的监控界面可以看到名称  </span>
      conf.setAppName("OnlineBlackListFilter"<span style="line-height:1.5 !important;">)  
      </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 此时，程序在Spark集群  </span>
      conf.setMaster("spark://Master:7077"<span style="line-height:1.5 !important;">)  
  
      val ssc </span>= <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> StreamingContext(conf, Seconds(30<span style="line-height:1.5 !important;">))  
  
      </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">  
       * 黑名单数据准备，实际上黑名单一般都是动态的，例如在Redis或者数据库中，  
       * 黑名单的生成往往有复杂的业务逻辑，具体情况算法不同，  
       * 但是在Spark Streaming进行处理的时候每次都能够访问完整的信息。  
       </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">  
      val blackList </span>= Array(("Spy", <span style="color:rgb(0,0,255);line-height:1.5 !important;">true</span>),("Cheater", <span style="color:rgb(0,0,255);line-height:1.5 !important;">true</span><span style="line-height:1.5 !important;">))  
      val blackListRDD </span>= ssc.sparkContext.parallelize(blackList, 8<span style="line-height:1.5 !important;">)  
  
      val adsClickStream </span>= ssc.socketTextStream("Master", 9999<span style="line-height:1.5 !important;">)  
  
      </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">  
       * 此处模拟的广告点击的每条数据的格式为：time、name  
       * 此处map操作的结果是name、（time，name）的格式  
       </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">  
      val adsClickStreamFormatted </span>= adsClickStream.map { ads =&gt; (ads.split(" ")(1<span style="line-height:1.5 !important;">), ads) }  
      adsClickStreamFormatted.transform(userClickRDD </span>=&gt;<span style="line-height:1.5 !important;"> {  
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 通过leftOuterJoin操作既保留了左侧用户广告点击内容的RDD的所有内容，  
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">//</span><span style="color:rgb(0,128,0);line-height:1.5 !important;"> 又获得了相应点击内容是否在黑名单中  </span>
        val joinedBlackListRDD =<span style="line-height:1.5 !important;"> userClickRDD.leftOuterJoin(blackListRDD)  
  
        </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">  
         * 进行filter过滤的时候，其输入元素是一个Tuple：（name,((time,name), boolean)）  
         * 其中第一个元素是黑名单的名称，第二元素的第二个元素是进行leftOuterJoin的时候是否存在的值。  
         * 如果存在的话，表面当前广告点击是黑名单，需要过滤掉，否则的话是有效点击内容；  
         </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">  
        val validClicked </span>= joinedBlackListRDD.filter(joinedItem =&gt;<span style="line-height:1.5 !important;"> {  
          </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">if</span>(joinedItem._2._2.getOrElse(<span style="color:rgb(0,0,255);line-height:1.5 !important;">false</span><span style="line-height:1.5 !important;">))  
          {  
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">false</span><span style="line-height:1.5 !important;">  
          } </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">else</span><span style="line-height:1.5 !important;"> {  
            </span><span style="color:rgb(0,0,255);line-height:1.5 !important;">true</span><span style="line-height:1.5 !important;">  
          }  
  
        })  
  
        validClicked.map(validClick </span>=&gt;<span style="line-height:1.5 !important;"> {validClick._2._1})  
      }).print  
  
      </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">/**</span><span style="color:rgb(0,128,0);line-height:1.5 !important;">  
       * 计算后的有效数据一般都会写入Kafka中，下游的计费系统会从kafka中pull到有效数据进行计费  
       </span><span style="color:rgb(0,128,0);line-height:1.5 !important;">*/</span><span style="line-height:1.5 !important;">  
      ssc.start()  
      ssc.awaitTermination()
    }  
} </span></pre>
<br><p></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">2. 
 把程序的Batch Interval设置成300秒:</span><br></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span class="cnblogs_code" style="line-height:18px;border:1px solid rgb(204,204,204);overflow:auto;font-family:'Courier New' !important;background-color:rgb(245,245,245);">val ssc = <span style="color:rgb(0,0,255);line-height:1.5 !important;">new</span> StreamingContext(conf, Seconds(300))</span><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"> </span><br></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
3.  重新生成一下jar包 。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　4.  启动Spark的History Server，打开数据发送的端口 ： nc -lk 9999</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　5.  用spark-submit运行前面生成的jar包 。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　6.  在数据发送端口输入若干数据，比如：</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　　　1375864674543 Tom<br>
　　　　1375864674553 Spy<br>
　　　　1375864674571 Andy<br>
　　　　1375864688436 Cheater<br>
　　　　1375864784240 Kelvin<br>
　　　　1375864853892 Steven<br>
　　　　1375864979347 John</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">7. 打开浏览器，看History Server的日志信息：</span><br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503103115107-2038209016.png"><br></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">点击最新的应用，看我们目前运行的应用程序中有些什么Job：</span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503103521372-588199170.png"><br></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
总共竟然有5个Job。这完全不是我们此前做Spark SQL之类的应用程序时看到的样子。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
我们接下来看一看这些Job的内容，主要揭示一些现象，不会做完全深入的剖析，只是为了先让大家进行一些思考。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
 Job 0：此Job不体现我们的业务逻辑代码。这个Job是出于对后面计算的负载均衡的考虑。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503104014232-894429959.png"><br></p>
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">Job 0包含有Stage 0、Stage 1。随便看一个Stage，比如Stage 1。看看其中的Aggregated Metrics by Executor部分：</span><br><p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503104133279-181226598.png"><br></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
发现此Stage在所有Executor上都存在。　</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
Job 1：运行时间比较长，耗时1.5分钟。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503104503794-1305128388.png"><br></p>
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">点击Stage 2的链接，进去看看Aggregated Metrics By Executor部分：</span><br><p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503105325294-1635806919.png"><br></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
可以知道，Stage 2只在Worker4上的一个Executor执行，而且执行了1.5分钟。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
是否会觉得奇怪：从业务处理的角度看，我们发送的那么一点数据，没有必要去启动一个运行1.5分钟的任务吧。那这个任务是做什么呢？</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
 从DAG Visualization部分，就知道此Job实际就是启动了一个接收数据的Receiver：</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503105514294-1948273183.png"><br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">原来Receiver是通过一个Job来启动的。那肯定有一个Action来触发它。</span><br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">看看Tasks部分：</span><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503105624794-807370631.png"><br></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
只有一个Worker运行此Job。是用于接收数据。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
Locality Level是PROCESS_LOCAL，原来是内存节点。所以，默认情况下，数据接收不会使用磁盘，而是直接使用内存中的数据。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
看来，Spark Streaming应用程序启动后，自己会启动一些Job。默认启动了一个Job来接收数据，为后续处理做准备。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
重要启示：一个Spark应用程序中可以启动很多Job，而这些不同的Job之间可以相互配合。这一认识为我们写复杂Spark程序奠定了良好的基础。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">Job 2：看Details可以发现有我们程序的主要业务逻辑，体现在Stag 3、Stag4、Stag 5中。</span><br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503105824357-860139456.png"><br></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">我们看Stag3、Stage4的详情，可以知道这2个Stage都是用4个Executor执行的。所有数据处理是在4台机器上进行的。</span><br></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503110017513-1118686076.png"><br></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">Stag
 5只在Worker4上。这是因为这个Stage有Shuffle操作。</span><br></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503110128904-849501780.png"><br></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">　Job3：有Stage
 6、Stage 7、Stage 8。其中Stage 6、Stage 7被跳过。</span><br></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503110254044-1022615421.png"><br></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">看看Stage
 8的Aggregated Metrics by Executor部分。可以看到，数据处理是在4台机器上进行的：</span><br></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503110419716-68929394.png"><br></span></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">Job4：也体现了我们应用程序中的业务逻辑
 。有Stage 9、Stage 10、Stage 11。其中Stage 9、Stage 10被跳过。</span><br></span></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503110531607-267809828.png"><br></span></span></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">看看Stage
 11的详情。可以看到，数据处理是在Worker4之外的其它3台机器上进行的：</span><br></span></span></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503110632263-713116142.png"><br></span></span></span></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></span></span></span></span></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
综合以上的现象可以知道，Spark Streaming的一个应用中，运行了这么多Job，远不是我们从网络博客或者书籍上看的那么简单。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
我们有必要通过这些现象，反过来回溯去寻根问源。不过这次暂不做深入分析。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
我们的神奇之旅才刚刚开始。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="line-height:24px;font-size:16px;"><strong>四.  瞬间理解Spark Streaming本质</strong></span></span><br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="line-height:24px;font-size:16px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503113851747-1972451693.png"><br></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><span style="line-height:24px;font-size:16px;"></span></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
以上的连续4个图，分别对应以下4个段落的描述：</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　Spark Streaming接收Kafka、Flume、HDFS和Kinesis等各种来源的实时输入数据，进行处理后，处理结果保存在HDFS、Databases等各种地方。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　    Spark Streaming接收这些实时输入数据流，会将它们按批次划分，然后交给Spark引擎处理，生成按照批次划分的结果流。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　Spark Streaming提供了表示连续数据流的、高度抽象的被称为离散流的DStream。DStream本质上表示RDD的序列。任何对DStream的操作都会转变为对底层RDD的操作。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　Spark Streaming使用数据源产生的数据流创建DStream，也可以在已有的DStream上使用一些操作来创建新的DStream。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
在我们前面的实验中，每300秒会产生一批数据，基于这批数据会生成RDD，进而触发Job，执行处理。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　DStream是一个没有边界的集合，没有大小的限制。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　DStream代表了时空的概念。随着时间的推移，里面不断产生RDD。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　锁定到时间段后，就是空间的操作。也就是对本时间段的对应批次的数据的处理。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　下面用实例来讲解数据处理过程。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
　　数据处理会有若干个对DStream的操作，这些操作之间的依赖关系，构成了DStreamGraph。如以下图例所示：　　　</p>
<img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503114111372-842591203.png"><br><p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">上图中每个foreach都会触发一个作业，就会顺着依赖从后往前回溯，形成DAG，如下图所示：</span><br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503114201310-1852943945.png"><br></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"></span></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
空间维度确定之后，随着时间不断推进，会不断实例化RDD Graph，然后触发Job去执行处理。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
 现在再去读官方的Spark Streaming的文档，就好理解多了。</p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<img alt="" src="http://images2015.cnblogs.com/blog/948437/201605/948437-20160503114300294-2076880567.png"><br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">看来我们的学习，将从Spark Streaming的现象开始，深入到Spark Core和Spark Streaming的本质。</span><br><br><br><br><br><br></p>
<p style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;">
<span style="line-height:20.8px;"><span style="color:rgb(75,75,75);font-family:georgia, verdana, Arial, helvetica, 'sans-seriff';font-size:13px;line-height:20.8px;"><br></span></span></p>
            </div>
                </div>
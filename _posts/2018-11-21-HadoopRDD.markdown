---
layout:     post
title:      HadoopRDD
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:26px;background-color:rgb(255,255,255);">
Spark经常需要从hdfs读取文件生成RDD，然后进行计算分析。这种从hdfs读取文件生成的RDD就是HadoopRDD。那么HadoopRDD的分区是怎么计算出来的？如果从hdfs读取的文件非常大，如何高效的从hdfs加载文件生成HadoopRDD呢？本篇文章探讨这两个问题。</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
    SparkContext.objectFile方法经常用于从hdfs加载文件，从加载hdfs文件到生成HadoopRDD的函数调用过程为：</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
SparkContext.objectFile-&gt;<br></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span>SparkContext.</span>sequenceFile-&gt;</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span>SparkContext.</span>hadoopFile-&gt;</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span></span>new HadoopRDD</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
SparkContext.objectFile方法定义如下：</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);color:rgb(79,79,79);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);border-right:1px solid rgb(231,229,220);">
<strong>[java]</strong> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="ViewSource" title="view plain" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="CopyToClipboard" title="copy" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">copy</a></span>
<div style="width:16px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" class="dp-j" style="margin-left:0px;border-top:none;border-right:1px solid rgb(231,229,220);border-bottom:none;border-left:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span style="border:none;background-color:inherit;">def objectFile[T: ClassTag](  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      path: String,  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      minPartitions: Int = defaultMinPartitions): RDD[T] = withScope {  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    assertNotStopped()  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    sequenceFile(path, classOf[NullWritable], classOf[BytesWritable], minPartitions)  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      .flatMap(x =&gt; Utils.deserialize[Array[T]](x._2.getBytes, Utils.getContextOrSparkClassLoader))  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  }  </span></li></ol></div>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);color:rgb(79,79,79);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);border-right:1px solid rgb(231,229,220);">
<strong>[java]</strong> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="ViewSource" title="view plain" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="CopyToClipboard" title="copy" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">copy</a></span>
<div style="width:16px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" class="dp-j" style="margin-left:0px;border-top:none;border-right:1px solid rgb(231,229,220);border-bottom:none;border-left:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span style="border:none;background-color:inherit;">def defaultMinPartitions: Int = math.min(defaultParallelism, </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">2</span><span style="border:none;background-color:inherit;">)  </span></span></li></ol></div>
<span style="color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);">从以上代码可知如果不设置加载的hdfs文件生成的HadoopRDD的分区个数，默认最小分区个数是2</span>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
SparkContext.objectFile方法的minPartitions参数没有改变，一直传递到HadoopRDD的构造函数<br></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<br></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
HadoopRDD.getPartitions方法用于创建HadoopRDD的分区。</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
在本篇文章中，以如下语句加载hdfs文件创建HadoopRDD，创建的HadoopRDD分区个数最小是24个</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);color:rgb(79,79,79);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);border-right:1px solid rgb(231,229,220);">
<strong>[java]</strong> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="ViewSource" title="view plain" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="CopyToClipboard" title="copy" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">copy</a></span>
<div style="width:16px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" class="dp-j" style="margin-left:0px;border-top:none;border-right:1px solid rgb(231,229,220);border-bottom:none;border-left:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span style="border:none;background-color:inherit;">val login1RDD = sc.objectFile[String](</span><span class="string" style="border:none;color:rgb(0,0,255);background-color:inherit;">"/data/login/1448419800022"</span><span style="border:none;background-color:inherit;">, </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">24</span><span style="border:none;background-color:inherit;">)  </span></span></li></ol></div>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<br></p>
<span style="color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);">hdfs uri为:hdfs://ddos12/</span>
<p style="line-height:26px;background-color:rgb(255,255,255);">
hdfs上/data/login/1448419800022/目录内的文件情况，如下图所示：</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<img src="https://img-blog.csdn.net/20151126113101189?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;"><br></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
目录下存在12个文件，12个文件总共10974245字节，如果按照24个分区计算，<span style="font-weight:700;"><span style="font-weight:400;color:rgb(204,0,0);">平均一个分区是457260字节</span></span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
分区的 创建在HadoopRDD.getPartitions方法：</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;font-size:12px;background-color:rgb(231,229,220);color:rgb(79,79,79);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;background-color:rgb(248,248,248);border-left:3px solid rgb(108,226,108);border-right:1px solid rgb(231,229,220);">
<strong>[java]</strong> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="ViewSource" title="view plain" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">view
 plain</a><span class="tracking-ad"> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="CopyToClipboard" title="copy" style="background-color:inherit;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;">copy</a></span>
<div style="width:16px;z-index:99;">
</div>
<span class="tracking-ad"></span></div>
</div>
<ol start="1" class="dp-j" style="margin-left:0px;border-top:none;border-right:1px solid rgb(231,229,220);border-bottom:none;border-left:none;background-color:rgb(255,255,255);color:rgb(92,92,92);"><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span style="border:none;background-color:inherit;">override def getPartitions: Array[Partition] = {  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    val jobConf = getJobConf()  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// add the credentials here as this can be called before SparkContext initialized</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    SparkHadoopUtil.get.addCredentials(jobConf)  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    val inputFormat = getInputFormat(jobConf)  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);border:none;">if</span><span style="border:none;background-color:inherit;"> (inputFormat.isInstanceOf[Configurable]) {  </span></span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      inputFormat.asInstanceOf[Configurable].setConf(jobConf)  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    }  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/*</span> </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">    * 确定当前目录下有多少个分区，分区个数最小是minPartitions，以及每个分区多大，每个分区的数据所在的文件，分区数据在文件中的起始位置</span> </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">    * */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    val inputSplits = inputFormat.getSplits(jobConf, minPartitions)  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    val array = <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);border:none;">new</span><span style="border:none;background-color:inherit;"> Array[Partition](inputSplits.size)  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);border:none;">for</span><span style="border:none;background-color:inherit;"> (i &lt;- </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;"> until inputSplits.size) {  </span></span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/*创建分区*/</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">      array(i) = <span class="keyword" style="font-weight:700;background-color:inherit;color:rgb(0,102,153);border:none;">new</span><span style="border:none;background-color:inherit;"> HadoopPartition(id, i, inputSplits(i))  </span></span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    }  </span></li><li style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);background-color:rgb(248,248,248);line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">    array  </span></li><li class="alt" style="margin-left:40px;list-style-type:decimal;border-top:none;border-right:none;border-bottom:none;border-left:3px solid rgb(108,226,108);color:inherit;line-height:18px;list-style-position:outside !important;">
<span style="border:none;color:rgb(0,0,0);background-color:inherit;">  }  </span></li></ol></div>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<br></p>
<span style="color:rgb(79,79,79);text-align:justify;background-color:rgb(255,255,255);">根据上面的文件，创建的分区大概信息如下：</span>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<img src="https://img-blog.csdn.net/20151126140212893?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;"><br><br></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<span><span style="color:rgb(204,0,0);">可见总共创建了25个分区，通过上图可得出结论在切分hdfs目录中的文件的时候，对每个文件按照分区平均长度457260进行切分，每个分区的长度不能大于457260.比如说part-00009文件，切分成了3个分区，最后一个分区的长度只有62107字节</span></span></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
下图表示了一个index为1分区的详细信息：</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<img src="https://img-blog.csdn.net/20151126141008216?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;margin-left:0px;"><br></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<br></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<br></p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
可见里面包含了分区所在的hdfs的文件，以及分区数据在这个文件的起始位置，分区的大小</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
但是默认情况下，HadoopRDD一个分区最大是128M，假设一个文件大小是1G+10M，但是我设置4个分区，那么在计算分区个数的时候会计算为9个分区，最后一个分区大小为10M。</p>
<p style="line-height:26px;background-color:rgb(255,255,255);">
<br></p>
<br><p style="line-height:26px;background-color:rgb(255,255,255);">
<br></p>
<div><br></div>
            </div>
                </div>
---
layout:     post
title:      HadoopRDD 的生成过程解析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
 <a href="http://lib.csdn.net/base/spark" rel="nofollow" class="replace_word" title="Apache Spark知识库" style="color:rgb(223,52,52);text-decoration:none;font-weight:bold;">Spark</a>经常需要从hdfs读取文件生成RDD，然后进行计算分析。这种从hdfs读取文件生成的RDD就是HadoopRDD。那么HadoopRDD的分区是怎么计算出来的？如果从hdfs读取的文件非常大，如何高效的从hdfs加载文件生成HadoopRDD呢？本篇文章探讨这两个问题。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
    SparkContext.objectFile方法经常用于从hdfs加载文件，从加载hdfs文件到生成HadoopRDD的函数调用过程为：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
SparkContext.objectFile-&gt;<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span>SparkContext.</span>sequenceFile-&gt;</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span>SparkContext.</span>hadoopFile-&gt;</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span></span>new HadoopRDD</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
SparkContext.objectFile方法定义如下：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[java]</strong> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span> </span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1580741" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a></span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1580741/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a></span></div>
</div>
<ol start="1" class="dp-j" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">def objectFile[T: ClassTag](  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      path: String,  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      minPartitions: Int = defaultMinPartitions): RDD[T] = withScope {  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    assertNotStopped()  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    sequenceFile(path, classOf[NullWritable], classOf[BytesWritable], minPartitions)  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      .flatMap(x =&gt; Utils.deserialize[Array[T]](x._2.getBytes, Utils.getContextOrSparkClassLoader))  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  }  </span></li></ol></div>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[java]</strong> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span> </span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1580741" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a></span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1580741/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a></span></div>
</div>
<ol start="1" class="dp-j" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">def defaultMinPartitions: Int = math.min(defaultParallelism, </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">2</span><span style="border:none;background-color:inherit;">)  </span></span></li></ol></div>
<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">从以上代码可知如果不设置加载的hdfs文件生成的HadoopRDD的分区个数，默认最小分区个数是2</span>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
SparkContext.objectFile方法的minPartitions参数没有改变，一直传递到HadoopRDD的构造函数<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
HadoopRDD.getPartitions方法用于创建HadoopRDD的分区。</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
在本篇文章中，以如下语句加载hdfs文件创建HadoopRDD，创建的HadoopRDD分区个数最小是24个</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[java]</strong> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span> </span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1580741" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a></span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1580741/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a></span></div>
</div>
<ol start="1" class="dp-j" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">val login1RDD = sc.objectFile[String](</span><span class="string" style="border:none;color:#0000FF;background-color:inherit;">"/data/login/1448419800022"</span><span style="border:none;background-color:inherit;">, </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">24</span><span style="border:none;background-color:inherit;">)  </span></span></li></ol></div>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">hdfs uri为:hdfs://ddos12/</span>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
hdfs上/data/login/1448419800022/目录内的文件情况，如下图所示：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<img src="https://img-blog.csdn.net/20151126113101189?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
目录下存在12个文件，12个文件总共10974245字节，如果按照24个分区计算，<strong><span style="color:rgb(204,0,0);">平均一个分区是457260字节</span></strong></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
分区的 创建在HadoopRDD.getPartitions方法：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<div class="dp-highlighter bg_java" style="font-family:Consolas, 'Courier New', Courier, mono, serif;color:rgb(51,51,51);line-height:26px;background-color:rgb(231,229,220);">
<div class="bar">
<div class="tools" style="font-size:9px;line-height:normal;font-family:Verdana, Geneva, Arial, Helvetica, sans-serif;color:#C0C0C0;border-left-width:3px;border-left-style:solid;border-left-color:rgb(108,226,108);background-color:rgb(248,248,248);">
<strong>[java]</strong> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="ViewSource" title="view plain" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">view
 plain</a><span> <a href="http://blog.csdn.net/u012684933/article/details/50053095#" rel="nofollow" class="CopyToClipboard" title="copy" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;text-indent:-2000px;background-color:inherit;">copy</a></span>
<div style="width:18px;z-index:99;">
</div>
<span> </span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1580741" rel="nofollow" title="在CODE上查看代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/CODE_ico.png" width="12" height="12" alt="在CODE上查看代码片" style="border:none;"></a></span><span class="tracking-ad"><a href="https://code.csdn.net/snippets/1580741/fork" rel="nofollow" title="派生到我的代码片" style="color:rgb(160,160,160);text-decoration:none;border:none;font-size:9px;display:inline-block;width:16px;background-color:inherit;"><img src="https://code.csdn.net/assets/ico_fork.svg" width="12" height="12" alt="派生到我的代码片" style="border:none;"></a></span></div>
</div>
<ol start="1" class="dp-j" style="border:none;color:rgb(92,92,92);background-color:rgb(255,255,255);"><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span style="border:none;background-color:inherit;">override def getPartitions: Array[Partition] = {  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    val jobConf = getJobConf()  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">// add the credentials here as this can be called before SparkContext initialized</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    SparkHadoopUtil.get.addCredentials(jobConf)  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    val inputFormat = getInputFormat(jobConf)  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);font-weight:bold;background-color:inherit;">if</span><span style="border:none;background-color:inherit;"> (inputFormat.isInstanceOf[Configurable]) {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      inputFormat.asInstanceOf[Configurable].setConf(jobConf)  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    }  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/*</span> </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">    * 确定当前目录下有多少个分区，分区个数最小是minPartitions，以及每个分区多大，每个分区的数据所在的文件，分区数据在文件中的起始位置</span> </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;"><span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">    * */</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    val inputSplits = inputFormat.getSplits(jobConf, minPartitions)  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    val array = <span class="keyword" style="border:none;color:rgb(0,102,153);font-weight:bold;background-color:inherit;">new</span><span style="border:none;background-color:inherit;"> Array[Partition](inputSplits.size)  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    <span class="keyword" style="border:none;color:rgb(0,102,153);font-weight:bold;background-color:inherit;">for</span><span style="border:none;background-color:inherit;"> (i &lt;- </span><span class="number" style="border:none;color:rgb(192,0,0);background-color:inherit;">0</span><span style="border:none;background-color:inherit;"> until inputSplits.size) {  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">      <span class="comment" style="border:none;color:rgb(0,130,0);background-color:inherit;">/*创建分区*/</span><span style="border:none;background-color:inherit;">  </span></span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">      array(i) = <span class="keyword" style="border:none;color:rgb(0,102,153);font-weight:bold;background-color:inherit;">new</span><span style="border:none;background-color:inherit;"> HadoopPartition(id, i, inputSplits(i))  </span></span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">    }  </span></li><li style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;line-height:18px;background-color:rgb(248,248,248);">
<span style="border:none;color:#000000;background-color:inherit;">    array  </span></li><li class="alt" style="border-style:none none none solid;border-left-width:3px;border-left-color:rgb(108,226,108);list-style:outside;color:inherit;line-height:18px;">
<span style="border:none;color:#000000;background-color:inherit;">  }  </span></li></ol></div>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<span style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">根据上面的文件，创建的分区大概信息如下：</span>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<img src="https://img-blog.csdn.net/20151126140212893?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br><br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<span style="color:rgb(204,0,0);">可见总共创建了25个分区，通过上图可得出结论在切分hdfs目录中的文件的时候，对每个文件按照分区平均长度457260进行切分，每个分区的长度不能大于457260.比如说part-00009文件，切分成了3个分区，最后一个分区的长度只有62107字节</span></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
下图表示了一个index为1分区的详细信息：</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<img src="https://img-blog.csdn.net/20151126141008216?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:none;"><br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br><br><br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<br></p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
可见里面包含了分区所在的hdfs的文件，以及分区数据在这个文件的起始位置，分区的大小</p>
<p style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
但是默认情况下，HadoopRDD一个分区最大是128M，假设一个文件大小是1G+10M，但是我设置4个分区，那么在计算分区个数的时候会计算为9个分区，最后一个分区大小为10M。</p>
            </div>
                </div>
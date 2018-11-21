---
layout:     post
title:      Spark Core ：RDD与Spark流水线编程模型
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<ul style="margin-left:0px;color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;"><li style="margin-left:0px;">学会使用Spark中创建RDD的两种方法</li><li style="margin-left:0px;">列出创建Spark程序的步骤</li><li style="margin-left:0px;">结合本文内容解释“不可变性”</li><li style="margin-left:0px;">能将Spark程序翻译成“变换”和“行为”的流水线</li></ul><h4><span style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;color:#000000;"><span style="font-size:14px;">RDD的创建方法</span></span></h4>
<p style="color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">
Driver Program 是Spark程序的起点，它把RDD分发到集群并保证RDD的转化和动作的执行。Driver Program 中包含一个Spark Context，而每个Work Node中包含一个Spark Executor。RDD既可以从文件或集群中创建，也可以从数据结构中创建。</p>
<p style="color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">
从文件创建时，使用sc.textFile()方法，如</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);font-family:'Courier New';font-size:12px;border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);">
<pre style="font-family:'Courier New';font-size:12px;"><span style="color:rgb(0,128,0);">#</span><span style="color:rgb(0,128,0);">从hdfs内的文件创建</span>
lines = sc.textFile(<span style="color:rgb(128,0,0);">"</span><span style="color:rgb(128,0,0);">hdfs:/root/words.txt</span><span style="color:rgb(128,0,0);">"</span><span>)
</span><span style="color:rgb(0,128,0);">#</span><span style="color:rgb(0,128,0);">从本地文件创建</span>
lines = sc.textFile(<span style="color:rgb(128,0,0);">"</span><span style="color:rgb(128,0,0);">/root/words.txt</span><span style="color:rgb(128,0,0);">"</span>)</pre>
</div>
<p style="color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">
从数据结构创建时，使用sc.parallelize()方法，如</p>
<div class="cnblogs_code" style="background-color:rgb(245,245,245);font-family:'Courier New';font-size:12px;border:1px solid rgb(204,204,204);overflow:auto;color:rgb(0,0,0);">
<pre style="font-family:'Courier New';font-size:12px;"><span style="color:rgb(0,128,0);">#</span><span style="color:rgb(0,128,0);">从list创建</span>
lines = sc.parallelize([<span style="color:rgb(128,0,0);">"</span><span style="color:rgb(128,0,0);">big</span><span style="color:rgb(128,0,0);">"</span>,<span style="color:rgb(128,0,0);">"</span><span style="color:rgb(128,0,0);">data</span><span style="color:rgb(128,0,0);">"</span><span>])

</span><span style="color:rgb(0,128,0);">#</span><span style="color:rgb(0,128,0);">并发地将[0,1,2,3...9]分割成三部分</span>
numbers = sc.parallelize(range(10),3<span>)
</span><span style="color:rgb(0,128,0);">#</span><span style="color:rgb(0,128,0);">结果为[0,1,2],[3,4,5],[6,7,8,9]</span><span style="color:rgb(0,128,0);">
#</span><span style="color:rgb(0,128,0);">合并</span>
<span>number.collect()
</span><span style="color:rgb(0,128,0);">#</span><span style="color:rgb(0,128,0);">结果为[0,1,2,3...9]</span></pre>
</div>
<h4 style="color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">
流水线编程模型</h4>
<p style="color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">
Spark中有两种操作，Transformation和Action。Spark程序有一种叫lazy evaluation的执行机制，即并不立即执行Transformation，等到一个Action执行时再一并执行Transformation。在<a href="http://blog.csdn.net/zhouweiyu/article/details/78982610" rel="nofollow">前一篇大数据处理流水线</a>中有提到一些常见的Transformation,如下图中的filter、map和reduce。相应的，collect和saveAsTextFile都是Action。</p>
<p style="color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">
<img src="http://images2017.cnblogs.com/blog/1298721/201801/1298721-20180109201221879-297863743.png" alt="" style="border:0px;"><img src="http://images2017.cnblogs.com/blog/1298721/201801/1298721-20180109201235941-1136810824.png" alt="" style="border:0px;"></p>
<p style="color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">
<img src="http://images2017.cnblogs.com/blog/1298721/201801/1298721-20180109201254894-646790177.png" alt="" style="border:0px;"><img src="http://images2017.cnblogs.com/blog/1298721/201801/1298721-20180109201302519-2080033826.png" alt="" style="border:0px;"></p>
<h4 style="color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;">
 RDD的不可变性immutable</h4>
<div>immutable意味着RDD是不可变的，只读的。<a href="http://blog.csdn.net/zhouweiyu/article/details/78955317" rel="nofollow">前面</a>提到<span style="font-family:'microsoft yahei';background-color:rgb(255,255,255);">Spark会追踪每个文件分片的历史，当某一个文件分片损坏或丢失时，Spark会根据该文件分片所需的文件重新计算、创建该分片。
 Immutable对追踪应用到数据集上的所有操作很有必要，它提供了维护一个RDD的线性链的能力。</span></div>
<div>
<ul style="margin-left:0px;color:rgb(0,0,0);font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;"><li style="margin-left:0px;">结合本文内容解释“不可变性”</li></ul></div>
            </div>
                </div>
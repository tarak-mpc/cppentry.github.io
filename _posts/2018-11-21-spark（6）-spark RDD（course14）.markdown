---
layout:     post
title:      spark（6）-spark RDD（course14）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/answer100answer/article/details/78743053				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:30px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);">
1. Spark RDD</p>
<p style="font-size:30px;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);">
</p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);font-size:24px;">
1.1 spark RDD简介</h2>
<div><span style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">       <span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">RDD是spark的基石</span>。</span><br></div>
<div><span style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">       <span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"> 
       RDD提供了通用的抽象。</span><br></span></div>
<div><span style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">       <span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"> 
       现在Spark有5个子框架 <span style="background-color:#f0f0ee;">SparkStreaming</span>、<span style="background-color:#f0f0ee;">SparkSQL</span>、<span style="background-color:#f0f0ee;">SparkML</span>、<span style="background-color:#f0f0ee;">GraphX</span>、<span style="background-color:#f0f0ee;">SparkR</span></span><br></span></div>
<div><span style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"></span></span>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<strong><br></strong></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<strong>RDD弹性：</strong></p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="line-height:1.8;"></span>1. 自动的进行内存和磁盘数据存储的切换</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="line-height:1.8;"></span>2. 基于lineage的高效容错</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="line-height:1.8;"></span>3. task失败会自动进行特定次数的重试</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="line-height:1.8;"></span>4. stage如果失败会自动进行特定次数的重试而且重试时只会试算失败的分片。</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="line-height:1.8;"></span>5. checkpoint和persist，是效率和容错的延伸。</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="line-height:1.8;"></span>6. 数据调度弹性：DAG TASK和资源管理无关</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="line-height:1.8;"></span>7. 数据分片的高度弹性</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
</p>
<h2 style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);font-size:24px;">
1.1 数据分片partition</h2>
<div>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
        计算过程中有很多数据碎片，那么Partition就会非常小，每个Partition都会由一个线程处理，就会降低处理效率。这时就要考虑把小文件合并成一个大文件。另外一个方面，如果内存不多，而每个Partition比较大（数据Block大），就要考虑变成更小的分片，Sparke有更多的处理批次但不会出现OOM。</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
        所以说根据数据分片的大小来提高并行度或降低并行度也是Spark高度弹性的表现。同时需要指出的是，不管是提高并行度还是降低并行度，仍具有数据本地性。当然，提高并行度还是降低度行度都是人工通过代码来调整的。</p>
<div><br></div>
        <span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">coalesce</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;line-height:1.8;">假设有一百万个数据分片，每个数据分片都非常小（1K或10KB），如果要把数据分片调整为一万个，如果使用<span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"> </span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;background-color:rgb(240,240,238);"><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">repartition</span></span>，就需要Shuffle，这是不可取的，<span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">而要调用<span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;background-color:rgb(240,240,238);"><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">coalesce</span></span>，coalesce默认的shuffle</span><span style="color:rgb(69,69,69);font-size:16px;font-family:'宋体';">为</span><span style="color:rgb(69,69,69);font-size:16px;font-family:Consolas;">false</span>。</span></div>
<div><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">RDD.scala</span>源码：</span></div>
<div><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br></span></div>
<div><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"></span>
<pre style="font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;line-height:1.42857;color:rgb(51,51,51);background-color:rgb(245,245,245);border:1px solid rgb(204,204,204);">def repartition(numPartitions: Int)(implicit ord: Ordering[T] = null): RDD[T] = withScope {
    coalesce(numPartitions, <strong>shuffle = true</strong>)
}</pre><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><span style="color:rgb(51,51,51);font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;">
def coalesce(numPartitions: Int, <strong>shuffle: Boolean = false</strong>)(implicit ord: Ordering[T] = null)</span></p>: RDD[T] = withScope {
     ... ...
<div><br></div>
<div>
<h2 style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);font-size:24px;">
1.2 缓存cache</h2>
<br></div>
          <span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">RDD<span style="line-height:1.8;">允许用户在执行多个查询时显式地将工作集缓存在内存中，如果有其他人执行同样的查询的话就可以存入工作集，这就极大地提高了效率。特别是在数据仓库中，如果有一千人做同样的查询，第一个人在查询之后，每二个人查询时就可以直接从缓存中取结果。退一步说，如果一千人执行的查询只有前</span><span>10</span><span>个步骤是一样的，如果第一个人计算完成后，后面的人的前</span><span>10</span><span>步就不需要再计算了。这就极大地提升了查询速度。</span>如果是<span>Hadoop</span><span>的话一千个人执行同样的查询，就需要重复计算一千次。</span></span></div>
<br><p><span><span style="line-height:1.8;font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;color:#454545;"><span style="font-size:16px;">        缓存随时可以清理掉，如果内存或磁盘不足就需要根据优先度将不常使用的缓存内容清理掉。</span></span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">RDD</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">的</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">cache</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">是直接放在内存中的。</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">RDD</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">的</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">cache</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">通过</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">checkpoint</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">来清除。但</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">checkpoint</span><span><span style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;color:#454545;"><span style="font-size:16px;">是重量级的。<span>SparkStreaming<span>经常进行</span><span>Checkpoint</span><span>，原因是经常要用到以前的内容。假设要统计一段时间的内容，那就需要以前的数据。</span></span></span></span></span></span></p>
<p><span><span style="line-height:1.8;"><span><span><span style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;color:#454545;"><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;"> 
       如果</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">Spark</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">的一个</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">Stage</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">中有一千个步骤的话，默认只会产生一次结果。如果是</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">HadoopMR</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">就会产生</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">999</span></span><span><span style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;color:#454545;"><span style="font-size:16px;">次中间结果，如果数据量很大的话，内存和磁盘都可能存不下。</span></span></span></span></span></span></span></p>
<p><span style="line-height:1.8;font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">        Spark</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">本身就是</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">RDD</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">的内容，</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">RDD</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">是只读分区的集合。</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">RDD</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">是数据集合，可以简单理解为</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">List</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">或</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">Array</span><span style="font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">。</span></p>
<p><span style="line-height:1.8;font-size:16px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;">        Spark<span>的每一步操作都是对</span><span>RDD</span><span>进行操作，而</span><span>RDD</span><span>是只读分区的集合。</span></span></p>
<p><br><span style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;color:#454545;"></span></p>
<p><span><span><span><span><span style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;color:#454545;"><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><u><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">※ </span>由于每一次操作都是只读的，而操作会改变数据，那么产生中间结果怎么办？</u></span><br></span></span></span></span></span></p>
<p><span><span><span><span></span></span></span></span></p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
=&gt;<span style="line-height:1.8;">不能立即计算。这就是</span>lazy：不用时不算，用时才计算，所以不会产生中间结果</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
RDD的核心之一就是它的Lazy，因为这不计算，开始时只是对数据处理进行标记而已。例如WordCount中的map、flatmap其实并不计算数据，只是对数据操作的标记而已。<br></p>
<p><span><span><span><span><span style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;color:#454545;"><u><br></u></span></span></span></span></span></p>
<p><span><span><span style="font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;color:#454545;"><span style="font-size:16px;"><span><span></span></span></span></span></span></span></p>
<pre style="background-color:rgb(245,245,245);font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;line-height:1.42857;color:rgb(51,51,51);border:1px solid rgb(204,204,204);">/**
 *  Return a new RDD by first applying a function to all elements of this
 *  RDD, and then flattening the results.
 */

def flatMap[U: ClassTag](f: T =&gt; TraversableOnce[U]): RDD[U] = withScope {
  val cleanF = sc.clean(f)
  new MapPartitionsRDD[U, T](this, (context, pid, iter) =&gt; iter.flatMap(cleanF))
}</pre>
<br><span style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">        可以看出在flatMap</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">中创建了一个</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">MapPartitionsRDD</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">，但第一个参数是</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">this</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">，这个</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">this</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">是指它依赖的父</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">RDD</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">。每次创建新的</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">RDD</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">都会把父</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">RDD</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">作为第一个参数传入。而这只是对数据处理的标记而已。可以看出在flatMap<span>中创建了一个</span><span>MapPartitionsRDD</span><span>，但第一个参数是</span><span>this</span><span>，这个</span><span>this</span><span>是指它依赖的父</span><span>RDD</span><span>。每次创建新的</span><span>RDD</span><span>都会把父</span><span>RDD</span><span>作为第一个参数传入。而这只是对数据处理的标记而已。</span></span><br><p></p>
</div>
<h2 style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);font-size:24px;">
1.3 函数展开式回溯</h2>
<br><span style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">        每次创建新的</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">RDD</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">都会把父</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">RDD</span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">作为第一个参数传入，这就类比函数展开式操作：</span><br style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span></span>f(x)=x+2</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span></span>x=y+1</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span></span>y=z+3</p>
<p style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
<span style="line-height:1.8;"></span>执行时只是函数展开。</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
        所以RDD每次构建对象都依赖于父RDD，最后就是函数展开。所以如果一个Stage中有一千个步骤的话，不会产生999次中间结果。可以看出在flatMap<span>中创建了一个</span><span>MapPartitionsRDD</span><span>，但第一个参数是</span><span>this</span><span>，这个</span><span>this</span><span>是指它依赖的父</span><span>RDD</span><span>。每次创建新的</span><span>RDD</span><span>都会把父</span><span>RDD</span><span>作为第一个参数传入。而这只是对数据处理的标记而已</span><br></p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
        由于RDD是只读的，为了应对计算模型，RDD又是lazy级别的。每次操作都会产生RDD，每次构建新的RDD都是把父RDD作为第一个参数传入，这就构成了一个链条。在最后Action时才触发，这就构成了一个从后往前回溯的过程，其实就是函数展开的过程。</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
由于这种从后往前的回溯机制，Spark的容错的开销会非常低。</p>
<br><p>
<span style="text-decoration:underline;"><br><span style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><span>※ </span></span>以前有人说spark不适合大规模计算，当时确实有其道理，主要有两点原因：</span></span><br></p>
<br><p></p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
1）Spark一直基于内存迭代会消耗大量内存。例如如一千个步骤虽然不产生中间结果，但如果要复用别人的结果时就需要手动persist或cache，这确实非常消耗内存。</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
2）主要是因为shuffle机制。但现在Shuffle支持很多种机制，如hashShuffle、sortBasedShuffle、钨丝计划等，而且现在Shuffle只是一个接口，一个插件，可以自定义，所以可以应对任意规模的数据处理。</p>
<div><span style="line-height:1.8;"><span><br><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">Spark1.2以前确实有规模的限制，但1.3以后就无限制。<span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">因为Spark1.3引入了<strong>dataframe</strong>，这是里程碑式的。</span></span><br></span></span></div>
<div><span style="line-height:1.8;"><span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><br></span></span></span></span></div>
<div><span style="line-height:1.8;"><span><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;"><span style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">可以看出在flatMap<span>中创建了一个</span><span>MapPartitionsRDD</span><span>，但第一个参数是</span><span>this</span><span>，这个</span><span>this</span><span>是指它依赖的父</span><span>RDD</span><span>。每次创建新的</span><span>RDD</span><span>都会把父</span><span>RDD</span><span>作为第一个参数传入。而这只是对数据处理的标记而已</span><br></span></span></span></span>
<h2 style="line-height:1.8;font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-weight:500;line-height:1.1;color:rgb(49,126,172);font-size:24px;">
1.4 RDD与迭代器</h2>
<div>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
        如果想让Spark直接操作MYSQL的数据或者操作HBASE数据就需要复写RDD。</p>
<p style="line-height:1.8;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;font-size:16px;">
        RDD的数据分片上运行的计算逻辑都是一样的。对于每个计算逻辑都有计算函数compute</p>
</div>
<div><br><pre style="line-height:1.8;background-color:rgb(245,245,245);font-family:Menlo, Monaco, Consolas, 'Courier New', monospace;font-size:13px;line-height:1.42857;color:rgb(51,51,51);border:1px solid rgb(204,204,204);">def compute(split: Partition, context: TaskContext): Iterator[T]</pre>
<br></div>
</div>
<br>            </div>
                </div>
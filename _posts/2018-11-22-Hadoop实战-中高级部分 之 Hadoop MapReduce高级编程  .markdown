---
layout:     post
title:      Hadoop实战-中高级部分 之 Hadoop MapReduce高级编程  
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>
</p>
<div style="font-size:14px;">
<div><a href="http://sishuok.com/forum/blogPost/list/5833.html" rel="nofollow">Hadoop RestFul</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5936.html" rel="nofollow">Hadoop HDFS原理1</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5937.html" rel="nofollow">Hadoop HDFS原理2</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5938.html" rel="nofollow">Hadoop作业调优参数调整及原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5939.html" rel="nofollow">Hadoop HA</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5961.html" rel="nofollow">Hadoop MapReduce高级编程</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5963.html" rel="nofollow">Hadoop IO</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5965.html" rel="nofollow">Hadoop MapReduce工作原理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5966.html" rel="nofollow">Hadoop 管理</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5969.html" rel="nofollow">Hadoop 集群安装</a></div>
<div><a href="http://sishuok.com/forum/blogPost/list/5968.html" rel="nofollow">Hadoop RPC</a></div>
<div> </div>
</div>
<div style="font-size:14px;"><span class="bold" style="font-weight:bold;">第一部分：重要的组件</span></div>
<div style="font-size:14px;">
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">Combiner</span></div>
</div>
<div style="font-size:14px;">
<div class="O">•什么是Combiner</div>
<div class="O1">•combine函数把一个map函数产生的&lt;key,value&gt;对（多个key, value）合并成一个新的&lt;key2,value2&gt;. 将新的&lt;key2,value2&gt;作为输入到reduce函数中，其格式与reduce函数相同。</div>
<div class="O1">•这样可以有效的较少中间结果，减少网络传输负荷。</div>
<div class="O1"> </div>
<div class="O">•什么情况下可以使用Combiner</div>
<div class="O1">•可以对记录进行汇总统计的场景，如求和。</div>
<div class="O1">•求平均数的场景就不可以使用了</div>
<div class="O1">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Combiner</span>执行时机</span></div>
<div>
<div class="O">•运行combiner函数的时机有可能会是merge完成之前，或者之后，这个时机可以由一个参数控制，即 <strong>min.num.spill.for.combine</strong>（default 3）</div>
<div class="O">•当job中设定了combiner，并且spill数最少有3个的时候，那么combiner函数就会在merge产生结果文件之前运行</div>
<div class="O">•通过这样的方式，就可以在spill非常多需要merge，并且很多数据需要做conbine的时候，减少写入到磁盘文件的数据数量，同样是为了减少对磁盘的读写频率，有可能达到优化作业的目的。</div>
<div class="O">•Combiner也有可能不执行， Combiner会考虑当时集群的负载情况。</div>
<div class="O1">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Combiner</span>如何使用</span></div>
<div>
<div class="O">•代码示例</div>
<div class="O">•继承Reducer类</div>
<div class="O">public static class Combiner extends MapReduceBase implements</div>
<div class="O">           Reducer&lt;Text, Text, Text, Text&gt; {</div>
<div class="O">       public void reduce(Text key, Iterator&lt;Text&gt; values,</div>
<div class="O">               OutputCollector&lt;Text, Text&gt; output, Reporter reporter)</div>
<div class="O">               throws IOException {</div>
<div class="O">                 }</div>
<div class="O">    }</div>
<div class="O"> </div>
<div class="O">•配置作业时加入conf.setCombinerClass(Combiner.class)</div>
<div class="O"> </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">Partitioner</span></div>
<div>
<div class="O">•什么是Partitioner</div>
<div class="O1">•Mapreduce 通过Partitioner 对Key 进行分区，进而把数据按我们自己的需求来分发。</div>
<div class="O">•什么情况下使用Partitioner</div>
<div class="O1">•如果你需要key按照自己意愿分发，那么你需要这样的组件。</div>
<div class="O1">•例如：数据文件内包含省份，而输出要求每个省份输出一个文件。</div>
<div class="O">•框架默认的HashPartitioner</div>
<div class="O1">•public class HashPartitioner&lt;K, V&gt; extends Partitioner&lt;K, V&gt; {  <br><br>  /** Use {@link Object#hashCode()} to partition. */  <br>  public int getPartition(K key, V value,  <br>                          int numReduceTasks) {  <br>    return (key.hashCode() &amp; Integer.MAX_VALUE) % numReduceTasks;  <br>  } <br>} </div>
<div class="O1">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Partitioner</span>如何使用</span></div>
<div>
<div class="O">•实现Partitioner接口覆盖getPartition()方法</div>
<div class="O">•配置作业时加入conf.setPartitionerClass(MyPartitioner.class);</div>
<div class="O">•Partitioner示例</div>
<div class="O1">        public static class MyPartitioner implements Partitioner&lt;Text, Text&gt; {</div>
<div class="O1">           </div>
<div class="O1">         @Override <br>            public int getPartition(Text key, Text value, int numPartitions) {</div>
<div class="O1">             }</div>
<div class="O1"> </div>
<div class="O1">}</div>
<div class="O1">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Partitioner</span>需求示例</span></div>
<div>
<div class="O">•需求描述</div>
<div class="O1">•数据文件中含有省份</div>
<div class="O1">•需要相同的省份送到相同的Reduce里</div>
<div class="O1">•从而产生不同的文件</div>
<div class="O">•数据样例</div>
<div class="O1">•1 liaoning</div>
<div class="O1">•1 代表该省份有多少个直辖市</div>
<div class="O">•步骤</div>
<div class="O1">•实现Partitioner，覆盖getPartition</div>
<div class="O1">•根据省份字段进行切分</div>
<div class="O"> </div>
</div>
</div>
<div class="O"> 
<div class="O"><span class="bold" style="font-weight:bold;" lang="en-us" xml:lang="en-us">RecordReader</span></div>
</div>
</div>
<div>
<div class="O">•什么是RecordReader</div>
<div class="O1">•用于在分块中读取&lt;Key,Value&gt;对，也就是说每一次我们读取一条记录都会调用该类。</div>
<div class="O1">•主要是处理经过InputFormat分片完的数据 </div>
<div class="O">•什么时候使用RecordReader</div>
<div class="O1">•需要对输入的数据按自己的需求处理</div>
<div class="O1">•如：要求输入的key不是文件的偏移量而是文件的路径或者名字</div>
<div class="O1">
<div class="O">
<div>•系统默认为LineRecordReader</div>
<div>•按照每行的偏移量做为map输出时的key值，每行的内容作为map的value值，默认的分隔符是回车和换行。</div>
<div> </div>
</div>
</div>
<div class="O1">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">RecordReader</span>需求示例</span></div>
</div>
<div>
<div class="O">•需求</div>
<div class="O1">•更改map对应的输入的&lt;key,value&gt;值，key对应的文件的路径（或者是文件名），value对应的是文件的内容（content）。</div>
<div class="O">•步骤</div>
<div class="O1">•重写InputFormat不对文件切分</div>
<div class="O1">•重写RecordReader</div>
<div class="O1">•在配置作业时使用自定义的组件进行数据处理</div>
<div class="O"> </div>
</div>
</div>
</div>
<div class="O"> </div>
</div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第二部分：Join</span></div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">案例分析</span></div>
<div>
<div class="O">•输入为2个文件，文件一内容如下</div>
<div class="O1">•空格分割：用户名 手机号 年龄</div>
<div class="O1">•内容样例</div>
<div class="O2">•Tom 1314567890 14</div>
<div class="O">•文件二内容</div>
<div class="O1">•空格分割：手机号 地市</div>
<div class="O1">•内容样例</div>
<div class="O2">•13124567890 hubei</div>
<div class="O">•需要统计出的汇总信息为 用户名 手机号 年龄 地市</div>
<div class="O">
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Map</span>端<span lang="en-us" xml:lang="en-us">Join</span></span></div>
<div>
<div class="O">•设计思路</div>
<div class="O1">•使用DistributedCache.addCacheFile()将地市的文件加入到所有Map的缓存里</div>
<div class="O1">•在Map函数里读取该文件，进行Join</div>
<div class="O1">•  将结果输出到reduce</div>
<div class="O">•需要注意的是</div>
<div class="O1">•DistributedCache需要在生成Job作业前使用</div>
<div class="O"> </div>
</div>
 
<div class="O"><span class="bold" style="font-weight:bold;"><span lang="en-us" xml:lang="en-us">Reduce</span>端<span lang="en-us" xml:lang="en-us">Join</span></span></div>
</div>
</div>
<div>
<div class="O">•设计思路</div>
<div class="O1">•Map端读取所有文件，并在输出的内容里加上标识代表数据时从哪个文件里来的</div>
<div class="O1">•在reduce对按照标识对数据进行保存</div>
<div class="O1">•然后根据Key的Join来求出结果直接输出</div>
<div class="O"> </div>
</div>
<div class="O">
<div><span class="bold" style="font-weight:bold;">第三部分：排序</span></div>
<div> </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">普通排序</span></div>
<div class="O">
<div>•Mapreduce本身自带排序功能</div>
<div>•Text对象是不适合排序的，如果内容为整型不会安照编码顺序去排序</div>
<div>•一般情况下我们可以考虑以IntWritable做为Key,同时将Reduce设置成0 ,进行排序</div>
<div> </div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">部分排序</span></div>
<div class="O">
<div>•即输出的每个文件都是排过序的</div>
<div>•如果我们不需要全局排序，那么这是个不错的选择。</div>
<div> </div>
</div>
<div class="O"><span class="bold" style="font-weight:bold;">全局排序</span></div>
<div>
<div class="O">•产生背景</div>
<div class="O1">•Hadoop平台没有提供全局数据排序，而在大规模数据处理中进行数据的全局排序是非常普遍的需求。</div>
<div class="O1">•使用hadoop进行大量的数据排序排序最直观的方法是把文件所有内容给map之后，map不做任何处理，直接输出给一个reduce，利用hadoop的自己的shuffle机制，对所有数据进行排序，而后由reduce直接输出。</div>
<div class="O">•快速排序基本步骤就是需要现在所有数据中选取一个作为支点。然后将大于这个支点的放在一边，小于这个支点的放在另一边。</div>
<div class="O"> </div>
</div>
<div class="O">
<div>设想如果我们有 <span lang="en-us" xml:lang="en-us">N</span> 个支点（这里可以称为标尺），就可以把所有的数据分成 <span lang="en-us" xml:lang="en-us">N+1</span> 个<span lang="en-us" xml:lang="en-us">part</span> ，将这 <span lang="en-us" xml:lang="en-us">N+1</span> 个 <span lang="en-us" xml:lang="en-us">part</span> 丢给 <span lang="en-us" xml:lang="en-us">reduce</span> ，由 <span lang="en-us" xml:lang="en-us">hadoop</span> 自动排序，最后输出 <span lang="en-us" xml:lang="en-us">N+1</span> 个内部有序的文件，再把这 <span lang="en-us" xml:lang="en-us">N+1</span> 个文件首尾相连合并成一个文件，收工 。</div>
<div>由此我们可以归纳出这样一个用 <span lang="en-us" xml:lang="en-us">hadoop</span> 对大量数据排序的步骤：</div>
<div>
<span lang="en-us" xml:lang="en-us">1</span> ）   对待排序数据进行抽样；</div>
<div>
<span lang="en-us" xml:lang="en-us">2</span> ）   对抽样数据进行排序，产生标尺；</div>
<div>
<span lang="en-us" xml:lang="en-us">3</span> ）   Map 对输入的每条数据计算其处于哪两个标尺之间；将数据发给对应区间 <span lang="en-us" xml:lang="en-us">ID</span>的 <span lang="en-us" xml:lang="en-us">reduce</span>
</div>
<div>
<span lang="en-us" xml:lang="en-us">4</span> ）   Reduce 将获得数据直接输出。</div>
<div>
<div class="O">
<div>•Hadoop 提供了Sampler接口可以返回一组样本，该接口为Hadoop的采样器。</div>
<div>           public interface Sampler&lt;K, V&gt; {</div>
<div>                        K[] getSample(InputFormat&lt;K, V&gt; inf, Job job)</div>
<div>                         throws IOException, InterruptedException;</div>
<div>            }</div>
<div>•Hadoop提供了一个TotalOrderPartitioner，可以使我们来实现全局排序。</div>
<div>
<div class="O"><span class="bold" style="font-weight:bold;">二次排序</span></div>
<div>
<div class="O">•产生背景</div>
<div class="O1">•MapReduce默认会对key进行排序</div>
<div class="O1">•将输出到Reduce的values也进行预先的排序</div>
<div class="O">•实现方式</div>
<div class="O1">•重写Partitioner，完成key分区，进行第一次排序</div>
<div class="O1">•实现WritableComparator，完成自己的排序逻辑，完成key的第2次排序</div>
<div class="O1">
<div>
<div class="O">•原理</div>
<div class="O1">•Map之前的数据</div>
<div class="O1">         key1  1</div>
<div class="O1">         key2  2</div>
<div class="O1">         key2  3</div>
<div class="O1">         key3  4</div>
<div class="O1">         key1  2</div>
<div class="O1">•Mapduce只能排序key,所以为了二次排序我们要重新定义自己的key 简单说来就是&lt;key value&gt; value ,组合完后</div>
<div class="O1">         &lt;key1  1 &gt;    1</div>
<div class="O1">         &lt;key2  2 &gt;    2</div>
<div class="O1">         &lt;key2  3 &gt;    3</div>
<div class="O1">         &lt;key3  4&gt;     4</div>
<div class="O1">         &lt;key1  2 &gt;    2</div>
<div class="O"> </div>
</div>
<div>
<div class="O">•原理</div>
<div class="O1">•接下来实现自定义的排序类，分组类，数据变成</div>
<div class="O1">         &lt;key1  1 &gt;    1</div>
<div class="O1">         &lt;key1  2 &gt;    2</div>
<div class="O1">         &lt;key2  2 &gt;    2</div>
<div class="O1">         &lt;key2  3 &gt;    3</div>
<div class="O1">         &lt;key3  4&gt;     4</div>
<div class="O1">•最后 reduce处理后输出结果</div>
<div class="O1">           key1  1</div>
<div class="O1">           key1  2</div>
<div class="O1">           key2  2</div>
<div class="O1">           key2  3</div>
<div class="O1">           key3  4</div>
<div class="O1"> </div>
<div class="O"> 
<div class="O">
<div> </div>
<div><span class="bold" style="font-weight:bold;">第四部分：计数器</span></div>
<div>
<div>
<div class="O">•什么是计数器</div>
<div class="O">            计数器主要用来收集系统信息和作业运行信息，用于知道作业成功、失败等情况，比日志更便利进行分析。</div>
<div class="O">•内置计数器</div>
<div class="O1">•Hadoop内置的计数器，记录作业执行情况和记录情况。包括MapReduce框架、文件系统、作业计数三大类。</div>
<div class="O1">•计数器由关联任务维护，定期传递给tasktracker，再由tasktracker传给jobtracker。</div>
<div class="O1">•计数器可以被全局聚集。内置的作业计数器实际上由jobtracker维护，不必在整个网络中传递。</div>
<div class="O1">•当一个作业执行成功后，计数器的值才是完整可靠的。</div>
<div class="O"> </div>
</div>
 
<div class="O"><span class="bold" style="font-weight:bold;">用户自定义<span lang="en-us" xml:lang="en-us">Java</span>计数器</span></div>
</div>
<div>
<div class="O">•MapReduce框架允许用户自定义计数器</div>
<div class="O">•计数器是全局使用的</div>
<div class="O">•计数器有组的概念，可以由一个Java枚举类型来定义</div>
<div class="O">•如何配置</div>
<div class="O1">•0.20.2以下的版本使用Reporter,</div>
<div class="O1">•0.20.2以上的版本使用context.getCounter(groupName, counterName) 来获取计数器配置并设置。</div>
<div class="O">
<div class="O">•动态计数器</div>
<div class="O1">•所谓动态计数器即不采用Java枚举的方式来定义</div>
<div class="O"> </div>
<div class="O1">•Reporter中的获取动态计数器的方法</div>
<div class="O2">•public void incrCounter(String group,String counter,long amount)</div>
<div class="O">            组名称，计数器名称，计数值</div>
<div class="O"> </div>
<div class="O">•一些原则</div>
<div class="O1">•创建计数器时，尽量让名称易读</div>
<div class="O"> </div>
<div class="O"> 
<div>
<div class="O">•获取计数器</div>
<div class="O1">•Web UI</div>
<div class="O1">•命令行 hadoop job-counter</div>
<div class="O1">•Java API</div>
<div class="O">•Java API</div>
<div class="O1">•在作业运行完成后，计数器稳定后获取。 使用job.getCounters()得到Counters</div>
<div class="O1"> </div>
<div class="O"> 
<div class="O">
<div> </div>
<div><span class="bold" style="font-weight:bold;">第五部分：合并小文件示例</span></div>
<div>
<div>
<div class="O">•产生背景</div>
<div class="O1">•Hadoop不适合处理小文件</div>
<div class="O1">•会占用大量的内存空间</div>
<div class="O">•解决方案</div>
<div class="O1">•文件内容读取到SequenceFile内</div>
<div class="O"> </div>
</div>
  转载请注明出处【 <a href="http://sishuok.com/forum/blogPost/list/0/5961.html" rel="nofollow">http://sishuok.com/forum/blogPost/list/0/5961.html</a>】</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
</div>
            </div>
                </div>
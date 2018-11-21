---
layout:     post
title:      Hadoop MapReduce高级编程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
文章来源：【 http://sishuok.com/forum/blogPost/list/0/5961.html】<br>
Hadoop实战-中高级部分 之 Hadoop MapReduce高级编程<br>
浏览(9475)|评论(1) 交流分类：Java|笔记分类: Hadoop实战-中……<br><br>
Hadoop RestFul<br>
Hadoop HDFS原理1<br>
Hadoop HDFS原理2<br>
Hadoop作业调优参数调整及原理<br>
Hadoop HA<br>
Hadoop MapReduce高级编程<br>
Hadoop IO<br>
Hadoop MapReduce工作原理<br>
Hadoop 管理<br>
Hadoop 集群安装<br>
Hadoop RPC<br><br>
第一部分：重要的组件<br>
Combiner<br>
•什么是Combiner<br>
•combine函数把一个map函数产生的&lt;key,value&gt;对（多个key, value）合并成一个新的&lt;key2,value2&gt;. 将新的&lt;key2,value2&gt;作为输入到reduce函数中，其格式与reduce函数相同。<br>
•这样可以有效的较少中间结果，减少网络传输负荷。<br><br>
•什么情况下可以使用Combiner<br>
•可以对记录进行汇总统计的场景，如求和。<br>
•求平均数的场景就不可以使用了<br>
Combiner执行时机<br>
•运行combiner函数的时机有可能会是merge完成之前，或者之后，这个时机可以由一个参数控制，即 min.num.spill.for.combine（default 3）<br>
•当job中设定了combiner，并且spill数最少有3个的时候，那么combiner函数就会在merge产生结果文件之前运行<br>
•通过这样的方式，就可以在spill非常多需要merge，并且很多数据需要做conbine的时候，减少写入到磁盘文件的数据数量，同样是为了减少对磁盘的读写频率，有可能达到优化作业的目的。<br>
•Combiner也有可能不执行， Combiner会考虑当时集群的负载情况。<br>
Combiner如何使用<br>
•代码示例<br>
•继承Reducer类<br>
public static class Combiner extends MapReduceBase implements<br>
Reducer&lt;Text, Text, Text, Text&gt; {<br>
public void reduce(Text key, Iterator&lt;Text&gt; values,<br>
OutputCollector&lt;Text, Text&gt; output, Reporter reporter)<br>
throws IOException {<br>
}<br>
}<br><br>
•配置作业时加入conf.setCombinerClass(Combiner.class)<br><br>
Partitioner<br>
•什么是Partitioner<br>
•Mapreduce 通过Partitioner 对Key 进行分区，进而把数据按我们自己的需求来分发。<br>
•什么情况下使用Partitioner<br>
•如果你需要key按照自己意愿分发，那么你需要这样的组件。<br>
•例如：数据文件内包含省份，而输出要求每个省份输出一个文件。<br>
•框架默认的HashPartitioner<br>
•public class HashPartitioner&lt;K, V&gt; extends Partitioner&lt;K, V&gt; { <br><br>
/** Use {@link Object#hashCode()} to partition. */ <br>
public int getPartition(K key, V value, <br>
int numReduceTasks) { <br>
return (key.hashCode() &amp; Integer.MAX_VALUE) % numReduceTasks; <br>
}<br>
} <br>
Partitioner如何使用<br>
•实现Partitioner接口覆盖getPartition()方法<br>
•配置作业时加入conf.setPartitionerClass(MyPartitioner.class);<br>
•Partitioner示例<br>
public static class MyPartitioner implements Partitioner&lt;Text, Text&gt; {<br><br>
@Override<br>
public int getPartition(Text key, Text value, int numPartitions) {<br>
}<br><br>
}<br>
Partitioner需求示例<br>
•需求描述<br>
•数据文件中含有省份<br>
•需要相同的省份送到相同的Reduce里<br>
•从而产生不同的文件<br>
•数据样例<br>
•1 liaoning<br>
•1 代表该省份有多少个直辖市<br>
•步骤<br>
•实现Partitioner，覆盖getPartition<br>
•根据省份字段进行切分<br><br><br>
RecordReader<br>
•什么是RecordReader<br>
•用于在分块中读取&lt;Key,Value&gt;对，也就是说每一次我们读取一条记录都会调用该类。<br>
•主要是处理经过InputFormat分片完的数据 <br>
•什么时候使用RecordReader<br>
•需要对输入的数据按自己的需求处理<br>
•如：要求输入的key不是文件的偏移量而是文件的路径或者名字<br>
•系统默认为LineRecordReader<br>
•按照每行的偏移量做为map输出时的key值，每行的内容作为map的value值，默认的分隔符是回车和换行。<br><br>
RecordReader需求示例<br>
•需求<br>
•更改map对应的输入的&lt;key,value&gt;值，key对应的文件的路径（或者是文件名），value对应的是文件的内容（content）。<br>
•步骤<br>
•重写InputFormat不对文件切分<br>
•重写RecordReader<br>
•在配置作业时使用自定义的组件进行数据处理<br><br><br>
第二部分：Join<br>
案例分析<br>
•输入为2个文件，文件一内容如下<br>
•空格分割：用户名 手机号 年龄<br>
•内容样例<br>
•Tom 1314567890 14<br>
•文件二内容<br>
•空格分割：手机号 地市<br>
•内容样例<br>
•13124567890 hubei<br>
•需要统计出的汇总信息为 用户名 手机号 年龄 地市<br>
Map端Join<br>
•设计思路<br>
•使用DistributedCache.addCacheFile()将地市的文件加入到所有Map的缓存里<br>
•在Map函数里读取该文件，进行Join<br>
• 将结果输出到reduce<br>
•需要注意的是<br>
•DistributedCache需要在生成Job作业前使用<br><br><br>
Reduce端Join<br>
•设计思路<br>
•Map端读取所有文件，并在输出的内容里加上标识代表数据时从哪个文件里来的<br>
•在reduce对按照标识对数据进行保存<br>
•然后根据Key的Join来求出结果直接输出<br><br>
第三部分：排序<br><br>
普通排序<br>
•Mapreduce本身自带排序功能<br>
•Text对象是不适合排序的，如果内容为整型不会安照编码顺序去排序<br>
•一般情况下我们可以考虑以IntWritable做为Key,同时将Reduce设置成0 ,进行排序<br><br>
部分排序<br>
•即输出的每个文件都是排过序的<br>
•如果我们不需要全局排序，那么这是个不错的选择。<br><br>
全局排序<br>
•产生背景<br>
•Hadoop平台没有提供全局数据排序，而在大规模数据处理中进行数据的全局排序是非常普遍的需求。<br>
•使用hadoop进行大量的数据排序排序最直观的方法是把文件所有内容给map之后，map不做任何处理，直接输出给一个reduce，利用hadoop的自己的shuffle机制，对所有数据进行排序，而后由reduce直接输出。<br>
•快速排序基本步骤就是需要现在所有数据中选取一个作为支点。然后将大于这个支点的放在一边，小于这个支点的放在另一边。<br><br>
设想如果我们有 N 个支点（这里可以称为标尺），就可以把所有的数据分成 N+1 个 part ，将这 N+1 个 part 丢给 reduce ，由 hadoop 自动排序，最后输出 N+1 个内部有序的文件，再把这 N+1 个文件首尾相连合并成一个文件，收工 。<br>
由此我们可以归纳出这样一个用 hadoop 对大量数据排序的步骤：<br>
1 ） 对待排序数据进行抽样；<br>
2 ） 对抽样数据进行排序，产生标尺；<br>
3 ） Map 对输入的每条数据计算其处于哪两个标尺之间；将数据发给对应区间 ID 的 reduce<br>
4 ） Reduce 将获得数据直接输出。<br>
•Hadoop 提供了Sampler接口可以返回一组样本，该接口为Hadoop的采样器。<br>
public interface Sampler&lt;K, V&gt; {<br>
K[] getSample(InputFormat&lt;K, V&gt; inf, Job job)<br>
throws IOException, InterruptedException;<br>
}<br>
•Hadoop提供了一个TotalOrderPartitioner，可以使我们来实现全局排序。<br>
二次排序<br>
•产生背景<br>
•MapReduce默认会对key进行排序<br>
•将输出到Reduce的values也进行预先的排序<br>
•实现方式<br>
•重写Partitioner，完成key分区，进行第一次排序<br>
•实现WritableComparator，完成自己的排序逻辑，完成key的第2次排序<br>
•原理<br>
•Map之前的数据<br>
key1 1<br>
key2 2<br>
key2 3<br>
key3 4<br>
key1 2<br>
•Mapduce只能排序key,所以为了二次排序我们要重新定义自己的key 简单说来就是&lt;key value&gt; value ,组合完后<br>
&lt;key1 1 &gt; 1<br>
&lt;key2 2 &gt; 2<br>
&lt;key2 3 &gt; 3<br>
&lt;key3 4&gt; 4<br>
&lt;key1 2 &gt; 2<br><br>
•原理<br>
•接下来实现自定义的排序类，分组类，数据变成<br>
&lt;key1 1 &gt; 1<br>
&lt;key1 2 &gt; 2<br>
&lt;key2 2 &gt; 2<br>
&lt;key2 3 &gt; 3<br>
&lt;key3 4&gt; 4<br>
•最后 reduce处理后输出结果<br>
key1 1<br>
key1 2<br>
key2 2<br>
key2 3<br>
key3 4<br><br><br><br>
第四部分：计数器<br>
•什么是计数器<br>
计数器主要用来收集系统信息和作业运行信息，用于知道作业成功、失败等情况，比日志更便利进行分析。<br>
•内置计数器<br>
•Hadoop内置的计数器，记录作业执行情况和记录情况。包括MapReduce框架、文件系统、作业计数三大类。<br>
•计数器由关联任务维护，定期传递给tasktracker，再由tasktracker传给jobtracker。<br>
•计数器可以被全局聚集。内置的作业计数器实际上由jobtracker维护，不必在整个网络中传递。<br>
•当一个作业执行成功后，计数器的值才是完整可靠的。<br><br><br>
用户自定义Java计数器<br>
•MapReduce框架允许用户自定义计数器<br>
•计数器是全局使用的<br>
•计数器有组的概念，可以由一个Java枚举类型来定义<br>
•如何配置<br>
•0.20.2以下的版本使用Reporter,<br>
•0.20.2以上的版本使用context.getCounter(groupName, counterName) 来获取计数器配置并设置。<br>
•动态计数器<br>
•所谓动态计数器即不采用Java枚举的方式来定义<br><br>
•Reporter中的获取动态计数器的方法<br>
•public void incrCounter(String group,String counter,long amount)<br>
组名称，计数器名称，计数值<br><br>
•一些原则<br>
•创建计数器时，尽量让名称易读<br><br><br>
•获取计数器<br>
•Web UI<br>
•命令行 hadoop job-counter<br>
•Java API<br>
•Java API<br>
•在作业运行完成后，计数器稳定后获取。 使用job.getCounters()得到Counters<br><br><br><br>
第五部分：合并小文件示例<br>
•产生背景<br>
•Hadoop不适合处理小文件<br>
•会占用大量的内存空间<br>
•解决方案<br>
•文件内容读取到SequenceFile内<br><br>
转载请注明出处【 http://sishuok.com/forum/blogPost/list/0/5961.html】<br><br>            </div>
                </div>
---
layout:     post
title:      hadoop 简单入门与streaming常用配置参数说明
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="color:#000000;">1. Hadoop</span><span style="color:#000000;">包含两核心部分</span></p>

<ol><li>hdfs
	<ol><li><span style="color:#000000;">Hadoop distribute file system -- </span><span style="color:#000000;">hadoop</span><span style="color:#000000;">分布式文件系统，存储数据</span></li>
		<li><span style="color:#000000;">Namenode</span><span style="color:#000000;">、</span><span style="color:#000000;">Datanode</span></li>
		<li><span style="color:#000000;">常用命令</span><span style="color:#000000;">形式</span><span style="color:#000000;">：</span><span style="color:#000000;">hadoop</span><span style="color:#000000;"> fs -</span><span style="color:#000000;">ls</span><span style="color:#000000;">  /  </span><span style="color:#000000;">hadoop</span><span style="color:#000000;"> fs -</span><span style="color:#000000;">mkdir</span></li>
	</ol></li>
	<li><span style="color:#000000;">MapReduce</span>
	<ol><li><span style="color:#000000;">分而治之；</span><span style="color:#000000;">map:</span><span style="color:#000000;">实现分治；</span><span style="color:#000000;">reduce</span><span style="color:#000000;">：实现合并</span></li>
		<li><span style="color:#000000;">解决数据可分割的计算问题</span></li>
		<li><span style="color:#000000;">编程接口：常用</span><span style="color:#000000;">Streaming</span><span style="color:#000000;">；组成</span><span style="color:#000000;">：</span><span style="color:#000000;">Job</span><span style="color:#000000;">配置文件、</span><span style="color:#000000;">map</span><span style="color:#000000;">函数，</span><span style="color:#000000;">reduce</span><span style="color:#000000;">函数</span></li>
	</ol></li>
</ol><p>2. hdfs结构图</p>

<ol><li><span style="color:#000000;">Namenode</span><span style="color:#000000;">存储元数据，数据信息，数据备份信息</span></li>
	<li><span style="color:#000000;">Datanode</span> <span style="color:#000000;">数据备份：本机架备份、异地备份</span></li>
</ol><p style="text-indent:50px;"><img alt="" class="has" height="395" src="https://img-blog.csdn.net/2018101117292863?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVyX295YW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="576"></p>

<p>3. MapReduce调度框架</p>

<ol><li><span style="color:#000000;">JobClient</span><span style="color:#000000;">:  </span><span style="color:#000000;">负责根据用户指定参数生成一个</span><span style="color:#000000;">mapreduce</span><span style="color:#000000;">作业，提交到</span><span style="color:#000000;">JobTracker</span></li>
	<li><span style="color:#000000;">JobTracker</span><span style="color:#000000;">: </span><span style="color:#000000;">单</span><span style="color:#000000;">Master</span><span style="color:#000000;">节点，将</span><span style="color:#000000;">Job</span><span style="color:#000000;">所有</span><span style="color:#000000;">task</span><span style="color:#000000;">调度到</span><span style="color:#000000;">TaskTracker</span></li>
	<li><span style="color:#000000;">TaskTracker</span><span style="color:#000000;">: </span><span style="color:#000000;">部署在每台计算机节点的一个</span><span style="color:#000000;">service</span></li>
</ol><p style="text-indent:50px;"><img alt="" class="has" height="501" src="https://img-blog.csdn.net/20181011173214940?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVyX295YW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="656"></p>

<p>4.  MapReduce 执行层</p>

<ol><li><span style="color:#000000;">Map</span><span style="color:#000000;">阶段，读入数据，通过</span><span style="color:#000000;">partition</span><span style="color:#000000;">聚集相同</span><span style="color:#000000;">key数据，并写到本机磁盘</span></li>
	<li><span style="color:#000000;">Reduce</span><span style="color:#000000;">阶段，不同</span><span style="color:#000000;">reduce</span><span style="color:#000000;">，读入</span><span style="color:#000000;">Map</span><span style="color:#000000;">阶段各</span><span style="color:#000000;">maps</span><span style="color:#000000;">的</span><span style="color:#ff0000;">相应</span><span style="color:#000000;">输出</span></li>
</ol><p><img alt="" class="has" height="322" src="https://img-blog.csdn.net/20181011184149990?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVyX295YW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="938"></p>

<p>5. streaming 作业</p>

<ol><li>streaming mapper
	<ol><li>先启动用户提交作业时指定的一个外部程序，一般是脚本</li>
		<li>这个外部程序作为streaming mapper的子进程，streaming mapper读取用户输入后，不再是调用map函数处理，而是通过管道写到子进程的标准输入</li>
		<li>从子进程的标准输出读取数据，写到磁盘上</li>
	</ol></li>
	<li>streaming 作业数据流向
	<ol><li>父进程是Java，负责读取数据通过管道发送给子进程</li>
		<li>通过管道把结果再读取回来</li>
	</ol></li>
</ol><p><strong>      一份数据在两个不同进程中传递两次</strong></p>

<p>6 mapreduce – shuffle</p>

<ol><li>map --&gt; shuffle –&gt; reduce</li>
	<li>shuffle 从多个节点传递到多个节点，而不是多个节点到一个节点</li>
	<li>shuffle 包含partition、combiner
	<ol><li>partition : 数据归并，分割map每个节点的结果，按照key分别映射给不同的reduce，默认是HashPartition，which reducer == （key.hashCode &amp; Integer.MAX_VALUE）% numReduceTasks</li>
		<li>作用：计算（key, value）所属分区 ； 把同一分区数据合并、聚集</li>
	</ol></li>
	<li>combiner： combiner属于优化方案，由于带宽限制，应该尽量map和reduce之间的数据传输数量。它在Map端把同一个key的键值对合并在一起并计算，<strong>计算规则与reduce一致</strong>，所以combiner也可以看作特殊的Reducer</li>
</ol><p>7. streaming 常用配置项</p>

<ol><li>stream.map.output.field.separator // 该参数属于streaming作业参数，设置map输出的字段分隔符，默认为“\t”，该分隔符只对下面的stream.num.map.output.key.fields参数生效</li>
	<li>stream.num.map.output.key.fields // 设置map输出的前几个字段作为key，一般与第二项stream.map.output.field.separator 结合使用</li>
	<li>mapred.text.key.partitioner.options  // 设置key内某个字段或者某个字段范围用做partition</li>
	<li>mapred.text.key.comparator.options // 设置key中需要比较的字段或字节范围</li>
	<li>partitioner // 主要用于对键值进行划分，负责将map的输出结果根据key进行分割。Key用于确定不同的key落到不同的reduce上，通常对key进行Hash以后对reduce取mod，该key对应的纪录最终将根据mod值落到对应的reduce上进行处理。HashPartitioner, IndexUpdatePartitioner, KeyFieldBasedPartitioner, SleepJob，默认的为 HashPartitioner，即对key直接进行hash分到对应的reduce，具体见第6部分。更高级一点的为 KeyFieldBasedPartitioner，该partitioner可以指定key中前几个字段用于分割</li>
	<li>HashPartition  最基本的Partitioner，如果不指定Partitioner的话则默认使用该类。输出格式为最基本的key”\t“value</li>
	<li>KeyFieldBasedPartitioner 可以看做HashPartitioner的扩展，他将原有的对单字段Key的hash扩展到可以灵活地对多字段key进行分桶并排序，对应配置参数如下：</li>
	<li>-partitioner org.apache.hadoop.mapred.lib.KeyFieldBasedPartitioner // 该参数表示作业启用KeyFieldBasedParitioner</li>
	<li>-D map.ouput.key.field.separator // 该参数属于KeyFieldBasedPartitioner参数，只有当启用KeyFieldBasedParititioner时，该参数才会生效；该参数指明map输出结果中字段之间的分隔符（该分隔符只对下面的num.key.fields.for.partition参数生效）；</li>
	<li>-D num.key.fields.for.partition // 该参数同样属于KeyFieldBasedPartitioner参数；该参数指明map输出结果的key按上述分隔符切分后，前几个字段将用来做partition；该参数不能与mapred.text.key.partitioner.options共用；</li>
	<li>-D mapred.text.key.partitioner.options // 该参数同样属于KeyFieldBasedPartitioner参数； 该参数指明map输出结果的key按上述分隔符切分后，使用哪些字段用来做partition；该参数不能与num.key.fields.for.partition共用，一起使用则以num.key.fields.for.partition为准；</li>
	<li>KeyFieldBaseComparator // 可以灵活设置比较位置的高级比较器，但是它和没有自己独有的比较逻辑，而是使用默认Text的基于字典序或者通过-n来基于数字比较，直观来说，partition指定key中的分区元素，KeyFieldBaseComparator用作指定key排序字段以及排序规则，参数配置如下：</li>
	<li>-D mapred.output.key.comparator.class=org.apache.hadoop.mapred.lib.KeyFieldBasedComparator // 该参数表示作业启用KeyFieldBasedComparator</li>
	<li>-D mapred.text.key.comparator.options="-k3,3 -k4nr" // 以key中第三个字段正序，第四个字段逆序比较排序</li>
	<li>stream.memory.limit // 任务内存限制</li>
	<li>mapred.reduce.tasks // 指定reducer个数</li>
	<li>cmdenv //传递给streaming命令的环境变量</li>
	<li>-input //HDFS目录或文件路径, Mapper的输入数据，文件要在任务提交前手动上传到HDFS</li>
	<li>-output // reducer输出结果的HDFS存放路径,  <strong>不能已存在</strong>，但脚本中一定要配置，多次-input,指定多个输入文件</li>
	<li>-mapper // 可执行命令，mapper程序</li>
	<li>-reducer // 可执行命令， reduce程序，不需要reduce处理就不指定</li>
	<li>-file //本地mapper、reducer程序文件、程序运行需要的其他文件,将本地文件分发给计算节点;文件作为作业的一部分，一起被打包并提交，所有分发的文件最终会被放置在datanode该job的同一个专属目录下：jobcache/job_xxx/jar</li>
	<li>-cacheFile //分发HDFS文件</li>
	<li>-cacheArchive // 分发HDFS压缩文件、压缩文件内部具有目录结构</li>
	<li>mapred.job.priority //作业优先级</li>
	<li>mapred.job.map.capacity // 最多同时运行map任务数</li>
	<li>mapred.job.reduce.capacity //最多同时运行reduce任务数</li>
	<li>mapred.job.name // job name</li>
</ol><p> </p>

<p>8. key-partition 实例</p>

<ol><li>key 不等于 partition，也就是说，分桶规则跟map阶段的key<strong>有可能</strong>不是一回事</li>
	<li>假设，文件A中内容如下：</li>
</ol><p>                       <img alt="" class="has" height="228" src="https://img-blog.csdn.net/20181016154739115?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVyX295YW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="71"></p>

<p> </p>

<ul><li>第一种作业方式（部分参数）：</li>
</ul><pre class="has">
<code class="language-bash">./hadoop streaming
-D stream.map.output.field.separator=.
-D stream.num.map.output.key.fields=2</code></pre>

<p>                                  <img alt="" class="has" height="232" src="https://img-blog.csdn.net/20181016154725449?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVyX295YW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="83"></p>

<p>        只是将map的输出结果按两个字段切分成了key和value；再分桶上我们可以看出，它是以前两个字段作为一个整体来进行分桶的，e.5与e.9没有分在一个reduce</p>

<ul><li>第二种作业：</li>
</ul><pre class="has">
<code class="language-bash">./hadoop streaming
-D stream.map.output.field.separator=.
-D stream.num.map.output.key.fields=2
-D map.output.key.field.separator=.
-D num.key.fields.for.partition=1
-partitioner org.apache.hadoop.mapred.lib.KeyFieldBasedPartitioner\</code></pre>

<p>                                    <img alt="" class="has" height="242" src="https://img-blog.csdn.net/2018101615502792?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVyX295YW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="70"></p>

<p>              这里启用了KeyFieldBasedPartitioner，并且制定分桶以key的第一个字段为准；我们可以看出mapred依然采用的是前两个字段为key，但是在分桶上只对第一个字段做了哈希函数，因此这次e.5和e.9分到了一个reducer内</p>

<ul><li>第三种作业</li>
</ul><pre class="has">
<code class="language-bash">./hadoop streaming
-D stream.map.output.field.separator=.
-D stream.num.map.output.key.fields=3
-D map.output.key.field.separator=.
-D num.key.fields.for.partition=1  
-D mapred.text.key.partitioner.options=-k2,3
-partitioner org.apache.hadoop.mapred.lib.KeyFieldBasedPartitioner</code></pre>

<p>       这次将key的长度改成3个字段，分桶标准也变成key的第2、3个字段，可以看出e.5.1被分在了一起，而第三个字段不同的e.5.9被分到了其他的reducer中</p>

<ul><li>第四种作业</li>
</ul><pre class="has">
<code>./hadoop streaming \
-partitioner org.apache.hadoop.mapred.lib.KeyFieldBasedPartitioner \
-D mapred.output.key.comparator.class=org.apache.hadoop.mapred.lib.KeyFieldBasedComparator \
-D stream.num.map.output.key.fields=4 \
-D stream.map.output.field.separator=. \
-D map.output.key.field.separator=. \
-D mapred.text.key.partitioner.options=-k1,2 \
-D mapred.text.key.comparator.options="-k3,3 -k4nr" \</code></pre>

<p>                                     <img alt="" class="has" height="229" src="https://img-blog.csdn.net/20181016155534660?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2NvZGVyX295YW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="62"></p>

<p>         这次key的长度为4，分桶标准为key的第一、二个字段，可以看出，e.5被分到一个桶内，而输出结果，按照key的第三个字段的正序，第四个字段的逆序排列输出</p>            </div>
                </div>
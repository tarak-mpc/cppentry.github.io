---
layout:     post
title:      spark源码阅读一-spark读写文件代码分析
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>1.读取文件</div>
<div>从本地文件读取sparkcontext.textFile(“abc.txt”)</div>
<div>从hdfs文件读取sparkcontext.textFile("hdfs://s1:8020/user/hdfs/input”)</div>
<div><span style="font-weight:bold;">def</span> textFile(</div>
<div>    path: String,</div>
<div>    minPartitions: Int = defaultMinPartitions): RDD[String] = withScope {</div>
<div>  assertNotStopped()</div>
<div>  hadoopFile(path, <span style="font-style:italic;">classOf</span>[TextInputFormat],<span style="font-style:italic;">classOf</span>[LongWritable],<span style="font-style:italic;">classOf</span>[Text],</div>
<div>    minPartitions).map(pair =&gt; pair._2.toString).setName(path)</div>
<div>}</div>
<div>textFile第2个参数是partition个数，默认defaultMinPartitions为2，分2个区。也可以自定义最小分区数。</div>
<div>接下来就是看hadoopFile实现：</div>
<div><span style="font-weight:bold;">def</span> hadoopFile[K, V](</div>
<div>    path: String,</div>
<div>    inputFormatClass: Class[_ &lt;: InputFormat[K, V]],</div>
<div>    keyClass: Class[K],</div>
<div>    valueClass: Class[V],</div>
<div>    minPartitions: Int = defaultMinPartitions): RDD[(K, V)] = withScope {</div>
<div>  assertNotStopped()</div>
<div><br></div>
<div>  <span style="font-style:italic;">// This is a hack to enforce loading hdfs-site.xml.</span><span style="font-style:italic;">  </span></div>
<div><span style="font-style:italic;">  // See SPARK-11227 for details.</span><span style="font-style:italic;">  </span></div>
<div>  FileSystem.<span style="font-style:italic;">getLocal</span>(hadoopConfiguration)</div>
<div><br></div>
<div>  <span style="font-style:italic;">// A Hadoop configuration can be about 10 KB, which is pretty big, so broadcast it.</span><span style="font-style:italic;">  </span></div>
<div><span style="font-weight:bold;">  val</span> confBroadcast = broadcast(<span style="font-weight:bold;">new</span> SerializableConfiguration(hadoopConfiguration))</div>
<div>  <span style="font-weight:bold;">val</span> setInputPathsFunc = (jobConf: JobConf) =&gt; FileInputFormat.<span style="font-style:italic;">setInputPaths</span>(jobConf, path)</div>
<div>  <span style="font-weight:bold;">new</span> HadoopRDD(</div>
<div>    <span style="font-weight:bold;">this</span>,</div>
<div>    confBroadcast,</div>
<div>    <span style="font-style:italic;">Some</span>(setInputPathsFunc),</div>
<div>    inputFormatClass,</div>
<div>    keyClass,</div>
<div>    valueClass,</div>
<div>    minPartitions).setName(path)</div>
<div>}</div>
<div>这里返回的就是HadoopRDD，RDD最关键的2个信息就是如何分区，以及PreferredLocation。</div>
<div>关于第一个问题分区，我们看看getPartitions</div>
<div><span style="font-weight:bold;">override def</span> getPartitions: Array[Partition] = {</div>
<div>  <span style="font-weight:bold;">val</span> jobConf = getJobConf()</div>
<div>  <span style="font-style:italic;">// add the credentials here as this can be called before SparkContext initialized</span><span style="font-style:italic;"> </span> SparkHadoopUtil.<span style="font-style:italic;">get</span>.addCredentials(jobConf)</div>
<div>  <span style="font-weight:bold;">val</span> inputFormat = getInputFormat(jobConf)</div>
<div>  <span style="font-weight:bold;">val</span> inputSplits = inputFormat.getSplits(jobConf, minPartitions)</div>
<div>  <span style="font-weight:bold;">val</span> array = <span style="font-weight:bold;">
new</span> Array[Partition](inputSplits.size)</div>
<div>  <span style="font-weight:bold;">for</span> (i &lt;- 0 until inputSplits.size) {</div>
<div>    array(i) = <span style="font-weight:bold;">new</span> HadoopPartition(<span style="font-style:italic;">id</span>, i, inputSplits(i))</div>
<div>  }</div>
<div>  array</div>
<div>}</div>
<div>如何分区是从inputFormat.getSplits(jobConf, minPartitions)获得的，前面函数传入的inputFormat是TextInputFormat，TextInputFormat继承</div>
<div>于FileInputFormat，我们来看代码</div>
<div><span style="font-weight:bold;">public</span> InputSplit[] getSplits(JobConf job,<span style="font-weight:bold;">int</span> numSplits)</div>
<div>  <span style="font-weight:bold;">throws</span> IOException {</div>
<div>  Stopwatch sw = <span style="font-weight:bold;">new</span> Stopwatch().start();</div>
<div>  FileStatus[] files = listStatus(job);</div>
<div> </div>
<div>  <span style="font-style:italic;">// Save the number of input files for metrics/loadgen</span><span style="font-style:italic;"> </span></div>
<div>  job.setLong(<span style="font-weight:bold;font-style:italic;">NUM_INPUT_FILES</span>, files.<span style="font-weight:bold;">length</span>);</div>
<div>  <span style="font-weight:bold;">long</span> totalSize = 0;                           <span style="font-style:italic;">// compute total size</span><span style="font-style:italic;">  </span></div>
<div><span style="font-weight:bold;">  for</span> (FileStatus file: files) {               <span style="font-style:italic;">// check we have valid files</span><span style="font-style:italic;">    </span></div>
<div><span style="font-weight:bold;">    if</span> (file.isDirectory()) {</div>
<div>      <span style="font-weight:bold;">throw new</span> IOException(<span style="font-weight:bold;">"Not a file: "</span>+ file.getPath());</div>
<div>    }</div>
<div>    totalSize += file.getLen();</div>
<div>  }</div>
<div><br></div>
<div>  <span style="font-weight:bold;">long</span> goalSize = totalSize / (numSplits == 0 ? 1 : numSplits);</div>
<div>  <span style="font-weight:bold;">long</span> minSize = Math.<span style="font-style:italic;">max</span>(job.getLong(org.apache.hadoop.mapreduce.lib.input.</div>
<div>    FileInputFormat.<span style="font-weight:bold;font-style:italic;">SPLIT_MINSIZE</span>, 1),<span style="font-weight:bold;">minSplitSize</span>);</div>
<div><br></div>
<div>  <span style="font-style:italic;">// generate splits</span><span style="font-style:italic;"> </span> ArrayList&lt;FileSplit&gt; splits =<span style="font-weight:bold;">new</span> ArrayList&lt;FileSplit&gt;(numSplits);</div>
<div>  NetworkTopology clusterMap = <span style="font-weight:bold;">new</span> NetworkTopology();</div>
<div>  <span style="font-weight:bold;">for</span> (FileStatus file: files) {</div>
<div>    Path path = file.getPath();</div>
<div>    <span style="font-weight:bold;">long</span> length = file.getLen();</div>
<div>    <span style="font-weight:bold;">if</span> (length != 0) {</div>
<div>      FileSystem fs = path.getFileSystem(job);</div>
<div>      BlockLocation[] blkLocations;</div>
<div>      <span style="font-weight:bold;">if</span> (file <span style="font-weight:bold;">
instanceof</span> LocatedFileStatus) {</div>
<div>        blkLocations = ((LocatedFileStatus) file).getBlockLocations();</div>
<div>      } <span style="font-weight:bold;">else</span> {</div>
<div>        blkLocations = fs.getFileBlockLocations(file, 0, length);</div>
<div>      }</div>
<div>      <span style="font-weight:bold;">if</span> (isSplitable(fs, path)) {</div>
<div>        <span style="font-weight:bold;">long</span> blockSize = file.getBlockSize();</div>
<div>        <span style="font-weight:bold;">long</span> splitSize = computeSplitSize(goalSize, minSize, blockSize);</div>
<div><br></div>
<div>        <span style="font-weight:bold;">long</span> bytesRemaining = length;</div>
<div>        <span style="font-weight:bold;">while</span> (((<span style="font-weight:bold;">double</span>) bytesRemaining)/splitSize &gt;<span style="font-weight:bold;font-style:italic;">SPLIT_SLOP</span>) {</div>
<div>          String[][] splitHosts = getSplitHostsAndCachedHosts(blkLocations,</div>
<div>              length-bytesRemaining, splitSize, clusterMap);</div>
<div>          splits.add(makeSplit(path, length-bytesRemaining, splitSize,</div>
<div>              splitHosts[0], splitHosts[1]));</div>
<div>          bytesRemaining -= splitSize;</div>
<div>        }</div>
<div><br></div>
<div>        <span style="font-weight:bold;">if</span> (bytesRemaining != 0) {</div>
<div>          String[][] splitHosts = getSplitHostsAndCachedHosts(blkLocations, length</div>
<div>              - bytesRemaining, bytesRemaining, clusterMap);</div>
<div>          splits.add(makeSplit(path, length - bytesRemaining, bytesRemaining,</div>
<div>              splitHosts[0], splitHosts[1]));</div>
<div>        }</div>
<div>      } <span style="font-weight:bold;">else</span> {</div>
<div>        String[][] splitHosts = getSplitHostsAndCachedHosts(blkLocations,0,length,clusterMap);</div>
<div>        splits.add(makeSplit(path, 0, length, splitHosts[0], splitHosts[1]));</div>
<div>      }</div>
<div>    } <span style="font-weight:bold;">else</span> {</div>
<div>      <span style="font-style:italic;">//Create empty hosts array for zero length files</span><span style="font-style:italic;">      </span></div>
<div>      splits.add(makeSplit(path, 0, length, <span style="font-weight:bold;">new</span> String[0]));</div>
<div>    }</div>
<div>  }</div>
<div>  sw.stop();</div>
<div>  <span style="font-weight:bold;">if</span> (<span style="font-weight:bold;font-style:italic;">LOG</span>.isDebugEnabled()) {</div>
<div>    <span style="font-weight:bold;font-style:italic;">LOG</span>.debug(<span style="font-weight:bold;">"Total # of splits generated by getSplits: "</span> + splits.size()</div>
<div>        + <span style="font-weight:bold;">", TimeTaken: "</span> + sw.elapsedMillis());</div>
<div>  }</div>
<div>  <span style="font-weight:bold;">return</span> splits.toArray(<span style="font-weight:bold;">new</span> FileSplit[splits.size()]);</div>
<div>}</div>
<div>先获得读取文件的长度，然后得到goalsize，等于文件长度除以textfile函数输入的最小分区个数(默认为2).</div>
<div>在遍历files列表划分的过程中，会获取每个文件的blocklocation和blockSize。对于hdfs文件block location是hdfs保存的location；对于本地文件location就是localhost。</div>
<div>最终调用computeSplitSize方法计算每个输入文件应当划分的个数。</div>
<div><span style="font-weight:bold;">protected long</span> computeSplitSize(<span style="font-weight:bold;">long</span> goalSize,<span style="font-weight:bold;">long</span> minSize, <span style="font-weight:bold;">long</span> blockSize) {</div>
<div>  <span style="font-weight:bold;">return</span> Math.<span style="font-style:italic;">max</span>(minSize, Math.<span style="font-style:italic;">min</span>(goalSize, blockSize));</div>
<div>}</div>
<div>minSize是配置的mapreduce.input.fileinputformat.split.minsize参数，没有配置的话默认为1.所以一般来说就是goalSize、blockSize较小的那个。</div>
<div>后面的代码就是根据splitSize进行划分并填写split数组。最终split回用来生成HadoopPartition。</div>
<div>那我们来看第2个问题，PreferredLocation实际就是从HadoopPartition的split信息中的分区location获得的。代码如下：</div>
<div><span style="font-weight:bold;">override def</span> getPreferredLocations(split: Partition): Seq[String] = {</div>
<div>  <span style="font-weight:bold;">val</span> hsplit = split.asInstanceOf[HadoopPartition].<span style="font-style:italic;">inputSplit</span>.value</div>
<div>  <span style="font-weight:bold;">val</span> locs = hsplit <span style="font-weight:bold;">
match</span> {</div>
<div>    <span style="font-weight:bold;">case</span> lsplit: InputSplitWithLocationInfo =&gt;</div>
<div>      HadoopRDD.<span style="font-style:italic;">convertSplitLocationInfo</span>(lsplit.getLocationInfo)</div>
<div>    <span style="font-weight:bold;">case</span> _ =&gt; None</div>
<div>  }</div>
<div>  locs.getOrElse(hsplit.getLocations.filter(_ != <span style="font-weight:bold;">
"localhost"</span>))</div>
<div>} </div>
<div><br></div>
<div>
<div>2.写文件</div>
<div>写入本地文件repartition(1).saveAsTextFile("test.txt”)，这里加入repartition(1)是先合并成一个分区然后写入一个文件。</div>
<div>写入hdfs文件saveAsTextFile("hdfs://m1:8020/result”)</div>
<div>来看RDD里面的代码</div>
<div><span style="font-weight:bold;">def</span> saveAsTextFile(path: String): Unit = withScope {</div>
<div>  <span style="font-style:italic;">//</span> <a href="https://issues.apache.org/jira/browse/SPARK-2075" rel="nofollow" style="font-style:italic;">
https://issues.apache.org/jira/browse/SPARK-2075</a><span style="font-style:italic;">  //</span><span style="font-style:italic;">  </span></div>
<div><span style="font-style:italic;">// NullWritable is a `Comparable` in Hadoop 1.+, so the compiler cannot find an implicit</span><span style="font-style:italic;">  </span></div>
<div><span style="font-style:italic;">// Ordering for it and will use the default `null`. However, it's a `Comparable[NullWritable]`</span><span style="font-style:italic;">  </span></div>
<div><span style="font-style:italic;">// in Hadoop 2.+, so the compiler will call the implicit `Ordering.ordered` method to create an</span><span style="font-style:italic;">  </span></div>
<div><span style="font-style:italic;">// Ordering for `NullWritable`. That's why the compiler will generate different anonymous</span><span style="font-style:italic;">  </span></div>
<div><span style="font-style:italic;">// classes for `saveAsTextFile` in Hadoop 1.+ and Hadoop 2.+.</span><span style="font-style:italic;">  //</span><span style="font-style:italic;">  </span></div>
<div><span style="font-style:italic;">// Therefore, here we provide an explicit Ordering `null` to make sure the compiler generate</span><span style="font-style:italic;">  </span></div>
<div><span style="font-style:italic;">// same bytecodes for `saveAsTextFile`.</span><span style="font-style:italic;">  </span></div>
<div><span style="font-weight:bold;">  val</span> nullWritableClassTag = <span style="font-style:italic;">
implicitly</span>[ClassTag[NullWritable]]</div>
<div>  <span style="font-weight:bold;">val</span> textClassTag = <span style="font-style:italic;">
implicitly</span>[ClassTag[Text]]</div>
<div>  <span style="font-weight:bold;">val</span> r = <span style="font-weight:bold;">
this</span>.mapPartitions { iter =&gt;</div>
<div>    <span style="font-weight:bold;">val</span> text = <span style="font-weight:bold;">
new</span> Text()</div>
<div>    iter.map { x =&gt;</div>
<div>      text.set(x.toString)</div>
<div>      (NullWritable.<span style="font-style:italic;">get</span>(), text)</div>
<div>    }</div>
<div>  }</div>
<div>  RDD.<span style="font-style:italic;">rddToPairRDDFunctions</span>(r)(nullWritableClassTag, textClassTag,<span style="font-weight:bold;">null</span>)</div>
<div>    .saveAsHadoopFile[TextOutputFormat[NullWritable, Text]](path)</div>
<div>}</div>
<div>首先使用mapPartitions算子将每个分区都写入Text文件，然后调用PairRDDFunctions的saveAsHadoopFile写入文件。 </div>
<br></div>
            </div>
                </div>
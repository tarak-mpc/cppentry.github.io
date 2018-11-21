---
layout:     post
title:      HBase HFile bulkLoad过程详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="HBase__bulkLoad_0"></a>HBase  bulkLoad过程详解</h1>
<h2><a id="_2"></a>前言</h2>
<p>在https://blog.csdn.net/yulin_Hu/article/details/82313965  描述了Spark如何生成HFile，及其过程。<br>
生成HFile之后，就需要将HFile load进hbase。本文将对HBase bulkLoad过程进行一个描述。</p>
<h2><a id="bulkload___6"></a>bulkload  命令</h2>
<p>hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles  ${tmp_path}  ${table_name}</p>
<ol>
<li>
<p>discoverLoadQueue方法</p>
<pre class=" language-scala"><code class="prism  language-scala">维护了一个队列，队列的元素是：LoadQueueItem，包含了final byte[] family;  final Path hfilePath两个字段。
        
    discoverLoadQueue方法：在这里调用了visitBulkHFiles，
    对于给的路径下的的文件进行判断，将所有符合规范的需要load的hfile放到一个队列里面。
    在visitBulkHFiles，两层嵌套for 循环
    传入的的一个路径下面应该还包含列族这个目录，
    因为他需要通过路径来得到列族名。会跳过目录下不是目录的目录  

    我们给的的目录下，有多少个列族 就有多少个子目录。遍历这些子目录，校验每一个hfile文件是否合法，如果合法，则形成一个LoadQueueItem，加到队列中。
</code></pre>
</li>
<li>
<p>doBulkLoad(Path hfofDir, final Admin admin, Table table,<br>
RegionLocator regionLocator)：</p>
<pre class=" language-scala"><code class="prism  language-scala"> 在这个方法里，会调用prepareHFileQueue，里面会在调用discoverLoadQueue方法，
得到需要的那个队列Deque&lt;LoadQueueItem&gt; queue

然后得到所有region的的startKey，endKey。
final Pair&lt;byte[][], byte[][]&gt; startEndKeys = regionLocator.getStartEndKeys();
//regionLocator是根据tableName得到的

遍历我们得到的那个队列：判断每一个hfile是否需要进行切割：

  取出一个item，item可以取到对应的hfile，得到这个hfile的firstKey和lastKey。(一个hfile内部已经有序了)

  利用这个hfile的first去和之前得到的sartKey的数组进行search，用的比较器是Bytes.BYTES_COMPARATOR。
  sarch会返回一个index：找到了这个firstKey，就会返回所在的index，没找到返回的是-(insertion point) - 1
  这个insertion point表示的是这个firstKey在这个数组中应该被插入的位置，代表了在startKey这个数组中第一个比firstKey大的key所在的索引
  
  如果说最后得到的index&lt;0, 会进行这个操作 idx = -(idx + 1) - 1  也就是其前面的一个索引
  
  判断完了firstKey，接下来需要判断这个hfile的lastKey:从endKey里面取出前面的idx这个索引的值，如果取出的这个索引的值大于hfile的lastKey或是这个值是empty（代表这是最后一个区），则不需要进行切割
  
  如果不需要进行分割，则得到（startEndKeys.getFirst()[idx]-&gt;item）
  
  如果需要进行切割，则需要将这个item进行切割。从startKeys 和endKeys中分别取得idx这个索引的值，以从endKeys[idx]的值作为splitKey，将原item拆分成一个.top和.bottom的两个文件。
  拆分成的两个hfile会保持和原列族相同的配置
  那么拆分之后有会得到两个新的item，将这两个新的item再一次加到原队列中
  
  重复以上操作。

  具体代码如下：
    private Multimap&lt;ByteBuffer, LoadQueueItem&gt; groupOrSplitPhase(final Table table,
    ExecutorService pool, Deque&lt;LoadQueueItem&gt; queue,
    final Pair&lt;byte[][], byte[][]&gt; startEndKeys) throws IOException {

  Multimap&lt;ByteBuffer, LoadQueueItem&gt; rgs = HashMultimap.create();
  final Multimap&lt;ByteBuffer, LoadQueueItem&gt; regionGroups = Multimaps.synchronizedMultimap(rgs);

  // drain LQIs and figure out bulk load groups
  Set&lt;Future&lt;List&lt;LoadQueueItem&gt;&gt;&gt; splittingFutures = new HashSet&lt;Future&lt;List&lt;LoadQueueItem&gt;&gt;&gt;();  //这个是用来起表示一个hfile是否需要拆分
  while (!queue.isEmpty()) {
    final LoadQueueItem item = queue.remove();

    final Callable&lt;List&lt;LoadQueueItem&gt;&gt; call = new Callable&lt;List&lt;LoadQueueItem&gt;&gt;() {
      @Override
      public List&lt;LoadQueueItem&gt; call() throws Exception {
        List&lt;LoadQueueItem&gt; splits = groupOrSplit(regionGroups, item, table, startEndKeys);
        //这个方法  如果需要拆分 返回拆分之后的结果  如果不需要 擦拆分 返回null 同时将hfile加入到regionGroups
        return splits;
      }
    };
    splittingFutures.add(pool.submit(call));
  }
 

  for (Future&lt;List&lt;LoadQueueItem&gt;&gt; lqis : splittingFutures) {
    try {
      List&lt;LoadQueueItem&gt; splits = lqis.get();
      if (splits != null) {
        queue.addAll(splits);  //把需要擦拆分的重新加入
      }
    }
  return regionGroups;
  }
  也就是说遍历这个队列的时候，遇到需要擦拆分的，我们就拆分，然后重新放进队列，再一次判断(因为可能有些hfile需要拆分多次)，
  遇到不需要拆分的会得到一个startKey-&gt;item。
  一轮的判断得到一个Multimap&lt;ByteBuffer, LoadQueueItem&gt;。ByteBuffer是startkey。

</code></pre>
</li>
<li>
<p>接下来是第二部分</p>
<pre class=" language-scala"><code class="prism  language-scala"> 此处结束后代码回到doBulkLoad的425行
 接下来该方法调用了：bulkLoadPhase
  遍历前面得到的Multimap：它的key是一个startKey的bytes value是一个于其对应的Collection&lt;LoadQueueItem&gt;。key后面跟的是一个list可以理解为要往这一个区间里load的hfile有好几个
       
     如下：
     Set&lt;Future&lt;List&lt;LoadQueueItem&gt;&gt;&gt; loadingFutures = new HashSet&lt;Future&lt;List&lt;LoadQueueItem&gt;&gt;&gt;();
     for (Entry&lt;ByteBuffer, ? extends Collection&lt;LoadQueueItem&gt;&gt; e: regionGroups.asMap().entrySet()) {
       final byte[] first = e.getKey().array();
       final Collection&lt;LoadQueueItem&gt; lqis =  e.getValue();

       final Callable&lt;List&lt;LoadQueueItem&gt;&gt; call = new Callable&lt;List&lt;LoadQueueItem&gt;&gt;() {
         @Override
         public List&lt;LoadQueueItem&gt; call() throws Exception {
           List&lt;LoadQueueItem&gt; toRetry =
               tryAtomicRegionLoad(conn, table.getName(), first, lqis);   //从这里应该可以看出这里应该是一个startKey下面的的所有HFile
           return toRetry;
         }
       };
       loadingFutures.add(pool.submit(call));
     }   
   调用了tryAtomicRegionLoad：该方法说明如下：
   Attempts to do an atomic load of many hfiles into a region.  If it fails,it returns a list of hfiles that need to be retried.  If it is successful it will return an empty list.如果失败了，他会返回需要重试的hfile，会再加入队列中，如果成功会返回一个空。


   这个方法里面会封装成一个请求： BulkLoadHFileRequest request =
   RequestConverter.buildBulkLoadHFileRequest(familyPaths, regionName, assignSeqNum)

     public static BulkLoadHFileRequest buildBulkLoadHFileRequest(
       final List&lt;Pair&lt;byte[], String&gt;&gt; familyPaths,
       final byte[] regionName, boolean assignSeqNum) {
     BulkLoadHFileRequest.Builder builder = BulkLoadHFileRequest.newBuilder();
     RegionSpecifier region = buildRegionSpecifier(
       RegionSpecifierType.REGION_NAME, regionName);
     builder.setRegion(region);
     FamilyPath.Builder familyPathBuilder = FamilyPath.newBuilder();
     for (Pair&lt;byte[], String&gt; familyPath: familyPaths) {
       familyPathBuilder.setFamily(ByteStringer.wrap(familyPath.getFirst()));
       familyPathBuilder.setPath(familyPath.getSecond());
       builder.addFamilyPath(familyPathBuilder.build());
     }
     builder.setAssignSeqNum(assignSeqNum);
     return builder.build();
 }

   然后这个请求会被发送：
     BulkLoadHFileResponse response =client.bulkLoadHFile(null, request)
   这个方法就应该涉及到RPC的远程调用了，这里面调用了ClientService.BlockingInterface的bulkLoadHFile方法
   BlockingInterface的实现有多个，应该是RSRpcService这里的。
</code></pre>
</li>
<li>
<p>RSRpcService.bulkLoadHFile</p>
<pre class=" language-scala"><code class="prism  language-scala">在这个方法中，会根据请求，得到对应的region。
然后会对一个请求进行拆分，一个请求里面博包含这个region的多个family，多个列族。
  List&lt;Pair&lt;byte[], String&gt;&gt; familyPaths = new ArrayList&lt;Pair&lt;byte[], String&gt;&gt;();
  for (FamilyPath familyPath: request.getFamilyPathList()) {
    familyPaths.add(new Pair&lt;byte[], String&gt;(familyPath.getFamily().toByteArray(),
      familyPath.getPath()));
  }

最后又调用了region.bulkLoadHFiles()
region.bulkLoadHFiles(familyPaths, request.getAssignSeqNum());
</code></pre>
</li>
<li>
<p>HRegion.bulkLoadHFiles</p>
<pre class=" language-scala"><code class="prism  language-scala">该方法参数：(Collection&lt;Pair&lt;byte[], String&gt;&gt; familyPaths, boolean assignSeqId,BulkLoadListener bulkLoadListener)

其中 assignSeqId是由hbase.mapreduce.bulkload.assign.sequenceNumbers这个配置决定，默认是true，主要是决定我们是否需要给hfile分配seq_ID。

首先会检查这个region是否是关闭的。然后会给这个region上锁（应该是读写锁）。
向一个region里面load的可能会有多个列族，不同的列族对应不用HStore，需要分别load。

对要load的每一个列族，根据列族名得到其Store，传入路径，调用Store的assertBulkLoadHFileOk方法，这个方法主要是进行一系列的检查，不符合规范则抛出异常。

在load之前，如果文件与当前hbase所在的文件系统不是同一个文件系统，那么则会把hfile copy到当前的文件系统中。

接下来就是load过程，进入HStore的bulkLoadHFile(String srcPathStr, long seqNum)方法。
通过familyName得到需要移到的最终的文件目录，直接就是一个rename的操作，会在目标文件后面追加一个seqID
  
也就是说 如果在同一个文件系统中，移过去之后，源文件就没了。
</code></pre>
<p>前面说过 tryAtomicRegionLoad会发送请求到server端，一个请求包含了一个region里面的所有hfile，会发送这个请求。 RSRpcService.bulkLoadHFile接收请求进行load处理，会一个文件一个文件地移动文件</p>
</li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
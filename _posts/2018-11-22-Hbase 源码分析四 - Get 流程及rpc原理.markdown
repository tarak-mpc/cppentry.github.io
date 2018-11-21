---
layout:     post
title:      Hbase 源码分析四 - Get 流程及rpc原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="c_l"><br></div>
<div class="c_t">
<h1><br></h1>
</div>
<div class="c_a_1"><ins style="display:inline-table;border:none;visibility:visible;width:468px;"></ins><ins id="aswift_0_anchor" style="display:block;border:none;visibility:visible;width:468px;"></ins></div>
<div class="c_t">Hbase 源码分析4 - Get 流程及rpc原理<br><div id="cnblogs_post_body">
<p>分析版本为hbase 0.94</p>
<p>附上趋势团队画的图：</p>
<p><img class="confluence-embedded-image confluence-external-resource" src="http://www.myexception.cn/img/2012/07/27/111233289.png" alt=""></p>
<p>rpc角色表：</p>
<div class="table-wrap">
<table class="confluenceTable"><tbody><tr class="confluenceTr"><td class="confluenceTd" colspan="2">HBase通信信道</td>
<td class="confluenceTd" rowspan="2">HBase的通信接口</td>
</tr><tr class="confluenceTr"><td class="confluenceTd">客户端</td>
<td class="confluenceTd">服务端</td>
</tr><tr class="confluenceTr"><td class="confluenceTd">HBase Client</td>
<td class="confluenceTd">Master Server</td>
<td class="confluenceTd">HMasterInterface</td>
</tr><tr class="confluenceTr"><td class="confluenceTd">HBase Client</td>
<td class="confluenceTd">Region Server</td>
<td class="confluenceTd">HRegionInterface</td>
</tr><tr class="confluenceTr"><td class="confluenceTd">Region Server</td>
<td class="confluenceTd">Master Server</td>
<td class="confluenceTd">HMasterRegionInterface</td>
</tr></tbody></table></div>
<p> </p>
<p>客户端发起请求：</p>
<p>htable.get(Get)</p>
<p> </p>
<pre><code class="language-java">public Result get(final Get get) throws IOException {
return new ServerCallable&lt;Result&gt;(connection, tableName, get.getRow(), operationTimeout) {
public Result call() throws IOException {
return server.get(location.getRegionInfo().getRegionName(), get);
}
}.withRetries();
}</code></pre>
<p> </p>
<p>调用get方法后，客户端进入睡眠，睡眠时间为pause * HConstants.RETRY_BACKOFF[ntries];  </p>
<p>pause= HBASE_CLIENT_PAUSE（1秒）</p>
<p>RETRY_BACKOFF[] = { 1, 1, 1, 2, 2, 4, 4, 8, 16, 32 };</p>
<p>有结果则中断执行返回rpc结果，否则重试十次（默认DEFAULT_HBASE_CLIENT_RETRIES_NUMBER=10）</p>
<p>通过HConnectionManager的getHRegionConnection方法获取连接</p>
<p>通过HRegionServer的get方法获取结果</p>
<p> </p>
<p>服务器端：</p>
<p>当regionserver 收到来自客户端的Get请求时，调用接口 </p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><pre><code class="language-java">public Result get(byte[] regionName, Get get) 
{ 
... 
HRegion region = getRegion(regionName); 
return region.get(get, getLockFromId(get.getLockId())); 
... 
} </code></pre><br><br></div>
<div class="cnblogs_code_toolbar"><br></div>
</div>
<p>在HRegion中</p>
<p>Scan scan = new Scan(get); <br>
会先根据设置的columnFamily存放familyMap对  ----  columnFamily:null</p>
<pre><code class="language-java">public Get addFamily(byte [] family) {
familyMap.remove(family);
familyMap.put(family, null);
return this;
}</code></pre><br><p> </p>
<p>如果查询的family不在htableDescriptor中，返回错误</p>
<pre><code class="language-java">scanner = getScanner(scan);
public RegionScanner getScanner(Scan scan) throws IOException {
return getScanner(scan, null);
}</code></pre>
<p> </p>
<p>additionalScanners为null 所以在RegionScannerImpl的构造中只会使用StoreScanner</p>
<p><br>
return instantiateRegionScanner(scan, additionalScanners);<br>
return new RegionScannerImpl(scan, additionalScanners);</p>
<p>RegionScannerImpl 是 HRegion中的子类</p>
<p> </p>
<pre><code class="language-java">for (Map.Entry&lt;byte[], NavigableSet&lt;byte[]&gt;&gt; entry :
scan.getFamilyMap().entrySet()) {
Store store = stores.get(entry.getKey());
StoreScanner scanner = store.getScanner(scan, entry.getValue());
scanners.add(scanner);
}</code></pre><br><p> </p>
<p>按照familyMap的数量存放对应数量的 StoreScanner</p>
<p>Hregion initialize时会对应每个columnFamily存放一个stores<br>
Future&lt;Store&gt; future = completionService.take();<br>
Store store = future.get();<br>
this.stores.put(store.getColumnFamilyName().getBytes(), store);</p>
<p>scanners 添加从Store中获取的scanner</p>
<p>store.getScanners(cacheBlocks, isGet,<br>
isCompaction, matcher)</p>
<p>Store 类：</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><pre><code class="language-java">memStoreScanners = this.memstore.getScanners();
List&lt;StoreFileScanner&gt; sfScanners = StoreFileScanner
.getScannersForStoreFiles(storeFiles, cacheBlocks, isGet, isCompaction, matcher);
List&lt;KeyValueScanner&gt; scanners =
new ArrayList&lt;KeyValueScanner&gt;(sfScanners.size()+1);
scanners.addAll(sfScanners);
// Then the memstore scanners
scanners.addAll(memStoreScanners);
return scanners;</code></pre><br><br></div>
</div>
<p>memStoreScanners  为Collections.&lt;KeyValueScanner&gt;singletonList(<br>
new MemStoreScanner())</p>
<p> </p>
<p>Store中为StoreScanner添加了StoreFileScanner和 memStoreScanner</p>
<p>进行scan时</p>
<p>scanner = getScanner(scan);<br>
scanner.next(results);</p>
<p> </p>
<p>现在分析RegionScannerImpl中的next方法，此时正式进入获取数据流程</p>
<pre><code class="language-java">@Override
public synchronized boolean next(List&lt;KeyValue&gt; outResults)
throws IOException {
// apply the batching limit by default
return next(outResults, batch);
}</code></pre>
<p> </p>
<p>batch默认为-1</p>
<p> </p>
<p>   startRegionOperation();</p>
<p>  outResults.addAll(results);</p>
<p>startRegionOperation 会为操作加读锁，lock.readLock().lock();</p>
<p>然后遍历storeHeap，找到对应Row</p>
<p>do {<br>
    this.storeHeap.next(results, limit - results.size());<br>
} while (Bytes.equals(currentRow, nextRow = peekRow()));</p>
<p> this.storeHeap 会不断poll出存储的scanner</p>
<p>因RegionScannerImpl 中 memStoreScanners后添加，所以会先从memStoreScanners中查询，如果没有则从StoreFileScanner中查询</p>
<p>RegionScannerImpl 的 storeHeap为KeyValueHeap，会强制转型scanner为 InternalScanner</p>
<p>InternalScanner currentAsInternal = (InternalScanner)this.current;  </p>
<p> </p>
<p>总结下目前流程get request -&gt; regionServer -&gt; region -&gt; storeHeap -&gt; scanner -&gt; find row</p>
<p> </p>
<p>但上述流程没有解释reguest是怎么找到regionServer去处理请求的，下边我们在分析下</p>
<p>服务器端服务在HMaster和HRegionServer启动时，中都会生成一个全局的RpcServer  </p>
<p>hmaster的rpc server：</p>
<p>hmaster会使用org.apache.hadoop.hbase.executor.ExecutorService启动多种线程服务 （This is a generic executor service. This component abstracts a threadpool, a queue to which<code>EventHandler.EventType</code>s can be submitted, and a<code>Runnable</code> that handles
 the object that is added to the queue. ）：</p>
<p>MASTER_OPEN_REGION   （默认5）</p>
<p>MASTER_CLOSE_REGION   （默认5）</p>
<p>MASTER_SERVER_OPERATIONS  （默认3）</p>
<p>MASTER_META_SERVER_OPERATIONS  （默认5）</p>
<p>MASTER_TABLE_OPERATIONS （单线程）</p>
<p>logCleaner （单线程）</p>
<p>infoServer  (master-status 等信息展示)</p>
<p>rpcServer （我们需要用的rpc服务）</p>
<p>RpcServer是个接口，实现类为HBaseServer，启动时会开启responder listener handlers几种类去响应请求，如设置了priorityHandlers的数目，会另外启动priorityHandlers，listener监听端口，提供请求给handlers，handlers则调用RpcEngine，反射出需要的方法并执行，通过responder写结果回去（this.responder.doRespond）。</p>
<p>HMaster的 handlers的个数由hbase.master.handler.count</p>
<p>HRegionServer的 handlers的个数由 hbase.regionserver.handler.count 指定</p>
<p>HRegionServer的启动和HMaster类似，它启动以下线程：</p>
<div class="cnblogs_code">
<div class="cnblogs_code_toolbar"><pre><code class="language-java">this.service = new ExecutorService(getServerName().toString());
this.service.startExecutorService(ExecutorType.RS_OPEN_REGION,
conf.getInt("hbase.regionserver.executor.openregion.threads", 3));
this.service.startExecutorService(ExecutorType.RS_OPEN_ROOT,
conf.getInt("hbase.regionserver.executor.openroot.threads", 1));
this.service.startExecutorService(ExecutorType.RS_OPEN_META,
conf.getInt("hbase.regionserver.executor.openmeta.threads", 1));
this.service.startExecutorService(ExecutorType.RS_CLOSE_REGION,
conf.getInt("hbase.regionserver.executor.closeregion.threads", 3));
this.service.startExecutorService(ExecutorType.RS_CLOSE_ROOT,
conf.getInt("hbase.regionserver.executor.closeroot.threads", 1));
this.service.startExecutorService(ExecutorType.RS_CLOSE_META,
conf.getInt("hbase.regionserver.executor.closemeta.threads", 1));</code></pre><br><br></div>
</div>
<p>hlogRoller(daemon)</p>
<p>cacheFlusher(daemon)</p>
<p>compactionChecker(daemon)</p>
<p>Leases(它不是线程，会启动后台线程)</p>
<p>splitLogWorker</p>
<p>rpcServer</p>
<p> </p>
<p>HBaseClient 和 HMaster关系由HMasterInterface描述：</p>
<p>Clients interact with the HMasterInterface to gain access to meta-level<br>
 HBase functionality, like finding an HRegionServer and creating/destroying<br>
 tables.</p>
<p>HBaseClient 和 HRegionServer关系由HRegionInterface描述：</p>
<p>Clients interact with HRegionServers using a handle to the HRegionInterface</p>
<p> </p>
<p>参考资料：</p>
<p>http://zjushch.iteye.com/blog/1173304</p>
<p>http://www.spnguru.com/2010/07/hbase-%E6%BA%90%E7%A0%81%E5%88%86%E6%9E%90-rpc%E6%9C%BA%E5%88%B6-%E5%9F%BA%E7%A1%80/</p>
</div>
</div>
            </div>
                </div>
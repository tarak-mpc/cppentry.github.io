---
layout:     post
title:      HBase多线程建立HTable问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>﻿﻿</div>
最近在写wormhole的HBase plugin，需要分别实现hbase reader和hbase writer。
<p>　　<strong>相关阅读</strong>：</p>
<p>　　Hadoop+HBase搭建云存储总结 PDF </p>
<p>　　HBase 结点之间时间不一致造成regionserver启动失败 </p>
<p>　　Hadoop+ZooKeeper+HBase集群配置 </p>
<p>　　在测试的时候会报错如下：</p>
<p>　　2013-07-08 09:30:02,568 [pool-2-thread-1] org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation.processBatchCallback(HConnectionManager.java:1631) WARN  client.HConnectionManager$HConnectionImplementation - Failed all from region=t1,,1373246892580.877bb26da1e4aed541915870fa924224.,
 hostname=test89.hadoop, port=60020<br>
ncurrent.ExecutionException: java.io.IOException: Call to test89.hadoop/10.1.77.89:60020 failed on local exception: java.io.InterruptedIOException: Interruped while waiting for IO on channel java.nio.channels.SocketChannel[connected local=/10.1.77.84:51032
 remote=test89.hadoop/10.1.77.89:60020]. 59999 millis timeout left.<br>
 at ncurrent.FutureTask$Sync.innerGet(FutureTask.java:222)<br>
 at ncurrent.FutureTask.get(FutureTask.java:83)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation.processBatchCallback(HConnectionManager.java:1601)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation.processBatch(HConnectionManager.java:1453)<br>
 at org.apache.hadoop.hbase.client.HTable.flushCommits(HTable.java:936)<br>
 at org.apache.hadoop.hbase.client.HTable.put(HTable.java:783)<br>
 at com.dp.nebula.wormholmon.HBaseClient.flush(HBaseClient.java:121)<br>
 at com.dp.nebula.wormhole.plugins.writer.hbasewritemit(HBaseWriter.java:112)<br>
 at com.dp.nebula.wormholre.WriterThread.call(WriterThread.java:52)<br>
 at com.dp.nebula.wormholre.WriterThread.call(WriterThread.java:1)<br>
 at ncurrent.FutureTask$Sync.innerRun(FutureTask.java:303)<br>
 at ncurrent.FutureTask.run(FutureTask.java:138)<br>
 at ncurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:895)<br>
 at ncurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:918)<br>
 at java.lang.Thread.run(Thread.java:662)<br>
Caused by: java.io.IOException: Call to test89.hadoop/10.1.77.89:60020 failed on local exception: java.io.InterruptedIOException: Interruped while waiting for IO on channel java.nio.channels.SocketChannel[connected local=/10.1.77.84:51032 remote=test89.hadoop/10.1.77.89:60020].
 59999 millis timeout left.<br>
 at org.apache.hadoop.hbase.ipc.HBaseClient.wrapException(HBaseClient.java:1030)<br>
 at org.apache.hadoop.hbase.ipc.HBaseClient.call(HBaseClient.java:999)<br>
 at org.apache.hadoop.hbase.ipc.SecureRpcEngine$Invoker.invoke(SecureRpcEngine.java:104)<br>
? at com.sun.<span id="6_nwp"><a id="6_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.educity.cn%2Fnet%2F1617999.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=67051059_cpr&amp;k=proxy&amp;k0=%D4%B4%B4%FA%C2%EB&amp;kdi0=0&amp;k1=proxy&amp;kdi1=0&amp;k2=%B5%D7%B2%E3&amp;kdi2=0&amp;k3=admin&amp;kdi3=0&amp;k4=%D0%A3%D7%BC&amp;kdi4=0&amp;sid=def32b355efa3a11&amp;ch=0&amp;tu=u1740074&amp;jk=40c75c0f88901541&amp;cf=29&amp;rb=0&amp;fv=16&amp;stid=9&amp;urlid=0&amp;luki=2&amp;seller_id=1&amp;di=128" rel="nofollow" style="text-decoration:none;"><span style="color:rgb(0,0,255);font-size:16px;">proxy</span></a></span>.$Proxy5.multi(Unknown
 Source)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation$3$1.call(HConnectionManager.java:1430)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation$3$1.call(HConnectionManager.java:1428)<br>
 at org.apache.hadoop.hbase.client.ServerCallable.withoutRetries(ServerCallable.java:215)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation$3.call(HConnectionManager.java:1437)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation$3.call(HConnectionManager.java:1425)<br>
 ... 5 more<br>
Caused by: java.io.InterruptedIOException: Interruped while waiting for IO on channel java.nio.channels.SocketChannel[connected local=/10.1.77.84:51032 remote=test89.hadoop/10.1.77.89:60020]. 59999 millis timeout left.<br>
2013-07-08 09:30:03,579 [pool-2-thread-6] com.dp.nebula.wormholre.WriterThread.call(WriterThread.java:56) ERROR core.WriterThread - Exception occurs in writer thread!<br>
com.dmon.WormholeException: java.io.IOException: &lt;SPAN &gt;org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation@b7c96a9 closed&lt;/SPAN&gt;<br>
 at com.dp.nebula.wormhole.plugins.writer.hbasewritemit(HBaseWriter.java:114)<br>
 at com.dp.nebula.wormholre.WriterThread.call(WriterThread.java:52)<br>
 at com.dp.nebula.wormholre.WriterThread.call(WriterThread.java:1)<br>
 at ncurrent.FutureTask$Sync.innerRun(FutureTask.java:303)<br>
 at ncurrent.FutureTask.run(FutureTask.java:138)<br>
 at ncurrent.ThreadPoolExecutor$Worker.runTask(ThreadPoolExecutor.java:895)<br>
 at ncurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:918)<br>
 at java.lang.Thread.run(Thread.java:662)<br>
Caused by: java.io.IOException: org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation@b7c96a9 closed<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation.locateRegion(HConnectionManager.java:877)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation.locateRegion(HConnectionManager.java:857)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation.processBatchCallback(HConnectionManager.java:1568)<br>
 at org.apache.hadoop.hbase.client.HConnectionManager$HConnectionImplementation.processBatch(HConnectionManager.java:1453)<br>
 at org.apache.hadoop.hbase.client.HTable.flushCommits(HTable.java:936)<br>
 at org.apache.hadoop.hbase.client.HTable.put(HTable.java:783)<br>
 at com.dp.nebula.wormholmon.HBaseClient.flush(HBaseClient.java:121)<br>
 at com.dp.nebula.wormhole.plugins.writer.hbasewritemit(HBaseWriter.java:112)<br>
 ... 7 more</p>
<p>　　wormhole的reader和writer会分别起一个ThreadPoolExecutor，出错是在writer端的flush阶段，也就是最后一次批量插入操作。由于我的reader是每一个thread一个htable instance没有问题，而writer是共用了一个singleton HBaseClient，然后用ThreadLocal去保证每一个thread拥有一个本地htable对象，有可能有错误，最简单的方法是把writer端不用singleton HBaseClient，问题应该解决，不过没搞清root
 cause，不爽啊。。。</p>
<p>　　后来看了HTable和HAdmin的<span id="5_nwp"><a id="5_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.educity.cn%2Fnet%2F1617999.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=67051059_cpr&amp;k=%D4%B4%B4%FA%C2%EB&amp;k0=%D4%B4%B4%FA%C2%EB&amp;kdi0=0&amp;k1=proxy&amp;kdi1=0&amp;k2=%B5%D7%B2%E3&amp;kdi2=0&amp;k3=admin&amp;kdi3=0&amp;k4=%D0%A3%D7%BC&amp;kdi4=0&amp;sid=def32b355efa3a11&amp;ch=0&amp;tu=u1740074&amp;jk=40c75c0f88901541&amp;cf=29&amp;rb=0&amp;fv=16&amp;stid=9&amp;urlid=0&amp;luki=1&amp;seller_id=1&amp;di=128" rel="nofollow" style="text-decoration:none;"><span style="color:rgb(0,0,255);font-size:16px;">源代码</span></a></span>才有点线索</p>
<p>　　public HTable(Configuration conf, final byte [] tableName)<br>
  throws IOException {<br>
    this.tableName = tableName;<br>
    this.cleanupPoolOnClose = this.cleanupConnectionOnClose = true;<br>
    if (conf == null) {<br>
      nnection = null;<br>
      return;<br>
    }<br>
    nnection = HConnectionManager.getConnection(conf);<br>
    nfiguration = conf;<br>
    int maxThreads = conf.getInt("hbase.htable.threads.max", Integer.MAX_VALUE);<br>
    if (maxThreads == 0) {<br>
      maxThreads = 1; // is there a better default?<br>
    }<br>
    long keepAliveTime = conf.getLong("hbase.htable.threads.keepalivetime", 60);<br>
    ((ThreadPoolExecutor)this.pool).allowCoreThreadTimeOut(true);<br>
    this.finishSetup();<br>
  }</p>
<p>　　每一个HTable instance都有一个HConnection对象，它负责与Zookeeper和之后的HBase Cluster建立链接（比如cluster中定位region，locations的cache，当region移动后重新<span id="4_nwp"><a id="4_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.educity.cn%2Fnet%2F1617999.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=67051059_cpr&amp;k=%D0%A3%D7%BC&amp;k0=%D4%B4%B4%FA%C2%EB&amp;kdi0=0&amp;k1=proxy&amp;kdi1=0&amp;k2=%B5%D7%B2%E3&amp;kdi2=0&amp;k3=admin&amp;kdi3=0&amp;k4=%D0%A3%D7%BC&amp;kdi4=0&amp;sid=def32b355efa3a11&amp;ch=0&amp;tu=u1740074&amp;jk=40c75c0f88901541&amp;cf=29&amp;rb=0&amp;fv=16&amp;stid=9&amp;urlid=0&amp;luki=5&amp;seller_id=1&amp;di=128" rel="nofollow" style="text-decoration:none;"><span style="color:rgb(0,0,255);font-size:16px;">校准</span></a></span>），它由HConnectionManager来管理</p>
<p>　　public static HConnection getConnection(Configuration conf)<br>
  throws ZooKeeperConnectionException {<br>
    HConnectionKey connectionKey = new HConnectionKey(conf);<br>
    synchronized (HBASE_INSTANCES) {<br>
      HConnectionImplementation connection = HBASE_INSTANCES.get(connectionKey);<br>
      if (connection == null) {<br>
        connection = new HConnectionImplementation(conf, true);<br>
        HBASE_INSTANCES.put(connectionKey, connection);<br>
      }<br>
      connection.incCount();<br>
      return connection;<br>
    }<br>
  }</p>
<p>　　HConnectionManager内部有LRU MAP =&gt; HBASE_INSTANCES的静态变量作为cache，key为HConnectionKey，包含了username和指定的properties(由传进去的conf提取), value就是HConnection具体实现HConnectionImplementation，由于传入进去的conf都一样，所以都指向同一个HConnectionImplementation，最后会调用connection.incCount()将client reference
 count加1</p>
<p>　　public void close() throws IOException {<br>
if (this.closed) {<br>
return;<br>
}<br>
flushCommits();<br>
if (cleanupPoolOnClose) {<br>
this.pool.shutdown();<br>
}<br>
if (cleanupConnectionOnClose) {<br>
if (nnection != null) {<br>
nnection.close();<br>
}<br>
}<br>
this.closed = true;<br>
}</p>
<p>　　HTable close的时候，会先flushCommits，将writerBuffer中的List&lt;Put&gt;一次性通过connection的processBatch方法处理掉，然后进入close connection逻辑，依据也是reference count，先对其减1，当为0或者是过期connection，就会执行close connection并从HBASE_INSTANCES中remove掉。</p>
<p>　　关闭步骤：</p>
<p>　　1. 关闭HMasterInterface</p>
<p>　　2. 关闭一串HRegionInterface</p>
<p>　　3. 关闭zookeeper watcher</p>
<p>　　HConnectionImplementation connection = HBASE_INSTANCES<br>
          .get(connectionKey);<br>
      if (connection != null) {<br>
        connection.decCount();<br>
        if (connection.isZeroReference() || staleConnection) {<br>
          HBASE_INSTANCES.remove(connectionKey);<br>
          connection.close(stopProxy);<br>
        } else if (stopProxy) {<br>
          connection.stopProxyOnClose(stopProxy);<br>
        }<br>
      }else {<br>
        LOG.error("Connection not found in the list, can't delete it "+<br>
          "(connection key="+connectionKey+"). May be the key was modified?");<br>
      }</p>
<p>　　HBaseAdmin内部也是一样，也是reference了一个HConnection，可以认为和HTable是共享HConnection</p>
<p>　　分析到这里再联想到之前的报错信息"java.io.IOException: org.apache.Hadoop.hbase.client.HConnectionManager$HConnectionImplementation@b7c96a9 closed"，应该是client reference count数的问题，果然在我的HBaseClient代码里面在初始化环节会new一个singleton HBaseClient，里面new一个HBaseAdmin（thread共享），在各个thread启动时候会new
 一个threadloca的htable对象</p>
<p>　　private HTable getHTable() throws IOException {<br>
  HTable htable = threadLocalHtable.get();<br>
  if (htable == null) {<br>
   htable = new HTable(conf, tableName);<br>
   htable.setAutoFlush(autoFlush);<br>
   htable.setWriteBufferSize(writeBufferSize);<br>
   threadLocalHtable.set(htable);<br>
  }<br>
  return htable;<br>
 }</p>
<p>　　但是我的close方法写的有问题，把HBaseAdmin close了很多遍（每个thread close了一次）</p>
<p>　　public synchronized void close() throws IOException {<br>
  HTable table = threadLocalHtable.get();<br>
  if (table != null) {<br>
      ?table.close();<br>
    ?    ?    ?    ?    ?table = null;<br>
    ?    ?    ?    ?    ?threadLocalHtable.remove();<br>
  }<br>
  if (admin != null) {<br>
   <span id="3_nwp"><a id="3_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.educity.cn%2Fnet%2F1617999.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=67051059_cpr&amp;k=admin&amp;k0=%D4%B4%B4%FA%C2%EB&amp;kdi0=0&amp;k1=proxy&amp;kdi1=0&amp;k2=%B5%D7%B2%E3&amp;kdi2=0&amp;k3=admin&amp;kdi3=0&amp;k4=%D0%A3%D7%BC&amp;kdi4=0&amp;sid=def32b355efa3a11&amp;ch=0&amp;tu=u1740074&amp;jk=40c75c0f88901541&amp;cf=29&amp;rb=0&amp;fv=16&amp;stid=9&amp;urlid=0&amp;luki=4&amp;seller_id=1&amp;di=128" rel="nofollow" style="text-decoration:none;"><span style="color:rgb(0,0,255);font-size:16px;">admin</span></a></span>.close();<br>
  }<br>
 }</p>
<p>　　假设一种场景，有10个writer线程，那么reference count是11 (10个HTable和1个HBaseAdmin)，前5个thread close没问题，第6个thread先close htable，然后发现引用数为0了，它关闭了HConnection，然后再close admin无效，但是其余4个线程这时候有可能在执行flush操作，HConnection既然已经断了，那肯定没办法flush完啊，抛出异常出来</p>
<p>　　public void flush() throws IOException {<br>
  if (getPutBuffer().size() &gt; 0) {<br>
   getHTable().put(getPutBuffer());<br>
   clearPutBuffer();<br>
  }<br>
 }</p>
<p>　　知道了错误原因后，果断在close HBaseAdmin后，把它指向NULL,这样就不会导致重复关闭的问题了</p>
<p>　　if (admin != null) {<br>
   admin.close();<br>
   <span id="2_nwp"><a id="2_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.educity.cn%2Fnet%2F1617999.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=67051059_cpr&amp;k=admin&amp;k0=%D4%B4%B4%FA%C2%EB&amp;kdi0=0&amp;k1=proxy&amp;kdi1=0&amp;k2=%B5%D7%B2%E3&amp;kdi2=0&amp;k3=admin&amp;kdi3=0&amp;k4=%D0%A3%D7%BC&amp;kdi4=0&amp;sid=def32b355efa3a11&amp;ch=0&amp;tu=u1740074&amp;jk=40c75c0f88901541&amp;cf=29&amp;rb=0&amp;fv=16&amp;stid=9&amp;urlid=0&amp;luki=4&amp;seller_id=1&amp;di=128" rel="nofollow" style="text-decoration:none;"><span style="color:rgb(0,0,255);font-size:16px;">admin</span></a></span>
 = null;<br>
  }</p>
<p>　　这个问题困扰我几天，网上又找不到任何资料，后来还是看了HBase<span id="0_nwp"><a id="0_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.educity.cn%2Fnet%2F1617999.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=67051059_cpr&amp;k=%D4%B4%B4%FA%C2%EB&amp;k0=%D4%B4%B4%FA%C2%EB&amp;kdi0=0&amp;k1=proxy&amp;kdi1=0&amp;k2=%B5%D7%B2%E3&amp;kdi2=0&amp;k3=admin&amp;kdi3=0&amp;k4=%D0%A3%D7%BC&amp;kdi4=0&amp;sid=def32b355efa3a11&amp;ch=0&amp;tu=u1740074&amp;jk=40c75c0f88901541&amp;cf=29&amp;rb=0&amp;fv=16&amp;stid=9&amp;urlid=0&amp;luki=1&amp;seller_id=1&amp;di=128" rel="nofollow" style="text-decoration:none;"><span style="color:rgb(0,0,255);font-size:16px;">源代码</span></a></span>才有新的发现并解决的，看来真碰到问题一定要懂<span id="1_nwp"><a id="1_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.educity.cn%2Fnet%2F1617999.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=67051059_cpr&amp;k=%B5%D7%B2%E3&amp;k0=%D4%B4%B4%FA%C2%EB&amp;kdi0=0&amp;k1=proxy&amp;kdi1=0&amp;k2=%B5%D7%B2%E3&amp;kdi2=0&amp;k3=admin&amp;kdi3=0&amp;k4=%D0%A3%D7%BC&amp;kdi4=0&amp;sid=def32b355efa3a11&amp;ch=0&amp;tu=u1740074&amp;jk=40c75c0f88901541&amp;cf=29&amp;rb=0&amp;fv=16&amp;stid=9&amp;urlid=0&amp;luki=3&amp;seller_id=1&amp;di=128" rel="nofollow" style="text-decoration:none;"><span style="color:rgb(0,0,255);font-size:16px;">底层</span></a></span>代码才行。</p>
            </div>
                </div>
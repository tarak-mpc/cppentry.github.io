---
layout:     post
title:      HBase源码分析之Client端操作处理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/xiangel/article/details/53537637				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<pre><code>hbase version 1.1.5
</code></pre>

<p>使用HBase的服务要先建立与HBase服务器的连接，这里我们从hbase client的建立连接开始HBase源码分析的旅程。 <br>
    先看一段hbase连接的代码</p>



<pre class="prettyprint"><code class="language-java hljs ">Connection connection = ConnectionFactory.createConnection(config);
Table table = connection.getTable(TableName.valueOf(<span class="hljs-string">"table1"</span>));
<span class="hljs-keyword">try</span> {
    <span class="hljs-comment">// Use the table as needed, for a single operation and a single thread</span>
} <span class="hljs-keyword">finally</span> {
    table.close();
    connection.close();
}</code></pre>



<h2 id="connection接口">Connection接口</h2>

<p>Connection接口有定义以下常用方法</p>



<pre class="prettyprint"><code class="language-java hljs ">Table getTable(TableName tableName)  <span class="hljs-comment">// 返回一个Table的实现（HTable），用于访问hbase的表</span>
<span class="hljs-keyword">public</span> RegionLocator <span class="hljs-title">getRegionLocator</span>(TableName tableName)  //返回表的region信息
Admin <span class="hljs-title">getAdmin</span>() //返回Admin的实现来管理HBase集群</code></pre>

<p>这里我们先分析Table接口以及其实现HTable，先看看Table接口定义的方法，hbase table的所有的操作都定义在这了。</p>



<pre class="prettyprint"><code class=" hljs actionscript">boolean exists(Get <span class="hljs-keyword">get</span>) 
Result <span class="hljs-keyword">get</span>(Get <span class="hljs-keyword">get</span>)
Result[] <span class="hljs-keyword">get</span>(List&lt;Get&gt; gets) 
ResultScanner getScanner(Scan scan) 
<span class="hljs-keyword">void</span> put(Put put) 
<span class="hljs-keyword">void</span> put(List&lt;Put&gt; puts) 
<span class="hljs-keyword">void</span> <span class="hljs-keyword">delete</span>(Delete <span class="hljs-keyword">delete</span>)
<span class="hljs-keyword">void</span> mutateRow(<span class="hljs-keyword">final</span> RowMutations rm)</code></pre>



<h2 id="htable的实现">HTable的实现</h2>



<h3 id="get">Get</h3>

<p>接下来我们来看HTable的实现，首先我们从Get方法开始 <br>
先看最简单的单个Get请求</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-comment">// HTable.java</span>
 <span class="hljs-keyword">private</span> Result <span class="hljs-title">get</span>(Get <span class="hljs-keyword">get</span>, final boolean checkExistenceOnly) throws IOException {
    <span class="hljs-comment">// if we are changing settings to the get, clone it.</span>
   ...

    <span class="hljs-keyword">if</span> (<span class="hljs-keyword">get</span>.getConsistency() == Consistency.STRONG) {
      <span class="hljs-comment">// Good old call.</span>
      final Get getReq = <span class="hljs-keyword">get</span>;
      RegionServerCallable&lt;Result&gt; callable = <span class="hljs-keyword">new</span> RegionServerCallable&lt;Result&gt;(<span class="hljs-keyword">this</span>.connection,
          getName(), <span class="hljs-keyword">get</span>.getRow()) {
        @Override
        <span class="hljs-keyword">public</span> Result <span class="hljs-title">call</span>(<span class="hljs-keyword">int</span> callTimeout) throws IOException {
          ClientProtos.GetRequest request =
            RequestConverter.buildGetRequest(getLocation().getRegionInfo().getRegionName(), getReq);
          PayloadCarryingRpcController controller = rpcControllerFactory.newController();
          controller.setPriority(tableName);
          controller.setCallTimeout(callTimeout);
          <span class="hljs-keyword">try</span> {
            ClientProtos.GetResponse response = getStub().<span class="hljs-keyword">get</span>(controller, request);
            <span class="hljs-keyword">if</span> (response == <span class="hljs-keyword">null</span>) <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
            <span class="hljs-keyword">return</span> ProtobufUtil.toResult(response.getResult());
          } <span class="hljs-keyword">catch</span> (ServiceException se) {
            <span class="hljs-keyword">throw</span> ProtobufUtil.getRemoteException(se);
          }
        }
      };
      <span class="hljs-keyword">return</span> rpcCallerFactory.&lt;Result&gt;newCaller(rpcTimeout).callWithRetries(callable,
          <span class="hljs-keyword">this</span>.operationTimeout);
    }

    <span class="hljs-comment">// Call that takes into account the replica</span>
    RpcRetryingCallerWithReadReplicas callable = <span class="hljs-keyword">new</span> RpcRetryingCallerWithReadReplicas(
      rpcControllerFactory, tableName, <span class="hljs-keyword">this</span>.connection, <span class="hljs-keyword">get</span>, pool,
      connConfiguration.getRetriesNumber(),
      operationTimeout,
      connConfiguration.getPrimaryCallTimeoutMicroSecond());
    <span class="hljs-keyword">return</span> callable.call();
  }</code></pre>

<p>通过代码可以看出get请求直接构造了一个RegionServerCallable对象，通过RegionServerCallable的文档说该类是实现一个到RegionServer的调用，那怎么确定是哪个RegionServer和怎么和RegionServer建立连接的呢？我们来看看RegionServerCallable的prepare方法</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// RegionServerCallable.java</span>
 <span class="hljs-javadoc">/**
   * Prepare for connection to the server hosting region with row from tablename.  Does lookup
   * to find region location and hosting server.
   *<span class="hljs-javadoctag"> @param</span> reload Set this to true if connection should re-find the region
   *<span class="hljs-javadoctag"> @throws</span> IOException e
   */</span>
  <span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">prepare</span>(<span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> reload) <span class="hljs-keyword">throws</span> IOException {
    <span class="hljs-keyword">try</span> (RegionLocator regionLocator = connection.getRegionLocator(tableName)) {
      <span class="hljs-keyword">this</span>.location = regionLocator.getRegionLocation(row, reload);
    }
    <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.location == <span class="hljs-keyword">null</span>) {
      <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> IOException(<span class="hljs-string">"Failed to find location, tableName="</span> + tableName +
        <span class="hljs-string">", row="</span> + Bytes.toString(row) + <span class="hljs-string">", reload="</span> + reload);
    }
    setStub(getConnection().getClient(<span class="hljs-keyword">this</span>.location.getServerName()));
  }</code></pre>

<p>通过这里我们可以看出通过regionLocator.getRegionLocation 获取row所属region的regionserver的地址信息。再通过调用HConnetion.getClient 建立和对应regionserver的连接，返回一个regionserver的ClientProtocol proxy 。这样我们就可以封装一个请求和regionserver通信了。调用regionserver rpcserver的get方法。</p>



<pre class="prettyprint"><code class=" hljs axapta"><span class="hljs-comment">// HRegionLocation.java</span>
<span class="hljs-keyword">public</span> <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">HRegionLocation</span> <span class="hljs-inheritance"><span class="hljs-keyword">implements</span></span> <span class="hljs-title">Comparable</span>&lt;<span class="hljs-title">HRegionLocation</span>&gt; {</span>
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> HRegionInfo regionInfo;
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> ServerName serverName;
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">final</span> <span class="hljs-keyword">long</span> seqNum;</code></pre>

<p>这里单个的Get方法的客户端流程就走完了。下面我们来看看传入一个List的Get方法</p>



<pre class="prettyprint"><code class=" hljs r">// HTable.java
public Result[] get(List&lt;Get&gt; gets) throws IOException {
    <span class="hljs-keyword">if</span> (gets.size() == <span class="hljs-number">1</span>) {
      <span class="hljs-keyword">return</span> new Result[]{get(gets.get(<span class="hljs-number">0</span>))};
    }
    <span class="hljs-keyword">try</span> {
      Object [] r1 = batch((List)gets);

      // translate.
      Result [] results = new Result[r1.length];
      <span class="hljs-keyword">...</span>
    }
  }</code></pre>

<p>实际上这里就是调用了batch方法，</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// HTable.java</span>
<span class="hljs-keyword">protected</span> AsyncProcess multiAp;

<span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">batch</span>(<span class="hljs-keyword">final</span> List&lt;? extends Row&gt; actions, <span class="hljs-keyword">final</span> Object[] results)
      <span class="hljs-keyword">throws</span> InterruptedException, IOException {
    AsyncRequestFuture ars = multiAp.submitAll(pool, tableName, actions, <span class="hljs-keyword">null</span>, results);
    ars.waitUntilDone();
    <span class="hljs-keyword">if</span> (ars.hasError()) {
      <span class="hljs-keyword">throw</span> ars.getErrors();
    }
  }</code></pre>

<p>这里通过AsyncProcess.submitAll方法来提交操作请求</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// AsyncProcess.java</span>
<span class="hljs-keyword">public</span> &lt;CResult&gt; AsyncRequestFuture <span class="hljs-title">submitAll</span>(ExecutorService pool, TableName tableName,
      List&lt;? extends Row&gt; rows, Batch.Callback&lt;CResult&gt; callback, Object[] results) {
    List&lt;Action&lt;Row&gt;&gt; actions = <span class="hljs-keyword">new</span> ArrayList&lt;Action&lt;Row&gt;&gt;(rows.size());

    <span class="hljs-comment">// The position will be used by the processBatch to match the object array returned.</span>
    <span class="hljs-keyword">int</span> posInList = -<span class="hljs-number">1</span>;
    NonceGenerator ng = <span class="hljs-keyword">this</span>.connection.getNonceGenerator();
    <span class="hljs-keyword">for</span> (Row r : rows) {
      posInList++;
      <span class="hljs-keyword">if</span> (r <span class="hljs-keyword">instanceof</span> Put) {
        Put put = (Put) r;
        <span class="hljs-keyword">if</span> (put.isEmpty()) {
          <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> IllegalArgumentException(<span class="hljs-string">"No columns to insert for #"</span> + (posInList+<span class="hljs-number">1</span>)+ <span class="hljs-string">" item"</span>);
        }
      }
      Action&lt;Row&gt; action = <span class="hljs-keyword">new</span> Action&lt;Row&gt;(r, posInList);
      setNonce(ng, r, action);
      actions.add(action);
    }
    AsyncRequestFutureImpl&lt;CResult&gt; ars = createAsyncRequestFuture(
        tableName, actions, ng.getNonceGroup(), getPool(pool), callback, results, results != <span class="hljs-keyword">null</span>);
    ars.groupAndSendMultiAction(actions, <span class="hljs-number">1</span>);
    <span class="hljs-keyword">return</span> ars;
  }</code></pre>

<p>添加一个nonce的批次信息后，再通过AsyncRequestFutureImpl.groupAndSendMultiAction提交</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">private</span> <span class="hljs-keyword">void</span> <span class="hljs-title">groupAndSendMultiAction</span>(List&lt;Action&lt;Row&gt;&gt; currentActions, <span class="hljs-keyword">int</span> numAttempt) {
      Map&lt;ServerName, MultiAction&lt;Row&gt;&gt; actionsByServer =
          <span class="hljs-keyword">new</span> HashMap&lt;ServerName, MultiAction&lt;Row&gt;&gt;();

      <span class="hljs-keyword">boolean</span> isReplica = <span class="hljs-keyword">false</span>;
      List&lt;Action&lt;Row&gt;&gt; unknownReplicaActions = <span class="hljs-keyword">null</span>;
      <span class="hljs-keyword">for</span> (Action&lt;Row&gt; action : currentActions) {
        RegionLocations locs = findAllLocationsOrFail(action, <span class="hljs-keyword">true</span>);
        <span class="hljs-keyword">if</span> (locs == <span class="hljs-keyword">null</span>) <span class="hljs-keyword">continue</span>;
        <span class="hljs-keyword">boolean</span> isReplicaAction = !RegionReplicaUtil.isDefaultReplica(action.getReplicaId());
        <span class="hljs-keyword">if</span> (isReplica &amp;&amp; !isReplicaAction) {
          <span class="hljs-comment">// This is the property of the current implementation, not a requirement.</span>
          <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> AssertionError(<span class="hljs-string">"Replica and non-replica actions in the same retry"</span>);
        }
        isReplica = isReplicaAction;
        HRegionLocation loc = locs.getRegionLocation(action.getReplicaId());
        <span class="hljs-keyword">if</span> (loc == <span class="hljs-keyword">null</span> || loc.getServerName() == <span class="hljs-keyword">null</span>) {
          <span class="hljs-keyword">if</span> (isReplica) {
            <span class="hljs-keyword">if</span> (unknownReplicaActions == <span class="hljs-keyword">null</span>) {
              unknownReplicaActions = <span class="hljs-keyword">new</span> ArrayList&lt;Action&lt;Row&gt;&gt;();
            }
            unknownReplicaActions.add(action);
          } <span class="hljs-keyword">else</span> {
            <span class="hljs-comment">// TODO: relies on primary location always being fetched</span>
            manageLocationError(action, <span class="hljs-keyword">null</span>);
          }
        } <span class="hljs-keyword">else</span> {
          <span class="hljs-keyword">byte</span>[] regionName = loc.getRegionInfo().getRegionName();
          addAction(loc.getServerName(), regionName, action, actionsByServer, nonceGroup);
        }
      }
      <span class="hljs-keyword">boolean</span> doStartReplica = (numAttempt == <span class="hljs-number">1</span> &amp;&amp; !isReplica &amp;&amp; hasAnyReplicaGets);
      <span class="hljs-keyword">boolean</span> hasUnknown = unknownReplicaActions != <span class="hljs-keyword">null</span> &amp;&amp; !unknownReplicaActions.isEmpty();

      <span class="hljs-keyword">if</span> (!actionsByServer.isEmpty()) {
        <span class="hljs-comment">// If this is a first attempt to group and send, no replicas, we need replica thread.</span>
        sendMultiAction(actionsByServer, numAttempt, (doStartReplica &amp;&amp; !hasUnknown)
            ? currentActions : <span class="hljs-keyword">null</span>, numAttempt &gt; <span class="hljs-number">1</span> &amp;&amp; !hasUnknown);
      }

      <span class="hljs-keyword">if</span> (hasUnknown) {
       ...
      }
    }</code></pre>

<p>这里根据row region所在的regionserver进行分组，然后调用sendMultiAction。sendMultiAction中根据actionsByServer的分组，分别构造一个SingleServerRequestRunnable的Runnable对象提交到线程池之行。其执行流程与单个get方法一致，只是调用的是regionserver rpcserver的multi方法。</p>



<h3 id="delete">Delete</h3>



<pre class="prettyprint"><code class=" hljs actionscript"><span class="hljs-comment">// HTable.java</span>
<span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-keyword">delete</span>(<span class="hljs-keyword">final</span> Delete <span class="hljs-keyword">delete</span>)
<span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-keyword">delete</span>(<span class="hljs-keyword">final</span> List&lt;Delete&gt; deletes)</code></pre>

<p>delete方法与get方法类似，也是有单个和批量2个方法，其内部处理流程也与get方法一致，这里就不再说了。 <br>
其他的append和increment方法的处理流程与单个get类似,只是最后调用的是server端的mutate方法。</p>

<h3 id="put">Put</h3>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-comment">// HTable.java</span>
<span class="hljs-annotation">@Override</span>
  <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">put</span>(<span class="hljs-keyword">final</span> Put put) <span class="hljs-keyword">throws</span> IOException {
    getBufferedMutator().mutate(put);
    <span class="hljs-keyword">if</span> (autoFlush) {
      flushCommits();
    }
  }

  <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">put</span>(<span class="hljs-keyword">final</span> List&lt;Put&gt; puts) <span class="hljs-keyword">throws</span> IOException {
    getBufferedMutator().mutate(puts);
    <span class="hljs-keyword">if</span> (autoFlush) {
      flushCommits();
    }
  }</code></pre>

<p>Put方法的单个处理和多个处理的流程是一致的，都是调用了getBufferedMutator().mutate(puts)来处理的。</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-comment">// HTable.java</span>
BufferedMutator getBufferedMutator() </code></pre>

<p>BufferedMutator看其javadoc说是与Table接口一样,与单个HBase表通信，但是只用于异步的批量puts的操作。来看看异步是怎么实现的</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// BufferedMutatorImpl.java</span>
<span class="hljs-keyword">public</span> <span class="hljs-keyword">synchronized</span> <span class="hljs-keyword">void</span> <span class="hljs-title">mutate</span>(Mutation m) <span class="hljs-keyword">throws</span> InterruptedIOException,
      RetriesExhaustedWithDetailsException {
    doMutate(m);
  }

<span class="hljs-keyword">private</span> <span class="hljs-keyword">void</span> <span class="hljs-title">doMutate</span>(Mutation m) <span class="hljs-keyword">throws</span> InterruptedIOException,
      RetriesExhaustedWithDetailsException {
    <span class="hljs-keyword">if</span> (closed) {
      <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> IllegalStateException(<span class="hljs-string">"Cannot put when the BufferedMutator is closed."</span>);
    }
    <span class="hljs-keyword">if</span> (!(m <span class="hljs-keyword">instanceof</span> Put) &amp;&amp; !(m <span class="hljs-keyword">instanceof</span> Delete)) {
      <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> IllegalArgumentException(<span class="hljs-string">"Pass a Delete or a Put"</span>);
    }

    <span class="hljs-comment">// This behavior is highly non-intuitive... it does not protect us against</span>
    <span class="hljs-comment">// 94-incompatible behavior, which is a timing issue because hasError, the below code</span>
    <span class="hljs-comment">// and setter of hasError are not synchronized. Perhaps it should be removed.</span>
    <span class="hljs-keyword">if</span> (ap.hasError()) {
      writeAsyncBuffer.add(m);
      backgroundFlushCommits(<span class="hljs-keyword">true</span>);
    }

    <span class="hljs-keyword">if</span> (m <span class="hljs-keyword">instanceof</span> Put) {
      validatePut((Put) m);
    }

    currentWriteBufferSize += m.heapSize();
    <span class="hljs-comment">//private List&lt;Row&gt; writeAsyncBuffer = new LinkedList&lt;&gt;();</span>
    writeAsyncBuffer.add(m);

    <span class="hljs-keyword">while</span> (currentWriteBufferSize &gt; writeBufferSize) {
      backgroundFlushCommits(<span class="hljs-keyword">false</span>);
    }
  }</code></pre>

<p>doMutate方法的主要处理为将请求的操作放到writeAsyncBuffer里面，当占用大小大于writeBufferSize(由hbase.client.write.buffer配置，默认2097152)时调用backgroundFlushCommits。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// BufferedMutatorImpl.java </span>
 <span class="hljs-javadoc">/**
   * Send the operations in the buffer to the servers. Does not wait for the server's answer. If
   * the is an error (max retried reach from a previous flush or bad operation), it tries to send
   * all operations in the buffer and sends an exception.
   *
   *<span class="hljs-javadoctag"> @param</span> synchronous - if true, sends all the writes and wait for all of them to finish before
   *        returning.
   */</span>
  <span class="hljs-keyword">private</span> <span class="hljs-keyword">void</span> <span class="hljs-title">backgroundFlushCommits</span>(<span class="hljs-keyword">boolean</span> synchronous) <span class="hljs-keyword">throws</span> InterruptedIOException,
      RetriesExhaustedWithDetailsException {
    <span class="hljs-keyword">try</span> {
      <span class="hljs-keyword">if</span> (!synchronous) {
        ap.submit(tableName, writeAsyncBuffer, <span class="hljs-keyword">true</span>, <span class="hljs-keyword">null</span>, <span class="hljs-keyword">false</span>);
        <span class="hljs-keyword">if</span> (ap.hasError()) {
          LOG.debug(tableName + <span class="hljs-string">": One or more of the operations have failed -"</span>
              + <span class="hljs-string">" waiting for all operation in progress to finish (successfully or not)"</span>);
        }
      }
      <span class="hljs-keyword">if</span> (synchronous || ap.hasError()) {
        <span class="hljs-keyword">while</span> (!writeAsyncBuffer.isEmpty()) {
          ap.submit(tableName, writeAsyncBuffer, <span class="hljs-keyword">true</span>, <span class="hljs-keyword">null</span>, <span class="hljs-keyword">false</span>);
        }
        RetriesExhaustedWithDetailsException error = ap.waitForAllPreviousOpsAndReset(<span class="hljs-keyword">null</span>);
        <span class="hljs-keyword">if</span> (error != <span class="hljs-keyword">null</span>) {
          <span class="hljs-keyword">if</span> (listener == <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">throw</span> error;
          } <span class="hljs-keyword">else</span> {
            <span class="hljs-keyword">this</span>.listener.onException(error, <span class="hljs-keyword">this</span>);
          }
        }
      }
    } <span class="hljs-keyword">finally</span> {
      currentWriteBufferSize = <span class="hljs-number">0</span>;
      <span class="hljs-keyword">for</span> (Row mut : writeAsyncBuffer) {
        <span class="hljs-keyword">if</span> (mut <span class="hljs-keyword">instanceof</span> Mutation) {
          currentWriteBufferSize += ((Mutation) mut).heapSize();
        }
      }
    }
  }</code></pre>

<p>backgroundFlushCommits方法的功能为给servers发送buffer中的多个操作，但是不等待服务器的应答（通过boolean synchronous参数控制是同步还是异步,doMutate调用时用的是false，用异步方式）。如果有失败会重新发送（这里是同步的处理）。 <br>
主要代码为</p>



<pre class="prettyprint"><code class=" hljs cs"><span class="hljs-comment">// BufferedMutatorImpl.java </span>
<span class="hljs-keyword">protected</span> AsyncProcess ap;
ap.submit(tableName, writeAsyncBuffer, <span class="hljs-keyword">true</span>, <span class="hljs-keyword">null</span>, <span class="hljs-keyword">false</span>);</code></pre>

<p>下面来看看submit方法</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">//AsyncProcess.java</span>
<span class="hljs-javadoc">/**
 1. Extract from the rows list what we can submit. The rows we can not submit are kept in the
 2. list. Does not send requests to replicas (not currently used for anything other
 3. than streaming puts anyway).
 4.  5.<span class="hljs-javadoctag"> @param</span> pool ExecutorService to use.
 5.<span class="hljs-javadoctag"> @param</span> tableName The table for which this request is needed.
 6.<span class="hljs-javadoctag"> @param</span> callback Batch callback. Only called on success (94 behavior).
 7.<span class="hljs-javadoctag"> @param</span> needResults Whether results are needed, or can be discarded.
 8.<span class="hljs-javadoctag"> @param</span> rows - the submitted row. Modified by the method: we remove the rows we took.
 9.<span class="hljs-javadoctag"> @param</span> atLeastOne true if we should submit at least a subset.
   */</span>
  <span class="hljs-keyword">public</span> &lt;CResult&gt; AsyncRequestFuture <span class="hljs-title">submit</span>(ExecutorService pool, TableName tableName,
      List&lt;? extends Row&gt; rows, <span class="hljs-keyword">boolean</span> atLeastOne, Batch.Callback&lt;CResult&gt; callback,
      <span class="hljs-keyword">boolean</span> needResults) <span class="hljs-keyword">throws</span> InterruptedIOException {
    <span class="hljs-keyword">if</span> (rows.isEmpty()) {
      <span class="hljs-keyword">return</span> NO_REQS_RESULT;
    }

    Map&lt;ServerName, MultiAction&lt;Row&gt;&gt; actionsByServer =
        <span class="hljs-keyword">new</span> HashMap&lt;ServerName, MultiAction&lt;Row&gt;&gt;();
    List&lt;Action&lt;Row&gt;&gt; retainedActions = <span class="hljs-keyword">new</span> ArrayList&lt;Action&lt;Row&gt;&gt;(rows.size());

    NonceGenerator ng = <span class="hljs-keyword">this</span>.connection.getNonceGenerator();
    <span class="hljs-keyword">long</span> nonceGroup = ng.getNonceGroup(); <span class="hljs-comment">// Currently, nonce group is per entire client.</span>

    <span class="hljs-comment">// Location errors that happen before we decide what requests to take.</span>
    List&lt;Exception&gt; locationErrors = <span class="hljs-keyword">null</span>;
    List&lt;Integer&gt; locationErrorRows = <span class="hljs-keyword">null</span>;
    do {
      <span class="hljs-comment">// Wait until there is at least one slot for a new task.</span>
      waitForMaximumCurrentTasks(maxTotalConcurrentTasks - <span class="hljs-number">1</span>);

      <span class="hljs-comment">// Remember the previous decisions about regions or region servers we put in the</span>
      <span class="hljs-comment">//  final multi.</span>
      Map&lt;HRegionInfo, Boolean&gt; regionIncluded = <span class="hljs-keyword">new</span> HashMap&lt;HRegionInfo, Boolean&gt;();
      Map&lt;ServerName, Boolean&gt; serverIncluded = <span class="hljs-keyword">new</span> HashMap&lt;ServerName, Boolean&gt;();

      <span class="hljs-keyword">int</span> posInList = -<span class="hljs-number">1</span>;
      Iterator&lt;? extends Row&gt; it = rows.iterator();
      <span class="hljs-keyword">while</span> (it.hasNext()) {
        Row r = it.next();
        HRegionLocation loc;
        <span class="hljs-keyword">try</span> {
          <span class="hljs-keyword">if</span> (r == <span class="hljs-keyword">null</span>) <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> IllegalArgumentException(<span class="hljs-string">"#"</span> + id + <span class="hljs-string">", row cannot be null"</span>);
          <span class="hljs-comment">// Make sure we get 0-s replica.</span>
          RegionLocations locs = connection.locateRegion(
              tableName, r.getRow(), <span class="hljs-keyword">true</span>, <span class="hljs-keyword">true</span>, RegionReplicaUtil.DEFAULT_REPLICA_ID);
          <span class="hljs-keyword">if</span> (locs == <span class="hljs-keyword">null</span> || locs.isEmpty() || locs.getDefaultRegionLocation() == <span class="hljs-keyword">null</span>) {
            <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> IOException(<span class="hljs-string">"#"</span> + id + <span class="hljs-string">", no location found, aborting submit for"</span>
                + <span class="hljs-string">" tableName="</span> + tableName + <span class="hljs-string">" rowkey="</span> + Bytes.toStringBinary(r.getRow()));
          }
          loc = locs.getDefaultRegionLocation();
        } <span class="hljs-keyword">catch</span> (IOException ex) {
          locationErrors = <span class="hljs-keyword">new</span> ArrayList&lt;Exception&gt;();
          locationErrorRows = <span class="hljs-keyword">new</span> ArrayList&lt;Integer&gt;();
          LOG.error(<span class="hljs-string">"Failed to get region location "</span>, ex);
          <span class="hljs-comment">// This action failed before creating ars. Retain it, but do not add to submit list.</span>
          <span class="hljs-comment">// We will then add it to ars in an already-failed state.</span>
          retainedActions.add(<span class="hljs-keyword">new</span> Action&lt;Row&gt;(r, ++posInList));
          locationErrors.add(ex);
          locationErrorRows.add(posInList);
          it.remove();
          <span class="hljs-keyword">break</span>; <span class="hljs-comment">// Backward compat: we stop considering actions on location error.</span>
        }

        <span class="hljs-keyword">if</span> (canTakeOperation(loc, regionIncluded, serverIncluded)) {
          Action&lt;Row&gt; action = <span class="hljs-keyword">new</span> Action&lt;Row&gt;(r, ++posInList);
          setNonce(ng, r, action);
          retainedActions.add(action);
          <span class="hljs-comment">// TODO: replica-get is not supported on this path</span>
          <span class="hljs-keyword">byte</span>[] regionName = loc.getRegionInfo().getRegionName();
          addAction(loc.getServerName(), regionName, action, actionsByServer, nonceGroup);
          it.remove();
        }
      }
    } <span class="hljs-keyword">while</span> (retainedActions.isEmpty() &amp;&amp; atLeastOne &amp;&amp; (locationErrors == <span class="hljs-keyword">null</span>));

    <span class="hljs-keyword">if</span> (retainedActions.isEmpty()) <span class="hljs-keyword">return</span> NO_REQS_RESULT;

    <span class="hljs-keyword">return</span> submitMultiActions(tableName, retainedActions, nonceGroup, callback, <span class="hljs-keyword">null</span>, needResults,
      locationErrors, locationErrorRows, actionsByServer, pool);
  }</code></pre>

<p>这个方法比较长，这里大致说下其处理流程 <br>
1、由于多个操作会访问不同的regionserver，这里先根据操作请求内容获取其regionserver并做分组</p>



<pre class="prettyprint"><code class=" hljs mathematica"><span class="hljs-keyword">Map</span>&lt;ServerName, MultiAction&lt;<span class="hljs-keyword">Row</span>&gt;&gt; actionsByServer =
        new HashMap&lt;ServerName, MultiAction&lt;<span class="hljs-keyword">Row</span>&gt;&gt;();</code></pre>

<p>2、提交分好组的请求 submitMultiActions，submitMultiActions里面再调用了sendMultiAction，这个就和batch方法最终调用的一样了。</p>



<pre class="prettyprint"><code class=" hljs r">&lt;CResult&gt; AsyncRequestFuture submitMultiActions(TableName tableName,
      List&lt;Action&lt;Row&gt;&gt; retainedActions, long nonceGroup, Batch.Callback&lt;CResult&gt; callback,
      Object[] results, boolean needResults, List&lt;Exception&gt; locationErrors,
      List&lt;Integer&gt; locationErrorRows, Map&lt;ServerName, MultiAction&lt;Row&gt;&gt; actionsByServer,
      ExecutorService pool) {
    AsyncRequestFutureImpl&lt;CResult&gt; ars = createAsyncRequestFuture(
      tableName, retainedActions, nonceGroup, pool, callback, results, needResults);
    // Add location errors <span class="hljs-keyword">if</span> any
    <span class="hljs-keyword">if</span> (locationErrors != null) {
      <span class="hljs-keyword">...</span>
      }
    }
    ars.sendMultiAction(actionsByServer, <span class="hljs-number">1</span>, null, false);
    <span class="hljs-keyword">return</span> ars;
  }</code></pre>

<p>其他说明： <br>
基于性能考虑，会缓存历史的region和regionserver的信息</p>



<pre class="prettyprint"><code class=" hljs vhdl"><span class="hljs-keyword">Map</span>&lt;HRegionInfo, <span class="hljs-typename">Boolean</span>&gt; regionIncluded = <span class="hljs-keyword">new</span> HashMap&lt;HRegionInfo, <span class="hljs-typename">Boolean</span>&gt;();
      <span class="hljs-keyword">Map</span>&lt;ServerName, <span class="hljs-typename">Boolean</span>&gt; serverIncluded = <span class="hljs-keyword">new</span> HashMap&lt;ServerName, <span class="hljs-typename">Boolean</span>&gt;();</code></pre>

<p>并发控制处理 <br>
maxTotalConcurrentTasks参数hbase.client.max.total.tasks默认值100 <br>
maxConcurrentTasksPerServer 参数hbase.client.max.perserver.tasks默认值2 <br>
maxConcurrentTasksPerRegion 参数hbase.client.max.perregion.tasks默认值1</p>



<h3 id="scan">scan</h3>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// HTable.java</span>
<span class="hljs-keyword">public</span> ResultScanner <span class="hljs-title">getScanner</span>(<span class="hljs-keyword">final</span> Scan scan) <span class="hljs-keyword">throws</span> IOException {
    <span class="hljs-keyword">if</span> (scan.getBatch() &gt; <span class="hljs-number">0</span> &amp;&amp; scan.isSmall()) {
      <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> IllegalArgumentException(<span class="hljs-string">"Small scan should not be used with batching"</span>);
    }

    <span class="hljs-keyword">if</span> (scan.getCaching() &lt;= <span class="hljs-number">0</span>) {
      scan.setCaching(getScannerCaching());
    }
    <span class="hljs-keyword">if</span> (scan.getMaxResultSize() &lt;= <span class="hljs-number">0</span>) {
      scan.setMaxResultSize(scannerMaxResultSize);
    }

    <span class="hljs-keyword">if</span> (scan.isReversed()) {
      <span class="hljs-keyword">if</span> (scan.isSmall()) {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> ClientSmallReversedScanner(getConfiguration(), scan, getName(),
            <span class="hljs-keyword">this</span>.connection, <span class="hljs-keyword">this</span>.rpcCallerFactory, <span class="hljs-keyword">this</span>.rpcControllerFactory,
            pool, connConfiguration.getReplicaCallTimeoutMicroSecondScan());
      } <span class="hljs-keyword">else</span> {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> ReversedClientScanner(getConfiguration(), scan, getName(),
            <span class="hljs-keyword">this</span>.connection, <span class="hljs-keyword">this</span>.rpcCallerFactory, <span class="hljs-keyword">this</span>.rpcControllerFactory,
            pool, connConfiguration.getReplicaCallTimeoutMicroSecondScan());
      }
    }

    <span class="hljs-keyword">if</span> (scan.isSmall()) {
      <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> ClientSmallScanner(getConfiguration(), scan, getName(),
          <span class="hljs-keyword">this</span>.connection, <span class="hljs-keyword">this</span>.rpcCallerFactory, <span class="hljs-keyword">this</span>.rpcControllerFactory,
          pool, connConfiguration.getReplicaCallTimeoutMicroSecondScan());
    } <span class="hljs-keyword">else</span> {
      <span class="hljs-keyword">return</span> <span class="hljs-keyword">new</span> ClientScanner(getConfiguration(), scan, getName(), <span class="hljs-keyword">this</span>.connection,
          <span class="hljs-keyword">this</span>.rpcCallerFactory, <span class="hljs-keyword">this</span>.rpcControllerFactory,
          pool, connConfiguration.getReplicaCallTimeoutMicroSecondScan());
    }
  }</code></pre>

<p>这里根据isSmall和isReversed的不同，分别返回4个不同的ClientScanner子类 <br>
<img src="https://img-blog.csdn.net/20161211074008325?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveGlhbmdlbA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="ClientScanner子类关系" title=""> <br>
isSmall: small scan可以获得更好的性能，其使用pread方式。在客户端其将openScanner，next，closeScanner 放在一个RPC请求中。总的来说就是通过减少网络IO来达到提高性能的效果的。这种场景呢是适用于scan的范围在一个data block(64k)的。 <br>
isReversed: 使用反向的方式scan数据 <br>
下面先来看看ClientScanner</p>



<pre class="prettyprint"><code class="language-java hljs "><span class="hljs-comment">// ClientScanner.java</span>
 <span class="hljs-javadoc">/**
   * Create a new ClientScanner for the specified table Note that the passed {@link Scan}'s start
   * row maybe changed changed.
   *<span class="hljs-javadoctag"> @param</span> conf The {@link Configuration} to use.
   *<span class="hljs-javadoctag"> @param</span> scan {@link Scan} to use in this scanner
   *<span class="hljs-javadoctag"> @param</span> tableName The table that we wish to scan
   *<span class="hljs-javadoctag"> @param</span> connection Connection identifying the cluster
   *<span class="hljs-javadoctag"> @throws</span> IOException
   */</span>
  <span class="hljs-keyword">public</span> <span class="hljs-title">ClientScanner</span>(<span class="hljs-keyword">final</span> Configuration conf, <span class="hljs-keyword">final</span> Scan scan, <span class="hljs-keyword">final</span> TableName tableName,
      ClusterConnection connection, RpcRetryingCallerFactory rpcFactory,
      RpcControllerFactory controllerFactory, ExecutorService pool, <span class="hljs-keyword">int</span> primaryOperationTimeout)
      <span class="hljs-keyword">throws</span> IOException {
      ...
      <span class="hljs-keyword">this</span>.conf = conf;
      initializeScannerInConstruction();
  }

  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">initializeScannerInConstruction</span>() <span class="hljs-keyword">throws</span> IOException{
      <span class="hljs-comment">// initialize the scanner</span>
      nextScanner(<span class="hljs-keyword">this</span>.caching, <span class="hljs-keyword">false</span>);
  }

    <span class="hljs-comment">/*
     * Gets a scanner for the next region.  If this.currentRegion != null, then
     * we will move to the endrow of this.currentRegion.  Else we will get
     * scanner at the scan.getStartRow().  We will go no further, just tidy
     * up outstanding scanners, if &lt;code&gt;currentRegion != null&lt;/code&gt; and
     * &lt;code&gt;done&lt;/code&gt; is true.
     * @param nbRows
     * @param done Server-side says we're done scanning.
     */</span>
  <span class="hljs-keyword">protected</span> <span class="hljs-keyword">boolean</span> <span class="hljs-title">nextScanner</span>(<span class="hljs-keyword">int</span> nbRows, <span class="hljs-keyword">final</span> <span class="hljs-keyword">boolean</span> done)
    <span class="hljs-keyword">throws</span> IOException {
      <span class="hljs-comment">// Close the previous scanner if it's open</span>
      <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.callable != <span class="hljs-keyword">null</span>) {
        <span class="hljs-keyword">this</span>.callable.setClose();
        call(callable, caller, scannerTimeout);
        <span class="hljs-keyword">this</span>.callable = <span class="hljs-keyword">null</span>;
      }

      <span class="hljs-comment">// Where to start the next scanner</span>
      <span class="hljs-keyword">byte</span> [] localStartKey;

      <span class="hljs-comment">// if we're at end of table, close and return false to stop iterating</span>
      <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.currentRegion != <span class="hljs-keyword">null</span>) {
        <span class="hljs-keyword">byte</span> [] endKey = <span class="hljs-keyword">this</span>.currentRegion.getEndKey();
        <span class="hljs-keyword">if</span> (endKey == <span class="hljs-keyword">null</span> ||
            Bytes.equals(endKey, HConstants.EMPTY_BYTE_ARRAY) ||
            checkScanStopRow(endKey) ||
            done) {
          close();
          <span class="hljs-keyword">if</span> (LOG.isTraceEnabled()) {
            LOG.trace(<span class="hljs-string">"Finished "</span> + <span class="hljs-keyword">this</span>.currentRegion);
          }
          <span class="hljs-keyword">return</span> <span class="hljs-keyword">false</span>;
        }
        localStartKey = endKey;
        <span class="hljs-keyword">if</span> (LOG.isTraceEnabled()) {
          LOG.trace(<span class="hljs-string">"Finished "</span> + <span class="hljs-keyword">this</span>.currentRegion);
        }
      } <span class="hljs-keyword">else</span> {
        localStartKey = <span class="hljs-keyword">this</span>.scan.getStartRow();
      }

      <span class="hljs-keyword">if</span> (LOG.isDebugEnabled() &amp;&amp; <span class="hljs-keyword">this</span>.currentRegion != <span class="hljs-keyword">null</span>) {
        <span class="hljs-comment">// Only worth logging if NOT first region in scan.</span>
        LOG.debug(<span class="hljs-string">"Advancing internal scanner to startKey at '"</span> +
          Bytes.toStringBinary(localStartKey) + <span class="hljs-string">"'"</span>);
      }
      <span class="hljs-keyword">try</span> {
        callable = getScannerCallable(localStartKey, nbRows);
        <span class="hljs-comment">// Open a scanner on the region server starting at the</span>
        <span class="hljs-comment">// beginning of the region</span>
        call(callable, caller, scannerTimeout);
        <span class="hljs-keyword">this</span>.currentRegion = callable.getHRegionInfo();
        <span class="hljs-keyword">if</span> (<span class="hljs-keyword">this</span>.scanMetrics != <span class="hljs-keyword">null</span>) {
          <span class="hljs-keyword">this</span>.scanMetrics.countOfRegions.incrementAndGet();
        }
      } <span class="hljs-keyword">catch</span> (IOException e) {
        close();
        <span class="hljs-keyword">throw</span> e;
      }
      <span class="hljs-keyword">return</span> <span class="hljs-keyword">true</span>;
    }</code></pre>

<p>nextScanner方法是获取下一个region的scanner，</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// ClientScanner.java</span>
<span class="hljs-keyword">protected</span> ScannerCallableWithReplicas <span class="hljs-title">getScannerCallable</span>(<span class="hljs-keyword">byte</span> [] localStartKey,
        <span class="hljs-keyword">int</span> nbRows) {
      scan.setStartRow(localStartKey);
      ScannerCallable s =
          <span class="hljs-keyword">new</span> ScannerCallable(getConnection(), getTable(), scan, <span class="hljs-keyword">this</span>.scanMetrics,
              <span class="hljs-keyword">this</span>.rpcControllerFactory);
      s.setCaching(nbRows);
      ScannerCallableWithReplicas sr = <span class="hljs-keyword">new</span> ScannerCallableWithReplicas(tableName, getConnection(),
       s, pool, primaryOperationTimeout, scan,
       retries, scannerTimeout, caching, conf, caller);
      <span class="hljs-keyword">return</span> sr;
    }

Result[] call(ScannerCallableWithReplicas callable,
      RpcRetryingCaller&lt;Result[]&gt; caller, <span class="hljs-keyword">int</span> scannerTimeout)
      <span class="hljs-keyword">throws</span> IOException, RuntimeException {
    <span class="hljs-keyword">if</span> (Thread.interrupted()) {
      <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> InterruptedIOException();
    }
    <span class="hljs-comment">// callWithoutRetries is at this layer. Within the ScannerCallableWithReplicas,</span>
    <span class="hljs-comment">// we do a callWithRetries</span>
    <span class="hljs-keyword">return</span> caller.callWithoutRetries(callable, scannerTimeout);
  }</code></pre>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// RpcRetryingCaller.java</span>
<span class="hljs-keyword">public</span> T <span class="hljs-title">callWithoutRetries</span>(RetryingCallable&lt;T&gt; callable, <span class="hljs-keyword">int</span> callTimeout)
  <span class="hljs-keyword">throws</span> IOException, RuntimeException {
    <span class="hljs-comment">// The code of this method should be shared with withRetries.</span>
    <span class="hljs-keyword">this</span>.globalStartTime = EnvironmentEdgeManager.currentTime();
    <span class="hljs-keyword">try</span> {
      callable.prepare(<span class="hljs-keyword">false</span>);
      <span class="hljs-keyword">return</span> callable.call(callTimeout);
    } <span class="hljs-keyword">catch</span> (Throwable t) {
      Throwable t2 = translateException(t);
      ExceptionUtil.rethrowIfInterrupt(t2);
      <span class="hljs-comment">// It would be nice to clear the location cache here.</span>
      <span class="hljs-keyword">if</span> (t2 <span class="hljs-keyword">instanceof</span> IOException) {
        <span class="hljs-keyword">throw</span> (IOException)t2;
      } <span class="hljs-keyword">else</span> {
        <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> RuntimeException(t2);
      }
    }
  }</code></pre>

<p>最终调用的是ScannerCallable, 先prepare方法获取region所在的regionserver地址，然后建立rpc连接。然后在call方法中构造scan的rpc请求，调用rpcserver的scan方法，至此初始化ClientScanner完成。</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// ScannerCallable.java</span>
 <span class="hljs-keyword">public</span> <span class="hljs-keyword">void</span> <span class="hljs-title">prepare</span>(<span class="hljs-keyword">boolean</span> reload) <span class="hljs-keyword">throws</span> IOException {
    <span class="hljs-keyword">if</span> (Thread.interrupted()) {
      <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> InterruptedIOException();
    }
    RegionLocations rl = RpcRetryingCallerWithReadReplicas.getRegionLocations(!reload,
        id, getConnection(), getTableName(), getRow());
    location = id &lt; rl.size() ? rl.getRegionLocation(id) : <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">if</span> (location == <span class="hljs-keyword">null</span> || location.getServerName() == <span class="hljs-keyword">null</span>) {
      <span class="hljs-comment">// With this exception, there will be a retry. The location can be null for a replica</span>
      <span class="hljs-comment">//  when the table is created or after a split.</span>
      <span class="hljs-keyword">throw</span> <span class="hljs-keyword">new</span> HBaseIOException(<span class="hljs-string">"There is no location for replica id #"</span> + id);
    }
    ServerName dest = location.getServerName();
    setStub(<span class="hljs-keyword">super</span>.getConnection().getClient(dest));
    <span class="hljs-keyword">if</span> (!instantiated || reload) {
      checkIfRegionServerIsRemote();
      instantiated = <span class="hljs-keyword">true</span>;
    }

    ...
  }</code></pre>

<p>再来看看ClientScanner的next方法</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-keyword">public</span> Result <span class="hljs-title">next</span>() <span class="hljs-keyword">throws</span> IOException {
      <span class="hljs-comment">// If the scanner is closed and there's nothing left in the cache, next is a no-op.</span>
      <span class="hljs-keyword">if</span> (cache.size() == <span class="hljs-number">0</span> &amp;&amp; <span class="hljs-keyword">this</span>.closed) {
        <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
      }
      <span class="hljs-keyword">if</span> (cache.size() == <span class="hljs-number">0</span>) {
        loadCache();
      }

      <span class="hljs-keyword">if</span> (cache.size() &gt; <span class="hljs-number">0</span>) {
        <span class="hljs-keyword">return</span> cache.poll();
      }

      <span class="hljs-comment">// if we exhausted this scanner before calling close, write out the scan metrics</span>
      writeScanMetrics();
      <span class="hljs-keyword">return</span> <span class="hljs-keyword">null</span>;
    }

 <span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">loadCache</span>() <span class="hljs-keyword">throws</span> IOException {
    Result[] values = <span class="hljs-keyword">null</span>;
    <span class="hljs-keyword">long</span> remainingResultSize = maxScannerResultSize;
    <span class="hljs-keyword">int</span> countdown = <span class="hljs-keyword">this</span>.caching;
    <span class="hljs-comment">// We need to reset it if it's a new callable that was created with a countdown in nextScanner</span>
    callable.setCaching(<span class="hljs-keyword">this</span>.caching);
    <span class="hljs-comment">// This flag is set when we want to skip the result returned. We do</span>
    <span class="hljs-comment">// this when we reset scanner because it split under us.</span>
    <span class="hljs-keyword">boolean</span> retryAfterOutOfOrderException = <span class="hljs-keyword">true</span>;
    <span class="hljs-comment">// We don't expect that the server will have more results for us if</span>
    <span class="hljs-comment">// it doesn't tell us otherwise. We rely on the size or count of results</span>
    <span class="hljs-keyword">boolean</span> serverHasMoreResults = <span class="hljs-keyword">false</span>;
    <span class="hljs-keyword">boolean</span> allResultsSkipped = <span class="hljs-keyword">false</span>;
    do {
      allResultsSkipped = <span class="hljs-keyword">false</span>;
      <span class="hljs-keyword">try</span> {
        <span class="hljs-comment">// Server returns a null values if scanning is to stop. Else,</span>
        <span class="hljs-comment">// returns an empty array if scanning is to go on and we've just</span>
        <span class="hljs-comment">// exhausted current region.</span>
        values = call(callable, caller, scannerTimeout);
        <span class="hljs-comment">// When the replica switch happens, we need to do certain operations again.</span>
        <span class="hljs-comment">// The callable will openScanner with the right startkey but we need to pick up</span>
        <span class="hljs-comment">// from there. Bypass the rest of the loop and let the catch-up happen in the beginning</span>
        <span class="hljs-comment">// of the loop as it happens for the cases where we see exceptions.</span>
        <span class="hljs-comment">// Since only openScanner would have happened, values would be null</span>
        <span class="hljs-keyword">if</span> (values == <span class="hljs-keyword">null</span> &amp;&amp; callable.switchedToADifferentReplica()) {
          <span class="hljs-comment">// Any accumulated partial results are no longer valid since the callable will</span>
          <span class="hljs-comment">// openScanner with the correct startkey and we must pick up from there</span>
          clearPartialResults();
          <span class="hljs-keyword">this</span>.currentRegion = callable.getHRegionInfo();
          <span class="hljs-keyword">continue</span>;
        }
        retryAfterOutOfOrderException = <span class="hljs-keyword">true</span>;
        ...
    }</code></pre>

<p>这里主要看loadCache方法，也是通过调用call来从服务器端读取数据。 <br>
再看ClientSmallScanner</p>



<pre class="prettyprint"><code class=" hljs java"><span class="hljs-comment">// ClientSmallScanner.java</span>
<span class="hljs-keyword">protected</span> <span class="hljs-keyword">void</span> <span class="hljs-title">initializeScannerInConstruction</span>() <span class="hljs-keyword">throws</span> IOException {
    <span class="hljs-comment">// No need to initialize the scanner when constructing instance, do it when</span>
    <span class="hljs-comment">// calling next(). Do nothing here.</span>
  }</code></pre>

<p>初始化的时候不处理，在next方法中才去调用nextScanner。 <br>
至此hbase client的基本操作就分析完了。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
---
layout:     post
title:      Spark定制班第23课：Spark Streaming初始化和关闭源码图解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/andyshar/article/details/51645287				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><strong>本期内容：</strong></span>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">1. Spark Streaming初始化源码图解</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">2. Spark Streaming关闭源码图解</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="line-height:1.5;">Spark Streaming的</span><span style="line-height:1.5;background-color:inherit;">StreamingContext是采用装饰器模式，对SparkContext的封装。是在Spark Core的基础上加了一些功能，所有的实际上还是用Spark Core去实现。</span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">batchDuration是在Spark Core的基础上新加的参数。Driver上有一个定时器，用于定时产生job，就是使用了batchDuration。Executor上的定时器是用于定时接收和存储数据。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">看看StreamGraph中的成员。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">有生成的JobScheduler对象。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">progressListener是StreamingListener、SparkListener的子类对象，用于处理进度。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">uiTab用于Spark网页UI。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">socketTextStream：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="color:#4f81bd;background-color:inherit;"><br style="background-color:inherit;"></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  /**</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * Create a input stream from TCP source hostname:port. Data is received using</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * a TCP socket and the receive bytes is interpreted as UTF8 encoded `\n` delimited</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * lines.</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * @param hostname      Hostname to connect to for receiving data</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * @param port          Port to connect to for receiving data</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * @param storageLevel  Storage level to use for storing the received objects</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   *                      (default: StorageLevel.MEMORY_AND_DISK_SER_2)</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   */</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  def <span style="background-color:inherit;"><strong>socketTextStream</strong></span>(</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      hostname: String,</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      port: Int,</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      storageLevel: StorageLevel = StorageLevel.MEMORY_AND_DISK_SER_2</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    ): ReceiverInputDStream[String] = withNamedScope("socket text stream") {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    socketStream[String](hostname, port, SocketReceiver.</span><span style="color:#ff0000;background-color:inherit;">bytesToLines</span><span style="color:#4f81bd;background-color:inherit;">, storageLevel)</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  }</span></div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="color:#4f81bd;background-color:inherit;"><br style="background-color:inherit;"></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">SocketReceiver.bytesToLines：<br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  /**</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * This methods translates the data from an inputstream (say, from a socket)</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * to '\n' delimited strings and returns an iterator to access the strings.</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   */</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  def <span style="background-color:inherit;"><strong>bytesToLines</strong></span>(inputStream: InputStream): Iterator[String] = {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    val dataInputStream = new BufferedReader(new InputStreamReader(inputStream, "UTF-8"))</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    new NextIterator[String] {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      protected override def getNext() = {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        val nextValue = dataInputStream.readLine()</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        if (nextValue == null) {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">          finished = true</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        }</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        nextValue</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      }</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;"><br style="background-color:inherit;"></span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      protected override def close() {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        dataInputStream.close()</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      }</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    }</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  }</span></div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">把接收的数据转成用行的迭代器来表示。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">再说说ssc.awaittermination：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  /**</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * Wait for the execution to stop. Any exceptions that occurs during the execution</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * will be thrown in this thread.</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   */</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  def <span style="background-color:inherit;"><strong>awaitTermination</strong></span>() {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    waiter.waitForStopOrError()</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  }</span></div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="color:#4f81bd;background-color:inherit;"><br style="background-color:inherit;"></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">用于等待执行的停止，再结束全部运行程序。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><span style="color:#4f81bd;background-color:inherit;"><br style="background-color:inherit;"></span></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">ContextWaiter.waitForStopOrError：</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  /**</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * Return `true` if it's stopped; or throw the reported error if `notifyError` has been called; or</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   * `false` if the waiting time detectably elapsed before return from the method.</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">   */</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  def <span style="background-color:inherit;"><strong>waitForStopOrError</strong></span>(timeout: Long = -1): Boolean = {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    lock.lock()</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    try {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      if (timeout &lt; 0) {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        while (!stopped &amp;&amp; error == null) {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">          condition.await()</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        }</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      } else {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        var nanos = TimeUnit.MILLISECONDS.toNanos(timeout)</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        while (!stopped &amp;&amp; error == null &amp;&amp; nanos &gt; 0) {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">          nanos = condition.awaitNanos(nanos)</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">        }</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      }</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      // If already had error, then throw it</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      if (error != null) throw error</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      // already stopped or timeout</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      stopped</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    } finally {</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">      lock.unlock()</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">    }</span></div>
<div style="background-color:inherit;"><span style="color:#4f81bd;background-color:inherit;">  }</span></div>
</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">线程没停止，而且没有error时，会一直等待。</div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;">
<div><img src="https://img-blog.csdn.net/20160612123541760?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="display:inline-block;background-color:inherit;"></div>
<div><br style="background-color:inherit;"></div>
<br style="background-color:inherit;"><br style="background-color:inherit;"></div>
<div style="font-family:'微软雅黑';font-size:14px;line-height:21px;"><br style="background-color:inherit;"></div>
            </div>
                </div>
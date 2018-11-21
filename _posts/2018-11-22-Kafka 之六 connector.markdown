---
layout:     post
title:      Kafka 之六 connector
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;"><strong>一：介绍  </strong></span><br></p>
<p>           kafka Connect 是一个kafka和其他系统交互稳定的流处理。它使kafka和其他系统的数据交互变的十分容易。它可以把其他系统的数据导入到kafka中，也可以把kafka的数据导到其他系统中。kafka的sink功能可以把kafka的topic数据分发给其他储存系统或查询系统以便离线分析，它有下面的特征：</p>
<p>           1：对于<strong>Kafka connectors</strong>它有一套通用的框架</p>
<p>           2：可以分布式部署，也可以<strong>standalone </strong>模式部署（生产环境建议集群部署，可以容错）</p>
<p>           3：<strong>REST 接口</strong></p>
<p><strong>           4：offset自动管理 kafka自动管理和提交offset，这样开发者可以专注自己的业务开发</strong></p>
<p><strong>           5：流和批量整合</strong></p>
<p><strong>二：开发<strong>connector</strong>只要实现两个接口就可以，第一个接口是<strong><code>Connector</code></strong>，第二个接口是<strong><code>Task</code></strong>，若是开发<strong><code>Source</code></strong>，只要实现<strong><code>SourceConnector</code>/<code>SourceTask</code></strong>两个接口。比如把文件的数据读取到kafka中，<strong>/<code>SourceTask</code></strong>会读取文件的每一行并把他们封装为<strong>record</strong>发送出去。</strong></p>
<br><pre></pre><pre><code class="language-java"></code></pre><pre class="java">    第一步  实现SourceConnector类

    public class SourceConnectorPartition extends SourceConnector{
                
              private String zkServers;
           private String destination;
              //start方法会在task执行前调用，此方法多用于参数初始化
              public void start(Map&lt;String, String&gt; props) {
                    zkServers = PropertyVerify.getOrElse(props, ZK_SERVERS, null, "'zkServers' is null");
                    destination = PropertyVerify.getOrElse(props, DESTINATION, null, "'destination' is null");
               }
              //task 停止会回调此方法
              public void stop() {
                   // nothing to do
              }
              //此方法必须重新，指定重写的Task类
              public Class&lt;? extends Task&gt; taskClass() {
                          return TestSourceTask.class;
               }
              //此方法就通常用于参数传递，把参数传递给后续的Task，留task调用
              public List&lt;Map&lt;String, String&gt;&gt; taskConfigs(int maxTasks) {
                      List&lt;Map&lt;String, String&gt;&gt; taskConfigs = new ArrayList&lt;Map&lt;String, String&gt;&gt;();
                      return taskConfigs;
              }
              public String version() {
                  return VERSION;
              } 
              public ConfigDef config() {
              // TODO Auto-generated method stub
               return null;
              }
     }
</pre>    第二步：实现sourcetask，自定义自己的sourcetask          public abstract class TestSourceTask implements Task {                   protected SourceTaskContext context;                   //初始化方法                   public void initialize(SourceTaskContext context) {                                this.context = context;                    }                   //启动的时候调用此方法                   @Override                   public abstract void start(Map&lt;String, String&gt; props);                   //循环调用后把数据封装为SourceRecord                  public abstract List&lt;SourceRecord&gt; poll() throws InterruptedException;                  public void commit() throws InterruptedException                   public abstract void stop();                  public void commitRecord(SourceRecord record)         }<br>
<pre></pre>
<p>以上两步就开发完成了，下面源码走读一下一个task启动过程</p>
<p>     1.1 驱动类 驱动主程序 WorkerSourceTask类</p>
<p>     class WorkerSourceTask extends WorkerTask</p>
<p>     1.2 WorkerSourceTask 第一步初始化，运行下面的方法，获取taskConfig中参数，此参数在自己定义的task中设置</p>
<p><strong>      </strong></p>
<pre><code class="language-java">   public void initialize(TaskConfig taskConfig) {
        try {
            this.taskConfig = taskConfig.originalsStrings();
        } catch (Throwable t) {
            log.error("Task {} failed initialization and will not be started.", t);
            onFailure(t);
        }
    }</code></pre>     1.3 WorkerSourceTask 执行execute方法
<p></p>
<p><strong>        </strong></p>
<pre><code class="language-java">    @Override
    public void execute() {
        try {
            //初始化task类中的initialize方法
            task.initialize(new WorkerSourceTaskContext(offsetReader));

            //task中的start方法会执行
            task.start(taskConfig);
            log.info("Source task {} finished initialization and start", this);
            synchronized (this) {
                if (startedShutdownBeforeStartCompleted) {
                    task.stop();
                    return;
                }
                finishedStart = true;
            }
            while (!isStopping()) {
                if (shouldPause()) {
                    awaitUnpause();
                    continue;
                }

                if (toSend == null) {
                    log.debug("Nothing to send to Kafka. Polling source for additional records");
                   //调用task中的poll方法，并把List&lt;SourceRecord&gt;返回赋值给toSend
                   toSend = task.poll();
                }
                if (toSend == null)
                    continue;
                log.debug("About to send " + toSend.size() + " records to Kafka");
                if (!sendRecords())
                    stopRequestedLatch.await(SEND_FAILED_BACKOFF_MS, TimeUnit.MILLISECONDS);
            }
        } catch (InterruptedException e) {
            // Ignore and allow to exit.
        } finally {
            // It should still be safe to commit offsets since any exception would have
            // simply resulted in not getting more records but all the existing records should be ok to flush
            // and commit offsets. Worst case, task.flush() will also throw an exception causing the offset commit
            // to fail.
            commitOffsets();
        }
    }</code></pre><br><pre><code class="language-java"> private boolean sendRecords() {
        int processed = 0;

      //把record 循环发送到kafa

       for (final SourceRecord record : toSend) {
            byte[] key = keyConverter.fromConnectData(record.topic(), record.keySchema(), record.key());
            byte[] value = valueConverter.fromConnectData(record.topic(), record.valueSchema(), record.value());
            final ProducerRecord&lt;byte[], byte[]&gt; producerRecord = new ProducerRecord&lt;&gt;(record.topic(), record.kafkaPartition(), key, value);
            log.trace("Appending record with key {}, value {}", record.key(), record.value());
            // We need this queued first since the callback could happen immediately (even synchronously in some cases).
            // Because of this we need to be careful about handling retries -- we always save the previously attempted
            // record as part of toSend and need to use a flag to track whether we should actually add it to the outstanding
            // messages and update the offsets.
            synchronized (this) {
                if (!lastSendFailed) {
                    if (!flushing) {
                        outstandingMessages.put(producerRecord, producerRecord);
                    } else {
                        outstandingMessagesBacklog.put(producerRecord, producerRecord);
                    }
                    // Offsets are converted &amp; serialized in the OffsetWriter
                    offsetWriter.offset(record.sourcePartition(), record.sourceOffset());
                }
            }
            try {
                producer.send(
                        producerRecord,
                        new Callback() {
                            @Override
                            public void onCompletion(RecordMetadata recordMetadata, Exception e) {
                                if (e != null) {
                                    // Given the default settings for zero data loss, this should basically never happen --
                                    // between "infinite" retries, indefinite blocking on full buffers, and "infinite" request
                                    // timeouts, callbacks with exceptions should never be invoked in practice. If the
                                    // user overrode these settings, the best we can do is notify them of the failure via
                                    // logging.
                                    log.error("{} failed to send record to {}: {}", id, record.topic(), e);
                                    log.debug("Failed record: topic {}, Kafka partition {}, key {}, value {}, source offset {}, source partition {}",
                                            record.topic(), record.kafkaPartition(), record.key(), record.value(),
                                            record.sourceOffset(), record.sourcePartition());
                                } else {
                                    log.trace("Wrote record successfully: topic {} partition {} offset {}",
                                            recordMetadata.topic(), recordMetadata.partition(),
                                            recordMetadata.offset());
                                    commitTaskRecord(record);
                                }
                                recordSent(producerRecord);
                            }
                        });
                lastSendFailed = false;
            }
}
</code></pre><br><br><br><p></p>
<p><br></p>
<p>     <br></p>
<pre></pre>
            </div>
                </div>
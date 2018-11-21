---
layout:     post
title:      分布式日志收集系统Apache Flume的设计介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>概述</h2>
<div>Flume是Cloudera公司的一款高性能、高可能的分布式日志收集系统。现在已经是Apache Top项目。<a href="https://github.com/apache/flume" rel="nofollow">Github地址</a>。同Flume相似的日志收集系统还有<a href="https://github.com/facebook/scribe" rel="nofollow">Facebook Scribe</a>，<a href="http://chukwa.apache.org/" rel="nofollow">Apache
 Chuwka</a>，<a href="https://kafka.apache.org" rel="nofollow">Apache Kafka</a>(也是LinkedIn的)。Flume是后起之秀，本文尝试简要分析Flume数据流通过程中提供的组件、可靠性保证来介绍Flume的主要设计，不涉及Flume具体的安装使用，也不涉及代码层面的剖析。写博文来记录这个工具主要是觉得与最近开发的一个流式的数据搬运的工具在设计上有相似之处，想看看有没有可以参考的地方。在博文的基础上，还需要浏览一下源码。</div>
<div><br></div>
<h2>数据流通</h2>
<div>Flume传输的数据的基本单位是event，如果是文本文件，通常是一行记录，这也是事务的基本单位。flume运行的核心是agent。它是一个完整的数据收集工具，含有三个核心组件，分别是source、channel、sink。Event从Source，流向Channel，再到Sink，本身为一个byte数组，并可携带headers信息。Event代表着一个数据流的最小完整单元，从外部数据源来，向外部的目的地去。Source:完成对日志数据的收集，分成transtion 和 event 打入到channel之中。Channel:主要提供一个队列的功能，对source提供中的数据进行简单的缓存。Sink:取出Channel中的数据，进行相应的存储文件系统，数据库，或者提交到远程服务器。通过这些组件，event可以从一个地方流向另一个地方，如下图所示。<br><img src="https://img-blog.csdn.net/20141201164707171" alt=""><br><div>Source消费从外部流进的Events，如AvroSource接收外部客户端传来的或是从别的agent流出来的Avro Event。Source可以把event送往一个或多个channel。channel是一个队列，持有event等待sink来消费，一种Channel的实现：FileChannel使用本地文件系统来作为它的存储。Sink的作用是把Event从channel里移除，送往外部数据仓库或给下一站agent的Source，如HDFSEventSink送往HDFS。同个agent下的source和sink是异步的。下面再举几个数据流通的例子，说明不同的使用方式。<br></div>
<div><br></div>
<div><a href="http://flume.apache.org/FlumeUserGuide.html#setting-multi-agent-flow" rel="nofollow">多agent模式</a></div>
<img src="https://img-blog.csdn.net/20141201164648884" alt=""><br><a href="http://flume.apache.org/FlumeUserGuide.html#consolidation" rel="nofollow">多对一的合并/Collector</a>场景<br><img src="https://img-blog.csdn.net/20141201164654999" alt=""><br><a href="http://flume.apache.org/FlumeUserGuide.html#multiplexing-the-flow" rel="nofollow">一对多路输出模型</a><br><img src="https://img-blog.csdn.net/20141201164721800" alt=""><br><div><br></div>
<h2>Source接入</h2>
<div>Client端操作消费数据的来源，Flume支持Avro，log4j，syslog和http post(body为json格式)。可以让应用程序同已有的Source直接打交道，如AvroSource，SyslogTcpSource。也可以写一个Source，以IPC或RPC的方式接入自己的应用，Avro和Thrift都可以(分别有NettyAvroRpcClient和ThriftRpcClient实现了RpcClient接口)，其中Avro是默认的RPC协议。具体代码级别的Client端数据接入，可以参考<a href="http://flume.apache.org/FlumeDeveloperGuide.html#client" rel="nofollow">官方手册</a>。<br>
对现有程序改动最小的使用方式是使用是直接读取程序原来记录的日志文件，基本可以实现无缝接入，不需要对现有程序进行任何改动。 <br>
对于直接读取文件Source,有两种方式： <br></div>
<ol><li>ExecSource:以运行Linux命令的方式，持续的输出最新的数据，如tail -F 文件名指令，在这种方式下，取的文件名必须是指定的。 ExecSource可以实现对日志的实时收集，但是存在Flume不运行或者指令执行出错时，将无法收集到日志数据，无法保证日志数据的完整性。<br></li><li>SpoolSource:监测配置的目录下新增的文件，并将文件中的数据读取出来。需要注意两点：拷贝到spool目录下的文件不可以再打开编辑；spool目录下不可包含相应的子目录。SpoolSource虽然无法实现实时的收集数据，但是可以使用以分钟的方式分割文件，趋近于实时。如果应用无法实现以分钟切割日志文件的话，可以两种收集方式结合使用。 在实际使用的过程中，可以结合log4j使用，使用log4j的时候，将log4j的文件分割机制设为1分钟一次，将文件拷贝到spool的监控目录。log4j有一个TimeRolling的插件，可以把log4j分割的文件到spool目录。基本实现了实时的监控。Flume在传完文件之后，将会修改文件的后缀，变为.COMPLETED（后缀也可以在配置文件中灵活指定）</li></ol><pre><code class="language-java">public class MySource extends AbstractSource implements Configurable, PollableSource {
  private String myProp;

  @Override
  public void configure(Context context) {
    String myProp = context.getString("myProp", "defaultValue");

    // Process the myProp value (e.g. validation, convert to another type, ...)

    // Store myProp for later retrieval by process() method
    this.myProp = myProp;
  }

  @Override
  public void start() {
    // Initialize the connection to the external client
  }

  @Override
  public void stop () {
    // Disconnect from external client and do any additional cleanup
    // (e.g. releasing resources or nulling-out field values) ..
  }

  @Override
  public Status process() throws EventDeliveryException {
    Status status = null;

    // Start transaction
    Channel ch = getChannel();
    Transaction txn = ch.getTransaction();
    txn.begin();
    try {
      // This try clause includes whatever Channel operations you want to do

      // Receive new data
      Event e = getSomeData();

      // Store the Event into this Source's associated Channel(s)
      getChannelProcessor().processEvent(e)

      txn.commit();
      status = Status.READY;
    } catch (Throwable t) {
      txn.rollback();

      // Log exception, handle individual exceptions as needed

      status = Status.BACKOFF;

      // re-throw all Errors
      if (t instanceof Error) {
        throw (Error)t;
      }
    } finally {
      txn.close();
    }
    return status;
  }}
</code></pre><br><br><h2>Channel</h2>
<div>Channel有多种方式：有MemoryChannel,JDBC Channel,MemoryRecoverChannel,FileChannel。<br>
MemoryChannel可以实现高速的吞吐，但是无法保证数据的完整性。<br>
MemoryRecoverChannel在官方文档的建议上已经建义使用FileChannel来替换。<br>
FileChannel保证数据的完整性与一致性。在具体配置不现的FileChannel时，建议FileChannel设置的目录和程序日志文件保存的目录设成不同的磁盘，以便提高效率。 <br></div>
<div><br></div>
<h2>Sink</h2>
Sink在设置存储数据时，可以向文件系统、数据库、hadoop存数据，在日志数据较少时，可以将数据存储在文件系中，并且设定一定的时间间隔保存数据。在日志数据较多时，可以将相应的日志数据存储到Hadoop中，便于日后进行相应的数据分析。<br>
更多sink的内容可以参考<a href="http://flume.apache.org/FlumeDeveloperGuide.html#sink" rel="nofollow">官方手册</a>。<br><pre><code class="language-java">public class MySink extends AbstractSink implements Configurable {
  private String myProp;

  @Override
  public void configure(Context context) {
    String myProp = context.getString("myProp", "defaultValue");

    // Process the myProp value (e.g. validation)

    // Store myProp for later retrieval by process() method
    this.myProp = myProp;
  }

  @Override
  public void start() {
    // Initialize the connection to the external repository (e.g. HDFS) that
    // this Sink will forward Events to ..
  }

  @Override
  public void stop () {
    // Disconnect from the external respository and do any
    // additional cleanup (e.g. releasing resources or nulling-out
    // field values) ..
  }

  @Override
  public Status process() throws EventDeliveryException {
    Status status = null;

    // Start transaction
    Channel ch = getChannel();
    Transaction txn = ch.getTransaction();
    txn.begin();
    try {
      // This try clause includes whatever Channel operations you want to do

      Event event = ch.take();

      // Send the Event to the external repository.
      // storeSomeData(e);

      txn.commit();
      status = Status.READY;
    } catch (Throwable t) {
      txn.rollback();

      // Log exception, handle individual exceptions as needed

      status = Status.BACKOFF;

      // re-throw all Errors
      if (t instanceof Error) {
        throw (Error)t;
      }
    } finally {
      txn.close();
    }
    return status;
  }}
</code></pre><br><h2>可靠性</h2>
Flume的核心是把数据从数据源收集过来，再送到目的地。为了保证输送一定成功，在送到目的地之前，会先缓存数据，待数据真正到达目的地后，删除自己缓存的数据。<br>
Flume使用事务性的方式保证传送Event整个过程的可靠性。Sink必须在Event被存入Channel后，或者，已经被传达到下一站agent里，又或者，已经被存入外部数据目的地之后，才能把Event从Channel中remove掉。这样数据流里的event无论是在一个agent里还是多个agent之间流转，都能保证可靠，因为以上的事务保证了event会被成功存储起来。而Channel的多种实现在可恢复性上有不同的保证。也保证了event不同程度的可靠性。比如Flume支持在本地保存一份文件channel作为备份，而memory
 channel将event存在内存queue里，速度快，但丢失的话无法恢复。<br>
具体看一下Transaction。Source和Sink封装了Channel提供的对Event的事务存、取接口，下图为一个transaction过程：<br><img src="https://img-blog.csdn.net/20141201164728679" alt=""><br>
一个Channel的实现里会包括一个transaction的实现，每个与channel打交道的source和sink都得带有一个transaction对象。下面的例子中可以看到一个Event的状态和变化会在一次transation中完成。transaction的状态也对应了时序图中的各个状态。<br><pre><code class="language-java">Channel ch = new MemoryChannel();
Transaction txn = ch.getTransaction();
txn.begin();
try {
  // This try clause includes whatever Channel operations you want to do

  Event eventToStage = EventBuilder.withBody("Hello Flume!",
                       Charset.forName("UTF-8"));
  ch.put(eventToStage);
  // Event takenEvent = ch.take();
  // ...
  txn.commit();
} catch (Throwable t) {
  txn.rollback();

  // Log exception, handle individual exceptions as needed

  // re-throw all Errors
  if (t instanceof Error) {
    throw (Error)t;
  }
} finally {
  txn.close();
}</code></pre><br><br>
转自：<br><a href="http://blog.csdn.net/pelick/article/details/18193527" rel="nofollow">http://blog.csdn.net/pelick/article/details/18193527</a><br><a href="http://blog.csdn.net/rjhym/article/details/8450706" rel="nofollow">http://blog.csdn.net/rjhym/article/details/8450706</a><br><a href="http://flume.apache.org/FlumeDeveloperGuide.html" rel="nofollow">《Flume 1.5.2 Developer Guide》</a><br><br></div>
            </div>
                </div>
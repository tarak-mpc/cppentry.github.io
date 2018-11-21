---
layout:     post
title:      kafka  connect
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">kafka connect是一个kafka与其他系统进行数据流交换的可扩展并且高可用的工具</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">它可以简单定义connect将大的数据集放入kafka,比如它可以低延迟的将数据库或者应用服务器中的metrics数据放入kafka topic</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">导出job将kafka topic数据到另外的存储系统或查询系统或者离线系统进行批量处理</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">kafka connect包括以下特点</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">1.简单的整合其它系统，简单的发布管理</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">2.分布式和独立的模式  可扩展到一个大的，集中管理的服务支持整个组织或者规模下降到开发，测试和小型生产部署<br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">3.通过rest服务可以简单管理kafka connect</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">4.只需要少量的信息提供给connect,kafka 会自动完成offset的管理</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">5.streaming/batch整合，利用kafka现有的功能，可以同时支持streaming数据与批量数据系统</span></p>
<p><br></p>
<p></p>
<h4 style="color:rgb(46,74,142);font-size:14pt;font-family:'Source Sans Pro', sans-serif;line-height:31.111112594604492px;">
<a id="connect_running" href="http://kafka.apache.org/documentation.html#connect_running" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Running Kafka Connect</a></h4>
<span style="font-size:14px;">kafka connect包括两个运行模式;standalone,distribute</span>
<p></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">standalone发布配置简单，适合只有一个worker的工作场景，但是它没有完成分布式模式的容错机制</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">运行standalone使用如下命令</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p></p>
<pre style="font-family:'courier new', courier, monospace;color:rgb(51,51,51);background-color:rgb(255,255,255);"><span style="font-size:14px;">&gt; bin/connect-standalone.sh config/connect-standalone.properties connector1.properties [connector2.properties ...]</span></pre>
<span style="font-size:14px;"><br></span>
<p></p>
<p><span style="font-size:14px;">第一个参数是work的配置，包括序列化类型，提交offset的时间等等，剩下的参数是connector配置文件，你可以定义一些connector的特定配置，任务将工作在相同进程的不同线程中</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">分布式模式将动态扩展，自动负载均衡，容错，offset自动管理，它的执行非常像standalone模式 </span></p>
<p></p>
<pre style="font-family:'courier new', courier, monospace;color:rgb(51,51,51);background-color:rgb(255,255,255);"><span style="font-size:14px;">bin/connect-distributed.sh config/connect-distributed.properties</span></pre>
<span style="font-size:14px;"><br></span>
<p></p>
<p><span style="font-size:14px;">不同的是启动类与后面的参数，参数指定的配置文件用来定义怎样分配任务，怎样存储offset,特别是如下的参数至关重要</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">group.id唯一标识，不能冲突</span><br></span></p>
<p><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;"><span style="font-size:14px;"><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;"><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">config.storage.topic 用于存储connect及task的配置，这个topic应该有一个partition多个replica</span><br></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;"><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;"><span style="font-size:14px;"><br></span></span></span></p>
<p><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;"><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;"><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">offset.storage.topic
 用于存储offset信息，这个topic应该有多个partiton和replica</span><br></span></span></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">注意分布式环境下不能通过命令行修改配置，需要用rest服务来修改</span></p>
<p><br></p>
<p></p>
<h4 style="color:rgb(46,74,142);font-size:14pt;font-family:'Source Sans Pro', sans-serif;line-height:31.111112594604492px;">
<a id="connect_configuring" href="http://kafka.apache.org/documentation.html#connect_configuring" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Configuring Connectors</a></h4>
<span style="font-size:14px;">连接器的配置是简单的键值映射。对于standalone模式，这些被定义在一个属性文件中，并传递到命令行的连接进程。在分布式模式，他们将包括在JSON中，创建/修改连接</span>
<p></p>
<p><span style="font-size:14px;">器。大多数配置是连接器相关的，所以它们不能在这里概述。然而，有几个通用的选项：</span></p>
<p><span style="font-size:14px;"><br>
名称: 连接器的唯一名称。试图注册相同的名称将失败</span></p>
<p><span style="font-size:14px;"><br>
connector.class  连接器的java类</span></p>
<p><span style="font-size:14px;"><br>
tasks.max  可创建最大数量的任务。如果无法达到该值，该连接器创建较少的任务。</span><br></p>
<p><br></p>
<p></p>
<h4 style="color:rgb(46,74,142);font-size:14pt;font-family:'Source Sans Pro', sans-serif;line-height:31.111112594604492px;">
<a id="connect_rest" href="http://kafka.apache.org/documentation.html#connect_rest" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">REST API</a></h4>
<span style="font-size:14px;">kafka connect的目的是作为一个服务运行，它支持rest管理。默认情况下，此服务运行于端口8083。以下是当前支持的：</span>
<p></p>
<p><span style="font-size:14px;"><br><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">GET /connectors </span>返回一个活动的connect列表<br><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">POST /connectors </span>创建一个新的connect；请求体是一个JSON对象包含一个名称字段和连接器配置参数</span></p>
<p><span style="font-size:14px;"><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">GET /connectors/{name} </span>获取有关特定连接器的信息<br><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">GET /connectors/{name}/config </span>获得特定连接器的配置参数<br><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">PUT /connectors/{name}/config </span>更新特定连接器的配置参数<br><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">GET /connectors/{name}/tasks 获得</span>正在运行的一个连接器的任务的列表<br><span style="color:rgb(51,51,51);font-family:'courier new', courier, monospace;">DELETE /connectors/{name} </span>删除一个连接器，停止所有任务，并删除它的配置<br></span></p>
<p><br></p>
<p></p>
<h3 style="color:rgb(46,74,142);font-size:16pt;font-family:'Source Sans Pro', sans-serif;line-height:31.111112594604492px;">
<a id="connect_development" href="http://kafka.apache.org/documentation.html#connect_development" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Connector Development Guide</a></h3>
<h4 style="color:rgb(46,74,142);font-size:14pt;font-family:'Source Sans Pro', sans-serif;line-height:31.111112594604492px;">
<a id="connect_concepts" href="http://kafka.apache.org/documentation.html#connect_concepts" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Core Concepts and APIs</a></h4>
<div>
<h5 style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;line-height:31.111112594604492px;">
<a id="connect_connectorsandtasks" href="http://kafka.apache.org/documentation.html#connect_connectorsandtasks" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Connectors and Tasks</a></h5>
在kafka与其他系统间复制数据需要创建kafka connect,他们将数复制到kafka或者从kafka复制到其他系统</div>
<div>连接器有两种形式：sourceconnectors将另一个系统数据导入kafka，sinkconnectors将数据导出到另一个系统<br></div>
<div>连接器不执行任何数据复制：它们的描述复制的数据，并且负责将工作分配给多个task<br></div>
<div>task分为sourcetask与sinktask</div>
<div><br></div>
<br><p></p>
<p>每个task从kafka复制数据,connect会保证record与schema的一致性完成任务分配，通常record与schema的映射是明显的，每一个文件对应一个流，流中的每一条记录利用schema解析并且保存对应的offset,另外一种情况是我们需要自己完成这种映射，比如数据库，表的offset不是很明确（没有自增id),一种可能的选择是利用时间（timestamp)来完成增量查询。<br><br><br></p>
<h5 style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;line-height:29.33333396911621px;">
<a id="connect_streamsandrecords" href="http://kafka.apache.org/documentation.html#connect_streamsandrecords" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Streams and Records</a></h5>
<br><br><br>
每一个stream是包含key value对的记录的序列，key value可以是原始类型，可以支持复杂结构，除了array,object，嵌套等。数据转换是框架来完成的，record中包含stream id与offset,用于定时offset提交，帮助当处理失败时恢复避免重复处理。<br><br><br><h5 style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;line-height:29.33333396911621px;">
<a id="connect_dynamicconnectors" href="http://kafka.apache.org/documentation.html#connect_dynamicconnectors" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Dynamic Connectors</a></h5>
<br><br><br>
所有的job不是静态的，它需要监听外部系统的变化，比如数据库表的增加删除，当一个新table创建时，它必须发现并且更新配置由框架来分配给该表一个task去处理，当通知发布后框架会更新对应的task.<br><br><br><h4 style="color:rgb(46,74,142);font-size:14pt;font-family:'Source Sans Pro', sans-serif;line-height:29.33333396911621px;">
<a id="connect_developing" href="http://kafka.apache.org/documentation.html#connect_developing" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Developing a Simple Connector</a></h4>
<br><br><br>
例子很简单<br><br><br>
在standalone模式下实现 SourceConnector/SourceTask 读取文件并且发布record给SinkConnector/SinkTask 由sink写入文件<br><br><br><h5 style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;line-height:29.33333396911621px;">
<a id="connect_connectorexample" href="http://kafka.apache.org/documentation.html#connect_connectorexample" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Connector Example</a></h5>
<br><br><br>
我们将实现SourceConnector，SinkConnector实现与它非常类似，它包括两个私有字段存放配置信息（读取的文件名与topic名称）<br><br><br>
public class FileStreamSourceConnector extends SourceConnector {<br>
    private String filename;<br>
    private String topic;<br><br><br>
getTaskClass()方法定义实现执行处理的task<br><br><br>
@Override<br>
public Class getTaskClass() {<br>
    return FileStreamSourceTask.class;<br>
}<br><br><br>
下面定义FileStreamSourceTask，它包括两个生命周期方法start,stop<br><br><br>
@Override<br>
public void start(Map&lt;String, String&gt; props) {<br>
    // The complete version includes error handling as well.<br>
    filename = props.get(FILE_CONFIG);<br>
    topic = props.get(TOPIC_CONFIG);<br>
}<br><br><br>
@Override<br>
public void stop() {<br>
    // Nothing to do since no background monitoring is required.<br>
}<br><br><br>
最后是真正核心的方法getTaskConfigs（）在这里我们仅处理一个文件，所以我们虽然定义了max task（在配置文件里）但是只会返回一个包含一条entry的list<br><br><br><br><br>
@Override<br>
public List&lt;Map&lt;String, String&gt;&gt; getTaskConfigs(int maxTasks) {<br>
    ArrayList&gt;Map&lt;String, String&gt;&gt; configs = new ArrayList&lt;&gt;();<br>
    // Only one input stream makes sense.<br>
    Map&lt;String, String&gt; config = new Map&lt;&gt;();<br>
    if (filename != null)<br>
        config.put(FILE_CONFIG, filename);<br>
    config.put(TOPIC_CONFIG, topic);<br>
    configs.add(config);<br>
    return configs;<br>
}<br><br><br>
即使有多个任务，这种方法的执行通常很简单。它只是要确定输入任务的数量，这可能需要拉取数据从远程服务，然后分摊。请注意，这个简单的例子不包括动态输入。在下一节中看到讨论如何触发任务的配置更新。<br><br><br><h5 style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;line-height:29.33333396911621px;">
<a id="connect_taskexample" href="http://kafka.apache.org/documentation.html#connect_taskexample" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Task Example - Source Task</a></h5>
<br><br><br>
实现task，我们使用伪代码描述核心代码<br><br><br>
public class FileStreamSourceTask extends SourceTask&lt;Object, Object&gt; {<br>
    String filename;<br>
    InputStream stream;<br>
    String topic;<br><br><br>
    public void start(Map&lt;String, String&gt; props) {<br>
        filename = props.get(FileStreamSourceConnector.FILE_CONFIG);<br>
        stream = openOrThrowError(filename);<br>
        topic = props.get(FileStreamSourceConnector.TOPIC_CONFIG);<br>
    }<br><br><br>
    @Override<br>
    public synchronized void stop() {<br>
        stream.close()<br>
    }<br><br><br>
start方法读取之前的offset,并且处理新的数据，stop方法停止stream,下面实现poll方法<br><br><br>
@Override<br>
public List&lt;SourceRecord&gt; poll() throws InterruptedException {<br>
    try {<br>
        ArrayList&lt;SourceRecord&gt; records = new ArrayList&lt;&gt;();<br>
        while (streamValid(stream) &amp;&amp; records.isEmpty()) {<br>
            LineAndOffset line = readToNextLine(stream);<br>
            if (line != null) {<br>
                Map sourcePartition = Collections.singletonMap("filename", filename);<br>
                Map sourceOffset = Collections.singletonMap("position", streamOffset);<br>
                records.add(new SourceRecord(sourcePartition, sourceOffset, topic, Schema.STRING_SCHEMA, line));<br>
            } else {<br>
                Thread.sleep(1);<br>
            }<br>
        }<br>
        return records;<br>
    } catch (IOException e) {<br>
        // Underlying stream was killed, probably as a result of calling stop. Allow to return<br>
        // null, and driving thread will handle any shutdown if necessary.<br>
    }<br>
    return null;<br>
}<br><br><br>
该方法重复执行读取操作，跟踪file offset,并且利用上述信息创建SourceRecord，它需要四个字段：source partition,source offset,topic name,output value(包括value及value的schema)<br><br><br><h5 style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;line-height:29.33333396911621px;">
<a id="connect_sinktasks" href="http://kafka.apache.org/documentation.html#connect_sinktasks" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Sink Tasks</a></h5>
<br><br><br>
之前描述了sourcetask实现，sinktask与它完全不同，因为前者是拉取数据，后者是推送数据<br><br><br>
public abstract class SinkTask implements Task {<br>
public void initialize(SinkTaskContext context) { ... }<br><br><br>
public abstract void put(Collection&lt;SinkRecord&gt; records);<br><br><br>
public abstract void flush(Map&lt;TopicPartition, Long&gt; offsets);<br><br><br>
put方法是最重要的方法，接收sinkrecords，执行任何需要的转换，并将其存储在目标系统。此方法不需要确保数据已被完全写入目标系统，然后返回。事实上首先放入缓冲，因此，批量数据可以被一次发送，减少对下游存储的压力。sourcerecords中保存的信息与sourcesink中的相同。flush提交offset，它接受任务从故障中恢复，没有数据丢失。该方法将数据推送至目标系统，并且block直到写入已被确认。的offsets参数通常可以忽略不计，但在某些情况保存偏移信息到目标系统确保一次交货。例如，一个HDFS连接器可以确保flush()操作自动提交数据和偏移到HDFS中的位置。<br><br><br><h5 style="color:rgb(51,51,51);font-family:'Source Sans Pro', sans-serif;line-height:29.33333396911621px;">
<a id="connect_resuming" href="http://kafka.apache.org/documentation.html#connect_resuming" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Resuming from Previous Offsets</a></h5>
<br><br><br>
kafka connect是为了bulk 数据拷贝工作，它拷贝整个db而不是拷贝某个表，这样会使用connnect的input或者output随时改变，source connector需要监听source系统的改变，当改变时通知框架（通过ConnectorContext对象）<br>
举例<br>
if (inputsChanged())<br>
    this.context.requestTaskReconfiguration();<br><br><br>
当接收到通知框架会即时的更新配置，并且在更新前确保优雅完成当前任务<br><br><br>
如果一个额外的线程来执行此监控，该线程必须存在于连接器中。该线程不会影响connector。然而，其他变化也会影响task，最常见的是输入流失败在输入系统中，例如如果一个表被从数据库中删除。这时连接器需要进行更改，任务将需要处理这种异常。sinkconnectors只能处理流的加入，可以分配新的数据到task（例如，一个新的数据库表）。框架会处理任何kafka输入的改变，例如当组输入topic的变化因为一个正则表达式的订阅。sinktasks应该期待新的输入流，可能需要在下游系统创造新的资源，如数据库中的一个新的表。在这些情况下，可能会出现输入流之间的冲突（同时创建新资源），其他时候，一般不需要特殊的代码处理一系列动态流<br>
  <br><h4 style="color:rgb(46,74,142);font-size:14pt;font-family:'Source Sans Pro', sans-serif;line-height:29.33333396911621px;">
<a id="connect_dynamicio" href="http://kafka.apache.org/documentation.html#connect_dynamicio" rel="nofollow" style="color:rgb(46,74,142);text-decoration:none;">Dynamic Input/Output Streams</a></h4>
<br><br>
FileStream连接器是很好的例子，因为他们很简单的，每一行是一个字符串。实际连接器都需要具有更复杂的数据格式。要创建更复杂的数据，你需要使用kafka connector数据接口:Schema,Struct<br><br><br>
Schema schema = SchemaBuilder.struct().name(NAME)<br>
                    .field("name", Schema.STRING_SCHEMA)<br>
                    .field("age", Schema.INT_SCHEMA)<br>
                    .field("admin", new SchemaBuilder.boolean().defaultValue(false).build())<br>
                    .build();<br><br><br>
Struct struct = new Struct(schema)<br>
                           .put("name", "Barbara Liskov")<br>
                           .put("age", 75)<br>
                           .build();<br><br><br>
如果上游数据与schema数据格式不一致应该在sinktask中抛出异常<br><p></p>
<p><br></p>
            </div>
                </div>
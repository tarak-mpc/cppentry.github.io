---
layout:     post
title:      kafka connect，将数据批量写到hdfs完整过程
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>本文是基于hadoop 2.7.1，以及kafka 0.11.0.0。kafka-connect是以单节点模式运行，即standalone。</p>
<h3><a id="_kafkakafka_connect_2"></a>一. kafka和kafka connect简介</h3>
<p>kafka：Kafka是一种高吞吐量的分布式发布订阅消息系统，它可以处理消费者规模的网站中的所有动作流数据。比较直观的解释就是其有一个生产者（producer）和一个消费者（consumer）。可以将kafka想象成一个数据容器，生产者负责发送数据到这个容器中，而消费者从容器中取出数据，在将数据做处理，如存储到hdfs。</p>
<p>kafka connect：Kafka Connect是一种用于在Kafka和其他系统之间可扩展的、可靠的流式传输数据的工具。它使得能够快速定义将大量数据集合移入和移出Kafka的连接器变得简单。即适合批量数据导入导出操作。</p>
<h3><a id="_kafka_connecthdfs_8"></a>二. 如何用kafka connect将数据写入到hdfs中</h3>
<p>首先启动kafka-connect:</p>
<pre><code>bin/connect-standalone.sh config/connect-standalone.properties config/connector1.properties
</code></pre>
<p>这个命令后面两个参数，<br>
　　第一个是指定启动的模式，有分布式和单节点两种，这里是单节点。kafka自带，放于config目录下。<br>
　　第二个参数指向描述connector的属性的文件，可以有多个，这里只有一个connector用来写入到hdfs。需要自己创建。<br>
　　<br>
接下来看看connector1.properties的内容，</p>
<pre><code>name="test"    #该connector的名字
#将自己按connect接口规范编写的代码打包后放在kafka/libs目录下，再根据项目结构引用对应connector
connector.class=hdfs.HdfsSinkConnector 
#Task是导入导出的具体实现，这里是指定多少个task来并行运行导入导出作业，由多线程实现。由于hdfs中一个文件每次只能又一个文件操作，所以这里只能是1
tasks.max=1 
#指定从哪个topic读取数据，这些其实是用来在connector或者task的代码中读取的。
topics=test 
#指定key以那种方式转换，需和Producer发送方指定的序列化方式一致
key.converter=org.apache.kafka.connect.converters.ByteArrayConverter 
value.converter=org.apache.kafka.connect.json.JsonConverter     #同上
hdfs.url=hdfs://127.0.0.1:9000　　#hdfs的url路径，在Connector中会被读取
hdfs.path=/test/file　　#hdfs文件路径，同样Connector中被读取

key.converter.schemas.enable=true　　#稍后介绍，可以true也可以false，影响传输格式
value.converter.schemas.enable=true　　#稍后介绍，可以true也可以false
</code></pre>
<p>三. 主要代码<br>
connect主要是导入导出两个概念，导入是source，导出时Sink。这里只使用Sink，不过Source和Sink的实现其实基本相同。<br>
实现Sink其实不难，实现对应的接口，即SinkConnector和SinkTask两个接口，再打包放到kafka/libs目录下即可。其中SinkConnector只有一个，而Task可以有多<br>
先是Connector</p>
<pre><code>public class HdfsSinkConnector extends SinkConnector {
    //这两项为配置hdfs的urlh和路径的配置项，需要在connector1.properties中指定
    public static final String HDFS_URL = "hdfs.url";
    public static final String HDFS_PATH = "hdfs.path";
    private static final ConfigDef CONFIG_DEF = new ConfigDef()
            .define(HDFS_URL, ConfigDef.Type.STRING, ConfigDef.Importance.HIGH, "hdfs url")
            .define(HDFS_PATH, ConfigDef.Type.STRING, ConfigDef.Importance.HIGH, "hdfs path");
    private String hdfsUrl;
    private String hdfsPath;
    @Override
    public String version() {
        return AppInfoParser.getVersion();
    }
　　//start方法会再初始的时候执行一次，这里主要用于配置
    @Override
    public void start(Map&lt;String, String&gt; props) {
        hdfsUrl = props.get(HDFS_URL);
        hdfsPath = props.get(HDFS_PATH);
    }
　　//这里指定了Task的类
    @Override
    public Class&lt;? extends Task&gt; taskClass() {
        return HdfsSinkTask.class;
    }
　　//用于配置Task的config，这些都是会在Task中用到
    @Override
    public List&lt;Map&lt;String, String&gt;&gt; taskConfigs(int maxTasks) {
        ArrayList&lt;Map&lt;String, String&gt;&gt; configs = new ArrayList&lt;&gt;();
        for (int i = 0; i &lt; maxTasks; i++) {
            Map&lt;String, String&gt; config = new HashMap&lt;&gt;();
            if (hdfsUrl != null)
                config.put(HDFS_URL, hdfsUrl);
            if (hdfsPath != null)
                config.put(HDFS_PATH, hdfsPath);
            configs.add(config);
        }
        return configs;
    }
　　//关闭时的操作，一般是关闭资源。
    @Override
    public void stop() {
        // Nothing to do since FileStreamSinkConnector has no background monitoring.
    }

    @Override
    public ConfigDef config() {
        return CONFIG_DEF;
    }

}
</code></pre>
<p>接下来是Task</p>
<pre><code>public class HdfsSinkTask extends SinkTask {
    private static final Logger log = LoggerFactory.getLogger(HdfsSinkTask.class);

    private String filename;

    public static String hdfsUrl;
    public static String hdfsPath;
    private Configuration conf;
    private FSDataOutputStream os;
    private FileSystem hdfs;


    public HdfsSinkTask(){

    }

    @Override
    public String version() {
        return new HdfsSinkConnector().version();
    }
　　//Task开始会执行的代码，可能有多个Task，所以每个Task都会执行一次
    @Override
    public void start(Map&lt;String, String&gt; props) {
        hdfsUrl = props.get(HdfsSinkConnector.HDFS_URL);
        hdfsPath = props.get(HdfsSinkConnector.HDFS_PATH);
        System.out.println("----------------------------------- start--------------------------------");

        conf = new Configuration();
        conf.set("fs.defaultFS", hdfsUrl);
        //这两个是与hdfs append相关的设置
        conf.setBoolean("dfs.support.append", true);
        conf.set("dfs.client.block.write.replace-datanode-on-failure.policy", "NEVER");
        try{
            hdfs = FileSystem.get(conf);
//            connector.hdfs = new Path(HDFSPATH).getFileSystem(conf);
            os = hdfs.append(new Path(hdfsPath));
        }catch (IOException e){
            System.out.println(e.toString());
        }

    }
　　//核心操作，put就是将数据从kafka中取出，存放到其他地方去
    @Override
    public void put(Collection&lt;SinkRecord&gt; sinkRecords) {
        for (SinkRecord record : sinkRecords) {
            log.trace("Writing line to {}: {}", logFilename(), record.value());
            try{
                System.out.println("write info------------------------" + record.value().toString() + "-----------------");
                os.write((record.value().toString()).getBytes("UTF-8"));
                os.hsync();
            }catch(Exception e){
                System.out.print(e.toString());
            }
        }
    }

    @Override
    public void flush(Map&lt;TopicPartition, OffsetAndMetadata&gt; offsets) {
        try{
            os.hsync();
        }catch (Exception e){
            System.out.print(e.toString());
        }

    }
　　//同样是结束时候所执行的代码，这里用于关闭hdfs资源
    @Override
    public void stop() {
        try {
            os.close();
        }catch(IOException e){
            System.out.println(e.toString());
        }
    }

    private String logFilename() {
        return filename == null ? "stdout" : filename;
    }


}
</code></pre>
<p>这里重点提一下，因为在connector1.propertise中设置了key.converter=org.apache.kafka.connect.converters.ByteArrayConverter，所以不能用命令行形式的<br>
producer发送数据，而是要用程序的方式，并且在producer总也要设置key的序列化形式为org.apache.kafka.common.serialization.ByteArraySerializer。<br>
编码完成，先用idea以开发程序与依赖包分离的形式打包成jar包，然后将程序对应的jar包（一般就是“项目名.jar”）放到kafka/libs目录下面，这样就能被找到。</p>
<h3><a id="_178"></a>四.出现问题汇总</h3>
<h4><a id="1connector1propertieskeyconverterschemasenablefalsevalueconverterschemasenablefalse_179"></a>1.在connector1.properties中的key.converter.schemas.enable=false和value.converter.schemas.enable=false的问题。</h4>
<p>这个选项默认在connect-standalone.properties中是true的，这个时候发送给topic的Json格式是需要使用avro格式，具体情况可以百度，这里给出一个样例。</p>
<pre><code>{
    "schema": {
        "type": "struct",
        "fields": [{
            "type": "int32",
            "optional": true,
            "field": "c1"
        }, {
            "type": "string",
            "optional": true,
            "field": "c2"
        }, {
            "type": "int64",
            "optional": false,
            "name": "org.apache.kafka.connect.data.Timestamp",
            "version": 1,
            "field": "create_ts"
        }, {
            "type": "int64",
            "optional": false,
            "name": "org.apache.kafka.connect.data.Timestamp",
            "version": 1,
            "field": "update_ts"
        }],
        "optional": false,
        "name": "foobar"
    },
    "payload": {
        "c1": 10000,
        "c2": "bar",
        "create_ts": 1501834166000,
        "update_ts": 1501834166000
    }
}  
</code></pre>
<p>主要就是schema和payload这两个，不按照这个格式会报错如下</p>
<p>org.apache.kafka.connect.errors.DataException: JsonConverter with schemas.enable requires “schema” and “payload” fields and may not contain additional fields. If you are trying to deserialize plain JSON data, set schemas.enable=false in your converter configuration.</p>
<p>at org.apache.kafka.connect.json.JsonConverter.toConnectData(JsonConverter.java:308)<br>
如果想发送普通的json格式而不是avro格式的话，很简单key.converter.schemas.enable和value.converter.schemas.enable设置为false就行。这样就能发送普通的json格式数据。</p>
<h4><a id="2javalangClassNotFoundException_224"></a>2.在启动的过程中出现各种各样的java.lang.ClassNotFoundException。</h4>
<p>在启动connector的时候，一开始总是会报各个各样的ClassNotFoundException，不是这个包就是那个包，查找问题一直说要么缺少包要么是包冲突。这个是什么原因呢？</p>
<p>其实归根结底还是依赖冲突的问题，因为kafka程序自定义的类加载器加载类的目录是在kafka/libs中，而写到hdfs需要hadoop的包。</p>
<p>我一开始的做法是将hadoop下的包路径添加到CLASSPATH中，这样子问题就来了，因为kafka和hadoop的依赖包是有冲突的，比如hadoop是guava-11.0.2.jar，而kafka是guava-20.0.jar，两个jar包版本不同，而我们是在kafka程序中调用hdfs，所以当jar包冲突时应该优先调用kafka的。但是注意kafka用的是程序自定义的类加载器，其优先级是低于CLASSPATH路径下的类的，就是说加载类时会优先加载CLASSPATH下的类。这样子就有问题了。</p>
<p>我的解决方案时将kafka和hadoop加载的jar包路径都添加到CLASSPATH中，并且kafka的路径写在hadoop前面，这样就可以启动connector成功。</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
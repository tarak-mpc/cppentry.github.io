---
layout:     post
title:      kafka与zookeeper单机集群搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/llh1249055292/article/details/77876250				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="kafka与zookeeper单机集群搭建">kafka与zookeeper单机集群搭建</h1>

<h2 id="一zookeeper环境搭建">一、Zookeeper环境搭建</h2>

<blockquote>
  <p>Zookeeper是一种在分布式系统中被广泛用来作为分布式状态管理、分布式协调管理、 <br>
  分布式配置管理、和分布式锁服务的集群。kafka增加和减少服务器都会在Zookeeper节点上触发相应的事件kafka系统会捕获这些事件， <br>
  进行新一轮的负载均衡，客户端也会捕获这些事件来进行新一轮的处理，所以启动Kafka之前需要安装并启动zookeeper。</p>
</blockquote>

<p>Zookeeper的安装和配置十分简单, 既可以配置成单机模式, 也可以配置成集群模式. 下面将分别进行介绍.</p>



<h3 id="1-单机模式">1. 单机模式</h3>

<p><a href="https://zookeeper.apache.org/releases.html" rel="nofollow" target="_blank">下载</a> zookeeper的安装包之后, 解压到合适目录. 进入zookeeper目录下的conf子目录, 创建zoo.cfg进行如下配置:</p>



<pre class="prettyprint"><code class=" hljs ini"><span class="hljs-setting">tickTime=<span class="hljs-value"><span class="hljs-number">2000</span></span></span>
<span class="hljs-setting">dataDir=<span class="hljs-value">/opt/zookeeper/data</span></span>
<span class="hljs-setting">dataLogDir=<span class="hljs-value">/opt/zookeeper/logs</span></span>
<span class="hljs-setting">clientPort=<span class="hljs-value"><span class="hljs-number">2181</span></span></span></code></pre>

<p>参数说明: <br>
* tickTime: zookeeper中使用的基本时间单位, 毫秒值. <br>
* dataDir: 数据目录. 可以是任意目录. <br>
* dataLogDir: log目录, 同样可以是任意目录. 如果没有设置该参数, 将使用和dataDir相同的设置. <br>
* clientPort: 监听client连接的端口号.</p>

<p>到现在为止，zookeeper的单机模式已经配置好了. 启动server只需运行以下脚本:</p>



<pre class="prettyprint"><code class=" hljs avrasm">bin/zkServer<span class="hljs-preprocessor">.sh</span> start conf/zoo<span class="hljs-preprocessor">.cfg</span></code></pre>

<p>查看服务状态:</p>



<pre class="prettyprint"><code class=" hljs avrasm">bin/zkServer<span class="hljs-preprocessor">.sh</span> status conf/zoo<span class="hljs-preprocessor">.cfg</span></code></pre>

<p>Server启动之后, 就可以启动client连接server了, 启动客户端可查看具体信息:</p>



<pre class="prettyprint"><code class=" hljs profile">bin/<span class="hljs-filename">zkCli.sh -server localhost</span>:<span class="hljs-number">2181</span></code></pre>



<h3 id="2-伪集群模式">2. 伪集群模式</h3>

<p>所谓伪集群, 是指在单台机器中启动多个zookeeper进程, 并组成一个集群. 以启动3个zookeeper进程为例.</p>

<p>将conf/目录下的zoo.cfg复制两份，如下所示:</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">|</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zoo</span><span class="hljs-string">.</span><span class="hljs-comment">cfg</span>
<span class="hljs-comment">|</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zoo1</span><span class="hljs-string">.</span><span class="hljs-comment">cfg</span>
<span class="hljs-comment">|</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zoo2</span><span class="hljs-string">.</span><span class="hljs-comment">cfg</span></code></pre>

<p>更改conf/zoo.cfg文件如下:</p>



<pre class="prettyprint"><code class=" hljs makefile"><span class="hljs-constant">tickTime</span>=2000
<span class="hljs-constant">initLimit</span>=5
<span class="hljs-constant">syncLimit</span>=2
<span class="hljs-constant">dataDir</span>=/opt/zookeeper/data
<span class="hljs-constant">dataLogDir</span>=/opt/zookeeper/logs
<span class="hljs-constant">clientPort</span>=2181
server.0=127.0.0.1:2888:3888
server.1=127.0.0.1:2889:3889
server.2=127.0.0.1:2890:3890</code></pre>

<p>更改conf/zoo1.cfg文件如下:</p>



<pre class="prettyprint"><code class=" hljs makefile"><span class="hljs-constant">tickTime</span>=2000
<span class="hljs-constant">initLimit</span>=5
<span class="hljs-constant">syncLimit</span>=2
<span class="hljs-constant">dataDir</span>=/opt/zookeeper/data1
<span class="hljs-constant">dataLogDir</span>=/opt/zookeeper/logs1
<span class="hljs-constant">clientPort</span>=2182
server.0=127.0.0.1:2888:3888
server.1=127.0.0.1:2889:3889
server.2=127.0.0.1:2890:3890</code></pre>

<p>更改conf/zoo2.cfg文件如下:</p>



<pre class="prettyprint"><code class=" hljs makefile"><span class="hljs-constant">tickTime</span>=2000
<span class="hljs-constant">initLimit</span>=5
<span class="hljs-constant">syncLimit</span>=2
<span class="hljs-constant">dataDir</span>=/opt/zookeeper/data2
<span class="hljs-constant">dataLogDir</span>=/opt/zookeeper/logs2
<span class="hljs-constant">clientPort</span>=2183
server.0=127.0.0.1:2888:3888
server.1=127.0.0.1:2889:3889
server.2=127.0.0.1:2890:3890</code></pre>

<p>新增了几个参数, 其含义如下: <br>
* initLimit:  zookeeper集群中的包含多台server, 其中一台为leader, 集群中其余的server为follower. initLimit参数配置初始化连接时, follower和leader之间的最长心跳时间. 此时该参数设置为5, 说明时间限制为5倍tickTime, 即5*2000=10000ms=10s. <br>
* syncLimit:  该参数配置leader和follower之间发送消息, 请求和应答的最大时间长度. 此时该参数设置为2, 说明时间限制为2倍tickTime, 即4000ms. <br>
* server.X=A:B:C 其中X是一个数字, 表示这是第几号server. A是该server所在的IP地址. B配置该server和集群中的leader交换消息所使用的端口. C配置选举leader时所使用的端口. 由于配置的是伪集群模式, 所以各个server的B, C参数必须不同.</p>

<p>在之前设置的dataDir中新建myid文件, 写入一个数字, 该数字表示这是第几号server. 该数字必须和zoo.cfg文件中的server.X中的X一 一对应,如下所示:</p>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">|</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">data</span>  &gt;&gt; <span class="hljs-comment">0</span>
<span class="hljs-comment">|</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">data1</span> &gt;&gt; <span class="hljs-comment">1</span>
<span class="hljs-comment">|</span><span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">data2</span> &gt;&gt; <span class="hljs-comment">2</span></code></pre>

<p>分别启动三个server:</p>



<pre class="prettyprint"><code class=" hljs avrasm">bin/zkServer<span class="hljs-preprocessor">.sh</span> start conf/zoo<span class="hljs-preprocessor">.cfg</span>
bin/zkServer<span class="hljs-preprocessor">.sh</span> start conf/zoo1<span class="hljs-preprocessor">.cfg</span>
bin/zkServer<span class="hljs-preprocessor">.sh</span> start conf/zoo2<span class="hljs-preprocessor">.cfg</span></code></pre>

<p>任意选择一个server目录, 启动客户端可查看具体信息:</p>



<pre class="prettyprint"><code class=" hljs profile">bin/<span class="hljs-filename">zkCli.sh -server localhost</span>:<span class="hljs-number">2181</span></code></pre>



<h3 id="集群模式">集群模式</h3>

<p>集群模式的配置和伪集群基本一致 <br>
由于集群模式下, 各server部署在不同的机器上, 因此各server的conf/zoo.cfg文件可以完全一样,如下所示:</p>



<pre class="prettyprint"><code class=" hljs makefile"><span class="hljs-constant">tickTime</span>=2000
<span class="hljs-constant">initLimit</span>=5
<span class="hljs-constant">syncLimit</span>=2
<span class="hljs-constant">dataDir</span>=/opt/zookeeper/data
<span class="hljs-constant">dataLogDir</span>=/opt/zookeeper/logs
<span class="hljs-constant">clientPort</span>=2181
server.0=10.181.65.180:2888:3888
server.1=10.181.65.180:2888:3888
server.2=10.181.65.180:2888:3888</code></pre>

<blockquote>
  <p>注意事项: 无论是集群还是伪集群模式都要配置相应的myid文件</p>
</blockquote>



<h2 id="二kafka环境搭建">二、Kafka环境搭建</h2>

<blockquote>
  <p>Kafka是一个分布式消息系统，由linkedin使用scala编写，用作LinkedIn的活动流（Activity Stream）和运营数据处理管道（Pipeline）的基础,具有高水平扩展和高吞吐量</p>
</blockquote>

<p><a href="http://kafka.apache.org/" rel="nofollow" target="_blank">下载</a> Kafka的安装包之后, 解压到合适目录，Kafka和zookeeper一样可以分为单机模式、 <br>
伪集群模式和集群模式，因为都大同小异，这里只介绍一下集群模式的搭建.</p>

<p>将下载的Kafka软件解压进入config目录下，主要关注server.properties 这个文件即可，修改配置文件:</p>



<pre class="prettyprint"><code class=" hljs avrasm">broker<span class="hljs-preprocessor">.id</span>=<span class="hljs-number">0</span>     <span class="hljs-preprocessor">#当前机器在集群中的唯一标识，和zookeeper的myid性质一样</span>
port=<span class="hljs-number">9092</span>      <span class="hljs-preprocessor">#当前kafka对外提供服务的端口默认是9092</span>
host<span class="hljs-preprocessor">.name</span>=<span class="hljs-number">10.181</span><span class="hljs-number">.65</span><span class="hljs-number">.180</span>       <span class="hljs-preprocessor">#这个参数默认是关闭的，在0.8.1有个bug，DNS解析问题，失败率的问题。</span>
num<span class="hljs-preprocessor">.network</span><span class="hljs-preprocessor">.threads</span>=<span class="hljs-number">3</span>         <span class="hljs-preprocessor">#这个是borker进行网络处理的线程数</span>
num<span class="hljs-preprocessor">.io</span><span class="hljs-preprocessor">.threads</span>=<span class="hljs-number">8</span>              <span class="hljs-preprocessor">#这个是borker进行I/O处理的线程数</span>
log<span class="hljs-preprocessor">.dirs</span>=/opt/kafka/kafkalogs/       <span class="hljs-preprocessor">#消息存放的目录，这个目录可以配置为“，”逗号分割的表达式，上面的num.io.threads要大于这个目录的个数这个目录，如果配置多个目录，新创建的topic他把消息持久化的地方是，当前以逗号分割的目录中，那个分区数最少就放那一个</span>
socket<span class="hljs-preprocessor">.send</span><span class="hljs-preprocessor">.buffer</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">102400</span>      <span class="hljs-preprocessor">#发送缓冲区buffer大小，数据不是一下子就发送的，先回存储到缓冲区了到达一定的大小后在发送，能提高性能</span>
socket<span class="hljs-preprocessor">.receive</span><span class="hljs-preprocessor">.buffer</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">102400</span>   <span class="hljs-preprocessor">#kafka接收缓冲区大小，当数据到达一定大小后在序列化到磁盘</span>
socket<span class="hljs-preprocessor">.request</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">104857600</span>   <span class="hljs-preprocessor">#这个参数是向kafka请求消息或者向kafka发送消息的请请求的最大数，这个值不能超过java的堆栈大小</span>
num<span class="hljs-preprocessor">.partitions</span>=<span class="hljs-number">1</span>                     <span class="hljs-preprocessor">#默认的分区数，一个topic默认1个分区数</span>
log<span class="hljs-preprocessor">.retention</span><span class="hljs-preprocessor">.hours</span>=<span class="hljs-number">168</span>              <span class="hljs-preprocessor">#默认消息的最大持久化时间，168小时，7天</span>
message<span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.byte</span>=<span class="hljs-number">5242880</span>             <span class="hljs-preprocessor">#消息保存的最大值5M</span>
default<span class="hljs-preprocessor">.replication</span><span class="hljs-preprocessor">.factor</span>=<span class="hljs-number">2</span>         <span class="hljs-preprocessor">#kafka保存消息的副本数，如果一个副本失效了，另一个还可以继续提供服务</span>
replica<span class="hljs-preprocessor">.fetch</span><span class="hljs-preprocessor">.max</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">5242880</span>      <span class="hljs-preprocessor">#取消息的最大直接数</span>
log<span class="hljs-preprocessor">.segment</span><span class="hljs-preprocessor">.bytes</span>=<span class="hljs-number">1073741824</span>         <span class="hljs-preprocessor">#这个参数是：因为kafka的消息是以追加的形式落地到文件，当超过这个值的时候，kafka会新起一个文件</span>
log<span class="hljs-preprocessor">.retention</span><span class="hljs-preprocessor">.check</span><span class="hljs-preprocessor">.interval</span><span class="hljs-preprocessor">.ms</span>=<span class="hljs-number">300000</span>         <span class="hljs-preprocessor">#每隔300000毫秒去检查上面配置的log失效时间（log.retention.hours=168 ），到目录查看是否有过期的消息如果有，删除</span>
log<span class="hljs-preprocessor">.cleaner</span><span class="hljs-preprocessor">.enable</span>=false                       <span class="hljs-preprocessor">#是否启用log压缩，一般不用启用，启用的话可以提高性能</span>
zookeeper<span class="hljs-preprocessor">.connect</span>=<span class="hljs-number">10.181</span><span class="hljs-number">.65</span><span class="hljs-number">.180</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">0.181</span><span class="hljs-number">.65</span><span class="hljs-number">.181</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">0.181</span><span class="hljs-number">.65</span><span class="hljs-number">.182</span>:<span class="hljs-number">2181</span>             <span class="hljs-preprocessor">#设置zookeeper的连接端口</span></code></pre>

<p>具体的参数详细信息可以参考<a href="http://orchome.com/12" rel="nofollow" target="_blank">Kafka Broker配置</a></p>

<p>这只是其中一个broker的配置，其余broker配置一样只需要修改相应的broker.id和host.name就行啦</p>



<h2 id="三kafka常用命令">三、Kafka常用命令</h2>

<ul>
<li>启动服务</li>
</ul>



<pre class="prettyprint"><code class=" hljs axapta">bin/kafka-<span class="hljs-keyword">server</span>-start.sh config/<span class="hljs-keyword">server</span>.properties  <span class="hljs-preprocessor">#普通启动</span>
bin/kafka-<span class="hljs-keyword">server</span>-start.sh -daemon config/<span class="hljs-keyword">server</span>.properties <span class="hljs-preprocessor">#从后台启动Kafka集群（3台都需要启动）</span></code></pre>

<ul>
<li>新建主题</li>
</ul>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">create</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">181</span><span class="hljs-string">.</span><span class="hljs-comment">65</span><span class="hljs-string">.</span><span class="hljs-comment">180:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">replication</span><span class="hljs-literal">-</span><span class="hljs-comment">factor</span> <span class="hljs-comment">3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">partitions</span> <span class="hljs-comment">3</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">my</span><span class="hljs-literal">-</span><span class="hljs-comment">replicated</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span></code></pre>

<ul>
<li>查看主题信息</li>
</ul>



<pre class="prettyprint"><code class=" hljs brainfuck"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">10</span><span class="hljs-string">.</span><span class="hljs-comment">181</span><span class="hljs-string">.</span><span class="hljs-comment">65</span><span class="hljs-string">.</span><span class="hljs-comment">180:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">my</span><span class="hljs-literal">-</span><span class="hljs-comment">replicated</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span></code></pre>

<ul>
<li>查看主题列表</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> <span class="hljs-number">10.181</span><span class="hljs-number">.65</span><span class="hljs-number">.180</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic my<span class="hljs-attribute">-replicated</span><span class="hljs-attribute">-topic</span></code></pre>

<ul>
<li>启动生产者</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso"> bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-producer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>broker<span class="hljs-attribute">-list</span> <span class="hljs-number">10.181</span><span class="hljs-number">.65</span><span class="hljs-number">.180</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>topic my<span class="hljs-attribute">-replicated</span><span class="hljs-attribute">-topic</span></code></pre>

<ul>
<li>启动消费者</li>
</ul>



<pre class="prettyprint"><code class=" hljs lasso">bin/kafka<span class="hljs-attribute">-console</span><span class="hljs-attribute">-consumer</span><span class="hljs-built_in">.</span>sh <span class="hljs-subst">--</span>bootstrap<span class="hljs-attribute">-server</span> <span class="hljs-number">10.181</span><span class="hljs-number">.65</span><span class="hljs-number">.180</span>:<span class="hljs-number">9092</span> <span class="hljs-subst">--</span>from<span class="hljs-attribute">-beginning</span> <span class="hljs-subst">--</span>topic my<span class="hljs-attribute">-replicated</span><span class="hljs-attribute">-topic</span></code></pre>



<h2 id="四kafka监控软件">四、Kafka监控软件</h2>



<h3 id="1kafkamonitor">1.KafkaMonitor</h3>

<blockquote>
  <p>KafkaOffsetMonitor是一个可以用于监控Kafka的Topic及Consumer消费状况的工具，其配置和使用特别的方便,<a href="https://github.com/quantifind/KafkaOffsetMonitor" rel="nofollow" target="_blank">源项目Github地址</a>.</p>
</blockquote>

<p>最简单的使用方式是从Github上下载一个最新的<a href="https://github.com/quantifind/KafkaOffsetMonitor/releases/download/v0.2.1/KafkaOffsetMonitor-assembly-0.2.1.jar" rel="nofollow" target="_blank">KafkaOffsetMonitor-assembly-0.2.1.jar</a>， <br>
上传到某服务器上，然后执行一句命令就可以运行起来。</p>

<p>KafkaOffsetMonitor的使用 <br>
因为完全没有安装配置的过程，所以直接从KafkaOffsetMonitor的使用开始。 <br>
将KafkaOffsetMonitor-assembly-0.2.0.jar上传到服务器后，可以新建一个脚本用于启动该应用。脚本内容如下:</p>



<pre class="prettyprint"><code class=" hljs haml">java -cp KafkaOffsetMonitor-assembly-0.2.0.jar \
    com.quantifind.kafka.offsetapp.OffsetGetterWeb \
    -<span class="ruby">-zk <span class="hljs-number">10.181</span>.<span class="hljs-number">65.180</span><span class="hljs-symbol">:</span><span class="hljs-number">2181</span>,<span class="hljs-number">10.181</span>.<span class="hljs-number">65.181</span><span class="hljs-symbol">:</span><span class="hljs-number">2181</span>,<span class="hljs-number">10.181</span>.<span class="hljs-number">65.182</span><span class="hljs-symbol">:</span><span class="hljs-number">2181</span> \
</span>    -<span class="ruby">-port <span class="hljs-number">8080</span> \
</span>    -<span class="ruby">-refresh <span class="hljs-number">10</span>.seconds \
</span>    -<span class="ruby">-retain <span class="hljs-number">2</span>.days</span></code></pre>

<p>各参数的作用可以参考一下Github上的描述： <br>
* offsetStorage:  valid options are ”zookeeper”, ”kafka” or ”storm”. Anything else falls back to ”zookeeper” <br>
* zk:  the ZooKeeper hosts <br>
* port: on what port will the app be available <br>
* refresh:  how often should the app refresh and store a point in the DB <br>
* retain:  how long should points be kept in the DB <br>
* dbName:  where to store the history (default ‘offsetapp’) <br>
* kafkaOffsetForceFromStart:  only applies to ”kafka” format. Force KafkaOffsetMonitor to scan the commit messages from start (see notes below) <br>
* stormZKOffsetBase:  only applies to ”storm” format. Change the offset storage base in zookeeper, default to ”/stormconsumers” (see notes below) <br>
* pluginsArgs:  additional arguments used by extensions (see below)</p>

<p>然后访问8080端口即可</p>



<h3 id="2kafka-manager">2.kafka Manager</h3>



<h4 id="运行时环境要求">运行时环境要求</h4>

<ol>
<li>Kafka 0.8.1.1+</li>
<li>Java 7+</li>
</ol>



<h4 id="功能">功能</h4>

<p>为了简化开发者和服务工程师维护Kafka集群的工作，yahoo构建了一个叫做Kafka管理器的基于Web工具， <br>
叫做 Kafka Manager（<a href="https://github.com/yahoo/kafka-manager" rel="nofollow" target="_blank">GitHub源码地址</a>）。这个管理工具可以很容易地发现分布在集群中的哪些topic分布不均匀， <br>
或者是分区在整个集群分布不均匀的的情况。它支持管理多个集群、选择副本、副本重新分配以及创建Topic。 <br>
同时，这个管理工具也是一个非常好的可以快速浏览这个集群的工具，有如下功能： <br>
1. 管理多个kafka集群 <br>
2. 便捷的检查kafka集群状态(topics,brokers,备份分布情况,分区分布情况) <br>
3. 选择你要运行的副本 <br>
4. 基于当前分区状况进行 <br>
5. 可以选择topic配置并创建topic(0.8.1.1和0.8.2的配置不同) <br>
6. 删除topic(只支持0.8.2以上的版本并且要在broker配置中设置delete.topic.enable=true) <br>
7. Topic list会指明哪些topic被删除（在0.8.2以上版本适用） <br>
8. 为已存在的topic增加分区 <br>
9. 为已存在的topic更新配置 <br>
10. 在多个topic上批量重分区 <br>
11. 在多个topic上批量重分区(可选partition broker位置)</p>



<h4 id="安装配置启动">安装,配置,启动</h4>

<p><a href="https://github.com/yahoo/kafka-manager/releases/tag/1.3.3.13" rel="nofollow" target="_blank">下载zip</a>文件，解压后在conf/application.conf中将 <br>
kafka-manager.zkhosts的值设置为你的zk地址</p>



<pre class="prettyprint"><code class=" hljs fix"><span class="hljs-attribute">kafka-manager.zkhosts</span>=<span class="hljs-string">10.181.65.180:2181,10.181.65.181:2181,10.181.65.182:2181</span></code></pre>

<p>启动,指定配置文件位置和启动端口号，默认为9000</p>



<pre class="prettyprint"><code class=" hljs haml">nohup bin/kafka-manager
-<span class="ruby"><span class="hljs-constant">Dconfig</span>.file=conf/application.conf
</span> -<span class="ruby"><span class="hljs-constant">Dhttp</span>.port=<span class="hljs-number">8081</span> &amp;</span></code></pre>

<p>然后访问8081端口即可</p>



<h2 id="五测试">五、测试</h2>



<h2 id="六参考文档">六、参考文档</h2>

<ul>
<li><a href="http://kafka.apache.org/quickstart" rel="nofollow" target="_blank">kafka官方文档</a></li>
<li><a href="http://orchome.com/" rel="nofollow" target="_blank">OrcHome</a></li>
<li><a href="https://github.com/apache/kafka/" rel="nofollow" target="_blank">kafka源码</a></li>
<li><a href="http://www.cnblogs.com/huxi2b/tag/Kafka/" rel="nofollow" target="_blank">胡夕博客</a></li>
<li><a href="http://jm.taobao.org/" rel="nofollow" target="_blank">阿里中间件团队博客</a></li>
<li><a href="https://www.confluent.io/" rel="nofollow" target="_blank">confluent</a></li>
</ul>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
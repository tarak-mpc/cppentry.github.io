---
layout:     post
title:      一台Linux主机搭建kafka集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<h2>一、搭建Zookeeper集群</h2>
<div><span style="font-size:14px;">具体看：<a href="http://blog.csdn.net/jeffleo/article/details/75735136" rel="nofollow">http://blog.csdn.net/jeffleo/article/details/75735136</a></span></div>
<h2>二、kafka broker集群搭建</h2>
<ul><li>
<h5><span style="font-size:14px;">kafka 下载、解压</span></h5>
<pre class="hljs x86asm"><code class="x86asm"><span style="font-size:14px;">wget http://apache.opencas.org/kafka/<span class="hljs-number">0.9</span><span class="hljs-meta">.0</span><span class="hljs-meta">.1</span>/kafka_2<span class="hljs-meta">.11</span>-<span class="hljs-number">0.9</span><span class="hljs-meta">.0</span><span class="hljs-meta">.1</span>.tgz
tar -xzf kafka_2<span class="hljs-meta">.11</span>-<span class="hljs-number">0.9</span><span class="hljs-meta">.0</span><span class="hljs-meta">.0</span>.tgz</span></code></pre>
</li><li>
<h5><span style="font-size:14px;">kafka broker配置文件修改</span></h5>
<span style="font-size:14px;">修改kafka根目录下的config/server.properties</span>
<pre class="hljs x86asm"><code class="x86asm"><span style="font-size:14px;">broker.id=<span class="hljs-number">0</span> #整个集群内唯一id号，整数，一般从<span class="hljs-number">0</span>开始
listeners=PLAINTEXT://<span class="hljs-number">192.168</span><span class="hljs-meta">.6</span><span class="hljs-meta">.56</span>:<span class="hljs-number">9092</span> #协议、当前broker机器<span class="hljs-built_in">ip</span>、端口，此值可以配置多个，应该跟SSL等有关系，更多用法尚未弄懂，这里修改为<span class="hljs-built_in">ip</span>和端口。
port=<span class="hljs-number">9092</span> #broker端口
host.name=<span class="hljs-number">192.168</span><span class="hljs-meta">.6</span><span class="hljs-meta">.56</span> #broker 机器<span class="hljs-built_in">ip</span>
log.dirs=/zyxx_data/kafka-logs/kafka00 #kafka存储数据的目录
zookeeper.connect=<span class="hljs-number">192.168</span><span class="hljs-meta">.6</span><span class="hljs-meta">.56</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">192.168</span><span class="hljs-meta">.6</span><span class="hljs-meta">.56</span>:<span class="hljs-number">2182</span>,<span class="hljs-number">192.168</span><span class="hljs-meta">.6</span><span class="hljs-meta">.56</span>:<span class="hljs-number">2183</span> #zookeeper 集群列表</span></code></pre>
</li><li>
<h5><span style="font-size:14px;">kafka broker多节点配置</span></h5>
<p><span style="font-size:14px;">kafka多节点配置，可以像zookeeper一样把软件目录copy多份，修改各自的配置文件。这里介绍另外一种方式：同一个软件目录程序，但使用不同的配置文件启动</span></p>
<blockquote>
<p><span style="font-size:14px;">使用不同的配置文件启动多个broker节点，这种方式只适合一台机器下的伪集群搭建，在多台机器的真正集群就没有意义了</span></p>
</blockquote>
</li><li>
<h5><span style="font-size:14px;">把kafka根目录下的config/server.properties copy两份，名称分别修改为：server-1.properties 、server-2.properties。各自修改他们的配置如下：</span></h5>
<p><em><span style="font-size:14px;">config/server-1.properties</span></em></p>
<pre class="hljs routeros"><code class="routeros"><span style="font-size:14px;">broker.<span class="hljs-attribute">id</span>=1 
<span class="hljs-attribute">listeners</span>=PLAINTEXT://192.168.6.56:9093
<span class="hljs-attribute">port</span>=9093 
host.<span class="hljs-attribute">name</span>=192.168.6.56
log.<span class="hljs-attribute">dirs</span>=/zyxx_data/kafka-logs/kafka01 
zookeeper.<span class="hljs-attribute">connect</span>=192.168.6.56:2181,192.168.6.56:2182,192.168.6.56:2183</span></code></pre>
<p><em><span style="font-size:14px;">config/server-2.properties</span></em></p>
<pre class="hljs routeros"><code class="routeros"><span style="font-size:14px;">broker.<span class="hljs-attribute">id</span>=2 
<span class="hljs-attribute">listeners</span>=PLAINTEXT://192.168.6.56:9094
<span class="hljs-attribute">port</span>=9094 
host.<span class="hljs-attribute">name</span>=192.168.6.56
log.<span class="hljs-attribute">dirs</span>=/zyxx_data/kafka-logs/kafka02 
zookeeper.<span class="hljs-attribute">connect</span>=192.168.6.56:2181,192.168.6.56:2182,192.168.6.56:2183</span></code></pre>
</li></ul><h2><span style="font-size:14px;">集群启动</span></h2>
<ul><li>
<h5><span style="font-size:14px;">zookeeper集群启动</span></h5>
<p><span style="font-size:14px;">进入到/var/zookeeper/目录，执行</span></p>
<pre class="hljs stylus"><code class="stylus"><span style="font-size:14px;">zookeeper00/bin/zkServer<span class="hljs-selector-class">.sh</span> start
zookeeper01/bin/zkServer<span class="hljs-selector-class">.sh</span> start
zookeeper02/bin/zkServer<span class="hljs-selector-class">.sh</span> start</span></code></pre>
<p><span style="font-size:14px;">我们可以通过以下命令查看zookeeper是否已启动</span></p>
<pre class="hljs vim"><code class="vim"><span style="font-size:14px;"><span class="hljs-keyword">ps</span> -ef | <span class="hljs-keyword">grep</span> zoo.cfg</span></code></pre>
<div class="image-package"><span style="font-size:14px;"><img src="http://upload-images.jianshu.io/upload_images/1748661-a6326074016a8f15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt=""><br></span>
<div class="image-caption"><span style="font-size:14px;">查看zookeeper进程信息</span></div>
</div>
</li><li>
<h5><span style="font-size:14px;">kafka 集群启动</span></h5>
<p><span style="font-size:14px;">进入到kafka目录，执行</span></p>
<pre class="hljs stylus"><code class="stylus"><span style="font-size:14px;">bin/kafka-server-start<span class="hljs-selector-class">.sh</span> -daemon config/server<span class="hljs-selector-class">.properties</span>
bin/kafka-server-start<span class="hljs-selector-class">.sh</span> -daemon config/server-<span class="hljs-number">1</span><span class="hljs-selector-class">.properties</span>
bin/kafka-server-start<span class="hljs-selector-class">.sh</span> -daemon config/server-<span class="hljs-number">2</span>.properties</span></code></pre>
<blockquote>
<p><span style="font-size:14px;">"-daemon" 参数代表以守护进程的方式启动kafka server。<br>
官网及网上大多给的启动命令是没有"-daemon"参数，如：“bin/kafka-server-start.sh config/server.properties &amp;”，但是这种方式启动后，如果用户退出的ssh连接，进程就<strong>有可能</strong>结束，具体不清楚为什么。</span></p>
</blockquote>
<p><span style="font-size:14px;">我们可以通过以下命令查看kafka server是否已启动</span></p>
<pre class="hljs vim"><code class="vim"><span style="font-size:14px;"><span class="hljs-keyword">ps</span> -ef | <span class="hljs-keyword">grep</span> config/server</span></code></pre>
<div class="image-package"><span style="font-size:14px;"><img src="http://upload-images.jianshu.io/upload_images/1748661-94ccd578b8b6f6a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt=""><br></span>
<div class="image-caption"><span style="font-size:14px;">查看kafka server进程信息</span></div>
</div>
</li></ul><h2><span style="font-size:14px;">测试</span></h2>
<p><span style="font-size:14px;">为了简单起见，这里通过命令行的方式启动生产者和消费者进行测试。</span></p>
<ul><li>
<h5><span style="font-size:14px;">创建主题</span></h5>
<p><span style="font-size:14px;">进入到kafka目录，创建“test5”topic主题：分区为3、备份为3的</span></p>
<pre class="hljs x86asm"><code class="x86asm"><span style="font-size:14px;">bin/kafka-topics.sh --create --zookeeper <span class="hljs-number">192.168</span><span class="hljs-meta">.6</span><span class="hljs-meta">.56</span>:<span class="hljs-number">2181</span>,<span class="hljs-number">192.168</span><span class="hljs-meta">.6</span><span class="hljs-meta">.56</span>:<span class="hljs-number">2182</span>,<span class="hljs-number">192.168</span><span class="hljs-meta">.6</span><span class="hljs-meta">.56</span>:<span class="hljs-number">2183</span> --replication-factor <span class="hljs-number">3</span> --partitions <span class="hljs-number">3</span> --topic test5</span></code></pre>
<blockquote>
<p><span style="font-size:14px;"><strong>--zookeeper</strong> : <em>zookeeper集群列表，用英文逗号分隔。可以不用指定zookeeper整个集群内的节点列表，只指定某个或某几个zookeeper节点列表也是你可以的</em><br><strong>replication-factor</strong> ： <em>复制数目，提供failover机制；1代表只在一个broker上有数据记录，一般值都大于1，代表一份数据会自动同步到其他的多个broker，防止某个broker宕机后数据丢失。</em><br><strong>partitions</strong> ： <em>一个topic可以被切分成多个partitions，一个消费者可以消费多个partitions，但一个partitions只能被一个消费者消费，所以增加partitions可以增加消费者的吞吐量。kafka只保证一个partitions内的消息是有序的，多个一个partitions之间的数据是无序的。</em></span></p>
</blockquote>
<p><span style="font-size:14px;">查看"test5"主题详情</span></p>
<pre class="hljs brainfuck"><code class="brainfuck"><span style="font-size:14px;"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">topics</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">describe</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span>  <span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">6</span><span class="hljs-string">.</span><span class="hljs-comment">56:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test5</span></span></code></pre>
<div class="image-package"><span style="font-size:14px;"><img src="http://upload-images.jianshu.io/upload_images/1748661-5173e898914c2c27.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt=""><br></span>
<div class="image-caption"><span style="font-size:14px;">test5 topic详情</span></div>
</div>
</li><li>
<h5><span style="font-size:14px;">启动生产者、消费者</span></h5>
<p><span style="font-size:14px;">启动生产者</span></p>
<pre class="hljs armasm"><code class="armasm"><span style="font-size:14px;"><span class="hljs-keyword">bin/kafka-console-producer.sh </span>--<span class="hljs-keyword">broker-list </span><span class="hljs-number">192</span>.<span class="hljs-number">168</span>.<span class="hljs-number">6</span>.<span class="hljs-number">56</span>:<span class="hljs-number">9092</span> --topic test5</span></code></pre>
<blockquote>
<p><span style="font-size:14px;"><strong>--broker-list</strong> : <em>值可以为broker集群中的一个或多个节点</em></span></p>
</blockquote>
<p><span style="font-size:14px;">启动消费者（在新的ssh连接窗口）</span></p>
<pre class="hljs brainfuck"><code class="brainfuck"><span style="font-size:14px;"><span class="hljs-comment">bin/kafka</span><span class="hljs-literal">-</span><span class="hljs-comment">console</span><span class="hljs-literal">-</span><span class="hljs-comment">consumer</span><span class="hljs-string">.</span><span class="hljs-comment">sh</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">zookeeper</span> <span class="hljs-comment">192</span><span class="hljs-string">.</span><span class="hljs-comment">168</span><span class="hljs-string">.</span><span class="hljs-comment">6</span><span class="hljs-string">.</span><span class="hljs-comment">56:2181</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">topic</span> <span class="hljs-comment">test5</span> <span class="hljs-literal">-</span><span class="hljs-literal">-</span><span class="hljs-comment">from</span><span class="hljs-literal">-</span><span class="hljs-comment">beginning</span></span></code></pre>
<blockquote>
<p><span style="font-size:14px;"><strong>--zookeeper</strong> : <em>值可以为zookeeper集群中的一个或多个节点</em></span></p>
</blockquote>
<p><span style="font-size:14px;">我们可以发现，在生产者下输入一个消息，回车后可以在消费者下输出，说明我们集群搭建的没问题<br></span></p>
<div class="image-package"><span style="font-size:14px;"><img src="http://upload-images.jianshu.io/upload_images/1748661-5ef13b98e6f3bcc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt=""><br></span>
<div class="image-caption"><span style="font-size:14px;">生产者和消费者</span></div>
</div>
</li></ul><ul><li>
<h5><span style="font-size:14px;">模拟kafka broker集群某个节点宕机</span></h5>
<p><span style="font-size:14px;">我们直接通过kill 进程来模拟<br></span></p>
<div class="image-package"><span style="font-size:14px;"><img src="http://upload-images.jianshu.io/upload_images/1748661-db71eb1e22c27575.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt=""><br></span>
<div class="image-caption"></div>
</div>
<p><span style="font-size:14px;">再观察 生产者和消费者</span></p>
<div class="image-package"><span style="font-size:14px;"><img src="http://upload-images.jianshu.io/upload_images/1748661-20e6d0d9cd012341.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt=""><br></span>
<div class="image-caption"><span style="font-size:14px;">某个broker节点宕机后的生产者消费者</span></div>
</div>
<p><span style="font-size:14px;">我们可以发现，消费者出现了警告信息，但是依旧可以消费数据。</span></p>
</li><li>
<h5><span style="font-size:14px;">模拟zookeeper集群某个节点宕机</span></h5>
<p><span style="font-size:14px;">我们直接通过kill 进程来模拟</span></p>
<div class="image-package"><span style="font-size:14px;"><img src="http://upload-images.jianshu.io/upload_images/1748661-e7b9b8fcc24dc3b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt=""><br></span>
<div class="image-caption"></div>
</div>
<p><span style="font-size:14px;">再观察 生产者和消费者<br></span></p>
<div class="image-package"><span style="font-size:14px;"><img src="http://upload-images.jianshu.io/upload_images/1748661-7d310095cff29194.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240" alt=""><br></span>
<div class="image-caption"><span style="font-size:14px;">某个zookeeper节点宕机后的生产者和消费</span></div>
</div>
<p><span style="font-size:14px;">我们可以发现，消费者依旧可以消费数据。<br>
这就说明我们整个集群环境的搭建已经成功。</span></p>
<blockquote>
<p><span style="font-size:14px;">注意一点，你启动消费者时在命令中只指定了一个zookeeper节点，且模拟zookeeper某个节点宕机时，你kill掉的正好又是这个节点。这种情况下你的消费者会不停的报警告信息，但是不影响消费信息。</span></p>
</blockquote>
</li></ul><div><span style="font-size:14px;">转自：http://www.jianshu.com/p/dc4770fc34b6</span></div>
</div>
            </div>
                </div>
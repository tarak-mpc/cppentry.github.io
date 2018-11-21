---
layout:     post
title:      使用自带Zookeeper搭建kafka集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>简要说明</h2>
<p><span></span>新版本的kafka自带有zookeeper，其实自带的zookeeper完全够用，本篇文章以记录使用自带zookeeper搭建kafka集群。</p>
<h2>1、关于kafka</h2>
<div><span></span>kafka官网：http://kafka.apache.org/</div>
<div><span></span>kafka下载页面：http://kafka.apache.org/downloads</div>
<div><span></span>kafka配置快速入门：http://kafka.apache.org/quickstart</div>
<h2>2、修改kafka配置文件</h2>
<div><span></span>kafka安装目录下的config文件夹为其配置文件，我们需要修改的有 server.properties和zookeeper.properties。</div>
<div><span></span></div>
<h3><span></span>2.1、修改日志目录</h3>
<div><span></span>首先修改kafka的日志目录和zookeeper数据目录，因为这两项默认放在tmp目录，而tmp目录中内容会随重启而丢失：</div>
<div><span></span></div>
server.properties：<br>
    log.dirs=/tmp/kafka-logs<br>
    修改为<br>
    log.dirs=/usr/local/kafka/logs<br><br>
zookeeper.properties<br>
    dataDir=/tmp/zookeeper<br>
    修改为<br>
    dataDir=/usr/local/zookeeper/data
<div><span></span></div>
<div><span></span></div>
<h3><span></span>2.2、配置kafka</h3>
<div><span></span>kafka集群为便于推选<span></span>leader，一般由奇数台服务组件集群，这里以三台为例，分别已xxx.xxx.xxx.xxA、xxx.xxx.xxx.xxB和xxx.xxx.xxx.xxC代表服务的ip。</div>
<div><span></span>（服务器IP可使用ifconfig命令查看）</div>
<div><span></span></div>
<div><br></div>
<div><span></span>修改server.properties:</div>
<div><span></span>1、设置broker.id，保证每个broker唯一，第一台可以不修改默认为0，后面两台需要修改，如改为1和2</div>
<div><span></span>2、设置num.partitions，分区数量一般与broker保持一致</div>
<div><span></span>3、设置advertised.listeners和listeners，如listeners=PLAINTEXT://xxx.xxx.xxx.xx:9092</div>
<div><span></span>4、设置zookeeper.connect，配置三台服务zookeeper连接地址，如zookeeper.connect=xxx.xxx.xxx.xxA:2181,xxx.xxx.xxx.xxB:2181,xxx.xxx.xxx.xxC:2181</div>
<div><span></span></div>
<div><br></div>
<div><span></span>修改zookeeper.properties：</div>
<div><span></span>1、设置连接参数，添加如下配置</div>
<div></div>
<div>        tickTime=2000<br>
        initLimit=10<br>
        syncLimit=5<br></div>
<div><span><br></span></div>
<div></div>
<div></div>
<div><span></span>2、设置broker Id的服务地址</div>
<div><span></span><span></span>        server.0=xxx.xxx.xxx.xxA:2888:3888<br><span></span><span></span>        server.1=xxx.xxx.xxx.xxB:2888:3888<br><span></span><span></span>        server.2=xxx.xxx.xxx.xxC:2888:3888<br></div>
<div><span></span></div>
<div><br></div>
<div><span></span>zookeeper数据目录添加id配置</div>
<div><span></span>在各台服务的zookeeper数据目录添加myid文件，写入服务broker.id属性值，如这里的目录是<span>/usr/local/zookeeper/data</span></div>
<div><span></span>第一台broker.id为0的服务到该目录下执行：echo 0 &gt; myid</div>
<div><br></div>
<h2>3、启动kafka</h2>
<div><span></span>kafka启动时先启动zookeeper，再启动kafka；关闭时相反，先关闭kafka，再关闭zookeeper</div>
<div><span></span></div>
<div>启动zookeeper：<br>
        bin/zookeeper-server-start.sh config/zookeeper.properties &amp;<br>
启动kafka：<br>
        bin/kafka-server-start.sh config/server.properties &amp;<br></div>
<div><span><br></span></div>
<h2><span>4、测试kafka集群</span></h2>
<h5><span>4.1、在第一台服务上创建test主题</span></h5>
<div><span><span>bin/kafka-topics.sh --create --topic test --zookeeper xxx.xxx.xxx.xxA:2181 --replication-factor 3 --partition 3</span></span></div>
<h5><span><span>4.2、展示主题，确认主题创建成功</span></span></h5>
<div><span><span><span>bin/kafka-topics.sh --list --zookeeper</span>xxx.xxx.xxx.xxA<span>:2181</span></span></span></div>
<h5><span>4.3、创建生产者</span></h5>
<div><span><span>bin/kafka-console-producer.sh --broker-list</span>xxx.xxx.xxx.xxA<span>:</span>9092<span> --topic test</span></span></div>
<h5>4.4、创建消费者，再另外两台服务上分别创建消费者</h5>
<div>bin/kafka-console-consumer.sh --zookeeper xxx.xxx.xxx.xxB:2181 --topic test --from-beginning<br>
bin/kafka-console-consumer.sh --zookeeper xxx.xxx.xxx.xxC:2181 --topic test --from-beginning<br></div>
<h5><span>4.5、测试消息发布与消费</span></h5>
<div><span></span>在IP为xxx.xxx.xxx.xxA的服务生产中输入消息回车发送，确认在服务B和C中的消费者是否接收到</div>
<h2>5、其他</h2>
<div><span></span>如配置完成后需要修改端口或其他配置，但是没有生效，启动报错，可以尝试清楚kafka（<span>/usr/local/kafka/logs</span>）和zookeeper（<span>/usr/local/zookeeper/data</span>）缓存目录的内容然后重新启动。</div>
<div><br></div>
<div><br></div>
<div><br></div>
            </div>
                </div>
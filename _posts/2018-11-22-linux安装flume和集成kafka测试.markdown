---
layout:     post
title:      linux安装flume和集成kafka测试
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明原地址					https://blog.csdn.net/a123demi/article/details/72637847				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">一.前期准备</span></h1>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">1.1 kafka+zookeeper集群环境以安装</span></h3>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">1.2 下载flume</span></h3>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">本文使用flume1.7</span></div>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">下载地址:<a href="http://flume.apache.org/download.html" rel="nofollow">http://flume.apache.org/download.html</a></span></div>
<h1 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">二.配置flume</span></h1>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">2.1 上传flume</span></h3>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">#上传下载包至/opt/software
cd /opt/software
rz apache-flume-1.7.0-bin.tar.gz
#解压
tar -zxvf apache-flume-1.7.0-bin.tar.gz
#复制apache-flume-1.7.0-bin至/usr/local
cp -r apache-flume-1.7.0-bin flume</code></pre></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">2.2 配置环境变量</span></h3>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">vi /etc/profile
export FLUME_HOME=/usr/local/flume
export path=$PATH:$FLUME_HOME/bin
保存退出
重新编译profile
source /etc/profile</code></pre></div>
<div style="text-indent:28px;line-height:1.75;">
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523112247949?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">2.3 配置flume-env</span></h3>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">#添加jdk路径
cd /usr/local/flume/conf
cp -r flume-env.sh.template flume-env.sh
vi flume-env.sh
export JAVA_HOME=/usr/local/jdk</code></pre></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523112259071?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<h1 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">三.测试flume+kafka</span></h1>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">kafka接受flume监控数据</span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.1 配置flume-conf</span></h3>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">cp -r flume-conf.properties.template flume-conf.properties</code></pre></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523112302590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">vi flume-conf.properties</code></pre></div>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">#配置文件信息
agent.sources = s1 
agent.channels = c1 
agent.sinks = k1 

agent.sources.s1.type=exec
agent.sources.s1.command=tail -F /opt/software/abc.log
agent.sources.s1.channels=c1

agent.channels.c1.type=memory
agent.channels.c1.capacity=10000
agent.channels.c1.transactionCapacity=100

#设置Kafka接收器
agent.sinks.k1.type= org.apache.flume.sink.kafka.KafkaSink
#设置Kafka的broker地址和端口号
agent.sinks.k1.brokerList=192.168.32.128:9092,192.168.32.131:9092,192.168.32.132:9092
#设置Kafka的Topic
agent.sinks.k1.topic=flumeTest
#设置序列化方式
agent.sinks.k1.serializer.class=kafka.serializer.StringEncoder

agent.sinks.k1.channel=c1</code></pre></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.2 编写简单Shell脚本abc.sh，并修改权限为可执行权限</span></h3>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">#定位
cd /opt/software
#新建abc.sh
vi abc.sh 
   for((i=0;i&lt;=50000;i++));
	do echo "test-"+$i&gt;&gt;abc.log;
	done</code></pre><pre><code class="language-html">chmod 755 abc.sh
</code></pre></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.3 启动zookeeper和kafka</span></h3>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">详情见：<a href="http://blog.csdn.net/a123demi/article/details/70279296" rel="nofollow">http://blog.csdn.net/a123demi/article/details/70279296</a></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.4 kafka消费者监听flumeTest主题</span></h3>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">bin/kafka-console-consumer.sh --zookeeper 192.168.32.128:2181,192.168.32.131:2181,192.168.32.132:2181 --topic flumeTest --from-beginning</code></pre></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523112305540?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.5 启动flume</span></h3>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">./bin/flume-ng agent -n agent -c conf -f conf/flume-conf.properties -Dflume.root.logger=INFO,console</code></pre></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><span style="color:#ff0000;">注意：</span>-n agent中要与flume-conf配置文件中agent对应</span></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523112308837?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.6 运行abc.sh</span></h3>
<div style="text-indent:28px;line-height:1.75;"><pre><code class="language-html">./abc.sh</code></pre></div>
<div style="text-indent:28px;line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523112312821?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<h3 style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">3.7 输出结果</span></h3>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;">kafka消费者接受数据</span></div>
<div style="line-height:1.75;"><span style="font-family:'Microsoft YaHei';font-size:18px;"><img src="https://img-blog.csdn.net/20170523112315696?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYTEyM2RlbWk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></span></div>
<div><span style="font-family:'Microsoft YaHei';font-size:18px;"></span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<br></div>
            </div>
                </div>
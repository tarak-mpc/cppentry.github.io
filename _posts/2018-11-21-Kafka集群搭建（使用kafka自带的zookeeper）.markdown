---
layout:     post
title:      Kafka集群搭建（使用kafka自带的zookeeper）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';">
<span style="font-size:24px;">Kafka集群搭建</span></p>
<p style="line-height:27.2px;font-family:'microsoft yahei';font-size:16px;">
<span style="color:#cc0000;">本文使用的zookeeper是kafka自带的，最好不要用kafka自带的，可以先搭建好zookeeper集群</span></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
1、 Kafka的安装需要java环境，cent os 7自带java1.6版本，可以不用重新安装，直接使用自带的jdk 即可；如果觉得jdk版本太旧，也可以自己重新安装；</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
2、 准备好kafka安装包，官网下载地址： <br><a href="http://kafka.apache.org/downloads.html" rel="nofollow" style="background:transparent;color:rgb(202,12,22);">http://kafka.apache.org/downloads.html</a></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
3、 下载好kafka安装包后，将其解压到/usr/local目录下，删除压缩包</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
4、 目前搭建了三个节点的kafka集群，分别在10.10.67.102，10.10.67.104和10.10.67.106服务器上；</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
5、 查看配置文件 <br>
进入kafka的config的目录：</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20170518163541764?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenh5OTg3ODcyNjc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
6、 先建立zk集群，直接使用kafka自带的zookeeper建立zk集群，修改zookeeper.properties文件：</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20170518163645089?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenh5OTg3ODcyNjc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
三个机器上的zookeeper.properties文件配置相同，需要注意的是日志保存的路径，不会自动生成，需要自己手动建立相关的路径， dataLogDir是我自己加的，日志文件太多，把日志文件区分开；</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
7、 创建myid文件，进入/usr/local/kafka/zookeeper，创建myid文件，将三个服务器上的myid文件分别写入1，2，3，如图：</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20170518163911232?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenh5OTg3ODcyNjc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"> <br>
—-myid是zk集群用来发现彼此的标识，必须创建，且不能相同；</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
8、 进入kafka目录 执行启动zookeeper命令： <br>
./bin/zookeeper-server-start.sh config/zookeeper.properties &amp; <br>
三台机器都执行启动命令，查看zookeeper的日志文件，没有报错就说明zookeeper集群启动成功了。</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
9、 搭建kafka集群，修改server.properties配置文件： <br><img src="https://img-blog.csdn.net/20170518164309410?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenh5OTg3ODcyNjc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
<img src="https://img-blog.csdn.net/20170518164322254?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvenh5OTg3ODcyNjc0/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title="" style="border:0px;vertical-align:middle;"></p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
server.properties配置文件的修改主要在开头和结尾，中间保持默认配置即可；需要注意的点是broker.id的值三个节点要配置不同的值，分别配置为0，1，2；log.dirs必须保证目录存在，不会根据配置文件自动生成；</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';font-size:16px;">
10、 启动kafka集群，进入kafka目录，执行如下命令 ： <br>
./bin/kafka-server-start.sh –daemon config/server.properties &amp; <br>
三个节点均要启动；启动无报错，即搭建成功，可以生产和消费消息，来检测是否搭建成功。</p>
<p style="line-height:27.2px;color:rgb(63,63,63);font-family:'microsoft yahei';">
</p>
<div style="color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:1.5;">
<div style="line-height:1.5;"><span><span><span style="font-size:24px;"><br></span></span></span></div>
<div style="line-height:1.5;"><span><span><span style="font-size:24px;">测试kafka集群</span></span></span></div>
<div style="line-height:1.5;">（此处使用的zookeeper ip不同上所示，改成自己创建的即可）</div>
<div style="font-size:14px;line-height:1.5;"><span style="font-size:16px;">1-进入kafka根目录，创建topic--test</span></div>
<div style="font-size:14px;line-height:1.5;"><span style="font-size:16px;">bin/kafka-topics.sh --create --zookeeper 192.168.1.xx:2181,192.168.1.xx:2181,192.168.1.xx:2181 --replication-factor 1 --partitions 1
 --topic test</span></div>
<img src="http://blog.csdn.net/gongxinju/article/details/53415051" alt="" style="font-size:14px;border:0px;vertical-align:middle;"><span style="font-size:14px;"><img src="https://img-blog.csdn.net/20161130230805438?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;"></span><span style="font-size:14px;"><br></span></div>
<div style="font-size:14px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:1.5;">
<span style="font-size:16px;">2-列出已创建的topic列表</span></div>
<div style="font-size:14px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:1.5;">
<div style="line-height:1.5;"><span style="font-size:16px;">bin/kafka-topics.sh --list --zookeeper localhost:2181</span></div>
<div style="line-height:1.5;"><span style="font-size:16px;">3-模拟客户端去发送消息</span></div>
<div style="line-height:1.5;"><span style="font-size:16px;">bin/kafka-console-producer.sh --broker-list 192.168.1.xx:9092,192.168.1.xx:9092,192.168.1.xx:9092 --topic test</span></div>
<img src="http://blog.csdn.net/gongxinju/article/details/53415051" alt="" style="border:0px;vertical-align:middle;"><img src="https://img-blog.csdn.net/20161130230823308?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;"><br></div>
<div style="font-size:14px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:1.5;">
<span style="font-size:16px;">4-模拟客户端去接受消息</span></div>
<div style="font-size:14px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:1.5;">
<div style="line-height:1.5;"><span style="font-size:16px;">bin/kafka-console-consumer.sh --zookeeper 192.168.1.xx:2181,192.168.1.xx:2181,192.168.1.xx:2181 --from-beginning --topic test</span></div>
<img src="http://blog.csdn.net/gongxinju/article/details/53415051" alt="" style="border:0px;vertical-align:middle;"><img src="https://img-blog.csdn.net/20161130230835746?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;"><br></div>
<div style="font-size:14px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:1.5;">
<span style="font-size:18px;"><span>7、自己测试结果</span></span></div>
<div style="font-size:14px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:1.5;">
<img src="http://blog.csdn.net/gongxinju/article/details/53415051" alt="" style="border:0px;vertical-align:middle;"><img src="https://img-blog.csdn.net/20161130230921262?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="border:0px;vertical-align:middle;"><br></div>
<div style="font-size:14px;color:rgb(69,69,69);font-family:'PingFang SC', 'Microsoft YaHei', SimHei, Arial, SimSun;line-height:1.5;">
<span style="background-color:rgb(255,255,0);color:rgb(255,0,0);font-size:16px;">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 3 --partitions 1 --topic test</span></div>
<br><p></p>
            </div>
                </div>
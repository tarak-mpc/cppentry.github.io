---
layout:     post
title:      Docker自定义network搭建kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>         昨天在搞spring-cloud-bus 想到用kafka实现消息总线，然后就用docker起了一个zookeeper和kafka,本人docker版本是17.06,采用机器是Ubuntu18版本，详细安装kafka的过程如下。</p>

<p>1.docker  search kafka  我们选择星星最多的 wurstmeister/kafka</p>

<p style="text-align:center;"><img alt="" class="has" src="https://img-blog.csdn.net/20180801092412710?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dwZHNqcXdz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>2. 下载zookeeper和kafka的镜像</p>

<p>docker pull wurstmeister/zookeeper</p>

<p>docker pull wurstmeister/kafka</p>

<p>3.两个镜像下载好以后，我们给它重命名，方便操作。</p>

<p>a)镜像重命名 docker tag [oldName]:[tag] [newName]:[tag]</p>

<p>docker tag wurstmeister/zookeeper:latest zookeeper latest</p>

<p>docker tag wurstmeister/kafka:latest kafka latest</p>

<p>b)镜像的映射tag 删除 docker rmi -f [Name]:[Tag]</p>

<p>这里我们删除旧版的镜像。</p>

<p>docker rmi -f  wurstmeister/zookeeper:latest</p>

<p>docker rmi -f  wurstmeister/kafka:latest</p>

<p>4.由于kafka要和zookeeper相互通信，这里我们自己搭建一个bridge网络</p>

<p>docker network create app</p>

<p>5.查看新建的网络</p>

<p>docker  network list</p>

<p style="text-align:center;"><img alt="" class="has" src="https://img-blog.csdn.net/20180801094224665?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dwZHNqcXdz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p></p>

<p> app是我们刚刚创建的network   其中bridge，host,none是默认的。</p>

<p>6.查看网络的详细信息</p>

<p>docker network inspect app</p>

<p style="text-align:center;"><img alt="" class="has" src="https://img-blog.csdn.net/20180801094658978?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2dwZHNqcXdz/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70"></p>

<p>其中Containers中有两个容器，zookeeper和kafka,这个是我搭建完毕以后加入的，新创建的名字叫app的network中的Containers为空 </p>

<p>7.启动zookeeper  --net=app 指定加入自定的app网络 -v挂在docker容器的zookeeper文件到 zoolog -t可以替换为-d 这样如果看log的话 还需要 docker logs zookeeper</p>

<p>docker run --net=app --name zookeeper -p 2181:2181 -v /home/qiwenshuai/kafka/zoolog:/opt/zookeeper/data -t zookeeper</p>

<p></p>

<p>8. 启动kafka 指定network网络， 注册到zookeeper， 注意加入同一网络的容器，自己嵌套一个DNS解析，可以解析到其他容器，并且端口全部相互暴露。</p>

<p>docker run --net=app --name kafka -p 9092:9092 \</p>

<p>--link zookeeper \</p>

<p>--env KAFKA_ZOOKEEPER_CONNECT=zookeeper:2181 \</p>

<p>--env KAFKA_ADVERTISED_HOST_NAME=127.0.0.1 \</p>

<p>--env KAFKA_ADVERTISED_PORT=9092 \</p>

<p>-t kafka</p>

<p>9.测试---进入kafka内部，自定义生产者和消费者进行测试</p>

<p>docker exec -it kafka /bin/bash</p>

<p></p>

<p> a)kafka在opt 目录下</p>

<p>cd /opt/kafka_2.12-0.11.0.1/</p>

<p>b)调用kafka-topics.sh创建一个mykafka的topic</p>

<p>bin/kafka-topics.sh --create --zookeeper zookeeper:2181 --replication-factor 1 --partitions 1 --topic mykafka</p>

<p>c)调用kafka-console-producer.sh运行一个生产者</p>

<p>bin/kafka-console-producer.sh --broker-list localhost:9092 --topic mykafka</p>

<p>d)启动另一个窗口，按照上边的步骤启动kafka容器的命令行，执行如下消费者命令</p>

<p>bin/kafka-console-consumer.sh --zookeeper zookeeper:2181 --topic mykafka --from-beginning</p>

<p>在生产者窗口发送一条命令，查看消费者端口是否接到命令一些命令</p>

<p>10 附录一些kafka查看命令</p>

<p>a)查看kafka topic列表，使用--list参数</p>

<p>bin/kafka-topics.sh --zookeeper zookeeper:2181 --list</p>

<p>b)查看kafka特定topic的详情，使用--topic与--describe参数</p>

<p>bin/kafka-topics.sh --zookeeper zookeeper:2181 --topic mykafka --describe</p>

<p>c)查看consumer group列表，使用--list参数</p>

<p>bin/kafka-consumer-groups.sh --zookeeper zookeeper:2181 --list</p>

<p>d)查看特定consumer group 详情，使用--group与--describe参数</p>

<p>bin/kafka-consumer-groups.sh --zookeeper zookeeper:2181 --group console-consumer-15480 --describe</p>

<p></p>

<p></p>

<p></p>

<p></p>            </div>
                </div>
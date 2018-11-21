---
layout:     post
title:      docker部署kafka，k8s（helm）部署kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，转载请注明来源。开发合作联系luanpenguestc@sina.com					https://blog.csdn.net/luanpeng825485697/article/details/81562755				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<blockquote>
<p><strong>全栈工程师开发手册 （作者：栾鹏）</strong><br>
<strong><a href="https://blog.csdn.net/luanpeng825485697/article/details/83830968" rel="nofollow"> 架构系列文章</a></strong></p>
</blockquote>
<p>我们在k8s上部署kafka，接收kafka生产者的数据，并将信息分发到消费者</p>
<p>kafka的基础知识参考：<a href="https://blog.csdn.net/luanpeng825485697/article/details/81036028" rel="nofollow">https://blog.csdn.net/luanpeng825485697/article/details/81036028</a></p>
<h1><a id="dockerkafka_8"></a>docker部署kafka</h1>
<p>同之前一样，我们现在docker部署一遍试试坑</p>
<p>拉取镜像zookeeper和kafka的镜像</p>
<pre><code>docker pull wurstmeister/zookeeper
docker pull wurstmeister/kafka
</code></pre>
<p>启动镜像，在启动中设置端口好，挂载，名称，环境变量，kafka要绑定使用哪个zk，因为各种应用，例如hbase也是使用zk，所以k8s上可能有多个zk。</p>
<pre><code>docker run -d --name kafka-zookeeper -p 2181:2181 --volume /etc/localtime:/etc/localtime wurstmeister/zookeeper:latest   启动zookeeper，注意端口号，因为以后还有有其他的zookeeper   


docker run -d --name kafka -p 9092:9092 --link kafka-zookeeper --env KAFKA_ZOOKEEPER_CONNECT=kafka-zookeeper:2181 --env KAFKA_ADVERTISED_HOST_NAME=localhost --env KAFKA_ADVERTISED_PORT=9092 --volume /etc/localtime:/etc/localtime wurstmeister/kafka:latest              后台启动kafka


</code></pre>
<p>测试发送消息<br>
执行docker ps，找到kafka的CONTAINER ID，进入容器内部：</p>
<pre><code>docker exec -it &lt;CONTAINER ID&gt; /bin/bash 
</code></pre>
<p>进入kafka默认目录</p>
<pre><code>cd opt/kafka_2.11-2.0.0/ 
</code></pre>
<p>创建一个主题：主题是由zk保管的</p>
<pre><code>bin/kafka-topics.sh --create --zookeeper kafka-zookeeper:2181 --replication-factor 1 --partitions 1 --topic mykafka
</code></pre>
<p>运行一个消息生产者，指定topic为刚刚创建的主题</p>
<pre><code>bin/kafka-console-producer.sh --broker-list localhost:9092 --topic mykafka
</code></pre>
<p>运行生产者以后，命令行便可以一直输入数据。</p>
<p>另起一个端口进入容器，启动消费者</p>
<pre><code>cd opt/kafka_2.11-2.0.0/ 
</code></pre>
<p>查看所有的topic列表</p>
<pre><code>bin/kafka-topics.sh --list --zookeeper kafka-zookeeper:2181  
</code></pre>
<p>运行一个消费者，指定同样的主题</p>
<pre><code>bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic mykafka --from-beginning
</code></pre>
<p>这时在生产者输入测试消息，在消费者就可以接收消息了</p>
<h1><a id="helmk8skafka_72"></a>通过helm在k8s部署kafka</h1>
<p><strong>通过helm方法安装</strong></p>
<p>k8s上安装kafka，可以使用helm，将kafka作为一个应用安装。当然这首先要你的k8s支持使用helm安装。</p>
<p>关于helm的介绍和k8s对helm的支持，可以参考https://yq.aliyun.com/articles/159601</p>
<p>注意：本地控制服务器的k8s需要将服务器上kubectl的config文件，拷贝到本地，替换~/.kube/config文件</p>
<p>helm安装应用也是根据这个config文件识别在那个k8s上来进行操作。</p>
<p>在添加helm仓库时需要翻墙，并且helm仓库为境外仓库，所以我们需要先将helm下载下来，然后修改仓库为国内仓库，通过国内仓库拉取镜像（国内镜像不一定和国外镜像完全同步），当然也可以直接在helm添加国内仓库，如阿里云仓库。</p>
<p>helm仓库地址https://github.com/helm/charts</p>
<p><strong>通过helm官方仓库安装kafka</strong></p>
<p>参考：<a href="https://github.com/helm/charts/tree/master/incubator/kafka" rel="nofollow">https://github.com/helm/charts/tree/master/incubator/kafka</a></p>
<pre><code>$ helm repo add incubator http://storage.googleapis.com/kubernetes-charts-incubator
$ kubectl create ns mynamespaces
$ helm install --name my-kafka --namespace mynamespaces incubator/kafka
</code></pre>
<p>helm包含的这个kafka应用，已经包含了一个zookeeper，可以通过requirement.yaml来配置。配置方法具体参考github。</p>
<p><strong>通过阿里仓库安装kafka</strong></p>
<pre><code>只安装客户端
helm init --client-only --stable-repo-url https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts/
安装客户端和服务器端
helm init --upgrade -i registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:v2.9.1 --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
服务器端需要等一会才能安装成功
helm version   查看

helm repo add incubator https://aliacs-app-catalog.oss-cn-hangzhou.aliyuncs.com/charts-incubator/
helm repo update
helm search kafka
kubectl create ns mynamespaces
helm install --name my-kafka --namespace mynamespaces incubator/kafka


</code></pre>
<p>如果安装出错了可以删除charts</p>
<pre><code>
helm delete --purge cloud-kafka
</code></pre>
<p>注：阿里云Kubernetes服务已经内置提供了Helm/Chart支持，可以直接使用<br>
<a href="https://help.aliyun.com/document_detail/58587.html" rel="nofollow">https://help.aliyun.com/document_detail/58587.html</a></p>
<p><strong>修改helm的charts文件，自定义安装</strong></p>
<p>如果上面两种你发现都无法拉取镜像，可以使用自定义修改配置文件实现。</p>
<p>关于kafka的helm应用从哪里来，有几种方式。</p>
<p>helm search kafka或者直接在github的helm官网incubator文件夹下载kafka</p>
<p>通过搜索发现阿里的仓库存在incubator/kafka	0.2.1<br>
先尝试使用阿里的helm应用</p>
<pre><code>helm install --name cloud-kafka --namespace cloudai-2 incubator/kafka
kubectl get pod -n cloudai-2
</code></pre>
<p>发现pod创建时挂载失败，所以需要先设置pv，</p>
<pre><code>helm delete --purge cloud-kafka
</code></pre>
<pre><code>helm fetch incubator/kafka    把阿里仓库中helm应用kafka-0.2.1.tgz下载下来
</code></pre>
<p>修改values.yaml中zookeeper存储为2G</p>
<p>修改kafka的templates/statefulset.yaml中最下面的接入模式，改为accessModes: [ “ReadWriteMany” ]<br>
修改charts/zookeeper/templates/statefulset.yaml文件最下面的接入模式，改为accessModes: [ “ReadWriteMany” ]<br>
<a href="http://xn--zookeeperregistry-sn60a227af85avjas04g6l2ctz8e9e2h.cn-hangzhou.aliyuncs.com/appstore/k8szk:v2" rel="nofollow">将zookeeper的镜像地址改为registry.cn-hangzhou.aliyuncs.com/appstore/k8szk:v2</a></p>
<p>在服务器k8s上创建pv,(这首先要保证你的k8s已经设置了pv功能)</p>
<p>先在k8s服务器上创建存储数据的文件夹，</p>
<p>例如pv根目录为/mnt/cephfs/目录下</p>
<p>创建/mnt/cephfs/kafka/zookeeper/<br>
和/mnt/cephfs/kafka/kafka/目录</p>
<p>注意每个pod都要有自己的持久存储，因为我们创建3个zookeeper个3个kafka，所以需要创建6个持久卷。现在把pod改成1个，这样我们也先只使用一个文件夹。</p>
<p>创建目录后，要设置chown 777权限，这样k8s挂载到此处的时候，能在里面创建目录。</p>
<p>分别用于存储kafka的zk和kakfa数据<br>
yaml文件中path直接写/kafka/zookeeper和/kafka/kafka</p>
<p>创建secret，secret同于链接pv，</p>
<pre><code>kubectl create -f create_secret.yaml
</code></pre>
<p>创建pv，</p>
<pre><code>kubectl create -f create_kafka_pv.yaml
</code></pre>
<p>helm安装本地kafka文件夹应用。在下面的安装前要保证，pv是available状态，没有被占用，并且已经创建，pvc的访问模式和pv的访问模式相同，容量相同。镜像地址是可以拉取的。如果不能拉取可以去阿里云替换可以拉取的地址。如果安全失败，记得把安装失败的全部删除，把pv也全部删除重新设置。</p>
<pre><code>helm delete --purge cloud-kafka     # 先删除之前的应用
helm install --name cloud-kafka --namespace cloudai-2 kafka
</code></pre>
<p>安装中两个容器会启动，但是启动中会初始化很多东西，但是由于kafka要关联zookeeper，所以在zookeeper没有成功初始化以后，kafka会不停的启动失败，知道zookeeper启动完成。kafka才能初始化完成。</p>
<p>可以在k8s中的deshboard中刷新查看结果。当第二次安装的时候会比较顺利。</p>
<p>要想外网访问，修改服务的yaml文件，设置type为NodePort为30946</p>
<p>还要在安全组中开放这个端口</p>
<p>可以先在pod里面测试</p>
<p>进入kafka的pod</p>
<p>创建主题</p>
<pre><code>bin/kafka-topics.sh --create --zookeeper 10.233.61.237:2181 --replication-factor 1 --partitions 1 --topic device         这里面使用的是集群ip，容器间访问
</code></pre>
<p>创建消费者</p>
<pre><code>bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic device --from-beginning   
</code></pre>
<p>创建生产者</p>
<pre><code>bin/kafka-console-producer.sh --broker-list localhost:9092 --topic device     使用本地ip
bin/kafka-console-producer.sh --broker-list 10.233.9.150:9092 --topic device     使用集群id
bin/kafka-console-producer.sh --broker-list 192.168.2.177:30946 --topic device    使用服务器内网ip和nodeport
</code></pre>
<p>现在pod内部可以访问了，现在尝试在外部创建生产者。</p>
<p>实验发现，在k8s上部署服务来访问k8s上的kafka，依然没有问题。</p>
<p>下面我们尝试直接在本地pc访问服务器k8s中的kafka。</p>
<p>在本地电脑上cd到kafka的目录</p>
<pre><code>cd /home/lp/soft/kafka_2.11-1.1.0
bin/kafka-console-producer.sh --broker-list 192.168.2.177:30946 --topic device
</code></pre>
<p>发现访问不了。</p>
<p>进入pod<br>
在pod重启kafka。</p>
<pre><code>bin/kafka-server-stop.sh    关闭
</code></pre>
<p>进入pod，执行</p>
<pre><code>bin/zookeeper-shell.sh 10.233.61.237:2181 &lt;&lt;&lt; "get /brokers/ids/0"    使用的是zookeeper的集群ip
</code></pre>
<p>发现了 PLAINTEXT 里面注册的域名很诡异。发现这个是docker容器的id！！<br>
于是在宿主机把这个id加了个host对应127.0.0.1 问题就解决了。</p>
<p>然后就可以通过服务器ip地址访问了192.168.2.177:30946</p>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
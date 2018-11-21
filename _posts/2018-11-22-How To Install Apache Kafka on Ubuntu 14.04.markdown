---
layout:     post
title:      How To Install Apache Kafka on Ubuntu 14.04
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1 id="how-to-install-apache-kafka-on-ubuntu-1404">How To Install Apache Kafka on Ubuntu 14.04</h1>



<h3 id="introduction"><strong>Introduction</strong></h3>

<p>Apache Kafka is a popular distributed message broker designed to handle large volumes of real-time data efficiently. A Kafka cluster is not only highly scalable and fault-tolerant, but it also has a much higher throughput compared to other message brokers such as ActiveMQ and RabbitMQ. Though it is generally used as a pub/sub messaging system, a lot of organizations also use it for log aggregation because it offers persistent storage for published messages.</p>

<p>In this tutorial, you will learn how to install and use Apache Kafka 0.8.2.1 on Ubuntu 14.04.</p>

<h2 id="prerequisites">Prerequisites</h2>

<p>To follow along, you will need:</p>

<ul>
<li>Ubuntu 14.04 Droplet</li>
<li>At least 4GB of  <a href="https://www.digitalocean.com/community/tutorials/how-to-add-swap-on-ubuntu-14-04" rel="nofollow" target="_blank">swap space</a></li>
</ul>

<h2 id="step-1-create-a-user-for-kafka">Step 1 — Create a User for Kafka</h2>

<p>As Kafka can handle requests over a network, you should create a dedicated user for it. This minimizes damage to your Ubuntu machine should the Kafka server be comprised.</p>

<p><strong>Note:</strong>  After setting up Apache Kafka, it is recommended that you create a different non-root user to perform other tasks on this server.</p>

<p>As root, create a user called  <strong>kafka</strong>  using the useradd command:</p>

<ul>
<li>useradd kafka -m</li>
</ul>

<p>Set its password using passwd:</p>

<ul>
<li>passwd kafka</li>
</ul>

<p>Add it to the sudo group so that it has the privileges required to install Kafka's dependencies. This can be done using the adduser command:</p>

<ul>
<li>adduser kafka sudo</li>
</ul>

<p>Your Kafka user is now ready. Log into it using su:</p>

<ul>
<li>su - kafka</li>
</ul>

<h2 id="step-2-install-java">Step 2 — Install Java</h2>

<p>Before installing additional packages, update the list of available packages so you are installing the latest versions available in the repository:</p>

<ul>
<li>sudo apt-get update</li>
</ul>

<p>As Apache Kafka needs a Java runtime environment, use apt-get to install the default-jre package:</p>

<ul>
<li>sudo apt-get install default-jre</li>
</ul>

<h2 id="step-3-install-zookeeper">Step 3 — Install ZooKeeper</h2>

<p>Apache ZooKeeper is an open source service built to coordinate and synchronize configuration information of nodes that belong to a distributed system. A Kafka cluster depends on ZooKeeper to perform—among other things—operations such as detecting failed nodes and electing leaders.</p>

<p>Since the ZooKeeper package is available in Ubuntu's default repositories, install it using apt-get.</p>

<ul>
<li>sudo apt-get install zookeeperd</li>
</ul>

<p>After the installation completes, ZooKeeper will be started as a daemon automatically. By default, it will listen on port  <strong>2181</strong>.</p>

<p>To make sure that it is working, connect to it via Telnet:</p>

<ul>
<li>telnet localhost 2181</li>
</ul>

<p>At the Telnet prompt, type in ruok and press ENTER.</p>

<p>If everything's fine, ZooKeeper will say imok and end the Telnet session.</p>

<h2 id="step-4-download-and-extract-kafka-binaries">Step 4 — Download and Extract Kafka Binaries</h2>

<p>Now that Java and ZooKeeper are installed, it is time to download and extract Kafka.</p>

<p>To start, create a directory called Downloads to store all your downloads.</p>

<ul>
<li>mkdir -p ~/Downloads</li>
</ul>

<p>Use wget to download the Kafka binaries.</p>

<ul>
<li>wget "<a href="http://mirror.cc.columbia.edu/pub/software/apache/kafka/0.8.2.1/kafka_2.11-0.8.2.1.tgz&amp;quot" rel="nofollow" target="_blank">http://mirror.cc.columbia.edu/pub/software/apache/kafka/0.8.2.1/kafka_2.11-0.8.2.1.tgz&amp;quot</a>; -O ~/Downloads/kafka.tgz</li>
</ul>

<p>Create a directory called kafka and change to this directory. This will be the base directory of the Kafka installation.</p>



<h2 id="mkdir-p-kafka-cd-kafka">- mkdir -p ~/kafka &amp;&amp; cd ~/kafka</h2>

<p>Extract the archive you downloaded using the tar command.</p>

<ul>
<li>tar -xvzf ~/Downloads/kafka.tgz –strip 1</li>
</ul>

<h2 id="step-5-configure-the-kafka-server">Step 5 — Configure the Kafka Server</h2>

<p>The next step is to configure the Kakfa server.</p>

<p>Open server.properties using vi:</p>

<ul>
<li>vi ~/kafka/config/server.properties</li>
</ul>

<p>By default, Kafka doesn't allow you to delete topics. To be able to delete topics, add the following line at the end of the file:</p>

<p>~/kafka/config/server.properties</p>

<p>delete.topic.enable = true</p>

<p>Save the file, and exit vi.</p>

<h2 id="step-6-start-the-kafka-server">Step 6 — Start the Kafka Server</h2>

<p>Run the kafka-server-start.sh script using nohup to start the Kafka server (also called Kafka broker) as a background process that is independent of your shell session.</p>

<ul>
<li>nohup ~/kafka/bin/kafka-server-start.sh ~/kafka/config/server.properties &gt; ~/kafka/kafka.log 2&gt;&amp;1 &amp;</li>
</ul>

<p>Wait for a few seconds for it to start. You can be sure that the server has started successfully when you see the following messages in ~/kafka/kafka.log:</p>

<p>excerpt from ~/kafka/kafka.log</p>

<p>…</p>

<p>[2015-07-29 06:02:41,736] INFO New leader is 0 (kafka.server.ZookeeperLeaderElector$LeaderChangeListener)</p>

<p>[2015-07-29 06:02:41,776] INFO [Kafka Server 0], started (kafka.server.KafkaServer)</p>

<p>You now have a Kafka server which is listening on port  <strong>9092</strong>.</p>

<h2 id="step-7-test-the-installation">Step 7 — Test the Installation</h2>

<p>Let us now publish and consume a  <strong>"Hello World"</strong>  message to make sure that the Kafka server is behaving correctly.</p>

<p>To publish messages, you should create a Kafka producer. You can easily create one from the command line using the kafka-console-producer.sh script. It expects the Kafka server's hostname and port, along with a topic name as its arguments.</p>

<p>Publish the string  <strong>"Hello, World"</strong>  to a topic called  <strong>TutorialTopic</strong>  by typing in the following:</p>

<ul>
<li>echo "Hello, World" | ~/kafka/bin/kafka-console-producer.sh –broker-list localhost:9092 –topic TutorialTopic &gt; /dev/null</li>
</ul>

<p>As the topic doesn't exist, Kafka will create it automatically.</p>

<p>To consume messages, you can create a Kafka consumer using the kafka-console-consumer.sh script. It expects the ZooKeeper server's hostname and port, along with a topic name as its arguments.</p>

<p>The following command consumes messages from the topic we published to. Note the use of the –from-beginning flag, which is present because we want to consume a message that was published before the consumer was started.</p>

<ul>
<li>~/kafka/bin/kafka-console-consumer.sh –zookeeper localhost:2181 –topic TutorialTopic –from-beginning</li>
</ul>

<p>If there are no configuration issues, you should see Hello, World in the output now.</p>

<p>The script will continue to run, waiting for more messages to be published to the topic. Feel free to open a new terminal and start a producer to publish a few more messages. You should be able to see them all in the consumer's output instantly.</p>

<p>When you are done testing, press CTRL+C to stop the consumer script.</p>

<h2 id="step-8-install-kafkat-optional">Step 8 — Install KafkaT (Optional)</h2>

<p>KafkaT is a handy little tool from Airbnb which makes it easier for you to view details about your Kafka cluster and also perform a few administrative tasks from the command line. As it is a Ruby gem, you will need Ruby to use it. You will also need the build-essential package to be able to build the other gems it depends on. Install them using apt-get:</p>

<ul>
<li>sudo apt-get install ruby ruby-dev build-essential</li>
</ul>

<p>You can now install KafkaT using the gem command:</p>

<ul>
<li>sudo gem install kafkat –source <a href="https://rubygems.org" rel="nofollow" target="_blank">https://rubygems.org</a> –no-ri –no-rdoc</li>
</ul>

<p>Use vi to create a new file called .kafkatcfg.</p>

<ul>
<li>vi ~/.kafkatcfg</li>
</ul>

<p>This is a configuration file which KafkaT uses to determine the installation and log directories of your Kafka server. It should also point KafkaT to your ZooKeeper instance. Accordingly, add the following lines to it:</p>

<p>~/.kafkatcfg</p>

<p>{</p>

<p>"kafka_path": "~/kafka",</p>

<p>"log_path": "/tmp/kafka-logs",</p>

<p>"zk_path": "localhost:2181"</p>

<p>}</p>

<p>You are now ready to use KafkaT. For a start, here's how you would use it to view details about all Kafka partitions:</p>

<ul>
<li>kafkat partitions</li>
</ul>

<p>You should see the following output:</p>

<p>output of kafkat partitions</p>

<p>Topic           Partition   Leader      Replicas        ISRs</p>

<p>TutorialTopic   0             0           [0]           [0]</p>

<p>To learn more about KafkaT, refer to its  <a href="https://github.com/airbnb/kafkat" rel="nofollow" target="_blank">GitHub repository</a>.</p>

<h2 id="step-9-set-up-a-multi-node-cluster-optional">Step 9 — Set Up a Multi-Node Cluster (Optional)</h2>

<p>If you want to create a multi-broker cluster using more Ubuntu 14.04 machines, you should repeat Step 1, Step 3, Step 4 and Step 5 on each of the new machines. Additionally, you should make the following changes in the server.properties file in each of them:</p>

<ul>
<li>the value of the id property should be changed such that it is unique throughout the cluster</li>
<li>the value of the connect property should be changed such that all nodes point to the same ZooKeeper instance</li>
</ul>

<p>If you want to have multiple ZooKeeper instances for your cluster, the value of the zookeeper.connectproperty on each node should be an identical, comma-separated string listing the IP addresses and port numbers of all the ZooKeeper instances.</p>

<h2 id="step-10-restrict-the-kafka-user">Step 10 — Restrict the Kafka User</h2>

<p>Now that all installations are done, you can remove the kafka user's admin privileges. Before you do so, log out and log back in as any other non-root sudo user. If you are still running the same shell session you started this tutorial with, simply type exit.</p>

<p>To remove the kafka user's admin privileges, remove it from the sudo group.</p>

<ul>
<li>sudo deluser kafka sudo</li>
</ul>

<p>To further improve your Kafka server's security, lock the kafka user's password using the passwdcommand. This makes sure that nobody can directly log into it.</p>

<ul>
<li>sudo passwd kafka -l</li>
</ul>

<p>At this point, only root or a sudo user can log in as kafka by typing in the following command:</p>

<ul>
<li>sudo su - kafka</li>
</ul>

<p>In the future, if you want to unlock it, use passwd with the -u option:</p>

<ul>
<li>sudo passwd kafka -u</li>
</ul>

<h2 id="conclusion">Conclusion</h2>

<p>You now have a secure Apache Kafka running on your Ubuntu server. You can easily make use of it in your projects by creating Kafka producers and consumers using  <a href="https://cwiki.apache.org/confluence/display/KAFKA/Clients#Clients-For0.8.x" rel="nofollow" target="_blank">Kafka clients</a> which are available for most programming languages. To learn more about Kafka, do go through its  <a href="http://kafka.apache.org/documentation.html" rel="nofollow" target="_blank">documentation</a>.</p>

<p><a href="https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-14-04" rel="nofollow" target="_blank">https://www.digitalocean.com/community/tutorials/how-to-install-apache-kafka-on-ubuntu-14-04</a></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
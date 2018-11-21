---
layout:     post
title:      Ubuntu + Apache Kafka Installation
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>http://vulab.com/blog/?p=576</p>
<p><br></p>
<p></p>
<div class="entry" style="border:0px;vertical-align:baseline;color:rgb(51,51,51);line-height:18px;text-align:center;background:rgb(240,240,240);">
<h2 style="border:0px;font-size:25px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Aim of this Kafka Tutorial:</h2>
<h4 style="border:0px;font-size:15px;vertical-align:baseline;color:rgb(63,49,46);background:transparent;">
Install Apache Kafka, Create a topic and use a producer to send test messages to Kafka Topics. Also launch a consumer to consume messages from Kafka Topics.</h4>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 1:</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Our aim is to install a Ubuntu 64bit server using Oracle Virtual box.</p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Please download Ubuntu server from this link <a title="Download Ubuntu 64 bit server" href="http://www.ubuntu.com/download/server" rel="nofollow" style="border:0px;vertical-align:baseline;text-decoration:none;color:rgb(251,94,0);background:transparent;">Download
 Ubuntu Server</a></p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Please download Oracle Virtual Box from this link <a title="Download Oracle Virtual Box" href="https://www.virtualbox.org/wiki/Downloads" rel="nofollow" style="border:0px;vertical-align:baseline;text-decoration:none;color:rgb(251,94,0);background:transparent;">Download
 Virtual Box</a></p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Follow the instructions online to get your Virtual Box started with Ubuntu 64 bit server. Google is your best bet to get the correct instructions for this step.</p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
<span style="border:0px;vertical-align:baseline;background:transparent;">Note: Make sure you have JDK installed on the OS.</span></p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
On my machine java -version is displaying the following version<br>
vulab-build-system:~$ java -version<br>
java version “1.8.0_11″<br>
Java(TM) SE Runtime Environment (build 1.8.0_11-b12)<br>
Java HotSpot(TM) 64-Bit Server VM (build 25.11-b03, mixed mode)</p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
<span id="more-576" style="border:0px;vertical-align:baseline;background:transparent;"></span></p>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 2:</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Download Apache Kafka version 0.8.1.1 from <a title="Download Apache Kafka" href="https://www.apache.org/dyn/closer.cgi?path=/kafka/0.8.1.1/kafka_2.9.2-0.8.1.1.tgz" rel="nofollow" style="border:0px;vertical-align:baseline;text-decoration:none;color:rgb(251,94,0);background:transparent;">Download
 Apache Kafka</a></p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
You can use wget http://mirror.sdunix.com/apache/kafka/0.8.1.1/kafka_2.9.2-0.8.1.1.tgz</p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
<a href="http://vulab.com/blog/wp-content/uploads/2014/10/apache_kafka_download.png" rel="nofollow" style="border:0px;vertical-align:baseline;text-decoration:none;color:rgb(251,94,0);background:transparent;"><img class="alignnone wp-image-588 size-medium" src="http://vulab.com/blog/wp-content/uploads/2014/10/apache_kafka_download-300x222.png" alt="apache_kafka_download" width="300" height="222" style="border:0px;vertical-align:baseline;background:transparent;"></a></p>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 3:</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Unzip or expand the zip file downloaded at previous step.</p>
<pre style="border:0px;vertical-align:baseline;background:transparent;"><span style="border:0px;vertical-align:baseline;background:transparent;">tar -xzf kafka_2.9.2-0.8.1.1.tgz</span><span style="border:0px;vertical-align:baseline;background:transparent;">
CD to the folder and explore the folder structure.

</span></pre>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 4:</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Apache Kafka requires zookeeper to be available and running.</p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Start zookeeper with the following command</p>
<pre style="border:0px;vertical-align:baseline;background:transparent;"><span style="border:0px;vertical-align:baseline;background:transparent;">bin/zookeeper-server-start.sh config/zookeeper.properties</span></pre>
<pre style="border:0px;vertical-align:baseline;background:transparent;"><span style="border:0px;vertical-align:baseline;background:transparent;"> <a href="http://vulab.com/blog/wp-content/uploads/2014/10/zookeeper_started.png" rel="nofollow" style="border:0px;vertical-align:baseline;text-decoration:none;color:rgb(251,94,0);background:transparent;"></a></span>

</pre>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 5:</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Start Kafka Server using the following command</p>
<pre style="border:0px;vertical-align:baseline;background:transparent;"><span style="border:0px;vertical-align:baseline;background:transparent;">bin/kafka-server-start.sh config/server.properties

</span></pre>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 6:</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Create a topic in Apache Kafka using the following command</p>
<pre style="border:0px;vertical-align:baseline;background:transparent;"><span style="border:0px;vertical-align:baseline;background:transparent;">bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1
 --partitions 1 --topic test
Output will be displayed as Created topic "test"

</span></pre>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 7:</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Ask Zookeeper to list available topics on Apache Kafka.</p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Command is as below:</p>
<pre style="border:0px;vertical-align:baseline;background:transparent;"><span style="border:0px;vertical-align:baseline;background:transparent;">bin/kafka-topics.sh --list --zookeeper localhost:2181
Output will be displayed as test
</span></pre>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 8:</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Send test messages to Apache Kafka topic called Test using command line producer</p>
<p style="border:0px;vertical-align:baseline;background:transparent;">
Please use the below command</p>
<pre style="border:0px;vertical-align:baseline;background:transparent;"><span style="border:0px;vertical-align:baseline;background:transparent;">bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test
Please enter some messages like "Hello vulab" press enter
Enter another message "How are you kafka?" and press enter.

</span></pre>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
Step 9:</h2>
<pre style="border:0px;vertical-align:baseline;background:transparent;"><span style="border:0px;vertical-align:baseline;background:transparent;">Use command line consumer to check for messages on Apache Kafka Topic called test
Please use the following command
</span></pre>
<p style="border:0px;vertical-align:baseline;background:transparent;">
<span style="border:0px;vertical-align:baseline;background:transparent;">bin/kafka-console-consumer.sh –zookeeper localhost:2181 –topic test –from-beginning<br>
You will see messages printed on the console like “Hello vulab”</span><br>
With this step you have successfully verified that you have a valid Apache Kafka setup with Apache Zookeeper.</p>
<h2 style="border:0px;font-size:30px;vertical-align:baseline;font-family:Copse;color:rgb(63,49,46);font-weight:400;background:transparent;">
We had Success and what is next?</h2>
<p style="border:0px;vertical-align:baseline;background:transparent;">
You did good. In the next step we are going to setup an Apache Maven project in Eclipse IDE. We will use Apache Kafka Producer API and Apache Consumer API to publish and subscribe messages using Apache Kafka. Get ready for the next tutorial in this Apache Kafka
 learning series by Vulab.</p>
</div>
<div class="tags" style="border:0px;vertical-align:baseline;color:rgb(51,51,51);line-height:18px;text-align:center;background:rgb(240,240,240);">
</div>
<span style="color:rgb(51,51,51);line-height:18px;text-align:center;background-color:rgb(240,240,240);">- See more at: http://vulab.com/blog/?p=576#sthash.CijJE2Da.dpuf</span><br><p></p>
            </div>
                </div>
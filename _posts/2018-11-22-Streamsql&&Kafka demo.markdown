---
layout:     post
title:      Streamsql&&Kafka demo
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/age_of_big_data/article/details/71205036				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="line-height:1.875;font-size:14px;"><span style="font-size:24px;font-family:'Simsun-FZ';color:#333333;">一、建Kafka数据源</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-size:13px;font-family:SimSun, STSong;color:#333333;">1. 登陆您集群中的一台安装了Kafka的节点(node4,192.168.100.54)(zookeeper 192.168.100.51-53)。进入/usr/lib/kafka/bin目录，该目录下有建Kafka数据源的</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-size:13px;font-family:SimSun, STSong;color:#333333;">所需要的一些脚本。</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-size:13px;font-family:SimSun, STSong;color:#333333;">1. 建一个Kafka Topic</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-size:13px;font-family:SimSun, STSong;color:#333333;">执行下面指令，运行/usr/lib/kafka/bin目录下的kafka-create-topic.sh脚本：</span></div>
<div style="line-height:1.875;font-size:14px;">./kafka-create-topic.sh --partition 3 --topic demo --zookeeper 192.168.100.51:2181 --broker 192.168.100.54:9092</div>
<div style="line-height:1.875;font-size:14px;"><br></div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;">2.<span style="font-size:13px;font-family:SimSun, STSong;color:#333333;">查看Kafka Topic 执行下面指令，运行/usr/lib/kafka/bin目录下的kafka-list-topic.sh脚本</span></div>
<div style="line-height:1.875;font-size:14px;">./kafka-list-topic.sh --zookeeper 192.168.100.51:2181</div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-family:interm;color:#333333;">3. 建Kafka producer并发布消息</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-family:interm;color:#333333;">执行下面指令，运行/usr/lib/kafka/bin目录下的kafka-console-producer.sh脚本：</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-family:interm;color:#333333;">./kafka-console-producer.sh --broker-list 192.168.100.54:9092 --topic demo（为kafka节点）</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-family:interm;color:#333333;">这里我们指定了使用172.16.1.128节点为Kafka broker（详细介绍见后文），并且指定了producer发布</span></div>
<div style="line-height:1.5;font-size:14px;"><span style="font-family:interm;color:#333333;">消息的topic为demo。现在，我们可以在命令行中输入一些消息，这些消息都将被发布给demo</span></div>
<div style="line-height:1.5;font-size:14px;"><br></div>
<div style="line-height:1.5;font-size:14px;"><br></div>
<div style="line-height:1.5;font-size:14px;">1. 登陆您集群中的任意一个节点，连接到Inceptor。这里，我们以hive用户身份连接一个LDAP认证</div>
<div style="line-height:1.5;font-size:14px;">的Inceptor Server 2。</div>
<div style="line-height:1.5;font-size:14px;">beeline -u "jdbc:hive2://localhost:10000/default" -n hive -p 123456</div>
<div style="line-height:1.5;font-size:14px;">2. 建一个Stream</div>
<div style="line-height:1.5;font-size:14px;">CREATE STREAM demo_stream(id INT, letter STRING) ROW FORMAT DELIMITED FIELDS TERMINATED BY ','</div>
<div style="line-height:1.875;font-size:14px;">TBLPROPERTIES("topic"="demo","kafka.zookeeper"="192.168.100.51:2181");</div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;">3.查看Stream</div>
<div style="line-height:1.875;font-size:14px;">SHOW STREAMS;</div>
<div style="line-height:1.875;font-size:14px;">我们可以看到刚刚建好的demo_stream。</div>
<div style="line-height:1.875;font-size:14px;">4. 创建并触发一个StreamJob</div>
<div style="line-height:1.875;font-size:14px;">a. 建一张新表demo_table，它的需要和demo_stream有相同的schema：</div>
<div style="line-height:1.875;font-size:14px;">CREATE TABLE demo_table(id INT, letter STRING);</div>
<div style="line-height:1.875;font-size:14px;">b. 向demo_table插入demo_stream中的数据，这个操作会触发StreamJob的执行：</div>
<div style="line-height:1.875;font-size:14px;">INSERT INTO demo_table SELECT * FROM demo_stream;</div>
<div style="line-height:1.875;font-size:14px;">5. 列出正在运行的StreamJob</div>
<div style="line-height:1.875;font-size:14px;">执行下面指令：</div>
<div style="line-height:1.875;font-size:14px;">LIST STREAMJOBS;</div>
<div style="line-height:1.875;font-size:14px;">我们可以看到下面输出：</div>
<div style="line-height:1.875;font-size:14px;">输出中包含streamid，触发StreamJob的sql和status。</div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;">6..在Inceptor管理界面4040查看Streamjob运行状态</div>
<div style="line-height:1.875;font-size:14px;">可以看到在“Active Stages”下有正在运行的demo_stream。</div>
<div style="line-height:1.875;font-size:14px;">7. 此时demo_stream已经开始接收发布到之前创建的demo的消息。需要注意的是，demo_stream对发布</div>
<div style="line-height:1.875;font-size:14px;">到demo的消息的接收是从streamid=29008ed34b9e45bca784362948b88a85的StreamJob触发开始的，也就</div>
<div style="line-height:1.875;font-size:14px;">是说从执行INSERT开始的，在执行INSERT之前发布到demo的消息都不会被demo_stream接收。所以，目</div>
<div style="line-height:1.875;font-size:14px;">前demo_table中没有任何记录：</div>
<div style="line-height:1.875;font-size:14px;">SELECT * FROM demo_table;</div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:18px;">二、接收并处理Kafka传来的数据</span></div>
<div style="line-height:1.875;font-size:14px;">1. 切换到正在运行向demo发布数据的Kafka producer的页面：</div>
<div style="line-height:1.875;font-size:14px;">./kafka-console-producer.sh --broker-list 192.168.100.54:9092 --topic demo</div>
<div style="line-height:1.875;font-size:14px;">hello</div>
<div style="line-height:1.875;font-size:14px;">world</div>
<div style="line-height:1.875;font-size:14px;">由于“hello”，“world”都是在streamid=29008ed34b9e45bca784362948b88a85的StreamJob触发前发布的，这两条消息都不会被demo_stream接收。</div>
<div style="line-height:1.875;font-size:14px;"><span style="color:#FF0000;">（192.168.100.54为kafka集群的一个节点）</span></div>
<div style="line-height:1.875;font-size:14px;">6 | 2. 快速入门</div>
<div style="line-height:1.875;font-size:14px;">2. 在命令行中输入一些数据。由于我们已经规定了demo_stream接收消息的类型是由“,”分隔的两列文</div>
<div style="line-height:1.875;font-size:14px;">本，我们需要输入这个类型的消息，以便demo_stream处理。输入：</div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;">3. 切换回到Inceptor命令行的窗口，现在查看demo_table中的数据，我们可以看到demo_table中有我们刚</div>
<div style="line-height:1.875;font-size:14px;">才发布的四条消息：</div>
<div style="line-height:1.875;font-size:14px;">SELECT * FROM demo_table;</div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;">4. 现在我们可以在demo_table上进行一些查询：</div>
<div style="line-height:1.875;font-size:14px;">SELECT COUNT(*) FROM demo_table GROUP BY letter;</div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:18px;">三、停止Streamjob</span></div>
<div style="line-height:1.875;font-size:14px;">演示到此结束，用下面指令可以停止streamid为29008ed34b9e45bca784362948b88a85的streamjob：</div>
<div style="line-height:1.875;font-size:14px;">STOP STREAMJOB 29008ed34b9e45bca784362948b88a85;</div>
<div><img src="" alt=""></div>
<div style="line-height:1.875;font-size:14px;"><br></div>
<div style="line-height:1.875;font-size:14px;"><br></div>
<div style="line-height:1.875;font-size:14px;">StreamSQL的优势</div>
<div style="line-height:1.875;font-size:14px;">相对于采用编程（Scala/Java）的方式开发流应用，采用StreamSQL具有以下优势：</div>
<div style="line-height:1.875;font-size:14px;">微批模式和事件驱动模式的一体化</div>
<div style="line-height:1.875;font-size:14px;">在同一套系统里，用户可以根据业务需求，灵活切换微批模式的流处理和事件驱动模式的流处理。</div>
<div style="line-height:1.875;font-size:14px;">极高的易用性</div>
<div style="line-height:1.875;font-size:14px;">而使用StreamSQL，用户只需要有编写普通SQL的经验，就可以写出高效、安全、稳定的流处理应用。</div>
<div style="line-height:1.875;font-size:14px;">性能提升</div>
<div style="line-height:1.875;font-size:14px;">在一些条件下，采用StreamSQL的方式甚至比编程方式获得更高的性能提升。这是因为StreamSQL做了一些特殊优化，在编程模式下无法轻易实现。</div>
<div style="line-height:1.875;font-size:14px;">产品化程度高</div>
<div style="line-height:1.875;font-size:14px;">通过编程的方式来实现流处理的另一个问题是产品化程度非常低。由于编程有较高的自由度，出现问题的可能性很大；而又由于编程的方式将流处理平台和用户程序绑定在一起，用户没办法及时分析出出错问题的root cause。SQL作为一个通用的接口将大大地提高产品化程度。</div>
<div style="line-height:1.875;font-size:14px;">迁移成本低</div>
<div style="line-height:1.875;font-size:14px;">用户原有的很多业务逻辑是通过SQL实现，如果通过编程的方式迁移到流上，迁移成本非常高，难以保证迁移后逻辑的正确性。而一旦采用StreamSQL，用户只需要修改少量SQL，迁移成本几乎接近零。</div>
<div style="line-height:1.875;font-size:14px;">由于具备上述几项优点，Inceptor StreamSQL将会在建立流式应用时，表现出其强大的业务应对能力和易用性。用户将发现流式分析的实现过程也可以很便捷。</div>
            </div>
                </div>
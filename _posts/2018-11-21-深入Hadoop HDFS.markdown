---
layout:     post
title:      深入Hadoop HDFS
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span>
</span></p><blockquote>
<p>1. hdfs架构简介</p>
<p><span>	</span>1.1  hdfs架构挑战</p>
<p><span>	</span>1.2 架构简介</p>
<p><span>	</span>1.3 文件系统命名空间<em>File System Namespace</em></p>
<p><span>	</span>1.4  数据复制</p>
<p><span>	</span>1.5 元数据持久化</p>
<p><span>	</span>1.6 信息交换协议 </p>
<p>2.  hdfs数据可访问性</p>
<p><span>	</span>2.1  web interface<span>	</span></p>
<p><span>	</span>2.2 shell command<span>	</span></p>
</blockquote>
<h3><strong><span style="font-family:Cambria;">&lt;1&gt;. hdfs架构简介 </span></strong></h3>
<p>1.1 hdfs架构挑战</p>
<p>hdfs和大多数现有的分布式文件系统存在很多类似特点，但是又具有自己一些特性：具有很高的容错性<em>highly fault-tolerant</em>，较高的数据吞吐量<em>high throughput</em>等。为了满足上面的特性，hdfs将不得不解决下面的一些棘手问题：</p>
<p>1. 硬件错误：在一个 hdfs系统中可能保存大量的服务器，那么每个服务器均存在硬件故障的可能性，那么hdfs需要保证能够自动检测到某个服务器错误，同时能够自动恢复。这个目标是hdfs架构的首要解决的问题。</p>
<p>2. 流式的数据访问<em>Streaming Data Access</em>：hdfs需要向应用程序提供流式的数据访问。</p>
<p>3. 大文件支持：在hdfs上存储的文件可能是在G级别或者是T级别的，这样hdfs需要能够对大文件支持。同时需要支持在一个实例中存储大量的文件(<em>It should support tens of millions of files in a single instance</em>)。</p>
<p>4. 数据一致性保证：hdfs需要能够支持“write-once-read-many access” 模型。 </p>
<p>面对上面的架构需求，我们来看看hdfs是如何满足上面的架构需求的。 </p>
<p>1.2 架构简介</p>
<p>hdfs采用的是master/slave模型，一个hdfs cluster包含一个NameNode和一些列的DataNode，其中NameNode充当的是master的角色，主要负责管理hdfs文件系统，结构来自客户端的请求；DataNode主要是用来存储数据文件，hdfs将一个文件分割成一个多这是多个的block，这些block可能存储在一个DataNode上或者是多个DataNode上。</p>
<p><img src="http://images.cnblogs.com/cnblogs_com/xuqiang/hadoop/hdfsarchitecture.gif" alt="" width="600" height="414"></p>
<p>1.3 文件系统命名空间File System Namespace</p>
<p>hdfs支持传统文件系统的目录结构，应用程序能够创佳目录directory，在这些目录中存储文件，创建文件，移动文件remove file，重命名文件，但是不支持硬链接和软连接。 </p>
<p>1.4 数据复制<em>Data Replication</em></p>
<p>hdfs将一个发文件分割成block，然后将这些block存储到不同的DataNode中，那么如何保证如果一个DataNode死掉，保证数据的完整性，通常的技术就是进行数据的备份，hdfs同样使用的是这一策略。</p>
<p><img src="http://images.cnblogs.com/cnblogs_com/xuqiang/hadoop/hdfsdatanodes.gif" alt="" width="600" height="367"></p>
<div>
<div>我们现在考虑系统启动时，NameNode首先进入SafeMode，在这种模式下是不进行数据的备份（拷贝的）的，DataNode向NameNode发送Heartbeat和Blockreport，从而使得NameNode得到在每个DataNode上存储的数据文件，然后NameNode检查那些block的备份镜像数量还未达到所需备份数量，那么NameNode将对这些blocks。</div>
</div>
<p> </p>
<p>1.5 元数据持久化</p>
<p>hdfs使用日志机制将对文件系统的操作全部存储在一个日志文件中，同时将整个文件系统信息（<em>the mapping of blocks to files and file system properties</em>）映射成一个FsImage文件，该文件存储在NameNode主机的本地文件系统上。同时FsImage和Log支持multiple copies，这些hdfs保证这些备份文件的一致性。</p>
<p>1.6 信息交换协议</p>
<p>上面讲到“DataNode向NameNode发送Heartbeat和Blockreport”，这其中显然涉及到协议的问题，hdfs communication协议是构建在tcp/ip协议上的。客户端通过ClientProtocol协议和NameNode交换信息，NameNode通过DataNode Procotol协议和DataNode交换信息。</p>
<h3><strong><span style="font-family:Cambria;">&lt;2&gt;. 数据可访问性 </span></strong></h3>
<p>2.1 web interface</p>
<p>hdfs可以使用web页面来查看hdfs中文件系统中的目录http://localhost:50075：</p>
<p> <img src="http://images.cnblogs.com/cnblogs_com/xuqiang/hadoop/hdfs3.jpg" alt="" width="785" height="330"></p>
<p>2.2 shell command</p>
<p>1. 创建目录</p>
<div>
<div>xuqiang@ubuntu:~/hadoop/src/hadoop-0.21.0$ ./bin/hadoop dfs -mkdir /foodir</div>
<p> <img src="http://images.cnblogs.com/cnblogs_com/xuqiang/hadoop/hdfs4.jpg" alt="" width="764" height="193"></p>
</div>
<p>2. 删除目录</p>
<div>
<div>xuqiang@ubuntu:~/hadoop/src/hadoop-0.21.0$ ./bin/hadoop dfs -rmr/foodir</div>
</div>
<p>3. 上传文件</p>
<div>
<div>xuqiang@ubuntu:~/hadoop/src/hadoop-0.21.0$ ./bin/hadoop dfs -put./conf/* /foodir</div>
</div>
<p>4. 查看文件 </p>
<div>
<div>xuqiang@ubuntu:~/hadoop/src/hadoop-0.21.0$ bin/hadoop dfs -cat /foodir/capacity-scheduler.xml</div>
<p>5. 删除文件</p>
<div>
<div>xuqiang@ubuntu:~/hadoop/src/hadoop-0.21.0$ bin/hadoop dfs -rm /foodir/capacity-scheduler.xml</div>
</div>
<div>更多操作：<a href="http://hadoop.apache.org/common/docs/current/file_system_shell.html#rm" rel="nofollow">http://hadoop.apache.org/common/docs/current/file_system_shell.html</a> </div>
</div>
            </div>
                </div>
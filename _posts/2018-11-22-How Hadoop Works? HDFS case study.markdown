---
layout:     post
title:      How Hadoop Works? HDFS case study
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 class="entry-title" style="font-size:28px;font-family:Georgia, serif;font-weight:normal;line-height:30px;color:rgb(51,51,51);">
<strong>How Hadoop Works? HDFS case study</strong></h1>
<div class="entry-meta" style="color:rgb(153,153,153);font-size:11px;font-family:Arial, serif;">
<span class="meta-author">by</span> <span class="vcard author"><span class="fn"><a href="http://www.javacodegeeks.com/author/dane-dennis" rel="nofollow" title="Posts by Dane Dennis" style="text-decoration:none;color:rgb(136,136,136);">Dane Dennis</a></span></span> <span class="meta-date">on</span> <span class="date updated">April
 16th, 2013</span> <span class="meta-sep" style="color:rgb(204,204,204);">|</span> <span class="meta-author"><strong>Filed in: </strong><a href="http://www.javacodegeeks.com/java/enterprise-java" rel="nofollow" title="View all posts in Enterprise Java" style="text-decoration:none;color:rgb(136,136,136);">Enterprise
 Java</a> <span id="entry-tags" style="margin-left:3px;"><strong>Tags: </strong><a href="http://www.javacodegeeks.com/tag/apache-hadoop" rel="nofollow" style="text-decoration:none;color:rgb(136,136,136);">Apache Hadoop</a>, <a href="http://www.javacodegeeks.com/tag/apache-hadoop-hdfs" rel="nofollow" style="text-decoration:none;color:rgb(136,136,136);">Apache
 Hadoop HDFS</a>, <a href="http://www.javacodegeeks.com/tag/jarchitect" rel="nofollow" style="text-decoration:none;color:rgb(136,136,136);">JArchitect</a></span></span></div>
<div style="color:rgb(51,51,51);font-family:Arial, serif;font-size:14px;line-height:22px;width:300px;">
<ins style="display:inline-table;border:none;visibility:visible;width:300px;"></ins><ins id="aswift_1_anchor" style="display:block;border:none;visibility:visible;width:300px;"></ins></div>
<div class="entry entry-content" style="color:rgb(51,51,51);font-family:Arial, serif;font-size:14px;line-height:22px;">
<p>
The Apache Hadoop software library is a framework that allows for the distributed processing of large data sets across clusters of computers using simple programming models. It is designed to scale up from single servers to thousands of machines, each offering
 local computation and storage.</p>
<p>
Rather than rely on hardware to deliver high-availability, the library itself is designed to detect and handle failures at the application layer, so delivering a highly-available service on top of a cluster of computers, each of which may be prone to failures.
 The Hadoop library contains two major components HDFS and MapReduce, in this post we will go inside each HDFS part and discover how it works internally.<br>
 <br>
 <br>
HDFS has a master/slave architecture. An HDFS cluster consists of a single NameNode, a master server that manages the file system namespace and regulates access to files by clients. In addition, there are a number of DataNodes, usually one per node in the cluster,
 which manage storage attached to the nodes that they run on.</p>
<p>
HDFS exposes a file system namespace and allows user data to be stored in files. Internally, a file is split into one or more blocks and these blocks are stored in a set of DataNodes. The NameNode executes file system namespace operations like opening, closing,
 and renaming files and directories. It also determines the mapping of blocks to DataNodes. The DataNodes are responsible for serving read and write requests from the file system’s clients. The DataNodes also perform block creation, deletion, and replication
 upon instruction from the NameNode.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hdfsarchitecture.gif" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hdfsarchitecture" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hdfsarchitecture.gif?w=595&amp;h=411" width="595" height="411" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<h2 style="font-size:18px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
HDFS analysis</h2>
<p>
After the analysis of the Hadoop with <a href="http://www.jarchitect.com/" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);">JArchitect</a>, here’s the dependency graph of the hdfs project.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop23.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop23" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop23.png?w=595" width="455" height="436" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
To achieve its job, hdfs uses many third party libs like guava, jetty, jackson and others. The DSM (Design Structure Matrix) give us more info about the weight of using each lib.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop24.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop24" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop24.png?w=595" width="469" height="498" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
HDFS use mostly rt, hadoop-common and protobuf libraries. When external libs are used, it’s better to check if we can easily change a third party lib by another one without impacting the whole application, there are many reasons that can encourage us to change
 a third party lib. The other lib could:</p>
<ul style="list-style:square;"><li>Have more features</li><li>More performent</li><li>More secure</li></ul><p>
Let’s take the example of jetty lib and search which methods from hdfs use it directly. from m in Methods where m.IsUsing (“jetty-6.1.26″) &amp;&amp; m.ParentProject.Name==”hadoop-hdfs-0.23.6″ select new {m, m.NbBCInstructions}</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop25.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop25" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop25.png?w=595" width="376" height="285" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
Only few methods use directly jetty lib, and changing it with another one will be very easy. In general it’s very interesting to isolate when you can the using of an external lib in only some classes, it can help to maintain and evolve the project easily. Let’s
 discover now the major HDFS components:</p>
<h2 style="font-size:18px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
I-DataNode</h2>
<h4 style="font-size:13px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
Startup</h4>
<p>
To discover how to launch a data node, let’s search before all entry points of the hdfs jar. from m in Methods where m.Name.Contains(“main(String[])”) &amp;&amp; m.IsStatic select new {m, m.NbBCInstructions}</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop22.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop22" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop22.png?w=595" width="365" height="368" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
hdfs has many entries like DFSAdmin, DfSsc, Balancer and HDFSConcat. For the data node the entry point concerned is the DataNode class, and here’s what happen when its main method is invoked.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop11.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop11" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop11.png?w=595&amp;h=267" width="595" height="267" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
The main method invoke first securemain and pass the param securityresources to it, when the node is started in a not secure cluster this param is null, however in the case of starting it in a secure environment, the param is assigned with the secure resources.
 The SecureResources class contains two attributes:</p>
<ol><li>streamingSocket: secure port for data streaming to datanode.</li><li>listner: a secure listener for the web server.</li></ol><p>
And here are the methods invoked from DataNode.StartDataNode.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop3.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop3" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop3.png?w=595" width="396" height="410" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
This method initialize IPCServer,DataXceiver which is the thread for processing incoming/outgoing data stream, create data node metrics instance.</p>
<h2 style="font-size:18px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
How data is managed?</h2>
<p>
The DataNode class has an attribute named data of type FSDatasetinterface. FSDatasetinterface is an interface for the underlying storage that stores blocks for a data node. Let’s search which implementations are available in Hadoop. from t in Types where t.Implement
 (“org.apache.hadoop.hdfs.server.datanode.FSDatasetInterface”) select new {t, t.NbBCInstructions}</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop2.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop2" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop2.png?w=595" width="404" height="296" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
Hadoop provides FSDataset which manages a set of data blocks and store them on dirs. Using interfaces enforce low coupling and makes the design very flexible, however if the implementation is used instead of the interface we lose this advantage, and to check
 if interfaceDataSet is used anywhere to represent the data, let’s search for all methods using FSDataSet. from m in Methods where m.IsUsing (“org.apache.hadoop.hdfs.server.datanode.FSDataset”) select new {m, m.NbBCInstructions}</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop14.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop14" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop14.png?w=595" width="355" height="353" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
Only FSDataSet inner classes use it directly, and for all the other places the interfaceDataSet is used instead, what makes the possibility to change the dataset kind very easy. But how can I change the interfaceDataSet and give my own implementation? For that
 let’s search where the FSDataSet is created.</p>
<p>
from m in Methods let depth0 = m.DepthOfCreateA(“org.apache.hadoop.hdfs.server.datanode.FSDataset”) where depth0 == 1 select new {m, depth0}</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop26.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop26" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop26.png?w=595" width="382" height="250" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
The factory pattern is used to create the instance; the problem is if this factory create the implementation directly inside getFactory method, we have to change the Hadoop code to give it our custom DataSet manager. Let’s discover which methods are used by
 the getFactory method. from m in Methods where m.IsUsedBy (“org.apache.hadoop.hdfs.server.datanode.FSDatasetInterface$Factory.getFactory(Configuration)”) select new {m, m.NbBCInstructions}</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop27.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop27" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop27.png?w=595" width="385" height="309" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
The good news is that the factory uses the Configuration to get the class implementation, so we can only by configuration gives our custom DataSet, we can also search for all classes that can be given by configuration.</p>
<p>
from m in Methods where m.IsUsing (“org.apache.hadoop.conf.Configuration.getClass(String,Class,Class)”) select new {m, m.NbBCInstructions}</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop28.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop28" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop28.png?w=595" width="385" height="348" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
Many classes could be injected inside the Hadoop framework without changing its source code, what makes it very flexible.</p>
<h2 style="font-size:18px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
NameNode</h2>
<p>
The NameNode is the arbitrator and repository for all HDFS metadata. The system is designed in such a way that user data never flows through the NameNode. Here are some methods invoked when the name node is launched.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop5.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop5" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop5.png?w=595" width="558" height="329" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
The RPC Server is created, and the fsnamesystem is loaded, here’s a quick look to these two components:</p>
<h2 style="font-size:18px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
NameNodeRpcServer</h2>
<p>
NameNodeRpcServer is responsible for handling all of the RPC calls to the NameNode. For example when a data node is launched, it must register itself with the NameNode, the rpc server receive this request and forward it to fsnamesystem, which redirect it to
 dataNodeManager.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop8.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop8" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop8.png?w=595" width="539" height="345" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
Another example is when a block of data is received. from m in Methods where m.IsUsedBy (“org.apache.hadoop.hdfs.server.namenode.NameNodeRpcServer.blockReceived(DatanodeRegistration,String,Block[],String[])”) select new {m, m.NbBCInstructions}</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop18.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop18" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop18.png?w=595&amp;h=272" width="595" height="272" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<p>
Each rectangle in the graph is proportional to the number of bytes of code instructions, and we can observe the BlockManager.addBlock do the most of the job. What’s interesting with Haddop is that each class has a specific responsibility, and any request is
 redirected to the corresponding manager.</p>
<h2 style="font-size:18px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
FSnamesystem</h2>
<p>
HDFS supports a traditional hierarchical file organization. A user or an application can create directories and store files inside these directories. The file system namespace hierarchy is similar to most other existing file systems; one can create and remove
 files, move a file from one directory to another, or rename a file. For example here’s a dependency graph concerning the creation of a symbolic link.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop15.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop15" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop15.png?w=595" width="595" height="71" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<h2 style="font-size:18px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
HDFS Client</h2>
<p>
DFSClient can connect to a Hadoop Filesystem and perform basic file tasks. It uses the ClientProtocol to communicate with a NameNode daemon, and connects directly to DataNodes to read/write block data.<br>
Hadoop DFS users should obtain an instance of DistributedFileSystem, which uses DFSClient to handle filesystem tasks. DistributedFileSystem act as facade and redirect requests to the DFSClient class, here’s the dependency graph concerning the creation of a
 directory request.</p>
<p style="text-align:center;">
<a href="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop16.png" rel="nofollow" style="text-decoration:none;color:rgb(50,102,147);"><img class="aligncenter" alt="hadoop16" src="http://cdn.javacodegeeks.com/wp-content/uploads/2013/04/hadoop16.png?w=595" width="595" height="310" style="border:1px solid rgb(224,224,224);display:block;margin-left:auto;vertical-align:bottom;"></a></p>
<h2 style="font-size:18px;font-family:Arial, Helvetica, sans-serif;color:rgb(10,10,10);">
Conclusion:</h2>
<p>
Using frameworks as user is very interesting, but going inside this framework could give us more info suitable to understand it better, and adapt it to our needs easily. Hadoop is a powerful framework used by many companies, and most of them need to customize
 it, fortunately Hadoop is very flexible and permit us to change the behavior without changing the source code.</p>
<div class="yarpp-related"><br><strong>You might also like:</strong>
<ul style="list-style:square;"><li><a href="http://www.javacodegeeks.com/2012/05/hdfs-for-dummies.html" rel="nofollow" title="HDFS for dummies" style="text-decoration:none;color:rgb(50,102,147);">HDFS for dummies</a></li><li><a href="http://www.javacodegeeks.com/2013/02/hadoop-hangover-launch-a-hadoop-cluster-cdh4-using-apache-whirr.html" rel="nofollow" title="Hadoop Hangover: Launch a hadoop cluster CDH4 using Apache Whirr" style="text-decoration:none;color:rgb(50,102,147);">Hadoop
 Hangover: Launch a hadoop cluster CDH4 using Apache Whirr</a></li><li><a href="http://www.javacodegeeks.com/2011/05/hadoop-soft-introduction.html" rel="nofollow" title="Hadoop: A Soft Introduction" style="text-decoration:none;color:rgb(50,102,147);">Hadoop: A Soft Introduction</a></li><li><a href="http://www.javacodegeeks.com/2012/01/hadoop-modes-explained-standalone.html" rel="nofollow" title="Hadoop Modes Explained – Standalone, Pseudo Distributed, Distributed" style="text-decoration:none;color:rgb(50,102,147);">Hadoop
 Modes Explained – Standalone, Pseudo Distributed, Distributed</a></li><li><a href="http://www.javacodegeeks.com/2012/10/distributed-apache-flume-setup-with.html" rel="nofollow" title="Distributed Apache Flume Setup With an HDFS Sink" style="text-decoration:none;color:rgb(50,102,147);">Distributed
 Apache Flume Setup With an HDFS Sink</a></li></ul></div>
</div>
            </div>
                </div>
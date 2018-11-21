---
layout:     post
title:      Data-Intensive Systems:Real-time Stream Processing
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2 id="spark-stream" style="font-size:18px;color:rgb(0,27,96);">
Spark Streaming</h2>
<p style="font-size:14px;">
Spark Streaming is an interesting extension to Spark that adds support for continuous stream processing to Spark. Spark Streaming is in active development at UC Berkeley's amplab alongside the rest of the Spark project. The group recently gave a presentation
 at AmpCamp 2012 and the video gives a pretty good overview. If you'd like to follow along with the video with your own copy of the slides you can obtain them <a href="http://ampcamp.berkeley.edu/wp-content/uploads/2012/06/tathagata-das-ampcamp-2012-spark-streaming.pdf" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">here</a>.</p>
<p style="font-size:14px;">
The full paper for Spark Streaming can also be obtained from this <a href="http://www.cs.berkeley.edu/~matei/papers/2012/hotcloud_spark_streaming.pdf" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">link</a> for more detailed information.</p>
<h2 id="motivation" style="font-size:18px;color:rgb(0,27,96);">
Spark Streaming Motivation</h2>
<p style="font-size:14px;">
As mentioned on the overview page, most Internet scale systems have real time data requirements along side their growing batch processing needs. Spark Streaming is designed with the goal of providing near real time processing with approximately one second latency
 to such programs. Some of the most commonly used applications with such requirements include web site statistics/analytics, intrusion detection systems, and spam filters.</p>
<p style="font-size:14px;">
Spark Streaming seeks to support these queries while maintaining fault tolerance similar to batch systems that can recover from both outright failures and stragglers. They additionally sought to maximize cost-effectiveness at scale and provide a simple programming
 model. Finally, they recognized the importance of supporting integration with batch and ad hoc query systems</p>
<h2 style="font-size:18px;color:rgb(0,27,96);">
What is Spark?</h2>
<p style="font-size:14px;">
In order to understand Spark Streaming, it is important to have a good understanding on Spark itself. This section will include a basic architecture overview, but leaves out many of the finer details of how Spark functions. The best places for more information
 are the <a href="http://www.spark-project.org/" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">Spark Project</a> site and their <a href="http://www.cs.berkeley.edu/~matei/papers/2012/nsdi_spark.pdf" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">paper</a> which
 won the best paper award at USENIX NSDI 2012. the Spark site even includes detailed documentation for setting up your own cluster on EC2, in standalone mode, or on your own cluster if you have the hardware.</p>
<h2 id="spark-arch" style="font-size:18px;color:rgb(0,27,96);">
Spark Architecture</h2>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/sparkProgram.jpg" alt="Spark Performance" width="292" height="312" align="right">Spark is an open source cluster computing system developed in the UC Berkeley AMP Lab.  The system aims to provide fast
 computations, fast writes, and highly interactive queries.  Spark significantly outperforms Hadoop MapReduce for certain problem classes and provides a simple Ruby-like interpreter interface.  See Figure 1(right) for examples.</p>
<p style="font-size:14px;">
Spark beats Hadoop by providing primitives for in-memory cluster computing; thereby avoiding the I/O bottleneck between the individual jobs of an iterative MapReduce workflow that repeatedly performs computations on the same working set.   Spark makes use of
 the Scala language, which allows distributed datasets to be manipulated like local collections and provides fast development and testing through it’s interactive interpreter (similar to Python or Ruby).</p>
<p style="font-size:14px;">
Spark was also developed to support interactive data mining, (in addition to the obvious utility for iterative algorithms).  The system also has applicability for many general computing problems.  Their site includes example programs for File Search, In-memory
 Search, and Estimating Pi, in addition to the two examples in Figure 1.  Beyond these specifics Spark is well suited for most dataset transformation operations and computations.</p>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/SparkArchitecture.jpg" alt="Spark Architecture" width="280" height="162" align="left">Additionally, Spark is built to run on top of the Apache Mesos cluster manager.  This allows Spark to operate on a
 cluster side-by-side with Hadoop, Message Passing Interface (MPI), HyperTable, and other applications.  This allows an organization to develop hybrid workflows that can benefit from both dataflow models, with the cost, management, and interoperability concerns
 that would arise from using two independent clusters.</p>
<p style="font-size:14px;">
 </p>
<p style="font-size:14px;">
As “they” often say, there is no free lunch!  How does Spark provide it’s speed and avoid Disk I/O while retaining the attractive fault-toleranc, locality, and scalability properties of MapReduce?  The answer from the Spark team is a memory abstraction they
 call a Resilient Distributed Dataset (RDD).</p>
<p style="font-size:14px;">
Existing distributed memory abstractions such as key-value stores and databases allos fine-grained updates to mutable state.  This forces the cluster to replicate data or log updates across machinces to maintain fault tolerance.  Both of these approaches incur
 substantial overhead for a data-intensive workload.  RDDs instead have a restricted interface that only allows coarse-grained updates that apply the same operation to many data items (such as map, filter, or join).</p>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/rddfaulttolerance.jpg" alt="RDD Fault Tolerance" width="500" height="187"></p>
<p style="font-size:14px;">
This allows Spark to provide fault-tolerance through logs that simply record the transformations used to build a dataset instead of all the data.  This high-level log is called a lineage and the figure above shows a code snippet of the lineage being utilized. 
 Since parallel applications, by their very nature, typically apply the same operations to a large portion of a dataset, the coarse-grained restriction is not as limiting as it might seem.   In fact, the Spark paper showed that RDDs can efficiently express
 programming models from numerous separate frameworks including MapReduce, DryadLINQ, SQL, Pregel, and HaLoop.</p>
<p style="font-size:14px;">
Additionally, Spark also provides additional fault tolerance by allowing a user to specify a persistence condition on any transformation which causes it to immediately write to disk.  Data locality is maintained by allowing users to control data partitioning
 based on a key in each record.   (One example use of this partitioning scheme is to ensure that two datasets that will be joined are hashed in the same way.)  Spark maintains scalability beyond memory limitations for scan-based operations by storing partitions
 that grow too large on disk.</p>
<p style="font-size:14px;">
As stated previously, Spark is primarily designed to support batch transformations.  Any system that needs to make asynchronous fine grain updates to shared state (such as a datastore for a web application) should use a more traditional system such as a database,
 RAMCloud, or Piccolo. This figure gives an example of Spark's batch transformations and how the various RDDs and operations are grouped into stages.</p>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/SparkBatching.jpg" alt="Spark Batch Transformations" width="577" height="401"></p>
<h2 id="stream-arch" style="font-size:18px;color:rgb(0,27,96);">
Spark Streaming Architecture</h2>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/dstreamsTraditionalStreaming.jpg" alt="Traditional Streaming" width="750" height="324"></p>
<p style="font-size:14px;">
Most traditional streaming systems involve an update and pass methodology that handles a single record at a time. These systems can handle fault tolerance by replication (fast, 2x hardware cost) or upstream backup/buffered records(slow,~ 1x hardware cost),
 but neither approach scales to hundreds of nodes.</p>
<h2 style="font-size:18px;color:rgb(0,27,96);">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/dstreamRecoveryTimes.jpg" alt="dstream recovery times" width="651" height="330"></h2>
<h2 style="font-size:18px;color:rgb(0,27,96);">
Observations from Batch Systems</h2>
<p style="font-size:14px;">
With these limitations in mind the Spark Streaming team looked to batch systems for inspiration on how to improve scaling and noted that:</p>
<ul style="font-size:14px;"><li>batch systems always divide processing into deterministic sets that can be easily replayed</li><li>falied/slow tasks are re-run in parallel on other nodes</li><li>lower batch sizes have much lower latency</li></ul><p style="font-size:14px;">
This lead to the idea of using the same recovery mechanisms at a much smaller timescale. Spark became an ideal platform to build on because of it's in memory storage and optimization for iterative transformations on working sets.</p>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/dstreamProcessing.jpg" alt="Discretized Stream Processing" width="750" height="416"></p>
<h2 id="operators" style="font-size:18px;color:rgb(0,27,96);">
Streaming Operators</h2>
<p style="font-size:14px;">
Spark Streaming provides two types of operators to let users build streaming programs:</p>
<ul style="font-size:14px;"><li>Transformation operators, which produce a new DStream from one or more parent streams. These can<br>
be either stateless (i.e., act independently on each interval) or stateful (share data across intervals).</li><li>Output operators, which let the program write data to external systems (e.g., save each RDD to HDFS).<br></li></ul><p style="font-size:14px;">
Spark Streaming supports the same stateless transformations available in typical batch frameworks, including<br>
map, reduce, groupBy, and join (all operators supported in Spark are extended to this module).</p>
<p style="font-size:14px;">
Basic real-time streaming is a useful tool in itself, but being able the ability to look at aggregates within a given window of time is also very important for most business analytics. In order to support windowing and aggregation Spark Streaming development
 has created two evaluation models.</p>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/dstreamsWindowCalculations.jpg" alt="Aggregate Window Calculation Methods" width="360" height="208"></p>
<p style="font-size:14px;">
This figure compares the naive variant of reduceByWindow (a) with the incremental variant for invertible functions (b). Both versions compute a per-interval count only once, but the second avoids re-summing each window. Boxes denote RDDs, while arrows show
 the operations used to compute the window.</p>
<p style="font-size:14px;">
Spark also contains an operator that supports time-skewed joins that allows users to join a stream against its own RDDs from some time in the past to compute trends such as how current page view counts compare to those from five minutes ago.</p>
<p style="font-size:14px;">
For output Spark Streaming allows all RDDs to be written to a supported storage system using the save operator. Alternatively, the foreach operator can be used to process each RDD with a custom user code snippet.</p>
<h2 style="font-size:18px;color:rgb(0,27,96);">
Integration with Batch Processing</h2>
<p style="font-size:14px;">
Since Spark Streaming is built on the same fundamental data structure (RDDs) as Spark integrating streaming data with other batch processing for analysis is possible and users will be able to develop algorithms that will be potentially portable across their
 entire analytics stack.</p>
<p style="font-size:14px;">
The main shortcoming in this regard is that Spark can't currently share RDDs across process boundaries, as of Tathagata's presentation in September no solution currently exists other than writing these RDDs to storage. He did mention that a tool to enable this
 is currently in development, but gave no timeframe for a release.</p>
<h2 id="performance" style="font-size:18px;color:rgb(0,27,96);">
Spark Streaming Performance</h2>
<p style="font-size:14px;">
Since RDDs are kept in memory Spark Streaming has impressive performance on the benchmarks included in their paper and presentation. In the video from AMP Camp Tathagata Das states that they had 4GB/s (40M records/s) on 100 node cluster with less than 1 second
 latency, in addition to results listed below from smaller cluster sizes.</p>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/dstreamGrepWordCount.jpg" alt="Grep and WordCount Performance" width="681" height="346"></p>
<p style="font-size:14px;">
The ability to recreate lost data in parallel from other RDDs in memory using the lineage also give impressive performance in the presence of failures. Average interval processing times only spike for WordCount for the next thirty seconds and remain near real-time
 in that interval.</p>
<p style="font-size:14px;">
<img src="https://www.cs.duke.edu/~kmoses/cps516/images/dstreamWordCountRecovery.jpg" alt="Recovering from an injected fault in WordCount" width="689" height="253"></p>
<h2 style="font-size:18px;color:rgb(0,27,96);">
Comparison To Other Systems</h2>
<div align="center" style="font-size:14px;">
<table width="500" border="1"><tbody><tr><th scope="col" style="font-size:14px;">System</th>
<th scope="col" style="font-size:14px;">Performance</th>
</tr><tr><td style="font-size:14px;">Storm (Twitter)</td>
<td style="font-size:14px;">10,000 records/s/node</td>
</tr><tr><td style="font-size:14px;">Spark Streaming</td>
<td style="font-size:14px;">400,000 records/s/node</td>
</tr><tr><td style="font-size:14px;">Apache S4</td>
<td style="font-size:14px;">7,000 records/s/node</td>
</tr><tr><td style="font-size:14px;">Other Commericial Systems</td>
<td style="font-size:14px;">100,000 records/s/node</td>
</tr></tbody></table></div>
<p style="font-size:14px;">
The main reason cited by Tathagata for Spark's performance gain over Storm is the aggregation of small records that occurs through the mechanics of RDDs. These results are meant as general comparison metrics, individual programs will have varying performance
 as shown in the Grep and WordCount benchmarks above.</p>
<h2 style="font-size:18px;color:rgb(0,27,96);">
Where Can I Get It?</h2>
<p style="font-size:14px;">
The Spark Streaming source code is not publicly available, but it's release is expected in the near future. The Spark community has an active Google group, so this <a href="https://groups.google.com/forum/#!searchin/spark-users/spark%2420streaming/spark-users/chchejWLmEI/ndP5wos5XkYJ" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">thread</a> will
 be a good place to catch the release announcement. The main <a href="http://www.spark-project.org/" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">Spark Project</a> site will also likely have a release statement. No specific date has
 been set, but based on developer comments in the thread and the fact that Spark 0.6.0 is out on schedule; Spark Streaming might be available as early as mid-November.</p>
<h2 id="critique" style="font-size:18px;color:rgb(0,27,96);">
Critical Reviews</h2>
<p style="font-size:14px;">
Since Spark Streaming has yet to be released it's difficult to find much about the project anywhere other than their main website, but I did come across a critical but fair review from Marteen Ectors at Telruptive.com. The article talks about Spark in general,
 but highlights some practical challenges that may limit user adoption if not addressed.</p>
<p style="font-size:14px;">
He first highlights Spark's use of Scala as the primary API language: "Scala and has a very straight forward syntax to run applications from the command line or via compiled code. The possibilities to run iterative operations over large datasets or very compute
 intensive operations in parallel, make it ideal for big data analytics and distributed machine learning."</p>
<p style="font-size:14px;">
From there he highlights the challenges involved in actually integrating Spark, Mesos, and Hadoop on a single cluster. Since Mesos is also in early development and just recently became an Apache Incubator project, the code and documentation are in a state of
 flux. He points out that Spark only requires HDFS and that a full Hadoop installation should not be made necessary unless it is desired.</p>
<p style="font-size:14px;">
He also points to the fact that Storm can essentially be installed on EC2 with one click and that complex installation requirements will always deter users. Thankfully, it seems the Spark team took this criticism to heart (or realized the problem on their own).
 As I mentioned at the top of the page, the freshly released Spark 0.6.0 includes robust documentation including an EC2 deployment guide and an automated EC2 installation script.</p>
<p style="font-size:14px;">
He also makes an interesting point about adding an integration SDK and points out that strong integration with a system like Cassandra could yield impressive scaling and performance due to improved write speed/throughput, especially for small RDDs. HBase would
 likely be a good option for similar extensions.</p>
<h2 id="references" style="font-size:18px;color:rgb(0,27,96);">
References</h2>
<p style="font-size:14px;">
Below is a list of all the references I used in writing this system survey in order of their perceived usefulness</p>
<ol style="font-size:14px;"><li>Spark Streaming AMP Camp Video: <a href="http://www.youtube.com/watch?v=SU6Pn9g0YXE" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">YouTube</a></li><li>Spark &amp; Spark Streaming Papers and Slides: <a href="http://www.cs.berkeley.edu/~matei/" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">html</a></li><li>Spark Streaming - Google Groups: <a href="https://groups.google.com/forum/#!topic/spark-users/chchejWLmEI/discussion" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">html</a></li><li>Spark. <a href="http://www.spark-project.org/" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">html</a></li><li>Hadoop for Real-Time(telruptive.com): <a href="http://telruptive.com/2012/08/15/hadoop-for-real-time-spark-shark-spark-streaming-bagel-etc-will-be-2012s-new-buzzwords/" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">html</a></li><li>Apache Mesos.  <a href="http://www.mesosproject.org/" rel="nofollow" style="color:rgb(204,204,204);text-decoration:none;">html</a></li></ol><div><span style="font-size:14px;">Ref: <a href="https://www.cs.duke.edu/~kmoses/cps516/dstream.html" rel="nofollow">https://www.cs.duke.edu/~kmoses/cps516/dstream.html</a></span></div>
            </div>
                </div>
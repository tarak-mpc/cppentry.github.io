---
layout:     post
title:      HDFS Federation设计动机与基本原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-family:'Lucida Grande', 'Lucida Sans Unicode', Verdana, sans-serif;line-height:24px;background-color:#f7f3ed;">
</span></p><p style="font-style:normal;text-indent:2.1em;">HDFS Federation是Hadoop最新发布版本Hadoop-0.23.0中为解决HDFS单点故障而提出的namenode水平扩展方案。该方案允许HDFS创建多个namespace以提高集群的扩展性和隔离性。本篇文章主要介绍了HDFS Federation的设计动机和基本原理。</p>
<h3 style="font-style:normal;font-size:14px;color:#333333;font-family:Arial, Helvetica, sans-serif;line-height:26px;">1. 当前HDFS概况</h3>
<h4 style="font-size:16px;">1.1 当前HDFS架构</h4>
<p style="font-style:normal;text-indent:2.1em;">当前HDFS包含两层结构：</p>
<p style="font-style:normal;text-indent:2.1em;">（1）<strong> Namespace</strong> 管理目录，文件和数据块。它支持常见的文件系统操作，如创建文件，修改文件，删除文件等。</p>
<p style="font-style:normal;text-indent:2.1em;">（2） <strong>Block Storage</strong>有两部分组成：</p>
<p style="font-style:normal;text-indent:2.1em;"><strong>Block Management</strong>维护集群中datanode的基本关系，它支持数据块相关的操作，如：创建数据块，删除数据块等，同时，它也会管理副本的复制和存放。</p>
<p style="font-style:normal;text-indent:2.1em;"><strong>Physical Storage</strong>存储实际的数据块并提供针对数据块的读写服务。</p>
<p style="font-style:normal;text-indent:2.1em;">【Block Storage的这两部分分别在namenode和datanode上实现，所以该模块由namenode和datanode分工完成】</p>
<p style="font-style:normal;text-indent:2.1em;"><img class="aligncenter size-full wp-image-1280" style="margin-left:auto;" title="old-hdfs-architecture" src="http://dongxicheng.org/wp-content/uploads/2011/12/old-hdfs-architecture.jpg" alt="" width="283" height="221"></p>
<p style="font-style:normal;text-indent:2.1em;">当前HDFS架构只允许整个集群中存在一个namespace，而该namespace被仅有的一个namenode管理。这个架构使得HDFS非常容易实现，但是，它（见上图）在具体实现过程中会出现一些模糊点，进而导致了很多局限性（下面将要详细说明），当然这些局限性只有在拥有大集群的公司，像baidu，腾讯等出现。</p>
<h4 style="font-size:16px;">1.2 当前HDFS局限性</h4>
<h5 style="font-size:13px;">【Block Storage和namespace高耦合】</h5>
<p style="font-style:normal;text-indent:2.1em;">当前namenode中的namespace和block management的结合使得这两层架构耦合在一起，难以让其他可能namenode实现方案直接使用block storage。</p>
<h5 style="font-size:13px;">【namenode扩展性】</h5>
<p style="font-style:normal;text-indent:2.1em;">HDFS的底层存储是可以水平扩展的（解释：底层存储指的是datanode，当集群存储空间不够时，可简单的添加机器已进行水平扩展），但namespace不可以。当前的namespace只能存放在单个namenode上，而namenode在内存中存储了整个分布式文件系统中的元数据信息，这限制了集群中数据块，文件和目录的数目。</p>
<h5 style="font-size:13px;">【性能】</h5>
<p style="font-style:normal;text-indent:2.1em;">文件操作的性能制约于单个namenode的吞吐量，单个namenode当前仅支持约60K的task，而下一代Apache MapReduce将支持多余100K的并发任务，这隐含着要支持多个namenode。</p>
<h5 style="font-size:13px;">【隔离性】</h5>
<p style="font-style:normal;text-indent:2.1em;">现在大部分公司的集群都是共享的，每天有来自不同group的不同用户提交作业。单个namenode难以提供隔离性，即：某个用户提交的负载很大的job会减慢其他用户的job，单一的namenode难以像HBase按照应用类别将不同作业分派到不同namenode上。</p>
<h3 style="font-style:normal;font-size:14px;color:#333333;font-family:Arial, Helvetica, sans-serif;line-height:26px;">2. HDFS Federation</h3>
<h4 style="font-size:16px;">2.1  为什么采用Federation</h4>
<p style="font-style:normal;text-indent:2.1em;">采用Federation的最主要原因是简单，Federation能够快速的解决了大部分单Namenode的问题。</p>
<p style="font-style:normal;text-indent:2.1em;">Federation 整个核心设计实现大概用了4个月。大部分改变是在Datanode、Config和Tools，而Namenode本身的改动非常少，这样 Namenode原先的鲁棒性不会受到影响。这使得该方案与之前的HDFS版本兼容。</p>
<h4 style="font-size:16px;">2.2  Federation架构</h4>
<p style="font-style:normal;text-indent:2.1em;"><img class="aligncenter size-full wp-image-1281" style="margin-left:auto;" title="hdfs-federation" src="http://dongxicheng.org/wp-content/uploads/2011/12/hdfs-federation.jpg" alt="" width="539" height="397"></p>
<p style="font-style:normal;text-indent:2.1em;">为了水平扩展namenode，federation使用了多个独立的namenode/namespace。这些namenode之间是联合的，也就是说，他们之间相互独立且不需要互相协调，各自分工，管理自己的区域。分布式的datanode被用作通用的数据块存储存储设备。每个datanode要向集群中所有的namenode注册，且周期性地向所有namenode发送心跳和块报告，并执行来自所有namenode的命令。</p>
<p style="font-style:normal;text-indent:2.1em;">一个block pool由属于同一个namespace的数据块组成，每个datanode可能会存储集群中所有block pool的数据块。</p>
<p style="font-style:normal;text-indent:2.1em;">每个block pool内部自治，也就是说各自管理各自的block，不会与其他block pool交流。一个namenode挂掉了，不会影响其他namenode。</p>
<p style="font-style:normal;text-indent:2.1em;">某个namenode上的namespace和它对应的block pool一起被称为namespace volume。它是管理的基本单位。当一个namenode/nodespace被删除后，其所有datanode上对应的block pool也会被删除。当集群升级时，每个namespace volume作为一个基本单元进行升级。</p>
<h4 style="font-size:16px;">2.3  Federation关键技术点</h4>
<h5 style="font-size:13px;">【命名空间管理】</h5>
<p style="font-style:normal;text-indent:2.1em;">Federation中存在多个命名空间，如何划分和管理这些命名空间非常关键。在Federation中并采用“文件名hash”的方法，因为该方法的locality非常差，比如：查看某个目录下面的文件，如果采用文件名hash的方法存放文件，则这些文件可能被放到不同namespace中，HDFS需要访问所有namespace，代价过大。为了方便管理多个命名空间，HDFS Federation采用了经典的Client Side Mount Table。</p>
<p style="font-style:normal;text-indent:2.1em;"><img class="aligncenter size-full wp-image-1282" style="margin-left:auto;" title="client-side-mount-table" src="http://dongxicheng.org/wp-content/uploads/2011/12/client-side-mount-table.jpg" alt="" width="280" height="279"></p>
<p style="font-style:normal;text-indent:2.1em;">如上图所示，下面四个深色三角形代表一个独立的命名空间，上方浅色的三角形代表从客户角度去访问的子命名空间。各个深色的命名空间Mount到浅色的表中，客户可以访问不同的挂载点来访问不同的命名空间，这就如同在Linux系统中访问不同挂载点一样。这就是HDFS Federation中命名空间管理的基本原理：将各个命名空间挂载到全局mount-table中，就可以做将数据到全局共享；同样的命名空间挂载到个人的mount-table中，这就成为应用程序可见的命名空间视图。</p>
<p style="font-style:normal;text-indent:2.1em;">更多关于Client Side Mount Table的原理，可参考：</p>
<p style="font-style:normal;text-indent:2.1em;">Plan 9:<a style="color:#0054b3;text-decoration:none;" href="http://portal.acm.org/citation.cfm?id=506413&amp;dl=GUIDE&amp;coll=GUIDE&amp;CFID=82715774&amp;CFTOKEN=20109739" rel="nofollow">http://portal.acm.org/citation.cfm?id=506413&amp;dl=GUIDE&amp;coll=GUIDE&amp;CFID=82715774&amp;CFTOKEN=20109739</a></p>
<p style="font-style:normal;text-indent:2.1em;">The Per-Process View of Naming and Remote Execution:<a style="color:#0054b3;text-decoration:none;" href="http://portal.acm.org/citation.cfm?id=613822" rel="nofollow">http://portal.acm.org/citation.cfm?id=613822</a></p>
<p style="font-style:normal;text-indent:2.1em;">The Spring system:<a style="color:#0054b3;text-decoration:none;" href="http://www2.informatik.hu-berlin.de/~mint/Library/Spring/spring-namingpolicy.ps" rel="nofollow">http://www2.informatik.hu-berlin.de/~mint/Library/Spring/spring-namingpolicy.ps</a></p>
<h5 style="font-size:13px;">【Block Pool管理】</h5>
<p style="font-style:normal;text-indent:2.1em;">具体可参考文献【首要参考资料】之【2】【3】。</p>
<h4 style="font-size:16px;">2.4  主要优点</h4>
<h5 style="font-size:13px;">【扩展性和隔离性】</h5>
<p style="font-style:normal;text-indent:2.1em;">支持多个namenode水平扩展整个文件系统的namespace。可按照应用程序的用户和种类分离namespace volume，进而增强了隔离性。</p>
<h5 style="font-size:13px;">【通用存储服务】</h5>
<p style="font-style:normal;text-indent:2.1em;">Block Pool抽象层为HDFS的架构开启了创新之门。分离block storage layer使得：</p>
<p style="font-style:normal;text-indent:2.1em;">&lt;1&gt; 新的文件系统（non-HDFS）可以在block storage上构建</p>
<p style="font-style:normal;text-indent:2.1em;">&lt;2&gt; 新的应用程序（如HBase）可以直接使用block storage层</p>
<p style="font-style:normal;text-indent:2.1em;">&lt;3&gt; 分离的block storage层为将来完全分布式namespace打下基础</p>
<h5 style="font-size:13px;">【设计简单】</h5>
<p style="font-style:normal;text-indent:2.1em;">Federation 整个核心设计实现大概用了4个月。大部分改变是在Datanode、Config和Tools中，而Namenode本身的改动非常少，这样 Namenode原先的鲁棒性不会受到影响。虽然这种实现的扩展性比起真正的分布式的Namenode要小些，但是可以迅速满足需求，另外Federation具有良好的向后兼容性，已有的单Namenode的部署配置不需要任何改变就可以继续工作</p>
<h3 style="font-style:normal;font-size:14px;color:#333333;font-family:Arial, Helvetica, sans-serif;line-height:26px;">3. HDFS Federation不足</h3>
<h5 style="font-size:13px;">【单点故障问题】</h5>
<p style="font-style:normal;text-indent:2.1em;">HDFS Federation并没有完全解决单点故障问题。虽然namenode/namespace存在多个，但是从单个namenode/namespace看，仍然存在单点故障：如果某个namenode挂掉了，其管理的相应的文件便不可以访问。Federation中每个namenode仍然像之前HDFS上实现一样，配有一个secondary namenode，以便主namenode挂掉一下，用于还原元数据信息。</p>
<h5 style="font-size:13px;">【负载均衡问题】</h5>
<p style="font-style:normal;text-indent:2.1em;">HDFS Federation采用了Client Side Mount Table分摊文件和负载，该方法更多的需要人工介入已达到理想的负载均衡。</p>
<h3 style="font-style:normal;font-size:14px;color:#333333;font-family:Arial, Helvetica, sans-serif;line-height:26px;">4. 首要参考资料</h3>
<p style="font-style:normal;text-indent:2.1em;">（1） HDFS Federation ppt in Apache Hadoop India Summit 2011：</p>
<div id="__ss_7386931" style="width:425px;">
<strong style="margin-left:0px;"><a style="color:#0054b3;text-decoration:none;" title="Federated HDFS" href="http://www.slideshare.net/huguk/hdfs-federation-hadoop-summit2011" rel="nofollow">Federated HDFS</a></strong>


</div>
<p style="font-style:normal;text-indent:2.1em;">（2）Scaling HDFS cluster using Namenode Federation：</p>
<p style="font-style:normal;text-indent:2.1em;"><a style="color:#0054b3;text-decoration:none;" href="https://issues.apache.org/jira/secure/attachment/12453067/high-level-design.pdf" rel="nofollow">https://issues.apache.org/jira/secure/attachment/12453067/high-level-design.pdf</a></p>
<p style="font-style:normal;text-indent:2.1em;">（3）Apache Hadoop— The Scalability Update：</p>
<p style="font-style:normal;text-indent:2.1em;"><a style="color:#0054b3;text-decoration:none;" href="http://www.usenix.org/publications/login/2011-06/openpdfs/Shvachko.pdf" rel="nofollow">http://www.usenix.org/publications/login/2011-06/openpdfs/Shvachko.pdf</a></p>
<h3 style="font-style:normal;font-size:14px;color:#333333;font-family:Arial, Helvetica, sans-serif;line-height:26px;">5. 第二参考资料</h3>
<p style="font-style:normal;text-indent:2.1em;">（1）HDFS Federation:</p>
<p style="font-style:normal;text-indent:2.1em;"><a style="color:#0054b3;text-decoration:none;" href="http://hadoop.apache.org/common/docs/r0.23.0/index.html" rel="nofollow">http://hadoop.apache.org/common/docs/r0.23.0/index.html</a></p>
<p style="font-style:normal;text-indent:2.1em;">（2）HDFS scalability with multiple namenodes：</p>
<p style="font-style:normal;text-indent:2.1em;"><a style="color:#0054b3;text-decoration:none;" href="https://issues.apache.org/jira/browse/HDFS-1052" rel="nofollow">https://issues.apache.org/jira/browse/HDFS-1052</a></p>
<p style="font-style:normal;text-indent:2.1em;">（3）A client side mount table to give per-application/per-job file system view：</p>
<p style="font-style:normal;text-indent:2.1em;"><a style="color:#0054b3;text-decoration:none;" href="https://issues.apache.org/jira/browse/HADOOP-7257" rel="nofollow">https://issues.apache.org/jira/browse/HADOOP-7257</a></p>
<p style="font-style:normal;text-indent:2.1em;">（4）An Introduction to HDFS Federation：</p>
<p style="font-style:normal;text-indent:2.1em;"><a style="color:#0054b3;text-decoration:none;" href="http://hortonworks.com/an-introduction-to-hdfs-federation/" rel="nofollow">http://hortonworks.com/an-introduction-to-hdfs-federation/</a></p>
<p style="font-style:normal;text-indent:2.1em;">（5）HDFS Federation(HDFS 联盟)介绍：</p>
<p style="font-style:normal;text-indent:2.1em;"><a style="color:#0054b3;text-decoration:none;" href="http://blog.csdn.net/strongerbit/article/details/7013221" rel="nofollow">http://blog.csdn.net/strongerbit/article/details/7013221</a></p>
<div style="margin-left:0px;font-style:italic;">
<p style="font-style:normal;text-indent:2.1em;"><strong>原创文章，转载请注明：</strong> 转载自<a style="color:#0054b3;text-decoration:none;" href="http://dongxicheng.org/" rel="nofollow">董的博客</a></p>
<p style="font-style:normal;text-indent:2.1em;"><strong>本文链接地址:</strong> <a style="color:#0054b3;text-decoration:none;" href="http://dongxicheng.org/mapreduce/hdfs-federation-introduction/" rel="nofollow">http://dongxicheng.org/mapreduce/hdfs-federation-introduction/</a></p>
</div>
            </div>
                </div>
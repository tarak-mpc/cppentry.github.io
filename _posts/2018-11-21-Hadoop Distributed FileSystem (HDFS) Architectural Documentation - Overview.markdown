---
layout:     post
title:      Hadoop Distributed FileSystem (HDFS) Architectural Documentation - Overview
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>
<p><span style="font-size:16px;">Hadoop Distributed FileSystem (HDFS) Architectural Documentation - Overview</span></p>
<p><span style="font-size:16px;">全文地址：http://kazman.shidler.hawaii.edu/ArchDoc.html<br></span></p>
</div>
<p><span style="font-size:16px;"><a name="_Toc291720189"> 3      Overview of the HDFS Architecture</a></span></p>
<p><span style="font-size:16px;">Thissection provides a quick overview of the architecture of HDFS. Thematerial in here is elaborated in other sections. The figure belowgives a run-time view of the architecture showing three types ofaddress spaces: the application,
 the NameNode and the DataNode. Anessential portion of HDFS is that there are multiple instances ofDataNode.<br></span></p>
<p><span style="font-size:16px;"><img alt="HDFS Runtime View" title="HDFS Runtime View" src="http://kazman.shidler.hawaii.edu/RuntimeView.jpg"><br></span></p>
<p><span style="font-size:16px;">The application incorporates the HDFSclient library into its addressspace. Theclient library manages all communication from the application to theNameNodeand the DataNode. An HDFS cluster consists of a single NameNode—amaster
 serverthat manages the file system namespace and regulates access to files byclients. In addition, there are a number of DataNodes, usually one percomputernode in the cluster, which manage storage attached to the nodes thatthey runon.
</span></p>
<p><span style="font-size:16px;">The NameNode and DataNode are pieces of softwaredesigned torun on commodity machines. These machines typically run a GNU/Linuxoperatingsystem (OS). HDFS is built using the Java language; any machine thatsupportsJava can run the
 NameNode or the DataNode software. Usage of the Javalanguagemeans that HDFS can be deployed on a wide range of machines. A typicaldeployment has a dedicated machine that runs only the NameNodesoftware. Eachof the other machines in the cluster runs one instance
 of the DataNodesoftware. The architecture does not preclude running multiple DataNodeson thesame machine but in a real deployment that is rarely the case.</span></p>
<h2><span style="font-size:16px;"><a name="_Toc291720190">3.1      HDFSFiles</a></span></h2>
<p><span style="font-size:16px;">There is a distinction between an HDFS file and anative(Linux) file on the host computer. A computer in an HDFS installationis(typically) allocated to one NameNode or one DataNode. Each computerhas itsown file system and information
<em>about</em>an HDFS file—the metadata—is managed by the NameNode and persistentinformationis stored in the NameNode’s host filesystem. Theinformation
<em>contained</em> in an HDFS fileis managed by a DataNode and stored on the DataNode’shost computer file system.</span></p>
<p><span style="font-size:16px;">HDFS exposes a file system namespace and allowsuser data tobe stored in HDFS files. An HDFS file consists of a number of blocks.Eachblock is typically 64MByes. Each block is replicated some specifiednumber oftimes. The replicas
 of the blocks are stored on different DataNodeschosen to reflect loading on a DataNode aswell as toprovide both speed in transfer and resiliency in case of failure of arack. See<span style="background:#FFFF00 none repeat scroll 0% 0%;">BlockAllocation</span>for
 a description of the allocation algorithm.</span></p>
<p><span style="font-size:16px;">A standard directory structure is used in HDFS.That is,HDFS files exist in directories that may in turn be sub-directories ofotherdirectories, and so on. There is no concept of a current directorywithin HDFS.HDFS files are referred
 to by their fully qualified name which is aparameterof many of the elements of the interaction between the Client and theotherelements of the HDFS architecture.        </span></p>
<p><span style="font-size:16px;">The NameNodeexecutes HDFS file system namespace operationslike opening, closing, and renaming files and directories. It alsodeterminesthe mapping of blocks to DataNodes. The list of HDFS files belonging toeachblock, the current
 location of the block replicas on the DataNodes,the state of the file, and the access control information is themetadata forthe cluster and is managed by the NameNode.</span></p>
<p><span style="font-size:16px;">The DataNodes are responsible for serving read andwriterequests from the HDFS file system’s clients. The DataNodes alsoperform block replicacreation, deletion, and replication upon instruction from the NameNode.TheDataNodes are
 the arbiter of the state of the replicates and theyreport thisto the NameNode.</span></p>
<p><span style="font-size:16px;">The existence of a single NameNode in a clustergreatlysimplifies the architecture of the system. The NameNode is thearbitrator andrepository for all HDFS metadata. The client sends data directly to andreadsdirectly from DataNodes
 so that client data never flows through theNameNode.</span></p>
<h2><span style="font-size:16px;">3.2      <a name="BlockAllocation"></a>Block Allocation</span></h2>
<p><span style="font-size:16px;">Each block is replicated some number of times—thedefaultreplication factor for HDFS is three. When addBlock() isinvoked, spaceis allocated for each replica. Each replica is allocated on a differentDataNode. The algorithm for
 performing thisallocationattempts to balance performance and reliability. This is done byconsideringthe following factors:</span></p>
<ul type="disc"><li><span style="font-size:16px;">The dynamic loadon the set of DataNodes.  Preference is given to more lightly loaded DataNodes.</span></li></ul><p><span style="font-size:16px;">·        The location of the DataNodes.Communication between two nodes in different racks has to go throughswitches.In most cases, network bandwidth between machines in the same rack isgreaterthan network bandwidth between machines
 in different racks.</span></p>
<p><span style="font-size:16px;">·        For the common case, when thereplication factoris three, HDFS’s placement policy is to put one replica on one node inthelocal rack, another on a node in a different (remote) rack, and thelast on adifferent node in the
 same remote rack. This policy cuts the inter-rackwritetraffic which generally improves write performance. The chance of rackfailureis far less than that of a node failure; therefore this co-locationpolicy doesnot adversely impact data reliability and availability
 guarantees.However, itdoes reduce the aggregate network bandwidth used when reading datasince ablock is placed in only two unique racks rather than three. With thispolicy,the replicas of a file do not evenly distribute across the racks. Onethird ofreplicas
 are on one node on some rack; the other two thirds of replicasare on distinctnodes one a different rack. This policy improves write performancewithoutcompromising data reliability or read performance.</span></p>
<p><span style="font-size:16px;">Thefigure below shows how blocks are replicated on different DataNodes.
<br></span></p>
<p><span style="font-size:16px;"><img alt="HDFS Block Replication" title="HDFS Block Replication" src="http://kazman.shidler.hawaii.edu/BlockReplication.gif"><br></span></p>
<p><span style="font-size:16px;">Blocks are linked to the file through INode.Each block is given a timestamp that is used to determine whether areplica iscurrent. We discuss this further in the
<span style="background:#FFFF00 none repeat scroll 0% 0%;">BlockandReplicaManagement</span> section.</span></p>
<br>            </div>
                </div>
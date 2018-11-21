---
layout:     post
title:      Notes for Hadoop the definitive guide
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>1.       Introduction to HDFS</h1>
<h2>1.1.     HDFS Concepts</h2>
<h3>1.1.1.      Blocks</h3>
<p>l         HDFS too has the concept of a block, but it is a much larger unit 64 MB by default.</p>
<p>l         Like in a filesystem for a single disk, files in HDFS are broken into block-sized chunks, which are stored as independent units.
</p>
<p>l         Unlike a filesystem for a single disk, a file in HDFS that is smaller than a single block does not occupy a full block’s worth of underlying storage.</p>
<p></p>
<h3>1.1.2.      Namenodes and Datanodes</h3>
<p>l         The namenode manages the filesystem namespace. </p>
<p>n         It maintains the filesystem tree and the metadata for all the files and directories in the tree.</p>
<p>n         This information is stored persistently on the local disk in the form of two files: the namespace image and the edit log.
</p>
<p>n         The namenode also knows the datanodes on which all the blocks for a given file are located, however, it does not store block locations persistently, since this information is reconstructed from datanodes when the system starts.</p>
<p>l         Datanodes are the work horses of the filesystem. </p>
<p>n         They store and retrieve blocks when they are told to (by clients or the namenode)</p>
<p>n         They report back to the namenode periodically with lists of blocks that they are storing.</p>
<p>l         secondary namenode</p>
<p>n         It does not act as a namenode. </p>
<p>n         Its main role is to periodically merge the namespace image with the edit log to prevent the edit log from becoming too large.
</p>
<p>n         It keeps a copy of the merged name space image, which can be used in the event of the namenode failing.</p>
<p> </p>
<h4>Namenode directory structure</h4>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image002_2.jpg" rel="nofollow"><img title="clip_image002" alt="clip_image002" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image002_thumb.jpg" border="0" height="78" width="223"></a></p>
<p>l         The VERSION file is a Java properties file that contains information about the version of HDFS that is running</p>
<p>n         The layoutVersion is a negative integer that defines the version of HDFS’s persistent data structures.</p>
<p>n         The namespaceID is a unique identifier for the filesystem, which is created when  the filesystem is first formatted.</p>
<p>n         The cTime property marks the creation time of the namenode’s storage.</p>
<p>n         The storageType indicates that this storage directory contains data structures for a namenode.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image004_2.jpg" rel="nofollow"><img title="clip_image004" alt="clip_image004" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image004_thumb.jpg" border="0" height="105" width="240"></a></p>
<p> </p>
<h4>The filesystem image and edit log</h4>
<p>l         When a filesystem client performs a write operation, it is first recorded in the edit log.
</p>
<p>l         The namenode also has an in-memory representation of the filesystem metadata, which it updates after the edit log has been modified.</p>
<p>l         The edit log is flushed and synced after every write before a success code is returned to the client.</p>
<p>l         The fsimage file is a persistent checkpoint of the filesystem metadata. it is not updated for every filesystem write operation.</p>
<p>l         If the namenode fails, then the latest state of its metadata can be reconstructed by loading the fsimage from disk into memory, then applying each of the operations in the edit log.</p>
<p>l         This is precisely what the namenode does when it starts up.</p>
<p>l         The fsimage file contains a serialized form of all the directory and file inodes in the filesystem.</p>
<p>l         The secondary namenode is to produce checkpoints of the primary’s in-memory filesystem metadata.
</p>
<p>l         The checkpointing process proceeds as follows :</p>
<p>n         The secondary asks the primary to roll its edits file, so new edits go to a new file.</p>
<p>n         The secondary retrieves fsimage and edits from the primary (using HTTP GET).</p>
<p>n         The secondary loads fsimage into memory, applies each operation from edits, then creates a new consolidated fsimage file.</p>
<p>n         The secondary sends the new fsimage back to the primary (using HTTP POST).</p>
<p>n         The primary replaces the old fsimage with the new one from the secondary, and the old edits file with the new one it started in step 1. It also updates the fstime file to record the time that the checkpoint was taken.</p>
<p>n         At the end of the process, the primary has an up-to-date fsimage file, and a shorter edits file.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image006_2.jpg" rel="nofollow"><img title="clip_image006" alt="clip_image006" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image006_thumb.jpg" border="0" height="588" width="557"></a></p>
<p> </p>
<h4>Secondary namenode directory structure</h4>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image008_2.jpg" rel="nofollow"><img title="clip_image008" alt="clip_image008" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image008_thumb.jpg" border="0" height="157" width="374"></a></p>
<h4>Datanode directory structure</h4>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image010_2.jpg" rel="nofollow"><img title="clip_image010" alt="clip_image010" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image010_thumb.jpg" border="0" height="220" width="306"></a></p>
<p>l         A datanode’s VERSION file</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image012_2.jpg" rel="nofollow"><img title="clip_image012" alt="clip_image012" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image012_thumb.jpg" border="0" height="115" width="419"></a></p>
<p>l         The other files in the datanode’s current storage directory are the files with the blk_ prefix.
</p>
<p>n         There are two types: the HDFS blocks themselves (which just consist of the file’s raw bytes) and the metadata for a block (with a .meta suffix).
</p>
<p>n         A block file just consists of the raw bytes of a portion of the file being stored;
</p>
<p>n         the metadata file is made up of a header with version and type information, followed by a series of checksums for sections of the block.</p>
<p>l         When the number of blocks in a directory grows to a certain size, the datanode creates a new subdirectory in which to place new blocks and their accompanying metadata.</p>
<p> </p>
<h2>1.2.     Data Flow</h2>
<h3>1.2.1.      Anatomy of a File Read</h3>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image014_2.jpg" rel="nofollow"><img title="clip_image014" alt="clip_image014" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image014_thumb.jpg" border="0" height="338" width="557"></a></p>
<p>l         The client opens the file it wishes to read by calling open() on the FileSystem object (step 1).</p>
<p>l         DistributedFileSystem calls the namenode, using RPC, to determine the locations of the blocks for the first few blocks in the file (step 2).
</p>
<p>l         For each block, the namenode returns the addresses of the datanodes that have a copy of that block.
</p>
<p>l         The datanodes are sorted according to their proximity to the client.</p>
<p>l         The DistributedFileSystem returns a FSDataInputStream to the client for it to read data from.</p>
<p>l         The client then calls read() on the stream (step 3). </p>
<p>l         DFSInputStream connects to the first (closest) datanode for the first block in the file.
</p>
<p>l         Data is streamed from the datanode back to the client (step 4). </p>
<p>l         When the end of the block is reached, DFSInputStream will close the connection to the datanode, then find the best datanode for the next block (step 5).</p>
<p>l         When the client has finished reading, it calls close() on the FSDataInputStream (step 6).</p>
<p>l         During reading, if the client encounters an error while communicating with a datanode, then it will try the next closest one for that block.
</p>
<p>l         It will also remember datanodes that have failed so that it doesn’t needlessly retry them for  later blocks.
</p>
<p>l         The client also verifies checksums for the data transferred to it from the datanode. If a corrupted block is found, it is reported to the namenode.</p>
<p></p>
<h3>1.2.2.      Anatomy of a File Write</h3>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image016_2.jpg" rel="nofollow"><img title="clip_image016" alt="clip_image016" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image016_thumb.jpg" border="0" height="341" width="557"></a></p>
<p> </p>
<p>l         The client creates the file by calling create() (step 1). </p>
<p>l         DistributedFileSystem makes an RPC call to the namenode to create a new file in the filesystem’s namespace, with no blocks associated with it (step 2).
</p>
<p>l         The namenode performs various checks to make sure the file doesn’t already exist, and that the client has the right permissions to create the file. If these checks pass, the namenode makes a record of the new file; otherwise, file creation fails
 and the client is thrown an IOException. </p>
<p>l         The DistributedFileSystem returns a FSDataOutputStream for the client to start writing data to.</p>
<p>l         As the client writes data (step 3), DFSOutputStream splits it into packets, which it writes to an internal queue, called the data queue.
</p>
<p>l         The data queue is consumed by the Data Streamer, whose responsibility it is to ask the namenode to allocate new blocks by picking a list of suitable datanodes to store the replicas. The list of datanodes forms apipeline.</p>
<p>l         The DataStreamer streams the packets to the first datanode in the pipeline, which stores the packet and forwards it to the second datanode in the pipeline. Similarly, the second datanode stores the packet and forwards it to the third (and last)
 datanode in the pipe line (step 4).</p>
<p>l         DFSOutputStream also maintains an internal queue of packets that are waiting to be acknowledged by datanodes, called the ack queue. A packet is removed from the ack queue only when it has been acknowledged by all the datanodes in the pipeline (step
 5).</p>
<p>l         If a datanode fails while data is being written to it, </p>
<p>n         First the pipeline is closed, and any packets in the ack queue are added to the front of the data queue.
</p>
<p>n         The current block on the good datanodes is given a new identity by the namenode, so that the partial block on the failed datanode will be deleted if the failed data node recovers later on.
</p>
<p>n         The failed datanode is removed from the pipeline and the remainder of the block’s data is written to the two good datanodes in the pipeline.
</p>
<p>n         The namenode notices that the block is under-replicated, and it arranges for a further replica to be created on another node.
</p>
<p>l         When the client has finished writing data it calls close() on the stream (step 6). This action flushes all the remaining packets to the datanode pipeline and waits for acknowledgments before contacting the namenode to signal that the file is complete
 (step7). </p>
<p> </p>
<h1>2.       Meet Map/Reduce</h1>
<p>l         MapReduce has two phases: the map phase and the reduce phase. </p>
<p>l         Each phase has key-value pairs as input and output (the types can be specified).
</p>
<p>n         The input key-value types of the map phase is determined by the input format</p>
<p>n         The output key-value types of the map phase should match the input key value types of the reduce phase</p>
<p>n         The output key-value types of the reduce phase can be set in the JobConf interface.</p>
<p>l         The programmer specifies two functions: the map function and the reduce function.</p>
<p> </p>
<h2>2.1.     MapReduce logical data flow</h2>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image018_2.jpg" rel="nofollow"><img title="clip_image018" alt="clip_image018" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image018_thumb.jpg" border="0" height="127" width="558"></a></p>
<p> </p>
<h2>2.2.     MapReduce Code</h2>
<h3>2.2.1.      The map function is represented by an implementation of the Mapper interface, which declares a map() method.</h3>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image020_2.jpg" rel="nofollow"><img title="clip_image020" alt="clip_image020" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image020_thumb.jpg" border="0" height="388" width="558"></a></p>
<p> </p>
<h3>2.2.2.      The reduce function is defined using a Reducer</h3>
<p>l         The input types of the reduce function must match the output type of the map function.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image022_2.jpg" rel="nofollow"><img title="clip_image022" alt="clip_image022" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image022_thumb.jpg" border="0" height="265" width="533"></a></p>
<p> </p>
<h3>2.2.3.      The code runs the MapReduce job</h3>
<p>l         An input path is specified by calling the static addInputPath() method on FileInputFormat</p>
<p>n         It can be a single file, a directory, or a file pattern. </p>
<p>n         addInputPath() can be called more than once to use input from multiple paths.</p>
<p>l         The output path is specified by the static setOutputPath() method on FileOutputFormat.
</p>
<p>n         It specifies a directory where the output files from the reducer functions are written.
</p>
<p>n         The directory shouldn’t exist before running the job</p>
<p>l         The map and reduce types can be specified via the setMapperClass() and setReducerClass() methods.</p>
<p>l         The setOutputKeyClass() and setOutputValueClass() methods control the output types for the map and the reduce functions, which are often the same.</p>
<p>n         If they are different, then the map output types can be set using the methods setMapOutputKeyClass() and setMapOutputValueClass().</p>
<p>l         The input types are controlled via the input format, which we have not explicitly set since we are using the default TextInputFormat.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image024_2.jpg" rel="nofollow"><img title="clip_image024" alt="clip_image024" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image024_thumb.jpg" border="0" height="394" width="558"></a></p>
<p> </p>
<h2>2.3.     Scaling Out</h2>
<h3>2.3.1.      MapReduce data flow with a single reduce task</h3>
<p>l         A MapReduce job is a unit of work that the client wants to be performed: it consists of the input data, the MapReduce program, and configuration information.
</p>
<p>l         Hadoop runs the job by dividing it into tasks, of which there are two types: map tasks and reduce tasks.</p>
<p>l         There are two types of nodes that control the job execution process: a jobtracker and a number of tasktrackers.
</p>
<p>n         The jobtracker coordinates all the jobs run on the system by scheduling tasks to run on tasktrackers.
</p>
<p>n         Tasktrackers run tasks and send progress reports to the jobtracker, which keeps a record of the overall progress of each job.
</p>
<p>n         If a tasks fails, the jobtracker can reschedule it on a different tasktracker.</p>
<p>l         Hadoop divides the input to a MapReduce job into fixed-size input splits.
</p>
<p>l         Hadoop creates one map task for each split, which runs the user defined map function for each record in the split.</p>
<p>l         Hadoop does its best to run the map task on a node where the input data resides in HDFS.</p>
<p>n         This is called the data locality optimization.</p>
<p>n         This is why the optimal split size is the same as the block size: it is the largest size of input that can be guaranteed to be stored on a single node.</p>
<p>l         Reduce tasks don’t have the advantage of data locality</p>
<p>n         The input to a single reduce task is normally the output from all mappers.</p>
<p>n         The output of the reduce is normally stored in HDFS for reliability.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image026_2.jpg" rel="nofollow"><img title="clip_image026" alt="clip_image026" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image026_thumb.jpg" border="0" height="302" width="557"></a></p>
<h3>2.3.2.      MapReduce data flow with multiple reduce tasks</h3>
<p>The number of reduce tasks is not governed by the size of the input, but is specified independently.</p>
<p>l         When there are multiple reducers, the map tasks partition their output, each creating one partition for each reduce task.
</p>
<p>l         There can be many keys (and their associated values) in each partition, but the records for every key are all in a single partition.
</p>
<p>l         The partitioning can be controlled by a user-defined partitioning function</p>
<p>n         Normally the default partitioner which buckets keys using a hash function.</p>
<p>n         conf.setPartitionerClass(HashPartitioner.class);</p>
<p>n         conf.setNumReduceTasks(1);</p>
<p>l         The data flow between map and reduce tasks is “the shuffle,” as each reduce task is fed by many map tasks.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image028_2.jpg" rel="nofollow"><img title="clip_image028" alt="clip_image028" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image028_thumb.jpg" border="0" height="303" width="558"></a></p>
<p>l         It’s also possible to have zero reduce tasks. This can be appropriate when you don’t need the shuffle since the processing can be carried out entirely in parallel</p>
<p> </p>
<h1>3.       MapReduce Types and Formats</h1>
<h2>3.1.     MapReduce Types</h2>
<p>l         The map and reduce functions in Hadoop MapReduce have the following general form:</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image030_2.jpg" rel="nofollow"><img title="clip_image030" alt="clip_image030" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image030_thumb.jpg" border="0" height="43" width="269"></a></p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image032_2.jpg" rel="nofollow"><img title="clip_image032" alt="clip_image032" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image032_thumb.jpg" border="0" height="192" width="556"></a></p>
<p>l         The partition function operates on the intermediate key and value types (K2 and V2), and returns the partition index.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image034_2.jpg" rel="nofollow"><img title="clip_image034" alt="clip_image034" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image034_thumb.jpg" border="0" height="29" width="215"></a></p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image036_2.jpg" rel="nofollow"><img title="clip_image036" alt="clip_image036" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image036_thumb.jpg" border="0" height="79" width="440"></a></p>
<h3>3.1.1.      Configuration of MapReduce types</h3>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image038_2.jpg" rel="nofollow"><img title="clip_image038" alt="clip_image038" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image038_thumb.jpg" border="0" height="304" width="557"></a></p>
<p>l         Input types are set by the input format.</p>
<p>n         For instance, a TextInputFormat generates keys of type LongWritable and values of type Text.</p>
<p>l         A minimal MapReduce driver, with the defaults explicitly set</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image040_2.jpg" rel="nofollow"><img title="clip_image040" alt="clip_image040" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image040_thumb.jpg" border="0" height="599" width="558"></a></p>
<p>l         The default input format is TextInputFormat, which produces keys of type LongWritable (the offset of the beginning of the line in the file) and values of type Text (the line of text).</p>
<p>l         The setNumMapTasks() call does not necessarily set the number of map tasks to one</p>
<p>n         The actual number of map tasks depends on the size of the input</p>
<p>l         The default mapper is IdentityMapper</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image042_2.jpg" rel="nofollow"><img title="clip_image042" alt="clip_image042" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image042_thumb.jpg" border="0" height="175" width="519"></a></p>
<p>l         Map tasks are run by MapRunner, the default implementation of MapRunnable that calls the Mapper’s map() method sequentially with each record.</p>
<p>l         The default partitioner is HashPartitioner, which hashes a record’s key to determine which partition the record belongs in.
</p>
<p>n         Each partition is processed by a reduce task, so the number of partitions is equal to the number of reduce tasks for the job</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image044_2.jpg" rel="nofollow"><img title="clip_image044" alt="clip_image044" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image044_thumb.jpg" border="0" height="198" width="555"></a></p>
<p>l         The default reducer is IdentityReducer</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image046_2.jpg" rel="nofollow"><img title="clip_image046" alt="clip_image046" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image046_thumb.jpg" border="0" height="216" width="552"></a></p>
<p>l         Records are sorted by the MapReduce system before being presented to the reducer.</p>
<p>l         The default output format is TextOutputFormat, which writes out records, one per line, by converting keys and values to strings and separating them with a tab character.</p>
<p> </p>
<h2>3.2.     Input Formats</h2>
<p> </p>
<h3>3.2.1.      Input Splits and Records</h3>
<p>l         An input split is a chunk of the input that is processed by a single map.
</p>
<p>l         Each split is divided into records, and the map processes each record—a key-value pair—in turn.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image048_2.jpg" rel="nofollow"><img title="clip_image048" alt="clip_image048" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image048_thumb.jpg" border="0" height="142" width="376"></a></p>
<p>l         An InputSplit has a length in bytes, and a set of storage locations, which are just hostname strings.
</p>
<p>l         A split doesn’t contain the input data; it is just a reference to the data.
</p>
<p>l         The storage locations are used by the MapReduce system to place map tasks as close to the split’s data as possible</p>
<p>l         The size is used to order the splits so that the largest get processed first</p>
<p>l         An InputFormat is responsible for creating the input splits, and dividing them into records.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image050_2.jpg" rel="nofollow"><img title="clip_image050" alt="clip_image050" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image050_thumb.jpg" border="0" height="149" width="558"></a></p>
<p>l         The JobClient calls the getSplits() method, passing the desired number of map tasks as the numSplits argument.
</p>
<p>l         Having calculated the splits, the client sends them to the jobtracker, which uses their storage locations to schedule map tasks to process them on the tasktrackers.</p>
<p>l         On a tasktracker, the map task passes the split to the getRecordReader() method on InputFormat to obtain a RecordReader for that split.
</p>
<p>l         A RecordReader is little more than an iterator over records, and the map task uses one to generate record key-value pairs, which it passes to the map function.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image052_2.jpg" rel="nofollow"><img title="clip_image052" alt="clip_image052" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image052_thumb.jpg" border="0" height="106" width="369"></a></p>
<p>l         The same key and value objects are used on each invocation of the map() method—only their contents are changed. If you need to change the value out of map, make a copy of the object you want to hold on to.</p>
<p> </p>
<h3>3.2.2.      FileInputFormat</h3>
<p>l         FileInputFormat is the base class for all implementations of InputFormat that use files as their data source.
</p>
<p>l         It provides two things: a place to define which files are included as the input to a job, and an implementation for generating splits for the input files.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image054_2.jpg" rel="nofollow"><img title="clip_image054" alt="clip_image054" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image054_thumb.jpg" border="0" height="431" width="557"></a></p>
<p>l         FileInputFormat input paths may represent a file, a directory, or, by using a glob, a collection of files and directories.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image056_2.jpg" rel="nofollow"><img title="clip_image056" alt="clip_image056" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image056_thumb.jpg" border="0" height="81" width="556"></a></p>
<p>l         To exclude certain files from the input, you can set a filter using the setInputPathFilter() method on FileInputFormat</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image058_2.jpg" rel="nofollow"><img title="clip_image058" alt="clip_image058" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image058_thumb.jpg" border="0" height="42" width="543"></a></p>
<p>l         FileInputFormat splits only large files. Here “large” means larger than an HDFS block.</p>
<p>l         Properties for controlling split size</p>
<p>n         The minimum split size is usually 1 byte, by setting this to a value larger than the block size, they can force splits to be larger than a block.</p>
<p>n         The maximum split size defaults to the maximum value that can be represented by a Java long type. It has an effect only when it is less than the block size, forcing splits to be smaller than a block.</p>
<h4>Small files and CombineFileInputFormat</h4>
<p>l         Hadoop works better with a small number of large files than a large number of small files.</p>
<p>l         Where FileInputFormat creates a split per file, CombineFileInputFormat packs many files into each split so that each mapper has more to process.</p>
<p>l         One technique for avoiding the many small files case is to merge small files into larger files by using a SequenceFile: the keys can act as filenames and the values as file contents.</p>
<p> </p>
<h3>3.2.3.      Text Input</h3>
<p>l         TextInputFormat is the default InputFormat. </p>
<p>n         Each record is a line of input. </p>
<p>n         The key, a LongWritable, is the byte offset within the file of the beginning of the line.
</p>
<p>n         The value is the contents of the line, excluding any line terminators, and is packaged as a Text object.</p>
<p>l         The logical records that FileInputFormats define do not usually fit neatly into HDFS blocks.</p>
<p>l         A single file is broken into lines, and the line boundaries do not correspond with the HDFS block boundaries.
</p>
<p>l         Splits honor logical record boundaries</p>
<p>n         The first split contains line 5, even though it spans the first and second block.
</p>
<p>n         The second split starts at line 6.</p>
<p>l         Data-local maps will perform some remote reads.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image060_2.jpg" rel="nofollow"><img title="clip_image060" alt="clip_image060" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image060_thumb.jpg" border="0" height="121" width="557"></a></p>
<h4>KeyValueTextInputFormat</h4>
<p>l         It is common for each line in a file to be a key-value pair, separated by a delimiter such as a tab character.</p>
<p>l         You can specify the separator via the key.value.separator.in.input.line property.</p>
<p> </p>
<h4>NLineInputFormat</h4>
<p>l         If you want your mappers to receive a fixed number of lines of input, then NLineInputFormat is the InputFormat to use.
</p>
<p>l         Like TextInputFormat, the keys are the byte offsets within the file and the values are the lines themselves.
</p>
<p>l         N refers to the number of lines of input that each mapper receives.</p>
<p> </p>
<h3>3.2.4.      Binary Input</h3>
<h4>SequenceFileInputFormat</h4>
<p>l         Hadoop’s sequence file format stores sequences of binary key-value pairs.</p>
<p>l         To use data from sequence files as the input to MapReduce, you use SequenceFileInputFormat.
</p>
<p>l         The keys and values are determined by the sequence file, and you need to make sure that your map input types correspond.
</p>
<p>l         For example, if your sequence file has IntWritable keys and Text values, then the map signature would be Mapper&lt;IntWritable, Text, K, V&gt;.</p>
<p> </p>
<h4>SequenceFileAsTextInputFormat</h4>
<p>l         SequenceFileAsTextInputFormat is a variant of SequenceFileInputFormat that converts the sequence file’s keys and values to Text objects.</p>
<p> </p>
<h4>SequenceFileAsBinaryInputFormat</h4>
<p>l         SequenceFileAsBinaryInputFormat is a variant of SequenceFileInputFormat that retrieves the sequence file’s keys and values as opaque binary objects.
</p>
<p>l         They are encapsulated as BytesWritable objects</p>
<p> </p>
<h4>SequenceFile</h4>
<p>l         Writing a SequenceFile</p>
<p>n         To create a SequenceFile, use one of its createWriter() static methods, which returns a SequenceFile.Writer instance.
</p>
<p>n         specify a stream to write to (either a FSDataOutputStream or a FileSystem and Path pairing), a Configuration object, and the key and value types.</p>
<p>n         Once you have a SequenceFile.Writer, you then write key-value pairs, using the append() method.
</p>
<p>n         Then when you’ve finished you call the close() method</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image062_2.jpg" rel="nofollow"><img title="clip_image062" alt="clip_image062" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image062_thumb.jpg" border="0" height="586" width="557"></a></p>
<p>l         Reading a SequenceFile</p>
<p>n         Reading sequence files from beginning to end is a matter of creating an instance of SequenceFile.Reader, and iterating over records by repeatedly invoking one of the next() methods.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image064_2.jpg" rel="nofollow"><img title="clip_image064" alt="clip_image064" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image064_thumb.jpg" border="0" height="435" width="557"></a></p>
<p>l         The SequenceFile Format</p>
<p>n         A sequence file consists of a header followed by one or more records.</p>
<p>n         The first three bytes of a sequence file are the bytes SEQ, which acts a magic number, followed by a single byte representing the version number.
</p>
<p>n         The header contains other fields including the names of the key and value classes,  compression details, user-defined metadata, and the sync marker.</p>
<p>n         The sync marker is used to allow a reader to synchronize to a record boundary from any position in the file.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image066_2.jpg" rel="nofollow"><img title="clip_image066" alt="clip_image066" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image066_thumb.jpg" border="0" height="281" width="557"></a></p>
<p> </p>
<h3>3.2.5.      Multiple Inputs</h3>
<p>l         The MultipleInputs class allows you to specify the InputFormat and Mapper to use on a per-path basis.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image068_2.jpg" rel="nofollow"><img title="clip_image068" alt="clip_image068" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image068_thumb.jpg" border="0" height="86" width="483"></a></p>
<p> </p>
<h2>3.3.     Output Formats</h2>
<h3>3.3.1.      Text Output</h3>
<p>l         The default output format, TextOutputFormat, writes records as lines of text.
</p>
<p>l         Its keys and values may be of any type, since TextOutputFormat turns them to strings by calling toString() on them.
</p>
<p>l         Each key-value pair is separated by a tab character, although that may be changed using the mapred.textoutputformat.separator property.</p>
<p> </p>
<h3>3.3.2.      Binary Output</h3>
<p>l         SequenceFileOutputFormat</p>
<p>l         SequenceFileAsBinaryOutputFormat</p>
<p>l         MapFileOutputFormat</p>
<p> </p>
<h4>Writing a MapFile</h4>
<p>l         You create an instance of MapFile.Writer, then call the append() method to add entries in order.
</p>
<p>l         Keys must be instances of WritableComparable, and values must be Writable</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image070_2.jpg" rel="nofollow"><img title="clip_image070" alt="clip_image070" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image070_thumb.jpg" border="0" height="573" width="502"></a></p>
<p>l         If we look at the MapFile, we see it’s actually a directory containing two files called data and index:</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image072_2.jpg" rel="nofollow"><img title="clip_image072" alt="clip_image072" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image072_thumb.jpg" border="0" height="86" width="381"></a></p>
<p>l         Both files are SequenceFiles. The data file contains all of the entries, in order:</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image074_2.jpg" rel="nofollow"><img title="clip_image074" alt="clip_image074" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image074_thumb.jpg" border="0" height="88" width="318"></a></p>
<p>l         The index file contains a fraction of the keys, and contains a mapping from the key to that key’s offset in the data file:</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image076_2.jpg" rel="nofollow"><img title="clip_image076" alt="clip_image076" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image076_thumb.jpg" border="0" height="175" width="282"></a></p>
<h4>Reading a MapFile</h4>
<p>l         you create a MapFile.Reader, then call the next() method until it returns false</p>
<p> </p>
<h3>3.3.3.      Multiple Outputs</h3>
<h4>MultipleOutputFormat</h4>
<p>l         MultipleOutputFormat allows you to write data to multiple files whose names are derived from the output keys and values.</p>
<p>n         conf.setOutputFormat(StationNameMultipleTextOutputFormat.class);</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image078_2.jpg" rel="nofollow"><img title="clip_image078" alt="clip_image078" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image078_thumb.jpg" border="0" height="196" width="557"></a></p>
<h4>MultipleOutputs</h4>
<p>l         MultipleOutputs can emit different types for each output.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image080_2.jpg" rel="nofollow"><img title="clip_image080" alt="clip_image080" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image080_thumb.jpg" border="0" height="75" width="558"></a></p>
<h1>4.       Developing a MapReduce Application</h1>
<h2>4.1.     The Configuration API</h2>
<p>l         An instance of the Configuration class (found in the org.apache.hadoop.conf package) represents a collection of configuration properties and their values.</p>
<p>l         Configurations read their properties from resources—XML files</p>
<p> </p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image082_2.jpg" rel="nofollow"><img title="clip_image082" alt="clip_image082" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image082_thumb.jpg" border="0" height="476" width="370"></a></p>
<p>l         we can access its properties using a piece of code like this:</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image084_2.jpg" rel="nofollow"><img title="clip_image084" alt="clip_image084" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image084_thumb.jpg" border="0" height="104" width="399"></a></p>
<p> </p>
<h2>4.2.     Configuring the Development Environment</h2>
<h3>4.2.1.      Managing Configuration</h3>
<p>l         When developing Hadoop applications, it is common to switch between running the application locally and running it on a cluster.</p>
<p>l         hadoop-local.xml</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image086_2.jpg" rel="nofollow"><img title="clip_image086" alt="clip_image086" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image086_thumb.jpg" border="0" height="255" width="284"></a></p>
<p>l         hadoop-localhost.xml</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image088_2.jpg" rel="nofollow"><img title="clip_image088" alt="clip_image088" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image088_thumb.jpg" border="0" height="256" width="295"></a></p>
<p>l         hadoop-cluster.xml</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image090_2.jpg" rel="nofollow"><img title="clip_image090" alt="clip_image090" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image090_thumb.jpg" border="0" height="138" width="294"></a></p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image092_2.jpg" rel="nofollow"><img title="clip_image092" alt="clip_image092" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image092_thumb.jpg" border="0" height="116" width="289"></a></p>
<p>l         With this setup, it is easy to use any configuration with the -conf command-line switch.</p>
<p>l         For example, the following command shows a directory listing on the HDFS server running in pseudo-distributed mode on localhost:</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image094_2.jpg" rel="nofollow"><img title="clip_image094" alt="clip_image094" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image094_thumb.jpg" border="0" height="85" width="560"></a></p>
<h3>4.2.2.      GenericOptionsParser, Tool, and ToolRunner</h3>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image096_2.jpg" rel="nofollow"><img title="clip_image096" alt="clip_image096" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image096_thumb.jpg" border="0" height="412" width="541"></a></p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image098_2.jpg" rel="nofollow"><img title="clip_image098" alt="clip_image098" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image098_thumb.jpg" border="0" height="59" width="498"></a></p>
<p> </p>
<h1>5.       How MapReduce Works</h1>
<h2>5.1.     Anatomy of a MapReduce Job Run</h2>
<p>l         There are four independent entities:</p>
<p>n         The client, which submits the MapReduce job.</p>
<p>n         The jobtracker, which coordinates the job run. The jobtracker is a Java application whose main class is JobTracker.</p>
<p>n         The tasktrackers, which run the tasks that the job has been split into. Tasktrackers are Java applications whose main class is TaskTracker.</p>
<p>n         The distributed filesystem, which is used for sharing job files between the other entities.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image100_2.jpg" rel="nofollow"><img title="clip_image100" alt="clip_image100" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image100_thumb.jpg" border="0" height="463" width="558"></a></p>
<h3>5.1.1.      Job Submission</h3>
<p>l         The runJob() method on JobClient creates a new JobClient instance and calls submitJob() on it.
</p>
<p>l         Having submitted the job, runJob() polls the job’s progress once a second, and reports the progress to the console if it has changed since the last report.
</p>
<p>l         When the job is complete, if it was successful, the job counters are displayed. Otherwise, the error that caused the job to fail is logged to the console.</p>
<p> </p>
<h4>The job submission process</h4>
<p>l         Asks the jobtracker for a new job ID (by calling getNewJobId() on JobTracker)</p>
<p>l         Checks the output specification of the job.</p>
<p>l         Computes the input splits for the job.</p>
<p>l         Copies the resources needed to run the job, including the job JAR file, the configuration file and the computed input splits, to the jobtracker’s filesystem in a directory named after the job ID.</p>
<p>l         Tells the jobtracker that the job is ready for execution (by calling submitJob() on JobTracker)</p>
<p> </p>
<h3>5.1.2.      Job Initialization</h3>
<p>l         When the JobTracker receives a call to its submitJob() method, it puts it into an internal queue from where the job scheduler will pick it up and initialize it.
</p>
<p>l         Initialization involves creating an object to represent the job being run, which encapsulates its tasks, and bookkeeping information to keep track of the tasks’ status and progress.</p>
<p>l         To create the list of tasks to run, the job scheduler first retrieves the input splits computed by the JobClient from the shared filesystem.
</p>
<p>l         It then creates one map task for each split. </p>
<p>l         Tasks are given IDs at this point.</p>
<p> </p>
<h3>5.1.3.      Task Assignment</h3>
<p>l         Tasktrackers run a simple loop that periodically sends heartbeat method calls to the jobtracker.</p>
<p>l         As a part of the heartbeat, a tasktracker will indicate whether it is ready to run a new task, and if it is, the jobtracker will allocate it a task, which it communicates to the tasktracker using the heartbeat return value</p>
<p>l         Before it can choose a task for the tasktracker, the jobtracker must choose a job to select the task from according to priority.(setJobPriority() and FIFO)</p>
<p>l         Tasktrackers have a fixed number of slots for map tasks and for reduce tasks.</p>
<p>l         The default scheduler fills empty map task slots before reduce task slots</p>
<p>l         To choose a reduce task the jobtracker simply takes the next in its list of yet-to-be-run reduce tasks, since there are no data locality considerations.</p>
<p> </p>
<h3>5.1.4.      Task Execution</h3>
<p>l         Now the tasktracker has been assigned a task, the next step is for it to run the task.</p>
<p>l         First, it localizes the job JAR by copying it from the shared filesystem to the tasktracker’s filesystem.
</p>
<p>l         It also copies any files needed from the distributed cache by the application to the local disk</p>
<p>l         Second, it creates a local working directory for the task, and un-jars the contents of the JAR into this directory. 
</p>
<p>l         Third, it creates an instance of TaskRunner to run the task.</p>
<p>l         TaskRunner launches a new Java Virtual Machine to run each task in </p>
<p>l         It is however possible to reuse the JVM between tasks; </p>
<p>l         The child process communicates with its parent through the umbilical interface.</p>
<p> </p>
<h3>5.1.5.      Job Completion</h3>
<p>l         When the jobtracker receives a notification that the last task for a job is complete, it changes the status for the job to “successful.” T</p>
<p>l         hen, when the JobClient polls for status, it learns that the job has completed successfully, so it prints a message to tell the user, and then returns from the runJob() method.</p>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image102_2.jpg" rel="nofollow"><img title="clip_image102" alt="clip_image102" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image102_thumb.jpg" border="0" height="465" width="558"></a></p>
<p> </p>
<h2>5.2.     Failures</h2>
<h3>5.2.1.      Task Failure</h3>
<p>l         The most common way is when user code in the map or reduce task throws a runtime exception.
</p>
<p>n         the child JVM reports the error back to its parent tasktracker, before it exits.
</p>
<p>n         The error ultimately makes it into the user logs.</p>
<p>n         The tasktracker marks the task attempt as failed, freeing up a slot to run another task.</p>
<p>l         Another failure mode is the sudden exit of the child JVM</p>
<p>n         the tasktracker notices that the process has exited, and marks the attempt as failed.</p>
<p>l         Hanging tasks are dealt with differently. </p>
<p>n         The tasktracker notices that it hasn’t received a progress update for a while, and proceeds to mark the task as failed.
</p>
<p>n         The child JVM process will be automatically killed after this period</p>
<p>l         When the jobtracker is notified of a task attempt that has failed (by the tasktracker’s heartbeat call) it will reschedule execution of the task.
</p>
<p>n         The jobtracker will try to avoid rescheduling the task on a tasktracker where it has previously failed.
</p>
<p>n         If a task fails more than four times, it will not be retried further.</p>
<p> </p>
<h3>5.2.2.      Tasktracker Failure</h3>
<p>l         If a tasktracker fails by crashing, or running very slowly, it will stop sending heartbeats to the jobtracker (or send them very infrequently).
</p>
<p>l         The jobtracker will notice a tasktracker that has stopped sending heartbeats and remove it from its pool of tasktrackers to schedule tasks on.
</p>
<p>l         The jobtracker arranges for map tasks that were run and completed successfully on that tasktracker to be rerun if they belong to incomplete jobs, since their intermediate output residing on the failed tasktracker’s local filesystem may not be accessible
 to the reduce task. Any tasks in progress are also rescheduled.</p>
<p> </p>
<h3>5.2.3.      Jobtracker Failure</h3>
<p> </p>
<h2>5.3.     Shuffle and Sort</h2>
<p><a href="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image104_2.jpg" rel="nofollow"><img title="clip_image104" alt="clip_image104" src="http://images.cnblogs.com/cnblogs_com/forfuture1978/WindowsLiveWriter/NotesforHadoopthedefinitiveguide_14109/clip_image104_thumb.jpg" border="0" height="261" width="557"></a></p>
<h3>5.3.1.      The Map Side</h3>
<p>l         When the map function starts producing output, it is not simply written to disk.</p>
<p>l         Each map task has a circular memory buffer that it writes the output to.</p>
<p>l         When the contents of the buffer reach a certain threshold size, a background thread will start to spill the contents to disk.</p>
<p>l         Spills are written in round-robin fashion to the directories specified by the mapred.local.dir property</p>
<p>l         Before it writes to disk, the thread first divides the data into partitions corresponding to the reducers that they will ultimately be sent to.
</p>
<p>l         Within each partition, the background thread performs an in-memory sort by key.</p>
<p>l         Each time the memory buffer reaches the spill threshold, a new spill file is created, so after the map task has written its last output record there could be several spill files.</p>
<p>l         Before the task is finished, the spill files are merged into a single partitioned and sorted output file.</p>
<p>l         The output file’s partitions are made available to the reducers over HTTP.
</p>
<p>l         The number of worker threads used to serve the file partitions is controlled by the task tracker.http.threads property</p>
<p> </p>
<h3>5.3.2.      The Reduce Side</h3>
<p>l         As map tasks complete successfully, they notify their parent tasktracker of the status update, which in turn notifies the jobtracker.</p>
<p>l         for a given job, the jobtracker knows the mapping between map outputs and tasktrackers.
</p>
<p>l         A thread in the reducer periodically asks the jobtracker for map output locations until it has retrieved them all.</p>
<p>l         The reduce task needs the map output for its particular partition from several map tasks across the cluster.
</p>
<p>l         The map tasks may finish at different times, so the reduce task starts copying their outputs as soon as each completes. This is known as the copy phase of the reduce task.
</p>
<p>l         The reduce task has a small number of copier threads so that it can fetch map outputs in parallel.</p>
<p>l         As the copies accumulate on disk, a background thread merges them into larger, sorted files.</p>
<p>l         When all the map outputs have been copied, the reduce task moves into the sort phase (which should properly be called the merge phase, as the sorting was carried out on the map side), which merges the map outputs, maintaining their sort ordering.</p>
l         During the reduce phase the reduce function is invoked for each key in the sorted output. The output of this phase is written directly to the output filesystem, typically HDFS.
            </div>
                </div>
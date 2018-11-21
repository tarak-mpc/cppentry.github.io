---
layout:     post
title:      Hadoop7-HDFS的NameNode的元数据管理机制与Hadoop的高可用架构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>在介绍HDFS的元数据管理之前，有必要先了解下HDFS的架构</p>

<p>1. HDFS架构简介</p>

<p>HDFS主要包含两个组件，NameNode与DataNode，其中NameNode主要用来管理元数据，DataNode用来存储数据，在分布式HDFS架构中，通常会有一台NameNode，一台SecondaryNameNode,多台DataNode。</p>

<p> </p>

<p>2. 上传文件</p>

<p>使用HDFS上传文件通常包含以下几个步骤</p>

<p>1. 客户端向NN请求上传文件</p>

<p>2. NN接到请求后，首先网edits log文件中记录元数据操作日志</p>

<p>3. NN返回分配的block元信息给客户端</p>

<p>4. 客户端开始上传文件，上传完成后返回成功信息给NN</p>

<p>5. NN在内存中写入这次操作产生的元数据信息</p>

<p>6. 每当edits log写满后，要将这段时间的元数据信息刷到fsimage中</p>

<p> </p>

<p>3. NN元数据的存储</p>

<p>一条元数据信息在150B以内，1G空间大概能存700w条左右的元数据。</p>

<p>这么多的元信息，客户端在查询的时候如何才能保证高效呢？首先要考虑元信息存储的位置，磁盘？内存？</p>

<p>磁盘肯定速度会很慢，因此需要将所有的元信息映射到内存中以便高效查找。</p>

<p>NN元数据通常分为两部分，一部分叫做edits log，位于磁盘中，用于记录最新的元数据信息，另外一份交fsimage，也位于磁盘中，当edits log写满时，需要将edits log的信息刷新到fsimage中，内存中有这两个文件的映像，因此，内存中包含了所有的元信息，当客户端文件上传成功时，NN才会在内存中写入这次操作产生的元信息。</p>

<p>3.1 NN的fsImage中存了些什么内容？</p>

<p>由于fsImage中存的内容是经过序列化的内容，不能直接看到里面的信息，因此需要通过hdfs oiv工具</p>

<p> </p>

<pre class="has">
<code class="language-html">hdfs oiv -i fsimage_xxxx -p Web
http://localhost:50070/webhdfs/v1/aa?op=GETFILESTATUS

jdk-8u60-linux-x64.tar.gz
{"FileStatus":{"accessTime":1496214541163,"blockSize":134217728,"childrenNum":0,"fileId":16392,"group":"supergroup","length":181238643,"modificationTime":1452395391526,"owner":"root","pathSuffix":"","permission":"644","replication":1,"storagePolicy":0,"type":"FILE"}}
aa
{"FileStatus":{"accessTime":0,"blockSize":0,"childrenNum":3,"fileId":16447,"group":"supergroup","length":0,"modificationTime":1503929690683,"owner":"sheldonwong","pathSuffix":"","permission":"755","replication":0,"storagePolicy":0,"type":"DIRECTORY"}}

hdfs oiv -i fsimage_xxxx -o ~/fsimage.xml -p XML
jdk-8u60-linux-x64.tar.gz
&lt;inode&gt;
    &lt;id&gt;16392&lt;/id&gt;
    &lt;type&gt;FILE&lt;/type&gt;
    &lt;name&gt;jdk-8u60-linux-x64.tar.gz&lt;/name&gt;
    &lt;replication&gt;1&lt;/replication&gt;
    &lt;mtime&gt;1452395391526&lt;/mtime&gt;
    &lt;atime&gt;1496214541163&lt;/atime&gt;
    &lt;perferredBlockSize&gt;134217728&lt;/perferredBlockSize&gt;
    &lt;permission&gt;root:supergroup:rw-r--r--&lt;/permission&gt;
    
    &lt;blocks&gt;
        &lt;block&gt;
            &lt;id&gt;1073741827&lt;/id&gt;
            &lt;genstamp&gt;1003&lt;/genstamp&gt;
            &lt;numBytes&gt;134217728&lt;/numBytes&gt;
        &lt;/block&gt;
        &lt;block&gt;
            &lt;id&gt;1073741828&lt;/id&gt;
            &lt;genstamp&gt;1004&lt;/genstamp&gt;
            &lt;numBytes&gt;47020915&lt;/numBytes&gt;
        &lt;/block&gt;
    &lt;/blocks&gt;
&lt;/inode&gt;</code></pre>

<p>可以看到namenode维护了整个文件系统的目录树，同时存了文件与block的映射信息，以及一些文件元信息</p>

<p>1）由此可见，namenode维护的整个文件系统的目录树、文件/目录的元信息以及文件和数据块的映射信息（目录树，元信息，文件-block映射）</p>

<p>文件元信息：包括创建时间、修改时间、权限等。</p>

<p>文件-block映射：文件对应的block集合。</p>

<p>2）注意，与数据节点相关的信息并不保存在名字节点的本地文件系统中，而是系统启动时，名字节点动态重建这些信息。<br>
 </p>

<p> </p>

<p>4. checkpoint与SN（Secondary NameNode）</p>

<p>当edits log写满时，需要将edits log与fsimage合并，这个时间节点就叫做checkpoint，但是又不能在NN中进行合并，因为这样会降低NN的响应速度，所以就出现了SN，合并步骤具体如下</p>

<p>1）edits log写满，到达checkpoint</p>

<p>2）此时，SN向NN发出请求，要通过http下载edits log与fsimage，不能再向edits log中写数据了，但是NN还是要向外提供服务，因此就会新建一个edits log.new的文件，</p>

<p>3）SN从NN下载好edits log与fsimage后，就开始合并，并将合并后的文件命名为 fsimage.chkpoint,合并好后上传给NN</p>

<p>4）NN在接收到SN上传的fsimage.chkpoint后，就将原有的edits log文件删除，将fsimage.chkpoint重命名为fsimage，将edits log.new重命名为edits log，这样NN就又恢复了开始的状态</p>

<p> </p>

<p>5. NameNode高可用的实现</p>

<p> </p>            </div>
                </div>
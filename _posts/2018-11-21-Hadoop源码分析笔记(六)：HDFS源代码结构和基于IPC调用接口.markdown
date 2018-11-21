---
layout:     post
title:      Hadoop源码分析笔记(六)：HDFS源代码结构和基于IPC调用接口
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3>HDFS源代码结构</h3>
<p>        HDFS的源代码都在org.apche.hadoop.hdfs包下。HDFS的源代码分布16个目录下，它们可以分为如下四类。</p>
<p>        1、基础包</p>
<p>        包括工具和安全包。其中，hdfs.util包含了一些HDFS实现需要的辅助数据结构；hdfs.security.token.block和hdfs.sercurity.token.delegation结合Hadoop的安全框架，提供了安全访问HDFS的机制，该安全特性最先是有Yahoo开发的，集成了企业广泛应用的Kerberos标准，使得用户可以在一个集群管理各类商业敏感数据。</p>
<p>        2、HDFS实体实现包</p>
<p>        这是代码分析的重点，包括7个包：</p>
<p>        hdfs.server.commom包含了一些名字节点和数据节点共享的功能，如系统升级、存储空间等。</p>
<p>        hdfs.protocol提供了HDFS各个实体间通过IPC交互的接口。</p>
<p>        hdfs.server.namenode、hdfs.server.datanode和hdfs分别包含了名字节点、数据节点和客户端的实现。上述代码是HDFS代码分析的重点。</p>
<p>        hdfs.server.namenode.metrics和hdfs.server.datanode.metrics实现了名字节点和数据节点上度量数据的收集功能。度量数据包括名字节点进程和数据节点进程上事件的计数，例如数据节点上就可以收集到写入字节数、被复制的块的数据等信息。</p>
<p>       3、应用包</p>
<p>        包括hdfs.tools和hdfs.server.balancer，这两个包提供查询HDFS状态信息工具dfsadmin、文件系统检查工具fsck和HDFS均衡器balancer(通过start-balancer.sh启动)的实现。</p>
<p>        4、WebHDFS相关包</p>
<p>         包括hdfs.web.resources、hdfs.web.namenode.web.resources、hdfs.server.datanode.web.resources和hdfs.web共4个包。</p>
<p>         WebHDFS是HDFS1.0中引入的新功能，它提供了一个完整的、通过HTTP访问HDFS的机制。对比只读的hftp文件系统，WebHDFS提供了HTTP上读写HDFS的能力，并在此基础上实现了访问HDFS的c客户端和用户空间文件系统(FUSE)。</p>
<h3>基于远程过程调用的接口</h3>
<p>        接口是软件系统不同组成部分衔接的约定，一个良好的接口设计可以降低系统各部分的相互依赖，提高组成单元的内聚性，降低组成单元间的耦合程度，从而提高系统的维护性和扩展性。对于HDFS这样的一个复杂系统，接口也是观察系统工作的一个出发点。</p>
<p>         HDFS的体系结构包括了名字节点、数据节点和客户端3个主要角色，它们间有两种主要的通信接口：</p>
<p>         1、 Hadoop远程过程调用接口。</p>
<p>         2、基于TCP或HTTP的流式接口。</p>
<p>         本节先分析HDFS各节点间的IPC接口。</p>
<p>          这些接口可以分为三大类。</p>
<p>          1、客户端相关的接口</p>
<p>           定义在org.apache.hadoop.hdfs.protocol包中。具体接口包括：</p>
<p>           ClientProtocol：客户端与名字节点间的接口，这是一个非常重要的接口，是HDFS客户访问文件系统的入口。客户端通过这个接口访问名字节点，操作文件或目录的元数据信息，读写文件也必须先访问名字节点，接下来再和数据节点进行交互，操作文件数据。另外，从名字节点能获取分布式系统的一些整体运行状态信息，也是通过这个接口进行的。</p>
<p>           ClientDatanodeProtocol：客户端与数据节点间的接口。用于客户端和数据节点进行交互，这个接口用的比较少，客户端和数据节点间的主要交互是通过流接口进行读/写文件数据的操作。错误发生时，客户端需要数据节点配合进行恢复，或者当客户端进行本地文件读优化设时，需要通过IPC接口获取一些信息。</p>
<p>          2、服务器间的接口</p>
<p>          包括名字节点、第二名字节点、数据节点间存在的IPC调用关系，其接口定义在org.apache.hadoop.hdfs.server.protocol包中。具体接口包括：</p>
<p>          DatanodeProtocol：数据节点与名字节点间的接口，这是另外一个重要的接口。在HDFS的主从体系结构中，数据节点作为从节点不断地通过这个接口向主节点名字节点报告一些信息，同步信息到名字节点；同时，该节口的一些访问，方法的返回值会带回名字节点指令，根据这些指令，数据节点或移动、或删除、或恢复本地磁盘上的数据块，或者执行其他操作。</p>
<p>          InterDatanodeProtocol：数据节点与数据节点间的接口。数据节点通过这个接口，和其他数据节点进行通信，恢复数据块，保证数据的一致性。</p>
<p>          NamenodeProtocol：第二名字节点、HDFS均衡器与名字节间的接口。第二名字节点会不停地获取名字节点上某一个时间点的命名空间镜像和镜像的变化日志，然后合并得到一个新的镜像，并将该结果发送回名字节点，在这个过程，名字节点会通过这个接口，配合第二名字节点完成元数据的合并。该接口也为HDFS均衡器balancer的正常工作提供一些信息。</p>
<p>          3、和安全相关的接口</p>
<p>           分别在org.apche.hadoop.security.authorize包和org.apache.hadoop.sercurity包中，包括RefreshAuthorizationPolicyProtocol和RefreshUserMappingsProtocol。名字节点实现了这两个接口。</p>
<h4>客户端相关的接口</h4>
<p>        客户端相关接口上的类不但为ClientProtocol和ClientDatanodeProtocol服务，同时也应用服务器间接口的交互，包含了很多HDFS中基本概念的抽象。</p>
<p>        其中这些抽象中，其中的类可以分为3种：</p>
<p>         1、数据块相关</p>
<p>         通过前面的分析已经了解了，HDFS将文件内容分为数据块，多副本保存在数据节点上。</p>
<p>         数据块在HDFS中的抽象是org.apache.hadoop.hdfs.protocol.Block，它包含了3个成员变量，都是长整形：</p>
<p>               BlockId：数据块的唯一标识，即数据块的ID号</p>
<p>               numBytes：数据块包含的文件数据大小。</p>
<p>               generationStamp：数据块的版本号，或数据块时间戳</p>
<p>         需要注意的是，Block类还维护了一个“关联”影子成员变量：数据块名。如果某个数据块的ID号是123456，那么其数据块名为blk_123456.数据块名应用与数据节点中，作为数据块保存在Linux文件系统上的文件名的一部分,Block.getBlockName()、Block.isBlockFilename()、Block.filename2id()等方法都与数据块名相关。</p>
<p>          Block是大量和数据块相关的类的基础，在客户端接口上，这样的类有LocatedBlock、LocatedBlocks和BlockLocalPathInfo。顾名思义，LocatedBlock就是已经去认了存储位置的数据块。其成员变量除了对应的数据块之外，还包括数据块在对应文件中的偏移量offset、数据块所在的数据节点信息locs和数据块是否损坏标志corrupt等。locs是一个类型为DatanodeInfo的数组，包含了所有可用的数据块位置，损坏的数据块对应的数据节点信息，则不会出现在数组里。或得数据块的位置信息，是读写数据块的前提条件。如下所示：</p>
<p>         </p>
<pre><code class="language-java">//Block 
public class Block implements Writable, Comparable&lt;Block&gt; {

  static {                                      // register a ctor
    WritableFactories.setFactory
      (Block.class,
       new WritableFactory() {
         public Writable newInstance() { return new Block(); }
       });
  }

  // generation stamp of blocks that pre-date the introduction of
  // a generation stamp.
  public static final long GRANDFATHER_GENERATION_STAMP = 0;

  /**
   */
  public static boolean isBlockFilename(File f) {
    String name = f.getName();
    if ( name.startsWith( "blk_" ) &amp;&amp; 
        name.indexOf( '.' ) &lt; 0 ) {
      return true;
    } else {
      return false;
    }
  }

  static long filename2id(String name) {
    return Long.parseLong(name.substring("blk_".length()));
  }

  private long blockId;
  private long numBytes;
  private long generationStamp;

    public void write(DataOutput out) throws IOException {
    out.writeLong(blockId);
    out.writeLong(numBytes);
    out.writeLong(generationStamp);
  }

  public void readFields(DataInput in) throws IOException {
    this.blockId = in.readLong();
    this.numBytes = in.readLong();
    this.generationStamp = in.readLong();
    if (numBytes &lt; 0) {
      throw new IOException("Unexpected block size: " + numBytes);
    }
  }
        ......
  }
//LocatedBlock
public class LocatedBlock implements Writable {

  static {                                      // register a ctor
    WritableFactories.setFactory
      (LocatedBlock.class,
       new WritableFactory() {
         public Writable newInstance() { return new LocatedBlock(); }
       });
  }

  private Block b;
  private long offset;  // offset of the first byte of the block in the file
  private DatanodeInfo[] locs;
  // corrupt flag is true if all of the replicas of a block are corrupt.
  // else false. If block has few corrupt replicas, they are filtered and 
  // their locations are not part of this object
  private boolean corrupt;
  private Token&lt;BlockTokenIdentifier&gt; blockToken = new Token&lt;BlockTokenIdentifier&gt;();

   ///////////////////////////////////////////
  // Writable
  ///////////////////////////////////////////
  public void write(DataOutput out) throws IOException {
    blockToken.write(out);
    out.writeBoolean(corrupt);
    out.writeLong(offset);
    b.write(out);
    out.writeInt(locs.length);
    for (int i = 0; i &lt; locs.length; i++) {
      locs[i].write(out);
    }
  }

  public void readFields(DataInput in) throws IOException {
    blockToken.readFields(in);
    this.corrupt = in.readBoolean();
    offset = in.readLong();
    this.b = new Block();
    b.readFields(in);
    int count = in.readInt();
    this.locs = new DatanodeInfo[count];
    for (int i = 0; i &lt; locs.length; i++) {
      locs[i] = new DatanodeInfo();
      locs[i].readFields(in);
   } }
   ......
}
//locatedBlocks
public class LocatedBlocks implements Writable {
  private long fileLength;
  private List&lt;LocatedBlock&gt; blocks; // array of blocks with prioritized locations
  private boolean underConstruction;

    /**
   * Find block containing specified offset.
   * 
   * @return block if found, or null otherwise.
   */
  public int findBlock(long offset) {
    // create fake block of size 1 as a key
    LocatedBlock key = new LocatedBlock();
    key.setStartOffset(offset);
    key.getBlock().setNumBytes(1);
    Comparator&lt;LocatedBlock&gt; comp = 
      new Comparator&lt;LocatedBlock&gt;() {
        // Returns 0 iff a is inside b or b is inside a
        public int compare(LocatedBlock a, LocatedBlock b) {
          long aBeg = a.getStartOffset();
          long bBeg = b.getStartOffset();
          long aEnd = aBeg + a.getBlockSize();
          long bEnd = bBeg + b.getBlockSize();
          if(aBeg &lt;= bBeg &amp;&amp; bEnd &lt;= aEnd 
              || bBeg &lt;= aBeg &amp;&amp; aEnd &lt;= bEnd)
            return 0; // one of the blocks is inside the other
          if(aBeg &lt; bBeg)
            return -1; // a's left bound is to the left of the b's
          return 1;
        }
      };
    return Collections.binarySearch(blocks, key, comp);
  }
  
  public void insertRange(int blockIdx, List&lt;LocatedBlock&gt; newBlocks) {
    int oldIdx = blockIdx;
    int insStart = 0, insEnd = 0;
    for(int newIdx = 0; newIdx &lt; newBlocks.size() &amp;&amp; oldIdx &lt; blocks.size(); 
                                                        newIdx++) {
      long newOff = newBlocks.get(newIdx).getStartOffset();
      long oldOff = blocks.get(oldIdx).getStartOffset();
      if(newOff &lt; oldOff) {
        insEnd++;
      } else if(newOff == oldOff) {
        // replace old cached block by the new one
        blocks.set(oldIdx, newBlocks.get(newIdx));
        if(insStart &lt; insEnd) { // insert new blocks
          blocks.addAll(oldIdx, newBlocks.subList(insStart, insEnd));
          oldIdx += insEnd - insStart;
        }
        insStart = insEnd = newIdx+1;
        oldIdx++;
      } else {  // newOff &gt; oldOff
        assert false : "List of LocatedBlock must be sorted by startOffset";
      }
    }
    insEnd = newBlocks.size();
    if(insStart &lt; insEnd) { // insert new blocks
      blocks.addAll(oldIdx, newBlocks.subList(insStart, insEnd));
    }
  }
  
  public static int getInsertIndex(int binSearchResult) {
    return binSearchResult &gt;= 0 ? binSearchResult : -(binSearchResult+1);
  }

  //////////////////////////////////////////////////
  // Writable
  //////////////////////////////////////////////////
  static {                                      // register a ctor
    WritableFactories.setFactory
      (LocatedBlocks.class,
       new WritableFactory() {
         public Writable newInstance() { return new LocatedBlocks(); }
       });
  }

  public void write(DataOutput out) throws IOException {
    out.writeLong(this.fileLength);
    out.writeBoolean(underConstruction);
    // write located blocks
    int nrBlocks = locatedBlockCount();
    out.writeInt(nrBlocks);
    if (nrBlocks == 0) {
      return;
    }
    for (LocatedBlock blk : this.blocks) {
      blk.write(out);
    }
  }
  
  public void readFields(DataInput in) throws IOException {
    this.fileLength = in.readLong();
    underConstruction = in.readBoolean();
    // read located blocks
    int nrBlocks = in.readInt();
    this.blocks = new ArrayList&lt;LocatedBlock&gt;(nrBlocks);
    for (int idx = 0; idx &lt; nrBlocks; idx++) {
      LocatedBlock blk = new LocatedBlock();
      blk.readFields(in);
      this.blocks.add(blk);
    }
  }
......}
//BlockLocalPathInfo
public class BlockLocalPathInfo implements Writable {
  static final WritableFactory FACTORY = new WritableFactory() {
    public Writable newInstance() { return new BlockLocalPathInfo(); }
  };
  static {                                      // register a ctor
    WritableFactories.setFactory(BlockLocalPathInfo.class, FACTORY);
  }

  private Block block;
  private String localBlockPath = "";  // local file storing the data
  private String localMetaPath = "";   // local file storing the checksum

   @Override
  public void write(DataOutput out) throws IOException {
    block.write(out);
    Text.writeString(out, localBlockPath);
    Text.writeString(out, localMetaPath);
  }

  @Override
  public void readFields(DataInput in) throws IOException {
    block = new Block();
    block.readFields(in);
    localBlockPath = Text.readString(in);
    localMetaPath = Text.readString(in);
  }
  
  /**
   * Get number of bytes in the block.
   * @return Number of bytes in the block.
   */
  public long getNumBytes() {
    return block.getNumBytes();
  }
}
</code></pre>
<p></p>
<p> </p>
<p>         另一组类和数据节点相关，它们是DatanodeId和Datanode Info。DatanodeInfo继承自DatanodeID，在DatanodeID的基础上，提供了数据节点上的一些度量信息。</p>
<p>         数据节点标识DatanodeId用于在HDFS集群中确定一个数据节点，它的成员变量包括：name：数据节点使用IP套接字地址作为它的名字，name是一个字符串，可以是IP地址、端口号对，也可以是主机名、端口号对，其中，端口是数据节点的流接口地址，后续我们会继续讨论数据节点的流接口。storageID：数据节点的存储标识，当数据节点用不同的存储标识在名字节点上注册时，名字节点通过这个标识，了解到着是一个呗重新使用的数据节点。inofoPort：数据节点WWW服务器的监听端口，通过何故端口可以使用HTTP/HTTPS协议访问数据节点。ipcPort：数据节点IPC服务器监听端口，对应客户端，该端口提供了ClientDatanodeProtocol接口中定义的服务。相对与DatanodeInfo提供了附加状态信息包括：容量、已经使用容量、剩余容量、状态更新时间、流接口服务线程数、数据节点在集群中的位置、数据节点状态灯信息，通过这些信息可以分析数据节点的负载状态，应用与资源调度、数据节点服务选择等。</p>
<pre><code class="language-java">//DatanodeId
public class DatanodeID implements WritableComparable&lt;DatanodeID&gt; {  
public static final DatanodeID[] EMPTY_ARRAY = {};   
public String name;      /// hostname:portNumber  
public String storageID; /// unique per cluster storageID  protected int infoPort;     /// the port where the infoserver is running  
public int ipcPort;     /// the port where the ipc server is running
}
//DatanodeInfo相关结构public class DatanodeInfo extends DatanodeID implements Node {
  protected long capacity;
  protected long dfsUsed;
  protected long remaining;
  protected long lastUpdate;
  protected int xceiverCount;
  protected String location = NetworkTopology.DEFAULT_RACK;
  ......
}

</code></pre>
<p><br>
        出现在ClientProtocol接口上的最后一组类是HdfsFileStatus和DirectoryListing。HdfsFileStatus保存了HDFS文件/目录的属性，DirectoryListing用于一次返回一个目录下的多个文件/子目录的熟悉，它们都用于实现FileSystem.getFileStatus()方法族。详情如下：</p>
<p>         </p>
<pre><code class="language-java">//HdfsFileStatus
public class HdfsFileStatus implements Writable {
  static {                                      // register a ctor
    WritableFactories.setFactory
      (HdfsFileStatus.class,
       new WritableFactory() {
         public Writable newInstance() { return new HdfsFileStatus(); }
       });
  }

  private byte[] path;  // local name of the inode that's encoded in java UTF8
  private long length;
  private boolean isdir;
  private short block_replication;
  private long blocksize;
  private long modification_time;
  private long access_time;
  private FsPermission permission;
  private String owner;
  private String group;
   ......
}
//DirectoryListing 
public class DirectoryListing implements Writable {
  static {                                      // register a ctor
    WritableFactories.setFactory
      (DirectoryListing.class,
       new WritableFactory() {
         public Writable newInstance() { return new DirectoryListing(); }
       });
  }

  private HdfsFileStatus[] partialListing;
  private int remainingEntries;
  ......  
}</code></pre>
<h4><br>
ClientProtocol</h4>
<p>         客户端和名字节点间的通信协议定义在ClientProtocol接口，这个接口是个冗长的接口。它主要提供的能力可以分两大类：用于实现Hadoop文件系统相关功能的能力；用于对HDFS状态进行查询、设置的能力。</p>
<h4>ClientDataProtocol</h4>
<p>        和ClientProtocol相比，ClientDatanodeProtocol接口相当简单，它只有三个方法，recoverBlock()、geBlickInfo()、getBlockLocakPathInfo()。recoverBlock()方法应用于HDFS的客户端DFSClient的输出流DFSOutputStram中，客户端往数据节点输出数据的过程中，如果某个副本所在的数据节点出现错误，客户端就会尝试进行数据块恢复，这时候需要调用recoverBlock()，从正常工作的数据节点中找到恢复点，然后才能继续输出数据。getBlockInfo()和HDFS的文件一致性相关。文件一致性模型描述了对文件进行读写时数据的可见性。这个方法的输入是Block对象，输出是更新的，反映数据变化的新Block对象。而getBlockLocakPathInfo()方法类应用与本地读优化，执行本地读的客户端通过getBlockLocalPathInfo()成员函数，获得某个数据块对应的数据块文件及数据块校验信息文件的本地路径，然后就可以进一步的本地读操作。</p>
<p>        在一般情况下，客户端和数据节点主要通过基于流的接口进行交互，较少使用ClientDatanodeProtocol中提供的方法。</p>
<h4>DatanodeProtocol</h4>
<p>        DatanodeRegistration类和NamespaceInfo类。由类的名字可知，DatanodeRegistration用于数据节点注册。它继承自DatanodeId，在介绍ClientProtocol时，我们已经知道DatanodeID可用于在HDFS中确定一个数据节点，由此可见，在数据节点注册的时间还必须包含其他的信息，即DatanodeRegistration在DatanodeID基础上新增加的成员变量exportedKeys和storageInfo，其中exportedKeys用于HDFS的安全特性，我们不再分析，类型为StrorageInfo的另一个变量保存了数据节点的存储系统信息。它包括了三个成员变量：layoutVersion：是一个负整数，保存了HDFS存储系统信息结构的版本号。namespaceID：存储系统的唯一标识符。cTime：该存储系统信息的创建时间。当数据节点注册时，名字节点和数据节点需要对这些信息，即StorageInfo包含的信息进行检查，以保证当前注册的节点是HDFS的一个合法数据节点，而不是一个属于其他集群的节点，或者曾经属于集群，但未进行必要升级的节点，以保证存储系统的一致性。NamespaceInfo继承自StorageInfo，除了StorageInfo中的layoutVersion、namespanceID和cTime属性，它引入了buildVersion和distributedUpgradeVersion。成员变量buildVersion保存了系统构建的(subversion)版本号，distributedUpgradeVersion则用于数据节点升级前进行的版本检查。注意，NamespaceInfo中包含的信息是整个HDFS集群的信息，和具体的数据节点没有关系。</p>
<p>        数据节点通过DatanodeProtocol.register()方法向名字节点注册，注册成功后，数据节点通过DatanodeProtocol.blockReport()方法上报它所管理的全部数据块信息，DatanodeProtocol。sendHeartbeat()是数据节点与名字节点的心跳节点，当然这个接口还包括reportBadBlocks()、blockReceived()、errorReport()、processUpgradeCommand()等方法。blockReport()和sendHeartbeat()的返回值都和名字节点指令DatanodeCommand，它们的定义如下：</p>
<pre><code class="language-java">public interface DatanodeProtocol extends VersionedProtocol {
  /**
   * 25: Serialized format of BlockTokenIdentifier changed to contain
   *     multiple blocks within a single BlockTokenIdentifier
   *     
   *     (bumped to 25 to bring in line with trunk)
   */
  public static final long versionID = 25L;
  
  // error code
  final static int NOTIFY = 0;
  final static int DISK_ERROR = 1; // there are still valid volumes on DN
  final static int INVALID_BLOCK = 2;
  final static int FATAL_DISK_ERROR = 3; // no valid volumes left on DN

  /**
   * Determines actions that data node should perform 
   * when receiving a datanode command. 
   */
  final static int DNA_UNKNOWN = 0;    // unknown action   
  final static int DNA_TRANSFER = 1;   // transfer blocks to another datanode
  final static int DNA_INVALIDATE = 2; // invalidate blocks
  final static int DNA_SHUTDOWN = 3;   // shutdown node
  final static int DNA_REGISTER = 4;   // re-register
  final static int DNA_FINALIZE = 5;   // finalize previous upgrade
  final static int DNA_RECOVERBLOCK = 6;  // request a block recovery
  final static int DNA_ACCESSKEYUPDATE = 7;  // update access key
  final static int DNA_BALANCERBANDWIDTHUPDATE = 8; // update balancer bandwidth

  /** 
   * Register Datanode.
   *
   * @see org.apache.hadoop.hdfs.server.datanode.DataNode#dnRegistration
   * @see org.apache.hadoop.hdfs.server.namenode.FSNamesystem#registerDatanode(DatanodeRegistration)
   * 
   * @return updated {@link org.apache.hadoop.hdfs.server.protocol.DatanodeRegistration}, which contains 
   * new storageID if the datanode did not have one and
   * registration ID for further communication.
   */
  public DatanodeRegistration register(DatanodeRegistration registration
                                       ) throws IOException;
  /**
   * sendHeartbeat() tells the NameNode that the DataNode is still
   * alive and well.  Includes some status info, too. 
   * It also gives the NameNode a chance to return 
   * an array of "DatanodeCommand" objects.
   * A DatanodeCommand tells the DataNode to invalidate local block(s), 
   * or to copy them to other DataNodes, etc.
   */
  public DatanodeCommand[] sendHeartbeat(DatanodeRegistration registration,
                                       long capacity,
                                       long dfsUsed, long remaining,
                                       int xmitsInProgress,
                                       int xceiverCount) throws IOException;

  /**
   * blockReport() tells the NameNode about all the locally-stored blocks.
   * The NameNode returns an array of Blocks that have become obsolete
   * and should be deleted.  This function is meant to upload *all*
   * the locally-stored blocks.  It's invoked upon startup and then
   * infrequently afterwards.
   * @param registration
   * @param blocks - the block list as an array of longs.
   *     Each block is represented as 2 longs.
   *     This is done instead of Block[] to reduce memory used by block reports.
   *     
   * @return - the next command for DN to process.
   * @throws IOException
   */
  public DatanodeCommand blockReport(DatanodeRegistration registration,
                                     long[] blocks) throws IOException;
......
}</code></pre>
<h4><br>
InterDatanodeProtocol</h4>
<p>        InterDatanodeProtocol有三个方法，getBlockMetaDataInfo()、startBlockRevovery()、updateBlock()方法，其中getBlockMetaDataInfo并没有被其他HDFS代码使用，该方法可用于实现HDFS的维护工具。startBlockRevover和updateBlock用于数据块恢复。前面在ClientDatanodeProtocol接口的时候，就知道数据节点想客户端提供了错误恢复的远程方法：ClientDatanodeProtocol.recoverBlock方法。它用于对客户端写数据过程中出现的故障进行错误恢复；名字节点到数据节点的指令DNA_RECOVERBLOCK也可可发起一个数据块恢复。</p>
<p>         为IPC建立连接是一个开销比较大的操作，在正常的数据节点写操作的过程中，节点间不需要通过远程方法调用进行交互。但故障发生后，参与到这个写操作的数据节点汇总，有一个数据节点会被选中出来，协调其他数据节点，进行故障恢复，该过程需要使用到InterDatanodeProtocol中的这两个远程方法。</p>
<h4>NamenodeProtocol</h4>
<p>        名字节点实现了该远程接口，调用者有两个，一个是第二名字节点，另一个而是HDFS工具：均衡器balancer。均衡器使用了NamenodeProtocol的getBlocks()和getBlockKeys()方法，getBlocks()可以获取某一个数据节点上的一系列数据块及位置，根据这些返回值，均衡器可以把数据块从该数据节点移动到其他数据节点，达到平衡各个数据节点数据块数量的目的；getBlockKes()方法用于支持这个过程中需要的安全特性。</p>
<p>         NamenodeProtocol中的另外三个方法（getEditLogSize、rollEditLog、rollFsImage）相互配合，完成第二名字节点的功能：获取HDFS的命名空间镜像和镜像编辑日志，合并得到一个新的镜像，上传新命名空间镜像到名字节点，替换原有镜像并清空镜像编辑日志。<br></p>
<p> </p>
<strong> <span style="font-family:Arial;font-size:14px;text-align:left;line-height:26px;"> <span style="color:#ff0000;"> 版权申明：本文部分摘自【蔡斌、陈湘萍】所著【Hadoop技术内幕 深入解析Hadoop Common和HDFS架构设计与实现原理】一书，仅作为学习笔记，用于技术交流，其商业版权由原作者保留，推荐大家购买图书研究，转载请保留原作者，谢谢！</span></span></strong>
            </div>
                </div>
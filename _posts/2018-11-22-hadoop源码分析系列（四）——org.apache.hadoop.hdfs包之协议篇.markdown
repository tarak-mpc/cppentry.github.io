---
layout:     post
title:      hadoop源码分析系列（四）——org.apache.hadoop.hdfs包之协议篇
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="s">
<div><strong>摘要</strong>: hdfs包是hadoop HDFS的主要实现，首先分析下协议包，这个包定义了hdfs在不同节点中的通信协议，对于协议的分析有助于后面的章节对于hdfs服务端、客户端通信的深入理解，按照惯例，首先看一下这个包中几个孤立的类： ...</div>
</div>
hdfs包是hadoop HDFS的主要实现，首先分析下协议包，这个包定义了hdfs在不同节点中的通信协议，对于协议的分析有助于后面的章节对于hdfs服务端、客户端通信的深入理解，按照惯例，首先看一下这个包中几个孤立的类：<br><img id="aimg_42961" src="http://f.dataguru.cn/farattach/forum/201209/11/113412r66fzfex5hqx7eiw.png" class="zoom" alt="1.png" title="1.png" width="600"><br>
DataTransferProtocol接口：这个接口定义了客户端和datanode传输数据所采用的流协议，包含如下属性：<br><img id="aimg_42962" src="http://f.dataguru.cn/farattach/forum/201209/11/113412rsszazp4ds4dsdal.png" class="zoom" alt="2.png" title="2.png" width="194"><br>
特殊的说明下面几个：<br>
OP_REPLACE_BLOCK从balancer节点发出到目标节点，包含了block id, 来源和 proxy的信息，OP_COPY_BLOCK从目的节点发送到proxy，只包含block id，对OP_COPY_BLOCK的回应要发送block内容，对OP_REPLACE_BLOCK的回应是要包括操作状态的信息，HEARTBEAT_SEQNO是心跳报文的序列号。剩下的常量从名字就可以得到具体意义了。<br>
接口中的静态内部类PipelineAck对报文的返回做了初步解析，例如对心跳报文的解析，对返回状态的初步判断等等。<br><br>
ClientDatanodeProtocol接口主要定义了用于数据块恢复的方法recoverBlock。<br><br>
FSConstants主要定义了常量<br><br>
AlreadyBeingCreatedException异常类定义了创建正在创建的文件时的异常<br><br>
ClientProtocol接口定义了用户代码和namenode之前的交互，这个接口的方法将做详细分析，这也是api操作hdfs的基础方法：<br>
getBlockLocations(String src, long offset,long length)：获得指定的文件偏移块所在的datanode列表，按照和客户端距离排序<br><br>
create(String src, FsPermission masked,String clientName, boolean overwrite, <br>
short replication,long blockSize )：在namespace上创建一个新的文件实体，一旦创建了，这个文件对其他的client就可见了，还可以通过addBlock方法添加多个块。<br>
append(String src, String clientName)：在尾部追加文件<br>
setReplication(String src, short replication) ：对指定存在的文件设置冗余<br><br>
setPermission(String src, FsPermission permission)：对存在的文件设置权限<br>
abandonBlock(Block b, String src, String holder)：丢弃一个块<br>
addBlock(String src, String clientName)：追加块<br>
complete(String src, String clientName)：写块是否完成<br>
reportBadBlocks(LocatedBlock[] blocks)：报告坏块<br>
rename(String src, String dst)：重命名块<br>
delete(String src)：从fs上删除块<br>
delete(String src, boolean recursive)：递归删除<br>
mkdirs(String src, FsPermission masked) ：创建指定权限和名称的目录<br>
getListing(String src) ：获得指定目录下的文件列表<br>
getStats()：获得fs的统计信息<br>
getDatanodeReport(FSConstants.DatanodeReportType type)：返回此时datanodes的信息<br>
getPreferredBlockSize(String filename)：返回指定文件的块的大小<br>
setSafeMode(FSConstants.SafeModeAction action)：设置安全模式<br>
saveNamespace()：保存当前的namespace信息并重置edit log<br>
metaSave(String filename)：dump namenode的信息到指定文件<br>
fsync(String src, String client)：把指定的文件的元数据信息写到存储上<br><br>
UnregisteredDatanodeException类定义了没有注册的DataNode发生的异常信息<br><br>
BlockListAsLongs类的作用就是把块数组直接转换为一个long数组<br><br><br>
下面是一个异常体系：<br><img id="aimg_42963" src="http://f.dataguru.cn/farattach/forum/201209/11/113413sslbacha0lceeeuk.png" class="zoom" alt="3.png" title="3.png" width="600"><br>
首先理解下quota：一个目录的quota可能是磁盘quota，也可能是namespace的quota<br>
QuotaExceededException类说明了目录实际的quota和规定的quota发生了冲突<br>
NSQuotaExceededException类就是指namespace中目录实际的quota和规定的quota发生了冲突<br>
DSQuotaExceededException类就是指disk space中目录实际的quota和规定的quota发生了冲突<br><br><br>
协议部分最后一个类层次关系：<br><img id="aimg_42964" src="http://f.dataguru.cn/farattach/forum/201209/11/113413knp3672ykj6nyyk6.png" class="zoom" alt="4.png" title="4.png" width="600"><br>
DatanodeID类用主机、端口、存储id唯一标示了一个datanode，其中端口信息包括了info server端口和ipc server端口<br>
Block类是hdfs特有的存储结构，用一个long类型来标示，名字以blk_为前缀，有id、长度和时间戳标示<br>
LocatedBlock类顾名思义就是已分配空间，或者是已存储在namenode上的块，信息包括block的信息和DatanodeInfo[]<br>
LocatedBlocks记录了文件长度、已分配的块列表和是否正在分配<br>
DatanodeInfo类，在Datanode Protocol 和  Client Protocol中代表一个DataNode的状态信息，<br>
这个类主要分析下属性：<br><br><img id="aimg_42965" src="http://f.dataguru.cn/farattach/forum/201209/11/113413l4yr4ksbrsbplgpz.png" class="zoom" alt="5.png" title="5.png" width="211"><br>
AdminStates {NORMAL, 正常态 DECOMMISSION_INPROGRESS, 正在退役 DECOMMISSIONED;已经退役 }<br>
capacity：数据结点的总容量<br>
dfsUsed：已经使用的空间<br>
remaining：未使用的空间<br>
lastUpdate：最后的更新时间<br>
xceiverCount：和该结点相连的活动的连接数量<br>
location：默认机架<br>
hostName：DataNode注册时的主机名<br>
level：在node tree中的级别<br>
parent：父节点<br><br><br>
下面分析server.protocol自包中的协议<br><img id="aimg_42966" src="http://f.dataguru.cn/farattach/forum/201209/11/113414ripi1g2jn2r8s750.png" class="zoom" alt="6.png" title="6.png" width="600"><br><br>
BlockMetaDataInfo继承了Block类，增加了lastScanTime属性，记录上次扫描的时间<br>
NamespaceInfo类继承了StorageInfo类，除了记录基本的存储信息外还记录了build版本信息和更新的版本信息<br>
InterDatanodeProtocol接口继承了带版本的协议接口，可以用新的标示和长度来更新数据块<br>
DisallowedDatanodeException：datanode和namenode失去联系抛出的异常<br>
NamenodeProtocol接口继承了带版本的协议接口，这个协议是namenode和secondary之间通信用的，提供了对EditLog和FsImage操作的方法<br>
BlocksWithLocations提供了对于BlockLocations更为高效的序列化和反序列化的方法<br>
DatanodeRegistration类提供了对datanode进行标示和验证的在namenode上所有的信息<br>
DatanodeProtocol提供了datanode和namenode交互的协议<br><img id="aimg_42967" src="http://f.dataguru.cn/farattach/forum/201209/11/113414sqwgg41n6qu5ngg6.png" class="zoom" alt="7.png" title="7.png" width="164"><br>
NOTIFY、DISK_ERROR、INVALID_BLOCK定义了三个错误码<br>
DNA_UNKNOWN：不被识别的操作<br>
DNA_TRANSFER：数据块复制操作<br>
DNA_INVALIDATE：删除数据块<br>
DNA_SHUTDOWN：关闭节点<br>
DNA_REGISTER：重新注册<br>
DNA_FINALIZE：回收之前的升级<br>
DNA_RECOVERBLOCK：需要块恢复<br>
主要的方法：<br>
register(DatanodeRegistration registration)：注册datanode<br>
sendHeartbeat(DatanodeRegistration registration,long capacity,long dfsUsed, long remaining,int xmitsInProgress,int xceiverCount)发送心跳报文告诉namenode，datanode还活着，同时允许namenode在心跳报文中返回要执行的命令列表<br>
blockReport(DatanodeRegistration registration,long[] blocks)：告诉namenode节点中块情况<br>
blockReceived(DatanodeRegistration registration,Block blocks[],String[] delHints) ：告诉namenode最近收到的块信息，同时可以删除些冗余过量的块<br>
errorReport():报告错误<br>
commitBlockSynchronization：提交块同步<br><br><br><br><img id="aimg_42968" src="http://f.dataguru.cn/farattach/forum/201209/11/11341433o3vfd3rqqrvfro.png" class="zoom" alt="8.png" title="8.png" width="600"><br>
DatanodeCommand标示datanode执行的命令：注册、升级<br>
BlockCommand：块命令 主要是给别的datanode发送块<br>
UpgradeCommand：升级命令 执行升级有关的命令如开始升级、升级的状态等<br><br>
原创作品 转载请标明出处 <a href="http://f.dataguru.cn/thread-19209-1-1.html" rel="nofollow">
http://f.dataguru.cn/thread-19209-1-1.html</a> <br>            </div>
                </div>
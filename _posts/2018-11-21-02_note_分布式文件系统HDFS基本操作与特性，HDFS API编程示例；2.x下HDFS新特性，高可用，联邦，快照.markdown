---
layout:     post
title:      02_note_分布式文件系统HDFS基本操作与特性，HDFS API编程示例；2.x下HDFS新特性，高可用，联邦，快照
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="font-size:14px;color:#33ffff;"><strong>02_note_分布式文件系统HDFS原理与操作，HDFS API编程；2.x下HDFS新特性，高可用，联邦，快照</strong></span><br><strong>HDFS基本特性</strong><br><blockquote style="border:none;">/home/henry/app/hadoop-2.8.1/tmp/dfs/name/current - on namenode<br>
    <span></span>cat ./VERSION<br>
        <span></span>namespaceID (空间标识号，类似集群识别号)<br>
/home/henry/app/hadoop-2.8.1/tmp/dfs/data – on datanode<br>
    <span></span>ls -lR<br>
        <span></span>blk_1073741844xx  (数据块，典型的数据块大小是64M) <br>
/home/henry/app/hadoop-2.8.1/etc/hadoop/hdfs-site.xml<br>
    <span></span>&lt;name&gt;dfs.replication&lt;/name&gt;  (设定block副本数，所有block都有副本)<br>
机架感知功能(本机架其他机架都有副本，避免整个机架故障，也提高网络效率)<br>
    <span></span>core-site.xml中配置<br>
        <span></span>&lt;name&gt;topology.script.file.name&lt;/name&gt;  （指定机架感知脚本文件RackAware.py）<br>
        <span></span>&lt;name&gt;topology.script.number.args&lt;/name&gt;  （指定机架服务器数量）<br>
心跳机制<br>
    <span></span>datanode定期发送block report给namenode<br>
    <span></span>如果没有定期收到信号，则namenode会标记该datanode宕机，不会给IO需求<br>
    <span></span>如果datanode失敁造成副本数量下降，并且低于预先设置的阈值，namenode会在合适的时机迕行重新复制<br>
安全模式<br>
    <span></span>Namenode启动时先经过"安全模式"<br>
    <span></span>"安全模式"期间收集data block,当检测到副本数不足的data block，会复制达到最小副本数才结束安全模式<br>
    <span></span>hadoop dfsadmin -safemode enter  -   强制进去安全模式<br>
    <span></span>hadoop fs -put 等增删操作在安全模式下会报错<br>
    <span></span>hadoop dfsadmin -safemode leave  -  关闭安全模式<br>
校验和<br>
    <span></span>blk_1073741844xx.meta<span></span>(文件创立时，每个数据块都产生CRC校验和保存在blk-xxx.meta同名文件中，获取数据时检查以确定block是否损坏)<br>
打开回收站<br>
    <span></span>core-site.xml中配置<br>
        <span></span>&lt;name&gt;fs.trash.interval&lt;/name&gt;<br>
        <span></span>&lt;value&gt;10080&lt;/value&gt;  -  时间阈值(单位分钟)，-rm删除的文件会放到.trash下,过阈值时间后被删除，0则禁用<br>
            <span></span>恢复文件，-mv到相应目录即可<br>
            <span></span>-expunge, 清空回收站<br>
            <span></span>hadoop fs -rm -r -skipTrash  -  会直接删除不经过tash<br>
            <span></span>默认只有hadoop shell才会删除到trash，其他程序化操作不会move to trash,例如webHDFS,java API等，除非代码写入call to the trash functionality<br>
元数据保护<br>
    <span></span>映像文件和事务日志是namenode核心数据，可以配置为多个副本<br>
    <span></span>增强安全性，但降低namenode处理速度<br>
    <span></span>namenode依然是单点，故障需要手动切换2nd namenode<br><strong>Snapshot快照功能</strong>-2.x支持xxx重要需求xxx<br>
    <span></span>（http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html）<br>
    <span></span>snapshot不是整体备份，需要对某个目录创建snapshot则对该目录hdfs dfsadmin -allowSnapshot ./in （对./in目录开启）<br>
        <span></span>hdfs dfsadmin -allowSnapshot &lt;path&gt;  -  需要开启备份的folder，可以开启多个path<br>
        <span></span>hdfs dfsadmin -disallowSnapshot &lt;path&gt;<br>
            <span></span>hdfs dfs -createSnapshot &lt;path&gt; [&lt;snapshotName&gt;]<br>
            <span></span>&lt;snapshotName&gt;optional参数，不设置则defaul with the format "'s'yyyyMMdd-HHmmss.SSS", e.g. "s20130412-151029.033"<br>
            <span></span>hdfs dfs -deleteSnapshot &lt;path&gt; &lt;snapshotName&gt;<br>
            <span></span>hdfs dfs -renameSnapshot &lt;path&gt; &lt;oldName&gt; &lt;newName&gt;<br>
            <span></span>hdfs lsSnapshottableDir  -  获取当前用户权限下的所有开启snapshot的目录<br>
            <span></span>hdfs dfs -ls ./in/.snapshot  -  访问snapshot需要后边加/.snapshot才可以<br>
            <span></span>hdfs snapshotDiff &lt;path&gt; &lt;fromSnapshot&gt; &lt;toSnapshot&gt;  -  获取不同snapshot的更改记录<br>
                    <span></span>+    The file/directory has been created.<br>
                    <span></span>-    The file/directory has been deleted.<br>
                    <span></span>M    The file/directory has been modified.<br>
                    <span></span>R    The file/directory has been renamed.<br>
Block Pool (块池的概念)<br>
HDFS Federation (HDFS联邦)<br>
    <span></span>并非HA，不是互相冗余，namenode失效会造成部分数据无法访问，类似拼图的概念<br>
    <span></span>比较大的集群，遇到namenode性能瓶颈才会需要<br>
    <span></span>hdfs-site.xml配置(多个namenode)<br>
    <span></span>格式化多个namenode<br>
        <span></span>hdfs namenode -format[-clusterId &lt;cluster_id&gt;] &gt; 第一个namenode,clusterid可选，会自动生成<br>
        <span></span>hdfs namenode -format -clusterId &lt;cluster_id&gt; &gt; 第二个namenode，必须指定clusterID,才能跟第一个namenode绑定在同一个Federation<br>
2.x 支持多个NameNode,可以分散负载，得到性能保障<br>
命名空间管理 - Client Side Mount Table<br>
添加新datanode节点<br>
    <span></span>install hadoop on new datanode and copy config from namenode<br>
    <span></span>update masters and slaves file on all namenode and datanode<br>
    <span></span>config no pwd access <br>
    <span></span>start datanode and nodemanager<br>
    <span></span>start-balancer.sh 进行负载均衡</blockquote>
<strong>Java连接HDFS, Run URLCat.java 示例程序</strong><br><blockquote style="border:none;">    echo $HADOOP_CLASSPATH 返回hadoop-env.sh默认的classpath配置，并copy新建HADOOP_CLASSPATH到/etc/profile<br>
    自己存放.class的path添加到HADOOP_CLASSPATH中 source /etc/profile<br>
    需要配置HADOOP_CLASSPATH,把用来放.class文件的path放进去(/home/henry/myjava/class/),否则在运行编译后的程序会报错 "Could not find or load main class"<br>
    HADOOP_CLASSPATH可以在HADOOP_HOME/etc/hadoop/hadoop_env.sh配置，也可以直接在/etc/profile配置<br>
    hadoop classpath  &gt;  取回当前的HADOOP_CLASSPATH配置<br>
    cd ~/myjava<br>
    javac -cp ../app/hadoop-2.8.1/share/hadoop/common/hadoop-common-2.8.1.jar URLCat.java  -d ./class/<br>
    javac -cp $HADOOP_CLASSPATH URLCat.java -d ./class/  （因为已经配置相关的.jar包到HADOOP_CLASSPATH,可以直接调用）<br>
        <span></span>javac编译的时候一定要加上已经配置的class path，比如-d ./class/, 负责运行时找不到会报错, 因为已经把.class path配置到HADOOP_CLASSPATH<br>
    1) hadoop URLCat hdfs://master.henry:9000/user/henry/in/README.txt (需要把.class path配置到HADOOP_CLASSPATH,编译后可以在任意path下运行)<br>
        <span></span>jar -cvf URLCat.jar<br>
    2) hadoop jar ./URLCat.jar URLCat hdfs://master.henry:9000/user/henry/in/README.txt <br>
        <span></span>不需要把.class path配置到HADOOP_CLASSPATH<br>
        <span></span>但是必须在存放.class的path下运行才可以，否则识别不到.class文件<br>
        Eclipse下写的.java代码需要去掉包名再编译成class文件(或直接linux下javac编译)再移动到Linux下打包成jar文件<br>
    修改Java文件使用UTF-8编码,否则linux上编译乱码报错<br>
        <span></span>菜单导航栏上Window--&gt;Preferences 打开"首选项"对话框，左侧导航树，导航到 General--&gt;Workspace<br>
        <span></span>修改单个项目的Java文件编码为UTF-8,package或者文件右键properties&gt;resource&gt;text file encoding<br>
        <span></span>javac -encoding utf-8 (指定编译使用UTF-8编码)</blockquote>
<strong>Ant使用</strong><br>
    <span></span>download and tar apache_ant<br>
    <span></span>add ant_home and bin to /etc/profile<br>
    <span></span>ant -version ok<br>
    <span></span>Copy MapReduce_Cookbook_Code到Mater<br>
    <span></span>随便进入源码目录，主要有.src目录(存放.java)和build.xml配置文件<br>
    <span></span>build.xml - 配置文件(比如编译使用的jar包路径,编译源目录，编译后.class放置路径等等)<br>
    <span></span>执行ant命令会直接生成一个build目录(存放.class文件) - build.xml中配置的
            </div>
                </div>
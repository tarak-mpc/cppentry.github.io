---
layout:     post
title:      Hadoop分布式文件系统HDFS——Flume和Sqoop导入数据，distcp并行复制，Hadoop存档
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/zhaojw_420/article/details/60135990				</div>
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>一、Flume和Sqoop导入数据 <br>
     Apache Flume是一个将大规模流数据导入HDFS的工具，最典型的应用是从另外一个系统中手机日志数据。比如银行的网络服务器。其特性如下：</p>

<pre><code>1、 Flume能够支持大量的数据源，其中的一些通常用于包含tail（通过管道的方式将本地文件写入Flume中），syslog和apache log4j（允许java应用通过Flume将事件写入HDFS中的文件）的系统。
2、 Flume节点允许以任何拓扑方式进行组织。典型配置是在每个源机器运行一个Flume节点，通过多个层级的聚合节点，最后将数据存入HDFS中。
3、Flume提供了不同级别的数据投递可靠性。例如最大努力投递，端到端投递。

 Apache Sqoop是为了将数据从结构化存存储设备批量导入HDFS中设计的，例如关系型数据库。Sqoop的应用场景，是组织将白天生产的数据库中的数据在晚间导入Hive数据仓库中心进行分析。
</code></pre>

<p>二、distcp并行复制 <br>
         distcp分布式复制程序，可以从Hadoop文件系统间复制大量数据，也可以将大量的数据复制到Hadoop中。其典型的应用场景是在两个HDFS集群之间传输数据。 <br>
命令：bin/hadoop distcp hdfs://namenode1/foo hdfs://namenode2/har <br>
这行指令把第一个集群的/foo目录及其内容复制到第二个集群的/har目录下。所以第二个集群最后的目录结构是/bar/foo。若/bar不存在，则新建一个。也可以指定多个源路径，并把所有的路径都复制到目标路径下。<strong>注意源路径是绝对路径。</strong> <br>
     默认情况下，distcp可以跳过目标路径已存在的文件，但可以通过orverview选项覆盖现有的文件。也可以通过-update选项选择有改动的文件。 <br>
     distcp是作为一个MapReduce作业来实现的，该复制作业是通过集群中并行运行的map实现的。每个文件通过一个map进行复制，并且distcp试图为每个map分配大致相等的数据来执行，即把文件划分为大致相等的块。 <br>
     如果在不同版本HDFS的集群上使用distcp复制数据时会导致复制失败。弥补这种情况，可以使用基于只读HTTP协议的HFTP文件系统并从源文件系统读取数据。这个作业必须运行在目标集群上，进而实现HDFS RPC版本的兼容。 <br>
bin/hadoop distcp hftp://namenode1:50070/foo hdfs://namenode2/bar  <br>
注意：需要在URI源中指定namenode的web端口。这是由dfs.http.address属性决定的，其默认值50070。 <br>
使用新的webhdfs协议后(替代hftp)，对源集群和目标集群均可以使用HTTP协议进行通信。且不会造成任何不兼容的问题。 <br>
bin/hadoop distcp webhftp://namenode1:50070/foo hdfs://namenode2/bar <br>
还有一种方法是使用了HDFS HTTP代理服务器作为源distcp或者目标distcp，进而具备了设置防火墙和控制带宽的优点。 <br>
三、Hadoop存档工具–archive <br>
命令：bin/hadoop archive -archiveName  file.har  /my/files /my <br>
     第一个选项是存档文件的名称，这里是file.har。HAR文件总是以.har为扩展名的文件。然后是需要存档的文件，例如/my/files下的文件。最后一个是HAR文件的输出目录。 <br>
HAR文件的组成：两个索引文件以及部分文件的集合。 <br>
不足：新建一个存档文件会创建一个存档文件的副本。因此至少需要与要存档的文件容量相同大小的磁盘空间。目前不支持存档文件的压缩。 <br>
存档文件一旦创建就不能修改，要想从其中增加或者删除文件，必须重新创建的那个文件。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
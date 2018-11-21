---
layout:     post
title:      HDFS的SecondaryNameNode
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kaede1209/article/details/82012585				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h2>HDFS</h2>

<p>HDFS 采用Master/Slave的架构来存储数据，该架构主要由四个部分组成</p>

<ul><li>HDFS Client</li>
	<li>NameNode</li>
	<li>DataNode</li>
	<li>SecondaryNameNode</li>
</ul><h2>HDFS体系结构</h2>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/5959612-041373c34b34f1b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700"></p>

<p>HDFS体系结构</p>

<p><strong>HDFS Client</strong></p>

<blockquote>
<p>文件切分，文件上传 HDFS 的时候，Client 将文件切分成 一个一个的Block，然后进行存储<br>
与 NameNode 交互，获取文件的位置信息<br>
与 DataNode 交互，读取或者写入数据<br>
Client 提供一些命令来管理 HDFS，比如启动或者关闭HDFS<br>
Client 可以通过一些命令来访问 HDFS</p>
</blockquote>

<p><strong>NameNode</strong></p>

<blockquote>
<p>master，一个管理者，不实际存储数据<br>
管理 HDFS 的名称空间，维护着文件系统树以及整个树的所有文件和目录(fsimage+edits)<br>
管理数据块（Block）映射信息<br>
配置副本策略<br>
处理客户端读写请求</p>
</blockquote>

<p><strong>DataNode</strong></p>

<blockquote>
<p>Slave，NameNode 下达命令，DataNode 执行实际的操作<br>
存储实际的数据块<br>
执行数据块的读/写操作</p>
</blockquote>

<p><strong>SecondaryNameNode</strong></p>

<blockquote>
<p>辅助NameNode，分担其工作量<br>
定期合并fsimage和edits，并推送给NameNode，紧急情况下，可辅助恢复NameNode</p>
</blockquote>

<p><img alt="" class="has" src="//upload-images.jianshu.io/upload_images/5959612-c78b6423e1f5fe86.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700"></p>

<p>Fsimage和edits合并</p>

<p>Fsimage文件包含整个文件系统所有的文件和目录，是文件系统元数据的持久化检查点，当NameNode重启后都需载入fsimage进入内存，恢复到某个检查点，再执行检查点后的编辑日志，进行重建。</p>

<p>edits是编辑日志文件，记录检查点后所有文件等信息的改动</p>

<p><strong>Fsimage和Edits合并过程？</strong></p>

<blockquote>
<p>1 secondarynamenode周期性通过edits文件大小，当达到合并的阈值后，Namenode停止使用 edits文件，并生成一个新的临时edits.new文件</p>
</blockquote>

<blockquote>
<p>2 secondarynamenode通过http的get方式获取namenode节点上的edits和fsimage文件</p>
</blockquote>

<blockquote>
<p>3 secondarynamenode将fsimage载入内存并逐一执行edits文件中的操作</p>
</blockquote>

<blockquote>
<p>4 执行完毕后，生成fsimages.ckpt，会向namenode发送http请求，通知namenode来获取fsimage.ckpt文件</p>
</blockquote>

<blockquote>
<p>5 NameNode更新fsimage文件中的记录检查点执行时间，改名为fsimage文件</p>
</blockquote>

<blockquote>
<p>6 edits.new文件更名为edits文件</p>
</blockquote>            </div>
                </div>
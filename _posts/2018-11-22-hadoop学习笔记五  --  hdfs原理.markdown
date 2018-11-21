---
layout:     post
title:      hadoop学习笔记五  --  hdfs原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/mingtian625/article/details/49234053				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>首先说hdfs的原理：</p>
<p><img src="https://img-blog.csdn.net/20151018230337447?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
<p>hdfs的原理会从三个方面描述，一个方面是提交文件到hdfs的原理，一方面是从hdfs下载文件的原理，最后是SecondaryNameNode工作原理</p>
<p>#上传文件到hdfs</p>
<p>*客户端提交文件信息到namenode，然后namenode会接收要上传的文件信息，根据文件的大小和实际的datanode的使用情况分配空间生成metadata，Namenode始终在内存中保存metedata，用于处理“读请求”</p>
<p><br></p>
<p>*当有“写请求”到来时，namenode会首先写editlog到磁盘，即向edits文件中写日志，成功返回后，才会修改内存；关于namenode，Hadoop会维护一个fsimage文件，也就是namenode中metedata的镜像，但是fsimage不会随时与namenode内存中的metedata保持一致，而是每隔一段时间通过合并edits文件来更新内容。Secondary namenode就是用来合并fsimage和edits文件来更新NameNode的metedata的。</p>
<p><br></p>
<p>*namenode在内存中修改完成以后，会向客户端返回文件要提交到的节点信息 , </p>
<p><br></p>
<p>*客户端获取完信息以后，然后会将数据写入到对应的节点上</p>
<p><br></p>
<p>#hdfs下载文件</p>
<p>*客户端会先请求namenode，然后nameNode会将文件所在的位置和节点信息，还有大小返回给客户端，然后客户端根据返回信息来从不同的datanode获取文件合并下载</p>
<p><br></p>
<p><br></p>
<p><br></p>
<p>#secondarynamenode原理</p>
<p><img src="https://img-blog.csdn.net/20151019000930790?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p><br></p>
<p>*secondary通过rpc方式通知namenode切换edits文件<br>
*secondary通过http的post方式从namenode下载fsimage和edits，这两个文件相当于secondarynamenode在内存中的镜像，fsimage是上次合并的磁盘镜像，edits相当于日志文件，每次namenode有 写请求  的时候，都会先将metadata写到edits成功以后，再修改到内存中，edits相当于增量备份的概念 <br>
*secondary将fsimage载入内存，然后开始合并edits<br>
*secondary将新的fsimage通过http方式发回给namenode<br>
*namenode用新的fsimage替换旧的fsimage<br></p>
<p><br></p>
            </div>
                </div>
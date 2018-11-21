---
layout:     post
title:      Hadoop之——HDFS随笔
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/l1028386804/article/details/45896307				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://blog.csdn.net/l1028386804/article/details/45896307" rel="nofollow">转载请注明出处：http://blog.csdn.net/l1028386804/article/details/45896307</a><br></p>
<p>1.对hdfs的操作方式：hadoop fs xxx</p>
  hadoop fs -ls  /    查看hdfs的根目录下的内容的<br>
  hadoop fs -lsr /    递归查看hdfs的根目录下的内容的<br>
  hadoop fs -mkdir /d1    在hdfs上创建文件夹d1<br>
  hadoop fs -put &lt;linux source&gt; &lt;hdfs destination&gt; 把数据从linux上传到hdfs的特定路径中<br>
  hadoop fs -get &lt;hdfs source&gt; &lt;linux destination&gt; 把数据从hdfs下载到linux的特定路径下<br>
  hadoop fs -text &lt;hdfs文件&gt;    查看hdfs中的文件<br>
  hadoop fs -rm        删除hdfs中文件<br>
  hadoop fs -rmr    删除hdfs中的文件夹<br><br>
  hadoop fs -ls hdfs://hadoop0:9000/<br><br><br>
2.HDFS的datanode在存储数据时，如果原始文件大小&gt;64MB,按照64MB大小切分；如果&lt;64MB，只有一个block，占用磁盘空间是源文件实际大小。<br><br>
3.RPC(remote procedure call)<br>
  不同java进程间的对象方法的调用。<br>
  一方称作服务端(server)，一方称作客户端(client)。<br>
  server端提供对象，供客户端调用的，被调用的对象的方法的执行发生在server端。<br>
  <br>
  RPC是hadoop框架运行的基础。<br>
4.通过rpc小例子获得的认识？<br>
4.1 服务端提供的对象必须是一个接口，接口extends VersioinedProtocal<br>
4.2 客户端能够的对象中的方法必须位于对象的接口中。<br><br>            </div>
                </div>
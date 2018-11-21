---
layout:     post
title:      hadoop中hdfs通讯原理
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、对hdfs的操作方式：hadoop fs fs *****</p>
<p><span></span>hadoop fs -ls /   查看hdfs的跟目录下的内容</p>
<p><span></span>hadoop fs -lsr /  递归hdfs的根目录下的内容</p>
<p><span></span>hadoop fs mkdir  /d1 在hdfs上创建文件夹</p>
<p>        hadoop fs -put   xxx       ******   把linux数据从xxxxx上次到hdfs目录******下</p>
<p>        hadoop fs -get    xxxx    ******   把数据从hdfs下载到linux特定路径</p>
<p>        hadoop fs -text  查看hdfs中的文件</p>
<p>        hadoop fs -rm  删除hdfs中的文件</p>
<p>        hadoop fs  -rmr   删除hdfs中的文件夹</p>
<p>2、HDFS的datanode在存储数据时，如果原始文件大小&gt;64mb，按照64mb切分，反之直接占用64mb</p>
<p>3、RPC(remote procedure call)</p>
<p>      不同的java进程间的对象方法的调用</p>
<p>      一方称作服务端（server)，一方称作客户端（client),</p>
<p>      server端提供对象，供客户端调用的。被调用的对象的方法的执行发生在server</p>
<p>      RPC是hadoop框架运行的基础，</p>
<p>4、通过rpc小例子获得的认识？</p>
<p><span></span>服务端通过的对象是一个接口，接口extends VersionedProtocal</p>
<p> <span> </span>客户端能过的对象中的方法必须位于对象的接口中，</p>
            </div>
                </div>
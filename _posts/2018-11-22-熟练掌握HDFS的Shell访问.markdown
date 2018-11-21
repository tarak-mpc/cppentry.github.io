---
layout:     post
title:      熟练掌握HDFS的Shell访问
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>HDFS设计的主要目的是对海量数据进行存储，也就是说在其上能够存储很大量文件（可以存储TB级的文件）。HDFS将这些文件分割之后，存储在不同的DataNode上， HDFS 提供了两种访问接口：Shell接口和Java API 接口，对HDFS里面的文件进行操作，具体每个Block放在哪台DataNode上面，对于开发者来说是透明的。</p>

<p>下面将介绍通过Shell接口对HDFS进行操作，HDFS处理文件的命令和Linux命令基本相同，这里区分大小写</p>

<p><strong>目录</strong></p>

<p>1、shell操作单个HDFS集群</p>

<p>2、shell操作多个HDFS集群</p>

<p>3、hadoop管理员其他常见shel操作</p>

<p><strong>1、shell 操作单个HDFS集群</strong></p>

<p>下面列举出几个常用场景下的命令</p>

<p>创建文件夹 <br>
　　HDFS上的文件目录结构类似Linux，根目录使用”/”表示。</p>

<p>　　下面的命令将在/middle(已存在)目录下建立目录weibo</p>

<p>　　[hadoop@ljc hadoop]$ hadoop fs -mkdir /middle/weibo</p>

<p>　　效果如下：</p>

<p>　　<img src="https://img-blog.csdn.net/20180812162924573?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>上传文件weibo.txt到weibo目录下 <br>
　　[hadoop@ljc ~]$ hadoop fs -put weibo.txt /middle/weibo/</p>

<p>　　效果如下：</p>

<p>　　<img src="https://img-blog.csdn.net/20180812163140213?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>　　还可以使用 -copyFromLocal参数。</p>

<p>　　[hadoop@ljc ~]$ hadoop fs -copyFromLocal weibo.txt /middle/weibo/</p>

<p>查看weibo.txt文件内容。 <br>
　　[hadoop@ljc ~]$ hadoop fs -text /middle/weibo/weibo.txt</p>

<p>　　效果如下： <br>
　　 <br>
   <img src="https://img-blog.csdn.net/20180812163153616?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　还可以用 -cat、-tail 参数查看文件的内容。但是对于压缩的结果文件只能用 -text 参数来查看，否则是乱码。</p>

<p>　　[hadoop@ljc ~]$ hadoop fs -cat /middle/weibo/weibo.txt</p>

<p>　　[hadoop@ljc ~]$ hadoop fs -tail /middle/weibo/weibo.txt</p>

<p>通过终端向”/middle/weibo/weibo.txt”中输入内容 <br>
　　[hadoop@ljc ~]$ hadoop fs -appendToFile - /middle/weibo/weibo.txt</p>

<p>　　如下所示：</p>

<p>　　<img src="https://img-blog.csdn.net/20180812163325319?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>　　退出终端输入，按Ctrl + C</p>

<p>把”/middle/weibo/weibo.txt”复制到”/middle” <br>
　　[hadoop@ljc ~]$ hadoop fs -cp /middle/weibo/weibo.txt /middle</p>

<p>　　效果如下：</p>

<p>　　<img src="https://img-blog.csdn.net/20180812163352638?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>把weibo.txt文件复制到本地。 <br>
　　[hadoop@ljc ~]$ hadoop fs -get /middle/weibo/weibo.txt</p>

<p>　　效果如下：</p>

<p>　　<img src="https://img-blog.csdn.net/20180812163422301?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>　　还可以用 -copyToLocal 参数。</p>

<p>　　[hadoop@ljc ~]$ hadoop fs -copyToLocal /middle/weibo/weibo.txt</p>

<p>删除weibo.txt文件。 <br>
　　[hadoop@ljc ~]$ hadoop fs -rm /middle/weibo/weibo.txt</p>

<p>　　效果如下： <br>
<img src="https://img-blog.csdn.net/20180812163433491?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　</p>

<p>删除/middle/weibo文件夹。 <br>
　　[hadoop@ljc ~]$ hadoop fs -rm -r /middle/weibo</p>

<p>　　效果如下：</p>

<p>　　<img src="https://img-blog.csdn.net/20180812163448169?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>显示 /middle 目录下的文件。 <br>
　　[hadoop@ljc ~]$ hadoop fs -ls /middle</p>

<p>　　效果如下：</p>

<p>　　<img src="https://img-blog.csdn.net/20180812163457496?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><strong>2、shell 操作多个 HDFS 集群</strong></p>

<p>　　上面我们介绍的是访问单个HDFS集群，但是多个Hadoop集群需要复制数据该怎么办呢？幸运的是，Hadoop 有一个有用的distcp分布式复制程序，该程序是由 MapReduce作业来实现的，它是通过集群中并行运行的map来完成集群之间大量数据的复制。下面我们将介绍 distcp在不同场景下该如何使用</p>

<p>两个集群运行相同版本的Hadoop,确保两个集群版本相同，这里以hadoop1、hadoop2集群为例，如下所示 <br>
<img src="https://img-blog.csdn.net/20180812163725954?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
<img src="https://img-blog.csdn.net/20180812163739739?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>　　1)、两个 HDFS 集群之间传输数据，默认情况下 distcp 会跳过目标路径下已经存在的文件 <br>
[hadoop@hadoop1 ~]$ hadoop distcp /weather hdfs://hadoop2:9000/middle <br>
效果如下： <br>
<img src="https://img-blog.csdn.net/20180812163803513?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　这条指令是在hadoop1中执行，意思是把/weather目录及其内容复制到hadoop2集群的/middle目录下，所以hadoop2集群最后的目录结构为/middle/weather <br>
如下所示 <br>
　　　　<img src="https://img-blog.csdn.net/20180812163822212?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>　　如果/middle 不存在，则新建一个。也可以指定多个源路径，并把所有路径都复制到目标路径下。 <br>
        这里的目标路径（hadoop2）必须是绝对路径，源路径（hadoop1）可以是绝对路径，也可以是相对路径，因为我是在hadoop1中执行的，且默认是HDFS协议 <br>
      在执行这条指令时可能会报错 <br>
如下所示 <br>
<img src="https://img-blog.csdn.net/20180812163856685?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　　　 <br>
   这是因为没有把hadoop2(hadoop2对应IP：192.168.233.130)追加到/etc/hosts文件中，如下所示 <br>
<img src="https://img-blog.csdn.net/20180812163910314?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　　　 <br>
　如果指令在hadoop2中执行，可以这样写，如下</p>

<p>[hadoop@hadoop2 ~]$ hadoop distcp hdfs://hadoop1:9000/weather /middle <br>
效果如下： <br>
　　　　<img src="https://img-blog.csdn.net/20180812163933806?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>　　这时，源路径就必须写绝对路径，目录路径可以是绝对路径，也可以是相对路径，因为我是在hadoop2中执行的，且默认是HDFS协议，如果报错，请参考上面</p>

<p>2)、两个 HDFS 集群之间传输数据，覆盖现有的文件使用overwrite</p>

<p>[hadoop@hadoop1 ~]$ hadoop distcp -overwrite /weather hdfs://hadoop2:9000/middle/weather <br>
如下所示 <br>
<img src="https://img-blog.csdn.net/20180812163955281?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　　　 <br>
注意，在overwrite时，只是将/weather中的内容覆盖到”hdfs://hadoop2:9000/middle/weather”中，不包含/weather目录本身，所以在overwrite时，目录路径加上了/weather</p>

<p>　 3)、两个 HDFS 集群之间传输数据，更新有改动过的文件使用update。 <br>
[hadoop@hadoop1 ~]$ hadoop distcp -update /weather hdfs://hadoop2:9000/middle/weather <br>
效果如下：</p>

<p>　　　　<img src="https://img-blog.csdn.net/20180812164020374?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>注意，在update时，只是将/weather中的内容覆盖到”hdfs://hadoop2:9000/middle/weather”中，不包含/weather目录本身，所以在update时，目录路径加上了/weather</p>

<p>两个集群运行不同版本的Hadoop <br>
　　不同版本Hadoop集群的RPC是不兼容的，使用distcp复制数据并使用hdfs协议，会导致复制作业失败。想要弥补这种情况，可以在下面两种方式选择一种；下面以hadoop1、hadoop3两个集群为例，版本如下</p>

<p>　　　　<img src="https://img-blog.csdn.net/20180812164051705?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　　　<img src="https://img-blog.csdn.net/20180812164112883?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>　1)、基于hftp实现两个HDFS集群之间传输数据 <br>
[hadoop@hadoop3 ~]$ hadoop distcp hftp://hadoop1:50070/weather /middle <br>
如下所示 <br>
<img src="https://img-blog.csdn.net/20180812164133352?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　　　 <br>
有三点需要说明：</p>

<p>1、这个命令必须运行在目标集群上，进而实现hdfs RPC版本的兼容 <br>
  2、hftp地址由dfs.http.address属性决定的，其端口默认值为50070 <br>
  3、该命令是将hftp://hadoop1:9000/weather中内容传输到/middle目录中，不包含/middle目录本身</p>

<p>　2)、基于webhdfs实现两个HDFS集群之间传输数据</p>

<p>　　如果使用新出的webhdfs协议（替代 hftp）后，对源集群和目标集群均可以使用 HTTP协议进行通信，且不会造成任何不兼容的问题 <br>
[hadoop@hadoop3 ~]$ hadoop distcp webhdfs://hadoop1:50070/weather webhdfs://hadoop3:50070/middle <br>
如下所示 <br>
　　　　<img src="https://img-blog.csdn.net/20180812164155447?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p><strong>3、Hadoop管理员其他常见shell操作</strong></p>

<p>　　掌握了 shell 如何访问 HDFS，作为 Hadoop 管理员，还需要掌握如下常见命令</p>

<p>查看正在运行的Job。 <br>
　　[hadoop@hadoop1 ~]$ hadoop job –list</p>

<p>　　如下所示</p>

<p>　　<img src="https://img-blog.csdn.net/20180812164222708?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>关闭正在运行的Job <br>
　　[hadoop@hadoop1 ~]$ hadoop job -kill job_1432108212572_0001</p>

<p>　　如下所示 <br>
<img src="https://img-blog.csdn.net/20180812164233428?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　</p>

<p>检查 HDFS 块状态，查看是否损坏。 <br>
　　[hadoop@hadoop1 ~]$ hadoop fsck /</p>

<p>检查 HDFS 块状态，并删除损坏的块。 <br>
　　[hadoop@hadoop1 ~]$ hadoop fsck / -delete</p>

<p>检查 HDFS 状态，包括 DataNode 信息。 <br>
　　[hadoop@hadoop1 ~]$ hadoop dfsadmin -report</p>

<p>Hadoop 进入安全模式。 <br>
　　[hadoop@hadoop1 ~]$ hadoop dfsadmin -safemode enter</p>

<p>　　如下所示 <br>
<img src="https://img-blog.csdn.net/20180812164251401?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""> <br>
　　</p>

<p>Hadoop 离开安全模式。 <br>
　　[hadoop@hadoop1 ~]$ hadoop dfsadmin -safemode leave</p>

<p>　　如下所示</p>

<p>　　<img src="https://img-blog.csdn.net/20180812164314511?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>

<p>平衡集群中的文件 <br>
　　[hadoop@hadoop1 ~]$ /usr/java/hadoop/sbin/start-balancer.sh</p>

<p>　　start-balancer.sh命令位于hadoop安装路径下的/sbin下</p>

<p>　　如下所示</p>

<p>　　<img src="https://img-blog.csdn.net/20180812164327772?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3picTEzMzQ=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" alt="这里写图片描述" title=""></p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
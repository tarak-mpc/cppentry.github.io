---
layout:     post
title:      Hadoop——hdfs原理详解
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'comic sans ms', sans-serif;">      首先，<span style="font-family:SimSun;">HDFS是Hadoop的分布式文件系统，简单的说就是hadoop用来存储文件的，HDHS是一个主从结构，一个HDFS是由NameNode（名字节点），和若干个DataNode（数据节点），稍后详细介绍，HDFS对外开放文件命名空间并允许用户数据以文件形式存储。<br></span></span></p>
<h2> 接下来让我们用一张图来详细说明HDFS的实现机制</h2>
<p><img src="https://img-blog.csdn.net/20170511161432474?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzM3MzQ1Mjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="910" height="552"></p>
<p>    由上图可以看出hdfs是通过分布式集群来存储文件的，并且为客户端提供一个便捷的访问方式，即一个虚拟目录结构 <br></p>
<p>      <img src="https://img-blog.csdn.net/20170511162508378?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzM3MzQ1Mjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>    1.客户端读取文件test.txt,并将文件分块（block1,block2,block3）</p>
<p>     2.将分好的block块放在若干台datanode上，（本例有3个，通常一个datanode对应一台服务器）</p>
<p>     3.namenode用来保存并且管理文件与真实block的映射关系。</p>
<p>     4.每个block在集群中会有多个副本，默认3个，好处是可以提高数据的可靠性，还可以提高访问的吞吐量。</p>
<h2>NameNode管理数据的机制</h2>
<p>     namenode不存储物理数据，只存储某个客户端虚拟路径对应的各个datanode的文件分块信息，所有的文件读取和写入都得经过namenode,由namenode 统一管理</p>
<p>   <img src="https://img-blog.csdn.net/20170516120219497?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzM3MzQ1Mjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" width="910" height="545"><br></p>
<p>   上图介绍了一个文件上传的具体流程，接下来我们讲解namenode的edits log ,内存，以及fsimages的关系以及具体的运作方式</p>
<p>    1.NameNode为什么不直接将文件和DataNode分块的映射关系直接写如磁盘？</p>
<p>    这是由于效率，我们知道读取磁盘的熟读和直接读取内存比起来简直差太多了，当数据量过大时，简直就是一个悲剧</p>
<p>    2.既然内存效率高，为什么不直接保存到内存呢？</p>
<p>    数据是宝贵的，如果我们直接将映射信息保存到内存，万一断电了呢？所有的datanode节点再也找不回来了，所以磁盘是必须的，内存只是为了提高读取效率<br></p>
<p>    3.可是我们发现，nameNode好像没有直接将datanode的分块信息写在内存中，这是为什么呢？</p>
<p>    NameNode直接将文件分块信息写在edits log日志中，第一，是为了防止写了一半之后，系统出现故障，比如断电等；第二，将最新的文件分块数据写在edits log中，那么在将文件分块信息持久化到磁盘的时候，就不用直接从内存中读取数据，而是直接读取edits log中的数据，这样可以减轻内存的压力，内存只需要负责客户端读取的职责；</p>
<p>   4.那么edits log 中的信息是什么时候持久化到磁盘的呢？</p>
<p>    namenode为我们提供了一个阈值，用来控制edits log信息持久化到磁盘，也就是与fsimages合并，edits log 本身是一个很小的文件，这样做也是为了将其中的信息读取到内存中是的速度快，并且这也是没有将文件分块信息直接写到磁盘的原因，让我们想想，如果把文件分块信息直接持久化到内存，而不是写到edits log中，那么当namenode接到客户端将数据成功上传到datanoide的通知后，需要将文件分块的映射信息读取到内存中，读取edits log 这个小文件会很快，而读取磁盘（日积月累，磁盘上保存了大量的信息）就显得太慢了。</p>
<h2>edits log 中的文件分块映射信息合并到fsimages中</h2>
<p>   当edits log中的信息达到我们设定的阈值（如果不设定，使用默认值），会将自己的信息追加到fsimages,具体只执行逻辑由ScondeNode控制，具体流程如下图</p>
<p><img src="https://img-blog.csdn.net/20170516153411473?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvcXFfMzM3MzQ1Mjk=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p>  最后上传上来的新的fsimges覆盖旧的，new edits 顶替edits log ，NameNode继续执行。<br></p>
<p><br></p>
            </div>
                </div>
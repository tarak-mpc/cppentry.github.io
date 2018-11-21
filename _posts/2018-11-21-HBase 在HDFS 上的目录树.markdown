---
layout:     post
title:      HBase 在HDFS 上的目录树
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_19968255/article/details/82803701				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>自0.96版本之后，hbase 源码结构上做了很大的优化，目录结构也发生了变化，做了精简和优化，这里以0.98.8为例介绍，目录如下：</p>

<p>/hbase/.tmp</p>

<p>/hbase/WALs</p>

<p>/hbase/archive</p>

<p>/hbase/corrupt</p>

<p>/hbase/data</p>

<p>/hbase/hbase.id</p>

<p>/hbase/hbase.version</p>

<p>/hbase/oldWALs</p>

<p> </p>

<p>1、/hbase/.tmp</p>

<p>这个目录不变还是原来的tmp目录，作用是一样的。</p>

<p>2、/hbase/WALs</p>

<p>这里对应0.94的.logs 目录，取名为 WALs 更加见名知意了，点个赞！</p>

<p>3、/hbase/archive</p>

<p>和0.94一样，只是去掉了.而已，估计是作者不想把它作为一个隐藏文件夹了吧</p>

<p>4、/hbase/corrupt</p>

<p>和0.94一样，去了.</p>

<p>5、/hbase/data</p>

<p>这个才是 hbase 的核心目录，0.98版本里支持 namespace 的概念模型，系统会预置两个 namespace 即：hbase和default</p>

<p>5.1 /hbase/data/default</p>

<p>     这个默认的namespace即没有指定namespace 的表都将会flush 到该目录下面。</p>

<p>5.2 /hbase/data/hbase</p>

<p>     这个namespace 下面存储了 HBase 的 namespace、meta 和acl 三个表，这里的 meta 表跟0.94版本的.META.是一样的，自0.96之后就已经将 ROOT 表去掉了，直接从Zookeeper 中找到meta 表的位置，然后通过 meta 表定位到 region。 namespace 中存储了 HBase 中的所有 namespace 信息，包括预置的hbase 和 default。acl 则是表的用户权限控制。</p>

<p>     如果自定义一些 namespace 的话，就会再/hbase/data 目录下新建一个 namespace 文件夹，该 namespace 下的表都将 flush 到该目录下。</p>

<p>6、/hbase/hbase.id</p>

<p>     它是一个文件，存储集群唯一的 cluster id 号，是一个 uuid。</p>

<p>7、/hbase/hbase.version</p>

<p>     同样也是一个文件，存储集群的版本号，貌似是加密的，看不到，只能通过web-ui 才能正确显示出来。</p>

<p>8、/hbase/oldWALs</p>

<p>这里对应0.94的.oldlogs 目录，取名为 oldWALs 是不是更好了呢！</p>            </div>
                </div>
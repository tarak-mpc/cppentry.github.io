---
layout:     post
title:      Hbase 在HDFS上的目录树结构
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>自从学习了HDFS之后，再学习HBASE之后，我了解到HBASE是架设在HDFS上的，数据存储在HDFS上，哪HBASE在HDFS上的目录树是怎样的呢？</p>

<p><strong>一.0.94-cdh4.2.1版本</strong></p>

<p>系统级别的一级目录如下，用户自定义的均在这个/hbase 下的一级子目录下</p>

<pre class="has">
<code>/hbase/-ROOT-

/hbase/.META.

/hbase/.archive

/hbase/.corrupt

/hbase/.hbck

/hbase/.logs

/hbase/.oldlogs

/hbase/.snapshot

/hbase/.tmp

/hbase/hbase.id

/hbase/hbase.version</code></pre>

<p><strong>1、</strong><strong>/hbase/-ROOT-</strong></p>

<p><strong>    </strong> hbase读写数据的时候采用三级寻址方式，首先找到从 zk 中找到ROOT 表所在位置，通过 ROOT 表找到 META 表所在位置，然后再从 META 表定位到你要读写数据Region 所在的Regionserver。所以-ROOT-目录对应 HBase 中的系统表ROOT，就不多做解释了。</p>

<p><strong>2、/hbase/.META.</strong></p>

<p>     就是存储1中介绍的 META 表的存储路径。</p>

<p><strong>3、/hbase/.archive</strong></p>

<p><strong>     </strong>HBase 在做 Split或者 compact 操作完成之后，会将 HFile 移到.archive 目录中，然后将之前的 hfile 删除掉，该目录由 HMaster 上的一个定时任务定期去清理。</p>

<p><strong>4、/hbase/.corrupt</strong></p>

<p><strong>     </strong>存储HBase做损坏的日志文件，一般都是为空的。<strong> </strong></p>

<p><strong>5、/hbase/.hbck</strong></p>

<p>     HBase 运维过程中偶尔会遇到元数据不一致的情况，这时候会用到提供的 hbck 工具去修复，修复过程中会使用该目录作为临时过度缓冲。</p>

<p><strong>6、/hbase/.logs</strong></p>

<p><strong>     </strong>大家都知道 HBase 是支持 WAL（Write Ahead Log） 的，HBase 会在第一次启动之初会给每一台 RegionServer 在.log 下创建一个目录，若客户端如果开启WAL 模式，会先将数据写入一份到.log 下，当 RegionServer crash 或者目录达到一定大小，会开启 replay 模式，类似 MySQL 的 binlog。</p>

<p><strong>7、/hbase/.oldlogs</strong></p>

<p>当.logs 文件夹中的 HLog 没用之后会 move 到.oldlogs 中，HMaster 会定期去清理。</p>

<p><strong>8、/hbase/.snapshot</strong></p>

<p><strong>     </strong>hbase若开启了 snapshot 功能之后，对某一个用户表建立一个 snapshot 之后，snapshot 都存储在该目录下，如对表test 做了一个 名为sp_test 的snapshot，就会在/hbase/.snapshot/目录下创建一个sp_test 文件夹，snapshot 之后的所有写入都是记录在这个 snapshot 之上。</p>

<p><strong>9、/hbase/.tmp</strong></p>

<p>     当对表做创建或者删除操作的时候，会将表move 到该 tmp 目录下，然后再去做处理操作。</p>

<p><strong>10、/hbase/hbase.id</strong></p>

<p>     它是一个文件，存储集群唯一的 cluster id 号，是一个 uuid。</p>

<p><strong>11、/hbase/hbase.version</strong></p>

<p>     同样也是一个文件，存储集群的版本号，貌似是加密的，看不到，只能通过web-ui 才能正确显示出来</p>

<p> </p>

<p><strong>二.1.2.0-cdh5.7.6版本</strong></p>

<p>自0.96版本之后，hbase 源码结构上做了很大的优化，目录结构也发生了变化，做了精简和优化，这里以<strong>1.2.0</strong>为例介绍，目录如下：</p>

<p><img alt="" class="has" height="510" src="https://img-blog.csdn.net/20180718230425905?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3d5ejA1MTYwNzExMjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="1004"></p>

<p><strong>1、/hbase/.tmp</strong></p>

<p>当对表做创建或者删除操作的时候，会将表move 到该 tmp 目录下，然后再去做处理操作。</p>

<p><strong>2、/hbase/WALs</strong></p>

<p><strong> </strong>大家都知道 HBase 是支持 WAL（Write Ahead Log） 的，HBase 会在第一次启动之初会给每一台 RegionServer 在.log 下创建一个目录，若客户端如果开启WAL 模式，会先将数据写入一份到.log 下，当 RegionServer crash 或者目录达到一定大小，会开启 replay 模式，类似 MySQL 的 binlog。</p>

<p><strong>3、/hbase/archive</strong></p>

<p>归档，HBase 在做 Split或者 compact 操作完成之后，会将 HFile 移到.archive 目录中，然后将之前的 hfile 删除掉，该目录由 HMaster 上的一个定时任务定期去清理。和0.94一样，只是去掉了<strong>.</strong>而已，估计是作者不想把它作为一个隐藏文件夹了吧</p>

<p><strong>4、/hbase/corrupt</strong></p>

<p>存储HBase做损坏的日志文件，一般都是为空的。</p>

<p><strong>5、/hbase/data</strong></p>

<p>这个才是 hbase 的核心目录，0.98版本里支持 namespace 的概念模型，系统会预置两个 namespace 即：hbase和default</p>

<p>存储HBASE中所有的数据，与hive中存储在HDFS上的data目录一样。</p>

<p><strong>5.1 /hbase/data/default</strong></p>

<p>     这个默认的namespace即没有指定namespace 的表都将会flush 到该目录下面。</p>

<p><strong>5.2 /hbase/data/hbase</strong></p>

<p>     这个namespace 下面存储了 HBase 的 namespace、meta 和acl 三个表，这里的 meta 表跟0.94版本的.META.是一样的，自0.96之后就已经将 ROOT 表去掉了，直接从Zookeeper 中找到meta 表的位置，然后通过 meta 表定位到 region。 namespace 中存储了 HBase 中的所有 namespace 信息，包括预置的hbase 和 default。acl 则是表的用户权限控制。</p>

<p>     如果自定义一些 namespace 的话，就会再/hbase/data 目录下新建一个 namespace 文件夹，该 namespace 下的表都将 flush 到该目录下。</p>

<p><strong>6、/hbase/hbase.id</strong></p>

<p><strong>     </strong>它是一个文件，存储集群唯一的 cluster id 号，是一个 uuid<strong>。</strong></p>

<p><strong>7、/hbase/hbase.version</strong></p>

<p><strong>     </strong>同样也是一个文件，存储集群的版本号，貌似是加密的，看不到，只能通过web-ui 才能正确显示出来<strong>。</strong></p>

<p><strong>8、/hbase/oldWALs</strong></p>

<p>这里对应0.94的.oldlogs 目录，取名为 oldWALs 是不是更好了呢！</p>

<p> </p>

<p> </p>            </div>
                </div>
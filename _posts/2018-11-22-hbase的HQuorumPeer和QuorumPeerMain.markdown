---
layout:     post
title:      hbase的HQuorumPeer和QuorumPeerMain
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：遥想公瑾当年，小乔初嫁了，使我不得开心颜。					https://blog.csdn.net/Beeman_xia/article/details/76825743				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div>hbase是列式数据库，既可以单机也可以以集群的方式搭建，以集群的方式搭建一般建立在hdfs之上。</div>
<div><br></div>
<div>分布式的hbase如何启动？</div>
<div>首先启动hadoop，然后就来问题了：zookeeper和hbase的启动顺序是什么？</div>
<div><br></div>
<div>1,先启动hbase:hbase有内置的zookeeper，如果没有装zookeeper，启动hbase的时候会有一个HQuorumPeer进程。<br>
2.先启动zookeeper：如果用外置的zookeeper管理hbase，则先启动zookeeper，然后启动hbase，启动后会有一个QuorumPeerMain进程。</div>
<div><br></div>
<div>两个进程的名称不一样<br>
HQuorumPeer表示hbase管理的zookeeper<br>
QuorumPeerMain表示zookeeper独立的进程</div>
            </div>
                </div>
---
layout:     post
title:      HBase表“usertable”无法删除问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>在Hbase shell下，执行create “usertable”时提示usertable表存在，但 disable “usertable”时，提示usertable表不存在。</p>
<p>由于HBase表需要通过Zookeeper创建或删除，“usertable”创建时在Zookeeper中留下记录，后来对Hbase的不合理操作删除了“usertable”（如直接rm删除了HBase的“usertable”数据目录，但没有删除Zookeeper中的“usertable”元数据），导致HBase数据和ZooKeeper中保持的HBase元数据不一致。</p>
<p>解决方法：将ZooKeeper中的元数据表“usertable”删除</p>
<p>1）登录Zookeeper：/opt/zookeeper/bin/zkCli.sh -server 127.0.0.1:2181 </p>
<p>2）在Zookeeper客户端执行ls /hbase/table，可以看到[xxx, usertable]</p>
<p>     cd /hbase/table</p>
<p>     delete usertable</p>
<p>3）退出Zookeeper客户端</p>
            </div>
                </div>
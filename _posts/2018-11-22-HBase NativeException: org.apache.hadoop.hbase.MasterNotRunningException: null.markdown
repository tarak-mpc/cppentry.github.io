---
layout:     post
title:      HBase NativeException: org.apache.hadoop.hbase.MasterNotRunningException: null
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="content-head clearfix">
<h2 class="title content-title">NativeException: org.apache.hadoop.hbase.MasterNotRunningException: null</h2>
</div>
<div id="content" class="content text-content clearfix">
<p>重启机房服务器后，用./start-hbase.sh启动HBase后，执行hbase shell，出现如下情况</p>
<p>[root@localhost bin]# hbase shell<br>
HBase Shell; enter 'help&lt;RETURN&gt;' for list of supported commands.<br>
Version: 0.20.6, rUnknown, Thu Oct 28 19:02:04 CST 2010<br>
hbase(main):001:0&gt; list<br>
NativeException: org.apache.hadoop.hbase.MasterNotRunningException: null</p>
<p>解决步骤：</p>
<p>1、启动hadoop后，需要等一段时间，再开启hbase</p>
<p>2、去掉hadoop的安全模式：</p>
<p>hadoop dfsadmin -safemode leave</p>
<p>3、把/etc/hosts里的master01的IP改为服务器当前的IP</p>
<p>4、确认hbase的hbase-site.xml中</p>
<p>&lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p>&lt;value&gt;hdfs://localhost:8020/hbase&lt;/value&gt;</p>
<p>与hadoop的core-site.xml中</p>
<p>&lt;name&gt;fs.default.name&lt;/name&gt;<br>
&lt;value&gt;hdfs://localhost:8020&lt;/value&gt;</p>
<p>红字部分保持一致</p>
<p>5、确认hadoop版本是0.20.2，hbase版本是0.20.6</p>
<p>6、重新执行./start-hbase.sh之前，先kill掉当前的hbase和zookeeper进程</p>
<p>PS：遇到问题时，先查看logs，很有帮助。</p>
</div>
            </div>
                </div>
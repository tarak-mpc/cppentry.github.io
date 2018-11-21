---
layout:     post
title:      Hive与HBase的整合
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
hive/lib下的hbase-handler ,zookeeper都使用Hbase/lib下的版本</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
/hive --auxpath /opt/hive/lib/hive-hbase-handler-0.8.1.jar,/opt/hive/lib/hbase-0.90.4.jar,/opt/hive/lib/zookeeper-3.3.2.jar -hiveconf hbase.zookeeper.quorum=datanode1,datanode2<br></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
三个Jar之前用‘,’直接相连接，不能有空格，quorum只是regionserver，即slaves</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
Hive与HBase的整合功能的实现是利用两者本身对外的API接口互相进行通信，相互通信主要是依靠hive_hbase-handler.jar工具类 (<a href="http://wiki.apache.org/hadoop/Hive/StorageHandlers" rel="nofollow" style="font-family:inherit;color:rgb(30,91,119);text-decoration:none;font-style:inherit;">Hive
 Storage Handlers</a>)， 大致意思如图所示：</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);text-align:center;">
<img src="http://simpleframework.net/%24resource/default/myFileSelect/jsp/dl.jsp?__file_Id=13158&amp;job=sys_account_normal&amp;loc=true" alt="" style="border:1px solid rgb(153,153,153);"></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
<strong>口水：</strong><br>
 对 hive_hbase-handler.jar 这个东东还有点兴趣，有空来磋磨一下。</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
<strong>一、2个注意事项：</strong><br>
1、需要的软件有 Hadoop、Hive、Hbase、Zookeeper，Hive与HBase的整合对Hive的版本有要求，所以不要下载.0.6.0以前的老版 本，Hive.0.6.0的版本才支持与HBase对接，因此在Hive的lib目录下可以看见多了hive_hbase-handler.jar这个 jar包，他是Hive扩展存储的Handler ，HBase 建议使用 0.20.6的版本，这次我没有启动HDFS的集群环境，本次所有测试环境都在一台机器上。<br>
    <br>
2、运行Hive时，也许会出现如下错误，表示你的JVM分配的空间不够，<strong>错误信息</strong>如下：<br>
Invalid maximum heap size: -Xmx4096m<br>
The specified size exceeds the maximum representable size.<br>
Could not create the Java virtual machine.</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
解决方法：<br>
/work/hive/bin/ext# vim util/execHiveCmd.sh 文件中第33行<br>
修改，<br>
HADOOP_HEAPSIZE=4096<br>
为<br>
HADOOP_HEAPSIZE=256</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
另外，在 /etc/profile/ 加入 export $HIVE_HOME=/work/hive</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
<strong>二、启动运行环境</strong><br>
1启动Hive<br>
hive –auxpath /work/hive/lib/hive_hbase-handler.jar,/work/hive/lib/hbase-0.20.3.jar,/work/hive/lib/zookeeper-3.2.2.jar -hiveconf hbase.master=127.0.0.1:60000<br>
加载 Hive需要的工具类，并且指向HBase的master服务器地址，我的HBase master服务器和Hive运行在同一台机器，所以我指向本地。</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
2启动HBase<br>
/work/hbase/bin/hbase master start</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
3启动Zookeeper<br>
/work/zookeeper/bin/zkServer.sh start</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
<strong>三、执行</strong><br>
在Hive中创建一张表，相互关联的表<br>
CREATE TABLE hbase_table_1(key int, value string) STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,cf1:val") TBLPROPERTIES ("hbase.table.name" = "xyz");</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
在运行一个在Hive中建表语句，并且将数据导入<br>
建表<br>
    CREATE TABLE pokes (foo INT, bar STRING);<br>
数据导入<br>
    LOAD DATA LOCAL INPATH '/work/hive/examples/files/kv1.txt' OVERWRITE INTO TABLE pokes;</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
在Hive与HBase关联的表中 插入一条数据<br>
    INSERT OVERWRITE TABLE hbase_table_1 SELECT * FROM pokes WHERE foo=98;<br>
运行成功后，如图所示：</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);text-align:center;">
<img src="http://simpleframework.net/%24resource/default/myFileSelect/jsp/dl.jsp?__file_Id=13159&amp;job=sys_account_normal&amp;loc=true" alt="" style="border:1px solid rgb(153,153,153);"></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
插入数据时采用了MapReduce的策略算法，并且同时向HBase写入，如图所示：</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);text-align:center;">
<img src="http://simpleframework.net/%24resource/default/myFileSelect/jsp/dl.jsp?__file_Id=13160&amp;job=sys_account_normal&amp;loc=true" alt="" style="border:1px solid rgb(153,153,153);"></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
在HBase shell中运行 scan 'xyz' 和describe "xyz" 命令，查看表结构，运行结果如图所示：</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);text-align:center;">
<img src="http://simpleframework.net/%24resource/default/myFileSelect/jsp/dl.jsp?__file_Id=13161&amp;job=sys_account_normal&amp;loc=true" alt="" style="border:1px solid rgb(153,153,153);"></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
xyz是通过Hive在Hbase中创建的表，刚刚在Hive的建表语句中指定了映射的属性 <strong>"hbase.columns.mapping" = ":key,cf1:val" </strong> 和 在HBase中建表的名称 <strong>"hbase.table.name" = "xyz"</strong></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
在hbase在运行put命令，插入一条记录<br>
    <strong>put 'xyz','10001','cf1:val','www.javabloger.com'</strong></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
在hive上运行查询语句，看看刚刚在hbase中插入的数据有没有同步过来，<br>
    select * from hbase_table_1 WHERE key=10001;<br>
如图所示：</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);text-align:center;">
<img src="http://simpleframework.net/%24resource/default/myFileSelect/jsp/dl.jsp?__file_Id=13162&amp;job=sys_account_normal&amp;loc=true" alt="" style="border:1px solid rgb(153,153,153);"></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);">
<strong>最终的效果</strong><br>
    以上整合过程和操作步骤已经执行完毕，现在<strong>Hive中添加记录HBase中有记录添加，同样你在HBase中添加记录Hive中也会添加</strong>， 表示Hive与HBase整合成功，对海量级别的数据我们是不是可以在HBase写入，在Hive中查询 喃？因为<span style="font-family:inherit;font-style:inherit;color:inherit;">HBase 不支持<span style="font-family:inherit;font-style:inherit;color:inherit;">复杂的查询，但是HBase可以作为基于
 key 获取一行或多行数据，或者扫描数据区间，以及过滤操作。而</span>复杂的<span style="font-family:inherit;font-style:inherit;color:inherit;">查询可以让</span></span><span style="font-family:inherit;font-style:inherit;color:inherit;">Hive</span><span style="font-family:inherit;font-style:inherit;color:inherit;">来完成</span>，一个作为存储的入口(HBase)，一个作为查询的入口(Hive)。如下图示。</p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);text-align:center;">
<img src="http://simpleframework.net/%24resource/default/myFileSelect/jsp/dl.jsp?__file_Id=13163&amp;job=sys_account_normal&amp;loc=true" alt="" style="border:1px solid rgb(153,153,153);"></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);text-align:center;">
<br></p>
<p style="font-family:Verdana, 'Microsoft YaHei', Tahoma, Arial, sans-serif;font-size:12.727272033691406px;color:rgb(51,51,51);text-align:center;">
</p>
<div class="article_title" style="font-size:20px;line-height:30px;font-family:'Microsoft YaHei';">
<span class="ico ico_type_Translated" style="display:inline-block;width:19px;vertical-align:middle;"></span><a href="http://blog.csdn.net/kky2010_110/article/details/8332320" rel="nofollow" style="color:rgb(0,0,0);text-decoration:none;">hive
 整合 hbase</a></div>
<div class="article_manage" style="color:rgb(153,153,153);font-size:11.818181991577148px;line-height:24px;font-family:Arial;text-align:right;">
<span class="link_categories">分类： <a href="http://blog.csdn.net/kky2010_110/article/category/1307632" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">hive</a> <a href="http://blog.csdn.net/kky2010_110/article/category/1241453" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">hbase</a></span><span class="link_postdate">2012-12-19
 16:03</span> <span class="link_view" title="阅读次数">192人阅读</span> <span class="link_comments" title="评论次数"><a href="http://blog.csdn.net/kky2010_110/article/details/8332320#comments" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;">评论</a>(0)</span> <span class="link_collect"><a href="" rel="nofollow" title="收藏" style="color:rgb(51,102,153);text-decoration:none;">收藏</a></span> <span class="link_report"><a href="http://blog.csdn.net/kky2010_110/article/details/8332320#report" rel="nofollow" title="举报" style="color:rgb(51,102,153);text-decoration:none;">举报</a></span></div>
<div id="article_content" class="article_content" style="font-size:14px;line-height:26px;font-family:Arial;color:rgb(51,51,51);">
用hbase做数据库,但由于hbase没有类sql查询方式,所以操作和计算数据非常不方便,于是整合hive,让hive支撑在hbase数据库层面 的 hql查询.hive也即 做数据仓库<br><br>
1. 基于Hadoop+Hive架构对海量数据进行查询：http://blog.csdn.net/kunshan_shenbin/article/details/7105319<br>
2. HBase 0.94.0 + Hadoop 1.0.2集成：http://blog.csdn.net/kunshan_shenbin/article/details/7209990<br>
本文的目的是要讲述如何让Hbase和Hive能互相访问，让Hadoop/Hbase/Hive协同工作，合为一体。 <br>
本文测试步骤主要参考自：http://running.iteye.com/blog/898399 <br>
当然，这边博文也是按照官网的步骤来的：http://wiki.apache.org/hadoop/Hive/HBaseIntegration <br>
1. 拷贝HBase 0.94.0.jar和zookeeper-3.4.3.jar到hive/lib下。 0.94.0和hive不兼容改用0.94.2<br>
注意：如何hive/lib下已经存在这两个文件的其他版本（例如zookeeper-3.3.1.jar），建议删除后使用hbase下的相关版本。 <br>
2. 修改hive/conf下hive-site.xml文件，在底部添加如下内容： <br><div class="syntaxhighlighter" id="highlighter_229799">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>01</code></td>
<td class="content"><code class="plain">[html] view plaincopy</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>02</code></td>
<td class="content"><code class="plain">&lt;!-- </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>03</code></td>
<td class="content"><code class="plain">&lt;property&gt; </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>04</code></td>
<td class="content"><code class="spaces">  </code><code class="plain">&lt;name&gt;hive.exec.scratchdir&lt;/name&gt;  </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>05</code></td>
<td class="content"><code class="spaces">  </code><code class="plain">&lt;value&gt;/usr/local/hive/tmp&lt;/value&gt;  </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>06</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>07</code></td>
<td class="content"><code class="plain">&lt;/property&gt;  </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>08</code></td>
<td class="content"><code class="plain">--&gt; </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>09</code></td>
<td class="content"><code class="spaces">  </code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>10</code></td>
<td class="content"><code class="plain">&lt;property&gt;  </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>11</code></td>
<td class="content"><code class="spaces">  </code><code class="plain">&lt;name&gt;hive.querylog.location&lt;/name&gt;  </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>12</code></td>
<td class="content"><code class="spaces">  </code><code class="plain">&lt;value&gt;/usr/local/hive/logs&lt;/value&gt;  </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>13</code></td>
<td class="content"><code class="plain">&lt;/property&gt;  </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>14</code></td>
<td class="content"><code class="spaces">  </code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>15</code></td>
<td class="content"><code class="plain">&lt;property&gt; </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>16</code></td>
<td class="content"><code class="spaces">  </code><code class="plain">&lt;name&gt;hive.aux.jars.path&lt;/name&gt;  </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>17</code></td>
<td class="content"><code class="spaces">  </code><code class="plain">&lt;value&gt;file:</code><code class="comments">///usr/local/hive/lib/hive-hbase-handler-0.8.0.jar,file:///usr/local/hive/lib/hbase-0.90.5.jar,file:///usr/local/hive/lib/zookeeper-3.3.2.jar&lt;/value&gt; </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>18</code></td>
<td class="content"> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>19</code></td>
<td class="content"><code class="plain">&lt;/property&gt;</code></td>
</tr></tbody></table></div>
</div>
</div>
<br><br>
注意：如果hive-site.xml不存在则自行创建，或者把hive-default.xml.template文件改名后使用。 <br>
具体请参见：http://blog.csdn.net/kunshan_shenbin/article/details/7210020 <br><br>
3. 拷贝hbase-0.94.2.jar到所有hadoop节点(包括master)的hadoop/lib下。 <br>
4. 拷贝hbase/conf下的hbase-site.xml文件到所有hadoop节点(包括master)的hadoop/conf下。 <br>
注意，hbase-site.xml文件配置信息参照：http://blog.csdn.net/kunshan_shenbin/article/details/7209990<br>
注意，如果3,4两步跳过的话，运行hive时很可能出现如下错误： <br><div class="syntaxhighlighter" id="highlighter_606562">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">[html] view plaincopy</code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">org.apache.hadoop.hbase.ZooKeeperConnectionException: HBase is able to connect to ZooKeeper but the connection closes immediately.  </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="plain">This could be a sign that the server has too many connections (</code><code class="value">30</code><code class="plain">is the</code><code class="keyword">default</code><code class="plain">). Consider inspecting your ZK
 server logs</code><code class="keyword">for</code><code class="plain">that error and  </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="plain">then make sure you are reusing HBaseConfiguration as often as you can. See HTable's javadoc</code><code class="keyword">for</code><code class="plain">more information. at org.apache.hadoop. </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="plain">hbase.zookeeper.ZooKeeperWatcher.</code></td>
</tr></tbody></table></div>
</div>
</div>
<br><br>
参考：http://blog.sina.com.cn/s/blog_410d18710100vlbq.html <br><br>
现在可以尝试启动Hive了。 <br>
单节点启动： <br><div class="syntaxhighlighter" id="highlighter_758610">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">&gt; bin/hive -hiveconf hbase.master=master:</code><code class="value">60000</code></td>
</tr></tbody></table></div>
</div>
</div>
<br>
集群启动： <br><div class="syntaxhighlighter" id="highlighter_532969">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">&gt; bin/hive -hiveconf hbase.zookeeper.quorum=slave</code></td>
</tr></tbody></table></div>
</div>
</div>
<br>
如何hive-site.xml文件中没有配置hive.aux.jars.path，则可以按照如下方式启动。 <br><div class="syntaxhighlighter" id="highlighter_539975">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">&gt; bin/hive --auxpath /usr/local/hive/lib/hive-hbase-handler-</code><code class="value">0.9</code><code class="plain">.</code><code class="value">0</code><code class="plain">.jar, /usr/local/hive/lib/hbase-</code><code class="value">0.94</code><code class="plain">.0</code><code class="value"></code><code class="plain">.jar,
 /usr/local/hive/lib/zookeeper-</code><code class="value">3.4</code><code class="plain">.3</code><code class="value"></code><code class="plain">.jar -hiveconf hbase.zookeeper.quorum=slave</code></td>
</tr></tbody></table></div>
</div>
</div>
<br><br>
接下来可以做一些测试了。 <br>
1.创建hbase识别的数据库： <br>
[sql] view plaincopy <br>
CREATE TABLE hbase_table_1(key int, value string)  <br>
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'  <br>
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,cf1:val")  <br>
TBLPROPERTIES ("hbase.table.name" = "xyz");  <br>
hbase.table.name 定义在hbase的table名称 <br>
hbase.columns.mapping 定义在hbase的列族 <br>
2.使用sql导入数据 <br>
a) 新建hive的数据表 <br>
[sql] view plaincopy <br>
&lt;span&gt;&lt;span&gt;&lt;/span&gt;&lt;/span&gt;hive&gt; CREATE TABLE pokes (foo INT, bar STRING);  <br>
b) 批量插入数据 <br>
[sql] view plaincopy <br><div class="syntaxhighlighter" id="highlighter_53211">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">hive&gt; LOAD DATA LOCAL INPATH </code><code class="string">'./examples/files/kv1.txt'</code><code class="plain">OVERWRITE INTO TABLE</code></td>
</tr></tbody></table></div>
</div>
</div>
pokes;  <br>
c) 使用sql导入hbase_table_1 <br>
[sql] view plaincopy <br>
hive&gt; INSERT OVERWRITE TABLE hbase_table_1 SELECT * FROM pokes WHERE foo=86;  <br>
3. 查看数据 <br>
[sql] view plaincopy <br>
hive&gt; select * from  hbase_table_1;  <br>
这时可以登录Hbase去查看数据了. <br>
&gt; /usr/local/hbase/bin/hbase shell <br>
hbase(main):001:0&gt; describe 'xyz'   <br>
hbase(main):002:0&gt; scan 'xyz'   <br>
hbase(main):003:0&gt; put 'xyz','100','cf1:val','www.360buy.com' <br>
这时在Hive中可以看到刚才在Hbase中插入的数据了。 <br>
hive&gt; select * from hbase_table_1 <br>
4. hive访问已经存在的hbase <br>
使用CREATE EXTERNAL TABLE <br>
[sql] view plaincopy <br>
CREATE EXTERNAL TABLE hbase_table_2(key int, value string)  <br>
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'  <br>
WITH SERDEPROPERTIES ("hbase.columns.mapping" = "cf1:val")  <br>
TBLPROPERTIES("hbase.table.name" = "some_existing_table");  <br><br><br>
多列和多列族（Multiple Columns and Families） <br>
1．创建数据库 <br>
Java代码  <br>
CREATE TABLE hbase_table_2(key int, value1 string, value2 int, value3 int)   <br>
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'  <br>
WITH SERDEPROPERTIES (  <br>
"hbase.columns.mapping" = ":key,a:b,a:c,d:e"  <br>
);  <br><br>
2.插入数据 <br>
Java代码  <br>
INSERT OVERWRITE TABLE hbase_table_2 SELECT foo, bar, foo+1, foo+2   <br>
FROM pokes WHERE foo=98 OR foo=100;  <br><br><br>
这个有3个hive的列（value1和value2，value3），2个hbase的列族（a，d） <br>
Hive的2列（value1和value2）对应1个hbase的列族（a，在hbase的列名称b，c），hive的另外1列（value3）对应列（e）位于列族（d）<br><br>
3.登录hbase查看结构 <br>
Java代码  <br><div class="syntaxhighlighter" id="highlighter_907689">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">hbase(main):</code><code class="value">003</code><code class="plain">:</code><code class="value">0</code><code class="plain">&gt; describe</code><code class="string">"hbase_table_2"</code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">DESCRIPTION                                                             ENABLED                                </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="spaces"> </code><code class="plain">{NAME =&gt; </code><code class="string">'hbase_table_2'</code><code class="plain">, FAMILIES =&gt; [{NAME =&gt;</code><code class="string">'a'</code><code class="plain">, COMPRESSION =&gt; 'N</code><code class="keyword">true</code>                                   </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="spaces"> </code><code class="plain">ONE</code><code class="string">', VERSIONS =&gt; '</code><code class="value">3</code><code class="string">', TTL =&gt; '</code><code class="value">2147483647</code><code class="string">', BLOCKSIZE
 =&gt; '</code><code class="value">65536</code><code class="plain">', IN_M                                        </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="spaces"> </code><code class="plain">EMORY =&gt; </code><code class="string">'false'</code><code class="plain">, BLOCKCACHE =&gt; </code><code class="string">'true'</code><code class="plain">}, {NAME =&gt;</code><code class="string">'d'</code><code class="plain">,
 COMPRESSION =&gt;                                         </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>6</code></td>
<td class="content"><code class="spaces"> </code><code class="string">'NONE'</code><code class="plain">, VERSIONS =&gt;</code><code class="string">'3'</code><code class="plain">, TTL =&gt;</code><code class="string">'2147483647'</code><code class="plain">, BLOCKSIZE
 =&gt;</code><code class="string">'65536'</code><code class="plain">, IN                                        </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>7</code></td>
<td class="content"><code class="spaces"> </code><code class="plain">_MEMORY =&gt; </code><code class="string">'false'</code><code class="plain">, BLOCKCACHE =&gt;</code><code class="string">'true'</code><code class="plain">}]}                                                                   </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>8</code></td>
<td class="content"><code class="value">1</code> <code class="plain">row(s) in </code><code class="value">1.0630</code> <code class="plain">seconds</code></td>
</tr></tbody></table></div>
</div>
</div>
<br><br>
4.查看hbase的数据 <br>
Java代码  <br><div class="syntaxhighlighter" id="highlighter_522898">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">hbase(main):</code><code class="value">004</code><code class="plain">:</code><code class="value">0</code><code class="plain">&gt; scan</code><code class="string">'hbase_table_2'</code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">ROW                          COLUMN+CELL                                                                       </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="spaces"> </code><code class="value">100</code>                        <code class="plain">column=a:b, timestamp=</code><code class="value">1297695262015</code><code class="plain">, value=val_100                                </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="spaces"> </code><code class="value">100</code>                        <code class="plain">column=a:c, timestamp=</code><code class="value">1297695262015</code><code class="plain">, value=</code><code class="value">101</code>                                    </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="spaces"> </code><code class="value">100</code>                        <code class="plain">column=d:e, timestamp=</code><code class="value">1297695262015</code><code class="plain">, value=</code><code class="value">102</code>                                    </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>6</code></td>
<td class="content"><code class="spaces"> </code><code class="value">98</code>                         <code class="plain">column=a:b, timestamp=</code><code class="value">1297695242675</code><code class="plain">, value=val_98                                 </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>7</code></td>
<td class="content"><code class="spaces"> </code><code class="value">98</code>                         <code class="plain">column=a:c, timestamp=</code><code class="value">1297695242675</code><code class="plain">, value=</code><code class="value">99</code>                                     </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>8</code></td>
<td class="content"><code class="spaces"> </code><code class="value">98</code>                         <code class="plain">column=d:e, timestamp=</code><code class="value">1297695242675</code><code class="plain">, value=</code><code class="value">100</code>                                    </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>9</code></td>
<td class="content"><code class="value">2</code> <code class="plain">row(s) in </code><code class="value">0.0380</code> <code class="plain">seconds</code></td>
</tr></tbody></table></div>
</div>
</div>
<br><br>
5.在hive中查看 <br>
Java代码  <br><div class="syntaxhighlighter" id="highlighter_802809">
<div class="lines">
<div class="line alt1">
<table><tbody><tr><td class="number"><code>1</code></td>
<td class="content"><code class="plain">hive&gt; select * from hbase_table_2; </code></td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>2</code></td>
<td class="content"><code class="plain">OK </code></td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>3</code></td>
<td class="content"><code class="value">100</code>     <code class="plain">val_100</code><code class="value">101</code>    <code class="value">102</code> </td>
</tr></tbody></table></div>
<div class="line alt2">
<table><tbody><tr><td class="number"><code>4</code></td>
<td class="content"><code class="value">98</code>      <code class="plain">val_98 </code><code class="value">99</code>     <code class="value">100</code> </td>
</tr></tbody></table></div>
<div class="line alt1">
<table><tbody><tr><td class="number"><code>5</code></td>
<td class="content"><code class="plain">Time taken: </code><code class="value">3.238</code><code class="plain">seconds</code></td>
</tr></tbody></table></div>
</div>
</div>
<p>问题：Can not create a Path from an empty string</p>
<p>解决：</p>
<pre>hive-env.sh</pre>
<pre>export HIVE_AUX_JARS_PATH=/hadoop/hive-0.9.0-bin/lib/hive-hbase-handler-0.9.0.jar, /hadoop/hive-0.9.0-bin/lib/hbase-0.94.0.jar, hadoop/hive-0.9.0-bin/lib/zookeeper-3.4.3.jar </pre>
<br><p></p>
<p><br></p>
<br>
参考资料： <br>
http://running.iteye.com/blog/898399 <br>
http://heipark.iteye.com/blog/1150648 <br>
http://www.javabloger.com/article/apache-hadoop-hive-hbase-integration.html</div>
<br><p></p>
<div><br></div>
            </div>
                </div>
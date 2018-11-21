---
layout:     post
title:      HBase快照（Snapshot）技术
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 id="什么是快照" style="font-family:'Microsoft YaHei';font-weight:100;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
<span>什么是快照</span></h3>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
快照就是一份<span style="font-size:16px;">元信息的合集</span>，允许管理员恢复到表的先前状态。快照不是表的复制而是一个文件名称列表，因而不会复制数据。 <br><span style="font-size:16px;">完全快照恢复</span>是<span style="font-size:16px;">指恢复到之前的“表结构”以及当时的数据，快照之后发生的数据不会恢复。</span></p>
<h3 id="快照的作用" style="font-family:'Microsoft YaHei';font-weight:100;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
<span>快照的作用</span></h3>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
HBase中存在的备份或克隆表的方法就是使用复制/导出表或者在关闭表之后拷贝HDFS中的所有HFile。 <br>
复制或导出是通过一系列工具调用MapReduce来扫描并复制表，这样会对RegionServer有直接的影响。关闭表会停止所有的读写操作，实际环境中往往无法接受。 <br><span style="font-size:16px;">相比之下HBase快照允许管理员不拷贝数据，而直接克隆一张表，这对域服务器产生的影响最小。</span>将快照导出至其他集群不会直接影响到任何服务器；导出只是带有一些额外逻辑的群间数据同步。</p>
<h3 id="快照优势" style="font-family:'Microsoft YaHei';font-weight:100;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
<span>快照优势</span></h3>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
导出快照与复制/导出表除了更好地保持一致性外，主要的不同在于导出快照是在HDFS的层面操作的。 这意味着HMaster和域服务器与操作无关。因此不需要为不必要的数据创建缓存空间，也不会有扫描过程。因为大量对象创建引起的GC暂停，对于HBase 来说主要性能影响就是DataNode额外的网络和磁盘负载。</p>
<h3 id="应用场景" style="font-family:'Microsoft YaHei';font-weight:100;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
<span>应用场景</span></h3>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
1、从用户／应用异常中还原。 <br>
2、从一个已知的安全状态恢复／还原。 <br>
3、查看之前的快照并有选择性的合并不同写入产品环境。 <br>
4、当主应用程序升级或改版时保存快照。 <br>
5、在指定时间审查和 或 报告数据。 <br>
6、按照规定<span style="font-size:16px;">捕获月度数据</span>。 <br>
7、生成日终/月末/季末报告。 <br>
8、应用测试。 <br>
9、通过快照模拟生产环境下结构或应用发生的变化，测试完成即可丢弃。 <br>
例如：生成快照，利用快照中内容构建新表（原有结构＋数据）并且修改新的结构，添加或删除列之类。（原始表、快照和新表保持相互独立） <br>
10、减少工作压力。 <br>
11、生成快照，导入到其他集群，然后运行MapReduce jobs。因为导出的快是HDFS级别，所以不会像复制表那样降低HBase主集群的效率。</p>
<h3 id="快照操作" style="font-family:'Microsoft YaHei';font-weight:100;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
<span>快照操作</span></h3>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
<span style="font-size:16px;">生成快照：</span> <br>
本操作尝试对指定表生成快照。如果集群在执行数据均衡、分隔或合并等操作时，可能会引起操作失败。</p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
<span style="font-size:16px;">克隆快照：</span> <br>
本操作使用与指定快照相同的结构数据构建一张新表。 操作结果会生成一张有完整功能的表，对该表的任意修改不会对原表或快照产生影响。</p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
<span style="font-size:16px;">还原快照：</span> <br>
本操作将表结构和数据恢复到生成快照时的状态。（注意：本操作会舍弃快照生成后任何改变）。</p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
<span style="font-size:16px;">删除快照：</span> <br>
本操作将系统中的快照删除，释放未共享的磁盘空间， 而且不会影响其他克隆或快照。</p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
导出快照： <br>
本操作将快照数据和元数据复制到其他集群。操作只会涉及HDFS，不会与HMaster或RegionServer产生任何联系，因此HBase集群可以关闭。</p>
<h3 id="demo" style="font-family:'Microsoft YaHei';font-weight:100;line-height:1.1;color:rgb(51,51,51);font-size:24px;">
<span>Demo</span></h3>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
通过检查<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">hbase-site.xml</code>中的<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">hbase.snapshot.enabled</code>是否设置为true确认打开了快照许可。</p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
1、获取指定表的快照使用snapshot命令（不产生文件复制） <br><code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">hbase&gt;snapshot 'tableName','snapshotName'</code></p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
<img src="https://img-blog.csdn.net/20150709170751404" alt="" title="" style="border:none;vertical-align:middle;"></p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
2、列出所有的快照，使用<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">list_snapshot</code>命令。会展示出快照名称，源表，以及创建日期和时间 <br><code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">hbase&gt;list_snapshots</code></p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
<img src="https://img-blog.csdn.net/20150709170816800" alt="" title="" style="border:none;vertical-align:middle;"><br>
3、删除快照使用<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">deleted_snapshot</code>命令。删除快照不会影响到克隆表或者之后生成的快照。 <br><code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">hbase&gt;delete_snapshot 'snapshotName'</code></p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
4、使用<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">clone_snapshot</code>命令从指定的快照生成新表（克隆）。由于不会产生数据复制，所以最终用到的数据不会是之前的两倍。 <br><code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">hbsse&gt;clone_snapshot 'snapshotName','newTableName'</code> <br><img src="https://img-blog.csdn.net/20150709172928270" alt="" title="" style="border:none;vertical-align:middle;"></p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
5、使用<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">restore_snapshot</code>命令将指定快照内容替换当前表结构或数据； <br><code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">hbase&gt;restore_snapshot 'snapshotName'</code></p>
<p style="font-family:'Microsoft YaHei';font-size:15px;color:rgb(102,102,102);line-height:26px;">
<img src="https://img-blog.csdn.net/20150709173851720" alt="" title="" style="border:none;vertical-align:middle;"><br><img src="https://img-blog.csdn.net/20150709173902780" alt="" title="" style="border:none;vertical-align:middle;"></p>
<p style="line-height:26px;">
<span style="font-family:'Microsoft YaHei';color:#666666;"><span style="font-size:15px;">6、使用ExportSnapshot工具将现有快照导出至其他集群。导出工具不会影响到域服务器负载，只是工作在HDFS层面，所以需要指定HDFS路径（其他集群的hbase根目录）。 </span></span><br></p>
<p style="color:rgb(102,102,102);font-family:'Microsoft YaHei';font-size:15px;">
<code style="font-family:'Source Code Pro', monospace;font-size:13.5px;color:rgb(63,63,63);">hbase org.apache.hadoop.hbase.snapshot.ExportSnapshot -snapshotName -copy-to hdfs :///srv2:8082/hbase</code></p>
<p><span style="font-family:'Source Code Pro', monospace;color:#3f3f3f;"><span style="font-size:13.5px;">本文转载自：http://blog.csdn.net/scgaliguodong123_/article/details/46817059</span></span></p>
            </div>
                </div>
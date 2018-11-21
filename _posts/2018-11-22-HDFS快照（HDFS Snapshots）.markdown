---
layout:     post
title:      HDFS快照（HDFS Snapshots）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3 style="line-height:26px;font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
综述</h3>
<div style="color:rgb(51,51,51);font-family:Arial;font-size:14px;line-height:26px;">
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">HDFS快照是一个只读的基于时间点文件系统拷贝。快照可以是整个文件系统的也可以是一部分。常用来作为数据备份，防止用户错误和容灾。</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">HDFS实现了：</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"></p>
<ul style="font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:1.5;"><li>Snapshot 创建的时间 复杂度为O(1)，但是不包括INode 的寻找时间</li><li><span style="line-height:1.5;">只有当修改SnapShot时，才会有额外的内存占用，内存使用量为O(M),M 为修改的文件或者目录数</span></li><li><span style="line-height:1.5;">在datanode 上面的blocks 不会复制，做Snapshot 的文件是纪录了block的列表和文件的大小，但是没有数据的复制</span></li><li><span style="line-height:1.5;">Snapshot 并不会影响HDFS 的正常操作：修改会按照时间的反序记录，这样可以直接读取到最新的数据。快照数据是当前数据减去修改的部分计算出来的。</span></li></ul><p></p>
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
<a name="t1" style="color:rgb(51,102,153);"></a>可以被快照的目录</h3>
<div>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="font-size:12px;">快照<span style="font-family:Helvetica, Tahoma, Arial, sans-serif;line-height:25.1875px;">会存储在snapshottable的目录下。snapshottable下存储的snapshots 最多为65535个。没有限制<span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:15.59375px;">snapshottable目录的数量。管理员可以设置任何的目录成为snapshottable。如果snapshottable里面存着快照，那么文件夹不能删除或者改名。</span></span></span></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">网络的<span style="line-height:15.59375px;">snapshottable目录现在是不允许的。另外如果一个目录的父目录或者子目录是snapshottable，那么这个文件夹不能作为snapshottable。</span></p>
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
<a name="t2" style="color:rgb(51,102,153);"></a>快照的目录</h3>
<p style="line-height:1.3em;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">对于一个</span><span style="line-height:15.59375px;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">snapshottable文件夹，</span><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"><em>".snapshot"</em></span><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> 被用于进入他的快照</span><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> </span><tt style="font-family:Verdana, Helvetica, Arial, sans-serif;">/foo</tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> 是一个</span><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">snapshottable目录，</span><span style="font-family:monospace;">/foo/bar是一个</span><tt>/foo<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">下面的文件目录，<span style="font-family:monospace;">/foo有一个快照s0，那么路径就是</span></span></tt></span></p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<pre>/foo/.snapshot/s0/bar</pre>
</div>
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></span>
<div><span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">引用快照副本<span style="font-family:monospace;">/foo/bar。常用的API和CLI能够在<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">".snapshot" 的路径下运行。下面是一些例子。</span></span></span></div>
</div>
<div><span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;">列出snapshottable目录的所有快照</span>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -ls /foo/.snapshot</pre>
</div>
</li></ul><div><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">列出在快照s0的所有文件</span></div>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -ls /foo/.snapshot/s0</pre>
</div>
</li></ul><div><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">从s0拷贝一个文件:</span></div>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -cp /foo/.snapshot/s0/bar /tmp</pre>
</div>
</li></ul><p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">注意<span style="line-height:15.59375px;"> ".snapshot"这个名字已经被HDFS保留用户不能创建这个同名目录。如果".snapshot"正在被前一个版本的HDFS使用，他必须在升级之前重命名，否则会升级失败。 </span></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><br></p>
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
<a name="t3" style="color:rgb(51,102,153);"></a>操作</h3>
<div><br></div>
<div>
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
<a name="t4" style="color:rgb(51,102,153);"></a>管理员操作</h3>
<div>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">本节的操作需要超级用户权限。</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">快照可用</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">快照目录建立。如果这个操作成果，那么目录会变成<span style="line-height:15.59375px;">snapshottable。</span></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="line-height:15.59375px;">命令</span></p>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<ul><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfsadmin -allowSnapshot &lt;path&gt;</pre>
</div>
</li><li style="font-size:12px;">参数：<br><table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
snapshottable目录路径.</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">同时请参考<span style="font-family:monospace;">在</span><span style="font-family:monospace;">HdfsAdmin中</span> <tt>void allowSnapshot(Path path)的API。</tt></p>
<p style="line-height:1.3em;font-size:12px;"><tt></tt></p>
<p style="line-height:1.3em;font-size:12px;">快照不可用<br></p>
<p style="line-height:1.3em;font-size:12px;">文件夹里面的所有快照在失效快照前必须被删除，如果没有该目录会被建立。</p>
<p style="line-height:1.3em;font-size:12px;">命令：</p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<ul><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfsadmin -disallowSnapshot &lt;path&gt;</pre>
</div>
</li><li style="font-size:12px;">参数:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
snapshottable目录路径.</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">同时请参考<span style="font-family:monospace;">在</span><span style="font-family:monospace;">HdfsAdmin中</span> <tt>void disallowSnapshot(Path path)的API。</tt></p>
</div>
<br></div>
<h3 style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:14px;color:rgb(102,102,102);background-color:rgb(204,204,204);">
<a name="t5" style="color:rgb(51,102,153);"></a>用户操作</h3>
<br></div>
<p style="text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;line-height:1.3em;">本小节描述用户操作。注意</span><span style="font-family:Tahoma, Arial;"><span style="line-height:24px;">HDFS超级用户可以执行所有的操作除了有权限的需求的操作。</span></span></p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;">创建快照</p>
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">snapshottable目录创建一个快照。这个操作需要snapshottable目录的权限。</span></p>
<p style="text-align:justify;"><span style="font-family:Tahoma, Arial;"><span style="line-height:24px;">命令</span></span></p>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<ul><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -createSnapshot &lt;path&gt; [&lt;snapshotName&gt;]</pre>
</div>
</li><li style="font-size:12px;">参数:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
snapshotName</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
快照名字。如果没有指定，会用时间戳生成一个默认的名字格式是<tt>"'s'yyyyMMdd-HHmmss.SSS"</tt>, 例如 "s20130412-151029.033".</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">可以<span style="font-family:monospace;">createSnapshot(Path path)和createSnapshot(Path path, String snapshotName)在文件系统中的API，方法的返回值是<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">snapshot </span>路径</span></p>
<p style="line-height:1.3em;font-size:12px;"><br></p>
<p style="line-height:1.3em;font-size:12px;">删除快照</p>
<p style="line-height:1.3em;font-size:12px;"><span id="tran_0" class="copied" style="border:0px;font-family:Tahoma, Arial;line-height:24px;text-align:justify;">从一个snapshottable目录删除的快照。</span><span id="tran_1" class="copied" style="border:0px;font-family:Tahoma, Arial;line-height:24px;text-align:justify;">这个操作需要snapshottable目录的权限。</span><br></p>
<p style="line-height:1.3em;font-size:12px;"><span class="copied" style="border:0px;font-family:Tahoma, Arial;line-height:24px;text-align:justify;">命令：</span></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<ul><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -deleteSnapshot &lt;path&gt; &lt;snapshotName&gt;</pre>
</div>
</li><li style="font-size:12px;">参数:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
snapshotName</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
快照名字</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">参考<tt style="font-family:Verdana, Helvetica, Arial, sans-serif;color:rgb(102,136,170);"><a href="http://hadoop.apache.org/docs/stable/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow" style="color:rgb(102,136,170);text-decoration:none;">FileSystem</a>类</tt><tt>void
 deleteSnapshot(Path path, String snapshotName)的API</tt></p>
<p style="line-height:1.3em;font-size:12px;"><tt>重命名快照</tt></p>
<p style="line-height:1.3em;font-size:12px;"><tt>重命名快照。这个命令也需要<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">snapshottable目录的</span>权限。<br></tt></p>
<p style="line-height:1.3em;font-size:12px;"><tt>命令：</tt></p>
</div>
<div class="section">
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -renameSnapshot &lt;path&gt; &lt;oldName&gt; &lt;newName&gt;</pre>
</div>
</li><li style="font-size:12px;">参数:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
oldName</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
老的快照名字</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
newName</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
新的快照名字</td>
</tr></tbody></table></li></ul><p style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:1.3em;">
参考<tt style="font-family:Verdana, Helvetica, Arial, sans-serif;color:rgb(102,136,170);"><a href="http://hadoop.apache.org/docs/stable/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow" style="color:rgb(102,136,170);text-decoration:none;">FileSystem</a>类</tt><tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> </span><tt>void
 renameSnapshot(Path path, String oldName, String newName)</tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> </span>的API</tt><br></p>
<p style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:12px;line-height:1.3em;">
得到<span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">snapshottable目录列表</span></p>
<p></p>
<div style="text-align:justify;"><span style="font-family:Tahoma, Arial;"><span style="line-height:24px;">获取当前用户的所有snapshottable。<br></span></span></div>
<div style="text-align:justify;"><span style="font-family:Tahoma, Arial;"><span style="line-height:24px;">命令：</span></span></div>
<p></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<ul><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs lsSnapshottableDir</pre>
</div>
</li><li style="font-size:12px;">参数: 无</li></ul><p style="line-height:1.3em;font-size:12px;"><br></p>
<p style="line-height:1.3em;font-size:12px;"><span style="line-height:1.3em;">参考</span><tt style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"><a href="http://hadoop.apache.org/docs/stable/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;"><span style="font-family:monospace;">DistributedFileSystem</span></a>类</tt><tt style="line-height:1.3em;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> </span><tt>SnapshottableDirectoryStatus[]
 getSnapshottableDirectoryListing()</tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> </span>的API</tt><br></p>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">获取快照差异报告</span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">得到两个快照之间的不同。需要两个目录的权限。</span></p>
<p style="line-height:1.3em;font-size:12px;"><span style="font-family:Tahoma, Arial;line-height:24px;text-align:justify;">命令：</span></p>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<ul><li style="font-size:12px;">
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs snapshotDiff &lt;path&gt; &lt;fromSnapshot&gt; &lt;toSnapshot&gt;</pre>
</div>
</li><li style="font-size:12px;">参数:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
fromSnapshot</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
开始的快照名字</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
toSnapshot</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
结束的快照名字。</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;"><span style="line-height:1.3em;">参考</span><tt style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;color:rgb(102,136,170);"><a href="http://hadoop.apache.org/docs/stable/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow" style="color:rgb(102,136,170);text-decoration:none;"><span style="font-family:monospace;">DistributedFileSystem</span></a>类</tt><tt style="line-height:1.3em;"><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> </span><tt>SnapshotDiffReport
 getSnapshotDiffReport(Path path, String fromSnapshot, String toSnapshot)</tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"> </span>的API</tt></p>
</div>
</div>
</div>
            </div>
                </div>
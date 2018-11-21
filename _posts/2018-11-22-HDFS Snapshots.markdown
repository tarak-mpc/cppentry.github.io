---
layout:     post
title:      HDFS Snapshots
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="font-family:Verdana, Helvetica, Arial, sans-serif;">HDFS Snapshots</h1>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Overview" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Overview</a>
<ul><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Snapshottable_Directories" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Snapshottable Directories</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Snapshot_Paths" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Snapshot Paths</a></li></ul></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Upgrading_to_a_version_of_HDFS_with_snapshots" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Upgrading
 to a version of HDFS with snapshots</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Snapshot_Operations" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Snapshot Operations</a>
<ul><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Administrator_Operations" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Administrator Operations</a>
<ul><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Allow_Snapshots" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Allow Snapshots</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Disallow_Snapshots" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Disallow Snapshots</a></li></ul></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#User_Operations" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">User Operations</a>
<ul><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Create_Snapshots" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Create Snapshots</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Delete_Snapshots" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Delete Snapshots</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Rename_Snapshots" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Rename Snapshots</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Get_Snapshottable_Directory_Listing" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Get Snapshottable Directory Listing</a></li><li><a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsSnapshots.html#Get_Snapshots_Difference_Report" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Get Snapshots Difference Report</a></li></ul></li></ul></li></ul><a name="Overview" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></a><span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></span>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a></a>Overview</h2>
<p style="line-height:1.3em;font-size:12px;">HDFS Snapshots are read-only point-in-time copies of the file system. Snapshots can be taken on a subtree of the file system or the entire file system. Some common use cases of snapshots are data backup, protection
 against user errors and disaster recovery.</p>
<p style="line-height:1.3em;font-size:12px;">The implementation of HDFS Snapshots is efficient:</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">Snapshot creation is instantaneous: the cost is <em>O(1)</em> excluding the inode lookup time.</li><li style="font-size:12px;color:rgb(51,51,51);">Additional memory is used only when modifications are made relative to a snapshot: memory usage is <em>O(M)</em>, where <em>M</em> is the number of modified files/directories.</li><li style="font-size:12px;color:rgb(51,51,51);">Blocks in datanodes are not copied: the snapshot files record the block list and the file size. There is no data copying.</li><li style="font-size:12px;color:rgb(51,51,51);">Snapshots do not adversely affect regular HDFS operations: modifications are recorded in reverse chronological order so that the current data can be accessed directly. The snapshot data is computed by subtracting
 the modifications from the current data.</li></ul><a name="SnapshottableDirectories"></a>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Snapshottable_Directories"></a>Snapshottable Directories</h3>
<p style="line-height:1.3em;font-size:12px;">Snapshots can be taken on any directory once the directory has been set as <em>snapshottable</em>. A snapshottable directory is able to accommodate 65,536 simultaneous snapshots. There is no limit on the number of
 snapshottable directories. Administrators may set any directory to be snapshottable. If there are snapshots in a snapshottable directory, the directory can be neither deleted nor renamed before all the snapshots are deleted.</p>
<p style="line-height:1.3em;font-size:12px;">Nested snapshottable directories are currently not allowed. In other words, a directory cannot be set to snapshottable if one of its ancestors/descendants is a snapshottable directory.</p>
</div>
<a name="SnapshotPaths"></a>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Snapshot_Paths"></a>Snapshot Paths</h3>
<p style="line-height:1.3em;font-size:12px;">For a snapshottable directory, the path component <em>".snapshot"</em> is used for accessing its snapshots. Suppose <tt>/foo</tt> is a snapshottable directory, <tt>/foo/bar</tt> is a file/directory in <tt>/foo</tt>,
 and <tt>/foo</tt> has a snapshot <tt>s0</tt>. Then, the path</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>/foo/.snapshot/s0/bar</pre>
</div>
refers to the snapshot copy of <tt>/foo/bar</tt>. The usual API and CLI can work with the ".snapshot" paths. The following are some examples.
<ul><li style="font-size:12px;color:rgb(51,51,51);">Listing all the snapshots under a snapshottable directory:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -ls /foo/.snapshot</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Listing the files in snapshot <tt>s0</tt>:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -ls /foo/.snapshot/s0</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Copying a file from snapshot <tt>s0</tt>:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -cp -ptopax /foo/.snapshot/s0/bar /tmp</pre>
</div>
<p style="line-height:1.3em;color:rgb(0,0,0);">Note that this example uses the preserve option to preserve timestamps, ownership, permission, ACLs and XAttrs.</p>
</li></ul></div>
</div>
<a name="Upgrade" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></a><span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></span>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Upgrading_to_a_version_of_HDFS_with_snapshots"></a>Upgrading to a version of HDFS with snapshots</h2>
<p style="line-height:1.3em;font-size:12px;">The HDFS snapshot feature introduces a new reserved path name used to interact with snapshots: <tt>.snapshot</tt>. When upgrading from an older version of HDFS, existing paths named <tt>.snapshot</tt> need to first
 be renamed or deleted to avoid conflicting with the reserved path. See the upgrade section in <a href="http://hadoop.apache.org/docs/r2.7.3/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html#Upgrade_and_Rollback" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">the
 HDFS user guide</a> for more information.</p>
</div>
<a name="SnapshotOperations" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></a><span style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"></span>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);"><a name="Snapshot_Operations"></a>Snapshot Operations</h2>
<a name="AdministratorOperations"></a>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="Administrator_Operations"></a>Administrator Operations</h3>
<p style="line-height:1.3em;font-size:12px;">The operations described in this section require superuser privilege.</p>
<div class="section">
<h4><a name="Allow_Snapshots"></a>Allow Snapshots</h4>
<p style="line-height:1.3em;font-size:12px;">Allowing snapshots of a directory to be created. If the operation completes successfully, the directory becomes snapshottable.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">Command:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfsadmin -allowSnapshot &lt;path&gt;</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Arguments:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">See also the corresponding Java API <tt>void allowSnapshot(Path path)</tt> in <tt>HdfsAdmin</tt>.</p>
</div>
<div class="section">
<h4><a name="Disallow_Snapshots"></a>Disallow Snapshots</h4>
<p style="line-height:1.3em;font-size:12px;">Disallowing snapshots of a directory to be created. All snapshots of the directory must be deleted before disallowing snapshots.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">Command:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfsadmin -disallowSnapshot &lt;path&gt;</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Arguments:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">See also the corresponding Java API <tt>void disallowSnapshot(Path path)</tt> in <tt>HdfsAdmin</tt>.</p>
</div>
</div>
<a name="UserOperations"></a>
<div class="section">
<h3 style="color:rgb(102,102,102);font-size:14px;background-color:rgb(204,204,204);">
<a name="User_Operations"></a>User Operations</h3>
<p style="line-height:1.3em;font-size:12px;">The section describes user operations. Note that HDFS superuser can perform all the operations without satisfying the permission requirement in the individual operations.</p>
<div class="section">
<h4><a name="Create_Snapshots"></a>Create Snapshots</h4>
<p style="line-height:1.3em;font-size:12px;">Create a snapshot of a snapshottable directory. This operation requires owner privilege of the snapshottable directory.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">Command:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -createSnapshot &lt;path&gt; [&lt;snapshotName&gt;]</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Arguments:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
snapshotName</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The snapshot name, which is an optional argument. When it is omitted, a default name is generated using a timestamp with the format <tt>"'s'yyyyMMdd-HHmmss.SSS"</tt>, e.g. "s20130412-151029.033".</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">See also the corresponding Java API <tt>Path createSnapshot(Path path)</tt> and <tt>Path createSnapshot(Path path, String snapshotName)</tt> in <a href="http://hadoop.apache.org/docs/r2.7.3/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);"><tt>FileSystem</tt></a>.
 The snapshot path is returned in these methods.</p>
</div>
<div class="section">
<h4><a name="Delete_Snapshots"></a>Delete Snapshots</h4>
<p style="line-height:1.3em;font-size:12px;">Delete a snapshot of from a snapshottable directory. This operation requires owner privilege of the snapshottable directory.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">Command:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -deleteSnapshot &lt;path&gt; &lt;snapshotName&gt;</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Arguments:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
snapshotName</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The snapshot name.</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">See also the corresponding Java API <tt>void deleteSnapshot(Path path, String snapshotName)</tt> in <a href="http://hadoop.apache.org/docs/r2.7.3/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);"><tt>FileSystem</tt></a>.</p>
</div>
<div class="section">
<h4><a name="Rename_Snapshots"></a>Rename Snapshots</h4>
<p style="line-height:1.3em;font-size:12px;">Rename a snapshot. This operation requires owner privilege of the snapshottable directory.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">Command:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs dfs -renameSnapshot &lt;path&gt; &lt;oldName&gt; &lt;newName&gt;</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Arguments:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
oldName</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The old snapshot name.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
newName</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The new snapshot name.</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">See also the corresponding Java API <tt>void renameSnapshot(Path path, String oldName, String newName)</tt> in <a href="http://hadoop.apache.org/docs/r2.7.3/api/org/apache/hadoop/fs/FileSystem.html" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);"><tt>FileSystem</tt></a>.</p>
</div>
<div class="section">
<h4><a name="Get_Snapshottable_Directory_Listing"></a>Get Snapshottable Directory Listing</h4>
<p style="line-height:1.3em;font-size:12px;">Get all the snapshottable directories where the current user has permission to take snapshtos.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">Command:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs lsSnapshottableDir</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Arguments: none</li></ul><p style="line-height:1.3em;font-size:12px;">See also the corresponding Java API <tt>SnapshottableDirectoryStatus[] getSnapshottableDirectoryListing()</tt> in <tt>DistributedFileSystem</tt>.</p>
</div>
<div class="section">
<h4><a name="Get_Snapshots_Difference_Report"></a>Get Snapshots Difference Report</h4>
<p style="line-height:1.3em;font-size:12px;">Get the differences between two snapshots. This operation requires read access privilege for all files/directories in both snapshots.</p>
<ul><li style="font-size:12px;color:rgb(51,51,51);">Command:
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>hdfs snapshotDiff &lt;path&gt; &lt;fromSnapshot&gt; &lt;toSnapshot&gt;</pre>
</div>
</li><li style="font-size:12px;color:rgb(51,51,51);">Arguments:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
path</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The path of the snapshottable directory.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
fromSnapshot</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The name of the starting snapshot.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
toSnapshot</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The name of the ending snapshot.</td>
</tr></tbody></table></li><li style="font-size:12px;color:rgb(51,51,51);">Results:
<table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
+</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The file/directory has been created.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The file/directory has been deleted.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
M</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The file/directory has been modified.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
R</td>
<td style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The file/directory has been renamed.</td>
</tr></tbody></table></li></ul><p style="line-height:1.3em;font-size:12px;">A <em>RENAME</em> entry indicates a file/directory has been renamed but is still under the same snapshottable directory. A file/directory is reported as deleted if it was renamed to outside of the snapshottble directory.
 A file/directory renamed from outside of the snapshottble directory is reported as newly created.</p>
<p style="line-height:1.3em;font-size:12px;">The snapshot difference report does not guarantee the same operation sequence. For example, if we rename the directory <em>"/foo"</em> to <em>"/foo2"</em>, and then append new data to the file <em>"/foo2/bar"</em>,
 the difference report will be:</p>
<div class="source" style="border:1px solid rgb(153,153,153);overflow:auto;">
<pre>    R. /foo -&gt; /foo2
    M. /foo/bar
    </pre>
</div>
I.e., the changes on the files/directories under a renamed directory is reported using the original path before the rename (<em>"/foo/bar"</em> in the above example).
<p style="line-height:1.3em;font-size:12px;">See also the corresponding Java API <tt>SnapshotDiffReport getSnapshotDiffReport(Path path, String fromSnapshot, String toSnapshot)</tt> in <tt>DistributedFileSystem</tt>.</p>
</div>
</div>
</div>
            </div>
                </div>
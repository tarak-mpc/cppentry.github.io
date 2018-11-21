---
layout:     post
title:      HDFS的JAVA接口API操作实例
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>
</p><div><span><img src="http://t1.gstatic.com/images?q=tbn:W80NyWOrh52zMM::ftp.gva.es/mirror/apache/hadoop/core/hadoop-0.20.2/hadoop-0.20.2/src/docs/cn/src/documentation/resources/images/hadoop-logo-big.jpg&amp;t=1&amp;h=109&amp;w=462&amp;usg=__S8BZS5V33x3sS7LrVm4Cz1K4ffM=" alt=""></span></div>
<div><span><br></span></div>
<div><span>20:55 2010-6-2</span></div>
<div><span>运行环境：</span></div>
<div><span>Hadoop.0.20.2</span></div>
<div><span>CentOS 5.4 </span></div>
<div><span>
</span><div>java version "1.6.0_20-ea"</div>
<div>配置的是单机Hadoop环境</div>
</div>
<div><span>先看下我的运行截图</span></div>
<div>
<div><span><img src="http://blogimg.chinaunix.net/blog/upfile2/100602224327.png" border="0" alt="" width="500"></span></div>
</div>
<div><span><br></span></div>
<div><strong><span>主要参考这篇文章</span></strong></div>
<div><a href="http://myjavanotebook.blogspot.com/2008/05/hadoop-file-system-tutorial.html" rel="nofollow"><span>http://myjavanotebook.blogspot.com/2008/05/hadoop-file-system-tutorial.html</span></a></div>
<div><span><br></span></div>
<div><strong><span>1.Copy a file from the local file system to HDFS</span></strong></div>
<div><span>The srcFile variable needs to contain the full name (path + file name) of the file in the local file system. </span></div>
<div><span>The dstFile variable needs to contain the desired full name of the file in the Hadoop file system.</span></div>
<div><span><br></span></div>
<div><span><span>
</span></span><table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem hdfs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  Path srcPath <span>=</span> <span>new</span> Path<span>(</span>srcFile<span>)</span><span>;</span><br>  Path dstPath <span>=</span> <span>new</span> Path<span>(</span>dstFile<span>)</span><span>;</span><br>  hdfs<span>.</span>copyFromLocalFile<span>(</span>srcPath<span>,</span> dstPath<span>)</span><span>;</span></span></code></p>
<div><code><span><span><br></span></span></code></div>
</td>
</tr></tbody></table></div>
<div><span><br></span></div>
<div><span><span><strong><span>2.Create HDFS file</span></strong></span></span></div>
<div><span>The fileName variable contains the file name and path in the Hadoop file system. </span></div>
<div><span>The content of the file is the buff variable which is an array of bytes.</span></div>
<div>
<table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>//byte[] buff - The content of the file<br></span><br>  <span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem hdfs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  Path path <span>=</span> <span>new</span> Path<span>(</span>fileName<span>)</span><span>;</span><br>  FSDataOutputStream <span>outputStream</span> <span>=</span> hdfs<span>.</span><span>create</span><span>(</span>path<span>)</span><span>;</span><br>  <span>outputStream</span><span>.</span><span>write</span><span>(</span>buff<span>,</span> 0<span>,</span> buff<span>.</span><span>length</span><span>)</span><span>;</span></span></code></p>
</td>
</tr></tbody></table></div>
<div><span><br></span></div>
<div><strong><span>3.Rename HDFS file</span></strong></div>
<div><span>In order to rename a file in Hadoop file system, we need the full name (path + name) of </span></div>
<div><span>the file we want to rename. The rename method returns true if the file was renamed, otherwise false.</span></div>
<div><span><br></span></div>
<div><span><span>
</span></span><table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem hdfs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  Path fromPath <span>=</span> <span>new</span> Path<span>(</span>fromFileName<span>)</span><span>;</span><br>  Path toPath <span>=</span> <span>new</span> Path<span>(</span>toFileName<span>)</span><span>;</span><br>  <span>boolean</span> isRenamed <span>=</span> hdfs<span>.</span><span>rename</span><span>(</span>fromPath<span>,</span> toPath<span>)</span><span>;</span></span></code></p>
<div><code><span><span><br></span></span></code></div>
</td>
</tr></tbody></table></div>
<div><span><br></span></div>
<div><strong><span>4.Delete HDFS file</span></strong></div>
<div><span>In order to delete a file in Hadoop file system, we need the full name (path + name) </span></div>
<div><span>of the file we want to delete. The delete method returns true if the file was deleted, otherwise false.</span></div>
<div><span><br></span></div>
<div><span><span>
</span></span><table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem hdfs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  Path path <span>=</span> <span>new</span> Path<span>(</span>fileName<span>)</span><span>;</span><br>  <span>boolean</span> isDeleted <span>=</span> hdfs<span>.</span><span>delete</span><span>(</span>path<span>,</span> false<span>)</span><span>;</span><br><br>Recursive <span>delete</span><span>:</span><br>  <span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem hdfs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  Path path <span>=</span> <span>new</span> Path<span>(</span>fileName<span>)</span><span>;</span><br>  <span>boolean</span> isDeleted <span>=</span> hdfs<span>.</span><span>delete</span><span>(</span>path<span>,</span> true<span>)</span><span>;</span></span></code></p>
<div><code><span><span><br></span></span></code></div>
</td>
</tr></tbody></table></div>
<div><span> </span></div>
<div><span>  </span></div>
<div><strong><span>5.Get HDFS file last modification time</span></strong></div>
<div><span>In order to get the last modification time of a file in Hadoop file system, </span></div>
<div><span>we need the full name (path + name) of the file.</span></div>
<div><span><br></span></div>
<div><span><span>
</span></span><table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem hdfs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  Path path <span>=</span> <span>new</span> Path<span>(</span>fileName<span>)</span><span>;</span><br>  FileStatus fileStatus <span>=</span> hdfs<span>.</span>getFileStatus<span>(</span>path<span>)</span><span>;</span><br>  <span>long</span> modificationTime <span>=</span> fileStatus<span>.</span>getModificationTime</span></code></p>
</td>
</tr></tbody></table></div>
<div><span><br></span></div>
<div><span>  </span></div>
<div><strong><span> 6.Check if a file exists in HDFS</span></strong></div>
<div><span>In order to check the existance of a file in Hadoop file system, </span></div>
<div><span>we need the full name (path + name) of the file we want to check. </span></div>
<div><span>The exists methods returns true if the file exists, otherwise false.</span></div>
<div><span><br></span></div>
<div><span><span>
</span></span><table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem hdfs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  Path path <span>=</span> <span>new</span> Path<span>(</span>fileName<span>)</span><span>;</span><br>  <span>boolean</span> isExists <span>=</span> hdfs<span>.</span><span>exists</span><span>(</span>path<span>)</span><span>;</span></span></code></p>
</td>
</tr></tbody></table></div>
<div><span><br></span></div>
<div><span>  </span></div>
<div><strong><span> 7.Get the locations of a file in the HDFS cluster</span></strong></div>
<div><span> A file can exist on more than one node in the Hadoop file system cluster for two reasons:</span></div>
<div><span>Based on the HDFS cluster configuration, Hadoop saves parts of files on different nodes in the cluster.</span></div>
<div><span>Based on the HDFS cluster configuration, Hadoop saves more than one copy of each file on different nodes for redundancy (The default is three).</span></div>
<div><span> </span></div>
<table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem hdfs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  Path path <span>=</span> <span>new</span> Path<span>(</span>fileName<span>)</span><span>;</span><br>  FileStatus fileStatus <span>=</span> hdfs<span>.</span>getFileStatus<span>(</span>path<span>)</span><span>;</span><br><br>  BlockLocation<span>[</span><span>]</span> blkLocations <span>=</span> hdfs<span>.</span>getFileBlockLocations<span>(</span>path<span>,</span> 0<span>,</span> fileStatus<span>.</span>getLen<span>(</span><span>)</span><span>)</span><span>;</span></span></code></p>
<p><code><span><span style="color:#0000cc;"><span>BlockLocation<span>[</span><span>]</span> blkLocations <span>=</span> hdfs<span>.</span>getFileBlockLocations<span>(</span>fileStatus<span>,</span> 0<span>,</span> fileStatus<span>.</span>getLen<span>(</span><span>)</span><span>)</span><span>;</span></span><br></span>     //<strong>这个地方，作者写错了，需要把path改为fileStatus</strong><br>  <span>int</span> blkCount <span>=</span> blkLocations<span>.</span><span>length</span><span>;</span><br>  <span>for</span> <span>(</span><span>int</span> i<span>=</span>0<span>;</span> i <span>&lt;</span> blkCount<span>;</span> i<span>+</span><span>+</span><span>)</span> <span>{</span><br>    <span>String</span><span>[</span><span>]</span> hosts <span>=</span> blkLocations<span>[</span>i<span>]</span><span>.</span>getHosts<span>(</span><span>)</span><span>;</span><br>    <span>// Do something with the block hosts<br></span>   <span>}</span><br></span></code></p>
</td>
</tr></tbody></table><div><span><br></span></div>
<div><strong><span>8. Get a list of all the nodes host names in the HDFS cluster</span></strong></div>
<div><span><br></span></div>
<div><span>  his method casts the FileSystem Object to a DistributedFileSystem Object. </span></div>
<div><span>  This method will work only when Hadoop is configured as a cluster. </span></div>
<div><span>  Running Hadoop on the local machine only, in a non cluster configuration will</span></div>
<div><span>   cause this method to throw an Exception.</span></div>
<div><span>   </span></div>
<table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>Configuration</span> <span>config</span> <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>  FileSystem fs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span><span>config</span><span>)</span><span>;</span><br>  DistributedFileSystem hdfs <span>=</span> <span>(</span>DistributedFileSystem<span>)</span> fs<span>;</span><br>  DatanodeInfo<span>[</span><span>]</span> dataNodeStats <span>=</span> hdfs<span>.</span>getDataNodeStats<span>(</span><span>)</span><span>;</span><br>  <span>String</span><span>[</span><span>]</span> names <span>=</span> <span>new</span> <span>String</span><span>[</span>dataNodeStats<span>.</span><span>length</span><span>]</span><span>;</span><br>  <span>for</span> <span>(</span><span>int</span> i <span>=</span> 0<span>;</span> i <span>&lt;</span> dataNodeStats<span>.</span><span>length</span><span>;</span> i<span>+</span><span>+</span><span>)</span> <span>{</span><br>      names<span>[</span>i<span>]</span> <span>=</span> dataNodeStats<span>[</span>i<span>]</span><span>.</span><span>getHostName</span><span>(</span><span>)</span><span>;</span><br>  <span>}</span></span></code></p>
<div><code><span><span><br></span></span></code></div>
</td>
</tr></tbody></table><div><span>  </span></div>
<div><span>  </span></div>
<div><span>程序实例</span></div>
<div><span><br></span></div>
<div><span><span>
</span></span><table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>/*<br> * <br> * 演示操作HDFS的java接口<br> * <br> * */</span><br><br><br><span>import</span> <span>org</span><span>.</span>apache<span>.</span>hadoop<span>.</span>conf<span>.</span><span>*</span><span>;</span><br><span>import</span> <span>org</span><span>.</span>apache<span>.</span>hadoop<span>.</span>fs<span>.</span><span>*</span><span>;</span><br><span>import</span> <span>org</span><span>.</span>apache<span>.</span>hadoop<span>.</span>hdfs<span>.</span><span>*</span><span>;</span><br><span>import</span> <span>org</span><span>.</span>apache<span>.</span>hadoop<span>.</span>hdfs<span>.</span>protocol<span>.</span><span>*</span><span>;</span><br><span>import</span> <span>java</span><span>.</span><span>util</span><span>.</span><span>Date</span><span>;</span><br><br><span>public</span> <span>class</span> DFSOperater <span>{</span><br><br>    <span>/**<br>     * @param args<br>     */</span><br>    <span>public</span> <span>static</span> <span>void</span> main<span>(</span><span>String</span><span>[</span><span>]</span> args<span>)</span> <span>{</span><br><br>        <span>Configuration</span> conf <span>=</span> <span>new</span> <span>Configuration</span><span>(</span><span>)</span><span>;</span><br>        <br>        <span>try</span> <span>{</span><br>            <span>// Get a list of all the nodes host names in the HDFS cluster<br></span><br>            FileSystem fs <span>=</span> FileSystem<span>.</span><span>get</span><span>(</span>conf<span>)</span><span>;</span><br>            DistributedFileSystem hdfs <span>=</span> <span>(</span>DistributedFileSystem<span>)</span>fs<span>;</span><br>            DatanodeInfo<span>[</span><span>]</span> dataNodeStats <span>=</span> hdfs<span>.</span>getDataNodeStats<span>(</span><span>)</span><span>;</span><br>            <span>String</span><span>[</span><span>]</span> names <span>=</span> <span>new</span> <span>String</span><span>[</span>dataNodeStats<span>.</span><span>length</span><span>]</span><span>;</span><br>            <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span><span>"list of all the nodes in HDFS cluster:"</span><span>)</span><span>;</span> <span>//print info<br></span><br>            <span>for</span><span>(</span><span>int</span> i<span>=</span>0<span>;</span> i <span>&lt;</span> dataNodeStats<span>.</span><span>length</span><span>;</span> i<span>+</span><span>+</span><span>)</span><span>{</span><br>                names<span>[</span>i<span>]</span> <span>=</span> dataNodeStats<span>[</span>i<span>]</span><span>.</span><span>getHostName</span><span>(</span><span>)</span><span>;</span><br>                <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span>names<span>[</span>i<span>]</span><span>)</span><span>;</span> <span>//print info<br></span><br>            <span>}</span><br>            Path f <span>=</span> <span>new</span> Path<span>(</span><span>"/user/cluster/dfs.txt"</span><span>)</span><span>;</span><br>            <br>            <span>//check if a file exists in HDFS<br></span><br>            <span>boolean</span> isExists <span>=</span> fs<span>.</span><span>exists</span><span>(</span>f<span>)</span><span>;</span><br>            <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span><span>"The file exists? ["</span> <span>+</span> isExists <span>+</span> <span>"]"</span><span>)</span><span>;</span><br>            <br>            <span>//if the file exist, delete it<br></span><br>            <span>if</span><span>(</span>isExists<span>)</span><span>{</span><br>                 <span>boolean</span> isDeleted <span>=</span> hdfs<span>.</span><span>delete</span><span>(</span>f<span>,</span> false<span>)</span><span>;</span><span>//fase : not recursive<br></span><br>                 <span>if</span><span>(</span>isDeleted<span>)</span><span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span><span>"now delete "</span> <span>+</span> f<span>.</span><span>getName</span><span>(</span><span>)</span><span>)</span><span>;</span>                 <br>            <span>}</span><br>            <br>            <span>//create and write<br></span><br>            <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span><span>"create and write ["</span> <span>+</span> f<span>.</span><span>getName</span><span>(</span><span>)</span> <span>+</span> <span>"] to hdfs:"</span><span>)</span><span>;</span><br>            FSDataOutputStream os <span>=</span> fs<span>.</span><span>create</span><span>(</span>f<span>,</span> true<span>,</span> 0<span>)</span><span>;</span><br>            <span>for</span><span>(</span><span>int</span> i<span>=</span>0<span>;</span> i<span>&lt;</span>10<span>;</span> i<span>+</span><span>+</span><span>)</span><span>{</span><br>                os<span>.</span><span>writeChars</span><span>(</span><span>"test hdfs "</span><span>)</span><span>;</span><br>            <span>}</span><br>            os<span>.</span><span>writeChars</span><span>(</span><span>"/n"</span><span>)</span><span>;</span><br>            os<span>.</span><span>close</span><span>(</span><span>)</span><span>;</span><br>            <br>            <span>//get the locations of a file in HDFS<br></span><br>            <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span><span>"locations of file in HDFS:"</span><span>)</span><span>;</span><br>            FileStatus filestatus <span>=</span> fs<span>.</span>getFileStatus<span>(</span>f<span>)</span><span>;</span><br>            BlockLocation<span>[</span><span>]</span> blkLocations <span>=</span> fs<span>.</span>getFileBlockLocations<span>(</span>filestatus<span>,</span> 0<span>,</span>filestatus<span>.</span>getLen<span>(</span><span>)</span><span>)</span><span>;</span><br>            <span>int</span> blkCount <span>=</span> blkLocations<span>.</span><span>length</span><span>;</span><br>            <span>for</span><span>(</span><span>int</span> i<span>=</span>0<span>;</span> i <span>&lt;</span> blkCount<span>;</span> i<span>+</span><span>+</span><span>)</span><span>{</span><br>                <span>String</span><span>[</span><span>]</span> hosts <span>=</span> blkLocations<span>[</span>i<span>]</span><span>.</span>getHosts<span>(</span><span>)</span><span>;</span><br>                <span>//Do sth with the block hosts<br></span><br>                <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span>hosts<span>)</span><span>;</span><br>            <span>}</span><br>            <br>            <span>//get HDFS file last modification time<br></span><br>            <span>long</span> modificationTime <span>=</span> filestatus<span>.</span>getModificationTime<span>(</span><span>)</span><span>;</span> <span>// measured in milliseconds since the epoch<br></span><br>            <span>Date</span> d <span>=</span> <span>new</span> <span>Date</span><span>(</span>modificationTime<span>)</span><span>;</span><br>         <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span>d<span>)</span><span>;</span><br>            <span>//reading from HDFS<br></span><br>            <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span><span>"read ["</span> <span>+</span> f<span>.</span><span>getName</span><span>(</span><span>)</span> <span>+</span> <span>"] from hdfs:"</span><span>)</span><span>;</span><br>     FSDataInputStream dis <span>=</span> fs<span>.</span><span>open</span><span>(</span>f<span>)</span><span>;</span><br>     <span>System</span><span>.</span>out<span>.</span><span>println</span><span>(</span>dis<span>.</span><span>readUTF</span><span>(</span><span>)</span><span>)</span><span>;</span><br>     dis<span>.</span><span>close</span><span>(</span><span>)</span><span>;</span><br><br>        <span>}</span> <span>catch</span> <span>(</span><span>Exception</span> e<span>)</span> <span>{</span><br>            <span>// TODO: handle exception<br></span><br>            e<span>.</span><span>printStackTrace</span><span>(</span><span>)</span><span>;</span><br>        <span>}</span><br>                <br>    <span>}</span><br><br><span>}</span><br></span></code></p>
<div><code><span><span><br></span></span></code></div>
</td>
</tr></tbody></table></div>
<div><span><br></span></div>
<div><span>编译后拷贝到node1上面运行，杯具，不会用Eclipse插件</span></div>
<div><span><span>
</span></span><table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>[</span>cluster <span>/</span>opt<span>/</span>hadoop<span>/</span>source<span>]</span><span>$</span><span>cp</span> <span>/</span>opt<span>/</span>winxp<span>/</span>hadoop<span>/</span>dfs_operator<span>.</span>jar <span>.</span><br><span>[</span>cluster <span>/</span>opt<span>/</span>hadoop<span>/</span>source<span>]</span><span>$</span><span>hadoop</span> jar dfs_operator<span>.</span>jar DFSOperater<br>list of all the nodes in HDFS cluster<span>:</span><br>node1<br>The file <span>exists</span><span>?</span> <span>[</span><span>true</span><span>]</span><br>now <span>delete</span> dfs<span>.</span>txt<br>create <span>and</span> <span>write</span> <span>[</span>dfs<span>.</span>txt<span>]</span> to hdfs<span>:</span><br>locations of file in HDFS<span>:</span><br><span>[</span>Ljava<span>.</span>lang<span>.</span>String<span>;</span><span>@</span><span>72ffb</span><br>Wed Jun 02 18<span>:</span>29<span>:</span>14 CST 2010<br><span>read</span> <span>[</span>dfs<span>.</span>txt<span>]</span> from hdfs<span>:</span><br>est hdfs test hdfs test hdfs test hdfs test hdfs test hdfs</span></code></p>
<div><code><span><br></span></code></div>
</td>
</tr></tbody></table></div>
<div><span><br></span></div>
<div><span>运行成功！查看输出文件</span></div>
<table border="1" cellspacing="0" cellpadding="0" bgcolor="#F1F1F1"><tbody><tr><td>
<p><code><span><span>[</span>cluster <span>/</span>opt<span>/</span>hadoop<span>/</span>source<span>]</span><span>$</span><span>hadoop</span> fs <span>-</span>cat dfs<span>.</span>txt<br>test hdfs test hdfs test hdfs test hdfs test hdfs test hdfs test hdfs test hdfs test hdfs test hdfs</span></code></p>
</td>
</tr></tbody></table><br><div><span><span style="font-family:'宋体';"><span style="font-size:medium;"><span>作者：qkshan@twitter, &lt;myduanli@gmail.com&gt; </span><br><span>来源：</span></span></span><a href="http://duanli.cublog.cn/" rel="nofollow"><span style="color:#0000ff;font-size:medium;">http://duanli.cublog.cn</span></a><span><br><span><span style="font-size:medium;">说明：转载请注明来源，交流请Email给作者</span></span></span></span></div>
            </div>
                </div>
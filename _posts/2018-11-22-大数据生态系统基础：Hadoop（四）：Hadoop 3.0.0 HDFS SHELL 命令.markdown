---
layout:     post
title:      大数据生态系统基础：Hadoop（四）：Hadoop 3.0.0 HDFS SHELL 命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/caridle/article/details/77394680				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>一、介绍</p>
<p>       可以通过许多不同的方式从应用程序访问HDFS。在本地，HDFS为应用程序提供了一个文件系统Java API。这个Java API和REST API的C语言包装也是可用的。此外，还可以使用HTTP浏览器来浏览HDFS实例的文件。通过使用NFS网关，可以将HDFS作为客户机本地文件系统的一部分进行安装。<br></p>
<p>       使用 hadoop fs 命令可以直接操作 hdfs里面的文件，这些操作很类似 linux 的操作。</p>
<p> </p>
<p>二、FS SHELL</p>
<p>      HDFS允许用户数据以文件和目录的形式进行组织。它提供了一个名为FS shell的commandline接口，它允许用户与HDFS中的数据进行交互。这个命令集的语法类似于其他用户已经熟悉的shell(如bash、csh)。以下是一些示例操作/命令对:</p>
<p>      
</p><table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;color:rgb(0,0,0);font-family:Verdana, Helvetica, Arial, sans-serif;"><thead><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
说明</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
命令</th>
</tr></thead><tbody><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
 创建目录名 <tt>/foodir</tt></td>
<td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>bin/hadoop dfs -mkdir /foodir</tt></td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">删除目录名 </span>/foodir</tt></td>
<td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>bin/hadoop fs -rm -R /foodir</tt></td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt><span style="font-family:Verdana, Helvetica, Arial, sans-serif;">查看</span>/foodir/myfile.txt文件内容</tt></td>
<td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>bin/hadoop dfs -cat /foodir/myfile.txt<br><br></tt></td>
</tr></tbody></table><br><p>        FS shell 的内容比较多，可以参考：http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html</p>
<p>      </p>
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#appendToFile" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">appendToFile</a> ：将单个src或多个srcs从本地文件系统附加到目标文件系统。还可以从stdin和附加到目标文件系统读取输入。</li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#cat" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">cat</a> ：查看文件内容</li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#checksum" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">checksum</a>：返回一个文件的校验和信息。
<ul style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;"><li style="font-size:12px;color:rgb(51,51,51);"><tt>hadoop fs -checksum hdfs://nn1.example.com/file1</tt></li><li style="font-size:12px;color:rgb(51,51,51);"><tt>hadoop fs -checksum file:///etc/hosts</tt></li></ul></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#chgrp" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">chgrp</a>  ：更改组文件关联。用户必须是文件的所有者，或者是超级用户。-r
 ，表示目录下所有的内容</li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#chmod" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">chmod</a> ：改变权限</li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#chown" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">chown</a> ：改变拥有者</li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#copyFromLocal" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">copyFromLocal</a>：类似于fs
 -put命令，但源代码仅限于本地文件引用。</li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#copyToLocal" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">copyToLocal</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#count" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">count</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#cp" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">cp</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#createSnapshot" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">createSnapshot</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#deleteSnapshot" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">deleteSnapshot</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#df" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">df</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#du" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">du</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#dus" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">dus</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#expunge" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">expunge</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#find" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">find</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#get" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">get</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfacl" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">getfacl</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#getfattr" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">getfattr</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#getmerge" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">getmerge</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#help" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">help</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#ls" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">ls</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#lsr" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">lsr</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#mkdir" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">mkdir</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#moveFromLocal" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">moveFromLocal</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#moveToLocal" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">moveToLocal</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#mv" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">mv</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#put" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">put</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#renameSnapshot" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">renameSnapshot</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">rm</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#rmdir" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">rmdir</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#rmr" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">rmr</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfacl" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">setfacl</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#setfattr" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">setfattr</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#setrep" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">setrep</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#stat" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">stat</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#tail" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">tail</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#test" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">test</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#text" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">text</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#touchz" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">touchz</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#truncate" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">truncate</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#usage" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">usage</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#Deleting_objects" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Deleting objects</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#Overwriting_Objects" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Overwriting
 Objects</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#Timestamps" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Timestamps</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#Security_model_and_operations" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Security
 model and operations</a></li><li style="font-size:12px;color:rgb(51,51,51);"><a href="http://hadoop.apache.org/docs/r3.0.0-alpha4/hadoop-project-dist/hadoop-common/FileSystemShell.html#Commands_of_limited_value" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Commands
 of limited value</a></li></ul><p></p>
<p>二、DFSAdmin</p>
<p> 他的DFSAdmin命令集用于管理HDFS集群。这些命令只能由HDFS管理员使用。以下是一些示例操作/命令对:</p>
<p>
</p><table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;color:rgb(0,0,0);font-family:Verdana, Helvetica, Arial, sans-serif;"><thead><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
说民</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
命令</th>
</tr></thead><tbody><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
设置集群为安全模式</td>
<td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>bin/hdfs dfsadmin -safemode enter</tt></td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
查看DataNodes的说明报告 </td>
<td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>bin/hdfs dfsadmin -report</tt></td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
重新校验或解除DataNode(s) </td>
<td align="left" style="font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>bin/hdfs dfsadmin -refreshNodes</tt></td>
</tr></tbody></table><br><p>三、浏览接口</p>
<p> 典型的HDFS安装配置一个web服务器，通过一个可配置的TCP端口来公开HDFS命名空间。这允许用户浏览HDFS名称空间，并使用web浏览器查看其文件的内容。</p>
<p><br></p>
<p>四、空间回收</p>
<p> 1、文件删除和恢复</p>
<p>           如果启用了垃圾配置，那么通过FS Shell删除的文件不会立即从HDFS中删除。相反，HDFS将它移动到一个垃圾目录(每个用户都有自己的垃圾目录下/用户/用户名/.Trash)。只要文件还在垃圾中，文件就可以迅速恢复。<br><br><br>
           最近删除的文件被移动到当前的垃圾目录(/用户/用户名/.trash/current)，并且在一个可配置的时间间隔内，HDFS在当前的垃圾目录中创建检查点(在/用户/用户名/.Trash/&lt;date&gt;)，并在过期时删除旧的检查点。请参阅FS shell关于清除垃圾的命令。<br><br><br>
       在垃圾结束后，NameNode从HDFS名称空间中删除了文件。文件的删除会导致与该文件相关的块被释放。注意，在一个用户删除文件和在HDFS中相应增加空闲空间的时间之间，可能会有一个明显的时间延迟。<br><br><br>
        下面是一个示例，它将展示如何通过FS Shell将文件从HDFS中删除。我们在删除目录下创建了两个文件(test1和test2)</p>
<p></p>
<pre style="font-size:13px;">$ hadoop fs -mkdir -p delete/test1
$ hadoop fs -mkdir -p delete/test2
$ hadoop fs -ls delete/
Found 2 items
drwxr-xr-x   - hadoop hadoop          0 2015-05-08 12:39 delete/test1
drwxr-xr-x   - hadoop hadoop          0 2015-05-08 12:40 delete/test2</pre>
我们将删除文件test1。下面的注释显示该文件已被移动到垃圾目录。<br><p></p>
<p></p>
<pre style="font-size:13px;">$ hadoop fs -rm -r delete/test1
Moved: hdfs://localhost:9820/user/hadoop/delete/test1 to trash at: hdfs://localhost:9820/user/hadoop/.Trash/Current</pre>
<br><p></p>
<p>现在，我们将使用skip垃圾箱选项删除该文件，该选项不会将文件发送到垃圾。它将完全从HDFS中删除。<br></p>
<p></p>
<pre style="font-size:13px;">$ hadoop fs -rm -r -skipTrash delete/test2
Deleted delete/test2</pre>
<br><p></p>
<p>我们现在可以看到，垃圾目录只包含文件test1。<br></p>
<p></p>
<pre style="font-size:13px;">$ hadoop fs -ls .Trash/Current/user/hadoop/delete/
Found 1 items\
drwxr-xr-x   - hadoop hadoop          0 2015-05-08 12:39 .Trash/Current/user/hadoop/delete/test1</pre>
<br><p></p>
<p>因此，文件test1将被删除，文件test2被永久删除。<br></p>
<p> </p>
<p>2、降低复制因子</p>
<p>    当文件的复制因子减少时，NameNode会选择可以删除的多余副本。下一个心跳将这些信息传输到DataNode。然后DataNode删除相应的块，并在集群中显示相应的空闲空间。再一次，在setrepapi调用的完成和集群中的空闲空间的出现之间可能会有一段时间延迟。<br></p>
<p><br></p>
<p><br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
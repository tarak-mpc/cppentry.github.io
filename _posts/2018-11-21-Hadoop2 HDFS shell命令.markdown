---
layout:     post
title:      Hadoop2 HDFS shell命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h3><span style="font-size:12px;">Hadoop2 HDFS shell命令：</span></h3>
<p><span style="font-size:12px;">1. <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -appendToFile &lt;localsrc&gt; ... &lt;dst&gt;</span></tt></span></p>
<p><span style="font-size:12px;">可同时上传多个文件到HDFS里面</span></p>
<p><span style="font-size:12px;">2.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -cat URI [URI ...]</span></tt></span></p>
<p><span style="font-size:12px;">查看文件内容</span></p>
<p><span style="font-size:12px;">3.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -chgrp [-R] GROUP URI [URI ...]</span></span></p>
<p><span style="font-size:12px;color:#000000;background:#FFFFFF;">修改文件所属组</span></p>
<p><span style="font-size:12px;">4.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; URI [URI ...]</span></tt></span></p>
<p><span style="font-size:12px;color:#000000;">修改文件权限</span></p>
<p><span style="font-size:12px;">5.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -chown [-R] [OWNER][:[GROUP]] URI [URI ]</span></span></p>
<p><span style="font-size:12px;color:#000000;background:#FFFFFF;">修改文件所有者，文件所属组，其他用户的读、写、执行权限</span></p>
<p><span style="font-size:12px;">6.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -copyFromLocal &lt;localsrc&gt; URI</span></span></p>
<p><span style="font-size:12px;">复制文件到hdfs</span></p>
<p><span style="font-size:12px;">7.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -copyToLocal [-ignorecrc] [-crc] URI &lt;localdst&gt;</span></tt></span></p>
<p><span style="font-size:12px;">复制文件到本地</span></p>
<p><span style="font-family:Arial;"><span style="font-size:12px;"><tt><span style="color:#000000;background:#FFFFFF;">8.</span></tt><span style="color:#000000;background:#FFFFFF;"> hdfs dfs -count [-q] &lt;paths&gt;</span></span></span></p>
<p><span style="font-size:12px;">统计文件及文件夹数目</span></p>
<p><span style="font-size:12px;">9.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -cp [-f] URI [URI ...] &lt;dest&gt;</span></tt></span></p>
<p><span style="font-size:12px;">Hadoop HDFS 文件系统间的文件复制</span></p>
<p><span style="font-size:12px;">10.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -du [-s] [-h] URI [URI ...]</span></span></p>
<p><span style="font-size:12px;">统计目录下的文件及大小</span></p>
<p><span style="font-size:12px;">11.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -dus &lt;args&gt;</span></span></p>
<p><span style="font-size:12px;">汇总目录下的文件总大小</span></p>
<p><span style="font-size:12px;">12.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -expunge</span></tt></span></p>
<p><span style="font-size:12px;"><span style="color:#333333;background:#FFFFFF;">清空回收站，文件被删除时，它首先会移到临时目录</span><span style="color:#333333;background:#FFFFFF;">.Trash/</span><span style="color:#333333;background:#FFFFFF;">中，当超过延迟时间之后，文件才会被永久删除</span></span></p>
<p><span style="font-size:12px;">13.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -get [-ignorecrc] [-crc] &lt;src&gt; &lt;localdst&gt;</span></span></p>
<p><span style="font-size:12px;">下载文件到本地</span></p>
<p><span style="font-size:12px;">14.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -getfacl [-R] &lt;path&gt;</span></span></p>
<p><span style="font-size:12px;">查看ACL （访问权限组拥有者）</span></p>
<p><span style="font-size:12px;">15.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -getmerge &lt;src&gt; &lt;localdst&gt; [addnl]</span></span></p>
<p><span style="font-size:12px;">合并下载文件到本地</span></p>
<p><span style="font-size:12px;">16.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -ls &lt;args&gt;</span></span></p>
<p><span style="font-size:12px;">查看目录</span></p>
<p><span style="font-size:12px;">17.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -lsr &lt;args&gt;</span></span></p>
<p><span style="font-size:12px;"><span style="color:#333333;background:#FFFFFF;">循环列出目录、子目录及文件信息</span><span style="color:#333333;background:#FFFFFF;"> </span></span></p>
<p><span style="font-size:12px;">18.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -mkdir [-p] &lt;paths&gt;</span></span></p>
<p><span style="font-size:12px;">创建空白文件夹</span></p>
<p><span style="font-size:12px;">19.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">dfs -moveFromLocal &lt;localsrc&gt; &lt;dst&gt;</span></tt></span></p>
<p><span style="font-size:12px;">剪切文件到hdfs</span></p>
<p><span style="font-size:12px;">20.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -moveToLocal [-crc] &lt;src&gt; &lt;dst&gt;</span></tt></span></p>
<p><span style="font-size:12px;">剪切文件到本地</span></p>
<p><span style="font-size:12px;">21.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -mv URI [URI ...] &lt;dest&gt;</span></span></p>
<p><span style="font-size:12px;">剪切hdfs文件</span></p>
<p><span style="font-size:12px;">22.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -put &lt;localsrc&gt; ... &lt;dst&gt;</span></span></p>
<p><span style="font-size:12px;">上传文件</span></p>
<p><span style="font-size:12px;">23.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -rm [-skipTrash] URI [URI ...]</span></span></p>
<p><span style="font-size:12px;">删除文件/空白文件夹</span></p>
<p><span style="font-size:12px;">24.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -rmr [-skipTrash] URI [URI ...]</span></tt></span></p>
<p><span style="font-size:12px;">递归删除  删除文件及文件夹下的所有文件</span></p>
<p><span style="font-size:12px;">25.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -setfacl [-R] [</span><span style="color:#000000;">-b|-k</span> <span style="color:#000000;">-m|-x &lt;acl_spec&gt;</span> &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]</span></p>
<p style="background:#FFFFFF;"><span style="font-size:12px;">Sets Access Control Lists (ACLs) of files and directories.</span></p>
<p style="background:#FFFFFF;"><span style="font-size:12px;">Options:</span></p>
<ul><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">-b: Remove all but the base ACL entries. The entries for user, group and others are retained for compatibility with permission bits.</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">-k: Remove the default ACL.</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">-R: Apply operations to all files and directories recursively.</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">-m: Modify ACL. New entries are added to the ACL, and existing entries are retained.</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">-x: Remove specified ACL entries. Other ACL entries are retained.</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">--set: Fully replace the ACL, discarding all existing entries. The <em>acl_spec</em> must include entries for user, group, and others for compatibility with permission bits.</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;"><em>acl_spec</em>: Comma separated list of ACL entries.</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;"><em>path</em>: File or directory to modify.</span></li></ul><p style="background:#FFFFFF;"><span style="font-size:12px;">Examples:</span></p>
<ul><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">hdfs dfs -setfacl -m user:hadoop:rw- /file</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">hdfs dfs -setfacl -x user:hadoop /file</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">hdfs dfs -setfacl -b /file</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">hdfs dfs -setfacl -k /dir</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">hdfs dfs -setfacl --set user::rw-,user:hadoop:rw-,group::r--,other::r-- /file</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">hdfs dfs -setfacl -R -m user:hadoop:r-x /dir</span></li><li style="background:#FFFFFF;color:rgb(51,51,51);"><span style="font-size:12px;">hdfs dfs -setfacl -m default:user:hadoop:r-x /dir</span></li></ul><p style="background:#FFFFFF;"><span style="font-size:12px;">Exit Code:</span></p>
<p style="background:#FFFFFF;"><span style="font-size:12px;">Returns 0 on success and non-zero on error.</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;">26.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -setrep [-R] [-w] &lt;numReplicas&gt; &lt;path&gt;</span></tt></span></p>
<p><span style="font-size:12px;">修改副本数</span></p>
<p><span style="font-size:12px;">27.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -stat URI [URI ...]</span></span></p>
<p><span style="font-size:12px;">显示文件统计信息</span></p>
<p><span style="font-size:12px;">28.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -tail [-f] URI</span></tt></span></p>
<p><span style="font-size:12px;">查看文件尾部信息</span></p>
<p><span style="font-size:12px;">29.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -test -[ezd] URI</span></span></p>
<p style="background:#FFFFFF;"><span style="font-size:12px;"><span style="color:#333333;">对PATH</span>进行如下类型的检查： </span></p>
<p style="background:#FFFFFF;"><span style="font-size:12px;"><span style="color:#333333;">-e PATH</span><span style="color:#333333;">是否存在，如果PATH</span>存在，返回0，否则返回1 </span></p>
<p style="background:#FFFFFF;"><span style="font-size:12px;"><span style="color:#333333;">-z</span><span style="color:#333333;">文件是否为空，如果长度为0</span>，返回0，否则返回1 </span></p>
<p style="background:#FFFFFF;"><span style="font-size:12px;"><span style="color:#333333;">-d</span><span style="color:#333333;">是否为目录，如果PATH</span>为目录，返回0，否则返回1 </span></p>
<p><span style="font-size:12px;">30.<span style="color:#000000;background:#FFFFFF;"> hdfs dfs -text &lt;src&gt;</span></span></p>
<p><span style="font-size:12px;">查看文件内容</span></p>
<p><span style="font-size:12px;">31.  <tt><span style="font-family:Arial;color:#000000;background:#FFFFFF;">hdfs dfs -touchz URI [URI ...]</span></tt></span></p>
<p><span style="font-size:12px;"><span style="color:#333333;background:#FFFFFF;">创建长度为</span><span style="color:#333333;background:#FFFFFF;">0</span><span style="color:#333333;background:#FFFFFF;">的空文件</span></span></p>
<p><span style="color:#333333;"></span><span style="font-size:12px;"> </span></p>
<h2><span><span><span style="font-size:12px;">FS Shell</span></span></span></h2>
<p><span style="font-size:12px;">      调用文件系统<span>(FS)Shell</span>命令应使用<span> bin/hadoop fs &lt;args&gt;</span>的形式。 所有的的<span>FS shell</span>命令使用<span>URI</span>路径作为参数。<span>URI</span>格式是<span>scheme://authority/path</span>。对<span>HDFS</span>文件系统，<span>scheme</span>是<span>hdfs</span>，对本地文件系统，<span>scheme</span>是<span>file</span>。其中<span>scheme</span>和<span>authority</span>参数都是可选的，如果未加指定，就会使用配置中指定的默认<span>scheme</span>。一个<span>HDFS</span>文件或目录比如<span>/parent/child</span>可以表示成<span>hdfs://namenode:namenodeport/parent/child</span>，或者更简单的<span>/parent/child</span>（假设你配置文件中的默认值是<span>namenode:namenodeport</span>）。大多数<span>FS
 Shell</span>命令的行为和对应的<span>Unix Shell</span>命令类似，不同之处会在下面介绍各命令使用详情时指出。出错信息会输出到<span>stderr</span>，其他信息输出到<span>stdout</span>。</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;"><span>1.2<span>     </span></span>常用命令使用</span></p>
<p><span style="font-size:12px;"><span>1)<span>   </span></span>cat</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -cat URI [URI </span>…<span>]</span></span></p>
<p><span style="font-size:12px;">将路径指定文件的内容输出到<span>stdout</span>。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -cat hdfs://host1:port1/file1 hdfs://host2:port2/file2</span></p>
<p><span style="font-size:12px;">hadoop fs -cat file:///file3 /user/hadoop/file4</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>2)<span>   </span></span>chgrp</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -chgrp [-R]</span></span></p>
<p><span style="font-size:12px;">改变文件所属的组。使用<span>-R</span>将使改变在目录结构下递归进行。命令的使用者必须是文件的所有者或者超级用户。更多的信息请参见<span>HDFS</span>权限用户指南。</span></p>
<p><span style="font-size:12px;"><span>3)<span>   </span></span>chmod</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; URI [URI</span>…<span>]</span></span></p>
<p><span style="font-size:12px;">改变文件的权限。使用<span>-R</span>将使改变在目录结构下递归进行。命令的使用者必须是文件的所有者或者超级用户。更多的信息请参见<span>HDFS</span>权限用户指南。</span></p>
<p><span style="font-size:12px;"><span>4)<span>   </span></span>chown</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI ]</span></span></p>
<p><span style="font-size:12px;">改变文件的拥有者。使用<span>-R</span>将使改变在目录结构下递归进行。命令的使用者必须是超级用户。更多的信息请参见<span>HDFS</span>权限用户指南。</span></p>
<p><span style="font-size:12px;"><span>5)<span>   </span></span>copyFromLocal</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -copyFromLocal &lt;localsrc&gt; URI</span></span></p>
<p><span style="font-size:12px;">除了限定源路径是一个本地文件外，和<span>put</span>命令相似。</span></p>
<p><span style="font-size:12px;"><span>6)<span>   </span></span>copyToLocal</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -copyToLocal [-ignorecrc] [-crc] URI &lt;localdst&gt;</span></span></p>
<p><span style="font-size:12px;">除了限定目标路径是一个本地文件外，和<span>get</span>命令类似。</span></p>
<p><span style="font-size:12px;"><span>7)<span>   </span></span>cp</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -cp URI [URI </span>…<span>] &lt;dest&gt;</span></span></p>
<p><span style="font-size:12px;">将文件从源路径复制到目标路径。这个命令允许有多个源路径，此时目标路径必须是一个目录。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -cp /user/hadoop/file1 /user/hadoop/file2</span></p>
<p><span style="font-size:12px;">hadoop fs -cp /user/hadoop/file1 /user/hadoop/file2 /user/hadoop/dir</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>8)<span>   </span></span>du</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -du URI [URI </span>…<span>]</span></span></p>
<p><span style="font-size:12px;">显示目录中所有文件的大小，或者当只指定一个文件时，显示此文件的大小。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -du /user/hadoop/dir1 /user/hadoop/file1 hdfs://host:port/user/hadoop/dir1</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>9)<span>   </span></span>dus</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -dus &lt;args&gt;</span></span></p>
<p><span style="font-size:12px;">显示文件的大小。</span></p>
<p><span style="font-size:12px;"><span>10)<span>  </span></span>expunge</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -expunge</span></span></p>
<p><span style="font-size:12px;">清空回收站。请参考<span>HDFS</span>设计文档以获取更多关于回收站特性的信息。</span></p>
<p><span style="font-size:12px;"><span>11)<span>  </span></span>get</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -get [-ignorecrc] [-crc] &lt;src&gt; &lt;localdst&gt;</span></span></p>
<p><span style="font-size:12px;">复制文件到本地文件系统。可用<span>-ignorecrc</span>选项复制<span>CRC</span>校验失败的文件。使用<span>-crc</span>选项复制文件以及<span>CRC</span>信息。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -get /user/hadoop/file localfile</span></p>
<p><span style="font-size:12px;">hadoop fs -get hdfs://host:port/user/hadoop/file localfile</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>12)<span>  </span></span>getmerge</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -getmerge &lt;src&gt; &lt;localdst&gt; [addnl]</span></span></p>
<p><span style="font-size:12px;">接受一个源目录和一个目标文件作为输入，并且将源目录中所有的文件连接成本地目标文件。<span>addnl</span>是可选的，用于指定在每个文件结尾添加一个换行符。</span></p>
<p><span style="font-size:12px;"><span>13)<span>  </span></span>ls</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -ls &lt;args&gt;</span></span></p>
<p><span style="font-size:12px;">如果是文件，则按照如下格式返回文件信息：</span></p>
<p><span style="font-size:12px;">文件名<span> &lt;</span>副本数<span>&gt; </span>文件大小 修改日期 修改时间 权限 用户<span>ID</span>组<span>ID</span></span></p>
<p><span style="font-size:12px;">如果是目录，则返回它直接子文件的一个列表，就像在<span>Unix</span>中一样。目录返回列表的信息如下：</span></p>
<p><span style="font-size:12px;">目录名<span> &lt;dir&gt; </span>修改日期 修改时间 权限 用户<span>ID </span>
组<span>ID </span></span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -ls /user/hadoop/file1 /user/hadoop/file2 hdfs://host:port/user/hadoop/dir1 /nonexistentfile</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>14)<span>  </span></span>lsr</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -lsr &lt;args&gt; </span></span></p>
<p><span style="font-size:12px;">ls命令的递归版本。类似于<span>Unix</span>中的<span>ls -R</span>。</span></p>
<p><span style="font-size:12px;"><span>15)<span>  </span></span>mkdir</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -mkdir &lt;paths&gt; </span></span></p>
<p><span style="font-size:12px;">接受路径指定的<span>uri</span>作为参数，创建这些目录。其行为类似于<span>Unix</span>的<span>mkdir -p</span>，它会创建路径中的各级父目录。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -mkdir /user/hadoop/dir1 /user/hadoop/dir2</span></p>
<p><span style="font-size:12px;">hadoop fs -mkdir hdfs://host1:port1/user/hadoop/dir hdfs://host2:port2/user/hadoop/dir</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>16)<span>  </span></span>movefromLocal</span></p>
<p><span style="font-size:12px;">使用方法：<span>dfs -moveFromLocal &lt;src&gt; &lt;dst&gt;</span></span></p>
<p><span style="font-size:12px;">输出一个”<span>not implemented</span>“信息。</span></p>
<p><span style="font-size:12px;"><span>17)<span>  </span></span>mv</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -mv URI [URI </span>…<span>] &lt;dest&gt;</span></span></p>
<p><span style="font-size:12px;">将文件从源路径移动到目标路径。这个命令允许有多个源路径，此时目标路径必须是一个目录。不允许在不同的文件系统间移动文件。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -mv /user/hadoop/file1 /user/hadoop/file2</span></p>
<p><span style="font-size:12px;">hadoop fs -mv hdfs://host:port/file1 hdfs://host:port/file2 hdfs://host:port/file3 hdfs://host:port/dir1</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>18)<span>  </span></span>put</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -put &lt;localsrc&gt; ... &lt;dst&gt;</span></span></p>
<p><span style="font-size:12px;">从本地文件系统中复制单个或多个源路径到目标文件系统。也支持从标准输入中读取输入写入目标文件系统。</span></p>
<p><span style="font-size:12px;">hadoop fs -put localfile /user/hadoop/hadoopfile</span></p>
<p><span style="font-size:12px;">hadoop fs -put localfile1 localfile2 /user/hadoop/hadoopdir</span></p>
<p><span style="font-size:12px;">hadoop fs -put localfile hdfs://host:port/hadoop/hadoopfile</span></p>
<p><span style="font-size:12px;">hadoop fs -put - hdfs://host:port/hadoop/hadoopfile</span></p>
<p><span style="font-size:12px;">从标准输入中读取输入。</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>19)<span>  </span></span>rm</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -rm URI [URI </span>…<span>]</span></span></p>
<p><span style="font-size:12px;">删除指定的文件。只删除非空目录和文件。请参考<span>rmr</span>命令了解递归删除。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -rm hdfs://host:port/file /user/hadoop/emptydir</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>20)<span>  </span></span>rmr</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -rmr URI [URI </span>…<span>]</span></span></p>
<p><span style="font-size:12px;">delete的递归版本。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -rmr /user/hadoop/dir</span></p>
<p><span style="font-size:12px;">hadoop fs -rmr hdfs://host:port/user/hadoop/dir</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>21)<span>  </span></span>setrep</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -setrep [-R] &lt;path&gt;</span></span></p>
<p><span style="font-size:12px;">改变一个文件的副本系数。<span>-R</span>选项用于递归改变目录下所有文件的副本系数。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -setrep -w 3 -R /user/hadoop/dir1</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>22)<span>  </span></span>stat</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -stat URI [URI </span>…<span>]</span></span></p>
<p><span style="font-size:12px;">返回指定路径的统计信息。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -stat path</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>23)<span>  </span></span>tail</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -tail [-f] URI</span></span></p>
<p><span style="font-size:12px;">将文件尾部<span>1K</span>字节的内容输出到<span>stdout</span>。支持<span>-f</span>选项，行为和<span>Unix</span>中一致。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -tail pathname</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span>。</span></p>
<p><span style="font-size:12px;"><span>24)<span>  </span></span>test</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -test -[ezd] URI</span></span></p>
<p><span style="font-size:12px;">选项：</span></p>
<p><span style="font-size:12px;">-e 检查文件是否存在。如果存在则返回<span>0</span>。</span></p>
<p><span style="font-size:12px;">-z 检查文件是否是<span>0</span>字节。如果是则返回<span>0</span>。</span></p>
<p><span style="font-size:12px;">-d 如果路径是个目录，则返回<span>1</span>，否则返回<span>0</span>。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop fs -test -e filename</span></p>
<p><span style="font-size:12px;"><span>25)<span>  </span></span>text</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -text &lt;src&gt; </span></span></p>
<p><span style="font-size:12px;">将源文件输出为文本格式。允许的格式是<span>zip</span>和<span>TextRecordInputStream</span>。</span></p>
<p><span style="font-size:12px;"><span>26)<span>  </span></span>touchz</span></p>
<p><span style="font-size:12px;">使用方法：<span>hadoop fs -touchz URI [URI </span>…<span>]</span></span></p>
<p><span style="font-size:12px;">创建一个<span>0</span>字节的空文件。</span></p>
<p><span style="font-size:12px;">示例：</span></p>
<p><span style="font-size:12px;">hadoop -touchz pathname</span></p>
<p><span style="font-size:12px;">返回值：</span></p>
<p><span style="font-size:12px;">成功返回<span>0</span>，失败返回<span>-1</span></span></p>
<p><span><span style="font-size:12px;"><span style="font-size:15pt;"><span>2.<span>      </span></span></span><span style="font-size:15pt;">jar</span></span></span></p>
<p><span style="font-size:12px;">运行<span>jar</span>文件。用户可以把他们的<span>Map Reduce</span>代码捆绑到<span>jar</span>文件中，使用这个命令执行。</span></p>
<p><span style="font-size:12px;">用法：<span>hadoop jar &lt;jar&gt; [mainClass] args...</span></span></p>
<p><span style="font-size:12px;">例如我们提交<span>wordcount mapreduce</span>的时候：</span></p>
<p><span style="font-size:12px;">hadoop jar hadoop-examples.jar wordcount input output</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span><span><span style="font-size:12px;"><span style="font-size:15pt;"><span>3.<span>      </span></span></span><span style="font-size:15pt;">archive</span></span></span></span></p>
<p><span style="font-size:12px;">bin/hadoop archive</span></p>
<p><span style="font-size:12px;">创建一个<span>hadoop</span>档案文件。参考<span> Hadoop Archives.</span></span></p>
<p><span style="font-size:12px;">用法：<span>hadoop archive -archiveName NAME &lt;src&gt;* &lt;dest&gt;</span></span></p>
<p><span style="font-size:12px;">-archiveName NAME<span>   </span>要创建的档案的名字。</span></p>
<p><span style="font-size:12px;">src文件系统的路径名，和通常含正则表达的一样。</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span><span><span style="font-size:12px;"><span style="font-size:15pt;"><span>4.<span>      </span></span></span><span style="font-size:15pt;">distcp</span></span></span></span></p>
<p><span style="font-size:12px;">bin/hadoop distcp</span></p>
<p><span style="font-size:12px;">递归地拷贝文件或目录。参考<span>DistCp</span>指南以获取等多信息。</span></p>
<p><span style="font-size:12px;">用法：<span>hadoop distcp &lt;srcurl&gt; &lt;desturl&gt;</span></span></p>
<p><span style="font-size:12px;">srcurl<span>  </span>源<span>Url</span></span></p>
<p><span style="font-size:12px;">desturl目标<span>Url</span></span></p>
<p><span style="font-size:12px;">daemonlog</span></p>
<p><span style="font-size:12px;">获取或设置每个守护进程的日志级别。</span></p>
<p><span style="font-size:12px;">用法：<span>hadoop daemonlog -getlevel &lt;host:port&gt; &lt;name&gt;</span></span></p>
<p><span style="font-size:12px;">用法：<span>hadoop daemonlog -setlevel &lt;host:port&gt; &lt;name&gt; &lt;level&gt;</span></span></p>
<p><span style="font-size:12px;">-getlevel &lt;host:port&gt; &lt;name&gt;<span>    </span>打印运行在<span>&lt;host:port&gt;</span>的守护进程的日志级别。这个命令内部会连接<span>http://&lt;host:port&gt;/logLevel?log=&lt;name&gt;</span></span></p>
<p><span style="font-size:12px;">-setlevel &lt;host:port&gt; &lt;name&gt; &lt;level&gt;<span>    </span>
设置运行在<span>&lt;host:port&gt;</span>的守护进程的日志级别。这个命令内部会连接<span>http://&lt;host:port&gt;/logLevel?log=&lt;name&gt;</span></span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;"><span style="font-size:15pt;"><span>5.<span>      </span></span></span><span style="font-size:15pt;">bin/hdfs shell</span></span></p>
<p><span style="font-size:12px;">dfs<span>                  </span>run a filesystem command on the file systems supported in Hadoop.</span></p>
<p><span style="font-size:12px;">namenode -format<span>     </span>format the DFS filesystem</span></p>
<p><span style="font-size:12px;">secondarynamenode<span>    </span>run the DFS secondary namenode</span></p>
<p><span style="font-size:12px;">namenode<span>     </span><span>        </span>run the DFS namenode</span></p>
<p><span style="font-size:12px;">zkfc<span>                 </span>run the ZK Failover Controller daemon</span></p>
<p><span style="font-size:12px;">datanode<span>             </span>run a DFS datanode</span></p>
<p><span style="font-size:12px;">dfsadmin<span>             </span>run a DFS admin client</span></p>
<p><span style="font-size:12px;">haadmin<span>              </span>run a DFS HA admin client</span></p>
<p><span style="font-size:12px;">fsck<span>                 </span>run a DFS filesystem checking utility</span></p>
<p><span style="font-size:12px;"><span> </span>balancer<span>             </span>run a cluster balancing utility</span></p>
<p><span style="font-size:12px;">jmxget<span>            </span>get JMX exported values from NameNode or DataNode.</span></p>
<p><span style="font-size:12px;">oiv<span>                  </span>apply the offline fsimage viewer to an fsimage</span></p>
<p><span style="font-size:12px;">oev<span>                  </span>apply the offline edits viewer to an edits file</span></p>
<p><span style="font-size:12px;">fetchdt<span>              </span>fetch a delegation token from the NameNode</span></p>
<p><span style="font-size:12px;">getconf<span>              </span>get config values from configuration</span></p>
<p><span style="font-size:12px;">groups<span>               </span>get the groups which users belong to</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;"><span>1)<span>   </span></span>balancer</span></p>
<p><span style="font-size:12px;">运行集群平衡工具。管理员可以简单的按<span>Ctrl-C</span>来停止平衡过程。参考<span>Rebalancer</span>了解更多。</span></p>
<p><span style="font-size:12px;">用法：<span>hadoop balancer [-threshold &lt;threshold&gt;]</span></span></p>
<p><span style="font-size:12px;">-threshold &lt;threshold&gt;<span>  </span>磁盘容量的百分比。这会覆盖缺省的阀值。</span></p>
<p><span style="font-size:12px;"><span>2)<span>   </span></span>datanode</span></p>
<p><span style="font-size:12px;">运行一个<span>HDFS</span>的<span>datanode</span>。</span></p>
<p><span style="font-size:12px;">用法：<span>hadoop datanode [-rollback]</span></span></p>
<p><span style="font-size:12px;">-rollback<span>   </span>将<span>datanode</span>回滚到前一个版本。这需要在停止<span>datanode</span>，分发老的<span>hadoop</span>版本之后使用。</span></p>
<p><span style="font-size:12px;"><span>3)<span>   </span></span>dfsadmin</span></p>
<p><span style="font-size:12px;">bin/hdfs dfsadmin</span></p>
<p><span style="font-size:12px;">Usage: java DFSAdmin</span></p>
<p><span style="font-size:12px;">Note: Administrative commands can only be run as the HDFS superuser.</span></p>
<p><span style="font-size:12px;"><span>           </span>[-report]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-safemode enter | leave | get | wait]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-saveNamespace]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-restoreFailedStorage true|false|check]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-refreshNodes]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-finalizeUpgrade]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-upgradeProgress status | details | force]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-metasave filename]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-refreshServiceAcl]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-refreshUserToGroupsMappings]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-refreshSuperUserGroupsConfiguration]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-printTopology]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-refreshNamenodes datanodehost:port]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-deleteBlockPool datanode-host:port blockpoolId [force]]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-setQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-clrQuota &lt;dirname&gt;...&lt;dirname&gt;]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-setSpaceQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-clrSpaceQuota &lt;dirname&gt;...&lt;dirname&gt;]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-setBalancerBandwidth &lt;bandwidth in bytes per second&gt;]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-fetchImage &lt;local directory&gt;]</span></p>
<p><span style="font-size:12px;"><span>           </span>[-help [cmd]]</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;">参数说明：</span></p>
<p><span style="font-size:12px;">-report报告文件系统的基本信息和统计信息。</span></p>
<p><span style="font-size:12px;">-safemode enter | leave | get | wait<span>    </span>
安全模式维护命令。安全模式是<span>Namenode</span>的一个状态，这种状态下，<span>Namenode</span></span></p>
<p><span style="font-size:12px;">1. 不接受对名字空间的更改<span>(</span>只读<span>)</span></span></p>
<p><span style="font-size:12px;">2. 不复制或删除块</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;">Namenode会在启动时自动进入安全模式，当配置的块最小百分比数满足最小的副本数条件时，会自动离开安全模式。安全模式可以手动进入，但是这样的话也必须手动关闭安全模式。</span></p>
<p><span style="font-size:12px;">-refreshNodes<span>   </span>重新读取<span>hosts</span>和<span>exclude</span>文件，更新允许连到<span>Namenode</span>的或那些需要退出或入编的<span>Datanode</span>的集合。</span></p>
<p><span style="font-size:12px;">-finalizeUpgrade<span>    </span>终结<span>HDFS</span>的升级操作。<span>Datanode</span>删除前一个版本的工作目录，之后<span>Namenode</span>也这样做。这个操作完结整个升级过程。</span></p>
<p><span style="font-size:12px;">-upgradeProgress status | details | force<span>  </span>请求当前系统的升级状态，状态的细节，或者强制升级操作进行。</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;">-metasave filename<span>  </span>保存<span>Namenode</span>的主要数据结构到<span>hadoop.log.dir</span>属性指定的目录下的<span>&lt;filename&gt;</span>文件。对于下面的每一项，<span>&lt;filename&gt;</span>中都会一行内容与之对应</span></p>
<p><span style="font-size:12px;">1. Namenode收到的<span>Datanode</span>的心跳信号</span></p>
<p><span style="font-size:12px;">2. 等待被复制的块</span></p>
<p><span style="font-size:12px;">3. 正在被复制的块</span></p>
<p><span style="font-size:12px;">4. 等待被删除的块</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;">-setQuota &lt;quota&gt; &lt;dirname&gt;...&lt;dirname&gt;为每个目录<span> &lt;dirname&gt;</span>设定配额<span>&lt;quota&gt;</span>。目录配额是一个长整型整数，强制限定了目录树下的名字个数。</span></p>
<p><span style="font-size:12px;">命令会在这个目录上工作良好，以下情况会报错：</span></p>
<p><span style="font-size:12px;">1. N不是一个正整数，或者</span></p>
<p><span style="font-size:12px;">2. 用户不是管理员，或者</span></p>
<p><span style="font-size:12px;">3. 这个目录不存在或是文件，或者</span></p>
<p><span style="font-size:12px;">4. 目录会马上超出新设定的配额。</span></p>
<p><span style="font-size:12px;"> </span></p>
<p><span style="font-size:12px;">-clrQuota &lt;dirname&gt;...&lt;dirname&gt;为每一个目录<span>&lt;dirname&gt;</span>清除配额设定。</span></p>
<p><span style="font-size:12px;">命令会在这个目录上工作良好，以下情况会报错：</span></p>
<p><span style="font-size:12px;">1. 这个目录不存在或是文件，或者</span></p>
<p><span style="font-size:12px;">2. 用户不是管理员。</span></p>
<p><span style="font-size:12px;">如果目录原来没有配额不会报错。</span></p>
<p><span style="font-size:12px;">-help [cmd]显示给定命令的帮助信息，如果没有给定命令，则显示所有命令的帮助信息。</span></p>
<p><span style="font-size:12px;"><span>4)<span>   </span></span>namenode</span></p>
<p><span style="font-size:12px;">运行<span>namenode</span>。有关升级，回滚，升级终结的更多信息请参考升级和回滚。</span></p>
<p><span style="font-size:12px;">用法：<span>namenode [-backup] | [-checkpoint] | [-format [-clusterid cid ] [-force] [-nonInteractive] ] | [-upgrade] | [-rollback] | [-finalize] | [-importCheckpoint] | [-initializeSharedEdits] | [-bootstrapStandby] | [-recover
 [ -force ] ]</span></span></p>
<p><span style="font-size:12px;">-format格式化<span>namenode</span>。它启动<span>namenode</span>，格式化<span>namenode</span>，之后关闭<span>namenode</span>。</span></p>
<p><span style="font-size:12px;">-upgrade<span>    </span>分发新版本的<span>hadoop</span>后，<span>namenode</span>应以<span>upgrade</span>选项启动。</span></p>
<p><span style="font-size:12px;">-rollback<span>   </span>将<span>namenode</span>回滚到前一版本。这个选项要在停止集群，分发老的<span>hadoop</span>版本后使用。</span></p>
<p><span style="font-size:12px;">-finalize<span>   </span>finalize会删除文件系统的前一状态。最近的升级会被持久化，<span>rollback</span>选项将再不可用，升级终结操作之后，它会停掉<span>namenode</span>。</span></p>
<p><span style="font-size:12px;">-importCheckpoint<span>   </span>从检查点目录装载镜像并保存到当前检查点目录，检查点目录由<span>fs.checkpoint.dir</span>指定。</span></p>
<p><span style="font-size:12px;"> </span></p>
            </div>
                </div>
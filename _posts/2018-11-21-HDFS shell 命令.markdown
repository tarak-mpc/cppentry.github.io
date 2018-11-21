---
layout:     post
title:      HDFS shell 命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
HDFS FileSystem Shell<br>
All FS shell commands take path URIs as arguments. The URI format is scheme://authority/path. For HDFS the scheme is hdfs, and for the Local FS the scheme is file. The scheme and authority are optional. <br><br><br>
appendToFile<br>
 hdfs dfs -appendToFile &lt;localsrc&gt; ... &lt;dst&gt;<br>
 hdfs dfs -appendToFile localfile hdfs://nn.example.com/hadoop/hadoopfile<br>
 Returns 0 on success and 1 on error.<br><br><br>
cat:Copies source paths to stdout.<br>
 hdfs dfs -cat URI [URI ...]<br>
 hdfs dfs -cat hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2<br>
 hdfs dfs -cat file:///file3 /user/hadoop/file4<br>
 Returns 0 on success and -1 on error.<br><br><br>
chgrp:Change group association of files,改变文件所属的组,改变文件组权 change group<br>
 hdfs dfs -chgrp [-R] GROUP URI [URI ...]<br>
 The -R option will make the change recursively through the directory structure.<br>
chmod:Change the permissions of files--change mode<br>
 hdfs dfs -chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; URI [URI ...]<br>
chown:Change the owner of files<br>
 hdfs dfs -chown [-R] [OWNER][:[GROUP]] URI [URI ]<br><br><br>
copyFromLocal:Similar to put command, except that the source is restricted to a local file reference.<br>
 hdfs dfs -copyFromLocal &lt;localsrc&gt; URI<br>
 The -f option will overwrite the destination if it already exists.<br>
copyToLocal:Similar to get command, except that the destination is restricted to a local file reference.<br>
 hdfs dfs -copyToLocal [-ignorecrc] [-crc] URI &lt;localdst&gt;<br><br><br>
count:Count the number of directories, files and bytes under the paths that match the specified file pattern<br>
The output columns with -count are: DIR_COUNT, FILE_COUNT, CONTENT_SIZE FILE_NAME<br>
The output columns with -count -q are: QUOTA, REMAINING_QUATA, SPACE_QUOTA, REMAINING_SPACE_QUOTA, DIR_COUNT, FILE_COUNT, CONTENT_SIZE, FILE_NAMEquota:配额；定额；限额 <br>
The -h option shows sizes in human readable format. h-humanity<br>
 hdfs dfs -count [-q] [-h] &lt;paths&gt;<br>
 hdfs dfs -count hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2<br>
 hdfs dfs -count -q hdfs://nn1.example.com/file1<br>
 hdfs dfs -count -q -h hdfs://nn1.example.com/file1<br>
 Returns 0 on success and -1 on error.<br>
cp:Copy files from source to destination. This command allows multiple sources as well in which case the destination must be a directory<br>
 hdfs dfs -cp [-f] [-p | -p[topax]] URI [URI ...] &lt;dest&gt;topax:timestamps, ownership, permission, ACL, XAttr ACL容许机舱负荷（Allowance Cabin Load）<br>
 hdfs dfs -cp /user/hadoop/file1 /user/hadoop/file2<br>
 hdfs dfs -cp /user/hadoop/file1 /user/hadoop/file2 /user/hadoop/dir<br><br><br>
du:Displays sizes of files and directories contained in the given directory or the length of a file in case its just a file.<br>
 hdfs dfs -du [-s] [-h] URI [URI ...]<br>
 The -s option will result in an aggregate summary of file lengths being displayed, rather than the individual files.<br>
 The -h option will format file sizes in a "human-readable" fashion (e.g 64.0m instead of 67108864)<br>
dus:This command is deprecated. Instead use hdfs dfs -du -s.<br><br><br>
expunge:Empty the Trash(垃圾).<br><br><br>
get：Copy files to the local file system. Files that fail the CRC check may be copied with the -ignorecrc option. Files and CRCs may be copied using the -crc option.crc循环冗余码校验<br>
 hdfs dfs -get [-ignorecrc] [-crc] &lt;src&gt; &lt;localdst&gt;<br>
 hdfs dfs -get /user/hadoop/file localfile<br>
 hdfs dfs -get hdfs://nn.example.com/user/hadoop/file localfile<br>
getfacl:Displays the Access Control Lists (ACLs:访问控制列表) of files and directories. If a directory has a default ACL, then getfacl also displays the default ACL. <br>
 hdfs dfs -getfacl [-R] &lt;path&gt;<br>
 hdfs dfs -getfacl /file<br>
 hdfs dfs -getfacl -R /dir<br><br><br>
getfattr:Displays the extended attribute names and values (if any) for a file or directory<br>
 hdfs dfs -getfattr [-R] -n name | -d [-e en] &lt;path&gt;<br>
 hdfs dfs -getfattr -d /file<br>
 hdfs dfs -getfattr -R -n user.myAttr /dir<br>
getmerge:Takes a source directory and a destination file as input and concatenates files in src into the destination local file. <br>
  hdfs dfs -getmerge &lt;src&gt; &lt;localdst&gt; [addnl]<br>
ls:<br>
 hdfs dfs -ls [-R] &lt;args&gt;<br>
 hdfs dfs -ls /user/hadoop/file1<br>
lsr:This command is deprecated. Instead use hdfs dfs -ls -R<br><br><br>
mkdir:Takes path uri's as argument and creates directories.<br>
moveFromLocal:Similar to put command, except that the source localsrc is deleted after it's copied.<br>
 hdfs dfs -moveFromLocal &lt;localsrc&gt; &lt;dst&gt;<br>
moveToLocal:Displays a "Not implemented yet" message.<br>
 hdfs dfs -moveToLocal [-crc] &lt;src&gt; &lt;dst&gt;<br>
mv:Moves files from source to destination.<br>
 hdfs dfs -mv /user/hadoop/file1 /user/hadoop/file2<br>
put:Copy single src, or multiple srcs from local file system to the destination file system. Also reads input from stdin and writes to destination file system.<br><br><br>
rm:Delete files specified as args.<br>
 hdfs dfs -rm [-f] [-r|-R] [-skipTrash] URI [URI ...]<br>
 hdfs dfs -rm hdfs://nn.example.com/file /user/hadoop/emptydir
            </div>
                </div>
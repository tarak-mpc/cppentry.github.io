---
layout:     post
title:      hadoop之 HDFS fs 命令总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，可以转载，但必须以链接形式声明出处。					https://blog.csdn.net/zhang123456456/article/details/77625928				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
版本：Hadoop 2.7.4</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-- 查看hadoop fs帮助信息<br>
[root@hadp-master sbin]# hadoop fs<br>
Usage: hadoop fs [generic options]<br>
[-appendToFile &lt;localsrc&gt; ... &lt;dst&gt;]<br>
[-cat [-ignoreCrc] &lt;src&gt; ...]<br>
[-checksum &lt;src&gt; ...]<br>
[-chgrp [-R] GROUP PATH...]<br>
[-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]<br>
[-chown [-R] [OWNER][:[GROUP]] PATH...]<br>
[-copyFromLocal [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]<br>
[-copyToLocal [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]<br>
[-count [-q] [-h] &lt;path&gt; ...]<br>
[-cp [-f] [-p | -p[topax]] &lt;src&gt; ... &lt;dst&gt;]<br>
[-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]<br>
[-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]<br>
[-df [-h] [&lt;path&gt; ...]]<br>
[-du [-s] [-h] &lt;path&gt; ...]<br>
[-expunge]<br>
[-find &lt;path&gt; ... &lt;expression&gt; ...]<br>
[-get [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]<br>
[-getfacl [-R] &lt;path&gt;]<br>
[-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]<br>
[-getmerge [-nl] &lt;src&gt; &lt;localdst&gt;]<br>
[-help [cmd ...]]<br>
[-ls [-d] [-h] [-R] [&lt;path&gt; ...]]<br>
[-mkdir [-p] &lt;path&gt; ...]<br>
[-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]<br>
[-moveToLocal &lt;src&gt; &lt;localdst&gt;]<br>
[-mv &lt;src&gt; ... &lt;dst&gt;]<br>
[-put [-f] [-p] [-l] &lt;localsrc&gt; ... &lt;dst&gt;]<br>
[-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]<br>
[-rm [-f] [-r|-R] [-skipTrash] &lt;src&gt; ...]<br>
[-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; ...]<br>
[-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]<br>
[-setfattr {-n name [-v value] | -x name} &lt;path&gt;]<br>
[-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; ...]<br>
[-stat [format] &lt;path&gt; ...]<br>
[-tail [-f] &lt;file&gt;]<br>
[-test -[defsz] &lt;path&gt;]<br>
[-text [-ignoreCrc] &lt;src&gt; ...]<br>
[-touchz &lt;path&gt; ...]<br>
[-truncate [-w] &lt;length&gt; &lt;path&gt; ...]<br>
[-usage [cmd ...]]</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
 </p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
注意：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
以下指令均是在，Linux 命令行窗口界面操作。<br>
[ ]表示可选参数，&lt;&gt;表示必须参数。<br>
开始使用命令前，必须启动Hadoop<br>
(1)-appendToFile</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法： hadoop fs -appendToFile &lt;localsrc&gt; ... &lt;dst&gt;</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：是将一个或者多个文件添加到HDFS系统中。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例:</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -appendToFile localfile /user/hadoop/hadoopfile<br>
hadoop fs -appendToFile localfile1 localfile2 /user/hadoop/hadoopfile<br>
hadoop fs -appendToFile localfile hdfs://nn.example.com/hadoop/hadoopfile<br>
hadoop fs -appendToFile - hdfs://nn.example.com/hadoop/hadoopfile Reads the input from stdin.<br>
　　<br>
(2)-cat</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法：hadoop fs -cat URI [URI ...]</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：查看文件内容（可以查看本地和HDFS上的内容）。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -cat hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2<br>
hadoop fs -cat file:///file3 /user/hadoop/file4　　</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(3)-checksum</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法： hadoop fs -checksum URI</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：查看校验码信息。（例子显示了MD5）</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -checksum hdfs://nn1.example.com/file1<br>
hadoop fs -checksum file:///etc/hosts<br>
　　<br>
(4)-chgrp</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法： hadoop fs -chgrp [-R] GROUP URI [URI ...]</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：改变文件所属的组。（Change group association of files.）</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
使用-R 将使改变在目录结构下递归进行。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(5)-chmod</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：改变文件访问权限。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法：hadoop fs -chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; URI [URI ...]</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
这里可以参考 Linux下文件系统的chmod的用法，基本类似。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(6)-chown</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI ]</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法:改变文件的所有者。使用-R 将使改变在目录结构下递归进行。命令的使用者必须是超级用户。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(7)-copyFromLocal</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法：hadoop fs -copyFromLocal &lt;localsrc&gt; URI</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：类似于put命令，和put不同的是，拷贝的源地址必须是本地文件地址。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-f 参数 当拷贝的目标文件存在时，进行覆盖。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
[root@two1 fanrui]# hadoop fs -copyFromLocal testFlatMap.txt /1.txt<br>
copyFromLocal: `/1.txt': File exists<br>
　　<br>
这个时候加上-f参数。即可覆盖。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
[root@two1 fanrui]# hadoop fs -copyFromLocal -f testFlatMap.txt /1.txt<br><br>
(8)-copyToLocal</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法： hadoop fs -copyToLocal [-ignorecrc] [-crc] URI &lt;localdst&gt;</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：类似于get指令。和get不同的是，拷贝的目的地址必须是本地文件地址。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(9)-count</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：计算paths下的目录数，文件数和字节数。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法： hadoop fs -count [-q] [-h] [-v] &lt;paths&gt;</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -count hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2<br>
hadoop fs -count -q hdfs://nn1.example.com/file1<br>
hadoop fs -count -q -h hdfs://nn1.example.com/file1<br>
hdfs dfs -count -q -h -v hdfs://nn1.example.com/file1<br>
　　<br>
(10)-cp<br>
用法：hadoop fs -cp [-f] [-p | -p[topax]] URI [URI ...] &lt;dest&gt;<br>
作用：拷贝，HDFS文件系统中进行的拷贝操作。<br>
-f 参数选项：当文件存在时，进行覆盖。<br>
-p 参数选项：将权限、所属组、时间戳、ACL以及XAttr等也进行拷贝。下面是官网的描述。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
The -p option will preserve file attributes [topx] (timestamps, ownership, permission, ACL, XAttr). If -p is specified with no arg, then preserves timestamps, ownership, permission. If -pa is specified, then preserves permission also because ACL is a super-set
 of permission. Determination of whether raw namespace extended attributes are preserved is independent of the -p flag.</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
[root@two1 fanrui]# hadoop fs -cp -p /tmp/fan /tmp/fan1<br>
　　<br>
(11)-df<br>
用法：hadoop fs -df [-h] URI [URI ...]<br>
作用：显示剩余空间。<br>
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
[root@two1 fanrui]# hadoop fs -df /<br>
Filesystem Size Used Available Use%<br>
hdfs://localhost:9000 37626667008 311296 24792702976 0%<br><br>
（12）-dus<br>
作用：显示文件长度概要。该方法已经被舍去，等价于 -du -s 方法。见（11）</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
（13）-expunge<br>
作用：从垃圾桶目录永久删除超过保留阈值的检查点中的文件，并创建新检查点。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法：hadoop fs -expunge</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
（14）-find<br>
作用：查找满足表达式的文件和文件夹。没有配置path的话，默认的就是全部目录/；如果表达式没有配置，则默认为-print。<br>
用法: hadoop fs -find &lt;path&gt; ... &lt;expression&gt; ...<br>
-name pattern 所要查找文件的文件名。<br>
-iname pattern 所要查找的文件名，不区分大小写。<br>
-print 打印。<br>
-print0 打印在一行，如下图所示。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -find / -name test -print<br>
　　<br>
（15）-get<br>
作用：从HDFS上拷贝文件到本地。<br>
用法：hadoop fs -get [-ignorecrc] [-crc] &lt;src&gt; &lt;localdst&gt;<br>
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -get /user/hadoop/file localfile<br>
hadoop fs -get hdfs://nn.example.com/user/hadoop/file localfile</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
（16）getfacl</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：显示文件和文件夹的ACLs（Access Control Lists）。如果目录有默认的ACL，则显示之。<br>
-R参数：递归显示。<br>
用法：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -getfacl [-R] &lt;path&gt;</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
Options:</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-R: List the ACLs of all files and directories recursively.<br>
path: File or directory to list.<br>
示例:</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -getfacl /file<br>
hadoop fs -getfacl -R /dir<br>
　　<br>
Exit Code:</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
Returns 0 on success and non-zero on error.</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
（17）getfattr</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：显示文件或目录的扩展属性名和值（如果有的话）</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法：hadoop fs -getfattr [-R] -n name | -d [-e en] &lt;path&gt;</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
Options:</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-R：递归显示文件夹和文件。<br>
-n name：转储命名的扩展属性值。<br>
-d：转储与路径名相关联的所有扩展属性值。<br>
-e en: 检索后的值进行编码。 有效的编码是 “text”, “hex”, and “base64”. 值编码作为文本字符串是用双引号括起来的（“），值编码作为16进制和64进制，前缀分别为 0x 和 0s。<br>
path：文件或文件夹路径。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -getfattr -d /file<br>
hadoop fs -getfattr -R -n user.myAttr /dir<br>
　　<br>
(18)-getmerge<br>
作用：是将HDFS上一个目录中所有的文件合并到一起输出到一个本地文件上。<br>
用法：hadoop fs -getmerge [-nl] &lt;src&gt; &lt;localdst&gt;</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -getmerge -nl /src /opt/output.txt<br>
hadoop fs -getmerge -nl /src/file1.txt /src/file2.txt /output.txt<br>
　　<br>
(19)-help<br>
作用：帮助文档</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法：hadoop fs -help</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(20)-ls<br>
作用：查看文件，与linux下ls命令基本类似。<br>
用法:hadoop fs -ls [-d] [-h] [-R] &lt;args&gt;<br>
选项：<br>
-d:只展示查询展示目录；</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-h:显示为人眼更易识别的单位（原来是字节）。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-R:递归展示，显示所有的文件夹及文件</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -ls -d /<br>
hadoop fs -ls -h /<br>
hadoop fs -ls -R /<br>
　　<br>
-lsr<br>
作用：已经被舍去，效果等同于-ls -R</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(21)-mkdir<br>
作用：创建文件夹。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法：hadoop fs -mkdir [-p] &lt;paths&gt;<br>
选项：<br>
-p:创建父目录。类似于Unix的mkdir -p命令。<br>
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -mkdir /user/hadoop/dir1 /user/hadoop/dir2<br>
hadoop fs -mkdir hdfs://nn1.example.com/user/hadoop/dir hdfs://nn2.example.com/user/hadoop/dir<br>
　　<br>
（22）-moveFromLocal<br>
用法：hadoop fs -moveFromLocal &lt;localsrc&gt; &lt;dst&gt;<br>
作用：类似于put命令，不同put命令的是，该操作是移动（意思就是localsrc将被删除）。localsrc应是本地文件。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
（23）-moveToLocal<br>
用法：hadoop fs -moveToLocal [-crc] &lt;src&gt; &lt;dst&gt;<br>
作用：该命令尚未实现，显示“Not implemented yet”。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
（24）-mv <br>
用法：移动文件。<br>
作用： hadoop fs -mv URI [URI ...] &lt;dest&gt;<br>
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -mv /user/hadoop/file1 /user/hadoop/file2<br>
hadoop fs -mv hdfs://nn.example.com/file1 hdfs://nn.example.com/file2 hdfs://nn.example.com/file3 hdfs://nn.example.com/dir1<br><br>
（25）-put<br>
用法： hadoop fs -put &lt;localsrc&gt; ... &lt;dst&gt;<br>
作用：将本地的文件上传（复制）到HDFS是dst目录下。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -put localfile /user/hadoop/hadoopfile<br>
hadoop fs -put localfile1 localfile2 /user/hadoop/hadoopdir<br>
hadoop fs -put localfile hdfs://nn.example.com/hadoop/hadoopfile<br>
hadoop fs -put - hdfs://nn.example.com/hadoop/hadoopfile Reads the input from stdin.<br>
　　<br>
（26）-rm<br>
用法：hadoop fs -rm [-f] [-r |-R] [-skipTrash] URI [URI ...]<br>
作用：删除文件。<br>
选项：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
The -f option will not display a diagnostic message or modify the exit status to reflect an error if the file does not exist.<br>
The -R option deletes the directory and any content under it recursively.<br>
The -r option is equivalent to -R.<br>
The -skipTrash option will bypass trash, if enabled, and delete the specified file(s) immediately. This can be useful when it is necessary to delete files from an over-quota directory.</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -rm hdfs://nn.example.com/file /user/hadoop/emptydir</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(27)-rmdir<br>
用法：hadoop fs -rmdir [--ignore-fail-on-non-empty] URI [URI ...]<br>
作用：删除空目录。<br>
选项：<br>
—ignore-fail-on-non-empty：使用它的时候，忽略因文件夹非空删除失败的信息。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(28)-rmr<br>
作用：该方法已经被舍去。和-rm -r效果一样。递归删除。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
（29）-setfacl<br>
用法：hadoop fs -setfacl [-R] [-b |-k -m |-x &lt;acl_spec&gt; &lt;path&gt;] |[--set &lt;acl_spec&gt; &lt;path&gt;]<br>
作用：设置访问控制列表（ACL）的文件和目录。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
选项：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-b：移除所有除了基本的ACL条目。用户、组和其他的条目被保留为与权限位的兼容性。<br>
-k：删除默认的ACL。<br>
-R： 递归应用于所有文件和目录的操作。<br>
-m：修改ACL。新的项目添加到ACL，并保留现有的条目。<br>
-x：删除指定的ACL条目。其他保留ACL条目。<br>
–set：完全替换ACL，丢弃所有现有的条目。acl_spec必须包括用户，组，和其他有权限位的兼容性。<br>
acl_spec：逗号分隔的ACL条目列表。<br>
path：修改文件或目录。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -setfacl -m user:hadoop:rw- /file<br>
hadoop fs -setfacl -x user:hadoop /file<br>
hadoop fs -setfacl -b /file<br>
hadoop fs -setfacl -k /dir<br>
hadoop fs -setfacl --set user::rw-,user:hadoop:rw-,group::r--,other::r-- /file<br>
hadoop fs -setfacl -R -m user:hadoop:r-x /dir<br>
hadoop fs -setfacl -m default:user:hadoop:r-x /dir<br>
　　<br>
(30)-setrep<br>
用法：hadoop fs -setrep [-R] [-w] &lt;numReplicas&gt; &lt;path&gt;<br>
作用：改变文件的目标副本系数，放入REP中。选项-R将递归的改变PATH指定的目录中所有文件的目标副本系数。副本系数需要一定的时间才能达到目标值。选项-w将等待副本系数以与目标值相匹配。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -setrep -w 3 /user/hadoop/dir1<br>
　　<br>
(31)-stat<br>
用法： hadoop fs -stat [format] &lt;path&gt; ...<br>
作用：根据一定格式打印文件/文件夹的统计信息。 文件大小 (%b), 类型 (%F), 所有者所在组 (%g), 名字 (%n), 块大小 (%o), 副本 (%r), 用户名(%u), 修改时间 (%y, %Y)。默认的是%y。<br>
示例：<br>
hadoop fs -stat "%F %u:%g %b %y %n" /file</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(32)-tail<br>
用法：hadoop fs -tail [-f] URI<br>
作用:输出文件最后1kb的内容。<br>
选项：<br>
-f:和unix中tail -f命令类似，当文件内容更新时，输出将会改变，具有实时性。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：用一个场景测试下。首先HDFS的/目录下有文件mpwtest1.txt<br>
命令：hadoop fs -tail -f /mpwtest1.txt<br>
开启另外一个终端。输入命令： hadoop fs -appendToFile mpwtest2.txt /mpwtest1.txt<br>
可以发现 窗口1 有变化。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(33)-test</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：判断文件信息</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
用法：hadoop fs -test -[defsz] URI</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
选项：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-d:如果路径是一个目录，返回0<br>
-e:如果路径已经存在，返回0<br>
-f: 如果路径是一个文件，返回0<br>
-s:如果路径不是空，返回0<br>
-z:如果文件长度为0，返回0<br>
URI：资源地址，可以是文件也可以是目录。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -test -e filename</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(34)-text<br>
用法：hadoop fs -text &lt;src&gt;<br>
作用：将HDFS中文件以文本形式输出（包括zip包，jar包等形式）<br>
示例：hadoop fs -text /wc.jar</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
（35）-touchz<br>
用法： hadoop fs -touchz URI [URI ...]<br>
作用：创建一个空文件。<br>
示例：hadoop fs -touchz /hello.jar</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(35)-truncate<br>
用法: hadoop fs -truncate [-w] &lt;length&gt; &lt;paths&gt;</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
作用：截断指定长度匹配的所有文件内容。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
选项：</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
-w：需要等待命令完成块恢复。如果没有-w选项，在恢复的过程中可能是未闭合的。<br>
length：截断处的值，如果是100，则表示在100B处截断。<br>
paths：文件地址。</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
示例:</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
hadoop fs -truncate 55 /user/hadoop/file1 /user/hadoop/file2<br>
hadoop fs -truncate -w 127 hdfs://nn1.example.com/user/hadoop/file1</p>
<p style="font-family:'PingFang SC', 'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:14px;line-height:21px;">
(36)-usage<br>
用法: hadoop fs -usage command<br>
作用：返回命令的help信息。</p>
<br>            </div>
                </div>
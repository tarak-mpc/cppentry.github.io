---
layout:     post
title:      hadoop shell命令介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>Hadoop 文件系统shell使用命令介绍，基本上所有的命令与linux命令相差不大，下面为我的hadoop系统文件夹 <br>
查看命令：hadoop fs -ls /home/hadoop/ <br>
drwxr-xr-x   - hadoop supergroup          0 2013-11-30 17:51 /home/hadoop/dir <br>
drwxr-xr-x   - hadoop supergroup          0 2013-11-30 17:48 /home/hadoop/input <br>
-rw-r–r–   1 hadoop supergroup         64 2013-11-30 17:50 /home/hadoop/ouput <br>
drwxr-xr-x   - hadoop supergroup          0 2013-11-29 22:50 /home/hadoop/output <br>
drwxr-xr-x   - hadoop supergroup          0 2013-11-29 22:50 /home/hadoop/tmp</p>

<p>查看所有命令帮助：hadoop fs –help</p>

<p><strong>cat</strong> <br>
Usage: hadoop fs -cat URI [URI …] <br>
查看fs文件内容 <br>
例子: <br>
hadoop fs -cat /home/hadoop/input/content.txt <br>
Returns 0 on success and -1 on error.</p>

<p><strong>chgrp</strong> <br>
Usage: hadoop fs -chgrp [-R] GROUP URI [URI …] <br>
Change group association of files. With -R, make the change recursively through the directory structure. The user must be the owner of files, or else a super-user. Additional information is in the Permissions User Guide.</p>

<p><strong>chmod</strong> <br>
Usage: hadoop fs -chmod [-R] &lt;<strong>MODE[,MODE]… | OCTALMODE</strong>&gt; URI [URI …] <br>
Change the permissions of files. With -R, make the change recursively through the directory structure. The user must be the owner of the file, or else a super-user. Additional information is in the Permissions User Guide.</p>

<p><strong>chown</strong> <br>
Usage: hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI ] <br>
Change the owner of files. With -R, make the change recursively through the directory structure. The user must be a super-user. Additional information is in the Permissions User Guide.</p>

<p><strong>copyFromLocal</strong> <br>
Usage: hadoop fs -copyFromLocal  URI <br>
例子： <br>
hadoop fs -copyFromLocal /home/hadoop/address /home/hadoop/input <br>
Similar to put command, except that the source is restricted to a local file reference.</p>

<p><strong>copyToLocal</strong> <br>
Usage: hadoop fs -copyToLocal [-ignorecrc] [-crc] URI  <br>
例子： <br>
hadoop fs -copyToLocal /home/hadoop/input/content.txt /home/hadoop/mylocal <br>
Similar to get command, except that the destination is restricted to a local file reference.</p>

<p><strong>cp</strong> <br>
Usage: hadoop fs -cp URI [URI …]  <br>
Copy files from source to destination. This command allows multiple sources as well in which case the destination must be a directory.  <br>
例子: <br>
hadoop fs -cp /home/hadoop/input/content.txt /home/hadoop/ouput <br>
hadoop fs -cp /home/hadoop/input/address /home/hadoop/ouput /home/hadoop/dir <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>du</strong> <br>
Usage: hadoop fs -du URI [URI …] <br>
Displays aggregate length of files contained in the directory or the length of a file in case its just a file. <br>
例子: <br>
hadoop fs -du /home/hadoop/dir /home/hadoop/input <br>
输出结果： <br>
Found 2 items <br>
65          hdfs://localhost:9000/home/hadoop/dir/address <br>
64          hdfs://localhost:9000/home/hadoop/dir/ouput <br>
Found 2 items <br>
65          hdfs://localhost:9000/home/hadoop/input/address <br>
64          hdfs://localhost:9000/home/hadoop/input/content.txt</p>

<p>Exit Code: <br>
Returns 0 on success and -1 on error. </p>

<p><strong>dus</strong> <br>
Usage: hadoop fs -dus  <br>
例子： <br>
hadoop fs -dus /home/hadoop/dir</p>

<p>输出：hdfs://localhost:9000/home/hadoop/dir        129 <br>
Displays a summary of file lengths.</p>

<p><strong>expunge 手动清除fs回收站</strong> <br>
Usage: hadoop fs -expunge <br>
Empty the Trash. Refer to HDFS Design for more information on Trash feature.</p>

<p><strong>get</strong> <br>
Usage: hadoop fs -get [-ignorecrc] [-crc]    <br>
Copy files to the local file system. Files that fail the CRC check may be copied with the -ignorecrc option. Files and CRCs may be copied using the -crc option. <br>
例子: <br>
hadoop fs -get /home/hadoop/input/content.txt /home/hadoop/mylocal/ <br>
hadoop fs -get hdfs://nn.example.com/user/hadoop/file localfile <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>getmerge</strong> <br>
Usage: hadoop fs -getmerge   [addnl] <br>
Takes a source directory and a destination file as input and concatenates files in src into the destination local file. Optionally addnl can be set to enable adding a newline character at the end of each file.</p>

<p><strong>ls</strong> <br>
Usage: hadoop fs -ls  <br>
For a file returns stat on the file with the following format: <br>
filename  filesize modification_date modification_time permissions userid groupid  <br>
For a directory it returns list of its direct children as in unix. A directory is listed as:  <br>
dirname </p><dir> modification_time modification_time permissions userid <strong>groupid</strong>  <br>
例子: <br>
hadoop fs -ls /home/hadoop/input/ /home/hadoop/ouput/ <br>
输出： <br>
Found 2 items <br>
-rw-r–r–   1 hadoop supergroup         65 2013-11-30 17:48 /home/hadoop/input/address <br>
-rw-r–r–   1 hadoop supergroup         64 2013-11-29 22:48 /home/hadoop/input/content.txt <br>
Found 1 items <br>
-rw-r–r–   1 hadoop supergroup         64 2013-11-30 17:50 /home/hadoop/ouput <br>
Exit Code: <br>
Returns 0 on success and -1 on error. </dir><p></p>

<p><strong>lsr</strong> <br>
Usage: hadoop fs -lsr  <br>
例子： <br>
hadoop fs -lsr /home/hadoop/input/ /home/hadoop/ouput/</p>

<p>输出： <br>
-rw-r–r–   1 hadoop supergroup         65 2013-11-30 17:48 /home/hadoop/input/address <br>
-rw-r–r–   1 hadoop supergroup         64 2013-11-29 22:48 /home/hadoop/input/content.txt <br>
-rw-r–r–   1 hadoop supergroup         64 2013-11-30 17:50 /home/hadoop/ouput <br>
Recursive version of ls. Similar to Unix ls -R.</p>

<p><strong>mkdir</strong> <br>
Usage: hadoop fs -mkdir   <br>
Takes path uri’s as argument and creates directories. The behavior is much like unix mkdir -p creating parent directories along the path. <br>
例子: <br>
hadoop fs -mkdir /home/hadoop/input/ /home/hadoop/ouput/ <br>
hadoop fs -mkdir hdfs://nn1.example.com/user/hadoop/dir hdfs://nn2.example.com/user/hadoop/dir <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>movefromLocal</strong> <br>
Usage: dfs -moveFromLocal   <br>
Displays a “not implemented” message.</p>

<p><strong>mv</strong> <br>
Usage: hadoop fs -mv URI [URI …]  <br>
Moves files from source to destination. This command allows multiple sources as well in which case the destination needs to be a directory. Moving files across filesystems is not permitted.  <br>
例子: <br>
hadoop fs -mv /home/hadoop/input/lzw /home/hadoop/output <br>
hadoop fs -mv hdfs://nn.example.com/file1 hdfs://nn.example.com/file2 hdfs://nn.example.com/file3 hdfs://nn.example.com/dir1 <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>put</strong> <br>
Usage: hadoop fs -put  …  <br>
Copy single src, or multiple srcs from local file system to the destination filesystem. Also reads input from stdin and writes to destination filesystem. <br>
hadoop fs -put /home/hadoop/lzw /home/hadoop/input <br>
hadoop fs -put /home/hadoop/lzw /home/hadoop/lzw1 /home/hadoop/input <br>
hadoop fs -put localfile hdfs://nn.example.com/hadoop/hadoopfile <br>
hadoop fs -put - hdfs://nn.example.com/hadoop/hadoopfile  <br>
Reads the input from stdin.</p>

<p>Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>rm 删除文件</strong> <br>
Usage: hadoop fs -rm URI [URI …] <br>
Delete files specified as args. Only deletes non empty directory and files. Refer to rmr for recursive deletes. <br>
例子: <br>
hadoop fs -rm /home/hadoop/output/lzw <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>rmr 删除文件夹</strong> <br>
Usage: hadoop fs -rmr URI [URI …] <br>
Recursive version of delete. <br>
例子: <br>
hadoop fs -rmr /home/hadoop/output <br>
hadoop fs -rmr hdfs://nn.example.com/user/hadoop/dir <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>setrep</strong> <br>
Usage: hadoop fs -setrep [-R]  <br>
Changes the replication factor of a file. -R option is for recursively increasing the replication factor of files within a directory. <br>
例子: <br>
hadoop fs -setrep -w 3 -R /user/hadoop/dir1 <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>stat</strong> <br>
Usage: hadoop fs -stat URI [URI …] <br>
Returns the stat information on the path. <br>
例子: <br>
hadoop fs -stat /home/hadoop/input <br>
输出： <br>
2013-11-30 16:00:27 <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<p><strong>tail</strong> <br>
Usage: hadoop fs -tail [-f] URI <br>
Displays last kilobyte of the file to stdout. -f option can be used as in Unix. <br>
例子: <br>
hadoop fs -tail pathname <br>
Exit Code:  <br>
Returns 0 on success and -1 on error.</p>

<p><strong>test</strong> <br>
Usage: hadoop fs -test -[ezd] URI <br>
Options:  <br>
-e check to see if the file exists. Return 0 if true.  <br>
-z check to see if the file is zero length. Return 0 if true  <br>
-d check return 1 if the path is directory else return 0.  <br>
例子: <br>
hadoop fs -test -d /home/hadoop/input <br>
hadoop fs -test -e /home/hadoop/input <br>
hadoop fs -test -z /home/hadoop/input</p>

<p>text 查看文件内容，跟cat基本相似 <br>
Usage: hadoop fs -text  <br>
例子： <br>
hadoop fs -text /home/hadoop/input/address <br>
输出： <br>
addressID        addressname <br>
1        Beijing <br>
2        Guangzhou <br>
3        Shenzhen <br>
4        Xian <br>
Takes a source file and outputs the file in text format. The allowed formats are zip and TextRecordInputStream.</p>

<p><strong>touchz 创建空文件</strong> <br>
Usage: hadoop fs -touchz URI [URI …]  <br>
Create a file of zero length. <br>
例子: <br>
hadoop fs -touchz /home/hadoop/input/lzw <br>
Exit Code: <br>
Returns 0 on success and -1 on error.</p>

<hr>

<p>以上命令先记下来，在以后的用的过程中不会的再来翻阅。</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
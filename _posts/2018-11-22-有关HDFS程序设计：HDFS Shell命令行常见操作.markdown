---
layout:     post
title:      有关HDFS程序设计：HDFS Shell命令行常见操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p align="justify"><span style="font-family:'宋体';">在</span>/usr/local/hadoop/etc/hadoop <span style="font-family:'宋体';">目录下：</span></p><p align="justify"><strong><span style="font-family:'宋体';">帮助相关命令</span></strong></p><p align="justify">1.hdfs dfs     <span style="font-family:'宋体';">可以显示</span><span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">常用命令</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs 
Usage: hadoop fs [generic options]
	[-appendToFile &lt;localsrc&gt; ... &lt;dst&gt;]
	[-cat [-ignoreCrc] &lt;src&gt; ...]
	[-checksum &lt;src&gt; ...]
	[-chgrp [-R] GROUP PATH...]
	[-chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; PATH...]
	[-chown [-R] [OWNER][:[GROUP]] PATH...]
	[-copyFromLocal [-f] [-p] [-l] [-d] [-t &lt;thread count&gt;] &lt;localsrc&gt; ... &lt;dst&gt;]
	[-copyToLocal [-f] [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]
	[-count [-q] [-h] [-v] [-t [&lt;storage type&gt;]] [-u] [-x] [-e] &lt;path&gt; ...]
	[-cp [-f] [-p | -p[topax]] [-d] &lt;src&gt; ... &lt;dst&gt;]
	[-createSnapshot &lt;snapshotDir&gt; [&lt;snapshotName&gt;]]
	[-deleteSnapshot &lt;snapshotDir&gt; &lt;snapshotName&gt;]
	[-df [-h] [&lt;path&gt; ...]]
	[-du [-s] [-h] [-v] [-x] &lt;path&gt; ...]
	[-expunge]
	[-find &lt;path&gt; ... &lt;expression&gt; ...]
	[-get [-f] [-p] [-ignoreCrc] [-crc] &lt;src&gt; ... &lt;localdst&gt;]
	[-getfacl [-R] &lt;path&gt;]
	[-getfattr [-R] {-n name | -d} [-e en] &lt;path&gt;]
	[-getmerge [-nl] [-skip-empty-file] &lt;src&gt; &lt;localdst&gt;]
	[-help [cmd ...]]
	[-ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [-e] [&lt;path&gt; ...]]
	[-mkdir [-p] &lt;path&gt; ...]
	[-moveFromLocal &lt;localsrc&gt; ... &lt;dst&gt;]
	[-moveToLocal &lt;src&gt; &lt;localdst&gt;]
	[-mv &lt;src&gt; ... &lt;dst&gt;]
	[-put [-f] [-p] [-l] [-d] &lt;localsrc&gt; ... &lt;dst&gt;]
	[-renameSnapshot &lt;snapshotDir&gt; &lt;oldName&gt; &lt;newName&gt;]
	[-rm [-f] [-r|-R] [-skipTrash] [-safely] &lt;src&gt; ...]
	[-rmdir [--ignore-fail-on-non-empty] &lt;dir&gt; ...]
	[-setfacl [-R] [{-b|-k} {-m|-x &lt;acl_spec&gt;} &lt;path&gt;]|[--set &lt;acl_spec&gt; &lt;path&gt;]]
	[-setfattr {-n name [-v value] | -x name} &lt;path&gt;]
	[-setrep [-R] [-w] &lt;rep&gt; &lt;path&gt; ...]
	[-stat [format] &lt;path&gt; ...]
	[-tail [-f] &lt;file&gt;]
	[-test -[defsz] &lt;path&gt;]
	[-text [-ignoreCrc] &lt;src&gt; ...]
	[-touchz &lt;path&gt; ...]
	[-truncate [-w] &lt;length&gt; &lt;path&gt; ...]
	[-usage [cmd ...]]

Generic options supported are:
-conf &lt;configuration file&gt;        specify an application configuration file
-D &lt;property=value&gt;               define a value for a given property
-fs &lt;file:///|hdfs://namenode:port&gt; specify default filesystem URL to use, overrides 'fs.defaultFS' property from configurations.
-jt &lt;local|resourcemanager:port&gt;  specify a ResourceManager
-files &lt;file1,...&gt;                specify a comma-separated list of files to be copied to the map reduce cluster
-libjars &lt;jar1,...&gt;               specify a comma-separated list of jar files to be included in the classpath
-archives &lt;archive1,...&gt;          specify a comma-separated list of archives to be unarchived on the compute machines

The general command line syntax is:
command [genericOptions] [commandOptions]</code></pre><p align="justify">2.usage  <span style="font-family:'宋体';">查看命令的用法，例如，查看</span><span style="font-family:Calibri;">ls</span><span style="font-family:'宋体';">的用法</span></p><p align="justify">   hdfs dfs -usage ls</p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -usage ls
Usage: hadoop fs [generic options] -ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [-e] [&lt;path&gt; ...]</code></pre><p align="justify">3.help  <span style="font-family:'宋体';">查看命名的详细帮助，例如查看</span><span style="font-family:Calibri;">ls</span><span style="font-family:'宋体';">命令的详细帮助</span></p><p align="justify">  hdfs dfs -help ls</p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -help ls
-ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [-e] [&lt;path&gt; ...] :
  List the contents that match the specified file pattern. If path is not
  specified, the contents of /user/&lt;currentUser&gt; will be listed. For a directory a
  list of its direct children is returned (unless -d option is specified).
  
  Directory entries are of the form:
  	permissions - userId groupId sizeOfDirectory(in bytes)
  modificationDate(yyyy-MM-dd HH:mm) directoryName
  
  and file entries are of the form:
  	permissions numberOfReplicas userId groupId sizeOfFile(in bytes)
  modificationDate(yyyy-MM-dd HH:mm) fileName
  
    -C  Display the paths of files and directories only.
    -d  Directories are listed as plain files.
    -h  Formats the sizes of files in a human-readable fashion
        rather than a number of bytes.
    -q  Print ? instead of non-printable characters.
    -R  Recursively list the contents of directories.
    -t  Sort files by modification time (most recent first).
    -S  Sort files by size.
    -r  Reverse the order of the sort.
    -u  Use time of last access instead of modification for
        display and sorting.
    -e  Display the erasure coding policy of files and directories.</code></pre><p align="justify"><strong><span style="font-family:'宋体';">查看相关命令</span></strong></p><p align="justify">4.ls <span style="font-family:'宋体';">查看文件或者目录</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -ls /
Found 21 items
-rw-r--r--   2 hadoop supergroup         39 2018-03-21 17:17 /aaaa
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 17:24 /ffk
-rw-r--r--   2 hadoop supergroup        312 2018-03-21 17:10 /hhh
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 09:31 /hltest
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 10:09 /hltestout
drwxr-xr-x   - hadoop supergroup          0 2018-03-21 17:15 /home
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:01 /input
drwxr-xr-x   - hadoop supergroup          0 2018-03-08 22:26 /ning
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:17 /output
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:22 /output2
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:24 /output3
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:26 /output4
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:27 /output5
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 18:34 /result
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 21:51 /test
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 20:48 /testxuning
drwx------   - hadoop supergroup          0 2018-03-07 19:00 /tmp
drwxr-xr-x   - hadoop supergroup          0 2018-03-09 21:04 /xu
drwxr-xr-x   - hadoop supergroup          0 2018-03-10 21:45 /xun
drwxr-xr-x   - hadoop supergroup          0 2018-03-10 21:50 /xunin
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 18:33 /xuning</code></pre><p align="justify"><span style="font-family:'宋体';">选项</span>-r<span style="font-family:'宋体';">可连同其子文件目录一起列出：</span><span style="font-family:Calibri;">hdfs dfs -ls -r/</span></p><p align="justify">5.cat <span style="font-family:'宋体';">查看文件内容</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -cat /aaaa
/home/hadoop/eclipse/committers-oxygen</code></pre><p align="justify"><span style="font-family:'宋体';">文件及目录相关命令</span></p><p align="justify">6.touchz<span style="font-family:'宋体';">创建一个空文件夹，如果存在指定名称的非空文件夹，则返回错误</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -touchz /mal
hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -ls /
Found 22 items
-rw-r--r--   2 hadoop supergroup         39 2018-03-21 17:17 /aaaa
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 17:24 /ffk
-rw-r--r--   2 hadoop supergroup        312 2018-03-21 17:10 /hhh
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 09:31 /hltest
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 10:09 /hltestout
drwxr-xr-x   - hadoop supergroup          0 2018-03-21 17:15 /home
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:01 /input
-rw-r--r--   2 hadoop supergroup          0 2018-03-21 19:38 /mal</code></pre><p align="justify"></p><p class="p" align="justify" style="line-height:19.5pt;"><span style="font-family:Calibri;font-size:12pt;">7.appendtofile <span style="font-family:'宋体';">向现有文件中追加内容</span></span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -cat /mal
hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -appendToFile /home/hadoop/桌面/aaaa /mal
hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -cat /mal
/home/hadoop/eclipse/committers-oxygen</code></pre><p align="justify">8.put<span style="font-family:'宋体';">从本地文件系统上传文件到</span><span style="font-family:Calibri;">HDFS</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -put /home/hadoop/桌面/325 /shiyan
hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -ls /
Found 23 items
-rw-r--r--   2 hadoop supergroup         39 2018-03-21 17:17 /aaaa
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 17:24 /ffk
-rw-r--r--   2 hadoop supergroup        312 2018-03-21 17:10 /hhh
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 09:31 /hltest
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 10:09 /hltestout
drwxr-xr-x   - hadoop supergroup          0 2018-03-21 17:15 /home
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:01 /input
-rw-r--r--   2 hadoop supergroup         39 2018-03-25 15:38 /mal
drwxr-xr-x   - hadoop supergroup          0 2018-03-08 22:26 /ning
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:17 /output
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:22 /output2
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:24 /output3
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:26 /output4
drwxr-xr-x   - hadoop supergroup          0 2018-03-07 15:27 /output5
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 18:34 /result
-rw-r--r--   2 hadoop supergroup         39 2018-03-25 15:51 /shiyan
hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -cat /shiyan
/home/hadoop/eclipse/committers-oxygen</code></pre><p align="justify"><span style="font-family:'宋体';">选项</span>-f<span style="font-family:'宋体';">：如果文件已经存在，则覆盖已有文件</span></p><p align="justify"><span style="font-family:'宋体';">选项</span>-p<span style="font-family:'宋体';">：保留原文件的访问和修改时间，用户和组，权限属性</span></p><p align="justify">9.get<span style="font-family:'宋体';">从</span><span style="font-family:Calibri;">HDFS</span><span style="font-family:'宋体';">上下载文件到本地，与</span><span style="font-family:Calibri;">put</span><span style="font-family:'宋体';">不同，没有覆盖本地已有文件的选项</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -get /shiyan ~
hadoop@master:/usr/local/hadoop/etc/hadoop$ cd
hadoop@master:~$ ls
cqq                examples.desktop  hl               wordcount  模板  未命名文件夹  音乐
eclipse            fk2               mapred-site.xml  xuning     视频  文档          桌面
eclipse-workspace  hadoop            shiyan           公共的     图片  下载</code></pre><p align="justify">10.getmerge:<span style="font-family:'宋体';">将指定的</span><span style="font-family:Calibri;">HDFS</span><span style="font-family:'宋体';">目录下的文件合并成一个文件并下载到本地，源文件保留</span></p><p align="justify"><span style="font-family:'宋体';">选项</span>-nl<span style="font-family:'宋体';">：在每个文件的最后一行增加一新行</span></p><p align="justify">11.copyFromLocal</p><p align="justify"><span style="font-family:'宋体';">从本地文件系统上传到</span>HDFS<span style="font-family:'宋体';">，与</span><span style="font-family:Calibri;">put</span><span style="font-family:'宋体';">命令相同</span></p><p align="justify">12.copyToLocal</p><p align="justify"><span style="font-family:'宋体';">从</span>HDFS<span style="font-family:'宋体';">下载文件到本地系统文件，与</span><span style="font-family:Calibri;">get</span><span style="font-family:'宋体';">命令相同</span></p><p align="justify">13.moveFromLocal</p><p align="justify"><span style="font-family:'宋体';">与</span>put<span style="font-family:'宋体';">命令相同，只是上传成功后本地文件会被删除</span></p><p align="justify">14.cp<span style="font-family:'宋体';">复制文件</span></p><p align="justify"><span style="font-family:'宋体';">选项</span>-f<span style="font-family:'宋体';">如果文件已存在，覆盖已有文件</span></p><p align="justify">15.mkdir<span style="font-family:'宋体';">创建文件夹</span></p><p align="justify"><span style="font-family:'宋体';">选项</span>-p<span style="font-family:'宋体';">如果上层目录不存在则递归建立所需目录</span></p><p align="justify">16.rm<span style="font-family:'宋体';">删除文件</span></p><p align="justify"><span style="font-family:'宋体';">选项</span>-r<span style="font-family:'宋体';">，递归删除，可以删除非空目录</span></p><p align="justify">17.rmdir<span style="font-family:'宋体';">删除空目录</span></p><p align="justify"><span style="font-family:'宋体';">选项</span>--ignore-fail-on-non-empety<span style="font-family:'宋体';">：忽略非空删除失败时的提示</span></p><p align="justify"><span style="font-family:'宋体';">（此时，用此命令来删除非空目录，没有错误信息提示，但是文件未被删除）</span></p><p align="justify">18.setrep<span style="font-family:'宋体';">改变一个文件的副本数</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -setrep 2 /aaaa
Replication 2 set: /aaaa</code></pre><p align="justify"><span style="font-family:'宋体';">选项</span>-w<span style="font-family:'宋体';">：命令等待副本数调整完成</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -setrep -w 2 /aaaa
Replication 2 set: /aaaa
Waiting for /aaaa ... done</code></pre><p align="justify">19.expunge<span style="font-family:'宋体';">清空回收站</span></p><p align="justify">20.chgrp<span style="font-family:'宋体';">修改文件用户组</span></p><pre><code class="language-html">[hadoop@localhost hadoop-2.5.2]$ hdfs dfs -chgrp test /output
[hadoop@localhost hadoop-2.5.2]$ hdfs dfs -ls -R /
drwxr-xr-x   - hadoop supergroup          0 2015-03-27 19:19 /input
-rw-r--r--   1 hadoop supergroup         14 2015-03-27 19:19 /input/input1.txt
-rw-r--r--   1 hadoop supergroup         32 2015-03-27 19:19 /input/input2.txt
-rw-r--r--   1 hadoop supergroup          0 2015-04-02 08:43 /input.zip
-rw-r--r--   1 hadoop supergroup        184 2015-03-31 08:14 /input1.zip
drwxr-xr-x   - hadoop test                0 2015-04-02 08:34 /output                     --修改后的用户组（未建立test组，仍可成功）
-rwxrwxrwx   1 hadoop hadoops            28 2015-03-31 08:59 /output/input1.txt    --目录下文件的用户组未修改</code></pre><p align="justify"><span style="font-family:'宋体';">选项</span>-R<span style="font-family:'宋体';">：递归修改，如果是目录，则递归的修改其下的文件和目录</span></p><p align="justify">21.chmod<span style="font-family:'宋体';">修改文件权限</span></p><p align="justify"><span style="font-family:'宋体';">补充文件权限知识：</span></p><p align="justify">d<span style="font-family:'宋体';">：表示一个目录（目录也是一个特殊的文件）</span></p><p align="justify">-<span style="font-family:'宋体';">：表示这个是普通文件</span></p><p align="justify">r<span style="font-family:'宋体';">（</span><span style="font-family:Calibri;">read</span><span style="font-family:'宋体';">，读取）：读取文件内容；浏览目录</span></p><p align="justify">w<span style="font-family:'宋体';">（</span><span style="font-family:Calibri;">write</span><span style="font-family:'宋体';">，写入）</span><span style="font-family:Calibri;">:</span><span style="font-family:'宋体';">新增、修改文件内容；删除、移动目录内文件</span></p><p align="justify">x<span style="font-family:'宋体';">（</span><span style="font-family:Calibri;">execute</span><span style="font-family:'宋体';">，执行）</span><span style="font-family:Calibri;">:</span><span style="font-family:'宋体';">执行文件；进入目录</span></p><p align="justify">-<span style="font-family:'宋体';">：表示不具备该项权限</span></p><p align="justify"><span style="font-family:'宋体';">将</span>rwx<span style="font-family:'宋体';">看成二进制，如果有则用</span><span style="font-family:Calibri;">1</span><span style="font-family:'宋体';">表示，没有则用</span><span style="font-family:Calibri;">0</span><span style="font-family:'宋体';">表示，如：</span><span style="font-family:Calibri;">rwx r-x r--</span><span style="font-family:'宋体';">即可以表示为</span><span style="font-family:Calibri;">111 101 100</span><span style="font-family:'宋体';">将其每三位转换成</span><span style="font-family:Calibri;">754</span></p><p align="justify"><span style="color:rgb(102,102,102);background:rgb(255,255,255);">-rwx------:</span><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><span style="font-family:'宋体';">等于数字表示</span></span><span style="color:rgb(102,102,102);background:rgb(255,255,255);">700</span><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><br></span><span style="color:rgb(102,102,102);background:rgb(255,255,255);">-rwxr―r--:</span><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><span style="font-family:'宋体';">等于数字表示</span></span><span style="color:rgb(102,102,102);background:rgb(255,255,255);">744</span><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><br></span><span style="color:rgb(102,102,102);background:rgb(255,255,255);">-rw-rw-r-x:</span><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><span style="font-family:'宋体';">等于数字表示</span></span><span style="color:rgb(102,102,102);background:rgb(255,255,255);">665</span><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><br></span><span style="color:rgb(102,102,102);background:rgb(255,255,255);">drwx―x―x:</span><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><span style="font-family:'宋体';">等于数字表示</span></span><span style="color:rgb(102,102,102);background:rgb(255,255,255);">711</span></p><p align="justify"><span style="color:rgb(102,102,102);background:rgb(255,255,255);">drwx------:</span><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><span style="font-family:'宋体';">等于数字表示</span></span><span style="color:rgb(102,102,102);background:rgb(255,255,255);">700</span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -ls /
Found 23 items
-rw-r--r--   2 hadoop supergroup         39 2018-03-21 17:17 /aaaa
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 17:24 /ffk
-rw-r--r--   2 hadoop supergroup        312 2018-03-21 17:10 /hhh
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 09:31 /hltest
hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -chmod 754 /hltest
hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -ls /
Found 23 items
-rw-r--r--   2 hadoop supergroup         39 2018-03-21 17:17 /aaaa
drwxr-xr-x   - hadoop supergroup          0 2018-03-11 17:24 /ffk
-rw-r--r--   2 hadoop supergroup        312 2018-03-21 17:10 /hhh
drwxr-xr--   - hadoop supergroup          0 2018-03-11 09:31 /hltest</code></pre><p align="justify">22.getfacl显示访问控制列表（ACL）</p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -getfacl /hltest
# file: /hltest
# owner: hadoop
# group: supergroup
user::rwx
group::r-x
other::r--</code></pre><p><span style="color:rgb(102,102,102);background:rgb(255,255,255);"><span style="font-family:'PingFang SC';">选项</span>-R<span style="font-family:'PingFang SC';">：递归显示</span></span></p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -getfacl -R /hltest
# file: /hltest
# owner: hadoop
# group: supergroup
user::rwx
group::r-x
other::r--

# file: /hltest/file1
# owner: hadoop
# group: supergroup
user::rw-
group::r--
other::r--

# file: /hltest/file2
# owner: hadoop
# group: supergroup
user::rw-
group::r--
other::r--</code></pre><p align="justify">23.setfacl设置访问控制列表</p><p align="justify"><strong>统计相关命令</strong></p><p align="justify">24.count：显示指定文件或目录的：DIR_COUNT、FILE_COUNT、CONTENT_SIZE、FILE_NAME,分别表示子目录个数（如果指定路径是目录，则包含目录本身）、文件个数、使用字节数，以及文件或目录名。</p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -count /
          75         2008           37952956 /</code></pre><p align="justify">25.du显示文件大小，如果指定目录，则会显示该目录中每个文件的大小</p><pre><code class="language-html">39        78        /aaaa
0         0         /ffk
312       624       /hhh
140       280       /hltest
0         0         /hltestout
34929793  69859586  /home
87        174       /input
39        78        /mal
207       414       /ning
93        186       /output
0         0         /output2
93        186       /output3
0         0         /output4
93        186       /output5
2004      4008      /result
39        78        /shiyan
2314      4628      /test
96        192       /testxuning
3017342   6244108   /tmp
92        184       /xu
85        170       /xun
0         0         /xunin
88        176       /xuning</code></pre><p align="justify">26.df检查文件系统的磁盘空间占用情况</p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -df /
Filesystem                  Size       Used     Available  Use%
hdfs://master:9000  820654354432  103129088  753473880064    0%</code></pre><p align="justify">27.stat显示文件统计信息</p><p align="justify"><span style="font-family:'PingFang SC';">格式：</span>%b-文件所占块数；%g-文件所属的用户组；%n-文件名；%o-文件块大小；%r-备份数；%u-文件所属用户；%y-文件修改时间</p><pre><code class="language-html">hadoop@master:/usr/local/hadoop/etc/hadoop$ hdfs dfs -stat %b,%g,%n,%o,%r,,%u,%y /hltest
0,supergroup,hltest,0,0,,hadoop,2018-03-11 01:31:47</code></pre><p>以上命令基本全部进行实现，少数不常用命令未曾列举，仅供个人以后学习参考。</p>            </div>
                </div>
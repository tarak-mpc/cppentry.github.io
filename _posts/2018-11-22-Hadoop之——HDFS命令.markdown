---
layout:     post
title:      Hadoop之——HDFS命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/l1028386804/article/details/45769573				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<pre><a href="http://blog.csdn.net/l1028386804/article/details/45769573" rel="nofollow">转载请注明出处：http://blog.csdn.net/l1028386804/article/details/45769573</a>
</pre>
<p>1.对hdfs操作的命令格式是hadoop fs </p>
    1.1 -ls        &lt;path&gt;    表示对hdfs下一级目录的查看<br>
    1.2 -lsr    &lt;path&gt;    表示对hdfs目录的递归查看<br>
    1.3    -mkdir    &lt;path&gt;    创建目录<br>
    1.4 -put    &lt;src&gt;    &lt;des&gt;    从linux上传文件到hdfs<br>
    1.5 -get    &lt;src&gt;    &lt;des&gt;    从hdfs下载文件到linux<br>
    1.6 -text    &lt;path&gt;    查看文件内容<br>
    1.7 -rm        &lt;path&gt;    表示删除文件<br>
    1.7 -rmr    &lt;path&gt;    表示递归删除文件<br>
2.hdfs在对数据存储进行block划分时，如果文件大小超过block，那么按照block大小进行划分；不如block size的，划分为一个块，是实际数据大小。<br><br><p>*****PermissionDenyException  权限不足**********</p>
<p><br></p>
<p><span style="color:#000000;">cat</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -cat URI [URI …]</span></p>
<p><span style="color:#000000;">将路径指定文件的内容输出到</span><span style="color:#000000;">stdout
</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -cathdfs://host1:port1/file1 hdfs://host2:port2/file2</span></p>
<p><span style="color:#000000;">hadoop fs -cat file:///file3/user/hadoop/file4</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">chgrp</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -chgrp [-R] GROUP URI [URI …] Change group association of files. With -R ,make the change recursively through the directory structure. The user must bethe owner of files, or else a
 super-user. Additional information is in thePermissions User Guide . --&gt;</span></p>
<p><span style="color:#000000;">改变文件所属的组。使用</span><span style="color:#000000;">-R </span>
<span style="color:#000000;">将使改变在目录结构下递归进行。命令的使用者必须是文件的所有者或者超级用户。更多的信息请参见</span><span style="color:#000000;">HDFS</span><span style="color:#000000;">权限用户指南 。</span></p>
<p><span style="color:#000000;">chmod</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; URI [URI …]</span></p>
<p><span style="color:#000000;">改变文件的权限。使用</span><span style="color:#000000;">-R </span>
<span style="color:#000000;">将使改变在目录结构下递归进行。命令的使用者必须是文件的所有者或者超级用户。更多的信息请参见</span><span style="color:#000000;">HDFS</span><span style="color:#000000;">权限用户指南 。</span></p>
<p><span style="color:#000000;">chown</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -chown [-R] [OWNER][:[GROUP]] URI [URI ]</span></p>
<p><span style="color:#000000;">改变文件的拥有者。使用</span><span style="color:#000000;">-R </span>
<span style="color:#000000;">将使改变在目录结构下递归进行。命令的使用者必须是超级用户。更多的信息请参见</span><span style="color:#000000;">HDFS
</span><span style="color:#000000;">权限用户指南 。</span></p>
<p><span style="color:#000000;">copyFromLocal</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -copyFromLocal &lt;localsrc&gt; URI</span></p>
<p><span style="color:#000000;">除了限定源路径是一个本地文件外，和</span><span style="color:#000000;">put
</span><span style="color:#000000;">命令相似。</span></p>
<p><span style="color:#000000;">copyToLocal</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -copyToLocal [-ignorecrc] [-crc] URI &lt;localdst&gt;</span></p>
<p><span style="color:#000000;">除了限定目标路径是一个本地文件外，和</span><span style="color:#000000;">get
</span><span style="color:#000000;">命令类似。</span></p>
<p><span style="color:#000000;">cp</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -cp URI [URI …] &lt;dest&gt;</span></p>
<p><span style="color:#000000;">将文件从源路径复制到目标路径。这个命令允许有多个源路径，此时目标路径必须是一个目录。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -cp /user/hadoop/file1/user/hadoop/file2</span></p>
<p><span style="color:#000000;">hadoop fs -cp /user/hadoop/file1/user/hadoop/file2 /user/hadoop/dir</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">du</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -du URI [URI …]</span></p>
<p><span style="color:#000000;">显示目录中所有文件的大小，或者当只指定一个文件时，显示此文件的大小。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -du /user/hadoop/dir1/user/hadoop/file1 hdfs://host:port/user/hadoop/dir1</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">dus</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -dus &lt;args&gt;</span></p>
<p><span style="color:#000000;">显示文件的大小。</span></p>
<p><span style="color:#000000;">expunge</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -expunge</span></p>
<p><span style="color:#000000;">清空回收站。请参考</span><span style="color:#000000;">HDFS </span>
<span style="color:#000000;">设计文档以获取更多关于回收站特性的信息。</span></p>
<p><span style="color:#000000;">get</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -get [-ignorecrc] [-crc] &lt;src&gt; &lt;localdst&gt;</span></p>
<p><span style="color:#000000;">复制文件到本地文件系统。可用</span><span style="color:#000000;">-ignorecrc
</span><span style="color:#000000;">选项复制</span><span style="color:#000000;">CRC</span><span style="color:#000000;">校验失败的文 件。使用</span><span style="color:#000000;">-crc
</span><span style="color:#000000;">选项复制文件以及</span><span style="color:#000000;">CRC</span><span style="color:#000000;">信息。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -get /user/hadoop/filelocalfile</span></p>
<p><span style="color:#000000;">hadoop fs -gethdfs://host:port/user/hadoop/file localfile</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">getmerge</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -getmerge &lt;src&gt; &lt;localdst&gt; [addnl]</span></p>
<p><span style="color:#000000;">接受一个源目录和一个目标文件作为输入，并且将源目录中所有的文件连接成本地目标文件。</span><span style="color:#000000;">addnl
</span><span style="color:#000000;">是 可选的，用于指定在每个文件结尾添加一个换行符。</span></p>
<p><span style="color:#000000;">ls</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -ls &lt;args&gt;</span></p>
<p><span style="color:#000000;">如果是文件，则按照如下格式返回文件信息：</span></p>
<p><span style="color:#000000;">文件名 </span><span style="color:#000000;">&lt;</span><span style="color:#000000;">副本数</span><span style="color:#000000;">&gt;</span><span style="color:#000000;">文件大小 修改日期 修改时间 权限 用户</span><span style="color:#000000;">ID
</span><span style="color:#000000;">组</span><span style="color:#000000;">ID</span></p>
<p><span style="color:#000000;">如果是目录，则返回它直接子文件的一个列表，就像在</span><span style="color:#000000;">Unix</span><span style="color:#000000;">中一样。目录返回列表的信息如下：</span></p>
<p><span style="color:#000000;">目录名 </span><span style="color:#000000;">&lt;dir&gt;</span><span style="color:#000000;">修改日期 修改时间 权限 用户</span><span style="color:#000000;">ID
</span><span style="color:#000000;">组</span><span style="color:#000000;">ID</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -ls /user/hadoop/file1/user/hadoop/file2 hdfs://host:port/user/hadoop/dir1 /nonexistentfile</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">lsr</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -lsr &lt;args&gt;</span></p>
<p><span style="color:#000000;">ls </span><span style="color:#000000;">命令的递归版本。类似于</span><span style="color:#000000;">Unix</span><span style="color:#000000;">中的</span><span style="color:#000000;">ls -R
</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">mkdir</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -mkdir &lt;paths&gt;</span></p>
<p><span style="color:#000000;">接受路径指定的</span><span style="color:#000000;">uri</span><span style="color:#000000;">作为参数，创建这些目录。其行为类似于</span><span style="color:#000000;">Unix</span><span style="color:#000000;">的</span><span style="color:#000000;">mkdir -p</span><span style="color:#000000;">，它会创建路径中的各级父目录。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -mkdir /user/hadoop/dir1/user/hadoop/dir2</span></p>
<p><span style="color:#000000;">hadoop fs -mkdirhdfs://host1:port1/user/hadoop/dir hdfs://host2:port2/user/hadoop/dir</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">movefromLocal</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">dfs-moveFromLocal &lt;src&gt; &lt;dst&gt;</span></p>
<p><span style="color:#000000;">输出一个”</span><span style="color:#000000;">notimplemented“</span><span style="color:#000000;">信息。</span></p>
<p><span style="color:#000000;">mv</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -mv URI [URI …] &lt;dest&gt;</span></p>
<p><span style="color:#000000;">将文件从源路径移动到目标路径。这个命令允许有多个源路径，此时目标路径必须是一个目录。不允许在不同的文件系统间移动文件。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -mv /user/hadoop/file1/user/hadoop/file2</span></p>
<p><span style="color:#000000;">hadoop fs -mv hdfs://host:port/file1hdfs://host:port/file2 hdfs://host:port/file3 hdfs://host:port/dir1</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">put</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -put &lt;localsrc&gt; ... &lt;dst&gt;</span></p>
<p><span style="color:#000000;">从本地文件系统中复制单个或多个源路径到目标文件系统。也支持从标准输入中读取输入写入目标文件系统。</span></p>
<p><span style="color:#000000;">hadoop fs -put localfile/user/hadoop/hadoopfile</span></p>
<p><span style="color:#000000;">hadoop fs -put localfile1 localfile2/user/hadoop/hadoopdir</span></p>
<p><span style="color:#000000;">hadoop fs -put localfilehdfs://host:port/hadoop/hadoopfile</span></p>
<p><span style="color:#000000;">hadoop fs -put -hdfs://host:port/hadoop/hadoopfile</span></p>
<p><span style="color:#000000;">从标准输入中读取输入。</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">rm</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -rm URI [URI …]</span></p>
<p><span style="color:#000000;">删除指定的文件。只删除非空目录和文件。请参考</span><span style="color:#000000;">rmr</span><span style="color:#000000;">命令了解递归删除。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -rm hdfs://host:port/file/user/hadoop/emptydir</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">rmr</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -rmr URI [URI …]</span></p>
<p><span style="color:#000000;">delete</span><span style="color:#000000;">的递归版本。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -rmr /user/hadoop/dir</span></p>
<p><span style="color:#000000;">hadoop fs -rmrhdfs://host:port/user/hadoop/dir</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">setrep</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -setrep [-R] &lt;path&gt;</span></p>
<p><span style="color:#000000;">改变一个文件的副本系数。</span><span style="color:#000000;">-R</span><span style="color:#000000;">选项用于递归改变目录下所有文件的副本系数。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -setrep -w 3 -R/user/hadoop/dir1</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">stat</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -stat URI [URI …]</span></p>
<p><span style="color:#000000;">返回指定路径的统计信息。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -stat path</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">tail</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -tail [-f] URI</span></p>
<p><span style="color:#000000;">将文件尾部</span><span style="color:#000000;">1K</span><span style="color:#000000;">字节的内容输出到</span><span style="color:#000000;">stdout</span><span style="color:#000000;">。支持</span><span style="color:#000000;">-f</span><span style="color:#000000;">选项，行为和</span><span style="color:#000000;">Unix</span><span style="color:#000000;">中一致。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -tail pathname</span></p>
<p><span style="color:#000000;">返回值：</span></p>
<p><span style="color:#000000;">成功返回</span><span style="color:#000000;">0</span><span style="color:#000000;">，失败返回</span><span style="color:#000000;">-1</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">test</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -test -[ezd] URI</span></p>
<p><span style="color:#000000;">选项：</span></p>
<p><span style="color:#000000;">-e </span><span style="color:#000000;">检查文件是否存在。如果存在则返回</span><span style="color:#000000;">0</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">-z </span><span style="color:#000000;">检查文件是否是</span><span style="color:#000000;">0</span><span style="color:#000000;">字节。如果是则返回</span><span style="color:#000000;">0</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">-d </span><span style="color:#000000;">如果路径是个目录，则返回</span><span style="color:#000000;">1</span><span style="color:#000000;">，否则返回</span><span style="color:#000000;">0</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop fs -test -e filename</span></p>
<p><span style="color:#000000;">text</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -text &lt;src&gt;</span></p>
<p><span style="color:#000000;">将源文件输出为文本格式。允许的格式是</span><span style="color:#000000;">zip</span><span style="color:#000000;">和</span><span style="color:#000000;">TextRecordInputStream</span><span style="color:#000000;">。</span></p>
<p><span style="color:#000000;">touchz</span></p>
<p><span style="color:#000000;">使用方法：</span><span style="color:#000000;">hadoopfs -touchz URI [URI …]</span></p>
<p><span style="color:#000000;">创建一个</span><span style="color:#000000;">0</span><span style="color:#000000;">字节的空文件。</span></p>
<p><span style="color:#000000;">示例：</span></p>
<p><span style="color:#000000;">hadoop -touchz pathname</span></p>
            </div>
                </div>
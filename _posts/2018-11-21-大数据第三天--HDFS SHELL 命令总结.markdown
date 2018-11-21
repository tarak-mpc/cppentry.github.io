---
layout:     post
title:      大数据第三天--HDFS SHELL 命令总结
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h5><span style="font-family:'Times New Roman';font-size:14px;">(1)-appendToFile</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法： <tt>hadoop fs -appendToFile &lt;localsrc&gt; ... &lt;dst&gt;</tt><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：附加文件到指定文件后。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">示例:</span></p>
<ul><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -appendToFile localfile /user/hadoop/hadoopfile</span></tt></li><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -appendToFile localfile1 localfile2 /user/hadoop/hadoopfile</span></tt></li><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -appendToFile localfile hdfs://nn.example.com/hadoop/hadoopfile</span></tt></li><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;"><tt>hadoop fs -appendToFile - hdfs://nn.example.com/hadoop/hadoopfile</tt> Reads the input from stdin.</span></li></ul><span style="font-family:'Times New Roman';font-size:14px;"><br></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(2)-cat</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -cat URI [URI ...]<br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：查看文件内容（可以查看本地和HDFS上的内容）。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><br>
示例：</span></p>
<ul><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -cat hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2</span></tt></li><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -cat file:///file3 /user/hadoop/file4</span></tt></li></ul><h5><span style="font-family:'Times New Roman';font-size:14px;">(3)-checksum</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法： <tt>hadoop fs -checksum URI</tt><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：查看文件校验和。（例子显示了MD5）、</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">示例：</span></p>
<ul><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -checksum hdfs://nn1.example.com/file1</span></tt></li><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -checksum file:///etc/hosts</span></tt></li></ul><span style="font-family:'Times New Roman';font-size:14px;"><br></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(4)-chgrp</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法： <tt>hadoop fs -chgrp [-R] GROUP URI [URI ...]</tt><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：改变文件所属的组。（Change group association of files.）</span></p>
<p><tt><span style="font-family:'Times New Roman';font-size:14px;">使用-R 将使改变在目录结构下递归进行。</span></tt></p>
<p><tt><span style="font-family:'Times New Roman';font-size:14px;"><br></span></tt></p>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(5)-chmod</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：改变文件访问权限。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; URI [URI ...]</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">这里可以参考 linux下文件系统的chmod的用法，基本类似。</span></p>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(6)-chown</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI ]</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法:改变文件的所有者。使用-R 将使改变在目录结构下递归进行。命令的使用者必须是超级用户。</span></p>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(7)-copyFromLocal</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -copyFromLocal &lt;localsrc&gt; URI<br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：类似于put命令，和put不同的是，拷贝的源地址必须是本地文件地址。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">  <span></span>-f 参数 当拷贝的目标文件存在时，进行覆盖。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">示例：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"></span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> <a href="http://blog.csdn.net/u010536377/article/details/50075325#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><span> <a href="http://blog.csdn.net/u010536377/article/details/50075325#" rel="nofollow" class="CopyToClipboard" title="copy">
copy</a></span><span> <a href="http://blog.csdn.net/u010536377/article/details/50075325#" rel="nofollow" class="PrintSource" title="print">
print</a></span><a href="http://blog.csdn.net/u010536377/article/details/50075325#" rel="nofollow" class="About" title="?">?</a></div>
</div>
<ol start="1"><li class="alt"><span><span>taodj@ubuntu:~$ hadoop fs -copyFromLocal testFlatMap.txt /1.txt  </span></span></li><li><span>copyFromLocal: `/1.txt': File exists  </span></li></ol></div>
<br><br><p><span style="font-family:'Times New Roman';font-size:14px;">这个时候加上-f参数。即可覆盖。</span></p>
<div class="dp-highlighter bg_plain">
<div class="bar">
<div class="tools"><strong>[plain]</strong> <a href="http://blog.csdn.net/u010536377/article/details/50075325#" rel="nofollow" class="ViewSource" title="view plain">
view plain</a><span> <a href="http://blog.csdn.net/u010536377/article/details/50075325#" rel="nofollow" class="CopyToClipboard" title="copy">
copy</a></span><span> <a href="http://blog.csdn.net/u010536377/article/details/50075325#" rel="nofollow" class="PrintSource" title="print">
print</a></span><a href="http://blog.csdn.net/u010536377/article/details/50075325#" rel="nofollow" class="About" title="?">?</a></div>
</div>
<ol start="1"><li class="alt"><span><span>taodj@ubuntu:~$ hadoop fs -copyFromLocal -f testFlatMap.txt /1.txt  </span></span></li></ol></div>
<br><br><h5><span style="font-family:'Times New Roman';font-size:14px;">(8)-copyToLocal</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法： <tt>hadoop fs -copyToLocal [-ignorecrc] [-crc] URI &lt;localdst&gt;</tt><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：类似于get指令。和get不同的是，拷贝的目的地址必须是本地文件地址。</span></p>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(9)-count</span></h5>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：计算paths下的目录数，文件数和字节数。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">用法： <tt>hadoop fs -count [-q] [-h] [-v] &lt;paths&gt;</tt></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"></span></p>
<ul><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -count hdfs://nn1.example.com/file1 hdfs://nn2.example.com/file2</span></tt></li><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -count -q hdfs://nn1.example.com/file1</span></tt></li><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -count -q -h hdfs://nn1.example.com/file1</span></tt></li><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hdfs dfs -count -q -h -v hdfs://nn1.example.com/file1</span></tt></li></ul><div><span style="color:#333333;"><span style="font-family:'Times New Roman';font-size:14px;"><br></span></span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;"></span></span></div>
<h5><span style="color:#333333;font-weight:normal;"><span style="font-family:'Times New Roman';font-size:14px;">(10)-cp</span></span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -cp [-f] [-p | -p[topax]] URI [URI ...] &lt;dest&gt;<br></span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：拷贝，HDFS文件系统中进行的拷贝操作。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">-f 参数选项：当文件存在时，进行覆盖。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">-p 参数选项：将权限、所属组、时间戳、ACL以及XAttr等也进行拷贝。下面是官网的描述。</span></div>
<div>
<ul><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">The -p option will preserve file attributes [topx] (timestamps, ownership, permission, ACL, XAttr). If -p is specified with no <em>arg</em>, then preserves timestamps,
 ownership, permission. If -pa is specified, then preserves permission also because ACL is a super-set of permission. Determination of whether raw namespace extended attributes are preserved is independent of the -p flag.</span></li></ul></div>
<div>
<div>
<h5><span style="font-weight:normal;"><span style="font-family:'Times New Roman';font-size:14px;">(11)-df</span></span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;">用法：</span>hadoop fs -df [-h] URI [URI ...]<br></span></div>
<div>
<div><span style="color:#333333;"><span style="font-family:'Times New Roman';font-size:14px;">作用：显示剩余空间。</span></span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">示例：</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">taodj@ubuntu:~$ hadoop fs -df -h /</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">Filesystem               Size     Used  Available  Use%</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">hdfs://localhost:9000  18.6 G  282.1 K      9.3 G    0%</span></div>
</div>
</div>
</div>
<p><span style="font-family:'Times New Roman';font-size:14px;"></span></p>
<span style="font-family:'Times New Roman';font-size:14px;"><br></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（12）-dus</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：显示文件长度概要。该方法以及被舍去，等价于 -du -s 方法。见（11）</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（13）-expunge</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：清空回收站（废纸篓）。Refer to the <a href="http://hadoop.apache.org/docs/r2.7.1/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">HDFS
 Architecture Guide</a> for more information on the Trash feature.</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（14）-find</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：查找满足表达式的文件和文件夹。没有配置path的话，默认的就是全部目录/；如果表达式没有配置，则默认为-print。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法: hadoop fs -find &lt;path&gt; ... &lt;expression&gt; ...</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">-name pattern 不区分大小写，对大小写不敏感</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">-iname pattern 对大小写敏感。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">-print 打印。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">-print0 打印在一行，如下图所示。</span><span style="font-family:'Times New Roman';font-size:14px;"> </span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（15）-get</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：从HDFS上拷贝文件到本地。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -get [-ignorecrc] [-crc] &lt;src&gt; &lt;localdst&gt;</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">示例：</span>
<p><span style="font-family:'Times New Roman';font-size:14px;">Example:</span></p>
<ul><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -get hdfs://hl-virtual-machine:9000/output/hdfs.txt  /home/hl</span></li></ul></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">     <br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（16）getfacl</span></h5>
<div><span style="font-family:'Times New Roman';"><span style="font-size:14px;">（该条命令没有搞明白，欢迎交流，下面贴出官网的说法）</span></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：显示文件和文件夹的ACLs（Access Control Lists）。如果目录有默认的ACL，则显示之。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">-R参数：递归显示。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：</span>
<p><span style="font-family:'Times New Roman';font-size:14px;"> hadoop fs -getfattr [-R] -n name | -d [-e en] &lt;path&gt;</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Displays the extended attribute names and values (if any) for a file or directory.</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">Options:</span></p>
<ul><li><span style="font-family:'Times New Roman';font-size:14px;">-R: Recursively list the attributes for all files and directories.</span></li><li><span style="font-family:'Times New Roman';font-size:14px;">-n name: Dump the named extended attribute value.</span></li><li><span style="font-family:'Times New Roman';font-size:14px;">-d: Dump all extended attribute values associated with pathname.</span></li><li><span style="font-family:'Times New Roman';font-size:14px;">-e <em>encoding</em>: Encode values after retrieving them. Valid encodings are “text”, “hex”, and “base64”. Values encoded as text strings are enclosed in double quotes ("), and values encoded as
 hexadecimal and base64 are prefixed with 0x and 0s, respectively.</span></li><li><span style="font-family:'Times New Roman';font-size:14px;"><em>path</em>: The file or directory.</span></li></ul><div>
<div><span style="font-family:'Times New Roman';font-size:14px;">示例:</span></div>
</div>
<ul><li><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -getfattr -d /file</span></li><li><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -getfattr -R -n user.myAttr /dir</span></li></ul></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(17)-getmerge</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：将源文件地址的内容合并到目标文件上，将覆盖目标文件的内容。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法： hadoop fs -getmerge &lt;src&gt; &lt;localdst&gt;</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(18)-help</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：帮助文档</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(19)-ls</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：查看文件，与linux下ls命令基本类似。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法:hadoop fs -ls [-d] [-h] [-R] [-t] [-S] [-r] [-u] &lt;args&gt;</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">选项：</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">     -d:以纯文件的形式展示目录；</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">taodj@ubuntu:~/testcommand$ hadoop fs -ls -d /</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">drwxrwxrwx   - taodj supergroup          0 2015-11-27 07:00 /</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">     -h:显示为人眼更易识别的单位（原来是字节）。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">     -R:递归展示</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">注：官网上提供的Options比较多，但是测试了，并不行（我所用的版本号不存在问题的），查看help文档，发现help中所述与官网并不符合。怀疑是官网提供信息有误。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（20）-lsr</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：已经被舍去，效果等同于-ls -R</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(21)-mkdir</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：创建文件夹。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">选项：</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">     -p:创建父目录。类似于Unix的mkdir -p命令。<br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">示例：</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">taodj@ubuntu:~/testcommand$ hadoop fs -mkdir /arthur/test1/test2</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">mkdir: `/arthur/test1/test2': No such file or directory</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">taodj@ubuntu:~/testcommand$ hadoop fs -mkdir -p /arthur/test1/test2</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（22）-moveFromLocal</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -moveFromLocal &lt;localsrc&gt; &lt;dst&gt;</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：类似于put命令，不同put命令的是，该操作是移动（意思就是localsrc将被删除）。localsrc应是本地文件。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（23）-moveToLocal</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -moveToLocal [-crc] &lt;src&gt; &lt;dst&gt;</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：该命令尚未实现，显示“Not implemented yet”。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（24）-mv </span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：移动文件。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用： hadoop fs -mv URI [URI ...] &lt;dest&gt;</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">示例：</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">taodj@ubuntu:~/testcommand$ hadoop fs -mv /wordcount.java /testFlatMap.txt /test1</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">(将前面两个文件移动到指定目录下)。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">（25）-put</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法： hadoop fs -put &lt;localsrc&gt; ... &lt;dst&gt;</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：将本地的文件上传（复制）到HDFS是dst目录下。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(26)-rm</span></h5>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -rm [-f] [-r |-R] [-skipTrash] URI [URI ...]</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：删除文件。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">选项：</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">     -f：不显示诊断信息和错误信息（文件不存在，路径不正确均不会显示信息）。</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">taodj@ubuntu:~/testcommand$ hadoop fs -rm -f /111.txt</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;">taodj@ubuntu:~/testcommand$ hadoop fs -rm -f 111.txt</span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<span style="font-family:'Times New Roman';font-size:14px;"></span>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(27)<span style="color:#333333;">-rmdir</span></span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -rmdir [--ignore-fail-on-non-empty] URI [URI ...]</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：删除文件夹。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">选项：</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">     —ignore-fail-on-non-empty：使用它的时候，忽略因文件夹非空删除失败的信息。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(28)-rmr</span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：该方法已经被舍去。和-rm -r效果一样。递归删除。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(29)-setfacl</span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -setfacl [-R] [-b |-k -m |-x &lt;acl_spec&gt; &lt;path&gt;] |[--set &lt;acl_spec&gt; &lt;path&gt;]</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：设置文件和文件夹的ACLs。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">示例：</span></div>
<div>
<ul><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -setfacl -m user:hadoop:rw- /file</span></li><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -setfacl -x user:hadoop /file</span></li><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -setfacl -b /file</span></li><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -setfacl -k /dir</span></li><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -setfacl --set user::rw-,user:hadoop:rw-,group::r--,other::r-- /file</span></li><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -setfacl -R -m user:hadoop:r-x /dir</span></li><li style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -setfacl -m default:user:hadoop:r-x /dir</span></li></ul></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(30)-setrep</span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -setrep [-R] [-w] &lt;numReplicas&gt; &lt;path&gt;</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：<span style="color:#362e2b;">改变文件的目标副本系数，放入REP中。选项-R将递归的改变PATH指定的目录中所有文件的目标副本系数。副本系数需要一定的时间才能达到目标值。选项-w将等待副本系数以与目标值相匹配。</span></span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<h5><span style="color:#333333;"><span style="font-family:'Times New Roman';font-size:14px;">(31)-stat</span></span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;">用法：</span> hadoop fs -stat [format] &lt;path&gt; ...</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;">作用：根据一定格式打印文件/文件夹的统计信息。</span> 文件大小 (%b), 类型 (%F), 所有者所在组 (%g), 名字 (%n), 块大小 (%o), 副本 (%r), 用户名(%u), 修改时间 (%y, %Y)。默认的是%y。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;">示例：</span></span></div>
<div>
<div style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -stat "%F %u:%g %b %y %n" /file</span></div>
</div>
<div><span style="color:#333333;"><span style="font-family:'Times New Roman';font-size:14px;"><br></span></span></div>
<h5><span style="color:#333333;"><span style="font-family:'Times New Roman';font-size:14px;">(32)-tail</span></span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;">用法：</span>hadoop fs -tail [-f] URI</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用:输出文件最后1kb的内容。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">选项：</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">     -f:和unix中tail -f命令类似，当文件内容更新时，输出将会改变，具有实时性。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">示例：用一个场景测试下。首先HDFS的/目录下有文件mpwtest1.txt</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">命令：hadoop fs -tail -f /mpwtest1.txt</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">开启另外一个终端。输入命令： hadoop fs -appendToFile mpwtest2.txt /mpwtest1.txt</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">可以发现 窗口1 有变化。</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(33)-text</span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法：hadoop fs -text &lt;src&gt;</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：将HDFS中文件以文本形式输出（包括zip包，jar包等形式）</span></div>
<div><span style="font-family:'Times New Roman';font-size:14px;">示例：hadoop fs -text /wc.jar</span></div>
<div><br></div>
<div><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<h5><span style="font-family:'Times New Roman';font-size:14px;">(34)-</span><span style="font-family:'Times New Roman';font-size:14px;color:#333333;">touchz</span></h5>
<div><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;">用法：</span> <tt>hadoop fs -touchz URI [URI ...]</tt></span></div>
<div><span style="color:#333333;"><span style="font-family:'Times New Roman';font-size:14px;">作用：创建一个空文件。</span></span></div>
<div><span style="color:#333333;"><span style="font-family:'Times New Roman';font-size:14px;">示例：hadoop fs -touchz /hello.jar</span></span></div>
<div><span style="color:#333333;"><span style="font-family:'Times New Roman';font-size:14px;"><br></span></span></div>
<h5><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;">(35)-</span><span style="color:#333333;">truncate</span></span></h5>
<div>
<div>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法: <tt>hadoop fs -truncate [-w] &lt;length&gt; &lt;paths&gt;</tt></span></div>
</div>
<p><span style="font-family:'Times New Roman';font-size:14px;">作用：将文件按照length进行截取。可以理解成截取[1/length]部分。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">选项：-w：</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">(对于该指令仅仅是个人理解，不一定正确</span><span style="font-family:'Times New Roman';font-size:14px;">)</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">如果其他应用在使用文件时候，-w可以保证使用不会出现问题并且可以正常截断。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">比如两个端口：一个端口执行命令： hadoop fs -tail -f /test1to10.txt</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">另一端口执行：hadoop fs -cat /test1to10.txt。而此时tail的端口并没有受到影响。</span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;"><br></span></p>
<p><span style="font-family:'Times New Roman';font-size:14px;">示例:</span></p>
<ul><li style="color:rgb(51,51,51);"><tt><span style="font-family:'Times New Roman';font-size:14px;">hadoop fs -truncate 55 /user/hadoop/file1 /user/hadoop/file2</span></tt></li><li><span style="font-family:'Times New Roman';font-size:14px;"><tt><span style="color:#333333;">hadoop fs -truncate -w 127 hdfs://n</span></tt>n1.example.com/user/hadoop/file1</span></li></ul></div>
<div style="color:rgb(51,51,51);"><span style="font-family:'Times New Roman';font-size:14px;"><br></span></div>
<h5><span style="font-family:'Times New Roman';font-size:14px;"><span style="color:#333333;">(36)-</span><span style="color:#333333;">usage</span></span></h5>
<div>
<div><span style="font-family:'Times New Roman';font-size:14px;">用法: <tt>hadoop fs -usage command</tt></span></div>
<div>
<div><span style="font-family:'Times New Roman';font-size:14px;">作用：返回命令的help信息。</span></div>
</div>
</div>
            </div>
                </div>
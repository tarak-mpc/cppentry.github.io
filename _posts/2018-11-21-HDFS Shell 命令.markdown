---
layout:     post
title:      HDFS Shell 命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:14px;">hadoop fs [GENERIC_OPTIONS][COMMAND_OPTIONS]</span></p>
<p><span style="font-size:14px;color:#000000;">1. </span><span style="font-size:14px;">将本地文件上传到HDFS上
</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs -copyFromLocal /local/data/hdfs/data</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs –put /local/data /hdfs/data </span>
</p>
<p><span style="font-size:14px;">2.  将HDFS上的文件传到本地文件</span></p>
<p><span style="font-size:14px;color:#000000;">   </span><span style="font-size:14px;">bin/hadoop fs -copyToLocal/hdfs/data /local/data</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs –get /hdfs/data /local/data</span></p>
<p><span style="font-size:14px;">3. 创建目录</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs -mkdir /hdfs/data</span></p>
<p><span style="font-size:14px;">4. 查看目录</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs –ls /hdfs</span></p>
<p><span style="font-size:14px;">5. 查看文件内容及其子文件</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs –ls [-R]/hdfs</span></p>
<p><span style="font-size:14px;">6. 查看文件内容</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs –cat /hdfs/data</span></p>
<p><span style="font-size:14px;">7. 显示目录中所有文件的大小或当指定一个文件时，显示此文件的大小。</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs –du /user/hadoop/dir1/user/hadoop/file1</span></p>
<p><span style="font-size:14px;">8. 显示文件的大小</span></p>
<p><span style="font-size:14px;">   bin/hadoop fs –dus /user/hadoop/file</span></p>
<p><span style="font-size:14px;">9. 将文件从源路径移动到目标路径</span></p>
<p><span style="font-size:14px;">   bin/hadoop mv /user/hadoop/file1/user/hadoop/file2</span></p>
<p><span style="font-size:14px;">10.删除文件/目录 </span></p>
<p><span style="font-size:14px;">    bin/hadoop fs –rm /hdfs/data</span></p>
<p><span style="font-size:14px;">   删除目录及其子目录</span></p>
<p><span style="font-size:14px;">    bin/hadoop fs –rm [-R]/hdfs/</span></p>
<p><span style="font-size:14px;">11.清空回收站</span></p>
<p><span style="font-size:14px;">    bin/hadoop fs –expunge</span></p>
<p><span style="font-size:14px;">12.改变一个文件的副本系数。-R选项用于递归改变目录下所有文件的副本系数。</span></p>
<p><span style="font-size:14px;">    bin/hadoop fs -setrep-w 3 -R /user/hadoop/dir1</span></p>
<p><span style="font-size:14px;">13.改变文件的权限。</span></p>
<p><span style="font-size:14px;">    bin/hadoopfs -chmod [-R] /user/hadoop</span></p>
<p><span style="font-size:14px;">使用-R将使改变在目录结构下递归进行。</span></p>
<p><span style="font-size:14px;">14.改变文件所属的组。</span></p>
<p><span style="font-size:14px;">    <span style="font-size:14px;">bin/</span>hadoop fs -chmod [-R]  graoupname</span></p>
<p><span style="font-size:14px;">15.改变文件的拥有者。</span></p>
<p><span style="font-size:14px;">    <span style="font-size:14px;">bin/</span>hadoop fs -chown [-R] [qwean][:[graoup]]URI</span></p>
<p><span style="font-size:14px;">16.返回指定路径的统计信息。</span></p>
<p><span style="font-size:14px;">    bin/hadoop fs -stat /user/hadoop/</span></p>
<p><span style="font-size:14px;">17.将文件尾部1K字节的内容输出到stdout。</span></p>
<p><span style="font-size:14px;">    bin/hadoop fs -tail /user/hadoop/data</span></p>
<p><span style="font-size:14px;">18.检查文件</span></p>
<p><span style="font-size:14px;">    <span style="font-size:14px;">bin/</span>hadoop fs -test -[ezd]URI</span></p>
<p><span style="font-size:14px;">选项:</span></p>
<p><span style="font-size:14px;">-e 检查文件是否存在。如果存在则返回0。 </span></p>
<p><span style="font-size:14px;">-z 检查文件是否是0字节。如果是则返回0。 </span></p>
<p><span style="font-size:14px;">-d 如果路径是个目录，则返回1，否则返回0。</span></p>
<p><span style="font-size:14px;">19.将源文件输出为文本格式</span></p>
<p><span style="font-size:14px;">    bin/hadoopfs -text &lt;src&gt;</span></p>
<p><span style="font-size:14px;">20.创建一个空文件</span></p>
<p><span style="font-size:14px;">    bin/hadoopfs -touchz /user/hadoop/file </span>
</p>
<p align="center"><span style="font-size:14px;">HDFS Shell 所有命令总结</span></p>
<p align="center"><img src="https://img-blog.csdn.net/20150522110909215?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NDYwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p align="center"><img src="https://img-blog.csdn.net/20150522110758153?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMjk2NDYwMA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p align="center"></p>
<p></p>
            </div>
                </div>
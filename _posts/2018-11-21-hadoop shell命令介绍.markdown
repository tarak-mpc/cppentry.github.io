---
layout:     post
title:      hadoop shell命令介绍
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<span style="color:#000000;font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="font-weight:700;">Hadoop 文件系统shell使用命令介绍，基本上所有的命令与linux命令相差不大，</span>下面为我的hadoop系统文件夹<br>
查看命令：hadoop fs -ls /home/hadoop/<br>
drwxr-xr-x   - hadoop supergroup          0 2013-11-30 17:51 /home/hadoop/dir<br>
drwxr-xr-x   - hadoop supergroup          0 2013-11-30 17:48 /home/hadoop/input<br>
-rw-r--r--   1 hadoop supergroup         64 2013-11-30 17:50 /home/hadoop/ouput<br>
drwxr-xr-x   - hadoop supergroup          0 2013-11-29 22:50 /home/hadoop/output<br>
drwxr-xr-x   - hadoop supergroup          0 2013-11-29 22:50 /home/hadoop/tmp<br><br>
查看所有命令帮助：hadoop fs --help<br><br><span style="font-weight:700;">cat</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -cat URI [URI …]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">查看fs文件内容</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -cat /home/hadoop/input/content.txt</span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;"><br></span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;">chgrp</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -chgrp [-R] GROUP URI [URI …]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Change group association of files. With -R, make the change recursively through
 the directory structure. The user must be the owner of files, or else a super-user. Additional information is in the <a href="http://hadoop.apache.org/docs/r0.18.3/hdfs_permissions_guide.html" rel="nofollow" style="color:rgb(51,102,153);">Permissions
 User Guide</a>.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
chmod</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -chmod [-R] &lt;MODE[,MODE]... | OCTALMODE&gt; URI [URI …]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Change the permissions of files. With -R, make the change recursively through the
 directory structure. The user must be the owner of the file, or else a super-user. Additional information is in the <a href="http://hadoop.apache.org/docs/r0.18.3/hdfs_permissions_guide.html" rel="nofollow" style="color:rgb(51,102,153);">Permissions
 User Guide</a>.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
chown</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI ]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Change the owner of files. With -R, make the change recursively through the directory
 structure. The user must be a super-user. Additional information is in the <a href="http://hadoop.apache.org/docs/r0.18.3/hdfs_permissions_guide.html" rel="nofollow" style="color:rgb(51,102,153);">Permissions User Guide</a>.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
copyFromLocal</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -copyFromLocal &lt;localsrc&gt; URI</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:'Courier New', Courier, monospace;"><span style="font-size:14px;"><span style="color:#000000;">hadoop fs -copyFromLocal /home/hadoop/address /home/hadoop/input</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Similar to <a href="http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html#putlink" rel="nofollow" style="color:rgb(51,102,153);"><span style="font-weight:700;">put</span></a> command,
 except that the source is restricted to a local file reference.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
copyToLocal</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -copyToLocal [-ignore<span id="3_nwp"><a id="3_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-5603-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=crc&amp;k0=crc&amp;kdi0=8&amp;k1=only&amp;kdi1=1&amp;k2=shell&amp;kdi2=8&amp;k3=beijing&amp;kdi3=8&amp;k4=well&amp;kdi4=8&amp;sid=ada966655a11d638&amp;ch=0&amp;tu=u1692056&amp;jk=aaf9f46ce702dcda&amp;cf=29&amp;fv=15&amp;stid=9&amp;urlid=0&amp;luki=1&amp;seller_id=1&amp;di=128" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;"><span style="color:rgb(0,0,255);">crc</span></a></span>]
 [-crc] URI &lt;localdst&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:'Courier New', Courier, monospace;"><span style="font-size:14px;"><span style="color:#000000;">hadoop fs -copyToLocal /home/hadoop/input/content.txt /home/hadoop/mylocal</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Similar to <a href="http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html#getlink" rel="nofollow" style="color:rgb(51,102,153);"><span style="font-weight:700;">get</span></a> command,
 except that the destination is restricted to a local file reference.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
cp</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -cp URI [URI …] &lt;dest&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Copy files from source to destination. This command allows multiple sources as <span id="2_nwp"><a id="2_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-5603-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=well&amp;k0=crc&amp;kdi0=8&amp;k1=only&amp;kdi1=1&amp;k2=shell&amp;kdi2=8&amp;k3=beijing&amp;kdi3=8&amp;k4=well&amp;kdi4=8&amp;sid=ada966655a11d638&amp;ch=0&amp;tu=u1692056&amp;jk=aaf9f46ce702dcda&amp;cf=29&amp;fv=15&amp;stid=9&amp;urlid=0&amp;luki=5&amp;seller_id=1&amp;di=128" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;"><span style="color:rgb(0,0,255);">well</span></a></span> in
 which case the destination must be a directory. <br>
例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -cp /home/hadoop/input/content.txt /home/hadoop/ouput</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -cp /home/hadoop/input/address /home/hadoop/ouput /home/hadoop/dir<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
du</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -du URI [URI …]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:14px;">Displays aggregate length of files contained in the directory or the length of
 a file in case its just a file.</span><br><span style="font-size:14px;">例子:</span><br><span style="font-size:14px;">hadoop fs -du /home/hadoop/dir /home/hadoop/input</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">输出结果：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;"></span></span></span>
<div align="left">Found 2 items</div>
<div align="left">65          hdfs://localhost:9000/home/hadoop/dir/address</div>
<div align="left">64          hdfs://localhost:9000/home/hadoop/dir/ouput</div>
<div align="left">Found 2 items</div>
<div align="left">65          hdfs://localhost:9000/home/hadoop/input/address</div>
<div align="left">64          hdfs://localhost:9000/home/hadoop/input/content.txt</div>
</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;"><br>
Exit Code:<br>
Returns 0 on success and -1 on error. <br></span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
dus</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -dus &lt;args&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;">hadoop fs -dus /home/hadoop/dir</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;"><br></span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;">输出：</span></span></span><span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;">hdfs://localhost:9000/home/hadoop/dir 
       129</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Displays a summary of file lengths.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
expunge 手动清除fs回收站</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -expunge</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Empty the Trash. Refer to <a href="http://hadoop.apache.org/docs/r0.18.3/hdfs_design.html" rel="nofollow" style="color:rgb(51,102,153);">HDFS
 Design</a> for more information on Trash feature.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
get</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -get [-ignorecrc] [-crc] &lt;src&gt; &lt;localdst&gt; <br></span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Copy files to the local file system. Files that fail the CRC check may be copied
 with the -ignore<span id="1_nwp"><a id="1_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-5603-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=crc&amp;k0=crc&amp;kdi0=8&amp;k1=only&amp;kdi1=1&amp;k2=shell&amp;kdi2=8&amp;k3=beijing&amp;kdi3=8&amp;k4=well&amp;kdi4=8&amp;sid=ada966655a11d638&amp;ch=0&amp;tu=u1692056&amp;jk=aaf9f46ce702dcda&amp;cf=29&amp;fv=15&amp;stid=9&amp;urlid=0&amp;luki=1&amp;seller_id=1&amp;di=128" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;"><span style="color:rgb(0,0,255);">crc</span></a></span> option.
 Files and CRCs may be copied using the -crc option.</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -get /home/hadoop/input/content.txt /home/hadoop/mylocal/</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -get hdfs://nn.example.com/user/hadoop/file localfile<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
getmerge</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -getmerge &lt;src&gt; &lt;localdst&gt; [addnl]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Takes a source directory and a destination file as input and concatenates files
 in src into the destination local file. Optionally addnl can be set to enable adding a newline character at the end of each file.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
ls</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -ls &lt;args&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:14px;">For a file returns stat on the file with the following format:</span><br><span style="font-size:14px;">filename &lt;number of replicas&gt; filesize modification_date modification_time permissions userid groupid </span><br><span style="font-size:14px;">For a directory it returns list of its direct children as in unix. A directory is listed as: </span><br><span style="font-size:14px;">dirname &lt;dir&gt; modification_time modification_time permissions userid groupid </span><br><span style="font-size:14px;">例子:</span><br><span style="font-size:14px;">hadoop fs -ls /home/hadoop/input/ /home/hadoop/ouput/</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:14px;">输出：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:14px;"></span></span></span>
<div align="left">Found 2 items</div>
<div align="left">-rw-r--r--   1 hadoop supergroup         65 2013-11-30 17:48 /home/hadoop/input/address</div>
<div align="left">-rw-r--r--   1 hadoop supergroup         64 2013-11-29 22:48 /home/hadoop/input/content.txt</div>
<div align="left">Found 1 items</div>
<div align="left">-rw-r--r--   1 hadoop supergroup         64 2013-11-30 17:50 /home/hadoop/ouput</div>
Exit Code:<br><span style="font-size:14px;">Returns 0 on success and -1 on error. </span><br></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
lsr</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -lsr &lt;args&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:14px;">hadoop fs -lsr /home/hadoop/input/ /home/hadoop/ouput/</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:14px;"><br></span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:14px;">输出：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:14px;"></span></span></span>
<div align="left">-rw-r--r--   1 hadoop supergroup         65 2013-11-30 17:48 /home/hadoop/input/address</div>
<div align="left">-rw-r--r--   1 hadoop supergroup         64 2013-11-29 22:48 /home/hadoop/input/content.txt</div>
<div align="left">-rw-r--r--   1 hadoop supergroup         64 2013-11-30 17:50 /home/hadoop/ouput</div>
Recursive version of ls. Similar to Unix ls -R.</div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
mkdir</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -mkdir &lt;paths&gt; <br></span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Takes path uri's as argument and creates directories. The behavior is much like
 unix mkdir -p creating parent directories along the path.</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -mkdir /home/hadoop/input/ /home/hadoop/ouput/</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -mkdir hdfs://nn1.example.com/user/hadoop/dir hdfs://nn2.example.com/user/hadoop/dir<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
movefromLocal</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: dfs -moveFromLocal &lt;src&gt; &lt;dst&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Displays a "not implemented" message.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
mv</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -mv URI [URI …] &lt;dest&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Moves files from source to destination. This command allows multiple sources as <span id="0_nwp"><a id="0_nwl" href="http://cpro.baidu.com/cpro/ui/uijs.php?rs=1&amp;u=http%3A%2F%2Fwww.aboutyun.com%2Fthread-5603-1-1.html&amp;p=baidu&amp;c=news&amp;n=10&amp;t=tpclicked3_hc&amp;q=92051019_cpr&amp;k=well&amp;k0=crc&amp;kdi0=8&amp;k1=only&amp;kdi1=1&amp;k2=shell&amp;kdi2=8&amp;k3=beijing&amp;kdi3=8&amp;k4=well&amp;kdi4=8&amp;sid=ada966655a11d638&amp;ch=0&amp;tu=u1692056&amp;jk=aaf9f46ce702dcda&amp;cf=29&amp;fv=15&amp;stid=9&amp;urlid=0&amp;luki=5&amp;seller_id=1&amp;di=128" rel="nofollow" style="color:rgb(51,102,153);text-decoration:none;"><span style="color:rgb(0,0,255);">well</span></a></span> in
 which case the destination needs to be a directory. Moving files across filesystems is not permitted. <br>
例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -mv /home/hadoop/input/lzw /home/hadoop/output</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -mv hdfs://nn.example.com/file1 hdfs://nn.example.com/file2 hdfs://nn.example.com/file3 hdfs://nn.example.com/dir1<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
put</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -put &lt;localsrc&gt; ... &lt;dst&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Copy single src, or multiple srcs from local file system to the destination filesystem.
 Also reads input from stdin and writes to destination filesystem.<br></span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -put /home/hadoop/lzw /home/hadoop/input</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -put /home/hadoop/lzw /home/hadoop/lzw1 /home/hadoop/input</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -put localfile hdfs://nn.example.com/hadoop/hadoopfile</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -put - hdfs://nn.example.com/hadoop/hadoopfile <br>
Reads the input from stdin.<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
rm 删除文件</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -rm URI [URI …]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Delete files specified as args. Only deletes non empty directory and files. Refer
 to rmr for recursive deletes.<br>
例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -rm /home/hadoop/output/lzw<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
rmr 删除文件夹</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -rmr URI [URI …]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Recursive version of delete.<br>
例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -rmr /home/hadoop/output</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -rmr hdfs://nn.example.com/user/hadoop/dir<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
setrep</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -setrep [-R] &lt;path&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Changes the replication factor of a file. -R option is for recursively increasing
 the replication factor of files within a directory.</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -setrep -w 3 -R /user/hadoop/dir1<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
stat</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -stat URI [URI …]</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Returns the stat information on the path.</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -stat /home/hadoop/input</span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">输出：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;">2013-11-30 16:00:27</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:<br>
Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
tail</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -tail [-f] URI</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Displays last kilobyte of the file to stdout. -f option can be used as in Unix.</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -tail pathname<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code: <br>
Returns 0 on success and -1 on error.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
test</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -test -[ezd] URI</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Options: <br>
-e check to see if the file exists. Return 0 if true. <br>
-z check to see if the file is zero length. Return 0 if true <br>
-d check return 1 if the path is directory else return 0. <br></span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -test -d /home/hadoop/input</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -test -e /home/hadoop/input</span></li><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -test -z /home/hadoop/input</span><br></li></ul><span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
text 查看文件内容，跟cat基本相似</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -text &lt;src&gt;</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;">hadoop fs -text /home/hadoop/input/address</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;">输出：</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="font-size:14px;"><span style="color:#000000;"></span></span></span>
<div align="left">addressID        addressname</div>
<div align="left">1        Beijing</div>
<div align="left">2        Guangzhou</div>
<div align="left">3        Shenzhen</div>
<div align="left">4        Xian</div>
</div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Takes a source file and outputs the file in text format. The allowed formats are
 zip and TextRecordInputStream.</span></span></span></div>
<span style="font-weight:700;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><span style="color:#000000;"><br>
touchz 创建空文件</span></span>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Usage: hadoop fs -touchz URI [URI …] <br></span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Create a file of zero length.</span></span></span></div>
<div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">例子:</span></span></span></div>
<ul style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;"><li style="list-style:disc;">
<span style="color:#000000;">hadoop fs -touchz /home/hadoop/input/lzw<br></span><br></li></ul><div align="left" style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;line-height:21px;">
<span style="font-family:Verdana, Helvetica, sans-serif;"><span style="color:#000000;"><span style="font-size:16px;">Exit Code:<br>
Returns 0 on success and -1 on error.</span></span></span></div>
            </div>
                </div>
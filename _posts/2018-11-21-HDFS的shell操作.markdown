---
layout:     post
title:      HDFS的shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq351564759/article/details/53374056				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:#FF0000;">ls查看hdfs的list</span><br></p>
<p><span style="color:#FF0000;">[root@master sbin]# hadoop fs -ls hdfs://master:9000</span></p>
<p>ls: `hdfs://master:9000': No such file or directory</p>
<p><br></p>
<span style="color:#FF0000;">[root@master sbin]# hadoop fs -ls hdfs://master:9000/</span><br>
Found 4 items<br>
-rw-r--r--   1 root supergroup  294307840 2016-11-27 00:11 hdfs://master:9000/jdk<br>
drwx------   - root supergroup          0 2016-11-27 00:47 hdfs://master:9000/tmp<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 00:53 hdfs://master:9000/wcout<br><p>-rw-r--r--   1 root supergroup         56 2016-11-27 00:32 hdfs://master:9000/words</p>
<p><span style="color:#FF0000;">[root@master sbin]# hadoop fs -ls /</span><br>
Found 4 items<br>
-rw-r--r--   1 root supergroup  294307840 2016-11-27 00:11 /jdk<br>
drwx------   - root supergroup          0 2016-11-27 00:47 /tmp<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 00:53 /wcout<br>
-rw-r--r--   1 root supergroup         56 2016-11-27 00:32 /words</p>
<p>这里ｄｒｗｘ中的ｄ代表是目录。。。ｗｃｏｕｔ是一个文件夹，下面还有文件<br></p>
<p><br></p>
<p><span style="color:#FF0000;">[root@master sbin]# hadoop fs -copyFromLocal /root/a /cfl</span></p>
<p>类似put上传命令。。。从本地上传a文件到hdfs根目录下，并且重命名为cfl</p>
<p><span style="color:#FF0000;">[root@master sbin]# hadoop fs -get /cfl /root/test1</span></p>
<p>get命令就是从hdfs下载。。从根目录下载cfl到本地根目录下root并且重命名为test1</p>
<p>[root@master ~]# cd /<br>
[root@master /]# ls<br>
bin   dev  home  lib64   media  opt   root  sbin  sys  usr<br>
boot  etc  lib   master  mnt    proc  run   srv   tmp  var<br>
[root@master /]# cd root<br>
[root@master ~]# ls<br>
a                Documents            jdk-7u60-linux-x64.tar  Public     Videos<br>
anaconda-ks.cfg  Downloads            Music                   Templates<br>
Desktop          hadoop-2.5.2.tar.gz  Pictures                test1</p>
<p>[root@master ~]# hadoop fs -ls -R<br>
ls: `.': No such file or directory</p>
<p>命令虽然对，但是没有指明要查看什么目录下的。。。<br></p>
<p>[root@master ~]# hadoop fs -ls -R/<br>
-ls: Illegal option -R/<br>
Usage: hadoop fs [generic options] -ls [-d] [-h] [-R] [&lt;path&gt; ...]</p>
<p>R和/之间要刚跟一个空格。。。毕竟/是一个目录啊。。。<br></p>
<p>[root@master ~]# hadoop fs -ls -R /<br>
-rw-r--r--   1 root supergroup          3 2016-11-27 17:53 /cfl<br>
-rw-r--r--   1 root supergroup  294307840 2016-11-27 00:11 /jdk<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 00:53 /wcout<br>
-rw-r--r--   1 root supergroup          0 2016-11-27 00:53 /wcout/_SUCCESS<br>
-rw-r--r--   1 root supergroup         38 2016-11-27 00:53 /wcout/part-r-00000<br>
-rw-r--r--   1 root supergroup         56 2016-11-27 00:32 /words</p>
<p>[root@master ~]# hadoop fs -ls -R /。。。。。递归查看／下面的文件</p>
<p><span style="color:#FF0000;">[root@master ~]# hadoop fs -ls -R -h /</span><br>
-rw-r--r--   1 root supergroup          3 2016-11-27 17:53 /cfl<br>
-rw-r--r--   1 root supergroup    280.7 M 2016-11-27 00:11 /jdk<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 00:53 /wcout<br>
-rw-r--r--   1 root supergroup          0 2016-11-27 00:53 /wcout/_SUCCESS<br>
-rw-r--r--   1 root supergroup         38 2016-11-27 00:53 /wcout/part-r-00000<br>
-rw-r--r--   1 root supergroup         56 2016-11-27 00:32 /words</p>
<p>这个－ｈ命令把文件大小变成人类可读了</p>
<p><span style="color:#FF0000;">[root@master ~]# hadoop fs -mkdir /master</span></p>
<p>在ｈｄｆｓ上个创建一个新的文件夹ｍａｓｔｅｒ</p>
<p><span style="color:#FF0000;">[root@master ~]# hadoop fs -rm /jdk</span><br>
16/11/27 18:18:36 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0 minutes, Emptier interval = 0 minutes.<br>
Deleted /jdk</p>
<p>删除文件ｊｄｋ<br></p>
<p>[root@master ~]# hadoop fs -ls /<br>
Found 4 items<br>
-rw-r--r--   1 root supergroup          3 2016-11-27 17:53 /cfl<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 18:16 /master<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 00:53 /wcout<br>
-rw-r--r--   1 root supergroup         56 2016-11-27 00:32 /words</p>
<p>现在我们要删掉一个目录。。。</p>
<p>[root@master ~]# hadoop fs -rm /master<br>
rm: `/master': Is a directory</p>
<p>告诉我们ｍａｓｔｅｒ不能删除掉，因为他是一个目录</p>
<p><span style="color:#FF0000;">[root@master ~]# hadoop fs -rmr /master</span><br>
rmr: DEPRECATED: Please use 'rm -r' instead.<br>
16/11/27 18:23:26 INFO fs.TrashPolicyDefault: Namenode trash configuration: Deletion interval = 0 minutes, Emptier interval = 0 minutes.<br>
Deleted /master</p>
<p>修改文件执行权限</p>
<p>[root@master ~]# hadoop fs -ls /<br>
Found 3 items<br>
-rw-r--r--   1 root supergroup          3 2016-11-27 17:53 /cfl<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 00:53 /wcout<br>
-rw-r--r--   1 root supergroup         56 2016-11-27 00:32 /words</p>
<p>[root@master ~]# hadoop fs -chmod a+x /cfl<br>
[root@master ~]# hadoop fs -ls /<br>
Found 3 items<br>
-rwxr-xr-x   1 root supergroup          3 2016-11-27 17:53 /cfl<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 00:53 /wcout<br>
-rw-r--r--   1 root supergroup         56 2016-11-27 00:32 /words<br></p>
<p>以上命令可以用如下替换</p>
<p><span style="color:#FF0000;">[root@master ~]# hdfs dfs -ls /</span><br>
Found 3 items<br>
-rwxr-xr-x   1 root supergroup          3 2016-11-27 17:53 /cfl<br>
drwxr-xr-x   - root supergroup          0 2016-11-27 00:53 /wcout<br>
-rw-r--r--   1 root supergroup         56 2016-11-27 00:32 /words<br></p>
<p><br></p>
<p><br></p>
            </div>
                </div>
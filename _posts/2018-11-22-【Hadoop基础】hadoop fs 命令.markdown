---
layout:     post
title:      【Hadoop基础】hadoop fs 命令
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="article_title" style="font-size:20px;line-height:30px;font-family:'Microsoft YaHei';">
<h1 style="display:inline;font-weight:normal;font-size:20px;vertical-align:middle;">
<span class="link_title"><a href="http://blog.csdn.net/wuwenxiang91322/article/details/22166423" rel="nofollow" style="color:rgb(51,51,51);text-decoration:none;">【Hadoop基础】hadoop fs 命令</a></span></h1>
</div>
<div class="article_manage clearfix" style="color:rgb(153,153,153);line-height:22px;font-family:Arial;text-align:right;border-bottom-style:solid;border-bottom-width:1px;border-bottom-color:rgb(237,237,237);overflow:hidden;">
<div class="article_r"><br></div>
</div>
<div id="article_content" class="article_content" style="font-size:14px;line-height:26px;font-family:Arial;">
<p>
<span style="font-family:SimSun;">1，hadoop fs –fs [local | &lt;file system URI&gt;]：声明hadoop使用的文件系统，如果不声明的话，使用当前配置文件配置的，按如下顺序查找：hadoop jar里的hadoop-default.xml-&gt;$HADOOP_CONF_DIR下的hadoop-default.xml-&gt;$HADOOP_CONF_DIR下的hadoop-site.xml。使用local代表将本地文件系统作为hadoop的DFS。如果传递uri做参数，那么就是特定的文件系统作为DFS。</span></p>
<p>
<span style="font-family:SimSun;">2，hadoop fs –ls &lt;path&gt;：等同于本地系统的ls，列出在指定目录下的文件内容，支持pattern匹配。输出格式如filename(full path)   &lt;r n&gt;  size.其中n代表replica的个数，size代表大小（单位bytes）。</span></p>
<p>
<span style="font-family:SimSun;">3，hadoop fs –lsr &lt;path&gt;：递归列出匹配pattern的文件信息，类似ls，只不过递归列出所有子目录信息。</span></p>
<p>
<span style="font-family:SimSun;">4，hadoop fs –du &lt;path&gt;：列出匹配pattern的指定的文件系统空间总量（单位bytes），等价于unix下的针对目录的du –sb &lt;path&gt;/*和针对文件的du –b &lt;path&gt; ，输出格式如name(full path)  size(in bytes)。</span></p>
<p>
<span style="font-family:SimSun;">5，hadoop fs –dus &lt;path&gt;：等价于-du，输出格式也相同，只不过等价于unix的du -sb。</span></p>
<p>
<span style="font-family:SimSun;">6，hadoop fs –mv &lt;src&gt; &lt;dst&gt;：将制定格式的文件 move到指定的目标位置。当src为多个文件时，dst必须是个目录。</span></p>
<p>
<span style="font-family:SimSun;">7，hadoop fs –cp &lt;src&gt; &lt;dst&gt;：拷贝文件到目标位置，当src为多个文件时，dst必须是个目录。</span></p>
<p>
<span style="font-family:SimSun;">8，hadoop fs –rm [-skipTrash] &lt;src&gt;：删除匹配pattern的指定文件，等价于unix下的rm &lt;src&gt;。</span></p>
<p>
<span style="font-family:SimSun;">9，hadoop fs –rmr [skipTrash] &lt;src&gt;：递归删掉所有的文件和目录，等价于unix下的rm –rf &lt;src&gt;。</span></p>
<p>
<span style="font-family:SimSun;">10，hadoop fs –rmi [skipTrash] &lt;src&gt;：等价于unix的rm –rfi &lt;src&gt;。</span></p>
<p>
<span style="font-family:SimSun;">11，hadoop fs –put &lt;localsrc&gt; … &lt;dst&gt;：从本地系统拷贝文件到DFS。</span></p>
<p>
<span style="font-family:SimSun;">12，hadoop fs –copyFromLocal &lt;localsrc&gt; … &lt;dst&gt;：等价于-put。</span></p>
<p>
<span style="font-family:SimSun;">13，hadoop fs –moveFromLocal &lt;localsrc&gt; … &lt;dst&gt;：等同于-put，只不过源文件在拷贝后被删除。</span></p>
<p>
<span style="font-family:SimSun;">14，hadoop fs –get [-ignoreCrc] [-crc] &lt;src&gt; &lt;localdst&gt;：从DFS拷贝文件到本地文件系统，文件匹配pattern，若是多个文件，则dst必须是目录。</span></p>
<p>
<span style="font-family:SimSun;">15，hadoop fs –getmerge &lt;src&gt; &lt;localdst&gt;：顾名思义，从DFS拷贝多个文件、合并排序为一个文件到本地文件系统。</span></p>
<p>
<span style="font-family:SimSun;">16，hadoop fs –cat &lt;src&gt;：展示文件内容。</span></p>
<p>
<span style="font-family:SimSun;">17，hadoop fs –copyToLocal [-ignoreCrc] [-crc] &lt;src&gt; &lt;localdst&gt;：等价于-get。</span></p>
<p>
<span style="font-family:SimSun;">18，hadoop fs –mkdir &lt;path&gt;：在指定位置创建目录。</span></p>
<p>
<span style="font-family:SimSun;">19，hadoop fs –setrep [-R] [-w] &lt;rep&gt; &lt;path/file&gt;：设置文件的备份级别，-R标志控制是否递归设置子目录及文件。</span></p>
<p>
<span style="font-family:SimSun;">20，hadoop fs –chmod [-R] &lt;MODE[,MODE]…|OCTALMODE&gt; PATH…：修改文件的权限，-R标记递归修改。MODE为a+r,g-w,+rwx等，OCTALMODE为755这样。</span></p>
<p>
<span style="font-family:SimSun;">21，hadoop fs -chown [-R] [OWNER][:[GROUP]] PATH…：修改文件的所有者和组。-R表示递归。</span></p>
<p>
<span style="font-family:SimSun;">22，hadoop fs -chgrp [-R] GROUP PATH…：等价于-chown … :GROUP …。</span></p>
<p>
<span style="font-family:SimSun;">23，hadoop fs –count[-q] &lt;path&gt;：计数文件个数及所占空间的详情，输出表格的列的含义依次为：DIR_COUNT,FILE_COUNT,CONTENT_SIZE,FILE_NAME或者如果加了-q的话，还会列出QUOTA,REMAINING_QUOTA,SPACE_QUOTA,REMAINING_SPACE_QUOTA。</span></p>
<p>
<span style="font-family:SimSun;color:rgb(255,0,0);">转载路径：</span><a href="http://www.blogjava.net/changedi/archive/2013/08/12/402696.html" rel="nofollow" style="color:rgb(255,153,0);text-decoration:none;"><span style="font-family:SimSun;color:rgb(255,0,0);">http://www.blogjava.net/changedi/archive/2013/08/12/402696.html</span></a></p>
</div>
            </div>
                </div>
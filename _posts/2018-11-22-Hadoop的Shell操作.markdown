---
layout:     post
title:      Hadoop的Shell操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lzm1340458776/article/details/37991809				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong><span style="color:#ff0000;">Hadoop shell命令操作，输入hadoop回车后可以发现如图所示：</span></strong></p>
<p><img src="https://img-blog.csdn.net/20150104145413585" alt=""><br></p>
<p><strong><span style="color:#ff0000;">常用的命令有：</span></strong></p>
<p><span style="color:#ff0000;">hadoop namenode -format</span> 这个是启动Hadoop之前格式化文件系统时使用的命令。</p>
<p><span style="color:#ff0000;">hadoop dfsadmin </span>这是Hadoop的管理命令，我们输入hadoop dfsadmin回车后可以看到详细命令如下图：</p>
<p><img src="https://img-blog.csdn.net/20150104145413585" alt=""><br></p>
<p>hadoop dfsadmin常用的命令有：</p>
<p>1)：<span style="color:#ff0000;">hadoop dfsadmin -report</span> 查看Hadoop的运行状态</p>
<p>2)：<span style="color:#ff0000;">hadoop dfsadmin -safemode</span> enter | leave | get | wait 设置Hadoop的安全模式(安全模式下不允许对文件进行上传和修改操作)</p>
<p>3)：<span style="color:#ff0000;">hadoop dfsadmin -refreshNodes</span> 刷新节点(重新读取hosts和exclude文件，使新的结点和退出集群的节点能够被NameNode重新识别，这个命令在新增节点或注销节点时使用)</p>
<p>4)：<span style="color:#ff0000;">hadoop dfsadmin -help</span> 查看命令帮助，如：hadoop dfsadmin -help safemode。</p>
<p><br></p>
<p><br></p>
<p>hadoop fsck 检查hadoop文件系统的健康状态；查看文件所在的数据块；删除坏掉的块；查找缺失的块，输入hadoop fsck 如下：</p>
<p><img src="https://img-blog.csdn.net/20150104150424716" alt=""><br></p>
<p>hadoop fsck命令的例子：</p>
<p><span style="color:#ff0000;">hadoop fsck /lavimer/liao.txt -file -blocks</span> 查看文件所在块和健康状态，结果如下：</p>
<p><img src="https://img-blog.csdn.net/20150104150618500" alt=""><br></p>
<p><br></p>
<p><span style="color:#ff0000;">hadoop balancer</span> 磁盘均衡。</p>
<p><br></p>
<p><span style="color:#ff0000;">hadoop jar</span> 运行jar包，例如：hadoop jar liao.jar 参数1 参数2</p>
<p><br></p>
<p><span style="color:#ff0000;">hadoop archive </span>文件归档，这个命令非常有用，hadoop可以用这个命令来解决处理众多小文件的问题。</p>
<p>例如：<span style="color:#ff0000;">hadoop archive -archiveName liao.har -p /usr /</span>   把/usr目录下的所有文件归档打包到/目录下(这是hadoop的根目录)</p>
<p>查看归档后har包的内部结构，使用 <span style="color:#ff0000;">hadoop fs -lsr /liao.har</span>命令，如图：</p>
<p><img src="https://img-blog.csdn.net/20150104151249611" alt=""><br></p>
<p>如果要查看归档后的文件更详细的内容，可以使用：<span style="color:#ff0000;">hadoop fs -lsr har:///liao.har</span>(三个斜杠)，结果如下：</p>
<p><img src="https://img-blog.csdn.net/20150104151423554" alt=""><br></p>
<p><br></p>
<p><strong><span style="color:#ff0000;">下面介绍hadoop中最重要的一种shell命令，即对HDFS的shell操作命令！</span></strong></p>
<p><br></p>
<p>既然HDFS是存取数据的分布式文件系统，那么对HDFS的操作，就是对文件系统的基本操作，比如文件的创建、修改、删除、修改权限等，文件夹的创建、删除、重命名等。对HDFS的操作命令类似于Linux的Shell对文件的操作，如：ls、mkdir、rm等。</p>
<p>我们在操作HDFS的时候一定要确保Hadoop是正常运行的，可以使用jps命令来确保看到各个Hadoop进程，如图：</p>
<p><img src="https://img-blog.csdn.net/20140720141857686" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：如上图所示，使用jps命令可以看到Hadoop的五个进程即NameNode、DataNode、SecondaryNameNode、TaskTrackers、JobTracker启动了，就表示hadoop启动成功了。</span></strong></p>
<p><br></p>
<p>我们执行hadoop fs命令来查看HDFS的相关命令，如图：</p>
<p><img src="https://img-blog.csdn.net/20140720142331763" alt=""><br></p>
<p>上图中显示了很多命令选项信息，截图不全，我在一下表格中完整的列出了支持的命令选项：</p>
<p><img src="https://img-blog.csdn.net/20140720142651475" alt=""><br></p>
<p><img src="https://img-blog.csdn.net/20140720142704454" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：以上表格中的路径包括HDFS中的路径和Linux中的路径。对于容易产生歧义的地方，会特别指出"Linux路径"或者"HDFS路径"，如果没有明确指出，意味着是HDFS路径。</span></strong></p>
<p><strong><span style="color:#ff0000;"><br></span></strong></p>
<p><strong><span style="font-size:24px;">下面详细讲解上述表格中的每个命令：</span></strong></p>
<p><span style="font-size:14px;"><strong><span style="color:#009900;">-ls 显示当前目录结构</span></strong></span></p>
<p>该命令选项表示查看指定路径的当前目录结构，后面跟HDFS路径，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720143127782" alt=""><br></p>
<p><strong>上图中的路径是HDFS根目录，显示的内容格式与Linux的命令ls -l 显示的内容格式非常相似，下面解释每一列内容的含义：</strong></p>
<p><strong><span style="color:#ff0000;">1.</span></strong>首字母表示文件类型(d表示目录，-表示普通文件)</p>
<p><strong><span style="color:#ff0000;">2.</span></strong>后面的9位是权限位，每3个一组(第一组为文件拥有者的权限；第二组为文件拥有组的权限；第三组表示其他人的权限，如上图第一行的drwxr-xr-x表示这是一个目录，目录拥有者有rwx的权限；目录拥有组有rx的权限；其他人有rx的权限)。</p>
<p><strong><span style="color:#ff0000;">3.</span></strong>权限位后面的数字或者是"-"表示副本数。如果是文件，使用数字表示副本数量；文件夹没有副本。</p>
<p><strong><span style="color:#ff0000;">4.</span></strong>后面的"root"和"Liao"表示所属主即拥有者。</p>
<p><strong><span style="color:#ff0000;">5.</span></strong>所属者后面的"supergroup"表示所属组。</p>
<p><strong><span style="color:#ff0000;">6.</span></strong>所属组后面的0表示文件的大小，单位为字节。</p>
<p><strong><span style="color:#ff0000;">7.</span></strong>文件大小后面的为修改时间，格式是年月日时分。</p>
<p><strong><span style="color:#ff0000;">8.</span></strong>最后那个表示文件的路径。</p>
<p><br></p>
<p><strong><span style="color:#ff0000;">如果hadoop fs -ls 后面没有路径，那么就会访问/user/当前用户目录。我们使用root用户登录，因此会访问HDFS的/user/root目录，</span></strong>如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720144307459" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：如果没有/user/root目录，会提示文件不存在的错误。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#009900;">-lsr 递归显示目录结构</span></strong></span></p>
<p>该命令选项表示递归显示当前路径的目录结构，后面跟HDFS路径，如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140720144506581" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上述命令为递归显示HDFS根目录下的内容</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-du 统计目录下个文件大小</span></strong></span></p>
<p>该命令选项显示指定路径下的文件大小，单位是字节，如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140720144435296" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-dus汇总统计目录下文件大小</span></strong></span></p>
<p>该命令选项显示指定路径的文件大小，单位是字节，如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140720144846744" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-count统计文件(夹)数量</span></strong></span></p>
<p>该命令选项显示指定路径下的文件夹数量、文件数量、文件总大小信息，如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140720144814687" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上图中的7表示文件夹数量，1表示文件数量，4表示文件总共4kb。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-mv移动</span></strong></span></p>
<p>该命令选项表示移动HDFS的文件到指定的目录中。后面跟两个路径，第一个表示源文件，第二个表示目的目录，如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140720145355484" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上述3条命令展示了移动前后的变化情况。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-cp复制</span></strong></span></p>
<p>该命令选项表示复制HDFS指定的文件到指定的HDFS目录中，后面跟两个路径，第一个是被复制的文件，第二个是目的地，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720145619722" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上述3条命令体现了文件移动前后的变化情况。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-rm删除文件/空白文件夹</span></strong></span></p>
<p>该命令选项表示删除指定的文件或者是空目录，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720145834397" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：最后一条命令hadoop fs -rm /user/root表示删除目录，但是该目录下有内容，所以不能删除，如果该目录是空的，那么就可以删除。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-rmr递归删除</span></strong></span></p>
<p>该命令选项表示递归删除指定目录及该目录下的所有子目录和文件，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720150141285" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上述递归删除表示删除HDFS根目录下的user目录即user目录下的所有内容。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-put上传文件</span></strong></span></p>
<p>该命令选项表示把Linux上的文件复制到HDFS中，如下图表示：</p>
<p><img src="https://img-blog.csdn.net/20140720150806715" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上述命令体现了文件上传前后的变化。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-copyFromLocal从本地复制</span></strong></span></p>
<p>操作与-put一致，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720150947055" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-moveFromLocal从本地移动</span></strong></span></p>
<p>该命令表示把文件从Linux上移动到HDFS中，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720151234755" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-getmerge合并到本地</span></strong></span></p>
<p>该命令选项的含义是把HDFS指定目录下的所有文件内容合并到本地Linux文件中，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720151638006" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-cat查看文件内容</span></strong></span></p>
<p>该命令选项是查看文件内容，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720151324140" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-text查看文件内容</span></strong></span></p>
<p>该命令可以认为和-cat的作用相同，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720151521734" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-mkdir创建空白文件夹</span></strong></span></p>
<p>该命令选项表示创建文件夹，后面跟的是在HDFS中将要被创建的文件夹，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720152115375" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-setrep设置副本数量</span></strong></span></p>
<p>该命令选项是修改已保存文件的副本数量，后面跟的是副本数量再跟文件路径，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720152034312" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上图中，我们把/liao.txt文件的副本数由1变为了3，意味着多了两个副本，HDFS会自动执行文件的复制工作，产生新的副本。</span></strong></p>
<p><br></p>
<p>如果最后的路径是文件夹，那么需要跟选项-R，表示对文件夹中的所有文件都修改副本，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720152330781" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：当我们对目录进行设置副本数时，该目录下的所有文件都会产生效果。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-touchz创建空白文件夹</span></strong></span></p>
<p>该命令选项是在HDFS中创建空白文件夹，如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140720152808073" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-stat显示文件的统计信息</span></strong></span></p>
<p>该命令选项显示文件的一些统计信息，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720152649406" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：命令选项后面可以有格式，使用单引号表示。上述示例中的格式：‘%b %n %o %r %Y’依次表示文件大小、文件名称、块大小、副本数、访问时间。</span></strong></p>
<p><br></p>
<p><strong><span style="color:#006600;">-tail查看文件尾部内容</span></strong></p>
<p>该命令选项显示文件最后1k字节的内容。一般用于查看日志。如果带有选项-f，那么当文件内容变化时，也会自动显示，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720153403832" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-chmod修改文件权限</span></strong></span></p>
<p>该命令选项使用类似于Linux的Shell中的chmod用法，作用是修改文件的权限，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720153533626" alt=""><br></p>
<p><strong><span style="color:#ff0000;">加上选项-R，可以对文件夹中的所有文件修改权限，如下图所示：</span></strong></p>
<p><img src="https://img-blog.csdn.net/20140720153818502" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-chown修改所属主</span></strong></span></p>
<p>该命令选项表示修改文件的所属主，如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140720154202113" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上述命令把所属主为root的文件改为了所属主为lavimer。另外如果带有选项-R，意味着可以递归修改文件夹中的所有文件的所属主和所属组的信息。</span></strong></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-chgrp修改所属组</span></strong></span></p>
<p>该命令的作用是修改文件的所属组，如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140720154535802" alt=""><br></p>
<p><br></p>
<p><span style="font-size:14px;"><strong><span style="color:#006600;">-help帮助</span></strong></span></p>
<p>该命令选项会显示帮助信息，后面跟上需要查询的命令选项即可，如下图：</p>
<p><img src="https://img-blog.csdn.net/20140720154811662" alt=""><br></p>
<p><strong><span style="color:#ff0000;">注：上述命令为查询rm的用法。</span></strong></p>
<p><br></p>
<p><strong>-help命令显示的内容也并非完全准确，比如查询count的结果就不正确，而是把所有命令选项的用法都显示出来了，如下图：</strong></p>
<p><img src="https://img-blog.csdn.net/20140720154749343" alt=""><br></p>
<p><strong><span style="color:#ff0000;">希望以后的版本会纠正！</span></strong></p>
<p><strong><span style="color:#ff0000;"><br></span></strong></p>
<p><span style="font-size:24px;color:#006600;"><strong>请多加练习!!!</strong></span></p>
            </div>
                </div>
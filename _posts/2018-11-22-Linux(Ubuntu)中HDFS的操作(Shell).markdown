---
layout:     post
title:      Linux(Ubuntu)中HDFS的操作(Shell)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/kan_fearless/article/details/79712572				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <ul><li>启动Hadoop</li>
</ul><p>--选择hadoop的文件夹下(本机的hadoop文件夹)</p>

<p>cd hadoop/sbin</p>

<p>./start-all.sh</p>

<ul><li>调用文件系统（FS）Shell命令为：hadoop fs &lt;args&gt;形式</li>
	<li>检查Hadoop 相关的进程是否启动</li>
</ul><p>jps</p>

<ul><li>在hadoop文件下创建一个test文件夹</li>
</ul><p><span style="color:#000000;">hadoop fs -mkdir /test</span></p>

<ul><li>在test文件夹中创建一个file.txt文件</li>
</ul><p>hadoop fs -touchz /test/file.txt</p>

<ul><li>查看此时hadoop的目录下的所有文件</li>
</ul><p><span style="color:#000000;">hadoop fs -ls /</span></p>

<ul><li>还可以用ls -R 的方式递归查看根下所有文件</li>
</ul><p>hadoop fs -ls -R /</p>

<ul><li>将hadoop根下test目录中的file.txt文件，移动到根下并重命名为file2.txt</li>
</ul><p><span style="color:#000000;">hadoop fs -mv /test/file.txt /file2.txt</span></p>

<ul><li>将hadoop根下的file2.txt文件复制到test目录下</li>
</ul><p><span style="color:#000000;">hadoop fs -cp /file2.txt /test</span></p>

<ul><li>在Linux本地/data 目录下，创建一个data.txt,并向其中写入hello hadoop </li>
</ul><p>cd /data</p>

<p>touch data.txt</p>

<p>echo hello hadoop &gt;&gt; data.txt</p>

<ul><li>将Linux本地的/data目录下的data.txt，上传到HDFS中的/test目录下</li>
</ul><p>hadoop fs -put /data/data.txt /test</p>

<p> </p>

<ul><li>查看hadoop 中/test目录下的data.txt文件（-cat）</li>
</ul><p>hadoop fs -cat /test/data.txt</p>

<p> </p>

<ul><li>除此之外还可以使用tail方法（-tail 方法是将文件尾部的内容输出）</li>
</ul><p>hadoop fd -tail /test/data.txt</p>

<p> </p>

<ul><li>查看hadoop 中/test目录下的data.txt文件大小（-du）</li>
</ul><p>hadoop fs -du /test/data.txt</p>

<p> </p>

<ul><li>将data.txt文件输出格式为文本格式（-text）</li>
</ul><p>hadoop fs -text /test/data.txt</p>

<p> </p>

<ul><li>stat方法可以返回指定路径的统计信息，有多个参数可选，当使用-stat选项但是不设定format时候，值打印文件创建日期，相当于%y</li>
</ul><p>hadoop fs -stat /test/data.txt</p>

<p>%n 打印文件名</p>

<p>%o 打印block size</p>

<p>%y 打印UTC日期</p>

<p>%b 打印文件大小。</p>

<p>%F 目录打印directory</p>

<p>%r 打印备份数</p>

<p> </p>

<ul><li>将hadoop 中的/test目录下的data.txt文件，下载到Linux本地/data目录中</li>
</ul><p>hadoop fs -get /test/data.txt /data</p>

<p> </p>

<ul><li>使用chown 方法，改变Hadoop中/test目录中的data.txt文件拥有者为root,使用-R将使改变在目录结构下递归进行。</li>
</ul><p>hadoop fs -chown root /test/data.txt</p>

<p> </p>

<ul><li>使用chmod方法，赋予Hadoop 中/test目录中的data.txt文件777权限</li>
</ul><p>hadoop fs -chmod 777 /test/data.txt</p>

<p> </p>

<ul><li>删除hadoop根下的file2.txt</li>
</ul><p>hadoop fs -rm file2.txt</p>

<p> </p>

<ul><li>删除hadoop根下的test目录</li>
</ul><p>hadoop fs -rm -r /test</p>

<p> </p>

<ul><li>挡在Hadoop中设置了回收站功能时，删除的文件会保留在回收站中，可以使用expunge 方法清空回收站</li>
</ul><p>hadoop fs -expunge</p>

<p> </p>

<ul><li>使用Shell命令执行hadoop自带的WordCount,执行hadoop jar 命令，在hadoop的 hadoop/share/hadoop/mapreduce路径下存在hadoop-mapreduce-examples-2.6.0-cdh5.4.5.jar包，我们执行其中的worldcount类，数据来源为HDFS的/in目录，数据输出到HDFS的/out目录</li>
</ul><p>hadoop jar /home/szk/Documents/sofe/hadoop-2.7.2/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.6.0-cdh5.4.5.jar wordcount /in /out</p>

<p> </p>

<ul><li>查看HDFS中的/out目录</li>
</ul><p>hadoop fs -cat /out</p>

<p> </p>

<ul><li>进入Hadoop安全模式</li>
</ul><p>hadoop fs -dfsadmin -safemode enter</p>

<p> </p>

<ul><li>退出Hadoop安全模式</li>
</ul><p>hadoop fs -dfsadmin -safemode leave</p>

<p> </p>

<ul><li>切换到/hadoop/sbin，关闭Hadoop</li>
</ul><p>/stop-all.sh</p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>
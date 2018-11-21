---
layout:     post
title:      Hadoop学习笔记_操作篇之一：HDFS操作
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/rumengjian/article/details/20545557				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>1、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">查看版本</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop version</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>2、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">查看目录</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -ls /</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -ls /user/hadoop</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>3、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">创建目录</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -mkdir /user/in</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>4、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">删除目录</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop dfs -rmr test</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>5、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">删除文件</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -rm test.txt</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>6、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">上传文件到</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HDFS</span><span style="font-size:9pt;font-family:'宋体';">目录</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">haddop dfs -put *.txt test</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>7、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">下载文件</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop dfs -get test test1</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>8、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">查看文件内容</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -text /user/hadoop/20120722/test1.txt</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop dfs –tail test</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop dfs –cat test</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>9、<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">复制文件：</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">从本地到</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hdfs</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -copyFromLocal /tmp/test.txt /user/in/test.txt</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">从</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hdfs</span><span style="font-size:9pt;font-family:'宋体';">到</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hdfs</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -cp /hadoop/test.txt /user/in/test.txt</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>10、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">分布式复制</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(distcp)</span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">说明：</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">dist</span><span style="font-size:9pt;font-family:'宋体';">分布式，</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">cp</span><span style="font-size:9pt;font-family:'宋体';">复制</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">distcp</span><span style="font-size:9pt;font-family:'宋体';">操作会被解析为一个</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">MapReduce</span><span style="font-size:9pt;font-family:'宋体';">操作来执行</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">用途：</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span style="font-size:9pt;font-family:'宋体';">用于在集群内部及集群之间复制数据</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(3)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">注意：</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">源路径必须是绝对路径</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">一般会跳过目标路径上已存在的文件，</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">可通过</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">-overwirte</span><span style="font-size:9pt;font-family:'宋体';">可以选择对已存在的文件进行覆盖</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">通过</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">-update</span><span style="font-size:9pt;font-family:'宋体';">可以对仅更新过的文件进行重写</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(4)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">用法：</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">要求集群版本一致</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop distcp hdfs://192.168.32.168/test hdfs://192.168.32.68/in</span></p>
<h6 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:Wingdings;" xml:lang="en-us"><span>n
</span></span><span style="font-size:9pt;font-family:'宋体';">集群版本不一致</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h6>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop distcp hftp://192.168.32.168/test hdfs://192.168.32.68/in</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>11、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">移动文件</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -mv /data/in/test.txt /data/ok.txt</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>12、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">执行</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">jar</span><span style="font-size:9pt;font-family:'宋体';">文件</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop jar /tmp/testhdfs.jar test.my.CopyToHadoop</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop jar testhdfs.jar /user/hadoop/in/ncdc_all.txt /user/hadoop/output</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>13、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">使用</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop</span><span style="font-size:9pt;font-family:'宋体';">归档文件</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(archive)</span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">作用：</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop</span><span style="font-size:9pt;font-family:'宋体';">归档文件和</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HAR</span><span style="font-size:9pt;font-family:'宋体';">文件可以将文件高效的放入</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HDFS</span><span style="font-size:9pt;font-family:'宋体';">块中的文件存档设备，在减少</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">NameNode</span><span style="font-size:9pt;font-family:'宋体';">内在使用的同时，仍然允许对文件进行透明访问。也就是</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop</span><span style="font-size:9pt;font-family:'宋体';">归档文件可以作为</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">MapReduce</span><span style="font-size:9pt;font-family:'宋体';">的输入。</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">用法</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop archive -archiveName
</span><span style="font-size:9pt;font-family:'宋体';">归档文件名　要归档的文件源（可有多个）</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HAR</span><span style="font-size:9pt;font-family:'宋体';">文件的输出目录</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop archive -archiveName test.har /data/test/ /data/in/</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(3)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">查看归档文件中的文件</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fs -lsr har:///data/in/test.har</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>14、<span style="font:7pt 'Times New Roman';">
</span></span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop</span><span style="font-size:9pt;font-family:'宋体';">中</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">System.out.println</span><span style="font-size:9pt;font-family:'宋体';">输出</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">
</span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">首先打开</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">http://localhost:50030/jobtracker.jsp</span><span style="font-size:9pt;font-family:'宋体';">进入</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">jobtracker</span><span style="font-size:9pt;font-family:'宋体';">，在</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Completed
 Jobs</span><span style="font-size:9pt;font-family:'宋体';">中选刚执行的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">job</span></h5>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">再选对应的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">map</span><span style="font-size:9pt;font-family:'宋体';">或者</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">reduce,</span><span style="font-size:9pt;font-family:'宋体';">进</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Task
 Logs</span><span style="font-size:9pt;font-family:'宋体';">下面的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">all</span><span style="font-size:9pt;font-family:'宋体';">，里面有</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">stdout
 logs</span><span style="font-size:9pt;font-family:'宋体';">和</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">stderr logs</span></h5>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>15、<span style="font:7pt 'Times New Roman';">
</span></span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">fsck</span><span style="font-size:9pt;font-family:'宋体';">工具</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">:</span><span style="font-size:9pt;font-family:'宋体';">检查</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HDFS</span><span style="font-size:9pt;font-family:'宋体';">中文件的健康状况</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fsck /</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>16、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">查找文件的所有块</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fsck /data/in/hello.txt -files -blocks -racks</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">-files</span><span style="font-size:9pt;font-family:'宋体';">：显示文件的文件名、大小、块数量、及是否可用（是否存在丢失的块）</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">-blocks</span><span style="font-size:9pt;font-family:'宋体';">：显示每个块在文件中的信息，一个块用一行显示</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">-racks</span><span style="font-size:9pt;font-family:'宋体';">：显示每个块所在机架位置和</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">datanode</span><span style="font-size:9pt;font-family:'宋体';">的位置</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></p>
<p class="MsoNormal"><span style="font-size:9pt;font-family:'宋体';">不加选项，则执行以上所有指令　＝　</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop fsck /data/in/hello.txt</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>17、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">均衡器</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">start-balancer.sh</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> 
</span>or</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop balancer</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>18、<span style="font:7pt 'Times New Roman';">
</span></span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">secondarynamenode</span><span style="font-size:9pt;font-family:'宋体';">操作</span><span lang="en-us" style="font-weight:normal;font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">运行</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">DFS</span><span style="font-size:9pt;font-family:'宋体';">的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">secondarynamenode</span><span style="font-size:9pt;font-family:'宋体';">进程</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop secondarynamenode</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">启动检查</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">secondarynamenode</span><span style="font-size:9pt;font-family:'宋体';">的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">checkpoint</span><span style="font-size:9pt;font-family:'宋体';">过程</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(editlog</span><span style="font-size:9pt;font-family:'宋体';">超过规定大小，默认为</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">64MB)</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop secondarynamenode -checkpoint</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(3)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">强制启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">checkpoint</span><span style="font-size:9pt;font-family:'宋体';">过程</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop secondarynamenode -checkpoint force</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(4)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">显示</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">editlog</span><span style="font-size:9pt;font-family:'宋体';">文件大小</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop secondarynamenode -geteditsize</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>19、<span style="font:7pt 'Times New Roman';">
</span></span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">NameNode</span><span style="font-size:9pt;font-family:'宋体';">操作</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">运行</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">DFS</span><span style="font-size:9pt;font-family:'宋体';">的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">namenode</span><span style="font-size:9pt;font-family:'宋体';">进程</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span> </span>hadoop namenode</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">格式化</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">DFS</span><span style="font-size:9pt;font-family:'宋体';">文件系统</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop namenode -format</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(3)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">升级</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop</span><span style="font-size:9pt;font-family:'宋体';">后启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">namenode</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop namenode -upgrade</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(4)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">回滚</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">namenode</span><span style="font-size:9pt;font-family:'宋体';">到前一版本</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop namenode -rollback</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(5)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">删除文件系统的前一个状态</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">(</span><span style="font-size:9pt;font-family:'宋体';">将会导致系统不能回到前一个状态</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">)</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop namenode -finalize</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(6)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">复制备份</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">checkpoint</span><span style="font-size:9pt;font-family:'宋体';">的状态到当前</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">checkpoint</span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop namenode -importcheckpoint</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>20、<span style="font:7pt 'Times New Roman';">
</span></span></span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">datanode</span><span style="font-size:9pt;font-family:'宋体';">操作</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(1)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">运行</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">DFS</span><span style="font-size:9pt;font-family:'宋体';">的</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">datanode</span><span style="font-size:9pt;font-family:'宋体';">进程</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop datanode</span></p>
<h5 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>(2)<span style="font:7pt 'Times New Roman';"> 
</span></span></span><span style="font-size:9pt;font-family:'宋体';">回滚</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">namenode</span><span style="font-size:9pt;font-family:'宋体';">到前一版本</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h5>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop datanode -rollback</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>21、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">验证</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hadoop</span><span style="font-size:9pt;font-family:'宋体';">集群状态</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">hadoop dfsadmin -report</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>22、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">启动</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">Hadoop</span><span style="font-size:9pt;font-family:'宋体';">和</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">HBase</span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">start-all.sh</span></p>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">start-hbase.sh</span></p>
<h4 style="text-indent:-21pt;line-height:normal;">
<span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"><span>23、<span style="font:7pt 'Times New Roman';">
</span></span></span><span style="font-size:9pt;font-family:'宋体';">查看启动进程</span><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us"></span></h4>
<p class="MsoNormal"><span lang="en-us" style="font-size:9pt;font-family:'Courier New';" xml:lang="en-us">jps</span></p>
            </div>
                </div>
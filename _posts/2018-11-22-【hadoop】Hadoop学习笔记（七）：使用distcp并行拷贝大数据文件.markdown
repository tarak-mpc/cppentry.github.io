---
layout:     post
title:      【hadoop】Hadoop学习笔记（七）：使用distcp并行拷贝大数据文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
以前我们介绍的访问HDFS的方法都是单线程的，Hadoop中有一个工具可以让我们并行的拷贝大量数据文件，这个工具就是distcp。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　distcp的典型应用就是在两个HDFS集群中拷贝文件，如果两个集群使用的Hadoop版本相同，可以使用hdfs标识符：<br>
　　　　% hadoop distcp hdfs://namenode1/foo hdfs://namenode2/bar<br>
　　这条命令会把第一个集群（namenode为命令中指定的namenode1）中的/foo目录拷贝到第二个集群中的/bar目录下，于是在第二个集群中就得到了/bar/foo这样的目录结构，我们也可以指定多个拷贝源，但拷贝目的地只有一个。要注意的是，指定拷贝路径的时候要使用绝对路径。　</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　distcp命令是以mapreduce作业的形式实现的，只不过此作业没有reduce任务。每一个文件是由一个map任务来拷贝的，distcp尽量把大小之和相同的各个文件导入到同一个map任务中。这样可以每个map任务拷贝的数据量大致相同。<br>
　　Map任务的个数是按如下方式决定的：<br>
　　1）考虑到创建每个map任务的开销，每个map任务至少应处理256MB大小的数据（如果总输入文件的大小小于256MB，则把这些输入数据全部交给一个map任务执行）。例如，一个1GB大小的输入数据会被分配四个map任务来拷贝。<br>
　　2）如果待拷贝的数据实在很大，这时候就不能只按每个map任务256MB输入数据的标准来划分了，因为这样可能需要创建很多map任务。这是可以按每个datanode20个map任务来划分，例如如果有1000GB的输入数据和100个节点，这是就会启动100*20=2000个map任务来拷贝数据，每个map任务拷贝512MB数据。同时我们也可通过-m选项指定要使用的map数，例如-m 1000就会只启动1000个map任务，每个map任务拷贝1GB数据。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　默认情况下，如果在拷贝的目的地同名文件已经存在，则会默认跳过这些文件。可以通过-overwrite选项指定覆盖掉同名文件，或者通过-update选项来更新同名文件。<br>
　　关于distcp的更多用法，可以不加参数运行“hadoop distcp”命令来查看其用法。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　如果两个集群的Hadoop版本不一致就不能使用hdfs标识符来拷贝文件了，因为两者的RPC系统是不兼容的。这是可以使用只读的基于HTTP的HFTP文件系统来读取源数据，如下所示（注意此命令是在第二个集群上执行的，以确保rpc版本兼容）：<br>
　　　　% hadoop distcp hftp://namenode1:50070/foo hdfs://namenode2/bar<br>
　　注意在上述命令中需要制定namenode1的网络端口，它是由dfs.http.address指定的，默认为50070.</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　另一种可选的方法是使用webhdfs协议（替换hftp协议），这样在拷贝的源和目的地都可以使用http而不用担心版本不兼容的问题：<br>
　　　　% hadoop distcp webhdfs://namenode1:50070/foo webhdfs://namenode2:50070/bar</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
　　我们还可以使用HDFS HTTP代理作为拷贝的源和目的地，这样可以设置防火墙以及进行带宽控制。</p>
<p style="font-family:'Helvetica Neue', Helvetica, Arial, sans-serif;font-size:13px;line-height:19px;background-color:rgb(245,245,245);">
 　  转载请注明出处：<span style="color:rgb(255,0,0);"><a id="Editor_Edit_hlEntryLink" title="view: Hadoop学习笔记（七）：使用distcp并行拷贝大数据文件" href="http://www.cnblogs.com/beanmoon/archive/2012/12/18/2823581.html" rel="nofollow" style="color:rgb(0,105,214);line-height:inherit;"><span style="color:rgb(255,0,0);">http://www.cnblogs.com/beanmoon/archive/2012/12/18/2823581.html</span></a></span></p>
            </div>
                </div>
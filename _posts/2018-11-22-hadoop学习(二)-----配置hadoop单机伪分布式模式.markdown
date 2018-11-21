---
layout:     post
title:      hadoop学习(二)-----配置hadoop单机伪分布式模式
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/happyAnger6/article/details/44274597				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>(1).修改hadoop的核心配置文件core-site.xml.主要是配置HDFS的地址和端口号。</p>
<p> &lt;property&gt;<br>
                &lt;name&gt;fs.default.name&lt;/name&gt;          #HDFS NameNode的ip和端口<br>
                &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;<br>
        &lt;/property&gt;<br>
        &lt;property&gt;<br>
                &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;            #HDFS tmp dir<br>
                &lt;value&gt;/usr/local/hadoop/hadoop-1.2.1/tmp&lt;/value&gt;<br>
        &lt;/property&gt;</p>
<p><br></p>
<p>(2).配置HDFS配置文件hdfs-site.xml,主要是配置replication</p>
<p>&lt;configuration&gt;<br>
        &lt;property&gt;<br>
                &lt;name&gt;dfs.replication&lt;/name&gt;    #HDFS的备份数量，这里单机，只有1台机器。<br>
                &lt;value&gt;1&lt;/value&gt;<br>
        &lt;/property&gt;<br>
        &lt;property&gt;  <br>
                &lt;name&gt;dfs.name.dir&lt;/name&gt;         #NameNode存储名字空间及汇报日志位置<br>
                &lt;value&gt;/usr/local/hadoop/hadoop-1.2.1/hdfs/name&lt;/value&gt;<br>
        &lt;/property&gt;<br>
        &lt;property&gt;<br>
                &lt;name&gt;dfs.data.dir&lt;/name&gt;    #DataNode存储数据块位置<br>
                &lt;value&gt;/usr/local/hadoop/hadoop-1.2.1/hdfs/data&lt;/value&gt;<br>
        &lt;/property&gt;<br>
&lt;/configuration&gt;</p>
<p><br></p>
<p>(3).修改mapred-site.xml<br>
&lt;configuration&gt;<br>
        &lt;property&gt;<br>
                &lt;name&gt;mapred.job.tracker&lt;/name&gt;  #jobTracker的IP和端口<br>
                &lt;value&gt;localhost:9001&lt;/value&gt;<br>
        &lt;/property&gt;<br>
&lt;/configuration&gt;</p>
<p><br></p>
<p>(4).通过上面的配置，完成了最简单的伪分布式配置</p>
<p>还需在hadoop-1.2.1目录下创建几个目录:</p>
<p>mkdir tmp</p>
<p>mkdir hdfs</p>
<p>mkdir hdfs/data</p>
<p>mkdir hdfs/name<br></p>
<p><br></p>
<p>执行hadoop的namenode格式化</p>
<p>4.1hadoop namenode -format</p>
<p>4.2格式化完毕后,启动hadoop.</p>
<p>start-all.sh</p>
<p>4.3.通过jps命令查看输出，确认启动成功。</p>
<p>4082 Jps<br>
3719 Worker<br>
3553 Master<br>
3331 TaskTracker<br>
3190 JobTracker<br>
2800 NameNode<br>
3105 SecondaryNameNode<br>
2961 DataNode<br></p>
<p><br></p>
<p>(5).通过WEB页面localhost:50070查看hadoop运行情况。</p>
<p><br></p>
<p>(6).用新建的伪分布式环境运行wordcount例子.</p>
<p>6.1通过dfs命令创建input目录</p>
<p>hadoop dfs -mkdir input</p>
<p>6.2复制文件</p>
<p>hadoop dfs -copyFromLocal /usr/local/hadoop/hadoop-1.2.1/conf/* input</p>
<p>6.3运行wordcount</p>
<p>hadoop jar hadoop-examples-1.2.1.jar wordcount input output</p>
<p>6.4查看输出结果</p>
<p>hadoop dfs -cat output/*</p>
<p> 也可以通过WEB页面查看我们提交的Job.</p>
<p><br></p>
<p>至此，单机伪分布式环境搭建完成。<br></p>
<p><br></p>
<p><br><br><br></p>
            </div>
                </div>
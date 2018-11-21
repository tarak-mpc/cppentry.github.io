---
layout:     post
title:      hadoop安装日记（四）：安装和配置hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>以hadoop用户登录。</p>
<p>（一）下载hadoop：</p>
<p>命令行输入：wget -c <a href="http://mirror.bjtu.edu.cn/apache/hadoop/common/hadoop-1.1.2/hadoop-1.1.2.tar.gz" rel="nofollow">
http://mirror.bjtu.edu.cn/apache/hadoop/common/hadoop-1.1.2/hadoop-1.1.2.tar.gz</a>  /usr/local/</p>
<p>把hadoop下载到  /home/hadoop目录</p>
<p>（二）解压缩</p>
<p>命令行输入： sudo tar xzf  hadoop-1.1.2.tar.gz</p>
<p> </p>
<p>（三）重命名</p>
<p>命令行输入：mv hadoop-1.1.2.tar.gz hadoop</p>
<p> </p>
<p>（四）配置/etc/profile </p>
<p>命令行终端输入：sudo gedit /etc/profile</p>
<p>适当修改原有内容，添加内容如下：</p>
<p>export JAVA_HOME=/usr/lib/jvm/jdk</p>
<p>export HADOOP_HOME=/home/hadoop/hadoop</p>
<p>export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/lib:$JAVA_HOME/bin</p>
<p>export HADOOP_HOME_WARN_SUPPRESS=1   #忽略hadoop的警告。</p>
<p> </p>
<p>（五）配置修改conf/hadoop-env.sh，添加jdk支持</p>
<p>命令行终端输入：sudo gedit hadoop/conf/hadoop-env.sh</p>
<p>启动gedit后，在文档中 设置：</p>
<p>export JAVA_HOME=/usr/lib/jvm/jdk，注意把本行开头的“#”去掉。</p>
<p> </p>
<p>（六）修改conf/cor-site.xml配置如下：</p>
<p> </p>
<p>&lt;property&gt; </p>
<p>        &lt;name&gt;fs.default.name&lt;/name&gt; </p>
<p>        &lt;value&gt;hdfs://localhost:9000&lt;/value&gt; //决定namenode </p>
<p>&lt;/property&gt; </p>
<p>&lt;property&gt; </p>
<p>        &lt;name&gt;Hadoop.tmp.dir&lt;/name&gt; </p>
<p>        &lt;value&gt;/home/Hadoop/tmp&lt;/value&gt; //临时文件，有问题的时候，可以删除 </p>
<p>        &lt;description&gt;A base for other temporary directories.&lt;/description&gt; </p>
<p>&lt;/property&gt;</p>
<p>（七）配置conf/hdfs-site.xml，增加下面内容：</p>
<p>&lt;property&gt; </p>
<p>        &lt;name&gt;dfs.name.dir&lt;/name&gt; </p>
<p>        &lt;value&gt;/home/Hadoop/name&lt;/value&gt; </p>
<p>&lt;description&gt;namenode持久存储名字空间，事务日志的本地路径 &lt;/description&gt;</p>
<p>&lt;/property&gt; </p>
<p>&lt;property&gt; </p>
<p>        &lt;name&gt;dfs.data.dir&lt;/name&gt; </p>
<p>        &lt;value&gt;/home/Hadoop/data&lt;/value&gt; </p>
<p>&lt;description&gt;datanode存放数据的路径 &lt;/description&gt;</p>
<p>&lt;/property&gt; </p>
<p>&lt;property&gt; </p>
<p>        &lt;name&gt;dfs.datanode.max.xcievers&lt;/name&gt; </p>
<p>        &lt;value&gt;4096&lt;/value&gt; </p>
<p>&lt;/property&gt; </p>
<p>&lt;property&gt; </p>
<p>        &lt;name&gt;dfs.replication&lt;/name&gt; </p>
<p>        &lt;value&gt;1&lt;/value&gt; </p>
<p>&lt;description&gt;数据备份的个数，默认是3 &lt;/description&gt;</p>
<p>&lt;/property&gt;</p>
<p> </p>
<p>（八）配置conf/mapred-site.xml内容如下：</p>
<p>&lt;property&gt; </p>
<p>        &lt;name&gt;mapred.job.tracker&lt;/name&gt; </p>
<p>        &lt;value&gt;localhost:9001&lt;/value&gt; </p>
<p>&lt;/property&gt;</p>
<p> </p>
<p>（九）修改conf/masters，这个决定那个是secondarynamenode ，里边只保留下面的内容<br>
                 localhost</p>
<p>            修改conf/slaves，这个是所有datanode的机器 ，里边只保留下面的内容</p>
<p>                  localhost</p>
<p>（十） 以root登录，修改 /etc/hosts</p>
<p>里边只保留   127.0.0.1  localhost  这个对应行。</p>
<p> </p>
<p>（十一）启动hadoop服务。</p>
<p>回到hadoop用户。</p>
<p>登录：</p>
<p>ssh localhost</p>
<p> </p>
<p>格式化hdfs文件系统的namenode</p>
<p>命令行输入：</p>
<p>bin/hadoop namenode -format</p>
<p>启动Hadoop集群</p>
<p>命令行输入：hadoop/bin/start-all.sh</p>
<p> </p>
<p>测试方法一：jps，查看结果。显示各个节点情况。</p>
<p>测试方法二：打开浏览器，分别输入以下网址：<br>
        http://localhost:50030 (MapReduce的Web页面)<br>
        http://localhost:50070 (HDfS的web页面)</p>
<p>        如果能成功访问，则说明Hadoop安装成功</p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
<p> </p>
            </div>
                </div>
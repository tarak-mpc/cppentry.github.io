---
layout:     post
title:      Linux下图示安装Hadoop-2.8.5(单机版)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/HcJsJqJSSM/article/details/83387153				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>前言：Hadoop2.x的四个模块.Hadoop Common，Hadoop HDFS，Hadoop MapReduce，Hadoop YARN.Centos7的版本。</p>

<p>一：Hadoop的的的下载。</p>

<p>       <strong>1. <a href="http://hadoop.apache.org/" rel="nofollow">官网</a>。</strong></p>

<p>       <strong>2.版本。</strong></p>

<p><img alt="" class="has" height="702" src="https://img-blog.csdnimg.cn/20181025214941770.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1200"></p>

<p> 分为源码和二进制的。这里下载二进制的。这里是简单学习使用的是阿帕奇的版本。</p>

<p><img alt="" class="has" height="703" src="https://img-blog.csdnimg.cn/20181025215120868.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1200"></p>

<p><strong>3.下载的Hadoop。</strong></p>

<p><strong>wget XXX.tar.gz</strong></p>

<p><img alt="" class="has" height="119" src="https://img-blog.csdnimg.cn/2018102521525063.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1127"><strong> 4.加压到指定的目录下。</strong></p>

<p> <strong>tar -zxvf XXX.tar.gz -C / usr / local  </strong></p>

<p><strong>5. Hadoop目录结构。</strong></p>

<p><img alt="" class="has" height="298" src="https://img-blog.csdnimg.cn/20181025215510180.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1124"></p>

<p><strong>6.查看的Hadoop的版本。（记得配置环境的Hadoop的环境变量）。</strong></p>

<p>   <strong>cd bin</strong></p>

<p><strong>   Hadoop的版本</strong></p>

<p><img alt="" class="has" height="217" src="https://img-blog.csdnimg.cn/20181025220332413.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1123"></p>

<p>二.Hadoop环境配置。</p>

<p>   <strong>1. Java环境配置。（使用Java8的版本）   </strong></p>

<p>  Hadoop的的是Java的的开发的大数据框架，因此首先要准备好Java的的的环境。这个就不介绍了。注意一下配置文件。</p>

<p>  <strong>cat / etc / profile</strong></p>

<p><img alt="" class="has" height="65" src="https://img-blog.csdnimg.cn/20181025220029727.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1124"><strong>  2.配置的Hadoop的环境变量。</strong></p>

<p><strong> vim / etc / profile</strong></p>

<p><strong> export HAOOP_HOME = / usr / local / hadoop-2.8.5</strong></p>

<p><strong> export PATH = $ PATH：$ HADOOP_HOME / bin</strong></p>

<p><img alt="" class="has" height="181" src="https://img-blog.csdnimg.cn/20181025220526427.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1128"></p>

<p><strong>vim httpfs-env.sh</strong></p>

<p><img alt="" class="has" height="379" src="https://img-blog.csdnimg.cn/20181025220912323.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1120"></p>

<p>查看Hadoop HDFS支持的所有命令。</p>

<p><img alt="" class="has" height="561" src="https://img-blog.csdnimg.cn/20181025232025559.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1123"></p>

<p><strong>3.查看防火墙和关闭SELinux的。</strong></p>

<p><strong>   firewall-cmd --state </strong><img alt="" class="has" height="76" src="https://img-blog.csdnimg.cn/20181025221145818.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1125"></p>

<p> <strong>cat / etc / selinux / config</strong></p>

<p><img alt="" class="has" height="369" src="https://img-blog.csdnimg.cn/20181025221325857.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1122"></p>

<p><strong>4.修改配置文件。（这里就直接使用自己Linux的IP了，就不配置域名了）.etc / hosts</strong></p>

<p>    在修改配置文件前，先创建一下几个目录。</p>

<p><strong>    mkdir / root / hadoop</strong></p>

<p><strong>    mkdir / root / hadoop / tmp</strong></p>

<p><strong>    mkdir / root / hadoop / var</strong></p>

<p><strong>    mkdir / root / hadoop / dfs</strong></p>

<p><strong>    mkdir / root / hadoop / dfs / name</strong></p>

<p><strong>    mkdir / root / hadoop / dfs / data</strong></p>

<p>    然后是依次修改下面一个XML文件。</p>

<p>    <strong>cd /usr/local/hadoop-2.8.5/etc/hadoop</strong></p>

<p><strong>    vim core-site.xml</strong></p>

<pre class="has">
<code>&lt;configuration&gt;
&lt;property&gt;
        &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
        &lt;value&gt;/root/hadoop/tmp&lt;/value&gt;
        &lt;description&gt;Abase for other temporary directories.&lt;/description&gt;
   &lt;/property&gt;
   &lt;property&gt;
        &lt;name&gt;fs.default.name&lt;/name&gt;
        &lt;value&gt;hdfs://192.168.217.134:9000&lt;/value&gt;
   &lt;/property&gt;
&lt;/configuration&gt;</code></pre>

<p>   接下来就是<strong>vim hdfs-site.xml</strong></p>

<pre class="has">
<code>&lt;configuration&gt;
&lt;property&gt;
   &lt;name&gt;dfs.name.dir&lt;/name&gt;
   &lt;value&gt;/root/hadoop/dfs/name&lt;/value&gt;
   &lt;description&gt;Path on the local filesystem where theNameNode stores the namespace and transactions logs persistently.&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
   &lt;name&gt;dfs.data.dir&lt;/name&gt;
   &lt;value&gt;/root/hadoop/dfs/data&lt;/value&gt;
   &lt;description&gt;Comma separated list of paths on the localfilesystem of a DataNode where it should store its blocks.&lt;/description&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.http.address&lt;/name&gt;
&lt;value&gt;192.168.217.134:50070&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
   &lt;name&gt;dfs.replication&lt;/name&gt;
   &lt;value&gt;1&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
&lt;name&gt;dfs.namenode.secondary.http-address&lt;/name&gt;
&lt;value&gt;192.168.217.134:50090&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
      &lt;name&gt;dfs.permissions&lt;/name&gt;
      &lt;value&gt;true&lt;/value&gt;
      &lt;description&gt;need not permissions&lt;/description&gt;
&lt;/property&gt;
&lt;/configuration&gt;</code></pre>

<p><strong>dfs.permissions配置为假后，可以允许不要检查权限就生成DFS上的文件，方便倒是方便了，但是你需要防止误删除，请将它设置为真，或者直接将该属性节点删除，因为默认就是真正。</strong></p>

<p>接下来是：</p>

<p><strong>vim mapred-site.xml（没有那个mapred-site.xml文件就触摸mapred-site.xml）。</strong></p>

<pre class="has">
<code>&lt;configuration&gt;
&lt;property&gt;
    &lt;name&gt;mapred.job.tracker&lt;/name&gt;
    &lt;value&gt;192.168.217.134:9001&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
      &lt;name&gt;mapred.local.dir&lt;/name&gt;
       &lt;value&gt;/root/hadoop/var&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
       &lt;name&gt;mapreduce.framework.name&lt;/name&gt;
       &lt;value&gt;yarn&lt;/value&gt;
&lt;/property&gt;
&lt;/configuration&gt;</code></pre>

<p> 三.Hadoop启动。</p>

<p>     1.第一次启动时要初始化，格式化的NameNode。</p>

<p><strong>    cd bin</strong></p>

<p><strong>   ./hdfs namenode -format </strong></p>

<p><strong>    2.切换sbin目录目录下。</strong></p>

<p><strong>    cd ..</strong></p>

<p><strong>    cd sbin</strong></p>

<p><strong>    sh start-dfs.sh（需要输入用户名，分别是启动namenode）。</strong></p>

<p><strong>    sh start-yarn.sh</strong></p>

<p><img alt="" class="has" height="382" src="https://img-blog.csdnimg.cn/20181025230504337.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1123"></p>

<p><strong>  </strong><img alt="" class="has" height="234" src="https://img-blog.csdnimg.cn/20181025230702372.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1119"><strong> JPS</strong></p>

<p><img alt="" class="has" height="164" src="https://img-blog.csdnimg.cn/20181025230724681.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1122"></p>

<p>浏览器访问如下：</p>

<p><img alt="" class="has" height="689" src="https://img-blog.csdnimg.cn/20181025230807917.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1200"></p>

<p><img alt="" class="has" height="701" src="https://img-blog.csdnimg.cn/20181025230843734.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1200"></p>

<p>关闭HDFS和纱线：</p>

<p><img alt="" class="has" height="309" src="https://img-blog.csdnimg.cn/20181025231040938.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1121"></p>

<p><img alt="" class="has" height="232" src="https://img-blog.csdnimg.cn/20181025231121588.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L0hjSnNKcUpTU00=,size_27,color_FFFFFF,t_70" width="1123"></p>

<p>遇到启动问题可以看一看启动日志吧。</p>

<p>重新格式化名称节点之前需要清空DFS下的名称和数据文件夹以解决数据节点无法启动的问题。</p>

<p><strong>cat hadoop-root-datanode-localhost.localdomain.out.1<br>
cat hadoop-root-namenode-localhost.localdomain.log</strong></p>

<p>至此完成的的Linux下的的Hadoop的安装。</p>            </div>
                </div>
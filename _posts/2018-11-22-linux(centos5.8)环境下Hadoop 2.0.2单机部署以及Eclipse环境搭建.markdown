---
layout:     post
title:      linux(centos5.8)环境下Hadoop 2.0.2单机部署以及Eclipse环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h2>Hadoop 2.0.2-alpha单机部署</h2>
<h3><span style="font-size:14px;">（1）新建hadoop用户以及hadoop用户组</span></h3>
<p><span style="font-size:14px;">首先取得root权限</span><span style="font-size:14px;">新</span></p>
<p><span style="font-size:14px;"><em>        $ su -</em><br></span></p>
<p><span style="font-size:14px;">建用户hadoop组</span></p>
<p><span style="font-size:14px;"><em>       # groupadd hadoop</em></span><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">新建用户hadoop，放入hadoop组中，并设置主目录为/home/hadoop</span></p>
<p><span style="font-size:14px;"><em>       # useradd -g hadoop -d /home/hadoop hadoop</em><br></span></p>
<p><span style="font-size:14px;">修改密码</span></p>
<p><span style="font-size:14px;"><em>      # passwd hadoop</em><br></span></p>
<p><span style="font-size:14px;">其中删除用户命令为userdel，删除用户组命令为groupdel，查看用户和分组信息可以cat /etc/groups 或cat /etc/passwd</span></p>
<p><span style="font-size:14px;">回到新建用户目录</span></p>
<p><span style="font-size:14px;"><em>     # su hadoop</em></span></p>
<p><span style="font-size:14px;"><em>     $ cd</em></span></p>
<h3><span style="font-size:14px;">（2）下载和配置hadoop<br></span></h3>
<p><span style="font-size:14px;">下载hadoop文件</span></p>
<p><span style="font-size:14px;"><em><span><span>      $ wget http://mirror.bjtu.edu.cn/apache/hadoop/common/hadoop-2.0.2-alpha/hadoop-2.0.2-alpha.tar.gz</span></span></em><br></span></p>
<p><span style="font-size:14px;">下载完成后解压</span></p>
<p><span style="font-size:14px;"><em><span><span>     $ tar xzvf hadoop-2.0.2-alpha.tar.gz</span></span></em><br></span></p>
<p><span style="font-size:14px;">新建一个叫hadoop的文件夹，建立软链接，以便以后使用其他版本</span></p>
<p><span style="font-size:14px;"><em><span><span>     $ ln -sf hadoop-2.0.2-alpha hadoop</span></span></em><br></span></p>
<p><span style="font-size:14px;">配置系统环境变量<span><span><br></span></span></span></p>
<p><span style="font-size:14px;"><em><span><span>    $ su -  </span></span><span><br></span></em></span></p>
<p><span style="font-size:14px;"><em><span>    # vim /etc/profile</span></em></span></p>
<p><span style="font-size:14px;">在最后加上以下几行</span><span><span></span></span></p>
<p></p>
<pre><code class="language-plain">export HADOOP_PREFIX="/home/hadoop/hadoop"   
PATH="$PATH:$HADOOP_PREFIX/bin:$HADOOP_PREFIX/sbin"   
export HADOOP_MAPRED_HOME=${HADOOP_PREFIX}   
export HADOOP_COMMON_HOME=${HADOOP_PREFIX}   
export HADOOP_HDFS_HOME=${HADOOP_PREFIX}   
export YARN_HOME=${HADOOP_PREFIX}
export HADOOP_CONF_DIR="${HADOOP_PREFIX}/etc/hadoop"
</code></pre><span style="font-size:14px;">重新启动系统</span>
<p></p>
<p><em><span style="font-size:14px;">       # reboot</span></em></p>
<p><span style="font-size:14px;">重启后使用hadoop用户登录，进入/home/hadoop/hadoop/etc/hadoop中进行配置</span></p>
<p><em><span style="font-size:14px;">      $ cd /home/hadoop/hadoop/etc/hadoop/</span></em></p>
<p><span style="font-size:14px;">修改hadoop-env.sh<br>
修改JAVA_HOME，这里JAVA_HOME的路径必须指定为真实的路径，不能引用${JAVA_HOME}，否则运行的时候会有错误JAVA_HOME is not set</span></p>
<p><em><span style="font-size:14px;"></span></em></p>
<pre><code class="language-plain">export JAVA_HOME=／user/java/jdk1.6.0_35</code></pre><span style="font-size:14px;">修改core-site.xml</span><em><span style="font-size:14px;"><br></span></em><pre><code class="language-plain">&lt;configuration&gt;
  &lt;property&gt;
    &lt;name&gt;fs.default.name&lt;/name&gt;
    &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
  &lt;/property&gt;
  &lt;property&gt;
    &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
    &lt;value&gt;/tmp/hadoop/hadoop-${user.name}&lt;/value&gt;
  &lt;/property&gt;
&lt;/configuration&gt;
</code></pre><span style="font-size:14px;">修改hdfs-site.xml </span><em><br><span style="font-size:14px;"></span></em>
<p></p>
<p><span style="font-size:14px;">其中，/home/hadoop/dfs/name，/home/hadoop/dfs/data都是文件系统中的目录，需要先新建</span></p>
<p><em><span style="font-size:14px;"></span></em></p>
<pre><code class="language-plain">&lt;configuration&gt;
  &lt;property&gt;  
    &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;  
    &lt;value&gt;file:/home/hadoop/dfs/name&lt;/value&gt;  
    &lt;description&gt;Determines where on the local filesystem the DFS name node  
      should store the name table.  If this is a comma-delimited list  
      of directories then the name table is replicated in all of the  
      directories, for redundancy. &lt;/description&gt;  
    &lt;final&gt;true&lt;/final&gt;  
  &lt;/property&gt;  
  
  &lt;property&gt;  
    &lt;name&gt;dfs.datanode.data.dir&lt;/name&gt;  
    &lt;value&gt;file:/home/hadoop/dfs/data&lt;/value&gt;  
    &lt;description&gt;Determines where on the local filesystem an DFS data node  
       should store its blocks.  If this is a comma-delimited  
       list of directories, then data will be stored in all named  
       directories, typically on different devices.  
       Directories that do not exist are ignored.  
    &lt;/description&gt;  
    &lt;final&gt;true&lt;/final&gt;  
  &lt;/property&gt;  
  
  &lt;property&gt;  
    &lt;name&gt;dfs.replication&lt;/name&gt;  
    &lt;value&gt;1&lt;/value&gt;  
  &lt;/property&gt;  
  
  &lt;property&gt;  
    &lt;name&gt;dfs.permissions&lt;/name&gt;  
    &lt;value&gt;false&lt;/value&gt;  
  &lt;/property&gt;  
&lt;/configuration&gt;
</code></pre>修改mapred-site.xml
<p></p>
<p><em><span style="font-size:14px;"></span></em></p>
<pre><code class="language-plain">&lt;configuration&gt;
  &lt;property&gt;  
    &lt;name&gt;mapreduce.framework.name&lt;/name&gt;  
    &lt;value&gt;yarn&lt;/value&gt;  
  &lt;/property&gt; 
  
  &lt;property&gt;  
    &lt;name&gt;mapred.system.dir&lt;/name&gt;  
    &lt;value&gt;file:/home/hadoop/mapred/system&lt;/value&gt;  
    &lt;final&gt;true&lt;/final&gt;  
  &lt;/property&gt;  
  
  &lt;property&gt;  
    &lt;name&gt;mapred.local.dir&lt;/name&gt;  
    &lt;value&gt;file:/home/hadoop/mapred/local&lt;/value&gt;  
    &lt;final&gt;true&lt;/final&gt;  
  &lt;/property&gt;
&lt;/configuration&gt;
</code></pre><span style="font-size:14px;">修改yarn-site.xml</span><em><span style="font-size:14px;"><br></span></em><pre><code class="language-plain">&lt;configuration&gt;

&lt;!-- Site specific YARN configuration properties --&gt;
  &lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.resource-tracker.address&lt;/name&gt;
    &lt;value&gt;localhost:8081&lt;/value&gt;
    &lt;description&gt;host is the hostname of the resource manager and 
    port is the port on which the NodeManagers contact the Resource Manager.
    &lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.scheduler.address&lt;/name&gt;
    &lt;value&gt;localhost:8082&lt;/value&gt;
    &lt;description&gt;host is the hostname of the resourcemanager and port is the port
    on which the Applications in the cluster talk to the Resource Manager.
    &lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.scheduler.class&lt;/name&gt;
    &lt;value&gt;org.apache.hadoop.yarn.server.resourcemanager.scheduler.capacity.CapacityScheduler&lt;/value&gt;
    &lt;description&gt;In case you do not want to use the default scheduler&lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.resourcemanager.address&lt;/name&gt;
    &lt;value&gt;localhost:8083&lt;/value&gt;
    &lt;description&gt;the host is the hostname of the ResourceManager and the port is the port on
    which the clients can talk to the Resource Manager. &lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.local-dirs&lt;/name&gt;
    &lt;value&gt;&lt;/value&gt;
    &lt;description&gt;the local directories used by the nodemanager&lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.address&lt;/name&gt;
    &lt;value&gt;0.0.0.0:port&lt;/value&gt;
    &lt;description&gt;the nodemanagers bind to this port&lt;/description&gt;
  &lt;/property&gt;  

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.resource.memory-mb&lt;/name&gt;
    &lt;value&gt;10240&lt;/value&gt;
    &lt;description&gt;the amount of memory on the NodeManager in GB&lt;/description&gt;
  &lt;/property&gt;
 
  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.remote-app-log-dir&lt;/name&gt;
    &lt;value&gt;/app-logs&lt;/value&gt;
    &lt;description&gt;directory on hdfs where the application logs are moved to &lt;/description&gt;
  &lt;/property&gt;

   &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.log-dirs&lt;/name&gt;
    &lt;value&gt;&lt;/value&gt;
    &lt;description&gt;the directories used by Nodemanagers as log directories&lt;/description&gt;
  &lt;/property&gt;

  &lt;property&gt;
    &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;
    &lt;value&gt;mapreduce.shuffle&lt;/value&gt;
    &lt;description&gt;shuffle service that needs to be set for Map Reduce to run &lt;/description&gt;
  &lt;/property&gt;
&lt;/configuration&gt;
</code></pre>
<p></p>
<p></p>
<h3><span style="font-size:14px;">（3）启动hdfs以及yarn<br></span></h3>
<p><span style="font-size:14px;">完成以上配置后可以检测是否配置成</span></p>
<p><span style="font-size:14px;">首先格式化namenode</span></p>
<p><span style="font-size:14px;">         <em>$ hdfs namenode -format</em></span></p>
<p><span style="font-size:14px;">然后启动hdfs</span></p>
<p><span style="font-size:14px;">     <em>   $ start-dfs.sh</em> <br></span></p>
<p><span style="font-size:14px;">或者</span><span><span><span style="font-size:14px;"><br></span></span></span></p>
<p><em><span><span><span style="font-size:14px;">       $ hadoop-daemon.sh start namenode </span></span></span><span style="font-size:14px;"><span><br></span></span></em></p>
<p><em><span style="font-size:14px;"><span>       $ hadoop-daemon.sh start datanode</span></span></em></p>
<p><span style="font-size:14px;">接着启动yarn daemons</span></p>
<p><span style="font-size:14px;">      <em> $ start-yarn.sh</em></span></p>
<p><span style="font-size:14px;">或者</span><span><span><span style="font-size:14px;"><br></span></span></span></p>
<p><em><span><span><span style="font-size:14px;">      $ yarn-daemon.sh start resourcemanager </span></span></span><span style="font-size:14px;"><span><br></span></span></em></p>
<p><span><em><span style="font-size:14px;">      $ yarn-daemon.sh start nodemanager</span></em><br></span></p>
<p><span style="font-size:14px;">启动完成后可以进入http://localhost:50070/dfshealth.jsp 查看dfs状态，如下图所示</span></p>
<p><span style="font-size:14px;">转载请注明：http://blog.csdn.net/lawrencesgj/article/details/8240571</span></p>
<p><span style="font-size:14px;"><img alt="" src="http://img.my.csdn.net/uploads/201211/29/1354196048_8217.jpg"><br></span></p>
<p></p>
<h2>Eclipse环境配置</h2>
<h3><span style="font-size:14px;">（1）下载eclipse插件并导入到eclipse中</span></h3>
<p><span style="font-size:18px;"><span style="font-size:14px;">         到http://wiki.apache.org/hadoop/EclipsePlugIn上下载对应的eclipse插件，然后eclipse的plugin目录中，我的环境是/usr/share/eclipse/plugins</span></span></p>
<h3><span style="font-size:18px;"><span style="font-size:14px;">（2）使用hadoop用户启动eclipse</span></span></h3>
<p><span style="font-size:18px;"><span style="font-size:14px;">       <em>$ su hadoop</em></span></span></p>
<p><em><span style="font-size:18px;"><span style="font-size:14px;">       $ eclipse&amp;</span></span></em></p>
<h3><span style="font-size:18px;"><span style="font-size:14px;">（3）配置eclipse</span></span></h3>
<p><span style="font-size:18px;"><span style="font-size:14px;">       启动后在视图中调出mapreduce视图，并点击右下角new hadoop的大象图标，如下图</span></span></p>
<p><span style="font-size:18px;"><img alt="" src="http://img.my.csdn.net/uploads/201211/29/1354194596_6664.jpg"><br></span></p>
<p><span style="font-size:14px;">       填写安装目录以及用户明后Vaildate Location 就可以看到发现hadoop2.02了！这里我自己花了一点时间摸索，要完成关键是用hadoop用户启动eclipse，之前用系统的用户一直验证不成功。。<br></span></p>
<p><span style="font-size:14px;">（3）新建mapreduce project</span></p>
<p><span style="font-size:14px;">       如无意外，在File&gt;&gt;New&gt;&gt;Project下面就会看到有Mapreduce Project，点击后需要配置hadoop install directory，如果用mapreduce框架配置为/home/hadoop/hadoop/share/hadoop/mapreduce，如果用yarn框架配置为/home/hadoop/hadoop/share/hadoop/yarn即可。</span></p>
<p><span style="font-size:14px;"></span></p>
<p><span style="font-size:14px;">      转载请注明：http://blog.csdn.net/lawrencesgj/article/details/8240571</span></p>
<p><span style="font-size:14px;"><img alt="" src="http://img.my.csdn.net/uploads/201211/29/1354195510_1551.jpg"><br></span></p>
<p><span style="font-size:14px;">       一开始因为没有用hadoop用户打开eclipse所以这里费了不少时间，后来才醒悟过来。这样我们就新建了一个mapreduce项目了！<br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">参考：</span></p>
<ul><li><span style="font-size:14px;">http://ipjmc.iteye.com/blog/1703112</span></li><li><span style="font-size:14px;">http://slaytanic.blog.51cto.com/2057708/885198</span></li><li><span style="font-size:14px;">http://hadoop.apache.org/docs/r2.0.2-alpha/hadoop-yarn/hadoop-yarn-site/SingleCluster.html</span></li><li><span style="font-size:14px;">http://wiki.apache.org/hadoop/EclipseEnvironment<br></span></li></ul><p><span style="font-size:14px;"></span><br></p>
            </div>
                </div>
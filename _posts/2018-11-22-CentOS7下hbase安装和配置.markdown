---
layout:     post
title:      CentOS7下hbase安装和配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>版本 hadoop2.7.6</p>

<p>版本 hbase-1.2.7</p>

<p> </p>

<p>#Hadoop启动</p>

<p>#hbase启动，cd /usr/lib/hbase/bin</p>

<p>/usr/lib/hbase/bin/start-hbase.sh</p>

<p>#进入HBASE</p>

<p>/usr/lib/hbase/bin/hbase shell</p>

<p> </p>

<p>##########################################以下为HBASE安装过程</p>

<p>#解压文件到指定目录</p>

<p>tar -zxvf /hbase-1.2.7-bin.tar.gz -C /usr/lib/</p>

<p>#修改文件夹名称</p>

<p>mv hbase-1.2.7 hbase</p>

<p> </p>

<p>#配置 hbase-site.xml 文件</p>

<p>/usr/lib/hbase/conf</p>

<p>##增加</p>

<pre class="has">
<code>&lt;property&gt;

&lt;name&gt;hbase.rootdir&lt;/name&gt; &lt;!-- hbase存放数据目录 --&gt;

&lt;value&gt;hdfs://itcast01:9000/hbase&lt;/value&gt;

&lt;!-- 端口要和Hadoop的fs.defaultFS端口一致--&gt;
&lt;/property&gt;

&lt;property&gt;
&lt;name&gt;hbase.cluster.distributed&lt;/name&gt; &lt;!-- 是否分布式部署 --&gt;
&lt;value&gt;true&lt;/value&gt;

&lt;/property&gt;</code></pre>

<p>#设置环境变量</p>

<p>.bashrc 隐藏的文件和profile 都要改，然后使用 source .XXX 使其生效</p>

<p>修改HBase下的conf目录中的hbase-env.sh文件</p>

<p>export JAVA_HOME=/usr/java/jdk1.8.0_181</p>

<p>export HBASE_MANAGES_ZK=true &lt;!--使用自带的Zookeper--&gt;</p>

<p>#修改 /etc/profile 文件或者  #修改 根目录下  .bashrc文件</p>

<p># set hbase path</p>

<p>export PATH=$PATH:/usr/lib/hbase/bin</p>

<p>#启动，在bin目录下</p>

<p>./start-hbase.sh 启动</p>

<p>/stop-hbase.sh 停止</p>

<p>使用jps命令查看</p>

<p> </p>

<p>#在启动连接的时候，连接报错，在Windows中添加了Hadoop-home的环境变量后正常</p>

<p>#在连接时报错 Call exception，</p>

<p> </p>

<p>/hbase-1.0.3/conf/hbase-env.sh</p>

<p>export HBASE_MANAGES_ZK=true</p>

<p>说明：BASE_MANAGES_ZK 来切换zookeeper。如果未true，是让Hbase启动的时候同时也启动zookeeper。如果自己安装了zookeeper，则这里要配置成false。</p>

<p>我直接配置单机版，默认使用内部的zookeeper，未单独安装，所以配置成了true。</p>

<p> </p>

<p>3.2.2 验证启动</p>

<p>1. 在hadoop节点使用jps查看节点状态</p>

<p> </p>

<p><img alt="" class="has" height="190" src="https://img-blog.csdn.net/20181014174228496?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbWluZ2NhaTE2OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="654"></p>

<p> </p>

<p>2. 进入hbase的shell命令行，创建表member并进行查看</p>

<p>hbase shell</p>

<p>hbase&gt;create 'member', 'm_id', 'address', 'info'</p>

<p><img alt="" class="has" height="186" src="https://img-blog.csdn.net/20181014174458783?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpbWluZ2NhaTE2OA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="516"></p>

<p> </p>

<p>遇到的问题（坑）</p>

<p>1 execstack -c /itcast/hadoop-2.7.6/lib/native/libhadoop.so.1.0.0 这个错误主要在window解压之后再打包上传到服务器，造成文件格式不对</p>

<p>2 hbase主节点HMaster启动失败</p>

<p>ERROR: Can't get master address from ZooKeeper; znode data == null</p>

<p>注意：看错误提示运行hbase(zookeeper)的用户无法写入zookeeper文件，导致znode data为空。</p>

<p>解决：在hbase-site.xml指定一个运行hbase的用户有写入文件权限的目录作为zookeeper数据目录，如</p>

<p>&lt;property&gt;</p>

<p>&lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;</p>

<p>&lt;value&gt;/opt/zkData&lt;/value&gt; &lt;!--这个目录是你自己设置的目录--&gt;</p>

<p>&lt;/property&gt;</p>

<p> </p>

<p>（2）hbase-site.xml文件中的</p>

<p>&lt;property&gt;</p>

<p>&lt;name&gt;hbase.rootdir&lt;/name&gt;</p>

<p>&lt;value&gt;hdfs://itcast01:9000/hbase&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p> </p>

<p>rootdir中的IP设定很重要，需要设定对应的IP</p>

<p>与core-site.xml中fs.defaultFS中的路径不相同</p>

<p>&lt;property&gt;</p>

<p>&lt;name&gt;fs.defaultFS&lt;/name&gt;</p>

<p>&lt;value&gt;hdfs://itcast01:9000&lt;/value&gt;</p>

<p>&lt;/property&gt;</p>

<p>不能写IP，必须在host文件里配置 域名 与hadoop配置一样</p>

<p> </p>

<p>3 关闭hbase服务的时候，HRegionserver服务一直关不掉,修改配置文件无法重启生效，hbase-daemon.sh stop regionserver RegionServer就报错提示如下</p>

<p>no zookeeper to stop because no pid file /tmp/hbase-hadoop-zookeeper.pid</p>

<p>原因是，默认情况下pid文件保存在/tmp目录下，/tmp目录下的文件很容易丢失，</p>

<p>解决办法：在hbase-env.sh中修改pid文件的存放路径；</p>

<p>在hbase-env.sh中下面的文字默认是注释掉的，放开即可，也可以自己指定存放位置：</p>

<p># The directory where pid files are stored. /tmp by default.</p>

<p>export HBASE_PID_DIR=/opt/hbase/pids</p>

<p> </p>

<p> </p>            </div>
                </div>
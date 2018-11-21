---
layout:     post
title:      hadoop安装之-hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章,未经博主允许不得转载。					https://blog.csdn.net/Stubborn_Cow/article/details/49761017				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
一、安装准备<br>
1， 下载hbase包 http://mirrors.hust.edu.cn/apache/<br>
        hbase的版本和hadoop的版本存在对应关系，本例的hadoop是2.6，所以使用hbase-1.0.2-bin.tar.gz，太低可能造成hbase安装或使用错误。<br><br>
二、安装hbase<br>
1，解压文件hbase-1.0.2-bin.tar.gz到usr/local下<br>
      /usr/local/hbase<br>
2，添加环境变量<br>
      vim /etc/profile<br>
      # set hbase<br>
      export HBASE_HOME=/usr/local/hbase<br>
      export PATH=$PATH:$HBASE_HOME/bin<br>
3，配置hbase文件，在/usr/local/hbase/conf下<br>
(1) hbase-site.xml，修改配置文件<br>
      sys1为机器名，不要用IP，使用IP会报错<br>
&lt;configuration&gt;<br>
&lt;property&gt;<br>
           &lt;name&gt;hbase.master&lt;/name&gt;<br>
           &lt;value&gt;sys1:6000&lt;/value&gt;<br>
   &lt;/property&gt;<br>
   &lt;property&gt;<br>
           &lt;name&gt;hbase.rootdir&lt;/name&gt;<br>
           &lt;value&gt;hdfs://sys1:9000/hbase&lt;/value&gt;<br>
   &lt;/property&gt;<br>
   &lt;property&gt;<br>
           &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;<br>
           &lt;value&gt;true&lt;/value&gt;<br>
   &lt;/property&gt;<br>
   &lt;property&gt;<br>
           &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;<br>
           &lt;value&gt;sys1&lt;/value&gt;<br>
   &lt;/property&gt;<br>
   &lt;property&gt;<br>
           &lt;name&gt;hbase.tmp.dir&lt;/name&gt;<br>
           &lt;value&gt;/usr/local/hbase/tmp&lt;/value&gt;<br>
   &lt;/property&gt;<br>
   &lt;property&gt;<br>
           &lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;<br>
           &lt;value&gt;/usr/local/hbase/zookeeper&lt;/value&gt;<br>
   &lt;/property&gt;<br>
  &lt;property&gt;<br>
           &lt;name&gt;dfs.replication&lt;/name&gt;<br>
           &lt;value&gt;1&lt;/value&gt;<br>
   &lt;/property&gt;<br>
&lt;/configuration&gt;<br>
(2) hbase-env.sh，修改配置文件<br>
export JAVA_HOME=/usr/local/jdk<br>
export HBASE_CLASSPATH= /usr/local/hadoop/etc/hadoop<br>
#使用hbase自带的zookeeper<br>
export HBASE_MANAGES_ZK=true<br>
(3) regionservers，添加从机器的名<br>
      hadoop1<br>
      hadoop2<br><br>
三、启动并测试<br>
1，启动hbase服务<br>
      start-hbase.sh<br>
2，进入hbase<br>
      hbase shell<br>            </div>
                </div>
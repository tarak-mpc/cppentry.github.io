---
layout:     post
title:      Spark on YARN 部署实验
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wzy0623/article/details/51120680				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
        以前的Spark部署都是使用的standalone方式，集群中的每台机器都安装部署Spark，然后启动Master和Worker进程运行Spark。今天尝试一下Spark on YARN的部署方式。<br><br><strong>一、实验目的</strong><br>
1. 只在一台机器上安装Spark，基于已有的Hadoop集群，使用YARN调度资源。<br>
2. 不启动Master和Worker进程提交Spark作业。<br>
3. 通过YARN的WebUI查看Spark作业的执行情况。<br><br><strong>二、实验环境：</strong><br>
4台CentOS release 6.4虚拟机，IP地址为<br>
192.168.56.101<br>
192.168.56.102<br>
192.168.56.103<br>
192.168.56.104<br><br>
192.168.56.101是Hadoop集群的主，运行NameNode和ResourceManager进程。<br>
192.168.56.102、192.168.56.103是Hadoop的从，运行DataNode和NodeManager进程。<br>
192.168.56.104安装Pentaho的PDI，安装目录为/home/grid/data-integration。<br><br>
Hadoop版本：2.7.2<br>
Spark版本：1.5.0<br>
PDI版本：6.0<br><br>
Hadoop集群的安装配置参考<a href="http://blog.csdn.net/wzy0623/article/details/50681554" rel="nofollow">http://blog.csdn.net/wzy0623/article/details/50681554</a><br><br><strong>三、安装Spark</strong><br>
只在192.168.56.101一台机器上上安装Spark，具体安装步骤参考<a href="http://blog.csdn.net/wzy0623/article/details/50946766" rel="nofollow">http://blog.csdn.net/wzy0623/article/details/50946766</a><br><br><strong>四、配置步骤</strong><br>
1. 启动Hadoop集群<br>
# 启动hdfs<br>
/home/grid/hadoop-2.7.2/sbin/start-dfs.sh<br>
# 启动yarn<br>
/home/grid/hadoop-2.7.2/sbin/start-yarn.sh<br><br>
2. 将spark自带的与Hadoop集成的jar包上传到hdfs<br>
hadoop fs -put /home/grid/spark/lib/spark-assembly-1.5.0-hadoop2.6.0.jar /user/<br><br>
3. 编辑spark-defaults.conf文件，添加如下一行<br>
spark.yarn.jar=hdfs://master:9000/user/spark-assembly-1.5.0-hadoop2.6.0.jar<br>
修改后的spark-defaults.conf文件如图1所示<br><p style="text-align:center;"><img src="https://img-blog.csdn.net/20160411123651901?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="text-align:center;">图1</p>
<strong>五、测试</strong><br>
1. 运行spark的示例run-example，提交到yarn<br>
cd $SPARK_HOME/bin<br>
cp run-example run-example2<br>
# 需要将EXAMPLE_MASTER设置成yarn<br>
vi run-example2<br>
EXAMPLE_MASTER="yarn"<br>
# 执行示例<br>
./run-example2 org.apache.spark.examples.SparkPi 2<br>
执行结果如图2所示<br><p style="text-align:center;"><img src="https://img-blog.csdn.net/20160411123721183?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="text-align:center;">图2</p>
<p>2. Kettle提交Spark作业到yarn</p>
<p>以下是本实验相关的步骤，关于PDI提交Spark作业的配置参考<a href="http://blog.csdn.net/wzy0623/article/details/51097471" rel="nofollow">http://blog.csdn.net/wzy0623/article/details/51097471</a></p>
# 将yarn和spark配置文件拷贝到客户端机器<br>
scp /home/grid/hadoop/etc/hadoop/yarn-site.xml 192.168.56.104:/home/grid/data-integration/plugins/pentaho-big-data-plugin/hadoop-configurations/cdh54/<br>
scp /home/grid/spark/conf/spark-defaults.conf 192.168.56.104:/home/grid/spark/conf/<br><br>
修改PDI的Spark例子<br>
cp /home/grid/data-integration/samples/jobs/Spark\ Submit/Spark\ submit.kjb /home/grid/data-integration/test/Spark\ Submit\ Sample.kjb<br>
在Kettle中打开/home/grid/data-integration/test/Spark\ Submit\ Sample.kjb文件，编辑Spark Submit Sample作业项，填写如图3所示的信息<br><p style="text-align:center;"><img src="https://img-blog.csdn.net/20160411123751199?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="text-align:center;">图3</p>
执行Spark Submit Sample作业，结果如图4所示<br><p style="text-align:center;"><img src="https://img-blog.csdn.net/20160411123812058?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="text-align:center;">图4</p>
<br>
3. 在yarn的WebUI查看作业的运行情况<br><p>http://<span style="font-size:13.3333px;">192.168.56.101:8088/</span></p>
<p>正在执行的spark作业如图5所示</p>
<p style="text-align:center;"><img src="https://img-blog.csdn.net/20160411123855091?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="text-align:center;">图5</p>
点击“ApplicationMaster”，进入Spark作业页面，如图6所示<br><p style="text-align:center;"><img src="https://img-blog.csdn.net/20160411123918170?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="text-align:center;">图6</p>
作业执行完，应用历史页面如图7所示<br><p style="text-align:center;"><img src="https://img-blog.csdn.net/20160411123952356?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p style="text-align:center;">图7</p>
<p style="text-align:left;">4. 执行SparkSQL<br>
关于用beeline连接SparkSQL的相关配置，参考<a href="http://blog.csdn.net/wzy0623/article/details/50999197" rel="nofollow">http://blog.csdn.net/wzy0623/article/details/50999197</a><br>
# 启动mysql，用于存储hive的元数据<br>
mysqld &amp;<br>
# 启动hive元数据存储服务<br>
hive --service metastore &gt; /tmp/grid/hive_metastore.log 2&gt;&amp;1 &amp;<br>
# 启动spark thriftserver服务<br>
$SPARK_HOME/sbin/start-thriftserver.sh --master yarn<br>
此时在yarn的WebUI查看应用的运行情况，如图8所示<br></p>
<div style="text-align:center;"><img src="https://img-blog.csdn.net/20160411142652766?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="font-size:12px;"></div>
<p></p>
<p style="text-align:left;"></p>
<div style="text-align:center;"><span style="font-size:12px;">图8</span></div>
# 登入beeline<br>
$SPARK_HOME/bin/beeline -u jdbc:hive2://192.168.56.101:10001/<br>
执行SparkSQL查询：<br>
use test;<br>
select * from session_test;<br>
select count(*) from session_test;<br>
此时在yarn的WebUI查看Spark作业的运行情况，如图9所示<br><div style="text-align:center;"><img src="https://img-blog.csdn.net/20160411142732408?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="" style="font-size:12px;"></div>
<p></p>
<p style="text-align:center;">图9<br></p>
<p style="text-align:left;"><strong>参考：</strong></p>
<p style="text-align:left;"><a href="http://blog.csdn.net/xiao_jun_0820/article/details/46561097" rel="nofollow">http://blog.csdn.net/xiao_jun_0820/article/details/46561097</a><br></p>
            </div>
                </div>
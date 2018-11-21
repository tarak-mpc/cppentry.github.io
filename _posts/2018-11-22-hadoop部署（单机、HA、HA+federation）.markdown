---
layout:     post
title:      hadoop部署（单机、HA、HA+federation）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/m0_37746890/article/details/78655912				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><strong></strong></p>
<h1><strong><span style="font-family:'宋体';font-size:22pt;">Hadoop<span style="font-family:'宋体';">目录结构及其意思：</span></span><span style="font-family:'宋体';font-size:22pt;"></span></strong></h1>
<img src="https://img-blog.csdn.net/20171128160925841?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p></p>
<p><strong><br></strong></p>
<p><strong></strong></p>
<h1><strong>单机部署：</strong></h1>
<p></p>
<p>1、tar -zvxf java.......tar.gz</p>
<p>2、tar -zvxf hadoop .... .tar.gz</p>
<p>3、<span style="font-family:'宋体';">修改</span>hadoop <span style="font-family:'宋体';">目录下的</span><span style="font-family:Calibri;">etc/hadoop/hadoop-env.sh</span></p>
<p>Java_home<span style="font-family:'宋体';">配置：</span></p>
<p>export JAVA_HOME = jdk<span style="font-family:'宋体';">地址</span></p>
<p>4、<span style="font-family:'宋体';">修改</span>hadoop <span style="font-family:'宋体';">目录下的</span><span style="font-family:Calibri;">etc/hadoop/</span><span style="color:rgb(255,0,0);">mapred-site.xml</span></p>
<p>  &lt;configuration&gt;</p>
<p>&lt;property&gt;</p>
<p>  &lt;name&gt;mapreduce.framework.name&lt;/name&gt;</p>
<p>  &lt;value&gt;yarn&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;/configuration&gt; </p>
<p>5、修改<span style="color:rgb(255,0,0);">slaves</span>文件</p>
<p><span style="font-family:'宋体';">加上</span> slaves<span style="font-family:'宋体';">的主机名</span></p>
<p>6、修改<span style="color:rgb(255,0,0);">core-site.xml</span></p>
<p>   &lt;property&gt;</p>
<p> &lt;name&gt;fs.default.name&lt;/name&gt;</p>
<p> &lt;value&gt;hdfs://<span style="color:rgb(255,0,0);">YARN001</span>:8020&lt;/value&gt;</p>
<p>   &lt;/property&gt;</p>
<p>  <span style="font-family:'宋体';">其中</span><span style="color:rgb(255,0,0);">YARN001
</span><span style="font-family:'宋体';">在</span>/etc/hosts <span style="font-family:'宋体';">
下配置</span></p>
<p>7、修改<span style="color:rgb(255,0,0);">yarn-site.xml</span></p>
<p>    &lt;property&gt;</p>
<p> &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;</p>
<p> &lt;value&gt;mapreduce_shuffle&lt;/value&gt;</p>
<p>    &lt;/property&gt;</p>
<p> </p>
<p>8、修改<span style="color:rgb(255,0,0);">core-site.xml<span style="font-family:'宋体';">，配置副本数量</span></span></p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;dfs.replication&lt;/name&gt;   </p>
<p>&lt;value&gt;1&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>9<span style="font-family:'宋体';">、格式化</span><span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">：</span><span style="font-family:Calibri;">bin/hadoop namenode -format</span></p>
<p>10<span style="font-family:'宋体';">、启动</span><span style="font-family:Calibri;">hdfs: sbin/ start-dfs.sh</span></p>
<p>11<span style="font-family:'宋体';">、启动</span><span style="font-family:Calibri;">yarn: sbin/ start-yarn.sh</span></p>
<p> </p>
<p>Yarn web:   http://yarn001:8088</p>
<p>Hdfs web:   <a href="http://yarn001:50070" rel="nofollow"><u><span style="color:rgb(0,0,255);">http://yarn001:50070</span></u></a></p>
<p> </p>
<p> </p>
<p><strong><u><span style="font-family:'宋体';">重启失败</span>:</u></strong></p>
<p><span style="font-family:'宋体';">修改</span>core-site.xml</p>
<p>  &lt;property&gt;</p>
<p> &lt;name&gt;dfs.namenode.name.dir&lt;/name&gt;</p>
<p> &lt;value&gt;home/luobida/hadoop/dfs/name&lt;/value&gt;</p>
<p>   &lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;dfs.namenode.data.dir&lt;/name&gt;</p>
<p>&lt;value&gt;home/luobida/hadoop/dfs/data&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p> </p>
<p><span style="font-family:'宋体';">如果不配置的话，</span>hadoop<span style="font-family:'宋体';">会默认配置到</span><span style="font-family:Calibri;">linux</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">/tmp</span><span style="font-family:'宋体';">目录下，然而</span><span style="font-family:Calibri;">/tmp</span><span style="font-family:'宋体';">目录在</span><span style="font-family:Calibri;">linux</span><span style="font-family:'宋体';">重启都会被清空，需要重新格式化，并且会丢失</span><span style="font-family:Calibri;">namenode</span><span style="font-family:'宋体';">上元信息，从而与</span><span style="font-family:Calibri;">datanode</span><span style="font-family:'宋体';">上的信息不一致。</span></p>
<br><p><strong></strong></p>
<h1><strong>多机部署（生产环境）：</strong></h1>
<p></p>
<p>Hdfs ha <span style="font-family:'宋体';">部署：</span></p>
<img src="https://img-blog.csdn.net/20171128161016825?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><strong></strong></p>
<p>hdfs-site.xml<span style="font-family:'宋体';">各个配置的解释：</span></p>
<p>dfs.nameservices :<span style="font-family:'宋体';">集群中命名服务列表</span></p>
<p>dfs.ha.namenodes.${ns}:<span style="font-family:'宋体';">命名服务中的</span><span style="font-family:Calibri;">namenode</span><span style="font-family:'宋体';">的逻辑名称</span></p>
<p>dfs.namenode.rpc-address.${ns}.${nn}:<span style="font-family:'宋体';">命名服务中逻辑名称对应的</span><span style="font-family:Calibri;">rpc</span><span style="font-family:'宋体';">地址</span></p>
<p>dfs.namenode.http-address.${ns}.${nn}:<span style="font-family:'宋体';">命名服务中逻辑名称对应的</span><span style="font-family:Calibri;">http</span><span style="font-family:'宋体';">地址</span></p>
<p>dfs.namenode.name.dir:  namenode<span style="font-family:'宋体';">存放目录</span></p>
<p>dfs.namenode.shared.edits.dir:  <span style="font-family:'宋体';">主备</span><span style="font-family:Calibri;">namenode</span><span style="font-family:'宋体';">同步元信息的共享存储系统</span></p>
<p>dfs.journalnode.edits.dir:  journalnode<span style="font-family:'宋体';">数据存放的目录</span></p>
<p> </p>
<p> </p>
<p><strong>hdfs-site.xml<span style="font-family:'宋体';">配置</span><span style="font-family:Calibri;">:</span></strong></p>
<img src="https://img-blog.csdn.net/20171128161114915?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><strong><img src="https://img-blog.csdn.net/20171128161129016?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></strong></p>
<p><strong></strong></p>
<p><strong>core-site.xml<span style="font-family:'宋体';">配置：</span><span style="color:rgb(255,0,0);"><span style="font-family:'宋体';">主</span>namenode<span style="font-family:'宋体';">地址</span></span></strong></p>
   <img src="https://img-blog.csdn.net/20171128161205245?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><strong><br></strong></p>
<p><strong></strong></p>
<p><strong>slaves<span style="font-family:'宋体';">配置：</span></strong></p>
<img src="https://img-blog.csdn.net/20171128161237926?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><strong></strong></p>
<p>启动方式：</p>
<p>1、<span style="font-family:'宋体';">启动各个节点上的</span>journalnode<span style="font-family:'宋体';">服务：</span><span style="font-family:Calibri;">sbin/hadoop-daemon.sh start journalnode</span></p>
<p>2、<span style="font-family:'宋体';">格式化</span>namenode1<span style="font-family:'宋体';">，并且启动</span><span style="font-family:Calibri;">namenode1</span><span style="font-family:'宋体';">：</span><span style="font-family:Calibri;">bin/hdfs namenode -format    </span></p>
<p>                                                      sbin/hadoop-daemon.sh start namenode</p>
<p>3、<span style="font-family:'宋体';">在</span>namenode2<span style="font-family:'宋体';">上同步元信息：</span><span style="font-family:Calibri;">bin/hdfs namenode bootstrapStandby</span></p>
<p>4、<span style="font-family:'宋体';">启动</span>namenode2<span style="font-family:'宋体';">：
</span><span style="font-family:Calibri;">sbin/hadoop-daemon.sh start namenode</span></p>
<p>5、<span style="font-family:'宋体';">将</span>namenode1<span style="font-family:'宋体';">切换为</span><span style="font-family:Calibri;">active</span><span style="font-family:'宋体';">：</span><span style="font-family:Calibri;">bin/hdfs haadmin -transitionToActive nn1</span></p>
<p>6、<span style="font-family:'宋体';">在</span>namenode1<span style="font-family:'宋体';">上启动</span><span style="font-family:Calibri;">datanode</span><span style="font-family:'宋体';">：</span><span style="font-family:Calibri;">sbin/hadoop-daemons.sh start datanode</span></p>
<br><p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong></strong></p>
<h1><strong>Hdfs HA+federation:</strong></h1>
<p></p>
<p>架构图：</p>
<img src="https://img-blog.csdn.net/20171128161355188?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><strong></strong></p>
<p>Hdfs-site.xml<span style="font-family:'宋体';">配置</span><span style="font-family:Calibri;">:</span></p>
<img src="https://img-blog.csdn.net/20171128161437683?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br><p><strong><img src="https://img-blog.csdn.net/20171128161452816?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbTBfMzc3NDY4OTA=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></strong></p>
<p><strong><br></strong></p>
<p><strong><br></strong></p>
<p><strong>hadoop-cluster1中nn1和nn2配置如下（hdfs-site.xml）：</strong></p>
<p>&lt;property&gt;</p>
<p>   &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;</p>
<p>   &lt;value&gt;qjournal://SY-0355:8485;SY-0225:8485;SY-0226:8485;/<span style="color:#ff0000;">hadoop-cluster1</span>&lt;/value&gt;</p>
<p>&lt;/property&gt;<br></p>
<p><br></p>
<p><strong>hadoop-cluster2中nn3和nn4配置如下（hdfs-site.xml）：</strong><br></p>
<p></p>
<p>&lt;property&gt;</p>
<p>   &lt;name&gt;dfs.namenode.shared.edits.dir&lt;/name&gt;</p>
<p>   &lt;value&gt;qjournal://SY-0355:8485;SY-0225:8485;SY-0226:8485;/<span style="color:#ff0000;">hadoop-cluster2</span>&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<br><p>启动：</p>
<p><strong>在nn1和nn2节点上进行如下操作：</strong></p>
<p>1、在各个journalnode节点上： sbin/hadoop-daemon.sh start journalnode</p>
<p>2、在nn1上，进行格式化和启动：</p>
<p>      bin/hdfs namenode -format <span style="color:#ff0000;">-clusterId hadoop-cluster</span></p>
<p><span style="color:#ff0000;">     </span> sbin/hadoop-daemon.sh start namenode</p>
<p>3、在nn2上，同步nn1上元信息：bin/hdfs namenode -bootstrapStandby</p>
<p>4、在nn2启动namenode</p>
<p>（经过上面四步后，nn1和nn2还是处于standby状态）</p>
<p>5、在nn1上，将namenode切换成active状态：bin/hdfs haadmin <span style="color:#ff0000;">-ns hadoop-cluster1</span> -transitionToActive nn1</p>
<p><br></p>
<p></p>
<p><strong>在nn3和nn4节点上进行如下操作：</strong></p>
<p>1、在各个journalnode节点上： sbin/hadoop-daemon.sh start journalnode</p>
<p>2、在nn3上，进行格式化和启动：</p>
<p>      bin/hdfs namenode -format <span style="color:#ff0000;">-clusterId hadoop-cluster</span></p>
<p><span style="color:#ff0000;">     </span> sbin/hadoop-daemon.sh start namenode</p>
<p>3、在nn4上，同步nn3上元信息：bin/hdfs namenode -bootstrapStandby</p>
<p>4、在nn4启动namenode</p>
<p>（经过上面四步后，nn3和nn4还是处于standby状态）</p>
<p>5、在nn3上，将namenode切换成active状态：bin/hdfs haadmin <span style="color:#ff0000;">-ns hadoop-cluster1</span> -transitionToActive nn3</p>
<p><br></p>
<p><strong>最后：在nn1上启动datanode：</strong></p>
<p>sbin/hadoop-daemons.sh start datanode</p>
<br>            </div>
                </div>
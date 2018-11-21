---
layout:     post
title:      Linux上安装和配置Hadoop
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.<span style="font-family:'宋体';">上传</span><span style="font-family:Arial;">Hadoop</span><span style="font-family:'宋体';">包</span></p>
<p>2<span style="font-family:'宋体';">解压</span><span style="font-family:Arial;">Hadoop</span><span style="font-family:'宋体';">包</span></p>
<p>创建一个目录 <span style="font-family:Arial;">mkdir  /itcast</span></p>
<p>tar -zxvf hadoop-2.7.2.tar.gz -C /itcast</p>
<p>3.配置<span style="font-family:Arial;">Hadoop</span><span style="font-family:'宋体';">伪分布式（修改</span>五个文件）</p>
<p>首先转到<span style="font-family:Arial;">hadoop</span><span style="font-family:'宋体';">目录下</span></p>
<p>cd /itcast/hadoop-2.7.2/etc/hadoop</p>
<p>第一个修改<span style="font-family:Calibri;">hadoop-env.sh</span></p>
<p>vim hadoop-env.sh</p>
<p>export <a name="OLE_LINK9">JAVA_HOME=/usr/java/jdk1.7.0_79</a></p>
<p>第二个修改<span style="font-family:Calibri;">core-site.xml</span></p>
<p>vim core-site.xml</p>
<p>&lt;configuration&gt;</p>
<p>&lt;!--<span style="font-family:'宋体';">用来指定</span><span style="font-family:Calibri;">HDFS</span><span style="font-family:'宋体';">的</span><span style="font-family:Calibri;">NameNode</span><span style="font-family:'宋体';">的地址</span><span style="font-family:Calibri;">--&gt;</span></p>
<p>      &lt;property&gt;</p>
<p>            &lt;name&gt;fs.defaultFS&lt;/name&gt;</p>
<p>            &lt;value&gt;hdfs://senwen1:9000&lt;/value&gt;</p>
<p>      &lt;/property&gt;</p>
<p>&lt;!--<span style="font-family:'宋体';">用来指定</span><span style="font-family:Calibri;">Hadoop</span><span style="font-family:'宋体';">运行时产生文件的存放目录</span><span style="font-family:Calibri;">--&gt;</span></p>
<p>      <a name="OLE_LINK1">&lt;property&gt;</a></p>
<p>            &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;</p>
<p>            &lt;value&gt;/itcast/hadoop-2.7.2/tmp&lt;/value&gt;</p>
<p>      &lt;/property&gt;</p>
<p>&lt;/configuration&gt;</p>
<p>第三个修改<span style="font-family:Calibri;">hdfs-site.xml</span></p>
<p>&lt;configuration&gt;</p>
<p>&lt;!-- <span style="font-family:'宋体';">指定</span><span style="font-family:Calibri;">HDFS</span><span style="font-family:'宋体';">保存数据副本的数量</span><span style="font-family:Calibri;">--&gt;</span></p>
<p>      &lt;property&gt;</p>
<p>            &lt;name&gt;dfs.replication&lt;/name&gt;</p>
<p>            &lt;value&gt;1&lt;/value&gt;</p>
<p>      &lt;/property&gt;</p>
<p><a name="OLE_LINK2"></a>&lt;/configuration&gt;</p>
<p>第四个修改<span style="font-family:Calibri;">mapred-site.xml</span></p>
<p>原目录下没有该文件，只有一个示例文件，需要将该示例文件重命名后进行修改。</p>
<p>[root@senwen1 hadoop]# mv mapred-site.xml.template mapred-site.xml</p>
<p>&lt;configuration&gt;</p>
<p>&lt;!-- <span style="font-family:'宋体';">告诉</span><span style="font-family:Calibri;">Hadoop</span><span style="font-family:'宋体';">以后</span><span style="font-family:Calibri;">MR</span><span style="font-family:'宋体';">运行在</span><span style="font-family:Calibri;">yarn</span><span style="font-family:'宋体';">上</span><span style="font-family:Calibri;">--&gt;</span></p>
<p>      <a name="OLE_LINK3">&lt;property&gt;</a></p>
<p>            &lt;name&gt;mapreduce.framework.name&lt;/name&gt;</p>
<p>            &lt;value&gt;yarn&lt;/value&gt;</p>
<p>      &lt;/property&gt;</p>
<p><a name="OLE_LINK4"></a>&lt;/configuration&gt;</p>
<p>第五个修改<span style="font-family:Calibri;">yarn-site.xml</span></p>
<p><a name="OLE_LINK5"></a>&lt;configuration&gt;</p>
<p><a name="OLE_LINK7"></a>&lt;!--NodeManager<span style="font-family:'宋体';">获取数据的方式是</span><span style="font-family:Calibri;">shuffle--&gt;</span></p>
<p>       &lt;property&gt;</p>
<p>            &lt;name&gt;yarn.nodemanager.aux-services&lt;/name&gt;</p>
<p>            &lt;value&gt;mapreduce_shuffle&lt;/value&gt;</p>
<p>      &lt;/property&gt;</p>
<p>       &lt;property&gt;</p>
<p>&lt;!--<span style="font-family:'宋体';">指定</span><span style="font-family:Calibri;">yarn</span><span style="font-family:'宋体';">的老大（</span><span style="font-family:Calibri;">ResourceManager</span><span style="font-family:'宋体';">）的地址</span><span style="font-family:Calibri;">--&gt;</span></p>
<p>      &lt;property&gt;</p>
<p>            &lt;name&gt;yarn.resourcemanager.hostname&lt;/name&gt;</p>
<p>            &lt;value&gt;senwen1&lt;/value&gt;</p>
<p>      <a name="OLE_LINK6">&lt;/property&gt;</a></p>
<p>&lt;/configuration&gt;</p>
<p>4<span style="font-family:'宋体';">配置</span><span style="font-family:Calibri;">Hadoop</span><span style="font-family:'宋体';">的环境变量</span></p>
<p>[root@senwen1 hadoop]# vim /etc/profile</p>
<p><a name="OLE_LINK8"></a>export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin</p>
<p>export HADOOP_HOME=/itcast/hadoop-2.7.2</p>
<p>刷新配置：</p>
<p>[root@senwen1 hadoop]# source /etc/profile</p>
<p><span style="color:rgb(51,51,51);">修改</span><span style="color:rgb(51,51,51);">.bashrc</span><span style="color:rgb(51,51,51);">文件</span></p>
<p><span style="color:rgb(51,51,51);">vim .</span><span style="color:rgb(51,51,51);">bashrc</span></p>
<p>export HADOOP_HOME=/itcast/hadoop-2.7.2</p>
<p><span style="color:rgb(51,51,51);">export CLASSPATH=</span>JAVA_HOME=/usr/java/jdk1.7.0_79</p>
<p>export PATH=$PATH:$HADOOP_HOME/bin</p>
<p><span style="color:rgb(51,51,51);"> </span></p>
<p>5<span style="font-family:'宋体';">初始化</span><span style="font-family:Calibri;">HDFS(</span><span style="font-family:'宋体';">格式化文件系统</span><span style="font-family:Calibri;">)</span></p>
<p>#hadoop namenode -format  (<span style="font-family:'宋体';">过时</span><span style="font-family:Calibri;">)</span><span style="font-family:'宋体';">还可以用</span></p>
<p>用<span style="font-family:Calibri;">hdfs namenode -format</span></p>
<p><span style="background:rgb(255,255,0);">改了之后显示<span style="font-family:Calibri;">16/06/1614:58:17 INFO common.Storage:</span></span></p>
<p><span style="background:rgb(255,255,0);">Storage directory /itcast/hadoop-2.7.2/hadoop_root/dfs/name has been successfully formatted.</span></p>
<p> ./start-all.sh<span style="font-family:'宋体';">也过时了</span><span style="font-family:Calibri;">This script is Deprecated. Instead use start-dfs.sh and start-yarn.sh</span></p>
<p>这种方式需要多次输入密码</p>
<p>还可以通过浏览器的方式验证</p>
<p><a name="OLE_LINK12"></a><a href="http://192.168.56.8:50070" rel="nofollow"><u><span style="color:rgb(128,0,128);">http://192.168.56.8:50070</span></u></a> (hdfs<span style="font-family:'宋体';">管理界面</span><span style="font-family:Calibri;">)</span></p>
<p><a href="http://192.168.56.8:8088" rel="nofollow"><u><span style="color:rgb(128,0,128);">http://192.168.56.8:8088</span></u></a> (yarn<span style="font-family:'宋体';">管理界面</span><span style="font-family:Calibri;">)</span></p>
<p>到这里<a name="OLE_LINK10">连不上<span style="font-family:Calibri;">50070</span><span style="font-family:'宋体';">，</span></a><a name="OLE_LINK13">并且<span style="font-family:Calibri;">jps</span><span style="font-family:'宋体';">命令后并没有出现</span><span style="font-family:Calibri;">Namenode</span></a></p>
<p>解决办法：</p>
<p><a name="OLE_LINK14"></a>namenode <span style="font-family:'宋体';">
默认在</span><span style="font-family:Calibri;">/tmp</span><span style="font-family:'宋体';">下建立临时文件，但关机后，</span><span style="font-family:Calibri;">/tmp</span><span style="font-family:'宋体';">下文档自动删除，再次启动</span><span style="font-family:Calibri;">Master</span><span style="font-family:'宋体';">造成文件不匹配，所以</span><span style="font-family:Calibri;">namenode</span><span style="font-family:'宋体';">启动失败。</span></p>
<p>在<span style="font-family:Calibri;">core-site.xml</span><span style="font-family:'宋体';">中指定临时文件位置，然后重新格式化，终极解决！</span></p>
<p>&lt;property&gt;</p>
<p>&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;</p>
<p>&lt;value&gt;/usr/grid/hadoop1.7.0_17/hadoop_${user.name}&lt;/value&gt;</p>
<p>&lt;property&gt;</p>
<p>value<span style="font-family:'宋体';">中的路径只要不是</span><span style="font-family:Calibri;">/tmp</span><span style="font-family:'宋体';">就行。</span></p>
<p>6.<span style="font-family:'宋体';">上传</span><span style="font-family:Calibri;">linux</span><span style="font-family:'宋体';">系统中一个文件到</span><span style="font-family:Calibri;">hdfs</span><span style="font-family:'宋体';">上</span></p>
<p>[root@senwen1 ~]# hadoop fs -put /root/jdk-7u79-linux-i586.tar.gz hdfs://senwen1:9000/jdk</p>
<p>通过<span style="font-family:Calibri;">Browsing HDFS</span><span style="font-family:'宋体';">查看上传的文件</span></p>
<p> </p>
<p>7.<span style="font-family:'宋体';">使用命令下载：</span></p>
<p>[root@senwen1 ~]# hadoop fs -get hdfs://senwen1:9000/jdk /home/jdk1.7</p>
<p>8.<span style="font-family:'宋体';">测试</span><span style="font-family:Calibri;">MR </span>
<span style="font-family:'宋体';">和</span><span style="font-family:Calibri;">YARN</span></p>
<p>[root@senwen1 mapreduce]# hadoop jar hadoop-mapreduce-examples-2.7.2.jar wordcount hdfs://senwen1:9000/words hdfs://senwen1:9000/wcout</p>
<p>9.配置<span style="font-family:Calibri;">hadoop ssh</span><span style="font-family:'宋体';">免密码登陆</span></p>
<p>生成<span style="font-family:Calibri;">ssh</span><span style="font-family:'宋体';">免登陆密钥</span></p>
<p>ssh-keygen -t rsa</p>
<p>执行完后会生成两个文件<span style="font-family:Calibri;">id_rsa(</span><span style="font-family:'宋体';">私钥</span><span style="font-family:Calibri;">)</span><span style="font-family:'宋体';">、</span><span style="font-family:Calibri;">id rsa.pub(</span><span style="font-family:'宋体';">公钥</span><span style="font-family:Calibri;">)</span></p>
<p>将公钥拷贝到要免登陆的机器上</p>
<p>ssh-copy-id senwen1</p>
<p> </p>
<img src="https://img-blog.csdn.net/20160616201501026?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt="">            </div>
                </div>
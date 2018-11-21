---
layout:     post
title:      Hadoop-04-HBase完全分布式环境搭建
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<p>Hbase<span style="font-family:'宋体';">分布式详细安装步骤</span></p>
<p><span style="color:rgb(0,0,255);"> </span></p>
<p><span style="color:rgb(0,0,255);">Hadoop<span style="font-family:'宋体';">完全分布式环境已经搭建完成（参见</span><span style="font-family:'Times New Roman';">01_Hadoop</span><span style="font-family:'宋体';">完全分布式环境搭建）。</span></span></p>
<p><span style="color:rgb(0,0,255);"> </span></p>
<p><span style="color:rgb(255,0,0);">注意：<span style="font-family:'Times New Roman';">Hbase</span><span style="font-family:'宋体';">和</span><span style="font-family:'Times New Roman';">Hadoop</span><span style="font-family:'宋体';">的版本必须对应！不然会出现各种问题！</span></span></p>
<p><span style="color:rgb(255,0,0);">这里选用的是<span style="font-family:'Times New Roman';">Hadoop-0.20.2</span><span style="font-family:'宋体';">和</span><span style="font-family:'Times New Roman';">Hbase-0.90.5!</span><span style="font-family:'宋体';">经测试成功。</span></span></p>
<p><span style="color:rgb(0,0,255);"> </span></p>
<p>四、安装配置<span style="font-family:'Times New Roman';">Hbase</span></p>
<p>Hadoop用户登录</p>
<p>1、下载hbase-0.90.5.tar.gz</p>
<p> </p>
<p>2、解压<span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">到某一目录</span><span style="font-family:'Times New Roman';">/home/hadoop/hadoop_sub/hbase-0.90.5</span></p>
<p> </p>
<p>3<span style="font-family:'宋体';">、配置</span>hbase<span style="font-family:'宋体';">安装目录下</span>conf下文件hbase-env.sh,<span style="font-family:'宋体';">导出</span><span style="font-family:'Times New Roman';">JAVA_HOME</span></p>
<p>文件未尾加：</p>
<p># The java implementation to use.  Required.</p>
<p> export JAVA_HOME=/usr/lib/jvm/java-7-openjdk</p>
<p> export HBASE_CLASSPATH=<span style="color:rgb(255,0,0);">hadoop<span style="font-family:'宋体';">的安装路径</span><span style="font-family:'Times New Roman';">/conf</span></span></p>
<p><span style="color:rgb(255,0,0);"> export HBASE_MANAGE_ZK=true      //<span style="font-family:'宋体';">允许</span><span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">管理其自带的</span><span style="font-family:'Times New Roman';">zookeeper</span><span style="font-family:'宋体';">！</span></span></p>
<p> </p>
<p>4<span style="font-family:'宋体';">、配置</span>/home/coole/hbase-0.20.6/conf下文件hbase-site.xml</p>
<p>&lt;?xml version="1.0"?&gt;</p>
<p>&lt;?xml-stylesheet type="text/xsl" href="configuration.xsl"?&gt;</p>
<p>&lt;configuration&gt;</p>
<p>&lt;property&gt;</p>
<p>    &lt;name&gt;hbase.rootdir&lt;/name&gt;</p>
<p>    &lt;value&gt;<span style="color:rgb(0,0,255);">hdfs://</span><span style="color:rgb(0,0,255);">h1</span><span style="color:rgb(0,0,255);">:9000</span>/hbase&lt;/value&gt;   <span style="color:rgb(0,0,255);">注意：这里的路径一定要和<span style="font-family:'Times New Roman';">hadoop</span><span style="font-family:'宋体';">的</span><span style="font-family:'Times New Roman';">core-site.xml</span><span style="font-family:'宋体';">下配置的路径一致！！！！</span></span></p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>    &lt;name&gt;hbase.cluster.distributed&lt;/name&gt;</p>
<p>    &lt;value&gt;true&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>    &lt;name&gt;hbase.master&lt;/name&gt;</p>
<p>    &lt;value&gt;h1:60000&lt;/value&gt;</p>
<p>  &lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>      &lt;name&gt;hbase.zookeeper.quorum&lt;/name&gt;</p>
<p>      &lt;value&gt;h1,h2,h3&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;property&gt;</p>
<p>      &lt;name&gt;hbase.tmp.dir&lt;/name&gt;</p>
<p>      &lt;value&gt;/home/hadoop/hadoop_sub/hbase_tmp&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;!--<span style="font-family:'宋体';">指</span><span style="font-family:'Times New Roman';">zookeeper</span><span style="font-family:'宋体';">集群</span><span style="font-family:'Times New Roman';">data</span><span style="font-family:'宋体';">目录</span><span style="font-family:'Times New Roman';">--&gt;</span></p>
<p>&lt;name&gt;hbase.zookeeper.property.dataDir&lt;/name&gt;</p>
<p>&lt;value&gt;/home/hadoop/hadoop_sub/zookeeper_data&lt;/value&gt;</p>
<p>&lt;/property&gt;</p>
<p>&lt;/configuration&gt;</p>
<p> </p>
<p><span style="color:rgb(0,0,255);">hbase.rootdir<span style="font-family:'宋体';">设置</span><span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">在</span><span style="font-family:'Times New Roman';">hdfs</span><span style="font-family:'宋体';">上的目录</span></span>，主机名为<span style="font-family:'Times New Roman';">hdfs</span><span style="font-family:'宋体';">的</span><span style="font-family:'Times New Roman';">namenode</span><span style="font-family:'宋体';">节点所在的主机 </span></p>
<p>hbase.cluster.distributed<span style="font-family:'宋体';">设置为</span><span style="font-family:'Times New Roman';">true</span><span style="font-family:'宋体';">，表明是完全分布式的</span><span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">集群 </span></p>
<p>hbase.master<span style="font-family:'宋体';">设置</span><span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">的</span><span style="font-family:'Times New Roman';">master</span><span style="font-family:'宋体';">主机名和端口 </span></p>
<p>hbase.zookeeper.quorum<span style="font-family:'宋体';">设置</span><span style="font-family:'Times New Roman';">zookeeper</span><span style="font-family:'宋体';">的主机，官方推荐设置为</span><span style="font-family:'Times New Roman';">3</span><span style="font-family:'宋体';">，</span><span style="font-family:'Times New Roman';">5</span><span style="font-family:'宋体';">，</span><span style="font-family:'Times New Roman';">7</span><span style="font-family:'宋体';">比较好，奇数。</span></p>
<p> </p>
<p>5.替换<span style="font-family:Arial;">hbase</span><span style="font-family:'宋体';">自带的</span><span style="font-family:Arial;">hadoop</span><span style="font-family:'宋体';">包</span>！！！（重要，为了避免不兼容的情况）</p>
<p>备份hbase-0.90.5/lib<span style="font-family:'宋体';">目录下的</span><span style="font-family:Arial;">hadoop-core-0.20-append-r1056497.jar,</span><span style="font-family:'宋体';">再</span><span style="font-family:Arial;">cp hadoop</span><span style="font-family:'宋体';">目录下的</span><span style="font-family:Arial;">hadoop-0.20.2-core.jar</span><span style="font-family:'宋体';">到</span><span style="font-family:Arial;">hbase-0.90.5/lib</span><span style="font-family:'宋体';">目录。</span></p>
<p> </p>
<p> </p>
<p>6.配置/home/coole/hbase-0.20.6/conf下文件regionservers</p>
<p>h1</p>
<p>h2</p>
<p>h3</p>
<p> </p>
<p> </p>
<p> </p>
<p>7、设置环境变量，用<span style="font-family:'Times New Roman';">gedit</span><span style="font-family:'宋体';">打开</span><span style="font-family:'Times New Roman';">/etc/profile</span><span style="font-family:'宋体';">文件在文件未尾添加：</span></p>
<p>export HBASE_HOME=/home/coole/ hbase-0.20.6</p>
<p>export PATH=$PATH:$HBASE_HOME/bin</p>
<p> </p>
<p><span style="color:rgb(0,0,255);">完成后使用 <span style="font-family:'Times New Roman';">source /etc/profile </span><span style="font-family:'宋体';">命令使之生效。</span></span></p>
<p> </p>
<p>8、 在完成以上修改之后，把<span style="font-family:'Times New Roman';">master</span><span style="font-family:'宋体';">上的</span>hbase原样复制到<span style="font-family:'Times New Roman';">slave</span><span style="font-family:'宋体';">上，保证目录结构一致，</span>可使用如下命令： </p>
<p>scp –r /home/hadoop/ hadoop_sub/  hadoop@h2:/home/hadoop</p>
<p>scp –r /home/hadoop/hadoop_sub/   hadoop@h3:/home/hadoop</p>
<p>本次测试将<span style="font-family:'Times New Roman';">Hbase</span><span style="font-family:'宋体';">等子项目统一放在</span><span style="font-family:'Times New Roman';">~/hadoop_sub</span><span style="font-family:'宋体';">目录下，故可以整体复制到其他节点。</span></p>
<p> </p>
<p> </p>
<p>9、启动<span style="font-family:'Times New Roman';">Hbase</span></p>
<p>start-hbase.sh </p>
<p>stop-hbase.sh</p>
<p> </p>
<p>10.<span style="font-family:'宋体';">使用</span><span style="font-family:'Times New Roman';">jps</span><span style="font-family:'宋体';">命令查看</span><span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">是否启动成功</span></p>
<p>在<span style="font-family:'Times New Roman';">master</span><span style="font-family:'宋体';">上可以看到</span><span style="font-family:'Times New Roman';">HMaster</span><span style="font-family:'宋体';">、</span><span style="font-family:'Times New Roman';">RegionServer</span><span style="font-family:'宋体';">和</span><span style="font-family:'Times New Roman';">QuorumPeer</span><span style="font-family:'宋体';">启动成功；</span></p>
<p>在<span style="font-family:'Times New Roman';">slaves</span><span style="font-family:'宋体';">上可以看到</span><span style="font-family:'Times New Roman';">HRegionServer</span><span style="font-family:'宋体';">和</span><span style="font-family:'Times New Roman';">QuorumPeer</span><span style="font-family:'宋体';">启动成功</span></p>
<p> </p>
<p><span style="color:rgb(0,0,255);">注：</span></p>
<p>1、安装hbase 0.20.5版本自带了zookeeper-3.2.2<span style="font-family:'宋体';">，</span>所以不需要单独安装zookeeper。</p>
<p>2、hbase默认通过zookeeper管理，配置项在/jz/hbase-0.20.5/conf/hbase-env.sh文件中：</p>
<p># Tell HBase whether it should manage it's own instance of Zookeeper or not.</p>
<p># export HBASE_MANAGES_ZK=true如果需要采用自带安装的zookeeper<span style="font-family:'宋体';">，</span>可以将注释取消，把true修改为false。否则启动<span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">的时候将会提示地址被占用。不过不影响</span><span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">正常使用。</span></p>
<p>3<span style="font-family:'宋体';">、通过</span><span style="font-family:'Times New Roman';">shell</span><span style="font-family:'宋体';">控制台想</span><span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">插入中文数据将会报错，这是由于</span><span style="font-family:'Times New Roman';">hbase</span><span style="font-family:'宋体';">中只是存放字节，采用程序将汉字改为字节录入即可。</span></p>
<p><span style="font-family:'宋体';"><br></span></p>
            </div>
                </div>
---
layout:     post
title:      spark与shark的安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
1. 约定</h1>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
本文约定<span>Hadoop 2.4.0</span><span>安装在</span><span>/data/hadoop/current</span><span>，而</span><span>Spark 0.9.1</span><span>被安装在</span><span>/data/hadoop/spark</span><span>，其中</span><span>/data/hadoop/spark</span><span>为指向</span><span>/data/hadoop/spark</span><span>。</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
Spark<span>官网为：</span><a href="http://spark.apache.org/" rel="nofollow" style="text-decoration:none;color:rgb(25,89,155);">http://spark.apache.org/</a>，<span>Shark</span><span>官网为：</span><a href="http://shark.cs.berkeley.edu/" rel="nofollow" style="text-decoration:none;color:rgb(25,89,155);">http://shark.cs.berkeley.edu/</a>。</p>
<h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc14614" style="color:rgb(86,86,86);"></a>2. 安装<span>Scala</span></h1>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
联邦理工学院洛桑（<span>EPFL</span><span>）的</span><span>Martin Odersky</span><span>于</span><span>2001</span><span>年基于</span><span>Funnel</span><span>的工作开始设计</span><span>Scala</span><span>。</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
Scala<span>是一种</span>多范式的编程语言，设计初衷是要集成纯面向对象编程和函数式编程的各种特性。运行在<span>Java</span><span>虚拟机</span><span>JVM</span><span>之上，兼容现有的</span><span>Java</span><span>程序，并可调用</span><span>Java</span><span>类库。</span><span>Scala</span><span>包含编译器和类库，以</span><span>BSD</span><span>许可证发布。</span></p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc2064" style="color:rgb(86,86,86);"></a>2.1. 下载</h2>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
Spark<span>使用</span><span>Scala</span><span>开发的，在安装</span><span>Spark</span><span>之前，先在各个节上将</span><span>Scala</span><span>安装好。</span><span>Scala</span><span>的官网为：</span><a href="http://www.scala-lang.org/" rel="nofollow" style="text-decoration:none;color:rgb(25,89,155);">http://www.scala-lang.org/</a>，下载网址为：<a href="http://www.scala-lang.org/download/" rel="nofollow" style="text-decoration:none;color:rgb(25,89,155);">http://www.scala-lang.org/download/</a>，本文下载的是二进制安装包scala-2.11.0.tgz。</p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc20815" style="color:rgb(86,86,86);"></a>2.2. 安装</h2>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
本文以<span>root</span><span>用户（</span>实则也可以非<span>root</span><span>用户，建议事先规划好</span>）将<span>Scala</span><span>安装在</span><span>/data/scala</span><span>，其中</span><span>/data/scala</span><span>是指向</span><span>/data/scala-2.11.0</span><span>的软链接。</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
安装方法非常简单，将scala-2.11.0.tgz上传到<span>/data</span><span>目录，然后在</span><span>/data/</span><span>目录下对</span>scala-2.11.0.tgz进行解压。</p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
接着，建立软链接：<span>ln -s /data/</span>scala-2.11.0 /data/scala。</p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc27520" style="color:rgb(86,86,86);"></a>2.3. 设置环境变量</h2>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
Scala<span>被安装完成后，需要将它添加到</span><span>PATH</span><span>环境变量中，可以直接修改</span><span>/etc/profile</span><span>文件，加入以下内容即可：</span></p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export </span><span style="font-size:10.5pt;font-family:'宋体';"><strong>SCALA_HOME</strong></span><span style="font-size:10.5pt;font-family:'宋体';">=/data/scala</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export PATH=$SCALA_HOME/bin:$PATH</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc12755" style="color:rgb(86,86,86);"></a>3. 安装<span>Spark</span></h1>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
Spark<span>的安装以非</span><span>root</span><span>用户进行，本文以</span><span>hadoop</span><span>用户安装它。</span></p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc31768" style="color:rgb(86,86,86);"></a>3.1. 部署</h2>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
本文的部署如下表格所示，主<span>Master</span><span>部署在</span>172.25.39.166节点上：</p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="236" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';"><strong>主<span>Master</span></strong></span><span style="font-size:10.5pt;font-family:'宋体';"><strong></strong></span></p>
</td>
<td width="236" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';"><strong>备<span>Master</span></strong></span><span style="font-size:10.5pt;font-family:'宋体';"><strong></strong></span></p>
</td>
<td width="236" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';"><strong>Slaves</strong></span><span style="font-size:10.5pt;font-family:'宋体';"><strong></strong></span></p>
</td>
</tr><tr><td width="236" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">172.25.39.166</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
<td width="236" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">172.25.40.171</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
<td width="236" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">10.12.154.77</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">10.12.154.7</span><span style="font-size:10.5pt;font-family:'宋体';">8</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">10.12.154.79</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">10.12.154.80</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
 </p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
要求每个<span>Slaves</span><span>上都安装有</span><span>Hive</span><span>，否则会报“</span><span>readObject can't find class org.apache.hadoop.hive.conf.HiveConf</span><span>”错误。</span></p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc29120" style="color:rgb(86,86,86);"></a>3.2. 下载</h2>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
本文下载的二进制安装包，推荐这种方式，否则编译还得折腾。下载网址为：<a href="http://spark.apache.org/downloads.html#userconsent%23" rel="nofollow">http://spark.apache.org/downloads.html#userconsent#</a>，本文下载的是cdh5版本的，这个可以直接跑在<span>YARN</span><span>上。</span></p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;text-indent:-28.75pt;">
<a name="_Toc20174" style="color:rgb(86,86,86);"></a>3.3. 安装</h2>
<p style="margin-left:21.25pt;color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:-21.25pt;">
1) 将<span>spark-0.9.1-bin-hadoop2.tgz</span><span>上传到目录</span><span>/data/hadoop</span><span>下</span></p>
<p style="margin-left:21.25pt;color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:-21.25pt;">
2) 解压：<span>tar xzf spark-0.9.1-bin-hadoop2.tgz</span></p>
<p style="margin-left:21.25pt;color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:-21.25pt;">
3) 建立软链接：<span>ln -s spark-0.9.1-bin-hadoop2 spark</span></p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc2878" style="color:rgb(86,86,86);"></a>3.4. 配置</h2>
<h3 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc29204" style="color:rgb(86,86,86);"></a>3.4.1. 修改<span>conf/spark-env.sh</span></h3>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
可以<span>spark-env.sh.template</span><span>复制一份，然后增加以下内容：</span></p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export </span><span style="font-size:10.5pt;font-family:'宋体';"><strong>SCALA_HOME</strong></span><span style="font-size:10.5pt;font-family:'宋体';">=/data/scala</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export </span><span style="font-size:10.5pt;font-family:'宋体';"><strong>JAVA_HOME</strong></span><span style="font-size:10.5pt;font-family:'宋体';">=/data/jdk</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export </span><span style="font-size:10.5pt;font-family:'宋体';"><strong>SPARK_MASTER_IP</strong></span><span style="font-size:10.5pt;font-family:'宋体';">=172.25.39.166</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><h3 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc25684" style="color:rgb(86,86,86);"></a>3.4.2. 修改<span>conf/slaves</span></h3>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
这个和配置<span>HDFS</span><span>的</span><span>slaves</span><span>类似，一行一个</span><span>slave</span><span>的</span><span>IP</span><span>：</span></p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">10.12.154.77</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">10.12.154.7</span><span style="font-size:10.5pt;font-family:'宋体';">8</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">10.12.154.79</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">10.12.154.80</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc26561" style="color:rgb(86,86,86);"></a>4. 启动<span>Spark</span></h1>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
进入到<span>Spark</span><span>的</span><span>sbin</span><span>目录，执行</span><span>start-all.sh</span><span>即可将</span><span>Spark</span><span>运行起来。使用浏览器打开“</span><span>http://172.25.39.166:</span>8080/<span>”，即可看到</span><span>Spark</span><span>运行状况。“</span>8080”是<span>Spark</span><span>的</span>http端口。</p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
 </p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
如果要以<span>Server</span><span>方式运行</span><span>Spark</span><span>，可以这样：</span></p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">$./bin/</span><span style="font-size:10.5pt;font-family:'宋体';"><strong>shark </strong></span><span style="font-size:10.5pt;font-family:'宋体';">--service </span><span style="font-size:10.5pt;font-family:'宋体';"><strong>sharkserver </strong></span><span style="font-size:10.5pt;font-family:'宋体';">&lt;port&gt;</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
 </p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
客户端可以这样去连接它：</p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">$./bin/</span><span style="font-size:10.5pt;font-family:'宋体';"><strong>shark </strong></span><span style="font-size:10.5pt;font-family:'宋体';">-h &lt;server-host&gt; -p &lt;server-port&gt;</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc9120" style="color:rgb(86,86,86);"></a>5. 安装<span>Shark</span></h1>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
使用<span>Shark</span><span>，需要先安装好</span><span>Hive</span><span>，并且得在所有</span><span>Spark slaves</span><span>机上都安装</span><span>Hive</span><span>，同时也得在所有</span><span>Spark slaves</span><span>机上安装</span><span>Shark</span><span>。有关</span><span>Hive</span><span>的安装，请参见《</span><span>Hive 0.12.0</span><span>安装指南》。</span></p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc24444" style="color:rgb(86,86,86);"></a>5.1. 下载</h2>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
Shark<span>官网：</span><a href="http://shark.cs.berkeley.edu/" rel="nofollow" style="text-decoration:none;color:rgb(25,89,155);">http://shark.cs.berkeley.edu/</a>。</p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
下载网址：<a href="https://github.com/amplab/shark/releases/tag/v0.9.1#userconsent%23" rel="nofollow">https://github.com/amplab/shark/releases/tag/v0.9.1#userconsent#</a>。</p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc22447" style="color:rgb(86,86,86);"></a>5.2. 安装</h2>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
本文将<span>shark</span><span>安装到</span><span>/data/hadoop/shark</span><span>，步骤为：</span></p>
<p style="margin-left:21.25pt;color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:-21.25pt;">
1) 将shark-0.9.1-bin-hadoop2.tgz上传到主<span>Master</span><span>的</span><span>/data/hadoop</span><span>目录</span></p>
<p style="margin-left:21.25pt;color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:-21.25pt;">
2) 解压：<span>tar xzf </span>shark-0.9.1-bin-hadoop2.tgz</p>
<p style="margin-left:21.25pt;color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:-21.25pt;">
3) 建立链连接：<span>ln -s shark-0.9.1-bin-hadoop2 </span>shark</p>
<h2 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc15669" style="color:rgb(86,86,86);"></a>5.3. 配置</h2>
<div>
<h3 style="font-family:'宋体', Arial;line-height:26px;">
<span style="color:#ff0000;">  5.3.0. 往</span><span style="font-family:'宋体';font-size:14px;line-height:26px;"><span style="color:#ff0000;">$SHARK_HOME/lib添加mysql-connector-java-5.1.26-bin.jar</span><span style="color:#339999;">(我开始就是因为这个原因搞了半天)</span></span></h3>
<div><span style="font-family:'宋体';font-size:14px;line-height:26px;"><span style="color:#339999;">   </span>还有可能出现的问题链接<a href="http://zhousheng29.iteye.com/blog/1938042" rel="nofollow"><span style="color:#000000;">http://zhousheng29.iteye.com/blog/1938042</span></a></span></div>
<br></div>
<h3 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc28147" style="color:rgb(86,86,86);"></a>5.3.1. 修改<span>shark-env.sh</span></h3>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
从<span>shark-env.sh.template</span><span>复制生成一份</span><span>shark-env.sh</span><span>，然后增加以下内容：</span></p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export HADOOP_HOME=/data/</span><span style="font-size:10.5pt;font-family:'宋体';"><strong>hadoop</strong></span><span style="font-size:10.5pt;font-family:'宋体';">/current</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export HIVE_HOME=/data/hadoop/</span><span style="font-size:10.5pt;font-family:'宋体';"><strong>hive</strong></span><span style="font-size:10.5pt;font-family:'宋体';"><strong></strong></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export HIVE_CONF_DIR=$HIVE_HOME/conf</span><span style="font-size:10.5pt;font-family:'宋体';"><strong></strong></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export SPARK_HOME=/data/hadoop/</span><span style="font-size:10.5pt;font-family:'宋体';"><strong>spark</strong></span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export MASTER=spark://172.25.39.166:7077</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc5298" style="color:rgb(86,86,86);"></a>6. 启动<span>Shark</span></h1>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
进入到<span>Shark</span><span>的</span><span>bin</span><span>目录，执行</span><span>shark-withinfo</span><span>（</span><span>shark-withdebug</span><span>或</span><span>shark</span><span>也可以的），进入</span><span>Shark</span><span>命令行操作界面，如果觉得</span><span>shark-withinfo</span><span>屏幕显示的信息太多，可以使用</span><span>shark</span><span>。另外，</span><span>Shark</span><span>像</span><span>Hive</span><span>一样，也有个</span><span>beeline</span><span>。</span></p>
<h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc7868" style="color:rgb(86,86,86);"></a>7. 执行<span>Shark</span><span>命令</span></h1>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
Shark<span>和</span><span>Hive</span><span>是兼容的，可以像</span><span>Hive</span><span>一样操作。</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
请参照：<a href="https://github.com/amplab/shark/wiki/Running-Shark-on-a-Cluster" rel="nofollow" style="text-decoration:none;color:rgb(25,89,155);">https://github.com/amplab/shark/wiki/Running-Shark-on-a-Cluster</a>进行：</p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'新宋体';"><strong>CREATE TABLE</strong></span><span style="font-size:10.5pt;font-family:'新宋体';"> src(key INT, value STRING);</span><span style="font-size:10.5pt;font-family:'新宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'新宋体';"><strong>LOAD DATA</strong></span><span style="font-size:10.5pt;font-family:'新宋体';"> LOCAL INPATH '${env:HIVE_HOME}/examples/files/kv1.txt' INTO TABLE src;</span><span style="font-size:10.5pt;font-family:'新宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'新宋体';"><strong>SELECT COUNT(1)</strong></span><span style="font-size:10.5pt;font-family:'新宋体';"> FROM src;</span><span style="font-size:10.5pt;font-family:'新宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'新宋体';"><strong>CREATE TABLE </strong></span><span style="font-size:10.5pt;font-family:'新宋体';">src_cached AS SELECT * FROM SRC;</span><span style="font-size:10.5pt;font-family:'新宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'新宋体';"><strong>SELECT COUNT(1)</strong></span><span style="font-size:10.5pt;font-family:'新宋体';"> FROM src_cached;</span><span style="font-size:10.5pt;font-family:'新宋体';"></span></p>
</td>
</tr></tbody></table><h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc19003" style="color:rgb(86,86,86);"></a>8. 常见错误</h1>
<p style="margin-left:21.25pt;color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:-21.25pt;">
1) readObject can't find class org.apache.hadoop.hive.conf.HiveConf</p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
运行“select count(1) from src;”时，报如下所示的错误，但<span>show databases</span><span>和</span><span>show tables</span><span>不会报错。建议使用</span><span>shark-withinfo</span><span>，以便看到更详细的信息。类</span>org.apache.hadoop.hive.conf.HiveConf在包<span>hive-common-0.12.0.jar</span><span>中，估计在</span><span>CLASSPATH</span><span>中找不到</span><span>hive-common-0.12.0.jar</span><span>。</span></p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">org.apache.spark.SparkException: Job aborted: Task 1.0:1 failed 4 times (most recent failure: Exception failure: java.lang.RuntimeException: </span><span style="font-size:10.5pt;font-family:'宋体';"><strong>readObject can't find class org.apache.hadoop.hive.conf.HiveConf</strong></span><span style="font-size:10.5pt;font-family:'宋体';">)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at org.apache.spark.scheduler.DAGScheduler$$anonfun$org$apache$spark$scheduler$DAGScheduler$$abortStage$1.apply(DAGScheduler.scala:1028)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at org.apache.spark.scheduler.DAGScheduler$$anonfun$org$apache$spark$scheduler$DAGScheduler$$abortStage$1.apply(DAGScheduler.scala:1026)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at org.apache.spark.scheduler.DAGScheduler.org$apache$spark$scheduler$DAGScheduler$$abortStage(DAGScheduler.scala:1026)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at org.apache.spark.scheduler.DAGScheduler$$anonfun$processEvent$10.apply(DAGScheduler.scala:619)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at org.apache.spark.scheduler.DAGScheduler$$anonfun$processEvent$10.apply(DAGScheduler.scala:619)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at scala.Option.foreach(Option.scala:236)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at org.apache.spark.scheduler.DAGScheduler.processEvent(DAGScheduler.scala:619)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at org.apache.spark.scheduler.DAGScheduler$$anonfun$start$1$$anon$2$$anonfun$receive$1.applyOrElse(DAGScheduler.scala:207)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at akka.actor.ActorCell.receiveMessage(ActorCell.scala:498)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at akka.actor.ActorCell.invoke(ActorCell.scala:456)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at akka.dispatch.Mailbox.processMailbox(Mailbox.scala:237)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at akka.dispatch.Mailbox.run(Mailbox.scala:219)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at akka.dispatch.ForkJoinExecutorConfigurator$AkkaForkJoinTask.exec(AbstractDispatcher.scala:386)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at scala.concurrent.forkjoin.ForkJoinTask.doExec(ForkJoinTask.java:260)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at scala.concurrent.forkjoin.ForkJoinPool$WorkQueue.runTask(ForkJoinPool.java:1339)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at scala.concurrent.forkjoin.ForkJoinPool.runWorker(ForkJoinPool.java:1979)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">        at scala.concurrent.forkjoin.ForkJoinWorkerThread.run(ForkJoinWorkerThread.java:107)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">FAILED: Execution Error, return code -101 from shark.execution.SparkTask</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
 </p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
当使用<span>shark-withinfo</span><span>替代</span><span>shark</span><span>运行后，多了些信息：在</span><span>DEVNET-154-77</span><span>机器上找不到“</span>org.apache.hadoop.hive.conf.HiveConf”，那么也就是需要在<span>DEVNET-154-77</span><span>机器上有</span>hive-common-0.12.0.jar<span>文件存在，并且可以找到它</span>。</p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">14/04/24 05:09:00 WARN scheduler.TaskSetManager: Lost TID 1 (task 1.0:1)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">14/04/24 05:09:00 INFO scheduler.TaskSetManager: Loss was due to java.lang.RuntimeException: readObject can't find class org.apache.hadoop.hive.conf.HiveConf [duplicate 1]</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">14/04/24 05:09:00 INFO scheduler.TaskSetManager: Starting task 1.0:1 as TID 3 on executor 2: </span><span style="font-size:10.5pt;font-family:'宋体';"><strong>DEVNET-154-77</strong></span><span style="font-size:10.5pt;font-family:'宋体';"> (PROCESS_LOCAL)</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
<p>
<span style="font-size:10.5pt;font-family:'宋体';">14/04/24 05:09:00 INFO scheduler.TaskSetManager: Serialized task 1.0:1 as 4597 bytes in 0 ms</span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
 </p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
网上找了很多办法，都不太好，最后摸索，在Spark的环境变量文件spark-env.sh中增加对环境变量<span>CLASSPATH</span><span>的设置，结果问题解决了（在此之前，试过在</span><span>/etc/profile</span><span>增加，没生效），另外保证每个</span><span>Spark slaves</span><span>上都有安装</span><span>Shark</span><span>，并且安装目录保持一致：</span></p>
<table style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;width:426.1pt;"><tbody><tr><td width="710" valign="top" style="border:.5pt solid rgb(0,0,0);">
<p>
<span style="font-size:10.5pt;font-family:'宋体';">export </span><span style="font-size:10.5pt;font-family:'宋体';"><strong>CLASSPATH</strong></span><span style="font-size:10.5pt;font-family:'宋体';">=$CLASSPATH:</span><span style="font-size:10.5pt;font-family:'宋体';"><strong>/data/hadoop/</strong></span><span style="color:rgb(0,0,255);font-size:10.5pt;font-family:'宋体';"><strong>hive</strong></span><span style="font-size:10.5pt;font-family:'宋体';"><strong>/lib</strong></span><span style="font-size:10.5pt;font-family:'宋体';"></span></p>
</td>
</tr></tbody></table><p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
 </p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;text-indent:21pt;">
修改后，注意需要重启<span>Spark</span>，再运行<span>Shark</span><span>。</span></p>
<h1 style="color:rgb(102,102,102);font-family:'宋体', Arial;line-height:26px;">
<a name="_Toc23960" style="color:rgb(86,86,86);"></a>9. 相关文档</h1>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
《<span>HBase-0.98.0</span><span>分布式安装指南》</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
《<span>Hive 0.12.0</span><span>安装指南》</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
《<span>ZooKeeper-3.4.6</span><span>分布式安装指南》</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
《<span>Hadoop 2.3.0</span><span>源码反向工程》</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
《在<span>Linux</span><span>上编译</span><span>Hadoop-2.4.0</span><span>》</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
《<span>Accumulo-1.5.1</span><span>安装指南》</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
《<span>Drill 1.0.0</span><span>安装指南》</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
《<span>Shark 0.9.1</span><span>安装指南》</span></p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
 </p>
<p style="color:rgb(102,102,102);font-family:'宋体', Arial;font-size:16px;line-height:26px;">
更多，敬请关注技术博客：<a href="http://blog.chinaunix.net/uid/20682147.html" rel="nofollow" style="text-decoration:none;color:rgb(25,89,155);">http://aquester.cublog.cn</a>。</p>
            </div>
                </div>
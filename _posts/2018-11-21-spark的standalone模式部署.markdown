---
layout:     post
title:      spark的standalone模式部署
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p></p>1.安装配置JDK<br>2.安装配置Spark，修改Spark配置文件(两个配置文件spark-env.sh和slaves)<br>vim spark-env.sh<br>#指定JAVA_HOME位置<br>export JAVA_HOME=/usr/java/jdk1.7.0_45<br>#指定spark老大Master的IP<br>export SPARK_MASTER_IP=spark1.zeng.cn<br>#指定spark老大Master的端口<br>export SPARK_MASTER_PORT=7077<br>#指定可用的CPU内核数量(默认:所有可用)<br>export SPARK_WORKER_CORES=2<br>#作业可使用的内存容量，默认格式为1000m或者2g(默认:所有RAM去掉给操作系统用的1GB)<br>export SPARK_WORKER_MEMORY=2g<br><br><br><br><br>3.在slaves文件中加入所有Work的地址<br>node3<br>node4<br>node5<br><br><br>4.(可选)配置两个Spark Master实现高可靠(首先要配置zookeeper集群，在spark-env.sh添加SPARK_DAEMON_JAVA_OPTS)<br>export JAVA_HOME=/usr/java/jdk1.7.0_45<br>export SPARK_DAEMON_JAVA_OPTS="-Dspark.deploy.recoveryMode=ZOOKEEPER -Dspark.deploy.zookeeper.url=192.168.80.10:2181 -Dspark.deploy.zookeeper.dir=/spark"<br>export SPARK_WORKER_CORES=2<br>export SPARK_WORKER_MEMORY=2g<br><br><br><br><br><br><br><br><br><br><br><br><br>export JAVA_HOME=/usr/local/jdk1.7.0_45<br>export SPARK_MASTER_IP=node-1.zeng.cn<br>export SPARK_MASTER_PORT=7077<br>export SPARK_WORKER_CORES=2<br>export SPARK_WORKER_MEMORY=1g<p><br></p>            </div>
                </div>
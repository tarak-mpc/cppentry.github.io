---
layout:     post
title:      阿里腾讯云 hadoop+spark集群搭建（2）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<h1><a id="_hadoopspark2_0"></a>阿里腾讯云 hadoop+spark集群搭建（2）</h1>
<p>linux版本： centos7</p>
<p>hadoop版本： 3.1.1</p>
<p>spark版本： 2.3.2</p>
<hr>
<p>在1中已经搭建好了hadoop，接下来就是spark。</p>
<p>为方便起见，用shell脚本完成下载spark、hive（后面再搭，先把spark跑起来）的操作。</p>
<pre><code>download_file.sh
-------------------------------
#!/bin/bash
TARGET=files
HADOOP_VERSION=3.1.1
HIVE_VERSION=2.3.3
SPARK_VERSION=2.3.2
HADOOP_FILE=hadoop-$HADOOP_VERSION.tar.gz
HIVE_FILE=apache-hive-$HIVE_VERSION-bin.tar.gz
SPARK_FILE=spark-$SPARK_VERSION-bin-hadoop2.7.tgz

if [ ! -f "$HADOOP_FILE" ]; then
  echo "https://www-us.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/$HADOOP_FILE is downloading"
  curl -O https://www-us.apache.org/dist/hadoop/common/hadoop-$HADOOP_VERSION/$HADOOP_FILE
fi
echo "Hadoop is completed!"

if [ ! -f "$HIVE_FILE" ]; then
  echo "https://www-us.apache.org/dist/hive/hive-$HIVE_VERSION/$HIVE_FILE is downloading"
  curl -O https://www-us.apache.org/dist/hive/hive-$HIVE_VERSION/$HIVE_FILE
fi
echo "HIVE is completed!"

if [ ! -f "$SPARK_FILE" ]; then
  echo "https://www-us.apache.org/dist/spark/spark-$SPARK_VERSION/$SPARK_FILE is downloading"
  curl -O https://www-us.apache.org/dist/spark/spark-$SPARK_VERSION/$SPARK_FILE
fi
echo "$SPARK_FILE completed!"
</code></pre>
<ol>
<li>
<p><strong>运行脚本下载spark和hive</strong></p>
</li>
<li>
<p><strong>解压缩到~/hadoop下（hadoop用户身份）</strong></p>
<pre><code>$ cd ~/hadoop

$ ls

hadoop-3.1.1  hive-2.3.3  spark-2.3.2
</code></pre>
</li>
<li>
<p><strong>下载Anaconda3</strong></p>
<pre><code>$ cd ~/hadoop
$ ls
hadoop-3.1.1  hive-2.3.3  spark-2.3.2
</code></pre>
<p>由于我打算使用pyspark，且想使用python3，所以安装Anaconda3。</p>
<pre><code>$ curl -O https://mirrors.tuna.tsinghua.edu.cn/anaconda/archive/Anaconda3-5.3.0-Linux-x86_64.sh
$ ./Anaconda3-5.3.0-Linux-x86_64.sh
</code></pre>
</li>
<li>
<p><strong>配置文件</strong></p>
<ul>
<li><a href="http://spark-env.sh" rel="nofollow">spark-env.sh</a></li>
</ul>
<p>追加如下内容</p>
<pre><code>export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-0.el7_5.x86_64
export JRE_HOME=${JAVA_HOME}/jre
export HADOOP_HOME=/home/hadoop/hadoop/hadoop-3.1.1
export HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
export SPARK_MASTER_PORT=7077
export SPARK_MASTER_HOST=master
</code></pre>
<ul>
<li>slaves</li>
</ul>
<pre><code>slave1
slave2
</code></pre>
<ul>
<li>log4j.properties</li>
</ul>
<p>若想日志信息仅记载警告信息，则将<code>log4j.rootCategory=INFO, console</code>改为<code>log4j.rootCategory=WARN, console</code></p>
<ul>
<li>/etc/profile.d/spark-2.3.2.sh</li>
</ul>
<pre><code>export SPARK_HOME=/home/hadoop/hadoop/spark-2.3.2
export PATH=$SPARK_HOME/bin:$PATH
</code></pre>
<pre><code>$ source /etc/profile
</code></pre>
<p>以上内容master和slaves机器都要进行，master配置好拷贝过去就可以了。</p>
</li>
<li>
<p><strong>master机器运行spark</strong></p>
<pre><code>$ cd $SPARK_HOME/sbin
$ ./start-all.sh 
&lt;!--先执行start-master再执行start-slave--&gt;
$ jps
16000 Master
15348 NameNode
15598 SecondaryNameNode
16158 Jps
&lt;!--只启动了hdfs和spark--&gt;
</code></pre>
<p>打开http://master:8080（公网），显示<strong>Alive Workers:2</strong>，完成。</p>
</li>
</ol>

            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
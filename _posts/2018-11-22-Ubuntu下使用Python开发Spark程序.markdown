---
layout:     post
title:      Ubuntu下使用Python开发Spark程序
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/csj941227/article/details/78921848				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1、安装配置好Spark环境，确认执行Spark目录下的/bin/pyspark能够成功进入。</p>
<p><a href="http://blog.csdn.net/csj941227/article/details/78023770" rel="nofollow">Spark 集群搭建从零开始之1 Scala的安装与配置</a><br></p>
<p><a href="http://blog.csdn.net/csj941227/article/details/78026423" rel="nofollow">Spark 集群搭建从零开始之2 Spark单机伪分布式安装与配置</a><br></p>
<p><a href="http://blog.csdn.net/csj941227/article/details/78030890" rel="nofollow">Spark 集群搭建从零开始之3 Spark Standalone集群安装、配置与测试</a><br></p>
<p>2、安装anaconda2</p>
<p>https://www.anaconda.com/download/#linux<br></p>
<p>bash  Anaconda2-5.0.1-Linux-x86_64.sh</p>
<p>3、sudo pip install pyspark</p>
<p>4、进入jupyter notebook，编写程序测试</p>
<p>基本上SparkContext那句不报错就说明已经能够启动Spark</p>
<p><img src="https://img-blog.csdn.net/20171228144240509?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY3NqOTQxMjI3/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" alt=""><br></p>
<p><br></p>
<p>附上环境变量:</p>
<p></p><pre><code class="language-plain"># /etc/profile: system-wide .profile file for the Bourne shell (sh(1))
# and Bourne compatible shells (bash(1), ksh(1), ash(1), ...).
export JAVA_HOME=/usr/lib/jvm/java-8-oracle
export JRE_HOME=$JAVA_HOME/jre
export CLASSPATH=$JAVA_HOME/lib:$JRE_HOME/lib:$CLASSPATH
export PATH=$JAVA_HOME/bin:$JRE_HOME/bin:$HADOOP_HOME/bin:$HIVE_HOME/bin:$PATH

export HADOOP_HOME=/home/chenjie/hadoop-2.6.5
#export HADOOP_HOME=/home/chenjie/hadoop-2.6.5-net
export CLASSPATH=.:$HADOOP_HOME/lib:$CLASSPATH
export PATH=$PATH:$HADOOP_HOME/bin
export PATH=$PATH:$HADOOP_HOME/sbin
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_ROOT_LOGGER=INFO,console
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export HADOOP_OPTS="-Djava.library.path=$HADOOP_HOME/lib"


#scala
export SCALA_HOME=/home/chenjie/scala-2.10.4
export PATH=${SCALA_HOME}/bin:$PATH
#spark
export SPARK_HOME=/home/chenjie/spark-1.6.0-bin-hadoop2.6
#export SPARK_HOME=/home/chenjie/spark-1.6.0-bin-hadoop2.6-net
export PATH=${SPARK_HOME}/bin:${SPARK_HOME}/sbin:$PATH


#Flume
export FLUME_HOME=/home/chenjie/apache-flume-1.5.0-bin
export FLUME_CONF_DIR=$FLUME_HOME/conf
export PATH=.:$PATH::$FLUME_HOME/bin



#hive
export HIVE_HOME=/home/chenjie/apache-hive-2.3.0-bin
export PATH=$PATH:$HIVE_HOME/bin

#sqoop
export SQOOP_HOME=/home/chenjie/sqoop-1.4.6.bin__hadoop-2.0.4-alpha
export PATH=$PATH:$SQOOP_HOME/bin
export SQOOP_SERVER_EXTRA_LIB=$SQOOP_HOME/extra

#maven
export PATH=$PATH:/home/chenjie/apache-maven-3.5.0/bin

#
export PYTHONPATH=/home/chenjie/spark-1.6.0-bin-hadoop2.6/python
export PYTHONPATH=$SPARK_HOME/python/:$PYTHONPATH
export PYTHONPATH=$SPARK_HOME/python/lib/py4j-0.9.0-src.zip:$PYTHONPATH
PYSPARK_DRIVER_PYTHON=ipython  
PYSPARK_DRIVER_PYTHON_OPTS='notebook' 




if [ "$PS1" ]; then
  if [ "$BASH" ] &amp;&amp; [ "$BASH" != "/bin/sh" ]; then
    # The file bash.bashrc already sets the default PS1.
    # PS1='\h:\w\$ '
    if [ -f /etc/bash.bashrc ]; then
      . /etc/bash.bashrc
    fi
  else
    if [ "`id -u`" -eq 0 ]; then
      PS1='# '
    else
      PS1='$ '
    fi
  fi
fi

# The default umask is now handled by pam_umask.
# See pam_umask(8) and /etc/login.defs.

if [ -d /etc/profile.d ]; then
  for i in /etc/profile.d/*.sh; do
    if [ -r $i ]; then
      . $i
    fi
  done
  unset i
fi</code></pre><br><br><p><br></p>
            </div>
                </div>
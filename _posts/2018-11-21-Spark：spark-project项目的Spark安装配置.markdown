---
layout:     post
title:      Spark：spark-project项目的Spark安装配置
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/weixin_38750084/article/details/82765496				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h3>安装spark客户端</h3>

<p>1、将spark-1.5.1-bin-hadoop2.4.tgz使用WinSCP上传到/usr/local目录下。</p>

<p>2、解压缩spark包：tar -zxvf spark-1.5.1-bin-hadoop2.4.tgz。</p>

<p>3、重命名spark目录：mv spark-1.5.1-bin-hadoop2.4 spark</p>

<p>4、修改spark环境变量</p>

<p>vi ~/.bashrc</p>

<p>export SPARK_HOME=/usr/local/spark</p>

<p>export PATH=$SPARK_HOME/bin</p>

<p>export CLASSPATH=.:$CLASSPATH:$JAVA_HOME/lib:$JAVA_HOME/jre/lib</p>

<p>source ~/.bashrc</p>

<h3>修改spark-env.sh文件</h3>

<p>1、cd /usr/local/spark/conf</p>

<p>2、cp spark-env.sh.template spark-env.sh</p>

<p>3、vi spark-env.sh</p>

<p>export JAVA_HOME=/usr/java/latest</p>

<p>export SCALA_HOME=/usr/local/scala</p>

<p>export HADOOP_HOME=/usr/local/hadoop</p>

<p>export HADOOP_CONF_DIR=/usr/local/hadoop/etc/hadoop</p>            </div>
                </div>
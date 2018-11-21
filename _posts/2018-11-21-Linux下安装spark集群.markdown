---
layout:     post
title:      Linux下安装spark集群
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <div id="content_views" class="markdown_views prism-atom-one-dark">
							<!-- flowchart 箭头图标 勿删 -->
							<svg xmlns="http://www.w3.org/2000/svg" style="display: none;"><path stroke-linecap="round" d="M5,0 0,2.5 5,5z" id="raphael-marker-block" style="-webkit-tap-highlight-color: rgba(0, 0, 0, 0);"></path></svg>
							<p>1、安装scala <br>
2、安装Spark <br>
3、配置Spark <br>
    修改配置文件spark-env.sh，在解压spark下的conf文件中</p>

<blockquote>
  <p>将模板文件复制出来 <br>
      cp spark-env.sh.template  spark-env.sh <br>
      cp slave.template slave</p>
</blockquote>

<p>4、配置spark和slave</p>

<blockquote>
  <p>配置spark-env.sh(scala_home和java_home都是其安装路径) <br>
      export SCALA_HOME=/usr/soft/scala-2.13.0 <br>
      export JAVA_HOME=/usr/soft/jdk-9.0.1 <br>
      SPARK_MASTER_IP=192.168.136.132（自己的主节点IP）</p>
</blockquote>

<p>5、启动Spark</p>

<blockquote>
  <p>进到以下目录（也是spark的安装路径下的sbin） <br>
  cd /home/hadoop/spark/spark-1.6.1/sbin <br>
  <code>start-all.sh</code></p>
</blockquote>

<p>6、使用jps查看进程 <br>
<code>worker</code> <br>
<code>jps</code> <br>
7、进入电脑浏览器输入主机名：8080可以查看spark的详细详细信息</p>            </div>
						<link href="https://csdnimg.cn/release/phoenix/mdeditor/markdown_views-9e5741c4b9.css" rel="stylesheet">
                </div>
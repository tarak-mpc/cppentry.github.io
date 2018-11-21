---
layout:     post
title:      Hadoop之HDFS上测试创建目录、上传、下载文件(这些基本操作记录下来，总有忘的时候)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1> </h1>

<p>1、HDFS上创建目录</p>

<pre class="has">
<code>${HADOOP_HOME}/bin/hdfs dfs -mkdir /demo1</code></pre>

<ul><li>1</li>
</ul><p>2、上传本地文件到HDFS上</p>

<pre class="has">
<code>${HADOOP_HOME}/bin/hdfs dfs -put ${HADOOP_HOME}/etc/hadoop/core-site.xml /demo1</code></pre>

<ul><li> </li>
</ul><p>3、上传本地文件到HDFS上</p>

<pre class="has">
<code>${HADOOP_HOME}/bin/hdfs dfs -cat /demo1/core-site.xml</code></pre>

<ul><li>1</li>
</ul><p>4、从HDFS上下载文件到本地</p>

<pre class="has">
<code>${HADOOP_HOME}/bin/hdfs dfs -get /demo1/core-site.xml</code></pre>

<p> </p>

<p>其他：</p>

<p>hdfs dfs -cat   /demo2/1.txt |grep liugen     查看文件</p>

<p>hdfs dfs -mkdir -P  /demo2/haha/wa    创建多级目录（不存在的也给我创建出来）</p>

<p>hdfs dfs  -rm -R  删除多级目录</p>

<p>hdfs dfs  -ls  /demo 查看目录下内容</p>

<p>hdfs dfs -ls -R  /demo  把目录和子目录下的文件都列出来</p>

<p>hdfs dfs -chmod -R  777  /demo  对目录和子目录都修改权限</p>

<p>hdfs dfs  -get  /demo2/1.txt   liugen.txt       把hdfs文件拉到本地，并重命名。</p>

<p>hdfs dfs -put  -f   1.txt   /demo2     将hdfs中已有的文件强制覆盖（-f）</p>

<p>hdfs dfs -cp /demo2/1.txt  /user/          //复制文件</p>

<p>hdfs dfs -mv /-user/file.csv   /demo2/1.csv   //移动文件并重命名（原文件没了）</p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>

<p> </p>            </div>
                </div>
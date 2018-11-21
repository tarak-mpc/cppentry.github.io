---
layout:     post
title:      ubuntu16 + jdk8 + hadoop2.7 安装
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1.安装JDK</p>
<p>2.解压hadoop 安装包至 /usr/local/hadoop 目录<br></p>
<p>3.启动ssh : ssh localhost <br></p>
<p> 关闭机器的防火墙 ：<br>
    ufw disable<br></p>
<p>4.配置hadoop:</p>
<p></p>
<p>进入<code>/usr/local/hadoop/etc/hadoop/目录下，修改三个配置文件：</code><br></p>
<p>第一个文件core-site.xml</p>
<pre><code>core-site.xml
 &lt;!-- 指定HDFS老大（namenode）的通信地址 --&gt;
    &lt;property&gt;
        &lt;name&gt;fs.default.name&lt;/name&gt;
        &lt;value&gt;hdfs://localhost:9000&lt;/value&gt;
    &lt;/property&gt;
    &lt;property&gt;
        &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
        &lt;value&gt;/home/hadoop/tmp&lt;/value&gt;
    &lt;/property&gt;
</code></pre>
<p><br></p>
<p>第二个文件hdfs-site.xml</p>
<pre><code>hdfs-site.xml
 &lt;!-- 设置hdfs副本数量 --&gt;
    &lt;property&gt;
        &lt;name&gt;dfs.replication&lt;/name&gt;
        &lt;value&gt;1&lt;/value&gt;
    &lt;/property&gt;
</code></pre>
<p>第三个文件hadoop-env.sh中找到如下行然后写入内容</p>
<pre><code># The java implementation to use.
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
export HADOOP_HOME=/usr/local/hadoop/
export PATH=$PATH:/usr/local/hadoop/bin
</code></pre>
<p>接下来在系统环境变量中写入Hadoop的环境变量 <br>
vim /etc/environment</p>
<pre><code>#在文件的结尾""之内加上
:/usr/local/hadoop/bin
:/usr/local/hadoop/sbin
</code></pre>
4.
<p>第一步格式化</p>
<pre><code>hadoop namenode -format
</code></pre>
<p>启动hdfs</p>
<pre><code>sbin/start-all.sh
</code></pre>
<p>显示进程</p>
<pre><code>jps
</code></pre>
<p>5.出现namenode、datanode、jps、resourcemanager、nodemanager、secondarymanager 六个进程即成功<br></p>
            </div>
                </div>
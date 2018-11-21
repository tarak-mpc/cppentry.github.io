---
layout:     post
title:      Centons7下安装配置Flume、Linux下安装配置Flume、Flume的简单使用示例、Flume整合Kafka使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>本篇文章主要介绍在windows下使用vmware虚拟机中的Linux(Centons7)下配置安装Flume。</p>

<p>目前Flume 的最新版本为1.8，笔者安装的是1.6，是Flume的一个经典版本，通常在生产环境中使用的就是这个版本，在安装Flume前，看先它的前置条件 </p>

<p>(1).JDK1.8 </p>

<p>(2).足够的内存 </p>

<p>(3).足够的磁盘空间 </p>

<p>(4).目录及文件要有读写权限</p>

<h2>简要步骤：</h2>

<p>①、下载Flume解压包</p>

<p>②、解压Flume</p>

<p>③、修改配置文件</p>

<p>④、配置环境变量</p>

<p>⑤、使用Flume帮助命令、查看版本</p>

<p>⑥、启动Flume</p>

<p>⑦、使用Flume</p>

<p>⑧、Flume整合Kafka使用</p>

<p> </p>

<h2><br>
详细步骤：</h2>

<h3>一、Flume下载</h3>

<p>从官网下载，或者在linux中使用下面的命令下载，然后解压</p>

<p>方式1:官网下载地址：<a href="http://archive.apache.org/dist/flume/" rel="nofollow">http://archive.apache.org/dist/flume/</a> </p>

<p>方式2：如果提示没有 wget 命令， 则使用如下命令安装即可 ，yum install wget # 使用 root 用户安装</p>

<pre class="has">
<code>wget http://archive.apache.org/dist/flume/1.6.0/apache-flume-1.6.0-bin.tar.gz</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181107221109370.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<h3>二、解压</h3>

<pre class="has">
<code> tar -zxvf apache-flume-1.6.0-bin.tar.gz </code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181107221625617.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181107221740181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<h3> 三、修改配置文件</h3>

<p>①进入flume的conf目录下，拷贝flume-env.sh.template然后重命名为flume-env.sh（图1）</p>

<pre class="has">
<code>cp flume-env.sh.template flume-env.sh</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181107221918218.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<p>②在flume-env.sh中配置jdk</p>

<p> </p>

<pre class="has">
<code>vim flume-env.sh

export JAVA_HOME=/usr/java/jdk1.8.0_151</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181107222058979.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<h3>四、配置环境变量</h3>

<pre class="has">
<code>vim ~/.bashrc 
source ~/.bashrc 


# Flume Environment Variable
export FLUME_HOME=/usr/local/devtool/apache-flume-1.6.0-bin

export PATH=$PATH:$FLUME_HOME/bin</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/2018110722224134.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p><span style="color:#f33b45;">完整环境变量</span> </p>

<p> </p>

<p> </p>

<pre class="has">
<code>
# Uncomment the following line if you don't like systemctl's auto-paging feature:
# export SYSTEMD_PAGER=

# User specific aliases and functions
export JAVA_HOME=/usr/java/jdk1.8.0_151
export HADOOP_HOME=/usr/local/hadoop        # hadoop的安装目录，替换为你的hadoop的安装目录
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
# Zookeeper Environment Variable
export ZOOKEEPER_HOME=/usr/local/devtool/zookeeper-3.4.10
# Flume Environment Variable
export FLUME_HOME=/usr/local/devtool/apache-flume-1.6.0-bin




export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/sbin:$HADOOP_HOME/bin:$ZOOKEEPER_HOME/bin:$FLUME_HOME/bin
</code></pre>

<h3>五、查看Flume 的命令帮助</h3>

<pre class="has">
<code>flume-ng help</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181107222452844.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<p><span style="color:#f33b45;"> 查看版本信息</span></p>

<pre class="has">
<code>flume-ng version</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/2018110722253566.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<h3>六、启动Flume</h3>

<pre class="has">
<code>./bin/flume-ng agent -n agent -c conf -f conf/flume-conf.properties.template -Dflume.root.logger=INFO,console</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181107222656657.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p>参数说明： </p>

<p>–conf：指定flume的配置文件所在目录 </p>

<p>–name：指定Agent的名称 </p>

<p>–conf-file：指定编写的flume配置文件 </p>

<p>-Dflume.root.logger：指定日志级别</p>

<p>flume启动后，打印的日志中可以看到如下信息（部分日志）</p>

<p>输入jps命令可以查看到一个Application的进程，如果有这个进程，说明我们的flume启动成功了</p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/2018111717495865.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p> </p>

<h3>七、简单使用Flume</h3>

<p>测试（启动一个telnet进程，telnet 数据输入）</p>

<pre class="has">
<code>telnet node1 44444</code></pre>

<p>启动后在这里输入完按回车去flume控制台查看输出即可</p>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175040829.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<p>查看Flume控制台的输出（Event是Flume数据传输的基本单元，由可选的header和一个byte array的数据构成）</p>

<pre class="has">
<code>2018-08-08 13:55:58,446 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{} body: 68 65 6C 6C 6F 0D                               hello. }
2018-08-08 13:55:58,447 (SinkRunner-PollingRunner-DefaultSinkProcessor) [INFO - org.apache.flume.sink.LoggerSink.process(LoggerSink.java:94)] Event: { headers:{} body: 77 6F 72 6C 64 0D                               world. }</code></pre>

<p><img alt="" class="has" src="https://img-blog.csdnimg.cn/20181117175108181.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3pqaF83NDYxNDAxMjk=,size_16,color_FFFFFF,t_70"></p>

<h3>八、Flume整合Kafka使用</h3>

<p> </p>            </div>
                </div>
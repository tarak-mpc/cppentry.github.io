---
layout:     post
title:      Hadoop之—— WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <pre><code class="language-html">[oracle@master opt]$ cd /opt/hadoopdata/hdfs
[oracle@master hdfs]$ ls
data  name  snn
[oracle@master hdfs]$ start-dfs.sh
18/07/15 06:06:20 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Starting namenodes on [master]
master: Error: JAVA_HOME is not set and could not be found.
slave2: Error: JAVA_HOME is not set and could not be found.
slave1: Error: JAVA_HOME is not set and could not be found.
master: Error: JAVA_HOME is not set and could not be found.
Starting secondary namenodes [slave1]
slave1: Error: JAVA_HOME is not set and could not be found.
18/07/15 06:06:32 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
[oracle@master hdfs]$ </code></pre><br><p></p><pre><code class="language-html">[oracle@master native]$ ldd libhadoop.so.1.0.0 
./libhadoop.so.1.0.0: /lib64/libc.so.6: version `GLIBC_2.14' not found (required by ./libhadoop.so.1.0.0)
        linux-vdso.so.1 =&gt;  (0x00007fff386ea000)
        libdl.so.2 =&gt; /lib64/libdl.so.2 (0x00007fa46cdc3000)
        libpthread.so.0 =&gt; /lib64/libpthread.so.0 (0x00007fa46cba5000)
        libc.so.6 =&gt; /lib64/libc.so.6 (0x00007fa46c811000)
        /lib64/ld-linux-x86-64.so.2 (0x000055af585c3000)
[oracle@master native]$ </code></pre><pre><code class="language-html">[oracle@master native]$ ldd --version
ldd (GNU libc) 2.12
Copyright (C) 2010 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
Written by Roland McGrath and Ulrich Drepper.
[oracle@master native]$ 
</code></pre><span style="color:rgb(51,51,51);font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;font-size:14px;background-color:rgb(255,255,255);">原来系统预装的glibc库是2.12版本，而hadoop期望是2.14版本，所以打印警告信息。</span><br style="color:rgb(51,51,51);font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;font-size:14px;background-color:rgb(255,255,255);"><p style="font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;background-color:rgb(255,255,255);">现在有两个办法，重新编译glibc.2.14版本，安装后专门给hadoop使用，这个有点危险。</p><span style="color:rgb(51,51,51);font-family:'-apple-system', 'SF UI Text', Arial, 'PingFang SC', 'Hiragino Sans GB', 'Microsoft YaHei', 'WenQuanYi Micro Hei', sans-serif, SimHei, SimSun;font-size:14px;background-color:rgb(255,255,255);">第二个办法直接在log4j日志中去除告警信息。在/opt/hadoop/etc/hadoop/log4j.properties文件中添加</span><br><pre><code class="language-html">log4j.logger.org.apache.hadoop.util.NativeCodeLoader=ERROR</code></pre><p> 在hadoop-env.sh添加JAVA_HOME路径</p><pre><code class="language-html">[oracle@master hadoop]$ pwd
/opt/hadoop/etc/hadoop
[oracle@master hadoop]$ vi hadoop-env.sh
# The java implementation to use.
#export JAVA_HOME=${JAVA_HOME}
export JAVA_HOME=/opt/jdk

# The jsvc implementation to use. Jsvc is required to run secure datanodes
# that bind to privileged ports to provide authentication of data transfer
"hadoop-env.sh" 118L, 4996C written  </code></pre><br><p><br></p><p><br></p><p><br></p><p><br></p><br>            </div>
                </div>
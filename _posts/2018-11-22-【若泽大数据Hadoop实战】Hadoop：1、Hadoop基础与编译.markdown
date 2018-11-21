---
layout:     post
title:      【若泽大数据Hadoop实战】Hadoop：1、Hadoop基础与编译
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：					https://blog.csdn.net/qq_24073707/article/details/80321839				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <h1 style="font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;margin-top:0px;margin-bottom:0px;padding:0px;line-height:1.5;font-size:22px;color:rgb(85,85,85);background-color:rgb(255,255,255);">Hadoop基础</h1><h5 style="font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;margin-top:20px;margin-bottom:15px;padding:10px 0px 0px;line-height:1.5;font-size:14px;color:rgb(85,85,85);background-color:rgb(255,255,255);"><a href="https://www.coderfei.com/2017/12/10/hadoop-1-hadoop-foundation-and-compilation.html#%E5%A4%A7%E6%95%B0%E6%8D%AE%E6%A6%82%E8%BF%B0" rel="nofollow" class="headerlink" title="大数据概述" style="color:rgb(85,85,85);background-color:transparent;border-bottom:1px solid rgb(153,153,153);"></a>大数据概述</h5><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">可以用“5V + 1C”来概括：
    Variety (多样化）
    Volume (海量）
    Velocity (快速）
    Vitality (灵活）
    Value (价值性）
    Complexity (复杂）
</code></pre><h5 style="font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;margin-top:20px;margin-bottom:15px;padding:10px 0px 0px;line-height:1.5;font-size:14px;color:rgb(85,85,85);background-color:rgb(255,255,255);"><a href="https://www.coderfei.com/2017/12/10/hadoop-1-hadoop-foundation-and-compilation.html#Hadoop%E4%B8%8EHadoop%E7%94%9F%E6%80%81%E5%9C%88" rel="nofollow" class="headerlink" title="Hadoop与Hadoop生态圈" style="color:rgb(85,85,85);background-color:transparent;border-bottom:1px solid rgb(153,153,153);"></a>Hadoop与Hadoop生态圈</h5><ul style="color:rgb(85,85,85);font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;background-color:rgb(255,255,255);"><li style="list-style:circle;"><p style="margin-bottom:20px;">Hadoop</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">狭义: 软件(HDFS、MapReduce、Yarn)
广义: 以hadoop为主的生态圈
</code></pre></li><li style="list-style:circle;"><p style="margin-bottom:20px;">Hadoop1.x</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">hdfs: 分布式文件管理系统                     存储
mapreduce1: 执行引擎                  计算+资源、作业调度
</code></pre></li><li style="list-style:circle;"><p style="margin-bottom:20px;">Hadoop2.x三大组件</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">hdfs: 分布式文件管理系统            存储
mapreduce2: 执行引擎                   计算
yarn: 资源(memory cpu)和作业调度平台    资源
</code></pre></li></ul><a style="color:rgb(85,85,85);background-color:rgb(255,255,255);border-bottom:1px solid rgb(153,153,153);font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;"></a><h5 style="font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;margin-top:20px;margin-bottom:15px;padding:10px 0px 0px;line-height:1.5;font-size:14px;color:rgb(85,85,85);background-color:rgb(255,255,255);"><a href="https://www.coderfei.com/2017/12/10/hadoop-1-hadoop-foundation-and-compilation.html#Hadoop%E7%BC%96%E8%AF%91" rel="nofollow" class="headerlink" title="Hadoop编译" style="color:rgb(85,85,85);background-color:transparent;border-bottom:1px solid rgb(153,153,153);"></a>Hadoop编译</h5><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">[root@hadoop-01 ~]# cd /opt/
[root@hadoop-01 opt]# mkdir sourcecode software
[root@hadoop-01 opt]# cd sourcecode
[root@hadoop-01 sourcecode]# pwd
/opt/sourcecode
</code></pre><ul style="color:rgb(85,85,85);font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;background-color:rgb(255,255,255);"><li style="list-style:circle;"><p style="margin-bottom:20px;">hadoop源代码下载</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;"># 将hadoop-2.8.1-src.tar.gz下载（或者用rz上传）到sourcecode目录
[root@hadoop-01 sourcecode]# ll
total 33756
-rw-r--r--. 1 root root 34523353 Aug 20 12:14 hadoop-2.8.1-src.tar.gz

# 解压
[root@hadoop-01 sourcecode]# tar -xzvf hadoop-2.8.1-src.tar.gz
[root@hadoop-01 sourcecode]# ll
total 33760
drwxr-xr-x. 17 root root     4096 Jun  2 14:13 hadoop-2.8.1-src
-rw-r--r--.  1 root root 34523353 Aug 20 12:14 hadoop-2.8.1-src.tar.gz

[root@hadoop-01 sourcecode]# cd hadoop-2.8.1-src
</code></pre></li><li style="list-style:circle;"><p style="margin-bottom:20px;">JAVA安装</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">[root@hadoop-01 ~]# mkdir -p /usr/java
[root@hadoop-01 ~]# cd /usr/java
[root@hadoop-01 java]# rz #上传jdk-8u45-linux-x64.gz
[root@hadoop-01 java]# tar -xzvf jdk-8u45-linux-x64.gz

# 修改用户和用户组
[root@hadoop-01 java]# chown -R root:root jdk1.8.0_45

# 设置环境变量
[root@hadoop-01 java]# vi /etc/profile
# 在最底下加入
export JAVA_HOME=/usr/java/jdk1.8.0_45
export PATH=$JAVA_HOME/bin:$PATH
# 生效
[root@hadoop-01 java]# source /etc/profile
</code></pre></li></ul><ul style="color:rgb(85,85,85);font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;background-color:rgb(255,255,255);"><li style="list-style:circle;"><p style="margin-bottom:20px;">Maven安装</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">[root@hadoop-01 ~]# cd /opt/software/
[root@hadoop-01 software]# rz  #上传apache-maven-3.3.9-bin.zip
[root@hadoop-01 software]# ll
total 8432
-rw-r--r--. 1 root root 8617253 Aug 20 12:35 apache-maven-3.3.9-bin.zip
# 解压
[root@hadoop-01 software]# unzip apache-maven-3.3.9-bin.zip
# 设置环境变量
[root@hadoop-01 software]# vi /etc/profile
export MAVEN_HOME=/opt/software/apache-maven-3.3.9
export MAVEN_OPTS="-Xms256m -Xmx512m"
export PATH=$MAVEN_HOME/bin:$JAVA_HOME/bin:$PATH
# 生效
[root@hadoop-01 software]# source /etc/profile
# 查看
[root@hadoop-01 software]# mvn -version
Apache Maven 3.3.9 (bb52d8502b132ec0a5a3f4c09453c07478323dc5; 2015-11-11T00:41:47+08:00)
Maven home: /opt/software/apache-maven-3.3.9
Java version: 1.8.0_45, vendor: Oracle Corporation
Java home: /usr/java/jdk1.8.0_45/jre
Default locale: en_US, platform encoding: UTF-8
OS name: "linux", version: "2.6.32-431.el6.x86_64", arch: "amd64", family: "unix"
</code></pre></li><li style="list-style:circle;"><p style="margin-bottom:20px;">Findbugs安装</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">[root@hadoop-01 ~]# cd /opt/software/
[root@hadoop-01 software]# rz #上传findbugs-1.3.9.zip
# 解压
[root@hadoop-01 software]# unzip findbugs-1.3.9.zip
# 设置环境变量
[root@hadoop-01 software]# vi /etc/profile
export FINDBUGS_HOME=/opt/software/findbugs-1.3.9
export PATH=$FINDBUGS_HOME/bin:$MAVEN_HOME/bin:$JAVA_HOME/bin:$PATH
# 生效
[root@hadoop-01 software]# source /etc/profile
# 查看
[root@hadoop-01 software]# findbugs -version
1.3.9
</code></pre></li><li style="list-style:circle;"><p style="margin-bottom:20px;">protobuf安装</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">[root@hadoop-01 ~]# cd /opt/software/
[root@hadoop-01 software]# rz #上传protobuf-2.5.0.tar.gz
# 解压
[root@hadoop-01 software]# tar -xzvf protobuf-2.5.0.tar.gz
[root@hadoop-01 software]# cd protobuf-2.5.0
[root@hadoop-01 protobuf-2.5.0]# yum install -y gcc gcc-c++ make cmake
[root@hadoop-01 protobuf-2.5.0]# ./configure --prefix=/usr/local/protobuf
[root@hadoop-01 protobuf-2.5.0]# make &amp;&amp; make install
# 设置环境变量
[root@hadoop-01 java]# vi /etc/profile
export PROTOC_HOME=/usr/local/protobuf
export PATH=$PROTOC_HOME/bin:$FINDBUGS_HOME/bin:$MAVEN_HOME/bin:$JAVA_HOME/bin:$PATH
# 生效
[root@hadoop-01 protobuf-2.5.0]# source /etc/profile
# 查看
[root@hadoop-01 protobuf-2.5.0]# protoc --version
libprotoc 2.5.0
</code></pre></li><li style="list-style:circle;"><p style="margin-bottom:20px;">其他依赖</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">yum install -y openssl openssl-devel svn ncurses-devel zlib-devel libtool
yum install -y snappy snappy-devel bzip2 bzip2-devel lzo lzo-devel lzop autoconf automake
</code></pre></li><li style="list-style:circle;"><p style="margin-bottom:20px;">编译</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">[root@hadoop-01 sourcecode]# cd hadoop-2.8.1-src
[root@hadoop-01 hadoop-2.8.1-src]# mvn clean package -Pdist,native -DskipTests -Dtar
</code></pre></li></ul><p style="margin-bottom:20px;color:rgb(85,85,85);font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;background-color:rgb(255,255,255);"><span style="font-weight:700;">编译好的tar包路径</span></p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">/opt/sourcecode/hadoop-2.8.1-src/hadoop-dist/target/hadoop-2.8.1.tar.gz
</code></pre><p style="margin-bottom:20px;color:rgb(85,85,85);font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;background-color:rgb(255,255,255);"><span style="font-weight:700;">配置本地Maven仓库</span><br>window/linux:</p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">cd /opt/software/apache-maven-3.3.9/conf

1.vi setting.xml    
&lt;localRepository&gt;D:\software\apache-maven-3.3.9\repository&lt;/localRepository&gt;

2.创建D:\software\apache-maven-3.3.9\repository
</code></pre><p style="margin-bottom:20px;color:rgb(85,85,85);font-family:Lato, 'PingFang SC', 'Microsoft YaHei', sans-serif;background-color:rgb(255,255,255);"><span style="font-weight:700;">提醒</span></p><pre style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;margin-top:20px;margin-bottom:20px;padding:10px;color:rgb(77,77,76);background:rgb(247,247,247);line-height:1.6;"><code style="font-family:consolas, Menlo, 'PingFang SC', 'Microsoft YaHei', monospace;font-size:13px;color:rgb(77,77,76);background:0px 0px;">1、有时候编译过程中会出现下载某个包的时间太久，这是由于连接网站的过程中会出现假死，
此时按ctrl+c，重新运行编译命令。 
2、如果出现缺少了某个文件的情况，则要先清理maven(使用命令 mvn clean) 再重新编译。</code></pre><p></p><p><span style="color:rgb(255,102,102);font-size:24px;background-color:rgb(255,255,255);">大数据课程推荐：</span></p><p><span style="color:rgb(255,102,102);font-size:24px;background-color:rgb(255,255,255);"><img src="https://img-blog.csdn.net/2018051615551284" alt=""></span></p><p><br></p><p></p>            </div>
                </div>
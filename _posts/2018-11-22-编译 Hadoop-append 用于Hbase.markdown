---
layout:     post
title:      编译 Hadoop-append 用于Hbase
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="background-color:rgb(255,255,255);"><span style="color:#3333ff;"><a href="http://hbase.apache.org/" rel="nofollow"><strong>Hbase</strong></a></span></span>基于 hadoop，若 Hbase 直接使用 release 版本的 hadoop，可能出现数据丢失的情况，hbase 需要使用  hadoop-append ，详细介绍参考<a href="http://hbase.apache.org/book/hadoop.html" rel="nofollow"><strong>Hbase
 官网资料</strong></a></p>
<p>下面以 hbase-0.90.2 为例，介绍 hadoop-0.20.2-append 的编译，以下操作参考：</p>
<p><strong><a href="http://www.michael-noll.com/blog/2011/04/14/building-an-hadoop-0-20-x-version-for-hbase-0-90-2/" rel="nofollow">Building an Hadoop 0.20.x version for HBase 0.90.2</a></strong></p>
<p>操作系统：CentOS Linux</p>
<p>所需工具：git、ant</p>
[zhankunlin@IctHTC ~]$ mkdir  hadoop-append-for-hbase<br>
[zhankunlin@IctHTC ~]$ cd hadoop-append-for-hbase<br>
[zhankunlin@IctHTC hadoop-append-for-hbase]$<strong><span style="color:#ff0000;"> git clone http://git.apache.org/hadoop-common.git</span></strong><br>
[zhankunlin@IctHTC hadoop-append-for-hbase]$   <strong><span style="color:#ff0000;">cd hadoop-common</span></strong><br>
[zhankunlin@IctHTC hadoop-common]$ <span style="color:#ff0000;"><strong>git checkout -t origin/branch-0.20-append</strong></span><br>
[zhankunlin@IctHTC hadoop-common]$ <strong><span style="color:#ff0000;">git show-branch branch-0.20-append</span></strong><br>
[zhankunlin@IctHTC hadoop-common]$ <strong><span style="color:#ff0000;">git show-branch release-0.20.2</span></strong><br>
[zhankunlin@IctHTC hadoop-common]$<strong><span style="color:#ff0000;"> vi ../build.properties </span></strong><br><span style="color:#3333ff;">#this is essential<br>
resolvers=internal<br>
#you can increment this number as you see fit<br>
version=0.20-append-for-hbase-zkl<br>
project.version=${version}<br>
hadoop.version=${version}<br>
hadoop-core.version=${version}<br>
hadoop-hdfs.version=${version}<br>
hadoop-mapred.version=${version}</span><br>
[zhankunlin@IctHTC hadoop-common]$<strong><span style="color:#ff0000;"> ln -s ../build.properties build.properties</span></strong><br>
[zhankunlin@IctHTC hadoop-common]$ pwd<br>
/home/zhankunlin/hadoop-append-for-hbase/hadoop-common<br><br>
安装 ant<br>
[zhankunlin@IctHTC ~]$ tar zxf apache-ant-1.8.2-bin.tar.gz<br><p>[zhankunlin@IctHTC ~]$ export PATH=/home/zhankunlin/apache-ant-1.8.2/bin:$PATH</p>
<p><strong>编译 hadoop</strong></p>
[zhankunlin@IctHTC ~]$ cd hadoop-append-for-hbase/hadoop-common<br>
[zhankunlin@IctHTC hadoop-common]$<span style="color:#ff0000;"><strong> git checkout branch-0.20-append</strong></span> 确保当前在使用 branch-0.20-append <br>
Already on 'branch-0.20-append'<br>
[zhankunlin@IctHTC hadoop-common]$ <strong><span style="color:#ff0000;">ant mvn-install</span></strong><br>
Buildfile: /home/zhankunlin/hadoop-append-for-hbase/hadoop-common/build.xml<br>
.<br>
.<br>
.<br>
mvn-install:<br>
[artifact:install] [INFO] Installing /home/zhankunlin/hadoop-append-for-hbase/hadoop-common/build/hadoop-core-0.20-append-for-hbase-zkl.jar to /home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-core/0.20-append-for-hbase-zkl/hadoop-core-0.20-append-for-hbase-zkl.jar<br>
[artifact:install] [INFO] Installing /home/zhankunlin/hadoop-append-for-hbase/hadoop-common/build/hadoop-test-0.20-append-for-hbase-zkl.jar to /home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-test/0.20-append-for-hbase-zkl/hadoop-test-0.20-append-for-hbase-zkl.jar<br>
[artifact:install] [INFO] Installing /home/zhankunlin/hadoop-append-for-hbase/hadoop-common/build/hadoop-tools-0.20-append-for-hbase-zkl.jar to /home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-tools/0.20-append-for-hbase-zkl/hadoop-tools-0.20-append-for-hbase-zkl.jar<br>
[artifact:install] [INFO] Installing /home/zhankunlin/hadoop-append-for-hbase/hadoop-common/build/hadoop-examples-0.20-append-for-hbase-zkl.jar to /home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-examples/0.20-append-for-hbase-zkl/hadoop-examples-0.20-append-for-hbase-zkl.jar<br>
[artifact:install] [INFO] Installing /home/zhankunlin/hadoop-append-for-hbase/hadoop-common/build/contrib/streaming/hadoop-streaming-0.20-append-for-hbase-zkl.jar to /home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-streaming/0.20-append-for-hbase-zkl/hadoop-streaming-0.20-append-for-hbase-zkl.jar<br><br>
BUILD SUCCESSFUL<br>
Total time: 11 minutes 42 seconds<br><br><strong>可选操作：</strong><br>
[zhankunlin@IctHTC hadoop-common]$<strong> <span style="color:#ff0000;">ant test</span></strong><span style="color:#ff0000;"></span> 测试所有功能，花很长时间<br>
[zhankunlin@IctHTC hadoop-common]$ <strong><span style="color:#ff0000;">ant test-core</span></strong> 测试核心功能<br><br>
查看编译好的 jar包(在当前用户主目录下)<br>
[zhankunlin@IctHTC hadoop-common]$ find ~/.m2/repository -name "hadoop-*.jar"<br>
/home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-tools/0.20-append-for-hbase-zkl/hadoop-tools-0.20-append-for-hbase-zkl.jar<br>
/home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-test/0.20-append-for-hbase-zkl/hadoop-test-0.20-append-for-hbase-zkl.jar<br>
/home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-examples/0.20-append-for-hbase-zkl/hadoop-examples-0.20-append-for-hbase-zkl.jar<br>
/home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-streaming/0.20-append-for-hbase-zkl/hadoop-streaming-0.20-append-for-hbase-zkl.jar<br>
/home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-core/0.20-append-for-hbase-zkl/hadoop-core-0.20-append-for-hbase-zkl.jar<br>
/home/zhankunlin/.m2/repository/org/apache/hadoop/hadoop-core/0.20.2/hadoop-core-0.20.2.jar<br><br>
编译好的包有：<br>
[zhankunlin@IctHTC hadoop-common]$ find ~/.m2/repository -name "hadoop-*.jar" | awk -F"/" '{print $11}'<br>
hadoop-tools-0.20-append-for-hbase-zkl.jar<br>
hadoop-test-0.20-append-for-hbase-zkl.jar<br>
hadoop-examples-0.20-append-for-hbase-zkl.jar<br>
hadoop-streaming-0.20-append-for-hbase-zkl.jar<br>
hadoop-core-0.20-append-for-hbase-zkl.jar<br>
hadoop-core-0.20.2.jar (这个包不需要)<br><br>
注意：<br>
这里 0.20-append-for-hbase-zkl 是在build.properties文件中定义的版本好号 version 值,即：<br><br>
hadoop-tools-VERSION.jar<br>
hadoop-test-VERSION.jar<br>
hadoop-examples-VERSION.jar<br>
hadoop-streaming-VERSION.jar<br>
hadoop-core-VERSION.jar<br><br>
将编译好的 jar 包复制出来.<br>
[zhankunlin@IctHTC hadoop-common]$ mk ../ok_jars<br>
[zhankunlin@IctHTC hadoop-common]$ jars=`find ~/.m2/repository -name "hadoop-*.jar"`; for jar in $jars; do cp $jar ../ok_jars/ ; done<br><br>
接下来就是<strong><span style="color:#3333ff;">将编译生成的新jar替换 hadoop-0.20.2-release 和 hbase-0.90.2 中旧的jar包<br></span></strong><br>
(1) 替换旧的hadoop包；<br><br>
    请注意，替换 hadoop 中旧的jar包前需要对编译生成的新jar包重新命名。<br><br><p>    Hadoop 0.20.2 release 版本中jar包的命名规则为 hadoop-VERSION-PACKAGE.jar,  如：hadoop-0.20.2-examples.jar。</p>
<p>    而新编译的jar包的命名规则为 hadoop-PACKAGE-VERSION.jar,如： hadoop-examples-0.20-append-for-hbase.jar. </p>
    所以需要将新编译的jar包按如下方式重命名以符合hadoop：<br><br>
    hadoop-examples-0.20-append-for-hbase-zkl.jar --&gt; hadoop-0.20-append-for-hbase-zkl-examples.jar<br>
    hadoop-test-0.20-append-for-hbase-zkl.jar --&gt; hadoop-0.20-append-for-hbase-zkl-test.jar <br>
    hadoop-tools-0.20-append-for-hbase-zkl.jar --&gt; hadoop-0.20-append-for-hbase-zkl-tools.jar <br>
    hadoop-streaming-0.20-append-for-hbase-zkl.jar --&gt; hadoop-0.20-append-for-hbase-zkl-streaming.jar     <br>
    hadoop-core-0.20-append-for-hbase-zkl.jar --&gt; hadoop-0.20-append-for-hbase-zkl-core.jar<br><br>
(2) 替换hbase中lib文件夹中的包<br><br>
    Hbase使用的命名规则为hadoop-PACKAGE-VERSION.jar，和新编译的jar包命名规则一致，所以只需要直接复制jar包到$HBASE_HOME/lib目录下，不需要重命名。<br><br>            </div>
                </div>
---
layout:     post
title:      BUILD HADOOP NATIVE LIBRARIES
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1><span style="font-size:14px;">BUILD HADOOP NATIVE LIBRARIES</span></h1>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">[FROM]<a href="http://www.drweiwang.com/build-hadoop-native-libraries/" rel="nofollow">http://www.drweiwang.com/build-hadoop-native-libraries/</a></span></p>
<p><span style="font-size:14px;"><br></span></p>
<h2><span style="font-size:14px;">How to build Hadoop Native Libraries for Hadoop 2.2.0</span></h2>
<p><span style="font-size:14px;"><br></span></p>
<span style="font-size:14px;">Because the distributed Hadoop 2.2.0 provides a 32bit libhadoop by default, user has to build the native libraries to avoid those warning messages such as, disabled stack guard of libhadoop.so.<br><br>
Java HotSpot(TM) 64-Bit Server VM warning: You have loaded library /opt/hadoop-2.2.0/lib/native/libhadoop.so.1.0.0 which might have disabled stack guard. The VM will try to fix the stack guard now.<br>
It's highly recommended that you fix the library with 'execstack -c &lt;libfile&gt;', or link it with '-z noexecstack'.<br></span>
<p><span style="font-size:14px;">13/11/01 10:58:59 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable</span></p>
<p><span style="font-size:14px;"><br></span></p>
<p><span style="font-size:14px;">For the version 2.4.1, the error is like:</span></p>
<p><span style="font-size:14px;">14/07/25 13:49:13 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable</span></p>
<p><span style="font-size:14px;"> </span></p>
<span style="font-size:14px;">The official Hadoop websitehttp://hadoop.apache.org/docs/r2.2.0/hadoop-project-dist/hadoop-common/NativeLibraries.html gives completely unclear instructions on how to build Hadoop native libraries.<br><br>
So here are what you should do:<br><br>
You need all the build tools:<br><br>
$ sudo apt-get install build-essential<br>
$ sudo apt-get install g++ autoconf automake libtool cmake zlib1g-dev pkg-config libssl-dev<br></span>
<p><span style="font-size:14px;">$ sudo apt-get install maven</span></p>
<p><span style="font-size:14px;"><br></span></p>
<span style="font-size:14px;">Another prerequisite, protoco buffer: protobuf version 2.5, which can be downloaded from https://protobuf.googlecode.com/files/protobuf-2.5.0.tar.gz. Download it to the /tmp directory; then,<br><br>
$ tar xzvf protobuf-2.5.0.tar.gz<br>
$ cd protobuf-2.5.0<br>
$ ./configure --prefix=/usr<br>
$ make<br>
$ make check<br></span>
<p><span style="font-size:14px;">$ sudo make install</span></p>
<p><span style="font-size:14px;"><br></span></p>
<span style="font-size:14px;">Having all the tools, we can now build Hadoop native libraries. Assuming you have downloaded the Hadoop 2.2.0 source code, do:<br><br>
$ tar xzvf hadoop-2.2.0-src.tar.gz<br>
$ cd hadoop-2.2.0-src<br></span>
<p><span style="font-size:14px;">$ mvn package -Pdist,native -DskipTests -Dtar</span></p>
<p><span style="font-size:14px;"><br></span></p>
<span style="font-size:14px;">Note: there is a missing dependency in the maven project module that results in a build failure at the hadoop-auth stage. Here is the official bug report and fix is<br><br>
Index: hadoop-common-project/hadoop-auth/pom.xml<br>
===================================================================<br>
--- hadoop-common-project/hadoop-auth/pom.xml (revision 1543124)<br>
+++ hadoop-common-project/hadoop-auth/pom.xml (working copy)<br>
@@ -54,6 +54,11 @@<br>
&lt;/dependency&gt;<br>
&lt;dependency&gt;<br>
&lt;groupId&gt;org.mortbay.jetty&lt;/groupId&gt;<br>
+ &lt;artifactId&gt;jetty-util&lt;/artifactId&gt;<br>
+ &lt;scope&gt;test&lt;/scope&gt;<br>
+ &lt;/dependency&gt;<br>
+ &lt;dependency&gt;<br>
+ &lt;groupId&gt;org.mortbay.jetty&lt;/groupId&gt;<br>
&lt;artifactId&gt;jetty&lt;/artifactId&gt;<br>
&lt;scope&gt;test&lt;/scope&gt;<br>
&lt;/dependency&gt;<br><br><br>
Maven will do all the heavy work for you, and you should get this after build is completed<br><br>
[INFO] BUILD SUCCESS<br>
[INFO] ------------------------------------------------<br>
[INFO] Total time: 15:39.705s<br>
[INFO] Finished at: Fri Nov 01 14:36:17 CST 2013<br>
[INFO] Final Memory: 135M/422M<br>
The built native libraries should be at<br><br>
hadoop-2.2.0-src/hadoop-dist/target/hadoop-2.2.0/lib</span>
            </div>
                </div>
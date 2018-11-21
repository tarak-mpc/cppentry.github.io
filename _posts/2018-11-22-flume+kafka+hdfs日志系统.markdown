---
layout:     post
title:      flume+kafka+hdfs日志系统
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:24px;">1. flume 1.7.0中的sink类型为hdfs 2.8.0，flume如何将文件发给hdfs中：</span></p>
<p>（1） 要导入对应的包</p>
<p></p>
<pre><code class="language-html">commons-configuration-1.6.jar
hadoop-annotations-2.8.0.jar
hadoop-auth-2.8.0.jar
hadoop-common-2.8.0.jar
hadoop-hdfs-2.8.0.jar
hadoop-hdfs-client-2.8.0.jar</code></pre><br>
将这些jar包从hadoop中拷贝到flume的lib目录下。
<p></p>
<p>同时要修改flume的conf/flume-env.sh.template</p>
<p></p>
<pre><code class="language-html"># Note that the Flume conf directory is always included in the classpath.
export FLUME_CLASSPATH="/home/sff/logSystem/apache-flume-1.7.0-bin/lib/"
FLUME_CLASSPATH="/home/sff/logSystem/apache-flume-1.7.0-bin/lib/"</code></pre><br>
做到这一步的时候，运行的话会出现一个 <span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">No FileSystem for scheme:</span>
<p></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);">（2）解决<span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">No
 FileSystem for scheme:错误</span></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);">将hadoop中的hadoop-commom-x.jar里面的core-default.xml文件取出并修改，添加如下代码：</span></span></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"></span></span></span></span></p>
<pre><code class="language-html">&lt;property&gt;
&lt;name&gt;fs.hdfs.impl&lt;/name&gt;
&lt;value&gt;org.apache.hadoop.hdfs.DistributedFileSystem&lt;/value&gt;
&lt;description&gt;The FileSystem for hdfs: uris.&lt;/description&gt;
&lt;/property&gt;</code></pre><br><p></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);">flume重新运行，flume发送文件，如果这是报DistributedFileSystem no class之类的错误，这是就需要再进一步修改：</span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;"><br style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;background-color:rgb(241,241,241);"><span style="background-color:rgb(255,255,255);"><span style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;">解决方法：</span><br style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;"><span style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;">vi ./hadoop-common-project/hadoop-auth/pom.xml</span><br style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;"><span style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;">vi ./hadoop-common-project/hadoop-common/pom.xml </span></span><br></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;"></span></span></p>
<pre><code class="language-html">&lt;dependency&gt;
&lt;groupId&gt;org.apache.hadoop&lt;/groupId&gt;
&lt;artifactId&gt;hadoop-annotations&lt;/artifactId&gt;
&lt;scope&gt;compile&lt;/scope&gt;
&lt;/dependency&gt;</code></pre><br><span style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;background-color:rgb(255,255,255);">将节点hadoop-annotations的值由provide 改成compile</span><br><p></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;"><span style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;background-color:rgb(255,255,255);"><br></span></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;"><span style="color:rgb(68,68,68);font-family:'微软雅黑', '宋体';font-size:14px;background-color:rgb(255,255,255);">最终运行flume,sink hdfs发送文件。</span></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';color:#333333;"><span style="font-size:14px;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><span style="color:rgb(51,51,51);font-family:tahoma, '宋体';font-size:14px;text-align:justify;background-color:rgb(250,250,252);"><br></span></span></span></span></p>
<p style="text-align:justify;"><span style="font-family:tahoma, '宋体';"><span style="font-family:tahoma, '宋体';text-align:justify;"><span style="font-family:tahoma, '宋体';text-align:justify;background-color:rgb(255,255,255);"><span style="font-size:18px;">2. hadoop 在hdfs
 namenode -format 的时候导致namenode的cluster和datanode的cluster id不一致导致datanode启动不起来</span></span></span></span></p>
<p style="text-align:justify;">看linux下/tmp/hadoop-root/dfs/data/currentVERSION 中把datanode clusterId的值改成namenode clusterId</p>
<p style="text-align:justify;"><br></p>
<p style="text-align:justify;"><span style="font-size:18px;">3. hadoop中的logs/hadoop-root-secondarynamenode-localhost.localdomain.log 文件报错</span></p>
<p style="text-align:justify;"></p><pre><code class="language-html">2016-06-09 18:07:04,364 ERROR org.apache.hadoop.hdfs.server.namenode.SecondaryNameNode: Exception in doCheckpoint
java.io.IOException: Inconsistent checkpoint fields.
LV = -60 namespaceID = 584809856 cTime = 0 ; clusterId = CID-c602bccc-3af9-431d-8391-f57b9660d078 ; blockpoolId = BP-998352565-192.168.1.118-1465453069192.
Expecting respectively: -60; 773040246; 0; CID-48c46c43-8a44-4096-93bf-61d9942c8e2b; BP-1325084739-192.168.1.108-1456487846292.</code></pre><br>
配置etc/hadoop/core-site.xml文件
<p style="text-align:justify;"></p><pre><code class="language-html">&lt;property&gt;
    &lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
    &lt;value&gt;/data/hadoop/tmp&lt;/value&gt;
&lt;/property&gt;</code></pre><br><span style="color:rgb(75,75,75);font-family:Verdana, Helvetica, Arial;">hadoop.tmp.dir： <span style="color:rgb(75,75,75);font-family:Verdana, Helvetica, Arial;">临时文件夹，指定后需将使用到的所有子级文件夹都要手动创建出来，否则无法正常启动服务。（没有默认值）</span></span>
<p style="text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial;color:#4b4b4b;">下面有dfs文件夹，dfs下面有data  name  namesecondary三个文件夹。</span></p>
<p style="text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial;color:#4b4b4b;"><br></span></p>
<p style="text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial;color:#4b4b4b;"><span style="font-size:18px;text-align:justify;">4. hadoop中的</span><span style="font-size:18px;text-align:justify;">logs/hadoop-root-namenode-localhost.localdomain.log
 文件报错</span></span></p>
<p style="text-align:justify;"><span style="font-family:Verdana, Helvetica, Arial;color:#4b4b4b;"></span></p><pre><code class="language-html">java.io.IOException:NameNode is not formatter</code></pre><span style="font-size:10px;">这时候关掉hadoop，bin/hdfs dfs namenode -format 即可</span><br><br><br>            </div>
                </div>
---
layout:     post
title:      Hadoop Commands Guide
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;">所有的Hadoop命令都通过bin/hadoop脚本调用。</span></p>
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><br></span></p>
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;">在没有任何参数的情况下，运行Hadoop脚本将打印该命令描述。</span><br></span></p>
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"></span></p>
<pre><code class="language-plain">[hadoop@hadoop191 hadoop-2.8.0]$ bin/hadoop
Usage: hadoop [--config confdir] [COMMAND | CLASSNAME]
  CLASSNAME            run the class named CLASSNAME
 or
  where COMMAND is one of:
  fs                   run a generic filesystem user client
  version              print the version
  jar &lt;jar&gt;            run a jar file
                       note: please use "yarn jar" to launch
                             YARN applications, not this command.
  checknative [-a|-h]  check native hadoop and compression libraries availability
  distcp &lt;srcurl&gt; &lt;desturl&gt; copy file or directories recursively
  archive -archiveName NAME -p &lt;parent path&gt; &lt;src&gt;* &lt;dest&gt; create a hadoop archive
  classpath            prints the class path needed to get the
                       Hadoop jar and the required libraries
  credential           interact with credential providers
  daemonlog            get/set the log level for each daemon
  trace                view and modify Hadoop tracing settings

Most commands print help when invoked w/o parameters.</code></pre>
<p><br></p>
<p><br></p>
<p></p>
<h2 style="font-size:17px;color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;">
Overview</h2>
<span style="font-family:Verdana, Helvetica, Arial, sans-serif;">Usage: </span><tt>hadoop [--config confdir] [--loglevel loglevel] [COMMAND] [GENERIC_OPTIONS] [COMMAND_OPTIONS]</tt>
<p></p>
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><tt></tt></span>
</p><table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;color:rgb(0,0,0);font-family:Verdana, Helvetica, Arial, sans-serif;"><thead><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);text-align:left;font-size:11px;">
FIELD</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);text-align:left;font-size:11px;">
Description</th>
</tr></thead><tbody><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>--config confdir</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
默认配置文件目录。<span style="color:rgb(51,51,51);font-family:monospace;background-color:rgb(238,238,238);">${HADOOP_HOME}/etc/hadoop  <br>
命令：bin/hadoop -config etc/hadoop/</span></td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>--loglevel loglevel</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Overwrites the log level. Valid log levels are FATAL, ERROR, WARN, INFO, DEBUG, and TRACE. Default is INFO.<br>
bin/hadoop --loglevel FATAL</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
GENERIC_OPTIONS</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The common set of options supported by multiple commands.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
COMMAND_OPTIONS</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Various commands with their options are described in this documention for the Hadoop common sub-project. HDFS and YARN are covered in other documents.</td>
</tr></tbody></table><br><p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><tt></tt></span></p>
<h2 style="font-size:17px;color:rgb(51,51,51);font-family:Verdana, Helvetica, Arial, sans-serif;">
Generic Options</h2>
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><tt></tt></span>
</p><table border="0" class="bodyTable" style="margin-left:-2px;font-size:10px;color:rgb(0,0,0);font-family:Verdana, Helvetica, Arial, sans-serif;"><thead><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);text-align:left;font-size:11px;">
GENERIC_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);text-align:left;font-size:11px;">
Description</th>
</tr></thead><tbody><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-archives &lt;comma separated list of archives&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify comma separated archives to be unarchived on the compute machines. Applies only to job.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-conf &lt;configuration file&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify an application configuration file.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-D &lt;property&gt;=&lt;value&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Use value for given property.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-files &lt;comma separated list of files&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify comma separated files to be copied to the map reduce cluster. Applies only to job.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-fs &lt;file:///&gt; or &lt;hdfs://namenode:port&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify default filesystem URL to use. Overrides ‘fs.defaultFS’ property from configurations.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-jt &lt;local&gt; or &lt;resourcemanager:port&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify a ResourceManager. Applies only to job.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-libjars &lt;comma seperated list of jars&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify comma separated jar files to include in the classpath. Applies only to job.</td>
</tr></tbody></table><br><p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><tt><br></tt></span></p>
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><tt><br></tt></span></p>
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><tt><br></tt></span></p>
<p><span style="color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:14px;"><tt><br></tt></span></p>
            </div>
                </div>
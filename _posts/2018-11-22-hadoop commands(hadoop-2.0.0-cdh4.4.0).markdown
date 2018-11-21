---
layout:     post
title:      hadoop commands(hadoop-2.0.0-cdh4.4.0)
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/wuzhongdehua1/article/details/14141025				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="line-height:1.3em;font-family:Verdana, Helvetica, Arial, sans-serif;"></p>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);">Overview<a name="Overview"></a></h2>
<p style="line-height:1.3em;font-size:12px;">All hadoop commands are invoked by the <tt>bin/hadoop</tt> script. Running the hadoop script without any arguments prints the description for all commands.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop [--config confdir] [COMMAND] [GENERIC_OPTIONS] [COMMAND_OPTIONS]</tt></p>
<p style="line-height:1.3em;font-size:12px;">Hadoop has an option parsing framework that employs parsing generic options as well as running classes.</p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>--config confdir</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Overwrites the default Configuration directory. Default is <tt>$<a name="HADOOP_HOME">HADOOP_HOME</a>/conf</tt>.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
GENERIC_OPTIONS COMMAND_OPTIONS</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
The common set of options supported by multiple commands. Various commands with their options are described in the following sections. The commands have been grouped into User Commands and Administration Commands.</td>
</tr></tbody></table></div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);">Generic Options<a name="Generic_Options"></a></h2>
<p style="line-height:1.3em;font-size:12px;">The following options are supported by <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#dfsadmin" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">dfsadmin</a>, <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#fs" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">fs</a>, <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#fsck" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">fsck</a>, <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#job" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">job</a> and <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#fetchdt" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">fetchdt</a>.
 Applications should implement <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#some_useful_url" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Tool</a> to
 support <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#another_useful_url" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">GenericOptions</a>.</p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
GENERIC_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-conf &lt;configuration file&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify an application configuration file.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-D &lt;property&gt;=&lt;value&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Use value for given property.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-jt &lt;local&gt; or &lt;jobtracker:port&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify a job tracker. Applies only to job.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-files &lt;comma separated list of files&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify comma separated files to be copied to the map reduce cluster. Applies only to job.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-libjars &lt;comma seperated list of jars&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify comma separated jar files to include in the classpath. Applies only to job.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<tt>-archives &lt;comma separated list of archives&gt;</tt></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Specify comma separated archives to be unarchived on the compute machines. Applies only to job.</td>
</tr></tbody></table></div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);">User Commands<a name="User_Commands"></a></h2>
<p style="line-height:1.3em;font-size:12px;">Commands useful for users of a hadoop cluster.</p>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>archive</tt><a name="archive"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Creates a hadoop archive. More information can be found at Hadoop Archives.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop archive -archiveName NAME &lt;src&gt;* &lt;dest&gt;</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-archiveName NAME</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Name of the archive to be created.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
src</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Filesystem pathnames which work as usual with regular expressions.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
dest</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Destination directory which would contain the archive.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>distcp</tt><a name="distcp"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Copy file or directories recursively. More information can be found at Hadoop DistCp Guide.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop distcp &lt;srcurl&gt; &lt;desturl&gt;</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
srcurl</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Source Url</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
desturl</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Destination Url</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>fs</tt><a name="fs"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop fs [GENERIC_OPTIONS] [COMMAND_OPTIONS]</tt></p>
<p style="line-height:1.3em;font-size:12px;">Deprecated, use <tt>hdfs dfs</tt> instead.</p>
<p style="line-height:1.3em;font-size:12px;">Runs a generic filesystem user client.</p>
<p style="line-height:1.3em;font-size:12px;">The various COMMAND_OPTIONS can be found at File System Shell Guide.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>fsck</tt><a name="fsck"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs a HDFS filesystem checking utility. See <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#Fsck" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">Fsck</a> for
 more info.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop fsck [GENERIC_OPTIONS] &lt;path&gt; [-move | -delete | -openforwrite] [-files [-blocks [-locations | -racks]]]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<em>path</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Start checking from this path.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-move</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Move corrupted files to /lost+found</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-delete</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Delete corrupted files.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-openforwrite</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Print out files opened for write.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-files</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Print out files being checked.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-blocks</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Print out block report.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-locations</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Print out locations for every block.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-racks</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Print out network topology for data-node locations.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>fetchdt</tt><a name="fetchdt"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Gets Delegation Token from a NameNode. See <a href="http://archive.cloudera.com/cdh4/cdh/4/hadoop-2.0.0-cdh4.4.0/hadoop-project-dist/hadoop-common/CommandsManual.html#fetchdt" rel="nofollow" style="text-decoration:none;color:rgb(102,136,170);">fetchdt</a> for
 more info.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop fetchdt [GENERIC_OPTIONS] [--webservice &lt;namenode_http_addr&gt;] &lt;path&gt;</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
<em>fileName</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
File name to store the token into.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
--webservice <em>https_address</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
use http protocol instead of RPC</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>jar</tt><a name="jar"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs a jar file. Users can bundle their Map Reduce code in a jar file and execute it using this command.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop jar &lt;jar&gt; [mainClass] args...</tt></p>
<p style="line-height:1.3em;font-size:12px;">The streaming jobs are run via this command. Examples can be referred from Streaming examples</p>
<p style="line-height:1.3em;font-size:12px;">Word count example is also run using jar command. It can be referred from Wordcount example</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>job</tt><a name="job"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Command to interact with Map Reduce Jobs.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop job [GENERIC_OPTIONS] [-submit &lt;job-file&gt;] | [-status &lt;job-id&gt;] | [-counter &lt;job-id&gt; &lt;group-name&gt; &lt;counter-name&gt;] | [-kill &lt;job-id&gt;] | [-events &lt;job-id&gt; &lt;from-event-#&gt; &lt;#-of-events&gt;] | [-history
 [all] &lt;jobOutputDir&gt;] | [-list [all]] | [-kill-task &lt;task-id&gt;] | [-fail-task &lt;task-id&gt;] | [-set-priority &lt;job-id&gt; &lt;priority&gt;]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-submit <em>job-file</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Submits the job.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-status <em>job-id</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Prints the map and reduce completion percentage and all job counters.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-counter <em>job-id group-namecounter-name</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Prints the counter value.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-kill <em>job-id</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Kills the job.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-events <em>job-id from-event-# #-of-events</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Prints the events' details received by jobtracker for the given range.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-history [all]<em>jobOutputDir</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Prints job details, failed and killed tip details. More details about the job such as successful tasks and task attempts made for each task can be viewed by specifying the [all] option.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-list [all]</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Displays jobs which are yet to complete. <tt>-list all</tt> displays all jobs.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-kill-task <em>task-id</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Kills the task. Killed tasks are NOT counted against failed attempts.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-fail-task <em>task-id</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Fails the task. Failed tasks are counted against failed attempts.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-set-priority <em>job-id priority</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Changes the priority of the job. Allowed priority values are VERY_HIGH, HIGH, NORMAL, LOW, VERY_LOW</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>pipes</tt><a name="pipes"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs a pipes job.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop pipes [-conf &lt;path&gt;] [-jobconf &lt;key=value&gt;, &lt;key=value&gt;, ...] [-input &lt;path&gt;] [-output &lt;path&gt;] [-jar &lt;jar file&gt;] [-inputformat &lt;class&gt;] [-map &lt;class&gt;] [-partitioner &lt;class&gt;] [-reduce &lt;class&gt;] [-writer
 &lt;class&gt;] [-program &lt;executable&gt;] [-reduces &lt;num&gt;]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-conf <em>path</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Configuration for job</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-jobconf <em>key=value</em>, <em>key=value</em>, ...</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Add/override configuration for job</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-input <em>path</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Input directory</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-output <em>path</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Output directory</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-jar <em>jar file</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Jar filename</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-inputformat <em>class</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
InputFormat class</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-map <em>class</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Java Map class</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-partitioner <em>class</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Java Partitioner</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-reduce <em>class</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Java Reduce class</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-writer <em>class</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Java RecordWriter</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-program <em>executable</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Executable URI</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-reduces <em>num</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Number of reduces</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>queue</tt><a name="queue"></a></h3>
<p style="line-height:1.3em;font-size:12px;">command to interact and view Job Queue information</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop queue [-list] | [-info &lt;job-queue-name&gt; [-showJobs]] | [-showacls]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-list</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Gets list of Job Queues configured in the system. Along with scheduling information associated with the job queues.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-info <em>job-queue-name</em> [-showJobs]</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Displays the job queue information and associated scheduling information of particular job queue. If <tt>-showJobs</tt> options is present a list of jobs submitted to the particular job queue is displayed.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-showacls</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Displays the queue name and associated queue operations allowed for the current user. The list consists of only those queues to which the user has access.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>version</tt><a name="version"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Prints the version.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop version</tt></p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>CLASSNAME</tt><a name="CLASSNAME"></a></h3>
<p style="line-height:1.3em;font-size:12px;">hadoop script can be used to invoke any class.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop CLASSNAME</tt></p>
<p style="line-height:1.3em;font-size:12px;">Runs the class named <tt>CLASSNAME</tt>.</p>
</div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>classpath</tt><a name="classpath"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Prints the class path needed to get the Hadoop jar and the required libraries.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop classpath</tt></p>
</div>
</div>
<div class="section" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:13px;">
<h2 style="font-size:17px;color:rgb(51,51,51);">Administration Commands<a name="Administration_Commands"></a></h2>
<p style="line-height:1.3em;font-size:12px;">Commands useful for administrators of a hadoop cluster.</p>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>balancer</tt><a name="balancer"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs a cluster balancing utility. An administrator can simply press Ctrl-C to stop the rebalancing process. See Rebalancer for more details.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop balancer [-threshold &lt;threshold&gt;]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Description</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-threshold <em>threshold</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Percentage of disk capacity. This overwrites the default threshold.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>daemonlog</tt><a name="daemonlog"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Get/Set the log level for each daemon.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop daemonlog -getlevel &lt;host:port&gt; &lt;name&gt; </tt>Usage: <tt>hadoop daemonlog -setlevel &lt;host:port&gt; &lt;name&gt; &lt;level&gt;</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-getlevel <em>host:port name</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Prints the log level of the daemon running at <em>host:port</em>. This command internally connects to http://<em>host:port</em>/logLevel?log=<em>name</em></td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-setlevel <em>host:port name level</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Sets the log level of the daemon running at <em>host:port</em>. This command internally connects to http://<em>host:port</em>/logLevel?log=<em>name</em></td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>datanode</tt><a name="datanode"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs a HDFS datanode.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop datanode [-rollback]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-rollback</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Rollsback the datanode to the previous version. This should be used after stopping the datanode and distributing the old hadoop version.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>dfsadmin</tt><a name="dfsadmin"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs a HDFS dfsadmin client.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop dfsadmin [GENERIC_OPTIONS] [-report] [-safemode enter | leave | get | wait] [-refreshNodes] [-finalizeUpgrade] [-upgradeProgress status | details | force] [-metasave filename] [-setQuota &lt;quota&gt;
 &lt;dirname&gt;...&lt;dirname&gt;] [-clrQuota &lt;dirname&gt;...&lt;dirname&gt;] [-restoreFailedStorage true|false|check] [-help [cmd]]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-report</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Reports basic filesystem information and statistics.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-safemode enter / leave / get / wait</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Safe mode maintenance command. Safe mode is a Namenode state in which it  <br>
1. does not accept changes to the name space (read-only)  <br>
2. does not replicate or delete blocks.  <br>
Safe mode is entered automatically at Namenode startup, and leaves safe mode automatically when the configured minimum percentage of blocks satisfies the minimum replication condition. Safe mode can also be entered manually, but then it can only be turned off
 manually as well.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-refreshNodes</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Re-read the hosts and exclude files to update the set of Datanodes that are allowed to connect to the Namenode and those that should be decommissioned or recommissioned.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-finalizeUpgrade</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Finalize upgrade of HDFS. Datanodes delete their previous version working directories, followed by Namenode doing the same. This completes the upgrade process.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-upgradeProgress status / details / force</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Request current distributed upgrade status, a detailed status or force the upgrade to proceed.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-metasave filename</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Save Namenode's primary data structures to <em>filename</em> in the directory specified by hadoop.log.dir property. <em>filename</em> will contain one line for each of the following <br>
1. Datanodes heart beating with Namenode <br>
2. Blocks waiting to be replicated <br>
3. Blocks currrently being replicated <br>
4. Blocks waiting to be deleted </td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-setQuota <em>quotadirname</em>...<em>dirname</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Set the quota <em>quota</em> for each directory <em>dirname</em>. The directory quota is a long integer that puts a hard limit on the number of names in the directory tree. Best effort for the directory, with faults reported if  <br>
1. N is not a positive integer, or  <br>
2. user is not an administrator, or  <br>
3. the directory does not exist or is a file, or  <br>
4. the directory would immediately exceed the new quota.  </td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-clrQuota<em>dirname</em>...<em>dirname</em></td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Clear the quota for each directory <em>dirname</em>. Best effort for the directory. with fault reported if  <br>
1. the directory does not exist or is a file, or  <br>
2. user is not an administrator. It does not fault if the directory has no quota.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-restoreFailedStorage true / false / check</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
This option will turn on/off automatic attempt to restore failed storage replicas. If a failed storage becomes available again the system will attempt to restore edits and/or fsimage during checkpoint. 'check' option will return current setting.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-help [cmd]</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Displays help for the given command or all commands if none is specified.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>mradmin</tt><a name="mradmin"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs MR admin client</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop mradmin [ GENERIC_OPTIONS ] [-refreshQueueAcls]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-refreshQueueAcls</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Refresh the queue acls used by hadoop, to check access during submissions and administration of the job by the user. The properties present in mapred-queue-acls.xml is reloaded by the queue manager.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>jobtracker</tt><a name="jobtracker"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs the MapReduce job Tracker node.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop jobtracker [-dumpConfiguration]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-dumpConfiguration</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Dumps the configuration used by the JobTracker alongwith queue configuration in JSON format into Standard output used by the jobtracker and exits.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>namenode</tt><a name="namenode"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs the namenode. More info about the upgrade, rollback and finalize is at Upgrade Rollback</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop namenode [-format] | [-upgrade] | [-rollback] | [-finalize] | [-importCheckpoint]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-format</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Formats the namenode. It starts the namenode, formats it and then shut it down.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-upgrade</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Namenode should be started with upgrade option after the distribution of new hadoop version.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-rollback</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Rollsback the namenode to the previous version. This should be used after stopping the cluster and distributing the old hadoop version.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-finalize</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Finalize will remove the previous state of the files system. Recent upgrade will become permanent. Rollback option will not be available anymore. After finalization it shuts the namenode down.</td>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-importCheckpoint</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Loads image from a checkpoint directory and save it into the current one. Checkpoint dir is read from property fs.checkpoint.dir</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>secondarynamenode</tt><a name="secondarynamenode"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs the HDFS secondary namenode. See Secondary Namenode for more info.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop secondarynamenode [-checkpoint [force]] | [-geteditsize]</tt></p>
<table border="1" class="bodyTable" style="margin-left:-2px;font-size:10px;"><tbody><tr class="a" style="background-color:rgb(221,221,221);"><th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
COMMAND_OPTION</th>
<th align="left" style="vertical-align:top;color:#FFFFFF;background-color:rgb(187,187,187);font-size:11px;">
Description</th>
</tr><tr class="b" style="background-color:rgb(238,238,238);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-checkpoint [-force]</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Checkpoints the Secondary namenode if EditLog size &gt;= fs.checkpoint.size. If <tt>-force</tt> is used, checkpoint irrespective of EditLog size.</td>
</tr><tr class="a" style="background-color:rgb(221,221,221);"><td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
-geteditsize</td>
<td align="left" style="font-family:Verdana, Helvetica, Arial, sans-serif;font-size:11px;color:rgb(51,51,51);vertical-align:top;">
Prints the EditLog size.</td>
</tr></tbody></table></div>
<div class="section">
<h3 style="color:rgb(102,102,102);background-color:rgb(204,204,204);font-size:14px;">
<tt>tasktracker</tt><a name="tasktracker"></a></h3>
<p style="line-height:1.3em;font-size:12px;">Runs a MapReduce task Tracker node.</p>
<p style="line-height:1.3em;font-size:12px;">Usage: <tt>hadoop tasktracker</tt></p>
</div>
</div>
<p><span style="font-family:Verdana, Helvetica, Arial, sans-serif;"><br></span></p>
            </div>
                </div>
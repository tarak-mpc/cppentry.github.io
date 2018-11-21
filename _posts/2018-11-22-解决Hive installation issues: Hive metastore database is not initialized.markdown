---
layout:     post
title:      解决Hive installation issues: Hive metastore database is not initialized
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
I tried to install hive on a raspberry pi 2. I installed Hive by uncompress zipped Hive package and configure $HADOOP_HOME and $HIVE_HOME manually under hduser user-group I created. When running hive, I got the following error message: hive</p>
<blockquote style="font-size:15px;border-width:0px 0px 0px 2px;border-left-style:solid;border-left-color:rgb(255,235,142);color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;background-color:rgb(255,248,220);">
<p style="border:0px;clear:both;">
ERROR StatusLogger No log4j2 configuration file found. Using default configuration: logging only errors to the console.</p>
<p style="border:0px;clear:both;">
Exception in thread "main" java.lang.RuntimeException: Hive metastore database is not initialized. Please use schematool (e.g. ./schematool -initSchema -dbType ...) to create the schema. If needed, don't forget to include the option to auto-create the underlying
 database in your JDBC connection string (e.g. ?createDatabaseIfNotExist=true for <a href="http://lib.csdn.net/base/14" rel="nofollow" class="replace_word" title="undefined" style="text-decoration:none;color:rgb(223,52,52);font-weight:bold;">MySQL</a>)</p>
</blockquote>
<p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
So I ran the command suggested in the above error message: schematool -dbType derby -initSchema I got the error message:</p>
<blockquote style="font-size:15px;border-width:0px 0px 0px 2px;border-left-style:solid;border-left-color:rgb(255,235,142);color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;background-color:rgb(255,248,220);">
<p style="border:0px;clear:both;">
Error: FUNCTION 'NUCLEUS_ASCII' already exists. (state=X0Y68,code=30000) org.apache.hadoop.hive.metastore.HiveMetaException: Schema initialization FAILED! Metastore state would be inconsistent !! <span style="border:0px;">* schemaTool
 failed *</span></p>
</blockquote>
<p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
It seems there aren't any helpful information when I try to google this error online. Any help or any explanation on how Hive works with Derby would be appreciated!</p>
<br style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;"><p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
answer：</p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
<br></p>
<p style="color:rgb(85,85,85);font-family:'microsoft yahei';font-size:15px;line-height:35px;">
</p>
<p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
After installing hive, if the first thing you did was run hive, hive attempted to create/initialize the metastore_db, but apparently might not get it right. On that initial run, maybe you saw your error:</p>
<pre style="line-height:35px;border:0px;font-size:13px;overflow:auto;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;color:rgb(36,39,41);background-color:rgb(239,240,241);"><code style="border:0px;font-size:13px;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;">Exception in thread "main" java.lang.RuntimeException: Hive metastore database is not initialized. Please use schematool (e.g. ./schematool -initSchema -dbType ...) to create the schema. If needed, don't forget to include the option to auto-create the underlying database in your JDBC connection string (e.g. ?createDatabaseIfNotExist=true for mysql)
</code></pre>
<p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
Running hive, even though it fails, creates a metastore_db directory in the directory from which you ran hive:</p>
<pre style="line-height:35px;border:0px;font-size:13px;overflow:auto;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;color:rgb(36,39,41);background-color:rgb(239,240,241);"><code style="border:0px;font-size:13px;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;">ubuntu15-laptop: ~ $&gt;ls -l |grep meta
drwxrwxr-x 5 testuser testuser 4096 Apr 14 12:44 metastore_db
</code></pre>
<p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
So when you then tried running</p>
<pre style="line-height:35px;border:0px;font-size:13px;overflow:auto;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;color:rgb(36,39,41);background-color:rgb(239,240,241);"><code style="border:0px;font-size:13px;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;">ubuntu15-laptop: ~ $&gt;schematool -initSchema -dbType derby<span style="color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;font-size:15px;line-height:15.6px;">（</span><span style="color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;font-size:15px;line-height:15.6px;">或者</span><span style="color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;font-size:15px;line-height:15.6px;">mysql）</span>
</code></pre>
<p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
The metastore already existed, but not in complete form.</p>
<p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
Soooooo the answer is:</p>
<ol style="font-size:15px;border:0px;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;"><li style="border:0px;">
<p style="border:0px;clear:both;">
Before you run hive for the first time, run</p>
<p style="border:0px;clear:both;">
schematool -initSchema -dbType derby<span style="color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;font-size:15px;line-height:15.6px;">（</span><span style="color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;font-size:15px;line-height:15.6px;">或者</span><span style="color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;font-size:15px;line-height:15.6px;">mysql）</span></p>
<pre style="color:rgb(36,39,41);font-size:13px;line-height:35px;border:0px;overflow:auto;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, sans-serif;background-color:rgb(239,240,241);">
</pre>
<p></p>
</li><li style="border:0px;">
<p style="border:0px;clear:both;">
If you already ran hive and then tried to initSche<br>
ma and it's failing:</p>
<p style="border:0px;clear:both;">
mv metastore_db metastore_db.tmp</p>
</li><li style="border:0px;">
<p style="border:0px;clear:both;">
Re run</p>
<p style="border:0px;clear:both;">
schematool -initSchema -dbType derby（<span style="color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;font-size:15px;line-height:15.6px;">或者</span>mysql）</p>
</li><li style="border:0px;">
<p style="border:0px;clear:both;">
Run hive again</p>
</li></ol><p style="font-size:15px;border:0px;clear:both;color:rgb(36,39,41);font-family:Arial, 'Helvetica Neue', Helvetica, sans-serif;line-height:15.6px;">
**Also of note: if you change directories, the metastore_db created above won't be found! I'm sure there's a good reason for this that I don't know yet because I'm literally trying to use hive for the first time today. Ahhh here's information on this: metastore_db
 created wherever I run Hive</p>
            </div>
                </div>
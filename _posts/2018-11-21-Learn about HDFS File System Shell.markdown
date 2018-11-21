---
layout:     post
title:      Learn about HDFS File System Shell
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">Core-site.xml</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;"> &lt;property&gt;</span></div>
<p><span style="color:#2C3134;">    </span>&lt;name&gt;<span style="color:#2C3134;">fs.defaultFS</span><span style="color:#2C3134;">&lt;/name&gt;</span></p>
<p><span style="color:#2C3134;">   </span>&lt;value&gt;hdfs://cilax01.unx.sas.com:8020&lt;/value&gt;</p>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">  </span>&lt;/property&gt;</div>
<div><span style="color:#00539B;">•</span></div>
<p><span style="color:#2C3134;">This is the Default </span><span style="color:#2C3134;">Namenode</span><span style="color:#2C3134;"> URI hdfs</span><span style="color:#2C3134;">://cilax01.unx.sas.com:8020. This will be used as URI in HDFS File system shell command.</span></p>
<p><br></p>
<p><br><span style="color:#2C3134;"></span></p>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">Hdfs-site.xml</span></div>
<div><span style="color:#00539B;">•</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;"> &lt;property&gt;   
</span></div>
<p></p>
<p><span style="color:#2C3134;">  </span>    &lt;<span style="color:#2C3134;">name&gt;</span><span style="color:#2C3134;">dfs.namenode.http</span><span style="color:#2C3134;">-address&lt;/name&gt;  
</span></p>
<p><span style="color:#2C3134;">  </span>     &lt;<span style="color:#2C3134;">value&gt;cilax01.unx.sas.com:50070&lt;/value&gt; 
</span></p>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">&lt;/</span><span style="color:#2C3134;">property</span><span style="color:#2C3134;">&gt;</span></div>
<div><span style="color:#00539B;">•</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">This is the Hadoop web interface
</span><span style="color:#2C3134;">cilax01.unx.sas.com:50070 (If you cannot open it, add http://)<br><br><br><br></span></div>
You can check data with
<div><span style="color:#00539B;">•</span><span style="color:#00539B;">Hadoop web interface<span style="color:#2C3134;">     
</span></span><span style="color:#2C3134;">dfs.namenode.http</span><span style="color:#2C3134;">-address</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">HDFS </span>
<span style="color:#2C3134;">File System Shell</span></div>
<p><br></p>
<p><span style="color:#2C3134;"></span></p>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs –command 
</span>URI [URI …]</div>
<div><span style="color:#00539B;">•</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">The </span><span style="color:#2C3134;">URI format is
</span><span style="color:#2C3134;">scheme://autority/path</span><span style="color:#2C3134;">. For HDFS the scheme is
</span><span style="color:#2C3134;">hdfs</span><span style="color:#2C3134;">, and for the local
</span><span style="color:#2C3134;">filesystem</span><span style="color:#2C3134;"> the scheme is
</span><span style="color:#2C3134;">file</span><span style="color:#2C3134;">. The scheme and authority are optional. If not specified, thedefault scheme specified in the configuration is used</span><span style="color:#2C3134;">.</span></div>
<div><span style="color:#00539B;">•</span><font color="#2C3134">Default URI is current user’spath   hdfs://hostname:port/user/username<br><br>
If use hadoop fs -ls <span style="color:#2C3134;"><span style="color:#2C3134;">hdfs://hostname:port/</span></span> check, it will be the root path, contain all the users directories. Here pay attention to the '/' at the end of the comamnd.<br>
If use hadoop fs -ls <font color="#2C3134"><span style="color:#2C3134;"><span style="color:#2C3134;"><span style="color:#2C3134;">hdfs://hostname:port</span></span></span></font>, it will be the user's directory. Same as hadoop fs -ls.
<br><br></font></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">You can use the command tosee the guide</span></div>
<span style="color:#2C3134;"></span>
<div><span style="color:#2C3134;">  hadoop</span></div>
<div><span style="color:#2C3134;">  h</span><span style="color:#2C3134;">adoop</span><span style="color:#2C3134;"> fs</span></div>
<div><span style="color:#2C3134;">  hadoop</span><span style="color:#2C3134;"> fs -command
</span></div>
<p></p>
<p><br></p>
<p><span style="color:#2C3134;">The shell usually use:</span></p>
<p><span style="color:#2C3134;"></span></p>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">p</span><span style="color:#2C3134;">ut         
</span><span style="color:#2C3134;"> </span>Read input from local filesystem or <span style="color:#2C3134;">
stdin</span></div>
<div><span style="color:#00539B;">      </span><span style="color:#2C3134;">h</span><span style="color:#2C3134;">adoop</span><span style="color:#2C3134;">fs -put core-site.xml
</span><span style="color:#2C3134;">hdfs-site.xml /user/</span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;">/.../</span><span style="color:#2C3134;">tempdir</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">mkdir</span><span style="color:#2C3134;">             
</span><span style="color:#2C3134;">Create path in </span><span style="color:#2C3134;">hadoop</span></div>
<div><span style="color:#00539B;">      </span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs –</span><span style="color:#2C3134;">mkdir</span><span style="color:#2C3134;"> /user/</span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;">/.../</span><span style="color:#2C3134;">tempdir</span><span style="color:#2C3134;">/</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">ls</span><span style="color:#2C3134;">              </span>List files under the path</div>
<div><span style="color:#00539B;">      </span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs -</span><span style="color:#2C3134;">ls</span><span style="color:#2C3134;"> /user/</span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;">/.../</span><span style="color:#2C3134;">tempdir<br></span>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">text</span></div>
<div><span style="color:#00539B;"> </span><span style="color:#2C3134;">Usage: </span>
<span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs -text &lt;</span><span style="color:#2C3134;">src</span><span style="color:#2C3134;">&gt; </span></div>
<div><span style="color:#2C3134;"> Takes a source file and outputs the file in text format. Theallowed formats are zip and
</span><span style="color:#2C3134;">TextRecordInputStream</span><span style="color:#2C3134;">.</span></div>
<div><span style="color:#00539B;">•</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">t</span><span style="color:#2C3134;">ouchz</span></div>
<div><span style="color:#2C3134;">    Usage: </span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs -</span><span style="color:#2C3134;">touchz</span><span style="color:#2C3134;"> URI [URI …] </span></div>
<div><span style="color:#2C3134;">    Create </span><span style="color:#2C3134;">a file of zero length</span><span style="color:#2C3134;">.
</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">rm</span></div>
<div><span style="color:#00539B;">   </span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs -</span><span style="color:#2C3134;">rm</span><span style="color:#2C3134;"> /user/</span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;">/.../</span><span style="color:#2C3134;">tempdir</span><span style="color:#2C3134;">/core-site.xml
</span></div>
<div><span style="color:#2C3134;">   hadoop</span><span style="color:#2C3134;">fs -</span><span style="color:#2C3134;">rm</span><span style="color:#2C3134;"> -r
</span><span style="color:#2C3134;">Liastest</span><span style="color:#2C3134;">/Lisatest1</span></div>
<p><span style="color:#2C3134;">Notes:  </span>–<span style="color:#2C3134;">rm</span><span style="color:#2C3134;"> –r can delete any typefile or folder, folder empty or not now. Do not recommand -rmr</span></p>
<p></p>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">c</span><span style="color:#2C3134;">ount
</span></div>
<div><span style="color:#2C3134;">   Usage: </span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs -count [-q] &lt;paths</span><span style="color:#2C3134;">&gt;</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">d</span><span style="color:#2C3134;">f</span><span style="color:#2C3134;"> 
</span>and <span style="color:#2C3134;">du</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">getmerge</span></div>
<div><span style="color:#00539B;"> </span><span style="color:#2C3134;">  hadoop</span><span style="color:#2C3134;"> fs -</span><span style="color:#2C3134;">getmerge</span><span style="color:#2C3134;"> &lt;</span><span style="color:#2C3134;">src</span><span style="color:#2C3134;">&gt;
 &lt;</span><span style="color:#2C3134;">localdst</span><span style="color:#2C3134;">&gt; [</span><span style="color:#2C3134;">addnl</span><span style="color:#2C3134;">]</span></div>
<div><span style="color:#00539B;"> </span><span style="color:#2C3134;">  Src</span><span style="color:#2C3134;"> is a directory of
</span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;">, </span>
<span style="color:#2C3134;">localdst</span><span style="color:#2C3134;"> is a locale file.
</span></div>
<div><span style="color:#2C3134;">   This command merge all files content under the
</span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> directory to the local file, then you can view the content fromthe local file</span></div>
<div><span style="color:#2C3134;">   The local file content will be overwrite</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">setrep</span></div>
<div><span style="color:#00539B;">   </span><span style="color:#2C3134;">Usage: </span>
<span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs -</span><span style="color:#2C3134;">setrep</span><span style="color:#2C3134;"> [-R] &lt;path&gt;</span></div>
<div><span style="color:#2C3134;">   Changes the replicationfactor of a file. -R option is for recursively increasing the replicationfactor of files within a directory.</span></div>
<div><span style="color:#00539B;">   Example: </span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;"> fs -</span><span style="color:#2C3134;">setrep</span><span style="color:#2C3134;">-w 3
</span><span style="color:#2C3134;">-R /</span><span style="color:#2C3134;">user/</span><span style="color:#2C3134;">hadoop</span><span style="color:#2C3134;">/dir1</span></div>
<div><span style="color:#00539B;">•</span><span style="color:#2C3134;">cp</span><span style="color:#2C3134;">   </span><span style="color:#2C3134;">Copy
</span><span style="color:#2C3134;">files from source to destination. This command allows multiplesources as well in which case the destination must be a directory</span><span style="color:#2C3134;">.</span></div>
<br><p></p>
<p><span style="color:#2C3134;"><br></span></p>
<br></div>
<br><p></p>
            </div>
                </div>
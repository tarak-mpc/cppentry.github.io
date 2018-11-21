---
layout:     post
title:      Hadoop：hadoop fs、hadoop dfs与hdfs dfs命令的区别
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主皮皮http://blog.csdn.net/pipisorry原创文章，未经博主允许不得转载。					https://blog.csdn.net/pipisorry/article/details/51340838				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>http://<a href="http://blog.csdn.net/pipisorry/article/details/51340838" rel="nofollow">blog.csdn.net/pipisorry/article/details/51340838</a><br></p>
<p></p>
<h1><span style="color:#FF0000;">'Hadoop DFS'和'Hadoop FS'的区别</span></h1>
<div>While exploring HDFS, I came across these two syntaxes for querying HDFS:<br></div>
<div>&gt; hadoop dfs<br style="list-style-type:none;color:rgb(51,51,51);font-family:Verdana, Arial, Tahoma;font-size:14px;line-height:25px;">
&gt; hadoop fs<br style="list-style-type:none;color:rgb(51,51,51);font-family:Verdana, Arial, Tahoma;font-size:14px;line-height:25px;"><br style="list-style-type:none;color:rgb(51,51,51);font-family:Verdana, Arial, Tahoma;font-size:14px;line-height:25px;">
why we have two different syntaxes for a common purpose<br style="list-style-type:none;color:rgb(51,51,51);font-family:Verdana, Arial, Tahoma;font-size:14px;line-height:25px;"><h2>为什么会对同一个功能提供两种命令标记?</h2>
<h3>命令的定义</h3>
it seems like there's no difference between the two syntaxes. If we look at the definitions of the two commands (hadoop fs and hadoop dfs) in $HADOOP_HOME/bin/hadoop<br></div>
<div>从两个命令的定义中（在$HADOOP_HOME/bin/hadoop）可以看到这两者之间似乎没有什么区别。</div>
<div>...  <br>
elif [ "$COMMAND" = "datanode" ] ; then  <br>
  CLASS='org.apache.hadoop.hdfs.server.datanode.DataNode'  <br>
  HADOOP_OPTS="$HADOOP_OPTS $HADOOP_DATANODE_OPTS"  <br>
elif [ "$COMMAND" = "fs" ] ; then  <br>
  CLASS=org.apache.hadoop.fs.FsShell  <br>
  HADOOP_OPTS="$HADOOP_OPTS $HADOOP_CLIENT_OPTS"  <br>
elif [ "$COMMAND" = "dfs" ] ; then  <br>
  CLASS=org.apache.hadoop.fs.FsShell  <br>
  HADOOP_OPTS="$HADOOP_OPTS $HADOOP_CLIENT_OPTS"  <br>
elif [ "$COMMAND" = "dfsadmin" ] ; then  <br>
  CLASS=org.apache.hadoop.hdfs.tools.DFSAdmin  <br>
  HADOOP_OPTS="$HADOOP_OPTS $HADOOP_CLIENT_OPTS"  <br>
...  <br></div>
<h3>貌似更有道理的解释</h3>
<div>
<p style="list-style-type:none;">
Unconvinced, and these excerpts made more sense to me:<br style="list-style-type:none;"></p>
<blockquote style="list-style-type:none;">FS relates to a generic file system which can point to any file systems like local, HDFS etc. But dfs is very specific to HDFS. So when we use FS it?can perform operation with from/to local or
 hadoop distributed file system to destination. But specifying DFS operation relates to?HDFS.</blockquote>
这个理由并不完全信服，下面这个解释貌似更有道理：</div>
<div>FS涉及到一个通用的文件系统，可以指向任何的文件系统如local，HDFS等。</div>
<div>但是DFS仅是针对HDFS的。</div>
<div>那么什么时候用FS呢？可以在本地与hadoop分布式文件系统的交互操作中使用。特定的DFS指令与HDFS有关。</div>
<h3><a href="http://lib.csdn.net/base/20" rel="nofollow" class="replace_word" title="Hadoop知识库">Hadoop</a>文档对这两个不同的shell的描述</h3>
<div>Below are two excerpts from the Hadoop documentation that describe these two as different shells.</div>
<div>
<p>下面是两个摘录<span style="color:#df3434;"><strong>Hadoop</strong></span>文档，描述这两个不同的shell。</p>
<ol start="1"><li class="alt">FS Shell  </li><li>The FileSystem (FS) shell is invoked by bin/hadoop fs. All the FS shell commands take path URIs as arguments. The URI format is scheme://autority/path. For HDFS the scheme is hdfs, and for the local filesystem the scheme is file. The scheme and authority are optional. If not specified, the default scheme specified in the configuration is used. An HDFS file or directory such as /parent/child can be specified as hdfs://namenodehost/parent/child or simply as /parent/child (given that your configuration is set to point to hdfs://namenodehost). Most of the commands in FS shell behave like corresponding Unix commands.? <br></li></ol><div>
<blockquote style="list-style-type:none;">
<div class="dp-highlighter bg_plain">
<ol start="1"><li class="alt">DFShell  </li><li>
<p>The HDFS shell is invoked by bin/hadoop dfs. All the HDFS shell commands take path URIs as arguments. The URI format is scheme://autority/path. For HDFS the scheme is hdfs, and for the local filesystem the scheme is file. The scheme and authority are optional. If not specified, the default scheme specified in the configuration is used. An HDFS file or directory such as /parent/child can be specified as hdfs://namenode:namenodeport/parent/child or simply as /parent/child (given that your configuration is set to point to namenode:namenodeport). Most of the commands in HDFS shell behave like corresponding Unix commands.? </p>
</li></ol></div>
</blockquote>
</div>
<p></p>
</div>
<div>So, based on the above, we can conclude that it all depends on the scheme configuration. When using these two commands with absolute URI (i.e. scheme://a/b) the?behavior?shall be identical. Only it's the default configured scheme value for file and hdfs
 for fs and dfs respectively, which is the cause for difference in?behavior. </div>
<div>由上述内容可以看出来，这两个命令依赖于模式的配置。当使用绝对URI（如scheme://a/b）这两个命令式相同的。只有默认的模式配置参数对dfs和fs起作用。<br>
[<a href="http://java.dzone.com/articles/difference-between-hadoop-dfs" rel="nofollow" style="list-style-type:none;">http://java.dzone.com/articles/difference-between-hadoop-dfs</a>]</div>
<div><a href="http://blog.csdn.net/pipisorry" rel="nofollow">皮皮blog</a></div>
<div><br><h3>stackoverflow的解释</h3>
<p><a href="http://lib.csdn.net/base/20" rel="nofollow" class="replace_word" title="Hadoop知识库" style="color:#df3434;font-weight:bold;">Hadoop</a> fs：使用面最广，可以操作任何文件系统。</p>
<p>hadoop dfs与hdfs dfs：只能操作HDFS文件系统相关（包括与Local FS间的操作），前者已经Deprecated，一般使用后者。</p>
<p style="border:0px;vertical-align:baseline;clear:both;background:transparent;">
Following are the three commands which appears same but have minute differences</p>
<ol style="border:0px;vertical-align:baseline;background:transparent;"><li style="border:0px;vertical-align:baseline;background:transparent;">
hadoop fs {args}</li><li style="border:0px;vertical-align:baseline;background:transparent;">
hadoop dfs {args}</li><li style="border:0px;vertical-align:baseline;background:transparent;">
<p style="border:0px;vertical-align:baseline;clear:both;background:transparent;">
hdfs dfs {args}</p>
<pre style="border:0px;vertical-align:baseline;font-family:Consolas, Menlo, Monaco, 'Lucida Console', 'Liberation Mono', 'DejaVu Sans Mono', 'Bitstream Vera Sans Mono', 'Courier New', monospace, serif;overflow:auto;background:rgb(238,238,238);">hadoop fs &lt;args&gt;
</pre>
</li></ol><p style="border:0px;vertical-align:baseline;clear:both;background:transparent;">
FS relates to a generic file system which can point to any file systems like local, HDFS etc. So this can be used when you are dealing with different file systems such as Local FS, HFTP FS, S3 FS, and others</p>
<pre style="border:0px;vertical-align:baseline;overflow:auto;">  hadoop dfs &lt;args&gt;
</pre>
<p style="border:0px;vertical-align:baseline;clear:both;">
dfs is very specific to HDFS. would work for operation relates to HDFS. This has been deprecated and we should use hdfs dfs instead.</p>
<pre style="border:0px;vertical-align:baseline;overflow:auto;">  hdfs   dfs &lt;args&gt;
</pre>
<p style="border:0px;vertical-align:baseline;clear:both;">
same as 2nd i.e would work for all the operations related to HDFS and is the recommended command instead of hadoop dfs</p>
<p style="border:0px;vertical-align:baseline;clear:both;">
below is the list categorized as HDFS commands.</p>
<pre style="border:0px;vertical-align:baseline;overflow:auto;">  **#hdfs commands**
  namenode|secondarynamenode|datanode|dfs|dfsadmin|fsck|balancer|fetchdt|oiv|dfsgroups
</pre>
<p style="border:0px;vertical-align:baseline;clear:both;">
So even if you use Hadoop dfs , it will look locate hdfs and delegate that command to hdfs dfs</p>
<p style="border:0px;vertical-align:baseline;clear:both;">
[<span class="link_title"><a href="http://blog.csdn.net/jediael_lu/article/details/37649609" rel="nofollow">何时使用hadoop fs、hadoop dfs与hdfs dfs命令</a></span>]</p>
</div>
from: <a href="http://blog.csdn.net/pipisorry/article/details/51340838" rel="nofollow">
http://blog.csdn.net/pipisorry/article/details/51340838</a>
<p></p>
<p>ref:<br></p>
<p><br></p>
            </div>
                </div>
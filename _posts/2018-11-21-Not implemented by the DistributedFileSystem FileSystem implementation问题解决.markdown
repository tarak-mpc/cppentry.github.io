---
layout:     post
title:      Not implemented by the DistributedFileSystem FileSystem implementation问题解决
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<div>
<pre>上传文件到HDFS时，<span style="font-family:'Courier New';font-size:10pt;background-color:rgb(232,242,254);"><span style="background-color:rgb(255,255,255);"> FileSystem hdfs=FileSystem.</span></span><span style="background-color:rgb(255,255,255);"><span style="font-family:'Courier New';font-size:10pt;"><em>get</em></span></span><span style="font-family:'Courier New';font-size:10pt;background-color:rgb(232,242,254);"><span style="background-color:rgb(255,255,255);">(conf); </span>报错</span></pre>
</div>
<p></p>
<p></p>
<div>
<pre><code>ava.lang.UnsupportedOperationException: Not implemented by the DistributedFileSystem FileSystem implementation
    at org.apache.hadoop.fs.FileSystem.getScheme(FileSystem.java:214)
    at org.apache.hadoop.fs.FileSystem.loadFileSystems(FileSystem.java:2365)
    at org.apache.hadoop.fs.FileSystem.getFileSystemClass(FileSystem.java:2375)
    at org.apache.hadoop.fs.FileSystem.createFileSystem(FileSystem.java:2392)
    at org.apache.hadoop.fs.FileSystem.access$200(FileSystem.java:89)
    at org.apache.hadoop.fs.FileSystem$Cache.getInternal(FileSystem.java:2431)
    at org.apache.hadoop.fs.FileSystem$Cache.get(FileSystem.java:2413)
    at org.apache.hadoop.fs.FileSystem.get(FileSystem.java:368)
    at org.apache.hadoop.fs.Path.getFileSystem(Path.java:296)</code></pre>
</div>
<p></p>
<p>最后发现是hadoop-hdfs 和hadoop-core两个jar包的问题，删除hadoop-core.jar就可以了<br></p>
            </div>
                </div>
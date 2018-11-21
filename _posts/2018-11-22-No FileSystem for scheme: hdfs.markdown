---
layout:     post
title:      No FileSystem for scheme: hdfs
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-size:18px;">当引入hadoop-common-2.2.0.jar包进行二次开发，比如读写HDFS文件时，初次运行报错。</span></p>
<p><span style="font-size:18px;"></span></p><pre><code class="language-plain">java.io.IOException: No FileSystem for scheme: hdfs
        at org.apache.hadoop.fs.FileSystem.getFileSystemClass(FileSystem.java:2421)
        at org.apache.hadoop.fs.FileSystem.createFileSystem(FileSystem.java:2428)
        at org.apache.hadoop.fs.FileSystem.access$200(FileSystem.java:88)
        at org.apache.hadoop.fs.FileSystem$Cache.getInternal(FileSystem.java:2467)
        at org.apache.hadoop.fs.FileSystem$Cache.get(FileSystem.java:2449)
        at org.apache.hadoop.fs.FileSystem.get(FileSystem.java:367)
        at FileCopyToHdfs.readFromHdfs(FileCopyToHdfs.java:65)
        at FileCopyToHdfs.main(FileCopyToHdfs.java:26)</code></pre>
<p><span style="font-size:18px;">这是因为该包下默认的<span style="font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;color:#393939;line-height:21px;background-color:rgb(250,247,239);">core-default.xml没有配置如下属性。</span></span></p>
<p><span style="font-size:18px;"><span style="font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;color:#393939;line-height:21px;background-color:rgb(250,247,239);"><br></span></span></p>
<p><span style="font-size:18px;"><span style="font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;color:#393939;line-height:21px;background-color:rgb(250,247,239);">方法一（在core-site.xml中添加如下配置）：<br></span></span></p>
<p><span style="font-size:18px;"></span></p><pre><code class="language-html">&lt;property&gt;
    &lt;name&gt;fs.hdfs.impl&lt;/name&gt;
    &lt;value&gt;org.apache.hadoop.hdfs.DistributedFileSystem&lt;/value&gt;
    &lt;description&gt;The FileSystem for hdfs: uris.&lt;/description&gt;
&lt;/property&gt;</code></pre>
<p><span style="font-size:18px;">上属性指定fs.hdfs.impl的实现类。</span><span style="font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;color:#393939;line-height:21px;background-color:rgb(250,247,239);"><span style="font-size:18px;">添加完后，问题解决。</span></span></p>
<p><br></p>
<p><span style="font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;color:#393939;line-height:21px;background-color:rgb(250,247,239);"><span style="font-size:18px;">方法二（在程序中添加如下配置）：<br></span></span></p>
<p><span style="font-family:verdana, 'ms song', Arial, Helvetica, sans-serif;color:#393939;line-height:21px;background-color:rgb(250,247,239);"><span style="font-size:18px;"></span></span></p><pre><code class="language-java">conf.set("fs.hdfs.impl", "org.apache.hadoop.hdfs.DistributedFileSystem"); </code></pre><br><p><br></p>
<p><a href="http://www.cnblogs.com/fbiswt/archive/2013/09/11/3314439.html" rel="nofollow"><span style="font-size:18px;"><br></span></a></p>
            </div>
                </div>
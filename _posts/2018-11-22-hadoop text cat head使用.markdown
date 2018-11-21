---
layout:     post
title:      hadoop text cat head使用
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="post-text">
<pre><span style="font-size:14px;"><code>hadoop fs -get 
hadoop fs -copyToLocal 
</code></span></pre>
<p><span style="font-size:14px;">Above HDFS commands can be used for copying the HDFS files to local system.</span></p>
<pre><span style="font-size:14px;"><code>hadoop fs -cat 
</code></span></pre>
<p><span style="font-size:14px;">This command will display the content of the HDFS file on your stdout (console or command prompt).</span></p>
<pre><span style="font-size:14px;"><code>hadoop fs  -text 
</code></span></pre>
<p><span style="font-size:14px;">This will display the content of the hdfs file ( But text only work with zip and TextRecordInputStream formats like SequenceFieFormat).</span></p>
<p><span style="font-size:14px;color:#FF0000;"><br></span></p>
<p><span style="font-size:14px;color:#FF0000;">按照行数读取hadoop文件方法：hadoop fs -text file | head</span><span style="color:#FF0000;">
<span style="font-size:18px;">-n 100</span></span><br></p>
</div>
            </div>
                </div>
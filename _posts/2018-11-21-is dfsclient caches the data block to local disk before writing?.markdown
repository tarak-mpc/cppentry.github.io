---
layout:     post
title:      is dfsclient caches the data block to local disk before writing?
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<h1>Question:</h1>
<p></p>
<pre>Is staging still used in hdfs when writing the data? This doubt arose when I was going through the hdfs documents. 
ref :http://hadoop.apache.org/hdfs/docs/current/hdfs_design.html#Staging

I believe dfsclient does not cache the datablock to local fs (as the document says) but it does streaming of 64KB packets to the datanode and caches the packets of current block only in memory via dataqueue and ackqueue.Is the document needs to be corrected or my understanding is wrong?
</pre>
<br><p></p>
<h1>Answer:</h1>
<p></p>
<pre>DFS client does not write the data to local disk first. Instead, it streams data directly to the datanodes in the write pipeline. I will update the document.</pre>
<br><p></p>
<p><br></p>
            </div>
                </div>
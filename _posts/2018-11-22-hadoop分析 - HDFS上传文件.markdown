---
layout:     post
title:      hadoop分析 - HDFS上传文件
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hdfs 上传文件原理如下图所示：</p>
<p><img src="https://img-blog.csdn.net/20140417225736609?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvZ3lmbHl4/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt=""><br></p>
<p>这里有一个非常重要的类：DFSOutputStream, 它继承 FSOuputSummer</p>
<p>其他比较重要的类有：FileSystem, DFSClient, 至于类之间的关系，请查看api文档</p>
<p>1. 校验码</p>
<p>采用CRC32，所以校验和位是4Bytes</p>
<p>2. 队列只有一个消费者线程：DataStreamer </p>
<p>作用详见java api</p>
<p><br></p>
<p><br></p>
            </div>
                </div>
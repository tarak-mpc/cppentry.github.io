---
layout:     post
title:      DistributedCache   FileNotFoundException
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><span style="font-size:small;"><span style="font-size:small;">此时注意两种文件路径表示形式，一个在HDFS中。一一个是本地文件系统</span>
</span></p>
<p><span style="font-size:small;"><span style="font-size:small;"><span style="font-size:x-small;">DistributedCache.<em>getLocalCacheFiles</em>
(job) 返回的是：/home/hadoop/In</span>
</span>
</span></p>
<p><span style="font-size:small;"><span style="font-size:small;">然而：HDFS：hdfs://home/hadoop/In</span>
</span></p>
<p><span style="font-size:small;"><span style="font-size:small;">            本地: file://home/hadoop/In</span>
</span></p>
<p><span style="font-size:small;"><span style="font-size:small;">此时如果你想在HDFS获取数据，同时直接使用<span style="font-size:x-small;">DistributedCache.<em>getLocalCacheFiles</em>
(job)的路径。</span>
</span>
</span></p>
<p><span style="font-size:small;"><span style="font-size:small;"><span style="font-size:x-small;">则会发生在本地系统查找文件的情况，则就会报FileNotFoundException</span>
</span>
</span></p>
<p><span style="font-size:small;"><span style="font-size:small;">解决方法：<span style="font-size:x-small;">file：// + DistributedCache.<em>getLocalCacheFiles</em>
(job) </span></span>
</span>
</p>            </div>
                </div>
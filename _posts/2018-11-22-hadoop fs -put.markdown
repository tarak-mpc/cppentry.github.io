---
layout:     post
title:      hadoop fs -put
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>hadoop fs -put    filename   hdfs</p>
<p><br></p>
<p>这个时候，如果hdfs文件目录已经存在，此时则会将file放在hdfs/下的，而如果hdfs文件目录不存在，则把file/*的文件放在目录下，即此时hdfs目录下不存在filename.</p>
<p><br></p>
<p>比如 hadoop fs -put /home/test/20121127   /user/test/data/  </p>
<p>此时，如果 /user/test/data/  不存在，则在put时，会把/home/test/20121127下面的文件拷贝到该目录下。如果/home/test/20121127/0000*.lzo  则，此时 /user/test/data/ 下是 /user/test/data/00000*.lzo 而没有20121127这个目录了。但是，如果存在 /user/test/data/  这个目录了，这个时候就是   /user/test/data/ 20121127/0000*.lzo了。</p>
<p><br></p>
<p>因此，需要先判断一下是否存在这个目录了，如果不存在，则mkdir一下，然后再put。否则会和你想要的不一样啊……</p>
<p><br></p>
<p>NND，最近老犯错误……</p>
            </div>
                </div>
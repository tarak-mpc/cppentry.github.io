---
layout:     post
title:      大数据集群，hbase 更新无法创建表（zookeeper 上有历史数据导致）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/qq_27404929/article/details/79226335				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="font-family:'FangSong_GB2312';font-size:24px;">不管是集群还是但节点，都可以使用如下方式。</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:24px;">1.连接到Hbase </span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:24px;"><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>bin/zkCli.sh -server localhost:2181</span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:24px;">2.查看HDFS 上的历史信息</span></p>
<p><span style="font-size:24px;"><span style="font-family:'FangSong_GB2312';"><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>ls /hbase/</span><br></span></p>
<p><span style="font-size:24px;"><span style="font-family:'FangSong_GB2312';">3.删除/hbase 整个目录即可</span></span></p>
<p><span style="font-size:24px;"><span style="font-family:'FangSong_GB2312';"><span class="space" style="display:inline-block;text-indent:2em;line-height:inherit;"> </span>rmr /hbase</span></span></p>
<p><span style="font-family:'FangSong_GB2312';font-size:24px;">4. 重启hbase 和 zookeeper </span></p>
            </div>
                </div>
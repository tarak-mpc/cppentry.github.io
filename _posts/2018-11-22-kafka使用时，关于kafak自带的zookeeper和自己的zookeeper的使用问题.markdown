---
layout:     post
title:      kafka使用时，关于kafak自带的zookeeper和自己的zookeeper的使用问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p><strong>前言：</strong>最近在看到挺多人在问，kafka已经自带zookeeper，会不会和自己的zookeeper起冲突。</p><p><strong>解答：</strong>当然是不会的，在使用kafka时，你可以自己决定使用哪个zookeeper。</p><p><strong>一，使用kafka自带的zookeeper</strong></p><p><strong>  </strong>   kafka自带的zookeeper的启动文件默认在kafka的启动目录下，也就是kafak的bin目录。</p><p><img src="https://img-blog.csdn.net/20180306104522518" alt=""><br></p><p>所以如果你想使用kafka自带的zookeeper，则可以使用这个脚本启动。</p><p><strong>二，使用自己集群里的zookeeper</strong></p><p>  找到自己zookeeper的安装目录的bin目录</p><p><img src="https://img-blog.csdn.net/20180306104853883" alt=""><br></p><p><strong>总结：归根结底就是想用哪个zookeeper就去启动哪个zookeeper就行。</strong></p><p><br></p><p><br></p>            </div>
                </div>
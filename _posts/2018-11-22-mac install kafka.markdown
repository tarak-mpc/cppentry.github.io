---
layout:     post
title:      mac install kafka
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/niu0niu0/article/details/70655620				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p>1. brew install kafka</p>
<p>2. </p>
<p class="p1"><span class="s1">==&gt;</span><span class="s2"> <strong>Pouring kafka-0.10.2.0.sierra.bottle.tar.gz</strong></span></p>
<p class="p1"><span class="s1">==&gt;</span><span class="s2"> <strong>Caveats</strong></span></p>
<p class="p1"><span class="s2">To have launchd start kafka now and restart at login:</span></p>
<p class="p1"><span class="s2">  brew services start kafka</span></p>
<p class="p1"><span class="s2">Or, if you don't want/need a background service you can just run:</span></p>
<p class="p1"><span class="s2">  zookeeper-server-start /usr/local/etc/kafka/zookeeper.properties &amp; kafka-server-start /usr/local/etc/kafka/server.properties</span></p>
            </div>
                </div>
---
layout:     post
title:      Kafka学习笔记 --- Kafka消息机制
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：学习交流为主，未经博主同意禁止转载，禁止用于商用。					https://blog.csdn.net/u012965373/article/details/75063945				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p></p>
<ul><li><span style="font-size:18px;"><em>At most once</em>—Messages may be lost but are never redelivered.
</span></li><li><span style="font-size:18px;"><em>At least once</em>—Messages are never lost but may be redelivered.
</span></li><li><span style="font-size:18px;"><em>Exactly once</em>—this is what people actually want, each message is delivered once and only once.     </span></li></ul>            </div>
                </div>
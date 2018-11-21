---
layout:     post
title:      解决kafka数据目录迁移后，flume无法正常调用Consumer的问题
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/hardyer/article/details/79310122				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <p>    集群环境是CDH5.12，上周由于kafka原始的/var/local下的数据目录被灌满数据，于是在CDH的manager上重新配置kafka的数据文件存储位置，重启kafka集群后flume无法正常读取kafka中的数据，且无报错信息。</p><p>    解决思路：</p><p>    1 对比生产环境与测试环境，定位问题到：<span style="background-color:rgb(255,255,255);">Kafka集群从broker中选择一个broker作为其coordinator，无法读取到</span><span style="background-color:rgb(255,255,255);">coordinator导致阻塞。</span></p><p><span style="background-color:rgb(255,255,255);">    2 研究后排除flume产生问题的嫌疑，定位到kafka和zookeeper。</span></p><p><span style="background-color:rgb(255,255,255);">    3 重新安装kafka，删除topic与zookeeper中topic相关的信息，观察brockerid正确，但是问题依旧存在。</span></p><p><span style="background-color:rgb(255,255,255);">    4 删除zookeeper中的/borcker ，重启kafka集群，问题解决。<br></span></p>            </div>
                </div>
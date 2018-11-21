---
layout:     post
title:      Hive SQL 监控系统 - Hive Falcon
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<table id="pid219256" class="plhin" summary="pid219256" cellspacing="0" cellpadding="0" style="border-collapse:collapse;table-layout:fixed;color:rgb(68,68,68);font-family:Tahoma, 'Microsoft Yahei', Simsun;font-size:12px;line-height:18px;"><tbody><tr><td class="plc" style="vertical-align:top;">
<div class="pct">
<div class="pcb" style="clear:left;">
<div class="t_fsz" style="min-height:100px;">
<table cellspacing="0" cellpadding="0" style="border-collapse:collapse;table-layout:fixed;"><tbody><tr><td class="t_f" id="postmessage_219256" style="font-size:14px;">
<span class="pstatus" style="display:block;font-size:12px;text-align:center;line-height:32px;color:rgb(153,153,153);">本帖最后由 levycui 于 2017-3-28 16:08 编辑</span><br><span style="color:#FF0000;"><span style="font-weight:700;">问题导读：<br>
1、Hive Falcon是什么？<br>
2、Hive Falcon有什么用途？<br>
3、Hive Falcon包含哪些组件？<br>
4、如何使用Hive Falcon？</span></span><br><br><img id="aimg_bD08S" class="zoom" border="0" alt="" src="http://www.aboutyun.com/static/image/hrline/3.gif" width="451" height="30"><br><br><span style="font-size:14px;"><span style="font-weight:700;">Hive SQL 监控系统 - Hive Falcon介绍</span></span><br><span style="font-weight:700;"><br>
1.概述</span><br>
在开发工作当中，提交 Hadoop 任务，任务的运行详情，这是我们所关心的，当业务并不复杂的时候，我们可以使用 Hadoop 提供的命令工具去管理 YARN 中的任务。在编写 Hive SQL 的时候，需要在 Hive 终端，编写 SQL 语句，来观察 MapReduce 的运行情况，长此以往，感觉非常的不便。另外随着业务的复杂化，任务的数量增加，此时我们在使用这套流程，已预感到力不从心，这时候 Hive 的监控系统此刻便尤为显得重要，我们需要观察 Hive SQL 的 MapReduce 运行详情以及在 YARN
 中的相关状态。<br><br>
因此，我们经过调研，从互联网公司的一些需求出发，从各位 DEVS 的使用经验和反馈出发，结合业界的一些大的开源的 Hadoop SQL 消息监控，用监控的一些思考出发，设计开发了现在这样的监控系统：Hive Falcon。<br><br>
Hive Falcon 用于监控 Hadoop 集群中被提交的任务，以及其运行的状态详情。其中 Yarn 中任务详情包含任务 ID，提交者，任务类型，完成状态等信息。另外，还可以编写 Hive SQL，并运 SQL，查看 SQL 运行详情。也可以查看 Hive 仓库中所存在的表及其表结构等信息。下载地址，如下所示：<br><br>
    ［<a href="https://hf.smartloli.org/2.Install/2.Installing.html" rel="nofollow" style="color:rgb(51,102,153);">Hive Falcon 下载地址</a>］<br><br><span style="font-weight:700;">2.内容</span><br>
Hive Falcon 涉及以下内容：<br><ul><li style="list-style:disc;">
    Dashboard</li><li style="list-style:disc;">
    Query</li><li style="list-style:disc;">
    Tables</li><li style="list-style:disc;">
    Tasks</li><li style="list-style:disc;">
    Clients &amp;  Nodes<br></li></ul><br>
2.1 Dashboard<br>
我们通过在浏览器中输入 http://host:port/hf，访问 Hive Falcon 的 Dashboard 页面。该页面包含以下内容：<br><ul><li style="list-style:disc;">
    Hive Clients</li><li style="list-style:disc;">
    Hive Tables</li><li style="list-style:disc;">
    Hadoop DataNodes</li><li style="list-style:disc;">
    YARN Tasks</li><li style="list-style:disc;">
    Hive Clients Graph<br></li></ul><br>
如下图所示：<br><img id="aimg_29747" src="http://www.aboutyun.com/data/attachment/forum/201703/28/143612iokovlphoorxbuwb.png" class="zoom" width="600" alt=""> <br><br>
2.2 Query<br>
Query 模块下，提供一个运行 Hive SQL 的界面，该界面可以用来查看观察 SQL 运行的 MapReduce 详情。包含 SQL 编辑区，日志输出，以及结果展示。如下图所示：<br><img id="aimg_29748" src="http://www.aboutyun.com/data/attachment/forum/201703/28/143645gubouugo5ii27igg.png" class="zoom" width="600" alt=""> <br><br>
提示：在 SQL 编辑区可以通过 Alt+/ 快捷键，快速调出 SQL 关键字。<br><br>
2.3 Tables<br>
Tables 展示 Hive 中所有的表信息，包含以下内容：<br><ul><li style="list-style:disc;">
    表名</li><li style="list-style:disc;">
    表类型（如：内部表，外部表等）</li><li style="list-style:disc;">
    所属者</li><li style="list-style:disc;">
    存放路径</li><li style="list-style:disc;">
    创建时间<br></li></ul>
如下图所示：<br><img id="aimg_29749" src="http://www.aboutyun.com/data/attachment/forum/201703/28/143734uv3e53fivfeo13ei.png" class="zoom" width="600" alt=""> <br><br>
每一个表名都附带一个超链接，可以通过该超链接查看该表的表结构，如下图所示：<br><img id="aimg_29750" src="http://www.aboutyun.com/data/attachment/forum/201703/28/143804uzxbbcina5anenvx.png" class="zoom" width="600" alt=""> <br><br>
2.4 Tasks<br>
Tasks 模块下所涉及的内容是 YARN 上的任务详情，包含的内容如下所示：<br><ul><li style="list-style:disc;">
    All（所有任务）</li><li style="list-style:disc;">
    Running（正在运行的任务）</li><li style="list-style:disc;">
    Finished（已完成的任务）</li><li style="list-style:disc;">
    Failed（以失败的任务）</li><li style="list-style:disc;">
    Killed（已失败的任务）<br></li></ul>
如下图所示：<br><img id="aimg_29751" src="http://www.aboutyun.com/data/attachment/forum/201703/28/143847qngog47gjg82r2lq.png" class="zoom" width="600" alt=""> <br><br>
2.5 Clients &amp; Nodes<br>
该模块展示 Hive Client 详情，以及 Hadoop DataNode 的详情，如下图所示：<br><img id="aimg_29752" src="http://www.aboutyun.com/data/attachment/forum/201703/28/143914lmhj704wjj77wo77.png" class="zoom" width="600" alt=""> <br><br>
2.6 脚本命令<br>
命令
<table cellspacing="0" class="t_table" style="border-collapse:collapse;border:1px solid rgb(227,237,245);table-layout:auto;"><tbody><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
<br></td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
描述</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
hf.sh start</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
启动 Hive Falcon</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
hf.sh status</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
查看 Hive Falcon</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
hf.sh stop</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
停止 Hive Falcon</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
hf.sh restart</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
重启 Hive Falcon</td>
</tr><tr><td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
hf.sh stats</td>
<td style="font-size:14px;border:1px solid rgb(227,237,245);overflow:hidden;">
查看 Hive Falcon 在 Linux 系统中所占用的句柄数量</td>
</tr></tbody></table><br><span style="font-weight:700;">3.数据采集</span><br>
Hive Falcon 系统的各个模块的数据来源，所包含的内容，如下图所示： <br><img id="aimg_29753" src="http://www.aboutyun.com/data/attachment/forum/201703/28/143957uwp629z229w4cnnh.png" class="zoom" width="600" alt=""> <br><br><span style="font-weight:700;">4.总结</span><br>
Hive Falcon 的安装使用比较简单，下载安装，安装文档的描述进行安装配置即可，安装部署文档地址，如下所示：<br><a href="https://hf.smartloli.org/" rel="nofollow" style="color:rgb(51,102,153);">Hive Falcon 使用文档</a><br><br><span style="font-weight:700;">5.结束语</span><br>
这篇博客就和大家分享到这里，如果大家在研究学习的过程当中有什么问题，可以加群进行讨论或发送邮件给我，我会尽我所能为您解答，与君共勉！<br><br>
来源：cnblogs<br>
作者：哥不是小萝莉<br></td>
</tr></tbody></table></div>
<div id="comment_219256" class="cm"></div>
<div id="post_rate_div_219256"></div>
<div id="aliyun_cnzz_tui_1000072387">
</div>
</div>
</div>
</td>
</tr><tr><td class="plc plm" style="vertical-align:top;">
<div id="p_btn" class="mtw mbm hm cl" style="text-align:center;line-height:24px;">
<div class="tshare cl">
<span>分享到: </span> <a href="http://www.aboutyun.com/home.php?mod=spacecp&amp;ac=plugin&amp;id=qqconnect:spacecp&amp;pluginop=share&amp;sh_type=4&amp;thread_id=21379" rel="nofollow" id="k_share_to_qq" title="QQ好友和群" style="color:rgb(51,102,153);text-decoration:none;display:inline-block;vertical-align:top;background:none no-repeat 0px 0px;"><span style="display:block;background:none no-repeat 100% 100%;"><img src="http://www.aboutyun.com/static/image/common/qq_share.png" alt="QQ好友和群" style="border:none;">QQ好友和群</span></a> <a id="k_qqshare" title="QQ空间" style="color:rgb(51,102,153);display:inline-block;vertical-align:top;background:none no-repeat 0px 0px;"><span style="display:block;background:none no-repeat 100% 100%;"><img src="http://www.aboutyun.com/static/image/common/qzone.gif" alt="QQ空间" style="border:none;">QQ空间</span></a> <a id="k_weiboshare" title="腾讯微博" style="color:rgb(51,102,153);display:inline-block;vertical-align:top;background:none no-repeat 0px 0px;"><span style="display:block;background:none no-repeat 100% 100%;"><img src="http://www.aboutyun.com/static/image/common/weibo.png" alt="腾讯微博" style="border:none;">腾讯微博</span></a> <a id="k_pengyoushare" title="腾讯朋友" style="color:rgb(51,102,153);display:inline-block;vertical-align:top;background:none no-repeat 0px 0px;"><span style="display:block;background:none no-repeat 100% 100%;"><img src="http://www.aboutyun.com/static/image/common/pengyou.png" alt="腾讯朋友" style="border:none;">腾讯朋友</span></a> <a style="color:rgb(51,102,153);display:inline-block;vertical-align:top;background:none no-repeat 0px 0px;"><span style="display:block;background:none no-repeat 100% 100%;"><img src="http://www.aboutyun.com/source/plugin/wechat/image/share.png" alt="微信" style="border:none;">微信</span></a></div>
<a href="http://www.aboutyun.com/home.php?mod=spacecp&amp;ac=favorite&amp;type=thread&amp;id=21379&amp;formhash=bf3c5165" rel="nofollow" id="k_favorite" title="收藏本帖" style="color:rgb(51,51,51);text-decoration:none;display:inline-block;vertical-align:top;"><span style="display:block;"><img src="http://www.aboutyun.com/static/image/common/fav.gif" alt="收藏" style="border:none;">收藏</span></a> <a class="followp" href="http://www.aboutyun.com/home.php?mod=spacecp&amp;ac=follow&amp;op=relay&amp;tid=21379&amp;from=forum" rel="nofollow" title="转播求扩散" style="color:rgb(51,51,51);text-decoration:none;display:inline-block;vertical-align:top;"><span style="display:block;"><img src="http://www.aboutyun.com/static/image/common/rt.png" alt="转播" style="border:none;">转播</span></a> <a class="sharep" href="http://www.aboutyun.com/home.php?mod=spacecp&amp;ac=share&amp;type=thread&amp;id=21379" rel="nofollow" title="分享推精华" style="color:rgb(51,51,51);text-decoration:none;display:inline-block;vertical-align:top;"><span style="display:block;"><img src="http://www.aboutyun.com/static/image/common/oshr.png" alt="分享" style="border:none;">分享</span></a> <a href="http://www.aboutyun.com/forum.php?mod=collection&amp;action=edit&amp;op=addthread&amp;tid=21379" rel="nofollow" id="k_collect" title="0 人淘帖" style="color:rgb(51,51,51);text-decoration:none;display:inline-block;vertical-align:top;"><span style="display:block;"><img src="http://www.aboutyun.com/static/image/common/collection.png" alt="分享" style="border:none;">淘帖</span></a> <a id="recommend_add" href="http://www.aboutyun.com/forum.php?mod=misc&amp;action=recommend&amp;do=add&amp;tid=21379&amp;hash=bf3c5165" rel="nofollow" title="0 人支持" style="color:rgb(51,51,51);text-decoration:none;display:inline-block;vertical-align:top;"><span style="display:block;"><img src="http://www.aboutyun.com/static/image/common/rec_add.gif" alt="支持" style="border:none;">支持</span></a> <a id="recommend_subtract" href="http://www.aboutyun.com/forum.php?mod=misc&amp;action=recommend&amp;do=subtract&amp;tid=21379&amp;hash=bf3c5165" rel="nofollow" title="0 人反对" style="color:rgb(51,51,51);text-decoration:none;display:inline-block;vertical-align:top;"><span style="display:block;"><img src="http://www.aboutyun.com/static/image/common/rec_subtract.gif" alt="反对" style="border:none;">反对</span></a></div>
</td>
</tr></tbody></table>            </div>
                </div>
---
layout:     post
title:      spark 源代码分析 （一）准备阶段： idea 导入spark 源代码
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<br><br><div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
按计划这几天应该分析zookeeper，结果项目组将我换工作了，以后可能就专门研究开源的代码hbase，我自己想研究spark，提前准备一下，或者以后有更多机会。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
spark 研究准备</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
1）jdk 安装</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
2）scala 安装</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
3）spark 安装。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
如果想部署一个单机版本，可以安装上面的东西做一些，否则，你可以跳过上面的步骤。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
·1）idea 准备，去官网下载一个idea 社区版的。并安装</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<img src="https://img-blog.csdn.net/20170726201400611?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmZlbmdnYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" border="0" alt="" style="border:0px;"><div> </div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
2）打开Idea，安装scala</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
这个过程可以选择，启动后的plugin里安装</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
或者去setting里 </div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<img src="https://img-blog.csdn.net/20170726201514884?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmZlbmdnYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" border="0" alt="" style="border:0px;"><div> 选择在线安装就行。</div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
3） 去github 下载spark master，如果你安装git可以直接下载master ，或者直接下载压缩包。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<img src="https://img-blog.csdn.net/20170726201551373?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmZlbmdnYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" border="0" alt="" style="border:0px;"><div> </div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
4）安装maven，如果你不知道怎么配置，百度，或者留言</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
提供一个比较好的miror</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<img src="https://img-blog.csdn.net/20170726201616048?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmZlbmdnYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" border="0" alt="" style="border:0px;"></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
5）用idea 打开spark ，配置maven的</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<img src="https://img-blog.csdn.net/20170726201638983?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmZlbmdnYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" border="0" alt="" style="border:0px;"><div> </div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
6）长时间的等待下载依赖包，如果中间出现什么问题可以留言。我基本等了半个多小时。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
7） 配置一下jdk</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<img src="https://img-blog.csdn.net/20170726201745841?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmZlbmdnYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" border="0" alt="" style="border:0px;"><div><br></div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
基本可以用了</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
最后的结果</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<img src="https://img-blog.csdn.net/20170726201717889?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hlbmZlbmdnYW5n/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center" border="0" alt="" style="border:0px;"><div> </div>
<div><br></div>
</div>
<div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
按计划这几天应该分析zookeeper，结果项目组将我换工作了，以后可能就专门研究开源的代码hbase，我自己想研究spark，提前准备一下，或者以后有更多机会。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
spark 研究准备</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
1）jdk 安装</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
2）scala 安装</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
3）spark 安装。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
如果想部署一个单机版本，可以安装上面的东西做一些，否则，你可以跳过上面的步骤。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
·1）idea 准备，去官网下载一个idea 社区版的。并安装</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<div> </div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
2）打开Idea，安装scala</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
这个过程可以选择，启动后的plugin里安装</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
或者去setting里 </div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<div> 选择在线安装就行。</div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
3） 去github 下载spark master，如果你安装git可以直接下载master ，或者直接下载压缩包。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<div> </div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
4）安装maven，如果你不知道怎么配置，百度，或者留言</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
提供一个比较好的miror</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
5）用idea 打开spark ，配置maven的</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<div> </div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
6）长时间的等待下载依赖包，如果中间出现什么问题可以留言。我基本等了半个多小时。</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
7） 配置一下jdk</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<div><br></div>
</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
基本可以用了</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
最后的结果</div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<br></div>
<div style="font-family:Helvetica, 'Hiragino Sans GB', '微软雅黑', 'Microsoft YaHei UI', SimSun, SimHei, arial, sans-serif;font-size:15px;line-height:24px;">
<div> </div>
<div><br></div>
</div>
</div>
            </div>
                </div>
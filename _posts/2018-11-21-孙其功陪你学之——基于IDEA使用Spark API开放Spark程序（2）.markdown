---
layout:     post
title:      孙其功陪你学之——基于IDEA使用Spark API开放Spark程序（2）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><span style="color:rgb(0,102,0);font-family:'宋体';font-size:14px;line-height:26px;">《Spark亚太研究院系列丛书——Spark实战高手之路 从零开始》本书通过Spark的shell测试Spark的工作；使用Spark的cache机制观察一下效率的提升构建Spark的IDE开发环境；通过Spark的IDE搭建Spark开发环境；测试Spark IDE开发环境等等。本节为大家介绍基于IDEA使用Spark API开放Spark程序。</span></p>
<p><span style="color:rgb(0,102,0);font-family:'宋体';font-size:14px;line-height:26px;"></span></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="text-align:center;">2.基于IDEA使用Spark API开放Spark程序（2）</span></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
下面修改Libraries：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s9.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3hjXgjaliAABYdjdCyIg177.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s9.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3hjXgjaliAABYdjdCyIg177.jpg" width="498" height="240" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<br style="clear:both;width:0px;">
因为要开发Spark程序，所以需要把Spark的开发需要的jar包导进来：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s5.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3hk2hAAeWAACGoRiwdmI260.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s5.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3hk2hAAeWAACGoRiwdmI260.jpg" width="498" height="468" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<br style="clear:both;width:0px;">
加入后：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s6.51cto.com/wyfs02/M02/4C/29/wKiom1Q3hirRznn5AABJ6KwuyUM769.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s6.51cto.com/wyfs02/M02/4C/29/wKiom1Q3hirRznn5AABJ6KwuyUM769.jpg" width="498" height="188" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<br style="clear:both;width:0px;">
导入包完成后，在工程的scala下面创建一个package：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s2.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3hmezTMYvAABWpoRX3aQ495.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s2.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3hmezTMYvAABWpoRX3aQ495.jpg" width="498" height="219" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<br style="clear:both;width:0px;">
创建一个Object对象：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s7.51cto.com/wyfs02/M00/4C/29/wKiom1Q3hj7ztReoAABYv22sbjE646.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s7.51cto.com/wyfs02/M00/4C/29/wKiom1Q3hj7ztReoAABYv22sbjE646.jpg" width="498" height="216" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
完成初始类的创建：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s4.51cto.com/wyfs02/M00/4C/29/wKiom1Q3hk3APg7LAAA56olu2as361.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s4.51cto.com/wyfs02/M00/4C/29/wKiom1Q3hk3APg7LAAA56olu2as361.jpg" width="468" height="214" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
首先构建Spark Driver的模板代码：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s8.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3hpTSuKImAABg4S5TMX0548.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s8.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3hpTSuKImAABg4S5TMX0548.jpg" width="498" height="337" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
该程序是对前面的搜狗日志的处理代码，只不过这个时候在IDEA中编写而已。</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
接下来进行打包，使用Project  Structure的Artifacts：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s4.51cto.com/wyfs02/M01/4C/29/wKiom1Q3hqDxCR3OAAA5kg_efiw703.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s4.51cto.com/wyfs02/M01/4C/29/wKiom1Q3hqDxCR3OAAA5kg_efiw703.jpg" width="478" height="286" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
使用From modules with dependencies：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s6.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3huSixuo_AABnVaIUsMk884.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s6.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3huSixuo_AABnVaIUsMk884.jpg" width="498" height="395" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<br style="clear:both;width:0px;">
选择Main Class：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s4.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3hvzhoK3gAABAH4yMq-k329.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s4.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3hvzhoK3gAABAH4yMq-k329.jpg" width="467" height="372" style="border:0px;text-align:center;"></a> </p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s4.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3hwPicPtrAABWxjUUD3M747.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s4.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3hwPicPtrAABWxjUUD3M747.jpg" width="473" height="316" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
点击“OK”:<br style="clear:both;width:0px;">
 </p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s8.51cto.com/wyfs02/M02/4C/29/wKiom1Q3hvvjhc-aAABikphzPak031.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s8.51cto.com/wyfs02/M02/4C/29/wKiom1Q3hvvjhc-aAABikphzPak031.jpg" width="498" height="273" style="border:0px;text-align:center;"></a></p>
<br><br>            </div>
                </div>
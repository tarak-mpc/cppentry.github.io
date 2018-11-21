---
layout:     post
title:      孙其功陪你学之——基于IDEA使用Spark API开放Spark程序（1）
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="text-align:center;"><span style="color:rgb(0,102,0);font-family:'宋体';font-size:14px;line-height:26px;">《Spark亚太研究院系列丛书——Spark实战高手之路 从零开始》本书通过Spark的shell测试Spark的工作；使用Spark的cache机制观察一下效率的提升构建Spark的IDE开发环境；通过Spark的IDE搭建Spark开发环境；测试Spark IDE开发环境等等。本节为大家介绍基于IDEA使用Spark
 API开放Spark程序。</span></span></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<span style="text-align:center;">基于IDEA使用Spark API开放Spark程序（1）</span></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
创建一个Scala IDEA工程：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s4.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3hVnQGw2WAAA96u8SkKE725.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s4.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3hVnQGw2WAAA96u8SkKE725.jpg" width="495" height="380" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
我们使用Non-SBT的方式，点击“Next”：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s3.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3hW3i-fHbAABbHSjHQBY912.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s3.51cto.com/wyfs02/M02/4C/2A/wKioL1Q3hW3i-fHbAABbHSjHQBY912.jpg" width="498" height="371" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
把工程命名一下，其它的按照默认：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s6.51cto.com/wyfs02/M02/4C/28/wKiom1Q3hUSCubINAABcWOA5wwg496.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s6.51cto.com/wyfs02/M02/4C/28/wKiom1Q3hUSCubINAABcWOA5wwg496.jpg" width="498" height="366" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<br style="clear:both;width:0px;">
点击“Finish”完成工程的创建：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s1.51cto.com/wyfs02/M02/4C/29/wKiom1Q3hViglSFNAAA6at7vaes923.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s1.51cto.com/wyfs02/M02/4C/29/wKiom1Q3hViglSFNAAA6at7vaes923.jpg" width="498" height="277" style="border:0px;text-align:center;"></a> <br style="clear:both;width:0px;">
修改项目的属性：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s2.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3ha7TGbx7AABqEPkmQzw341.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s2.51cto.com/wyfs02/M00/4C/2A/wKioL1Q3ha7TGbx7AABqEPkmQzw341.jpg" width="498" height="330" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
首先修改Modules选项：</p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s8.51cto.com/wyfs02/M00/4C/29/wKiom1Q3hYjBhgcGAABXu8jJvVw112.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s8.51cto.com/wyfs02/M00/4C/29/wKiom1Q3hYjBhgcGAABXu8jJvVw112.jpg" width="498" height="257" style="border:0px;text-align:center;"></a></p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<br style="clear:both;width:0px;">
在src下创建两个文件夹，并把其属性改为source：<br style="clear:both;width:0px;">
 </p>
<p style="color:rgb(51,51,51);background-color:rgb(248,248,248);text-indent:28px;font-family:'宋体';font-size:14px;line-height:28px;">
<a href="http://s6.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3hc3iV4POAAB1ZnBmx4o256.jpg" rel="nofollow" style="color:rgb(0,66,118);"><img class="fit-image" border="0" alt="" src="http://s6.51cto.com/wyfs02/M01/4C/2A/wKioL1Q3hc3iV4POAAB1ZnBmx4o256.jpg" width="498" height="345" style="border:0px;text-align:center;"></a></p>
            </div>
                </div>